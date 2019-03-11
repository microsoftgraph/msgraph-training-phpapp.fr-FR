<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez incorporer Microsoft Graph dans l'application. Pour cette application, vous allez utiliser la bibliothèque [Microsoft Graph](https://github.com/microsoftgraph/msgraph-sdk-php) pour passer des appels à Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Obtenir des événements de calendrier à partir d'Outlook

Nous allons commencer par ajouter un contrôleur pour l'affichage Calendrier. Créez un fichier dans le `./app/Http/Controllers` dossier nommé `CalendarController.php`et ajoutez le code suivant.

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

Examinez ce que fait ce code.

- L'URL qui sera appelée est `/v1.0/me/events`.
- Le `$select` paramètre limite les champs renvoyés pour chaque événement à ceux que l'affichage utilise réellement.
- Le `$orderby` paramètre trie les résultats en fonction de la date et de l'heure de leur création, avec l'élément le plus récent en premier.

Mettre à jour les `./routes/web.php` itinéraires dans pour ajouter un itinéraire à ce nouveau contrôleur

```php
Route::get('/calendar', 'CalendarController@calendar');
```

À présent, vous pouvez le tester. Connectez-vous, puis cliquez sur le lien **calendrier** dans la barre de navigation. Si tout fonctionne, vous devez voir un vidage JSON des événements sur le calendrier de l'utilisateur.

## <a name="display-the-results"></a>Afficher les résultats

À présent, vous pouvez ajouter une vue pour afficher les résultats de manière plus conviviale. Créez un fichier dans le `./resources/views` répertoire nommé `calendar.blade.php` et ajoutez le code suivant.

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

Cela permet d'exécuter une boucle dans une collection d'événements et d'ajouter une ligne de tableau pour chacun d'eux. Supprimez `return response()->json($events);` la ligne de `calendar` l'action `./app/Http/Controllers/CalendarController.php`dans et remplacez-la par le code suivant.

```php
$viewData['events'] = $events;
return view('calendar', $viewData);
```

Actualisez la page et l'application doit maintenant afficher un tableau d'événements.

![Capture d'écran du tableau des événements](./images/add-msgraph-01.png)