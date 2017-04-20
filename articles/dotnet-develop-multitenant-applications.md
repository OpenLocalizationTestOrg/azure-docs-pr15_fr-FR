<properties
    pageTitle="Modèle d’Application Web client multiples | Microsoft Azure"
    description="Rechercher une vue d’ensemble architecturale et modèles de conception qui décrivent comment mettre en œuvre une application web client à plusieurs sur Azure."
    services=""
    documentationCenter=".net"
    authors="wadepickett" 
    manager="wpickett"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/05/2015"
    ms.author="wpickett"/>

# <a name="multitenant-applications-in-azure"></a>Applications partagées dans Azure

Une application partagée est une ressource partagée qui permet aux utilisateurs distincts, ou « utilisateurs » afficher l’application comme s’il s’agissait leur propre. Scénario qui se prête à une application partagée est une dans laquelle tous les utilisateurs de l’application voudrez peut-être personnaliser l’expérience utilisateur, mais dans le cas contraire ont les mêmes besoins professionnels de base. Office 365, Outlook.com et visualstudio.com sont des exemples de grandes applications partagées.

Point de vue d’un fournisseur d’application, les avantages des donc le partage concernent principalement opérationnelles et l’efficacité. Une version de votre application peut répondre aux besoins de nombreux clients/clients, ce qui permet de consolidation du système de tâches d’administration tels que surveillance optimisation des performances, maintenance des logiciels et des sauvegardes de données.

Les éléments suivants fournit une liste des objectifs et des exigences du point de vue d’un fournisseur plus significatifs.

- **Provisioning**: vous devez être en mesure de mise en service de nouveaux clients de l’application.  Pour les applications partagées avec un grand nombre de clients, il est généralement nécessaire automatiser ce processus en activant attribution en libre-service.
- **Facilité de gestion**: vous devez être en mesure de mettre à niveau de l’application et effectuer d’autres tâches de maintenance alors que plusieurs clients sont à l’utiliser.
- **Analyse**: vous devez être en mesure de surveiller l’application à tout moment pour identifier les problèmes et de les résoudre. Ceci inclut l’analyse comment chaque client est à l’aide de l’application.

Une application partagée correctement implémentée offre les avantages suivants aux utilisateurs.

- **Isolement**: les activités des clients individuels ne concernent pas l’utilisation de l’application par d’autres clients. Clients ne peuvent pas accéder à d’autres données. Il s’affiche sur le client comme s’ils avaient exclusif utilisation de l’application.
- **Disponibilité**: clients individuels que l’application doit être constamment disponibles, peut-être avec garanties définies dans accord. Là encore, les activités des autres clients ne doivent pas affecter la disponibilité de l’application.
- **Extensibilité du**: l’application s’adapte à répondre à la demande de clients individuels. La présence et les actions d’autres clients ne devraient pas affecter les performances de l’application.
- **Coûts**: coûts sont inférieures à l’exécution d’une application dédiée, seul client, car plusieurs Location permet de partager des ressources.
- **Personnalisation**. La possibilité de personnaliser l’application pour un client individuel de diverses façons telles que l’ajout ou suppression de fonctionnalités, modification des couleurs et logos ou en ajoutant même leur propre code ou un script.

En bref, il existe de nombreuses considérations que vous devez prendre en compte pour fournir un service hautement scalable, il existe également un nombre des objectifs et exigences qui sont communes à de nombreuses applications partagées. Certaines peuvent ne pas être pertinents dans des scénarios spécifiques, et l’importance des objectifs individuels et les exigences diffèrent dans chaque scénario. En tant que fournisseur de l’application partagé, vous aurez également objectifs et les exigences telles que des clients objectifs et configuration requise, rentabilité, de facturation, plusieurs niveaux de service, mise en service, la facilité de gestion de surveillance et automatisation de la réunion.

Pour plus d’informations sur les considérations supplémentaires de conception d’une application partagée, voir [hébergeant une Application client à plusieurs sur Azure][]. Pour plus d’informations sur les modèles d’architecture données courantes des applications de base de données clients à plusieurs logiciels en tant que service (SaaS), voir [Modèles de conception pour les Applications de SaaS client à plusieurs avec la base de données SQL Azure](./sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md). 

Azure fournit de nombreuses fonctionnalités qui vous permettent de résoudre les problèmes clés rencontrés lors de la conception d’un système partagé.

**Isolement**

- Site Web de segment initiés par en-têtes d’hôte avec ou sans communication SSL
- Site Web de segment initiés par des paramètres de requête
- Services Web dans les rôles de travail
    - Rôles de travail. traitent généralement les données sur le serveur principal d’une application.
    - Rôles Web qui agissent en tant que frontend pour les applications.

