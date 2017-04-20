<properties
   pageTitle="Chiffrer disques sur un Linux VM | Microsoft Azure"
   description="Comment chiffrer des disques sur un VM Linux à l’aide de l’infrastructure du langage commun Azure et le modèle de déploiement du Gestionnaire de ressources"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/11/2016"
   ms.author="iainfou"/>

# <a name="encrypt-disks-on-a-linux-vm-using-the-azure-cli"></a>Chiffrer disques sur un VM Linux à l’aide de l’infrastructure du langage commun Azure
Pour la sécurité renforcée machine virtuelle () et la conformité, disques virtuels dans Azure peuvent être chiffrées inactives. Disques sont chiffrés à l’aide de clés de chiffrement qui sont sécurisées dans un archivage sécurisé de clé Azure. Vous contrôler ces clés de chiffrement et que vous pouvez auditer leur utilisation. Cet article explique comment chiffrer des disques virtuels sur un VM Linux à l’aide de l’infrastructure du langage commun Azure et le modèle de déploiement du Gestionnaire de ressources.


## <a name="quick-commands"></a>Commandes rapides
Si vous devez effectuer rapidement la tâche, les détails de la section suivante de la base de commandes pour chiffrer disques virtuels sur votre ordinateur virtuel. Plus d’informations et le contexte de chaque étape vous pouvez trouver le reste du document, en [commençant ici](#overview-of-disk-encryption).

Vous devez la [Dernière Azure infrastructure du langage commun](../xplat-cli-install.md) installé et connecté à l’aide du mode directeur des ressources comme suit :

```
azure config mode arm
```

Dans les exemples suivants, remplacez les noms de paramètres exemple avec vos propres valeurs. Incluent les noms de paramètres exemple `myResourceGroup`, `myKeyVault`, et `myVM`.

Tout d’abord, activez le fournisseur de l’archivage sécurisé de clé Azure au sein de votre abonnement Azure et créez un groupe de ressources. L’exemple suivant crée un nom de groupe de ressources `myResourceGroup` dans les `WestUS` emplacement :

```bash
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Créer un archivage sécurisé clé Azure. L’exemple suivant crée un archivage sécurisé clé nommé `myKeyVault`:

```bash
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Créer une clé de chiffrement dans votre l’archivage sécurisé clé et activez-le pour le chiffrement des disques. L’exemple suivant crée une clé nommée `myKey`:

```bash
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```

Créer un point de terminaison à l’aide d’Azure Active Directory pour gérer l’authentification et l’échange de clés de chiffrement de clé de l’archivage sécurisé. La `--home-page` et `--identifier-uris` n’avez pas besoin adresse prenant réelle. Le plus haut niveau de sécurité, secrets du client doivent être utilisés à la place de mots de passe. L’infrastructure du langage commun Azure ne peut pas actuellement générer secrets du client. Secrets du client ne peuvent être générés dans le portail Azure. L’exemple suivant crée un point de terminaison Azure Active Directory nommé `myAADApp` et utilise un mot de passe de `myPassword`. Spécifiez votre propre mot de passe comme suit :

```bash
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Remarque la `applicationId` figurant dans le résultat de la commande précédente. ID de l’application est utilisé dans les étapes suivantes :

```bash
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```

Ajout d’un disque de données pour une machine virtuelle existante. L’exemple suivant ajoute un disque de données pour une machine virtuelle nommée `myVM`:

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Passez en revue les détails de votre l’archivage sécurisé clé et la clé que vous avez créé. Vous devez l’ID de l’archivage sécurisé clé, URI et clé URL dans la dernière étape. L’exemple suivant passe en revue les détails pour un archivage sécurisé clé nommé `myKeyVault` et clé nommée `myKey`:

```bash
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Chiffrer vos disques comme suit, entrer vos propres noms de paramètres dans l’ensemble :

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

L’infrastructure du langage commun Azure ne fournissent des erreurs détaillées au cours du processus de chiffrement. Pour plus d’informations, consultez `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log`. Tandis que la commande précédente comporte plusieurs variables et vous pouvez obtenir pas quantité indique pourquoi le processus échoue, un exemple de commande complète se présente comme suit :

```bash
azure vm enable-disk-encryption -g myResourceGroup -n MyVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \ 
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Pour finir, passez en revue l’état de chiffrement à nouveau pour confirmer que vos disques virtuels ont désormais été chiffrés. L’exemple suivant vérifie l’état d’un ordinateur virtuel nommé `myVM` dans les `myResourceGroup` groupe de ressources :

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```

## <a name="overview-of-disk-encryption"></a>Vue d’ensemble du chiffrement de disque
Disques virtuels sur machines virtuelles Linux sont chiffrés inactives à l’aide de [dm crypt](https://wikipedia.org/wiki/Dm-crypt). Il est gratuit pour le chiffrement des disques virtuels dans Azure. Clés de chiffrement sont stockées dans l’archivage sécurisé de clé Azure à l’aide de la protection du logiciel, ou vous pouvez importer ou générer vos clés dans les Modules de sécurité matérielle (HSM) certifiés FIPS normes de niveau 2 140-2. Vous gardez le contrôle de ces clés de chiffrement et que vous pouvez auditer leur utilisation. Ces clés de chiffrement sont utilisés pour chiffrer et déchiffrer disques virtuels connectés à votre ordinateur virtuel. Un point de terminaison Azure Active Directory fournit un mécanisme sécurisé pour émettre ces touches cryptographiques comme machines virtuelles sont sous tension et désactiver.

Le processus de chiffrement une machine virtuelle est la suivante :

1. Créer une clé de chiffrement dans un archivage sécurisé de clé Azure.
2. Configurer la clé de chiffrement pour pouvoir l’utiliser pour le chiffrement des disques.
3. Pour lire la clé de chiffrement de l’archivage sécurisé de clé Azure, créez un point de terminaison à l’aide d’Azure Active Directory avec les autorisations appropriées.
4. Exécutez la commande pour chiffrer vos disques virtuels, spécifiant le point de terminaison Azure Active Directory et de la clé de chiffrement approprié à utiliser.
5. Le point de terminaison Azure Active Directory demande la clé de chiffrement requise de l’archivage sécurisé de clé Azure.
6. Les disques virtuels sont chiffrés à l’aide de la clé de chiffrement fournie.


## <a name="supporting-services-and-encryption-process"></a>Services d’assistance et processus de chiffrement
Chiffrement de disque repose sur les composants supplémentaires suivants :

- **L’archivage sécurisé de clé azure** - permet de protéger les clés de chiffrement et secrets utilisés pour le processus de chiffrement/déchiffrement disque. 
  - S’il en existe, vous pouvez utiliser un archivage sécurisé de clé Azure existant. Vous n’avez pas à dédier un archivage sécurisé clé pour le chiffrement des disques.
  - Pour séparer les limites administratives et la visibilité clée, vous pouvez créer un archivage sécurisé clé dédié.
- **Azure Active Directory** - gère l’échange sécurisé de clés de chiffrement requis et l’authentification pour les actions demandées. 
  - Vous pouvez généralement utiliser une instance Azure Active Directory existante pour héberger votre application. 
  - L’application est plus d’un point de terminaison pour les services de l’archivage sécurisé clé et Machine virtuelle demander et obtenir émis les clés de chiffrement appropriés. Vous développez pas une application qui s’intègre à Azure Active Directory.


## <a name="requirements-and-limitations"></a>Configuration requise et limitations
Scénarios pris en charge et configuration requise pour le chiffrement des disques :

- Le Linux serveur suivant références SKU - Ubuntu CentOS, SUSE et SUSE Linux Enterprise Server (SLES) et rouge chapeau Enterprise Linux.
- Toutes les ressources (par exemple, l’archivage sécurisé clé, un compte de stockage et machine virtuelle) doivent être placé dans la même région Azure et l’abonnement.
- Série standard A, D, DS, G et GS machines virtuelles.

Chiffrement de disque n’est pas pris en charge dans les scénarios suivants :

- Machines virtuelles de niveau simple.
- Machines virtuelles créées à l’aide du modèle de déploiement classique.
- La désactivation de chiffrement de disque du système d’exploitation sur machines virtuelles Linux.
- Mise à jour les clés de chiffrement sur une VM Linux déjà chiffré.


## <a name="create-the-azure-key-vault-and-keys"></a>Créer les touches et l’archivage sécurisé de clé Azure
Pour terminer le reste de ce guide, vous devez la [Dernière Azure infrastructure du langage commun](../xplat-cli-install.md) installé et connecté à l’aide du mode directeur des ressources comme suit :

```
azure config mode arm
```

Dans les exemples de commande, remplacez tous les paramètres d’exemple par vos propres noms, l’emplacement et les valeurs de clé. Les exemples suivants utilisent une convention de `myResourceGroup`, `myKeyVault`, `myAADApp`, etc..

La première étape consiste à créer un archivage sécurisé Azure clé pour stocker vos clés de chiffrement. L’archivage sécurisé clé Azure peuvent stocker les clés, secrets ou les mots de passe qui vous permettent de les implémenter en toute sécurité dans vos applications et services. Pour le chiffrement de disque virtuel, vous utilisez l’archivage sécurisé clé pour stocker une clé de chiffrement qui est utilisée pour chiffrer ou déchiffrer vos disques virtuels. 

Activer le fournisseur de l’archivage sécurisé de clé Azure dans votre abonnement Azure, puis créer un groupe de ressources. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` dans les `WestUS` emplacement :

```bash
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

La clé Azure l’archivage sécurisé les contenant les clés de chiffrement et les ressources de cluster associé tels que le stockage et la machine virtuelle lui-même doit se trouver dans la même région. L’exemple suivant crée un archivage sécurisé de clé Azure nommé `myKeyVault`:

```bash
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Vous pouvez stocker des clés de chiffrement à l’aide du logiciel ou protection de modèle de sécurité matériel (HSM). Utiliser un module HSM nécessite une clé de l’archivage sécurisé premium. Il existe un coût supplémentaire pour la création d’une prime l’archivage sécurisé clé plutôt que standard l’archivage sécurisé clé qui stocke les clés protégées par un logiciel. Pour créer une clé de l’archivage sécurisé premium, à l’étape précédente, ajoutez `--sku Premium` à la commande. L’exemple suivant utilise des clés protégées par un logiciel étant donné que nous avons créé un archivage sécurisé clé standard. 

Pour les deux modèles protection, la plateforme Azure doit disposer d’un accès pour demander les clés de chiffrement au démarrage de la machine virtuelle à déchiffrer les disques virtuels. Créez une clé de chiffrement au sein de votre l’archivage sécurisé clé, puis activez-le pour une utilisation avec chiffrement de disque virtuel. L’exemple suivant crée une clé nommée `myKey` et lui permet de pour le chiffrement de disque :

```bash
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```


## <a name="create-the-azure-active-directory-application"></a>Créer l’application Azure Active Directory
Lorsque des disques virtuels sont chiffrés ou déchiffrés, un point de terminaison vous permet de gérer l’authentification et l’échange de clés de chiffrement de clé de l’archivage sécurisé. Ce point de terminaison, une application Azure Active Directory, permet de la plateforme Azure demander les clés de chiffrement appropriés au nom de la machine virtuelle. Une instance Azure Active Directory par défaut est disponible dans votre abonnement, bien que de nombreuses organisations ont dédié annuaires Azure Active Directory.

Comme vous ne créez pas une application Azure Active Directory complète, la `--home-page` et `--identifier-uris` paramètres dans l’exemple suivant n’avez pas besoin d’être adresse prenant réelle. L’exemple suivant spécifie également un mot de passe secret plutôt que clés générer au sein du portail Azure. En tant que cette fois, générer des clés n’est pas possible depuis l’interface Azure. 

Créez votre application Azure Active Directory. L’exemple suivant crée une application nommée `myAADApp` et utilise un mot de passe de `myPassword`. Spécifiez votre propre mot de passe comme suit :

```bash
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Prenez note de la `applicationId` qui est renvoyée dans le résultat de la commande précédente. ID de l’application est utilisé dans certaines étapes restantes. Ensuite, créez un nom principal de service (nom principal de service) afin que l’application est accessible au sein de votre environnement. Pour chiffrer ou déchiffrer disques virtuels avec succès, les autorisations sur la clé de chiffrement stockées dans l’archivage sécurisé de clé doivent être définies pour permettre l’application Azure Active Directory pour lire les clés. 

Créer le nom principal de service et définissez les autorisations appropriées comme suit :

```bash
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```


## <a name="add-a-virtual-disk-and-review-encryption-status"></a>Ajouter un disque virtuel et consulter l’état de chiffrement
Pour chiffrer réellement certains des disques virtuels, vous permet d’ajouter un disque à une machine virtuelle existante. Ajoutez un disque de données 5 Go pour une machine virtuelle existante comme suit :

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Les disques virtuels ne sont pas actuellement chiffrés. Consulter l’état actuel de chiffrement de votre ordinateur virtuel comme suit :

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="encrypt-virtual-disks"></a>Chiffrer disques virtuels
Pour chiffrer maintenant les disques virtuels, vous permettent de rassembler tous les composants précédents :

1. Spécifier l’application Azure Active Directory et le mot de passe.
2. Spécifiez l’archivage sécurisé clé pour stocker les métadonnées de vos disques chiffrés.
3. Spécifier les clés de chiffrement à utiliser pour le chiffrement réel et déchiffrement.
4. Indiquez si vous souhaitez chiffrer le disque du système d’exploitation, les disques de données ou tout.

Vous permet de consulter les détails de votre l’archivage sécurisé de clé Azure et la clé que vous avez créé, comme vous avez besoin de l’ID de l’archivage sécurisé clé, URI et puis clé URL dans la dernière étape :

```bash
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Chiffrer vos disques virtuels en utilisant la sortie à partir de la `azure keyvault show` et `azure keyvault key show` commandes comme suit :

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Comme la commande précédente comporte plusieurs variables, l’exemple suivant est la commande complète pour référence :

```bash
azure vm enable-disk-encryption -g myResourceGroup -n MyVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \ 
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

L’infrastructure du langage commun Azure ne fournissent des erreurs détaillées au cours du processus de chiffrement. Pour plus d’informations, consultez `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log` sur l’ordinateur virtuel vous chiffrez.

Enfin, vous permet de consulter l’état de chiffrement à nouveau pour confirmer que vos disques virtuels ont désormais été chiffrés :

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="add-additional-data-disks"></a>Ajouter des données supplémentaires disques
Une fois que vous avez chiffré vos disques de données, vous pouvez ultérieurement ajouter disques virtuels supplémentaires à votre ordinateur virtuel et également les chiffrer. Lorsque vous exécutez le `azure vm enable-disk-encryption` commande, incrémenter la version de séquence à l’aide de la `--sequence-version` paramètre. Ce paramètre de version de séquence vous permet d’effectuer les opérations répétées sur la même machine virtuelle.

Par exemple, vous permet d’ajouter un deuxième disque virtuel à votre machine virtuelle comme suit :

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Exécutez à nouveau la commande pour chiffrer les disques virtuels, cette fois ajout le `--sequence-version` paramètre et incrémentation de la valeur de notre première exécution comme suit :

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
  --sequence-version 2
```


## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la gestion de l’archivage sécurisé clé Azure, y compris la suppression des clés de chiffrement et chambres fortes, voir [gérer l’archivage sécurisé clé à l’aide d’infrastructure du langage commun](../key-vault/key-vault-manage-with-cli.md).
- Pour plus d’informations sur le chiffrement de disque, telles que la préparation d’un ordinateur virtuel personnalisé chiffré à télécharger dans Azure, voir [Le chiffrement des disques Azure](../security/azure-security-disk-encryption.md).