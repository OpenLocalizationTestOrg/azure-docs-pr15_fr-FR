<properties
    pageTitle="Migrer à partir de Services mobiles à une application Mobile Application Service"
    description="Découvrez comment migrer facilement votre application de Services mobiles pour une application application Service Mobile"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="adrianha"/>

# <a name="article-top"></a>Migrer votre Service Mobile Azure existants vers Azure Application Service

Avec la [disponibilité générale du Service d’application Azure], sites Azure Mobile Services peuvent être migrées facilement sur place pour tirer parti de toutes les fonctionnalités du Service application Azure.  Ce document explique ce qui se passe lors de la migration de votre site à partir de Services mobiles Azure Azure application service.

## <a name="what-does-migration-do"></a>Que faire par le migration à votre site

Migration de votre Service Mobile Azure Active votre Service Mobile dans une application de [Service d’application Azure] sans affecter le code.  Votre Notification Hubs SQL connexion de données, paramètres d’authentification, les tâches planifiées et nom de domaine restent ne change pas.  Les clients mobiles à l’aide de votre Service Mobile Azure continuent à fonctionner normalement.  Migration redémarre votre service une fois qu’il est transféré vers Azure Application Service.

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>Pourquoi vous devez migrer votre site

Microsoft recommande que vous migrez votre Service Mobile Azure pour tirer parti des fonctionnalités de Service d’application Azure, notamment :

  *  Nouvelles fonctionnalités hôte, notamment les [noms de domaine personnalisé]et [WebJobs] .
  *  Connectivité à vos ressources locales à l’aide de [VNet] en plus de [Connexions hybride].
  *  Surveillance et résolution des problèmes avec Relic nouveau ou [Application perspectives].
  *  Outils DevOps intégrée, y compris les [emplacements de mise en attente], retour arrière et le test d’en production.
  *  [Ajuster automatiquement], l’équilibrage de charge et [analyse des performances].

Pour plus d’informations sur les avantages du Service d’application Azure, consultez la rubrique [Mobile Services par rapport à l’application de Service] .

## <a name="before-you-begin"></a>Avant de commencer

Avant de commencer votre travail principal sur votre site, vous devez [sauvegarder votre Service Mobile] scripts et base de données SQL.

## <a name="migrating-site"></a>Migrer vos sites

Le processus de migration migre tous les sites au sein d’une région Azure unique.

Pour migrer votre site :

  1.  Connectez-vous au [portail classique Azure].
  2.  Sélectionnez un Service Mobile dans la région à migrer.
  3.  Cliquez sur le bouton **migrer au Service d’application** .

    ![Le bouton migrer][0]

  4.  Lisez le migrer vers la boîte de dialogue Service d’application.
  5.  Entrez le nom de votre Service Mobile dans la zone prévue à cet effet.  Par exemple, si votre nom de domaine est contoso.azure mobile.net, puis entrez _contoso_ dans la zone prévue à cet effet.
  6.  Cliquez sur le bouton coche.

Surveiller l’état de la migration du moniteur d’activité. Votre site est répertorié en tant que la *migration* dans le portail classique Azure.

  ![Moniteur d’activité de migration][1]

Chaque migration peut prendre de 3 à 15 minutes par service mobile en cours de migration.  Votre site reste disponible lors de la migration.
Redémarrage de votre site à la fin du processus de migration.  Le site n’est pas disponible lors du redémarrage, qui peuvent durer quelques secondes.

## <a name="finalizing-migration"></a>Finalisation de la Migration

Pensez à tester votre site à partir d’un client mobile à la fin du processus de migration.  Vérifiez que vous pouvez effectuer toutes les actions client courantes sans modifications sur le client mobile.  

### <a name="update-app-service-tier"></a>Sélectionnez un Service d’application approprié tarifs niveau

Vous avez plus de flexibilité dans tarifs après la migration vers Azure Application Service.

  1.  Connectez-vous au [portail Azure].
  2.  Sélectionnez **toutes les ressources** ou les **Services d’application** , puis cliquez sur le nom de votre Service Mobile a été déplacée.
  3.  La carte de paramètres s’ouvre par défaut.
  4.  Dans le menu Paramètres, cliquez sur **Plan de Service d’application** .
  5.  Cliquez sur la vignette de **Niveau de prix** .
  6.  Cliquez sur la vignette adaptée à vos besoins, puis cliquez sur **Sélectionner**.  Vous devrez peut-être cliquez sur **Afficher tout** pour voir les tarifs disponibles niveaux.

