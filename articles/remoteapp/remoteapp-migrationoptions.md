
<properties 
    pageTitle="Options de la migration de RemoteApp Azure | Microsoft Azure" 
    description="Découvrez les options de la migration de RemoteApp Azure." 
    services="remoteapp" 
    documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/06/2016" 
    ms.author="elizapo" />

# <a name="options-for-migrating-out-of-azure-remoteapp"></a>Options de la migration de RemoteApp Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Si vous avez arrêté à l’aide de RemoteApp Azure en raison de l' [annonce retraite](https://go.microsoft.com/fwlink/?linkid=821148) ou parce que vous avez terminé votre évaluation, vous devez migrer des Azure RemoteApp à un autre service de l’application. Il existe deux façons de migrer : un gérés automatiquement (souvent appelée Infrastructure comme un Service [IaaS]) déploiement ou un entièrement gérée (souvent appelée Platform en tant que Service) ou logiciel en tant que Service [PaaS/SaaS] offrant. 

Libre-service IaaS est un déploiement personnalisables qui est géré, géré et dont vous êtes propriétaire, directement déployé sur des systèmes physiques ou machines virtuelles (machines virtuelles). À l’autre extrémité, une entièrement géré PaaS/offre SaaS est plus comme Azure RemoteApp - un partenaire fournit un calque service en haut d’une solution d’accès distant prenant en charge opérationnelles et maintenance, pendant que vous, que le client, effectuer des opérations image et application de gestion.

Poursuivez votre lecture pour plus d’informations, y compris des exemples de différentes options d’hébergement.    

## <a name="self-managed-iaas-solutions"></a>Solutions (IaaS) gérées automatiquement

### <a name="rds-on-iaas"></a>**RDS sur IaaS** 
Vous pouvez déployer un déploiement de Services Bureau à distance (dans Windows Server) natif du basé sur session à l’aide de RemoteApp ou bureaux locaux ou dans un environnement hébergé (comme sur machines virtuelles Azure). RDS sur des déploiements IaaS sont adaptés aux clients connaissez déjà et qui ont une expertise technique existante avec les déploiements RDS. 

> [AZURE.NOTE]
> Vous devez avec Software Assurance (SA) pour les licences d’accès client RDS pour utiliser cette option de déploiement de licences en Volume.

