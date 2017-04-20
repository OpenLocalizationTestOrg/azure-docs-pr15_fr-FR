
<properties
    pageTitle="Configuration requise d’image RemoteApp Azure | Microsoft Azure"
    description="En savoir plus sur la configuration requise pour la création d’images pour être utilisé avec Azure RemoteApp"
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



# <a name="requirements-for-azure-remoteapp-images"></a>Configuration requise pour les images RemoteApp Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

RemoteApp Azure utilise une image de Windows Server 2012 R2 pour héberger tous les programmes que vous voulez partager avec d’autres utilisateurs. Pour créer une image personnalisée, vous pouvez commencer avec une image existante ou [créez-en une](remoteapp-create-custom-image.md).

> [AZURE.TIP] Saviez-vous que votre abonnement Azure RemoteApp permet d’accéder à une image de Windows Server 2012 R2 dans la galerie de machine virtuelle Azure que vous pouvez utiliser pour créer votre propre image du modèle ? [Extraire](remoteapp-image-on-azurevm.md).  


La configuration requise pour l’image qui peuvent être téléchargés pour une utilisation avec Azure RemoteApp est :


- Applications personnalisées ne pas stocker des données localement sur l’image. Ces images sont sans état et doivent contenir uniquement des applications.
- L’image ne contient pas de données qui peuvent être perdues.
- La taille d’image doit être un multiple de mégaoctets. Si vous essayez de télécharger une image qui n’est pas un multiple exact, le téléchargement échoue.
- La taille d’image doit être 127 Go ou plus petit.
- Il doit se trouver sur un fichier de disque dur virtuel (fichiers VHDX ne sont pas pris en charge).
- Le disque dur virtuel ne doit pas être une machine virtuelle génération 2.
- Le disque dur virtuel peut être taille fixe ou dynamique. Un disque dur virtuel dynamique est recommandé, car il prend moins de temps pour la télécharger vers Azure qu’un fichier de disque dur virtuel de taille fixe.
- Le disque doit être initialisé à l’aide de l’enregistrement (démarrage principal) partition style. Le style de partition GUID partition GPT (table) n’est pas pris en charge.
- Le disque dur virtuel doit contenir une seule installation de Windows Server 2012 R2. Il peut contenir plusieurs volumes, mais un seul qui contient une installation de Windows.
- Le rôle Remote Desktop Session hôte (RDSH) et la fonctionnalité Experience utilisateur doivent être installés.
- Le rôle de l’agent de connexion Bureau à distance doit *pas* être installé.
- Le fichier système de cryptage doit être désactivé.
- L’image doit être préparée avec Sysprep en utilisant les paramètres **/oobe /generalize /shutdown** (ne pas utiliser le paramètre **/mode:vm** ).
- Téléchargement de votre disque dur virtuel à partir d’une chaîne d’instantanés n’est pas pris en charge.

Pour plus d’informations sur la création d’images pour Azure RemoteApp, voir [créer une image RemoteApp Azure](remoteapp-imageoptions.md) .
