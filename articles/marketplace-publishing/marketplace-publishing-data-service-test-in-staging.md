<properties
   pageTitle="Test de votre offre de Service de données pour le marché | Microsoft Azure"
   description="Comprendre comment tester votre offre de Service de données de Azure Marketplace."
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
   ms.date="08/26/2016"
   ms.author="hascipio; avikova" />

# <a name="testing-your-data-service-offer-in-staging"></a>Test de votre offre de Service de données dans un environnement intermédiaire

>[AZURE.IMPORTANT] **Pour le moment nous ne sont plus arrivant les nouvelles publications de Service de données. Nouvelle dataservices ne seront pas obtenir approuvées pour la liste.** Si vous avez une application métier SaaS que vous voulez publier sur AppSource vous trouverez plus d’informations [ici](https://appsource.microsoft.com/partners). Si vous avez un applications IaaS ou service développeur que vous voulez publier sur Azure Marketplace, vous pouvez en savoir plus [ici](https://azure.microsoft.com/marketplace/programs/certified/).

Une fois les deux premières étapes de [Création de votre compte Microsoft Developer](marketplace-publishing-accounts-creation-registration.md) et de [Création de votre offre de Service de données dans le portail de publication](marketplace-publishing-data-service-creation.md) , vous êtes prêt pour rendre votre offre disponibles sur le marché Azure. Cette rubrique vous guidera tout d’abord, intermédiaires, étape appelée « Intermédiaire »

Environnement intermédiaire signifie déployer votre offre privé « sandbox » dans laquelle vous pouvez tester et vérifier ses fonctionnalités avant d’installer en production. L’offre s’affichent dans les intermédiaires comme s’il s’agissait à un client qui a déployé.

## <a name="step-1-pushing-your-offer-to-staging"></a>Étape 1. Distribution de votre offre de mise en attente
Pousser votre offre mis en œuvre permet de vous permettent de tester l’offre avant qu’elle est disponible pour les abonnés futures.  Vous pouvez voir comment votre offre s’affichent et fonctionnent pour ceux l’abonnement à vos données.  

  ![dessin](media/marketplace-publishing-data-service-test-in-staging/step-1.1.png)

1.  Ouverture de session dans le [portail de publication](https://publish.windowsazure.com)
2.  Dans la fenêtre de navigation de gauche, sélectionnez **Services de données**
3.  Sélectionnez votre offre que vous souhaitez lancer vers mise en attente. Vous verrez l’écran ci-dessus.
4.  Cliquez sur bouton **De transmission pour la mise en attente** .  
5.  S’il existe des problèmes avec l’offre qui doivent être effectuées avant que reçue mis en œuvre, vous verrez une liste affichée.  Corriger ces éléments en cliquant sur chaque élément dans la liste. Lorsque toutes les corrections effectuées, cliquez à nouveau sur le bouton **transmission mis en œuvre** .

Si aucun problème avec votre offre, vous verrez la fenêtre contextuelle ci-dessous.  

Si vous êtes non planification/non approuvé pour retrouver votre offre dans Azure Portal (actuellement limite le capacité), puis fermez simplement la fenêtre contextuelle.

Pour tester votre Service de données dans le portail Azure (en plus du portail DataMarket), vous avez besoin d’un ID de l’abonnement Azure pour le test.  ID de l’abonnement identifie le compte qui sera autorisé à tester votre offre.  

Couper et coller votre ID de l’abonnement, puis cliquez sur la coche en regard de continuer.

  ![dessin](media/marketplace-publishing-data-service-test-in-staging/step-1.2.png)

> [AZURE.NOTE] Ces abonnements Azure ID sont uniquement requis pour le test et mis en œuvre dans le [Portail de gestion Azure](https://manage.windowsazure.com). Ils ne sont pas requis pour tester dans Azure Marketplace.

L’écran suivant qui s’affiche indique que la publication a lieu en affichant l’icône « en cours » mis en surbrillance jaune ci-dessous. Reçue mis en œuvre prend entre 10 à 15 minutes.  S’il faut plus de temps, tout d’abord actualisez votre navigateur (appuyez sur F5 dans Internet Explorer).  Dans les cas rares où votre offre est toujours reçue mis en œuvre après une heure, cliquez sur le lien au contact dites-nous qu’il existe un problème.

  ![dessin](media/marketplace-publishing-data-service-test-in-staging/step-1.3.png)

Lorsque la diffusion en œuvre termine l’icône « en cours » seront arrêter déplacement et l’état est mise à jour à « Intermédiaire ».  Vous êtes maintenant prêt à tester votre offre.  

## <a name="step-2-test-your-staged-offer-in-datamarket"></a>Étape 2. Tester votre offre intermédiaire dans DataMarket

Cliquez sur le lien suivant le texte **« Voir votre service offrez à... »** Pour afficher l’écran que l’abonné verra s’afficher lorsque votre offre accède à production et s’affichera dans DataMarket.

  ![dessin](media/marketplace-publishing-data-service-test-in-staging/step-2.2.png)

Tester ou vérifier chacun des 12 éléments marqués ci-dessus pour vérifier toutes les logos, prix/transactions, texte, des images, documentation, et des liens sont corrects et qu’il fonctionne correctement.  Il s’agit d’un temps utile pour que les valeurs de test que vous avez entré lors de la création de votre offre ont été remplacés par des valeurs réelles.

1. Logo de l’offre
2. Nom de l’offre
3. Publisher nom/lien au site Web de votre société
4. Catégories de recherche pour votre offre
5. Lien vers le support de votre offre pour aider les abonnés
6. Description contextuelle pour votre offre
7. Plan d’offre illustrant les détails de facturation
8. Créer un lien vers le code d’implémentation
9. Exemples d’images qui illustrent utilisent des données d’offre
10. Métadonnées d’entrée/sortie pour chaque service au sein de l’offre
11. Conditions d’utilisation de l’offre
12. Aperçu des données de l’offre


Enfin, recherchez que le service fonctionne par le biais du Datamarket en cliquant sur le lien « Explorer ce jeu de données ».  Une nouvelle fenêtre s’ouvre dans l’outil nous appelons « Service l’Explorateur » afin que vous pouvez afficher les résultats d’une requête par rapport à votre service.  Dans cette fenêtre, vous pouvez entrer les paramètres nécessaires et consultez les résultats affichées à partir d’une requête sur votre service.   En outre, affichée est l’URL pour votre requête.  

> [AZURE.NOTE] Veillez à consulter la texte de description du service affiché en haut.  Si votre offre est constituée de plusieurs services d’appel, cliquez sur les onglets situés en bas pour basculer vers le service suivant et passez en revue le test.



## <a name="next-step"></a>Étape suivante
Si vous rencontrez des problèmes et que vous avez besoin de vous aider à résoudre les contactez le [Support technique de Publisher Azure]( http://go.microsoft.com/fwlink/?LinkId=272975).

Si vous êtes satisfait et vous êtes prêt à publier votre offre Lisez la documentation de [Demander une approbation à la transmission de Production](marketplace-publishing-push-to-production.md) .

## <a name="see-also"></a>Voir aussi
- [Mise en route : Comment publier une offre à la Azure Marketplace](marketplace-publishing-getting-started.md)
