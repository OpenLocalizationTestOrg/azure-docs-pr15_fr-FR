<properties
    pageTitle="Considérations relatives aux performances pour le Gestionnaire de trafic Azure | Microsoft Azure"
    description="Comprendre les performances sur le trafic responsable et comment tester les performances de votre site Web lorsque vous utilisez le trafic Manager"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="performance-considerations-for-traffic-manager"></a>Remarques sur les performances du Gestionnaire de trafic

Cette page explique les considérations relatives aux performances à l’aide du Gestionnaire de trafic. Considérez le scénario suivant :

Vous avez des instances de votre site Web dans les régions WestUS et EastAsia. Une des instances ignore la vérification au niveau de la sonde de gestionnaire le trafic. Application le trafic est dirigé vers la zone correcte. Ce basculement est attendu mais performances peuvent être un problème en fonction de la latence du trafic maintenant voyager à une zone distante.

## <a name="how-traffic-manager-works"></a>Comment fonctionne le trafic Manager

L’impact des performances uniquement le trafic Gestionnaire pouvant avoir sur votre site Web est la recherche DNS initiale. Une requête DNS pour le nom de votre profil le trafic Manager est gérée par le serveur DNS Microsoft racine qui héberge la zone trafficmanager.net. Le trafic responsable remplit et met à jour régulièrement, en fonction de la stratégie du Gestionnaire de trafic et les résultats de la sonde serveurs de Microsoft DNS racine. Donc, même au cours de la recherche DNS initiale, les requêtes DNS sont envoyés vers le Gestionnaire de trafic.

Gestionnaire de trafic est constitué de plusieurs composants : serveurs, un service d’API, la couche de stockage et un point de terminaison analyse du service de noms DNS. Si un composant de service Manager le trafic échoue, il n’existe aucun effet sur le nom DNS associé à votre profil Manager le trafic. Les enregistrements dans les serveurs DNS Microsoft restent inchangées. Toutefois, point de terminaison de surveillance et mise à jour de DNS ne sont pas effectuées. Par conséquent, le trafic gestionnaire n’est pas en mesure de mettre à jour DNS pointe vers votre site de basculement lorsque votre site principal s’arrête.

Résolution de noms DNS est rapide et les résultats sont mis en cache. La vitesse de la recherche DNS initiale dépend des serveurs DNS qu'utilise le client de résolution de noms. En règle générale, un client sont habilités à effectuer une recherche DNS au sein de ~ 50 ms. Les résultats de la recherche sont mis en cache pour la durée de la DNS Time-to-live (TTL). La valeur par défaut pour le Gestionnaire de trafic TTL est 300 secondes.

Le trafic n’est pas transmis via le Gestionnaire de trafic. Une fois que la recherche DNS est terminée, le client possède une adresse IP d’une instance de votre site web. Le client se connecte directement à cette adresse et ne correspond pas à l’aide du trafic Manager. La stratégie du Gestionnaire de trafic choisie n’a aucune influence sur les performances de DNS. Toutefois, une méthode routage performances peut impact négatif sur l’expérience de l’application. Par exemple, si votre stratégie redirige le trafic en Amérique du Nord sur une instance hébergée en Asie, la latence du réseau pour les sessions peut être un problème de performance.

## <a name="measuring-traffic-manager-performance"></a>Mesurer les performances du trafic Manager

Il existe plusieurs sites Web, que vous pouvez utiliser pour comprendre les performances et le comportement d’un profil Manager le trafic. La plupart de ces sites sont gratuites, mais peuvent comporter des limites. Certains sites proposent améliorée de surveillance et de création de rapports pour un abonnement.

Les outils sur ces sites mesure DNS latence et affiche la résolu adresses IP pour les emplacements de client dans le monde entier. La plupart d'entre eux ne pas mettre en cache les résultats DNS. Par conséquent, les outils affichent la recherche DNS complète chaque fois qu'un test est exécuté. Lorsque vous testez à partir de votre propre client, vous rencontrez uniquement les performances de recherche DNS complète une fois pendant la durée de vie.

## <a name="sample-tools-to-measure-dns-performance"></a>Outils d’exemple pour mesurer les performances de DNS

- [SolveDNS](http://www.solvedns.com/dns-comparison/)

    SolveDNS propose de nombreux outils de performances. L’outil de comparaison DNS peut afficher le temps nécessaire pour résoudre votre nom de DNS et comment qui compare à d’autres fournisseurs de service DNS.

- [WebSitePulse](http://www.websitepulse.com/help/tools.php)

    Un des outils la plus simple est WebSitePulse. Entrez l’URL pour afficher le temps de résolution DNS, le premier octet, dernier octet et autres statistiques sur les performances. Vous pouvez choisir parmi trois emplacements différents test. Dans cet exemple, vous constatez que la première exécution indique que la recherche DNS prend 0.204 s.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Étant donné que les résultats sont mis en cache, le second test du point de terminaison le trafic gestionnaire même la recherche DNS prend sec 0,002.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

- [Autorité de certification synthétique application Moniteur](https://asm.ca.com/en/checkit.php)

    Auparavant appelé l’outil Watchmouse à cocher site Web, ce site montrent le temps de résolution DNS provenant de plusieurs régions géographiques simultanément. Entrez l’URL pour afficher le temps de résolution DNS, heure de la connexion et la vitesse de plusieurs emplacements géographiques. Ce test permet de voir quel service hébergé est renvoyée pour différents emplacements dans le monde entier.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

- [Pingdom](http://tools.pingdom.com/)

    Cet outil fournit des statistiques de performance pour chaque élément d’une page web. L’onglet analyse de la Page affiche le pourcentage de temps passé sur la recherche DNS.

- [Quel est mon DNS ?](http://www.whatsmydns.net/)

    Ce site effectue une recherche DNS à partir de 20 emplacements et affiche les résultats sur une carte.

- [Retrouvez Interface Web](http://www.digwebinterface.com)

    Ce site affiche plus d’informations DNS, y compris les enregistrements CNAME et enregistrements A. Vérifiez que vous vérifiez le 'Option Redéfinir sortie' et le « Statistiques » sous options, puis sélectionnez « Tout » sous serveurs de noms.

## <a name="next-steps"></a>Étapes suivantes

[À propos du Gestionnaire de trafic du trafic méthodes de routage](traffic-manager-routing-methods.md)

[Testez vos paramètres de trafic Manager](traffic-manager-testing-settings.md)

[Opérations sur le trafic Manager (référence des API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Applets de commande Gestionnaire de trafic Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)
