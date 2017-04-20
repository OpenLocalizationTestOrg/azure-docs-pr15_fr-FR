<properties
  pageTitle="Sondes personnalisé équilibrage de charge et le suivi de l’état d’intégrité | Microsoft Azure"
  description="Découvrez comment utiliser personnalisé détecte équilibrage de charge Azure pour surveiller instances derrière équilibrage de charge"
  services="load-balancer"
  documentationCenter="na"
  authors="sdwheeler"
  manager="carmonm"
  editor=""
  tags="azure-resource-manager"
/>
<tags
  ms.service="load-balancer"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
  ms.date="10/24/2016"
  ms.author="sewhee" />

# <a name="understand-load-balancer-probes"></a>Comprendre les sondes d’équilibrage de charge

Équilibrage de charge Azure offre la possibilité de surveiller l’intégrité des instances de serveur à l’aide de sondes. Lorsqu’une sonde ne répond pas, équilibrage de charge cesse d’envoyer des nouvelles connexions à l’instance mauvais état. Les connexions existantes ne sont pas concernées et les nouvelles connexions sont envoyées sur des instances correct.

Rôles de service cloud (rôles de travail et les rôles web) utilisent un agent invité pour l’analyse de sonde. TCP ou HTTP sondes personnalisé doivent être configurés lorsque vous utilisez des machines virtuelles derrière équilibrage de charge.

## <a name="understand-probe-count-and-timeout"></a>Comprendre la sonde Nb et délai d’attente

Comportement sonde dépend :

- Le nombre de succès sondes qui permettent une instance doivent être étiquetées comme étant en service.
- Le nombre de sondes échecs occasionnant une instance doivent être étiquetées comme étant hors service.

La valeur du délai d’expiration et la fréquence définie dans SuccessFailCount déterminer si une instance est confirmée être en cours d’exécution ou ne pas en cours d’exécution. Dans le portail Azure, le délai est défini sur deux fois la valeur de la fréquence.

La configuration de la sonde de toutes les instances d’équilibrage de charge pour un point de terminaison (autrement dit, un ensemble équilibrage de charge) doit être identiques. Cela signifie que vous ne peut pas contenir une configuration sondage différentes pour chaque instance de rôle ou une machine virtuelle dans le même service hébergé pour une combinaison de point de terminaison particulier. Par exemple, chaque instance doit avoir des délais d’expiration et ports locaux identiques.

>[AZURE.IMPORTANT] Une sonde équilibrage de charge utilise l’adresse IP 168.63.129.16. Cette adresse IP publique facilite la communication avec les ressources de plateforme interne pour la mettre votre-appartient-IP scénario Azure virtuelle réseau. L’adresse IP publique virtuelle 168.63.129.16 est utilisée dans toutes les régions et ne change pas. Nous vous conseillons d’autoriser cette adresse IP dans les stratégies de pare-feu local. Il ne doit pas être considéré comme un risque de sécurité car uniquement la plateforme Azure interne puisse fournir un message à partir de cette adresse de la source. Si vous ne le faites pas, il sera un comportement inattendu dans différents scénarios similaire à la configuration de la même plage d’adresses IP de 168.63.129.16 et des adresses IP en double.

## <a name="learn-about-the-types-of-probes"></a>En savoir plus sur les types de sondes

### <a name="guest-agent-probe"></a>Sonde de l’agent invité

Cette sonde est uniquement disponible pour les Services en nuage Azure. Équilibrage de charge utilise l’agent invité à l’intérieur de la machine virtuelle, puis écoute et répond avec une réponse HTTP 200 OK uniquement lorsque l’instance est prête (autrement dit, pas dans un autre état comme occupé (e), recyclage ou d’arrêter).

