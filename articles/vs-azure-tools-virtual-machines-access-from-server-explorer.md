<properties
   pageTitle="Accéder à Azure Machines virtuelles à partir de l’Explorateur de serveurs | Microsoft Azure"
   description="Obtenir une vue d’ensemble de l’affichage créer et gérer des machines virtuelles Azure (machines virtuelles) dans l’Explorateur de serveurs dans Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>L’accès aux Machines virtuelles Azure à partir de l’Explorateur de serveurs

En utilisant l’Explorateur de serveurs dans Visual Studio, vous pouvez afficher des informations sur vos machines virtuelles hébergés par Azure.

## <a name="accessing-virtual-machines-in-server-explorer"></a>L’accès aux machines virtuelles dans l’Explorateur de serveurs

Si vous avez machines virtuelles hébergées par Azure, vous pouvez y accéder dans l’Explorateur de serveur. Vous devez tout d’abord se connecter à votre abonnement Azure pour afficher vos services mobiles. Pour vous connecter, ouvrez le menu contextuel pour le nœud Azure dans l’Explorateur de serveur, puis sélectionnez **se connecter à Microsoft Azure**.

### <a name="to-get-information-about-your-virtual-machines"></a>Pour obtenir des informations sur vos ordinateurs virtuels

1. Dans l’Explorateur de serveur, sélectionnez une machine virtuelle, puis la touche F4 pour afficher sa fenêtre de propriétés.

    Le tableau suivant indique les propriétés disponibles, mais ils sont en lecture seule. Pour les modifier, utiliser le [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

  	|Propriété|Description|
  	|---|---|
  	|Nom de DNS|L’URL avec l’adresse Internet de la machine virtuelle.|
  	|Environnement|Pour une machine virtuelle, la valeur de cette propriété est toujours Production.|
  	|Nom|Le nom de la machine virtuelle.|
  	|Taille|La taille de la machine virtuelle, qui reflète la quantité de mémoire et l’espace disque disponible. Pour plus d’informations, voir Procédure : configurer les tailles de Machine virtuelle.|
  	|État|Valeurs possibles démarrage, mise en route, arrêt, arrêt et extraction de l’état. Récupération d’un état s’affiche, l’état actuel est inconnu. Les valeurs de cette propriété diffèrent les valeurs qui sont utilisées dans le [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885).|
  	|SubscriptionID|L’ID de l’abonnement de votre compte Azure. Vous pouvez afficher ces informations dans le [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885) en affichant les propriétés d’un abonnement.|

1. Sélectionnez un nœud de point de terminaison et affichez la fenêtre **Propriétés** .

1. Le tableau suivant décrit les propriétés disponibles des points de terminaison, mais ils sont en lecture seule. Pour ajouter ou modifier les points de terminaison pour une machine virtuelle, utiliser le [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885). 

  	|Propriété|Description|
  	|---|---|
  	|Nom|Un identificateur pour le point de terminaison.|
  	|Port privé|Port pour accéder au réseau interne à votre application.|
  	|Protocole|Le protocole utilisé par la couche de transport pour ce point de terminaison, TCP ou UDP.|
  	|Port public|Le port qui est utilisé pour l’accès public à votre application.|

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation des rôles d’Azure dans Visual Studio, reportez-vous [à l’aide de bureau à distance avec Azure rôles](vs-azure-tools-remote-desktop-roles.md).
