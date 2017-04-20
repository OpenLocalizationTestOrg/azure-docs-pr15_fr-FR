<properties
   pageTitle="Solutions de Suite de gestion des opérations (OMS) | Microsoft Azure"
   description="Solutions étendent les fonctionnalités de Suite de gestion des opérations (OMS) en fournissant des scénarios de package de gestion des clients peuvent ajouter à son espace de travail OMS.  Cet article fournit des détails sur les solutions personnalisées créées par les clients et partenaires."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="bwren" />

# <a name="management-solutions-in-operations-management-suite-oms-preview"></a>Solutions de gestion des opérations de gestion des Suite (OMS) (Preview)

>[AZURE.NOTE]Il s’agit d’une documentation préliminaire pour les solutions de gestion OMS qui sont actuellement en mode Aperçu avant.    

Solutions de gestion étendent les fonctionnalités de Suite de gestion des opérations (OMS) en fournissant des scénarios de package de gestion des clients peuvent ajouter à leur environnement.  Outre les [solutions proposées par Microsoft](../log-analytics/log-analytics-add-solutions.md), partenaires et clients peuvent créer des solutions de gestion à utiliser dans leur propre environnement ou accessibles aux clients grâce à la Communauté.

## <a name="finding-and-installing-management-solutions"></a>Recherche et l’installation de gestion des solutions
Il existe plusieurs méthodes pour localiser et l’installation de gestion des solutions comme décrit dans les sections suivantes.

### <a name="azure-marketplace"></a>Azure Marketplace
Solutions de gestion fournis par Microsoft et partenaires de confiance peuvent être installées depuis le Azure Marketplace dans le portail Azure.

1. Connectez-vous au portail Azure.
2. Dans le volet gauche, sélectionnez **plusieurs services**.
3. Faites défiler jusqu'à **Solutions** ou tapez *solutions* dans la boîte de dialogue **filtre** .
4. Cliquez sur le bouton **+ Ajouter** .
5. Rechercher des solutions qui vous intéresse dans l’aide de navigation, en cliquant sur le bouton **filtre** ou en tapant dans la zone de **Recherche Everthing** .
6. Cliquez sur un élément marketplace pour afficher des informations détaillées.
4. Cliquez sur **créer** pour ouvrir le volet **Ajouter la Solution** .
5. Vous devrez informations requises, telles que l' [espace de travail OMS et compte Automation](#oms-workspace-and-automation-account) en plus des valeurs pour tous les paramètres de la solution.
6. Cliquez sur **créer** pour installer la solution.

### <a name="oms-portal"></a>Portail OMS
Solutions de gestion fournies par Microsoft peuvent être installées à partir de la galerie de Solutions dans le portail OMS.

1. Connectez-vous au portail OMS.
2. Cliquez sur la vignette de la **Galerie de Solutions** .
2. Dans la page Galerie de Solutions OMS, découvrez chaque solution disponible. Cliquez sur le nom de la solution que vous voulez ajouter à OMS.
3. Dans la page de la solution que vous avez choisi, des informations détaillées sur la solution sont affiche. Cliquez sur **Ajouter**.
4. Une vignette de la solution que vous avez ajouté apparaît dans la vue d’ensemble page OMS et que vous pouvez commencer à l’utiliser une fois que le service OMS traite vos données.

