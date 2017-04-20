<properties
    pageTitle="Redéployez pile Azure | Microsoft Azure"
    description="Redéployez pile Azure."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="erikje"/>

# <a name="redeploy-azure-stack"></a>Redéployez pile Azure

Pour redéployez pile Azure, vous devez démarrer sur à partir de zéro comme décrit ci-dessous.

## <a name="steps-to-redeploy-azure-stack"></a>Étapes à suivre pour redéployez Azure pile

1. Redémarrez l’hôte dans le système d’exploitation d’origine (installé à vierge). Il s’agit pas le paramètre par défaut dans le menu de démarrage, vous devez utiliser KVM ou console locale pour la sélectionner lors du redémarrage (pendant l’installation, vous avez nommé le « démarrage de disque dur virtuel » du système d’exploitation à « AzureStack TP2 », cela vous aidera à laquelle OS est qui).

    Vous n’avez pas besoin de supprimer l’entrée de démarrage existante (nouveau prise en charge de script « PrepareBootFromVHD.ps1 » prend en charge que pour vous.)

2. Si vous n’avez pas KVM, ou pour sélectionner le système d’exploitation de démarrage avant de redémarrer :
    
    1. Recherchez le script.\BootMenuNoKVM.ps1. Ce fichier est disponible avec les autres scripts de prise en charge fournis avec cette version.
    
    2. Exécuter le script avec des privilèges élevés. Sélectionnez le nom du système d’exploitation hôte d’origine. Cela démarre l’hôte de l’hôte d’origine du système d’exploitation sans nécessitant un accès KVM.
    
    3. Une fois le script terminée vous êtes invité à confirmer le redémarrage.

    - S’il existe d’autres personnes qui se connectent, cette commande échouera.

    - Veuillez simplement exécuter la commande suivante : redémarrer l’ordinateur-forcer 
 
3. Supprimez le fichier CloudBuilder.vhdx qui a été utilisé dans le cadre du déploiement précédent.

    Vous n’avez pas besoin de supprimer le Pool de stockage existant à partir du déploiement TP2 précédent. Le script de déploiement détecte efface les existantes, puis crée de nouveaux.

5. Redéployez de copier une nouvelle copie de la CloudBuilder.vhdx, démarrez, etc..

## <a name="next-steps"></a>Étapes suivantes

[Se connecter à pile Azure](azure-stack-connect-azure-stack.md)
