<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="7a777-101">Commencez par créer un projet Laravel.</span><span class="sxs-lookup"><span data-stu-id="7a777-101">Begin by creating a new Laravel project.</span></span>

1. <span data-ttu-id="7a777-102">Ouvrez votre interface de ligne de commande( CLI), accédez à un répertoire dans lequel vous avez le droit de créer des fichiers et exécutez la commande suivante pour créer une application PHP.</span><span class="sxs-lookup"><span data-stu-id="7a777-102">Open your command-line interface (CLI), navigate to a directory where you have rights to create files, and run the following command to create a new PHP app.</span></span>

    ```Shell
    laravel new graph-tutorial
    ```

1. <span data-ttu-id="7a777-103">Accédez au répertoire **graph-tutorial** et entrez la commande suivante pour démarrer un serveur web local.</span><span class="sxs-lookup"><span data-stu-id="7a777-103">Navigate to the **graph-tutorial** directory and enter the following command to start a local web server.</span></span>

    ```Shell
    php artisan serve
    ```

1. <span data-ttu-id="7a777-104">Ouvrez votre navigateur et accédez à `http://localhost:8000`.</span><span class="sxs-lookup"><span data-stu-id="7a777-104">Open your browser and navigate to `http://localhost:8000`.</span></span> <span data-ttu-id="7a777-105">Si tout fonctionne, vous verrez une page Laravel par défaut.</span><span class="sxs-lookup"><span data-stu-id="7a777-105">If everything is working, you will see a default Laravel page.</span></span> <span data-ttu-id="7a777-106">Si cette page n’est pas consultable, consultez la [documentation Laravel.](https://laravel.com/docs/8.x)</span><span class="sxs-lookup"><span data-stu-id="7a777-106">If you don't see that page, check the [Laravel docs](https://laravel.com/docs/8.x).</span></span>

## <a name="install-packages"></a><span data-ttu-id="7a777-107">Installer des packages</span><span class="sxs-lookup"><span data-stu-id="7a777-107">Install packages</span></span>

<span data-ttu-id="7a777-108">Avant de passer à la suite, installez des packages supplémentaires que vous utiliserez ultérieurement :</span><span class="sxs-lookup"><span data-stu-id="7a777-108">Before moving on, install some additional packages that you will use later:</span></span>

- <span data-ttu-id="7a777-109">[oauth2-client pour](https://github.com/thephpleague/oauth2-client) la gestion des flux de jeton OAuth et de la signature.</span><span class="sxs-lookup"><span data-stu-id="7a777-109">[oauth2-client](https://github.com/thephpleague/oauth2-client) for handling sign-in and OAuth token flows.</span></span>
- <span data-ttu-id="7a777-110">[microsoft-graph pour](https://github.com/microsoftgraph/msgraph-sdk-php) effectuer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="7a777-110">[microsoft-graph](https://github.com/microsoftgraph/msgraph-sdk-php) for making calls to Microsoft Graph.</span></span>

1. <span data-ttu-id="7a777-111">Exécutez la commande suivante dans votre CLI.</span><span class="sxs-lookup"><span data-stu-id="7a777-111">Run the following command in your CLI.</span></span>

    ```Shell
    composer require league/oauth2-client microsoft/microsoft-graph
    ```

## <a name="design-the-app"></a><span data-ttu-id="7a777-112">Concevoir l’application</span><span class="sxs-lookup"><span data-stu-id="7a777-112">Design the app</span></span>

1. <span data-ttu-id="7a777-113">Créez un fichier dans le répertoire **./resources/views** nommé `layout.blade.php` et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="7a777-113">Create a new file in the **./resources/views** directory named `layout.blade.php` and add the following code.</span></span>

    :::code language="php" source="../demo/graph-tutorial/resources/views/layout.blade.php" id="LayoutSnippet":::

    <span data-ttu-id="7a777-114">Ce code ajoute [Bootstrap](http://getbootstrap.com/) pour la mise en forme simple et [Font Awesome](https://fontawesome.com/) pour certaines icônes simples.</span><span class="sxs-lookup"><span data-stu-id="7a777-114">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="7a777-115">Il définit également une disposition globale avec une barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="7a777-115">It also defines a global layout with a nav bar.</span></span>

1. <span data-ttu-id="7a777-116">Créez un répertoire dans le répertoire nommé, puis créez un fichier `./public` dans le répertoire nommé `css` `./public/css` `app.css` .</span><span class="sxs-lookup"><span data-stu-id="7a777-116">Create a new directory in the `./public` directory named `css`, then create a new file in the `./public/css` directory named `app.css`.</span></span> <span data-ttu-id="7a777-117">Ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="7a777-117">Add the following code.</span></span>

    :::code language="css" source="../demo/graph-tutorial/public/css/app.css":::

1. <span data-ttu-id="7a777-118">Ouvrez `./resources/views/welcome.blade.php` le fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="7a777-118">Open the `./resources/views/welcome.blade.php` file and replace its contents with the following.</span></span>

    :::code language="php" source="../demo/graph-tutorial/resources/views/welcome.blade.php" id="WelcomeSnippet":::

1. <span data-ttu-id="7a777-119">Mettez à jour la classe de base dans `Controller` **./app/Http/Controllers/Controller.php** en ajoutant la fonction suivante à la classe.</span><span class="sxs-lookup"><span data-stu-id="7a777-119">Update the base `Controller` class in **./app/Http/Controllers/Controller.php** by adding the following function to the class.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/Controller.php" id="LoadViewDataSnippet":::

1. <span data-ttu-id="7a777-120">Créez un fichier dans `./app/Http/Controllers` le répertoire nommé et `HomeController.php` ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="7a777-120">Create a new file in the `./app/Http/Controllers` directory named `HomeController.php` and add the following code.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/HomeController.php":::

1. <span data-ttu-id="7a777-121">Mettez à jour l’itinéraire `./routes/web.php` pour utiliser le nouveau contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7a777-121">Update the route in `./routes/web.php` to use the new controller.</span></span> <span data-ttu-id="7a777-122">Remplacez tout le contenu de ce fichier par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="7a777-122">Replace the entire contents of this file with the following.</span></span>

    ```php
    <?php

    use Illuminate\Support\Facades\Route;

    Route::get('/', 'HomeController@welcome');
    ```

1. <span data-ttu-id="7a777-123">Ouvrez **./app/Providers/RouteServiceProvider.php** et désapplémentez la `$namespace` déclaration.</span><span class="sxs-lookup"><span data-stu-id="7a777-123">Open **./app/Providers/RouteServiceProvider.php** and uncomment the `$namespace` declaration.</span></span>

    ```php
    /**
     * This namespace is applied to your controller routes.
     *
     * In addition, it is set as the URL generator's root namespace.
     *
     * @var string
     */
    protected $namespace = 'App\Http\Controllers';
    ```

1. <span data-ttu-id="7a777-124">Enregistrez toutes vos modifications et redémarrez le serveur.</span><span class="sxs-lookup"><span data-stu-id="7a777-124">Save all of your changes and restart the server.</span></span> <span data-ttu-id="7a777-125">L’application doit maintenant avoir une apparence très différente.</span><span class="sxs-lookup"><span data-stu-id="7a777-125">Now, the app should look very different.</span></span>

    ![Capture d’écran de la page d’accueil repensée](./images/create-app-01.png)
