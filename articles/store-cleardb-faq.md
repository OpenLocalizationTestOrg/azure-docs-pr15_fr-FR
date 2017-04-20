<properties
    pageTitle="Forum aux questions pour les bases de données ClearDB MySql avec le Service d’application Azure | Microsoft Azure"
    description="Réponses aux questions courantes sur l’utilisation de bases de données ClearDB MySQL avec Azure Application Service."
    documentationCenter="php"
    services=""
    authors="sunbuild"
    manager="yochayk"
    editor=""
    tags="mysql"/>

<tags
    ms.service="multiple"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="sumuth"/>

# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Forum aux questions pour les bases de données ClearDB MySql avec le Service d’application Azure

Ce forum aux questions répond aux questions fréquentes sur l’utilisation et d’achat ClearDB MySQL bases de données pour les applications Web Azure.

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Quelles options ai-je pour MySQL sur Azure ?

Vous disposez de plusieurs options :

* [Base de données MySQL partagés ClearDB](/marketplace/partners/cleardb/databases/)

* [Clusters ClearDB MySQL Premium](/marketplace/partners/cleardb-clusters/cluster/)

* [Cluster MySQL s’exécutant sur un ordinateur virtuel Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)

* [Instance unique de MySQL en cours d’exécution sur un ordinateur virtuel Azure](virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md)

ClearDB est un service d’hébergement de MySQL et gère l’infrastructure MySQL pour vous. Lorsque vous exécutez votre propre cluster MySQL ou une base de données sur une Machine virtuelle Azure, vous devez configurer le serveur MySQL et mettre à jour régulièrement avec correctifs.

## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Dois-je une carte de crédit pour l’application Web + modèle MySQL sur le marché Azure ?

Cela dépend du type d’abonnement que vous utilisez. Voici quelques types d’abonnement fréquemment utilisé :

* [Paiement](/offers/ms-azr-0003p/): nécessite une carte de crédit, et lorsque vous achetez une base de données MySQL payant votre carte de crédit est facturée.

* [Version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/): inclut des crédits pour utiliser avec Microsoft Azure services, mais ne permet pas d’achat de ressources de tiers. Acheter des services tiers ou une base de données MySQL payant, que vous devez utiliser un abonnement activé par carte bancaire. Pour les applications Web, vous pouvez créer une base de données ClearDB MySQL gratuit.

* [Abonnement MSDN](/pricing/member-offers/msdn-benefits/) et **MSDN développement Test payer en tant que vous accédez**: semblable à la version d’évaluation gratuite, un abonnement MSDN, vous devez avoir une carte de crédit pour acheter une solution MySQL payant à partir de ClearDB.

* [Accord entreprise (EA)](/pricing/enterprise-agreement/): clients EA sont facturés par rapport à leur EA chaque trimestre pour l’ensemble de leurs achats (tierce) Azure Marketplace sur une facture distincte, consolidée. Vous êtes facturé en dehors de l’engagement monétaire pour n’importe quel achats marketplace. Veuillez noter que, pour le moment, Azure magasin n’est pas disponible pour les clients inscrits dans Azerbaïdjan, Croatie, Norvège et Porto Rico. 

*  [DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99): vous pouvez créer des bases de données ClearDB gratuit uniquement pour les applications Web. Il n’existe aucune limite sur le nombre de bases de données ClearDB MySQL gratuit que vous pouvez créer. Notez que les bases de données gratuits ne doivent ne pas être utilisés pour les applications web de production, que ce service a été conçu uniquement pour la version d’évaluation.

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Pourquoi m’a facturé 3.50 $ pour une application Web + MySQL depuis la Azure Marketplace ?

L’option de base de données par défaut est Titan, qui est de 3,50 €. Nous ne pas afficher le coût lors de la création de base de données, et vous pouvez acheter par inadvertance une base de données que vous souhaitiez pas. Nous essayons de trouver une solution pour améliorer l’expérience mais jusque là, vous devez consulter tous les votre niveaux de tarification sélectionnées pour le navigateur et base de données avant de cliquer sur **créer** et démarrer le déploiement des ressources.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>J’exécute MySQL sur mon propre machine virtuelle Azure. Puis-je me connecter mon application web Azure à ma base de données ?

Oui. Vous pouvez connecter votre application web à votre base de données dans la mesure où votre machine virtuelle Azure a donné l’accès à distance à votre application web. Pour plus d’informations, voir [Installer MySQL sur une machine virtuelle](virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md).

## <a name="in-which-countries-are-cleardb-premium-mysql-clusters-supported"></a>Dans laquelle pays sont ClearDB Premium MySQL clusters pris en charge ?

[ClearDB Premium MySQL clusters](/marketplace/partners/cleardb-clusters/cluster/) sont disponibles dans tous les régions Azure dans le monde à l’exception d’Inde, Australie, Brésil Sud et en Chine.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Puis-je créer un nouveau cluster avant de créer une base de données avec la solution de cluster ClearDB premium ?

Non, la création de clusters ClearDB vides n’est pas pris en charge. Le portail Azure vous permet de créer des bases de données dans un cluster, ce qui peut créer un nouveau cluster en même temps.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Sera j’averti si je tentez de supprimer une base de données ClearDB MySQL est en cours d’utilisation par un de mes applications ?

Azure ne vous avertit non, pas de si vous supprimez un achat marketplace qui dépend de votre application.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Les régions puis-je créer bases de données ClearDB ?

Azure Marketplace n’est pas disponible pour les clients inscrits dans Azerbaïdjan, Croatie, Norvège ou Porto Rico. ClearDB n’est pas disponible dans ces régions.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Quel niveau de tarification dois-je choisir pour une application web de production et de la base de données ?