**Espace de stockage**

Gestion des données comme base de données SQL Azure ou stockage Azure services tels que le service de tableau qui fournit des services pour le stockage de grandes quantités de données non structurées et le service d’objets Blob qui fournit des services pour stocker de grandes quantités de texte non structurée ou des données binaires telles que des images, l’audio et vidéo.

- Protection des données pouvant être partagée dans le cas de la base de données SQL les connexions SQL Server par client
- À l’aide de Tables Azure pour Application ressources en spécifiant une stratégie d’accès au niveau de conteneur, vous pouvez la fonctionnalité permettant d’ajuster les autorisations sans avoir à émettre nouvelle URL pour les ressources protégés à l’aide des signatures accès partagé.
- Files d’attente Azure pour files d’attente Azure de ressources d’Application sont généralement utilisés pour traitement de lecteur au nom de clients, mais peuvent également être utilisées pour distribuer le travail requis pour la mise en service ou de gestion.
- Files d’attente Bus de service pour les ressources de l’Application qui pousse de travail partagé à un service, vous pouvez utiliser une seule file d’attente où chaque expéditeur client uniquement avec autorisations (dérivé de revendications émises ACS) pour transmission à cette file d’attente, tandis que seuls les destinataires à partir du service ont autorisé à extraire à partir de la file d’attente les données provenant de plusieurs clients.


**Connexion et Services de sécurité**

- Azure Service marché, une infrastructure de messagerie qui se situe entre les applications leur permettant d’échanger des messages dans une façon souple pour échelle amélioré et résilience

**Services de mise en réseau**

Azure fournit plusieurs services de réseau prenant en charge l’authentification et améliorent la gestion de vos applications hébergées. Ces services sont les suivants :

- Azure vous permet de réseau virtuel vous mise en service et de gestion des réseaux privés virtuels (VPN) de Azure ainsi liez en toute sécurité avec locale infrastructure informatique.
- Gestionnaire de trafic réseau virtuel vous permet de charger le trafic entrant solde entre plusieurs services Azure hébergés si ils exécutent dans le même centre de données ou sur différents centres de données dans le monde entier.
- Azure Active Directory (AD Azure) est un service reste, moderne qui fournit les fonctionnalités de contrôle de gestion et d’accès identité pour vos applications cloud. À l’aide de Azure AD pour les ressources de l’Application Azure AD pour offre un moyen facile d’authentification et d’autorisation des utilisateurs d’accéder à vos applications web et services tout en autorisant les fonctionnalités d’authentification et d’autorisation pour être pris en compte se déconnecter de votre code.
- Bus des services Azure fournit une messagerie sécurisée et fonctionnalité de flux de données pour distribués et applications hybrides, telles que les communications entre Azure hébergé applications et applications en local et services, sans avoir besoin d’infrastructure de pare-feu et de sécurité complexes. L’aide du Service Bus relais pour les ressources de l’Application aux services qui sont exposées comme points de terminaison peut-être appartenir vers le client (par exemple, hébergé à l’extérieur du système, telles que sur site), ou qu’ils soient services mis en service spécifiquement pour le client (parce que des données sensibles spécifiques au client parcourt entre eux).



**Mise en service des ressources**

Azure fournit moyens de clients nouvelle disposition pour l’application. Pour les applications partagées avec un grand nombre de clients, il est généralement nécessaire automatiser ce processus en activant attribution en libre-service.

- Rôles de travail vous permettent de mise en service et la mise en service par client ressources (par exemple, quand un nouveau client signes à distance ou annule), recueillir les mesures pour contrôle utilisation et la gestion échelle suivant une certaine planification ou en réponse à l’intersection des seuils d’indicateurs de performance clés. Ce rôle même peut également être utilisé pour diffuser les mises à jour et mises à niveau vers la solution.
- Des objets BLOB Azure peuvent être utilisés pour la mise en service de cluster ou préalable initialisés Packages, images de disque dur virtuel et d’autres ressources du service des ressources de stockage pour les nouveaux clients tout en fournissant les stratégies d’accès au niveau de conteneur protéger le cluster.
- Options de mise en service des ressources de base de données SQL pour un client incluent :

    -   DDL dans les scripts ou incorporée en tant que ressources dans des assemblys
    -   Packages DAC SQL Server 2008 R2 déployé par programme.
    -   Copie d’une base de données de référence principales
    -   À l’aide de la base de données importation et exportation de mise en service de nouvelles bases de données à partir d’un fichier.



<!--links-->

[Héberger une Application cliente à plusieurs sur Azure]: http://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: http://msdn.microsoft.com/library/windowsazure/hh689716