Déploiement RDS sur machines virtuelles Azure est plu faciles lorsque vous utilisez le déploiement et la correction des modèles (lire une [vue d’ensemble](https://blogs.technet.microsoft.com/enterprisemobility/2015/07/13/azure-resource-manager-template-for-rds-deployment/) , puis [accédez disposons](https://aka.ms/rdautomation)). Vous pouvez obtenir les mêmes fonctionnalités de mise à l’échelle élastiques avec les ressources de modèle de déploiement d’Azure classique (pas les ressources de modèles de ressources Azure) dans Azure RemoteApp à l’aide de [mise à l’échelle script](https://gallery.technet.microsoft.com/scriptcenter/Automatic-Scaling-of-9b4f5e76), bien qu’il existe plusieurs personnalisations et configurations. Lorsque vous déployez RDS sur machines virtuelles Azure, prise en charge est fourni par le biais [d’Azure prend en charge](https://azure.microsoft.com/support/plans/), les même professionnels du support technique vous pris en charge avec Azure RemoteApp. Vous pouvez obtenir des estimations de coûts en fonction de votre utilisation existante en contactant [Le Support Azure](https://azure.microsoft.com/support/plans/), ou vous pouvez effectuer des calculs vous-même à l’aide une sortie prochaine calculatrice de coût.  En outre, machines virtuelles série N (actuellement en privé preview) vous pouvez d’ajouter vGPU - en savoir plus sur l’ajout de vGPU et sur la façon [d’exploiter les améliorations RDS dans Windows Server 2016](https://myignite.microsoft.com/videos/2794) dans notre session Ignite.   

Nous avons guides déploiement étape par étape pour [Windows Server 2012 R2](http://aka.ms/rdsonazure) et [Windows Server 2016](http://aka.ms/rdsonazure2016) pour vous aider à votre déploiement. Consultez le [blog de bureau à distance](https://blogs.technet.microsoft.com/enterprisemobility/?product=windows-server-remote-desktop-services) pour obtenir les dernières informations.
 
### <a name="citrix-on-iaas"></a>**Citrix sur IaaS** 
Un Citrix native déploiement de XenApp basé sur session ou XenDesktop peut être déployé en local ou dans un environnement hébergé (tel que sur machines virtuelles Azure). 

Consultez le guide de déploiement étape par étape, [Citrix XA 7.6 sur Azure](http://www.citrixandmicrosoft.com/Documents/Citrix-Azure Deployment Guide-v.1.0.docx), pour plus d’informations. En savoir plus sur [Citrix sur Azure](http://www.citrixandmicrosoft.com/Solutions/AzureCloud.aspx), y compris une calculatrice de prix. Vous trouverez également un [Citrix contacter](http://citrix.com/English/contact/index.asp) pour décrire les options disponibles avec.

## <a name="fully-managed-paassaas-offerings"></a>Offres (PaaS/SaaS) entièrement gérées

### <a name="citrix-cloud"></a>**Citrix Cloud** 
[Solution de cloud existante Citrix](https://www.citrix.com/products/citrix-cloud/)identique architecturale à Citrix XenApp Express. Citrix offre une [promotion de remise de 50 %](https://www.citrix.com/blogs/2016/10/03/special-promotion-for-microsoft-azure-remoteapp-customers/) pour les clients Azure RemoteApp existants. 

### <a name="citrix-xenapp-express-in-tech-preview"></a>**Citrix XenApp Express (en mode Aperçu avant tech)**
[Enregistrer pour la leur tech preview](http://now.citrix.com/remoteapp)et observez leur [session Ignite](https://myignite.microsoft.com/videos/2792) (en commençant à minute 20:30). XenApp Express est architecturale identique au Citrix Cloud sauf il inclut Gestion simplifiée l’interface utilisateur et d’autres fonctionnalités et fonctionnalités qui sont semblables aux RemoteApp Azure. 

En savoir plus sur [Citrix XenApp Express](http://now.citrix.com/remoteapp).   

### <a name="citrix-service-provider-program"></a>**Programme de fournisseur de services de Citrix** 
Le programme de fournisseur de Service Citrix facilite pour les fournisseurs de services d’effectuer la simplicité du virtuel cloud computing aux PME, leur proposant les services qu’ils souhaitent dans un modèle facile et paiement. Fournisseurs de services Citrix développer leur activité SPLA Microsoft et développer leurs investissements de plateforme RDS avec n’importe quel appareil, accès en tout lieu, la prise en charge application plus large, une expérience enrichie, renforcer la sécurité et extensibilité élevées accrue. À son tour, fournisseurs de services Citrix attirer plus abonnés, augmenter la satisfaction et réduire les coûts d’exploitation. [En savoir plus](http://www.citrix.com/products/service-providers.html) ou [Rechercher un partenaire](https://www.citrix.com/buy/partnerlocator.html).

### <a name="microsoft-hosted-service-provider"></a>**Fournisseur de services hébergé Microsoft** 
Partenaires d’hébergement offrent généralement entièrement géré hébergé bureau Windows et service d’application, qui peut inclure la gestion des ressources Azure, des systèmes d’exploitation, des applications et à l’aide du partenaire de support technique de licences accords avec Microsoft et d’autres fournisseurs de logiciels ainsi que d’en cours d’un contrat de licence de fournisseur de Service pour permettre le revendeur de licence de l’accès abonné (SAL). Les informations suivantes fournissent des détails et des informations de contact pour certains hébergeurs spécialisés dans assistance des clients avec leur migration RemoteApp Azure. Consultez [la liste actuelle des fournisseurs de services hébergés](http://aka.ms/rdsonazurecertified) qui se sont terminées le RDS sur IaaS chemin d’accès et de l’évaluation d’apprentissage.  

#### <a name="aspex"></a>**ASPEX**
[ASPEX](http://www.aspex.be/en) est spécialisé dans les éditeurs de logiciels transition vers le Cloud et fil « vous cherchez à optimiser leurs installations cloud en cours. ASPEX propose une large gamme de services gérés, devops et services de Conseil.  

Emplacement principal : Anvers, Belgique

Région opération : Europe occidentale

Statuts de partenaire : [argent](https://partnercenter.microsoft.com/pcv/solution-providers/aspex_9397f5dd-ebdd-405b-b926-19a5bda61f7a/cfe00bac-ea36-4591-a60b-ec001c4c3dff)

Fournisseur de services de Cloud Microsoft : Oui

Propose des solutions de RemoteApp et bureau session : Oui, les deux

Solutions de migration RemoteApp Azure : Oui, [en savoir plus](https://www.aspex.be/en/azure-remote-apps)

**Contact :**

- Téléphone : +3232202198
- Courrier :[info@aspex.be](mailto:info@aspex.be)
- Web : [http://cloud.aspex.be/contact-ara-0](http://cloud.aspex.be/contact-ara-0)

#### <a name="conexlink-platform-name-mycloudit"></a>**Conexlink (nom de la plateforme : MyCloudIT)**
[MyCloudIT](http://www.mycloudit.com) est une plateforme d’automatisation pour les entreprises informatiques simplifier, optimiser et mettre à l’échelle la migration et la remise de bureaux à distance, des applications à distance et infrastructure dans le nuage Azure Microsoft. 

La plateforme MyCloudIT réduit le temps de déploiement de 95 %, Azure des coûts de 30 % et déplace l’infrastructure informatique de leurs clients dans le nuage en termes de quelques séquences de touches. Partenaires peuvent maintenant gérer les clients à partir d’un tableau de bord globale, les utilisateurs finaux de service dans le monde entier valorisation et augmenter le chiffre d’affaires sans l’ajouter surcharge supplémentaire ou formation Azure complète.  

Emplacement principal : Dallas, TX, USA

Région opération : dans le monde

Statuts de partenaire : [or](https://partnercenter.microsoft.com/pcv/solution-providers/conexlink_4298787366/843036_1?k=Conexlink)

Fournisseur de services de Cloud Microsoft : Oui

Propose des solutions de RemoteApp et bureau session : Oui, les deux

Solutions de migration RemoteApp Azure : Oui, [en savoir plus](https://mycloudit.com/remote-app-microsoft/)

**Contact :**
- Bernard Garoutte, VP de développement d’entreprise

   Téléphone : 972-218-0741

   Messagerie :[brian.garoutte@conexlink.com](mailto:brian.garoutte@conexlink.com)

#### <a name="acuutech"></a>**Acuutech**
[Acuutech](http://www.acuutech.com) est spécialisé en matière de solutions de bureau hébergées, exécution complè applications de bureau et fil expériences basés sur la technologie Microsoft à un client global base à partir d’Azure et leurs propres centres de données.

Emplacement principal : Londres ; Singapour ; Houston, au Texas

Région opération : dans le monde

Statuts de partenaire : or

Fournisseur de services de Cloud Microsoft : Oui

Propose des solutions de RemoteApp et bureau session : Oui, les deux

Solutions de migration RemoteApp Azure : Oui, [en savoir plus](http://www.acuutech.com/ara-migration/)

**Contact :**

- Royaume-Uni :

  5/6 York maison, route Langston,

  Loughton, Essex IG10 3TQ
  
  Téléphone : + 44 (0) 20 8502 2155
 
- Singapour :

  Rue Cecil 100, #09-02, 
  
  Le Globe Singapour 069532
  
  Téléphone : + 65 6709 4933
 
- Amérique du Nord : 

  3601 s Sandman St.
  
  200 suite, Houston, TX 77098
  
  Téléphone : +1 713 691 0800

## <a name="need-more-help"></a>Besoin d’aide supplémentaire ?
Toujours besoin d’aide en choisissant ou avez d’autres questions ? Utilisez une des méthodes suivantes pour obtenir de l’aide. 

1.  Nous envoyer un e-mail à [arainfo@microsoft.com](mailto:arainfo@microsoft.com).
2.  Contactez [Azure prend en charge](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Commencez par ouvrir une [Azure prend en charge la casse](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
3.  Nous contacter. [Rechercher un numéro de vente local](https://azure.microsoft.com/overview/sales-number/).
