# <a name="contributing-to-microsoft-graph-training-repositories"></a>Contribution aux référentiels de formation Microsoft Graph

Merci d’avoir contribué à ce projet ! Avant d’envoyer votre requête de tirage, veillez à prendre en compte les points suivants.

## <a name="overview"></a>Vue d’ensemble

Le code de ce référentiel répond à trois objectifs :

- Les fichiers de démarque dans le dossier [Tutorial](/tutorial) sont publiés sous la forme d’un didacticiel sur la page des [didacticiels Microsoft Graph](https://docs.microsoft.com/graph/tutorials) .
- L’exemple de projet dans le dossier [Demo](/demo) est la source pour un [démarrage rapide de Microsoft Graph](https://developer.microsoft.com/graph/quick-start).**\***
- L’exemple de projet dans le dossier Demo est également téléchargeable directement auprès de GitHub et doit être exécuté en l’après d’une configuration simple.

> **\*** Tous les référentiels d’apprentissage ne sont pas disponibles en tant que Démarrages rapides (encore).

Il est important de garder à l’esprit que, étant donné que les modifications apportées à un emplacement *peuvent* nécessiter des modifications apportées dans un autre, pour maintenir la synchronisation. Whereever possible, les fichiers de démarque font référence directement aux fichiers de code source (à l’aide d’une `:::code` syntaxe personnalisée), de sorte que la mise à jour du code de la source met automatiquement à jour le code dans la démarque.

## <a name="updating-code"></a>Mise à jour du code

La `:::code` syntaxe utilisée dans la démarque dépend de commentaires spécifiques dans le fichier de code source. Ces commentaires ressemblent à ce qui suit :

```csharp
// <MySnippet>
Console.WriteLine("Hello World!");
// </MySnippet>
```

Si vous mettez à jour le code entre ces commentaires « marque », les fichiers de démarque reçoivent automatiquement ces modifications lorsqu’elles sont publiées sur le site de documentation Microsoft Graph. Si vous mettez à jour le code en dehors de ces commentaires, il est fort probable que vous devez mettre à jour la démarque correspondante.

## <a name="adding-features"></a>Ajout de fonctionnalités

Bien que l’enthousiasme soit apprécié, n’envoyez pas de demandes d’extraction pour ajouter de nouvelles fonctionnalités à l’exemple. Étant donné que ce référentiel est principalement un didacticiel de création de votre première application, le jeu de fonctionnalités est limité, par conception.

## <a name="submitting-pull-requests"></a>Envoi de demandes de tirage

Envoyez toutes les requêtes de tirage à la `master` succursale.

## <a name="when-do-changes-get-published"></a>Quand les modifications sont-elles publiées ?

La publication de mises à jour sur le site de [didacticiels Microsoft Graph](https://docs.microsoft.com/graph/tutorials) n’est pas automatique. Les modifications doivent d’abord être promues vers la `live` succursale, puis une build doit être déclenchée par les administrateurs du site. Cette opération est généralement réalisée en fonction des besoins.

## <a name="code-of-conduct"></a>Code de conduite

Ce projet a adopté le [code de conduite Microsoft Open Source](https://opensource.microsoft.com/codeofconduct/). Pour plus d’informations, reportez-vous à la [FAQ relative au code de conduite](https://opensource.microsoft.com/codeofconduct/faq/) ou contactez [opencode@microsoft.com](mailto:opencode@microsoft.com) pour toute question ou tout commentaire.
