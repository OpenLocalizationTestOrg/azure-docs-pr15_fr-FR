<properties 
    pageTitle="Sélection de noms d’utilisateur pour Linux | Microsoft Azure" 
    description="Découvrez comment sélectionner des noms d’utilisateur pour une machine virtuelle Linux dans Azure." 
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



#<a name="selecting-user-names-for-linux-on-azure"></a>Sélection de noms d’utilisateur pour Linux sur Azure#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Lorsque vous configurez une machine virtuelle Linux sur Azure, vous devez spécifier le nom d’un utilisateur non racine que vous pouvez utiliser ultérieurement pour vous connecter à la machine virtuelle. Vous pouvez choisir le nom du nouvel utilisateur, ou si la mise en service via le portail Azure classique, vous pouvez accepter le nom par défaut « azureuser ».

Dans la plupart des cas, cet utilisateur n’existe pas dans l’image de base et est créé pendant le processus de mise en service. Si l’utilisateur existe sur l’image de machine virtuelle base, puis l’agent Azure Linux configure simplement le mot de passe et/ou code SSH pour cet utilisateur en fonction des informations que vous avez spécifié lors de la création de la machine virtuelle.

**Toutefois**, Linux définit un ensemble de noms d’utilisateurs qui ne doit pas être utilisé. Le processus de configuration va **échouer** si vous essayez de mettre en service un VM Linux à l’aide d’un utilisateur système existant qui est défini comme un utilisateur avec UID 0 et 99. Un exemple classique est la `root` utilisateur, c'est-à-dire UID 0.

 - Voir aussi : [plages de Base Linux Standard - ID d’utilisateur](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

Voici une liste des utilisateurs système intégré courantes pour CentOS et Ubuntu que vous devez éviter d’utiliser lors de la configuration d’une machine virtuelle Linux sur Azure. Cette liste est qu’un exemple, reportez-vous à la documentation de votre distribution pour vous assurer que le nom d’utilisateur que vous choisissez n’est pas en conflit avec un utilisateur système existant.


## <a name="centos"></a>CentOS
- Abrt
- ADM
- audio
- Corbeille
- CD-ROM
- cgred
- processus
- dbus
- appels distant
- fondu
- disque
- disquette
- FTP
- FTP
- jeux
- Gopher
- haldaemon
- arrêter
- kmem
- verrouillage
- LP
- courrier
- homme
- mémoire
- nfsnobody
- ne pas partager
- NTP
- opérateur
- oprofile
- postdrop
- suffixe
- qpidd
- racine
- RPC
- rpcuser
- saslauth
- arrêt
- slocate
- sshd
- stapdev
- stapusr
- synchronisation
- Sys
- bande
- test
- tcpdump
- TTY
- utilisateurs
- utempter
- utmp
- UUCP
- vcsa
- vidéo
- ROUE


## <a name="ubuntu"></a>Ubuntu
- ADM
- administrateur
- audio
- sauvegarde
- Corbeille
- CD-ROM
- crontab
- processus
- appels distant
- fondu
- disque
- télécopie
- disquette
- fusible
- jeux
- gnats
- IRC
- kmem
- orientation paysage
- libuuid
- liste
- LP
- courrier
- homme
- bus de messages des
- mlocate
- NETDEV
- obtenir des informations
- ne pas partager
- nogroup
- opérateur
- plugdev
- proxy
- racine
- SASL
- ombre
- src
- SSH
- sshd
- personnel
- sudo
- synchronisation
- Sys
- journal système
- bande
- TTY
- utilisateurs
- utmp
- UUCP
- vidéo
- voix
- whoopsie
- données de www

 