Comme point de départ, nous vous recommandons des niveaux suivants :

| Niveau de prix de Service mobile | Niveau de tarifs de Service d’application |
| :-------------------------- | :----------------------- |
| Gratuit                        | F1 gratuit                  |
| Base                       | Basic B1                 |
| Standard                    | S1 Standard              |

Il existe une flexibilité considérable dans choisir le bon tarifs couche pour votre application.  Pour plus d’informations sur les tarifs de votre nouveau Service d’application, voir [Application Service tarifs] .

> [AZURE.TIP]La couche application Service Standard contient l’accès à de nombreuses fonctionnalités que vous souhaitiez utiliser, y compris les [emplacements de mise en attente], les sauvegardes automatiques et mise à l’échelle.  Découvrez les nouvelles fonctionnalités lorsque vous y !

### <a name="review-migration-scheduler-jobs"></a>Passez en revue les tâches du planificateur a été déplacée

Tâches du planificateur ne sera pas visibles jusqu'à 30 minutes après la migration.  Tâches planifiées continuent à s’exécuter en arrière-plan.
Pour afficher vos tâches planifiées une fois qu’ils sont visibles :

  1.  Connectez-vous au [portail Azure].
  2.  Sélectionnez **Parcourir >**, entrez **planification** dans la zone de _filtre_ , puis sélectionnez **Collections planificateur**.

Il existe un nombre limité de planificateur gratuit travaux disponibles après la migration.  Passez en revue votre activité et les [Plans de planificateur Azure].

### <a name="configure-cors"></a>Configurer CORS si nécessaire

Partage des ressources d’origine croisée est une technique pour permettre à un site Web accéder à une API Web sur un autre domaine.  Si vous utilisez les Services mobiles Azure avec un site Web associé, vous devez configurer CORS dans le cadre de la migration.  Si vous accédez à Azure Mobile Services en mode exclusif à partir d’appareils mobiles, CORS n’a pas besoin doit être configuré à l’exception dans certains cas rares.

Vos paramètres CORS migrés sont disponibles en tant que le paramètre d’application **MS_CrossDomainWhitelist** .  Pour migrer votre site vers la facilité d’application Service CORS :

  1.  Connectez-vous au [portail Azure].
  2.  Sélectionnez **toutes les ressources** ou les **Services d’application** , puis cliquez sur le nom de votre Service Mobile a été déplacée.
  3.  La carte de paramètres s’ouvre par défaut.
  4.  Cliquez sur **CORS** dans le menu de l’API.
  5.  Entrez les origines autorisés dans la zone fournie, en appuyant sur ENTRÉE après chacune d’elles.
  6.  Une fois que votre liste des origines autorisée est correcte, cliquez sur le bouton Enregistrer.

