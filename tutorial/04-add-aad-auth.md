<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="f96c8-101">Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD.</span><span class="sxs-lookup"><span data-stu-id="f96c8-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="f96c8-102">Cela est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="f96c8-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="f96c8-103">Dans cette étape, vous allez intégrer la bibliothèque [oauth2-cliente](https://github.com/thephpleague/oauth2-client) dans l’application.</span><span class="sxs-lookup"><span data-stu-id="f96c8-103">In this step you will integrate the [oauth2-client](https://github.com/thephpleague/oauth2-client) library into the application.</span></span>

1. <span data-ttu-id="f96c8-104">Ouvrez le `.env` fichier à la racine de votre application php et ajoutez le code suivant à la fin du fichier.</span><span class="sxs-lookup"><span data-stu-id="f96c8-104">Open the `.env` file in the root of your PHP application, and add the following code to the end of the file.</span></span>

    :::code language="ini" source="../demo/graph-tutorial/.env.example" id="OAuthSettingsSnippet":::

1. <span data-ttu-id="f96c8-105">Remplacez `YOUR_APP_ID_HERE` par l’ID de l’application dans le portail d’inscription de `YOUR_APP_PASSWORD_HERE` l’application et remplacez par le mot de passe que vous avez généré.</span><span class="sxs-lookup"><span data-stu-id="f96c8-105">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_PASSWORD_HERE` with the password you generated.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="f96c8-106">Si vous utilisez le contrôle de code source tel que git, il est maintenant recommandé d’exclure le `.env` fichier du contrôle de code source afin d’éviter une fuite accidentelle de votre ID d’application et de votre mot de passe.</span><span class="sxs-lookup"><span data-stu-id="f96c8-106">If you're using source control such as git, now would be a good time to exclude the `.env` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="f96c8-107">Implémentation de la connexion</span><span class="sxs-lookup"><span data-stu-id="f96c8-107">Implement sign-in</span></span>

1. <span data-ttu-id="f96c8-108">Créez un fichier dans le répertoire **./app/http/Controllers** nommé `AuthController.php` et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="f96c8-108">Create a new file in the **./app/Http/Controllers** directory named `AuthController.php` and add the following code.</span></span>

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

    <span data-ttu-id="f96c8-109">Cela définit un contrôleur avec deux actions : `signin` et `callback`.</span><span class="sxs-lookup"><span data-stu-id="f96c8-109">This defines a controller with two actions: `signin` and `callback`.</span></span>

    <span data-ttu-id="f96c8-110">L' `signin` action génère l’URL de connexion Azure ad, enregistre la `state` valeur générée par le client OAuth, puis redirige le navigateur vers la page de connexion Azure ad.</span><span class="sxs-lookup"><span data-stu-id="f96c8-110">The `signin` action generates the Azure AD signin URL, saves the `state` value generated by the OAuth client, then redirects the browser to the Azure AD signin page.</span></span>

    <span data-ttu-id="f96c8-111">L' `callback` action est l’endroit où Azure redirige une fois la connexion terminée.</span><span class="sxs-lookup"><span data-stu-id="f96c8-111">The `callback` action is where Azure redirects after the signin is complete.</span></span> <span data-ttu-id="f96c8-112">Cette action permet de s' `state` assurer que la valeur correspond à la valeur enregistrée, puis aux utilisateurs le code d’autorisation envoyé par Azure pour demander un jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="f96c8-112">That action makes sure the `state` value matches the saved value, then users the authorization code sent by Azure to request an access token.</span></span> <span data-ttu-id="f96c8-113">Il redirige ensuite vers la page d’accueil avec le jeton d’accès dans la valeur d’erreur temporaire.</span><span class="sxs-lookup"><span data-stu-id="f96c8-113">It then redirects back to the home page with the access token in the temporary error value.</span></span> <span data-ttu-id="f96c8-114">Vous l’utiliserez pour vérifier que la connexion fonctionne avant de poursuivre.</span><span class="sxs-lookup"><span data-stu-id="f96c8-114">You'll use this to verify that sign-in is working before moving on.</span></span>

1. <span data-ttu-id="f96c8-115">Ajoutez les itinéraires à **./routes/Web.php**.</span><span class="sxs-lookup"><span data-stu-id="f96c8-115">Add the routes to **./routes/web.php**.</span></span>

    ```php
    Route::get('/signin', 'AuthController@signin');
    Route::get('/callback', 'AuthController@callback');
    ```

1. <span data-ttu-id="f96c8-116">Démarrez le serveur et accédez à `https://localhost:8000`.</span><span class="sxs-lookup"><span data-stu-id="f96c8-116">Start the server and browse to `https://localhost:8000`.</span></span> <span data-ttu-id="f96c8-117">Cliquez sur le bouton de connexion. vous serez redirigé vers `https://login.microsoftonline.com`.</span><span class="sxs-lookup"><span data-stu-id="f96c8-117">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="f96c8-118">Connectez-vous avec votre compte Microsoft et acceptez les autorisations demandées.</span><span class="sxs-lookup"><span data-stu-id="f96c8-118">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="f96c8-119">Le navigateur vous redirige vers l’application, affichant le jeton.</span><span class="sxs-lookup"><span data-stu-id="f96c8-119">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="f96c8-120">Obtenir les détails de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="f96c8-120">Get user details</span></span>

<span data-ttu-id="f96c8-121">Dans cette section, vous allez mettre `callback` à jour la méthode pour obtenir le profil de l’utilisateur à partir de Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="f96c8-121">In this section you'll update the `callback` method to get the user's profile from Microsoft Graph.</span></span>

1. <span data-ttu-id="f96c8-122">Ajoutez les instructions `use` suivantes en haut de **/app/http/Controllers/AuthController.php**, sous la `namespace App\Http\Controllers;` ligne.</span><span class="sxs-lookup"><span data-stu-id="f96c8-122">Add the following `use` statements to the top of **/app/Http/Controllers/AuthController.php**, beneath the `namespace App\Http\Controllers;` line.</span></span>

    ```php
    use Microsoft\Graph\Graph;
    use Microsoft\Graph\Model;
    ```

1. <span data-ttu-id="f96c8-123">Remplacez le `try` bloc dans la `callback` méthode par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="f96c8-123">Replace the `try` block in the `callback` method with the following code.</span></span>

    ```php
    try {
      // Make the token request
      $accessToken = $oauthClient->getAccessToken('authorization_code', [
        'code' => $authCode
      ]);

      $graph = new Graph();
      $graph->setAccessToken($accessToken->getToken());

      $user = $graph->createRequest('GET', '/me')
        ->setReturnType(Model\User::class)
        ->execute();

      // TEMPORARY FOR TESTING!
      return redirect('/')
        ->with('error', 'Access token received')
        ->with('errorDetail', 'User:'.$user->getDisplayName().', Token:'.$accessToken->getToken());
    }
    ```

<span data-ttu-id="f96c8-124">Le nouveau code crée un `Graph` objet, lui attribue le jeton d’accès, puis l’utilise pour demander le profil de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f96c8-124">The new code creates a `Graph` object, assigns the access token, then uses it to request the user's profile.</span></span> <span data-ttu-id="f96c8-125">Il ajoute le nom complet de l’utilisateur à la sortie temporaire pour le test.</span><span class="sxs-lookup"><span data-stu-id="f96c8-125">It adds the user's display name to the temporary output for testing.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="f96c8-126">Stockage des jetons</span><span class="sxs-lookup"><span data-stu-id="f96c8-126">Storing the tokens</span></span>

<span data-ttu-id="f96c8-127">Maintenant que vous pouvez obtenir des jetons, nous vous conseillons d’implémenter un moyen de les stocker dans l’application.</span><span class="sxs-lookup"><span data-stu-id="f96c8-127">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="f96c8-128">Étant donné qu’il s’agit d’un exemple d’application, pour des raisons de simplicité, vous les stockerez dans la session.</span><span class="sxs-lookup"><span data-stu-id="f96c8-128">Since this is a sample app, for simplicity's sake, you'll store them in the session.</span></span> <span data-ttu-id="f96c8-129">Une application réelle utilise une solution de stockage sécurisé plus fiable, comme une base de données.</span><span class="sxs-lookup"><span data-stu-id="f96c8-129">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

1. <span data-ttu-id="f96c8-130">Créez un répertoire dans le répertoire **./app** nommé `TokenStore`, puis créez un fichier dans ce répertoire nommé `TokenCache.php`et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="f96c8-130">Create a new directory in the **./app** directory named `TokenStore`, then create a new file in that directory named `TokenCache.php`, and add the following code.</span></span>

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
          'userEmail' => null !== $user->getMail() ? $user->getMail() : $user->getUserPrincipalName()
        ]);
      }

      public function clearTokens() {
        session()->forget('accessToken');
        session()->forget('refreshToken');
        session()->forget('tokenExpires');
        session()->forget('userName');
        session()->forget('userEmail');
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

1. <span data-ttu-id="f96c8-131">Ajoutez l’instruction `use` suivante en haut de **./app/http/Controllers/AuthController.php**, sous la `namespace App\Http\Controllers;` ligne.</span><span class="sxs-lookup"><span data-stu-id="f96c8-131">Add the following `use` statement to the top of **./app/Http/Controllers/AuthController.php**, beneath the `namespace App\Http\Controllers;` line.</span></span>

    ```php
    use App\TokenStore\TokenCache;
    ```

1. <span data-ttu-id="f96c8-132">Remplacez le `try` bloc dans la fonction `callback` existante par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="f96c8-132">Replace the `try` block in the existing `callback` function with the following.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/AuthController.php" id="StoreTokensSnippet":::

## <a name="implement-sign-out"></a><span data-ttu-id="f96c8-133">Mettre en œuvre la déconnexion</span><span class="sxs-lookup"><span data-stu-id="f96c8-133">Implement sign-out</span></span>

<span data-ttu-id="f96c8-134">Avant de tester cette nouvelle fonctionnalité, ajoutez une méthode pour vous déconnecter.</span><span class="sxs-lookup"><span data-stu-id="f96c8-134">Before you test this new feature, add a way to sign out.</span></span>

1. <span data-ttu-id="f96c8-135">Ajoutez l’action suivante à la `AuthController` classe.</span><span class="sxs-lookup"><span data-stu-id="f96c8-135">Add the following action to the `AuthController` class.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/AuthController.php" id="SignOutSnippet":::

1. <span data-ttu-id="f96c8-136">Ajoutez cette action à **./routes/Web.php**.</span><span class="sxs-lookup"><span data-stu-id="f96c8-136">Add this action to **./routes/web.php**.</span></span>

    ```php
    Route::get('/signout', 'AuthController@signout');
    ```

1. <span data-ttu-id="f96c8-137">Redémarrez le serveur et suivez le processus de connexion.</span><span class="sxs-lookup"><span data-stu-id="f96c8-137">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="f96c8-138">Vous devez revenir sur la page d’accueil, mais l’interface utilisateur doit changer pour indiquer que vous êtes connecté.</span><span class="sxs-lookup"><span data-stu-id="f96c8-138">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Capture d’écran de la page d’accueil après la connexion](./images/add-aad-auth-01.png)

