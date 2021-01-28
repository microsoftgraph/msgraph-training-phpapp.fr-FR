<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e0960-101">Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application.</span><span class="sxs-lookup"><span data-stu-id="e0960-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="e0960-102">Pour cette application, vous allez utiliser la bibliothèque [microsoft-graph](https://github.com/microsoftgraph/msgraph-sdk-php) pour appeler Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="e0960-102">For this application, you will use the [microsoft-graph](https://github.com/microsoftgraph/msgraph-sdk-php) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="e0960-103">Récupérer les événements de calendrier à partir d’Outlook</span><span class="sxs-lookup"><span data-stu-id="e0960-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="e0960-104">Créez un répertoire dans le répertoire **./app** nommé, puis créez un fichier dans ce répertoire nommé, puis ajoutez `TimeZones` le code `TimeZones.php` suivant.</span><span class="sxs-lookup"><span data-stu-id="e0960-104">Create a new directory in the **./app** directory named `TimeZones`, then create a new file in that directory named `TimeZones.php`, and add the following code.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/TimeZones/TimeZones.php":::

    <span data-ttu-id="e0960-105">Cette classe implémente un mappage simple des noms de fuseau horaire Windows aux identificateurs de fuseau horaire IANA.</span><span class="sxs-lookup"><span data-stu-id="e0960-105">This class implements a simplistic mapping of Windows time zone names to IANA time zone identifiers.</span></span>

1. <span data-ttu-id="e0960-106">Créez un fichier dans le répertoire **./app/Http/Controllers** nommé `CalendarController.php` et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="e0960-106">Create a new file in the **./app/Http/Controllers** directory named `CalendarController.php`, and add the following code.</span></span>

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

    <span data-ttu-id="e0960-107">Que fait ce code ?</span><span class="sxs-lookup"><span data-stu-id="e0960-107">Consider what this code is doing.</span></span>

    - <span data-ttu-id="e0960-108">L’URL qui sera appelée est `/v1.0/me/calendarView`.</span><span class="sxs-lookup"><span data-stu-id="e0960-108">The URL that will be called is `/v1.0/me/calendarView`.</span></span>
    - <span data-ttu-id="e0960-109">Les `startDateTime` `endDateTime` paramètres et les paramètres définissent le début et la fin de l’affichage.</span><span class="sxs-lookup"><span data-stu-id="e0960-109">The `startDateTime` and `endDateTime` parameters define the start and end of the view.</span></span>
    - <span data-ttu-id="e0960-110">Le paramètre limite les champs renvoyés pour chaque événement à ceux que `$select` l’affichage utilisera réellement.</span><span class="sxs-lookup"><span data-stu-id="e0960-110">The `$select` parameter limits the fields returned for each events to just those the view will actually use.</span></span>
    - <span data-ttu-id="e0960-111">Le paramètre trie les résultats par date et heure de création, l’élément le plus `$orderby` récent étant le premier.</span><span class="sxs-lookup"><span data-stu-id="e0960-111">The `$orderby` parameter sorts the results by the date and time they were created, with the most recent item being first.</span></span>
    - <span data-ttu-id="e0960-112">Le `$top` paramètre limite les résultats à 25 événements.</span><span class="sxs-lookup"><span data-stu-id="e0960-112">The `$top` parameter limits the results to 25 events.</span></span>
    - <span data-ttu-id="e0960-113">L’en-tête entraîne l’ajustement des heures de début et de fin dans la réponse au fuseau horaire préféré de `Prefer: outlook.timezone=""` l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="e0960-113">The `Prefer: outlook.timezone=""` header causes the start and end times in the response to be adjusted to the user's preferred time zone.</span></span>

1. <span data-ttu-id="e0960-114">Mettez à jour les itinéraires **dans ./routes/web.php** pour ajouter un itinéraire à ce nouveau contrôleur.</span><span class="sxs-lookup"><span data-stu-id="e0960-114">Update the routes in **./routes/web.php** to add a route to this new controller.</span></span>

    ```php
    Route::get('/calendar', 'CalendarController@calendar');
    ```

1. <span data-ttu-id="e0960-115">Connectez-vous et cliquez **sur le lien** Calendrier dans la barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="e0960-115">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="e0960-116">Si tout fonctionne, vous devriez voir une image mémoire JSON des événements dans le calendrier de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="e0960-116">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="e0960-117">Afficher les résultats</span><span class="sxs-lookup"><span data-stu-id="e0960-117">Display the results</span></span>

<span data-ttu-id="e0960-118">Vous pouvez désormais ajouter une vue pour afficher les résultats de façon plus parlante.</span><span class="sxs-lookup"><span data-stu-id="e0960-118">Now you can add a view to display the results in a more user-friendly manner.</span></span>

1. <span data-ttu-id="e0960-119">Créez un fichier dans le répertoire **./resources/views** nommé `calendar.blade.php` et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="e0960-119">Create a new file in the **./resources/views** directory named `calendar.blade.php` and add the following code.</span></span>

    :::code language="php" source="../demo/graph-tutorial/resources/views/calendar.blade.php" id="CalendarSnippet":::

    <span data-ttu-id="e0960-120">Cela permet de parcourir une collection d’événements et d’ajouter une ligne de tableau pour chacun d’eux.</span><span class="sxs-lookup"><span data-stu-id="e0960-120">That will loop through a collection of events and add a table row for each one.</span></span>

1. <span data-ttu-id="e0960-121">Mettez à jour les itinéraires **dans ./routes/web.php** pour ajouter des itinéraires pour `/calendar/new` .</span><span class="sxs-lookup"><span data-stu-id="e0960-121">Update the routes in **./routes/web.php** to add routes for `/calendar/new`.</span></span> <span data-ttu-id="e0960-122">Vous allez implémenter ces fonctions dans la section suivante, mais l’itinéraire doit être défini maintenant, car **calendar.blade.php** le référence.</span><span class="sxs-lookup"><span data-stu-id="e0960-122">You will implement these functions in the next section, but the route need to be defined now because **calendar.blade.php** references it.</span></span>

    ```php
    Route::get('/calendar/new', 'CalendarController@getNewEventForm');
    Route::post('/calendar/new', 'CalendarController@createNewEvent');
    ```

1. <span data-ttu-id="e0960-123">Supprimez `return response()->json($events);` la ligne de l’action dans `calendar` **./app/Http/Controllers/CalendarController.php** et remplacez-la par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="e0960-123">Remove the `return response()->json($events);` line from the `calendar` action in **./app/Http/Controllers/CalendarController.php**, and replace it with the following code.</span></span>

    ```php
    $viewData['events'] = $events;
    return view('calendar', $viewData);
    ```

1. <span data-ttu-id="e0960-124">Actualisez la page et l’application doit maintenant restituer une table des événements.</span><span class="sxs-lookup"><span data-stu-id="e0960-124">Refresh the page and the app should now render a table of events.</span></span>

    ![Capture d’écran du tableau des événements](./images/add-msgraph-01.png)
