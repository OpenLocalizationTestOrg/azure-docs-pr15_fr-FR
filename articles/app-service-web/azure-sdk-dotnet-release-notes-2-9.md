<properties 
   pageTitle="Notes de mise à jour de SDK Azure pour .NET 2,9" 
   description="Notes de mise à jour de SDK Azure pour .NET 2,9" 
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

# <a name="azure-sdk-for-net-29-release-notes"></a>Notes de mise à jour de SDK Azure pour .NET 2,9

##<a name="overview"></a>Vue d’ensemble

Ce document contient les notes de publication pour le Kit de développement Azure pour .NET 2,9 mise à jour. 

Pour plus d’informations sur les mises à jour dans cette version, voir l' [annonce Azure SDK 2,9 publier](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>Afficher un aperçu de Azure SDK 2,9 pour la mise à jour de Visual Studio 2015 2 et Visual Studio « 15 »
 
Cette mise à jour inclut les correctifs de bogues suivants :

- Questions sur le reste de génération de Client de l’API dans lequel la chaîne « Type inconnu » apparaît comme le nom du dossier de génération du code et/ou le nom de l’espace de noms déplacée dans le code généré.
- Problème lié à WebJobs planifiée dans lequel les informations d’authentification a été ne parvient pas à transmettre au planificateur de processus de mise en service.

Cette mise à jour inclut la nouvelle fonctionnalité suivante :

- Prise en charge pour les Services d’application secondaire dans l’onglet « Services » de la boîte de dialogue Mise en service de Service d’application. 

##<a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Données Azure Lake Tools pour Visual Studio 2015 Update 2
 
Cette mise à jour comprend les éléments suivants :

- **Azure données Lake Tools** pour Visual Studio est maintenant fusionnés dans le Kit de développement Azure version .NET. L’outil est installé automatiquement lorsque vous installez Azure SDK. 

    L’outil est mis à jour fréquemment, accédez [ici](http://aka.ms/datalaketool) pour obtenir les mises à jour.

- **Explorateur de serveurs** permet maintenant afficher toutes les et créer certaines entités de métadonnées U-SQL. Pour plus d’informations, voir [ce](https://azure.microsoft.com/documentation/services/data-lake-analytics/) billet de blog.


##<a name="hdinsight-tools"></a>Outils HDInsight 

**Outils HDInsight** pour Visual Studio prend désormais en charge HDInsight version 3.3, y compris affichant Tez graphiques et autres langues résout.


##<a name="azure-resource-manager"></a>Gestionnaire de ressources Azure 

Cette version ajoute [KeyVault](../resource-manager-keyvault-parameter.md) prise en charge pour les modèles de processeur.

##<a name="see-also"></a>Voir aussi

[Billet d’annonce SDK 2,9 Azure](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)
