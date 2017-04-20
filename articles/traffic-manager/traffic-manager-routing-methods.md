<properties
    pageTitle="Méthodes de routage de trafic Gestionnaire de trafic - | Microsoft Azure"
    description="Cet article vous aidera à comprendre les méthodes de routage de trafic différent utilisés par le trafic Manager"
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

# <a name="traffic-manager-traffic-routing-methods"></a>Méthodes de routage du trafic du Gestionnaire de trafic

Gestionnaire de trafic Azure prend en charge trois méthodes le routage du trafic pour déterminer comment router le trafic réseau vers les différents points de terminaison de service. Gestionnaire de trafic applique la méthode le routage du trafic à chaque requête DNS qu’il reçoit. La méthode le routage du trafic détermine quel point de terminaison renvoyés dans la réponse DNS.

La prise en charge du Gestionnaire de ressources Azure pour le trafic Manager utilise la terminologie autre que le modèle de déploiement classique. Le tableau suivant montre les différences entre le Gestionnaire de ressources et classique les conditions :

| Termes du Gestionnaire de ressources | Magasin de termes classique |
|-----------------------|--------------|
| Méthode de routage du trafic | Méthode de l’équilibrage de charge |
| Méthode de priorité | Méthode de basculement |
| Méthode pondérée | Méthode alternées |
| Méthode de performances | Méthode de performances |

Selon les commentaires des clients, nous avons modifié la terminologie pour améliorer la clarté et réduire malentendus courants. Il n’existe aucune différence en termes de fonctionnalité.

Il existe trois méthodes de routage de trafic disponibles dans le Gestionnaire de trafic :

- **Priorité :** Sélectionnez 'Priority' lorsque vous voulez utiliser un point de terminaison du service principal pour tout le trafic et fournir des sauvegardes au cas où le serveur principal ou les points de terminaison sauvegarde ne sont pas disponibles.
- **Pondérés :** Sélectionnez « pondérée » lorsque vous voulez répartir le trafic dans un jeu de points de terminaison, soit uniformément ou en fonction de grammages et que vous définissez.
- **Performances :** Sélectionnez « Performance » lorsque vous avez des points de terminaison dans différentes zones géographiques et que vous souhaitez aux utilisateurs finaux d’utiliser le point de terminaison « le plus proche » en ce qui concerne la latence du réseau les plus faibles.

Tous les profils Manager le trafic incluent surveillance de point de terminaison santé et basculement de point de terminaison automatique. Pour plus d’informations, voir [Le trafic Gestionnaire point de terminaison de surveillance](traffic-manager-monitoring.md). Un seul profil le trafic Gestionnaire pouvez utiliser uniquement une méthode de routage du trafic. Vous pouvez sélectionner une méthode de routage de trafic différent pour votre profil à tout moment. Les modifications sont appliquées au sein d’une minute et sans interruption de service est prévue. Méthodes de routage de trafic peuvent être combinées à l’aide de profils le trafic Gestionnaire imbriquées. Imbrication permet flexibles et élaborées configurations le routage du trafic répondant aux besoins des applications plus grandes et complexes. Pour plus d’informations, voir [les profils le trafic Gestionnaire imbriquée](traffic-manager-nested-profiles.md).

## <a name="priority-traffic-routing-method"></a>Méthode de routage du trafic priorité

Une organisation souhaite souvent prévoir la fiabilité ses services en déployant un ou plusieurs services de sauvegarde en cas de leur service principal vers le bas. La méthode de routage du trafic 'Priority' permet aux clients Azure facilement implémenter ce modèle de basculement.

![Gestionnaire de trafic Azure 'Priority' méthode le routage du trafic][1]

Le profil de trafic Manager contient une liste hiérarchisée des points de terminaison de service. Par défaut, le trafic Manager envoie tout le trafic au point de terminaison (plus haute priorité) principal. Si le point de terminaison principal n’est pas disponible, le trafic Gestionnaire achemine le trafic vers le deuxième point de terminaison. Si les deux extrémités principales et secondaires ne sont pas disponibles, le trafic accède au troisième et ainsi de suite. Disponibilité du point de terminaison est basée sur l’état configuré (activé ou désactivé) et le suivi de point de terminaison en cours.

### <a name="configuring-endpoints"></a>Configuration des points de terminaison

