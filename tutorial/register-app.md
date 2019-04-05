<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="0592c-101">Dans cet exercice, vous allez créer une inscription de l'application Web Azure AD à l'aide du centre d'administration Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="0592c-101">In this exercise, you will create a new Azure AD web application registration using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="0592c-102">Ouvrez un navigateur et accédez au [Centre d'administration Azure Active Directory](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="0592c-102">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="0592c-103">Connectez-vous à l'aide d'un compte **personnel** (alias Microsoft) ou **compte professionnel ou scolaire**.</span><span class="sxs-lookup"><span data-stu-id="0592c-103">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="0592c-104">Sélectionnez **Azure Active Directory** dans le volet de navigation de gauche, puis sélectionnez **inscriptions des applications (aperçu)** sous **gérer**.</span><span class="sxs-lookup"><span data-stu-id="0592c-104">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations (Preview)** under **Manage**.</span></span>

    ![<span data-ttu-id="0592c-105">Capture d'écran des inscriptions d'application</span><span class="sxs-lookup"><span data-stu-id="0592c-105">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="0592c-106">Sélectionnez **nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="0592c-106">Select **New registration**.</span></span> <span data-ttu-id="0592c-107">Sur la page **inscrire une application** , définissez les valeurs comme suit.</span><span class="sxs-lookup"><span data-stu-id="0592c-107">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="0592c-108">Définissez **nom** sur `PHP Graph Tutorial`.</span><span class="sxs-lookup"><span data-stu-id="0592c-108">Set **Name** to `PHP Graph Tutorial`.</span></span>
    - <span data-ttu-id="0592c-109">Définissez les types de comptes **pris en charge** sur **les comptes de tous les comptes d'annuaire et de Microsoft personnels**.</span><span class="sxs-lookup"><span data-stu-id="0592c-109">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="0592c-110">Sous **URI**de redirection, définissez la première liste déroulante sur `Web` et définissez la `http://localhost:8000/callback`valeur sur.</span><span class="sxs-lookup"><span data-stu-id="0592c-110">Under **Redirect URI**, set the first drop-down to `Web` and set the value to `http://localhost:8000/callback`.</span></span>

    ![Capture d'écran de la page inscrire une application](./images/aad-register-an-app.png)

1. <span data-ttu-id="0592c-112">Sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="0592c-112">Choose **Register**.</span></span> <span data-ttu-id="0592c-113">Sur la page **didacticiel de graphique php** , copiez la valeur de l' **ID d'application (client)** et enregistrez-la, vous en aurez besoin à l'étape suivante.</span><span class="sxs-lookup"><span data-stu-id="0592c-113">On the **PHP Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Capture d'écran de l'ID d'application de la nouvelle inscription de l'application](./images/aad-application-id.png)

1. <span data-ttu-id="0592c-115">Sélectionnez **certificats & secrets** sous **gérer**.</span><span class="sxs-lookup"><span data-stu-id="0592c-115">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="0592c-116">Sélectionnez le bouton **nouveau client secrète** .</span><span class="sxs-lookup"><span data-stu-id="0592c-116">Select the **New client secret** button.</span></span> <span data-ttu-id="0592c-117">Entrez une valeur dans **Description** , puis sélectionnez l'une des options \*\*\*\* pour expirer, puis choisissez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="0592c-117">Enter a value in **Description** and select one of the options for **Expires** and choose **Add**.</span></span>

    ![Capture d'écran de la boîte de dialogue Ajouter une clé secrète client](./images/aad-new-client-secret.png)

1. <span data-ttu-id="0592c-119">Copiez la valeur de la clé secrète client avant de quitter cette page.</span><span class="sxs-lookup"><span data-stu-id="0592c-119">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="0592c-120">Vous en aurez besoin à l'étape suivante.</span><span class="sxs-lookup"><span data-stu-id="0592c-120">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="0592c-121">Cette clé secrète client ne s'affiche plus, vérifiez que vous la copiez maintenant.</span><span class="sxs-lookup"><span data-stu-id="0592c-121">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![Capture d'écran de la clé secrète client récemment ajoutée](./images/aad-copy-client-secret.png)