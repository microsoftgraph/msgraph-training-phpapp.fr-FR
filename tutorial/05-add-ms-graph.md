<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application. Pour cette application, vous allez utiliser la bibliothèque [Microsoft Graph](https://github.com/microsoftgraph/msgraph-sdk-php) pour passer des appels à Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Récupérer les événements de calendrier à partir d’Outlook

1. Créez un répertoire dans le répertoire **./app** nommé `TimeZones` , puis créez un fichier dans ce répertoire nommé `TimeZones.php` et ajoutez le code suivant.

    :::code language="php" source="../demo/graph-tutorial/app/TimeZones/TimeZones.php":::

    Cette classe implémente un mappage simpliste des noms des fuseaux horaires Windows aux identificateurs de fuseau horaire IANA.

1. Créez un fichier dans le répertoire **./app/http/Controllers** nommé `CalendarController.php` et ajoutez le code suivant.

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
    - Les `startDateTime` `endDateTime` paramètres et définissent le début et la fin de l’affichage.
    - Le `$select` paramètre limite les champs renvoyés pour chaque événement à ceux que l’affichage utilise réellement.
    - Le `$orderby` paramètre trie les résultats en fonction de la date et de l’heure de leur création, avec l’élément le plus récent en premier.
    - Le `$top` paramètre limite les résultats à 25 événements.
    - L' `Prefer: outlook.timezone=""` en-tête entraîne l’ajustement des heures de début et de fin dans la réponse au fuseau horaire préféré de l’utilisateur.

1. Mettez à jour les itinéraires dans **./routes/Web.php** pour ajouter un itinéraire à ce nouveau contrôleur.

    ```php
    Route::get('/calendar', 'CalendarController@calendar');
    ```

1. Connectez-vous, puis cliquez sur le lien **calendrier** dans la barre de navigation. Si tout fonctionne, vous devriez voir une image mémoire JSON des événements dans le calendrier de l’utilisateur.

## <a name="display-the-results"></a>Afficher les résultats

Vous pouvez désormais ajouter une vue pour afficher les résultats de façon plus parlante.

1. Créez un fichier dans le répertoire **./Resources/views** nommé `calendar.blade.php` et ajoutez le code suivant.

    :::code language="php" source="../demo/graph-tutorial/resources/views/calendar.blade.php" id="CalendarSnippet":::

    Cela permet de parcourir une collection d’événements et d’ajouter une ligne de tableau pour chacun d’eux.

1. Supprimez la `return response()->json($events);` ligne de l' `calendar` action dans **./app/http/Controllers/CalendarController.php**et remplacez-la par le code suivant.

    ```php
    $viewData['events'] = $events;
    return view('calendar', $viewData);
    ```

1. Actualisez la page et l’application doit maintenant afficher un tableau d’événements.

    ![Capture d’écran du tableau des événements](./images/add-msgraph-01.png)
