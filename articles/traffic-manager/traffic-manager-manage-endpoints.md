<properties
    pageTitle="Gérer les points de terminaison dans le Gestionnaire de trafic Azure | Microsoft Azure"
    description="Cet article vous aidera à ajouter, supprimer, activer et désactiver les points de terminaison à partir du Gestionnaire de trafic Azure."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="add-disable-enable-or-delete-endpoints"></a>Ajouter, désactiver, activer ou supprimer des points de terminaison

La fonctionnalité d’applications Web dans le Service d’application Azure fournit déjà basculement et fonctionnalité de routage cyclique le trafic pour les sites Web dans un centre de données, quel que soit le mode de site Web. Azure le trafic Manager vous permet de spécifier basculement et le routage du trafic cyclique services cloud et sites Web dans différents centres de données. La première étape nécessaire pour fournir ces fonctionnalités consiste à ajouter le point de terminaison service ou site Web du cloud vers le Gestionnaire de trafic.

>[AZURE.NOTE]  Cet article explique comment utiliser le portail classique. Le portail classique Azure prend uniquement en charge la création et l’attribution des services cloud et des applications Web comme points de terminaison. Le nouveau [portail Azure](https://portal.azure.com) est l’interface préférée.

Vous pouvez également désactiver des points de terminaison individuels qui font partie d’un profil Manager le trafic. Désactivation d’un point de terminaison laisse dans le cadre du profil, mais le profil se comporte comme si le point de terminaison n’est pas inclus dans celle-ci. Cette action est utile pour la suppression d’un point de terminaison est en mode maintenance ou redéployé temporairement. Une fois que le point de terminaison est encore en cours d’exécution, il peut être activée.

>[AZURE.NOTE] Désactivation d’un point de terminaison ne comporte rien faire avec son état déploiement dans Azure. Un point de terminaison exact reste opérationnel et en mesure de recevoir le trafic même lorsque désactivée dans le Gestionnaire de trafic. En outre, la désactivation d’un point de terminaison d’un profil n’affecte pas son statut dans un autre profil.

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>Pour ajouter un point de terminaison service ou site Web du cloud

1. Dans le volet Gestionnaire le trafic dans le portail classique Azure, recherchez le profil de trafic gestionnaire qui contient les paramètres de point de terminaison que vous souhaitez modifier. Pour ouvrir la page Paramètres, cliquez sur la flèche située à droite du nom du profil.
2. En haut de la page, cliquez sur les **points de terminaison** pour afficher les points de terminaison qui font déjà partie de votre configuration.
3. Dans la partie inférieure de la page, cliquez sur **Ajouter** pour accéder à la page **Ajouter des points de terminaison de Service** . Par défaut, la page répertorie les services cloud sous **Points de terminaison de Service**.
4. Pour les services en nuage, sélectionnez les services de cloud dans la liste pour les ajouter en tant que points de terminaison pour ce profil. Effacer le nom du service cloud supprime de la liste des points de terminaison.
5. Les sites Web, cliquez sur la liste déroulante **Type de Service** , puis **dans le navigateur**.
6. Sélectionnez les sites Web dans la liste pour les ajouter en tant que points de terminaison pour ce profil. Effacement du nom du site Web supprime de la liste des points de terminaison. Vous pouvez sélectionner qu’un seul site Web par centre de données Azure (également appelé une région). Lorsque vous sélectionnez le premier site Web, les autres sites Web dans le même centre de données ne sont plus disponibles pour la sélection. Notez également que des sites Web uniquement Standard sont répertoriés.
7. Une fois que vous sélectionnez les points de terminaison pour ce profil, cliquez sur la coche dans le coin inférieur droit pour enregistrer vos modifications.

>[AZURE.NOTE] Une fois que vous ajoutez ou supprimez un point de terminaison à partir d’un profil en utilisant la méthode de routage de trafic *basculement* , la liste priorité basculement ne peut pas être trié ils comme vous le souhaitez. Vous pouvez ajuster l’ordre de la liste priorité basculement dans la page de Configuration. Pour plus d’informations, voir [configurer le basculement le routage du trafic](traffic-manager-configure-failover-routing-method.md).

## <a name="to-disable-an-endpoint"></a>Pour désactiver un point de terminaison

1. Dans le volet Gestionnaire le trafic dans le portail classique Azure, recherchez le profil de trafic gestionnaire qui contient les paramètres de point de terminaison que vous souhaitez modifier. Pour ouvrir la page Paramètres, cliquez sur la flèche située à droite du nom du profil.
2. En haut de la page, cliquez sur les **points de terminaison** pour afficher les points de terminaison qui sont inclus dans votre configuration.
3. Cliquez sur le point de terminaison que vous souhaitez désactiver, puis cliquez sur **désactiver** dans la partie inférieure de la page.
4. Clients continuent à acheminer le trafic vers le point de terminaison pendant la durée de temps de vie (TTL). Vous pouvez modifier la durée de vie dans la page de Configuration du profil gestionnaire le trafic.

## <a name="to-enable-an-endpoint"></a>Pour activer un point de terminaison

1. Dans le volet Gestionnaire le trafic dans le portail classique Azure, recherchez le profil de trafic gestionnaire qui contient les paramètres de point de terminaison que vous souhaitez modifier. Pour ouvrir la page Paramètres, cliquez sur la flèche située à droite du nom du profil.
2. En haut de la page, cliquez sur les **points de terminaison** pour afficher les points de terminaison qui sont inclus dans votre configuration.
3. Cliquez sur le point de terminaison que vous souhaitez activer, puis cliquez sur **Activer** en bas de la page.
4. Clients sont dirigés vers le point de terminaison activé comme indiqué par le profil.

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>Pour supprimer un point de terminaison service ou site Web du cloud

1. Dans le volet Gestionnaire le trafic dans le portail classique Azure, recherchez le profil de trafic gestionnaire qui contient les paramètres de point de terminaison que vous souhaitez modifier. Pour ouvrir la page Paramètres, cliquez sur la flèche située à droite du nom du profil.
2. En haut de la page, cliquez sur les **points de terminaison** pour afficher les points de terminaison qui font déjà partie de votre configuration.
3. Dans la page points de terminaison, cliquez sur le nom du point de terminaison que vous voulez supprimer à partir du profil.
4. Dans la partie inférieure de la page, cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

* [Gérer les profils de trafic Manager](traffic-manager-manage-profiles.md)
* [Configurer des méthodes de routage](traffic-manager-configure-routing-method.md)
* [Résolution des problèmes le trafic gestionnaire est détérioré état](traffic-manager-troubleshooting-degraded.md)
* [Remarques sur les performances du trafic Manager](traffic-manager-performance-considerations.md)
* [Opérations sur le trafic Manager (référence des API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)
