# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="8cf24-101">Exécution du projet terminé</span><span class="sxs-lookup"><span data-stu-id="8cf24-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8cf24-102">Conditions requises</span><span class="sxs-lookup"><span data-stu-id="8cf24-102">Prerequisites</span></span>

<span data-ttu-id="8cf24-103">Pour exécuter le projet terminé dans ce dossier, vous avez besoin des éléments suivants:</span><span class="sxs-lookup"><span data-stu-id="8cf24-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="8cf24-104">[Php](http://php.net/downloads.php) installé sur votre ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="8cf24-104">[PHP](http://php.net/downloads.php) installed on your development machine.</span></span> <span data-ttu-id="8cf24-105">Si vous n'avez pas PHP, visitez le lien précédent pour obtenir les options de téléchargement.</span><span class="sxs-lookup"><span data-stu-id="8cf24-105">If you do not have PHP, visit the previous link for download options.</span></span> <span data-ttu-id="8cf24-106">(**Remarque:** ce didacticiel a été écrit avec la version 7,2 de php.</span><span class="sxs-lookup"><span data-stu-id="8cf24-106">(**Note:** This tutorial was written with PHP version 7.2.</span></span> <span data-ttu-id="8cf24-107">Les étapes de ce guide peuvent fonctionner avec d'autres versions, mais cela n'a pas été testé.)</span><span class="sxs-lookup"><span data-stu-id="8cf24-107">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="8cf24-108">[Composer](https://getcomposer.org/) installé sur votre ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="8cf24-108">[Composer](https://getcomposer.org/) installed on your development machine.</span></span>
- <span data-ttu-id="8cf24-109">[Laravel](https://laravel.com/) installé sur votre ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="8cf24-109">[Laravel](https://laravel.com/) installed on your development machine.</span></span>
- <span data-ttu-id="8cf24-110">Soit un compte Microsoft personnel avec une boîte aux lettres sur Outlook.com, soit un compte professionnel ou scolaire Microsoft.</span><span class="sxs-lookup"><span data-stu-id="8cf24-110">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

<span data-ttu-id="8cf24-111">Si vous n'avez pas de compte Microsoft, vous disposez de deux options pour obtenir un compte gratuit:</span><span class="sxs-lookup"><span data-stu-id="8cf24-111">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="8cf24-112">Vous pouvez vous [inscrire pour obtenir un nouveau compte Microsoft personnel](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span><span class="sxs-lookup"><span data-stu-id="8cf24-112">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="8cf24-113">Vous pouvez vous [inscrire au programme pour les développeurs office 365](https://developer.microsoft.com/office/dev-program) pour obtenir un abonnement gratuit à Office 365.</span><span class="sxs-lookup"><span data-stu-id="8cf24-113">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-application-registration-portal"></a><span data-ttu-id="8cf24-114">Enregistrer une application Web avec le portail d'inscription des applications</span><span class="sxs-lookup"><span data-stu-id="8cf24-114">Register a web application with the Application Registration Portal</span></span>

1. <span data-ttu-id="8cf24-115">Ouvrez un navigateur et accédez au [portail d'inscription des applications](https://apps.dev.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="8cf24-115">Open a browser and navigate to the [Application Registration Portal](https://apps.dev.microsoft.com).</span></span> <span data-ttu-id="8cf24-116">Connectez-vous à l'aide d'un compte **personnel** (alias Microsoft) ou **compte professionnel ou scolaire**.</span><span class="sxs-lookup"><span data-stu-id="8cf24-116">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="8cf24-117">Sélectionnez **Ajouter une application** en haut de la page.</span><span class="sxs-lookup"><span data-stu-id="8cf24-117">Select **Add an app** at the top of the page.</span></span>

    > <span data-ttu-id="8cf24-118">**Remarque:** Si vous voyez plus d'un bouton **Ajouter une application** sur la page, sélectionnez celui qui correspond à la liste **applications** convergées.</span><span class="sxs-lookup"><span data-stu-id="8cf24-118">**Note:** If you see more than one **Add an app** button on the page, select the one that corresponds to the **Converged apps** list.</span></span>

1. <span data-ttu-id="8cf24-119">Sur la page **inscrire votre application** , définissez le **nom** de l'application sur **didacticiel de graphique php** et sélectionnez **créer**.</span><span class="sxs-lookup"><span data-stu-id="8cf24-119">On the **Register your application** page, set the **Application Name** to **PHP Graph Tutorial** and select **Create**.</span></span>

    ![Capture d'écran de la création d'une nouvelle application dans le site Web du portail d'inscription des applications](/tutorial/images/arp-create-app-01.png)

1. <span data-ttu-id="8cf24-121">Sur la page **d'inscription du didacticiel de graphique php** , dans la section **Propriétés** , copiez l' **ID d'application** , car vous en aurez besoin plus tard.</span><span class="sxs-lookup"><span data-stu-id="8cf24-121">On the **PHP Graph Tutorial Registration** page, under the **Properties** section, copy the **Application Id** as you will need it later.</span></span>

    ![Capture d'écran de l'ID de l'application nouvellement créée](/tutorial/images/arp-create-app-02.png)

1. <span data-ttu-id="8cf24-123">Faites déFiler jusqu'à la section secrets de l' **application** .</span><span class="sxs-lookup"><span data-stu-id="8cf24-123">Scroll down to the **Application Secrets** section.</span></span>

    1. <span data-ttu-id="8cf24-124">Sélectionnez **générer un nouveau mot de passe**.</span><span class="sxs-lookup"><span data-stu-id="8cf24-124">Select **Generate New Password**.</span></span>
    1. <span data-ttu-id="8cf24-125">Dans la boîte de dialogue **nouveau mot de passe généré** , copiez le contenu de la zone, car vous en aurez besoin plus tard.</span><span class="sxs-lookup"><span data-stu-id="8cf24-125">In the **New password generated** dialog, copy the contents of the box as you will need it later.</span></span>

        > <span data-ttu-id="8cf24-126">**Important:** Ce mot de passe ne s'affiche plus, vérifiez que vous le copiez maintenant.</span><span class="sxs-lookup"><span data-stu-id="8cf24-126">**Important:** This password is never shown again, so make sure you copy it now.</span></span>

    ![Capture d'écran du mot de passe d'une application nouvellement créée](/tutorial/images/arp-create-app-03.png)

1. <span data-ttu-id="8cf24-128">Faites déFiler \*\*\*\* vers le bas jusqu'à la section plateformes.</span><span class="sxs-lookup"><span data-stu-id="8cf24-128">Scroll down to the **Platforms** section.</span></span>

    1. <span data-ttu-id="8cf24-129">Sélectionnez **Ajouter une plateforme**.</span><span class="sxs-lookup"><span data-stu-id="8cf24-129">Select **Add Platform**.</span></span>
    1. <span data-ttu-id="8cf24-130">Dans la boîte de dialogue **Ajouter une plateforme** , sélectionnez **Web**.</span><span class="sxs-lookup"><span data-stu-id="8cf24-130">In the **Add Platform** dialog, select **Web**.</span></span>

        ![Capture d'écran création d'une plateforme pour l'application](/tutorial/images/arp-create-app-04.png)

    1. <span data-ttu-id="8cf24-132">Dans la zone plateforme **Web** , entrez l'URL `http://localhost:8000/callback` de l'URL de redirection. \*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="8cf24-132">In the **Web** platform box, enter the URL `http://localhost:8000/callback` for the **Redirect URLs**.</span></span>

        ![Capture d'écran de la plateforme Web récemment ajoutée pour l'application](/tutorial/images/arp-create-app-05.png)

1. <span data-ttu-id="8cf24-134">Faites déFiler la page jusqu'en bas et sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="8cf24-134">Scroll to the bottom of the page and select **Save**.</span></span>

## <a name="configure-the-sample"></a><span data-ttu-id="8cf24-135">Configurer l'exemple</span><span class="sxs-lookup"><span data-stu-id="8cf24-135">Configure the sample</span></span>

1. <span data-ttu-id="8cf24-136">Renommez `.env.example` le fichier `.env`.</span><span class="sxs-lookup"><span data-stu-id="8cf24-136">Rename the `.env.example` file to `.env`.</span></span>
1. <span data-ttu-id="8cf24-137">Modifiez le `.env` fichier et effectuez les modifications suivantes.</span><span class="sxs-lookup"><span data-stu-id="8cf24-137">Edit the `.env` file and make the following changes.</span></span>
    1. <span data-ttu-id="8cf24-138">Remplacez `YOUR_APP_ID_HERE` par l' **ID d'application** que vous avez obtenu à partir du portail d'inscription des applications.</span><span class="sxs-lookup"><span data-stu-id="8cf24-138">Replace `YOUR_APP_ID_HERE` with the **Application Id** you got from the App Registration Portal.</span></span>
    1. <span data-ttu-id="8cf24-139">Remplacez `YOUR_APP_PASSWORD_HERE` par le mot de passe que vous avez obtenu à partir du portail d'inscription des applications.</span><span class="sxs-lookup"><span data-stu-id="8cf24-139">Replace `YOUR_APP_PASSWORD_HERE` with the password you got from the App Registration Portal.</span></span>
1. <span data-ttu-id="8cf24-140">Dans votre interface de ligne de commande (CLI), accédez à ce répertoire et exécutez la commande suivante pour installer les conditions requises.</span><span class="sxs-lookup"><span data-stu-id="8cf24-140">In your command-line interface (CLI), navigate to this directory and run the following command to install requirements.</span></span>

    ```Shell
    composer install
    ```
1. <span data-ttu-id="8cf24-141">Dans votre interface de ligne de commande (CLI), exécutez la commande suivante pour générer une clé d'application.</span><span class="sxs-lookup"><span data-stu-id="8cf24-141">In your command-line interface (CLI), run the following command to generate an application key.</span></span>

    ```Shell
    php artisan key:generate
    ```

## <a name="run-the-sample"></a><span data-ttu-id="8cf24-142">Exécution de l’exemple</span><span class="sxs-lookup"><span data-stu-id="8cf24-142">Run the sample</span></span>

1. <span data-ttu-id="8cf24-143">Exécutez la commande suivante dans votre interface CLI pour démarrer l'application.</span><span class="sxs-lookup"><span data-stu-id="8cf24-143">Run the following command in your CLI to start the application.</span></span>

    ```Shell
    php artisan serve
    ```

1. <span data-ttu-id="8cf24-144">Ouvrez un navigateur et accédez à `http://localhost:8000`.</span><span class="sxs-lookup"><span data-stu-id="8cf24-144">Open a browser and browse to `http://localhost:8000`.</span></span>