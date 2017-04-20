<properties
   pageTitle="Configurer le trafic Gestionnaire basculement le trafic routage méthode | Microsoft Azure"
   description="Cet article vous aidera à configurer la méthode de routage le trafic basculement dans le Gestionnaire de trafic"
   services="traffic-manager"
   documentationCenter=""
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

# <a name="configure-failover-routing-method"></a>Configurer la méthode de routage basculement

Une organisation souhaite souvent prévoir la fiabilité de ses services. Pour cela, fournir des services de sauvegarde en cas de leur service principal vers le bas. Un modèle commun pour le basculement de service consiste à fournir un ensemble de services identiques acheminer le trafic vers un service principal, tout en conservant une liste configurée d’un ou plusieurs services de sauvegarde. Vous pouvez configurer ce type de sauvegarde avec les services en nuage Azure et les sites Web en suivant les procédures ci-dessous.

Notez que des sites Web Azure fournit déjà le trafic basculement routage des fonctionnalités de méthode pour les sites Web dans un centre de données (également appelé une région), quel que soit le mode de site Web. Gestionnaire de trafic permet de vous permettent de spécifier la méthode de routage de trafic basculement pour les sites Web dans différents centres de données.

## <a name="to-configure-failover-traffic-routing-method"></a>Pour configurer la méthode de routage le trafic basculement :

1. Dans le portail classique Azure, dans le volet gauche, cliquez sur l’icône **Gestionnaire de trafic** pour ouvrir le volet Gestionnaire de trafic. Si vous n’avez pas encore créé votre profil le trafic Manager, voir [Gérer les profils de trafic Gestionnaire](traffic-manager-manage-profiles.md) pour obtenir la procédure pour créer un profil de gestionnaire le trafic de base.
2. Dans le volet Gestionnaire le trafic dans le portail classique Azure, recherchez le profil de trafic gestionnaire qui contient les paramètres que vous souhaitez modifier, puis cliquez sur la flèche située à droite du nom du profil. Ceci ouvre la page Paramètres pour le profil.
3. Dans votre page de profil, cliquez sur les **points de terminaison** en haut de la page et vérifiez que les deux services cloud et sites Web (points de terminaison) que vous souhaitez inclure dans votre configuration sont présents. Pour obtenir la procédure Ajouter ou supprimer des points de terminaison, voir [Gérer les points de terminaison de dans le Gestionnaire de trafic](traffic-manager-endpoints.md).
4. Dans votre page de profil, cliquez sur **configurer** dans la partie supérieure pour ouvrir la page de configuration.
5. Pour les **paramètres de la méthode routage le trafic**, vérifiez que la méthode d’acheminement du trafic est **basculement**. Si elle n’est pas le cas, cliquez sur **Basculer** dans la liste déroulante.
6. Pour la **Liste priorité basculement**, ajustez le basculement afin que vos points de terminaison. Lorsque vous sélectionnez la méthode de routage de trafic **basculement** , l’ordre des points de terminaison sélectionné est également importante. Le point de terminaison principal se trouve au-dessus. Utilisez le haut et bas pour modifier l’ordre selon vos besoins. Pour plus d’informations sur la façon de définir la priorité de basculement à l’aide de Windows PowerShell, voir [Set-AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Vérifiez que les **Paramètres de surveillance** sont correctement configurés. Surveillance garantit que les points de terminaison qui sont en mode hors connexion ne sont pas envoyés le trafic. Afin de contrôler les points de terminaison, vous devez spécifier un chemin d’accès et le nom de fichier. Notez qu’une barre oblique « / » est une entrée valide pour le chemin d’accès relatif et indique que le fichier se trouve dans le répertoire racine (par défaut). Pour plus d’informations sur le contrôle, voir [Analyse du trafic Gestionnaire](traffic-manager-monitoring.md).
8. Après avoir effectué vos modifications de configuration, cliquez sur **Enregistrer** dans la partie inférieure de la page.
9. Tester les modifications dans votre configuration. Pour plus d’informations, voir [Paramètres du Gestionnaire de trafic test](traffic-manager-testing-settings.md) .
10. Une fois que votre profil le trafic Manager est le programme d’installation et l’utilisation, modifiez l’enregistrement DNS sur votre serveur DNS faisant autorité pour pointez sur le nom de domaine de votre entreprise le nom de domaine gestionnaire le trafic. Pour plus d’informations, voir [Point un domaine à un domaine Manager le trafic Internet d’entreprise](traffic-manager-point-internet-domain.md).

## <a name="next-steps"></a>Étapes suivantes

[Pointez sur un domaine Internet d’entreprise un domaine le trafic Manager](traffic-manager-point-internet-domain.md)

[Méthodes de routage de trafic Manager](traffic-manager-routing-methods.md)

[Configurer la méthode de routage alternées](traffic-manager-configure-round-robin-routing-method.md)

[Configurer la méthode de routage performances](traffic-manager-configure-performance-routing-method.md)

[Résolution des problèmes le trafic gestionnaire est détérioré état](traffic-manager-troubleshooting-degraded.md)

[Le trafic Gestionnaire - désactiver, activer ou supprimer un profil](disable-enable-or-delete-a-profile.md)

[Gestionnaire de trafic - désactiver ou activer un point de terminaison](disable-or-enable-an-endpoint.md)

