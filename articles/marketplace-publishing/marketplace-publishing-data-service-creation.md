<properties
   pageTitle="Guide de création d’un Service de données pour le marché | Microsoft Azure"
   description="Comment créer, certifier et déployer un Service de données pour obtenir des instructions détaillées acheter sur Azure Marketplace."
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

# <a name="data-service-publishing-guide-for-the-azure-marketplace"></a>Service de données publication Guide de Azure Marketplace

>[AZURE.IMPORTANT] **Pour le moment nous ne sont plus arrivant les nouvelles publications de Service de données. Nouvelle dataservices ne seront pas obtenir approuvées pour la liste.** Si vous avez une application métier SaaS que vous voulez publier sur AppSource vous trouverez plus d’informations [ici](https://appsource.microsoft.com/partners). Si vous avez un applications IaaS ou service développeur que vous voulez publier sur Azure Marketplace, vous pouvez en savoir plus [ici](https://azure.microsoft.com/marketplace/programs/certified/).

La fin de l’étape 1, [la création de comptes et l’enregistrement](marketplace-publishing-accounts-creation-registration.md), nous guidé vous [Général vont](marketplace-publishing-pre-requisites.md) et [Exigences techniques](marketplace-publishing-data-service-creation-prerequisites.md) d’une offre de Service de données sur Azure Marketplace. À présent, nous vous guidera étapes de création d’une offre d’un Service de données dans le [Portail de publication] [ link-pubportal] de Azure Marketplace.

## <a name="1---login-to-the-publishing-portal"></a>1. Connectez-vous à la publication portail.

Accédez à [https://publish.windowsazure.com](https://publish.windowsazure.com. )

**Pour première connexion au portail de publication, utilisez le même compte avec lesquels vendeur profil votre société a été enregistré dans le centre de développement.**  (Vous pouvez ultérieurement ajouter n’importe quel employé de votre société en tant qu’un administrateur de co-création dans le portail de publication).

Cliquez sur la vignette de **publier une Data Services** s’il s’agit de la première connexion au portail de publication.

## <a name="2---choose-data-services-in-the-navigation-menu-on-the-left-side"></a>2. Cliquez sur **Services de données** dans le menu de navigation sur le côté gauche.

  ![dessin](media/marketplace-publishing-data-service-creation/pubportal-main-nav.png)

## <a name="3---create-a-new-data-service"></a>3. création d’un Service de données

Entrer un titre pour votre nouvelle offre de Service de données, cliquez sur « + » sur la droite.

  ![dessin](media/marketplace-publishing-data-service-creation/step-3.png)

## <a name="4---review-the-sub-menu-under-the-newly-created-data-service-in-the-navigation-menu"></a>4. passer en revue le sous-menu sous le Service de données nouvellement créé dans le menu de navigation.

Cliquez sur l’onglet de la **procédure pas à pas** et passez en revue toutes les étapes nécessaires requises pour publier correctement le Service de données sur Azure Marketplace.

> [AZURE.TIP] Vous pouvez toujours cliquer sur les liens dans la page « Procédure pas à pas » ou utiliser les onglets sur sous-menu de l’offre Service de données sur le côté gauche.

## <a name="5---create-a-new-plan"></a>5. créer une nouvelle offre.

### <a name="offers-plans-transactions"></a>Les offres, offres, transactions.

Chaque offre peut avoir plusieurs offres, mais doit avoir au moins un (1) offre. Lorsque les utilisateurs finaux s’abonner à votre offre qu’ils s’abonner pour un de Plan de l’offre. Chaque plan définit la façon dont les utilisateurs finaux seront en mesure d’utiliser votre service.

Actuellement Azure Marketplace prise en charge des modèle de base Transaction d’abonnement mensuel uniquement pour les Services de données, c'est-à-dire que les utilisateurs finaux payer un abonnement mensuel selon le prix de l’offre spécifique ils abonnés à et seront en mesure de consommer chaque nombre de mois de transaction définie par l’offre.

Chaque Transaction généralement définie comme nombre d’enregistrements renvoyés par votre Service de données basée sur la requête envoyée au Service. La valeur par défaut est 100. Nombre de transactions renvoyé à chaque requête sera nombre d’enregistrements divisés par 100 et est arrondi à l’entier le plus proche.

Il est chargé de calque Azure Marketplace Service de surveiller (compteur) nombre de transactions consommées par chaque requête.

> [AZURE.IMPORTANT] Les utilisateurs finaux qui atteint la limite de transaction au cours du mois seront bloquées de continuer à utiliser le service jusqu'à la fin de son cycle d’abonnement mensuel.

> Le plan ou l’un des plans peut (mais pas doit) inclure nombre illimité de transactions.

### <a name="create-a-plan"></a>Créer un plan.
1. Cliquez sur **« + »** en regard de l’option « Ajouter une nouvelle offre ».

2. Choisissez une des options : **illimité** ou **limité** l’utilisation pour cette offre.  Si le numéro de transaction fournis limité le plan vous permet de consommer en un mois.

    ![dessin](media/marketplace-publishing-data-service-creation/step-5.1.png)  

    Portail de publication suggère également « Plan identificateur », qui seront utilisés pour communiquer avec les utilisateurs finaux le nom du plan dans l’interface utilisateur et également utilisées par le Service sur le marché pour identifier le Plan. Vous pouvez modifier le « identificateur de planifier » si vous voulez.

    > [AZURE.NOTE] « Identificateur planifier » doit être unique dans l’étendue de chaque offre. Comme de nombreux autres identificateurs utilisée dans l’identificateur de portail de publication planifier est verrouillée après que la première publication de production et vous ne pourrez pas modifier cet identificateur.

3. Cliquez pour accepter votre choix.

4. Puis vous demandera posez des questions supplémentaires concernant votre Plan nouvellement créé.

    ![dessin](media/marketplace-publishing-data-service-creation/step-5.2.png)


|Question|Argument précision|
|----|----|
|**Ce Plan est gratuit et disponible dans le monde ?**|Vous pouvez créer un plan complètement libre de charge. S’il s’agit de l’offre uniquement pour cette offre – cela signifie que vous publiez « Offre gratuite » sur le marché. S’il s’agit uniquement pour des (peu) offre, le service informatique vous donne la possibilité de proposer aux utilisateurs finaux pour en savoir plus sur votre service avec un petit nombre de transactions par mois.  Si la réponse est « Oui », aucune autre question n’est invitée.|

> [AZURE.NOTE] Les utilisateurs finaux peuvent toujours mise à niveau vers les offres payants.

|Question|Argument précision|
|----|----|
|**Version d’évaluation gratuite est-elle disponible ?**|Vous pouvez choisir entre « Aucune version d’évaluation » tout ou proposer une option pour utiliser votre Plan de « Un mois ». Éditeurs par exemple, utilisez cette option pour fournir aux utilisateurs finaux la possibilité pour mieux comprendre les avantages de l’offre gratuitement pour un mois.|

> [AZURE.IMPORTANT] Les utilisateurs finaux seulement pourront pas acheter une version d’évaluation gratuite s’ils ont établi le moyen de paiement par carte bancaire, accord entreprise.

> Après un mois de la version d’évaluation gratuite, Azure Marketplace démarre planification des clients le prix à la date de l’abonnement, à moins que le client initialisé par l’annulation de l’abonnement. Aucune notification spéciale n’est fournie pour les utilisateurs finaux.

|Question|Argument précision|
|----|----|
|**Cette offre requiert un code promotionnel d’acheter ?**| Éditeurs comportent une option pour limiter l’accès à leur plan de Service en fournissant un code spécial, appelé « A du Promocode » pour des clients spécifiques. Seuls les utilisateurs finaux qui auront du Promocode pourront s’abonner à l’offre. Si vous choisissez « Non », vous acceptez que tout le monde à partir de la région dans laquelle l’offre est disponible (voir [Guide contenu Marketing Marketplace](marketplace-publishing-push-to-staging.md) pour plus d’informations) seront en mesure de s’abonner à ce plan. Aucune autre question n’est invitée.|
|**Également masquer ce plan à partir de toutes les personnes qui n’a pas un code promotionnel valide ?**|Si la réponse à la question précédente est « Oui » l’éditeur a une option pour supprimer complètement cette offre ne s’affiche pas dans l’interface utilisateur de la place de marché. Cela signifie, clients ne verront pas ce plan de la page Détails de l’offre. Les utilisateurs finaux qui recevront du promocode pour acheter, pourront s’abonner à l’aide du promocode.|

## <a name="6---create-your-marketplace-marketing-content"></a>6. créer votre Marketplace contenu marketing
Pour savoir comment fournir les informations requises dans les onglets **Marketing, tarification, prise en charge et catégories** veuillez visite [Guide contenu Marketing Marketplace](marketplace-publishing-push-to-staging.md) qui sont communes à tous les objets publiés dans la Azure Marketplace.  

## <a name="7---connect-your-offer-to-your-service-sql-azure-based-or-web-service-based"></a>7. Connectez votre offre à votre Service (Service Web en fonction ou SQL Azure basé).

Cliquez sur le sous-menu **Data Services** .

Dans la partie supérieure de la page que vous êtes invité à fournir de l’offre **Namespace**.  

  ![dessin](media/marketplace-publishing-data-service-creation/step-7.png)

La sous question définissent comment l’éditeur va offre exposent nouvellement créé à Azure Marketplace. (Pour plus d’informations, consultez le [Guide prérequises technique Data Services](marketplace-publishing-data-service-creation-prerequisites.md)).

  ![dessin](media/marketplace-publishing-data-service-creation/step-7.2.png)

**Le service de base de données en fonction de publication**

Cliquez sur **base de données**. La page suivante s’affiche :

  ![dessin](media/marketplace-publishing-data-service-creation/step-7.3.png)

Pour créer un mappage CSDL pour le jeu de données en fonction de la base de données SQL Azure :

  ![dessin](media/marketplace-publishing-data-service-creation/step-7.4.png)

Puis pour chaque table

  ![dessin](media/marketplace-publishing-data-service-creation/step-7.5.png)

  ![dessin](media/marketplace-publishing-data-service-creation/step-7.6.png)

Si Service Web

  ![dessin](media/marketplace-publishing-data-service-creation/step-7.7.png)

> [AZURE.IMPORTANT] Lisez le [mappage d’un service web existant à OData via CSDL](marketplace-publishing-data-service-creation-odata-mapping.md) pour obtenir des instructions détaillées et des exemples de création d’un Service Web CSDL.

## <a name="next-steps"></a>Étapes suivantes
À présent que vous avez créé votre offre de Service de données, vérifiez que vous suivez les instructions dans le [Guide de contenu Marketing Marketplace](marketplace-publishing-push-to-staging.md) avant de le déplacer vers l’avant au [test de votre Service de données dans un environnement intermédiaire](marketplace-publishing-data-service-test-in-staging.md).

## <a name="see-also"></a>Voir aussi
- [Mise en route : Comment publier une offre à la Azure Marketplace](marketplace-publishing-getting-started.md)
- Si vous êtes intéressé à comprendre le processus de mappage OData et l’objectif globale, lisez cet article [Mappage du Service de données OData](marketplace-publishing-data-service-creation-odata-mapping.md) passer en revue les définitions, les structures et les instructions.
- Si vous êtes intéressé par apprentissage et la compréhension des nœuds spécifiques et leurs paramètres, lisez cet article [Données Service OData mappage des nœuds](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) de définitions et explications, des exemples et utiliser casse contexte.
- Si vous êtes intéressé révision exemples, lisez cet article [Exemples de mappage des données Service OData](marketplace-publishing-data-service-creation-odata-mapping-examples.md) pour voir des exemples de code et comprendre le contexte et la syntaxe de code.


[link-pubportal]:https://publish.windowsazure.com