Utiliser Basic ou un niveau plus élevé de tarification pour les applications Web. Pour ClearDB, nous vous recommandons de planifier un Saturne ou Jupiter. Passez en revue les fonctionnalités et limitations de chaque niveau de tarification pour les [Applications Web](/pricing/details/app-service/) et les [bases de données ClearDB MySQL](/marketplace/partners/cleardb/databases/) choisir celui qui répond à vos besoins.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Comment passer ma base de données ClearDB d’un plan à un autre ?

Dans le [portail Azure](https://portal.azure.com), vous pouvez évoluer vers un ClearDB d’hébergement base de données partagée. Lisez cet [article](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) pour en savoir plus. Nous ne pris en charge mise à niveau pour les groupes de ClearDB Premium dans le portail Azure.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Je ne parviens pas à voir ma base de données ClearDB portail Azure ?

Si nous créons ClearDB de base de données en utilisant le Gestionnaire de ressources Azure ou [Nouveau portail Azure](https://portal.azure.com), il ne sera pas visible dans l' [Ancien portail Azure](https://manage.windowsazure.com). Pour contourner ce consiste à lier manuellement votre base de données à l’application web. De même si créer ClearDB de base de données dans le [portail ancien](https://manage.windowsazure.com) , vous ne serez pas en mesure de voir votre base de données dans le [Nouveau portail Azure](https://portal.azure.com). Il n’existe aucune solution de contournement pour ce dernier scénario.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Qui contacter pour la prise en charge lorsque ma base de données est hors service ?

Contact [ClearDB prend en charge](https://www.cleardb.com/developers/help/support) pour les bases de données les problèmes. Préparez-vous à fournir les informations de votre abonnement Azure.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Puis-je créer des utilisateurs supplémentaires pour ma solution cluster de base de données ClearDB MySQL ? 

Non. Vous ne pouvez pas créer d’autres utilisateurs, mais vous pouvez créer des bases de données supplémentaires sur votre cluster de base de données ClearDB.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Peuvent série Basic/Pro bases de données être mis à niveau place similaire aux plans planétaires aujourd'hui sur ClearDB portail ?

Oui, base série bases de données peuvent être mis à niveau en place (base 60 via base 500). Série Pro peut être mis à niveau sur place (125 Pro et Pro 1000) à l’exception des 60 Pro. Nous ne permettent pas la mise à niveau de la base de données 60 Pro actuellement. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Lorsque je migre des ressources à partir d’un abonnement à un autre, ma base de données ClearDB MySQL sont-elles migrée également ? 

Lorsque vous effectuez la migration de ressources abonnements, certaines [limitations](./app-service-web/app-service-move-resources.md) s’appliquent. Une base de données ClearDB MySQL est un service tiers et par conséquent ne sont-elles pas migré lors de la migration d’abonnement Azure. Si vous ne gérez pas la migration de votre base de données MySQL avant la migration des ressources Azure, vos bases de données ClearDB MySQL peuvent être désactivées. Migrer manuellement vos bases de données tout d’abord, puis exécutez migration abonnement Azure pour votre application web. 

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Puis-je transférer une base de données ClearDB d’un abonnement paiement par carte bancaire à un abonnement EA ?

Bases de données existantes ClearDB utilisent la carte de crédit associée les abonnements existants. Pour utiliser un abonnement EA vous devez transférer vos données dans une base de données :

* Acheter une nouvelle base de données ClearDB avec votre abonnement EA.
* Transférer vos données dans votre nouvelle base de données.
* Mettre à jour votre application pour utiliser la nouvelle base de données.
* Supprimer votre ancienne base de données ClearDB.

Lorsque vous créez une nouvelle application web avec MySQL (ClearDB) ou créez une base de données MySQL (ClearDB), l’abonnement que vous choisissez détermine la façon dont vous allez payer pour le service. Avec un abonnement EA, nous ne bloque pas l’achat de services tiers comme ClearDB dans le portail Azure. Abonnements EA facturés en dehors d’engagement monétaire et sont facturés trimestrielles et en retard. Le client EA devra configurer un mode de paiement par exemple une carte de crédit payer pour tous les services tiers marketplace.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Où puis-je voir les frais pour les ressources ClearDB dans un abonnement EA ?

Pour les clients EA directs, frais Azure Marketplace sont visibles dans le portail d’entreprise. Notez que tous les achats marketplace et consommation facturés en dehors d’engagement monétaire et sont facturés trimestrielles et en retard. Les clients EA payer directement aux fournisseurs de services tiers et peuvent le faire en activant le mode de paiement, par exemple une carte de crédit avec leur propre compte EA.

Les clients EA indirects peuvent trouver leurs abonnements Azure Marketplace dans la page **Gérer les abonnements** du portail d’entreprise, mais tarifs est masquée. Les clients doivent contacter leur fournisseur de services superposés pour plus d’informations sur les frais marketplace.

Accès aux Azure Marketplace pour les services tiers peuvent être géré par les administrateurs d’inscription EA Azure. Ils peuvent désactiver ou réactiver accès aux 3e partie achats à partir du magasin dans gérer les comptes et abonnements sous la section comptes dans le portail d’entreprise.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Qui contacter pour les questions concernant votre facture pour les services ClearDB dans mon abonnement EA ?

Contactez [Le Support client entreprise](http://aka.ms/AzureEntSupport) en tenant compte de facturation sous leur inscription EA. L’équipe de Support de portail EA doit répondre à votre question ou aider à résoudre votre problème.

 



## <a name="more-information"></a>Plus d’informations

[Forum aux questions sur Azure Marketplace](/marketplace/faq/)
