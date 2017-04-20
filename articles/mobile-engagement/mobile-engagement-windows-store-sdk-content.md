<properties 
    pageTitle="Contenu du Kit de développement Windows universel applications" 
    description="En savoir plus sur le contenu du Kit de développement Windows universel applications pour Azure Mobile Engagement"                    
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-store" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-universal-apps-sdk-content"></a>Contenu du Kit de développement Windows universel applications

Ce document répertorie et décrit le contenu déployé par le Kit de développement de votre application.

##<a name="the-resources-folder"></a>La `/Resources` dossier

Ce dossier contient toutes les ressources nécessitant l’Engagement Mobile. Vous pouvez également personnaliser les adapter à votre application.

- `EngagementConfiguration.xml`: Fichier de configuration de l’Engagement Mobile, il s’agit de l’endroit où vous pouvez personnaliser les paramètres d’Engagement Mobile (chaîne de connexion Engagement Mobile, panne du rapport...).

### <a name="html-folder"></a>dossier /HTML

- `EngagementNotification.html`: Le `Notification` mode html conception pour bannières intégrée dans l’application web.

- `EngagementAnnouncement.html`: Le `Announcement` conception html d’affichage pour les vues INTERSTITIELLES intégrée dans l’application web.

### <a name="images-folder"></a>dossier /images

- `EngagementIconNotification.png`: L’icône de marque affichée à gauche d’une notification, remplacez celle-ci en icône de votre marque.

- `EngagementIconOk.png`: Le `Ok` icône accessibles pages de contenu pour le bouton action ou validation.

- `EngagementIconNOK.png`: Le `NOK` icône utilisée lorsque le bouton de validation accessibles pages de contenu est désactivé.
 
- `EngagementIconClose.png`: Le `Close` icône de la portée notifications et le contenu pour le bouton faire disparaître.

### <a name="overlay-folder"></a>dossier /Overlay

- `EngagementPageOverlay.cs`: La page superposition responsable de l’ajout de l’Engagement atteindre l’interface utilisateur dans l’application à son enfant.
  
