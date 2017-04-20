<properties
   pageTitle="Guide pour créer un modèle de solution de Marketplace | Microsoft Azure"
   description="Comment créer, certifier et déployer un modèle de Solution Multi-machine virtuelle Image pour obtenir des instructions détaillées acheter sur Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

   <tags
      ms.service="marketplace"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="07/27/2016"
      ms.author="hascipio; v-divte" />

# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>Guide pour créer un modèle de solution pour Azure Marketplace
Étape 1, [l’enregistrement et la création de comptes]à la fin[link-acct-creation], nous vous interactive sur la création d’un modèle de solution Azure compatibles en [techniques conditions préalables pour la création d’un modèle de solution](marketplace-publishing-solution-template-creation-prerequisites.md). À présent, nous vous guidera les étapes de création d’un modèle de solution pour plusieurs ordinateurs virtuels sur le [Portail de publication] [ link-pubportal] de Azure Marketplace.

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>Créer votre offre de modèle de solution dans le portail de publication
Accédez à [https://publish.windowsazure.com](http://publish.windowsazure.com). Lorsque vous vous connectez pour la première fois sur le [Portail de publication](https://publish.windowsazure.com/), utilisez le même compte avec lequel profil vendeur de votre entreprise a été enregistré. Plus tard, vous pouvez ajouter n’importe quel employé de votre société en tant qu’un administrateur de co-création dans le portail de publication.

### <a name="1-select-solution-templates"></a>1. Cliquez sur « Modèles de Solution »

  ![dessin][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2. créer un modèle de solution

  ![dessin][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>3. précédez topologies
Un modèle de solution est un « parent » à l’ensemble de ses topologies. Vous pouvez définir plusieurs topologies dans un modèle offre/solution. Lorsqu’une offre est poussée mis en œuvre, il est déplacé avec toutes ses topologies. Suivez les étapes ci-dessous pour définir votre offre :     

- Créez une topologie : « Identificateur de topologie » est généralement le nom de la topologie pour le modèle de solution. L’identificateur de topologie est utilisée dans l’URL, comme indiqué ci-dessous :

  Azure Marketplace : http://azure.microsoft.com/marketplace/partners/ {PublisherNamespace} / {OfferIdentifier} {TopologyIdentifier}

  Portail Azure : https://portal.azure.com/#gallery/ {PublisherNamespace}. {OfferIdentifier} {TopologyIdentifier}

- Ajouter une nouvelle version.

### <a name="4-get-your-topology-versions-certified"></a>4. obtenir vos versions topologie certifiées
Télécharger un fichier zip qui contient tous les fichiers requis pour la mise en service de cette version particulière de la topologie. Ce fichier zip doit contenir les éléments suivants :

- fichier *mainTemplate.json* et *createUiDefinition.json* dans le répertoire racine.
- Tous les modèles liées et toutes les scripts.

  > [AZURE.TIP] Alors que votre développeurs travaillent sur la création d’une solution topologies de modèle et de leur certifié, l’entreprise, marketing, et/ou juridiques départements de votre entreprise peuvent travailler sur le contenu marketing et juridique.

## <a name="next-steps"></a>Étapes suivantes
À présent que vous avez créé votre modèle de solution et téléchargez le fichier zip, suivez les instructions dans le [contenu marketing Marketplace guide](marketplace-publishing-push-to-staging.md) avant d’installer l’offre de mise en attente. Pour afficher l’ensemble des marketplace publication d’articles, visitez [mise en route : comment publier une offre à la place de marché Azure](marketplace-publishing-getting-started.md).

Vous serez peut également être intéressé ces articles connexes :

- Images de machine virtuelle : [Images de Machine virtuelle dans Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)
- Extensions de machine virtuelle : [Agent machine virtuelle et vue d’ensemble des Extensions machine virtuelle](https://msdn.microsoft.com/library/azure/dn832621.aspx) et [Extensions machine virtuelle Azure et fonctionnalités](https://msdn.microsoft.com/library/azure/dn606311.aspx)
- Gestionnaire de ressources Azure : [propos processeur Azure](../resource-group-authoring-templates.md) et [exemples de modèles processeur Simple](https://github.com/rjmax/ArmExamples)
- Compte de stockage limite : [Comment faire pour Analyseur de limitation du compte de stockage](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) et le [stockage de Premium](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com
