<properties
    pageTitle="Résolution des problèmes liés au portail DocumentDB | Microsoft Azure"
    description="Découvrez pour résoudre les problèmes dans le portail DocumentDB Azure." 
    services="documentdb"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="mimig"/>

# <a name="azure-documentdb-portal-troubleshooting-tips"></a>Azure portal DocumentDB des conseils de dépannage

Cet article décrit comment résoudre les problèmes de DocumentDB dans le portail Azure. 

## <a name="resources-are-missing"></a>Ressources sont manquantes

**Problème**: bases de données ou des collections de sites sont manquantes à partir de votre portails cartes.

**Solution**: réduisez l’utilisation des applications pour fonctionner sous le quota de débit maximal pour la collection de sites. 

**Explication**: le portail est une application telle que tout autre, passer des appels à votre base de données DocumentDB et collection de sites. Si vos demandes sont actuellement limitées en raison d’appels sachant depuis une autre application, le portail peut également être limité, à l’origine de ressources ne pas faire apparaître dans le portail. Pour résoudre le problème, la raison de l’utilisation du haut débit d’adresses, puis actualisez la carte du portail. Vous trouverez des informations sur l’utilisation de débit inférieure et comment mesurer dans la section de [débit](documentdb-performance-tips.md#throughput) de l’article [optimiser les performances](documentdb-performance-tips.md) .
 
## <a name="pages-or-blades-wont-load"></a>Pages ou des cartes ne se chargent pas

**Problème**: ne pas affichent les Pages et les cartes dans le portail.

**Solution**: réduisez l’utilisation des applications pour fonctionner sous le quota de débit maximal pour la collection de sites. 

**Explication**: le portail est une application telle que tout autre, passer des appels à votre base de données DocumentDB et collection de sites. Si vos demandes sont actuellement limitées en raison d’appels sachant depuis une autre application, le portail peut également être limité, à l’origine de ressources ne pas faire apparaître dans le portail. Pour résoudre le problème, la raison de l’utilisation du haut débit d’adresses, puis actualisez la carte du portail. Vous trouverez des informations sur l’utilisation de débit inférieure et comment mesurer dans la section de [débit](documentdb-performance-tips.md#throughput) de l’article [optimiser les performances](documentdb-performance-tips.md) .

## <a name="add-collection-button-is-disabled"></a>Ajouter le bouton de la collection de sites est désactivé

**Problème**: sur la carte de base de données, le bouton **Ajouter la collection de sites** est désactivé.

**Explication**: Si votre abonnement Azure est associé avantage crédits, telles que crédits gratuits proposés à partir d’un abonnement MSDN, et vous avez utilisé toutes vos crédits pour le mois, vous ne pouvez pas créer toutes les collections supplémentaires dans DocumentDB.

**Solution**: supprimez le plafond des dépenses de votre compte.

1. Dans le portail Azure, dans le Jumpbar, cliquez sur **abonnements**, cliquez sur l’abonnement associé à la base de données DocumentDB et puis, dans la carte de **l’abonnement** , cliquez sur **Gérer**. 
    ![DocumentDB propose plusieurs, bien définies choix à partir de modèles de cohérence (souple)](./media/documentdb-portal-troubleshooting/documentdb-change-billing.png)

2. Dans la nouvelle fenêtre de navigateur, vous verrez ne qu’aucun crédit restant. Cliquez sur le bouton **Supprimer plafond des dépenses** pour supprimer les dépenses pour la période de facturation actuelle ou indéfiniment. Vous exécutez l’Assistant pour ajouter ou vérifier vos informations de carte de crédit. 
    ![DocumentDB propose plusieurs, bien définies choix à partir de modèles de cohérence (souple)](./media/documentdb-portal-troubleshooting/documentdb-remove-spending-limit.png)

 
## <a name="query-explorer-completes-with-errors"></a>Explorateur de requête est terminée avec des erreurs

Voir [résoudre les problèmes de l’Explorateur de requête](documentdb-query-collections-query-explorer.md#troubleshoot).

## <a name="no-data-available-in-monitoring-tiles"></a>Données non disponibles dans les mosaïques de surveillance

Voir [résoudre les problèmes de surveillance vignettes](documentdb-monitor-accounts.md#troubleshooting).

## <a name="no-documents-returned-in-document-explorer"></a>Aucune documents renvoyés dans l’Explorateur de Document

Voir [résolution des problèmes de l’Explorateur de Document](documentdb-view-json-document-explorer.md#troubleshoot).

## <a name="next-steps"></a>Étapes suivantes

Si vous rencontrez toujours des problèmes dans le portail, veuillez envoyer un e-mail [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) pour obtenir de l’aide ou un fichier, une demande d’assistance dans le portail en cliquant sur **Parcourir**, **aide + prise en charge**, puis sur **demande de support technique de créer**.
