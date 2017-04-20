<properties
  pageTitle="Azure IoT Suite FAQ | Microsoft Azure"
  description="Forum aux questions IoT gamme"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="09/26/2016"
  ms.author="araguila"/>
   
# <a name="frequently-asked-questions-for-iot-suite"></a>Forum aux questions IoT gamme

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Quelle est la différence entre la suppression d’un groupe de ressources dans le portail Azure et en cliquant sur Supprimer une solution préconfigurés dans azureiotsuite.com ?

- Si vous supprimez la solution préconfigurée dans [azureiotsuite.com][lnk-azureiotsuite], vous supprimez toutes les ressources qui ont été mis en service lorsque vous avez créé la solution préconfigurée ; Si vous avez ajouté des ressources supplémentaires au groupe de ressources, celles-ci sont également supprimés. 

- Si vous supprimez le groupe de ressources dans le [portail Azure][lnk-azure-portal], vous supprimez uniquement les ressources dans ce groupe de ressources ; Vous devez également supprimer l’application Azure Active Directory associée à la solution préconfigurée dans le [portail classique Azure][lnk-classic-portal].

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Combien d’instances IoT concentrateur puis-je configurer un abonnement ? 

Dix. Vous pouvez créer une [Azure prend en charge les tickets] [ link-azuresupportticket] pour déclencher cette limite, mais par défaut, vous seulement pouvez configurer les dix Hubs IoT par abonnement, comme indiqué dans [la limite d’abonnements Azure][link-azuresublimits]. Par conséquent, étant donné que chaque solution préconfigurée dispositions un nouveau concentrateur IoT, vous pouvez prévoir uniquement jusqu'à dix solutions préconfigurées dans un abonnement donnée. 

### <a name="how-many-documentdb-instances-can-i-provision-in-a-subscription"></a>Combien d’instances DocumentDB puis-je configurer un abonnement ?

Cinquante. Vous pouvez créer une [Azure prend en charge les tickets] [ link-azuresupportticket] pour déclencher cette limite, mais par défaut, vous pouvez configurer uniquement cinquante instances DocumentDB par abonnement. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Combien API de cartes Bing gratuit puis-je configurer un abonnement ?

Deux. Vous pouvez créer uniquement deux interne Transactions niveau 1 Bing Maps pour les plans d’entreprise dans un abonnement Azure. La solution d’analyse à distance est configurée par défaut avec l’offre interne Transactions niveau 1. Par conséquent, vous pouvez prévoir uniquement jusqu'à deux solutions contrôle à distance pour un abonnement sans aucune modification.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>J’ai un déploiement de solution de surveillance à distance avec une carte statique, comment ajouter une carte Bing interactive ? 
1. Obtenir votre API Bing Maps pour QueryKey d’entreprise à partir [d’Azure portal][lnk-azure-portal]: 
 1. Accédez au groupe de ressources où se trouve votre API Bing Maps pour entreprise dans le [portail Azure][lnk-azure-portal].
 2. Cliquez sur tous les paramètres, puis gestion. 
 3. Vous remarquerez deux clés : clé principale et QueryKey. Copiez la valeur pour QueryKey.

     > [AZURE.NOTE] Vous ne possédez une API Bing Maps pour le compte d’entreprise ? Créer un dans le [portail Azure] [ lnk-azure-portal] en cliquant + nouveau, recherche d’API Bing Maps pour les entreprises et suivez les invites pour créer.

2. Extraire vers le bas le dernier code à partir de la [Azure-IoT-de surveillance à distance][lnk-remote-monitoring-github].

3. Exécuter une variable locale ou sur le cloud déploiement en suivant les instructions de déploiement de ligne de commande dans le dossier /docs/ dans le référentiel. 

4. Une fois que vous avez exécuté une variable locale ou sur le cloud déploiement, regardez dans votre dossier racine pour la *. fichier user.config créé pendant le déploiement. Ouvrir ce fichier dans un éditeur de texte. 

5. Modifiez la ligne suivante pour inclure la valeur que vous avez copiée à partir de votre QueryKey : 
   
  `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Puis-je créer une solution préconfigurée si j’utilise Microsoft Azure pour DreamSpark ?
Pour le moment, vous ne pouvez pas créer une solution préconfigurée avec un [Microsoft Azure pour DreamSpark] [ lnk-dreamspark] compte. Toutefois, vous pouvez créer un [compte d’essai gratuit pour Azure] [ lnk-30daytrial] en quelques minutes qui permettra que vous créez une solution préconfigurée.

### <a name="how-do-i-delete-an-aad-tenant"></a>Comment supprimer un client AAD ?

Consultez blog de Eric Golpe [procédure pas à pas de la suppression d’un client AD Azure][lnk-delete-aad-tennant].

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également Explorer les autres fonctionnalités et des fonctions des solutions Suite IoT préconfigurés :

- [Présentation de la solution maintenance prédictive préconfigurée][lnk-predictive-overview]
- [Sécurité IoT d’emblée][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx
