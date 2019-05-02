# <a name="how-to-run-the-completed-project"></a>Exécution du projet terminé

## <a name="prerequisites"></a>Conditions préalables

Pour exécuter le projet terminé dans ce dossier, vous avez besoin des éléments suivants:

- [Php](http://php.net/downloads.php) installé sur votre ordinateur de développement. Si vous n’avez pas PHP, visitez le lien précédent pour obtenir les options de téléchargement. (**Remarque:** ce didacticiel a été écrit avec la version 7,2 de php. Les étapes de ce guide peuvent fonctionner avec d’autres versions, mais cela n’a pas été testé.)
- [Composer](https://getcomposer.org/) installé sur votre ordinateur de développement.
- [Laravel](https://laravel.com/) installé sur votre ordinateur de développement.
- Soit un compte Microsoft personnel avec une boîte aux lettres sur Outlook.com, soit un compte professionnel ou scolaire Microsoft.

Si vous n’avez pas de compte Microsoft, vous disposez de deux options pour obtenir un compte gratuit:

- Vous pouvez vous [inscrire pour obtenir un nouveau compte Microsoft personnel](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).
- Vous pouvez vous [inscrire au programme pour les développeurs office 365](https://developer.microsoft.com/office/dev-program) pour obtenir un abonnement gratuit à Office 365.

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a>Enregistrer une application Web avec le centre d’administration Azure Active Directory

1. Ouvrez un navigateur et accédez au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com). Connectez-vous à l’aide d’un **compte personnel** (compte Microsoft) ou d’un **compte professionnel ou scolaire**.

1. Sélectionnez **Azure Active Directory** dans le volet de navigation de gauche, puis sélectionnez **inscriptions des applications** sous **gérer**.

    ![Capture d’écran des inscriptions d’application ](/tutorial/images/aad-portal-app-registrations.png)

1. Sélectionnez **Nouvelle inscription**. Sur la page **Inscrire une application**, définissez les valeurs comme suit.

    - Définissez le **Nom** sur `PHP Graph Tutorial`.
    - Définissez les **Types de comptes pris en charge** sur **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.
    - Sous **URI de redirection**, définissez la première flèche déroulante sur `Web`, et la valeur sur `http://localhost:8000/callback`.

    ![Capture d’écran de la page inscrire une application](/tutorial/images/aad-register-an-app.png)

1. Choisissez **Inscrire**. Sur la page **didacticiel de graphique php** , copiez la valeur de l' **ID d’application (client)** et enregistrez-la, vous en aurez besoin à l’étape suivante.

    ![Capture d’écran de l’ID d’application de la nouvelle inscription de l’application](/tutorial/images/aad-application-id.png)

1. Sélectionnez **Certificats et secrets** sous **Gérer**. Sélectionnez le bouton **Nouveau secret client**. Entrez une valeur dans **Description**, sélectionnez une des options pour **Expire le**, puis choisissez **Ajouter**.

    ![Capture d’écran de la boîte de dialogue Ajouter une clé secrète client](/tutorial/images/aad-new-client-secret.png)

1. Copiez la valeur du secret client avant de quitter cette page. Vous en aurez besoin à l’étape suivante.

    > [!IMPORTANT]
    > Ce secret client n’apparaîtra plus jamais, aussi veillez à le copier maintenant.

    ![Capture d’écran de la clé secrète client récemment ajoutée](/tutorial/images/aad-copy-client-secret.png)

## <a name="configure-the-sample"></a>Configurer l’exemple

1. Renommez `.env.example` le fichier `.env`.
1. Modifiez le `.env` fichier et effectuez les modifications suivantes.
    1. Remplacez `YOUR_APP_ID_HERE` par l' **ID d’application** que vous avez obtenu à partir du portail d’inscription des applications.
    1. Remplacez `YOUR_APP_PASSWORD_HERE` par le mot de passe que vous avez obtenu à partir du portail d’inscription des applications.
1. Dans votre interface de ligne de commande (CLI), accédez à ce répertoire et exécutez la commande suivante pour installer les conditions requises.

    ```Shell
    composer install
    ```

1. Dans votre interface de ligne de commande (CLI), exécutez la commande suivante pour générer une clé d’application.

    ```Shell
    php artisan key:generate
    ```

## <a name="run-the-sample"></a>Exécution de l’exemple

1. Exécutez la commande suivante dans votre interface CLI pour démarrer l’application.

    ```Shell
    php artisan serve
    ```

1. Ouvrez un navigateur et accédez à `http://localhost:8000`.