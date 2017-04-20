<properties
    pageTitle="Propriétés du rôle Azure"
    description="Découvrez comment utiliser le Kit de ressources Azure pour Eclipse pour configurer les paramètres de rôle Azure."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690945.aspx -->

# <a name="azure-role-properties"></a>Propriétés du rôle Azure #

Différents paramètres de configuration pour votre rôle Azure peuvent être définis dans la boîte à outils Azure pour Eclipse.

## <a name="configuring-azure-role-properties"></a>Configuration des propriétés de rôle Azure ##

Configuration des propriétés de votre rôle Azure est réalisé via les boîtes de dialogue Propriétés pour votre rôle de collaborateur. Ouvrir le menu contextuel pour le rôle dans le volet Explorateur de projet de Eclipse et sélectionnez le sous-menu **Azure** . (Si vous ne voyez pas le rôle dans l’Explorateur de projets Eclipse, développez votre projet dans l’Explorateur de projets Azure).

![][ic789599]

Les différentes propriétés pouvant être définies dans les boîtes de dialogue **Propriétés** sont décrites dans cette rubrique. Notez que de nombreuses propriétés sont renseignées automatiquement lorsque vous créez un nouveau projet de déploiement d’Azure.

Les pages de propriétés suivantes sont disponibles pour les rôles d’Azure.

