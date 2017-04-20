<properties
    pageTitle="Intégration de SDK Android Azure Engagement Mobile"
    description="Dernières mises à jour et procédures pour Android SDK pour Azure Mobile Engagement"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="piyushjo" />

# <a name="release-notes"></a>Notes de publication

## <a name="423-08102016"></a>4.2.3 (08/10/2016)

- Pas plus de verrou Wi-Fi.
- Corriger un blocage lors de l’appel getDeviceId avant initialisation (bogues introduite dans 4.2.0).

## <a name="422-05172016"></a>4.2.2 (17/05/2016)

- Améliorations de la stabilité.

## <a name="421-05102016"></a>4.2.1 (05/10/2016)

- Sécurité : désactiver l’accès de fichier local d’affichage web.
- Sécurité : supprimer `EngagementPreferenceActivity` classe étend sécurisé et obsolète `PreferenceActivity` cours.
- Sécurité : portée activités décrits maintenant pour utiliser `exported="false"`, cet indicateur peut également être utilisé dans les versions précédentes du Kit de développement logiciel.

## <a name="420-03112016"></a>4.2.0 (11/03/2016)

- Le Kit de développement est désormais sous licence sous MIT.
- Autorise à spécifier un identificateur d’appareil personnalisée lors de l’initialisation SDK.

## <a name="415-02012016"></a>4.1.5 (02/01/2016)

- Améliorations de la stabilité.

## <a name="414-01262016"></a>4.1.4 (26/01/2016)

- Améliorations de la stabilité.

## <a name="413-1292015"></a>4.1.3 (9/12/2015)

- Améliorations de la stabilité.

## <a name="412-11252015"></a>4.1.2 (25/11/2015)

- Améliorations de la stabilité.

## <a name="411-11042015"></a>4.1.1 (11/04/2015)

- Améliorations de la stabilité.

## <a name="410-08252015"></a>4.1.0 (25/08/2015)

- Gérer le nouveau modèle d’autorisation pour Android M.
- Pouvez désormais configurer les fonctionnalités de l’emplacement en cours d’exécution au lieu d’utiliser `AndroidManifest.xml`.
- Corriger un bogue d’autorisation : Si vous utilisez `ACCESS_FINE_LOCATION`, puis `ACCESS_COARSE_LOCATION` n’est plus nécessaire.
- Améliorations de la stabilité.

## <a name="400-07062015"></a>4.0.0 (07/06/2015)

-   Modifications de protocole interne pour rendre analytique et push plus fiable.
-   Push native (GCM/ADM) est désormais également utilisé pour dans les notifications d’application afin que vous devez configurer les informations d’identification native push pour n’importe quel type de campagne push.
-   Corriger la notification de vue d’ensemble : il s’agissait de 10 s uniquement affichée après vous appuyez sur.
-   Corriger un bogue dans l’affichage web : en cliquant sur un lien également l’exécution de l’URL de l’action par défaut.
-   Corriger un blocage rare lié à la gestion du stockage local.
-   Corriger la gestion des chaînes configuration dynamique.
-   Mettre à jour CLUF.

## <a name="300-02172015"></a>3.0.0 (17/02/2015)

-   Version initiale d’Engagement Mobile Azure
-   configuration de l’identificateur est remplacée par une configuration de chaîne de connexion.
-   Supprimer l’API pour envoyer et recevoir des messages XMPP arbitraires à partir d’entités XMPP arbitraires.
-   Supprimé API pour envoyer et recevoir des messages entre les périphériques.
-   Améliorations de la sécurité.
-   Suivi de Google Play et SmartAd supprimé.
