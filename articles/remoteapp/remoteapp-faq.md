<properties 
    pageTitle="Forum aux questions de RemoteApp Azure | Microsoft Azure" 
    description="Découvrez les réponses aux questions fréquemment posées RemoteApp Azure." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="swadhwa" 
    editor=""/>

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="azure-remoteapp-faq"></a>Forum aux questions RemoteApp Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Nous avons entendu les questions suivantes concernant RemoteApp Azure. Vous avez d’autres personnes ? Visitez les [forums RemoteApp](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp) et dites-nous ce que vous devez savoir ou liste déroulante un commentaire en dessous.

## <a name="cant-find-what-youre-looking-for-have-a-question-we-didnt-answer"></a>Impossible de trouver ce que vous recherchez ? Vous avez une question que nous n’a pas été répondre ?
Si vous ne trouvez pas les informations que vous avez besoin, ou vous avez une question supplémentaire que nous n’avons pas détaillé ici, veuillez allez vers le [forum Azure RemoteApp](http://aka.ms/araforum) et posez votre question il. Nous pouvons toujours ajouter d’autres réponses sont disponibles ici.

## <a name="what-is-azure-remoteapp"></a>Qu’est RemoteApp Azure ? ##


- **Qu’est RemoteApp Azure ?** RemoteApp est qu'un service d’Azure vous permet de fournir un accès à distance sécurisé aux applications de nombreux appareils différents utilisateur. En savoir plus sur [Azure RemoteApp](remoteapp-whatis.md).
- **Quelles sont les options de déploiement ?** Il existe deux types de collections de RemoteApp : cloud et hybride. Celui que vous avez besoin dépend de plusieurs facteurs, tels que si vous avez besoin de jointure de domaines. Nous parlons de toutes ces décisions [ici](remoteapp-collections.md).

## <a name="quick-tips-on-using-azure-remoteapp"></a>Conseils rapides sur l’utilisation de RemoteApp Azure ##
- **Combien de temps jusqu'à ce que je suis déconnecté ? Combien de temps être inactif avant d’effectuer me le démarrage ?** 4 heures. Si vous ou un de vos utilisateurs est inactif depuis 4 heures, vous devez être automatiquement déconnecté RemoteApp Azure. Consultez les autres paramètres par défaut dans [Azure abonnement et limites de Service, les Quotas et les contraintes](../azure-subscription-service-limits.md).
- **Puis-je essayer ce service gratuitement ?** Oui. Une version d’évaluation gratuite est disponible pour les 30 jours. Après la fin de la version d’évaluation, vous pouvez passer à un compte payant (que vous pouvez utiliser en production) ou arrêter l’utilisation du service. Démarrez votre version d’évaluation gratuite en accédant à [portal.azure.com](http://portal.azure.com) - créer une nouvelle instance de RemoteApp. Avec la version d’évaluation gratuite, vous pouvez générer 2 instances de RemoteApp avec 10 utilisateurs par instance. N’oubliez pas que cette version d’évaluation n’existent que pendant 30 jours.
## <a name="azure-remoteapp-subscription-details"></a>Détails de l’abonnement RemoteApp Azure ##

- **Quelles sont les limites de service ?** Vous pouvez en savoir plus sur les paramètres par défaut et les limites de service de RemoteApp Azure dans [Azure abonnement et limites de Service, les Quotas et les contraintes](../azure-subscription-service-limits.md). Dites-nous si vous avez d’autres questions.
- **Combien d’utilisateurs que je dois avoir ?** Il existe un minimum de 20 utilisateurs. Laissez-moi vous répétez que pour être super effacer - la valeur minimale est 20. Vous serez facturé pour 20. 
- **Combien coûte coût RemoteApp ?** Consultez les [Détails de tarification RemoteApp Azure ](https://azure.microsoft.com/pricing/details/remoteapp/).
- **Un seul type de collection de sites coûte plus de choses qu’une autre ?** Oui, il est possible, selon vos besoins en matière de collection de sites. Une collection de sites hybride nécessite une connexion à partir d’Azure RemoteApp à votre réseau local. Si vous utilisez un itinéraire VNET/Express existant, il est sans frais supplémentaires. Mais si vous utilisez un nouveau VNET Azure et une passerelle ou un itinéraire Express, vous devrez payer pour la [passerelle VPN](https://azure.microsoft.com/pricing/details/vpn-gateway) ou la [Gamme de Express](https://azure.microsoft.com/pricing/details/expressroute/). Ce coût (détaillé dans les liens) en haut de votre RemoteApp Azure mensuel coûts.

## <a name="collections---whats-supported-which-should-you-use-and-others"></a>Collections - qu’est pris en charge, laquelle devez-vous utiliser et autres personnes
- **Applications personnalisées de métier (métier) sont pris en charge ?** Oui. Pour utiliser une application personnalisée dans Azure RemoteApp, créer une [image du modèle personnalisé](remoteapp-create-custom-image.md), puis téléchargez-le sur la collection RemoteApp.
- **Mon application métier personnalisée ne fonctionnera dans Azure RemoteApp ?** La meilleure façon figure ça consiste à tester. Consultez le [Centre de compatibilité de bureau à distance](http://www.rdcompatibility.com/compatibility/default.aspx).
- **Quelle méthode de déploiement (cloud ou hybride) consiste le mieux pour mon organisation ?** Collections hybride offrent l’expérience plus complète si vous voulez intégration complète avec l’authentification unique (SSO) et la connectivité réseau sécurisé en local. Collections de cloud offrent un moyen simple et agile à isoler votre déploiement à l’aide de plusieurs méthodes d’authentification. En savoir plus sur les [options de déploiement](remoteapp-whatis.md).
- **Nous avons SQL ou une autre base de données localement ou dans Azure. Quel type de déploiement doit-on utiliser ?** Cela dépend où se trouve votre base de données SQL ou serveur principal. Si la base de données se trouve dans un réseau privé, utilisez la collection hybride. Si la base de données est visible sur Internet et autorise les connexions à s’y connecter client, vous pouvez utiliser la collection de cloud.
- **Qu’en est-il de mappage du lecteur USB et port série, le partage du Presse-papiers et la redirection d’imprimante ?** Toutes ces fonctionnalités sont prises en charge dans Azure RemoteApp. Redirection de partage et d’imprimantes Presse-papiers est activée par défaut. Vous pouvez en savoir plus sur la redirection [ici](remoteapp-redirection.md). 


## <a name="template-images"></a>Images de modèle
- **Puis-je utiliser un nuage ou un ordinateur virtuel existant comme modèle pour ma collection RemoteApp ?** Oui ! Vous pouvez créer une image selon une machine virtuelle Azure, utilisez une des images incluses avec votre abonnement ou créer une image personnalisée. Consultez les [options d’image RemoteApp](remoteapp-imageoptions.md).


## <a name="network-options"></a>Options de réseau
- **La collection hybride nécessite un VNET. Est-il possible d’utiliser notre VNET existant ?** Vous pouvez si le VNET existant est un VNET Azure. Voir « étape 1 : configurer votre réseau virtuel » dans les [instructions de collection de sites hybride](remoteapp-create-hybrid-deployment.md) pour plus d’informations.
- **Puis-je utiliser un VNET une collection de cloud ?** Vous pouvez en effet. Consultez [créer une collection de cloud](remoteapp-create-cloud-deployment.md), particulièrement étape 1, pour plus d’informations.

## <a name="authentication-options"></a>Options d’authentification



- **Qu’en est-il de l’authentification ? Quelles méthodes sont prises en charge ?** La collection de cloud prend en charge les comptes Microsoft et comptes Azure Active Directory, qui sont ainsi que les comptes Office 365. La collection hybride prend en charge uniquement les comptes Azure Active Directory qui ont été synchronisés (à l’aide d’un outil comme [Azure Active Directory Sync](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx)) à partir d’un déploiement Active Directory Windows Server ; plus précisément, soit synchronisées avec l’option de synchronisation de mot de passe ou synchronisé avec la fédération de Services de fédération Active Directory (AD FS) configurée. Vous pouvez également configurer [L’authentification multifacteur (MFA)](https://azure.microsoft.com/services/multi-factor-authentication/).

>[AZURE.NOTE]Les utilisateurs Azure Active Directory doivent être à partir du client associé à votre abonnement. (Vous pouvez afficher et modifier votre abonnement sous l’onglet **paramètres** dans le portail. Voir [Modifier le client Azure Active Directory utilisé par RemoteApp](remoteapp-changetenant.md) pour plus d’informations.)

- **Pourquoi ne puis-je pas pour donner à mon accès au compte Azure Active Directory ?** Les utilisateurs Azure Active Directory doivent être à partir du répertoire associée à votre abonnement. Vous pouvez afficher ou modifier ce répertoire sous l’onglet Paramètres dans le portail. Pour plus d’informations, voir [Modifier le client Azure Active Directory utilisés par RemoteApp](remoteapp-changetenant.md) .

## <a name="clients---what-device-can-i-use-to-access-azure-remoteapp"></a>Clients - appareil que peut utiliser pour accéder à RemoteApp Azure ?
Vous pouvez trouver des informations bon client, y compris les étapes pour l’installation des clients à [accéder à vos applications dans Azure RemoteApp](remoteapp-clients.md)différents.

- **Les appareils et les systèmes d’exploitation les applications clientes prennent en charge ?**
Premier, les ordinateurs et tablettes : 
    - Windows 10 (version bêta du client)
    - Windows 8.1 et Windows 8
    - Windows 7 Service Pack 1
    - Mac OS X
    - Windows RT
    - Tablettes Android
    - iPad et les téléphones :
    - iPhone
    - Téléphone Android
    - Windows Phone
 
    [Télécharger](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) un client RemoteApp maintenant.
- **Azure RemoteApp ne prend en charge les Clients légers ?** Oui, les clients légers Windows incorporé suivants sont pris en charge :
    - Windows incorporé 7 Standard
    - Windows incorporé Standard 8
    - Windows incorporé industrie 8.1 Pro
    - Windows 10 IoT entreprise

- **Quelle version de Windows Server est pris en charge pour l’hôte de Session Bureau à distance (RDSH) ?** Windows Server 2012 R2.

## <a name="support-and-feedback"></a>Prise en charge et les commentaires


- **Quel est le planning de prise en charge pour RemoteApp ?** Prise en charge pour la gestion de la facturation et l’abonnement est proposé gratuitement. Support technique est disponible via les [offres de service Azure](https://azure.microsoft.com/support/plans/). Vous pouvez également obtenir une assistance technique gratuite de la Communauté à notre [forum de discussion Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp). 
- **Comment envoyer des commentaires ?** Visitez le [forum de commentaires](https://feedback.azure.com/forums/247748-azure-remoteapp/).
- **Qui puis-je parler pour en savoir plus sur RemoteApp Azure ?** En plus de notre [forum de discussion](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp), qui est une excellente publiez des questions, vous pouvez rejoindre la hebdomadaire [Ask au webinaire Experts](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html), dans lequel nous parlons de tous les éléments RemoteApp.
- **Qu’en est-il documentation RemoteApp ?** Nous sommes si Content que vous vous êtes invité. Outre le contenu d’aide dans le bac d’alimentation aide du portail (cliquez simplement sur le paramètre **?** sur n’importe quelle page du portail), les articles suivants sont disponibles pour vous apprendre à propos RemoteApp :
    - **Prise en main :**
        - [Qu’est RemoteApp ?](remoteapp-whatis.md)
        - [Nouveautés dans les images des modèles RemoteApp ?](remoteapp-images.md)
        - [Comment fonctionne la gestion des licences ?](remoteapp-licensing.md)
        - [Comment RemoteApp et Office fonctionnent ensemble ?](remoteapp-o365.md)
        - [Redirection fonctionnement dans RemoteApp](remoteapp-redirection.md)?
    - **Déployer :**
        - [Créer un modèle personnalisé](remoteapp-create-custom-image.md)
        - [Créer une collection hybride](remoteapp-create-hybrid-deployment.md)
        - [Créer une collection de cloud](remoteapp-create-cloud-deployment.md)
        - [Configurez Azure Active Directory pour RemoteApp](remoteapp-ad.md)
        - [Publier une application dans RemoteApp](remoteapp-publish.md)
    - **Gérer :**
        - [Ajouter des utilisateurs](remoteapp-user.md)
        - [Meilleures pratiques pour la configuration et l’utilisation de RemoteApp](remoteapp-bestpractices.md)  

    Vidéos ! Nous avons également un certain nombre de vidéos sur RemoteApp. Certains proposent introduction ([présentation RemoteApp Azure](https://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)) tandis que d’autres personnes vous guident dans déploiement ([déploiement de Cloud](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) et [Déploiement hybride](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)). Les extraire !

 
### <a name="help-us-help-you"></a>Aidez-nous à améliorer vous 
Saviez-vous qu’outre évaluation de cet article et présenter des observations vers le bas en dessous, vous pouvez apporter des modifications à l’article lui-même ? Un élément manquant ? Un problème ? Écrire quelque chose qui se trouve juste confusion ? Défiler vers le haut, cliquez sur **Modifier sur GitHub** pour apporter des modifications - ceux soient transférés vers nous pour révision et puis, une fois que nous se les, vous voyez vos modifications et les améliorations ici.
