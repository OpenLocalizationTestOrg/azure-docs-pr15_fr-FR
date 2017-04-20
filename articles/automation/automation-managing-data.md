<properties 
   pageTitle="Gestion des données Azure automatisation | Microsoft Azure"
   description="Cet article contient plusieurs rubriques pour la gestion d’un environnement d’automatisation Azure.  Actuellement inclut conservation des données et la sauvegarde sinistre Azure Automation dans Azure Automation."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/02/2016"
   ms.author="bwren;sngun" />

# <a name="managing-azure-automation-data"></a>Gestion des données Azure Automation

Cet article contient plusieurs rubriques pour la gestion d’un environnement d’automatisation Azure.

## <a name="data-retention"></a>Conservation des données

Lorsque vous supprimez une ressource dans Azure Automation, elle est conservée pour 90 jours fins d’audit avant d’être supprimés définitivement.  Vous ne pouvez pas voir ou utiliser la ressource pendant ce temps.  Cette stratégie s’applique également aux ressources qui appartiennent à un compte d’automation est supprimé.

Automatisation Azure supprime automatiquement et définitivement travaux antérieurs à 90 jours.

Le tableau suivant résume la stratégie de rétention pour différentes ressources.

|Données|Stratégie|
|:---|:---|
|Comptes|Définitive 90 jours après que le compte est supprimé par un utilisateur.|
|Actifs|Supprimer définitivement 90 jours après que l’actif est supprimé par un utilisateur, ou 90 jours après le compte qui contient que l’élément est supprimé par un utilisateur.|
|Modules|Supprimer définitivement 90 jours après que le module est supprimé par un utilisateur, ou 90 jours après le compte qui contient que le module est supprimé par un utilisateur.|
|Procédures opérationnelles|Supprimer définitivement 90 jours après que la ressource est supprimée par un utilisateur, ou 90 jours après le compte qui contient que la ressource est supprimée par un utilisateur.|
|Tâches|Supprimés et définitivement supprimées 90 jours après la dernière modification. Il peut s’agir d’une fois que la tâche se termine, est arrêtée ou est suspendue.|
|Fichiers de Configurations/MOF nœud| Ancienne configuration nœud est supprimée définitivement 90 jours après qu’une nouvelle configuration nœud est générée.|
|DSC nœuds| Définitive 90 jours après que le nœud est supprimée de compte Automation à l’aide de portail Azure ou l’applet de commande [Unregister AzureRMAutomationDscNode](https://msdn.microsoft.com/library/mt603500.aspx) dans Windows PowerShell. Nœuds sont également définitivement supprimées 90 jours après le compte qui contient que le nœud est supprimé par un utilisateur. |
|Nœud rapports| 90 jours après qu’un nouveau rapport est généré pour ce nœud de manière définitive|

La stratégie de rétention s’applique à tous les utilisateurs et actuellement ne peuvent pas être personnalisée.

## <a name="backing-up-azure-automation"></a>Sauvegarder Automation Azure

Lorsque vous supprimez un compte automation dans Microsoft Azure, tous les objets dans le compte sont supprimés, y compris les procédures opérationnelles, modules, configurations, paramètres, tâches et éléments. Les objets ne peuvent pas être récupérés une fois que le compte est supprimé.  Vous pouvez utiliser les informations suivantes pour sauvegarder le contenu de votre compte d’automatisation avant de le supprimer. 

### <a name="runbooks"></a>Procédures opérationnelles

Vous pouvez exporter les procédures opérationnelles aux fichiers de script à l’aide du portail de gestion Azure ou l’applet de commande [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) dans Windows PowerShell.  Ces fichiers de script peuvent être importés dans un autre compte automation comme décrit dans [Création ou en important un Runbook](https://msdn.microsoft.com/library/dn643637.aspx).


### <a name="integration-modules"></a>Modules d’intégration

Vous ne pouvez pas exporter des modules d’intégration d’automatisation Azure.  Vous devez vous assurer qu’elles sont disponibles en dehors du compte automation.

### <a name="assets"></a>Actifs

Vous ne pouvez pas exporter [actifs](https://msdn.microsoft.com/library/dn939988.aspx) Automation Azure.  À l’aide du portail de gestion Azure, vous devez Notez les informations de variables, informations d’identification, des certificats, des connexions et des planifications.  Vous devez ensuite créer manuellement les actifs qui sont utilisés par les procédures opérationnelles que vous importez dans un autre automation.

Vous pouvez utiliser les [applets de commande Azure](https://msdn.microsoft.com/library/dn690262.aspx) pour récupérer les détails des éléments non chiffrés et enregistrer les pour référence ultérieure ou créer actifs équivalents dans un autre compte automation.

Vous ne pouvez pas récupérer la valeur des variables chiffrés ou le champ mot de passe des informations d’identification à l’aide des applets de commande.  Si vous ne connaissez pas ces valeurs, vous pouvez les récupérer à partir d’un runbook à l’aide des activités de [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) et [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) .

Vous ne pouvez pas exporter des certificats à partir d’Azure Automation.  Vous devez vous assurer que tous les certificats sont disponibles en dehors d’Azure.

### <a name="dsc-configurations"></a>Configurations DSC

Vous pouvez exporter vos configurations aux fichiers de script à l’aide du portail de gestion Azure ou l’applet de commande [Exporter AzureRmAutomationDscConfiguration](https://msdn.microsoft.com/library/mt603485.aspx) dans Windows PowerShell. Ces configurations peuvent être importées et utilisées dans un autre compte automation.


##<a name="geo-replication-in-azure-automation"></a>Geo-réplication dans Azure Automation

Geo-réplication, standard dans les comptes Azure automatisation, sauvegarde des données de compte pour une région géographique différente pour la redondance. Vous pouvez choisir une zone principale lors de la configuration de votre compte, et puis une région secondaire lui est affectée automatiquement. Les données secondaires, copiées à partir de la région principale, sont à jour en permanence en cas de perte de données.  

Le tableau suivant montre les paires de zones principales et secondaires disponibles.

|Principal            |Secondaire
| ---------------   |----------------
|États-Unis centre sud   |États-Unis centre nord
|États-Unis est 2          |États-Unis centre
|Europe ouest        |Europe du Nord
|Asie du Sud-est    |Asie de l’est
|Orient Japon         |Japon ouest

Dans l’éventualité qu’une donnée de région principal est perdue, Microsoft tente de récupérer. Si les données principales ne peuvent pas être récupérées, puis geo basculement est effectué et les clients concernés seront communiquées à ce sujet via son abonnement.

