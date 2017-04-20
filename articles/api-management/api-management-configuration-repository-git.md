<properties 
    pageTitle="Comment enregistrer et configurer votre configuration du service de gestion de l’API à l’aide de Git" 
    description="Découvrez comment enregistrer et configurer votre configuration du service de gestion de l’API à l’aide de Git." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>


# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Comment enregistrer et configurer votre configuration du service de gestion de l’API à l’aide de Git

>[AZURE.IMPORTANT] Configuration GIT pour la gestion de l’API est en cours d’aperçu. Il est terminée fonctionne, mais est en mode Aperçu avant, car nous cherchons activement des commentaires sur cette fonctionnalité. Il est possible que nous pouvons modifier un saut d’en réponse aux commentaires des clients, nous vous recommandons de pas selon la fonctionnalité à utiliser dans les environnements de production. Si vous avez des commentaires ou des questions, n’hésitez pas à `apimgmt@microsoft.com`.

Chaque instance de service de gestion des API gère une base de données de configuration qui contient des informations sur la configuration et les métadonnées de l’instance de service. Modifications peuvent être apportées à l’instance du service en modifiant un paramètre dans le portail publisher, en utilisant une applet de commande PowerShell ou effectuer un appel API REST. En plus de ces méthodes, vous pouvez également gérer votre configuration de l’instance service à l’aide de Git, l’activation de scénarios de gestion des services tels que :

-   Le contrôle de version configuration - télécharger et stocker plusieurs versions de votre configuration du service
-   En bloc des modifications de configuration - apporter des modifications à plusieurs parties de votre configuration du service dans votre magasin local et intégrer les modifications sur le serveur à une seule opération
-   Toolchain Git familière et flux de travail - utiliser les outils Git et le flux de travail que vous connaissez déjà

Le diagramme suivant montre une vue d’ensemble des différentes façons pour configurer votre instance de service de gestion de l’API.

![Configurer GIT][api-management-git-configure]

Lorsque vous apportez des modifications à votre service à l’aide du portail de publisher, applets de commande PowerShell ou l’API REST, vous gérez votre base de données de configuration de service à l’aide du `https://{name}.management.azure-api.net` point de terminaison, comme illustré sur le côté droit du diagramme. Le côté gauche du diagramme illustre comment vous pouvez gérer votre configuration du service à l’aide de Git et référentiel Git pour votre service situé à `https://{name}.scm.azure-api.net`.

Les étapes suivantes fournissent une vue d’ensemble de la gestion de votre instance de service de gestion de l’API à l’aide de Git.

1.  Activer l’accès Git dans votre service
2.  Enregistrer votre base de données de configuration de service dans votre référentiel Git
3.  Cloner le mis en pension Git sur votre ordinateur local
4.  Tirez la dernière mis en pension jusqu'à votre ordinateur local et valider et push des modifications apportées à votre mis en pension
5.  Déployer les modifications à partir de votre mis en pension dans votre base de données de configuration de service

Cet article décrit comment activer et utiliser Git pour gérer la configuration du service et fournit une référence pour les fichiers et dossiers dans le référentiel Git.

## <a name="to-enable-git-access"></a>Pour activer l’accès Git

Vous pouvez rapidement afficher l’état de votre configuration Git en affichant l’icône Git dans le coin supérieur droit du portail de publisher. Dans cet exemple, access Git n'a pas encore été activé.

![État GIT][api-management-git-icon-enable]

Pour afficher et configurer vos paramètres de configuration Git, vous pouvez cliquer sur l’icône Git, ou cliquez sur le menu de **sécurité** et accédez à l’onglet **référentiel de Configuration** .

![Activer GIT][api-management-enable-git]

Pour activer l’accès Git, cochez la case **Activer Git access** .

Après quelques instants, la modification est enregistrée et un message de confirmation s’affiche. Notez que Git icône a changé de couleur pour indiquer qu’access Git est activée et le message d’état indique maintenant qui y sont des modifications non enregistrées dans le référentiel. C’est parce que la base de données de configuration de service Gestion des API n’a pas encore été enregistré dans le référentiel.