* [Propriétés de la machine virtuelle](#virtual_machine_properties)
* [Propriétés de la mise en cache](#caching_properties)
* [Propriétés de certificats](#certificates_properties)
* [Propriétés de composants](#components_properties)
* [Propriétés de débogage](#debugging_properties)
* [Propriétés de points de terminaison](#endpoints_properties)
* [Propriétés de variables d’environnement](#environment_variables_properties)
* [Équilibrage de charge / propriétés de la session affinité (appelés « sessions permanentes »)](#session_affinity_properties)
* [Propriétés du stockage local](#local_storage_properties)
* [Propriétés de configuration de serveur](#server_configuration_properties)
* [SSL décharger propriétés](#ssl_offloading_properties)
    
<a name="virtual_machine_properties"></a>
### <a name="virtual-machine-properties"></a>Propriétés de la machine virtuelle ###

Ouvrir le menu contextuel pour le rôle dans le volet Explorateur de projet de Eclipse, **Azure**, puis cliquez sur **Propriétés**, et vous avez la possibilité de modifier la taille de la machine virtuelle et également modifier le nombre d’instances, comme le montre l’image suivante.

![][ic719499]

>[AZURE.NOTE] Windows uniquement : lorsque vous définissez le nombre d’instances à une valeur supérieure à 1 et que vous configurez également un serveur d’applications, la boîte à outils permettra uniquement 1 instance de rôle à exécuter dans l’émulateur, quel que soit ce paramètre. Il s’agit pour éviter les conflits de liaison de port entre les instances de serveur différent (par exemple, tous les tentative de liaison au port 8080) lorsqu’ils s’exécutent sur le même ordinateur. Votre paramètre du nombre instance souhaitée est conservé, mais il entre en vigueur uniquement lorsque vous déployez le cloud.

<a name="caching_properties"></a> 
### <a name="caching-properties"></a>Propriétés de la mise en cache ###

Ouvrir le menu contextuel pour le rôle dans le volet Explorateur de projet de Eclipse et cliquez sur **Azure**, puis cliquez sur **mise en cache**. Dans cette boîte de dialogue, vous pouvez activer cache compatible avec memcache situés nommé, ce qui vous permet de vous aider à accélérer vos applications web.

![][ic719483]

Dans la page de propriétés **mise en cache** , vous pouvez spécifier des paramètres globaux pour les éléments suivants :

* Si la mise en cache situés est activée.
* la taille de cache sous forme de pourcentage de mémoire.
* le nom de compte de stockage pour enregistrer l’état du cache lorsque votre application s’exécute comme un service cloud, ou aucun si vous ne souhaitez pas enregistrer l’état du cache. (Le nom de compte de stockage n’est pas utilisé lorsque vous exécutez votre application dans l’émulateur cluster.) Si vous définissez le nom de compte de stockage **(automatique)** (ce qui correspond à la valeur par défaut), la configuration de votre mise en cache utilise automatiquement le même compte de stockage que celui que vous sélectionnez dans la boîte de dialogue **publier sur Azure** .

>[AZURE.NOTE] Le paramètre **(automatique)** a l’effet souhaité uniquement si vous publiez votre déploiement à l’aide de la boîte à outils Eclipse Assistant de publication. Si vous publiez à la place le fichier .cspkg manuellement en utilisant un mécanisme externe, tels que le [Portail de gestion Azure][], le déploiement ne fonctionnera pas correctement.

La boîte de dialogue affiche les propriétés pour un cache.

![][ic719501]

* **Nom :** Le nom du cache situé.
* **Numéro de port :** Le numéro de port à utiliser pour le cache.
* **Stratégie d’expiration :** Une des valeurs suivantes, qui indique quand une clé dans le cache arrive à expiration.
    * **Absolue :** La clé expire lorsque l’heure spécifiée par les **Minutes de vie** est atteint.
    * **NeverExpires :** La clé ne comporte pas un délai d’expiration.
    * **SlidingWindow :** La clé expire si elle n’ont pas été utilisé pendant la durée spécifiée par **Minutes live**; chaque fois qu’il est consulté, l’horloge d’expiration est réinitialisé.
* **Minutes live :** Nombre maximal de minutes d’une clé de memcached de vie couvertes par la stratégie d’expiration.
* **Disponibilité avec les sauvegardes répliquées sur les instances de rôle différent :** Si activé, contribue à la disponibilité utilisant répliquées des sauvegardes sur des instances de rôle différent. Notez qu’au moins deux instances de rôle doit être en vigueur pour votre déploiement pour cette fonctionnalité pour l’utiliser.

Pour ajouter un nouveau cache, cliquez sur le bouton **Ajouter** dans la page de propriétés de **la mise en cache** et une boîte de dialogue **Configurer le Cache nommé** s’ouvre. Indiquez les valeurs pour les propriétés qui sont décrites ci-dessus.

Pour modifier un cache nommé, sélectionnez le cache, puis cliquez sur le bouton **Modifier** dans la page de propriétés de **la mise en cache** . Une boîte de dialogue s’ouvre et vous permet de modifier les propriétés du cache. Appuyez sur **OK** pour enregistrer les valeurs du cache.

Pour supprimer un cache, sélectionnez le cache et cliquez sur le bouton **Supprimer** dans la page de propriétés de **la mise en cache** , puis cliquez sur **Oui** pour confirmer la suppression.

Pour plus d’informations sur l’utilisation de la mise en cache, voir [comment utiliser la mise en cache Co-located][].

<a name="certificates_properties"></a> 
### <a name="certificates-properties"></a>Propriétés de certificats ###

Ouvrir le menu contextuel pour le rôle dans le volet Explorateur de projet de Eclipse et cliquez sur **Azure**, puis cliquez sur **certificats**.

![][ic710964]

Dans cette boîte de dialogue, vous pouvez ajouter ou supprimer des certificats référencées par votre projet Eclipse. Notez que les certificats répertoriés ici ne sont pas automatiquement stockés à l’intérieur d’une combinaison de touches Java et par conséquent ne sont pas automatiquement disponibles pour être n’importe quel utilisée dans une application Java. Ils sont enregistrés uniquement avec Azure afin qu’ils puissent être préchargés dans les fenêtres certificat stocker sur les ordinateurs virtuels votre déploiement et par la suite utilisé par d’autres logiciels Windows. Pour l’instant, la seule fonctionnalité du Kit de ressources qui utilise les certificats référencés de cette façon dans la boîte de dialogue **certificats** est [SSL décharger][], en raison de sa dépendance sur Internet Information Services (IIS) et Application demander routage (ARR), qui nécessitent le certificat approprié à la disposition de cette manière.

Lorsque vous déployez votre projet Azure à l’aide de l’Assistant Publication, vous devrez pointez sur les fichiers de personnel informations d’échange correspondant à ces certificats, ainsi que leur mot de passe, afin de les télécharger automatiquement au service Azure, mais uniquement si elles n'ont pas été téléchargées il précédemment.

<a name="components_properties"></a> 
### <a name="components-properties"></a>Propriétés de composants ###

Ouvrir le menu contextuel pour le rôle dans le volet Explorateur de projet de Eclipse et cliquez sur **Azure**, puis cliquez sur **composants**. Dans cette boîte de dialogue, vous avez la possibilité d’ajouter, modifier, ou supprimer les composants de votre rôle, mais aussi modifier l’ordre dans lequel ils sont traités.

![][ic719502]

La fonctionnalité des composants vous permet d’ajouter des dépendances à votre projet de déploiement d’Azure, tels que les projets d’application Java, fichiers spéciaux et les instructions de ligne de commande exécutable qui sont nécessaires à votre déploiement.

Pour chaque composant, vous pouvez spécifier :

* L’étape à prendre lorsque vous importez le composant dans votre projet de déploiement d’Azure lorsqu’elle est générée.
* L’étape à prendre lorsque vous déployez ce composant dans le cloud Azure.

>[AZURE.NOTE] Lorsque vous spécifiez les fichiers de composants ou lignes de commande, n’oubliez pas que votre déploiement sera publié à une machine virtuelle Windows, afin que vos étapes personnalisées doivent être valides pour un système d’exploitation Windows. 

Composants ont les propriétés suivantes :

* **Importation :** Méthode qui indique comment le composant est importé dans le projet lorsque le projet est généré. Il peut s’agir d’une des valeurs suivantes :
    * **copie :** Le composant est copié à partir du chemin d’accès local spécifié par la propriété **à partir de** , dans le répertoire **approot** du rôle.
    * **Effacer :** Le composant est une archive d’entreprise Java (Effacer) importée à partir d’un projet d’Application entreprise en le chemin d’accès local spécifié par la propriété **à partir de** . (Cela est détecté automatiquement par le Kit de ressources en fonction de la nature du projet à cet emplacement).
    * **JAR :** Le composant est une archive Java (JAR) et importé à partir d’un projet Java en le chemin d’accès local spécifié par la propriété **à partir de** . (Cela est détecté automatiquement par le Kit de ressources en fonction de la nature du projet à cet emplacement).
    * **Aucun :** Aucune action importer le composant. Ceci s’applique lorsque le composant est supposé égal à déjà présents dans le répertoire **approot** du rôle, ou lorsque le composant est simplement une instruction de ligne de commande exécutable, comme indiqué dans la propriété **en tant que** lorsque la méthode de **déploiement** est **exécution**.
    * **WAR :** Le composant est une archive d’application web Java (WAR) et importé à partir d’un projet Web dynamiques sur le chemin d’accès local spécifié par la propriété **à partir de** . (Cela est détecté automatiquement par le Kit de ressources en fonction de la nature du projet à cet emplacement).
    * **zip :** Le composant est un fichier zip et est importé par compresser le répertoire ou le fichier spécifié par la propriété **From** .
* **à partir de :** Chemin d’accès source sur votre ordinateur local vers le dossier ou fichier qui représente l’ou les éléments à importer dans votre déploiement. Variables d’environnement Windows peuvent être utilisées dans cette propriété. Tous les composants feuille seront importés dans le répertoire **approot** du rôle lorsque le projet est généré.
    
    Notez que vous avez la possibilité de déployer un composant à partir d’un téléchargement lorsque vous déployez dans le cloud (pas l’émulateur cluster). Voir les informations associées ci-dessous sur l’ajout d’un composant.    
    
* **As:** Nom de fichier sous lequel le composant sera importé dans le répertoire **approot** du rôle et finalement déployé dans le cloud Azure. Laissez cette propriété vide pour conserver le nom de la même qu’il est sur l’ordinateur local. (Pour les composants exécutables, c'est-à-dire que ceux dont la méthode de **déploiement** est définie sur **exécution**, cela peut être une instruction de ligne de commande Windows arbitraire.)

    >[AZURE.IMPORTANT] Si vous utilisez des caractères d’espace pour cette valeur, ceux-ci sont gérés différemment selon la méthode de déploiement. Si la méthode de déploiement est **exécution**, espaces seront interprétés comme séparateurs d’arguments de ligne de commande et non dans le cadre du nom du fichier. Pour tous les autres déployer méthodes, espaces seront interprétées comme partie du nom de fichier.
    
* **Déployer :** Méthode qui indique l’action appliquée au composant quand le déploiement est démarré. Il peut s’agir d’une des valeurs suivantes :
    * **copie :** Le composant est copié dans le chemin d’accès de destination spécifié par la propriété **To** .
    * **exécution :** Le composant est une instruction de ligne de commande Windows exécutable exécutée dans le contexte du chemin spécifié par la propriété **To** , lorsque le que déploiement démarre.
    * **Aucun :** Aucune action n’est appliquée au composant lorsque le déploiement commence.
    * **zip :** Le composant est décompressé dans le chemin d’accès de destination spécifié par la propriété **To** . Cette méthode est disponible uniquement si la propriété **importation** est **zip**.
* **To:** Chemin de destination sur l’ordinateur virtuel où le composant sera déployé. Variables d’environnement Windows peuvent être utilisés dans cette propriété et chemins d’accès du fichier dépendent de **approot**.
    
Pour ajouter un nouveau composant, cliquez sur le bouton **Ajouter** dans la page de propriétés de **composants** et une boîte de dialogue **Composant de rôle Azure** s’ouvre. Indiquez les valeurs pour les propriétés qui sont décrites ci-dessus. 

La figure suivante montre un exemple d’ajout d’un nouveau composant guerre.

![][ic719503]

Lorsque vous déployez dans le cloud (pas l’émulateur cluster), si vous voulez déployer le composant à partir d’un téléchargement, assurez-vous que **lorsque dans le cloud, au lieu d’inclure dans le package, déployez** d’est cochée. Si vous voulez télécharger à partir de votre compte de stockage Azure, sélectionnez le compte de stockage dans la liste déroulante de **compte de stockage** (vous pouvez cliquer sur le lien **comptes** pour modifier ce qui figure dans la liste), qui inséreront partiellement dans le champ **URL** , puis renseignez la partie restante de l’URL. Si vous ne souhaitez pas utiliser le stockage Azure, sélectionnez **(aucun)** dans la liste déroulante **compte de stockage** et entrez l’URL de votre composant dans le champ **URL** . Spécifier l’une des méthodes suivantes :

* **copie :** Le composant de téléchargement est copié dans le chemin d’accès de destination spécifié par le chemin d’accès **Au répertoire** .
* **même :** La méthode utilisée pour **déployer à partir de téléchargement** en ce qui concerne les **déployer du package**.
* **zip :** Le composant de téléchargement est décompressé dans le chemin d’accès de destination spécifié par le chemin d’accès **Au répertoire** .

Pour modifier un composant, sélectionnez le composant et cliquez sur le bouton **Modifier** dans la page de propriétés de **composants** . Une boîte de dialogue s’ouvre et vous permet de modifier les propriétés du composant. Appuyez sur **OK** pour enregistrer les valeurs de composant.

Pour supprimer un composant, sélectionnez le composant et cliquez sur le bouton **Supprimer** dans la page de propriétés de **composants** , puis cliquez sur **Oui** pour confirmer la suppression.

Composants sont traités dans l’ordre indiqué. Utilisez les boutons **Monter** et **Descendre** pour modifier l’ordre.

>[AZURE.NOTE] La fonctionnalité de configuration serveur repose sur composants également. Ces composants ne peut pas être supprimés ou modifiés sans supprimer la configuration du serveur correspondant. Vous devez à ce sujet lorsque vous tentez d’apporter des modifications à ces composants.

<a name="debugging_properties"></a> 
### <a name="debugging-properties"></a>Propriétés de débogage ###

Ouvrir le menu contextuel pour le rôle dans le volet Explorateur de projet de Eclipse et cliquez sur **Azure**, puis cliquez sur **débogage**. Dans cette boîte de dialogue, vous avez la possibilité d’activer ou désactiver le débogage à distance, ainsi que créez des configurations débogage, comme le montre l’image suivante.

![][ic719504]

Pour obtenir des informations connexes sur le débogage, consultez [Débogage des Applications Azure dans Eclipse][].

<a name="endpoints_properties"></a> 
### <a name="endpoints-properties"></a>Propriétés de points de terminaison ###

Ouvrir le menu contextuel pour le rôle dans le volet Explorateur de projet de Eclipse et cliquez sur **Azure**, puis cliquez sur **points de terminaison**. Dans cette boîte de dialogue, vous avez la possibilité de créer un point de terminaison, ainsi que modifier ou supprimer un point de terminaison, comme illustré dans l’image suivante.

![][ic719505]

Pour ajouter un point de terminaison, cliquez sur le bouton **Ajouter** dans la page de propriétés de **points de terminaison** et une boîte de dialogue **Ajouter un point de terminaison** sera ouvert.

![][ic710897]

Entrez un nom pour le point de terminaison, sélectionnez le type ( **Input**, **interne**ou **InstanceInput**) et spécifiez le port publique et privé. Appuyez sur **OK** pour enregistrer les nouvelles valeurs de point de terminaison.

Selon le type de point de terminaison, vous pouvez utiliser les plages de ports comme suit :

* Pour un point de terminaison instance d’entrée, le port public peut être une plage de ports (par exemple **2000-2010**) et le port privé est une valeur fixe.
* Pour un point de terminaison interne, le port public n’est pas utilisé et le port privé peut être une plage, ou gauche vide ou un astérisque pour indiquer qu’il a la valeur est automatiquement définie par Azure.
* Pour les points de terminaison d’entrée, le port public peut uniquement être une valeur fixe, et le port privé pouvant être une valeur fixe ou vide ou définir un astérisque pour indiquer qu’elle est définie automatiquement par Azure.

Si vous souhaitez utiliser un numéro de port unique au lieu d’une plage, laissez la zone de texte à la fin de la plage à blanc.

Pour les ports qui sont définies sur if automatique, vous devront déterminer quel port est utilisé lors de l’exécution, votre application peut utiliser l’API pour l’exécution du Service Azure, qui est décrite dans le [Résumé du package com.microsoft.windowsazure.serviceruntime][].

Pour voir comment les points de terminaison d’entrée instance peuvent être utilisés pour faciliter le débogage d’un déploiement de plusieurs instances, consultez [débogage d’une instance de rôle spécifique dans un déploiement de plusieurs instances][].

Pour modifier un point de terminaison, sélectionnez le point de terminaison et cliquez sur le bouton **Modifier** dans la page de propriétés de **points de terminaison** . Une boîte de dialogue s’ouvre et vous permet de modifier le nom du point de terminaison, type et ports publiques et privées. Appuyez sur **OK** pour enregistrer les valeurs de point de terminaison modifiée.

Pour supprimer un point de terminaison, sélectionnez le point de terminaison et cliquez sur le bouton **Supprimer** dans la page de propriétés de **points de terminaison** , puis cliquez sur **Oui** pour confirmer la suppression.

Afin de configurer correctement les fonctionnalités (par exemple, la mise en cache, débogage distant, Session affinité ou SSL décharger) activée par l’utilisateur sur un rôle, la boîte à outils peut configurer automatiquement les points de terminaison spéciales qui apparaîtront ainsi que des points de terminaison définis par l’utilisateur. Le Kit de ressources empêche l’utilisateur de modifier ou supprimer ces automatiquement généré points de terminaison tant que la fonction associée est activée.

<a name="environment_variables_properties"></a> 
### <a name="environment-variables-properties"></a>Propriétés de variables d’environnement ###

Ouvrir le menu contextuel pour le rôle dans le volet Explorateur de projet de Eclipse et cliquez sur **Azure**, puis cliquez sur **Variables d’environnement**. Dans cette boîte de dialogue, vous avez la possibilité de créer une variable d’environnement, ainsi que modifier ou supprimer une variable d’environnement, comme le montre l’image suivante.

![][ic719506]

Variables d’environnement sont disponibles à votre script de démarrage lorsque le rôle démarre.

>[AZURE.NOTE] Lorsque vous spécifiez des variables d’environnement, n’oubliez pas que votre déploiement sera publié à une machine virtuelle Windows, afin que vos variables d’environnement doivent être valides pour un système d’exploitation Windows.

Par exemple d’une variable d’environnement soient disponibles lorsque le rôle démarre, créez une nouvelle variable d’environnement en cliquant sur le bouton **Ajouter** . La figure suivante montre une variable d’environnement nommée **MyRoleVersion** créée et affectée la valeur **1.0**.

![][ic659268]

Au sein de votre code jsp, vous pouvez afficher la valeur à l’aide du `System.getenv` méthode :

    <body>
      <b> Hello World!</b>
      <p>Running role version: <%= System.getenv("MyRoleVersion") %></p>
    </body>

Résultant dans ce résultat lors de l’exécution de votre application :

![][ic552233]

Pour modifier une variable d’environnement, sélectionnez la variable d’environnement et cliquez sur le bouton **Modifier** dans la page de propriétés de **Variables d’environnement** . Une boîte de dialogue s’ouvre et vous permet de modifier les propriétés de la variable d’environnement. Appuyez sur **OK** pour enregistrer l’environnement de valeurs de variables.

Pour supprimer une variable d’environnement, sélectionnez la variable d’environnement et cliquez sur le bouton **Supprimer** dans la page de propriétés de **Variables d’environnement** , puis cliquez sur **Oui** pour confirmer la suppression.

Afin de configurer correctement certaines des fonctionnalités (par exemple, la Configuration du serveur, débogage distant ou stockage Local) activée par l’utilisateur sur un rôle, la boîte à outils peut configurer automatiquement les variables d’environnement spécial qui apparaîtront ainsi que des variables d’environnement définies par l’utilisateur. Le Kit de ressources empêche l’utilisateur de modifier ou supprimer ces automatiquement générée variables d’environnement tant que la fonction associée est activée.

<a name="session_affinity_properties"></a> 
### <a name="load-balancing--session-affinity-aka-sticky-sessions-properties"></a>Équilibrage de charge / propriétés de la session affinité (appelés « sessions permanentes ») ###

Ouvrir le menu contextuel pour le rôle dans le volet Explorateur de projet de Eclipse et cliquez sur **Azure**, puis cliquez sur **L’équilibrage de charge**. Dans cette boîte de dialogue, vous avez la possibilité pour activer ou désactiver affinité de session, comme le montre l’image suivante.

![][ic719492]

Pour plus d’informations, voir [Affinité Session][]. Notez également le comportement de cette fonctionnalité dans le contexte de SSL décharger, tels que décrits sur [Décharger SSL][].

<a name="local_storage_properties"></a> 
### <a name="local-storage-properties"></a>Propriétés du stockage local ###

Ouvrir le menu contextuel pour le rôle dans le volet Explorateur de projet de Eclipse et cliquez sur **Azure**, puis cliquez sur **Stockage Local**. Dans cette boîte de dialogue, vous avez la possibilité de créer, modifier ou supprimer le stockage local temporaire pour la machine virtuelle exécutant votre application. Valeurs spécifiques peuvent fixées pour la taille du stockage local, ainsi que le contenu est conservé lorsque le rôle est suppression initiale, comme le montre l’image suivante.

![][ic719508]

Vous pouvez également spécifier une variable d’environnement qui correspond au stockage local.

Par défaut, tous les éléments que vous déployez dans Azure sont placé (et décompressés) dans le dossier **approot** de l’instance de rôle. Contrairement à des déploiements plus simples s’adapter à la même une fois le ranger, l’espace alloué pour le répertoire **approot** est limitée et pas bien défini (inférieures à 1 Go est raisonnablement règle générale). Par conséquent, pour vous assurer que Azure affecte un espace disque suffisant pour les déploiements plus grandes qui ne tient pas dans le dossier **approot** , vous devez configurer une ressource de stockage local à l’aide de la boîte de dialogue **Stockage Local** . Pour un moyen facile pour ce faire, voir [Déployer des déploiements][].

Vous pouvez facilement faire référence à la ressource de stockage à partir de scripts de démarrage (par exemple, votre **startup.cmd**) à l’aide de la variable d’environnement associée automatiquement par la boîte à outils Eclipse avec la ressource, comme indiqué dans la boîte de dialogue **Stockage Local** . Cette variable d’environnement contient le chemin d’accès complet de la ressource locale que vous avez configuré lors de l’exécution de votre script de démarrage. 

Pour modifier une ressource de stockage local, sélectionnez la ressource de stockage local et cliquez sur le bouton **Modifier** dans la page de propriétés de **Stockage Local** . Une boîte de dialogue s’ouvre et vous permet de modifier les propriétés de ressources de stockage local. Appuyez sur **OK** pour enregistrer les valeurs de ressources de stockage local.

Pour supprimer une ressource de stockage local, sélectionnez la ressource de stockage local et cliquez sur le bouton **Supprimer** dans la page de propriétés de **Stockage Local** , puis cliquez sur **Oui** pour confirmer la suppression.

<a name="server_configuration_properties"></a> 
### <a name="server-configuration-properties"></a>Propriétés de configuration de serveur ###

Ouvrir le menu contextuel pour le rôle dans le volet Explorateur de projet de Eclipse et cliquez sur **Azure**, puis cliquez sur **Configuration du serveur**. Dans cette boîte de dialogue, ont la possibilité d’ajouter, supprimer et modifier le serveur d’applications JDK et Java utilisé par votre déploiement, mais aussi ajouter ou supprimer les applications (par exemple, fichiers WAR, JAR ou effacer) utilisées par votre déploiement.

### <a name="jdk-configuration"></a>Configuration de JDK ###

Cette boîte de dialogue vous permet de spécifier le package JDK à utiliser pour votre déploiement. Si vous utilisez Eclipse sous Windows, vous pouvez spécifier le package JDK d’utiliser localement lors de l’exécution dans l’émulateur Azure et vous avez la possibilité à déployer cette installation locale Azure. Systèmes d’exploitation non Windows, le paramètre de JDK émulateur n’est pas applicable et vous ne pouvez pas déployer JDK installé localement car il n’est pas compatible avec Windows. Toutefois, quel que soit le système d’exploitation que vous utilisez, vous pouvez toujours choisir parmi les packages JDK 3e partie à déployer sur Azure ou pointez sur votre propre package JDK compatible avec Windows depuis un emplacement autre téléchargement.

Voici un exemple de la façon dont vous pouvez spécifier un JDK sur Windows :

![][ic780647]

Si vous utilisez Eclipse sous Windows, vous pouvez spécifier un JDK à utiliser avec l’émulateur cluster ; Pour ce faire, vérifiez que **utiliser JDK à partir de ce chemin d’accès de fichier pour le test localement** est cochée dans la section **déploiement émulateur** . Ensuite, spécifiez le chemin d’accès local à votre JDK ; Vous pouvez accéder à JDK différent si l’option que vous voulez utiliser n’est pas activée automatiquement. Vous avez également la possibilité de déployer votre JDK à votre service cloud Azure ; Pour ce faire, sélectionnez l’option **déployer mon JDK local (téléchargement automatique au stockage cloud)** dans la section **déploiement de Cloud** .

Remarque : Sur les systèmes d’exploitation non Windows, les paramètres de **déploiement émulateur** et l’option **déployer mon JDK local** ne sont pas disponibles. L’exemple suivant illustre la spécification d’un JDK sur un Mac ou un autre système d’exploitation non Windows pris en charge :

![][ic789643]

Quel que soit le système d’exploitation que vous exécutez, vous avez deux options **déploiement de Cloud** suivantes pour la source et le type de votre package JDK :

* **Déployer un package JDK 3e partie disponible sur Azure** 
* **Déployer à partir d’un téléchargement personnalisé** 

Si vous utilisez l’option **déployer un 3e partie package JDK disponible à partir d’Azure** :

1. Cochez la case à cocher nommé **déployer un 3e partie package JDK disponible à partir d’Azure**.
1. Dans la liste déroulante, sélectionnez le package JDK tiers 3e disponible dans Azure.
1. Votre onglet **JDK** est semblable à ce qui suit sous Windows :  ![][ic780648] 
    et il est semblable à ce qui suit dans Mac OS ou autres systèmes d’exploitation Windows non pris en charge : ![][ic789643]
1. Cliquez sur **OK** pour enregistrer vos modifications.
1. Lorsque vous êtes invité à accepter le contrat de licence à partir du fournisseur de package 3e partie JDK, passez en revue le contrat de licence. En supposant que vous acceptez les termes, cliquez sur **Oui** pour fermer la boîte de dialogue **contrat de licence accepter** .
    Notez que la logique sous-jacente pour lequel les éléments apparaissent dans la liste déroulante de l’option **déployer un 3e partie package JDK disponible à partir d’Azure** peut être personnalisée. Pour personnaliser les éléments, dans la boîte de dialogue **JDK** , cliquez sur le lien **Personnaliser** . Cela sera fermer la page de propriétés **JDK** et ouvrez le fichier **componentsets.xml** dans Eclipse, vous pouvez ensuite modifier selon vos besoins. Documentation de **componentsets.xml** est incluse dans le fichier **componentsets.xml** proprement dit.

Si vous utilisez l’option **déployer un JDK à partir d’un téléchargement personnalisé** :

1. Créer un fichier ZIP de votre répertoire d’installation JDK, veiller à ce que le nœud du répertoire lui-même est l’enfant de la structure ZIP et pas son contenu. Notez le nom du répertoire, comme vous sera besoin ultérieurement, n’oubliez pas que cette installation JDK sera déployée sur une machine virtuelle Windows.
1. Télécharger le ZIP dans votre compte de stockage Azure comme un objet blob. Vous pouvez effectuer cette en utilisant un outil externes disponible pour les objets BLOB chargés au stockage Azure. Il est recommandé d’utiliser un blob privé. Prenez note de l’URL de blob du contenu ZIP.
1. Cochez la case à cocher nommé **déployer un JDK à partir d’un téléchargement personnalisé**.
    Si vous voulez télécharger à partir de votre compte de stockage Azure, sélectionnez le compte de stockage dans la liste déroulante de **compte de stockage** (vous pouvez cliquer sur le lien **comptes** pour modifier ce qui figure dans la liste), qui inséreront partiellement dans le champ **URL** , puis renseignez la partie restante de l’URL. Si vous ne souhaitez pas utiliser le stockage Azure, sélectionnez **(aucun)** dans la liste déroulante **compte de stockage** et entrez l’URL de votre téléchargement JDK dans le champ **URL** . Si l’utilisation du stockage Azure, les noms d’objets blob dans l’URL doivent être en minuscules.
1. Assurez-vous que la zone de texte **JAVA_HOME** désigne le nom de répertoire correct. Par défaut, il référence le nom du répertoire JDK même que celle que vous avez choisi pour votre utilisation locale. Mais si le répertoire contenu dans le ZIP a un autre nom (par exemple, en raison d’à l’aide d’une autre version), mettre à jour le nom du répertoire dans la zone de texte **JAVA_HOME** en conséquence, étant donné que ce paramètre sera utilisé dans le cloud (et non dans l’émulateur cluster).
1. Cliquez sur **OK** pour enregistrer vos modifications.

Voilà. À présent, lorsque vous créez pour le cloud, vous remarquerez la taille du package seront beaucoup plus petite, le processus de génération doit prendre généralement moins de temps et du déploiement proprement dit lorsque vous publiez sur le cloud conseillons de prendre moins de temps. Notez que les options de **déploiement mon JDK local (téléchargement automatique au stockage cloud)** ou **déployer un JDK à partir d’un téléchargement personnalisé** sont en vigueur uniquement lorsque votre application est déployée dans le cloud. Ils n’ont aucun effet sur votre expérience d’émulateur cluster ; la version locale des composants est toujours utilisée lorsque vous déployez l’émulateur cluster. 

### <a name="server-configuration"></a>Configuration du serveur ###

Voici un exemple de la façon dont vous pouvez spécifier un serveur d’applications.

![][ic796926]

Vérifiez que la case à cocher **déployer un serveur de ce type** est sélectionnée, puis sélectionnez le type de serveur d’applications que vous voulez utiliser.

Pour spécifier un serveur à utiliser pour le déploiement de cloud, vous pouvez tirer parti des options suivantes :

1. **Déployer un 3e partie server disponibles dans Azure** - il s’agit particulièrement applicable dans les scénarios de développement/test où l’efficacité de déploiement et la simplicité est une priorité et le serveur ne nécessite pas une configuration personnalisée. Ou lorsque vous voulez utiliser un de ces serveurs comme point de départ, mais vous incluez personnalisation serveur approprié les étapes dans la logique de démarrage de votre déploiement.
1. **Déployer à partir d’un téléchargement personnalisé** - il s’agit particulièrement applicable dans les scénarios de production lorsque vous disposez d’un serveur spécialement préparé et configuré que vous souhaitez utiliser dans le cloud.
1. **Déployer mon installation serveur local** - c’est particulièrement applicable dans si votre installation de serveur local est déjà configuré personnalisée pour votre utilisation. Si vous choisissez cette option, vous devez également spécifier le chemin d’accès de votre serveur local dans la zone de texte **chemin d’accès du serveur Local** ci-dessous.

Si vous utilisez l’option **déployer un 3e partie server disponibles dans Azure** :

1. Cochez la case à cocher nommé **déployer un 3e partie server disponibles dans Azure**.
1. Dans le menu déroulant, sélectionnez le logiciel de serveur de votre choix à utiliser avec votre déploiement dans le cloud. Remarque, si vous avez déjà spécifié un type de serveur à l’aide antérieure, vous serez limité à choisir un serveur de cloud qui se trouve dans la même famille en tant que type de serveur. Mais si vous n’avez pas choisi un type de serveur, vous pouvez choisir parmi tous les serveurs qui sont actuellement disponibles sur Azure et le type de serveur sera sélectionné automatiquement pour vous.
1. Cliquez sur **OK** pour enregistrer vos modifications.

Si vous utilisez l’option **déployer à partir d’un téléchargement personnalisé** :

1. Vérifiez que vous avez déjà sélectionné un type de serveur selon les étapes précédentes. Indique le plug-in comment déployer le serveur à partir de votre téléchargement personnalisé, car elle doit être de la même famille comme type de serveur sélectionné.
1. Cochez la case à cocher nommé **déployer à partir d’un téléchargement personnalisé**.
    Si vous voulez télécharger à partir de votre compte de stockage Azure, sélectionnez le compte de stockage dans la liste déroulante de **compte de stockage** (vous pouvez cliquer sur le lien **comptes** pour modifier ce qui figure dans la liste), qui inséreront partiellement dans le champ **URL** , puis renseignez la partie restante de l’URL à votre serveur de télécharger le fichier ZIP (lors de l’utilisation du stockage Azure, les noms d’objets blob dans l’URL doit être en minuscule). Si vous ne souhaitez pas utiliser le stockage Azure, sélectionnez **(aucun)** dans la liste déroulante **compte de stockage** et entrez l’URL de votre téléchargement server ZIP dans le champ **URL** . Le ZIP contient un dossier enfant représentant le répertoire de votre installation application server. Par exemple, si vous utilisez un zip pour Apache Tomcat 7.0.35, dans le zip serait le dossier enfant qui représente le répertoire d’installation, tels que **apache-tomcat-7.0.35**. 
1. Spécifier la valeur de la variable d’environnement répertoire de base. Par défaut est la valeur utilisée pour votre serveur application locale, le cas échéant, mais vous pouvez spécifier une valeur différente si votre serveur d’applications cloud est différent de votre serveur d’application local. Toutefois, vous devez vous assurer que votre serveur d’applications cloud est de la même famille en tant que le serveur type sélectionné précédemment.
    Si vous mettez à jour votre zip cloud application server à l’avenir, vous pouvez modifier manuellement le paramètre répertoire de base, ou, pour qu’il correspond à nouveau votre paramètre local (si vous avez modifié votre serveur application locale trop).
1. Cliquez sur **OK** pour enregistrer vos modifications.

La logique sous-jacente dont les éléments apparaissent dans l’onglet **serveur** de la page de propriétés de **Configuration du serveur** peut être personnalisée. Il s’agit d’une fonctionnalité avancée que vous devrez peut-être si vos besoins dépassent les valeurs par défaut ou si vous souhaitez ajouter d’autres serveurs. Pour personnaliser la logique, dans la boîte de dialogue **Server** , cliquez sur le lien **Personnaliser** . Cela sera fermer la page de propriétés de **Configuration du serveur** et ouvrez le fichier **componentsets.xml** dans Eclipse, vous pouvez ensuite modifier selon vos besoins pour étendre le modèle de configuration de serveur. Documentation de **componentsets.xml** est incluse dans le fichier **componentsets.xml** proprement dit.

Si vous utilisez l’option **déployer mon serveur local (téléchargement automatique au stockage cloud)** :

1. Cochez la case à cocher nommé **déployer mon serveur local (téléchargement automatique au stockage cloud)**.
1. À l’aide de la liste déroulante **compte de stockage** , sélectionnez **(automatique)**. Si vous spécifiez **(automatique)** ici, le jeu d’outils Eclipse utilisera le même compte de stockage pour votre serveur que celui que vous sélectionnez pour votre déploiement dans la boîte de dialogue **publier sur Azure** .
1. Cliquez sur **OK** pour enregistrer vos modifications.

Sélectionnez un chemin d’installation server sur votre ordinateur dans la zone de texte **chemin d’accès du serveur Local** si une des conditions suivantes est vraie :

* Vous souhaitez tester votre déploiement dans l’émulateur (s’applique uniquement à Windows).
* Vous voulez déployer votre serveur installée localement dans le cloud.
* Vous souhaitez utiliser un téléchargement serveur personnalisé de votre propre dans le cloud, dans lequel cas, vérifiez également que l’option **déployer mon serveur local (téléchargement automatique au stockage cloud)** est sélectionnée au-dessus.

Si aucune des options précédentes ne s’applique à votre situation, le paramètre de serveur local est facultatif.

### <a name="applications-configuration"></a>Configuration des applications ###

Voici un exemple de la façon dont vous pouvez spécifier une application.

![][ic719512]

Cliquez sur **Ajouter** pour ajouter une autre application, ou sur **Supprimer** pour supprimer une application. Pour plus d’efficacité, si vous voulez utiliser un téléchargement pour la source d’une application lors du déploiement dans le cloud, utilisez les [Propriétés de composants](#components_properties) pour spécifier une URL, le compte de stockage, etc.. 

À partir de la version d’avril 2014, vos applications sont automatiquement téléchargées sur le même compte de stockage (sous le conteneur **eclipsedeploy** ) que celui sélectionné pour votre déploiement. La logique de démarrage de votre déploiement contient une étape qui télécharge d’abord ces applications de ce compte de stockage. Cela signifie que vous pouvez mettre à niveau vos applications dans votre déploiement sans avoir à recréer et redéployez l’intégralité du package, en le téléchargeant manuellement les versions plus récentes de l’application directement dans ce compte de stockage (en utilisant le portail Azure par exemple), remplacez les fichiers WAR de télécharger à l’origine par le Kit de ressources. Ensuite, qu’initier le recyclage de toutes les instances de rôle à l’aide du portail de gestion de Azure, ou via des utilitaires de ligne de commande. (Déclenchement rôle recyclage directement à partir de la boîte à outils Eclipse n'est pas actuellement pris en charge.)

### <a name="notes-about-server-configuration"></a>Remarques sur la configuration du serveur ###

Modifications apportées à la page de propriétés de **configuration du serveur** sont répercutées dans le `<component>` éléments du fichier package.xml.

Lorsque vous utilisez le **Télécharger automatiquement...** ou créez des options de **déploiement de télécharger...** pour JDK ou serveur d’applications et vous pour le cloud (pas l’émulateur cluster), et vous êtes connecté au réseau, vous pouvez remarquer générer des messages tels que les éléments suivants dans le résultat de la Console, comme le Générateur de Ant vérifie la disponibilité du téléchargement :

`[windowsazurepackage] Verifying blob availability (https://example.blob.core.windows.net/temp/tomcat6.zip)...` 

Si vous avez sélectionné l’option **déployer à partir de télécharger...** , le message d’avertissement suivant peut s’afficher, mais la génération continue :

`[windowsazurepackage] warning: Failed to confirm blob availability! Make sure the URL and/or the access key is correct (https://example.blob.core.windows.net/temp/tomcat6.zip).` 

Ce message d’avertissement est la seule indication que disponibilité du téléchargement n’a pas été vérifiée. Donc si un déploiement échoue dans le cloud pour une raison quelconque, vérifiez si vous avez reçu ce message d’avertissement.

Si vous souhaitez désactiver la vérification de téléchargement (par exemple, si vous pensez qu’il ralentit inutilement vers le bas la génération), définissez la `verifydownloads` l’attribut `false` dans les `<windowsazurepackage>` élément de package.xml : 

`<windowsazurepackage verifydownloads="false" ...>` 

Si vous avez sélectionné l’option **charger automatiquement...** , puis dans la fenêtre de la console vous verrez les messages de génération signalement de la progression du téléchargement toutes les 5 secondes, chaque fois qu’un téléchargement est nécessaire.

<a name="ssl_offloading_properties"></a> 
### <a name="ssl-offloading-properties"></a>SSL décharger propriétés ###

Ouvrir le menu contextuel pour le rôle dans le volet Explorateur de projet de Eclipse et cliquez sur **Azure**, puis cliquez sur **Décharger SSL**. 

![][ic719481]

Dans cette boîte de dialogue, vous pouvez activer SSL décharger, ce qui vous permet d’activer facilement le support Hypertext Transfer Protocol sécurisé (HTTPS) dans votre déploiement Java sur Azure, sans avoir à configurer SSL dans votre serveur d’applications Java. Pour plus d’informations, voir [SSL décharger][] et [comment utiliser SSL décharger][].

## <a name="see-also"></a>Voir aussi ##

[Kit de ressources Azure pour Éclipse][]

[Installez le Kit de ressources de Azure pour Éclipse][]

[Création d’une Application World Hello pour Azure dans Éclipse][]

[Propriétés du projet Azure][]

[Liste des comptes de stockage Azure][]

Pour plus d’informations sur l’utilisation de Azure avec Java, voir le [Centre de développement Java Azure][].

<!-- URL List -->

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Portail de gestion Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Kit de ressources Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Propriétés du projet Azure]: http://go.microsoft.com/fwlink/?LinkID=699524
[Liste des comptes de stockage Azure]: http://go.microsoft.com/fwlink/?LinkID=699528
[Résumé du package com.Microsoft.windowsazure.ServiceRuntime]: http://azure.github.io/azure-sdk-for-java/com/microsoft/windowsazure/serviceruntime/package-summary.html
[Création d’une Application World Hello pour Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Déboguer une instance de rôle spécifique dans un déploiement de plusieurs instances]: http://go.microsoft.com/fwlink/?LinkID=699535#debugging_specific_role_instance
[Le débogage des Applications Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699535
[Déploiement des déploiements]: http://go.microsoft.com/fwlink/?LinkID=699536
[Comment utiliser la mise en cache situés]: http://go.microsoft.com/fwlink/?LinkID=699542
[Comment utiliser SSL décharger]: http://go.microsoft.com/fwlink/?LinkID=699545
[Installez le Kit de ressources de Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Session affinité]: http://go.microsoft.com/fwlink/?LinkID=699548
[Décharger SSL]: http://go.microsoft.com/fwlink/?LinkID=699549

<!-- IMG List -->

[ic789599]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789599.png
[ic719499]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719499.png
[ic719483]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719483.png
[ic719501]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719501.png
[ic710964]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710964.png
[ic719502]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719502.png
[ic719503]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719503.png
[ic719504]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719504.png
[ic719505]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719505.png
[ic710897]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710897.png
[ic719506]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719506.png
[ic659268]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic659268.png
[ic552233]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic552233.png
[ic719492]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719492.png
[ic719508]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719508.png
[ic780647]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780647.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic780648]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780648.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic796926]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic796926.png
[ic719512]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719512.png
[ic719481]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719481.png
