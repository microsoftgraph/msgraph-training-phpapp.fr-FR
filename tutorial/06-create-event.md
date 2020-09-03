<!-- markdownlint-disable MD002 MD041 -->

Dans cette section, vous allez ajouter la possibilité de créer des événements dans le calendrier de l’utilisateur.

## <a name="create-new-event-form"></a>Créer un formulaire d’événement

1. Créez un fichier dans le répertoire **./Resources/views** nommé `newevent.blade.php` et ajoutez le code suivant.

    :::code language="php" source="../demo/graph-tutorial/resources/views/newevent.blade.php" id="NewEventFormSnippet":::

## <a name="add-controller-actions"></a>Ajouter des actions de contrôleur

1. Ouvrez **./app/http/Controllers/CalendarController.php** et ajoutez la fonction suivante pour afficher le formulaire.

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/CalendarController.php" id="getNewEventFormSnippet":::

1. Ajoutez la fonction suivante pour recevoir les données du formulaire lors de l’envoi de l’utilisateur et créer un événement dans le calendrier de l’utilisateur.

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/CalendarController.php" id="createNewEventSnippet":::

    Examinez ce que fait ce code.

    - Il convertit l’entrée de champ participants en tableau d’objets [participants](https://docs.microsoft.com/graph/api/resources/attendee?view=graph-rest-1.0) Graph.
    - Il génère un [événement](https://docs.microsoft.com/graph/api/resources/event?view=graph-rest-1.0) à partir de l’entrée de formulaire.
    - Il envoie un billet au `/me/events` point de terminaison, puis redirige vers l’affichage Calendrier.

1. Mettez à jour les itinéraires dans **./routes/Web.php** pour ajouter des itinéraires pour ces nouvelles fonctions sur le contrôleur.

    ```php
    Route::get('/calendar/new', 'CalendarController@getNewEventForm');
    Route::post('/calendar/new', 'CalendarController@createNewEvent');
    ```

1. Enregistrez toutes vos modifications et redémarrez le serveur. Utilisez le bouton **nouvel événement** pour accéder au nouveau formulaire d’événement.

1. Renseignez les valeurs du formulaire. Utiliser une date de début à partir de la semaine en cours. Sélectionnez **Créer**.

    ![Capture d’écran du nouveau formulaire d’événement](images/create-event-01.png)

1. Lorsque l’application redirige vers l’affichage Calendrier, vérifiez que votre nouvel événement est présent dans les résultats.
