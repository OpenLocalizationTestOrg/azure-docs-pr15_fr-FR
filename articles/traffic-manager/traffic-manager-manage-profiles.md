<properties
    pageTitle="Gérer les profils Azure le trafic Manager | Microsoft Azure"
    description="Cet article vous permet de créer, désactiver, activer, supprimer et afficher l’historique d’un profil Azure le trafic Manager."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="manage-an-azure-traffic-manager-profile"></a>Gérer un profil Azure le trafic Manager

Profils de trafic Manager utilisent le routage du trafic méthodes pour contrôler la distribution du trafic vers votre cloud services ou les points de terminaison de site Web. Cet article explique comment créer et gérer les profils.

## <a name="create-a-traffic-manager-profile-using-quick-create"></a>Créer un profil de trafic Manager à l’aide de création rapide

Vous pouvez rapidement créer un profil Manager le trafic à l’aide de création rapide dans le portail classique Azure. Création rapide vous permet de créer des profils avec des paramètres de configuration de base. Toutefois, vous ne pouvez pas utiliser la création rapide pour des paramètres tels que le jeu de points de terminaison (cloud services et sites Web), l’ordre de basculement pour la méthode de routage de trafic basculement ou paramètres de contrôle. Après avoir créé votre profil, vous pouvez configurer ces paramètres dans le portail classique Azure. Gestionnaire de trafic prend en charge jusqu'à 200 points de terminaison par profil. Toutefois, la plupart des scénarios d’utilisation requièrent seulement quelques-unes des points de terminaison.

### <a name="to-create-a-traffic-manager-profile"></a>Pour créer un profil le trafic Manager

