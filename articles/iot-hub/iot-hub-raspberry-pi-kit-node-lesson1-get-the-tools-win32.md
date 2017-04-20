<properties
 pageTitle="Obtenez les outils (Windows 7 +) | Microsoft Azure"
 description="Téléchargez et installez les outils nécessaires et les logiciels pour le premier exemple d’application pour votre Pi sur Windows 7 et versions ultérieures."
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2016"
 ms.author="xshi"/>

# <a name="12-get-the-tools-windows-7-"></a>1.2 obtenir les outils (Windows 7 +) 

> [AZURE.SELECTOR]
- [Windows 7 +](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)
- [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-ubuntu.md)
- [Mac OS 10.10](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-mac.md)

## <a name="121-what-you-will-do"></a>1.2.1 ce que vous ferez

Téléchargez les outils de développement et le logiciel pour le premier exemple d’application pour votre framboises Pi 3. Si vous répondez à des problèmes, recherchent des solutions dans la [page de résolution des problèmes](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="122-what-you-will-learn"></a>1.2.2 enseignements de cet article
- Comment installer Git et Node.js
  - [GIT](https://git-scm.com) est un système de contrôle de version distribuée ouvrir la source. L’exemple d’application pour cette leçon est stocké sur Git.
  - [Node.js](https://nodejs.org/en/) est un runtime JavaScript avec un réseau de package complet.
- Comment utiliser NPM pour installer d’autres outils de développement Node.js.
  - La version minimale requise de Node.js est 4.5 LTS.
  - [NPM](https://www.npmjs.com) est un des gestionnaires de package pour Node.js.

## <a name="123-what-you-need"></a>1.2.3 ce dont vous avez besoin

- Une connexion Internet pour télécharger les outils de développement et les logiciels
- Un ordinateur exécutant Windows

## <a name="124-install-git-and-nodejs"></a>1.2.4 installer Git et Node.js

Cliquez sur les liens ci-dessous pour télécharger et installer Git et Node.js LTS pour Windows.

- [Obtenir Git pour Windows](https://git-scm.com/download/win/)
- [Obtenir Node.js LTS pour Windows](https://nodejs.org/en/)

## <a name="125-install-additional-nodejs-development-tools"></a>1.2.5 installer d’autres outils de développement Node.js

[Gulp.js](http://gulpjs.com) vous permet d’automatiser le déploiement de l’exemple d’application à votre Pi. Le [périphérique-découverte-cli](https://github.com/Azure/device-discovery-cli) permet également de récupérer des informations de réseau sur vos appareils IoT.

Démarrez une invite de commandes en tant qu’administrateur. Installer `gulp` et `device-discovery-cli` en exécutant la commande suivante :

```bash
npm install -g device-discovery-cli gulp
```
    
Si vous rencontrez des problèmes d’installation Node.js et ces outils de développement Node.js supplémentaires sur votre ordinateur, voir le [guide de dépannage](iot-hub-raspberry-pi-kit-node-troubleshooting.md) pour les solutions aux problèmes courants.

## <a name="126-install-visual-studio-code"></a>1.2.6 installer Code Visual Studio

[Téléchargez](https://code.visualstudio.com/docs/setup/windows) et installez Code Visual Studio. Code de Visual Studio est un éditeur de code source simple mais puissant pour Windows, Linux et Mac OS. Vous utilisez cet éditeur plus loin dans le didacticiel pour modifier l’exemple de code.

## <a name="127-summary"></a>1.2.7). résumé

Vous avez installé les outils de développement requis et les logiciels pour le premier exemple d’application. Dans la section suivante, créer, déployer et exécuter l’exemple d’application sur votre Pi.

## <a name="next-steps"></a>Étapes suivantes

[1.3 Créez et déployez clignoter l’exemple d’application](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)