Pour plus d’informations, voir [configuration du fichier de définition de service (csdef) pour sondes santé](https://msdn.microsoft.com/library/azure/ee758710.aspx) ou [commencer à créer un équilibrage de charge via Internet pour les services en nuage](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>En quoi consiste une sonde agent invité marquer une instance comme étant non fonctionnelle ?

Si l’agent invité ne répond pas avec HTTP 200 OK, l’équilibrage de charge marque l’instance comme ne répond pas et n’envoie le trafic à cette instance. L’équilibrage de charge continue avec la commande ping l’instance. Si l’agent invité répond avec un 200 HTTP, l’équilibrage de charge envoie à nouveau le trafic à cette instance.

Lorsque vous utilisez un rôle web, le code de site Web s’exécute en général en w3wp.exe, non géré par le Azure agent tissu ou invité. Cela signifie que des échecs de w3wp.exe (par exemple, les réponses HTTP 500) ne sont pas signalés à l’agent invité, et l’équilibrage de charge ne prend pas cette instance de rotation.

### <a name="http-custom-probe"></a>Sonde personnalisé HTTP

La sonde d’équilibrage de charge HTTP personnalisé remplace la sonde de l’agent d’invité par défaut, ce qui signifie que vous pouvez créer votre propre logique personnalisée pour déterminer l’état de l’instance de rôle. L’équilibrage de charge détecte votre point de terminaison toutes les 15 secondes, par défaut. L’instance est censée se trouver dans la rotation d’équilibrage de charge s’il doit répondre avec une HTTP 200 dans le délai (31 secondes par défaut).

Cela peut être utile si vous souhaitez implémenter votre propre logique pour supprimer les instances de rotation d’équilibrage de charge. Par exemple, vous pouvez décider de supprimer une instance s’il se trouve au-dessus de 90 % du processeur et renvoie un statut non-200. Si vous avez rôles web qui utilisent w3wp.exe, cela signifie également pouvez-vous automatique surveillance de votre site Web, car échecs dans le code de votre site Web renverra un statut non-200 à la sonde d’équilibrage de charge.

>[AZURE.NOTE] La sonde personnalisée HTTP prend en charge les chemins d’accès relatifs et protocole HTTP uniquement. HTTPS n’est pas pris en charge.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>En quoi consiste une sonde personnalisé HTTP marquer une instance comme étant non fonctionnelle ?

- L’application HTTP renvoie un code de réponse HTTP différent de 200 (par exemple, 403, 404 ou 500). Il s’agit d’un accusé de réception positif instance de l’application doit être mises hors service immédiatement.
- Le serveur HTTP ne répond pas du tout après un délai. Selon la valeur du délai d’attente défini, cela peut signifie que sonde plusieurs demandes d’atteindre sans réponse avant la sonde est marquée comme ne pas en cours d’exécution (autrement dit, avant SuccessFailCount sondes sont envoyés).
- Le serveur ferme la connexion via une réinitialisation TCP.

### <a name="tcp-custom-probe"></a>Sonde personnalisé TCP

TCP sondes établir une connexion en effectuant un contrôle en trois étapes avec le port défini.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>En quoi consiste une sonde personnalisé TCP marquer une instance comme étant non fonctionnelle ?

- Le serveur TCP ne répond pas du tout après un délai. Lorsque la sonde est marquée comme ne pas en cours d’exécution dépend du nombre de requêtes de sondage échec qui ont été configurés pour accéder sans réponse avant de marquer la sonde ne pas en cours d’exécution.
- La sonde reçoit un réinitialisation à partir de l’instance de rôle TCP.

Pour plus d’informations sur la configuration d’une sonde santé HTTP ou une sonde TCP, voir [créer un équilibrage de charge via Internet dans le Gestionnaire de ressources à l’aide de PowerShell](load-balancer-get-started-internet-arm-ps.md#create-lb-rules-nat-rules-a-probe-and-a-load-balancer).

## <a name="add-healthy-instances-back-into-load-balancer-rotation"></a>Ajouter des instances correct en rotation d’équilibrage de charge

Sondes TCP et HTTP sont considérés comme exact et marquer l’instance de rôle comme correct lorsque :

- L’équilibrage de charge Obtient une sonde positive la première fois que la machine virtuelle démarre.
- Le nombre SuccessFailCount (décrite plus haut) définit la valeur de réussis sondes qui sont nécessaires pour marquer l’instance de rôle comme correct. Si une instance de rôle a été supprimée, le nombre de sondes réussies, successifs égale ou supérieure à la valeur de SuccessFailCount pour marquer l’instance de rôle en cours d’exécution.

>[AZURE.NOTE] Si l’état d’une instance de rôle est taux varie, l’équilibrage de charge attend plus avant de mettre l’instance de rôle dans l’état correct. Pour ce faire via la stratégie de protéger l’utilisateur et l’infrastructure.

## <a name="use-log-analytics-for-load-balancer"></a>Utiliser les journaux analytique pour équilibrage de charge

Vous pouvez utiliser [analytique journal pour équilibrage de charge](load-balancer-monitor-log.md) pour vérifier l’état d’intégrité sonde et le nombre de sonde. Journalisation peut être utilisée avec Power BI ou perspectives opérationnelles Azure de fournir des statistiques sur le statut d’intégrité d’équilibrage de charge.
