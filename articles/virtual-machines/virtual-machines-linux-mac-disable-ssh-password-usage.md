<properties
    pageTitle="Désactiver les mots de passe SSH sur votre Linux VM en configurant SSHD | Microsoft Azure"
    description="Sécurisez votre VM Linux sur Azure en désactivant les connexions de mot de passe pour SSH."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="v-livech"/>

# <a name="disable-ssh-passwords-on-your-linux-vm-by-configuring-sshd"></a>Désactiver les mots de passe SSH sur votre Linux VM en configurant SSHD

Cet article explique comment faire pour verrouiller la sécurité de votre Linux VM login.  Dès que la voie SSH 22 est ouvert jusqu’au début de robots world essayer de se connecter en deviner les mots de passe.  Ce que nous fera dans cet article est désactivez les connexions de mot de passe sur SSH.  La possibilité d’utiliser les mots de passe en supprimant complètement nous protéger la VM Linux à partir de ce type d’attaque en force.  Le bonus supplémentaire est, nous allons configurer Linux SSHD pour autoriser uniquement les connexions via SSH des clés publiques et privées, de loin la méthode la plus sûre pour vous connecter à Linux.  Les combinaisons possibles de celui-ci de verser à deviner la clé privée est exponentielle et par conséquent permet de combattre robots à partir de la même déranger pour essayer de force brute SSH clés.


## <a name="goals"></a>Objectifs

- Configurer SSHD pour interdire :
  - Connexions de mot de passe
  - Connexion de l’utilisateur racine
  - Authentification défi-réponse
- Configurer SSHD pour permettre à :
  - Seules les connexions clées SSH
- Redémarrez SSHD lorsque vous êtes connecté toujours
- Tester la nouvelle configuration SSHD

## <a name="introduction"></a>Introduction

[SSH défini](https://en.wikipedia.org/wiki/Secure_Shell)

SSHD est le serveur SSH s’exécute sur le VM Linux.  SSH est un client qui s’exécute à partir d’un shell sur votre poste de travail MacBook ou Linux.  SSH est également le protocole utilisé pour sécuriser et chiffrer les communications entre votre poste de travail et la VM Linux.

Pour cet article, il est très important de maintenir une connexion à votre Linux VM Ouvrez la présentation entière de fusion.  Pour cette raison, nous allons ouvrir deux terminaux et SSH de Linux VM de chacun d’eux.  Nous utiliserons le premier terminal pour apporter les modifications à un fichier de configuration SSHDs et redémarrez le service SSHD.  Nous utiliserons le terminal deuxième pour tester ces modifications une fois que le service est redémarré.  Étant donné que nous apportions désactivant les mots de passe SSH et dépendre strictement SSH clés, si vos clés SSH ne sont pas corrects et que vous fermez la connexion à la machine virtuelle, la machine virtuelle seront définitivement verrouillé et ne seront en mesure de pour vous connecter à celle-ci qu’elle doit être supprimée et recréée.

## <a name="prerequisites"></a>Conditions préalables

- [Créer des clés SSH sur Linux et Mac pour les machines virtuelles Linux dans Azure](virtual-machines-linux-mac-create-ssh-keys.md)
- Compte Azure
  - [abonnement d’essai gratuit](https://azure.microsoft.com/pricing/free-trial/)
  - [Portail Azure](http://portal.azure.com)
- Linux VM s’exécutant sur azure
- SSH paire de clés publiques et privées dans`~/.ssh/`
- Clé publique SSH dans `~/.ssh/authorized_keys` sur la VM Linux
- Droits sudo sur l’ordinateur virtuel
- Port 22 ouvert

## <a name="quick-commands"></a>Commandes rapides

_Expérimenté Linux Admins qui veulent simplement la version TLDR commencez ici.  Pour tous les participants peuvent qui souhaite la description détaillée et parcourir ignorent cette section._

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes

# Change PermitRootLogin to this:
PermitRootLogin no

# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no

# On the Debian Family
username@macbook$ sudo service ssh restart

# On the RedHat Family
username@macbook$ sudo service sshd restart
```

## <a name="detailed-walk-through"></a>Présentation détaillée de fusion

Connexion de Linux VM sur Terminal Server 1 (T1).  Connexion de Linux VM sur Terminal Server 2 (T2).

Sous T2, nous allons modifier le fichier de configuration SSHD.  

```
username@macbook$ sudo vim /etc/ssh/sshd_config
```

À partir de là, nous allez modifier uniquement les paramètres pour désactiver les mots de passe et activer des connexions à des clés SSH.  Il existe plusieurs paramètres dans ce fichier que vous devez effectuer des recherches et modifiez pour sécuriser Linux & SSH comme que nécessaire.

#### <a name="disable-password-logins"></a>Désactivez les connexions de mot de passe

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no
```

#### <a name="enable-public-key-authentication"></a>Activer l’authentification par clé publique

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes
```

#### <a name="disable-root-login"></a>Désactiver la connexion racine

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PermitRootLogin to this:
PermitRootLogin no
```

#### <a name="disable-challenge-response-authentication"></a>Désactiver l’authentification défi-réponse

```
# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no
```

### <a name="restart-sshd"></a>Redémarrez SSHD

À partir du shell T1 Vérifiez que vous n’êtes toujours connecté.  Il s’agit critique afin que vous ne pas sont verrouillés se déconnecter de votre ordinateur virtuel si vos clés SSH ne sont pas correctes dans la mesure où les mots de passe sont à présent désactivés.  Si les paramètres sont incorrects sur votre VM Linux T1 permet de résoudre sshd_config comme vous serez toujours connecté et SSH conservera la connexion actif pendant le service SSHD redémarrer.

À partir de T2 exécuter :

##### <a name="on-the-debian-family"></a>Sur la famille Debian

```
username@macbook$ sudo service ssh restart
```

##### <a name="on-the-redhat-family"></a>Sur la famille RedHat

```
username@macbook$ sudo service sshd restart
```

Les mots de passe sont désormais désactivés sur votre ordinateur virtuel protection contre les tentatives d’ouverture de session en force mot de passe.  Avec uniquement la SSH touches autorisée que vous seront en mesure de connecter plus rapide et beaucoup plus sécurisé.
