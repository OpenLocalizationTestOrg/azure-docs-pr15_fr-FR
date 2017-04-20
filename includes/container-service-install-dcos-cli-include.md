<properties
   pageTitle="Installer l’infrastructure du langage commun DC/OS | Microsoft Azure"
   description="Installez le contrôleur de domaine/système d’exploitation infrastructure du langage commun."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Conteneurs, Micro-services, DC/système d’exploitation, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/10/2016"
   ms.author="rogardle"/>

>[AZURE.NOTE] Il s’agit pour l’utilisation de clusters ACS basé sur le système d’exploitation contrôleur de domaine. Il est inutile de faire ceci pour clusters ACS basée sur essaim.

Tout d’abord, [vous connecter à votre cluster ACS basé sur le système d’exploitation contrôleur de domaine](../articles/container-service/container-service-connect.md). Une fois que vous l’avez fait, vous pouvez installer l’infrastructure du langage commun DC/système d’exploitation sur votre ordinateur client avec les commandes ci-dessous :

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

Si vous utilisez une ancienne version de Python, vous pouvez remarquer certaines « InsecurePlatformWarnings ». Vous pouvez ignorer ces.

Pour mettre en route sans redémarrer votre shell, exécutez :

```bash
source ~/.bashrc
```

Cette étape ne sera pas nécessaire lorsque vous démarrez shell de nouveau.

Désormais, vous pouvez vérifier que l’infrastructure du langage commun est installé :

```bash
dcos --help
```