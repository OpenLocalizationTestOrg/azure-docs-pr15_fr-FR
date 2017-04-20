<properties
    pageTitle="Comment créer une collection de sites hybride pour Azure RemoteApp | Microsoft Azure"
    description="Découvrez comment créer un déploiement de RemoteApp qui se connecte à votre réseau interne."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo"/>

# <a name="how-to-create-a-hybrid-collection-for-azure-remoteapp"></a>Comment créer une collection de sites hybride pour Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Il existe deux types de collections de RemoteApp Azure :

- Cloud : réside complètement dans Azure. Vous pouvez choisir d’enregistrer toutes les données dans le cloud (afin d’une collection exclusivement le nuage) ou pour vous connecter votre collection de sites à une VNET et enregistrer les données.   
- Environnement hybride : inclut un réseau virtuel pour l’accès local - cela nécessite l’utilisation d’Azure AD et un environnement Active Directory local.

Vous ne savez dont vous aurez besoin ? Consultez [le type de collection de sites avez-vous besoin pour Azure RemoteApp](remoteapp-collections.md).

Ce didacticiel vous guide dans le processus de création d’une collection hybride. Il existe huit étapes :

1.  Décider quelle [image](remoteapp-imageoptions.md) à utiliser pour votre collection de sites. Vous pouvez créer une image personnalisée ou utilisez l’une des images Microsoft inclus avec votre abonnement.
2. Configurer votre réseau virtuel. Consultez les informations de [planification de VNET](remoteapp-planvnet.md) et de [redimensionnement](remoteapp-vnetsizing.md) .
2.  Créer une collection de sites.
2.  Participer à votre collection de sites à votre domaine local.
3.  Ajouter une image du modèle à votre collection de sites.
4.  Configurer la synchronisation d’annuaires. Azure RemoteApp nécessite intégrer Azure Active Directory par soit 1) configuration Azure Active Directory Sync avec l’option de synchronisation de mot de passe ou 2) configuration Azure Active Directory Sync sans l’option de synchronisation de mot de passe, mais à l’aide d’un domaine fédéré ADFS. Consultez les [informations de configuration pour Active Directory avec RemoteApp](remoteapp-ad.md).
5.  Publier des applications RemoteApp.
6.  Configurer l’accès utilisateur.

**Avant de commencer**

Vous devez effectuer les opérations suivantes avant de créer la collection de sites :

