<properties 
    pageTitle="Utilisez les touches de SSH avec Windows pour les machines virtuelles Linux | Microsoft Azure" 
    description="Apprenez à générer et utilisez les touches de SSH sur un ordinateur Windows pour vous connecter à une machine virtuelle Linux sur Azure." 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="squillace" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="rasquill"/>

# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Comment utiliser SSH touches avec Windows sur Azure

> [AZURE.SELECTOR]
- [Windows](virtual-machines-linux-ssh-from-windows.md)
- [Linux/Mac](virtual-machines-linux-mac-create-ssh-keys.md)

Lorsque vous vous connectez Linux des machines virtuelles (machines virtuelles) dans Azure, vous devez utiliser le [chiffrement à clé publique](https://wikipedia.org/wiki/Public-key_cryptography) pour permettre de manière plus sécurisée pour vous connecter à votre VM Linux. Ce processus implique un échange de clé privée et l’utilisation de la commande SSH (secure shell) pour authentifier vous-même au lieu d’un nom d’utilisateur et votre mot de passe. Les mots de passe sont concernés par force brute attaques, en particulier sur machines virtuelles via Internet tels que des serveurs web. Cet article fournit une vue d’ensemble des touches SSH et comment générer les clés appropriées sur un ordinateur Windows.


## <a name="overview-of-ssh-and-keys"></a>Vue d’ensemble des SSH et clés

Vous pouvez en toute sécurité vous connecter à votre Linux VM à l’aide de clés publiques et privées :

- La **clé publique** est placée dans votre Linux VM ou tout autre service que vous souhaitez utiliser avec chiffrement à clé publique.
- La **clé privée** est ce que vous présentez à votre Linux VM lorsque vous vous connectez, pour vérifier votre identité. Protéger cette clé privée. Ne pas partager celui-ci.

Ces clés publiques et privées utilisés sur plusieurs ordinateurs virtuels et services. Vous n’avez pas besoin d’une paire de clés pour chaque ordinateur virtuel ou le service que vous souhaitez accéder. Pour obtenir une présentation plus détaillée, voir [chiffrement par clé publique](https://wikipedia.org/wiki/Public-key_cryptography).

SSH est un protocole de connexion chiffrée suivant qui autorise les connexions sécurisées via des connexions non sécurisées. C’est le protocole de connexion par défaut pour les machines virtuelles Linux hébergé dans Azure. Bien que SSH lui-même fournit une connexion chiffrée suivant, l’utilisation de mots de passe avec les connexions SSH toujours expose la machine virtuelle à force brute attaques ou deviner des mots de passe. Une méthode plus sécurisée et par défaut, de se connecter à une machine virtuelle à l’aide de SSH est à l’aide de ces clés publiques et privées, également connu sous le nom des touches SSH.

Si vous ne souhaitez pas utiliser les touches SSH, vous pouvez toujours se connecter à vos ordinateurs virtuels Linux à l’aide d’un mot de passe. Si votre ordinateur virtuel n’est pas visible sur Internet, à l’aide de mots de passe peut être suffisante. Toutefois, vous avez besoin gérer vos mots de passe pour chaque VM Linux et maintenir des stratégies de mot de passe correct et pratiques, telles que la longueur minimale et les mettre à jour régulièrement. L’utilisation de clés SSH réduit la complexité de la gestion des informations d’identification individuelles sur plusieurs ordinateurs virtuels.


## <a name="windows-packages-and-ssh-clients"></a>Les packages Windows et clients SSH

Vous connecter à et gérer des ordinateurs virtuels Linux dans Azure à l’aide d’un client **ssh** . Ordinateurs Windows ne disposent pas généralement un **ssh** client est installé. Communs Windows SSH clients, vous pouvez installer sont inclus dans les packages suivants :

- [GIT pour Windows](https://git-for-windows.github.io/)
- [puTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
- [MobaXterm](http://mobaxterm.mobatek.net/)
- [Cygwin](https://cygwin.com/)

> [AZURE.NOTE] Dernière mise à jour de l’anniversaire de mariage pour Windows 10 inclut Bash pour Windows. Cette fonctionnalité permet d’exécuter le sous-système Windows pour utilitaires Linux et accès comme un clientSSH. Bash pour Windows est toujours en cours de développement et est considérée comme une version bêta. Pour plus d’informations sur Bash pour Windows, voir [Bash sur Ubuntu sous Windows](https://msdn.microsoft.com/commandline/wsl/about).


## <a name="which-key-files-do-you-need-to-create"></a>Quels fichiers clés devez-vous créer ?

**Ssh rsa** Azure nécessite au moins 2048 bits, mettre en forme les clés publiques et privées. Si vous gérez les ressources Azure à l’aide du modèle de déploiement classique, vous devez également générer un PEM (`.pem` fichier).

Voici les scénarios de déploiement et les types de fichiers que vous utilisez dans chaque :

1. **SSH rsa** clés sont requises pour un déploiement à l’aide du [portail Azure](https://portal.azure.com)et les déploiements du Gestionnaire de ressources à l’aide de l' [Infrastructure du langage commun Azure](../xplat-cli-install.md).
    - Ces touches sont généralement la plupart des tous les utilisateurs ont besoin.
2. `.pem`fichier est requis pour créer des machines virtuelles à l’aide du [portail classique](https://manage.windowsazure.com). Ces touches sont également pris en charge dans les déploiements classique qui utilisent l' [Infrastructure du langage commun Azure](../xplat-cli-install.md).
    - Vous devez uniquement créer ces touches supplémentaires, les certificats si vous gérez les ressources créées à l’aide du modèle de déploiement classique.


## <a name="install-git-for-windows"></a>Installer Git pour Windows

La section précédente répertorié plusieurs packages qui incluent le `openssl` outil pour Windows. Cet outil est nécessaire pour créer des clés publiques et privées. Les exemples suivants expliquent comment installer et utiliser **Git pour Windows**, bien que vous pouvez choisir n’importe quel package vous préférez. **GIT pour Windows** permet d’accéder à certains outils supplémentaires logiciels libres ([OSS](https://en.wikipedia.org/wiki/Open-source_software)) et utilitaires qui peuvent vous être utiles lorsque vous travaillez avec machines virtuelles Linux.

1. Téléchargez et installez **Git pour Windows** à l’emplacement suivant : [https://git-for-windows.github.io/](https://git-for-windows.github.io/).

2. Acceptez les options par défaut pendant le processus d’installation, sauf si vous avez besoin de les modifier.

3. Exécuter **Git Bash** à partir du **Menu Démarrer**de > **Git** > **Git Bash**. La console est semblable à l’exemple suivant :

    ![GIT pour Windows Bash shell](./media/virtual-machines-linux-ssh-from-windows/git-bash-window.png)


## <a name="create-a-private-key"></a>Créer une clé privée

1. Dans la fenêtre **Git Bash** , utilisez `openssl.exe` pour créer une clé privée. L’exemple suivant crée une clé nommée `myPrivateKey` et certificat nommé `myCert.pem`:

    ```bash
    openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout myPrivateKey.key -out myCert.pem
    ```

    Le résultat est semblable à l’exemple suivant :

    ```bash
    Generating a 2048 bit RSA private key
    .......................................+++
    .......................+++
    writing new private key to 'myPrivateKey.key'
    -----
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:
    ```

2. Répondez aux invites pour nom du pays, emplacement, nom de l’organisation, etc..

3. Votre nouvelle clé privée et le certificat sont créés dans votre répertoire de travail en cours. Meilleures pratiques de sécurité, vous devez définir les autorisations sur votre clé privée afin que seulement vous pouvez y accéder :

    ```bash
    chmod 0600 myPrivateKey
    ```

4. Si vous devez également gérer les ressources classique, convertir le `myCert.pem` à `myCert.cer` (X509 au format DER certificat). Effectuer cette étape facultative uniquement si vous avez besoin gérer plus précisément les plus anciens ressources classique. 

    Convertir le certificat à l’aide de la commande suivante :

    ```bash
    openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
    ```

## <a name="create-a-private-key-for-putty"></a>Créer une clé privée pour PuTTY

PuTTY est un clientSSH courantes pour Windows. Vous pouvez utiliser n’importe quel client SSH que vous souhaitez. Pour utiliser PuTTY, vous devez créer un autre type de clé - une clé privée PuTTY (PPK). Si vous ne souhaitez pas utiliser PuTTY, ignorez cette section.

L’exemple suivant crée cette clé privée supplémentaire spécialement pour PuTTY à utiliser :

1. Utilisez **Git Bash** pour convertir votre clé privée en une clé privée RSA que PuTTYgen peut comprendre. L’exemple suivant crée une clé nommée `myPrivateKey_rsa` à partir de la clé existante nommée `myPrivateKey`:

    ```bash
    openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
    ```

    Meilleures pratiques de sécurité, vous devez définir les autorisations sur votre clé privée afin que seulement vous pouvez y accéder :

    ```bash
    chmod 0600 myPrivateKey_rsa
    ```

2. Télécharger et exécuter PuTTYgen à partir de l’emplacement suivant : [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

3. Cliquez sur le menu : **fichier** > **charge une clé privée**

4. Recherchez votre clé privée (`myPrivateKey_rsa` dans l’exemple précédent). Le répertoire par défaut lorsque vous démarrez **Git Bash** est `C:\Users\%username%`. Modifier le filtre de fichiers pour afficher **tous les fichiers (\*.\*)**:

    ![Charger la clé privée existante dans PuTTYgen](./media/virtual-machines-linux-ssh-from-windows/load-private-key.png)

5. Cliquez sur **Ouvrir**. Un message indique que la clé a été importée avec succès :

    ![Touche importé avec succès pour PuTTYgen](./media/virtual-machines-linux-ssh-from-windows/successfully-imported-key.png)

6. Cliquez sur **OK** pour fermer l’invite.

7. La clé publique est affichée en haut de la fenêtre **PuTTYgen** . Vous copiez et collez cette clé publique dans le portail Azure ou modèle Azure le Gestionnaire de ressources lorsque vous créez un VM Linux. Vous pouvez également cliquer sur **Enregistrer la clé publique** pour enregistrer une copie sur votre ordinateur :

    ![Enregistrer le fichier de clé publique mastic](./media/virtual-machines-linux-ssh-from-windows/save-public-key.png)

    L’exemple suivant montre comment vous souhaitez copier et coller cette clé publique dans le portail Azure lorsque vous créez un VM Linux. La clé publique est généralement ensuite stockée dans `~/.ssh/authorized_keys` sur votre nouvelle machine virtuelle.

    ![Utiliser une clé publique lorsque vous créez une machine virtuelle dans le portail Azure](./media/virtual-machines-linux-ssh-from-windows/use-public-key-azure-portal.png)

7. Dans **PuTTYgen**, cliquez sur **Enregistrer la clé privée**:

    ![Enregistrer un fichier de clé privée PuTTY](./media/virtual-machines-linux-ssh-from-windows/save-ppk-file.png)

    > [AZURE.WARNING] Une invite vous demande si vous voulez continuer sans entrer un mot de passe pour votre clé. Un mot de passe est un mot de passe associé à votre clé privée. Même si une personne ont été pour obtenir votre clé privée, ils toujours pas serait pas s’authentifier à l’aide d’uniquement la clé. Ils doivent également le mot de passe. Sans un mot de passe, si une personne obtient votre clé privée, ils peuvent se connecter à tous les services qui utilise cette touche ou machine virtuelle. Nous vous recommandons de que vous créez un mot de passe. Toutefois, si vous avez oublié le mot de passe, il est inutile de le récupérer.

    Si vous souhaitez entrer un mot de passe, cliquez sur **non**, entrez un mot de passe dans la fenêtre principale de PuTTYgen, puis à nouveau sur **Enregistrer la clé privée** . Dans le cas contraire, cliquez sur **Oui** pour continuer sans fournir le mot de passe facultatif.

8. Entrez un nom et un emplacement pour enregistrer votre fichier PPK.


## <a name="use-putty-to-ssh-to-a-linux-machine"></a>Utiliser Putty à SSH pour une Machine Linux

Là encore, PuTTY est un clientSSH courantes pour Windows. Vous pouvez utiliser n’importe quel client SSH que vous souhaitez. Les étapes suivantes expliquent comment utiliser votre clé privée pour vous authentifier avec votre ordinateur virtuel Azure à l’aide de SSH. Les étapes sont similaires dans d’autres clients clés SSH en termes d’avoir à charger votre clé privée pour authentifier la connexion SSH.

1. Télécharger et exécuter putty à partir de l’emplacement suivant : [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

2. Renseignez le nom d’hôte ou l’adresse IP de votre machine virtuelle à partir du portail Azure :

    ![Ouverture d’une connexion mastic nouveau](./media/virtual-machines-linux-ssh-from-windows/putty-new-connection.png)

3. Avant de sélectionner **ouvert**, cliquez sur **la connexion** > **SSH** > onglet**Auth** . Recherchez et sélectionnez votre clé privée :

    ![Sélectionnez votre clé privée PuTTY pour l’authentification](./media/virtual-machines-linux-ssh-from-windows/putty-auth-dialog.png)

4. Cliquez sur **Ouvrir** pour vous connecter à votre machine virtuelle
 

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez également générer les clés privée et [à l’aide du système d’exploitation X et Linux](virtual-machines-linux-mac-create-ssh-keys.md).

Pour plus d’informations sur Bash pour Windows et les avantages d’outils OSS disponibles sur votre ordinateur Windows, voir [Bash sur Ubuntu sous Windows](https://msdn.microsoft.com/commandline/wsl/about).

Si vous rencontrez des problèmes à l’aide de SSH pour vous connecter à vos ordinateurs virtuels Linux, voir [résoudre les connexions SSH pour une machine virtuelle Linux Azure](virtual-machines-linux-troubleshoot-ssh-connection.md).