<properties
    pageTitle="Types de point de terminaison le trafic Gestionnaire | Microsoft Azure"
    description="Cet article présente les différents types de points de terminaison qui peuvent être utilisés avec le Gestionnaire de trafic Azure"
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

# <a name="traffic-manager-endpoints"></a>Points de terminaison du trafic Manager

Gestionnaire de Microsoft Azure le trafic permet de vous permettent de contrôler la façon dont le trafic réseau est distribué pour le déploiement des applications en cours d’exécution dans différents centres de données. Vous configurez chaque déploiement des applications comme un « point de terminaison » dans le Gestionnaire de trafic. Lorsque le trafic gestionnaire reçoit une demande DNS, il choisit un point de terminaison disponible pour retourner dans la réponse DNS. Gestionnaire de trafic base le choix sur l’état de point de terminaison actuel et la méthode le routage du trafic. Pour plus d’informations, voir [Fonctionnement du Gestionnaire de trafic](traffic-manager-how-traffic-manager-works.md).

Il existe trois types de point de terminaison pris en charge par le Gestionnaire de trafic :

- **Points de terminaison Azure** sont utilisés pour les services hébergés dans Azure.
- **Points de terminaison externes** sont utilisés pour les services hébergés en dehors d’Azure, localement ou avec un autre fournisseur d’hébergement.
- **Points de terminaison imbriquées** sont utilisés pour combiner des profils de trafic Manager pour créer des schémas de routage du trafic plus flexibles pour répondre aux besoins des déploiements plus grandes et plus complexes.

Il n’existe aucune restriction sur la façon dont les points de terminaison de différents types sont combinées dans un seul profil gestionnaire le trafic. Chaque profil peut contenir une combinaison de types de point de terminaison.

Les sections suivantes décrivent chaque type de point de terminaison de manière plus approfondie.

## <a name="azure-endpoints"></a>Points de terminaison Azure

Points de terminaison Azure sont utilisées pour les services Azure dans le Gestionnaire de trafic. Types de ressources Azure suivants sont pris en charge :

- Machines virtuelles IaaS « Classiques » et PaaS services cloud.
- Applications Web
- Ressources PublicIPAddress (qui peut être connecté au machines virtuelles directement ou via un programme d’équilibrage de charge Azure). La publicIpAddress doit avoir un nom DNS affecté à être utilisé dans un profil Manager le trafic.

PublicIPAddress les ressources Azure le Gestionnaire de ressources. Ils n’existent pas dans le modèle de déploiement classique. Par conséquent, ils sont uniquement pris en charge dans le trafic du responsable Gestionnaire de ressources Azure expériences. Les autres types de point de terminaison sont pris en charge par le Gestionnaire de ressources et le modèle de déploiement classique.