Avec le Gestionnaire de ressources Azure, vous configurez la priorité de point de terminaison explicitement à l’aide de la propriété 'priority' pour chaque point de terminaison. Cette propriété est une valeur comprise entre 1 et 1000. Valeurs les plus basses représentent une priorité plus élevée. Points de terminaison ne peuvent pas partager les valeurs de priorité. Définition de la propriété est facultative. S’il est omis, une priorité par défaut en fonction de l’ordre de point de terminaison est utilisée.

Avec l’interface classique, la priorité de point de terminaison est configurée implicitement. La priorité est basée sur l’ordre dans lequel les points de terminaison sont répertoriés dans la définition de profil.

## <a name="weighted-traffic-routing-method"></a>Méthode de routage du trafic pondérée

La méthode « Pondérée » le routage du trafic vous permet d’uniformiser la largeur du trafic ou d’utiliser une pondération prédéfinie.

![Méthode de routage du trafic 'Pondérée' Azure le trafic Gestionnaire][2]

Dans la méthode pondérée le routage du trafic, vous attribuez une épaisseur à chaque point de terminaison de la configuration du profil Gestionnaire de trafic. L’épaisseur est un nombre entier compris entre 1 et 1000. Ce paramètre est facultatif. En cas d’omission, le trafic responsables utilise un poids par défaut de « 1 ».

Pour chaque requête DNS reçu, le trafic Gestionnaire choisit de manière aléatoire un point de terminaison disponible. La probabilité de choix d’un point de terminaison est basée sur des poids affectés à tous les points de terminaison disponibles. À l’aide de la même poids sur tous les résultats de points de terminaison dans une distribution le trafic pair. L’utilisation de poids supérieur ou inférieurs sur les points de terminaison spécifiques provoque ces points de terminaison à renvoyer plus ou moins fréquemment dans les réponses DNS.

La méthode pondérée permet des scénarios utiles :

- Mise à niveau de l’application progressive : affecter un pourcentage de trafic pour acheminer les messages vers un point de terminaison et augmentez progressivement le trafic au fil du temps à 100 %.
- Migration des applications vers Azure : créez un profil avec points de terminaison Azure et externes. Ajuster l’épaisseur des points de terminaison à préférer les nouveaux points de terminaison.
- Rupture cloud pour une capacité supplémentaire : développer rapidement un déploiement local dans le nuage en le plaçant derrière un profil Manager le trafic. Lorsque vous avez besoin d’une capacité supplémentaire dans le cloud, vous pouvez ajouter ou activer plusieurs points de terminaison et spécifier quelle partie du trafic accède à chaque point de terminaison.

Le nouveau portail Azure prend en charge la configuration de l’acheminement du trafic pondéré. Poids ne peut pas être configurés dans le portail classique. Vous pouvez également configurer poids à l’aide du Gestionnaire de ressources et les versions classiques de PowerShell Azure infrastructure du langage commun et des API REST.

Il est important de comprendre que les réponses DNS sont mis en cache par les clients et par les serveurs DNS récursives que les clients utilisent pour résoudre les noms DNS. Cette mise en cache peut avoir un impact sur la distribution du trafic pondérée. Lorsque le nombre de clients et les serveurs DNS récursive est important, la distribution du trafic fonctionne comme prévu. Toutefois, lorsque le nombre de clients ou serveurs DNS récursive est petit, la mise en cache pouvez incliner considérablement la distribution du trafic.

Scénarios d’utilisation courants sont les suivantes :

- Environnements de test et de développement
- Communications d’application à
- Applications visant à une base d’utilisateur étroite qui partagent une infrastructure DNS récursive courantes (par exemple, les employés de la société via un proxy)

Ces effets de mise en cache DNS sont communes à tous les systèmes routage le trafic DNS, les pas seulement le Gestionnaire de trafic Azure. Dans certains cas, explicitement vider le cache DNS peut-être fournir une solution de contournement. Dans les autres cas, une autre méthode le routage du trafic peut être plus appropriée.

## <a name="performance-traffic-routing-method"></a>Méthode de routage du trafic de performances

Déploiement des points de terminaison dans deux ou plusieurs emplacements dans le monde peut améliorer la réactivité de nombreuses applications en routant le trafic vers l’emplacement « le plus proche » pour vous. La méthode de routage du trafic 'Performances' fournit cette fonctionnalité.

