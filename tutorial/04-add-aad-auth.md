<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="67a00-101">Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD.</span><span class="sxs-lookup"><span data-stu-id="67a00-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="67a00-102">Cette étape est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="67a00-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="67a00-103">Dans cette étape, vous allez intégrer la [bibliothèque oauth2-client](https://github.com/thephpleague/oauth2-client) dans l’application.</span><span class="sxs-lookup"><span data-stu-id="67a00-103">In this step you will integrate the [oauth2-client](https://github.com/thephpleague/oauth2-client) library into the application.</span></span>

1. <span data-ttu-id="67a00-104">Ouvrez **le fichier .env** à la racine de votre application PHP et ajoutez le code suivant à la fin du fichier.</span><span class="sxs-lookup"><span data-stu-id="67a00-104">Open the **.env** file in the root of your PHP application, and add the following code to the end of the file.</span></span>

    :::code language="ini" source="../demo/graph-tutorial/example.env" range="48-54":::

1. <span data-ttu-id="67a00-105">Remplacez-le par l’ID de l’application à partir du portail d’inscription des applications et par le `YOUR_APP_ID_HERE` mot de passe que vous avez `YOUR_APP_PASSWORD_HERE` généré.</span><span class="sxs-lookup"><span data-stu-id="67a00-105">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_PASSWORD_HERE` with the password you generated.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="67a00-106">Si vous utilisez un contrôle source tel que Git, il est temps d’exclure le fichier du contrôle source afin d’éviter toute fuite accidentelle de votre ID d’application et de votre mot de `.env` passe.</span><span class="sxs-lookup"><span data-stu-id="67a00-106">If you're using source control such as git, now would be a good time to exclude the `.env` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="67a00-107">Implémentation de la connexion</span><span class="sxs-lookup"><span data-stu-id="67a00-107">Implement sign-in</span></span>

1. <span data-ttu-id="67a00-108">Créez un fichier dans le répertoire **./app/Http/Controllers** nommé `AuthController.php` et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="67a00-108">Create a new file in the **./app/Http/Controllers** directory named `AuthController.php` and add the following code.</span></span>

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

    <span data-ttu-id="67a00-109">Cela définit un contrôleur avec deux actions : `signin` et `callback` .</span><span class="sxs-lookup"><span data-stu-id="67a00-109">This defines a controller with two actions: `signin` and `callback`.</span></span>

    <span data-ttu-id="67a00-110">L’action génère l’URL de la signature Azure AD, enregistre la valeur générée par le `signin` client OAuth, puis redirige le navigateur vers la page de signature `state` Azure AD.</span><span class="sxs-lookup"><span data-stu-id="67a00-110">The `signin` action generates the Azure AD signin URL, saves the `state` value generated by the OAuth client, then redirects the browser to the Azure AD signin page.</span></span>

    <span data-ttu-id="67a00-111">`callback`L’action est l’endroit où Azure redirige une fois la signature terminée.</span><span class="sxs-lookup"><span data-stu-id="67a00-111">The `callback` action is where Azure redirects after the signin is complete.</span></span> <span data-ttu-id="67a00-112">Cette action permet de s’assurer que la valeur correspond à la valeur enregistrée, puis d’utilisateurs le code d’autorisation envoyé par `state` Azure pour demander un jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="67a00-112">That action makes sure the `state` value matches the saved value, then users the authorization code sent by Azure to request an access token.</span></span> <span data-ttu-id="67a00-113">Il redirige ensuite vers la page d’accueil avec le jeton d’accès dans la valeur d’erreur temporaire.</span><span class="sxs-lookup"><span data-stu-id="67a00-113">It then redirects back to the home page with the access token in the temporary error value.</span></span> <span data-ttu-id="67a00-114">Vous l’utiliserez pour vérifier que la connectez-vous fonctionne avant de passer à autre chose.</span><span class="sxs-lookup"><span data-stu-id="67a00-114">You'll use this to verify that sign-in is working before moving on.</span></span>

1. <span data-ttu-id="67a00-115">Ajoutez les itinéraires **à ./routes/web.php**.</span><span class="sxs-lookup"><span data-stu-id="67a00-115">Add the routes to **./routes/web.php**.</span></span>

    ```php
    Route::get('/signin', 'AuthController@signin');
    Route::get('/callback', 'AuthController@callback');
    ```

1. <span data-ttu-id="67a00-116">Démarrez le serveur et accédez à `https://localhost:8000` .</span><span class="sxs-lookup"><span data-stu-id="67a00-116">Start the server and browse to `https://localhost:8000`.</span></span> <span data-ttu-id="67a00-117">Cliquez sur le bouton de connexion. vous serez redirigé vers `https://login.microsoftonline.com`.</span><span class="sxs-lookup"><span data-stu-id="67a00-117">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="67a00-118">Connectez-vous avec votre compte Microsoft.</span><span class="sxs-lookup"><span data-stu-id="67a00-118">Login with your Microsoft account.</span></span>

1. <span data-ttu-id="67a00-119">Examinez l’invite de consentement.</span><span class="sxs-lookup"><span data-stu-id="67a00-119">Examine the consent prompt.</span></span> <span data-ttu-id="67a00-120">La liste des autorisations correspond à la liste des étendues d’autorisations configurées dans **.env**.</span><span class="sxs-lookup"><span data-stu-id="67a00-120">The list of permissions correspond to list of permissions scopes configured in **.env**.</span></span>

    - <span data-ttu-id="67a00-121">**Conservez l’accès aux** données à qui vous avez accordé l’accès : ( ) Cette autorisation est demandée par MSAL afin de récupérer les jetons `offline_access` d’actualisation.</span><span class="sxs-lookup"><span data-stu-id="67a00-121">**Maintain access to data you have given it access to:** (`offline_access`) This permission is requested by MSAL in order to retrieve refresh tokens.</span></span>
    - <span data-ttu-id="67a00-122">**Connectez-vous et lisez votre** profil : ( ) Cette autorisation permet à l’application d’obtenir le profil et la photo de profil de `User.Read` l’utilisateur connecté.</span><span class="sxs-lookup"><span data-stu-id="67a00-122">**Sign you in and read your profile:** (`User.Read`) This permission allows the app to get the logged-in user's profile and profile photo.</span></span>
    - <span data-ttu-id="67a00-123">**Lisez les paramètres de votre** boîte aux lettres : ( ) Cette autorisation permet à l’application de lire les paramètres de boîte aux lettres de l’utilisateur, y compris le fuseau horaire et `MailboxSettings.Read` le format horaire.</span><span class="sxs-lookup"><span data-stu-id="67a00-123">**Read your mailbox settings:** (`MailboxSettings.Read`) This permission allows the app to read the user's mailbox settings, including time zone and time format.</span></span>
    - <span data-ttu-id="67a00-124">**Avoir un accès total à** vos calendriers : ( ) Cette autorisation permet à l’application de lire des événements sur le calendrier de l’utilisateur, d’ajouter de nouveaux événements et de modifier des `Calendars.ReadWrite` événements existants.</span><span class="sxs-lookup"><span data-stu-id="67a00-124">**Have full access to your calendars:** (`Calendars.ReadWrite`) This permission allows the app to read events on the user's calendar, add new events, and modify existing ones.</span></span>

1. <span data-ttu-id="67a00-125">Consentement aux autorisations demandées.</span><span class="sxs-lookup"><span data-stu-id="67a00-125">Consent to the requested permissions.</span></span> <span data-ttu-id="67a00-126">Le navigateur vous redirige vers l’application, affichant le jeton.</span><span class="sxs-lookup"><span data-stu-id="67a00-126">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="67a00-127">Obtenir les détails de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="67a00-127">Get user details</span></span>

<span data-ttu-id="67a00-128">Dans cette section, vous allez mettre à jour la méthode pour obtenir `callback` le profil de l’utilisateur à partir de Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="67a00-128">In this section you'll update the `callback` method to get the user's profile from Microsoft Graph.</span></span>

1. <span data-ttu-id="67a00-129">Ajoutez les instructions suivantes en haut de `use` **/app/Http/Controllers/AuthController.php**, sous la `namespace App\Http\Controllers;` ligne.</span><span class="sxs-lookup"><span data-stu-id="67a00-129">Add the following `use` statements to the top of **/app/Http/Controllers/AuthController.php**, beneath the `namespace App\Http\Controllers;` line.</span></span>

    ```php
    use Microsoft\Graph\Graph;
    use Microsoft\Graph\Model;
    ```

1. <span data-ttu-id="67a00-130">Remplacez `try` le bloc dans la méthode par le code `callback` suivant.</span><span class="sxs-lookup"><span data-stu-id="67a00-130">Replace the `try` block in the `callback` method with the following code.</span></span>

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

<span data-ttu-id="67a00-131">Le nouveau code crée un `Graph` objet, affecte le jeton d’accès, puis l’utilise pour demander le profil de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="67a00-131">The new code creates a `Graph` object, assigns the access token, then uses it to request the user's profile.</span></span> <span data-ttu-id="67a00-132">Il ajoute le nom complet de l’utilisateur à la sortie temporaire pour le test.</span><span class="sxs-lookup"><span data-stu-id="67a00-132">It adds the user's display name to the temporary output for testing.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="67a00-133">Stockage des jetons</span><span class="sxs-lookup"><span data-stu-id="67a00-133">Storing the tokens</span></span>

<span data-ttu-id="67a00-134">Maintenant que vous pouvez obtenir des jetons, nous vous conseillons d’implémenter un moyen de les stocker dans l’application.</span><span class="sxs-lookup"><span data-stu-id="67a00-134">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="67a00-135">Comme il s’agit d’un exemple d’application, par souci de simplicité, vous les stockerez dans la session.</span><span class="sxs-lookup"><span data-stu-id="67a00-135">Since this is a sample app, for simplicity's sake, you'll store them in the session.</span></span> <span data-ttu-id="67a00-136">Une application réelle utilise une solution de stockage sécurisé plus fiable, comme une base de données.</span><span class="sxs-lookup"><span data-stu-id="67a00-136">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

1. <span data-ttu-id="67a00-137">Créez un répertoire dans le répertoire **./app** nommé, puis créez un fichier dans ce répertoire nommé, puis ajoutez `TokenStore` le code `TokenCache.php` suivant.</span><span class="sxs-lookup"><span data-stu-id="67a00-137">Create a new directory in the **./app** directory named `TokenStore`, then create a new file in that directory named `TokenCache.php`, and add the following code.</span></span>

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

1. <span data-ttu-id="67a00-138">Ajoutez l’instruction suivante en haut de `use` **./app/Http/Controllers/AuthController.php**, sous la `namespace App\Http\Controllers;` ligne.</span><span class="sxs-lookup"><span data-stu-id="67a00-138">Add the following `use` statement to the top of **./app/Http/Controllers/AuthController.php**, beneath the `namespace App\Http\Controllers;` line.</span></span>

    ```php
    use App\TokenStore\TokenCache;
    ```

1. <span data-ttu-id="67a00-139">Remplacez `try` le bloc dans la fonction existante par ce qui `callback` suit.</span><span class="sxs-lookup"><span data-stu-id="67a00-139">Replace the `try` block in the existing `callback` function with the following.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/AuthController.php" id="StoreTokensSnippet":::

## <a name="implement-sign-out"></a><span data-ttu-id="67a00-140">Implémenter la signature</span><span class="sxs-lookup"><span data-stu-id="67a00-140">Implement sign-out</span></span>

<span data-ttu-id="67a00-141">Avant de tester cette nouvelle fonctionnalité, ajoutez un moyen de vous en sortir.</span><span class="sxs-lookup"><span data-stu-id="67a00-141">Before you test this new feature, add a way to sign out.</span></span>

1. <span data-ttu-id="67a00-142">Ajoutez l’action suivante à la `AuthController` classe.</span><span class="sxs-lookup"><span data-stu-id="67a00-142">Add the following action to the `AuthController` class.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/AuthController.php" id="SignOutSnippet":::

1. <span data-ttu-id="67a00-143">Ajoutez cette action **à ./routes/web.php**.</span><span class="sxs-lookup"><span data-stu-id="67a00-143">Add this action to **./routes/web.php**.</span></span>

    ```php
    Route::get('/signout', 'AuthController@signout');
    ```

1. <span data-ttu-id="67a00-144">Redémarrez le serveur et traversez le processus de sign-in.</span><span class="sxs-lookup"><span data-stu-id="67a00-144">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="67a00-145">Vous devez revenir sur la page d’accueil, mais l’interface utilisateur doit changer pour indiquer que vous êtes en cours de signature.</span><span class="sxs-lookup"><span data-stu-id="67a00-145">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Capture d’écran de la page d’accueil après la connexion](./images/add-aad-auth-01.png)

