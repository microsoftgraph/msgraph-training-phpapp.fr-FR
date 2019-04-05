<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez créer une inscription de l'application Web Azure AD à l'aide du centre d'administration Azure Active Directory.

1. Ouvrez un navigateur et accédez au [Centre d'administration Azure Active Directory](https://aad.portal.azure.com). Connectez-vous à l'aide d'un compte **personnel** (alias Microsoft) ou **compte professionnel ou scolaire**.

1. Sélectionnez **Azure Active Directory** dans le volet de navigation de gauche, puis sélectionnez **inscriptions des applications (aperçu)** sous **gérer**.

    ![Capture d'écran des inscriptions d'application ](./images/aad-portal-app-registrations.png)

1. Sélectionnez **nouvelle inscription**. Sur la page **inscrire une application** , définissez les valeurs comme suit.

    - Définissez **nom** sur `PHP Graph Tutorial`.
    - Définissez les types de comptes **pris en charge** sur **les comptes de tous les comptes d'annuaire et de Microsoft personnels**.
    - Sous **URI**de redirection, définissez la première liste déroulante sur `Web` et définissez la `http://localhost:8000/callback`valeur sur.

    ![Capture d'écran de la page inscrire une application](./images/aad-register-an-app.png)

1. Sélectionnez **Enregistrer**. Sur la page **didacticiel de graphique php** , copiez la valeur de l' **ID d'application (client)** et enregistrez-la, vous en aurez besoin à l'étape suivante.

    ![Capture d'écran de l'ID d'application de la nouvelle inscription de l'application](./images/aad-application-id.png)

1. Sélectionnez **certificats & secrets** sous **gérer**. Sélectionnez le bouton **nouveau client secrète** . Entrez une valeur dans **Description** , puis sélectionnez l'une des options **** pour expirer, puis choisissez **Ajouter**.

    ![Capture d'écran de la boîte de dialogue Ajouter une clé secrète client](./images/aad-new-client-secret.png)

1. Copiez la valeur de la clé secrète client avant de quitter cette page. Vous en aurez besoin à l'étape suivante.

    > [!IMPORTANT]
    > Cette clé secrète client ne s'affiche plus, vérifiez que vous la copiez maintenant.

    ![Capture d'écran de la clé secrète client récemment ajoutée](./images/aad-copy-client-secret.png)