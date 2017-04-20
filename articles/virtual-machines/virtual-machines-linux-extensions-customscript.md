<properties
   pageTitle="Les scripts personnalisés sur machines virtuelles Linux | Microsoft Azure"
   description="Automatiser les tâches de configuration Linux VM à l’aide de l’Extension de Script personnalisé"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="nepeters"/>

# <a name="using-the-azure-custom-script-extension-with-linux-virtual-machines"></a>À l’aide de l’Extension Azure Script personnalisé avec des Machines virtuelles Linux

L’Extension de Script personnalisé télécharge et exécute des scripts sur des machines virtuelles Azure. Cette extension est utile pour la configuration de déploiement de publication, installation des logiciels ou toute autre configuration / Gestion des tâches. Scripts pouvant être téléchargés à partir de stockage Azure ou un autre emplacement internet accessibles, ou à l’extension de temps d’exécution. L’extension de Script personnalisé s’intègre avec les modèles Azure le Gestionnaire de ressources et peut également être exécutée à l’aide de l’infrastructure du langage commun Azure, PowerShell, portail Azure ou l’API REST de Machine virtuelle Azure.

Ce document explique en détail comment utiliser l’Extension de Script personnalisé à partir de l’infrastructure du langage commun Azure et un modèle de gestionnaire de ressources Azure et également les détails des étapes de dépannage sur les systèmes Linux.

## <a name="extension-configuration"></a>Configuration de l’extension

La configuration de l’Extension de Script personnalisé spécifie des éléments tels que l’emplacement des scripts et la commande à exécuter. Cette configuration pouvant être stockée dans des fichiers de configuration, spécifié sur la ligne de commande, ou dans un modèle de gestionnaire de ressources Azure. Données sensibles peuvent être stockées dans une configuration protégée, qui est chiffrée et déchiffrée uniquement à l’intérieur de la machine virtuelle. La configuration protégée est utile lorsque la commande d’exécution inclut secrets comme un mot de passe.

### <a name="public-configuration"></a>Configuration publique

Schéma :

- **commandToExecute**: (obligatoire, chaîne) le script de point d’entrée à exécuter
- **fileUris**: (facultatif, tableau de chaînes) les URL des fichiers à télécharger.
- **horodatage** (facultatif, entier) Utilisez ce champ uniquement pour déclencher la ré-exécution du script en modifiant la valeur de ce champ.

```none
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>Configuration protégée

Schéma :

- **commandToExecute**: (facultatif, chaîne) le script de point d’entrée à exécuter. Utilisez ce champ à la place si votre commande contient des données confidentielles telles que les mots de passe.
- **storageAccountName**: (facultatif, chaîne) le nom du compte de stockage. Si vous spécifiez des informations d’identification de l’espace de stockage, tous les fileUris doit être URL pour les objets BLOB Azure.
- **storageAccountKey**: (facultatif, chaîne) touche d’accès rapide de compte de stockage.


```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>Azure infrastructure du langage commun

Lorsque vous utilisez l’infrastructure du langage commun Azure sera exécutée l’Extension de Script personnalisé, créez un fichier de configuration ou des fichiers contenant au moins l’uri de fichier et la commande de l’exécution de script.

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /scirpt-config.json
```

Vous pouvez également la commande peut être exécutée à l’aide de la `--public-config` et `--private-config` option qui autorise la configuration à préciser lors de l’exécution et sans un fichier de configuration distinct.

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

### <a name="azure-cli-examples"></a>Exemples de Azure infrastructure du langage commun

**Exemple 1** : configuration publics avec fichier de script.

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
  "commandToExecute": "./hello.sh"
}
```

Commande infrastructure du langage commun Azure :

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**Exemple 2** : configuration Public sans fichier de script.

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Commande infrastructure du langage commun Azure :

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**Exemple 3** : un fichier de configuration public est utilisé pour spécifier le fichier de script URI et un fichier de configuration protégée est utilisé pour spécifier la commande à exécuter.

Fichier de configuration public :

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
}
```

Fichier de configuration protégée :  

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Commande infrastructure du langage commun Azure :

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path ./public.json --private-config-path ./protected.json
```

## <a name="resource-manager-template"></a>Modèle de gestionnaire de ressources

L’Extension de Script personnalisé Azure peut être exécutée au moment du déploiement Machine virtuelle à l’aide d’un modèle de gestionnaire de ressources. Pour ce faire, ajoutez JSON correctement mis en forme au modèle de déploiement.

### <a name="resource-manager-examples"></a>Exemples de gestionnaire de ressources

**Exemple 1** : configuration publique.

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

**Exemple 2** : commande d’exécution dans configuration protégée.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

Voir .net Core musique Store démo pour un exemple complet - [Démo du magasin musique](https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db).

## <a name="troubleshooting"></a>Résolution des problèmes

Lors de l’exécution de l’Extension de Script personnalisé, il est créé ou téléchargé dans un répertoire similaire à l’exemple suivant. Le résultat de la commande est également enregistré dans ce répertoire dans `stdout` et `stderr` fichier.

```none
/var/lib/azure/custom-script/download/0/
```

L’Extension de Script Azure génère un journal, qui sont accessibles ici.

```none
/var/log/azure/customscript/handler.log
```

L’état d’exécution de l’Extension de Script personnalisé peut également être récupéré avec l’infrastructure du langage commun Azure.

```none
azure vm extension get <resource-group> <vm-name>
```

Le résultat se présente comme le texte suivant :

```none
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les autres Extensions de Script machine virtuelle, voir [vue d’ensemble de l’Extension de Script Azure pour Linux](./virtual-machines-linux-extensions-features.md).