<properties 
    pageTitle="Utiliser des privilèges racine sur machines virtuelles Linux | Microsoft Azure" 
    description="Découvrez comment utiliser des privilèges racine sur une machine virtuelle Linux dans Azure." 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="szarkos" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="szark"/>


# <a name="using-root-privileges-on-linux-virtual-machines-in-azure"></a>À l’aide des privilèges racine sur machines virtuelles Linux Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Par défaut, la `root` utilisateur est désactivée sur machines virtuelles de Linux dans Azure. Les utilisateurs peuvent exécuter des commandes avec des privilèges élevés à l’aide de la `sudo` commande. Toutefois, l’expérience peut-être varier selon la façon dont le système a été mis en service.

1. **Code et mot de passe ou uniquement** - la machine virtuelle a été mis en service avec soit un certificat (`.CER` fichier) ou code ainsi qu’un mot de passe, ou simplement un nom d’utilisateur et mot de passe. Dans ce cas `sudo` vous demandera le mot de passe avant d’exécuter la commande.

2. **Clé SSH uniquement** - la machine virtuelle a été configurée avec un certificat (`.cer`, `.pem`, ou `.pub` fichier) ou code, mais aucun mot de passe.  Dans ce cas `sudo` **ne sera pas** choisir le mot de passe de l’utilisateur avant d’exécuter la commande.


## <a name="ssh-key-and-password-or-password-only"></a>SSH clé et mot de passe ou uniquement

Connectez-vous à la machine virtuelle Linux à l’aide de l’authentification SSH clé ou mot de passe, puis sur Exécuter à l’aide des commandes `sudo`, par exemple :

    # sudo <command>
    [sudo] password for azureuser:

L’utilisateur devra dans ce cas pour un mot de passe. Après avoir entré le mot de passe `sudo` exécute la commande avec `root` privilèges.

Vous pouvez également activer sudo passwordless en modifiant la `/etc/sudoers.d/waagent` de fichiers, par exemple :

    #/etc/sudoers.d/waagent
    azureuser ALL = (ALL) NOPASSWD: ALL

Cette modification permettra sudo passwordless par l’utilisateur « azureuser ».

## <a name="ssh-key-only"></a>SSH clé uniquement

Connectez-vous à la machine virtuelle Linux à l’aide de l’authentification par clé SSH, puis sur Exécuter à l’aide des commandes `sudo`, par exemple :

    # sudo <command>

Dans ce cas l’utilisateur sera **pas** invité pour un mot de passe. Après avoir appuyé sur `<enter>`, `sudo` exécute la commande avec `root` privilèges.

 
