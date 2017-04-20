<properties
        pageTitle="Réinitialiser votre mot de passe Linux VM et code SSH depuis l’interface | Microsoft Azure"
        description="Comment utiliser l’extension VMAccess à partir de l’Interface de ligne de commande (CLI) Azure pour réinitialiser un mot de passe Linux VM ou code, corrigez la configuration SSH et vérifier la cohérence des disques"
        services="virtual-machines-linux"
        documentationCenter=""
        authors="cynthn"
        manager="timlt"
        editor=""
        tags="azure-service-management"/>

<tags
        ms.service="virtual-machines-linux"
        ms.workload="infrastructure-services"
        ms.tgt_pltfrm="vm-linux"
        ms.devlang="na"
        ms.topic="article"
        ms.date="06/14/2016"
        ms.author="cynthn"/>

# <a name="how-to-reset-a-linux-vm-password-or-ssh-key-fix-the-ssh-configuration-and-check-disk-consistency-using-the-vmaccess-extension"></a>Comment réinitialiser un mot de passe Linux VM ou code, corrigez la configuration SSH et vérifier la cohérence des disques à l’aide de l’extension VMAccess


Si vous ne pouvez pas vous connecter à une machine virtuelle Linux sur Azure en raison d’un mot de passe oublié, clé SSH (Secure Shell) incorrecte, ou un problème avec la configuration SSH, utilisez l’extension VMAccessForLinux avec l’infrastructure du langage commun Azure pour réinitialiser le mot de passe ou code, corrigez la configuration SSH et vérifier la cohérence des disques. 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de gestionnaire de ressources](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess).

Avec l’infrastructure du langage commun Azure, vous utilisez la commande **définie extension azure machine virtuelle** à partir de votre interface de ligne de commande (Bash, terminaux, invite) pour accéder aux commandes. Exécuter l' **aide azure machine virtuelle extension définir** pour l’utilisation d’extension détaillée.

Avec l’infrastructure du langage commun Azure, vous pouvez effectuer les tâches suivantes :