1. <span data-ttu-id="f96c8-140">Cliquez sur Avatar de l’utilisateur dans le coin supérieur droit pour accéder au lien **déconnexion** .</span><span class="sxs-lookup"><span data-stu-id="f96c8-140">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="f96c8-141">Le fait de cliquer sur **Se déconnecter** réinitialise la session et vous ramène à la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="f96c8-141">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Capture d’écran du menu déroulant avec le lien de déconnexion](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="f96c8-143">Actualisation des jetons</span><span class="sxs-lookup"><span data-stu-id="f96c8-143">Refreshing tokens</span></span>

<span data-ttu-id="f96c8-144">À ce stade, votre application a un jeton d’accès, qui est envoyé `Authorization` dans l’en-tête des appels d’API.</span><span class="sxs-lookup"><span data-stu-id="f96c8-144">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="f96c8-145">Il s’agit du jeton qui permet à l’application d’accéder à Microsoft Graph pour le compte de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f96c8-145">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="f96c8-146">Cependant, ce jeton est de courte durée.</span><span class="sxs-lookup"><span data-stu-id="f96c8-146">However, this token is short-lived.</span></span> <span data-ttu-id="f96c8-147">Le jeton expire une heure après son émission.</span><span class="sxs-lookup"><span data-stu-id="f96c8-147">The token expires an hour after it is issued.</span></span> <span data-ttu-id="f96c8-148">C’est là que le jeton d’actualisation devient utile.</span><span class="sxs-lookup"><span data-stu-id="f96c8-148">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="f96c8-149">Le jeton d’actualisation permet à l’application de demander un nouveau jeton d’accès sans obliger l’utilisateur à se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="f96c8-149">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span> <span data-ttu-id="f96c8-150">Mettez à jour le code de gestion des jetons pour implémenter l’actualisation des jetons.</span><span class="sxs-lookup"><span data-stu-id="f96c8-150">Update the token management code to implement token refresh.</span></span>

1. <span data-ttu-id="f96c8-151">Ouvrez **./app/TokenStore/TokenCache.php** et ajoutez la fonction suivante à la `TokenCache` classe.</span><span class="sxs-lookup"><span data-stu-id="f96c8-151">Open **./app/TokenStore/TokenCache.php** and add the following function to the `TokenCache` class.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/TokenStore/TokenCache.php" id="UpdateTokensSnippet":::

1. <span data-ttu-id="f96c8-152">Remplacez la fonction `getAccessToken` existante par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="f96c8-152">Replace the existing `getAccessToken` function with the following.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/TokenStore/TokenCache.php" id="GetAccessTokenSnippet":::

<span data-ttu-id="f96c8-153">Cette méthode vérifie d’abord si le jeton d’accès a expiré ou s’il arrive à expiration.</span><span class="sxs-lookup"><span data-stu-id="f96c8-153">This method first checks if the access token is expired or close to expiring.</span></span> <span data-ttu-id="f96c8-154">Si c’est le cas, il utilise le jeton d’actualisation pour obtenir de nouveaux jetons, puis il met à jour le cache et renvoie le nouveau jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="f96c8-154">If it is, then it uses the refresh token to get new tokens, then updates the cache and returns the new access token.</span></span>
