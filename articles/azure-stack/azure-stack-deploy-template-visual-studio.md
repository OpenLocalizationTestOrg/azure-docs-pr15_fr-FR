<properties
    pageTitle="Déployer des modèles avec Visual Studio dans une pile Azure | Microsoft Azure"
    description="Découvrez comment déployer des modèles avec Visual Studio dans Azure pile."
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Déployer des modèles dans la pile Azure à l’aide de Visual Studio

Utilisez Visual Studio pour déployer des modèles Azure le Gestionnaire de ressources sur le contrôle du concept pile Azure.

Gestionnaire de ressources modèles déployer et mise en service de toutes les ressources de votre application dans une seule opération coordonnée.

1.  Ouvrir Visual Studio 2015 Update 1.

2.  Cliquez sur **fichier**, cliquez sur **Nouveau**et cliquez sur **Groupe de ressources Azure**dans la boîte de dialogue **Nouveau projet** .

3.  Entrez un **nom** pour le nouveau projet, puis cliquez sur **OK**.

4.  Dans la boîte de dialogue **Sélectionner un modèle de Azure** , cliquez sur **Machine virtuelle Windows**, puis cliquez sur **OK**.

  Dans votre projet, vous pouvez afficher la liste des modèles disponibles, développez le nœud de **modèles** dans le volet **Explorateur de solutions** .

5.  Dans le volet **Explorateur de solutions** , double-cliquez sur le nom de votre projet, cliquez sur **déployer**, puis cliquez sur **Nouveau déploiement**.

6.  Dans la boîte de dialogue **déployer au groupe de ressources** , dans le menu déroulant **abonnement** , sélectionnez votre abonnement Microsoft Azure pile.

7.  Dans la liste de **Groupe de ressources** , sélectionnez un groupe de ressources existant ou créez-en un.

8.  Dans la liste **emplacement de groupe de ressources** , choisissez un emplacement, puis cliquez sur **déployer**.

9.  Dans la boîte de dialogue **Modifier les paramètres** , entrez des valeurs pour les paramètres (qui peuvent varier selon le modèle), puis cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

[Déployer des modèles avec la ligne de commande](azure-stack-deploy-template-command-line.md)