![Gestionnaire de trafic Azure 'Performances' méthode le routage du trafic][3]

Le point de terminaison « le plus proche » n’est pas nécessairement le plus proche en termes de distance géographique. À la place, la méthode de routage du trafic 'Performances' détermine le point de terminaison le plus proche en mesure de la latence réseau. Gestionnaire de trafic gère une Table de latence Internet pour effectuer le suivi de l’heure aller-retour entre les plages d’adresses IP et chaque centre de données Azure.

Gestionnaire de trafic recherche l’adresse IP source de la requête entrante DNS dans la Table de latence Internet. Gestionnaire de trafic choisit un point de terminaison disponible dans le centre de données Azure qui comporte la plus basse latence pour cette plage d’adresses IP, puis renvoie ce point de terminaison de la réponse DNS.

Comme indiqué dans le [Fonctionnement du Gestionnaire de trafic](traffic-manager-how-traffic-manager-works.md), le trafic gestionnaire ne reçoit pas de requêtes DNS directement à partir de clients. Au lieu de cela, les requêtes DNS proviennent du service DNS récursives que les clients sont configurés pour utiliser. Par conséquent, l’adresse IP utilisée pour déterminer le point de terminaison « le plus proche » n’est pas l’adresse IP du client, mais il est l’adresse IP du service DNS récursive. Dans la pratique, cette adresse IP est un bonne proxy pour le client.

Gestionnaire de trafic met régulièrement à jour la Table de latence Internet pour prendre en compte les modifications dans le Internet global et les nouvelles zones Azure. Toutefois, performances de l’application varient en fonction des écarts en temps réel de charge via Internet. Performances-le routage du trafic ne contrôle pas la charge sur un point de terminaison du service donné. Toutefois, si un point de terminaison n’est plus disponible, le trafic Manager n’existe pas dans les réponses de requêtes DNS.

Points à noter :

- Si votre profil contient plusieurs points de terminaison dans la même région Azure, puis le trafic Gestionnaire répartit le trafic uniformément sur les points de terminaison disponibles dans cette zone. Si vous préférez une distribution trafic différent au sein d’une région, vous pouvez utiliser [imbriquée profils de trafic Manager](traffic-manager-nested-profiles.md).

- Si tous les points de terminaison activés dans une région Azure donnée sont dégradées, le trafic Gestionnaire répartit le trafic sur tous les autres points de terminaison disponibles à la place du point de terminaison le plus proche suivant. Cette logique empêche un échec en cascade par surcharge ne pas le point de terminaison le plus proche suivant. Si vous voulez définir une séquence de basculement préféré, utiliser [des profils le trafic Gestionnaire imbriquée](traffic-manager-nested-profiles.md).

- Lorsque vous utilisez la méthode de routage de trafic performances avec des points de terminaison externes ou des points de terminaison imbriquées, vous devez spécifier l’emplacement de ce point de terminaison. Choisir le plus proche de votre déploiement de région Azure. Ces emplacements sont les valeurs pris en charge par la Table de latence Internet.

- L’algorithme de choisit le point de terminaison est déterministe. Requêtes DNS répétées du même client sont dirigés vers le même point de terminaison. En règle générale, les clients utilisent serveurs DNS récursive différents lors de vos déplacements. Le client peut-être être routé vers un autre point de terminaison. Routage peut également être affecté à mises à jour la Table de latence Internet. Par conséquent, la méthode de routage du trafic performances ne garantit pas qu’un client est toujours acheminé au même point de terminaison.

- Lorsque la Table de latence Internet est modifiée, vous pouvez remarquer que certains clients sont dirigées vers un autre point de terminaison. Cette modification routage est plus précise en fonction des données de latence en cours. Ces mises à jour sont essentiels pour assurer la précision de performances-le routage du trafic que Internet évolue en permanence.

## <a name="next-steps"></a>Étapes suivantes

Apprenez à développer des applications à haute disponibilité à l’aide du [Gestionnaire de trafic point de terminaison surveillance](traffic-manager-monitoring.md)

Découvrez comment [créer un profil le trafic Manager](traffic-manager-manage-profiles.md)

<!--Image references-->
[1]: ./media/traffic-manager-routing-methods/priority.png
[2]: ./media/traffic-manager-routing-methods/weighted.png
[3]: ./media/traffic-manager-routing-methods/performance.png