1. <span data-ttu-id="67a00-147">Cliquez sur l’avatar de l’utilisateur dans le coin supérieur droit pour accéder au lien **de** connexion.</span><span class="sxs-lookup"><span data-stu-id="67a00-147">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="67a00-148">Le fait de cliquer sur **Se déconnecter** réinitialise la session et vous ramène à la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="67a00-148">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Capture d’écran du menu déroulant avec le lien de déconnexion](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="67a00-150">Actualisation des jetons</span><span class="sxs-lookup"><span data-stu-id="67a00-150">Refreshing tokens</span></span>

<span data-ttu-id="67a00-151">À ce stade, votre application dispose d’un jeton d’accès, qui est envoyé dans l’en-tête des `Authorization` appels d’API.</span><span class="sxs-lookup"><span data-stu-id="67a00-151">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="67a00-152">Il s’agit du jeton qui permet à l’application d’accéder à Microsoft Graph au nom de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="67a00-152">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="67a00-153">Cependant, ce jeton est de courte durée.</span><span class="sxs-lookup"><span data-stu-id="67a00-153">However, this token is short-lived.</span></span> <span data-ttu-id="67a00-154">Le jeton expire une heure après son émission.</span><span class="sxs-lookup"><span data-stu-id="67a00-154">The token expires an hour after it is issued.</span></span> <span data-ttu-id="67a00-155">C’est là que le jeton d’actualisation devient utile.</span><span class="sxs-lookup"><span data-stu-id="67a00-155">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="67a00-156">Le jeton d’actualisation permet à l’application de demander un nouveau jeton d’accès sans obliger l’utilisateur à se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="67a00-156">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span> <span data-ttu-id="67a00-157">Mettez à jour le code de gestion des jetons pour implémenter l’actualisation du jeton.</span><span class="sxs-lookup"><span data-stu-id="67a00-157">Update the token management code to implement token refresh.</span></span>

1. <span data-ttu-id="67a00-158">Ouvrez **./app/TokenStore/TokenCache.php** et ajoutez la fonction suivante à la `TokenCache` classe.</span><span class="sxs-lookup"><span data-stu-id="67a00-158">Open **./app/TokenStore/TokenCache.php** and add the following function to the `TokenCache` class.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/TokenStore/TokenCache.php" id="UpdateTokensSnippet":::

1. <span data-ttu-id="67a00-159">Remplacez la fonction `getAccessToken` existante par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="67a00-159">Replace the existing `getAccessToken` function with the following.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/TokenStore/TokenCache.php" id="GetAccessTokenSnippet":::

<span data-ttu-id="67a00-160">Cette méthode vérifie d’abord si le jeton d’accès a expiré ou arrive à expiration.</span><span class="sxs-lookup"><span data-stu-id="67a00-160">This method first checks if the access token is expired or close to expiring.</span></span> <span data-ttu-id="67a00-161">Si c’est le cas, il utilise le jeton d’actualisation pour obtenir de nouveaux jetons, puis met à jour le cache et renvoie le nouveau jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="67a00-161">If it is, then it uses the refresh token to get new tokens, then updates the cache and returns the new access token.</span></span>
