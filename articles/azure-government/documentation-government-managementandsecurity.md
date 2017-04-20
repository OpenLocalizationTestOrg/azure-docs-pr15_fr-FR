<properties
    pageTitle="Documentation pour le gouvernement Azure | Microsoft Azure"
    description="Cela fournit une comparaison des fonctionnalités et des recommandations sur le développement d’applications pour le gouvernement Azure"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="scooxl"
    manager="zakramer"
    editor=""/>
<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/25/2016"
    ms.author="scooxl"/>
#  <a name="azure-government-management-and-security"></a>Sécurité et gestion pour le gouvernement azure

## <a name="automation"></a>Automatisation

Automation est généralement disponible dans Azure pour le gouvernement.

### <a name="variations"></a>Variantes

Les fonctionnalités d’automatisation suivantes ne sont pas actuellement disponibles dans Azure pour le gouvernement.

+ Création d’informations d’identification pour l’authentification principe de Service

Pour plus d’informations, voir [documentation publique Automation](../automation/automation-intro.md).


##  <a name="key-vault"></a>Archivage sécurisé clé
Pour plus d’informations sur ce service et comment l’utiliser, reportez-vous à la <a href="https://azure.microsoft.com/documentation/services/key-vault">documentation publique l’archivage sécurisé de clé Azure.</a>
### <a name="data-considerations"></a>Considérations relatives à des données
Les informations suivantes identifient la limite pour le gouvernement Azure pour l’archivage sécurisé de Azure clé :

| Données réglée/contrôlée autorisé | Données réglée/contrôlée non autorisé |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Toutes les données chiffrées avec une clé de l’archivage sécurisé de clé Azure peuvent contenir des données réglementés/contrôlée. | Métadonnées de l’archivage sécurisé clé Azure ne sont pas autorisée pour contenir contrôlé à l’exportation des données. Ces métadonnées incluent toutes les données de configuration entrées lors de la création et la gestion de votre l’archivage sécurisé clé.  Ne saisissez réglementés/contrôlé des données dans les champs suivants : noms de groupe de ressources, les noms de l’archivage sécurisé clé, nom de l’abonnement |

Clé de l’archivage sécurisé est généralement disponible dans Azure pour le gouvernement. Obtenus public, il n’existe aucune extension, l’archivage sécurisé clé est uniquement disponible via PowerShell et infrastructure du langage commun.
## <a name="log-analytics"></a>Journal Analytique
Journal Analytique est généralement disponible dans Azure pour le gouvernement. 

### <a name="variations"></a>Variantes

Les fonctionnalités de journal Analytique et les solutions suivantes ne sont pas actuellement disponibles dans Azure pour le gouvernement. Cette liste est mis à jour lorsque l’état des fonctions / solutions change.

+ Solutions en mode Aperçu avant dans Azure public, y compris :
  - Solution de surveillance réseau
  - Contrôle de dépendance des applications
  - Solution Office 365
  - Solution Analytique de mettre à niveau Windows 10
  - Analyse de l’application
  - Solution de mise en réseau Analytique Azure
  - Automatisation Azure Analytique
  - L’archivage sécurisé clés Analytique
+ Solutions et les fonctionnalités qui nécessitent des mises à jour de logiciels en local, y compris
  - Intégration avec système Centre Operations Manager 2016 (les versions antérieures d’Operations Manager sont pris en charge)
  - Groupes d’ordinateurs à partir du Gestionnaire de Configuration de System Center
  - Solution concentrateur en surface
+ Des fonctionnalités dans l’aperçu dans Azure public, y compris
  - Exportation de données vers PowerBI
+ Métrique Azure et les diagnostics de Windows Azure
+ Applications mobiles OMS

Les URL des journaux Analytique sont différents dans Azure pour le gouvernement :

| Public Azure | Pour le gouvernement Azure | Notes |
|--------------|------------------|-------|
| MMS.Microsoft.com | OMS.Microsoft.us | Ouvrez une session portail Analytique |
| *workspaceId*. ods.opinsights.azure.com | *workspaceId*. ods.opinsights.azure.us | [Collecteur de données API](../log-analytics/log-analytics-data-collector-api.md) 
| \*. ods.opinsights.azure.com | \*. ods.opinsights.azure.us | Communication de l’agent - [Configuration des paramètres de pare-feu](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. oms.opinsights.azure.com | \*. oms.opinsights.azure.us | Communication de l’agent - [Configuration des paramètres de pare-feu](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. blob.core.windows.net | \*. blob.core.usgovcloudapi.net | Communication de l’agent - [Configuration des paramètres de pare-feu](../log-analytics/log-analytics-proxy-firewall.md) |


Les fonctionnalités de journal Analytique suivantes comportent différemment dans Azure pour le gouvernement :

+ L’agent Windows doit être téléchargé à partir du [portail de journal Analytique](https://oms.microsoft.us) pour Azure gouvernement.
+ Pour vous connecter à votre serveur de gestion de System Center Operations Manager à journal Analytique, vous devez télécharger et importer les Packs d’administration mis à jour.
  1. Téléchargez et enregistrez la [mise à jour des packs d’administration](http://go.microsoft.com/fwlink/?LinkId=828749)
  2. Décompresser le fichier que vous avez téléchargé
  3. Importer les packs d’administration dans Operations Manager. Pour plus d’informations sur l’importation d’un pack d’administration à partir d’un disque, consultez la rubrique [comment importer un Operations Manager Management Pack](http://technet.microsoft.com/library/hh212691.aspx) sur le site Web Microsoft TechNet.
  4. Pour connecter Operations Manager au journal Analytique, suivez les étapes de [Connexion Operations Manager pour journal Analytique](../log-analytics/log-analytics-om-agents.md) 



### <a name="frequently-asked-questions"></a>Forum aux questions

+ Puis-je migrer les données à partir de journal Analytique dans public pour le gouvernement Azure à Azure ?
  - Non. Il n’est pas possible de déplacer des données ou votre espace de travail public gouvernement Azure à Azure.
+ Puis-je passer publics Azure et pour le gouvernement Azure les espaces de travail à partir du portail OMS journal Analytique ?
  - Non. Les portails pour public Azure et pour le gouvernement Azure sont distincts et ne pas partagent les informations. 

Pour plus d’informations, voir [journal Analytique publique documentation](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Étapes suivantes

Pour des informations supplémentaires et des mises à jour, vous abonner à la <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog de Microsoft Azure pour le gouvernement.</a>
 
