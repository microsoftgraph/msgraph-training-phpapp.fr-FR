<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="2a285-101">Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application.</span><span class="sxs-lookup"><span data-stu-id="2a285-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="2a285-102">Pour cette application, vous allez utiliser la bibliothèque [Microsoft Graph](https://github.com/microsoftgraph/msgraph-sdk-php) pour passer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="2a285-102">For this application, you will use the [microsoft-graph](https://github.com/microsoftgraph/msgraph-sdk-php) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="2a285-103">Obtenir des événements de calendrier à partir d’Outlook</span><span class="sxs-lookup"><span data-stu-id="2a285-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="2a285-104">Nous allons commencer par ajouter un contrôleur pour l’affichage Calendrier.</span><span class="sxs-lookup"><span data-stu-id="2a285-104">Let's start by adding a controller for the calendar view.</span></span> <span data-ttu-id="2a285-105">Créez un fichier dans le `./app/Http/Controllers` dossier nommé `CalendarController.php`et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="2a285-105">Create a new file in the `./app/Http/Controllers` folder named `CalendarController.php`, and add the following code.</span></span>

```php
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;
use Microsoft\Graph\Graph;
use Microsoft\Graph\Model;
use App\TokenStore\TokenCache;

class CalendarController extends Controller
{
  public function calendar()
  {
    $viewData = $this->loadViewData();

    // Get the access token from the cache
    $tokenCache = new TokenCache();
    $accessToken = $tokenCache->getAccessToken();

    // Create a Graph client
    $graph = new Graph();
    $graph->setAccessToken($accessToken);

    $queryParams = array(
      '$select' => 'subject,organizer,start,end',
      '$orderby' => 'createdDateTime DESC'
    );

    // Append query parameters to the '/me/events' url
    $getEventsUrl = '/me/events?'.http_build_query($queryParams);

    $events = $graph->createRequest('GET', $getEventsUrl)
      ->setReturnType(Model\Event::class)
      ->execute();

    return response()->json($events);
  }
}
```

<span data-ttu-id="2a285-106">Examinez ce que fait ce code.</span><span class="sxs-lookup"><span data-stu-id="2a285-106">Consider what this code is doing.</span></span>

- <span data-ttu-id="2a285-107">L’URL qui sera appelée est `/v1.0/me/events`.</span><span class="sxs-lookup"><span data-stu-id="2a285-107">The URL that will be called is `/v1.0/me/events`.</span></span>
- <span data-ttu-id="2a285-108">Le `$select` paramètre limite les champs renvoyés pour chaque événement à ceux que l’affichage utilise réellement.</span><span class="sxs-lookup"><span data-stu-id="2a285-108">The `$select` parameter limits the fields returned for each events to just those the view will actually use.</span></span>
- <span data-ttu-id="2a285-109">Le `$orderby` paramètre trie les résultats en fonction de la date et de l’heure de leur création, avec l’élément le plus récent en premier.</span><span class="sxs-lookup"><span data-stu-id="2a285-109">The `$orderby` parameter sorts the results by the date and time they were created, with the most recent item being first.</span></span>

<span data-ttu-id="2a285-110">Mettre à jour les `./routes/web.php` itinéraires dans pour ajouter un itinéraire à ce nouveau contrôleur</span><span class="sxs-lookup"><span data-stu-id="2a285-110">Update the routes in `./routes/web.php` to add a route to this new controller</span></span>

```php
Route::get('/calendar', 'CalendarController@calendar');
```

<span data-ttu-id="2a285-111">À présent, vous pouvez le tester.</span><span class="sxs-lookup"><span data-stu-id="2a285-111">Now you can test this.</span></span> <span data-ttu-id="2a285-112">Connectez-vous, puis cliquez sur le lien **calendrier** dans la barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="2a285-112">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="2a285-113">Si tout fonctionne, vous devez voir un vidage JSON des événements sur le calendrier de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="2a285-113">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="2a285-114">Afficher les résultats</span><span class="sxs-lookup"><span data-stu-id="2a285-114">Display the results</span></span>

<span data-ttu-id="2a285-115">À présent, vous pouvez ajouter une vue pour afficher les résultats de manière plus conviviale.</span><span class="sxs-lookup"><span data-stu-id="2a285-115">Now you can add a view to display the results in a more user-friendly manner.</span></span> <span data-ttu-id="2a285-116">Créez un fichier dans le `./resources/views` répertoire nommé `calendar.blade.php` et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="2a285-116">Create a new file in the `./resources/views` directory named `calendar.blade.php` and add the following code.</span></span>

```php
@extends('layout')

@section('content')
<h1>Calendar</h1>
<table class="table">
  <thead>
    <tr>
      <th scope="col">Organizer</th>
      <th scope="col">Subject</th>
      <th scope="col">Start</th>
      <th scope="col">End</th>
    </tr>
  </thead>
  <tbody>
    @isset($events)
      @foreach($events as $event)
        <tr>
          <td>{{ $event->getOrganizer()->getEmailAddress()->getName() }}</td>
          <td>{{ $event->getSubject() }}</td>
          <td>{{ \Carbon\Carbon::parse($event->getStart()->getDateTime())->format('n/j/y g:i A') }}</td>
          <td>{{ \Carbon\Carbon::parse($event->getEnd()->getDateTime())->format('n/j/y g:i A') }}</td>
        </tr>
      @endforeach
    @endif
  </tbody>
</table>
@endsection
```

<span data-ttu-id="2a285-117">Cela permet d’exécuter une boucle dans une collection d’événements et d’ajouter une ligne de tableau pour chacun d’eux.</span><span class="sxs-lookup"><span data-stu-id="2a285-117">That will loop through a collection of events and add a table row for each one.</span></span> <span data-ttu-id="2a285-118">Supprimez `return response()->json($events);` la ligne de `calendar` l’action `./app/Http/Controllers/CalendarController.php`dans et remplacez-la par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="2a285-118">Remove the `return response()->json($events);` line from the `calendar` action in `./app/Http/Controllers/CalendarController.php`, and replace it with the following code.</span></span>

```php
$viewData['events'] = $events;
return view('calendar', $viewData);
```

<span data-ttu-id="2a285-119">Actualisez la page et l’application doit maintenant afficher un tableau d’événements.</span><span class="sxs-lookup"><span data-stu-id="2a285-119">Refresh the page and the app should now render a table of events.</span></span>

![Capture d’écran du tableau des événements](./images/add-msgraph-01.png)