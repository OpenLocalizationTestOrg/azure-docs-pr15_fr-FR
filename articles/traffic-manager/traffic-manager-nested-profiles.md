<properties
    pageTitle="Imbriqués profils le trafic Manager | Microsoft Azure"
    description="Cet article explique la fonctionnalité imbriquées les profils du Gestionnaire de trafic Azure"
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

# <a name="nested-traffic-manager-profiles"></a>Profils le trafic Gestionnaire imbriquées

Gestionnaire de trafic inclut une gamme de méthodes le routage du trafic qui vous permettent de contrôler la façon dont le trafic Gestionnaire sélectionne quel point de terminaison devant recevoir le trafic de chaque utilisateur final. Pour plus d’informations, voir [méthodes de routage du trafic du Gestionnaire de trafic](traffic-manager-routing-methods.md).

Chaque profil gestionnaire le trafic indique une méthode unique le routage du trafic. Toutefois, il existe des scénarios qui requièrent l’acheminement du trafic plus sophistiqués que le routage fournies par un seul profil gestionnaire le trafic. Vous pouvez imbriquer des profils de trafic Manager pour combiner les avantages de plusieurs méthodes de routage du trafic. Profils imbriquées permettent de modifier le comportement du trafic gestionnaire par défaut au support plus grande et les déploiements d’applications plus complexes.

Les exemples suivants montrent comment utiliser les profils de trafic Gestionnaire imbriquées dans différents scénarios.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Exemple 1 : Combinaison « Performances » et « Weighted » le routage du trafic

Supposons que vous avez déployé une application dans les régions Azure suivants : États-Unis Ouest, ouest Europe et Asie de l’est. Méthode de routage du trafic du responsable le trafic 'Performances' vous permet de distribuer le trafic vers la zone plus proche de l’utilisateur.

![Profil unique le trafic Manager][1]

À présent, supposons que vous souhaitez tester une mise à jour à votre service avant de le déployer plus largement. Vous souhaitez utiliser la méthode « pondérée » le routage du trafic pour diriger un petit pourcentage du trafic vers votre déploiement de test. Vous configurez le déploiement de test en parallèle avec le déploiement de production existant en Europe ouest.

Vous ne pouvez pas combiner les deux « Weighted » et « performances-le routage du trafic dans un seul profil. Pour prendre en charge ce scénario, vous créez un profil de gestionnaire le trafic à l’aide de deux points de terminaison Europe ouest et la méthode de routage du trafic « Weighted ». Ensuite, vous ajoutez ce profil « enfants » comme un point de terminaison au profil « parent ». Le profil parent utilise la méthode de routage du trafic performances toujours et contient les autres déploiements globales comme points de terminaison.

Le diagramme suivant illustre cet exemple :

![Profils le trafic Gestionnaire imbriquées][2]

Dans cette configuration, le trafic dirigé via le profil parent répartit le trafic sur régions normalement. Au sein de l’Europe ouest, le profil imbriqué distribue le trafic vers les points de terminaison de production et de test en fonction des poids affectés.