Lorsque vous utilisez des points de terminaison Azure, le trafic Gestionnaire détecte un VM IaaS « Classique », service cloud ou une application Web est arrêtée et démarrée. Ce statut est reflété dans le statut de point de terminaison. Pour plus d’informations, voir [Gestionnaire de trafic surveillance de point de terminaison](traffic-manager-monitoring.md#endpoint-and-profile-status) . Lorsque le service sous-jacent est arrêté, le trafic Manager n’effectue pas vérifications au niveau de point de terminaison ou diriger le trafic vers le point de terminaison. Aucun gestionnaire de trafic facturation se produisent pour l’instance arrêté. Lors du redémarrage, le service reprend facturation et le point de terminaison est autorisé à recevoir le trafic. Cette détection ne s’applique pas aux points de terminaison PublicIpAddress.

## <a name="external-endpoints"></a>Points de terminaison externes

Points de terminaison externes sont utilisés pour les services en dehors d’Azure. Par exemple, un service hébergé en local ou avec un autre fournisseur. Points de terminaison externes peuvent être utilisés individuellement ou combinés avec points de terminaison Azure dans le même profil Manager le trafic. Combinaison de points de terminaison Azure avec points de terminaison externe permet de différents scénarios :

- Dans un modèle de basculement actif ou actif-passif, utilisez Azure pour augmenter la redondance d’une application sur site.
- Pour réduire le délai d’application pour les utilisateurs dans le monde entier, étendre une application locale existante vers des emplacements géographiques supplémentaires dans Azure. Pour plus d’informations, voir [le trafic gestionnaire 'Performances' le routage du trafic](traffic-manager-routing-methods.md#performance-traffic-routing-method).
- Utiliser Azure pour fournir une capacité supplémentaire pour un existant en local application, en continu ou en tant que 'rupture-nuage' solution pour répondre à un pic à la demande.

Dans certains cas, il est préférable d’utiliser des points de terminaison externes pour référencer des services Azure (pour obtenir des exemples, consultez le [Forum aux questions](#faq)). Dans ce cas, vérifications au niveau sont facturées au taux de points de terminaison Azure, pas le taux de points de terminaison externes. Toutefois, contrairement aux points de terminaison Azure, si vous arrêtez ou supprimez le service sous-jacentes, vérification facturation continue jusqu'à ce que vous désactivez ou supprimez le point de terminaison dans le Gestionnaire de trafic.

## <a name="nested-endpoints"></a>Points de terminaison imbriquées

Points de terminaison imbriquées combinent plusieurs profils de trafic Manager pour créer des schémas de routage du trafic flexibles et répondre aux besoins des déploiements plus grandes et complexes. Points de terminaison imbriquées, un profil « enfants » est ajouté comme un point de terminaison à un profil « parent ». Profils de l’enfant et le parent peuvent contenir des autres points de terminaison de n’importe quel type, y compris les autres profils imbriquées. Pour plus d’informations, voir [les profils le trafic Gestionnaire imbriquée](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Web applications comme points de terminaison

Certaines considérations supplémentaires s’appliquent lors de la configuration des applications Web comme points de terminaison dans le Gestionnaire de trafic :

1. Uniquement applications Web à la référence (SKU) « Standard » ou ci-dessus sont éligibles pour une utilisation avec le Gestionnaire de trafic. Échouent de tentatives d’ajout d’une application Web d’une référence SKU inférieure. Mise à niveau la référence (SKU) d’une application Web existante comme résultat dans le Gestionnaire de trafic envoyant n’est plus le trafic vers cette application Web.

2. Lorsqu’un point de terminaison reçoit une demande HTTP, elle utilise l’en-tête « hôte » dans la demande pour déterminer quelle application Web App doit répondre à la requête. L’en-tête d’hôte contient le nom DNS utilisé pour lancer la requête, par exemple « contosoapp.azurewebsites.net ». Pour utiliser un autre nom DNS avec votre application Web, le nom DNS doit être enregistré comme un nom de domaine personnalisé pour l’application. Si vous ajoutez un point de terminaison Web App en un point de terminaison Azure, le nom DNS du profil le trafic responsable est automatiquement enregistré pour l’application. Cet enregistrement est supprimé automatiquement lorsque le point de terminaison est supprimé.

3. Chaque profil le trafic gestionnaire peut comporter au moins une extrémité Web App de chaque région Azure. Pour contourner cette contrainte, vous pouvez configurer une application Web comme un point de terminaison externe. Pour plus d’informations, consultez le [Forum aux questions](#faq).

## <a name="enabling-and-disabling-endpoints"></a>Activation et désactivation des points de terminaison

Désactivation d’un point de terminaison dans le Gestionnaire de trafic peut être utile pour supprimer temporairement le trafic à partir d’un point de terminaison est en mode maintenance ou redéployé. Une fois que le point de terminaison s’exécute à nouveau, il peut être réactivé.

Points de terminaison peuvent être activés et désactivés via le portail le trafic Manager, PowerShell, infrastructure du langage commun ou API REST, ce qui sont prises en charge dans le Gestionnaire de ressources et le modèle de déploiement classique.

>[AZURE.NOTE] Désactivation d’un point de terminaison Azure ne comporte rien faire avec son état déploiement dans Azure. Un service Azure (tel qu’une machine virtuelle ou Web App reste en cours d’exécution et peut recevoir le trafic même lorsque désactivée dans le Gestionnaire de trafic. Le trafic peut être résolu directement à l’instance de service plutôt que via le nom DNS Manager le trafic profil. Pour plus d’informations, voir [fonctionnement du Gestionnaire de trafic](traffic-manager-how-traffic-manager-works.md).

L’éligibilité en cours de chaque point de terminaison pour recevoir le trafic dépend des facteurs suivants :

- L’état de profil (activé/désactivé)
- L’état de point de terminaison (activé/désactivé)
- Vérifie les résultats de l’état pour ce point de terminaison

Pour plus d’informations, voir [Gestionnaire de trafic surveillance de point de terminaison](traffic-manager-monitoring.md#endpoint-and-profile-status).

>[AZURE.NOTE] Dans la mesure où le trafic Manager fonctionne au niveau du DNS, il ne peut pas influencer les connexions existantes à un point de terminaison. Lorsqu’un point de terminaison n’est pas disponible, le trafic Gestionnaire dirige nouvelles connexions à un autre point de terminaison disponible. Toutefois, l’hôte derrière le point de terminaison désactivé ou incorrecte peut continuer à recevoir le trafic via les connexions existantes jusqu'à ce que ces sessions sont terminées. Applications recommandé de limiter la durée de la session pour autoriser le trafic décharger de connexions existantes.

Si tous les points de terminaison dans un profil sont désactivées, ou si le profil lui-même est désactivé, le trafic gestionnaire envoie une réponse « NXDOMAIN » à une nouvelle requête DNS.

## <a name="faq"></a>FAQ

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Puis-je utiliser le Gestionnaire de trafic avec points de terminaison de plusieurs abonnements ?

À l’aide des points de terminaison de plusieurs abonnements n’est pas possible avec Azure Web Apps. Applications Web Azure nécessite que n’importe quel nom de domaine personnalisé utilisé avec des applications Web est utilisé uniquement dans un seul abonnement. Il n’est pas possible d’utiliser des applications Web à partir de plusieurs abonnements portant le même nom de domaine.

Pour les autres types de point de terminaison, il est possible d’utiliser le Gestionnaire de trafic avec points de terminaison de plusieurs abonnements. Comment vous configurez le trafic Gestionnaire varie selon que vous utilisez le modèle de déploiement classique ou l’expérience de gestionnaire de ressources.

- Dans le Gestionnaire de ressources, points de terminaison dans les abonnements peuvent être ajoutés au Gestionnaire de trafic, tant que la personne configuration du profil le trafic gestionnaire a accès en lecture sur le point de terminaison. Les autorisations suivantes peuvent être accordées à l’aide du [Gestionnaire de ressources Azure accès basé sur un rôle contrôle de](../active-directory/role-based-access-control-configure.md).
- Dans l’interface du modèle déploiement classique, le trafic Manager nécessite que les Services en nuage ou Web Apps est configuré comme points de terminaison Azure résident dans le même abonnement du profil Manager le trafic. Points de terminaison de Service cloud dans les autres abonnements peuvent être ajoutés au Gestionnaire de trafic comme points de terminaison « externes ». Ces points de terminaison externes sont facturées en tant que points de terminaison Azure, plutôt que la vitesse externe.

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Puis-je utiliser le Gestionnaire de trafic avec emplacements « Mis en œuvre » Service Cloud ?

Oui. « Intermédiaire » emplacements du Service cloud peut être configuré dans le Gestionnaire de trafic comme points de terminaison externes. Vérifications au niveau sont toujours facturée au taux de points de terminaison Azure. Étant donné que le type de point de terminaison externe est en cours d’utilisation, les modifications apportées au service sous-jacentes ne sont pas prises automatiquement. Avec points de terminaison externes, le trafic Manager ne peut pas détecter lorsque le Service en nuage est interrompue ou supprimé. Par conséquent, le Gestionnaire de trafic continue facturation pour les vérifications au niveau jusqu'à ce que le point de terminaison est désactivé ou supprimé.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Gestionnaire de trafic ne prend en charge les points de terminaison IPv6 ?

Gestionnaire de trafic ne fournit pas actuellement IPv6 addressible serveurs de noms. Toutefois, le trafic Gestionnaire peuvent toujours être utilisé par IPv6 clients qui se connectent aux points de terminaison du protocole IPv6. Un client ne fait pas les requêtes DNS directement au Gestionnaire de trafic. En revanche, le client utilise un service DNS récursive. Un client uniquement IPv6 envoie des demandes du service DNS récursive par le biais du protocole IPv6. Puis le service récursive doit être en mesure de contacter les serveurs de noms gestionnaire le trafic à l’aide de IPv4.

Gestionnaire de trafic répond avec le nom DNS du point de terminaison. Pour prendre en charge un point de terminaison du protocole IPv6, un enregistrement DNS AAAA pointant vers le nom DNS de point de terminaison à l’adresse IPv6 doit exister. Vérifications au niveau de gestionnaire le trafic ne prennent en charge que les adresses IPv4. Le service doit exposer un point de terminaison IPv4 sur le même nom DNS.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Puis-je utiliser le Gestionnaire de trafic avec plusieurs Web App dans la même région ?

En règle générale, le trafic gestionnaire est utilisé pour diriger le trafic vers les applications déployées dans différentes régions. Toutefois, il peut également être utilisé dont une application comporte plusieurs déploiements dans la même région. Les points de terminaison Azure le trafic Manager n’autorisent pas plus d’un point de terminaison Web App à partir de la même région Azure à inclure dans le même profil Manager le trafic.

Les étapes suivantes fournissent une solution de contournement à cette règle :

1. Vérifiez que vos points de terminaison sont dans un autre site web application « échelle unités ». Un nom de domaine doit mapper un site unique dans une unité d’échelle donnée. Par conséquent, les deux applications Web dans la même unité d’échelle ne peuvent pas partager un profil Manager le trafic.
2. Ajoutez votre surnom domaine comme un nom d’hôte personnalisé pour chaque application Web. Chaque application Web doit être placé dans une unité d’échelle différents. Toutes les applications Web doit appartenir à la même abonnement.
3. Ajouter un seul (et unique) point de terminaison Web App à votre profil le trafic Manager, comme un point de terminaison Azure.
4. Ajouter chaque point de terminaison Web App supplémentaire à votre profil le trafic gestionnaire sous la forme d’un point de terminaison externe. Points de terminaison externes ne peuvent être ajoutés à l’aide du modèle de déploiement Gestionnaire de ressources.
5. Créer un enregistrement CNAME DNS dans votre domaine personnel qui pointe vers le nom de DNS de votre profil le trafic Manager (<> …. trafficmanager.net).
6. Accéder à votre site via le nom de domaine vanity pas le nom DNS pour le trafic Gestionnaire profil.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez [comment fonctionne le trafic Gestionnaire](traffic-manager-how-traffic-manager-works.md).
- En savoir plus sur le trafic responsable [point de terminaison de surveillance et basculement automatique](traffic-manager-monitoring.md).
- En savoir plus sur le trafic gestionnaire [des méthodes de routage du trafic](traffic-manager-routing-methods.md).
