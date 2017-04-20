<properties
    pageTitle="Stockage pouvant être ajoutée Azure pour les objets BLOB | Microsoft Azure"
    description="Niveaux de stockage pour le stockage Blob Azure offre un stockage rentable données objet basées sur des modèles access. Le niveau de stockage pouvant être ajoutées est optimisé pour les données moins fréquemment utilisées."
    services="storage"
    documentationCenter=""
    authors="michaelhauss"
    manager="vamshik"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="mihauss"/>


# <a name="azure-blob-storage-hot-and-cool-storage-tiers"></a>Stockage d’objets Blob Azure : Hot et refroidir niveaux de stockage

## <a name="overview"></a>Vue d’ensemble

Stockage Azure propose désormais deux niveaux de stockage pour le stockage Blob (stockage d’objets), afin que vous pouvez stocker vos données plus économique selon la manière de l’utiliser. Le **niveau de stockage hot** Azure est optimisé pour stocker les données fréquemment utilisées. Le **niveau de stockage pouvant être ajoutées** Azure est optimisé pour stocker les données rarement consultées et durée de vie longue. Données dans le niveau de stockage pouvant être ajoutées tolèrent une disponibilité légèrement inférieure, mais il demande grande longévité similaire pour accéder à et les caractéristiques de débit en tant que données active. Pour les données pouvant être ajoutées, légèrement inférieur SLA disponibilité et les coûts d’accès plus élevés sont compromis acceptables pour quantité réduire les coûts de stockage.

Aujourd'hui, les données stockées dans le cloud sont développe à un rythme exponentiel. Pour gérer les coûts à vos besoins de stockage en expansion, il est utile organiser vos données en fonction des attributs comme la fréquence d’accès et de la période de rétention planifiée. Données stockées dans le cloud peuvent être différentes en termes de comment il est généré, traitée et accessible via sa durée de vie. Des données sont activement accessible et modifiées dans l’ensemble de sa durée de vie. Des données sont accessible très souvent au début de sa durée de vie, avec l’accès déposant considérablement en tant que l’âge de données. Certaines données restent inactives dans le cloud et sont rarement, stockés si jamais, accédé à la fois.

Chacun de ces scénarios d’accès de données ci-dessus avantages d’un niveau de stockage optimisée pour un modèle d’accès particulier différencié. Avec l’introduction de différents niveaux de stockage actif et pouvant être ajoutées, Azure Blob storage maintenant répond à ce besoin pour différents niveaux de stockage différencié avec séparer les modèles de tarification.

## <a name="blob-storage-accounts"></a>Comptes de stockage BLOB

**Comptes de stockage BLOB** sont stockage spécialisé des comptes pour le stockage de vos données non structurées comme des objets BLOB (objets) dans le stockage Azure. Avec les comptes de stockage Blob, vous pouvez choisir maintenant entre différents niveaux de stockage actif et pouvant être ajoutées à stocker vos données moins fréquemment consultées pouvant être ajoutées à un espace de stockage inférieur coût et stockent des données à chaud plus fréquemment consultées à un coût d’accès inférieur. Comptes de stockage BLOB sont semblables à vos comptes de stockage à usage général existants et partagent tous les durabilité excellente, disponibilité, extensibilité élevées et fonctionnalités de performances que vous utilisez aujourd'hui, y compris la cohérence des API 100 % des objets BLOB bloc et ajouter des objets BLOB.

> [AZURE.NOTE] Comptes de stockage BLOB prennent en charge uniquement bloc et ajouter des objets BLOB et des objets BLOB page pas.

Comptes de stockage BLOB exposent l’attribut **Niveau d’accès** , qui permettent de spécifier le niveau de stockage en tant que **chaud** ou **pouvant être ajoutées** en fonction des données stockées dans le compte. S’il existe une modification dans le modèle d’utilisation de vos données, vous pouvez également basculer entre ces niveaux de stockage à tout moment.

