
<properties 
   pageTitle="Notes de mise à jour de SDK Azure pour .NET 2.7 et .NET 2.7.1" 
   description="Notes de mise à jour de SDK Azure pour .NET 2.7 et .NET 2.7.1" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/17/2016"
   ms.author="juliako"/>

# <a name="azure-sdk-for-net-27-and-net-271-release-notes"></a>Notes de mise à jour de SDK Azure pour .NET 2.7 et .NET 2.7.1

##<a name="overview"></a>Vue d’ensemble

Ce document contient les notes de publication pour le Kit de développement Azure pour .NET 2.7 mise à jour. 

Le document contient également les notes de publication pour le Kit de développement Azure pour .NET 2.7.1 version.

Azure SDK 2.7 est uniquement prise en charge dans Visual Studio 2015 et Visual Studio 2013. [Azure SDK 2.6](https://azure.microsoft.com/downloads/) est que le dernier pris en charge le Kit de développement pour Visual Studio 2012.

Pour plus d’informations sur cette version, voir [annonce Azure SDK 2.7 publier](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) et [Azure SDK 2.7.1 annonce publier](http://go.microsoft.com/fwlink/?LinkId=623850).

##<a name="azure-sdk-for-net-27"></a>Kit de développement Azure pour .NET 2.7

###<a name="sign-in-improvements-for-visual-studio-2015"></a>Se connecter améliorations pour Visual Studio 2015

Azure 2.7 SDK Visual Studio 2015 prend en charge les nouvelles fonctionnalités de gestion des identités dans Visual Studio 2015.  Cela inclut la prise en charge des comptes ayant accès à Azure via un contrôle d’accès en fonction de rôle, fournisseurs de solutions de Cloud, DreamSpark et d’autres types de compte et d’abonnement.

Les améliorations connexion incluses avec Azure SDK 2.7 sont uniquement disponibles dans Visual Studio 2015. Prise en charge pour Visual Studio 2013 est inclus dans Azure SDK 2.7.1.


###<a name="mobile-sdk"></a>Kit de développement mobile

Mise à jour de modèles **d’Applications Mobile** afin de refléter le processus de [package NuGet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) et le programme d’installation plus récent.

###<a name="service-bus"></a>Bus des services 

Améliorations et correctifs générales. Pour des détails sur les fonctionnalités et les mises à jour, veuillez consulter les notes de publication de la dernière [Service Bus NuGet](http://www.nuget.org/packages/WindowsAzure.ServiceBus/).

###<a name="hdinsight-tools"></a>Outils HDInsight 

Les mises à jour suivantes ont été apportées dans cette version. Ces mises à jour sont dans l’aperçu. Pour plus d’informations, voir [ce billet de blog](http://go.microsoft.com/fwlink/?LinkId=619108).

- Graphiques Hive pour Hive sur les tâches Tez
- Prise en charge complète de la ruche DML IntelliSense
- Prise en charge des modèles cochon
- Modèles de vague pour les services d’Azure

####<a name="breaking-changes"></a>Annulation de modifications

- Ancien **vague de** projet doit être mis à niveau lors de l’utilisation de cette version des outils. Pour plus d’informations, voir [ce billet de blog](http://go.microsoft.com/fwlink/?LinkId=619108).
- Visual Studio Web Express n’est plus pris en charge. Pour plus d’informations, voir [ce billet de blog](http://go.microsoft.com/fwlink/?LinkId=619108).

###<a name="azure-app-service-tools"></a>Outils de Service application Azure

Dans cette version les mises à jour suivantes ont été apportées aux Extensions des outils Web. Pour plus d’informations, voir [ce](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) billet de blog. 

- Prise en charge pour les comptes DreamSpark ajoutés
- Modification complète Azure outils apportée à prendre en charge les nouvelles API de gestion des ressources Azure
- Prise en charge pour le Service d’application Azure ajouté à [l’Explorateur de Cloud](#cloud_explorer)

####<a name="known-issues"></a>Problèmes connus

Nœuds de connecteur de déploiement de l’application Web n’apparaissent pas sous le nœud emplacements dans l’Explorateur de serveurs et nœuds d’enfants emplacement Web App déploiement ne sont pas chargées sous Cloud Explorer. Ce problème a été résolu et préparé la prochaine version du Kit de développement logiciel. 


###<a name="cloud_explorer"></a>Explorer cloud pour Visual Studio 2015

Azure 2.7 SDK inclut Cloud Explorer Visual 2015 Studio qui vous permet d’afficher vos ressources Azure, inspecter leurs propriétés et effectuer des actions pour les développeurs clé à partir de dans Visual Studio. 

Explorer cloud prend en charge les éléments suivants :

- Groupe de ressources et Type de ressource des vues de vos ressources Azure 
- Rechercher des ressources par nom (disponible dans l’affichage Type de ressource)
- Prise en charge des abonnements et les ressources ayant rôle en fonction de contrôle d’accès (RBAC) appliqué 
- Panneau Action intégré qui affiche destinés aux développeurs des actions spécifiques aux ressources sélectionnées. Par exemple : joindre débogueur distant pour Machines virtuelles créées à l’aide de la pile Gestionnaire de ressources, afficher les données de diagnostic pour Machines virtuelles etc..
- Volet Propriétés intégrée qui affiche les propriétés destinés aux développeurs fréquemment nécessaires au cours de développement/test 
- Basculement rapide du compte à utiliser lors de l’énumération des ressources (utilisez la commande paramètres de barre d’outils) 
- Filtrage des abonnements à utiliser lors de l’énumération des ressources (utilisez la commande paramètres de barre d’outils) 
- Liens approfondies au portail Azure pour la gestion des ressources et des groupes de ressources 
 
 
###<a name="azure-resource-manager-tools"></a>Outils du Gestionnaire de ressources Azure 

Les outils du Gestionnaire de ressources Azure ont été mis à jour pour utiliser les nouveaux types d’abonnement et de contrôle d’accès basé sur (rôle RBAC).  Inclus avec ces modifications est la possibilité d’utiliser les nouveaux comptes d’espace de stockage, en plus de stockage classique, pour stocker des objets au cours du déploiement.  

Si vous utilisez un projet de groupe de ressources Azure à partir d’une version précédente du Kit de développement avec version2.7 SDK, un nouveau script de déploiement est nécessaire pour déployer à l’aide d’un nouveau compte de stockage au lieu de stockage classique.  Vous serez invité avant que les modifications sont apportées à votre projet pour ajouter le nouveau script.  L’ancien script est renommé et vous devrez effectuer manuellement les modifications apportées à nouveau le script.
 
 
###<a name="storage-explorer-tools"></a>Outils de l’Explorateur de stockage 

- Prise en charge pour l’affichage des objets BLOB ajouter. Plus d’informations dans [ce billet de blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx). 
- Prise en charge pour l’affichage des comptes de stockage Premium via l’Explorateur de serveur. Explorateur de serveurs n’affiche que des objets BLOB de page pour les comptes de stockage premium qu’ils sont le seul type pris en charge pour les comptes de stockage premium.

###<a name="azure-data-factory-tools-for-visual-studio"></a>Données Azure usine Tools pour Visual Studio 

Présentation des **Outils d’usine données Azure** pour Visual Studio. Voici les fonctionnalités activées. Voir [ce billet de blog](http://go.microsoft.com/fwlink/?LinkId=617530) pour plus d’informations.

- **Création de modèle basé**: sélectionnez utiliser la casse en fonction de modèles, les modèles de déplacement de données ou les modèles de traitement des données à déployer une solution d’intégration des données de bout en bout et prise en main pratique rapidement avec les données par défaut. 
- **Intégration avec l’Explorateur de solutions pour la création et de déploiement d’entités Data Factory**: créer et déployer des pipelines et entités associées projets Visual Studio. 
- **Intégration avec diagramme afficher pour l’interaction visuelle lors de la programmation**: Créez visuellement les pipelines et jeux de données à l’aide de la vue de diagramme. 
- **Intégration avec l’Explorateur de serveurs pour la navigation et interaction avec les entités déjà déployées**: tirer parti de l’Explorateur de serveurs pour parcourir déjà déployé données références et entités correspondantes. Importer une usine de données déployés ou n’importe quelle entité (Pipeline, services liés, les groupes de données) dans votre projet. 
- **JSON d’édition avec validation de schéma et intellisense enrichi**: efficacement configurer et modifier des documents JSON d’entités Data Factory enrichie validation intellisense et schéma 
- **Publication de type d’environnement**: publier en créant des fichiers de configuration distincte pour chaque environnement de création avec pipelines permettant de développement, test ou de production.
- **Cochon, Hive et .net en fonction de prise en charge du traitement des données**: prise en charge cochon et Scripts ruche dans project Data Factory. Prise en charge pour référencer le projet c# pour la gestion .net activité.

##<a name="azure-sdk-for-net-271"></a>Kit de développement Azure pour .NET 2.7.1

La section suivante contient des mises à jour qui ont été introduites avec le Kit de développement Azure pour .NET 2.7.1 version.
###<a name="hdinsight-tools"></a>Outils HDInsight 

Pour obtenir des informations plus détaillées sur les mises à jour des outils de HDInsight, voir [ce billet de blog](http://go.microsoft.com/fwlink/?LinkId=623831).

- Hive travail opérateur affichage (une nouvelle fonctionnalité)

    Pour vous aider à comprendre votre requête Hive mieux, la fonctionnalité d’affichage d’opérateur ruche a été ajoutée. Pour afficher tous les opérateurs à l’intérieur d’un sommet, double-cliquez sur les sommets du graphique travail. Pour afficher des détails d’un opérateur spécifique, placez le curseur sur cet opérateur.
- Indicateur d’erreur Hive (une nouvelle fonctionnalité)

    Pour vous permettent d’afficher les erreurs de grammaire instantanément, la fonctionnalité ruche erreur marqueur a été ajoutée. En outre, les messages d’erreur ont été améliorées et vous pouvez maintenant voir instantanément les fautes de grammaire détaillées (jusqu'à ce que cette version, vous deviez envoyer un script Hive au cluster et attendez depuis un certain temps avant d’obtenir plus d’informations sur votre message d’erreur).  
- Graphique de topologie vague (une nouvelle fonctionnalité)

    Il est très important de visualisation lorsque vous voulez vérifier si votre topologie fonctionne comme prévu. Dans cette version, nous avons ajouté visualisation pour les graphiques vague. Vous pouvez visualiser les mesures importantes pour votre topologie (par exemple, une couleur indique qu’un certain boulons météo est « occupé » ou non). Vous pouvez également double-cliquer sur boulons/bec pour afficher davantage de détails.

- Prise en charge des clusters HDInsight qui ont été créés dans le portail Azure (un correctif bogue)

    Vous pouvez désormais utiliser Visual Studio pour afficher et soumettre des travaux à tous vos clusters HDInsight quel que soit l’endroit où le cluster ont été créés.

- Prise en charge IntelliSense plus & chargement plus rapide ruche métadonnées (amélioration)

    Nous avons amélioré la IntelliSense en ajoutant davantage de suggestions convivial. Par exemple, alias de table peuvent désormais également être suggérés dans IntelliSense afin de pouvoir écrire plus facilement votre requête. En outre, nous avons amélioré les métadonnées Hive chargement il faudra seulement quelques secondes pour répertorier toutes les bases de données, les tables et les colonnes de votre metastore Hive.

Pour obtenir des informations plus détaillées sur les mises à jour des outils de HDInsight, voir [ce billet de blog](http://go.microsoft.com/fwlink/?LinkId=623831).

###<a name="improvements-in-visual-studio-2013"></a>Améliorations dans Visual Studio 2013

- Kit de développement logiciel Azure 2.7.1 permet de Visual Studio 2013 accéder aux abonnements via contrôle d’accès en fonction de rôle, les fournisseurs de Solution Cloud et Dreamspark et comptes Azure.
- Avec Azure SDK 2.7.1, la nouvelle fenêtre outil Cloud Explorer est maintenant également disponible dans Visual Studio 2013.

###<a name="known-issues"></a>Problèmes connus

Installez la version 2.6 SDK Azure ou 2.7.1 pour Visual Studio Communauté 2013 sur un non - anglais OS affiche un avertissement que les ressources en anglais et non anglais de Visual Studio peuvent ne pas correspondre. Ce message d’avertissement peut être fermé en toute sécurité. Il se produit uniquement si l’ordinateur ne contenait pas d’une installation précédente de Visual Studio Communauté 2013 et que vous installez le Kit de développement sur un non - anglais du système d’exploitation. L’avertissement s’affiche après que le module linguistique concerne les ressources RTM Visual Studio, mais avant d’appliquer la mise à jour 4. Faire disparaître l’avertissement permettra du module linguistique pour continuer et terminer appliquant la version mise à jour 4 du contenu pack linguistique.

Projets LightSwitch ne sont pas compatible avec cette version. Ce problème sera résolu avec la nouvelle version du Kit de développement logiciel.

##<a name="also-see"></a>Voir aussi
[Kit de développement logiciel Azure 2.7.1 billet annonce](http://go.microsoft.com/fwlink/?LinkId=623850)

[Billet d’annonce 2.7 SDK Azure](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[Prise en charge et déclassement des informations pour le Kit de développement Azure pour .NET et API](https://msdn.microsoft.com/library/azure/dn479282.aspx/)
