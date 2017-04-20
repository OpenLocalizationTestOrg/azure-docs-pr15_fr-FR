<properties
    pageTitle="Vue d’ensemble du portail Microsoft Azure"
    description="Découvrez comment utiliser le portail Microsoft Azure."
    services=""
    documentationCenter=""
    authors="davidwrede"
    manager="dwrede"
    editor="jimbe"/>

<tags
    ms.service="na"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="12/16/2015"
    ms.author="dwrede"/>

# <a name="microsoft-azure-portal-overview"></a>Vue d’ensemble du portail Microsoft Azure

Le portail Microsoft Azure est un emplacement central où vous pouvez configurer et gérer vos ressources Azure.  Ce didacticiel sera vous familiariser avec le portail et vous montrent comment utiliser certaines de ces fonctionnalités clées :
- **Marketplace complète** qui vous permet de parcourir des milliers d’éléments à partir de Microsoft et d’autres fournisseurs qui peuvent être achetés et/ou sa mise en service.
- **Expérience unifiée et scalable Parcourir** rend les ressources qui vous intéressent et effectuez diverses opérations de gestion de retrouver plus facilement.
- **Pages de gestion cohérente** (ou cartes) qui vous permettent de gérer variété de Azure de services de manière cohérente d’exposition des paramètres, actions, facturation d’informations, santé surveillance et l’utilisation des données et beaucoup plus.
- Une **expérience personnelle** qui vous permet de créer un écran d’accueil personnalisé qui affiche les informations que vous souhaitez faire figurer chaque fois que vous vous connectez.  Vous pouvez également personnaliser tous les cartes de gestion qui contiennent des vignettes.

 ![Orientation de l’interface utilisateur du portail Azure][UIOrientation]

## <a name="before-you-get-started"></a>Avant de commencer

Vous avez besoin d’un abonnement Azure valide traitée ce didacticiel.  Si vous n’en avez pas, puis [vous inscrire à une version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/) aujourd'hui.  Une fois que vous avez un abonnement, vous pouvez accéder au portail à [https://portal.azure.com].

## <a name="how-to-create-a-resource"></a>La création d’une ressource

Azure a un marketplace avec des milliers d’éléments que vous pouvez créer à partir d’un seul endroit.  Supposons que vous voulez créer une nouvelle machine virtuelle Windows Server 2012.  Le + nouveau concentrateur est votre point d’entrée dans un ensemble curated des catégories proposées à partir de la place de marché.  Chaque catégorie possède un petit ensemble d’éléments proposées ainsi qu’un lien à la place de marché complète qui affiche toutes les catégories et recherche. Pour créer cette nouvelle machine virtuelle Windows Server 2012, effectuer les opérations suivantes :  

1.  Windows Server 2012 est proposée, afin que vous pouvez le sélectionner dans la catégorie cluster.  
2.  Remplir certaines entrées de base d’un formulaire.
3.  Cliquez sur « Créer » et votre ordinateur virtuel commence immédiatement la mise en service.

Le hub de notifications vous avertit de la ressource a été créée et une carte de gestion s’ouvre (vous pouvez toujours parcourir aux ressources ultérieurement).

![Catégories du portail][PortalCategories]


## <a name="how-to-find-your-resources"></a>Comment trouver vos ressources

Vous pouvez toujours épingler ressources fréquemment consultés à votre startboard, mais vous devrez peut-être accédez à quelque chose que vous n’accédez fréquemment.  Le hub de parcourir ci-dessous est un moyen pour accéder à toutes vos ressources.  Vous pouvez filtrer par abonnement, sélectionnez/redimensionnement des colonnes et accédez aux cartes de gestion en cliquant sur les éléments individuels.

![Parcourir concentrateur][BrowseHub]

## <a name="how-to-manage-and-delegate-access-to-a-resource"></a>Comment gérer et déléguer l’accès à une ressource

À partir de cette carte que vous pouvez vous connecter à la machine virtuelle à l’aide de bureau à distance, surveiller les indicateurs de performance clés, contrôler l’accès à cette machine virtuelle à l’aide d’accès basé sur un rôle (RBAC), configurez la machine virtuelle et effectuer d’autres tâches de gestion important.  Il est essentiel de gestion à l’échelle de délégation d’accès en fonction du rôle.  Cliquez [ici](./active-directory/role-based-access-control-configure.md) pour en savoir plus. Pour l’accès délégué à une ressource, effectuer les opérations suivantes :

1.  Accédez à la ressource.
2.  Cliquez sur tous les paramètres dans la section Essentials.
3.  Dans la liste Paramètres, cliquez sur « Utilisateurs ».
4.  Cliquez sur « Ajouter » dans la barre de commandes.
5.  Sélectionnez un utilisateur et un rôle.

![Gestion d’une ressource][ManageResource]

## <a name="how-to-customize-a-resource-blade"></a>Comment faire pour personnaliser une carte de ressources

Azure préconfigure les cartes pour vos ressources, mais les mosaïques sur ces cartes sont le vôtre au contrôle.  Vous pouvez facilement accéder dans Personnaliser mode pour ajouter, supprimer, redimensionner ou réorganiser les mosaïques. Pour personnaliser une carte, effectuer les opérations suivantes :

1.  Accédez à la ressource.
2.  Cliquez sur la «... » en haut de la carte que vous voulez personnaliser.
3.  Cliquez sur « Ajouter parties ».
4.  Démarrez glisser-déplacer des composants WebPart.  

![Personnalisation de cartes][CustomizeBlades]

## <a name="how-to-get-help"></a>Comment obtenir de l’aide

Si vous rencontrez un problème, nous sommes là pour vous.  Le portail comporte une page aide et support qui peut vous pointent dans la bonne direction.  Selon votre [plan de prise en charge](https://azure.microsoft.com/support/plans/), vous pouvez également créer des tickets de support directement dans le portail.  Après avoir créé un tickets de support, vous pouvez gérer le cycle de vie du billet à partir du portail. Vous pouvez accéder à l’aide et support page en accédant à parcourir -> aide + prise en charge.  

![Aide et support][HelpSupport]

## <a name="summary"></a>Résumé

Passons en revue ce que vous avez appris dans ce didacticiel :
- Vous avez appris à s’inscrire et obtenir un abonnement accédez au portail
- Vous avez orienté le portail de l’interface utilisateur et appris à créer et parcourir les ressources
- Vous avez appris à créer une ressource et parcourir les ressources
- Vous avez appris sur les structure ou gestion des cartes et comment vous pouvez gérer manière cohérente à travers différents types de ressources
- Vous avez appris comment personnaliser le portail pour afficher les informations vous intéresse au centre et de premier plan
- Vous avez appris comment contrôler l’accès aux ressources à l’aide d’accès basé sur un rôle (RBAC)
- Vous avez appris à l’aide et support

Le portail Microsoft Azure simplifie considérablement la création et la gestion de vos applications dans le cloud.  Consultez le [blog sur la gestion](https://azure.microsoft.com/blog/topics/management/) pour maintenir à jour que nous sommes constamment [l’écoute](https://feedback.azure.com/forums/223579-azure-preview-portal/) et l’améliorer.  [Blog de ScottGu](http://weblogs.asp.net/scottgu) est un autre endroit idéal pour trouver toutes les mises à jour Azure.

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png