- [S’inscrire](https://azure.microsoft.com/services/remoteapp/) pour Azure RemoteApp.
- Créer un compte d’utilisateur dans Active Directory à utiliser comme compte de service RemoteApp Azure. Restreindre les autorisations pour ce compte afin qu’il peut uniquement joindre des ordinateurs au domaine.
- Recueillir des informations sur votre réseau local : adresse IP d’informations et des détails de l’appareil VPN.
- Installez le module [Azure PowerShell](../powershell-install-configure.md) .
- Collecter les informations sur les utilisateurs que vous voulez accorder l’accès à. Vous devez le nom d’utilisateur principal Azure Active Directory (par exemple, name@contoso.com) pour chaque utilisateur. Assurez-vous que le nom UPN correspond à entre Azure AD et Active Directory.
- Sélectionnez votre image du modèle. Une image du modèle RemoteApp Azure contient les applications et les programmes que vous souhaitez publier pour vos utilisateurs. Pour plus d’informations, voir [options d’image RemoteApp Azure](remoteapp-imageoptions.md) .
- Vous voulez utiliser l’image d’Office 365 ProPlus ? Consultez les informations [ici](remoteapp-officesubscription.md).
- [Configurer Active Directory pour RemoteApp](remoteapp-ad.md).



## <a name="step-1-set-up-your-virtual-network"></a>Étape 1 : Configurer votre réseau virtuel
Vous pouvez déployer une collection hybride qui utilise un réseau virtuel Azure existant, ou vous pouvez créer un nouveau réseau virtuel. Un réseau virtuel vous permet de vos données access aux utilisateurs de votre réseau local via des ressources à distance RemoteApp. À l’aide d’un réseau virtuel Azure offre un accès réseau direct votre collection de sites à d’autres services Azure et machines virtuelles déployés sur ce réseau virtuel.

Vérifiez que vous vérifiez les informations de [planification de VNET](remoteapp-planvnet.md) et [taille VNET](remoteapp-vnetsizing.md) avant de créer votre VNET.

### <a name="create-an-azure-vnet-and-join-it-to-your-active-directory-deployment"></a>Créer un VNET Azure et participer à votre déploiement Active Directory

Commencez par créer un [réseau virtuel](../virtual-network/virtual-networks-create-vnet-arm-pportal.md). Pour cela, sous l’onglet **réseau** dans le portail Azure. Vous devez vous connecter votre réseau virtuel pour le déploiement d’Active Directory est synchronisé à votre client Azure Active Directory.

Pour plus d’informations, voir [créer un réseau virtuel à l’aide du portail Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) .

### <a name="make-sure-your-virtual-network-is-ready-for-azure-remoteapp"></a>Vérifiez que votre réseau virtuel est prêt pour Azure RemoteApp
Avant de créer votre collection de sites, nous allons Assurez-vous que votre réseau virtuel est prêt. Vous pouvez le valider en procédant comme suit :

1. Créer une machine virtuelle Azure l’intérieur du sous réseau du réseau virtuel que vous venez de créer pour RemoteApp.
2. Utiliser le Bureau à distance pour vous connecter à la machine virtuelle. (Cliquez sur **se connecter**).
3. Joindre à la même déploiement d’Active Directory que vous voulez utiliser pour RemoteApp.

Qui fonctionné ? Votre réseau virtuel et sous-réseau sont prêts pour Azure RemoteApp !

Vous trouverez plus d’informations sur la création des machines virtuelles Azure et se connecter à leur bureau à distance [ici](https://msdn.microsoft.com/library/azure/jj156003.aspx).

## <a name="step-2-create-an-azure-remoteapp-collection"></a>Étape 2 : Créer une collection de sites RemoteApp Azure ##



1. Dans le [portail Azure](http://manage.windowsazure.com), accédez à la page RemoteApp Azure.
2. Cliquez sur **Nouveau > créer avec VNET**.
3. Entrez un nom pour votre collection de sites.
4. Choisir l’offre que vous souhaitez utiliser - standard ou de base.
5. Choisissez votre VNET dans la liste déroulante et puis votre sous-réseau.
6. Sélectionnez joindre à votre domaine.
5. Cliquez sur **créer RemoteApp collection**.

Une fois que votre collection de sites RemoteApp Azure a été créé, double-cliquez sur le nom de la collection de sites. Qui s’affiche la page de **Démarrage rapide** : il s’agit de l’endroit où vous terminez la configuration de la collection de sites.

Quelque chose rencontrer des problèmes ? Consultez les [informations de dépannage de collection de sites hybride](remoteapp-hybridtrouble.md).

## <a name="step-3-link-your-collection-to-the-local-domain"></a>Étape 3 : Créer un lien votre collection de sites vers le domaine local ##


1. Dans la page de **Démarrage rapide** , cliquez sur **participer à un domaine local**.
2. Ajoutez le compte de service Azure RemoteApp à votre domaine Active Directory local. Vous devez le nom de domaine, unité d’organisation, nom d’utilisateur de compte de service et mot de passe.

    Voici les informations recueillies si vous avez suivi les étapes décrites dans [Configurer Active Directory pour Azure RemoteApp](remoteapp-ad.md).


## <a name="step-4-link-to-an-azure-remoteapp-image"></a>Étape 4 : Lien vers une image RemoteApp Azure ##

Une image du modèle RemoteApp Azure contient les programmes que vous voulez partager avec des utilisateurs. Vous pouvez créer une nouvelle [image du modèle](remoteapp-imageoptions.md) ou créer un lien vers une image existante (soit un déjà importé ou téléchargé dans Azure RemoteApp). Vous pouvez également lier à un des Azure RemoteApp [images de modèle](remoteapp-images.md) qui contiennent des programmes Office 2013 (pour une utilisation d’évaluation) Office 365.

Si vous téléchargez la nouvelle image, vous devez entrer le nom et choisissez l’emplacement de l’image. Dans la page suivante de l’Assistant, vous voyez un ensemble d’applets de commande PowerShell - copie et exécuter ces applets de commande à partir d’une invite de Windows PowerShell élevée pour la télécharger l’image spécifiée.

Si vous le liez à une image du modèle existant, spécifiez simplement le nom de l’image, un emplacement et un abonnement Azure associé.



## <a name="step-5-configure-active-directory-directory-synchronization"></a>Étape 5 : Configurer la synchronisation d’annuaire Active Directory ##

Azure RemoteApp nécessite intégrer Azure Active Directory par soit 1) configuration Azure Active Directory Sync avec l’option de synchronisation de mot de passe ou 2) configuration Azure Active Directory Sync sans l’option de synchronisation de mot de passe, mais à l’aide d’un domaine fédéré ADFS.

Consultez [AD Connect](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) : cet article vous aide à configurer l’intégration d’annuaire en 4 étapes.

Pour la planification des informations et plus d’informations, voir [programme de synchronisation d’annuaire](http://msdn.microsoft.com//library/azure/hh967642.aspx) .

## <a name="step-6-publish-apps"></a>Étape 6 : Publier des applications ##

Une application Azure RemoteApp est l’application ou le programme que vous fournissez à vos utilisateurs. Il se trouve dans l’image du modèle que vous avez téléchargée pour la collection de sites. Lorsqu’un utilisateur accède à une application, il s’ouvre automatiquement dans leur environnement local, mais elle fonctionne bien dans Azure.

Avant que vos utilisateurs peuvent accéder aux applications, vous devez publier les – cette option permet de vos utilisateurs accéder les applications par le biais du client de bureau à distance.

Vous pouvez publier plusieurs applications à votre collection de sites. Dans la page de publication, cliquez sur **Publier** pour ajouter une application. Vous pouvez soit publier dans le menu **Démarrer** de l’image du modèle ou en spécifiant le chemin d’accès dans l’image du modèle pour l’application. Si vous choisissez d’ajouter dans le menu **Démarrer** , choisissez le programme à ajouter. Si vous décidez d’indiquer le chemin d’accès à l’application, indiquez un nom pour l’application et le chemin d’accès à l’endroit où il est installé sur l’image du modèle.

## <a name="step-7-configure-user-access"></a>Étape 7 : Configurer l’accès utilisateur ##

Maintenant que vous avez créé votre collection de sites, vous devez ajouter les utilisateurs que vous souhaitez être en mesure d’utiliser vos ressources à distance. Les utilisateurs que vous donne accès pour doivent figurer dans le client Active Directory associé à l’abonnement que vous avez utilisé pour créer cette collection RemoteApp Azure.

1.  Dans la page de démarrage rapide, cliquez sur **configurer l’accès des utilisateurs**.
2.  Entrez le compte professionnel (d’Active Directory) ou le compte Microsoft que vous souhaitez accorder l’accès.

    **Notes :**

    Assurez-vous que vous utilisez la “user@domain.com” format.

    Si vous utilisez Office 365 ProPlus dans votre collection de sites, vous devez utiliser les identités Active Directory pour vos utilisateurs. Cela permet de valider les licences.


3.  Une fois que les utilisateurs sont validées, cliquez sur **Enregistrer**.


## <a name="next-steps"></a>Étapes suivantes ##
C’est tout : vous avez correctement créé et déployé votre collection de sites Azure RemoteApp hybride. L’étape suivante consiste pour que vos utilisateurs de télécharger et installer le client de bureau à distance. Vous pouvez rechercher l’URL pour le client sur la page de démarrage rapide Azure RemoteApp. Ensuite, invitez utilisateurs dans le client et accéder les applications que vous avez publié.



### <a name="help-us-help-you"></a>Aidez-nous à améliorer vous
Saviez-vous qu’outre évaluation de cet article et présenter des observations vers le bas en dessous, vous pouvez apporter des modifications à l’article lui-même ? Un élément manquant ? Un problème ? Écrire quelque chose qui se trouve juste confusion ? Défiler vers le haut, cliquez sur **Modifier sur GitHub** pour apporter des modifications - ceux soient transférés vers nous pour révision et puis, une fois que nous se les, vous voyez vos modifications et les améliorations ici.
