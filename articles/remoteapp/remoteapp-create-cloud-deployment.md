<properties 
    pageTitle="Comment créer une collection de cloud d’Azure RemoteApp | Microsoft Azure" 
    description="Découvrez comment créer un déploiement d’Azure RemoteApp qui enregistre les données dans le cloud Azure." 
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

# <a name="how-to-create-a-cloud-collection-of-azure-remoteapp"></a>Comment créer une collection de cloud d’Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Il existe deux types de [collections de RemoteApp Azure](remoteapp-collections.md): 

- Cloud : réside complètement dans Azure. Vous pouvez choisir d’enregistrer toutes les données dans le cloud (afin d’une collection exclusivement le nuage) ou pour vous connecter votre collection de sites à une VNET et enregistrer les données.   
- Environnement hybride : inclut un réseau virtuel pour l’accès local - cela nécessite l’utilisation d’Azure AD et un environnement Active Directory local.

Ce didacticiel vous guide dans le processus de création d’une collection de cloud. Il existe quatre étapes : 

1.  Créer une collection de RemoteApp Azure.
2.  Vous pouvez également configurer la synchronisation d’annuaires. Si vous utilisez Azure AD + Active Directory, vous devez synchroniser les utilisateurs, les contacts et les mots de passe de votre locale d’Active Directory à votre client Azure AD.
5.  Publier des applications.
6.  Configurer l’accès utilisateur.


**Avant de commencer**

Vous devez effectuer les opérations suivantes avant de créer la collection de sites :

