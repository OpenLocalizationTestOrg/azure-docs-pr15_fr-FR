<properties
   pageTitle="Activation ou désactivation de l’analyse de la mémoire virtuelle Azure"
   description="Décrit comment activer ou désactiver l’analyse de la mémoire virtuelle Azure"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# <a name="enable-or-disable-azure-vm-monitoring"></a>Activer ou désactiver le contrôle de machine virtuelle Azure

Cette section décrit comment activer ou désactiver l’analyse sur machines virtuelles s’exécutant sur Azure. Par défaut surveillance est activé sur machines virtuelles Azure si déployé à partir du [portail Azure](https://portal.azure.com) et graphiques d’analyse sont fournies par défaut par un point 1 minute. Vous pouvez activer ou désactiver le contrôle à l’aide du portail ou une Interface Azure pour Mac, Linux et Windows (la Azure infrastructure du langage commun). 

## <a name="enable--disable-monitoring-through-the-azure-portal"></a>Activer / désactiver l’analyse via le portail d’Azure
 
Vous pouvez activer le suivi de votre ordinateur virtuel Azure, qui fournit des données relatives à votre instance de périodes de 1 minute. (stockage modifications s’appliquent). Données de diagnostic détaillées seront alors disponibles pour la machine virtuelle dans les graphiques portails ou via l’API. Par défaut, portail Azure Active l’analyse, mais vous pouvez désactiver cette option comme décrit ci-dessous. Vous pouvez activer la surveillance pendant la machine virtuelle est en cours d’exécution ou dans état arrêté.

- Ouvrez le portail en **Azure [https://portal.azure.com](https://portal.azure.com)**

- Dans le volet de navigation gauche, cliquez sur machines virtuelles.

- Dans la liste des machines virtuelles, sélectionnez une instance en cours d’exécution ou arrêtée. Machine virtuelle blad s’ouvre.

- Cliquez sur « Tous les paramètres ».

- Cliquez sur « Diagnostics ».

- Modifier l’état sur activé ou désactivé. Vous pouvez également choisir dans cette carte le niveau de contrôle de détails que vous voulez activer pour votre ordinateur virtuel.

[Azure.Note] Le commutateur Diagnostics sur est la valeur par défaut lorsque vous créez une nouvelle machine virtuelle

![Activer / désactiver l’analyse via le portail d’Azure.][1]


## <a name="enable--disable-monitoring-with-azure-cli"></a>Activer / désactiver l’analyse avec Azure infrastructure du langage commun
 
Pour activer le contrôle pour une machine virtuelle Azure.

- Créez un fichier nommé tels que PrivateConfig.json avec le contenu suivant.
        {« storageAccountName » : « le compte de stockage à recevoir des données », « storageAccountKey » : « la clé du compte »}
- Exécutez la commande suivante Azure infrastructure du langage commun.

        azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.0 --private-config-path PrivateConfig.json

[Azure.Note] Vous pouvez modifier de la version 2.0 vers une version ultérieure lorsqu’elles sont disponibles. 

Pour plus d’informations sur la configuration de surveillance des indicateurs et des exemples, visitez le document - **[À l’aide de l’Extension Diagnostic Linux pour moniteur Linux machine virtuelle performances et données de diagnostic](virtual-machines-linux-classic-diagnostic-extension.md).

<!--Image references-->
[1]: ./media/virtual-machines-linux-vm-monitoring/portal-enable-disable.png
 

