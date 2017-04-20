<properties
   pageTitle="Configuration de votre projet Azure à l’aide de plusieurs configurations de service | Microsoft Azure"
   description="Découvrez comment configurer un projet de service cloud Azure en modifiant les fichiers ServiceDefinition.csdef et ServiceConfiguration.cscfg."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="configuring-your-azure-project-using-multiple-service-configurations"></a>Configuration de votre projet Azure à l’aide de plusieurs Configurations de Service

Un projet de service cloud Azure inclut deux fichiers de configuration : ServiceDefinition.csdef et ServiceConfiguration.cscfg. Ces fichiers sont fournis avec votre application de service cloud Azure et déployés sur Azure.

- Le fichier **ServiceDefinition.csdef** contient les métadonnées qui sont requis par l’environnement Azure pour les besoins de votre application de service cloud, y compris les rôles qu’il contient. Ce fichier contient également les paramètres de configuration qui s’appliquent à toutes les instances. Ces paramètres de configuration peuvent être lus à l’exécution à l’aide de l’API Azure Service d’hébergement Runtime. Ce fichier ne peut pas être mis à jour lorsque votre service s’exécute dans Azure.

- Le fichier **ServiceConfiguration.cscfg** définit les valeurs pour les paramètres de configuration définis dans le fichier de définition de service et spécifie le nombre d’instances pour exécuter pour chaque rôle. Ce fichier peut être mis à jour lorsque votre service cloud s’exécute dans Azure.

Les outils Azure pour Microsoft Visual Studio fournissent des pages de propriétés que vous pouvez utiliser pour définir les paramètres de configuration stockés dans ces fichiers. Pour accéder aux pages de propriétés, double-cliquez sur la référence de rôle sous le projet de service cloud Azure dans l’Explorateur de solutions, ou avec le bouton droit de la référence de rôle, puis sélectionnez **Propriétés**, comme indiqué dans l’illustration suivante.