![GIT activé][api-management-git-enabled]

>[AZURE.IMPORTANT] Aucun secret qui n’est pas définies comme propriétés seront stockées dans le référentiel et resteront dans son historique jusqu'à ce que vous désactiver et réactiver Git access. Les propriétés fournissent un emplacement sécurisé pour gérer les valeurs de chaîne constante, y compris les données confidentielles, toutes les stratégies et configuration de l’API, vous n’aurez à les stocker directement dans les instructions de stratégie. Pour plus d’informations, voir [comment utiliser les propriétés de stratégies de gestion des API Azure](api-management-howto-properties.md).

Pour plus d’informations sur l’activation ou désactivation de l’accès Git à l’aide de l’API REST, voir [Activer ou désactiver l’accès Git à l’aide de l’API REST](https://msdn.microsoft.com/library/dn781420.aspx#EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Pour enregistrer la configuration du service dans le référentiel Git

La première étape avant clonant le référentiel consiste à enregistrer l’état actuel de la configuration du service dans le référentiel. Cliquez sur **Enregistrer la configuration vers le référentiel**.

![Enregistrer la configuration][api-management-save-configuration]

Apportez les modifications souhaitées dans l’écran de confirmation, cliquez sur **Ok** pour enregistrer.

![Enregistrer la configuration][api-management-save-configuration-confirm]

Après quelques instants la configuration est enregistrée, et l’état de la configuration du référentiel s’affiche, y compris la date et l’heure de dernière modification de la configuration et la dernière synchronisation entre la configuration du service et le référentiel.

![État de la configuration][api-management-configuration-status]

Une fois que la configuration est enregistrée dans le référentiel, il peut cloner.

Pour plus d’informations sur l’exécution de cette opération à l’aide de l’API REST, voir [Valider instantané de la configuration à l’aide de l’API REST](https://msdn.microsoft.com/library/dn781420.aspx#CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Pour dupliquer le référentiel sur votre ordinateur local

Pour dupliquer un référentiel, vous devez l’URL de votre référentiel, un nom d’utilisateur et un mot de passe. Le nom d’utilisateur et l’URL sont affichent dans la partie supérieure de l’onglet **référentiel de Configuration** .

![Cloner GIT][api-management-configuration-git-clone]

Le mot de passe est généré en bas de l’onglet **référentiel de Configuration** .

![Générer un mot de passe][api-management-generate-password]

Pour générer un mot de passe, tout d’abord vous assurer que l' **expiration** est définie sur la date d’expiration de votre choix et l’heure, puis cliquez sur **Générer des jetons**.

![Mot de passe][api-management-password]

>[AZURE.IMPORTANT] Prenez note de ce mot de passe. Une fois que vous quittez cette page le mot de passe ne s’affiche plus.

Les exemples suivants utilisent l’outil Git Bash [Git pour](http://www.git-scm.com/downloads) Windows, mais vous pouvez utiliser n’importe quel outil Git que vous connaissez.

Ouvrez votre outil Git dans le dossier souhaité et exécutez la commande suivante pour cloner le référentiel git sur votre ordinateur local, à l’aide de la commande fournie par le portail de publisher.

    git clone https://bugbashdev4.scm.azure-api.net/ 

Indiquez le nom d’utilisateur et mot de passe lorsque vous y êtes invité.

Si vous rencontrez des erreurs, essayez de modifier votre `git clone` commande pour inclure le nom d’utilisateur et mot de passe, comme illustré dans l’exemple suivant.

    git clone https://username:password@bugbashdev4.scm.azure-api.net/

Si cela fournit une erreur, essayez la partie de votre mot de passe de la commande de codage d’URL. Une méthode rapide pour effectuer cette action consiste à ouvrir Visual Studio, exécutez la commande suivante dans la **Fenêtre exécution**. Pour ouvrir la **Fenêtre exécution**, ouvrez une solution ou un projet dans Visual Studio (ou créez une nouvelle application console vide), puis sélectionnez **Windows**, **exécution** dans le menu **Déboguer** .

    ?System.NetWebUtility.UrlEncode("password from publisher portal")

Utilisez le mot de passe codé en même temps que votre emplacement de nom et le référentiel de l’utilisateur pour élaborer la commande git.

    git clone https://username:url encoded password@bugbashdev4.scm.azure-api.net/

Une fois le référentiel est cloner vous pouvez afficher et utiliser celles-ci dans votre système de fichiers local. Pour plus d’informations, voir [informations de référence structure fichier et un dossier de référentiel Git local](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Mettre à jour votre référentiel local avec la configuration de l’instance service plus récente

Si vous apportez des modifications à votre instance de service de gestion de l’API dans le portail publisher ou à l’aide de l’API REST, vous devez enregistrer ces modifications dans le référentiel avant que vous pouvez mettre à jour votre référentiel local avec les dernières modifications. Pour ce faire, cliquez sur **Enregistrer la configuration vers le référentiel** sous l’onglet **référentiel de Configuration** dans le portail publisher et puis exécutez la commande suivante dans votre magasin local.

    git pull

Avant d’exécuter `git pull` Vérifiez que vous êtes dans le dossier pour votre référentiel local. Si vous venez d’effectuer la `git clone` de commande, puis vous devez modifier le répertoire à votre mis en pension en exécutant une commande similaire à ce qui suit.

    cd bugbashdev4.scm.azure-api.net/

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Distribuer des modifications à partir de votre mis en pension local sur la mis en pension server

Pour envoyer les modifications à partir de votre référentiel local au référentiel du serveur, vous devez valider vos modifications et les pousser puis vers le référentiel du serveur. Pour valider les modifications, ouvrez votre outil de commande Git, passer à l’annuaire de votre référentiel local et lancez les commandes suivantes.

    git add --all
    git commit -m "Description of your changes"

Pour toutes les validations transmission sur le serveur, exécutez la commande suivante.

    git push

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Pour déployer des modifications de configuration de service à l’instance de service de gestion de l’API

Une fois vos modifications locales sont validées et envoyées au référentiel du serveur, vous pouvez les déployer sur votre instance de service de gestion de l’API.

![Déployer][api-management-configuration-deploy]

Pour plus d’informations sur l’exécution de cette opération à l’aide de l’API REST, voir [Git déployer des modifications à la base de données de configuration à l’aide de l’API REST](https://msdn.microsoft.com/library/dn781420.aspx#DeployChanges).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Fichiers et dossiers référence de structure de référentiel Git local

Les fichiers et dossiers dans le référentiel local git contiennent les informations de configuration de l’instance du service.

| Élément                       | Description                                                                                |
|-------------------------   |--------------------------------------------------------------------------------------------|
| dossier de gestion des api racine | Contient la configuration de niveau supérieur pour l’instance de service                                  |
| dossier API                | Contient la configuration de l’API dans l’instance de service                            |
| dossier de groupes              | Contient la configuration pour les groupes dans l’instance de service                          |
| dossier stratégies            | Contient les stratégies dans l’instance de service                                              |
| dossier portalStyles        | Contient la configuration pour les personnalisations portail développeur dans l’instance de service |
| dossier Products            | Contient la configuration des produits dans l’instance de service                        |
| dossier Templates           | Contient la configuration pour les modèles de courrier électronique dans l’instance de service                 |

Chaque dossier peut contenir un ou plusieurs fichiers, puis dans certains cas, un ou plusieurs dossiers, par exemple un dossier pour chaque API, un produit ou un groupe. Les fichiers au sein de chaque dossier sont spécifiques pour le type d’entité décrit par le nom du dossier.

| Type de fichier | Objectif                                                                |
|-----------|------------------------------------------------------------------------|
| JSON      | Informations de configuration de l’entité correspondante                  |
| HTML      | Descriptions sur l’entité, souvent affichée dans le portail de développement |
| XML       | Instructions de stratégie                                                      |
| CSS       | Feuilles de style pour la personnalisation du portail pour les développeurs                        |

Ces fichiers pouvant être créés, supprimés, modifiés et gérés dans votre système de fichiers local, et les modifications déployées sur la votre instance de service de gestion de l’API.

>[AZURE.NOTE] Les entités suivantes ne figurent pas dans le référentiel Git et ne peuvent pas être configurées à l’aide de Git.
>
>-    Utilisateurs
>-    Abonnements
>-    Propriétés
>-    Entités portails développeur différent de styles

### <a name="root-api-management-folder"></a>Dossier de gestion des api racine

La racine `api-management` dossier contient un `configuration.json` fichier qui contient des informations de niveau supérieur sur l’instance du service dans le format suivant.

    {
      "settings": {
        "RegistrationEnabled": "True",
        "UserRegistrationTerms": null,
        "UserRegistrationTermsEnabled": "False",
        "UserRegistrationTermsConsentRequired": "False",
        "DelegationEnabled": "False",
        "DelegationUrl": "",
        "DelegatedSubscriptionEnabled": "False",
        "DelegationValidationKey": ""
      },
      "$ref-policy": "api-management/policies/global.xml"
    }

Les quatre premiers paramètres (`RegistrationEnabled`, `UserRegistrationTerms`, `UserRegistrationTermsEnabled`, et `UserRegistrationTermsConsentRequired`) carte pour les paramètres suivants sous l’onglet **identités** dans la section **sécurité** .

| Paramètre d’identité                     | Mappe vers                                               |
|--------------------------------------|-------------------------------------------------------|
| RegistrationEnabled                  | **Rediriger les utilisateurs anonymes à la page de connexion à** la case à cocher |
| UserRegistrationTerms                | Zone de texte de **conditions d’utilisation sur l’inscription utilisateur**               |
| UserRegistrationTermsEnabled         | Case à cocher **Afficher les conditions d’utilisation sur la page d’abonnement**         |
| UserRegistrationTermsConsentRequired | Case à cocher **Exiger consentement**                          |

![Paramètres d’identité][api-management-identity-settings]

Les quatre paramètres (`DelegationEnabled`, `DelegationUrl`, `DelegatedSubscriptionEnabled`, et `DelegationValidationKey`) carte pour les paramètres suivants sous l’onglet **délégation** dans la section **sécurité** .

| Paramètres de délégation           | Mappe vers                                    |
|------------------------------|--------------------------------------------|
| DelegationEnabled            | Case à cocher **délégué se connecter et d’abonnement**    |
| DelegationUrl                | Zone de texte **URL de point de terminaison de délégation**        |
| DelegatedSubscriptionEnabled | Case à cocher **abonnement délégué** |
| DelegationValidationKey      | Zone de texte **Clé de Validation de délégué**        |

![Paramètres de la délégation][api-management-delegation-settings]

Le paramètre final, `$ref-policy`, mappe vers le fichier de déclarations de stratégie globale pour l’instance du service.

### <a name="apis-folder"></a>dossier API

La `apis` dossier contient un dossier pour chaque API dans l’instance de service qui contient les éléments suivants.

-   `apis\<api name>\configuration.json`-Il s’agit de la configuration de l’API et contient des informations sur l’URL du service principal et les opérations. Voici les mêmes informations devant être retournées si vous deviez appeler [obtenir une API spécifique](https://msdn.microsoft.com/library/azure/dn781423.aspx#GetAPI) avec `export=true` dans `application/json` format.
-   `apis\<api name>\api.description.html`-Il s’agit de la description de l’API et correspond à la `description` propriété de l' [entité de l’API](https://msdn.microsoft.com/library/azure/dn781423.aspx#EntityProperties).
-   `apis\<api name>\operations\`-ce dossier contient `<operation name>.description.html` fichiers établir une correspondance entre les opérations de l’API. Chaque fichier contient la description d’une seule opération dans l’API qui correspond à la `description` propriété de l' [entité de l’opération](https://msdn.microsoft.com/library/azure/dn781423.aspx#OperationProperties) dans l’API REST.

### <a name="groups-folder"></a>dossier de groupes

La `groups` dossier contient un dossier pour chaque groupe défini dans l’instance de service.

-   `groups\<group name>\configuration.json`-Il s’agit de la configuration du groupe. Voici les mêmes informations devant être retournées si vous deviez appeler l’opération [obtenir un groupe spécifique](https://msdn.microsoft.com/library/azure/dn776329.aspx#GetGroup) .
-   `groups\<group name>\description.html`-Il s’agit de la description du groupe et correspond à la `description` propriété de l' [entité du groupe](https://msdn.microsoft.com/library/azure/dn776329.aspx#EntityProperties).

### <a name="policies-folder"></a>dossier stratégies

La `policies` dossier contient les instructions de stratégie de l’instance du service.

-   `policies\global.xml`-contient les stratégies définies avec une portée globale pour votre instance de service.
-   `policies\apis\<api name>\`-Si vous avez les stratégies définies pour l’étendue de l’API, ils sont contenus dans ce dossier.
-   `policies\apis\<api name>\<operation name>\`dossier - si vous avez des stratégies définies avec une portée d’opération, ils sont contenus dans ce dossier dans `<operation name>.xml` fichiers établir une correspondance entre les instructions de stratégie pour chaque opération.
-   `policies\products\`-Si vous avez les stratégies définies pour l’étendue du produit, ils sont contenus dans ce dossier, qui contient `<product name>.xml` fichiers établir une correspondance entre les instructions de stratégie pour chaque produit.

### <a name="portalstyles-folder"></a>dossier portalStyles

La `portalStyles` dossier contient des feuilles de style et de configuration pour les personnalisations portail développeur pour l’instance du service.

-   `portalStyles\configuration.json`-contient les noms des feuilles de style utilisées par le portail de développement
-   `portalStyles\<style name>.css`-chaque `<style name>.css` fichier contient des styles dans le portail de développement (`Preview.css` et `Production.css` par défaut).

### <a name="products-folder"></a>dossier Products

La `products` dossier contient un dossier pour chaque produit défini dans l’instance de service.

-   `products\<product name>\configuration.json`-Il s’agit de la configuration du produit. Voici les mêmes informations devant être retournées si vous deviez appeler l’opération [obtenir un produit spécifique](https://msdn.microsoft.com/library/azure/dn776336.aspx#GetProduct) .
-   `products\<product name>\product.description.html`-Il s’agit de la description du produit et correspond à la `description` propriété de l' [entité de produit](https://msdn.microsoft.com/library/azure/dn776336.aspx#Product) dans l’API REST.

### <a name="templates"></a>modèles

La `templates` dossier contient la configuration pour les [modèles de courrier électronique](api-management-howto-configure-notifications.md) de l’instance du service.

-   `<template name>\configuration.json`-Il s’agit de la configuration pour le modèle de courrier électronique.
-   `<template name>\body.html`-Il s’agit du corps du modèle de courrier électronique.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les autres façons de gérer votre instance de service, voir :

-   Gérer votre instance de service en utilisant les applets de commande PowerShell
    -   [Déploiement du service de référence d’applet de commande PowerShell](https://msdn.microsoft.com/library/azure/mt619282.aspx)
    -   [Gestion des services de référence applet de commande PowerShell](https://msdn.microsoft.com/library/azure/mt613507.aspx)
-   Gérer votre instance de service dans le portail de publisher
    -   [Gérer votre première API](api-management-get-started.md)
-   Gérer votre instance de service à l’aide de l’API REST
    -   [Référence de l’API REST de gestion de l’API](https://msdn.microsoft.com/library/azure/dn776326.aspx)

## <a name="watch-a-video-overview"></a>Regarder une vidéo de présentation

> [AZURE.VIDEO configuration-over-git]

[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png




