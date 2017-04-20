<properties
    pageTitle="Paramètres de stratégie et la gestion des périphériques de groupe | Microsoft Azure"
    description="Fournit des informations sur la stratégie de groupe et appareil mobile des paramètres de gestion des (MDM) qui doivent être utilisés sur les appareils appartenant à l’entreprise. Ces politiques sont appliquées à l’ensemble de l’unité de l’utilisateur."
    services="active-directory"
    keywords="que sont une stratégie de groupe et les paramètres de périphériques mobiles pour entreprise état itinérance, entreprise état itinérance, cloud windows"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="group-policy-and-mdm-settings"></a>Paramètres de stratégie de groupe et la gestion des périphériques

Utilisez ces stratégie de groupe et les paramètres de gestion des (périphériques mobiles) appareil mobile uniquement sur les périphériques appartenant à l’entreprise, car ces stratégies sont appliquées à l’ensemble de l’unité de l’utilisateur. Appliquer une stratégie de la gestion des périphériques pour désactiver la synchronisation de paramètres pour un personnel, appareil appartenant à l’utilisateur sera négative ont une incidence sur l’utilisation de cet appareil. En outre, d’autres comptes d’utilisateur sur le périphérique seront également affectés par la stratégie.

Entreprises qui souhaitent gérer d’itinérance pour appareils mobiles (non gérés) personnels peuvent utiliser le portail Azure pour activer ou désactiver d’itinérance plutôt que d’utiliser la stratégie de groupe ou MDM.
Les tableaux suivants décrivent les paramètres de stratégie disponibles.

## <a name="mdm-settings"></a>Paramètres de la gestion des périphériques
Les paramètres de stratégie MDM s’appliquent à Windows 10 et Windows 10 Mobile.  Prise en charge Windows 10 Mobile existe uniquement pour le compte Microsoft en fonction d’itinérance via compte OneDrive de l’utilisateur.  Reportez-vous à la section « Périphériques et points de terminaison » pour plus d’informations sur les périphériques sont prises en charge pour Azure AD en fonction de la synchronisation.

| Nom                               | Description                                                          |
|------------------------------------|----------------------------------------------------------------------|
| Autoriser la connexion de compte Microsoft | Permet aux utilisateurs de s’authentifier à l’aide d’un compte Microsoft sur l’appareil |
| Autoriser les mes paramètres de synchronisation             | Permet aux utilisateurs de se déplacent paramètres Windows et les données de l’application ; Désactivation de cette stratégie désactive la synchronisation, ainsi que des sauvegardes sur les appareils mobiles                  |

## <a name="group-policy-settings"></a>Paramètres de stratégie de groupe
Les paramètres de stratégie de groupe s’appliquent aux appareils Windows 10 qui sont joints à un domaine Active Directory. Le tableau inclut également des paramètres hérités qui apparaît pour gérer les paramètres de synchronisation, mais qui ne fonctionnent pas pour entreprise état d’itinérance pour Windows 10, qui sont signalés par « N’utilisez pas » dans la description.

| Nom                                | Description |
|-------------------------------------|-------------|
| Comptes : Comptes Microsoft de bloc  |Ce paramètre de stratégie empêche les utilisateurs d’ajout de nouveaux comptes Microsoft sur cet ordinateur|
| Ne pas synchroniser                         |Empêche les utilisateurs pour se déplacent paramètres Windows et données d’application|
| Ne pas synchroniser personnaliser             |Désactive la synchronisation du groupe Thèmes|
| Ne pas synchroniser les paramètres du navigateur        |Désactive la synchronisation du groupe Internet Explorer|
| Ne pas synchroniser les mots de passe               |Désactive la synchronisation du groupe de mots de passe|
| Ne pas synchroniser les autres paramètres Windows  |Désactive la synchronisation de groupe paramètres autres fenêtres|
| Ne pas synchroniser de personnalisation du bureau |N’utilisez pas ; n’a aucun effet|
| Ne pas synchroniser sur les connexions limitées  |Désactive d’itinérance sur les connexions, telles que 3 G cellulaire limitées|
| Ne pas synchroniser des applications                    |N’utilisez pas ; n’a aucun effet|
|Ne pas synchroniser les paramètres de l’application             |Désactive d’itinérance de données d’application|
|Ne pas synchroniser les paramètres de démarrage           |N’utilisez pas ; n’a aucun effet|


## <a name="related-topics"></a>Rubriques connexes
- [Vue d’ensemble d’itinérance état entreprise](active-directory-windows-enterprise-state-roaming-overview.md)
- [Activer l’état d’entreprise d’itinérance dans Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
- [Paramètres et les données d’itinérance Forum aux questions](active-directory-windows-enterprise-state-roaming-faqs.md)
- [Référence des paramètres itinérance Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
