
<properties
    pageTitle="Ne stockez jamais des données sensibles sur des images personnalisées pour Azure RemoteApp | Microsoft Azure"
    description="En savoir plus sur les instructions pour le stockage des données dans des images personnalisées dans Azure RemoteApp"
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


# <a name="never-store-sensitive-data-on-custom-images"></a>Ne stockez jamais des données sensibles sur des images personnalisées

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Lorsque vous hébergez vos propres application dans Azure RemoteApp, la première étape consiste à créer une image personnalisée. Nous utilisons cette image personnalisée pour créer des instances de machine virtuelle qui servent vos applications à vos utilisateurs. L’image personnalisée doit contenir uniquement les applications et des données sensibles jamais qui peuvent être perdues, tels que des bases de données SQL, personnel ou des fichiers de données spéciales comme QuickBooks société. Toutes les données sensibles doivent résider externe à Azure RemoteApp sur un serveur de fichiers, machine virtuelle Azure un autre, ou dans SQL Azure. L’image doit héberger simplement l’application qui se connecte à la source de données et présente les données. Pour plus d’informations, consultez [Configuration requise pour les images RemoteApp Azure](remoteapp-imagereqs.md) . 

Pour mieux comprendre pourquoi vous ne devez pas stocker des données sensibles, vous devez comprendre le fonctionnement de RemoteApp Azure. Lorsqu’une collection de sites est créé ou mis à jour, en coulisses plusieurs clones ou plusieurs copies de l’image sont créés. Toutes les instances de machine virtuelle sont la réplique exacte de l’image personnalisée ; Lorsque les utilisateurs lancent des applications qu’ils sont connectés à un de ces instances machine virtuelle. Mais la même instance n’est pas garantie et doit n’a aucune importance, car ils ne sont pas permanentes. Les instances de machine virtuelle héberge les applications ne sont pas permanentes et peuvent être destruction ou supprimés basé, par exemple, au cours de la mise à jour de la collection de sites. 

Une fois que la collection est configurée et les utilisateurs commencent à se connecter aux ordinateurs virtuels, données utilisateur sont permanentes et protégé, car il est enregistré sur stockage distinct au sein d’un disque dur virtuel que nous appelons un [disque de profil utilisateur (UDP)](remoteapp-upd.md), ce qui est le profil utilisateur dans c:\users\<%USERPROFILE% >. Lorsqu’une application démarre, l’UDP est chargé et considéré comme un profil utilisateur local par le système d’exploitation. En savoir plus sur la façon de [RemoteApp Azure enregistre les données et paramètres utilisateur](remoteapp-upd.md).

Exemple de données qui ne doit pas résider dans l’image :

- Données pour les utilisateurs à accéder à partagées
- Base de données SQL ou QuickBooks DB
- Toutes les données de D:\

Données d’exemple qui peuvent se trouver dans le profil par défaut à copier dans UDP chaque utilisateurs :

- Fichiers de configuration par utilisateur
- Scripts que les utilisateurs devront conservées dans leur UDP

Points importants suivants :

- Ne jamais store des données sensibles qui peuvent être perdues sur l’image lors de la création d’une image personnalisée.
- Des données sensibles doivent toujours résider sur un serveur de fichier distinct, distincte Azure virtuelle, sur le cloud et toujours externe vers les instances de machine virtuelle qui héberge vos applications Azure RemoteApp. 
- Données de l’utilisateur sont enregistrées et est conservée dans le disque de profil utilisateur (UDP)


