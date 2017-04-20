<properties
    pageTitle="Solution de gestion de capacité de journal Analytique | Microsoft Azure"
    description="Vous pouvez utiliser la solution de planification de la capacité dans Analytique journal pour vous aider à comprendre la capacité de vos serveurs Hyper-V gérés par le Gestionnaire de Machine virtuelle System Center"
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="capacity-management-solution-in-log-analytics"></a>Solution de gestion de capacité de journal Analytique


Vous pouvez utiliser la solution planification de la capacité dans journal Analytique pour vous aider à comprendre la capacité de vos serveurs Hyper-V gérés par le Gestionnaire de Machine virtuelle System Center. Cette solution nécessite System Center Operations Manager et System Center Machine virtuelle Manager. Planification de la capacité n’est pas disponible si vous utilisez uniquement agents directement connecté. Vous installez la solution pour mettre à jour de l’agent Operations Manager. La solution lit compteurs sur le serveur analysé et envoie des données d’utilisation du service OMS dans le nuage pour traitement. Logique est appliquée aux données d’utilisation et le service cloud enregistre les données. Au fil du temps, des modèles d’utilisation sont identifiés et capacité est projetée, en fonction de consommation en cours.

Par exemple, une projection peut identifier lorsque cœurs supplémentaires ou davantage de mémoire est nécessaires pour un serveur individuel. Dans cet exemple, la projection peut indiquer que des 30 derniers jours le serveur devra davantage de mémoire. Cela peut vous aider à planifier une mise à niveau de mémoire au cours de la fenêtre de maintenance suivante du serveur, qui peut-être se produire toutes les deux semaines.

>[AZURE.NOTE] La solution de gestion de la capacité n’est pas disponible pour être ajoutés aux espaces de travail. Clients ayant la solution de gestion de la capacité installée peuvent continuer à utiliser la solution.  

Solution de planification de la capacité est en train d’être mis à jour pour résoudre le client suivant signalées défis :

- Configuration minimale requise pour utiliser le Gestionnaire de Machine virtuelle et Operations Manager
- Impossibilité de personnaliser ou un filtre en fonction des groupes
- Agrégation de données horaires ne fréquents pas assez
- Aucune insights niveau machine virtuelle
- Fiabilité des données

Avantages de la nouvelle solution capacité :

- Prise en charge de la collecte de données granulaires avec une précision et fiabilité améliorée
- Prise en charge pour Hyper-V sans avoir besoin de VMM
- Visualisation de mesures dans PowerBI
- Informations sur l’utilisation du niveau machine virtuelle


## <a name="installing-and-configuring-the-solution"></a>Installation et configuration de la solution
Utilisez les informations suivantes pour installer et configurer la solution.

