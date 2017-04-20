<properties 
    pageTitle="Comment gérer les comptes d’utilisateur dans la gestion des API Azure | Microsoft Azure" 
    description="Apprenez à créer ou inviter des utilisateurs dans la gestion des API Azure" 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Comment gérer les comptes d’utilisateur dans la gestion des API Azure

Gestion des API, les développeurs sont les utilisateurs de l’API qui vous exposez à l’aide de la gestion des API. Ce guide montre comment créer et invitez les développeurs à utiliser les API et les produits que vous apportez avec votre instance de gestion de l’API. Pour plus d’informations sur la gestion des comptes d’utilisateurs par programme, consultez la documentation [entité utilisateur](https://msdn.microsoft.com/library/azure/dn776330.aspx) dans la référence de [L’API gestion reste](https://msdn.microsoft.com/library/azure/dn776326.aspx) .

## <a name="create-developer"> </a>Créer un nouveau développeur

Pour créer un nouveau développeur, cliquez sur **Gérer** dans le portail classique Azure pour votre service de gestion de l’API. Cela vous permet d’accéder au portail de gestion de l’API de publisher. Si vous n’avez pas encore créé une instance de service de gestion des API, voir [créer une instance de service de gestion de l’API][] dans le didacticiel [mise en route de la gestion des API Azure][] .

![Portail de Publisher][api-management-management-console]

Cliquez sur **utilisateurs** dans le menu de **Gestion de l’API** sur la gauche, puis cliquez sur **Ajouter un utilisateur**.

![Créer pour les développeurs][api-management-create-developer]

Entrez le **courrier électronique**, le **mot de passe**et le **nom** de la nouvelle développeur et cliquez sur **Enregistrer**.

![Créer pour les développeurs][api-management-add-new-user]

Par défaut, comptes nouvellement créé pour les développeurs sont **actifs**et associés au groupe **développeurs** .

![Nouveau développeur][api-management-new-developer]

Comptes pour les développeurs qui se trouvent dans un état **actif** peuvent être utilisés pour accéder à l’ensemble des API pour lesquelles ils ont des abonnements. Pour associer le développeur nouvellement créé à des groupes supplémentaires, voir [comment associer des groupes avec les développeurs][].

## <a name="invite-developer"> </a>Inviter un développeur

Pour inviter un développeur, cliquez sur **utilisateurs** dans le menu de **Gestion de l’API** sur la gauche, puis cliquez sur **Inviter un utilisateur**.

![Inviter développeur][api-management-invite-developer]

Entrez l’adresse e-mail et le nom du développeur, puis cliquez sur **inviter**.

![Inviter développeur][api-management-invite-developer-window]

Un message de confirmation s’affiche, mais le développeur nouvellement invité n’apparaît pas dans la liste jusqu'à après avoir accepté l’invitation. 

![Inviter de confirmation][api-management-invite-developer-confirmation]

Lorsqu’un développeur est invité, un message électronique est envoyé au développeur. Ce message électronique est généré à l’aide d’un modèle et est personnalisable. Pour plus d’informations, voir [configurer les modèles de courrier électronique][].

Une fois que l’invitation est acceptée, le compte devient actif.

## <a name="block-developer"></a> Désactiver ou réactiver un compte de développeur

Par défaut, les comptes développeur nouvellement créé ou invités sont **actifs**. Pour désactiver un compte de développeur, cliquez sur **Bloquer**. Pour réactiver un compte bloqué développeur, cliquez sur **Activer**. Un compte bloqué développeur ne peut pas accéder au portail développeur ou appeler des API. Pour supprimer un compte d’utilisateur, cliquez sur **Supprimer**.

![Développeur de bloc][api-management-new-developer]

## <a name="reset-a-user-password"></a>Réinitialiser un mot de passe utilisateur

Pour réinitialiser le mot de passe pour un compte d’utilisateur, cliquez sur le nom du compte.

![Réinitialiser votre mot de passe][api-management-view-developer]

Cliquez sur **Réinitialiser votre mot de passe** pour envoyer un lien vers l’utilisateur de réinitialiser leur mot de passe.

![Réinitialiser votre mot de passe][api-management-reset-password]

Pour travailler par programme avec les comptes d’utilisateurs, voir la documentation [entité utilisateur](https://msdn.microsoft.com/library/azure/dn776330.aspx) dans la référence de [L’API gestion reste](https://msdn.microsoft.com/library/azure/dn776326.aspx) . Pour réinitialiser un mot de passe du compte utilisateur à une valeur spécifique, vous pouvez utiliser l’opération [mettre à jour un utilisateur](https://msdn.microsoft.com/library/azure/dn776330.aspx#UpdateUser) et spécifier le mot de passe.

## <a name="pending-verification"></a>Vérification en attente

![Vérification en attente][api-management-pending-verification]

## <a name="next-steps"> </a>Étapes suivantes

Une fois un compte de développeur est créé, vous pouvez associer à des rôles et s’abonner à des produits et des API. Pour plus d’informations, voir [comment créer et utiliser des groupes][].


[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png
[]: ./media/api-management-howto-create-or-invite-developers/.png



[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[Comment créer et utiliser des groupes]: api-management-howto-create-groups.md
[Comment faire pour associer des groupes avec les développeurs]: api-management-howto-create-groups.md#associate-group-developer

[Prise en main avec la gestion des API Azure]: api-management-get-started.md
[Créer une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance
[Configurer les modèles de courrier électronique]: api-management-howto-configure-notifications.md#email-templates