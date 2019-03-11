<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="4bf13-101">Dans cet exercice, vous allez créer une inscription de l'application Web Azure AD à l'aide du portail de registre d'applications (ARP).</span><span class="sxs-lookup"><span data-stu-id="4bf13-101">In this exercise, you will create a new Azure AD web application registration using the Application Registry Portal (ARP).</span></span>

1. <span data-ttu-id="4bf13-102">Ouvrez un navigateur et accédez au [portail d'inscription des applications](https://apps.dev.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="4bf13-102">Open a browser and navigate to the [Application Registration Portal](https://apps.dev.microsoft.com).</span></span> <span data-ttu-id="4bf13-103">Connectez-vous à l'aide d'un compte **personnel** (alias Microsoft) ou **compte professionnel ou scolaire**.</span><span class="sxs-lookup"><span data-stu-id="4bf13-103">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="4bf13-104">Sélectionnez **Ajouter une application** en haut de la page.</span><span class="sxs-lookup"><span data-stu-id="4bf13-104">Select **Add an app** at the top of the page.</span></span>

    > [!NOTE]
    > <span data-ttu-id="4bf13-105">Si vous voyez plus d'un bouton **Ajouter une application** sur la page, sélectionnez celui qui correspond à la liste **applications** convergées.</span><span class="sxs-lookup"><span data-stu-id="4bf13-105">If you see more than one **Add an app** button on the page, select the one that corresponds to the **Converged apps** list.</span></span>

1. <span data-ttu-id="4bf13-106">Sur la page **inscrire votre application** , définissez le **nom** de l'application sur **didacticiel de graphique php** et sélectionnez **créer**.</span><span class="sxs-lookup"><span data-stu-id="4bf13-106">On the **Register your application** page, set the **Application Name** to **PHP Graph Tutorial** and select **Create**.</span></span>

    ![Capture d'écran de la création d'une nouvelle application dans le site Web du portail d'inscription des applications](./images/arp-create-app-01.png)

1. <span data-ttu-id="4bf13-108">Sur la page **d'inscription du didacticiel de graphique php** , dans la section **Propriétés** , copiez l' **ID d'application** , car vous en aurez besoin plus tard.</span><span class="sxs-lookup"><span data-stu-id="4bf13-108">On the **PHP Graph Tutorial Registration** page, under the **Properties** section, copy the **Application Id** as you will need it later.</span></span>

    ![Capture d'écran de l'ID de l'application nouvellement créée](./images/arp-create-app-02.png)

1. <span data-ttu-id="4bf13-110">Faites déFiler jusqu'à la section secrets de l' **application** .</span><span class="sxs-lookup"><span data-stu-id="4bf13-110">Scroll down to the **Application Secrets** section.</span></span>

    1. <span data-ttu-id="4bf13-111">Sélectionnez **générer un nouveau mot de passe**.</span><span class="sxs-lookup"><span data-stu-id="4bf13-111">Select **Generate New Password**.</span></span>
    1. <span data-ttu-id="4bf13-112">Dans la boîte de dialogue **nouveau mot de passe généré** , copiez le contenu de la zone, car vous en aurez besoin plus tard.</span><span class="sxs-lookup"><span data-stu-id="4bf13-112">In the **New password generated** dialog, copy the contents of the box as you will need it later.</span></span>

        > [!IMPORTANT]
        > <span data-ttu-id="4bf13-113">Ce mot de passe ne s'affiche plus, vérifiez que vous le copiez maintenant.</span><span class="sxs-lookup"><span data-stu-id="4bf13-113">This password is never shown again, so make sure you copy it now.</span></span>

    ![Capture d'écran du mot de passe d'une application nouvellement créée](./images/arp-create-app-03.png)

1. <span data-ttu-id="4bf13-115">Faites déFiler \*\*\*\* vers le bas jusqu'à la section plateformes.</span><span class="sxs-lookup"><span data-stu-id="4bf13-115">Scroll down to the **Platforms** section.</span></span>

    1. <span data-ttu-id="4bf13-116">Sélectionnez **Ajouter une plateforme**.</span><span class="sxs-lookup"><span data-stu-id="4bf13-116">Select **Add Platform**.</span></span>
    1. <span data-ttu-id="4bf13-117">Dans la boîte de dialogue **Ajouter une plateforme** , sélectionnez **Web**.</span><span class="sxs-lookup"><span data-stu-id="4bf13-117">In the **Add Platform** dialog, select **Web**.</span></span>

        ![Capture d'écran création d'une plateforme pour l'application](./images/arp-create-app-04.png)

    1. <span data-ttu-id="4bf13-119">Dans la zone plateforme **Web** , entrez l'URL `http://localhost:8000/callback` de l'URL de redirection. \*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="4bf13-119">In the **Web** platform box, enter the URL `http://localhost:8000/callback` for the **Redirect URLs**.</span></span>

        ![Capture d'écran de la plateforme Web récemment ajoutée pour l'application](./images/arp-create-app-05.png)

1. <span data-ttu-id="4bf13-121">Faites déFiler la page jusqu'en bas et sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="4bf13-121">Scroll to the bottom of the page and select **Save**.</span></span>