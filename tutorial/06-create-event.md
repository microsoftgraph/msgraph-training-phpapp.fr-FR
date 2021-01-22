<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="17d95-101">Dans cette section, vous allez ajouter la possibilité de créer des événements sur le calendrier de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="17d95-101">In this section you will add the ability to create events on the user's calendar.</span></span>

## <a name="create-new-event-form"></a><span data-ttu-id="17d95-102">Créer un formulaire d’événement</span><span class="sxs-lookup"><span data-stu-id="17d95-102">Create new event form</span></span>

1. <span data-ttu-id="17d95-103">Créez un fichier dans le répertoire **./resources/views** nommé `newevent.blade.php` et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="17d95-103">Create a new file in the **./resources/views** directory named `newevent.blade.php` and add the following code.</span></span>

    :::code language="php" source="../demo/graph-tutorial/resources/views/newevent.blade.php" id="NewEventFormSnippet":::

## <a name="add-controller-actions"></a><span data-ttu-id="17d95-104">Ajouter des actions de contrôleur</span><span class="sxs-lookup"><span data-stu-id="17d95-104">Add controller actions</span></span>

1. <span data-ttu-id="17d95-105">Ouvrez **./app/Http/Controllers/CalendarController.php** et ajoutez la fonction suivante pour restituer le formulaire.</span><span class="sxs-lookup"><span data-stu-id="17d95-105">Open **./app/Http/Controllers/CalendarController.php** and add the following function to render the form.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/CalendarController.php" id="getNewEventFormSnippet":::

1. <span data-ttu-id="17d95-106">Ajoutez la fonction suivante pour recevoir les données du formulaire lorsque l’utilisateur est soumis et créez un événement sur le calendrier de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="17d95-106">Add the following function to receive the form data when the user's submits, and create a new event on the user's calendar.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/CalendarController.php" id="createNewEventSnippet":::

    <span data-ttu-id="17d95-107">Prenez en compte ce que fait ce code.</span><span class="sxs-lookup"><span data-stu-id="17d95-107">Consider what this code does.</span></span>

    - <span data-ttu-id="17d95-108">Il convertit l’entrée de champ participants en un tableau d’objets de [participant](https://docs.microsoft.com/graph/api/resources/attendee?view=graph-rest-1.0) Graph.</span><span class="sxs-lookup"><span data-stu-id="17d95-108">It converts the attendees field input to an array of Graph [attendee](https://docs.microsoft.com/graph/api/resources/attendee?view=graph-rest-1.0) objects.</span></span>
    - <span data-ttu-id="17d95-109">Il crée un événement [à partir de l’entrée](https://docs.microsoft.com/graph/api/resources/event?view=graph-rest-1.0) de formulaire.</span><span class="sxs-lookup"><span data-stu-id="17d95-109">It builds an [event](https://docs.microsoft.com/graph/api/resources/event?view=graph-rest-1.0) from the form input.</span></span>
    - <span data-ttu-id="17d95-110">Il envoie une publication au point `/me/events` de terminaison, puis redirige vers l’affichage Calendrier.</span><span class="sxs-lookup"><span data-stu-id="17d95-110">It sends a POST to the `/me/events` endpoint, then redirects back to the calendar view.</span></span>

1. <span data-ttu-id="17d95-111">Enregistrez toutes vos modifications et redémarrez le serveur.</span><span class="sxs-lookup"><span data-stu-id="17d95-111">Save all of your changes and restart the server.</span></span> <span data-ttu-id="17d95-112">Utilisez le **bouton Nouvel événement** pour accéder au nouveau formulaire d’événement.</span><span class="sxs-lookup"><span data-stu-id="17d95-112">Use the **New event** button to navigate to the new event form.</span></span>

1. <span data-ttu-id="17d95-113">Remplissez les valeurs du formulaire.</span><span class="sxs-lookup"><span data-stu-id="17d95-113">Fill in the values on the form.</span></span> <span data-ttu-id="17d95-114">Utilisez une date de début à partir de la semaine en cours.</span><span class="sxs-lookup"><span data-stu-id="17d95-114">Use a start date from the current week.</span></span> <span data-ttu-id="17d95-115">Sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="17d95-115">Select **Create**.</span></span>

    ![Capture d’écran du nouveau formulaire d’événement](images/create-event-01.png)

1. <span data-ttu-id="17d95-117">Lorsque l’application redirige vers l’affichage Calendrier, vérifiez que votre nouvel événement est présent dans les résultats.</span><span class="sxs-lookup"><span data-stu-id="17d95-117">When the app redirects to the calendar view, verify that your new event is present in the results.</span></span>
