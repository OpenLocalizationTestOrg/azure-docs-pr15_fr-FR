<properties
    pageTitle="Créer des clés de SSH sur Linux et Mac | Microsoft Azure"
    description="Générer et utiliser des clés SSH sur Linux et Mac pour le Gestionnaire de ressources et les modèles de déploiement classique sur Azure."
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
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="v-livech"/>

# <a name="create-ssh-keys-on-linux-and-mac-for-linux-vms-in-azure"></a>Créer des clés SSH sur Linux et Mac pour les machines virtuelles Linux dans Azure

Avec une paire de clés SSH, vous pouvez créer des Machines virtuelles sur Azure qui par défaut à l’aide des touches SSH pour l’authentification, supprimer la nécessité de mots de passe pour vous connecter.  Les mots de passe peuvent être à deviner et ouvrez vos ordinateurs virtuels jusqu'à tentatives en force constante à deviner votre mot de passe. Machines virtuelles créés avec des modèles Azure ou les `azure-cli` peut inclure votre clé publique SSH dans le cadre du déploiement, suppression d’une configuration de déploiement billet.  Si vous vous connectez à une VM Linux à partir de Windows, voir [ce document.](virtual-machines-linux-ssh-from-windows.md)

L’article nécessite :

- un compte Azure ([Obtenez un essai gratuit](https://azure.microsoft.com/pricing/free-trial/)).

- l' [Infrastructure du langage commun Azure](../xplat-cli-install.md) connecté à l’aide`azure login`

- le mode de gestionnaire de ressources Azure Azure infrastructure du langage commun _doit être placé dans_`azure config mode arm`

## <a name="quick-commands"></a>Commandes rapides

Dans les commandes suivantes, remplacez les exemples par vos propres choix.

SSH clés par défaut sont conservé dans le `.ssh` répertoire.  

```bash
cd ~/.ssh/
```

Si vous n’avez pas une `~/.ssh` répertoire la `ssh-keygen` commande créera vous disposant des autorisations appropriées.

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
```

Entrez le nom du fichier est enregistré dans le `~/.ssh/` répertoire :

```bash
id_rsa
```

Entrez le mot de passe pour id_rsa :

```bash
correct horse battery staple
```

Il existe désormais un `id_rsa` et `id_rsa.pub` paire de clés SSH dans le `~/.ssh` répertoire.

```bash
ls -al ~/.ssh
```

Ajoutez la clé nouvellement créée à `ssh-agent` sous Linux et Mac (également ajouté au OSX trousseau) :

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

Copiez la clé publique SSH à votre serveur Linux :

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub myusername@myserver
```

Tester la connexion à l’aide des touches au lieu d’un mot de passe :

```bash
ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/id_rsa myusername@myserver
Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
$
```

## <a name="detailed-walkthrough"></a>Procédure pas à pas détaillé

L’utilisation de clés publiques et privées SSH est le moyen le plus simple pour vous connecter à vos serveurs Linux. [Chiffrement de clé publique](https://en.wikipedia.org/wiki/Public-key_cryptography) fournit une manière beaucoup plus sûre pour vous connecter à votre Linux ou BSD VM dans Azure que les mots de passe, qui peuvent être forcé brute beaucoup plus facilement. Votre clé publique peut être partagée avec tout le monde ; mais seulement vous (ou votre infrastructure de sécurité locale) posséder votre clé privée.  La clé privée SSH doit avoir un [mot de passe très sécurisé](https://www.xkcd.com/936/) (source :[xkcd.com](https://xkcd.com)) à protéger.  Ce mot de passe est simplement pour accéder à la clé SSH privée et **n’est pas** le mot de passe du compte utilisateur.  Lorsque vous ajoutez un mot de passe à votre clé SSH, il chiffre la clé privée afin que la clé privée est inutilisable sans le mot de passe pour la déverrouiller.  Si un pirate a volé votre clé privée et que cette touche n’a pas d’un mot de passe, ils pourront utiliser cette clé privée pour vous connecter aux serveurs qui ont la clé publique correspondante.  Si une clé privée est protégée mot de passe ne peuvent pas être utilisés par ce pirate, fournissant un niveau de sécurité supplémentaire pour votre infrastructure sur Azure.

Cet article crée des fichiers de clé *ssh rsa* mis en forme, qui sont recommandés pour les déploiements sur le Gestionnaire de ressources.  clés *SSH rsa* sont requis dans le [portail](https://portal.azure.com) pour les déploiements classique et Gestionnaire de ressources.

## <a name="create-the-ssh-keys"></a>Créer les clés SSH

Ssh rsa Azure nécessite au moins 2048 bits, mettre en forme les clés publiques et privées. Pour créer l’utilisation de touches `ssh-keygen`, qui vous invite à fournir une série de questions, puis écrit une clé privée et une clé publique correspondante. Lorsqu’une machine virtuelle Azure est créée, la clé publique est copiée sur `~/.ssh/authorized_keys`.  Touches SSH dans `~/.ssh/authorized_keys` sont utilisées pour le client pour correspondre à la clé privée correspondante sur une connexion SSH un défi.

## <a name="using-ssh-keygen"></a>À l’aide de ssh keygen

Cette commande crée un mot de passe sécurisé (chiffrés) SSH paire de clés à l’aide de 2048 bits RSA et il est commenté pour l’identifier facilement.  

Commencez par modifier les répertoires, afin que tous les vos clés ssh sont créées dans ce répertoire.

```bash
cd ~/.ssh
```

Si vous n’avez pas une `~/.ssh` répertoire la `ssh-keygen` commande créera vous disposant des autorisations appropriées.

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
```

_Commande flux de travail expliquée_

`ssh-keygen`= le programme utilisé pour créer les clés

`-t rsa`= type de clé créer la [RSA format](https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048`= bits de la clé

`-C "myusername@myserver"`= un commentaire ajouté à la fin du fichier de clé publique pour l’identifier facilement.  En règle générale, un message électronique est utilisé comme le commentaire, mais vous pouvez utiliser ce qui convient le mieux pour votre infrastructure.

### <a name="using-pem-keys"></a>L’aide des touches PEM

Si vous utilisez le classique déployez modèle (portail classique Azure ou l’infrastructure du langage commun Azure Service gestion `asm`), vous devrez peut-être utiliser PEM mis en forme de touches SSH pour accéder à vos ordinateurs virtuels Linux.  Voici comment procéder pour créer une clé PEM à partir d’une SSH clé publique existante et un x509 existant certificat.

Pour créer un PEM mis en forme de clé à partir d’une clé publique SSH existante :

```bash
ssh-keygen -f ~/.ssh/id_rsa.pub -e > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>Exemple de ssh keygen

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/myusername/.ssh/id_rsa): id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in id_rsa.
Your public key has been saved in id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 myusername@myserver
The key's randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

Enregistrer les fichiers de clé :

`Enter file in which to save the key (/home/myusername/.ssh/id_rsa): id_rsa`

Le nom de la paire de clés de cet article.  Avoir une paire de clés nommée **id_rsa** est la valeur par défaut et certains outils attendiez le nom de fichier de clé privée **id_rsa** comportant une est recommandé. Le répertoire `~/.ssh/` est l’emplacement par défaut pour les paires de clés SSH et le fichier de configuration de la SSH.

```bash
ls -al ~/.ssh
-rw------- 1 myusername staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 myusername staff   410 Aug 25 18:04 rsa.pub
```
De manière significative les `~/.ssh` répertoire. `ssh-keygen`crée le `~/.ssh` répertoire si elle n’est pas présente et également définit les modes de la propriété et de fichier corrects.

Ce mot de passe :

`Enter passphrase (empty for no passphrase):`

`ssh-keygen`fait référence à un mot de passe en tant que « mot de passe. »  Il est *fortement* recommandé d’ajouter un mot de passe à votre paires de clés. Sans un mot de passe protégeant la paire de clés, toute personne disposant du fichier de clé privée s’en servir pour vous connecter à n’importe quel serveur qui comporte la clé publique correspondante. Ajout d’un mot de passe offre une protection plus en cas d’une personne en mesure d’accéder à votre fichier de clé privée, vous donné temps pour modifier les clés utilisée pour vous authentifier.

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>À l’aide de ssh agent pour stocker votre mot de passe de clé privée

Pour éviter de taper votre mot de passe du fichier de clé privée avec chaque connexion SSH, vous pouvez utiliser `ssh-agent` pour mettre en cache votre mot de passe du fichier de clé privée. Si vous utilisez un Mac, le trousseau OSX stocke en toute sécurité les mots de passe de clé privées lorsque vous appelez `ssh-agent`.

Vérifiez d’abord que `ssh-agent` est en cours d’exécution

```bash
eval "$(ssh-agent -s)"
```

Ajoutez maintenant la clé privée à `ssh-agent` à l’aide de la commande `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Le mot de passe de clé privée est maintenant stockée dans `ssh-agent`.

## <a name="create-and-configure-an-ssh-config-file"></a>Créer et configurer un fichier de configuration SSH

Il est recommandé pour créer et configurer un `~/.ssh/config` fichier pour accélérer la connexion et pour l’optimisation de votre comportement client SSH.

L’exemple suivant montre une configuration standard.

### <a name="create-the-file"></a>Créer le fichier

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Modifier le fichier pour ajouter la nouvelle configuration SSH :

```bash
vim ~/.ssh/config
```

### <a name="example-sshconfig-file"></a>Exemple `~/.ssh/config` fichier :

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User myusername
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

Cette configuration SSH vous donne des sections pour chaque serveur permettre à chaque à sa propre paire de clés dédié. Les paramètres par défaut (`Host *`) sont pour tous les hôtes qui ne correspondent pas à des hôtes spécifiques plus haut dans le fichier de configuration.


### <a name="config-file-explained"></a>Fichier de configuration du flux de travail expliquée

`Host`= le nom de l’hôte appelé sur le terminal.  `ssh fedora22`indique `SSH` d’utiliser les valeurs dans le bloc de paramètres intitulée `Host fedora22` Remarque : Ceci peut être n’importe quelle étiquette qui est logique pour votre utilisation et ne représente pas le nom d’hôte réel d’un serveur.

`Hostname 102.160.203.241`= l’adresse IP ou le nom DNS pour le serveur en cours d’accès.

`User myusername`= le compte d’utilisateur à distance à utiliser lors de la connexion au serveur.

`PubKeyAuthentication yes`= informe SSH vous souhaitez utiliser un code SSH pour vous connecter.

`IdentityFile /home/myusername/.ssh/id_id_rsa`= la clé privée SSH et clé publique à utiliser pour l’authentification.


## <a name="ssh-into-linux-without-a-password"></a>SSH dans Linux sans mot de passe

À présent que vous avez une paire de clés SSH et un fichier de configuration de la SSH configuré, vous ne pouvez pour vous connecter à votre Linux VM rapidement et en toute sécurité. La première fois que vous se connecter à un serveur à l’aide d’un code SSH les invites de commande vous pour le mot de passe pour ce fichier de clé.

```bash
ssh fedora22
```

### <a name="command-explained"></a>Commande flux de travail expliquée

Lorsque `ssh fedora22` est exécutée SSH tout d’abord localise et charge tous les paramètres de la `Host fedora22` bloc, puis charge tous les paramètres restants à partir du dernier bloc, `Host *`.

## <a name="next-steps"></a>Étapes suivantes

Distance suivante consiste à créer des machines virtuelles du Linux Azure à l’aide de la nouvelle clé publique SSH.  Machines virtuelles Azure créés avec une clé publique SSH sous la connexion sont mieux sécurisées machines virtuelles créés avec la méthode de connexion par défaut, les mots de passe.  Machines virtuelles Azure créés à l’aide des touches SSH sont par défaut configuré avec les mots de passe désactivés, pour éviter d’obtenir forcé brute déchiffrement tentatives.

- [Créer une VM Linux sécurisé à l’aide d’un modèle d’Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Créer une VM Linux sécurisé à l’aide du portail Azure](virtual-machines-linux-quick-create-portal.md)
- [Créer une VM Linux sécurisé à l’aide de l’infrastructure du langage commun Azure](virtual-machines-linux-quick-create-cli.md)