![VS_Solution_Explorer_Roles_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

Pour plus d’informations sur les schémas sous-jacente pour la définition de service et les fichiers de configuration de service, voir la [Référence de schéma](https://msdn.microsoft.com/library/azure/dd179398.aspx). Pour plus d’informations sur la configuration du service, voir [comment configurer les Services Cloud](./cloud-services/cloud-services-how-to-configure.md).

## <a name="configuring-role-properties"></a>Configuration des propriétés de rôle

Les pages de propriétés pour un rôle web et un rôle de collaborateur sont similaires, bien qu’il existe quelques différences, remarquer dans les sections suivantes.

Dans la page **mise en cache** , vous pouvez configurer le Azure mise en cache des services.

### <a name="configuration-page"></a>Page de configuration

Dans la page de **Configuration** , vous pouvez définir ces propriétés :

**Instances**

Définissez la propriété count **Instance** sur le nombre d’instances que le service doit s’exécuter pour ce rôle.

Définissez la propriété **taille de mémoire virtuelle** sur **Très petite**, **petite**, **moyenne**, **grande**ou **Très grande**.  Pour plus d’informations, voir [formats pour les Services en nuage](./cloud-services/cloud-services-sizes-specs.md).

**Action de démarrage** (Rôle web uniquement)

Définir cette propriété pour indiquer que Visual Studio doit lancer un navigateur web pour les points de terminaison HTTP ou les points de terminaison HTTPS lorsque vous démarrez le débogage.

L’option de point de terminaison HTTPS est disponible uniquement si vous avez déjà défini un point de terminaison HTTPS pour votre rôle. Vous pouvez définir un point de terminaison HTTPS sur la page de propriétés de **points de terminaison** .

Si vous avez déjà ajouté un point de terminaison HTTPS, l’option de point de terminaison HTTPS est activée par défaut et Visual Studio lancera un navigateur pour ce point de terminaison lorsque vous démarrez le débogage, en plus de navigateur sur votre point de terminaison HTTP. Cette procédure suppose que les deux options de démarrage sont activées.

**Diagnostics**

Par défaut, diagnostics est activé pour le rôle Web. Le compte de service cloud Azure project et de stockage sont définis pour utiliser l’émulateur de stockage local. Lorsque vous êtes prêt à déployer Azure, vous pouvez sélectionner le bouton Générateur (**...**) pour mettre à jour le compte de stockage pour utiliser le stockage Azure dans le cloud. Vous pouvez transférer les données de diagnostic pour le compte de stockage à la demande ou à intervalles réguliers automatiquement. Pour plus d’informations sur les diagnostics de Windows Azure, voir [Activation des Diagnostics dans Azure Cloud Services et Machines virtuelles](./cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="settings-page"></a>Page de paramètres

Dans la page **paramètres** , vous pouvez ajouter des paramètres de configuration de votre service. Paramètres de configuration sont paires nom-valeur. Code en cours d’exécution dans le rôle peut lire les valeurs de vos paramètres de configuration en cours d’exécution à l’aide de classes fournies par la [Bibliothèque managée Azure](http://go.microsoft.com/fwlink?LinkID=171026). Plus précisément, la méthode [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) renvoie la valeur d’un paramètre de configuration nommée en cours d’exécution.

### <a name="configuring-a-connection-string-to-a-storage-account"></a>Configuration d’une chaîne de connexion à un compte de stockage

Une chaîne de connexion est un paramètre de configuration qui fournit des informations de connexion et d’identification pour l’émulateur de stockage ou d’un compte de stockage Azure. Chaque fois que votre code doit accéder aux données de services de stockage Azure – c'est-à-dire blob, file d’attente ou données de la table – à partir de code en cours d’exécution dans un rôle, vous devez définir une chaîne de connexion pour ce compte de stockage.

Une chaîne de connexion qui pointe vers un compte de stockage Azure devez utiliser un format défini. Pour plus d’informations sur la façon de créer des chaînes de connexion, voir [Configurer les chaînes de connexion stockage Azure](./storage/storage-configure-connection-string.md).

Lorsque vous êtes prêt à tester votre service contre les services de stockage Azure, ou lorsque vous êtes prêt à déployer votre service cloud Azure, vous pouvez modifier la valeur de toutes les chaînes de connexion pour pointer vers votre compte de stockage Azure. Sélectionnez (**...**), sélectionnez les **informations d’identification de compte de stockage entrée**. Entrez vos informations de compte qui comprenant votre nom de compte et votre clé de compte. Dans la boîte de dialogue **Chaîne de connexion de compte de stockage** , vous pouvez également indiquer si vous voulez utiliser les points de terminaison HTTPS par défaut (option par défaut), points de terminaison HTTP par défaut ou les points de terminaison personnalisés. Vous pouvez décider d’utiliser des points de terminaison personnalisés si vous avez enregistré un nom de domaine personnalisé pour votre service, comme décrit dans [configurer un nom de domaine personnalisé pour les données blob dans un compte de stockage Azure](./storage/storage-custom-domain-name.md).

>[AZURE.IMPORTANT] Vous devez modifier vos chaînes de connexion pour pointer vers un compte de stockage Azure avant de déployer votre service. Le cas contraire peut entraîner votre rôle ne pas à démarrer, ou pour passer en revue les États lors de l’initialisation, occupé (e) et d’arrêt.

## <a name="endpoints-page"></a>Page points de terminaison

Un rôle de collaborateur peut avoir n’importe quel nombre de points de terminaison HTTP, HTTPS ou TCP. Points de terminaison peuvent être extrémités d’entrée, qui sont disponibles pour les clients externes, ou points de terminaison internes, qui sont disponibles à d’autres rôles qui sont exécutent dans le service.

- Pour qu’un point de terminaison HTTP disponible pour les clients externes et les navigateurs Web, modifiez le type de point de terminaison en entrée, puis spécifiez un nom et un numéro de port public.

- Pour qu’un point de terminaison HTTPS disponible pour les clients externes et les navigateurs Web, modifier le type de point de terminaison **d’entrée**, puis spécifiez un nom, un numéro de port public et un nom de certificat gestion.

    Notez qu’avant que vous puissiez spécifier un certificat de gestion, vous devez définir le certificat sur la page de propriétés de **certificats** .

- Pour qu’un point de terminaison disponible pour l’accès interne par d’autres rôles dans le service cloud, changez le type de point de terminaison interne et spécifiez un nom et ports privés possibles pour ce point de terminaison.

## <a name="local-storage-page"></a>Page de stockage local

Vous pouvez utiliser la page de propriétés de **Stockage Local** pour réserver une ou plusieurs des ressources de stockage local pour un rôle. Une ressource de stockage local est un répertoire réservé dans le système de fichiers de la machine virtuelle Azure dans lequel s’exécute une instance d’un rôle.

## <a name="certificates-page"></a>Page certificats

Dans la page **certificats** , vous pouvez associer des certificats à votre rôle. Les certificats que vous ajoutez peuvent être utilisés pour configurer vos points de terminaison HTTPS sur la page de propriétés de **points de terminaison** .

La page de propriétés de **certificats** ajoute des informations sur vos certificats à votre configuration du service. Notez que vos certificats ne sont pas compressées avec votre service ; Vous devez télécharger vos certificats séparément sur Azure via le [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

Pour associer un certificat à votre rôle, indiquez un nom pour le certificat. Vous utilisez ce nom pour faire référence au certificat lorsque vous configurez un point de terminaison HTTPS sur la page de propriétés de **points de terminaison** . Ensuite, indiquez si le magasin de certificats est **Ordinateur Local** ou **Utilisateur actuel** et le nom de la banque d’informations. Enfin, vous devez entrer l’empreinte du certificat. Si le certificat est dans l’utilisateur actuel\Personnel magasin (My), vous pouvez entrer l’empreinte du certificat en sélectionnant le certificat à partir d’une liste. Si elle se trouve dans un autre emplacement, entrez la valeur empreinte manuellement.

Lorsque vous ajoutez un certificat du magasin de certificats, les certificats intermédiaires sont automatiquement ajoutés aux paramètres de configuration pour vous. Ces certificats intermédiaires doivent également être téléchargés vers Azure afin de configurer correctement votre service pour SSL.

Les certificats de gestion associer votre service s’appliquent à votre service uniquement lorsqu’il est exécuté dans le cloud. Lorsque votre service est en cours d’exécution dans l’environnement de développement local, qu’il utilise un certificat standard est géré par l’émulateur cluster.

## <a name="configuring-the-azure-cloud-service-project"></a>Configuration du projet de service cloud Azure

Pour configurer des paramètres qui s’appliquent à un projet de service cloud Azure entière, vous ouvrez le menu contextuel pour ce nœud de projet pour la première fois, puis Propriétés pour ouvrir ses pages de propriétés. Le tableau suivant montre les pages de propriétés.

|Page de propriétés|Description|
|---|---|
|Application|À partir de cette page, vous pouvez afficher des informations sur la version d’outils Azure qui utilise ce projet de service cloud, et vous pouvez mettre à niveau vers la version actuelle des outils.|
|Événements de génération|À partir de cette page, vous pouvez définir des événements avant et après génération.|
|Développement|À partir de cette page, vous pouvez spécifier des instructions de configuration de build et les conditions dans lesquelles les événements post-build sont exécutés.|
|Web|À partir de cette page, vous pouvez configurer les paramètres relatifs au serveur web.|
