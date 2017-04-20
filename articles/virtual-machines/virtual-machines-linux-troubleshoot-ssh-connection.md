<properties
    pageTitle="Résoudre les problèmes de connexion SSH pour une machine virtuelle | Microsoft Azure"
    description="Comment résoudre les problèmes tels que « Échoué de la connexion SSH » ou « Connexion SSH refusée » pour une machine virtuelle Azure exécutant Linux."
    keywords="SSH connexion refusée, ssh erreur, azure ssh, SSH Échec de la connexion"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Résoudre les problèmes de connexions SSH vers une machine virtuelle Linux de Azure échoue, erreurs, ou est refusée
Il existe différentes raisons que vous rencontrez des erreurs SSH (Secure Shell), les échecs de connexion SSH ou SSH est refusée lorsque vous tentez de vous connecter à un Linux machine virtuelle (). Cet article vous permet de rechercher et corriger les problèmes. Vous pouvez utiliser le portail Azure, Azure infrastructure du langage commun ou Extension d’accès aux machine virtuelle pour Linux pour résoudre les problèmes de connexion.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Si vous avez besoin d’une aide supplémentaire à tout moment dans cet article, vous pouvez contacter experts Azure dans [les forums de débordement de pile Azure MSDN](http://azure.microsoft.com/support/forums/). Par ailleurs, vous pouvez créer un incident de support Azure. Atteindre le [site de support technique Azure](http://azure.microsoft.com/support/options/) et sélectionnez **obtenir une assistance technique**. Pour plus d’informations sur l’utilisation de Azure prend en charge, lisez le [prend en charge Microsoft Azure Forum aux questions](http://azure.microsoft.com/support/faq/).


## <a name="quick-troubleshooting-steps"></a>Étapes de dépannage rapides
Après chaque étape de résolution des problèmes, essayez de vous reconnecter à la machine virtuelle.

1. Réinitialiser la configuration SSH.
2. Réinitialiser les informations d’identification pour l’utilisateur.
3. Vérifier que les règles de [Groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) autorisent le trafic SSH.
    - Vérifiez l’existence d’une règle de groupe de sécurité de réseau pour autoriser le trafic SSH (par défaut, le port TCP 22).
    - Vous ne pouvez pas utiliser la redirection de port / mappage sans l’aide d’un programme d’équilibrage de charge Azure.
4. Vérifier la [santé des ressources machine virtuelle](../resource-health/resource-health-overview.md). 
    - Assurez-vous que la machine virtuelle signale comme étant correct.
    - Si vous avez diagnostics démarrage activé, vérifiez que la machine virtuelle ne signale des erreurs de démarrage dans les journaux.
5. Redémarrez l’ordinateur virtuel.
6. Redéployez la machine virtuelle.

Poursuivre la lecture des étapes de dépannage plus détaillées et des explications.


## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Méthodes disponibles pour résoudre les problèmes de connexion SSH

Vous pouvez réinitialiser les informations d’identification ou configuration SSH à l’aide d’une des méthodes suivantes :

- [Portail azure](#using-the-azure-portal) - correctement si vous avez besoin pour le réinitialiser rapidement la configuration SSH ou code et que vous n’avez pas installé les outils Azure.
- [Commandes de l’infrastructure du langage commun azure](#using-the-azure-cli) - si vous êtes déjà dans la ligne de commande rapidement réinitialiser la configuration SSH ou les informations d’identification.
- [Extension azure VMAccessForLinux](#using-the-vmaccess-extension) - créer et réutiliser les fichiers de définition de json pour réinitialiser les informations d’identification utilisateur ou de configuration SSH.

Après chaque étape de résolution des problèmes, essayez de vous reconnecter à votre ordinateur virtuel. Si vous ne pouvez toujours pas vous connecter, essayez l’étape suivante.


## <a name="using-the-azure-portal"></a>À l’aide du portail Azure
Le portail Azure fournit une méthode rapide pour rétablir les informations d’identification utilisateur ou de configuration SSH sans installer les outils sur votre ordinateur local.

Sélectionnez votre machine virtuelle dans le portail Azure. Faites défiler jusqu'à la section **prise en charge + résolution des problèmes** et sélectionnez **Réinitialiser votre mot de passe** comme dans l’exemple suivant :

![Réinitialiser configuration SSH ou les informations d’identification dans le portail Azure](./media/virtual-machines-linux-troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>Réinitialiser la configuration SSH
D’abord, sélectionnez `Reset SSH configuration only` dans le menu déroulant **Mode** comme dans la capture d’écran précédente, puis cliquez sur le bouton **Réinitialiser** . Une fois que cette action est terminée, essayez d’accéder à nouveau votre machine virtuelle.

### <a name="reset-ssh-credentials-for-a-user"></a>Réinitialiser les informations d’identification SSH pour un utilisateur
Pour réinitialiser les informations d’identification d’un utilisateur existant, sélectionnez des options `Reset SSH public key` ou `Reset password` dans le menu déroulant **Mode** comme dans la capture d’écran précédent. Spécifier le nom d’utilisateur et un code ou un nouveau mot de passe, puis cliquez sur le bouton **Réinitialiser** .

Vous pouvez également créer un utilisateur avec des privilèges sudo sur l’ordinateur virtuel à partir de ce menu. Entrez un nouveau nom d’utilisateur et mot de passe associé ou de code, puis cliquez sur le bouton **Réinitialiser** .


## <a name="using-the-azure-cli"></a>À l’aide de l’infrastructure du langage commun Azure
Si vous n’avez pas déjà fait, [installez l’infrastructure du langage commun Azure et connectez-vous à votre abonnement Azure](../xplat-cli-install.md). Vérifiez que vous utilisez le mode directeur des ressources comme suit :

```
azure config mode arm
```

Si vous avez créé et que vous avez téléchargée une image de disque Linux personnalisée, vérifiez que l' [Agent de Microsoft Azure Linux](virtual-machines-linux-agent-user-guide.md) version 2.0.5 ou version ultérieure est installé. Pour les machines virtuelles créées à l’aide d’images de la galerie, cette extension access est déjà installée et configurée pour vous.

### <a name="reset-ssh-configuration"></a>Réinitialiser configuration SSH
La configuration SSHD lui-même soit mal ou le service a rencontré une erreur. Vous pouvez réinitialiser SSHD pour vous assurer que la configuration SSH elle-même est correcte. La réinitialisation SSHD doit être la première étape de résolution des problèmes que vous prenez.

L’exemple suivant réinitialise SSHD sur un ordinateur virtuel nommé `myVM` dans le groupe de ressources nommé `myResourceGroup`. Utiliser vos propres noms de groupe machine virtuelle et des ressources comme suit :

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Réinitialiser les informations d’identification SSH pour un utilisateur
Si SSHD semble fonctionner correctement, vous pouvez réinitialiser le mot de passe pour un utilisateur inacceptable. L’exemple suivant redéfinit les informations d’identification pour `myUsername` à la valeur spécifiée dans `myPassword`, sur l’ordinateur virtuel nommé `myVM` dans `myResourceGroup`. Utiliser vos propres valeurs comme suit :

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Si vous utilisez l’authentification par clé SSH, vous pouvez réinitialiser le code SSH pour un utilisateur donné. L’exemple suivant met à jour le code SSH stocké dans `~/.ssh/azure_id_rsa.pub` pour l’utilisateur nommé `myUsername`, sur l’ordinateur virtuel nommé `myVM` dans `myResourceGroup`. Utiliser vos propres valeurs comme suit :

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-file ~/.ssh/azure_id_rsa.pub
```


## <a name="using-the-vmaccess-extension"></a>À l’aide de l’extension VMAccess
L’Extension accès machine virtuelle pour Linux lit un fichier json qui définit les actions à effectuer. Ces actions incluent la réinitialisation SSHD, la réinitialisation d’un code SSH ou en ajoutant un utilisateur. Vous utilisez toujours l’infrastructure du langage commun Azure à appeler l’extension VMAccess, mais vous pouvez réutiliser les fichiers json sur plusieurs ordinateurs virtuels si vous le souhaitez. Cette approche vous permet de créer un référentiel de fichiers json qui peut ensuite être appelée pour donné scénarios.

### <a name="reset-sshd"></a>Réinitialiser SSHD
Créez un fichier nommé `PrivateConf.json` avec le contenu suivant :

```bash
{  
    "reset_ssh":"True"
}
```

À l’aide de l’infrastructure du langage commun Azure, puis appelez la `VMAccessForLinux` extension pour réinitialiser votre connexion SSHD en spécifiant votre fichier json. L’exemple suivant réinitialise SSHD sur l’ordinateur virtuel nommé `myVM` dans `myResourceGroup`. Utiliser vos propres valeurs comme suit :

```bash
azure vm extension set myResourceGroup myVM \
    VMAccessForLinux Microsoft.OSTCExtensions "1.2" \
    --private-config-path PrivateConf.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Réinitialiser les informations d’identification SSH pour un utilisateur
Si SSHD semble fonctionner correctement, vous pouvez réinitialiser les informations d’identification d’un utilisateur inacceptable. Pour réinitialiser le mot de passe d’un utilisateur, créez un fichier nommé `PrivateConf.json`. L’exemple suivant redéfinit les informations d’identification pour `myUsername` à la valeur spécifiée dans `myPassword`. Entrez les lignes suivantes dans votre `PrivateConf.json` fichier, à l’aide de vos propres valeurs :

```bash
{
    "username":"myUsername", "password":"myPassword"
}
```

Ou pour réinitialiser le code SSH d’un utilisateur, commencez par créer un fichier nommé `PrivateConf.json`. L’exemple suivant redéfinit les informations d’identification pour `myUsername` à la valeur spécifiée dans `myPassword`, sur l’ordinateur virtuel nommé `myVM` dans `myResourceGroup`. Entrez les lignes suivantes dans votre `PrivateConf.json` fichier, à l’aide de vos propres valeurs :

```bash
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Après avoir créé votre fichier json, utilisez l’infrastructure du langage commun Azure pour appeler le `VMAccessForLinux` extension pour réinitialiser vos informations d’identification utilisateur SSH en spécifiant votre fichier json. L’exemple suivant redéfinit les informations d’identification sur l’ordinateur virtuel nommé `myVM` dans `myResourceGroup`. Utiliser vos propres valeurs comme suit :

```
azure vm extension set myResourceGroup myVM \
    VMAccessForLinux Microsoft.OSTCExtensions "1.2" \
    --private-config-path PrivateConf.json
```


## <a name="restart-a-vm"></a>Redémarrer une machine virtuelle
Si vous avez réinitialiser les informations d’identification utilisateur et de configuration SSH ou a rencontré une erreur de cette manière, essayez de redémarrer la machine virtuelle vers l’adresse sous-jacent cluster problèmes.

### <a name="azure-portal"></a>Portail Azure
Pour reprendre une machine virtuelle à l’aide du portail Azure, sélectionnez votre machine virtuelle, puis cliquez sur la * bouton**redémarrer** comme dans l’exemple suivant :

![Redémarrer une machine virtuelle dans le portail Azure](./media/virtual-machines-linux-troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Azure infrastructure du langage commun
L’exemple suivant redémarre la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup`. Utiliser vos propres valeurs comme suit :

```bash
azure vm restart --resource-group myResourceGroup --name myVM
```


## <a name="redeploy-a-vm"></a>Redéployez une machine virtuelle
Vous pouvez redéployez une machine virtuelle vers un autre nœud dans Azure, qui peut corriger les problèmes de mise en réseau sous-jacente. Pour plus d’informations sur les redéployez une machine virtuelle, voir [redéployez machine virtuelle au nouveau nœud Azure](virtual-machines-windows-redeploy-to-new-node.md).

> [AZURE.NOTE] Une fois cette opération terminée, les données de disque éphémères seront perdues et adresses IP dynamiques qui sont associées à la machine virtuelle seront mise à jour.

### <a name="azure-portal"></a>Portail Azure
Pour redéployez une machine virtuelle à l’aide du portail Azure, sélectionnez votre machine virtuelle, puis accédez à la section **prise en charge + résolution des problèmes** . Cliquez sur le bouton **redéployez** comme dans l’exemple suivant :

![Redéployez une machine virtuelle dans le portail Azure](./media/virtual-machines-linux-troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Azure infrastructure du langage commun
L’exemple suivant redéploie la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup`. Utiliser vos propres valeurs comme suit :

```bash
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>Machines virtuelles créés à l’aide du modèle de déploiement classique

Essayez les étapes suivantes pour résoudre les échecs de connexion SSH plus courantes pour les machines virtuelles créées à l’aide du modèle de déploiement classique. Après chaque étape, essayez de vous reconnecter à la machine virtuelle.

- Réinitialiser l’accès à distance à partir du [portail Azure](https://portal.azure.com). Dans le portail Azure, sélectionnez votre machine virtuelle et cliquez sur le bouton **Réinitialiser à distance...** .

- Redémarrez l’ordinateur virtuel. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre machine virtuelle, puis cliquez sur le bouton **redémarrer** .

    -OU-

    Dans le [portail classique Azure](https://manage.windowsazure.com), sélectionnez **machines virtuelles** > **Instances** > **redémarrer**.

- Redéployez la machine virtuelle vers un nouveau nœud Azure. Pour plus d’informations sur la façon redéployez une machine virtuelle, voir [redéployez machine virtuelle au nouveau nœud Azure](virtual-machines-windows-redeploy-to-new-node.md).

    Une fois cette opération terminée, les données de disque éphémères seront perdues et adresses IP dynamiques qui sont associées à la machine virtuelle seront mise à jour.

- Suivez les instructions de [comment réinitialiser un mot de passe ou SSH pour machines virtuelles basées sur Linux](virtual-machines-linux-classic-reset-access.md) :
    - Réinitialiser le mot de passe ou le code.
    - Créer un compte d’utilisateur _sudo_ .
    - Réinitialiser la configuration SSH.

- Vérifier l’intégrité de ressources de la machine virtuelle pour des problèmes de plateforme.<br>
  Sélectionnez votre machine virtuelle et défiler l’écran **paramètres** > **Vérifier l’intégrité**.


## <a name="additional-resources"></a>Ressources supplémentaires

- Si vous ne parvenez toujours pas à SSH de votre ordinateur virtuel après avoir suivi les étapes après, voir [plus détaillée des étapes de dépannage](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md) pour consulter des étapes supplémentaires pour résoudre le problème.

- Pour plus d’informations sur la résolution des problèmes d’accès aux applications, voir [résoudre les problèmes accéder à l’application en cours d’exécution sur un ordinateur virtuel Azure](virtual-machines-linux-troubleshoot-app-connection.md)

- Pour plus d’informations sur la résolution des machines virtuelles créées à l’aide du modèle de déploiement classique, voir [comment réinitialiser un mot de passe ou SSH pour machines virtuelles basées sur Linux](virtual-machines-linux-classic-reset-access.md).
