<!-- markdownlint-disable MD002 MD041 -->

Dans cette section, vous allez ajouter la possibilité de créer des événements sur le calendrier de l’utilisateur.

## <a name="create-new-event-form"></a>Créer un formulaire d’événement

1. Créez un fichier dans le répertoire **./resources/views** nommé `newevent.blade.php` et ajoutez le code suivant.

    :::code language="php" source="../demo/graph-tutorial/resources/views/newevent.blade.php" id="NewEventFormSnippet":::

## <a name="add-controller-actions"></a>Ajouter des actions de contrôleur

1. Ouvrez **./app/Http/Controllers/CalendarController.php** et ajoutez la fonction suivante pour restituer le formulaire.

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/CalendarController.php" id="getNewEventFormSnippet":::

1. Ajoutez la fonction suivante pour recevoir les données du formulaire lorsque l’utilisateur est soumis et créez un événement sur le calendrier de l’utilisateur.

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/CalendarController.php" id="createNewEventSnippet":::

    Prenez en compte ce que fait ce code.

    - Il convertit l’entrée de champ participants en un tableau d’objets de [participant](https://docs.microsoft.com/graph/api/resources/attendee?view=graph-rest-1.0) Graph.
    - Il crée un événement [à partir de l’entrée](https://docs.microsoft.com/graph/api/resources/event?view=graph-rest-1.0) de formulaire.
    - Il envoie une publication au point `/me/events` de terminaison, puis redirige vers l’affichage Calendrier.

1. Enregistrez toutes vos modifications et redémarrez le serveur. Utilisez le **bouton Nouvel événement** pour accéder au nouveau formulaire d’événement.

1. Remplissez les valeurs du formulaire. Utilisez une date de début à partir de la semaine en cours. Sélectionnez **Créer**.

    ![Capture d’écran du nouveau formulaire d’événement](images/create-event-01.png)

1. Lorsque l’application redirige vers l’affichage Calendrier, vérifiez que votre nouvel événement est présent dans les résultats.
