<properties 
   pageTitle="Azure Engagement Mobile Guide - Analytique de dépannage" 
   description="Dépannage des problèmes Analytique, surveillance, Segmentation et tableau de bord dans Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-analytics-monitoring-segmentation-and-dashboard-issues"></a>Guide de dépannage pour les problèmes Analytique, surveillance, Segmentation et tableau de bord

Les éléments suivants sont les éventuels problèmes que vous pouvez rencontrer avec comment Azure Mobile Engagement recueille des informations sur vos applications, les appareils et les utilisateurs.

## <a name="missingdelayed-information"></a>Informations manquantes/différées

### <a name="issue"></a>Problème
- Informations sont retardées dans qui apparaissent dans le tableau de bord, Segmentation ou Analytique.
- Il manque des informations à partir de surveillance.
- Il manque des informations d’Analytique, Segmentation ou du tableau de bord.
- Limites de réussir segmentation.

### <a name="causes"></a>Causes

- Vous pouvez utiliser l’API Analytique, API moniteur, et API de Segments pour voir si toutes les données vous ne disposez pas de l’interface utilisateur est visible à travers les API.
- Si le Kit de développement Azure Mobile Engagement n’est pas correctement intégré à votre application vous sera en mesure d’afficher les informations dans l’Analytique, Segmentation, surveillance ou des tableaux de bord.
- Segments ne peuvent pas être modifiés une fois qu’ils ont été créés, segments ne peuvent être « cloné » (copiés) ou « destruction » (supprimés). Segments peuvent contenir uniquement des 10 critères.
- La meilleure façon de tester des informations manquantes de l’analyse consiste à configurer un périphérique de test, désinstaller et/ou réinstaller l’application sur le périphérique de test.
- Informations sont actualisées toutes les 24 heures pour Analytique, Segmentation ou des tableaux de bord.
- Informations contenues dans les nouveaux segments peuvent ne pas apparaître jusqu'à 24 heures après leur création même si le segment est basé sur les informations précédentes.
- Filtrage de vos données analytique dans l’interface utilisateur s’affichent tous les exemples de ce type quelle que soit la version de votre application (par exemple, « se bloque » filtrée par nom s’affichent à partir de la version 1 et 2 de votre application).
- La période de temps pour Analytique est basée sur la date à partir des paramètres de périphérique de l’utilisateur, un utilisateur dont téléphone est incorrectement défini pour la date peut s’affiche pas dans la période de temps incorrecte.
- Ne pousse aucun côté serveur les données sont enregistrées lorsque vous utilisez le bouton « tester », les données sont enregistrées uniquement pour les campagnes push réel.

## <a name="cant-locate-items-in-ui"></a>Impossible de trouver des éléments dans l’interface utilisateur

### <a name="issue"></a>Problème
- Ne peut pas créer des segments en fonction de certaines intégré ou informations application personnalisée balise critères.
- Impossible de trouver certaines intégré ou d’informations d’application personnalisée ajouter des balises à des critères dans Analytique, surveillance ou des tableaux de bord.
- Ne peut pas interpréter les données d’Analytique, surveillance, Segmentation ou des tableaux de bord.

### <a name="causes"></a>Causes

- Certains éléments intégré et application informations balises sont uniquement disponibles en tant que critères push, mais peuvent ne pas être ajoutée à un segment ou visible d’Analytique, surveillance ou du tableau de bord. 
- Pour des éléments intégrés et balises informations application qui ne peut pas être ajoutées à un segment, vous devrez liste des paramètres de ciblage de critères dans chaque campagne pour effectuer la même fonction qu’un ciblage basé sur un segment.
- Voir les menus contextuels dans les sections Analytique, surveillance, Segmentation et tableaux de bord de l’interface utilisateur de Azure Mobile Engagement pour obtenir de l’aide et comment les informations.

## <a name="crash-troubleshooting"></a>Bloquer la résolution des problèmes

### <a name="issue"></a>Problème
- Application se bloque qui apparaissent dans le tableau de bord, surveillance ou Analytique.

### <a name="causes"></a>Causes

- Pour résoudre les problèmes Application se bloque dans Analytique, surveillance ou du tableau de bord veillez à vérifier les notes de publication pour des problèmes connus avec des versions précédentes du Kit de développement.
- Pour résoudre application se bloque effectuer un événement à partir d’un appareil de test avec votre application installée et recherchez votre ID de périphérique dans la section « Surveiller les événements – » de l’interface utilisateur de Azure Mobile Engagement. Puis exécutez l’événement à l’origine de votre application de se bloquer et consulter des informations supplémentaires dans la section « Moniteur – blocage » de l’interface utilisateur de Azure Mobile Engagement. 

 
