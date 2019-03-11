<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="fff0d-101">Ce didacticiel vous apprend à créer une application Web PHP qui utilise l'API Microsoft Graph pour récupérer des informations de calendrier pour un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="fff0d-101">This tutorial teaches you how to build a PHP web app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="fff0d-102">Si vous préférez télécharger simplement le didacticiel terminé, vous pouvez le télécharger de deux manières.</span><span class="sxs-lookup"><span data-stu-id="fff0d-102">If you prefer to just download the completed tutorial, you can download it in two ways.</span></span>
>
> - <span data-ttu-id="fff0d-103">Téléchargez le [démarrage rapide de php](https://developer.microsoft.com/graph/quick-start?platform=option-php) pour obtenir du code de travail en quelques minutes.</span><span class="sxs-lookup"><span data-stu-id="fff0d-103">Download the [PHP quick start](https://developer.microsoft.com/graph/quick-start?platform=option-php) to get working code in minutes.</span></span>
> - <span data-ttu-id="fff0d-104">Téléchargez ou clonez le [référentiel GitHub](https://github.com/microsoftgraph/msgraph-training-phpapp).</span><span class="sxs-lookup"><span data-stu-id="fff0d-104">Download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-phpapp).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fff0d-105">Conditions requises</span><span class="sxs-lookup"><span data-stu-id="fff0d-105">Prerequisites</span></span>

<span data-ttu-id="fff0d-106">Avant de commencer ce didacticiel, [php](http://php.net/downloads.php), [composer](https://getcomposer.org/)et [Laravel](https://laravel.com/) sont installés sur votre ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="fff0d-106">Before you start this tutorial, you should have [PHP](http://php.net/downloads.php), [Composer](https://getcomposer.org/), and [Laravel](https://laravel.com/) installed on your development machine.</span></span>

> [!NOTE]
> <span data-ttu-id="fff0d-107">Ce didacticiel a été écrit avec la version 7,2 de PHP.</span><span class="sxs-lookup"><span data-stu-id="fff0d-107">This tutorial was written with PHP version 7.2.</span></span> <span data-ttu-id="fff0d-108">Les étapes de ce guide peuvent fonctionner avec d'autres versions, mais cela n'a pas été testé.</span><span class="sxs-lookup"><span data-stu-id="fff0d-108">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="fff0d-109">Commentaires</span><span class="sxs-lookup"><span data-stu-id="fff0d-109">Feedback</span></span>

<span data-ttu-id="fff0d-110">Veuillez fournir des commentaires sur ce didacticiel dans le [référentiel GitHub](https://github.com/microsoftgraph/msgraph-training-phpapp).</span><span class="sxs-lookup"><span data-stu-id="fff0d-110">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-phpapp).</span></span>