- Operations Manager est requis pour la solution de gestion de la capacité.
- Machine virtuelle Manager est requis pour la solution de gestion de la capacité.
- Operations Manager connectivité avec Virtual Machine Manager (VMM) est requise. Pour plus d’informations sur la connexion les systèmes, voir [comment connecter VMM avec Operations Manager](http://technet.microsoft.com/library/hh882396.aspx).
- Operations Manager doit est relié à journal Analytique.
- Ajouter la solution de gestion de la capacité à votre espace de travail OMS à l’aide de la procédure décrite dans [solutions ajouter journal Analytique à partir de la galerie de Solutions](log-analytics-add-solutions.md).  Il n’existe aucune autre configuration requise.


## <a name="capacity-management-data-collection-details"></a>Détails de collection de sites de capacité gestion des données

Gestion de la capacité collecte des données de performance, les métadonnées et les données d’état à l’aide d’agents que vous avez activés.

Le tableau suivant indique les méthodes de collecte de données et autres informations sur la manière dont les données sont collectées pour la gestion de la capacité.

| plateforme | Agent directe | Agent SCOM | Stockage Azure | SCOM obligatoire ? | Données de l’agent SCOM envoyées par groupe d’administration | fréquence de collection de sites |
|---|---|---|---|---|---|---|
|Windows|![N°](./media/log-analytics-capacity/oms-bullet-red.png)|![Oui](./media/log-analytics-capacity/oms-bullet-green.png)|![N°](./media/log-analytics-capacity/oms-bullet-red.png)|            ![Oui](./media/log-analytics-capacity/oms-bullet-green.png)|![Oui](./media/log-analytics-capacity/oms-bullet-green.png)| toutes les heures|

Le tableau suivant répertorie des exemples de types de données collectées par la gestion des capacités :

|**Type de données**|**Champs**|
|---|---|
|Métadonnées|BaseManagedEntityId, ObjectStatus, unité d’organisation, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, adresse IP, ForestDNSName, Nom_ordinateur_netbios, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, adresse IP, NetbiosDomainName, LogicalProcessors, NomDNS, DisplayName, DomainDnsName, ActiveDirectorySite, au PrincipalName, OffsetInMinuteFromGreenwichTime|
|Performances|Nom_objet, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded|
|État|StateChangeEventId, identifiant d’état, NewHealthState, OldHealthState, contexte, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified|

## <a name="capacity-management-page"></a>Page de gestion de capacité


 Une fois la solution de planification de la capacité est installée, vous pouvez afficher la capacité de vos serveurs analysés à l’aide de la vignette de **Planification de la capacité** dans la page **vue d’ensemble** dans OMS.

![image d’une vignette de planification de la capacité](./media/log-analytics-capacity/oms-capacity01.png)

La vignette s’ouvre le tableau de bord de **Gestion de la capacité** dans laquelle vous pouvez afficher un résumé de la capacité de votre serveur. La page affiche les mosaïques suivants que vous pouvez cliquer sur :

- *Machine virtuelle Nb*: affiche le nombre de jours restant pour la capacité des machines virtuelles
- *Calculer*: affiche cœurs et la quantité de mémoire disponible
- *Stockage*: affiche l’espace disque utilisé et disque latence moyenne
- *Recherche*: l’Explorateur de données que vous pouvez utiliser pour rechercher des données dans le système OMS

![image du tableau de bord de gestion de la capacité](./media/log-analytics-capacity/oms-capacity02.png)


### <a name="to-view-a-capacity-page"></a>Pour afficher une page de capacité

- Dans la page **vue d’ensemble** , cliquez sur **Gestion de la capacité**, puis cliquez sur **Calculer** ou **stockage**.

## <a name="compute-page"></a>Calculer la page

Vous pouvez utiliser le tableau de bord **de calcul** dans Microsoft Azure OMS pour afficher des informations de capacité sur l’utilisation, projeté jours de la capacité et l’efficacité liés à votre infrastructure. Vous utilisez la zone **d’utilisation** pour afficher de l’UC core et la mémoire dans vos hôtes machine virtuelle. Vous pouvez utiliser l’outil de projection pour évaluer la capacité est censée être disponible pour une période donnée. Vous pouvez utiliser la zone de **l’efficacité** pour voir comment efficace vos hôtes machine virtuelle. Vous pouvez afficher des détails sur les éléments liés en cliquant dessus.

Vous pouvez générer un classeur Excel pour les catégories suivantes :

- Supérieure hosts avec une taux d’utilisation maximal
- Supérieure hosts avec le plus élevée utilisation de la mémoire
- Supérieure hôtes avec machines virtuelles inefficaces
- Principales vues par l’utilisation
- Hôtes bas par l’utilisation

![image de la page de gestion de la capacité calcul](./media/log-analytics-capacity/oms-capacity03.png)


Les zones suivantes sont affichent dans le tableau de bord **Calculer** :

**Taux d’utilisation**: utilisation de vue du processeur core et la mémoire dans vos hôtes machine virtuelle.

- *Cœurs utilisé*: somme pour tous les hôtes (% de l’utilisation du processeur multiplié par le nombre de cœurs physiques sur hôte).
- *Cœurs gratuit*: nombre Total de cœurs physiques moins cœurs utilisés.
- *Pourcentage cœurs disponibles*: libérer cœurs physiques divisés par le nombre total de cœurs physiques.
- *Cœurs virtuel par machine virtuelle*: Total virtuels cœurs dans le système divisé par le nombre total de machines virtuelles du système.
- *Cœurs virtuel physique cœurs ratio*: Ratio de total cœurs physiques à cœurs physiques qui sont utilisées par des machines virtuelles dans le système.
- *Nombre de cœurs virtuel disponibles*: virtuel core ratio cœurs physiques multipliée par les cœurs physiques disponibles.
- *Mémoire utilisée*: somme de la mémoire qui est utilisée par tous les hôtes.
- *Mémoire*: mémoire physique moins utilisé mémoire totale.
- *Pourcentage de mémoire disponible*: libérer de la mémoire physique divisée par la quantité de mémoire physique total.
- *Mémoire virtuelle par machine virtuelle*: mémoire virtuelle totale du système divisé par le nombre total de machines virtuelles du système.
- *Mémoire virtuelle physique ratio mémoire*: mémoire virtuelle totale du système divisé par la mémoire physique totale du système.
- *Mémoire virtuelle disponible*: mémoire virtuelle ratio mémoire physique multiplié par la quantité de mémoire physique disponible.

**Outil de projection**

À l’aide de l’outil de projection, vous pouvez afficher les tendances historiques pour votre utilisation des ressources. Cela inclut les tendances de l’utilisation de machines virtuelles, la mémoire, core et stockage. La fonctionnalité de projection utilise un algorithme de projection pour vous aider à déterminer si vous exécutez se déconnecter de chacune des ressources. Cela vous permet de calculer la capacité NOMPROPRE planification afin que vous sachiez lorsque vous avez besoin acheter davantage de capacité (par exemple, la quantité de mémoire, cœurs ou du stockage).

**Efficacité**

- *Machine virtuelle Idle*: à l’aide de moins de 10 % de la mémoire du processeur et 10 % pour la période spécifiée.
- *Machine virtuelle surutilisée*: à l’aide de plus de 90 % de la mémoire du processeur et 90 % pour la période spécifiée.
- *Hôte Idle*: à l’aide de moins de 10 % de la mémoire du processeur et 10 % pour la période spécifiée.
- *Hôte surutilisée*: à l’aide de plus de 90 % de la mémoire du processeur et 90 % pour la période spécifiée.

### <a name="to-work-with-items-on-the-compute-page"></a>Pour travailler avec des éléments dans la page de calcul

1. Dans le tableau de bord **de calcul** , dans la zone **d’utilisation** , afficher des informations de capacité sur les cœurs de processeur et la mémoire en cours d’utilisation.
2. Cliquez sur un élément pour l’ouvrir dans la page de **recherche** et afficher des informations détaillées à ce sujet.
3. Dans l’outil de **Projection** , déplacez le curseur de date pour afficher une projection de la capacité qui sera utilisée sur la date de que votre choix.
4. Dans la zone de **l’efficacité** , afficher des informations de l’efficacité de capacité sur machines virtuelles et des hôtes de machine virtuelle.

## <a name="direct-attached-storage-page"></a>Page de stockage direct

Vous pouvez utiliser le tableau de bord de **Stockage DAS** dans OMS pour afficher la capacité des informations sur l’utilisation du stockage, les performances du disque et les jours projetés de capacité de disque. Vous utilisez la zone **d’utilisation** pour afficher l’espace disque dans vos hôtes machine virtuelle. La zone de **Performances du disque** permet d’afficher des disque débit et la latence dans vos hôtes machine virtuelle. Vous pouvez également utiliser l’outil de projection pour évaluer la capacité est censée être disponible pour une période donnée. Vous pouvez afficher des détails sur les éléments liés en cliquant dessus.

Vous pouvez générer un classeur Excel à partir de ces informations de capacité pour les catégories suivantes :

- Utilisation de l’espace disque supérieure par hôte
- Latence moyenne supérieure par hôte

Les zones suivantes sont affichent dans la page de **stockage** :

- *Taux d’utilisation*: afficher l’espace disque dans vos hôtes machine virtuelle.
- *Espace disque total*: somme (espace disque logique) pour tous les hôtes
- *L’espace disque utilisé*: somme (espace disque logique utilisé) pour tous les hôtes
- *Espace disque disponible*: espace disque moins espace disque utilisé Total
- *Pourcentage disque utilisé*: espace disque divisé par l’espace disque total utilisé
- *Pourcentage disque disponible*: espace disque disponible divisé par l’espace disque total

![image de la page de capacité gestion stockage DAS](./media/log-analytics-capacity/oms-capacity04.png)

**Performances du disque**

À l’aide de OMS, vous pouvez afficher la tendance historique de l’utilisation de l’espace disque. La fonctionnalité de projection utilise un algorithme pour l’utilisation de futurs projets. Pour l’utilisation de l’espace en particulier, la fonctionnalité de projection vous permet de projet lorsque vous pouvez manquer d’espace disque. Cela vous permettra de planifier stockage NOMPROPRE et savoir quand vous avez besoin acheter davantage de stockage.

**Outil de projection**

À l’aide de l’outil de projection, vous pouvez afficher les tendances historiques pour votre utilisation de l’espace disque. La fonctionnalité de projection vous permet également de projet lorsque vous exécutez suffisamment d’espace disque. Cela vous permettra de planifier la capacité NOMPROPRE et savoir quand vous avez besoin acheter davantage de capacité de stockage.

### <a name="to-work-with-items-on-the-direct-attached-storage-page"></a>Pour travailler avec des éléments dans la page de stockage DAS

1. Dans le tableau de bord de **Stockage DAS** , dans la zone **d’utilisation** , vous pouvez afficher les informations de l’utilisation du disque.
2. Cliquez sur un élément lié pour l’ouvrir dans la page de **recherche** et afficher des informations détaillées à ce sujet.
3. Dans la zone de **Performances du disque** , vous pouvez afficher les informations de débit et la latence du disque.
4. Dans l' **outil de Projection**, déplacez le curseur de date pour afficher une projection de la capacité qui sera utilisée sur la date de que votre choix.


## <a name="next-steps"></a>Étapes suivantes

- [Recherches de journaux dans journal Analytique](log-analytics-log-searches.md) permet d’afficher les données de gestion détaillée de la capacité.
