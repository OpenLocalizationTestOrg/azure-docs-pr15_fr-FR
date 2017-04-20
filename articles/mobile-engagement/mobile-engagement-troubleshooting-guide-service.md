<properties 
   pageTitle="Azure Engagement Mobile Guide - Service de dépannage" 
   description="Résolution des problèmes de repères pour Azure Engagement Mobile" 
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

# <a name="troubleshooting-guide-for-service-issues"></a>Guide de dépannage pour les problèmes de Service

Les éléments suivants sont les éventuels problèmes rencontrés avec l’exécution de Azure Mobile Engagement.

## <a name="service-outages"></a>Interruption de service

### <a name="issue"></a>Problème
- Problèmes qui semblent avoir été causé par des interruptions de Service Engagement Azure Mobile.

### <a name="causes"></a>Causes
- Problèmes qui semblent avoir été causé par des interruptions de Service Engagement Azure Mobile peuvent être dû à plusieurs problèmes différents :
    - Problèmes qui apparaissent à l’origine systémiques à l’ensemble de Azure Mobile Engagement
    - Problèmes connus provoqués par des coupures server (pas toujours s’affiche dans l’état du serveur) :
    - Planification des retards, erreurs de ciblage, problèmes de mise à jour de Badge, arrêter statistiques collecte, Push cesse de fonctionner, cessent de fonctionner, les nouvelles applications ou les utilisateurs de l’API Impossible de créer, erreurs DNS et délai d’attente dans l’interface utilisateur, API ou les applications sur un appareil.
    - Cloud dépendance défaillances [état du Service Azure](http://status.azure.com/)
    - Notifications Push Notification Services (solution) dépendance défaillances
    - Défaillances App Store

1) Pour tester pour voir si le problème est systémique, vous pouvez tester la même fonction à partir d’un autre
   
   - Application Mobile Engagement intégré Azure
   - Dispositif de test
   - Version du système d’exploitation périphérique test
   - Campagne marketing
   - Compte d’administrateur
   - Navigateur (Internet Explorer, Firefox, Chrome, etc.)
   - Ordinateur

2) Pour tester si le problème n’affecte que l’interface utilisateur ou de l’API :

   - Testez la même fonction à partir de l’interface utilisateur de Azure Mobile Engagement et API Engagement Azure Mobile.

3) Pour tester si le problème avec votre réseau cellulaire téléphone :

   - Testez lorsque vous êtes connecté à Internet via Wi-Fi et lorsque vous êtes connecté via votre réseau cellulaire 3G.
   - Vérifiez que votre pare-feu ne bloque les Ports ou les adresses IP de Azure Mobile Engagement.

4) Pour tester si le problème est avec votre appareil :

   - Vérifier si votre appareil est en mesure de vous connecter à Azure Mobile Engagement avec une autre application intégrée Azure Mobile Engagement.
   - Test que vous pouvez générer des incidents, tâches et événements à partir de votre téléphone, qui peut être vues dans l’interface utilisateur de Azure Mobile Engagement. 
   - Vérifier si vous pouvez envoyer les notifications push à partir de l’interface utilisateur Engagement Azure Mobile sur votre appareil en fonction de son ID de l’appareil. 

5) Pour tester si le problème est avec votre application :

   - Installer et tester votre application à partir d’un émulateur plutôt que d’un appareil physique :
   
6) Pour tester si avec les mises à niveau du système d’exploitation à l’utilisateur final appareils, qui nécessitent une mise à niveau du Kit de développement pour résoudre le problème :

   - Tester votre application sur différents appareils avec différentes versions de système d’exploitation.
   - Vérifiez que vous utilisez la version la plus récente du Kit de développement.
 
## <a name="connectivity-and-incorrect-information-issues"></a>Problèmes de connectivité et des informations incorrectes

### <a name="issue"></a>Problème
- Problèmes de connexion à l’interface utilisateur de Azure Mobile Engagement.
- Erreurs de connexion avec l’API de Azure Mobile Engagement.
- Problèmes de téléchargement de balises d’informations application via l’API d’appareil.
- Problèmes de téléchargement des journaux ou les données exportées à partir d’Azure Mobile Engagement.
- Informations incorrectes indiquées dans l’interface utilisateur de Azure Mobile Engagement.
- Informations incorrectes indiquées dans les journaux Azure Mobile Engagement.

### <a name="causes"></a>Causes
* Vérifiez que votre compte d’utilisateur dispose des autorisations suffisantes pour effectuer la tâche.
* Vérifiez que le problème n’est pas isolé sur un ordinateur ou votre réseau local.
* Vérifiez que que le service Azure Mobile Engagement n’a aucun signalées défaillances.
* Vérifiez que vos fichiers application informations balise suivent toutes ces règles :
    - Utiliser uniquement le jeu de caractères UTF8 (jeu de caractères ANSI n’est pas pris en charge).
    - Utilisez une virgule «, » comme caractère de séparation (vous pouvez ouvrir un service de requête permet de demander à modifier le caractère de séparation .csv à partir d’une virgule «, » d’un autre caractère comme un point-virgule « ; »).
    - Utiliser des minuscules pour les valeurs booléennes « true » et « false ».
    - Utiliser un fichier qui est inférieur à la taille de fichier maximale de 35 Mo.
 
