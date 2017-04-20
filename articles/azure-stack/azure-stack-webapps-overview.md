<properties
    pageTitle="Vue d’ensemble des applications Web à pile Azure | Microsoft Azure"
    description="Vue d’ensemble des applications Web dans une pile Azure"
    services="azure-stack"
    documentationCenter=""
    authors="apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>
    
# <a name="azure-stack-web-apps-overview"></a>Vue d’ensemble des applications Web pile Azure
    
> [AZURE.NOTE] Les informations suivantes s’applique uniquement aux déploiements Azure pile TP1.

Azure pile Web Apps est le premier élément de l’offre de Service d’application Azure portée à pile Azure. Le programme d’installation Azure pile Web Apps crée une instance de chacune des cinq types de rôle requis et créera également un serveur de fichiers. Bien que vous pouvez ajouter plusieurs instances pour chacun des types de rôle, n’oubliez pas qu’il n’a pas d’espace pour les machines virtuelles dans Technical Preview 1. Les fonctionnalités en cours d’Azure pile Web Apps sont principalement les capacités foundation sont nécessaires pour gérer les applications web système et l’hôte.

![Pile Azure Application Service Web Apps dans la Azure empilent portail][1]

## <a name="limitations-of-the-technical-preview"></a>Limitations de la version d’évaluation technique

Il n’existe aucune prise en charge pour les versions preview Azure pile application Service. Ne placez les charges de travail dans cette version d’évaluation. Il n’existe également aucune mise à niveau entre versions preview Azure pile application Service. Les objectifs principaux de ces versions preview sont pour afficher ce que nous fournissons et d’obtenir des commentaires. 

## <a name="what-is-an-app-service-plan"></a>Qu’est un Plan de Service d’application ?

Le fournisseur de ressources Azure pile Web Apps utilise le même code qui utilise la fonction Azure Web Apps dans le Service d’application Azure. Par conséquent, quelques concepts courantes valent décrivant. Dans les applications Web, le conteneur tarification pour les applications web est appelé le plan de services d’application. Il représente l’ensemble des machines virtuelles dédiés permettant de conserver vos applications. Au sein d’un abonnement donné, vous pouvez avoir plusieurs offres de Service d’application. Cela est également vrai dans Azure pile Web Apps. 

Dans Azure, il existe des employés partagés et dédiés. Un collaborateur partagé prend en charge densité partagé application vous hébergez et qu’un seul ensemble de travailleurs partagés. Serveurs dédiés sont uniquement utilisées par un client et se présentent sous trois formats : petites, moyennes et grandes. Besoins de clients locaux ne peut pas toujours être décrites à l’aide de ces termes. Dans Azure pile Web Apps, les administrateurs de fournisseur ressource peuvent définir les niveaux de travail qu’ils souhaitent disponibilité, tels qu’ils aient plusieurs jeux de travailleurs partagés ou différents jeux de travailleurs dédiés en fonction de leur site web unique qui héberge les besoins. À l’aide de ces définitions de couche collaborateur, ils peuvent ensuite définir leur propre tarifs références SKU.

## <a name="portal-features"></a>Fonctionnalités du portail

S’applique également avec le serveur principal, Azure pile Web Apps utilise la même interface utilisateur qui utilise Azure Web Apps. Certaines fonctionnalités sont désactivées et ne sont pas encore fonctionnelles dans Azure pile. C’est parce que des exigences Azure spécifiques ou services qui nécessitent de ces fonctionnalités ne sont pas encore disponibles dans Azure pile. 

## <a name="next-steps"></a>Étapes suivantes

- [Avant de commencer avec Azure pile Web Apps](azure-stack-webapps-before-you-get-started.md)
- [Installer le fournisseur de ressources des applications Web](azure-stack-webapps-deploy.md)

Vous pouvez également essayer autre [plateforme en tant que service (PaaS) services](azure-stack-tools-paas-services.md), tels que le [fournisseur de ressources de SQL Server](azure-stack-sql-rp-deploy-short.md) et le [fournisseur de ressources MySQL](azure-stack-mysql-rp-deploy-short.md).

<!--Image references-->
[1]: ./media/azure-stack-webapps-overview/AppService_Portal.png
