<properties
   pageTitle="Configurer la méthode de routage le trafic performances | Microsoft Azure"
   description="Cet article vous aidera à configurer la méthode de routage le trafic performances dans le Gestionnaire de trafic"
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

# <a name="configure-performance-traffic-routing-method"></a>Configurer la méthode de routage le trafic performances

Pour acheminer le trafic de services de cloud et les sites Web (points de terminaison) qui sont trouvent dans différents centres de données dans le monde (également appelé régions), vous pouvez diriger le trafic entrant au point de terminaison avec la latence la plus faible émise par le client. En règle générale, le centre de données avec la latence la plus faible correspond à la plus proche dans la distance géographique. La méthode de routage de trafic performances vous permettra de distribution en fonction de latence la plus faible, mais ne peut pas prendre en compte les changements en temps réel dans la configuration du réseau ou les charger. Pour plus d’informations sur les méthodes de routage de trafic différent Azure le trafic Manager offre, voir [méthodes le routage du trafic à propos du Gestionnaire de trafic](traffic-manager-routing-methods.md).

## <a name="route-traffic-based-on-lowest-latency-across-a-set-of-endpoints"></a>Acheminer le trafic en fonction de latence les plus basses au sein d’un ensemble de points de terminaison :

1. Dans le portail classique Azure, dans le volet gauche, cliquez sur l’icône **Gestionnaire de trafic** pour ouvrir le volet Gestionnaire de trafic. Si vous n’avez pas encore créé votre profil le trafic Manager, voir [Gérer les profils de trafic Gestionnaire](traffic-manager-manage-profiles.md) pour connaître les étapes pour créer un profil de gestionnaire le trafic de base.
2. Dans le portail classique Azure, dans le volet Gestionnaire le trafic, recherchez le profil de trafic gestionnaire qui contient les paramètres que vous souhaitez modifier, puis cliquez sur la flèche située à droite du nom du profil. Ceci ouvre la page Paramètres pour le profil.
3. Dans la page de votre profil, cliquez sur les **points de terminaison** en haut de la page et vérifiez que les points de terminaison de service que vous souhaitez inclure dans votre configuration sont présents. Pour obtenir la procédure Ajouter ou supprimer des points de terminaison de votre profil, voir [Gérer les points de terminaison de dans le Gestionnaire de trafic](traffic-manager-endpoints.md).
4. Dans la page de votre profil, cliquez sur **configurer** dans la partie supérieure pour ouvrir la page de configuration.
5. Pour les **paramètres de la méthode routage le trafic**, vérifiez que la méthode d’acheminement du trafic est * *performances*. Si elle n’est pas le cas, cliquez sur * *performances** dans la liste déroulante.
6. Vérifiez que les **Paramètres de surveillance** sont correctement configurés. Surveillance garantit que les points de terminaison qui sont en mode hors connexion ne sont pas envoyés le trafic. Afin de contrôler les points de terminaison, vous devez spécifier un chemin d’accès et le nom de fichier. Notez qu’une barre oblique « / » est une entrée valide pour le chemin d’accès relatif et indique que le fichier se trouve dans le répertoire racine (par défaut). Pour plus d’informations sur l’analyse, voir [à propos du trafic Gestionnaire surveillance](traffic-manager-monitoring.md).
7. Après avoir effectué vos modifications de configuration, cliquez sur **Enregistrer** dans la partie inférieure de la page.
8. Tester les modifications dans votre configuration. Pour plus d’informations, voir [Paramètres du Gestionnaire de trafic test](traffic-manager-testing-settings.md).
9. Une fois que votre profil le trafic Manager est le programme d’installation et l’utilisation, modifiez l’enregistrement DNS sur votre serveur DNS faisant autorité pour pointez sur le nom de domaine de votre entreprise le nom de domaine gestionnaire le trafic. Pour plus d’informations, voir [Point un domaine à un domaine Manager le trafic Internet d’entreprise](traffic-manager-point-internet-domain.md).

## <a name="next-steps"></a>Étapes suivantes


[Pointez sur un domaine Internet d’entreprise un domaine le trafic Manager](traffic-manager-point-internet-domain.md)

[Méthodes de routage de trafic Manager](traffic-manager-routing-methods.md)

[Configurer la méthode de routage basculement](traffic-manager-configure-failover-routing-method.md)

[Configurer la méthode de routage alternées](traffic-manager-configure-round-robin-routing-method.md)

[Résolution des problèmes le trafic gestionnaire est détérioré état](traffic-manager-troubleshooting-degraded.md)

[Le trafic Gestionnaire - désactiver, activer ou supprimer un profil](disable-enable-or-delete-a-profile.md)

[Gestionnaire de trafic - désactiver ou activer un point de terminaison](disable-or-enable-an-endpoint.md)

