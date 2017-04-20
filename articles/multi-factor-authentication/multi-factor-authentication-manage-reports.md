<properties
    pageTitle="Rapports de l’authentification multifacteur Azure"
    description="Cela explique comment utiliser la fonctionnalité de l’authentification multifacteur Azure - rapports."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="reports-in-azure-multi-factor-authentication"></a>Rapports dans l’authentification multifacteur Azure

L’authentification multifacteur Azure fournit plusieurs rapports qui peuvent être utilisées par votre organisation et vous-même. Ces rapports sont accessibles via le portail de gestion de l’authentification multifacteur, qui nécessite que vous disposez d’une licence Azure l’authentification Multifacteur fournisseur, ou un authentification Multifacteur Azure, Azure AD Premium ou entreprise mobilité Suite. Voici une liste des rapports disponibles.

Vous pouvez accéder aux rapports via le portail de gestion Azure.

Nom| Description
:------------- | :------------- |
Utilisation | L’utilisation de rapports affichent des informations sur l’utilisation générale, utilisateur synthèse et détails de l’utilisateur.
État du serveur|Ce rapport affiche l’état des serveurs de l’authentification multifacteur associé à votre compte.
Historique des utilisateurs bloqués|Ces rapports affichent l’historique des demandes de bloquer ou débloquer les utilisateurs.
Historique de l’utilisateur ignorée|Affiche l’historique de requêtes pour ignorer l’authentification multifacteur pour numéro de téléphone d’un utilisateur.
Alerte de fraude|Affiche un historique des alertes de fraude soumis au cours de la plage de dates que vous avez spécifié.
En file d’attente|Listes de rapports en attente de traitement et leur statut. Un lien pour télécharger ou afficher le rapport est fourni lorsque le rapport est terminé.

## <a name="to-view-reports"></a>Pour afficher les rapports

1.  Ouvrez une session sur http://azure.microsoft.com
2.  Sur la gauche, sélectionnez Active Directory.
3.  Sélectionnez une des options suivantes :
    - **Option 1**: cliquez sur l’onglet fournisseurs d’authentification multifacteur. Sélectionnez votre fournisseur l’authentification Multifacteur et cliquez sur le bouton Gérer dans la partie inférieure.
    - **Option 2**: sélectionnez votre annuaire, cliquez sur l’onglet Configurer. Sous la section l’authentification multifacteur, sélectionnez Gérer les paramètres de service. Dans la partie inférieure de la page Paramètres du Service l’authentification Multifacteur, cliquez sur l’accéder au portail.
4.  Dans le portail de gestion Azure de l’authentification multifacteur, vous verrez l’affichage une section d’état dans le volet de navigation gauche. À partir de là, vous pouvez sélectionner les rapports ci-dessus.

<center>![Cloud](./media/multi-factor-authentication-manage-reports/report.png)</center>


**Ressources supplémentaires**

* [Pour les utilisateurs](./end-user/multi-factor-authentication-end-user.md)
* [Authentification multifacteur Azure sur MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx)
