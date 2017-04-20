<properties
   pageTitle="Test de votre offre de modèle de solution de Marketplace | Microsoft Azure"
   description="Comprendre comment tester votre offre de modèle de solution de Azure Marketplace."
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
   ms.date="12/04/2015"
   ms.author="hascipio; v-divte" />

# <a name="test-your-solution-template-offer-in-staging"></a>Tester votre offre de modèle de solution de mise en attente
Environnement intermédiaire signifie déployer votre offre privé « sandbox » dans laquelle vous pouvez tester et vérifier ses fonctionnalités avant d’installer en production. L’offre s’affiche dans les intermédiaires comme s’il s’agissait à un client qui a déployé. Votre offre doit être certifié vers mise en attente.

Une fois que l’offre est mis en place, vous pouvez afficher et tester l’offre dans le [Portail Azure](https://portal.azure.com/).

Suivez les étapes ci-dessous pour pousser votre offre mis en œuvre et tester dans le [Portail Azure](https://portal.azure.com/):

1.  Accédez au [Portail de publication](https://publish.windowsazure.com) > onglet**Solution modèles** > votre offre > **Publier** > **transmission mis en œuvre**.
2.  Fournir la liste des abonnements Azure que vous utiliserez pour afficher un aperçu et tester votre offre.
3.  Connectez-vous au portail Azure preview à l’aide de l’ID de l’abonnement que vous avez utilisé à l’étape précédente.
4.  Effectuez au moins une série de tests dans le portail Azure aperçu sur les points mentionnés ci-dessous :
  - Assurez-vous que contenu marketing s’affiche correctement dans l’Azure Marketplace.
  - Déploiement de bout en bout de la topologie.
  - Exécuter les tests de performances et le test de contrainte.
  - Assurez-vous que votre topologie respecte les recommandations fournies.

## <a name="next-steps"></a>Étapes suivantes
Si vous êtes satisfait des résultats, puis vous pouvez passer à la phase de publication offre final, **étape 4**: [déploiement de votre offre à la place de marché](marketplace-publishing-push-to-production.md). Dans le cas contraire, apporter des modifications dans votre offre et demander certification à nouveau.

> [AZURE.NOTE] Pour les modifications du contenu marketing, certification n’est pas obligatoire.

Voir [mise en route : comment publier une offre à la place de marché Azure](marketplace-publishing-getting-started.md) pour un guide pour toutes les tâches de publisher.