> [AZURE.TIP]Un des avantages de l’utilisation d’un Service d’application Azure est que vous pouvez exécuter votre site web et le service mobile sur le même site.  Pour plus d’informations, consultez la section [étapes suivantes](#next-steps) .

### <a name="download-publish-profile"></a>Télécharger un nouveau profil de publication

Le profil de publication de votre site est modifié lors de la migration vers Azure Application Service.  Si vous souhaitez publier votre site à partir de Visual Studio, vous avez besoin d’un nouveau profil de publication.  Pour télécharger le nouveau profil de publication :

  1.  Connectez-vous au [portail Azure].
  2.  Sélectionnez **toutes les ressources** ou les **Services d’application** , puis cliquez sur le nom de votre Service Mobile a été déplacée.
  3.  Cliquez sur **obtenir publier le profil**.

Le fichier PublishSettings est téléchargé sur votre ordinateur.  Il est appelé normalement _nom du site_. PublishSettings.  Importer les paramètres de publication dans votre projet existant :

  1.  Ouvrez votre projet de Service Mobile Azure et Visual Studio.
  2.  Avec le bouton droit de votre projet dans l' **Explorateur de solutions** et sélectionnez **publier...**
  3.  Cliquez sur **Importer**
  4.  Cliquez sur **Parcourir** , puis sélectionnez votre téléchargé publier le fichier de configuration.  Cliquez sur **OK**
  5.  Cliquez sur **Valider la connexion** pour vous assurer que le travail de paramètres de publication.
  6.  Cliquez sur **Publier** pour publier votre site.


## <a name="working-with-your-site"></a>Utilisation de votre postérieures à la migration de site

Commencer à utiliser votre nouveau Service d’application dans la [portail Azure] postérieures à la migration.  Voici quelques remarques sur les opérations spécifiques qui vous permettent d’opérer dans le [Portail classique Azure], ainsi que leur équivalent de Service d’application.

### <a name="publishing-your-site"></a>Téléchargement et publication de votre site a été déplacée

Votre site est disponible via git ou ftp et peut être publiées à nouveau avec différents mécanismes différents, y compris WebDeploy, TFS, mercurienne, GitHub et FTP.  Les informations d’identification de déploiement sont migrées avec le reste de votre site.  Si vous n’avez pas défini vos informations d’identification du déploiement ou vous ne vous souvenez pas les, vous pouvez réinitialiser les :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **toutes les ressources** ou les **Services d’application** , puis cliquez sur le nom de votre Service Mobile a été déplacée.
  3. La carte de paramètres s’ouvre par défaut.
  4. Cliquez sur les **informations d’identification de déploiement** de la publication de menu.
  5. Entrez les nouvelles informations d’identification de déploiement dans les zones appropriées, puis cliquez sur le bouton Enregistrer.

Vous pouvez utiliser ces informations d’identification pour cloner le site avec git ou configurer des déploiements automatisés de GitHub, TFS ou mercurienne.  Pour plus d’informations, consultez la [documentation de déploiement d’Azure Application Service].

### <a name="appsettings"></a>Paramètres de l’application

La plupart des paramètres d’un service mobile migré sont disponibles via les paramètres de l’application.  Vous pouvez obtenir une liste des paramètres de l’application à partir du [portail Azure].
Pour afficher ou modifier vos paramètres d’application :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **toutes les ressources** ou les **Services d’application** , puis cliquez sur le nom de votre Service Mobile a été déplacée.
  3. La carte de paramètres s’ouvre par défaut.
  4. Cliquez sur **paramètres de l’Application** dans le menu général.
  5. Faites défiler jusqu'à la section Paramètres de l’application et recherchez votre paramètre d’application.
  6. Cliquez sur la valeur du paramètre application pour modifier la valeur.  Cliquez sur **Enregistrer** pour enregistrer la valeur.

Vous pouvez mettre à jour plusieurs paramètres de l’application en même temps.

> [AZURE.TIP]Il existe deux paramètres de l’Application avec la même valeur.  Par exemple, vous pouvez voir _ApplicationKey_ et _MS\_ApplicationKey_.  Mettre à jour les paramètres de l’application en même temps.

### <a name="authentication"></a>Authentification

Tous les paramètres d’authentification sont disponibles en tant que paramètres de l’application de votre site a été déplacée.  Pour mettre à jour vos paramètres d’authentification, vous devez modifier les paramètres de l’application appropriée.  Le tableau suivant indique les paramètres de l’application appropriée pour votre fournisseur d’authentification :

| Fournisseur          | ID de client                 | Secret client                | Autres paramètres             |
| :---------------- | :------------------------ | :--------------------------- | :------------------------- |
| Compte Microsoft | **MS\_MicrosoftClientID**  | **MS\_MicrosoftClientSecret** | **MS\_MicrosoftPackageSID** |
| Facebook          | **MS\_FacebookAppID**      | **MS\_FacebookAppSecret**     |                            |
| Twitter           | **MS\_TwitterConsumerKey** | **MS\_TwitterConsumerSecret** |                            |
| Google            | **MS\_GoogleClientID**     | **MS\_GoogleClientSecret**    |                            |
| Azure AD          | **MS\_AadClientID**        |                              | **MS\_AadTenants**          |

Remarque : **MS\_AadTenants** est stocké comme une liste de domaines client (les champs « Clients autorisés » dans le portail de Services mobiles) séparées par des virgules.

> [AZURE.WARNING] **N’utilisez pas les mécanismes d’authentification dans le menu Paramètres**
>
> Service application Azure fournit un système de l’authentification et l’autorisation de « sans code » distinct sous la _authentification / autorisation_ menu Paramètres et l’option _D’authentification Mobile_ (déconseillée) sous le menu Paramètres.  Ces options sont incompatibles avec un Service Mobile Azure migrés.  Vous pouvez [mettre à niveau votre site](app-service-mobile-net-upgrading-from-mobile-services.md) pour tirer parti de l’authentification Azure Application Service.

### <a name="easytables"></a>Données

L’onglet _données_ de Services mobiles a été remplacé par les _Tables facile_ au sein du portail Azure.  Pour accéder aux Tables faciles :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **toutes les ressources** ou les **Services d’application** , puis cliquez sur le nom de votre Service Mobile a été déplacée.
  3. La carte de paramètres s’ouvre par défaut.
  4. Cliquez sur **tables faciles** dans le menu MOBILE.

Vous pouvez ajouter une table en cliquant sur le bouton **Ajouter** ou accéder à vos tableaux existante en cliquant sur un nom de table.  Il existe différentes opérations que vous pouvez faire à partir de cette carte, y compris :

* Modification des autorisations de table
* Modification des scripts d’exploitation
* Gérer le schéma de table
* Suppression de la table
* La désactivation des table des matières
* Suppression des lignes de la table

### <a name="easyapis"></a>API

L’onglet _API_ de Services mobiles a été remplacé par _API facile_ au sein du portail Azure.  Pour accéder aux API facile :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **toutes les ressources** ou les **Services d’application** , puis cliquez sur le nom de votre Service Mobile a été déplacée.
  3. La carte de paramètres s’ouvre par défaut.
  4. Cliquez sur **API facile** dans le menu MOBILE.

Votre API migrés figurent déjà dans la carte.  Vous pouvez également ajouter une API à partir de cette carte.  Pour gérer une API spécifique, cliquez sur l’API.
À partir de la nouvelle carte, vous pouvez modifier les autorisations et modifier les scripts de l’API.

### <a name="on-demand-jobs"></a>Tâches du planificateur

Toutes les tâches du planificateur sont disponibles via la section Collections travail planificateur.  Pour accéder à vos tâches :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **Parcourir >**, entrez **planification** dans la zone de _filtre_ , puis sélectionnez **Collections planificateur**.
  3. Sélectionnez l’ensemble de travail de votre site.  Il est nommé _nom du site_-tâches.
  4. Cliquez sur **paramètres**.
  5. Sous gérer, cliquez sur **tâches** .

Tâches planifiées sont répertoriés avec la fréquence que vous avez spécifié avant la migration.  Tâches à la demande sont désactivées.  Pour exécuter une tâche à la demande :

  1. Sélectionnez la tâche que vous souhaitez exécuter.
  2. Si nécessaire, cliquez sur **Activer** pour activer la tâche.
  3. Cliquez sur **paramètres**, puis sur **planification**.
  4. Sélectionnez une périodicité **d’une seule fois**, puis cliquez sur **Enregistrer**

Vos tâches à la demande se trouvent dans `App_Data/config/scripts/scheduler post-migration`.  Nous vous conseillons de convertir toutes les tâches à la demande à [WebJobs] ou des [fonctions].  Écrire des nouvelles tâches du planificateur en tant que [WebJobs] ou [fonctions].

### <a name="notification-hubs"></a>Notification Hubs

Services mobiles utilise Hubs de Notification pour les notifications de transmission.  Les paramètres d’application suivants sont utilisées pour lier le Hub de Notification à votre Service Mobile après la migration :

| Paramètre d’application                    | Description                              |
| :------------------------------------- | :--------------------------------------- |
| **MS\_PushEntityNamespace**             | Le Namespace concentrateur de Notification           |
| **MS\_NotificationHubName**             | Le nom de concentrateur de Notification                |
| **MS\_NotificationHubConnectionString** | La chaîne de connexion de concentrateur de Notification   |
| **MS\_NomEspaceNoms**                   | Un alias pour MS_PushEntityNamespace      |

Votre concentrateur de Notification est géré tout au long du [portail Azure].  Notez le nom du concentrateur de Notification (que vous pouvez trouver cette en utilisant les paramètres d’application) :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **Parcourir**>, puis sélectionnez **Hubs de Notification**
  3. Cliquez sur le nom de concentrateur de Notification associé au service mobile.

> [AZURE.NOTE]Si votre concentrateur de Notification est de type « Mixte », il n’est pas visible.  « Mixte » tapez notification hubs utilisent Hubs de Notification et fonctionnalités ESB héritées.  [Convertir vos espaces de noms mixte] avant de poursuivre.  Une fois que la conversion est terminée, votre concentrateur de notification s’affiche dans le [portail Azure].

Pour plus d’informations, consultez la documentation de [Notification Hubs] .

> [AZURE.TIP]Fonctionnalités de gestion de Hubs de notification dans le [portail Azure] sont toujours en mode Aperçu avant.  Le [Portail classique Azure] restent disponibles pour la gestion de tous vos Hubs de Notification.

### <a name="legacy-push"></a>Paramètres de Push hérités

Si vous avez configuré Push sur votre service mobile avant l’introduction sur centraux de Notification, vous utilisez _push héritée_.  Si vous utilisez Push et un concentrateur de Notification répertoriés dans votre configuration n’est pas visible, il est probable que vous utilisez _push héritée_.  Cette fonctionnalité est migrée avec toutes les autres fonctionnalités.  Toutefois, nous vous conseillons de changer aux Hubs Notification disponible une fois la migration terminée.

En attendant, tous les paramètres hérités push (à l’exception du certificat APNS remarquable) sont disponibles dans les paramètres de l’application.  Mettre à jour le certificat APNS en remplaçant le fichier approprié dans le système de fichiers.

### <a name="app-settings"></a>Autres paramètres de l’application

Les paramètres d’application supplémentaires suivants sont migrés à partir de votre Service Mobile et disponible sous *paramètres* > *Paramètres de l’application*:

| Paramètre d’application              | Description                             |
| :------------------------------- | :-------------------------------------- |
| **MS\_MobileServiceName**         | Le nom de votre application                    |
| **MS\_MobileServiceDomainSuffix** | Le préfixe du domaine. ex : Azure mobile.net |
| **MS\_ApplicationKey**            | Votre clé d’application                    |
| **MS\_clé principale.**                 | Votre clé de masque d’application                     |

La touche application et la clé principale sont identiques aux touches de l’Application à partir de votre Service Mobile d’origine.  En particulier, la touche Application est envoyée par les clients mobiles pour valider leur utilisation de l’API mobile.

### <a name="cliequivalents"></a>Équivalents de ligne de commande

Vous pouvez plus utiliser la commande _azure mobile_ pour gérer votre site de Services mobiles Azure.  En revanche, de nombreuses fonctions ont été remplacées par la commande _site azure_ .  Utiliser le tableau suivant pour rechercher des équivalents de commandes courantes :

| _Mobile Azure_ Commande                     | Commande _Site Azure_ équivalente            |
| :----------------------------------------- | :----------------------------------------- |
| emplacements mobiles                           | liste de site                         |
| liste mobile                                | liste des sites                                  |
| Afficher mobile _nom_                         | site afficher _nom_                           |
| redémarrer mobile _nom_                      | _nom_ du redémarrage de site                        |
| Redéploiement mobile _nom_                     | site déploiement redéploiement _commitId_ _nom_ |
| _nom du_ _type de_ _valeur_ la valeur clé mobile       | _nom_ du site appsetting supprimer _clé_ <br/> site appsetting ajouter _clé_=de la_valeur_ _nom_ |
| _nom_ de la liste configuration mobile                  | _nom_ de la liste appsetting site                |
| configuration de la mobile obtenir _nom_ _clé_             | _nom_ du site appsetting afficher _clé_          |
| une _clé_ de _nom_ du jeu de configuration mobile             | _nom_ du site appsetting supprimer _clé_ <br/> site appsetting ajouter _clé_=de la_valeur_ _nom_ |
| _nom_ de la liste domaine mobile                  | _nom_ de la liste domaine site                    |
| Ajouter un domaine mobile _nom_ du _domaine_          | Ajouter un domaine de site du _nom de_ _domaine_            |
| supprimer _nom_ du domaine mobile                | site domaine supprimer du _domaine_ _nom_         |
| échelle mobile afficher _nom_                   | site afficher _nom_                           |
| modification du _nom_ d’échelle mobile                 | _nom_ du site échelle mode _mode_ <br /> site échelle instances _instances_ _nom_ |
| _nom_ de la liste appsetting mobile              | _nom_ de la liste appsetting site                |
| Mobile appsetting ajouter _nom_ _clé_ _valeur_ | site appsetting ajouter _clé_=de la_valeur_ _nom_   |
| _nom_ _clé_ appsetting mobile SUPPR      | _nom_ du site appsetting supprimer _clé_        |
| Mobile appsetting afficher _nom_ _clé_        | _nom_ du site appsetting supprimer _clé_        |

Mettre à jour d’authentification ou les paramètres de notification push en mettant à jour le paramètre d’Application appropriée.
Modifier des fichiers et publier votre site via ftp ou git.

### <a name="diagnostics"></a>Diagnostics et journalisation

Journalisation des diagnostics est désactivée normalement dans un Service d’application Azure.  Pour activer la journalisation des diagnostics :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **toutes les ressources** ou les **Services d’application** , puis cliquez sur le nom de votre Service Mobile a été déplacée.
  3. La carte de paramètres s’ouvre par défaut.
  4. Sélectionnez **Les journaux de Diagnostic** sous le menu de fonctionnalités.
  5. Cliquez **sur** pour les fichiers journaux suivants : **L’Application enregistre (système de fichiers)**, **messages d’erreur détaillés**et le **traçage de demande a échoué**
  6. Cliquez sur **Système de fichiers** pour l’enregistrement de serveur Web
  7. Cliquez sur **Enregistrer**

Pour afficher les journaux :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **toutes les ressources** ou les **Services d’application** , puis cliquez sur le nom de votre Service Mobile a été déplacée.
  3. Cliquez sur le bouton **Outils**
  4. Sélectionnez **flux journal** sous le menu observer.

Les journaux sont affichés dans la fenêtre de mesure qu’ils sont générés.  Vous pouvez également télécharger les journaux d’analyse ultérieure à l’aide de vos informations d’identification de déploiement. Pour plus d’informations, consultez la documentation de [journalisation] .

## <a name="known-issues"></a>Problèmes connus

### <a name="deleting-a-migrated-mobile-app-clone-causes-a-site-outage"></a>Lorsque vous supprimez un cloner migrées de l’application Mobile, un arrêt du site

Si vous clonez votre service mobile a été déplacée à l’aide de PowerShell Azure, puis supprimez la cloner, l’entrée DNS de votre service de production est supprimée.  Votre site est plus accessibles à partir d’Internet.  

Résolution : Si vous souhaitez cloner votre site, faites-le via le portail.

### <a name="changing-webconfig-does-not-work"></a>Modification Web.config ne fonctionne pas

Si vous avez un site ASP.NET, modifications apportées à la `Web.config` fichier ne sont pas appliquées.  Le Service d’application Azure crée un approprié `Web.config` fichier lors du démarrage pour prendre en charge de l’exécution de Services mobiles.  Vous pouvez ignorer certains paramètres (par exemple, en-têtes personnalisés) à l’aide d’un fichier de transformation XML.  Créer un fichier en appelé `applicationHost.xdt` -doit se terminer ce fichier vers le haut dans la `D:\home\site` répertoire le service d’Azure.  Télécharger le `applicationHost.xdt` classer via un script de déploiement personnalisé ou directement à l’aide de Kudu.  La figure suivante montre un exemple de document :

```
<?xml version="1.0" encoding="utf-8"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <system.webServer>
    <httpProtocol>
      <customHeaders>
        <add name="X-Frame-Options" value="DENY" xdt:Transform="Replace" />
        <remove name="X-Powered-By" xdt:Transform="Insert" />
      </customHeaders>
    </httpProtocol>
    <security>
      <requestFiltering removeServerHeader="true" xdt:Transform="SetAttributes(removeServerHeader)" />
    </security>
  </system.webServer>
</configuration>
```

Pour plus d’informations, consultez la documentation [XDT transformer exemples] sur GitHub.

### <a name="migrated-mobile-services-cannot-be-added-to-traffic-manager"></a>Les Services Mobile migrés ne peut pas être ajoutés au Gestionnaire de trafic

Lorsque vous créez un profil le trafic Manager, vous ne pouvez pas choisir directement un service mobile a été déplacée vers le profil.  Utiliser un « point de terminaison externe. »  Le point de terminaison externes ne peut être ajouté via PowerShell.  Pour plus d’informations, consultez le [didacticiel Manager le trafic](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/).

## <a name="next-steps"></a>Étapes suivantes

À présent que votre application est éléments migrée vers le Service d’application, il existe davantage de fonctionnalités que vous pouvez utiliser :

  * [Emplacements de mise en attente] de déploiement permettent les modifications apportées à votre site de l’étape et effectuer A / B test.
  * [WebJobs] fournissent un remplacement pour les tâches planifiée à la demande.
  * Vous pouvez [déployer en permanence] votre site en liant votre site à GitHub, TFS ou mercurienne.
  * Vous pouvez utiliser des [Perspectives d’Application] pour surveiller votre site.
  * Répondre à un site Web et une API Mobile à partir du même code.

### <a name="upgrading-your-site"></a>La mise à niveau votre site de Services mobiles applications Kit de développement Mobile Azure

  * Pour les projets serveur Node.js, le nouveau [Kit de développement Mobile applications Node.js] fournit plusieurs nouvelles fonctionnalités. Par exemple, vous pouvez désormais faire développement local et le débogage, utiliser n’importe quelle version Node.js au-dessus 0,10 et personnaliser avec les logiciels intermédiaires Express.js.

  * Pour. Projets serveur réseau, les nouveaux [packages Mobile applications SDK NuGet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) ont plus de flexibilité sur les dépendances NuGet.  Ces packages prend en charge la nouvelle authentification de Service d’application, puis composent avec n’importe quel projet ASP.NET. Pour en savoir plus sur la mise à niveau, voir [mettre à niveau votre Service Mobile .NET existant vers l’application](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[Service d’application tarifs]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[Analyse de l’application]: ../application-insights/app-insights-overview.md
[Ajuster automatiquement]: ../app-service-web/web-sites-scale.md
[Service application Azure]: ../app-service/app-service-value-prop-what-is.md
[Documentation de déploiement de Service d’application Azure]: ../app-service-web/web-sites-deploy.md
[Portail classique Azure]: https://manage.windowsazure.com
[Portail Azure]: https://portal.azure.com
[Azure Region]: https://azure.microsoft.com/en-us/regions/
[Plans de planificateur Azure]: ../scheduler/scheduler-plans-billing.md
[déployer en continu]: ../app-service-web/app-service-continuous-deployment.md
[Convertir vos espaces de noms mixtes]: https://azure.microsoft.com/en-us/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: http://curl.haxx.se/
[noms de domaine personnalisé]: ../app-service-web/web-sites-custom-domain-name.md
[Fiddler]: http://www.telerik.com/fiddler
[disponibilité générale du Service d’application Azure]: https://azure.microsoft.com/blog/announcing-general-availability-of-app-service-mobile-apps/
[Connexions hybride]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[Journalisation]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Applications mobiles Node.js SDK]: https://github.com/azure/azure-mobile-apps-node
[Services et application Service mobile]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Notification Hubs]: ../notification-hubs/notification-hubs-push-notification-overview.md
[analyse des performances]: ../app-service-web/web-sites-monitor.md
[Postman]: http://www.getpostman.com/
[Sauvegarder votre Service Mobile]: ../mobile-services/mobile-services-disaster-recovery.md
[emplacements de mise en attente]: ../app-service-web/web-sites-staged-publishing.md
[VNet]: ../app-service-web/web-sites-integrate-with-vnet.md
[WebJobs]: ../app-service-web/websites-webjobs-resources.md
[Exemples de transformation XDT]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples
[Fonctions]: ../azure-functions/functions-overview.md