- [S’inscrire](https://azure.microsoft.com/services/remoteapp/) pour Azure RemoteApp. 
- Collecter les informations sur les utilisateurs que vous voulez accorder l’accès à. Cela peut être soit des informations de compte de travail Active Directory pour les utilisateurs ou des informations de compte Microsoft.
- Cette procédure suppose que vous avez l’intention d’utiliser l’une des images de modèle fournis dans le cadre de votre abonnement ou que vous avez déjà téléchargé l’image du modèle que vous voulez utiliser. Si vous avez besoin de télécharger une image autre modèle, vous pouvez le faire à partir de la page d’Images de modèle. Cliquez sur **télécharger une image du modèle** et suivez les étapes de l’Assistant. 
- Vous voulez utiliser l’image d’Office 365 ProPlus ? Consultez les informations [ici](remoteapp-officesubscription.md).
- Si vous voulez fournir des applications personnalisées ou département programmes ? Créer une nouvelle [image](remoteapp-imageoptions.md) et l’utiliser dans votre collection de cloud.
- Déterminez si vous avez besoin pour vous connecter à un VNET. Si vous choisissez pour vous connecter à un VNET, vérifiez qu’il répond aux [instructions de dimensionnement](remoteapp-vnetsizing.md) et qu’il [peut se connecter à RemoteApp](remoteapp-vnet.md). Consultez l' [article de planification VNET ](remoteapp-planvnet.md)pour plus d’informations.
- Si vous utilisez un VNET, décidez si vous souhaitez joindre à votre domaine Active Directory local.

## <a name="step-1-create-a-cloud-collection---with-or-without-a-vnet"></a>Étape 1 : Créer une collection de cloud - avec ou sans un VNET##


Procédez comme suit pour **créer une collection exclusivement le nuage**:

1. Dans le portail de gestion, accédez à la page RemoteApp.
2. Cliquez sur **Nouveau > Création rapide**.
3. Entrez un nom pour votre collection de sites, puis sélectionnez votre région.
4. Choisir l’offre que vous souhaitez utiliser - standard ou de base.
5. Choisissez le modèle à utiliser pour cette collection de sites. 

    **Conseil :** Votre abonnement à RemoteApp est fourni avec des [images de modèle](remoteapp-images.md) qui contiennent Office 365 ou Office 2013 (pour une utilisation d’évaluation) programmes, certaines publié (par exemple, Word) et autres personnes prêt à publier. Vous pouvez également créer une nouvelle [image](remoteapp-imageoptions.md) et l’utiliser dans votre collection de cloud.


1. Cliquez sur **créer RemoteApp collection**.
    
    **Importantes :** Il peut prendre jusqu'à 30 minutes mise en service de votre collection de sites.

Une fois que votre collection de sites RemoteApp Azure a été créé, double-cliquez sur le nom de la collection de sites. Qui s’affiche la page de **Démarrage rapide** : il s’agit de l’endroit où vous terminez la configuration de la collection de sites.

Procédez comme suit pour créer un **nuage + collection VNET**:

1. Dans le portail de gestion, accédez à la page RemoteApp Azure.
2. Cliquez sur **Nouveau** > **créer avec VNET**.
3. Entrez un nom pour votre collection de sites.
4. Choisir l’offre que vous souhaitez utiliser - standard ou de base.
5. Choisissez le VNET que vous avez déjà créé. Ne savez pas comment procéder ? Pour l’instant, les étapes sont dans la rubrique [hybride](remoteapp-create-hybrid-deployment.md) .
6. Décidez si vous souhaitez participer à votre collection de sites à votre domaine. Si Oui, vous devez utiliser AD Connect pour intégrer Azure AD et votre environnement Active Directory. Que vous trouverez ci-dessous à **l’étape 2**.
6. Cliquez sur **créer RemoteApp collection**.


## <a name="step-2-configure-active-directory-directory-synchronization-optional"></a>Étape 2 : Configurer la synchronisation d’annuaire Active Directory (facultative) ##

Si vous souhaitez utiliser Active Directory, Azure RemoteApp nécessite la synchronisation d’annuaires entre Azure Active Directory et votre locale d’Active Directory pour synchroniser les utilisateurs, les contacts et les mots de passe à votre client Azure Active Directory. Pour obtenir des informations, voir [Configuration de Active Directory pour Azure RemoteApp](remoteapp-ad.md) . Vous pouvez également accéder directement à [AD Connect](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) pour plus d’informations.

## <a name="step-3-publish-apps"></a>Étape 3 : Publier des applications ##

Une application Azure RemoteApp est l’application ou le programme que vous fournissez à vos utilisateurs. Il se trouve dans l’image du modèle que vous avez téléchargée pour la collection de sites. Lorsqu’un utilisateur accède à une application, l’application s’ouvre dans leur environnement local, mais elle fonctionne bien sur une machine virtuelle dans Azure. 

Avant de vos utilisateurs d’accéder aux applications, vous devez publier les – vous permet d’applications de publication vos utilisateurs accèdent les applications par le biais du client de bureau à distance.
 
Vous pouvez publier plusieurs applications à votre collection d’Azure RemoteApp. Dans la page de publication, cliquez sur **Publier** pour ajouter un programme. Vous pouvez soit publier dans le menu **Démarrer** de l’image du modèle ou en spécifiant le chemin d’accès dans l’image du modèle pour l’application. Si vous choisissez d’ajouter dans le menu **Démarrer** , choisissez l’application à publier. Si vous décidez d’indiquer le chemin d’accès à l’application, indiquez un nom pour l’application et le chemin d’accès à l’endroit où il est installé sur l’image du modèle.

## <a name="step-4-configure-user-access"></a>Étape 4 : Configurer l’accès utilisateur ##

Maintenant que vous avez créé votre collection de sites, vous devez ajouter les utilisateurs que vous souhaitez être en mesure d’utiliser vos ressources à distance. Si vous utilisez Active Directory, les utilisateurs que vous donne accès pour doivent figurer dans le client Active Directory associé à l’abonnement que vous avez utilisé pour créer cette collection de sites.

1.  Dans la page de démarrage rapide, cliquez sur **configurer l’accès des utilisateurs**. 
2.  Entrez le compte professionnel (d’Active Directory) ou le compte Microsoft que vous souhaitez accorder l’accès.

    **Notes :** 

    Assurez-vous que vous utilisez la “user@domain.com” format.

    Si vous utilisez Office 365 ProPlus dans votre collection de sites, vous devez utiliser les identités Active Directory pour vos utilisateurs. Cela permet de valider les licences. 

3.  Une fois que les utilisateurs sont validées, cliquez sur **Enregistrer**.


## <a name="next-steps"></a>Étapes suivantes ##

C’est tout : vous avez correctement créé et déployé votre collection de sites RemoteApp Azure cloud. L’étape suivante consiste pour que vos utilisateurs de télécharger et installer le client de bureau à distance. Vous pouvez rechercher l’URL pour le client sur la page de démarrage rapide Azure RemoteApp. Ensuite, invitez utilisateurs dans le client et accéder les applications que vous avez publié.

### <a name="help-us-help-you"></a>Aidez-nous à améliorer vous 
Saviez-vous qu’outre évaluation de cet article et présenter des observations vers le bas en dessous, vous pouvez apporter des modifications à l’article lui-même ? Un élément manquant ? Un problème ? Écrire quelque chose qui se trouve juste confusion ? Défiler vers le haut, cliquez sur **Modifier sur GitHub** pour apporter des modifications - ceux soient transférés vers nous pour révision et puis, une fois que nous se les, vous voyez vos modifications et les améliorations ici.