<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD. Cette étape est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph. Dans cette étape, vous allez intégrer la [bibliothèque oauth2-client](https://github.com/thephpleague/oauth2-client) dans l’application.

1. Ouvrez **le fichier .env** à la racine de votre application PHP et ajoutez le code suivant à la fin du fichier.

    :::code language="ini" source="../demo/graph-tutorial/example.env" range="48-54":::

1. Remplacez-le par l’ID de l’application à partir du portail d’inscription des applications et par le `YOUR_APP_ID_HERE` mot de passe que vous avez `YOUR_APP_PASSWORD_HERE` généré.

    > [!IMPORTANT]
    > Si vous utilisez un contrôle source tel que Git, il est temps d’exclure le fichier du contrôle source afin d’éviter toute fuite accidentelle de votre ID d’application et de votre mot de `.env` passe.

## <a name="implement-sign-in"></a>Implémentation de la connexion

1. Créez un fichier dans le répertoire **./app/Http/Controllers** nommé `AuthController.php` et ajoutez le code suivant.

    ```php
    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Http\Request;

    class AuthController extends Controller
    {
      public function signin()
      {
        // Initialize the OAuth client
        $oauthClient = new \League\OAuth2\Client\Provider\GenericProvider([
          'clientId'                => env('OAUTH_APP_ID'),
          'clientSecret'            => env('OAUTH_APP_PASSWORD'),
          'redirectUri'             => env('OAUTH_REDIRECT_URI'),
          'urlAuthorize'            => env('OAUTH_AUTHORITY').env('OAUTH_AUTHORIZE_ENDPOINT'),
          'urlAccessToken'          => env('OAUTH_AUTHORITY').env('OAUTH_TOKEN_ENDPOINT'),
          'urlResourceOwnerDetails' => '',
          'scopes'                  => env('OAUTH_SCOPES')
        ]);

        $authUrl = $oauthClient->getAuthorizationUrl();

        // Save client state so we can validate in callback
        session(['oauthState' => $oauthClient->getState()]);

        // Redirect to AAD signin page
        return redirect()->away($authUrl);
      }

      public function callback(Request $request)
      {
        // Validate state
        $expectedState = session('oauthState');
        $request->session()->forget('oauthState');
        $providedState = $request->query('state');

        if (!isset($expectedState)) {
          // If there is no expected state in the session,
          // do nothing and redirect to the home page.
          return redirect('/');
        }

        if (!isset($providedState) || $expectedState != $providedState) {
          return redirect('/')
            ->with('error', 'Invalid auth state')
            ->with('errorDetail', 'The provided auth state did not match the expected value');
        }

        // Authorization code should be in the "code" query param
        $authCode = $request->query('code');
        if (isset($authCode)) {
          // Initialize the OAuth client
          $oauthClient = new \League\OAuth2\Client\Provider\GenericProvider([
            'clientId'                => env('OAUTH_APP_ID'),
            'clientSecret'            => env('OAUTH_APP_PASSWORD'),
            'redirectUri'             => env('OAUTH_REDIRECT_URI'),
            'urlAuthorize'            => env('OAUTH_AUTHORITY').env('OAUTH_AUTHORIZE_ENDPOINT'),
            'urlAccessToken'          => env('OAUTH_AUTHORITY').env('OAUTH_TOKEN_ENDPOINT'),
            'urlResourceOwnerDetails' => '',
            'scopes'                  => env('OAUTH_SCOPES')
          ]);

          try {
            // Make the token request
            $accessToken = $oauthClient->getAccessToken('authorization_code', [
              'code' => $authCode
            ]);

            // TEMPORARY FOR TESTING!
            return redirect('/')
              ->with('error', 'Access token received')
              ->with('errorDetail', $accessToken->getToken());
          }
          catch (League\OAuth2\Client\Provider\Exception\IdentityProviderException $e) {
            return redirect('/')
              ->with('error', 'Error requesting access token')
              ->with('errorDetail', $e->getMessage());
          }
        }

        return redirect('/')
          ->with('error', $request->query('error'))
          ->with('errorDetail', $request->query('error_description'));
      }
    }
    ```

    Cela définit un contrôleur avec deux actions : `signin` et `callback` .

    L’action génère l’URL de la signature Azure AD, enregistre la valeur générée par le `signin` client OAuth, puis redirige le navigateur vers la page de signature `state` Azure AD.

    `callback`L’action est l’endroit où Azure redirige une fois la signature terminée. Cette action permet de s’assurer que la valeur correspond à la valeur enregistrée, puis d’utilisateurs le code d’autorisation envoyé par `state` Azure pour demander un jeton d’accès. Il redirige ensuite vers la page d’accueil avec le jeton d’accès dans la valeur d’erreur temporaire. Vous l’utiliserez pour vérifier que la connectez-vous fonctionne avant de passer à autre chose.

1. Ajoutez les itinéraires **à ./routes/web.php**.

    ```php
    Route::get('/signin', 'AuthController@signin');
    Route::get('/callback', 'AuthController@callback');
    ```

1. Démarrez le serveur et accédez à `https://localhost:8000` . Cliquez sur le bouton de connexion. vous serez redirigé vers `https://login.microsoftonline.com`. Connectez-vous avec votre compte Microsoft.

1. Examinez l’invite de consentement. La liste des autorisations correspond à la liste des étendues d’autorisations configurées dans **.env**.

    - **Conservez l’accès aux** données à qui vous avez accordé l’accès : ( ) Cette autorisation est demandée par MSAL afin de récupérer les jetons `offline_access` d’actualisation.
    - **Connectez-vous et lisez votre** profil : ( ) Cette autorisation permet à l’application d’obtenir le profil et la photo de profil de `User.Read` l’utilisateur connecté.
    - **Lisez les paramètres de votre** boîte aux lettres : ( ) Cette autorisation permet à l’application de lire les paramètres de boîte aux lettres de l’utilisateur, y compris le fuseau horaire et `MailboxSettings.Read` le format horaire.
    - **Avoir un accès total à** vos calendriers : ( ) Cette autorisation permet à l’application de lire des événements sur le calendrier de l’utilisateur, d’ajouter de nouveaux événements et de modifier des `Calendars.ReadWrite` événements existants.

1. Consentement aux autorisations demandées. Le navigateur vous redirige vers l’application, affichant le jeton.

### <a name="get-user-details"></a>Obtenir les détails de l’utilisateur

Dans cette section, vous allez mettre à jour la méthode pour obtenir `callback` le profil de l’utilisateur à partir de Microsoft Graph.

1. Ajoutez les instructions suivantes en haut de `use` **/app/Http/Controllers/AuthController.php**, sous la `namespace App\Http\Controllers;` ligne.

    ```php
    use Microsoft\Graph\Graph;
    use Microsoft\Graph\Model;
    ```

1. Remplacez `try` le bloc dans la méthode par le code `callback` suivant.

    ```php
    try {
      // Make the token request
      $accessToken = $oauthClient->getAccessToken('authorization_code', [
        'code' => $authCode
      ]);

      $graph = new Graph();
      $graph->setAccessToken($accessToken->getToken());

      $user = $graph->createRequest('GET', '/me?$select=displayName,mail,mailboxSettings,userPrincipalName')
        ->setReturnType(Model\User::class)
        ->execute();

      // TEMPORARY FOR TESTING!
      return redirect('/')
        ->with('error', 'Access token received')
        ->with('errorDetail', 'User:'.$user->getDisplayName().', Token:'.$accessToken->getToken());
    }
    ```

Le nouveau code crée un `Graph` objet, affecte le jeton d’accès, puis l’utilise pour demander le profil de l’utilisateur. Il ajoute le nom complet de l’utilisateur à la sortie temporaire pour le test.

## <a name="storing-the-tokens"></a>Stockage des jetons

Maintenant que vous pouvez obtenir des jetons, nous vous conseillons d’implémenter un moyen de les stocker dans l’application. Comme il s’agit d’un exemple d’application, par souci de simplicité, vous les stockerez dans la session. Une application réelle utilise une solution de stockage sécurisé plus fiable, comme une base de données.

1. Créez un répertoire dans le répertoire **./app** nommé, puis créez un fichier dans ce répertoire nommé, puis ajoutez `TokenStore` le code `TokenCache.php` suivant.

    ```php
    <?php

    namespace App\TokenStore;

    class TokenCache {
      public function storeTokens($accessToken, $user) {
        session([
          'accessToken' => $accessToken->getToken(),
          'refreshToken' => $accessToken->getRefreshToken(),
          'tokenExpires' => $accessToken->getExpires(),
          'userName' => $user->getDisplayName(),
          'userEmail' => null !== $user->getMail() ? $user->getMail() : $user->getUserPrincipalName(),
          'userTimeZone' => $user->getMailboxSettings()->getTimeZone()
        ]);
      }

      public function clearTokens() {
        session()->forget('accessToken');
        session()->forget('refreshToken');
        session()->forget('tokenExpires');
        session()->forget('userName');
        session()->forget('userEmail');
        session()->forget('userTimeZone');
      }

      public function getAccessToken() {
        // Check if tokens exist
        if (empty(session('accessToken')) ||
            empty(session('refreshToken')) ||
            empty(session('tokenExpires'))) {
          return '';
        }

        return session('accessToken');
      }
    }
    ```

1. Ajoutez l’instruction suivante en haut de `use` **./app/Http/Controllers/AuthController.php**, sous la `namespace App\Http\Controllers;` ligne.

    ```php
    use App\TokenStore\TokenCache;
    ```

1. Remplacez `try` le bloc dans la fonction existante par ce qui `callback` suit.

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/AuthController.php" id="StoreTokensSnippet":::

## <a name="implement-sign-out"></a>Implémenter la signature

Avant de tester cette nouvelle fonctionnalité, ajoutez un moyen de vous en sortir.

1. Ajoutez l’action suivante à la `AuthController` classe.

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/AuthController.php" id="SignOutSnippet":::

1. Ajoutez cette action **à ./routes/web.php**.

    ```php
    Route::get('/signout', 'AuthController@signout');
    ```

1. Redémarrez le serveur et traversez le processus de sign-in. Vous devez revenir sur la page d’accueil, mais l’interface utilisateur doit changer pour indiquer que vous êtes en cours de signature.

    ![Capture d’écran de la page d’accueil après la connexion](./images/add-aad-auth-01.png)

1. Cliquez sur l’avatar de l’utilisateur dans le coin supérieur droit pour accéder au lien **de** connexion. Le fait de cliquer sur **Se déconnecter** réinitialise la session et vous ramène à la page d’accueil.

    ![Capture d’écran du menu déroulant avec le lien de déconnexion](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Actualisation des jetons

À ce stade, votre application dispose d’un jeton d’accès, qui est envoyé dans l’en-tête des `Authorization` appels d’API. Il s’agit du jeton qui permet à l’application d’accéder à Microsoft Graph au nom de l’utilisateur.

Cependant, ce jeton est de courte durée. Le jeton expire une heure après son émission. C’est là que le jeton d’actualisation devient utile. Le jeton d’actualisation permet à l’application de demander un nouveau jeton d’accès sans obliger l’utilisateur à se reconnecter. Mettez à jour le code de gestion des jetons pour implémenter l’actualisation du jeton.

1. Ouvrez **./app/TokenStore/TokenCache.php** et ajoutez la fonction suivante à la `TokenCache` classe.

    :::code language="php" source="../demo/graph-tutorial/app/TokenStore/TokenCache.php" id="UpdateTokensSnippet":::

1. Remplacez la fonction `getAccessToken` existante par ce qui suit.

    :::code language="php" source="../demo/graph-tutorial/app/TokenStore/TokenCache.php" id="GetAccessTokenSnippet":::

Cette méthode vérifie d’abord si le jeton d’accès a expiré ou arrive à expiration. Si c’est le cas, il utilise le jeton d’actualisation pour obtenir de nouveaux jetons, puis met à jour le cache et renvoie le nouveau jeton d’accès.