### <a name="azure-quickstart-templates"></a>Modèles de démarrage rapide Azure
Membres de la Communauté peuvent envoyer des solutions de gestion des modèles de démarrage rapide Azure.  Vous pouvez télécharger ces modèles pour une installation ultérieure ou les inspecter pour découvrir comment [créer vos propres solutions](#creating-a-solution).

1. Suivez la procédure décrite dans l' [espace de travail OMS et Automation compte](#oms-workspace-and-automation-account) pour lier un compte et un espace de travail.
2. Accédez à des [modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/).  
3. Recherche d’une solution qui vous intéresse.
4. Sélectionnez la solution à partir des résultats pour afficher les détails.
5. Cliquez sur le bouton **déployer vers Azure** .
6. Vous devrez fournir des informations telles que le groupe de ressources et l’emplacement en plus des valeurs pour tous les paramètres de la solution.
7. Cliquez sur **acheter** pour installer la solution.

### <a name="deploy-azure-resource-manager-template"></a>Déployez le modèle de gestionnaire de ressources Azure
Solutions que vous demandez à la Communauté ou que vous [créez vous-même](#creating-a-solution) sont implémentées en tant que gestionnaire de ressources du modèle, et vous pouvez utiliser une des méthodes standards pour le [déploiement d’un modèle](../resource-group-template-deploy-portal.md).  Notez qu’avant d’installer la solution, vous devez créer et lier l' [espace de travail OMS et Automation compte](#oms-workspace-and-automation-account).

## <a name="oms-workspace-and-automation-account"></a>Espace de travail OMS et compte Automation
La plupart des solutions de gestion nécessitent un [espace de travail OMS](../log-analytics/log-analytics-manage-access.md) pour contenir des vues et un [compte Automation](../automation/automation-security-overview.md#automation-account-overview) contenir des procédures opérationnelles et les ressources associées. L’espace de travail et le compte doivent remplir les conditions suivantes.

- Une solution ne pouvez utiliser un espace de travail OMS et un seul compte d’automatisation.  
- L’espace de travail OMS et compte Automation utilisé par une solution doivent être liés entre eux. Un espace de travail OMS peut uniquement être lié à un seul compte d’automatisation, et un compte Automation peut uniquement être lié à un espace de travail OMS.
- Pour être lié, l’espace de travail OMS et compte Automation doivent être placé dans le même groupe de ressources et région.  L’exception est un espace de travail OMS dans la région des États-Unis Extrême-Orient et et compte Automation Extrême-Orient aux États-Unis 2.

### <a name="creating-a-link-between-an-oms-workspace-and-automation-account"></a>Création d’un lien entre un espace de travail OMS et le compte d’Automation
Comment vous spécifiez l’espace de travail OMS et Automation compte dépend de la méthode d’installation pour votre solution.

- Lorsque vous installez une solution Microsoft via le portail OMS, il est installé dans l’espace de travail OMS actuel et aucun compte Automation n’est obligatoire.

- Lorsque vous installez une solution via le Azure Marketplace, vous êtes invité à un espace de travail OMS et un compte de Automation, et le lien entre eux est créé pour vous.  

- Pour les solutions en dehors de la Azure Marketplace, vous devez lier l’espace de travail OMS et compte automatisation avant d’installer la solution.  Vous pouvez le faire en sélectionnant n’importe quelle solution sur le marché Azure l’espace de travail OMS et compte Automation.  Vous n’êtes pas obligé d’installer la solution, car le lien sera créé dès que l’espace de travail OMS et compte Automation sont sélectionnées.  Une fois que le lien est créé, vous pouvez utiliser cet espace de travail OMS et compte Automation pour n’importe quelle solution. 

### <a name="verifying-the-link-between-an-oms-workspace-and-automation-account"></a>Vérifiez le lien entre un espace de travail OMS et compte Automation
Vous pouvez vérifier le lien entre un espace de travail OMS et un compte Automation en procédant comme suit.

1. Sélectionnez le compte d’automatisation dans le portail Azure.
2. Faites défiler vers le bas du volet **paramètres** .
3. S’il existe une section intitulée **OMS ressources** dans le volet **paramètres** , ce compte est joint à un espace de travail OMS.
4. Sélectionnez l' **espace de travail** pour afficher les détails de l’espace de travail OMS lié à ce compte Automation.


## <a name="listing-management-solutions"></a>Solutions de gestion de la liste
Utilisez la procédure suivante pour afficher les solutions de gestion dans les espaces de travail liés à votre abonnement Azure.

1. Connectez-vous au portail Azure.
2. Dans le volet gauche, sélectionnez **plusieurs services**.
3. Faites défiler jusqu'à **Solutions** ou tapez *solutions* dans la boîte de dialogue **filtre** .
4. Solutions installées dans vos espaces de travail seront affichent.

Notez que vous pouvez afficher uniquement les solutions Microsoft installées dans l’espace de travail en cours à l’aide du portail OMS.

## <a name="removing-a-management-solution"></a>Suppression d’une solution de gestion
Lorsqu’une solution de gestion est supprimée, toutes les ressources de la solution sont également supprimés.  

1. Recherchez la solution dans le portail Azure à l’aide de la procédure à la [liste des solutions](#listing-solutions).
2. Sélectionnez la solution que vous voulez supprimer.
3. Cliquez sur le bouton **Supprimer** .

## <a name="creating-a-management-solution"></a>Création d’une solution de gestion
Guide pas à pas sur la création de solutions de gestion sont disponibles sur la [Création de solutions dans Suite de gestion des opérations (OMS)](operations-management-suite-solutions-creating.md). 


## <a name="next-steps"></a>Étapes suivantes

- Rechercher des [Modèles de démarrage rapide d’Azure](https://azure.microsoft.com/documentation/templates) des exemples de différents modèles de gestionnaire de ressources.
- Créer vos propres [solutions de gestion](operations-management-suite-solutions-creating.md).
 