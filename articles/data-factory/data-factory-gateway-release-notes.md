<properties 
    pageTitle="Notes de publication pour la passerelle de gestion des données | Données Azure usine" 
    description="Notes de publication de tory de passerelle de gestion des données" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/26/2016" 
    ms.author="spelluru"/>

# <a name="release-notes-for-data-management-gateway"></a>Notes de publication pour la passerelle de gestion des données
L’une des difficultés pour l’intégration de données moderne consiste à déplacer des données en toute transparence vers et depuis local vers le cloud. Données usine rend cette intégration transparente avec la passerelle de gestion des données, ce qui correspond à un agent que vous pouvez installer local pour permettre le déplacement de données hybride.

Consultez les articles suivants pour plus d’informations sur la passerelle de gestion des données et comment l’utiliser : 

- [Passerelle de gestion des données](data-factory-data-management-gateway.md)
- [Déplacer des données entre en local et en nuage à l’aide d’usine de données Azure](data-factory-move-data-between-onprem-and-cloud.md) 

## <a name="current-version-2260721"></a>Version actuelle (2.2.6072.1)

- Prend en charge la définition de proxy HTTP pour la passerelle à l’aide du Gestionnaire de Configuration de passerelle. Le cas échéant, Blob Azure, Table Azure, Azure données Lake et Document DB sont accessibles via proxy HTTP.
- Prend en charge l’en-tête gestion format du texte lors de la copie des données depuis/vers Blob Azure, Azure Data Lake Store, système de fichiers local et HADOOP local.
- Prend en charge la copie des données d’objets Blob ajouter et Blob de Page ainsi que les objets Blob bloc déjà pris en charge.
- Présente un nouveau statut de passerelle **en ligne (limité)**, qui indique que les principales fonctionnalités de la passerelle fonctionnent à l’exception de la prise en charge opération interactive Assistant copie.
- Améliore la fiabilité de l’enregistrement de passerelle à l’aide de la clé d’inscription.

## <a name="earlier-versions"></a>Versions antérieures

## <a name="2160401"></a>2.1.6040.1

- Pilote DB2 est inclus dans le package d’installation passerelle maintenant. Vous n’avez pas besoin d’installer séparément. 
- Pilote DB2 prend désormais en charge z/OS DB2 pour je (AS / 400) ainsi que les plateformes déjà pris en charge (Linux, Unix et Windows). 
- Prend en charge à l’aide de DocumentDB comme source ou destination pour banques de données locale
- Prend en charge copier des données à partir de/à chaud/froid blob storage ainsi que le compte de stockage à usage général déjà pris en charge. 
- Permet de vous connecter à locale SQL Server via une passerelle avec des privilèges d’ouverture de session distante.  

## <a name="2060131"></a>2.0.6013.1

- Vous pouvez sélectionner la langue/culture devant être utilisé par une passerelle pendant l’installation manuelle.
- Lors de la passerelle ne fonctionne pas comme prévu, vous pouvez choisir envoyer les journaux de passerelle de sept derniers jours à Microsoft pour faciliter la résolution des problèmes de ce problème. Si la passerelle n’est pas connectée au service cloud, vous pouvez choisir d’enregistrer et archiver les journaux de passerelle.  
- Améliorations de l’interface utilisateur du Gestionnaire de configuration de passerelle :
    - Améliorer la visibilité état de la passerelle sous l’onglet Accueil.
    - Contrôles réorganisées et simplifiées.
- Vous pouvez copier les données à partir d’un espace de stockage à l’aide de l' [outil Aperçu de la copie sans code](data-factory-copy-data-wizard-tutorial.md). Voir [Copie intermédiaire](data-factory-copy-activity-performance.md#staged-copy) pour plus d’informations sur cette fonctionnalité en général. 
- Vous pouvez utiliser la passerelle de gestion des données à des données de pénétration directement à partir d’une base de données SQL Server en local dans apprentissage automatique Azure.
- Améliorations des performances
    - Améliorer les performances d’affichage de schéma/aperçu par rapport à SQL Server dans l’outil Aperçu de la copie sans code.



## <a name="11259531"></a>1.12.5953.1
- Correctifs

## <a name="11159181"></a>1.11.5918.1

