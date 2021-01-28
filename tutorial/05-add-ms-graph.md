<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application. Pour cette application, vous allez utiliser la bibliothèque [microsoft-graph](https://github.com/microsoftgraph/msgraph-sdk-php) pour appeler Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Récupérer les événements de calendrier à partir d’Outlook

1. Créez un répertoire dans le répertoire **./app** nommé, puis créez un fichier dans ce répertoire nommé, puis ajoutez `TimeZones` le code `TimeZones.php` suivant.

    :::code language="php" source="../demo/graph-tutorial/app/TimeZones/TimeZones.php":::

    Cette classe implémente un mappage simple des noms de fuseau horaire Windows aux identificateurs de fuseau horaire IANA.

1. Créez un fichier dans le répertoire **./app/Http/Controllers** nommé `CalendarController.php` et ajoutez le code suivant.

    ```php
    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Http\Request;
    use Microsoft\Graph\Graph;
    use Microsoft\Graph\Model;
    use App\TokenStore\TokenCache;
    use App\TimeZones\TimeZones;

    class CalendarController extends Controller
    {
      public function calendar()
      {
        $viewData = $this->loadViewData();

        $graph = $this->getGraph();

        // Get user's timezone
        $timezone = TimeZones::getTzFromWindows($viewData['userTimeZone']);

        // Get start and end of week
        $startOfWeek = new \DateTimeImmutable('sunday -1 week', $timezone);
        $endOfWeek = new \DateTimeImmutable('sunday', $timezone);

        $viewData['dateRange'] = $startOfWeek->format('M j, Y').' - '.$endOfWeek->format('M j, Y');

        $queryParams = array(
          'startDateTime' => $startOfWeek->format(\DateTimeInterface::ISO8601),
          'endDateTime' => $endOfWeek->format(\DateTimeInterface::ISO8601),
          // Only request the properties used by the app
          '$select' => 'subject,organizer,start,end',
          // Sort them by start time
          '$orderby' => 'start/dateTime',
          // Limit results to 25
          '$top' => 25
        );

        // Append query parameters to the '/me/calendarView' url
        $getEventsUrl = '/me/calendarView?'.http_build_query($queryParams);

        $events = $graph->createRequest('GET', $getEventsUrl)
          // Add the user's timezone to the Prefer header
          ->addHeaders(array(
            'Prefer' => 'outlook.timezone="'.$viewData['userTimeZone'].'"'
          ))
          ->setReturnType(Model\Event::class)
          ->execute();

        return response()->json($events);
      }

      private function getGraph(): Graph
      {
        // Get the access token from the cache
        $tokenCache = new TokenCache();
        $accessToken = $tokenCache->getAccessToken();

        // Create a Graph client
        $graph = new Graph();
        $graph->setAccessToken($accessToken);
        return $graph;
      }
    }
    ```

    Que fait ce code ?

    - L’URL qui sera appelée est `/v1.0/me/calendarView`.
    - Les `startDateTime` `endDateTime` paramètres et les paramètres définissent le début et la fin de l’affichage.
    - Le paramètre limite les champs renvoyés pour chaque événement à ceux que `$select` l’affichage utilisera réellement.
    - Le paramètre trie les résultats par date et heure de création, l’élément le plus `$orderby` récent étant le premier.
    - Le `$top` paramètre limite les résultats à 25 événements.
    - L’en-tête entraîne l’ajustement des heures de début et de fin dans la réponse au fuseau horaire préféré de `Prefer: outlook.timezone=""` l’utilisateur.

1. Mettez à jour les itinéraires **dans ./routes/web.php** pour ajouter un itinéraire à ce nouveau contrôleur.

    ```php
    Route::get('/calendar', 'CalendarController@calendar');
    ```

1. Connectez-vous et cliquez **sur le lien** Calendrier dans la barre de navigation. Si tout fonctionne, vous devriez voir une image mémoire JSON des événements dans le calendrier de l’utilisateur.

## <a name="display-the-results"></a>Afficher les résultats

Vous pouvez désormais ajouter une vue pour afficher les résultats de façon plus parlante.

1. Créez un fichier dans le répertoire **./resources/views** nommé `calendar.blade.php` et ajoutez le code suivant.

    :::code language="php" source="../demo/graph-tutorial/resources/views/calendar.blade.php" id="CalendarSnippet":::

    Cela permet de parcourir une collection d’événements et d’ajouter une ligne de tableau pour chacun d’eux.

1. Mettez à jour les itinéraires **dans ./routes/web.php** pour ajouter des itinéraires pour `/calendar/new` . Vous allez implémenter ces fonctions dans la section suivante, mais l’itinéraire doit être défini maintenant, car **calendar.blade.php** le référence.

    ```php
    Route::get('/calendar/new', 'CalendarController@getNewEventForm');
    Route::post('/calendar/new', 'CalendarController@createNewEvent');
    ```

1. Supprimez `return response()->json($events);` la ligne de l’action dans `calendar` **./app/Http/Controllers/CalendarController.php** et remplacez-la par le code suivant.

    ```php
    $viewData['events'] = $events;
    return view('calendar', $viewData);
    ```

1. Actualisez la page et l’application doit maintenant restituer une table des événements.

    ![Capture d’écran du tableau des événements](./images/add-msgraph-01.png)
