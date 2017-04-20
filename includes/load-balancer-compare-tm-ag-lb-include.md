## <a name="load-balancer-differences"></a>Différences d’équilibrage de charge

Il existe différentes options pour distribuer le trafic réseau à l’aide de Microsoft Azure. Ces options peuvent être utilisées différemment d’autres, ayant une autre fonction définir et prend en charge différents scénarios. Ils peuvent chacune être utilisées séparément ou qui combine les.

- **Équilibrage de charge Azure** fonctionne sur la couche de transport (4 calque dans la pile OSI réseau référence). Il fournit une distribution au niveau du réseau de trafic pour plusieurs instances d’une application en cours d’exécution dans le centre de données Azure même.

- **Application passerelle** fonctionne au niveau de la couche d’application (couche 7 dans la pile OSI réseau référence). Il fonctionne comme un service proxy inverse, mettre fin à la connexion du client et transfert des demandes aux points de terminaison principale.

- **Gestionnaire de trafic** fonctionne au niveau du DNS.  Il utilise les réponses DNS pour diriger le trafic de l’utilisateur final aux points de terminaison globalement distribués. Clients puis se connecter à ces points de terminaison directement.

Le tableau suivant récapitule les fonctionnalités proposées par chaque service :

| Service | Équilibrage de charge Azure | Passerelle d’application | Gestionnaire de trafic |
|---|---|---|---|
|Technologie| Niveau de transport (calque 4) | Niveau de l’application (couche 7) | Niveau de DNS |
| Prise en charge les protocoles d’application | Tout | HTTP et HTTPS |  Tout (un point de terminaison HTTP est requis pour le contrôle de point de terminaison) |
| Points de terminaison | Instances de rôle machines virtuelles et Services de Cloud Azure | Toute adresse IP interne Azure ou l’internet public adresse IP | Machines virtuelles Azure, les Services en nuage, Azure Web Apps et points de terminaison externes |
| Prise en charge Vnet | Peut être utilisée pour les deux applications Internet en vis-à-vis et internes (Vnet) | Peut être utilisée pour les deux applications Internet en vis-à-vis et internes (Vnet) |    Prend uniquement en charge les applications ouvertes sur Internet |
Point de terminaison de surveillance | Prise en charge via sondes | Prise en charge via sondes | Prise en charge via HTTP/HTTPS GET | 

Équilibrage de charge Azure et de réseau de gammes passerelle d’Application du trafic aux points de terminaison, mais ils ont différents scénarios d’utilisation pour le trafic à gérer. Le tableau suivant vous aide à comprendre la différence entre les programmes d’équilibrage de deux charge :

| Type | Équilibrage de charge Azure | Passerelle d’application |
|---|---|---|
| Protocoles | TCP/UDP | HTTP / HTTPS |
| Réservation IP | Prise en charge | Non pris en charge | 
| Mode équilibrage de charge | 5-tuple(source IP, source port, destination IP, destination port, protocol type) | Fonction Round Robin<br>Routage basé sur l’URL | 
| Équilibrage de charge en mode (adresse IP source / pense-bête sessions) |  2-uplet (adresse IP source et adresse IP de destination), 3-uplet (adresse IP source, adresse IP de destination et port). Peuvent évoluer vers le haut ou vers le bas en fonction du nombre de machines virtuelles | En fonction des cookies affinité<br>Routage basé sur l’URL |
| Sondes d’intégrité | Par défaut : intervalle de sonde - 15 secondes. Extraits de rotation : 2 échecs continues. Teste prend en charge définies par l’utilisateur | Intervalle d’inactivité sonde 30 secondes. Contractées après 5 échecs consécutifs trafic live ou d’une défaillance sonde unique en mode inactif. Teste prend en charge définies par l’utilisateur | 
| Décharger SSL | Non pris en charge | Prise en charge | 
  