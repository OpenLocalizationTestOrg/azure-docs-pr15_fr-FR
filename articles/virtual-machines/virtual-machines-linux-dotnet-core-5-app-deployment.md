<properties
   pageTitle="Automatisation de déploiement d’applications avec les Extensions Machine virtuelle | Microsoft Azure"
   description="Machine virtuelle Azure DotNet Core didacticiel"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/21/2016"
   ms.author="nepeters"/>

# <a name="application-deployment-with-azure-resource-manager-templates"></a>Déploiement des applications avec les modèles de gestionnaire de ressources Azure

Une fois que toutes les exigences infrastructures Azure ont été identifiés et traduits dans un modèle de déploiement, le déploiement des applications réel doit être résolus. Déploiement d’une application ici fait référence à réinstaller les binaires application réelle sur les ressources d’Azure. Pour l’exemple de magasin de musique, .net Core, NGINX et contrôleur doivent être installé et configuré sur chaque ordinateur virtuel. Les fichiers binaires du magasin de musique doivent être installés sur l’ordinateur virtuel, et la base de données de magasin de musique créés à l’avance.

Ce document en détail comment automatiser les extensions Machine virtuelle déploiement des applications et la configuration des machines virtuelles Azure. Toutes les dépendances et configurations uniques sont mis en surbrillance. Pour optimiser les performances, avant de déployer une instance de la solution à votre abonnement Azure et le travail ainsi que le modèle Azure le Gestionnaire de ressources. Le modèle complète sont accessibles ici – [Déploiement de magasin de musique sur Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="configuration-script"></a>Script de configuration

Machine virtuelle extensions sont des programmes spécialisés qui s’exécutent sur machines virtuelles à fournir automation de configuration. Extensions sont disponibles à de nombreuses fins spécifiques tels que configuration Docker antivirus et configuration de la journalisation. Une extension de script personnalisé peut être utilisée pour exécuter un script sur une machine virtuelle. Dans l’exemple de magasin de musique, c’est à l’extension de script personnalisé pour configurer les ordinateurs virtuels Ubuntu et installer l’application de magasin de musique.

Avant d’indiquant comment les extensions de machine virtuelle sont déclarées dans un modèle de gestionnaire de ressources Azure, examinez le script est exécuté. Ce script configure la machine virtuelle Ubuntu pour héberger l’application de magasin de musique. Lorsque vous exécutez, le script installe tous nécessaires logiciel, installez l’application de magasin de musique à partir de contrôle de code source et préparer la base de données. 

Pour plus d’informations sur l’hébergement d’un .net application sur Linux de base, voir [publier dans un environnement de production Linux](https://docs.asp.net/en/latest/publishing/linuxproduction.html). 

> Cet exemple est pour la démonstration.

```none
#!/bin/bash

# install dotnet core
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
sudo apt-get update
sudo apt-get install -y dotnet-dev-1.0.0-preview2-003121

# download application
sudo wget https://raw.github.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/music-store-azure-demo-pub.tar /
sudo mkdir /opt/music
sudo tar -xf music-store-azure-demo-pub.tar -C /opt/music

# install nginx, update config file
sudo apt-get install -y nginx
sudo service nginx start
sudo touch /etc/nginx/sites-available/default
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/nginx-config/default -O /etc/nginx/sites-available/default
sudo cp /opt/music/nginx-config/default /etc/nginx/sites-available/
sudo nginx -s reload

# update and secure music config file
sed -i "s/<replaceserver>/$1/g" /opt/music/config.json
sed -i "s/<replaceuser>/$2/g" /opt/music/config.json
sed -i "s/<replacepass>/$3/g" /opt/music/config.json
sudo chown $2 /opt/music/config.json
sudo chmod 0400 /opt/music/config.json

# config supervisor
sudo apt-get install -y supervisor
sudo touch /etc/supervisor/conf.d/music.conf
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/supervisor/music.conf -O /etc/supervisor/conf.d/music.conf
sudo service supervisor stop
sudo service supervisor start

# pre-create music store database
/usr/bin/dotnet /opt/music/MusicStore.dll &
```

## <a name="vm-script-extension"></a>Extension de Script machine virtuelle

Machine virtuelle Extensions peuvent être exécutées sur une machine virtuelle au moment de créer en incluant la ressource extension dans le modèle Azure le Gestionnaire de ressources. L’extension peut être ajoutée avec l’Assistant Visual Studio ajouter une ressource, ou en insérant JSON valide dans le modèle. La ressource de Script Extension est imbriquée à l’intérieur de la ressource Machine virtuelle ; Ceci est visible dans l’exemple suivant.

Suivez ce lien pour afficher l’échantillon JSON dans le modèle de gestionnaire de ressources – [Extension de Script machine virtuelle](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L359). 

Notez que dans la ci-dessous JSON qu’il est stocké dans GitHub. Ce script peut également être stocké dans le stockage Blob Azure. En outre, le Gestionnaire de ressources Azure modèles permettre de la chaîne de l’exécution de script construite, tels que les valeurs de paramètres de modèle peuvent être utilisées en tant que paramètres pour l’exécution de scripts. Dans ce cas les données sont fournies lorsque vous déployez les modèles, et ces valeurs puis peuvent être utilisés lorsque vous exécutez le script.

```none
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
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
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Pour plus d’informations sur l’utilisation de l’extension de script personnalisé, voir [extensions de script personnalisé avec les modèles de gestionnaire de ressources](./virtual-machines-linux-extensions-customscript.md).

## <a name="next-step"></a>Étape suivante

<hr>

[Explorer plus Azure modèles Gestionnaire de ressources](https://github.com/Azure/azure-quickstart-templates)
