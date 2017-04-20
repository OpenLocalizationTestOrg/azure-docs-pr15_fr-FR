<properties
    pageTitle="Événement Azure Hubs Archive | Microsoft Azure"
    description="Vue d’ensemble de la fonctionnalité d’Azure événement Hubs Archive."
    services="event-hubs"
    documentationCenter=""
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="darosa;sethm"/>

# <a name="azure-event-hubs-archive"></a>Événement Azure Hubs Archive

Azure événement Hubs Archive vous permet d’effectuer automatiquement le flux des données dans votre Hubs événement à un compte de stockage Blob de votre choix avec souplesse pour spécifier une heure ou dimensionner intervalle de votre choix. La configuration d’Archive est rapide, il n’existe aucun frais administratif pour l’exécuter, et elle s’ajuste automatiquement sur votre événement Hubs [unités de débit](event-hubs-overview.md#capacity-and-security). Événement Hubs Archive le plus simple consiste à charger les données de diffusion en continu dans Azure et permet de vous recentrer sur le traitement des données au lieu d’acquisition de données.

Azure événement Hubs Archive vous permet de traitement par lots et en temps réel pipelines sur le même flux. Cela vous permet de créer des solutions qui peuvent évoluer avec vos besoins au fil du temps. Que vous créez systèmes lot aujourd'hui avec un œil vers future traitement en temps réel, ou que vous voulez ajouter un chemin d’accès à froid efficace à une solution existante en temps réel, Archive Hubs d’événements permet de travailler avec la diffusion en continu de données plus facilement.

## <a name="how-event-hubs-archive-works"></a>Fonctionnement de l’événement Hubs Archive

Événement Hubs est un tampon résistant rétention au moment de pénétration de télémétrie, similaire à un journal distribué. La clé à l’échelle dans événement Hubs est le [modèle partitionnée grand public](event-hubs-overview.md#partition-key). Chaque partition est un segment indépendamment de données et est utilisée séparément. Le temps ces données ans, en fonction de la période de rétention configurable. Par conséquent, un concentrateur événement donné obtient jamais « trop complet ».

Événement Hubs Archive vous permet de spécifier votre propre compte de stockage d’objets Blob Azure et conteneur qui sera utilisé pour stocker les données archivées. Ce compte peut être dans la même région comme concentrateur de votre événement ou dans une autre région, ajout à la flexibilité de l’événement Hubs archives.

Données archivées écrit au format [Avro Apache][] ; format compact, rapide binaire qui fournit des structures de données enrichi schéma inline. Ce format est largement utilisé dans le réseau Hadoop, ainsi que par flux Analytique et Azure Data Factory. Vous trouverez plus d’informations sur l’utilisation des Avro plus loin dans cet article.

### <a name="archive-windowing"></a>Archive Windowing

Événement Hubs Archive vous permet de configurer une fenêtre pour contrôler l’archivage. Cette fenêtre est une taille minimale et la configuration du temps avec une « première wins stratégie », ce qui signifie que le premier déclencheur rencontré provoquera une opération d’archivage. Si vous disposez d’une fenêtre 15-minute/100 Mo archive et envoyez 1 Mo/s, la fenêtre taille se déclenche avant la fenêtre de temps. Chaque partition indépendamment des archives et écrit un blob bloc terminé au moment de l’archivage, nommé pour l’heure lorsque l’intervalle d’archive est produite. La dénomination est la suivante :

```
<Namespace>/<EventHub>/<Partition>/<YYYY>/<MM>/<DD>/<HH>/<mm>/<ss>
```

### <a name="scaling-to-throughput-units"></a>Mise à l’échelle à unités de débit

Le trafic d’événements Hubs dépend des [unités de débit](event-hubs-overview.md#capacity-and-security). Une unité de débit unique permet de 1 Mo/s ou 1000 événements par seconde d’entrant et à deux reprises ce laps de sortie. Hubs événement standard peut être configuré avec des unités de débit 1-20, et plus peuvent être acheté via une augmentation de quota [prise en charge de la demande][]. L’utilisation au-delà des unités achetées débit est limitée. Événement Hubs Archive copie les données directement à partir du stockage Hubs événement interne, en ignorant les quotas de débit unité sortie et enregistrement de votre sortie d’autres lecteurs de traitement comme flux Analytique ou explosion.

Une fois configuré, événement Hubs Archive s’exécute automatiquement dès que vous envoyez votre premier événement. Il continue à s’exécuter en permanence. Pour rendre plus de votre traitement en aval pour savoir que le processus fonctionne, événement Hubs écrit des fichiers vides lorsqu’il n’y a aucune donnée. Cela permet un marqueur qui peut alimenter vos processeurs lot et cadence prévisible.

## <a name="setting-up-event-hubs-archive"></a>La configuration d’événement Hubs Archive

Événement Hubs Archive peut être configuré au moment de la création de concentrateur événement via le portail ou le Gestionnaire de ressources Azure. Vous activez simplement Archive en cliquant **sur** le bouton. Vous configurez un compte de stockage et le conteneur en cliquant sur la section **conteneur** de la cuillère. Événement Hubs Archive utilisant l’authentification service à service avec le stockage, vous n’avez pas besoin de spécifier une chaîne de connexion de stockage. Le sélecteur de ressource sélectionne automatiquement la ressource URI pour votre compte de stockage. Si vous utilisez le Gestionnaire de ressources Azure, vous devez fournir explicitement cet URI sous forme de chaîne.

La fenêtre de temps par défaut est de cinq minutes. La valeur minimale est 1, les 15 maximale. La fenêtre **taille** possède une plage de 10-500 Mo.

![][1]

## <a name="adding-archive-to-an-existing-event-hub"></a>Ajout d’Archive à un concentrateur événement existant

Archives peuvent être configurés sur Hubs événement existants qui se trouvent dans un espace de noms Hubs événement. La fonctionnalité n’est pas disponible dans les espaces de type messagerie ou mixte plus anciens. Pour activer l’archivage sur un concentrateur événement existant, ou pour modifier vos paramètres d’archivage, cliquez sur votre espace de noms pour charger la carte **Essentials** , puis cliquez sur le Hub de l’événement pour lequel vous souhaitez activer ou modifier le paramètre d’archivage. Pour finir, cliquez sur la section **Propriétés** de la cuillère open comme indiqué dans l’illustration suivante.

![][2]

Vous pouvez également configurer événement Hubs Archive via le Gestionnaire de ressources Azure des modèles. Pour plus d’informations, voir [cet article](event-hubs-resource-manager-namespace-event-hub-enable-archive.md).

## <a name="exploring-the-archive-and-working-with-avro"></a>Exploration de l’archivage et l’utilisation de Avro

Une fois configuré, événement Hubs Archive crée des fichiers dans le compte de stockage Azure et le conteneur fournies dans la fenêtre de temps configurée. Vous pouvez afficher ces fichiers dans n’importe quel outil comme [Explorateur de stockage Azure][]. Vous pouvez télécharger les fichiers localement à travailler dessus.

Les fichiers générés par événement Hubs Archive comportent le schéma Avro suivant :

![][3]

Un moyen facile pour Explorer les fichiers Avro est à l’aide de la jar [Avro outils][] d’Apache. Après avoir téléchargé cette jar, vous pouvez afficher le schéma d’un fichier Avro spécifique en exécutant la commande suivante :

```
java -jar avro-tools-1.8.1.jar getschema \<name of archive file\>
```

Cette commande renvoie

```
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

Vous pouvez également utiliser les outils de Avro pour convertir le fichier au format JSON et effectuer d’autres opérations de traitement.

Pour exécuter le traitement plus avancée, téléchargez et installez Avro pour votre choix de la plateforme. Au moment de la rédaction, mises en œuvre sont disponibles pour C, C++, C\#, Java, NodeJS, Perl, PHP, Python et Ruby.

Apache Avro a achevé repères de mise en route pour [Java][] et [Python][]. Vous pouvez également consulter l’article [Mise en route d’événement Hubs Archive](event-hubs-archive-python.md) .

## <a name="how-event-hubs-archive-is-charged"></a>Comment événement Hubs Archive est facturée

Événement Hubs Archive est limitée de la même façon à unités de débit, comme un tarif horaire. Les frais sont directement proportionnel au nombre d’unités de débit acheté pour l’espace de noms. Unités de débit sont augmentées et réduites, événement Hubs Archive augmente et diminue afin d’améliorer les performances correspondantes. Les indicateurs de se produisent en tandem. Les frais de l’événement Hubs archivage sont 0,10 $ par heure unitaire débit, offert avec une remise de 50 % pendant la période d’aperçu.

Événement Hubs Archive est véritablement le moyen le plus simple pour obtenir des données dans Azure. À l’aide de Lake de données Azure Azure Data Factory et Azure HDInsight, vous pouvez effectuer traitement par lots et autres analytique de votre choix à l’aide des outils familiers et plateformes à n’importe quelle échelle que vous avez besoin.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez plus d’informations sur événement Hubs en consultant les liens suivants :

- Un [exemple d’application qui utilise l’événement Hubs][]complète.
- L’exemple de [mise à l’échelle traitement de l’événement avec Hubs de l’événement][] .
- [Vue d’ensemble des Hubs événement][]

[Apache Avro]: http://avro.apache.org/
[demande de support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[1]: ./media/event-hubs-archive-overview/event-hubs-archive1.png
[2]: media/event-hubs-archive-overview/event-hubs-archive2.png
[Explorateur de stockage Azure]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-archive-overview/event-hubs-archive3.png
[Outils Avro]: http://www-us.apache.org/dist/avro/avro-1.8.1/java/avro-tools-1.8.1.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Vue d’ensemble des Hubs événement]: event-hubs-overview.md
[exemple d’application qui utilise l’événement Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Évoluer de traitement de l’événement avec Hubs d’événement]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3