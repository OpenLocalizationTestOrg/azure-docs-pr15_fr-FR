<properties 
    pageTitle="Service Bus tarifs et facturation | Microsoft Azure"
    description="Vue d’ensemble du Service Bus tarification."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/06/2016"
    ms.author="sethm" />

# <a name="service-bus-pricing-and-billing"></a>Service Bus tarifs et facturation

Bus des services est proposé en versions basique, Standard et [Premium](service-bus-premium-messaging.md) . Vous pouvez choisir un niveau de service pour chaque espace de noms de service Bus des services que vous créez, et cette sélection couche s’applique à toutes les entités créées dans cet espace de noms.

>[AZURE.NOTE] Pour obtenir des informations détaillées sur les tarifs actuels Bus des services, consultez le [Bus des services Azure tarifs page](https://azure.microsoft.com/pricing/details/service-bus/)et le [Forum aux questions sur Bus de Service](service-bus-faq.md#service-bus-pricing).

Bus des services utilise les règles suivantes deux files d’attente et rubriques/abonnements :

1. **Opérations de messagerie**: définie comme appels API par rapport aux points de terminaison de service file d’attente ou rubrique/abonnement. Ce compteur remplacera messages envoyé ou reçu en tant que l’unité principale de l’utilisation facturable pour les rubriques/abonnements et les files d’attente.

2. **Connexions demandé**: définie comme le nombre maximal de connexions permanentes ouvre contre les files d’attente, rubriques ou abonnements pendant une période donnée échantillonnages une heure. Ce compteur s’applique uniquement à la couche Standard, dans lequel vous pouvez ouvrir des connexions supplémentaires (auparavant, les connexions étaient limitées à 100 par file d’attente/rubrique/abonnement) pour les frais de connexion nominal.

La couche **Standard** présente tarifs dégradées pour les opérations effectuées avec files d’attente et rubriques/abonnements, ce qui crée de remises basées sur le volume de 80 % le plus haut niveau de l’utilisation. Vous trouverez également un niveau Standard base des frais de 10 $ par mois, ce qui vous permet d’effectuer les opérations jusqu'à 12,5 millions par mois sans frais supplémentaires.

La catégorie **Premium** offre isolement ressource au niveau du processeur et la mémoire afin que chaque charge de travail client s’exécute séparément. Ce conteneur de ressources est appelé une *unité de messagerie*. Chaque espace de noms premium alloué au moins une unité de messagerie. Vous pouvez acheter 1, 2 ou 4 unités de messagerie pour chaque espace de noms de Service Bus Premium. Une seule charge de travail ou une entité peut couvrir divisions de votre messageries et le nombre d’unités de messagerie peut être modifié en entraîne, bien que facturation de frais de taux quotidiennement ou 24 heures. Le résultat est prévisibles et répétitifs les performances de votre solution Bus des services. Non seulement cette efficacité n’est plus prévisibles et disponible, mais il est également plus rapide. Messagerie Service Bus Premium Azure s’appuie sur le moteur de stockage introduit dans Azure événement Hubs. Avec la messagerie Premium, des performances optimales sont beaucoup plus rapide que la couche Standard.

Notez que les frais de base standard sont facturée qu’une seule fois par mois par abonnement Azure. Cela signifie qu’après avoir créé un Standard unique ou un espace de noms Premium couche Bus des services, vous pourrez créer autant Standard ou Premium couche espaces de noms supplémentaires que vous le souhaitez sous ce même abonnement Azure, sans subir de base des frais supplémentaires.

Tous les espaces Bus des services existants créés avant le 1er novembre 2014 ont été automatiquement placés dans la couche Standard. Cela garantit que vous continuez à accéder à toutes les fonctionnalités actuellement disponibles à Service. Par la suite, vous pouvez utiliser le [portail classique Azure][] pour mettre à niveau vers le niveau de base si vous le souhaitez.

Le tableau suivant résume les différences fonctionnelles entre les couches Basic et Standard/Premium.

|Fonction|Base|Standard/Premium|
|---|---|---|
|Files d’attente|Oui|Oui|
|Messages planifiées|Oui|Oui|
|Rubriques/abonnements|N°|Oui|
|Relais|N°|Oui|
|Transactions|N°|Oui|
|Suppression des doublons|N°|Oui|
|Sessions|N°|Oui|
|Messages de grande taille|N°|Oui|
|ForwardTo|N°|Oui|
|SendVia|N°|Oui|
|Connexions traitées (incluses)|100 par l’espace de noms Bus des services|1 000 par abonnement Azure|
|Connexions traitées (excédent autorisé)|N°|Oui (facturable)|

## <a name="messaging-operations"></a>Opérations de messagerie

Dans le cadre du nouveau modèle de tarification, facturation pour les files d’attente et rubriques/abonnements change. Ces entités sont transition à partir de facturation par message à facturation par opération. Une « opération » fait référence à un appel API effectué par rapport à un point de terminaison du service file d’attente ou rubrique/abonnement. Cela inclut les opérations d’état gestion d’envoi/réception et session.

|Type d’opération|Description|
|---|---|
|Gestion des|Créer, lire, mise à jour, supprimer (CRUD) par rapport aux rubriques/abonnements ou files d’attente.|
|Messagerie|Envoi et réception de messages avec rubriques/abonnements ou files d’attente.|
|État de la session|Obtenir ou définir l’état de session sur un sujet/abonnement ou file d’attente.|

Les prix suivants ont été efficaces commençant le 1er novembre 2014 :

|Base|Coût|
|---|---|
|Opérations|0,05 $ par millions d’opérations|

|Standard|Coût|
|---|---|
|Frais de base|$10/ mois|
|Tout d’abord 12,5 millions opérations/mois|Inclus|
|12,5 opérations-100 millions par mois|$0,80 par millions d’opérations|
|opérations de 100 millions de-2 500 millions/mois|0,50 $ par millions d’opérations|
|Plus de 2 500 millions opérations/mois|0,20 $ par millions d’opérations|

|Premium|Coût|
|---|---|
|Quotidien|$11.13 taux par Message unité fixe|

## <a name="brokered-connections"></a>Connexions traitées

*Connexions Brokered* prendre en charge les modèles de l’utilisation de client qui impliquent un grand nombre d’expéditeurs/récepteurs « permanence connecté » par rapport aux files d’attente, les rubriques ou les abonnements. Expéditeurs/récepteurs reliés de façon permanente sont ceux qui se connectent à l’aide de AMQP ou HTTP avec un zéro recevoir le délai d’attente (par exemple, HTTP long d’interrogation). HTTP expéditeurs et destinataires avec un délai d’expiration immédiate ne génèrent pas traitées connexions.

Files d’attente et rubriques/abonnements avaient précédemment, une limite de 100 connexions simultanées par URL. Le modèle de facturation actuel supprime la limite d’URL pour les rubriques/abonnements et les files d’attente et mettent en œuvre, les quotas et contrôle sur les connexions traitées à l’espace de noms de Bus de Service et les niveaux d’abonnement Azure.

La couche base inclut et est strictement limitée à 100 connexions traitées par espace de noms Bus des services. Connexions au-dessus de ce numéro seront rejetées dans le niveau de base. La couche Standard supprime la limite par espace de noms et détermine l’utilisation de la connexion traité agrégation au sein de l’abonnement Azure. Dans la couche Standard, 1 000 connexions traitées par abonnement Azure seront autorisées gratuitement (au-delà de la charge de base). À l’aide de plus de 1 000 connexions traités au total à travers Standard de couche Service Bus espaces de noms dans un abonnement Azure seront facturés une fréquence dégradées, comme indiqué dans le tableau suivant.

|Connexions traitées (niveau Standard)|Coût|
|---|---|
|Premier 1 000/mois|Inclus dans les frais de base|
|1 000 100,000/mois|0,03 $ par connexion/mois|
|500 000 100,000/mois|$0,025 par connexion/mois|
|500 000/mois|0.015 $ par connexion/mois|

>[AZURE.NOTE] 1 000 connexions traitées sont incluses dans la couche de messagerie Standard (via les frais de base) et peuvent être partagées entre tous les abonnements au sein de l’abonnement Azure associé, rubriques et files d’attente.

<br />

>[AZURE.NOTE] Facturation est basée sur le nombre maximal de connexions simultanées et est calculé au prorata horaire basé sur les heures 744 par mois.

|Niveau Premium
|---|
|Connexions traitées ne seront pas imputées au niveau Premium.|

Pour plus d’informations sur les connexions traitées, consultez la section [Forum aux questions](#faq) plus loin dans cette rubrique.

## <a name="relay"></a>Relais

Relais sont disponibles uniquement dans les espaces de noms de niveau Standard. Dans le cas contraire, les quotas de connexion et de prix des relais restent inchangées. Cela signifie que relais continuent à être facturé sur le nombre de messages (pas les opérations) et transmettre les heures.

|Relais tarifs|Coût|
|---|---|
|Relais heures|0,10 $ pour toutes les heures 100 relais|
|Messages|0,01 $ pour chaque 10 000 messages|

## <a name="faq"></a>FAQ

### <a name="how-is-the-relay-hours-meter-calculated"></a>Comment se calcule la jauge d’heures relais ?

Consultez [la rubrique suivante](service-bus-faq.md#how-is-the-relay-hours-meter-calculated).

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>Que sont les demandé de connexions et comment puis-je obtenir facturée les ?

Une connexion traitée est définie comme une des opérations suivantes :

1. Une connexion AMQP à partir d’un client de file d’attente Bus de Service ou d’abonnement/rubrique.

2. Un appel HTTP pour recevoir un message à partir d’une rubrique Bus des services ou file d’attente qui comporte une valeur de délai d’attente de réception supérieure à zéro.

Frais Bus des services pour le nombre maximal de connexions simultanées traités qui dépasse la quantité incluse (1 000 dans la couche Standard). Pointes sont mesurées toutes les heures, au prorata en divisant par 744 heures par mois et cumulées pendant la période de facturation mensuelle. La quantité fournie (1 000 connexions traitées par mois) est appliquée à la fin de la période de facturation par rapport à la somme des proportion pointes horaires.

Par exemple :

1. Chacun de 10 000 périphériques se connecte via une connexion AMQP unique et reçoit des commandes à partir d’une rubrique Bus des services. Les appareils envoient des événements de télémétrie à un concentrateur de l’événement. Si tous les périphériques de se connectent pour 12 heures par jour, les frais de connexion suivantes s’appliquent (en plus de toutes les autres charges rubrique Bus des services) : 10 000 connexions *12 heures* jours 31 / 744 = 5 000 demandé de connexions. Après l’allocation de 1 000 connexions traitées mensuelle, vous le feriez seront-elles facturées 4 000 connexions traitées, à la vitesse de 0,03 $ par connexion traitée, pour un total de 120 $.

2. 10 000 appareils recevoir des messages à partir d’une file d’attente de Bus des services via le protocole HTTP, définir un délai non nulle. Si tous les périphériques de se connectent pour 12 heures par jour, vous verrez les frais de connexion suivant (en plus de toutes les autres charges Bus des services) : 10 000 réception HTTP connexions *12 heures par jour* 31 derniers jours / heures 744 = 5 000 dans les connexions.

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>Des frais de connexion traité s’appliquent aux files d’attente et rubriques/abonnements ?

Oui. Il n’existe aucune des frais de connexion d’envoi d’événements à l’aide de HTTP, quel que soit le nombre d’envoi de systèmes ou périphériques. Recevoir des événements avec HTTP en utilisant un délai d’expiration supérieur à zéro, parfois appelée « interrogation longue, » génère des frais de connexion traité. Connexions AMQP génèrent des frais de connexion traité indépendamment indique si les connexions sont utilisées pour envoyer ou recevoir. Notez que 100 connexions traitées sont autorisées sans frais dans un espace de noms de base. C’est aussi le nombre maximal de connexions traités autorisées pour l’abonnement Azure. Les 1 000 premiers connexions traitées sur tous les espaces de noms Standard dans un abonnement Azure sont incluses sans frais supplémentaires (au-delà de la charge de base). Étant donné que ces allocation est pour couvrir plusieurs scénarios de messagerie de service à service, des frais de connexion traité généralement uniquement deviennent pertinentes si vous prévoyez d’utiliser d’interrogation longue AMQP ou HTTP avec un grand nombre de clients ; par exemple, pour obtenir plus efficace événement diffusion en continu ou d’activer la communication bidirectionnelle avec de nombreux appareils ou instances de l’application.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les tarifs Bus des services, consultez le [Bus des services Azure tarifs page](https://azure.microsoft.com/pricing/details/service-bus/).

- Consultez le [Forum aux questions sur Bus de Service](service-bus-faq.md#service-bus-pricing) pour quelques questions courantes autour de bus des services tarifs et facturation.

[Portail classique Azure]: http://manage.windowsazure.com