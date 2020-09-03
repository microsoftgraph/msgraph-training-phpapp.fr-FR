<!-- markdownlint-disable MD002 MD041 -->

Commencez par créer un nouveau projet Laravel.

1. Ouvrez votre interface de ligne de commande (CLI), accédez à un répertoire où vous disposez de droits pour créer des fichiers, puis exécutez la commande suivante pour créer une application PHP.

    ```Shell
    laravel new graph-tutorial
    ```

1. Accédez au répertoire du **didacticiel Graph** et entrez la commande suivante pour démarrer un serveur Web local.

    ```Shell
    php artisan serve
    ```

1. Ouvrez votre navigateur et accédez à `http://localhost:8000`. Si tout fonctionne, vous verrez une page Laravel par défaut. Si vous ne voyez pas cette page, consultez les [docs Laravel](https://laravel.com/docs/7.x).

## <a name="install-packages"></a>Installer des packages

Avant de poursuivre, installez des packages supplémentaires que vous utiliserez plus tard :

- [oauth2-client pour le](https://github.com/thephpleague/oauth2-client) traitement des flux de connexion et de jetons OAuth.
- [Microsoft-Graph](https://github.com/microsoftgraph/msgraph-sdk-php) pour effectuer des appels à Microsoft Graph.

1. Exécutez la commande suivante pour supprimer la version existante de `guzzlehttp/guzzle` . La version installée par Laravel est en conflit avec la version requise par le kit de développement logiciel (SDK) Microsoft Graph PHP.

    ```Shell
    composer remove guzzlehttp/guzzle
    ```

1. Exécutez la commande suivante dans votre interface CLI.

    ```Shell
    composer require league/oauth2-client microsoft/microsoft-graph
    ```

## <a name="design-the-app"></a>Concevoir l’application

1. Créez un fichier dans le répertoire **./Resources/views** nommé `layout.blade.php` et ajoutez le code suivant.

    :::code language="php" source="../demo/graph-tutorial/resources/views/layout.blade.php" id="LayoutSnippet":::

    Ce code ajoute [Bootstrap](http://getbootstrap.com/) pour la mise en forme simple et [Font Awesome](https://fontawesome.com/) pour certaines icônes simples. Il définit également une disposition globale avec une barre de navigation.

1. Créez un répertoire dans le `./public` répertoire nommé `css` , puis créez un fichier dans le `./public/css` répertoire nommé `app.css` . Ajoutez le code suivant.

    :::code language="css" source="../demo/graph-tutorial/public/css/app.css":::

1. Ouvrez le `./resources/views/welcome.blade.php` fichier et remplacez son contenu par ce qui suit.

    :::code language="php" source="../demo/graph-tutorial/resources/views/welcome.blade.php" id="WelcomeSnippet":::

1. Mettez à jour la `Controller` classe de base dans **./app/http/Controllers/Controller.php** en ajoutant la fonction suivante à la classe.

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/Controller.php" id="LoadViewDataSnippet":::

1. Créez un fichier dans le `./app/Http/Controllers` répertoire nommé `HomeController.php` et ajoutez le code suivant.

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/HomeController.php":::

1. Mettez à jour l’itinéraire dans `./routes/web.php` pour utiliser le nouveau contrôleur. Remplacez tout le contenu de ce fichier par ce qui suit.

    ```php
    <?php

    use Illuminate\Support\Facades\Route;

    Route::get('/', 'HomeController@welcome');
    ```

1. Enregistrez toutes vos modifications et redémarrez le serveur. À présent, l’application doit être très différente.

    ![Capture d’écran de la page d’accueil repensée](./images/create-app-01.png)
