<properties
    pageTitle="Meilleures pratiques de Azure RemoteApp | Microsoft Azure"
    description="Meilleures pratiques pour la configuration et l’utilisation de RemoteApp Azure."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="best-practices-for-configuring-and-using-azure-remoteapp"></a>Meilleures pratiques pour la configuration et l’utilisation de RemoteApp Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Les informations suivantes peuvent vous aider à configurer et utiliser efficacement RemoteApp Azure.

## <a name="connectivity"></a>Connectivité


- Toujours utiliser la dernière version du client. À l’aide de clients plus anciens peut entraîner des problèmes de connectivité et autres expériences dégradés. L’activation de mises à jour automatiques des applications pour votre appareil garantit que le client plus récent est toujours installé.
- Toujours utiliser la connexion à internet plus stable et fiable à votre disposition.  
- Utiliser uniquement prise en charge les connexions proxy pour des performances optimales connectivity.  Le proxy SOCKS n’est pas pris en charge.

## <a name="applications"></a>Applications


- Enregistrez et fermez les applications RemoteApp lorsque vous avez fini avec l’application. Ne pas fermer l’application peut entraîner la perte de données.
- Valider des applications personnalisées avant de les utiliser dans Azure RemoteApp. Cela implique de vérifier qu’ils travaillent sur une plateforme de session multiples et n’utilisent pas de ressources inutiles tels que la quantité de mémoire et de l’UC qui peut-être détriment à un autre utilisateur dans la même collection. Pour plus d’informations, téléchargez et consultez les [Application compatibilité meilleures pratiques pour les Services Bureau à distance](http://www.dabcc.com/resources/Application%20Compatibility%20Best%20Practices%20for%20Remote%20Desktop%20Services.pdf).

## <a name="configuration-and-management"></a>Gestion et configuration


- Images de votre modèle de tenir à jour, l’installation des mises à jour logicielles et autres correctifs critiques selon vos besoins. Cela garantit que RemoteApp Azure échelles automatique pour répondre à votre capacité, chaque instance est corrigé.  
- Vérifiez que votre déploiement de Services de fédération Active Directory (AD FS) est fiable et sécurisée. Dans le cas contraire authentifications de client peuvent échouer pour empêcher les utilisateurs d’accéder à Azure RemoteApp.
- Configurer images de modèle avec les applications installées, des rôles ou des fonctionnalités telles qu’elles soient sans état. Ils ne doivent pas compter sur toutes les instances des machines virtuelles dans un service RemoteApp en cours dans un état permanent.
    - Stocker les données des utilisateurs dans les profils utilisateur ou d’autres emplacements de stockage externes du service, telles que locaux fichiers partages ou OneDrive.
    - Stocker des données partagées dans des emplacements de stockage externes du service, telles que locaux fichiers partages ou OneDrive.
    - Configurer des paramètres système à l’échelle de l’image du modèle plutôt que sur des machines virtuelles individuelles dans un service.
    - Désactiver les mises à jour logicielles automatiques pour les applications publiées - à la place les appliquer manuellement à l’image du modèle et de les tester avant de déployer à partir du modèle.
