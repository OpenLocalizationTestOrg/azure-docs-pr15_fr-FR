<properties
 pageTitle="Obtenez des outils Azure (Ubuntu 16.04) | Microsoft Azure"
 description="Installer Python et Azure de ligne de commande Interface de Azure sur Ubuntu."
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

# <a name="21-get-azure-tools-ubuntu-1604"></a>2.1 obtenir Azure outils (Ubuntu 16.04)

> [AZURE.SELECTOR]
- [Windows 7 +](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)
- [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-ubuntu.md)
- [Mac OS 10.10](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-mac.md)

## <a name="211-what-you-will-do"></a>2.1.1 ce que vous ferez

Installer le Azure Interface de ligne (commande Azure). Si vous répondez à des problèmes, recherchent des solutions dans la [page de résolution des problèmes](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="212-what-you-will-learn"></a>2.1.2 enseignements de cet article

- Comment installer Azure infrastructure du langage commun.
- Comment ajouter IoT sous-groupe d’Azure infrastructure du langage commun.

## <a name="213-what-you-need"></a>2.1.3 ce dont vous avez besoin

- Un ordinateur Ubuntu avec connexion Internet
- Un abonnement Azure actif. Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) en quelques minutes.

## <a name="214-install-the-azure-cli"></a>2.1.4 installer l’infrastructure du langage commun Azure

L’infrastructure du langage commun Azure offre une expérience de ligne de commande multi-plateforme pour Azure, qui vous permet de travailler directement à partir de la ligne de commande mise en service et gérer des ressources. 

Pour installer la dernière infrastructure du langage commun Azure, procédez comme suit :

1. Exécutez les commandes suivantes dans une fenêtre de Terminal. Cela peut prendre cinq minutes pour installer Azure infrastructure du langage commun.

    ```bash
    sudo apt-get update
    sudo apt-get install -y libssl-dev libffi-dev
    sudo apt-get install -y python-dev
    sudo apt-get install -y build-essential
    sudo apt-get install -y python-pip
    sudo pip install azure-cli-core==0.1.0b7 azure-cli-vm==0.1.0b7 azure-cli-storage==0.1.0b7 azure-cli-role==0.1.0b7 azure-cli-resource==0.1.0b7 azure-cli-profile==0.1.0b7 azure-cli-network==0.1.0b7 azure-cli-iot==0.1.0b7 azure-cli-feedback==0.1.0b7 azure-cli-configure==0.1.0b7 azure-cli-component==0.1.0b7 azure-cli==0.1.0b7
    ```

2. Vérification de l’installation en exécutant la commande suivante :

    ```bash
    az iot -h
    ```

Vous devriez voir le résultat suivant si l’installation est terminée.

![AZ iot -h](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_ubuntu.png)

## <a name="215-summary"></a>2.1.5 résumé

Vous avez installé Azure infrastructure du langage commun. Passez à la section suivante pour créer votre identité Azure IoT concentrateur et appareil à l’aide de l’infrastructure du langage commun Azure.

## <a name="next-steps"></a>Étapes suivantes

[2.2 créer votre concentrateur IoT et inscrire votre framboises Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)
