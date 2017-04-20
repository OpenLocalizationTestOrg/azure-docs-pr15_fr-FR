<properties
    pageTitle="Documentation pour le gouvernement Azure | Microsoft Azure"
    description="Cela fournit une comparaison des fonctionnalités et des conseils sur le développement d’applications pour le gouvernement Azure."
    services="Azure-Government"
    cloud="gov"
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/25/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-monitoring-and-management"></a>Gestion et contrôle pour le gouvernement Azure

Cet article décrit la surveillance et de gestion des services variantes et les considérations sur l’environnement pour le gouvernement Azure.

## <a name="automation"></a>Automatisation

Automation est généralement disponible dans Azure pour le gouvernement.

### <a name="variations"></a>Variantes

Les fonctionnalités d’automatisation suivantes ne sont pas actuellement disponibles dans Azure pour le gouvernement.

+ Création d’informations d’identification pour l’authentification principe de Service

Pour plus d’informations, voir [documentation publique Automation](../automation/automation-intro.md).

## <a name="log-analytics"></a>Journal Analytique

Journal Analytique est généralement disponible dans Azure pour le gouvernement.

### <a name="variations"></a>Variantes

Les fonctionnalités de journal Analytique et les solutions suivantes ne sont pas actuellement disponibles dans Azure pour le gouvernement.

+ Solutions en mode Aperçu avant dans Microsoft Azure, notamment :
  - Solution de surveillance réseau
  - Solution de surveillance des dépendances d’applications
  - Solution Office 365
  - Solution Analytique de mettre à niveau Windows 10
  - Solution d’analyse des applications
  - Solution de mise en réseau Analytique Azure
  - Solution automatisation Analytique Azure
  - Solution de clé l’archivage sécurisé Analytique
+ Solutions et les fonctionnalités qui nécessitent des mises à jour de logiciels en local, y compris :
  - Intégration avec système Centre Operations Manager 2016 (les versions antérieures d’Operations Manager sont pris en charge)
  - Groupes d’ordinateurs à partir de System Center Configuration Manager
  - Solution concentrateur en surface
+ Des fonctionnalités dans l’aperçu dans Azure public, y compris :
  - Exportation de données pour Power BI
+ Métrique Azure et les diagnostics de Windows Azure
+ Applications mobiles opérations Management Suite

Les URL des journaux Analytique sont différents dans Azure pour le gouvernement :

| Public Azure | Pour le gouvernement Azure | Notes |
|--------------|------------------|-------|
| MMS.Microsoft.com | OMS.Microsoft.us | Ouvrez une session portail Analytique |
| *workspaceId*. ods.opinsights.azure.com | *workspaceId*. ods.opinsights.azure.us | [Collecteur de données API](../log-analytics/log-analytics-data-collector-api.md) 
| \*. ods.opinsights.azure.com | \*. ods.opinsights.azure.us | Communication de l’agent - [Configuration des paramètres de pare-feu](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. oms.opinsights.azure.com | \*. oms.opinsights.azure.us | Communication de l’agent - [Configuration des paramètres de pare-feu](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. blob.core.windows.net | \*. blob.core.usgovcloudapi.net | Communication de l’agent - [Configuration des paramètres de pare-feu](../log-analytics/log-analytics-proxy-firewall.md) |


Les fonctionnalités de journal Analytique suivantes se comportent différemment dans Azure pour le gouvernement :

+ L’Agent Windows doivent être téléchargé à partir du [portail de journal Analytique](https://oms.microsoft.us) pour Azure gouvernement.
+ Pour vous connecter à votre serveur de gestion de System Center Operations Manager à journal Analytique, vous devez télécharger et importer les packs d’administration mis à jour.
  1. Téléchargez et enregistrez la [mise à jour des packs d’administration](http://go.microsoft.com/fwlink/?LinkId=828749).
  2. Décompressez le fichier que vous avez téléchargé.
  3. Importer les packs d’administration dans Operations Manager. Pour plus d’informations sur l’importation d’un pack d’administration à partir d’un disque, voir [comment importer un Operations Manager Management Pack](http://technet.microsoft.com/library/hh212691.aspx) sur le site Web Microsoft TechNet.
  4. Pour connecter Operations Manager au journal Analytique, suivez les étapes de [Connexion Operations Manager pour journal Analytique](../log-analytics/log-analytics-om-agents.md).


## <a name="frequently-asked-questions"></a>Forum aux questions

+ Puis-je migrer les données à partir de journal Analytique dans Microsoft Azure Azure gouvernement ?
  - Non. Il n’est pas possible de déplacer des données ou votre espace de travail à partir de Microsoft Azure Azure gouvernement.
+ Puis-je basculer entre Microsoft Azure et pour le gouvernement Azure espaces de travail à partir du portail opérations gestion Suite journal Analytique ?
  - Non. Les portails pour Microsoft Azure et pour le gouvernement Azure sont distincts et ne pas partagent les informations.

Pour plus d’informations, voir [journal Analytique publique documentation](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Étapes suivantes

Pour des informations supplémentaires et des mises à jour, vous abonner à la <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog de Microsoft Azure pour le gouvernement.</a>