> [AZURE.NOTE] Modifier le niveau de stockage peut entraîner des frais supplémentaires. Consultez la section [tarification et facturation](storage-blob-storage-tiers.md#pricing-and-billing) pour plus d’informations.

Scénarios d’utilisation exemple pour le niveau de stockage à chaud sont les suivantes :

- Données qui sont en cours d’utilisation ou prévu y accéder fréquemment (lecture à partir d’et écrite à).
- Données transférées de la migration de traitement et éventuelle au niveau de stockage pouvant être ajoutées.

Scénarios d’utilisation exemple pour le niveau de stockage pouvant être ajoutées sont les suivantes :

- Sauvegarde, archivage et des datasets de récupération après sinistre.
- Contenu multimédia plus ancien plus fréquemment consultés pas mais est censé être disponibles immédiatement lors de l’accès.
- Grands ensembles de données qui doivent être stockée coût efficacement pendant plus de données collectées d’un traitement ultérieur. (*par exemple*, stockage à long terme des données scientifiques, données de télémétrie brutes à partir d’une usine de fabrication)
- Données (brutes) d’origine qui doivent être conservées, même après que qu’elle a été traitée en formulaire utilisable final. (*par exemple*, fichiers multimédias brutes après le transcodage dans d’autres formats)
- Conformité et données d’archivage qui doivent être stockées pendant une longue période et se trouve très peu. *(par exemple*, une partie de sécurité caméra, ancien X-rayons/IRM pour les organismes de santé, les enregistrements audio et les transcriptions d’appels de clients pour les services financiers)

Pour plus d’informations sur les comptes de stockage, voir [comptes de stockage sur Azure](storage-create-storage-account.md) .

Pour les applications qui requièrent uniquement bloquent ou ajouter du stockage d’objets blob, nous recommandons d’utiliser des comptes de stockage Blob, pour tirer parti du modèle tarification différencié de stockage à plusieurs niveaux. Toutefois, nous comprendre que cela peut ne pas être possible dans certaines circonstances dans lesquelles les comptes de stockage à usage général serait la solution idéale, telles que :

- Vous devez utiliser des tableaux, des files d’attente ou des fichiers et souhaitez vos objets BLOB stockées dans le même compte de stockage. Notez qu’il n’existe aucune des avantages techniques de stocker ces éléments dans le même compte différent de l’incorporant des clés partagées.
- Vous devez toujours utiliser le modèle de déploiement classique. Comptes de stockage BLOB sont uniquement disponibles via le modèle de déploiement d’Azure le Gestionnaire de ressources.
- Vous devez utiliser des objets BLOB de page. Comptes de stockage BLOB ne prennent pas en charge des objets BLOB de page. Il est généralement recommandé à l’aide des objets BLOB bloc, sauf si vous avez besoin pour les objets BLOB de page.
- Vous utilisez une version de l' [API REST de Services de stockage](https://msdn.microsoft.com/library/azure/dd894041.aspx) qui est antérieure à 2014-02-14 ou une bibliothèque client avec une version inférieure à 4.x et que vous ne pouvez pas mettre à niveau votre application.

> [AZURE.NOTE] Comptes de stockage BLOB sont actuellement prises en charge dans une majorité des régions Azure avec plus de suivre. Vous pouvez trouver la liste des régions disponibles dans la page [Services Azure par région](https://azure.microsoft.com/regions/#services) mis à jour.

## <a name="comparison-between-the-storage-tiers"></a>Comparaison entre les niveaux de stockage

Le tableau suivant illustre la comparaison entre les deux niveaux de stockage :

<table border="1" cellspacing="0" cellpadding="0" style="border: 1px solid #000000;">
<col width="250">
<col width="250">
<col width="250">
<tbody>
<tr>
    <td><strong><center></center></strong></td>
    <td><strong><center>Niveau de stockage actif</center></strong></td>
    <td><strong><center>Niveau de stockage pouvant être ajoutées</center></strong></td
</tr>
<tr>
    <td><strong><center>Disponibilité</center></strong></td>
    <td><center>99,9 %</center></td>
    <td><center>99 %</center></td>
</tr>
<tr>
    <td><strong><center>Disponibilité<br>(RA GRS lit)</center></strong></td>
    <td><center>99,99 %</center></td>
    <td><center>99,9 %</center></td>
</tr>
<tr>
    <td><strong><center>Frais d’utilisation</center></strong></td>
    <td><center>Coûts de stockage supérieures<br>Réduire les coûts access et transaction</center></td>
    <td><center>Réduire les coûts de stockage<br>Coûts access et transaction plus élevés</center></td>
</tr>
<tr>
    <td><strong><center>Taille minimale de l’objet<center></strong></td>
    <td colspan="2"><center>N/A</center></td>
</tr>
<tr>
    <td><strong><center>Durée de stockage minimum<center></strong></td>
    <td colspan="2"><center>N/A</center></td>
</tr>
<tr>
    <td><strong><center>Latence<br>(Temps au premier octet)<center></strong></td>
    <td colspan="2"><center>millisecondes</center></td>
</tr>
<tr>
    <td><strong><center>Performances et extensibilité élevées cibles<center></strong></td>
    <td colspan="2"><center>Identique à objectif général de stockage des comptes</center></td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] Comptes de stockage BLOB prend en charge les performances et cibles extensibilité élevées en tant que comptes de stockage à usage général. Pour plus d’informations, voir [extensibilité du stockage Azure et des objectifs de Performance](storage-scalability-targets.md) .

## <a name="pricing-and-billing"></a>Tarifs et facturation

Comptes de stockage BLOB utilisent un nouveau modèle de tarification pour le stockage blob en fonction du niveau de stockage. Lorsque vous utilisez un compte de stockage Blob, les considérations facturation suivantes s’appliquent :

- **Les coûts de stockage**: en plus de la quantité de données stockées, le coût de stockage des données varie selon le niveau de stockage. Le coût au gigaoctet est inférieur pour le niveau de stockage pouvant être ajoutées que pour le niveau de stockage actif.
- **Les coûts d’accès aux données**: pour les données dans le niveau de stockage pouvant être ajoutées, vous devrez payer frais au gigaoctet données access pour la lecture et l’écriture.
- **Les coûts de transaction**: est payant par transaction pour les deux niveaux. Toutefois, le coût de chaque transaction pour le niveau de stockage pouvant être ajoutées est supérieur à celui pour le niveau de stockage actif.
- **Frais de transfert de données geo réplication**: cela s’applique uniquement aux comptes avec geo-réplication configurée, y compris GRS et RA GRS. Transfert de données geo réplication entraîne des frais au gigaoctet.
- **Données sortantes transférer des coûts**: transferts de données sortantes (données qui sont transférées à partir d’une région Azure) subir facturation pour réduire l’utilisation de la bande passante sur une base par gigaoctet, cohérente avec les comptes de stockage à usage général.
- **Modifier le niveau de stockage**: modifier le niveau de stockage de froid à chaud est facturés égal à toutes les données existantes dans le compte de stockage pour chaque transition de lecture. Modifier le niveau de stockage d’accès rapide à froid seront quant à eux, gratuitement.

> [AZURE.NOTE] Pour autoriser les utilisateurs à essayer les nouvelle niveaux de stockage et de valider les fonctionnalités après lancement, les frais de modification du stockage niveau à partir de froid à chaud est annulé désactiver jusqu'à 2016 au 30 juin. À partir de 2016 1er juillet, les frais seront appliquées à toutes les transitions de froid à chaud. Pour plus d’informations sur le modèle de tarification pour le stockage Blob comptes Voir page [Tarifs de stockage Azure](https://azure.microsoft.com/pricing/details/storage/) . Pour plus d’informations sur les données sortantes frais de transfert voir, page de [Détails de tarification pour les transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/) .

## <a name="quick-start"></a>Démarrage rapide

Dans cette section nous va vous montrer les scénarios suivants à l’aide du portail Azure :

- Comment créer un compte de stockage Blob.
- Comment gérer un compte de stockage Blob.

### <a name="using-the-azure-portal"></a>À l’aide du portail Azure

#### <a name="create-a-blob-storage-account-using-the-azure-portal"></a>Créer un compte de stockage Blob à l’aide du portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le menu concentrateur, sélectionnez **Nouveau** > **données + stockage** > **compte de stockage**.

3. Entrez un nom pour votre compte de stockage.

    Ce nom doit être globalement unique ; Il est utilisé dans le cadre de l’URL permettant d’accéder aux objets dans le compte de stockage.  

4. Sélectionnez **Le Gestionnaire de ressources** en tant que le modèle de déploiement.

    Stockage hiérarchisé peut uniquement être utilisé avec des comptes de stockage du Gestionnaire de ressources ; C’est le modèle de déploiement recommandé pour les nouvelles ressources. Pour plus d’informations, consultez la [vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md).  

5. Dans la liste déroulante Type de compte, sélectionnez **Stockage d’objets Blob**.

    Il s’agit de l’endroit où vous sélectionnez le type de compte de stockage. Stockage hiérarchisé n’est pas disponible dans le stockage universel ; Il est disponible uniquement dans le compte de type de stockage Blob.    

    Notez que lorsque vous sélectionnez cette option, le niveau de performance est défini sur Standard. Stockage hiérarchisé n’est pas disponible avec le niveau de performance Premium.

6. Sélectionnez l’option de réplication pour le compte de stockage : **LRS**, **GRS**ou **RA GRS**. La valeur par défaut est **RA GRS**.

    LRS = stockage localement redondants ; GRS = stockage geo redondantes (2 régions) ; RA GRS est stockage geo redondants accès en lecture (2 régions avec accès en lecture à la seconde).

    Pour plus d’informations sur les options de réplication de stockage Azure, consultez [réplication du stockage Azure](storage-redundancy.md).

7. Sélectionnez le niveau de stockage adapté à vos besoins : définir le **niveau d’accès** **pouvant être ajoutées** ou **Hot**. La valeur par défaut est **chaud**.

8. Sélectionnez l’abonnement dans lequel vous voulez créer le nouveau compte de stockage.

9. Spécifiez un nouveau groupe de ressources ou sélectionnez un groupe de ressources existant. Pour plus d’informations sur les groupes de ressources, voir [vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md).

10. Sélectionnez la région pour votre compte de stockage.

11. Cliquez sur **créer** pour créer le compte de stockage.

#### <a name="change-the-storage-tier-of-a-blob-storage-account-using-the-azure-portal"></a>Modifier le niveau de stockage d’un compte de stockage Blob à l’aide du portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Pour accéder à votre compte de stockage, sélectionnez toutes les ressources, puis sélectionnez votre compte de stockage.

3. Dans la carte de paramètres, cliquez sur **Configuration** pour afficher ou modifier la configuration du compte.

4. Sélectionnez le niveau de stockage adapté à vos besoins : définir le **niveau d’accès** **pouvant être ajoutées** ou **Hot**.

5. Cliquez sur Enregistrer en haut de la carte.

> [AZURE.NOTE] Modifier le niveau de stockage peut entraîner des frais supplémentaires. Consultez la section [tarification et facturation](storage-blob-storage-tiers.md#pricing-and-billing) pour plus d’informations.

## <a name="evaluating-and-migrating-to-blob-storage-accounts"></a>L’évaluation et la migration des comptes de stockage Blob

L’objectif de cette section est d’aider les utilisateurs pour faciliter la transition vers à l’aide de comptes de stockage Blob. Il existe deux scénarios utilisateur :

- Vous avez un compte de stockage à usage général existant et que vous souhaitez évaluer une modification apportée à un compte de stockage Blob avec le niveau de stockage approprié.
- Vous avez décidé d’utiliser un compte de stockage Blob ou déjà un et à évaluer si vous devez utiliser le niveau de stockage chaudes ou pouvant être ajoutées.

Dans les deux cas, la première chose consiste à estimer le coût de stocker et accéder à vos données stockées dans un compte de stockage Blob et comparez-les avec vos coûts en cours.

### <a name="evaluating-blob-storage-account-tiers"></a>Évaluation des niveaux de compte de stockage Blob

Afin d’évaluer le coût de stockage et d’accéder aux données stockées dans un compte de stockage Blob, vous devez évaluer vos habitudes existant ou approximative vos habitudes attendu. En règle générale, vous souhaiterez connaître :

- Votre consommation de stockage - la quantité de données est stockée et comment trouve-t-elle modifiée sur une base mensuelle ?
- La quantité de données est en cours de votre modèle d’accès stockage - lues et écrites sur le compte (y compris les nouvelles données) ? Combien de transactions est utilisées pour accéder aux données, et quels types de transactions sont agit-il ?

#### <a name="monitoring-existing-storage-accounts"></a>Surveillance des comptes de stockage existant

Pour contrôler vos comptes de stockage existants et rassembler ces données, vous pouvez vous servir d’Analytique de stockage Azure qui effectue la journalisation et fournit des données de mesures pour un compte de stockage.
Stockage Analytique peut stocker des indicateurs qui incluent des données statistiques et à la capacité transaction agrégé sur les requêtes au service de stockage Blob pour les comptes de stockage à usage général ainsi que des comptes de stockage Blob.
Ces données sont stockées dans des tables connues dans le même compte de stockage.

Pour plus d’informations, voir [à propos des indicateurs Analytique stockage](https://msdn.microsoft.com/library/azure/hh343258.aspx) et le [Schéma de la Table Analytique métriques de stockage](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [AZURE.NOTE] Comptes de stockage BLOB exposent le point de terminaison du service table uniquement pour le stockage et accéder aux données métriques pour ce compte.

Pour contrôler la consommation de stockage pour le service de stockage Blob, vous devez activer les mesures de capacité.
Avec cette option est activée, les données de capacité sont enregistrées quotidien pour le service d’objets Blob d’un compte de stockage et est enregistrée comme une entrée de table figurant dans la table *$MetricsCapacityBlob* dans le même compte de stockage.

Pour contrôler le modèle d’accès de données pour le service de stockage Blob, vous devez activer l’horaire de métriques de transactions au niveau de l’API.
Avec l’enregistrement activé, par API transactions sont agrégées toutes les heures et enregistrées comme une entrée de table figurant dans la table *$MetricsHourPrimaryTransactionsBlob* dans le même compte de stockage. La table *$MetricsHourSecondaryTransactionsBlob* enregistre les transactions au point de terminaison secondaire en cas de comptes de stockage RA GRS.

> [AZURE.NOTE] Au cas où vous avez un compte de stockage à usage général dans lequel vous stockées des objets BLOB de page et des disques machine virtuelle long bloc et ajoutez des données d’objets blob, ce processus estimation n’est pas applicable. C’est parce que vous n’aurez aucun moyen de capacité distinctive et métriques de transactions en fonction du type d’objets blob pour uniquement bloquent et ajouter des objets BLOB qui peuvent être migrées vers un compte de stockage Blob.

Pour obtenir une bonne approximation de consommation de données vous et modèle d’accès, nous vous recommandons de sélectionner une période de rétention pour les indicateurs est représentant de l’utilisation de votre normale et extrapoler.
Une option consiste à conserver les données métriques pendant 7 jours et collecter les données de chaque semaine, pour une analyse à la fin du mois.
Une autre option consiste à conserver les données de mesures pour les 30 derniers jours et collecter et analyser les données à la fin de la période de 30 jours.

Pour plus d’informations sur l’activation, collecter et afficher les données de la métrique, voir, [métriques de stockage de Azure l’activation et l’affichage des données de métrique](storage-enable-and-view-metrics.md).

> [AZURE.NOTE] Le stockage, accès et le téléchargement des données analytique sont également chargé comme données utilisateur régulier.

#### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Utilisation de mesures de l’utilisation pour estimer les coûts

##### <a name="storage-costs"></a>Coûts de stockage

La dernière entrée dans la capacité indicateurs tableau *$MetricsCapacityBlob* avec les clés de ligne *« données »* affiche la capacité de stockage utilisée par des données utilisateur.
La dernière entrée dans la capacité indicateurs tableau *$MetricsCapacityBlob* avec clé de ligne *« analytique »* montre la capacité de stockage utilisée par les journaux analytique.

Cette capacité totale utilisé par les deux des données utilisateur et journaux analytique (si activé) peuvent ensuite être utilisés pour estimer le coût de stockage des données dans le compte de stockage.
La même méthode peut également être utilisée pour estimer les coûts de stockage pour bloc et ajouter des comptes de stockage à usage général des objets BLOB.

##### <a name="transaction-costs"></a>Coûts de transaction

La somme des *« TotalBillableRequests »*, pour toutes les entrées pour une API dans la table métriques transaction indique le nombre total de transactions pour cette API particulière. *par exemple*, le nombre total de transactions *« GetBlob »* sur une période donnée peut être calculé à par la somme du total des demandes facturables pour toutes les entrées avec la clé de ligne *' utilisateur ; GetBlob'*.

Pour estimer les coûts de transaction pour les comptes de stockage Blob, vous avez besoin décomposer les transactions en trois groupes dans la mesure où ils sont prix différemment.

- Écrire des transactions tels que *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *« SnapshotBlob »*et *« CopyBlob »*.
- Supprimer les transactions tels que *« DeleteBlob »* et *« DeleteContainer »*.
- Toutes les autres opérations.

Pour estimer les coûts de transaction pour les comptes de stockage à usage général, vous devez agréger toutes les transactions, quel que soit l’opération/API.

##### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Coûts de transfert de données access et geo réplication de données

Tandis qu’analytique de stockage ne fournit pas la quantité de données lues et écrites à un compte de stockage, il peut être estimé à peu près en recherchant dans la table métriques transaction.
La somme des *« TotalIngress »* pour toutes les entrées pour une API dans la table métriques transaction indique le volume total de données de pénétration en octets pour cette API particulière.
De même la somme de *'TotalEgress'* indique le volume total de données de sortie, en octets.

Afin d’estimer les coûts d’accès de données pour les comptes de stockage Blob, vous avez besoin décomposer les transactions en deux groupes.

- La quantité de données extraites du compte de stockage peut être estimée en consultant la somme des *« TotalEgress »* pour principalement les opérations *« GetBlob »* et *« CopyBlob »* .
- La quantité de données écrites sur le compte de stockage peut être estimée en consultant la somme des *« TotalIngress »* pour principalement les *'PutBlob'*, *'PutBlock'*, *« CopyBlob »* et *« AppendBlock »* opérations.

Le coût geo réplication de transfert de données pour les comptes de stockage Blob peut également être calculé à l’aide de l’estimation pour la quantité de données écrites dans le cas d’un compte de stockage GRS ou RA GRS.

> [AZURE.NOTE] Pour obtenir un exemple plus détaillé sur le calcul des coûts d’utilisation le niveau de stockage chaudes ou pouvant être ajoutées, veuillez consultez le Forum aux questions intitulé *« que sont les niveaux d’accès chaud et froid et comment dois-je déterminer celui que vous souhaitez use? »* dans le [stockage Azure tarifs Page](https://azure.microsoft.com/pricing/details/storage/).

### <a name="migrating-existing-data"></a>Migration des données existantes

Un compte de stockage Blob est spécialisé pour le stockage de bloc uniquement et ajouter des objets BLOB. Les comptes généraux stockage existants, qui vous permet de stocker des tables, files d’attente, fichiers et des disques, ainsi que des objets BLOB, ne peut pas être convertis en comptes de stockage Blob. Pour utiliser les niveaux de stockage, vous devez créer des comptes de stockage Blob et migrer vos données dans les comptes nouvellement créés.
Vous pouvez utiliser les méthodes suivantes pour migrer les données existantes dans les comptes de stockage Blob de périphériques de stockage sur site, à partir de fournisseurs de stockage cloud tiers ou de vos comptes de stockage à usage général existants dans Azure :

#### <a name="azcopy"></a>AzCopy

AzCopy est un utilitaire de ligne de commande Windows conçu pour High performance copier des données vers ou depuis le stockage Azure. Vous pouvez utiliser AzCopy pour copier les données dans votre compte de stockage Blob de vos comptes de stockage à usage général existant, ou pour charger les données à partir de vos périphériques de stockage en local dans votre compte de stockage Blob.

Pour plus d’informations, voir [transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md).

#### <a name="data-movement-library"></a>Bibliothèque de déplacement de données

Bibliothèque de déplacement de données stockage Azure pour .NET est basée sur l’infrastructure de déplacement de données de base qui se met AzCopy. La bibliothèque est conçue pour les opérations de transfert de données High performance, fiable et facile similaire à AzCopy. Ainsi, vous permettent de tirer pleinement parti des fonctionnalités fournies par AzCopy dans votre application en mode natif sans avoir à traiter en cours d’exécution et de surveillance externes instances de AzCopy.

Pour plus d’informations, voir [Bibliothèque de déplacement de données de stockage Azure pour .net](https://github.com/Azure/azure-storage-net-data-movement)

#### <a name="rest-api-or-client-library"></a>API REST ou la bibliothèque de Client

Vous pouvez créer une application personnalisée pour migrer vos données dans un compte de stockage Blob à l’aide d’une des bibliothèques du client Azure ou les services de stockage Azure API REST. Stockage Azure fournit des bibliothèques clientes enrichies pour plusieurs langues et plateformes comme .NET, Java, C++, Node.JS, PHP, Ruby et Python. Les bibliothèques client proposent des fonctionnalités avancées comme logique des nouvelles tentatives, journalisation et téléchargements en parallèle. Vous pouvez également développer directement par rapport à l’API REST, qui peut être appelé par n’importe quelle langue qui effectue des requêtes de HTTP/HTTPS.

Pour plus d’informations, voir [Prise en main stockage d’objets Blob Azure](storage-dotnet-how-to-use-blobs.md).

> [AZURE.NOTE] Objets BLOB chiffrés à l’aide de chiffrement côté client stockent les métadonnées liées chiffrement stockées avec le blob. Il faut absolument que n’importe quel mécanisme de copie devez vous assurer que les métadonnées d’objets blob et en particulier les métadonnées liés de chiffrement, est conservé. Si vous copiez les objets BLOB sans ces métadonnées, le contenu d’objets blob sera pas récupérable à nouveau. Pour plus d’informations concernant les métadonnées associées de chiffrement, consultez [chiffrement de côté client stockage Azure](storage-client-side-encryption.md).

## <a name="faqs"></a>Forum aux questions

1. **Existe des comptes de stockage toujours disponibles ?**

    Oui, les comptes de stockage existants sont toujours disponibles et ne sont pas modifiés dans tarifs ou la fonctionnalité.  Ils n’ont pas la possibilité de choisir un niveau de stockage et n’ont pas les fonctionnalités de hiérarchisation à l’avenir.

2. **Pourquoi et quand dois-je utiliser comptes de stockage Blob ?**

    Comptes de stockage BLOB spécialisées pour le stockage des objets BLOB et nous permettent de présenter les nouvelles fonctionnalités centré sur les objets blob. Désormais, comptes de stockage Blob sont les procédures recommandées pour le stockage des objets BLOB, en tant que fonctionnalités futures, telles que stockage hiérarchique et hiérarchisation présentera basé sur ce type de compte. Toutefois, il est jusqu'à lorsque vous voulez migrer selon vos besoins professionnels.

3. **Puis-je convertir mon compte de stockage existante à un compte de stockage Blob ?**

    Non. Compte de stockage BLOB est un autre type de compte de stockage et vous devrez créer de nouveaux et migrer les données comme indiqué ci-dessus.

4. **Puis-je stocker des objets dans les deux niveaux de stockage dans le même compte ?**

    L’attribut *« niveau d’accès »* qui indique le niveau de stockage est définie à un niveau de compte et s’applique à tous les objets dans ce compte. Vous ne pouvez pas définir l’attribut niveau d’accès au niveau de l’objet.

5. **Puis-je modifier le niveau de stockage de mon compte de stockage Blob ?**

    Oui. Vous ne pourrez pas modifier le niveau de stockage en définissant l’attribut *« niveau d’accès »* sur le compte de stockage. Modifier le niveau de stockage s’appliquent à tous les objets stockés dans le compte. Modifier le niveau de stockage d’accès rapide à froid entraînera pas les frais, lors du changement de froid à chaud peut entraîner un coût de Go pour lire toutes les données dans le compte.

6. **À quelle fréquence puis-je modifier le niveau de stockage de mon compte de stockage Blob ?**

    Tandis que nous n’appliquent pas une limitation sur la fréquence à laquelle le niveau de stockage peut être modifié, sachez que modifier le niveau de stockage de froid à chaud entraînera frais significatives. Nous ne recommandons pas modifier le niveau de stockage fréquemment.

7. **Les objets BLOB dans le niveau de stockage pouvant être ajoutées seront comporte différemment de celles que dans le niveau de stockage à chaud ?**

    Objets BLOB dans le niveau de stockage à chaud ont la même latence comme des objets BLOB dans comptes de stockage à usage général. Objets BLOB dans le niveau de stockage pouvant être ajoutées ont une latence similaire (en millisecondes) sous forme d’objets BLOB dans les comptes de stockage à usage général.

    Objets BLOB dans le niveau de stockage pouvant être ajoutées ont légèrement disponibilité service niveau inférieur (SLA) que les objets BLOB stockées dans le niveau de stockage actif. Pour plus d’informations, voir [SLA pour le stockage](https://azure.microsoft.com/support/legal/sla/storage).

8. **Puis-je stocker des objets BLOB de page et des disques des machines virtuelles dans les comptes de stockage Blob ?**

    Comptes de stockage BLOB prennent en charge uniquement bloc et ajouter des objets BLOB et des objets BLOB page pas. Machine virtuelle Azure disques bénéficient des objets BLOB de page et par conséquent comptes de stockage Blob ne peut pas servir à stocker des disques des machines virtuelles. Il est toutefois possible stocker des sauvegardes des disques machine virtuelle comme des objets BLOB bloc dans un compte de stockage Blob.

9. **Dois-je modifier mes applications existantes à utiliser des comptes de stockage Blob ?**

    Comptes de stockage BLOB sont 100 % API cohérente avec les comptes de stockage à usage général pour bloc et ajouter des objets BLOB. Dans la mesure où votre application est à l’aide de bloquer des objets BLOB ou ajouter des objets BLOB et à l’aide de la version 2014-02-14 ou supérieur à l' [API REST de Services de stockage](https://msdn.microsoft.com/library/azure/dd894041.aspx) puis votre application doit fonctionner directement. Si vous utilisez une version antérieure du protocole, vous devez mettre à jour votre application pour utiliser la nouvelle version afin de travailler en toute transparence avec les deux types de comptes de stockage. En règle générale, nous toujours conseillé d’utiliser la dernière version indépendamment de compte de stockage type que vous utilisez.

10. **Il sera une modification de l’expérience utilisateur ?**

    Comptes de stockage BLOB sont très semblables à un comptes de stockage à usage général pour le stockage de bloc et ajouter des objets BLOB et prend en charge les principales fonctionnalités de stockage Azure, y compris grande longévité disponibilité, extensibilité élevées, performances et sécurité. Que les fonctionnalités et les restrictions spécifiques aux comptes de stockage Blob ou ses niveaux de stockage entouré au-dessus, tout le reste reste la même.

## <a name="next-steps"></a>Étapes suivantes

### <a name="evaluate-blob-storage-accounts"></a>Évaluer les comptes de stockage Blob

[Vérifier la disponibilité des comptes de stockage Blob par région](https://azure.microsoft.com/regions/#services)

[Évaluer l’utilisation de vos comptes de stockage actuelle en activant métriques de stockage Azure](storage-enable-and-view-metrics.md)

[Vérifier le prix de stockage Blob par région](https://azure.microsoft.com/pricing/details/storage/)

[Transferts de données à cocher tarifs](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-blob-storage-accounts"></a>Commencer à utiliser des comptes de stockage Blob

[Prise en main stockage d’objets Blob Azure](storage-dotnet-how-to-use-blobs.md)

[Déplacement de données vers et depuis le stockage Azure](storage-moving-data.md)

[Transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)

[Parcourir et d’Explorer vos comptes de stockage](http://storageexplorer.com/)