1. **Déployer vos services cloud et sites Web pour votre environnement de production.** Pour plus d’informations sur les services en nuage, consultez [Services Cloud](http://go.microsoft.com/fwlink/p/?LinkId=314074). Pour plus d’informations sur les sites Web, voir les [sites Web](http://go.microsoft.com/fwlink/p/?LinkId=393327).

2. **Connectez-vous au portail classique Azure.** Cliquez sur **Nouveau** dans le coin inférieur gauche du portail, cliquez sur **Services réseau > Gestionnaire de trafic**, puis cliquez sur **Création rapide** pour commencer à configurer votre profil.
3. **Configurez le préfixe DNS.** Donnez à votre profil manager le trafic DNS unique préfixe nom. Vous pouvez spécifier uniquement le préfixe pour un nom de domaine gestionnaire le trafic.
4. **Sélectionnez l’abonnement.** Sélectionnez l’abonnement Azure approprié. Chaque profil est associé à un seul abonnement. Si vous avez uniquement un abonnement, cette option n’apparaît pas.
5. **Sélectionnez la méthode d’acheminement du trafic.** Sélectionnez la méthode d’acheminement du trafic dans **routage stratégie du trafic**. Pour plus d’informations sur les méthodes de routage du trafic, voir [méthodes de routage de trafic à propos du Gestionnaire de trafic](traffic-manager-routing-methods.md).
6. **Cliquez sur « Créer » pour créer le profil**. Une fois la configuration du profil terminée, vous pouvez rechercher votre profil dans le volet Gestionnaire de trafic dans le portail classique Azure.
7. **Configurer les paramètres supplémentaires, de surveillance et de points de terminaison dans le portail classique Azure.** À l’aide de création rapide configure uniquement les paramètres de base. Il est nécessaire de configurer des paramètres supplémentaires telles que la liste des points de terminaison et l’ordre de basculement de point de terminaison.


## <a name="disable-enable-or-delete-a-profile"></a>Désactiver, activer ou supprimer un profil

Vous pouvez désactiver un profil existant afin que le Gestionnaire de trafic ne fait pas référence demandes de l’utilisateur aux points de terminaison configurés. Lorsque vous désactivez un profil le trafic Manager, le profil et les informations contenues dans le profil restent intactes et peuvent être modifiées dans l’interface du Gestionnaire de trafic.  Redirections reprendre lorsque vous réactivez le profil. Lorsque vous créez un profil Manager le trafic dans le portail classique Azure, il est automatiquement activée. Si vous décidez de qu'un profil n’est plus nécessaire, vous pouvez le supprimer.

### <a name="to-disable-a-profile"></a>Pour désactiver un profil

1. Si vous utilisez un nom de domaine personnalisé, modifiez l’enregistrement CNAME sur votre serveur DNS Internet pour qu’il pointe n’est plus à votre profil gestionnaire le trafic.
2. Arrêt du trafic redirigé vers les points de terminaison via les paramètres de profil du Gestionnaire de trafic.
3. Sélectionnez le profil que vous souhaitez désactiver. Dans la page Gestionnaire de trafic, mettez en surbrillance le profil en cliquant sur la colonne à côté du nom du profil. Note, en cliquant sur le nom du profil ou sur la flèche en regard du nom s’ouvre la page Paramètres pour le profil.
4. Après avoir sélectionné le profil, cliquez sur **désactiver** dans la partie inférieure de la page.

### <a name="to-enable-a-profile"></a>Pour activer un profil

1. Sélectionnez le profil que vous souhaitez désactiver. Dans la page Gestionnaire de trafic, mettez en surbrillance le profil en cliquant sur la colonne à côté du nom du profil. Note, en cliquant sur le nom du profil ou sur la flèche en regard du nom s’ouvre la page Paramètres pour le profil.
2. Après avoir sélectionné le profil, cliquez sur **Activer** en bas de la page.
3. Si vous utilisez un nom de domaine personnalisé, créez un enregistrement de ressource CNAME sur votre serveur DNS Internet pour pointer sur le nom de domaine de votre profil le trafic Manager.
4. Le trafic est dirigé vers les points de terminaison à nouveau.

### <a name="to-delete-a-profile"></a>Pour supprimer un profil

1. Assurez-vous que l’enregistrement de ressource DNS sur votre serveur DNS Internet utilise n’est plus un enregistrement de ressource CNAME qui pointe vers le nom de domaine de votre profil le trafic Manager.
2. Sélectionnez le profil que vous souhaitez désactiver. Dans la page Gestionnaire de trafic, mettez en surbrillance le profil en cliquant sur la colonne à côté du nom du profil. Note, en cliquant sur le nom du profil ou sur la flèche en regard du nom s’ouvre la page Paramètres pour le profil.
3. Après avoir sélectionné le profil, cliquez sur **Supprimer** dans la partie inférieure de la page.

## <a name="view-traffic-manager-profile-change-history"></a>Historique des modifications de profil Gestionnaire du trafic

Vous pouvez afficher l’historique des modifications pour votre profil gestionnaire le trafic dans le portail classique Azure dans la gestion des Services.

### <a name="to-view-your-traffic-manager-change-history"></a>Pour afficher votre historique des modifications du trafic Manager

1. Dans le volet gauche du portail Azure classique, cliquez sur **Gestion des Services**.
2. Dans la page Gestion des Services, cliquez sur **Les journaux d’opération**.
3. Dans la page journaux d’opération, vous pouvez filtrer pour afficher l’historique des modifications pour votre profil le trafic Manager. Après avoir sélectionné vos options de filtre, cliquez sur la case à cocher pour afficher les résultats.

   - Pour afficher les modifications pour tous vos profils, sélectionnez le laps de temps et votre abonnement, puis sélectionnez **Gestionnaire de trafic** dans le menu contextuel **Type** .
   - Pour filtrer par nom du profil, tapez le nom du profil dans le champ **Nom du Service** ou sélectionnez-le dans le menu contextuel.
   - Pour afficher les détails de chaque modification individuelle, sélectionnez la ligne comportant les modifications que vous souhaitez afficher, puis cliquez sur **Détails** dans la partie inférieure de la page. Dans la fenêtre **Détails de l’opération** , vous pouvez afficher la représentation XML de l’objet API qui a été créé ou mis à jour dans le cadre de l’opération.

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter un point de terminaison](traffic-manager-endpoints.md)
- [Configurer la méthode de routage basculement](traffic-manager-configure-failover-routing-method.md)
- [Configurer la méthode de routage alternées](traffic-manager-configure-round-robin-routing-method.md)
- [Configurer la méthode de routage performances](traffic-manager-configure-performance-routing-method.md)
- [Pointez sur un domaine Internet d’entreprise à un nom de domaine le trafic Manager](traffic-manager-point-internet-domain.md)
- [Résolution des problèmes le trafic gestionnaire est détérioré état](traffic-manager-troubleshooting-degraded.md)