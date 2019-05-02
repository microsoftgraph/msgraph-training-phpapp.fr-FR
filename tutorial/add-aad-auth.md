<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="c3188-101">Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD.</span><span class="sxs-lookup"><span data-stu-id="c3188-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="c3188-102">Cela est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="c3188-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="c3188-103">Dans cette étape, vous allez intégrer la bibliothèque [oauth2-cliente](https://github.com/thephpleague/oauth2-client) dans l’application.</span><span class="sxs-lookup"><span data-stu-id="c3188-103">In this step you will integrate the [oauth2-client](https://github.com/thephpleague/oauth2-client) library into the application.</span></span>

<span data-ttu-id="c3188-104">Ouvrez le `.env` fichier à la racine de votre application php et ajoutez le code suivant à la fin du fichier.</span><span class="sxs-lookup"><span data-stu-id="c3188-104">Open the `.env` file in the root of your PHP application, and add the following code to the end of the file.</span></span>

```text
OAUTH_APP_ID=YOUR_APP_ID_HERE
OAUTH_APP_PASSWORD=YOUR_APP_PASSWORD_HERE
OAUTH_REDIRECT_URI=http://localhost:8000/callback
OAUTH_SCOPES='openid profile offline_access user.read calendars.read'
OAUTH_AUTHORITY=https://login.microsoftonline.com/common
OAUTH_AUTHORIZE_ENDPOINT=/oauth2/v2.0/authorize
OAUTH_TOKEN_ENDPOINT=/oauth2/v2.0/token
```

<span data-ttu-id="c3188-105">Remplacez `YOUR APP ID HERE` par l’ID de l’application dans le portail d’inscription de `YOUR APP SECRET HERE` l’application et remplacez par le mot de passe que vous avez généré.</span><span class="sxs-lookup"><span data-stu-id="c3188-105">Replace `YOUR APP ID HERE` with the application ID from the Application Registration Portal, and replace `YOUR APP SECRET HERE` with the password you generated.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c3188-106">Si vous utilisez le contrôle de code source tel que git, il est maintenant recommandé d’exclure le `.env` fichier du contrôle de code source afin d’éviter une fuite accidentelle de votre ID d’application et de votre mot de passe.</span><span class="sxs-lookup"><span data-stu-id="c3188-106">If you're using source control such as git, now would be a good time to exclude the `.env` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="c3188-107">Mettre en œuvre la connexion</span><span class="sxs-lookup"><span data-stu-id="c3188-107">Implement sign-in</span></span>

<span data-ttu-id="c3188-108">Créez un fichier dans le `./app/Http/Controllers` répertoire nommé `AuthController.php` et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="c3188-108">Create a new file in the `./app/Http/Controllers` directory named `AuthController.php` and add the following code.</span></span>

```PHP
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

    if (!isset($expectedState) || !isset($providedState) || $expectedState != $providedState) {
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

<span data-ttu-id="c3188-109">Cela définit un contrôleur avec deux actions: `signin` et `callback`.</span><span class="sxs-lookup"><span data-stu-id="c3188-109">This defines a controller with two actions: `signin` and `callback`.</span></span>

<span data-ttu-id="c3188-110">L' `signin` action génère l’URL de connexion Azure ad, enregistre la `state` valeur générée par le client OAuth, puis redirige le navigateur vers la page de connexion Azure ad.</span><span class="sxs-lookup"><span data-stu-id="c3188-110">The `signin` action generates the Azure AD signin URL, saves the `state` value generated by the OAuth client, then redirects the browser to the Azure AD signin page.</span></span>

<span data-ttu-id="c3188-111">L' `callback` action est l’endroit où Azure redirige une fois la connexion terminée.</span><span class="sxs-lookup"><span data-stu-id="c3188-111">The `callback` action is where Azure redirects after the signin is complete.</span></span> <span data-ttu-id="c3188-112">Cette action permet de s' `state` assurer que la valeur correspond à la valeur enregistrée, puis aux utilisateurs le code d’autorisation envoyé par Azure pour demander un jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="c3188-112">That action makes sure the `state` value matches the saved value, then users the authorization code sent by Azure to request an access token.</span></span> <span data-ttu-id="c3188-113">Il redirige ensuite vers la page d’accueil avec le jeton d’accès dans la valeur d’erreur temporaire.</span><span class="sxs-lookup"><span data-stu-id="c3188-113">It then redirects back to the home page with the access token in the temporary error value.</span></span> <span data-ttu-id="c3188-114">Nous allons l’utiliser pour vérifier que notre connexion fonctionne avant de poursuivre.</span><span class="sxs-lookup"><span data-stu-id="c3188-114">We'll use this to verify that our sign-in is working before moving on.</span></span> <span data-ttu-id="c3188-115">Avant de tester, nous devons ajouter les itinéraires à `./routes/web.php`.</span><span class="sxs-lookup"><span data-stu-id="c3188-115">Before we test, we need to add the routes to `./routes/web.php`.</span></span>

```PHP
Route::get('/signin', 'AuthController@signin');
Route::get('/callback', 'AuthController@callback');
```

<span data-ttu-id="c3188-116">Démarrez le serveur et accédez à `https://localhost:8000`.</span><span class="sxs-lookup"><span data-stu-id="c3188-116">Start the server and browse to `https://localhost:8000`.</span></span> <span data-ttu-id="c3188-117">Cliquez sur le bouton de connexion et vous devez être redirigé vers `https://login.microsoftonline.com`.</span><span class="sxs-lookup"><span data-stu-id="c3188-117">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="c3188-118">Connectez-vous avec votre compte Microsoft et acceptez les autorisations demandées.</span><span class="sxs-lookup"><span data-stu-id="c3188-118">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="c3188-119">Le navigateur redirige vers l’application en affichant le jeton.</span><span class="sxs-lookup"><span data-stu-id="c3188-119">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="c3188-120">Obtenir les détails de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="c3188-120">Get user details</span></span>

<span data-ttu-id="c3188-121">Mettez à `callback` jour la `/app/Http/Controllers/AuthController.php` méthode dans pour obtenir le profil de l’utilisateur à partir de Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="c3188-121">Update the `callback` method in `/app/Http/Controllers/AuthController.php` to get the user's profile from Microsoft Graph.</span></span>

<span data-ttu-id="c3188-122">Tout d’abord, ajoutez `use` les instructions suivantes en haut du fichier, sous la `namespace App\Http\Controllers;` ligne.</span><span class="sxs-lookup"><span data-stu-id="c3188-122">First, add the following `use` statements to the top of the file, beneath the `namespace App\Http\Controllers;` line.</span></span>

```php
use Microsoft\Graph\Graph;
use Microsoft\Graph\Model;
```

<span data-ttu-id="c3188-123">Remplacez le `try` bloc dans la `callback` méthode par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="c3188-123">Replace the `try` block in the `callback` method with the following code.</span></span>

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

<span data-ttu-id="c3188-124">Le nouveau code crée un `Graph` objet, lui attribue le jeton d’accès, puis l’utilise pour demander le profil de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="c3188-124">The new code creates a `Graph` object, assigns the access token, then uses it to request the user's profile.</span></span> <span data-ttu-id="c3188-125">Il ajoute le nom complet de l’utilisateur à la sortie temporaire pour le test.</span><span class="sxs-lookup"><span data-stu-id="c3188-125">It adds the user's display name to the temporary output for testing.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="c3188-126">Stockage des jetons</span><span class="sxs-lookup"><span data-stu-id="c3188-126">Storing the tokens</span></span>

<span data-ttu-id="c3188-127">Maintenant que vous pouvez obtenir des jetons, il est temps de mettre en œuvre un moyen de les stocker dans l’application.</span><span class="sxs-lookup"><span data-stu-id="c3188-127">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="c3188-128">Étant donné qu’il s’agit d’un exemple d’application, pour des raisons de simplicité, vous les stockerez dans la session.</span><span class="sxs-lookup"><span data-stu-id="c3188-128">Since this is a sample app, for simplicity's sake, you'll store them in the session.</span></span> <span data-ttu-id="c3188-129">Une application réelle utiliserait une solution de stockage sécurisé plus fiable, comme une base de données.</span><span class="sxs-lookup"><span data-stu-id="c3188-129">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

<span data-ttu-id="c3188-130">Créez un répertoire dans le `./app` répertoire nommé `TokenStore`, puis créez un fichier dans ce répertoire nommé `TokenCache.php`et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="c3188-130">Create a new directory in the `./app` directory named `TokenStore`, then create a new file in that directory named `TokenCache.php`, and add the following code.</span></span>

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

<span data-ttu-id="c3188-131">Ensuite, mettez à `callback` jour la fonction `AuthController` dans la classe pour stocker les jetons dans la session et rediriger vers la page principale.</span><span class="sxs-lookup"><span data-stu-id="c3188-131">Then, update the `callback` function in the `AuthController` class to store the tokens in the session and redirect back to the main page.</span></span>

<span data-ttu-id="c3188-132">Tout d’abord, ajoutez `use` l’instruction suivante en haut `./app/Http/Controllers/AuthController.php`de, sous `namespace App\Http\Controllers;` la ligne.</span><span class="sxs-lookup"><span data-stu-id="c3188-132">First, add the following `use` statement to the top of `./app/Http/Controllers/AuthController.php`, beneath the `namespace App\Http\Controllers;` line.</span></span>

```php
use App\TokenStore\TokenCache;
```

<span data-ttu-id="c3188-133">Remplacez ensuite le `try` bloc dans la fonction `callback` existante par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="c3188-133">Then replace the `try` block in the existing `callback` function with the following.</span></span>

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

  $tokenCache = new TokenCache();
  $tokenCache->storeTokens($accessToken, $user);

  return redirect('/');
}
```

## <a name="implement-sign-out"></a><span data-ttu-id="c3188-134">Mettre en œuvre la déconnexion</span><span class="sxs-lookup"><span data-stu-id="c3188-134">Implement sign-out</span></span>

<span data-ttu-id="c3188-135">Avant de tester cette nouvelle fonctionnalité, ajoutez une méthode pour vous déconnecter. Ajoutez l’action suivante à la `AuthController` classe.</span><span class="sxs-lookup"><span data-stu-id="c3188-135">Before you test this new feature, add a way to sign out. Add the following action to the `AuthController` class.</span></span>

```PHP
public function signout()
{
  $tokenCache = new TokenCache();
  $tokenCache->clearTokens();
  return redirect('/');
}
```

<span data-ttu-id="c3188-136">Ajoutez cette action à `./routes/web.php`.</span><span class="sxs-lookup"><span data-stu-id="c3188-136">Add this action to `./routes/web.php`.</span></span>

```PHP
Route::get('/signout', 'AuthController@signout');
```

<span data-ttu-id="c3188-137">Redémarrez le serveur et suivez le processus de connexion.</span><span class="sxs-lookup"><span data-stu-id="c3188-137">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="c3188-138">Vous devez revenir sur la page d’accueil, mais l’interface utilisateur doit changer pour indiquer que vous êtes connecté.</span><span class="sxs-lookup"><span data-stu-id="c3188-138">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

![Capture d’écran de la page d’accueil après la connexion](./images/add-aad-auth-01.png)

<span data-ttu-id="c3188-140">Cliquez sur Avatar de l’utilisateur dans le coin supérieur droit pour \*\*\*\* accéder au lien Déconnexion.</span><span class="sxs-lookup"><span data-stu-id="c3188-140">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="c3188-141">Cliquez \*\*\*\* sur Déconnexion pour réinitialiser la session et revenir à la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="c3188-141">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

![Capture d’écran du menu déroulant avec le lien Déconnexion](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="c3188-143">Actualisation des jetons</span><span class="sxs-lookup"><span data-stu-id="c3188-143">Refreshing tokens</span></span>

<span data-ttu-id="c3188-144">À ce stade, votre application a un jeton d’accès, qui est envoyé `Authorization` dans l’en-tête des appels d’API.</span><span class="sxs-lookup"><span data-stu-id="c3188-144">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="c3188-145">Il s’agit du jeton qui permet à l’application d’accéder à Microsoft Graph pour le compte de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="c3188-145">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="c3188-146">Toutefois, ce jeton est éphémère.</span><span class="sxs-lookup"><span data-stu-id="c3188-146">However, this token is short-lived.</span></span> <span data-ttu-id="c3188-147">Le jeton expire une heure après son émission.</span><span class="sxs-lookup"><span data-stu-id="c3188-147">The token expires an hour after it is issued.</span></span> <span data-ttu-id="c3188-148">C’est ici que le jeton d’actualisation devient utile.</span><span class="sxs-lookup"><span data-stu-id="c3188-148">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="c3188-149">Le jeton d’actualisation permet à l’application de demander un nouveau jeton d’accès sans demander à l’utilisateur de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="c3188-149">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span> <span data-ttu-id="c3188-150">Mettez à jour le code de gestion des jetons pour implémenter l’actualisation des jetons.</span><span class="sxs-lookup"><span data-stu-id="c3188-150">Update the token management code to implement token refresh.</span></span>

<span data-ttu-id="c3188-151">Ouvrez `./app/TokenStore/TokenCache.php` et ajoutez la fonction suivante à la `TokenCache` classe.</span><span class="sxs-lookup"><span data-stu-id="c3188-151">Open `./app/TokenStore/TokenCache.php` and add the following function to the `TokenCache` class.</span></span>

```php
public function updateTokens($accessToken) {
  session([
    'accessToken' => $accessToken->getToken(),
    'refreshToken' => $accessToken->getRefreshToken(),
    'tokenExpires' => $accessToken->getExpires()
  ]);
}
```

<span data-ttu-id="c3188-152">Remplacez la fonction existante `getAccessToken` par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="c3188-152">Then replace the existing `getAccessToken` function with the following.</span></span>

```php
public function getAccessToken() {
  // Check if tokens exist
  if (empty(session('accessToken')) ||
      empty(session('refreshToken')) ||
      empty(session('tokenExpires'))) {
    return '';
  }

  // Check if token is expired
  //Get current time + 5 minutes (to allow for time differences)
  $now = time() + 300;
  if (session('tokenExpires') <= $now) {
    // Token is expired (or very close to it)
    // so let's refresh

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
      $newToken = $oauthClient->getAccessToken('refresh_token', [
        'refresh_token' => session('refreshToken')
      ]);

      // Store the new values
      $this->updateTokens($newToken);

      return $newToken->getToken();
    }
    catch (League\OAuth2\Client\Provider\Exception\IdentityProviderException $e) {
      return '';
    }
  }

  // Token is still valid, just return it
  return session('accessToken');
}
```

<span data-ttu-id="c3188-153">Cette méthode vérifie d’abord si le jeton d’accès a expiré ou s’il arrive à expiration.</span><span class="sxs-lookup"><span data-stu-id="c3188-153">This method first checks if the access token is expired or close to expiring.</span></span> <span data-ttu-id="c3188-154">Si c’est le cas, il utilise le jeton d’actualisation pour obtenir de nouveaux jetons, puis il met à jour le cache et renvoie le nouveau jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="c3188-154">If it is, then it uses the refresh token to get new tokens, then updates the cache and returns the new access token.</span></span>