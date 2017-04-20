<properties 
    pageTitle="Qu’est RemoteApp Azure ? | Microsoft Azure" 
    description="Découvrez comment partager des applications et des ressources à n’importe quel appareil via Azure RemoteApp." 
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
    ms.topic="get-started-article" 
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="what-is-azure-remoteapp"></a>Qu’est RemoteApp Azure ?

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

RemoteApp Azure apporte les fonctionnalités du programme Microsoft RemoteApp en local, sauvegardée par les Services Bureau à distance, à Azure. RemoteApp Azure vous permet de fournir un accès à distance sécurisé aux applications de nombreux appareils différents utilisateur. RemoteApp Azure héberge fondamentalement non permanent sessions Terminal Server dans le cloud, et vous obtenez les utiliser et les partager avec d’autres utilisateurs.

Avec Azure RemoteApp vous pouvez partager des applications et des ressources avec des utilisateurs sur n’importe quel appareil. Nous héberger vos applications dans le cloud, ce qui signifie que nous prendre en charge le matériel et la même échelle pour répondre aux besoins de l’utilisateur. Tout ce que vous avez à faire est télécharger les applications que vous voulez partager, puis d’obtenir vos utilisateurs à utiliser ces applications. [Obtenir des utilisateurs à leurs propres périphériques](remoteapp-clients.md), tandis que vous gérez tous les éléments à partir du portail Azure. Vous avez encore la possibilité de l’utilisation de vos informations d’identification d’entreprise, ce qui vous permet de garantir la sécurité des applications et des données.

Poursuivez votre lecture pour plus d’informations sur RemoteApp Azure, ou, si nous avons déjà convaincu vous [Essayez-le maintenant](https://azure.microsoft.com/services/remoteapp/).

Avez des questions sur RemoteApp Azure ? Consultez notre [Forum aux questions](remoteapp-faq.md).

RemoteApp Azure fait partie de l' [Microsoft Virtual Desktop Infrastructure](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**Nouveau !** Vous voulez en savoir plus sur RemoteApp Azure ? Ou vous êtes prêt à valider Azure RemoteApp à l’échelle ? Participer à notre hebdomadaire [Demandez aux experts webinaire](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website).

## <a name="azure-remoteapp-collections"></a>Collections RemoteApp Azure
Il existe deux types de [collections de RemoteApp Azure](remoteapp-collections.md):


- Une **collection de sites cloud** est hébergé dans et stocke les données des programmes dans le cloud. Les utilisateurs peuvent accéder aux applications en vous connectant à l’aide de leur compte Microsoft ou les informations d’identification d’entreprise synchronisé ou fédérée avec Azure Active Directory.

    Choisissez une collection de cloud lorsque l’application que vous voulez partager ne nécessite pas une connexion à n’importe quelle ressource réseau privé de votre société (par exemple, via un périphérique VPN). Si l’application utilise des ressources sur Internet, OneDrive ou Azure, une collection de cloud fonctionnera pour vous. Il est également le moyen le plus rapide créer.

- Une **collection de sites hybride** est hébergé dans et stocke les données dans le cloud Azure, mais également des données d’access vous permet d’utilisateurs et des ressources que qui se trouve sur votre réseau local. Les utilisateurs peuvent accéder applications en vous connectant à l’aide de leurs informations d’identification d’entreprise synchronisé ou fédérée avec Azure Active Directory.

    Choisissez une collection hybride si vous avez besoin d’une connexion à des ressources sur un réseau privé de votre entreprise. Par exemple, si l’application a besoin d’accéder à l’une des opérations suivantes :

    - Serveurs de fichiers situés sur votre intranet
    - Quicken
    - Bases de données derrière un pare-feu

    Il s’agit généralement plus utile pour les grandes entreprises avec un grand nombre de ressources sur leurs réseaux privés ne peuvent pas être déplacés vers le cloud.

Différentes collections présentant plusieurs options, y compris les réseaux, afin que le calcul de la [collection qui](remoteapp-collections.md) vous convient le mieux. 


### <a name="updating-your-collection"></a>Mise à jour de votre collection de sites
Parmi les principales différences entre les collections hybride et cloud est le traitement des mises à jour logicielles. Une collection de cloud qui utilise l’image préinstallé Office 365 ProPlus ou Office 2013, vous n’avez pas à vous soucier des mises à jour. Le service tient lui-même et déploie mises à jour en permanence, applications et le système d’exploitation.

Pour les collections hybride, ainsi que les collections de cloud qui utilisent une image du modèle personnalisé, vous êtes chargé de la maintenance de l’image et les applications. Pour les images à un domaine, vous pouvez contrôler les mises à jour à l’aide d’outils tels que Windows Update, stratégie de groupe ou System Center.

Une fois que vous mettez à jour votre image modèle personnalisé, vous téléchargez la nouvelle image dans le cloud Azure et mettez à jour la collection de sites pour utiliser la nouvelle image. (Vous pouvez faire ceci à partir de la page de RemoteApp Azure **Démarrage rapide** ou le tableau de bord.)

Pour plus d’informations, voir [mettre à jour votre collection de sites](remoteapp-update.md) .

## <a name="supported-remoteapp-clients"></a>Clients RemoteApp pris en charge
Azure RemoteApp est pris en charge dans les applications clientes RemoteApp pour Windows et Windows RT, ainsi que les applications de bureau à distance Microsoft pour Mac, iOS et Android. Vos utilisateurs peuvent utiliser ces applications sur leur appareil mobile ou le calcul des appareils pour accéder aux programmes RemoteApp Azure nouveau.

Pour plus d’informations sur les clients, voir [accéder à vos applications dans Azure RemoteApp](remoteapp-clients.md) .

## <a name="next-steps"></a>Étapes suivantes
Accédez ! Essayez-le ! Ces articles aident à la mise en route avec Azure RemoteApp :

- [Quel type de collection de sites devez-vous pour RemoteApp Azure ?](remoteapp-collections.md)
- [Créer une image RemoteApp Azure](remoteapp-imageoptions.md)
- [Comment créer une collection de cloud d’Azure RemoteApp](remoteapp-create-cloud-deployment.md)
- [Comment créer une collection hybride d’Azure RemoteApp](remoteapp-create-hybrid-deployment.md)
- [Gestion des licences fonctionnement dans Azure RemoteApp ?](remoteapp-licensing.md)
- [Recommandations d’utilisation RemoteApp Azure](remoteapp-bestpractices.md)
- [Forum aux questions RemoteApp Azure](remoteapp-faq.md)
 

### <a name="help-us-help-you"></a>Aidez-nous à améliorer vous 
Saviez-vous qu’outre évaluation de cet article et présenter des observations vers le bas en dessous, vous pouvez apporter des modifications à l’article lui-même ? Un élément manquant ? Un problème ? Écrire quelque chose qui se trouve juste confusion ? Défiler vers le haut et cliquez sur **Modifier sur GitHub** ou sur **Modifier** pour apporter des modifications - ceux soient transférés vers nous pour révision et puis, une fois que nous se les, vous voyez vos modifications et les améliorations ici.