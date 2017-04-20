<properties
   pageTitle="Sécurité Automation Azure | Microsoft Azure"
   description="Cet article fournit une vue d’ensemble de la sécurité automation et les différentes méthodes d’authentification disponibles pour les comptes Automation dans Azure Automation."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="sécurité Automation, automation sécurisée" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/29/2016"
   ms.author="magoedte" />

# <a name="azure-automation-security"></a>Sécurité Automation Azure
Azure Automation vous permet d’automatiser les tâches par rapport aux ressources dans Azure, en local et avec d’autres fournisseurs de nuage tel que Amazon Web Services (AWS).  Dans l’ordre d’une runbook d’exécuter ses actions requises, il doit disposer des autorisations un accès sécurisé aux ressources avec les droits minimum requis au sein de l’abonnement.  
Cet article aborde les différents scénarios d’authentification pris en charge par Automation Azure et vous montrent comment commencer en fonction de l’environnement ou nécessaires pour gérer les environnements.  

## <a name="automation-account-overview"></a>Vue d’ensemble du compte Automation
Lorsque vous démarrez Azure Automation pour la première fois, vous devez créer au moins un compte Automation. Automatisation comptes vous permettent d’isoler vos ressources Automation (procédures opérationnelles, biens, configurations) à partir des ressources contenues dans d’autres comptes Automation. Vous pouvez utiliser des comptes d’automatisation à séparer les ressources dans les environnements logiques distincts. Par exemple, vous pouvez utiliser un seul compte de développement, un autre pour production et un autre pour votre environnement local.  Un compte Azure Automation est différent de votre compte Microsoft ou comptes créés dans votre abonnement Azure.

Les ressources Automation pour chaque compte Automation sont associées à une seule région Azure, mais comptes Automation peuvent gérer les ressources dans n’importe quelle région. La principale raison pour laquelle créer des comptes d’automatisation dans différentes régions serait si vous avez stratégies nécessitant des données et des ressources lié à une région spécifique.

>[AZURE.NOTE]Automatisation comptes et les ressources qu’ils contiennent qui sont créés dans le portail Azure, ne sont pas accessibles dans le portail classique Azure. Si vous voulez gérer ces comptes ou leurs ressources avec Windows PowerShell, vous devez utiliser les modules Azure le Gestionnaire de ressources.

Toutes les tâches que vous effectuez par rapport aux ressources à l’aide d’Azure le Gestionnaire de ressources et les applets de commande Azure dans Azure Automation doivent s’authentifier à Azure à l’aide de l’authentification Azure Active Directory identité d’organisation en fonction des informations d’identification.  L’authentification basée sur le certificat a été la méthode d’authentification d’origine avec le mode de gestion des services Azure, mais il était compliqué à configurer.  Authentification à Azure avec utilisateur Azure AD a été introduite précédent dans 2014 non seulement simplifier la procédure à suivre pour configurer un compte d’authentification, mais également en charge la possibilité de manière non interactive authentifier vers Azure avec un seul compte d’utilisateur qui a collaboré avec le Gestionnaire de ressources Azure et ressources classiques.   

Actuellement lorsque vous créez un nouveau compte Automation dans le portail Azure, qu’il crée automatiquement :

-  Exécuter en tant que compte qui crée une nouvelle entité service dans Azure Active Directory, un certificat et affecte le contrôle d’accès basé sur un rôle Collaborateur (RBAC), qui sera utilisé pour gérer les ressources du Gestionnaire de ressources à l’aide de procédures opérationnelles.
-  Exécuter en tant que compte classique en téléchargeant un certificat de gestion, qui sera utilisé pour gérer les gestion des services Azure ou ressources classiques à l’aide des procédures opérationnelles.  

Contrôle d’accès basé sur un rôle est disponible avec le Gestionnaire de ressources Azure pour accorder actions autorisées pour un compte d’utilisateur Azure AD et exécuter en tant que compte, authentifier principal de ce service.  Lisez le [contrôle d’accès basé sur un rôle dans l’article Azure Automation](../automation/automation-role-based-access-control.md) pour plus d’informations pour contribuer au développement de votre modèle pour la gestion des autorisations d’automatisation.  

Procédures opérationnelles en cours d’exécution sur un collaborateur Runbook hybride dans votre centre de données ou contre computing services dans AWS Impossible d’utiliser la méthode qui est généralement utilisée pour l’authentification des procédures opérationnelles aux ressources Azure.  C’est parce que ces ressources sont en cours d’exécution en dehors d’Azure et par conséquent, nécessitent leurs propres informations d’identification de sécurité définies dans Automation s’authentifier aux ressources qui ils accèdent localement.  

## <a name="authentication-methods"></a>Méthodes d’authentification

Le tableau suivant résume les différentes méthodes d’authentification pour chaque environnement pris en charge par Automation Azure et à l’article décrivant comment configurer une authentification pour les procédures opérationnelles.

Méthode  |  Environnement  | Article
----------|----------|----------
Compte d’utilisateur AD Azure | Le Gestionnaire de ressources Azure et la gestion des services Azure | [Authentifier procédures opérationnelles compte Azure AD utilisateur](../automation/automation-sec-configure-aduser-account.md)
Azure exécuter en tant que compte | Gestionnaire de ressources Azure | [Authentifier procédures opérationnelles avec Azure exécuter en tant que compte](../automation/automation-sec-configure-azure-runas-account.md)
Azure exécuter classique en tant que compte | Gestion des services Azure | [Authentifier procédures opérationnelles avec Azure exécuter en tant que compte](../automation/automation-sec-configure-azure-runas-account.md)
Authentification Windows | Centre de données locale | [Authentifier procédures opérationnelles pour les travailleurs de Runbook hybride](../automation/automation-hybrid-runbook-worker.md)
Informations d’identification AWS | Services Web Amazon | [Authentifier procédures opérationnelles avec les Services Web Amazon (AWS)](../automation/automation-sec-configure-aws-account.md)



