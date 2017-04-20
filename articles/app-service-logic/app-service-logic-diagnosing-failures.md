<properties
   pageTitle="Diagnostiquer les défaillances d’applications logique | Microsoft Azure"
   description="Approches communes pour comprendre où logique applications échouent"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="diagnosing-logic-app-failures"></a>Diagnostiquer les défaillances d’application logique

Si vous rencontrez des problèmes ou défaillances avec la fonctionnalité logique applications de Service d’application Azure, quelques approches peuvent vous aider à mieux comprendre d'où proviennent les échecs.  

## <a name="azure-portal-tools"></a>Outils portails Azure

Le portail Azure fournit plusieurs outils pour diagnostiquer chaque application logique à chaque étape.

### <a name="trigger-history"></a>Historique du déclencheur

Chaque application logique comporte au moins un déclencheur. Si vous constatez que les applications ne sont pas licenciement, le premier lieu pour trouver des informations supplémentaires est l’historique de déclencheur. Vous pouvez accéder à l’historique de déclencheur sur la carte principale de l’application logique.

![Vous avez trouvé l’historique de déclencheur][1]

Répertorie toutes les tentatives de déclencheur votre application logique a été. Vous pouvez cliquer sur chaque tentative de déclencheur pour obtenir le prochain niveau de détail (en particulier, les entrées ou sorties à l’origine de la tentative de déclencheur). Si vous voyez des déclencheurs échecs, cliquez sur la tentative de déclencheur et explorez le lien **sorties** pour afficher des messages d’erreur qui a peut-être été générés (par exemple, pour les informations d’identification FTP non valides).

Les différents statuts que vous pouvez voir sont :

* **Ignoré**. Il interrogé le point de terminaison pour vérifier les données et reçu une réponse qu’aucune donnée n’est disponible.
* **A réussi**. Le déclencheur reçu une réponse que les données étaient disponibles. Il peut s’agir d’un déclencheur manuel, un déclencheur périodicité ou d’un déclencheur d’interrogation. Cela sera probablement s’accompagner dont l’état de **déclenchement**, mais que c’est peut-être pas si vous avez une condition ou une commande SplitOn en mode code qui n’a pas été satisfait.
* **A échoué**. Une erreur est générée.

#### <a name="starting-a-trigger-manually"></a>Démarrez manuellement un déclencheur

Si vous souhaitez que l’application logique pour vérifier un déclencheur disponible immédiatement (sans attendre la prochaine répétition), vous pouvez cliquer sur **Sélectionner le déclencheur** sur la carte principale pour forcer une vérification. Par exemple, cliquez sur ce lien avec un déclencheur Dropbox entraînera le flux de travail interroger immédiatement Dropbox pour les nouveaux fichiers.

### <a name="run-history"></a>Exécuter l’historique

Chaque qui est déclencheur résultats lors de l’exécution. Vous pouvez accéder aux informations exécuter à partir de la carte principale, qui contient une grande quantité d’informations qui peuvent être utiles de comprendre où est passée pendant le flux de travail.

![Vous avez trouvé l’historique d’exécution][2]

Une exécution affiche l’un des statuts suivants :

* **A réussi**. Toutes les actions a réussi, ou, si une erreur s’est produite, il a été gérée par une action qui se sont produites plus loin dans le flux de travail. Autrement dit, il a été gérée par une action qui a été configurée pour s’exécuter après une action qui a échoué.
* **A échoué**. Au moins une action a eu une défaillance qui n’a pas été traitée par une action plus loin dans le flux de travail.
* **Annulé**. Le flux de travail en cours d’exécution, mais avez reçu une demande d’annulation.
* **En cours d’exécution**. Le flux de travail est en cours d’exécution. Cela peut se produire des flux de travail qui est limitée ou en raison de l’offre de Service d’application en cours. Sur la [page de tarification](https://azure.microsoft.com/pricing/details/app-service/plans/) pour plus d’informations, consultez limites d’action. Configuration des diagnostics (les graphiques ci-dessous l’historique exécuter) peuvent également fournir d’informations sur les événements de limitation qui se produisent.

Lorsque vous recherchez un historique exécution, vous pouvez extraire pour plus d’informations.  

#### <a name="trigger-outputs"></a>Sorties de déclencheur

Déclencheur sorties affichent les données qui a été reçues à partir du déclencheur. Cela peut vous aider à déterminer si toutes les propriétés retourné comme prévu.

>[AZURE.NOTE] Il peut être nécessaire de comprendre comment les applications logique fonctionnalité [gère les différents types de contenu](app-service-logic-content-type.md) si vous voyez tout contenu que vous ne comprenez pas.

![Exemples de sortie déclencheur][3]

#### <a name="action-inputs-and-outputs"></a>Action et sorties

Vous pouvez explorer les entrées et des sorties reçue une action. Ceci est utile pour comprendre la taille et la forme des sorties, ainsi que sous pour voir les messages d’erreur qui ont été générés.

![Action et sorties][4]

## <a name="debugging-workflow-runtime"></a>Débogage pour l’exécution du flux de travail

Outre la surveillance les entrées, sorties et déclencheurs d’une série, il peut être utile ajouter quelques étapes au sein d’un flux de travail pour vous aider avec débogage. [RequestBin](http://requestb.in) est un outil puissant que vous pouvez ajouter une étape dans un flux de travail. À l’aide de RequestBin, vous pouvez configurer un inspecteur de demande HTTP pour déterminer la taille exacte, la forme et le format d’une requête HTTP. Vous pouvez créer un nouveau RequestBin et collez l’URL dans une application logique action de publication HTTP en même temps que vous souhaitez tester le contenu du corps (par exemple, une expression ou une autre étape sortie). Après avoir exécuté l’application logique, vous pouvez actualiser votre RequestBin pour voir comment la demande a été mis en forme comme il a été généré à partir du moteur d’applications logique.




<!-- image references -->
[1]: ./media/app-service-logic-diagnosing-failures/triggerHistory.PNG
[2]: ./media/app-service-logic-diagnosing-failures/runHistory.PNG
[3]: ./media/app-service-logic-diagnosing-failures/triggerOutputsLink.PNG
[4]: ./media/app-service-logic-diagnosing-failures/ActionOutputs.PNG
