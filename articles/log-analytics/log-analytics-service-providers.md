<properties
    pageTitle="Ouvrez une session Analytique fonctionnalités pour les fournisseurs de Service | Microsoft Azure"
    description="Journal Analytique permettent de fournisseurs de services gérés (MSP), grandes entreprises, un logiciel de fournisseurs () et les fournisseurs de services d’hébergement gérer et analyser les serveurs local du client ou infrastructure cloud."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="richrund"/>

# <a name="log-analytics-features-for-service-providers"></a>Journal Analytique fonctionnalités pour les fournisseurs de Service

Journal Analytique permettent de fournisseurs de services gérés (MSP), grandes entreprises, éditeurs de logiciels (éditeurs de logiciels) et des fournisseurs de services d’hébergement gérer et analyser des serveurs local du client ou infrastructure cloud. 

Grandes entreprises partagent nombreuses similitudes avec les fournisseurs de service, en particulier lorsqu’il y a une équipe informatique centralisée qui est responsable de la gestion informatique de nombreux différentes divisions. Pour simplifier, ce document utilise le magasin de termes *fournisseur de services* , mais la même fonctionnalité est également disponible pour les entreprises et d’autres clients.

## <a name="cloud-solution-provider"></a>Fournisseur de solutions de cloud

Des partenaires et des fournisseurs de services qui font partie du programme [Cloud Solution fournisseur (fournisseur)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) , journal Analytique est un des services Azure disponibles sur un abonnement fournisseur. 

Pour journal Analytique, les fonctionnalités suivantes sont activées dans les abonnements *Cloud Solution fournisseur* .

En tant que *Fournisseur de solutions de Cloud* , vous pouvez :

+ Créer des espaces de travail journal Analytique dans abonnement d’un client (client).
+ Espaces de travail Access créés par les clients. 
+ Ajouter et supprimer l’accès utilisateur à l’espace de travail à l’aide de la gestion des utilisateurs Azure. Lors de la page de la gestion des utilisateurs dans un espace de travail d’un client dans le portail OMS sous paramètres n’est pas disponible
  - Journal Analytique ne reconnaît pas accès basé sur un rôle encore - attribution à un utilisateur `reader` autorisation dans le portail Azure leur permet d’apporter des modifications de configuration dans le portail OMS

Pour vous connecter à un abonnement d’un client, vous devez spécifier l’identificateur de client. L’identificateur client est souvent cette dernière partie de l’adresse de messagerie utilisée pour vous connecter.

+ Dans le portail OMS, ajoutez `?tenant=contoso.com` dans l’URL pour le portail. Par exemple,`mms.microsoft.com/?tenant=contoso.com`
+ Dans PowerShell, utilisez la `-Tenant contoso.com` paramètre lorsque vous utilisez `Add-AzureRmAccount` applet de commande
+ L’identificateur de client est ajouté automatiquement lorsque vous utilisez la `OMS portal` lien à partir du portail Azure pour ouvrir et se connecter au portail OMS pour l’espace de travail sélectionné

En tant que *client* d’un fournisseur de Solution Cloud, vous pouvez :

+ Créer des espaces de travail analytique journal pour un abonnement fournisseur
+ Espaces de travail Access créés par le fournisseur
  -  Utiliser la `OMS portal` lien à partir du portail Azure pour ouvrir et se connecter au portail OMS pour l’espace de travail sélectionné
+ Afficher et utiliser la page de gestion des utilisateurs sous paramètres dans le portail OMS

>[AZURE.NOTE] Les solutions de sauvegarde et de restauration de Site pour Analytique de journal ne sont pas en mesure de vous connecter à un archivage sécurisé aux Services de récupération et ne peut pas être configurées pour un abonnement fournisseur.

## <a name="managing-multiple-customers-using-log-analytics"></a>Gestion de plusieurs clients à l’aide de journal Analytique 

Il est recommandé de créer un espace de travail Analytique journal pour chaque client que vous gérez. Un espace de travail journal Analytique fournit :

+ Un emplacement géographique de stockage des données. 
+ Précision de facturation 
+ Isolement des données 
+ Configuration unique

En créant un espace de travail par client, vous êtes en mesure de séparer les données de chaque client et également effectuer le suivi de l’utilisation de chaque client.

Plus d’informations sur quand et pourquoi créer plusieurs espaces de travail est décrite dans [gérer l’accès afin de vous connecter analytique] (log-analytics-manage-access.md#determine-the-number-of-workspaces-you-need).

Création et la configuration des espaces de travail client peuvent être automatisées à l’aide de [PowerShell](log-analytics-powershell-workspace-configuration.md), [modèles de gestionnaire de ressources](log-analytics-template-workspace-configuration.md), ou à l’aide de l' [API REST](https://www.nuget.org/packages/Microsoft.Azure.Management.OperationalInsights/).

L’utilisation de modèles de gestionnaire de ressources pour la configuration de l’espace de travail vous permet d’avoir une configuration maître pouvant être utilisées pour créer et configurer des espaces de travail. Vous pouvez être certain que comme espaces de travail sont créés pour les clients qu’ils sont configurés automatiquement à vos besoins. Lorsque vous mettez à jour vos besoins, le modèle est mis à jour et les espaces de travail existants, appliqué à nouveau. Ce processus garantit que les espaces de travail même existants répondent à vos nouvelles normes.    

Lorsque vous gérez plusieurs espaces de travail journal Analytique, nous vous recommandons d’intégration chaque espace de travail de votre système de tickets existant / console d’opérations à l’aide de la fonctionnalité [d’alertes](log-analytics-alerts.md) . Grâce à l’intégration avec vos systèmes existants, personnel de support pouvez continuer à suivre leurs processus familiers. Journal Analytique vérifie chaque espace de travail contre les critères d’alerte que vous spécifiez régulièrement et génère une alerte lorsqu’une action est nécessaire.

Pour les rapports de niveau direction qui synthétisent les données au sein des espaces de travail, vous pouvez utiliser l’intégration entre journal Analytique et [PowerBI](log-analytics-powerbi.md). Si vous avez besoin intégrer avec un autre système de création de rapports, vous pouvez utiliser l’API de recherche (via PowerShell ou [reste](log-analytics-log-search-api.md)) pour exécuter des requêtes et exporter des résultats de recherche.

## <a name="next-steps"></a>Étapes suivantes

+ Automatiser la création et la configuration des espaces de travail à l’aide de [modèles de gestionnaire de ressources](log-analytics-template-workspace-configuration.md)
+ Automatiser la création d’espaces de travail à l’aide de [PowerShell](log-analytics-powershell-workspace-configuration.md) 
+ Utiliser des [alertes](log-analytics-alerts.md) pour intégrer des systèmes existants
+ Générer des rapports de synthèse à l’aide de [PowerBI](log-analytics-powerbi.md)