Lorsque le profil parent utilise la méthode de routage du trafic 'Performances', chaque point de terminaison doit être affectée à un emplacement. L’emplacement est affecté lorsque vous configurez le point de terminaison. Choisir le plus proche de votre déploiement de région Azure. Les zones Azure sont les valeurs emplacement pris en charge par la Table de latence Internet. Pour plus d’informations, voir [le trafic gestionnaire 'Performances' méthode le routage du trafic](traffic-manager-routing-methods.md#performance-traffic-routing-method).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Exemple 2 : Surveillance de point de terminaison dans les profils imbriquées

Le trafic Manager surveille activement l’état de chaque point de terminaison de service. Si un point de terminaison ne fonctionne pas correctement, le trafic Gestionnaire dirige aux utilisateurs de points de terminaison de remplacement pour préserver la disponibilité de votre service. Ce comportement de surveillance et de basculement de point de terminaison s’applique à toutes les méthodes de routage du trafic. Pour plus d’informations, voir [Le trafic Gestionnaire point de terminaison de surveillance](traffic-manager-monitoring.md). Point de terminaison de surveillance diffère les profils imbriquées. Avec les profils imbriquées, le profil parent n’effectue pas vérifications au niveau de l’enfant directement. À la place, l’intégrité des points de terminaison du profil de l’enfant est utilisée pour calculer la santé générale du profil enfant. Ces informations d’intégrité sont propagées haut de la hiérarchie de profil imbriquée. Le profil parent Ceci agrégé santé pour déterminer s’il faut diriger le trafic vers le profil de l’enfant. Consultez la section [Forum aux questions](#faq) de cet article pour en savoir plus sur le contrôle d’état des profils imbriquées.

Retournez à l’exemple précédent, supposons que le déploiement de production en Europe ouest échoue. Par défaut, le profil « enfants » dirige tout le trafic vers le déploiement de test. Si le déploiement de test échoue également, le profil parent détermine que le profil enfant ne doit pas recevoir le trafic dans la mesure où tous les points de terminaison enfant sont incorrects. Ensuite, le profil parent distribue le trafic vers les autres régions.

![Basculement de profil imbriquée (comportement par défaut)][3]

Vous serez peut-être satisfait de cette disposition. Ou vous intéresser que tout le trafic pour l’Europe ouest va maintenant le déploiement de test au lieu de trafic de sous-ensemble limité. Quelle que soit l’état du déploiement test, que vous voulez basculer vers les autres régions en cas d’échec du déploiement en production en Europe ouest. Pour activer ce basculement, vous pouvez spécifier le paramètre « MinChildEndpoints » lorsque vous configurez le profil enfant comme un point de terminaison dans le profil parent. Le paramètre détermine le nombre minimal de points de terminaison disponibles dans le profil de l’enfant. La valeur par défaut est « 1 ». Dans ce scénario, vous définissez la valeur MinChildEndpoints 2. Sous ce seuil, le profil parent considère le profil tout enfant à ne pas être disponibles et achemine le trafic aux autres points de terminaison.

La figure suivante illustre cette configuration :

![Imbriqués basculement profil avec 'MinChildEndpoints' = 2][4]

>[AZURE.NOTE]
>La méthode de routage du trafic 'Priority' répartit tout le trafic vers un point de terminaison unique. Ainsi il est peu utile un paramètre MinChildEndpoints différent de « 1 » pour un profil enfant.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Exemple 3 : Priorités basculement régions dans le routage du trafic 'Performances'.

Le comportement par défaut pour la méthode de routage du trafic 'Performances' est conçu pour éviter trop chargement suivant le plus proche du point de terminaison et à l’origine d’une série d’échecs en cascade. Lorsqu’un point de terminaison échoue, tout le trafic qui serait ont été redirigé vers ce point de terminaison est réparti uniformément aux autres points de terminaison dans toutes les régions.

![Trafic 'Performances' routage avec basculement par défaut][5]

Toutefois, supposons que vous préférez le basculement le trafic Europe ouest US ouest, uniquement diriger le trafic vers d’autres régions lorsque les deux extrémités ne sont pas disponibles. Vous pouvez créer cette solution à l’aide d’un profil enfant avec la méthode de routage du trafic 'Priority'.

![Trafic 'Performances' routage avec basculement préférentiel][6]

Dans la mesure où le point de terminaison Europe Ouest a priorité plus élevée que le point de terminaison ouest US, tout le trafic est envoyé au point de terminaison Europe ouest lorsque les deux extrémités sont en ligne. Si Europe ouest échoue, le trafic est dirigé vers US ouest. Avec le profil imbriqué, le trafic est dirigé vers Asie de l’est uniquement lorsque Europe ouest et États-Unis Ouest échouent.

Vous pouvez répéter ce modèle pour toutes les régions. Remplacez toutes les trois points de terminaison dans le profil parent avec trois profils enfant, qui fournissent une séquence de basculement hiérarchisée chacune.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Exemple 4 : Contrôle 'Performances' le routage du trafic entre plusieurs points de terminaison dans la même région

Supposons que les performances méthode le routage du trafic est utilisée dans un profil qui comporte plus d’un point de terminaison dans une région particulière. Par défaut, le trafic dirigé vers cette région est réparti uniformément entre tous les points de terminaison disponibles dans cette zone.

![Trafic 'Performances' routage de distribution dans la région du trafic (comportement par défaut)][7]

Au lieu d’ajouter plusieurs points de terminaison en Europe ouest, ces points de terminaison placés entre un profil enfant séparée. Le profil enfant est ajouté au parent comme point de terminaison uniquement en Europe ouest. Les paramètres dans le profil enfant peuvent contrôler la distribution du trafic avec Europe ouest en activant le routage du trafic basé sur des priorités ou pondéré dans cette zone.

![Trafic 'Performances' routage avec une distribution de trafic dans la région personnalisé][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Exemple 5 : Paramètres de contrôle par point de terminaison

Supposons que vous utilisez le trafic gestionnaire pour passer facilement le trafic à partir d’un classique locale site web vers une nouvelle version sur le nuage hébergée dans Azure. Pour l’ancien site, que vous souhaitez utiliser la page d’accueil URI pour surveiller l’intégrité du site. Mais pour la nouvelle version sur le nuage, vous implémentez une page de surveillance personnalisée (chemin d’accès « / monitor.aspx ») qui inclut des contrôles supplémentaires.

![Point de terminaison le trafic Gestionnaire surveillance (comportement par défaut)][9]

Les paramètres de contrôle dans un profil le trafic Manager s’appliquent à tous les points de terminaison dans un seul profil. Avec les profils imbriquées, vous utilisez un profil enfants différents par site pour définir les paramètres de surveillance différents.

![Point de terminaison le trafic Gestionnaire surveillance avec les paramètres de per point de terminaison][10]

## <a name="faq"></a>FAQ

### <a name="how-do-i-configure-nested-profiles"></a>Comment configurer les profils imbriquées ?

Profils de gestionnaire le trafic imbriqués peuvent être configurés à l’aide du Gestionnaire de ressources Azure et classique API REST Azure, applets de commande PowerShell Azure et les commandes de Azure infrastructure du langage commun disponibilité sur plusieurs plateformes. Ils sont également pris en charge via le nouveau portail Azure. Ils ne sont pas pris en charge dans le portail classique.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Le nombre de couches d’imbrication est le trafic Manager prennent en charge ?

Vous pouvez imbriquer des profils jusqu'à 10 niveaux. « Boucles » ne sont pas autorisés.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Puis-je combiner des autres types de point de terminaison avec les profils enfants imbriqués, dans le même profil le trafic Manager ?

Oui. Il n’existe aucune restriction sur comment combiner des points de terminaison de types différents au sein d’un profil.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Comment le modèle de facturation applique-t-il les profils imbriquées ?

Il est non négatif tarifs impact de l’utilisation des profils imbriquées.

Gestionnaire de trafic facturation présente deux composants : vérifications au niveau de point de terminaison et des millions de requêtes DNS

- Vérifications au niveau de point de terminaison : il n’est pas facturé pour un profil enfant lorsqu’il est configuré comme un point de terminaison dans un profil parent. Analyse des points de terminaison dans le profil enfant sont facturé de la manière habituelle.
- Requêtes DNS : chaque requête est comptée uniquement une fois. Une requête sur un profil parent qui retourne un point de terminaison à partir d’un profil enfant est comptée par rapport au profil parent uniquement.

Pour plus d’informations, consultez la [page tarification du Gestionnaire de trafic](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Existe-t-il un impact sur les performances des profils imbriquées ?

Non. Il n’existe aucun impact sur les performances encourus lors de l’utilisation des profils imbriquées.

Les serveurs de noms gestionnaire le trafic parcourent la hiérarchie des profil en interne lors du traitement de chaque requête DNS. Une requête DNS pour un profil parent peut recevoir une réponse DNS avec un point de terminaison à partir d’un profil enfant. Un seul enregistrement CNAME est utilisé si vous utilisez un seul profil ou profils imbriquées. Il est inutile de créer un enregistrement CNAME pour chaque profil dans la hiérarchie.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Comment le trafic Gestionnaire calculer l’état d’un point de terminaison imbriqué dans un profil parent ?

Le profil parent n’effectue pas directement les vérifications au niveau de l’enfant. En revanche, l’intégrité des points de terminaison du profil de l’enfant sont utilisées pour calculer la santé générale du profil enfant. Ces informations sont propagées haut de la hiérarchie de profil imbriquées pour déterminer l’état du point de terminaison imbriquée. Le profil parent utilise cet état agrégé pour déterminer si le trafic peut être dirigé vers l’enfant.

Le tableau suivant décrit le comportement du Gestionnaire de trafic vérifications pour un point de terminaison imbriquée.

|Enfant profil surveiller l’état|État de point de terminaison moniteur parent|Notes|
|---|---|---|
|Désactivé. Le profil enfant a été désactivé.|Arrêté|L’état de point de terminaison parent est arrêté, ne pas désactivé. L’état désactivé est réservée aux indiquant que vous avez désactivé le point de terminaison dans le profil parent.|
|Dégradé. Point de terminaison enfant au moins un profil est dans un état dégradé.| Online : le nombre de points de terminaison en ligne dans le profil enfant est au moins la valeur de MinChildEndpoints.<BR>CheckingEndpoint : le nombre de points de terminaison Online plus CheckingEndpoint dans le profil enfant est au moins la valeur de MinChildEndpoints.<BR>Dégradé : dans le cas contraire.|Le trafic est acheminé vers un point de terminaison de statut CheckingEndpoint. Si MinChildEndpoints est trop élevée, le point de terminaison est toujours détérioré.|
|En ligne. Point de terminaison enfant au moins un profil est un état en ligne. Aucun point de terminaison n’est dans un état dégradé.|Consultez la section ci-dessus.||
|CheckingEndpoints. Point de terminaison enfant au moins un profil est « CheckingEndpoint ». Aucun point de terminaison n’est « En ligne » ou « Dégradé »|Identique à celle ci-dessus.||
|Inactif. Tous les points de terminaison de profil enfant sont désactivé ou arrêté ou ce profil ne comporte aucun point de terminaison.|Arrêté||


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le [fonctionnement du Gestionnaire de trafic](traffic-manager-how-traffic-manager-works.md)

Découvrez comment [créer un profil le trafic Manager](traffic-manager-manage-profiles.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png

