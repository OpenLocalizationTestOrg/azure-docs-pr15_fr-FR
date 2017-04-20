<properties
   pageTitle="Gérer les points de terminaison dans le Gestionnaire de trafic Azure | Microsoft Azure"
   description="Cet article vous aidera à ajouter, supprimer, activer et désactiver les points de terminaison à partir du Gestionnaire de trafic Azure."
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="sewhee" />

# <a name="add-disable-enable-or-delete-endpoints"></a>Ajouter, désactiver, activer ou supprimer des points de terminaison

La fonctionnalité d’applications Web dans le Service d’application Azure fournit déjà basculement et fonctionnalité de routage cyclique le trafic pour les sites Web dans un centre de données, quel que soit le mode de site Web. Azure le trafic Manager vous permet de spécifier basculement et le routage du trafic cyclique services cloud et sites Web dans différents centres de données. La première étape nécessaire pour fournir ces fonctionnalités consiste à ajouter le point de terminaison service ou site Web du cloud vers le Gestionnaire de trafic.

>[AZURE.NOTE] Vous ne pouvez pas ajouter des emplacements externes ou profils Manager le trafic comme points de terminaison à l’aide du portail classique Azure. Vous devez utiliser l’API REST [Créer une définition](http://go.microsoft.com/fwlink/p/?LinkId=400772) ou Windows PowerShell [Ajouter AzureTrafficManagerEndpoint](http://go.microsoft.com/fwlink/p/?LinkId=400774).

Vous pouvez également désactiver des points de terminaison individuels qui font partie d’un profil Manager le trafic. Points de terminaison incluent des services cloud et sites Web. Désactivation d’un point de terminaison laisse dans le cadre du profil, mais le profil se comporte comme si le point de terminaison n’est pas inclus dans celle-ci. Cette action est très utile pour la suppression d’un point de terminaison est en mode maintenance ou redéployé temporairement. Une fois que le point de terminaison est encore en cours d’exécution, il peut être activée.

>[AZURE.NOTE] Désactivation d’un point de terminaison ne comporte rien faire avec son état déploiement dans Azure. Un point de terminaison exact resteront vers le haut et en mesure de recevoir le trafic même lorsque désactivée dans le Gestionnaire de trafic. En outre, la désactivation d’un point de terminaison d’un profil n’affecte pas son statut dans un autre profil.

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>Pour ajouter un point de terminaison service ou site Web du cloud


1. Dans le volet Gestionnaire le trafic dans le portail classique Azure, recherchez le profil de trafic gestionnaire qui contient les paramètres de point de terminaison que vous souhaitez modifier, puis cliquez sur la flèche située à droite du nom du profil. Ceci ouvre la page Paramètres pour le profil.
2. En haut de la page, cliquez sur les **points de terminaison** pour afficher les points de terminaison qui font déjà partie de votre configuration.
3. Dans la partie inférieure de la page, cliquez sur **Ajouter** pour accéder à la page **Ajouter des points de terminaison de Service** . Par défaut, la page répertorie les services cloud sous **Points de terminaison de Service**.
4. Pour les services en nuage, sélectionnez les services de cloud dans la liste afin de les activer en tant que points de terminaison pour ce profil. Effacer le nom du service cloud supprime de la liste des points de terminaison.
5. Les sites Web, cliquez sur la liste déroulante **Type de Service** , puis **dans le navigateur**.
6. Sélectionnez les sites Web dans la liste pour les ajouter en tant que points de terminaison pour ce profil. Effacement du nom du site Web supprime de la liste des points de terminaison. Notez que vous ne pouvez sélectionner un site Web par centre de données Azure (également appelé une région). Si vous sélectionnez un site Web dans un centre de données qui héberge plusieurs sites Web, lorsque vous sélectionnez le premier site Web, les autres colonnes dans le centre de données même deviennent indisponibles pour la sélection. Notez également que des sites Web uniquement Standard sont répertoriés.
7. Une fois que vous sélectionnez les points de terminaison pour ce profil, cliquez sur la coche dans le coin inférieur droit pour enregistrer vos modifications.

>[AZURE.NOTE] Si vous utilisez la méthode de routage de trafic *basculement* , une fois que vous ajoutez ou supprimez un point de terminaison, veillez à ajuster la liste priorité basculement dans la page Configuration pour refléter l’ordre de basculement souhaité pour votre configuration. Pour plus d’informations, voir [configurer le basculement le routage du trafic](traffic-manager-configure-failover-routing-method.md).

## <a name="to-disable-an-endpoint"></a>Pour désactiver un point de terminaison

1. Dans le volet Gestionnaire le trafic dans le portail classique Azure, recherchez le profil de trafic gestionnaire qui contient les paramètres de point de terminaison que vous souhaitez modifier, puis cliquez sur la flèche située à droite du nom du profil. Ceci ouvre la page Paramètres pour le profil.
2. En haut de la page, cliquez sur les **points de terminaison** pour afficher les points de terminaison qui sont inclus dans votre configuration.
3. Cliquez sur le point de terminaison que vous souhaitez désactiver, puis cliquez sur **désactiver** dans la partie inférieure de la page.
4. Le trafic n’est plus dirige vers le point de terminaison basé sur la DNS Time-to-Live (TTL) configurée pour le nom de domaine gestionnaire le trafic. Vous pouvez modifier la durée de vie à partir de la page de Configuration du profil gestionnaire le trafic.

## <a name="to-enable-an-endpoint"></a>Pour activer un point de terminaison

1. Dans le volet Gestionnaire le trafic dans le portail classique Azure, recherchez le profil de trafic gestionnaire qui contient les paramètres de point de terminaison que vous souhaitez modifier, puis cliquez sur la flèche située à droite du nom du profil. Ceci ouvre la page Paramètres pour le profil.
2. En haut de la page, cliquez sur les **points de terminaison** pour afficher les points de terminaison qui sont inclus dans votre configuration.
3. Cliquez sur le point de terminaison que vous souhaitez activer, puis cliquez sur **Activer** en bas de la page.
4. Le trafic commence dirige vers le service à nouveau comme dictée par le profil.

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>Pour supprimer un point de terminaison service ou site Web du cloud


1. Dans le volet Gestionnaire le trafic dans le portail classique Azure, recherchez le profil de trafic gestionnaire qui contient les paramètres de point de terminaison que vous souhaitez modifier, puis cliquez sur la flèche située à droite du nom du profil. Ceci ouvre la page Paramètres pour le profil.
2. En haut de la page, cliquez sur les **points de terminaison** pour afficher les points de terminaison qui font déjà partie de votre configuration.
3. Dans la page points de terminaison, cliquez sur le nom du point de terminaison que vous voulez supprimer à partir du profil.
4. Dans la partie inférieure de la page, cliquez sur **Supprimer**.

>[AZURE.NOTE] Vous ne pouvez pas supprimer des emplacements externes ou profils Manager le trafic comme points de terminaison à l’aide du portail classique Azure. Vous devez utiliser Windows PowerShell. Pour plus d’informations, voir [Supprimer AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/dn690251.aspx).

## <a name="next-steps"></a>Étapes suivantes

- [Configurer la méthode de routage basculement](traffic-manager-configure-failover-routing-method.md)
- [Configurer la méthode de routage alternées](traffic-manager-configure-round-robin-routing-method.md)
- [Configurer la méthode de routage performances](traffic-manager-configure-performance-routing-method.md)
- [Résolution des problèmes le trafic gestionnaire est détérioré état](traffic-manager-troubleshooting-degraded.md)
- [Opérations sur le trafic Manager (référence des API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)
