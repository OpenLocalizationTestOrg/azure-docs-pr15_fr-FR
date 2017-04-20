<properties
   pageTitle="Désactiver ou activer un point de terminaison le trafic Gestionnaire | Microsoft Azure"
   description="Cet article vous aidera à désactiver ou activer vos points de terminaison de profil gestionnaire le trafic."
   services="traffic-manager"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="sewhee" />
<!-- repub for nofollow -->

# <a name="disable-or-enable-a-traffic-manager-endpoint"></a>Désactiver ou activer un point de terminaison le trafic Manager

Vous pouvez également désactiver des points de terminaison individuels qui font partie d’un profil Manager le trafic. Points de terminaison incluent des services cloud et sites Web. Désactivation d’un point de terminaison laisse dans le cadre du profil, mais le profil se comporte comme si le point de terminaison n’est pas inclus dans celle-ci. Cette action est très utile pour la suppression d’un point de terminaison est en mode maintenance ou redéployé temporairement. Une fois que le point de terminaison est encore en cours d’exécution, elle peut être activée

>[AZURE.NOTE] **Désactivation d’un point de terminaison ne comporte rien faire avec son état déploiement dans Azure. Un point de terminaison exact resteront vers le haut et en mesure de recevoir le trafic même lorsque désactivée dans le Gestionnaire de trafic. En outre, la désactivation d’un point de terminaison d’un profil n’affecte pas son statut dans un autre profil.**

## <a name="to-disable-an-endpoint"></a>Pour désactiver un point de terminaison

1. Dans le volet Gestionnaire le trafic dans le portail classique Azure, recherchez le profil de trafic gestionnaire qui contient les paramètres de point de terminaison que vous souhaitez modifier, puis cliquez sur la flèche située à droite du nom du profil. Ceci ouvre la page Paramètres pour le profil.
1. En haut de la page, cliquez sur les **points de terminaison** pour afficher les points de terminaison qui sont inclus dans votre configuration.
1. Cliquez sur le point de terminaison que vous souhaitez désactiver, puis cliquez sur **désactiver** dans la partie inférieure de la page.
1. Le trafic n’est plus dirige vers le point de terminaison basé sur la DNS Time-to-Live (TTL) configurée pour le nom de domaine gestionnaire le trafic. Vous pouvez modifier la durée de vie à partir de la page de Configuration du profil gestionnaire le trafic.

## <a name="to-enable-an-endpoint"></a>Pour activer un point de terminaison


1. Dans le volet Gestionnaire le trafic dans le portail classique Azure, recherchez le profil de trafic gestionnaire qui contient les paramètres de point de terminaison que vous souhaitez modifier, puis cliquez sur la flèche située à droite du nom du profil. Ceci ouvre la page Paramètres pour le profil.
1. En haut de la page, cliquez sur les **points de terminaison** pour afficher les points de terminaison qui sont inclus dans votre configuration.
1. Cliquez sur le point de terminaison que vous souhaitez activer, puis cliquez sur **Activer** en bas de la page.
1. Le trafic commence dirige vers le service à nouveau comme dictée par le profil.

## <a name="next-steps"></a>Étapes suivantes

[Le trafic Gestionnaire - désactiver, activer ou supprimer un profil](disable-enable-or-delete-a-profile.md)

[Résolution des problèmes le trafic gestionnaire est détérioré état](traffic-manager-troubleshooting-degraded.md)

[Remarques sur les performances du trafic Manager](traffic-manager-performance-considerations.md)