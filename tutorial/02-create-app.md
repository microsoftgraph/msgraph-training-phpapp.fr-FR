<!-- markdownlint-disable MD002 MD041 -->

Commencez par créer un projet Laravel.

1. Ouvrez votre interface de ligne de commande( CLI), accédez à un répertoire dans lequel vous avez le droit de créer des fichiers et exécutez la commande suivante pour créer une application PHP.

    ```Shell
    laravel new graph-tutorial
    ```

1. Accédez au répertoire **graph-tutorial** et entrez la commande suivante pour démarrer un serveur web local.

    ```Shell
    php artisan serve
    ```

1. Ouvrez votre navigateur et accédez à `http://localhost:8000`. Si tout fonctionne, vous verrez une page Laravel par défaut. Si cette page n’est pas consultable, consultez la [documentation Laravel.](https://laravel.com/docs/8.x)

## <a name="install-packages"></a>Installer des packages

Avant de passer à la suite, installez des packages supplémentaires que vous utiliserez ultérieurement :

- [oauth2-client pour](https://github.com/thephpleague/oauth2-client) la gestion des flux de jeton OAuth et de la signature.
- [microsoft-graph pour](https://github.com/microsoftgraph/msgraph-sdk-php) effectuer des appels à Microsoft Graph.

1. Exécutez la commande suivante dans votre CLI.

    ```Shell
    composer require league/oauth2-client microsoft/microsoft-graph
    ```

## <a name="design-the-app"></a>Concevoir l’application

1. Créez un fichier dans le répertoire **./resources/views** nommé `layout.blade.php` et ajoutez le code suivant.

    :::code language="php" source="../demo/graph-tutorial/resources/views/layout.blade.php" id="LayoutSnippet":::

    Ce code ajoute [Bootstrap](http://getbootstrap.com/) pour la mise en forme simple et [Font Awesome](https://fontawesome.com/) pour certaines icônes simples. Il définit également une disposition globale avec une barre de navigation.

1. Créez un répertoire dans le répertoire nommé, puis créez un fichier `./public` dans le répertoire nommé `css` `./public/css` `app.css` . Ajoutez le code suivant.

    :::code language="css" source="../demo/graph-tutorial/public/css/app.css":::

1. Ouvrez `./resources/views/welcome.blade.php` le fichier et remplacez son contenu par ce qui suit.

    :::code language="php" source="../demo/graph-tutorial/resources/views/welcome.blade.php" id="WelcomeSnippet":::

1. Mettez à jour la classe de base dans `Controller` **./app/Http/Controllers/Controller.php** en ajoutant la fonction suivante à la classe.

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/Controller.php" id="LoadViewDataSnippet":::

1. Créez un fichier dans `./app/Http/Controllers` le répertoire nommé et `HomeController.php` ajoutez le code suivant.

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/HomeController.php":::

1. Mettez à jour l’itinéraire `./routes/web.php` pour utiliser le nouveau contrôleur. Remplacez tout le contenu de ce fichier par ce qui suit.

    ```php
    <?php

    use Illuminate\Support\Facades\Route;

    Route::get('/', 'HomeController@welcome');
    ```

1. Ouvrez **./app/Providers/RouteServiceProvider.php** et désapplémentez la `$namespace` déclaration.

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

1. Enregistrez toutes vos modifications et redémarrez le serveur. L’application doit maintenant avoir une apparence très différente.

    ![Capture d’écran de la page d’accueil repensée](./images/create-app-01.png)