- Taille maximale du journal des événements passerelle a été augmentée de 1 Mo à 40 Mo.
- Un message d’avertissement s’affiche au cas où un redémarrage est nécessaire au cours de la mise à jour automatique de passerelle. Vous pouvez choisir de redémarrer droite puis ou version ultérieure. 
- En cas d’échec de la mise à jour automatique, programme d’installation de la passerelle tentatives de mise à jour automatique trois fois au maximum.
- Améliorations des performances
    - Améliorer les performances de chargement de grands tableaux à partir du serveur local dans un scénario de copie sans code.
- Correctifs

## <a name="11058921"></a>1.10.5892.1

- Améliorations des performances
- Correctifs

## <a name="1958652"></a>1.9.5865.2

- Zéro capacité de mise à jour automatique tactile
- Nouvelle icône de barre d’état avec indicateurs d’état passerelle
- Possibilité de « Mise à jour maintenant » à partir du client
- Possibilité de définir l’heure de mise à jour de la planification
- Script PowerShell pour activer/désactiver la mise à jour automatique/désactivation
- Prise en charge de format JSON  
- Améliorations des performances
- Correctifs

## <a name="1858221"></a>1.8.5822.1

- Améliorer l’expérience de résolution des problèmes
- Améliorations des performances
- Correctifs

### <a name="1757951"></a>1.7.5795.1

- Améliorations des performances
- Correctifs

### <a name="1757641"></a>1.7.5764.1

- Améliorations des performances
- Correctifs

### <a name="1657351"></a>1.6.5735.1

- Prise en charge en local HADOOP Source/récepteur
- Améliorations des performances
- Correctifs

### <a name="1656961"></a>1.6.5696.1

- Améliorations des performances
- Correctifs

### <a name="1656761"></a>1.6.5676.1

- Outils de diagnostic prise en charge dans le Gestionnaire de Configuration
- Prise en charge des colonnes d’un tableau des sources de données tabulaires destinée aux données Azure
- Prise en charge SQL DW destinée aux données Azure
- Prise en charge Reclusive dans BlobSource et FichierGestion destinée aux données Azure
- Prise en charge CopyBehavior – MergeFiles, PreserveHierarchy et FlattenHierarchy dans BlobSink et FileSink avec une copie binaire destinée aux données Azure
- Prend en charge les rapports progression d’Azure Data Factory des activités de copie
- Prise en charge des données Source connectivité Validation destinée aux données Azure
- Correctifs


### <a name="1656721"></a>1.6.5672.1

- Nom de la table prise en charge pour la source de données ODBC pour Azure Data Factory
- Améliorations des performances
- Correctifs

### <a name="1656581"></a>1.6.5658.1

- Fichier de prise en charge de récepteurs destinée aux données Azure
- Prise en charge conservant la hiérarchie dans copie binaire destinée aux données Azure
- Prise en charge de copie activité idempotence des données Azure usine
- Correctifs

### <a name="1656401"></a>1.6.5640.1

- Prise en charge 3 davantage de sources de données pour Azure Data Factory (ODBC, OData, HDFS)
- Prise en charge guillemet dans analyseur csv destinée aux données Azure
- Prise en charge de compression (BZip2)
- Correctifs

### <a name="1556121"></a>1.5.5612.1

- Prise en charge des bases de données relationnelles cinq pour Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata et Sybase)
- Prise en charge de compression (Gzip et Deflate)
- Améliorations des performances
- Correctifs


### <a name="1455491"></a>1.4.5549.1

- Ajouter la prise en charge de source de données Oracle destinée aux données Azure
- Améliorations des performances
- Correctifs

### <a name="1454921"></a>1.4.5492.1

- Binaire unifié qui prend en charge les services Microsoft Azure Data Factory et Office 365 Power BI
- Affiner le processus de Configuration de l’interface utilisateur et d’enregistrement
- Azure Data Factory – Azure entrant et sortant prend en charge pour la source de données SQL Server

### <a name="1253031"></a>1.2.5303.1

-   Résoudre le problème de délai d’expiration pour prendre en charge plusieurs connexions aux sources de données. 
    
### <a name="1155268"></a>1.1.5526.8

- Nécessite .NET Framework 4.5.1 préalablement pendant l’installation.

### <a name="1051442"></a>1.0.5144.2

- Aucune modification qui affectent les scénarios d’usine de données Azure. 