+ [Réinitialiser le mot de passe](#pwresetcli)
+ [Réinitialiser le code SSH](#sshkeyresetcli)
+ [Réinitialiser le mot de passe et le code SSH](#resetbothcli)
+ [Créer un nouveau compte d’utilisateur sudo](#createnewsudocli)
+ [Réinitialiser la configuration SSH](#sshconfigresetcli)
+ [Supprimer un utilisateur](#deletecli)
+ [Afficher l’état de l’extension VMAccess](#statuscli)
+ [Vérifier la cohérence des disques](#checkdisk)
+ [Réparer des disques ajoutés sur votre Linux VM](#repairdisk)


## <a name="prerequisites"></a>Conditions préalables

Vous devez effectuer les opérations suivantes :

- Vous devez [installer l’infrastructure du langage commun Azure](../xplat-cli-install.md) et [se connecter à votre abonnement](../xplat-cli-connect.md) à utiliser les ressources Azure associés à votre compte.
- Définir le mode approprié pour le modèle de déploiement classique en tapant ce qui suit à l’invite :
        
        azure config mode asm
        
- Ont un nouveau mot de passe ou un ensemble de clés SSH, si vous voulez réinitialiser le d’eux. Vous n’avez pas besoin ces si vous voulez rétablir la configuration SSH.


## <a name="pwresetcli"></a>Réinitialiser le mot de passe

1. Créer un fichier nommé PrivateConf.json contenant ces lignes. Remplacer les crochets et le & #60 ; espace réservé & #62 ; valeurs par vos propres informations.

        {
        "username":"<currentusername>",
        "password":"<newpassword>",
        "expiration":"<2016-01-01>"
        }

2. Exécutez la commande suivante, en remplaçant le nom de votre machine virtuelle pour #60 ; -nom de la machine virtuelle & #62 ;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

## <a name="sshkeyresetcli"></a>Réinitialiser le code SSH

1. Créer un fichier nommé PrivateConf.json avec ces contenu. Remplacer les crochets et le & #60 ; espace réservé & #62 ; valeurs par vos propres informations.

        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>"
        }

2. Exécutez la commande suivante, en remplaçant le nom de votre machine virtuelle pour #60 ; -nom de la machine virtuelle & #62 ;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="resetbothcli"></a>Réinitialiser le mot de passe et le code SSH

1. Créer un fichier nommé PrivateConf.json avec ces contenu. Remplacer les crochets et le & #60 ; espace réservé & #62 ; valeurs par vos propres informations.

        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>",
        "password":"<newpassword>"
        }

2. Exécutez la commande suivante, en remplaçant le nom de votre machine virtuelle pour #60 ; -nom de la machine virtuelle & #62 ;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="createnewsudocli"></a>Créer un nouveau compte d’utilisateur sudo

Si vous avez oublié votre nom d’utilisateur, vous pouvez utiliser VMAccess pour en créer un nouveau avec l’autorité sudo. Dans ce cas, le nom d’utilisateur et mot de passe existant ne seront pas modifiées.

Pour créer un nouvel utilisateur sudo avec accès mot de passe, utilisez le script dans [Réinitialiser le mot de passe](#pwresetcli) et spécifiez le nom d’utilisateur.

Pour créer un nouvel utilisateur sudo avec accèsSSH clé, utilisez le script dans [Réinitialiser le code SSH](#sshkeyresetcli) et spécifiez le nom d’utilisateur.

Vous pouvez également utiliser [Réinitialiser le mot de passe et le code SSH](#resetbothcli) pour créer un nouvel utilisateur avec mot de passe et clé accèsSSH.

## <a name="sshconfigresetcli"></a>Réinitialiser la configuration SSH

Si la configuration SSH est dans un état indésirable, vous pouvez également perdre l’accès à la machine virtuelle. Vous pouvez utiliser l’extension VMAccess pour rétablir la configuration à son état par défaut. Pour cela, il vous suffit de définir la clé « reset_ssh » par « True ». L’extension sera redémarrer le serveur SSH, ouvrez le port SSH sur votre ordinateur virtuel et rétablir la configuration SSH valeurs par défaut. Le compte d’utilisateur (nom, votre mot de passe ou touches SSH) n’est pas remplacé.

> [AZURE.NOTE] Le fichier de configuration de SSH est réinitialisé se trouve à /etc/ssh/sshd_config.

1. Créer un fichier nommé PrivateConf.json avec ce contenu.

        {
        "reset_ssh":"True"
        }

2. Exécutez la commande suivante, en remplaçant le nom de votre machine virtuelle pour #60 ; -nom de la machine virtuelle & #62 ;. 

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="deletecli"></a>Supprimer un utilisateur

Si vous voulez supprimer un compte d’utilisateur sans se connecter à la machine virtuelle directement, vous pouvez utiliser ce script.

1. Créer un fichier nommé PrivateConf.json avec ce contenu, en remplaçant le nom d’utilisateur à supprimer pour #60 ; usernametoremove & #62 ;. 

        {
        "remove_user":"<usernametoremove>"
        }

2. Exécutez la commande suivante, en remplaçant le nom de votre machine virtuelle pour #60 ; -nom de la machine virtuelle & #62 ;. 

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="statuscli"></a>Afficher l’état de l’extension VMAccess

Pour afficher l’état de l’extension VMAccess, exécutez la commande suivante.

        azure vm extension get

## <a name="a-namecheckdiskacheck-consistency-of-added-disks"></a>< un name = 'vérification du' <</a>vérifier la cohérence des disques

Pour exécuter la commande fsck sur tous les disques dans votre machine virtuelle Linux, vous devez effectuer les opérations suivantes :

1. Créer un fichier nommé PublicConf.json avec ce contenu. Vérification du disque prend une valeur booléenne pour s’il faut vérifier des disques connectés à votre machine virtuelle ou non. 

        {   
        "check_disk": "true"
        }

2. Exécutez cette commande pour exécuter, en remplaçant le nom de votre machine virtuelle pour #60 ; -nom de la machine virtuelle & #62 ;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json 

## <a name='repairdisk'></a>Réparer des disques 

Pour réparer les disques qui ne sont pas monter ou présentant des erreurs de configuration de montage, utilisez l’extension VMAccess pour réinitialiser la configuration de montage sur votre machine virtuelle Linux. En remplaçant le nom de votre disque #60 ; yourdisk & #62 ;.

1. Créer un fichier nommé PublicConf.json avec ce contenu. 

        {
        "repair_disk":"true",
        "disk_name":"<yourdisk>"
        }

2. Exécutez cette commande pour exécuter, en remplaçant le nom de votre machine virtuelle pour #60 ; -nom de la machine virtuelle & #62 ;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json



## <a name="next-steps"></a>Étapes suivantes

* Si vous souhaitez utiliser les applets de commande PowerShell Azure ou des modèles de gestionnaire de ressources Azure pour réinitialiser le mot de passe ou code, corrigez la configuration SSH et vérifier la cohérence des disques, consultez la [documentation d’extension VMAccess sur GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). 

* Vous pouvez également utiliser le [portail Azure](https://portal.azure.com) pour réinitialiser le mot de passe ou code SSH d’un Linux VM déployée dans le modèle de déploiement classique. Vous ne pouvez pas utiliser actuellement la ne portail à cette pour un Linux VM déployée dans le modèle de déploiement du Gestionnaire de ressources.

* Pour plus d’informations sur l’utilisation des extensions de machine virtuelle pour Azure machines virtuelles, consultez [les fonctionnalités et extensions de machine virtuelle](virtual-machines-linux-extensions-features.md) .
