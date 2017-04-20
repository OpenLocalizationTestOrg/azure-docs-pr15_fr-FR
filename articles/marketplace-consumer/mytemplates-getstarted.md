<properties
   pageTitle="Prise en main avec les modèles privés | Microsoft Azure"
   description="Ajouter, gérer et partager vos modèles privées en utilisant le portail Azure, l’infrastructure du langage commun Azure ou PowerShell."
   services="marketplace-customer"
   documentationCenter=""
   authors="VybavaRamadoss"
   manager="asimm"
   editor=""
   tags="marketplace, azure-resource-manager"
   keywords=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/18/2016"
   ms.author="vybavar"/>

# <a name="get-started-with-private-templates-on-the-azure-portal"></a>Prise en main des modèles privés sur le portail Azure

Un modèle de [Gestionnaire de ressources Azure](../resource-group-authoring-templates.md) est un modèle déclaratif permet de définir votre déploiement. Vous pouvez définir les ressources pour le déploiement pour une solution et spécifier des paramètres et variables qui vous permettent de valeurs d’entrée pour différents environnements. Le modèle est constitué de JSON et expressions que vous pouvez utiliser pour créer des valeurs pour votre déploiement.

Vous pouvez utiliser la nouvelle fonctionnalité de **modèles** dans le [Portail Azure](https://portal.azure.com) ainsi que le fournisseur de ressources **Microsoft.Gallery** comme une extension de la [Azure Marketplace](https://azure.microsoft.com/marketplace/) pour permettre aux utilisateurs de créer, gérer et déployer des modèles de privé à partir d’une bibliothèque personnelle.

Ce document vous guide via l’ajout, la gestion et le partage privé **modèle** à l’aide du portail Azure.

## <a name="guidance"></a>Recommandations

Les suggestions suivantes vous aideront à tirer pleinement parti des **modèles** lorsque vous travaillez avec vos solutions :

- Un **modèle** est une ressource encapsulation qui contient un modèle de gestionnaire de ressources et les métadonnées supplémentaires. Il se comporte très comme un élément sur le marché. La différence clée est qu’il est un élément privé plutôt que d’utiliser les éléments Marketplace publics.
- La bibliothèque de **modèles** fonctionne bien pour les utilisateurs qui ont besoin pour personnaliser leurs déploiements.
- **Les modèles** fonctionnent correctement pour les utilisateurs qui ont besoin d’un simple référentiel dans Azure.
- Commencer avec un modèle de gestionnaire de ressources existant. Recherchez des modèles dans [github](https://github.com/Azure/azure-quickstart-templates) ou [Exporter le modèle](../resource-manager-export-template.md) à partir d’un groupe de ressources existant.
- **Modèles** sont liés à l’utilisateur qui les publie. Nom de l’éditeur est visible à tous les participants qui ont accès en lecture à celui-ci.
- **Les modèles** sont des ressources du Gestionnaire de ressources et ne peut pas être renommé une fois publiées.

## <a name="add-a-template-resource"></a>Ajouter une ressource de modèle

Il existe deux façons de créer une ressource de **modèle** dans le portail Azure.

### <a name="method-1--create-a-new-template-resource-from-a-running-resource-group"></a>Méthode 1 : Créer une nouvelle ressource de modèle à partir d’un groupe de ressources en cours d’exécution

1. Accédez à un groupe de ressources existant dans le portail Azure. Dans **paramètres**, sélectionnez **Exporter le modèle** .
2. Une fois que le modèle de gestionnaire de ressources est exporté, utilisez le bouton **Enregistrer le modèle** pour l’enregistrer dans le référentiel de **modèles** . Trouver des informations complètes modèle d’exportation [ici](../resource-manager-export-template.md).
<br /><br />
![Exportation de groupe de ressources](media/rg-export-portal1.PNG)  <br />

3. Sélectionnez le bouton de commande **Enregistrer dans le modèle** .
<br /><br />

4. Entrez les informations suivantes :

    - Nom : nom de l’objet de modèle (Remarque : il s’agit d’un nom de gestionnaire de ressources Azure en fonction. Appliquent toutes les restrictions d’appellation et il ne peut pas être modifié une fois créés).
    - Description – récapitulatif rapide sur le modèle.

    ![Enregistrer le modèle](media/save-template-portal1.PNG)  <br />

5. Cliquez sur **Enregistrer**.

    > [AZURE.NOTE] La carte de modèle exportation indique les notifications lorsque le modèle de gestionnaire de ressources exporté comporte des erreurs, mais vous serez toujours en mesure d’enregistrer ce modèle de gestionnaire de ressources pour les modèles. Assurez-vous que vous vérifiez et corrigez les problèmes de modèle de n’importe quel gestionnaire de ressources avant redéployez le modèle de gestionnaire de ressources exporté.

### <a name="b-method-2--add-a-new-template-resource-from-browse"></a>B. Méthode 2 : Ajouter une nouvelle ressource de modèle à partir de parcourir

Vous pouvez également ajouter un nouveau **modèle** en utilisant la + ajouter le bouton de commande dans **Parcourir > modèles**. Vous devrez fournir un nom, Description et le modèle de gestionnaire de ressources JSON.

![Ajouter un modèle](media/add-template-portal1.PNG)  <br />

> [AZURE.NOTE] Microsoft.Gallery repose sur un client fournisseur de ressources Azure. La ressource de modèle est liée à l’utilisateur qui l’a créé. Elle n’est pas liée à un abonnement spécifique. Un abonnement doit être choisie uniquement lorsque le déploiement d’un modèle.

## <a name="view-template-resources"></a>Afficher les ressources de modèle

Tous les **modèles** disponibles peuvent être vu en **Parcourir > modèles**. Cela inclut les **modèles** que vous avez créés ainsi que ceux qui ont été partagés avec vous dans des niveaux d’autorisations. Obtenir plus d’informations dans la section [contrôle d’accès](#access-control-for-a-tenant-resource-provider) ci-dessous.

![Modèle d’affichage](media/view-template-portal1.PNG)  <br />

Vous pouvez afficher les détails d’un **modèle** en cliquant sur un élément dans la liste.

![Modèle d’affichage](media/view-template-portal2c.png)  <br />

## <a name="edit-a-template-resource"></a>Modifier une ressource de modèle

Vous pouvez démarrer le flux de modifier un **modèle** en cliquant avec le bouton droit sur l’élément dans la liste de navigation ou en cliquant sur le bouton de commande modifier.

![Modifier le modèle](media/edit-template-portal1a.PNG)  <br />

Vous pouvez modifier la description ou le texte de modèle Gestionnaire de ressources. Vous ne pouvez pas modifier le nom car il s’agit d’un nom de la ressource Gestionnaire de ressources. Lorsque vous modifiez le modèle de gestionnaire de ressources JSON valide pour vous assurer qu’il s’agit JSON valide. Cliquez sur **OK** , puis sur **Enregistrer** pour enregistrer votre modèle mis à jour.

![Modifier le modèle](media/edit-template-portal2a.PNG)  <br />

Une fois que le **modèle** est enregistré, vous verrez un message de confirmation.

![Modifier le modèle](media/edit-template-portal3b.png)  <br />

## <a name="deploy-a-template-resource"></a>Déployer une ressource de modèle

Vous pouvez déployer n’importe quel **modèle** dont vous disposez des autorisations de **lecture** sur. Le flux de déploiement lance la carte de déploiement Azure modèle standard. Remplissez les valeurs pour les paramètres de modèle Gestionnaire de ressources effectuer le déploiement.

![Modèle de déploiement](media/deploy-template-portal1b.png)  <br />

## <a name="share-a-template-resource"></a>Partager une ressource de modèle

Une ressource de **modèle** peut être partagée avec vos collègues. Partage se comporte comme [attribution de rôle pour toutes les ressources sur Azure](../active-directory/role-based-access-control-configure.md). Le propriétaire de **modèle** fournit des autorisations aux autres utilisateurs qui peuvent interagir avec une ressource de modèle. La personne ou du groupe de personnes que vous partagez le **modèle** avec seront en mesure de voir le modèle de gestionnaire de ressources et ses propriétés de la galerie.

### <a name="access-control-for-the-microsoftgallery-resources"></a>Contrôle d’accès pour les ressources Microsoft.Gallery

Rôle | Autorisations
---|----
Propriétaire | Permet de contrôle total sur la ressource de modèle, y compris les partager
Reader | Permet de lire et Execute(Deploy) sur la ressource de modèle
Collaboration | Permet de modifier et supprimer des autorisations sur la ressource de modèle. Utilisateur ne peuvent pas partager le modèle avec d’autres personnes

Sélectionnez **partage** dans l’élément Parcourir en cliquant avec le bouton droit sur ou sur la carte d’affichage d’un élément spécifique. Cette action lance une expérience de partage.

![Modèle de partage](media/share-template-portal1a.png)  <br />

 Vous pouvez maintenant choisir un rôle et un utilisateur ou un groupe pour fournir un accès à un **modèle**spécifique. Les rôles disponibles sont propriétaire, lecteur et collaboration. Obtenir plus d’informations dans la section [contrôle d’accès](#access-control-for-a-tenant-resource-provider) ci-dessus.

![Modèle de partage](media/share-template-portal2b.png)  <br />

![Modèle de partage](media/share-template-portal3b.png)  <br />

Cliquez sur **Ok**et **Sélectionnez** . Vous pouvez désormais voir les utilisateurs ou groupes que vous avez ajouté à la ressource.

![Modèle de partage](media/share-template-portal4b.png)  <br />

> [AZURE.NOTE] Un modèle peut uniquement être partagé avec les utilisateurs et groupes dans un seul client Azure Active Directory. Si vous partagez un modèle avec une adresse de messagerie qui n’est pas dans votre client, une invitation est envoyée demandant à l’utilisateur à rejoindre le client en tant qu’invité.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la création de modèles de gestionnaire de ressources, voir [modèles de co-création](../resource-group-authoring-templates.md)
- Pour mieux comprendre les fonctions que vous pouvez utiliser dans un modèle de gestionnaire de ressources, voir [fonctions de modèle](../resource-group-template-functions.md)
- Pour des instructions sur la conception des modèles, voir [meilleures pratiques pour la conception de modèles de gestionnaire de ressources Azure](../best-practices-resource-manager-design-templates.md)
