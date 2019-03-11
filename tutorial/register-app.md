<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez créer une inscription de l'application Web Azure AD à l'aide du portail de registre d'applications (ARP).

1. Ouvrez un navigateur et accédez au [portail d'inscription des applications](https://apps.dev.microsoft.com). Connectez-vous à l'aide d'un compte **personnel** (alias Microsoft) ou **compte professionnel ou scolaire**.

1. Sélectionnez **Ajouter une application** en haut de la page.

    > [!NOTE]
    > Si vous voyez plus d'un bouton **Ajouter une application** sur la page, sélectionnez celui qui correspond à la liste **applications** convergées.

1. Sur la page **inscrire votre application** , définissez le **nom** de l'application sur **didacticiel de graphique php** et sélectionnez **créer**.

    ![Capture d'écran de la création d'une nouvelle application dans le site Web du portail d'inscription des applications](./images/arp-create-app-01.png)

1. Sur la page **d'inscription du didacticiel de graphique php** , dans la section **Propriétés** , copiez l' **ID d'application** , car vous en aurez besoin plus tard.

    ![Capture d'écran de l'ID de l'application nouvellement créée](./images/arp-create-app-02.png)

1. Faites déFiler jusqu'à la section secrets de l' **application** .

    1. Sélectionnez **générer un nouveau mot de passe**.
    1. Dans la boîte de dialogue **nouveau mot de passe généré** , copiez le contenu de la zone, car vous en aurez besoin plus tard.

        > [!IMPORTANT]
        > Ce mot de passe ne s'affiche plus, vérifiez que vous le copiez maintenant.

    ![Capture d'écran du mot de passe d'une application nouvellement créée](./images/arp-create-app-03.png)

1. Faites déFiler **** vers le bas jusqu'à la section plateformes.

    1. Sélectionnez **Ajouter une plateforme**.
    1. Dans la boîte de dialogue **Ajouter une plateforme** , sélectionnez **Web**.

        ![Capture d'écran création d'une plateforme pour l'application](./images/arp-create-app-04.png)

    1. Dans la zone plateforme **Web** , entrez l'URL `http://localhost:8000/callback` de l'URL de redirection. ****

        ![Capture d'écran de la plateforme Web récemment ajoutée pour l'application](./images/arp-create-app-05.png)

1. Faites déFiler la page jusqu'en bas et sélectionnez **Enregistrer**.