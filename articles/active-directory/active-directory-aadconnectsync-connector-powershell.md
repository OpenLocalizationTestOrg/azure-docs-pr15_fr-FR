<properties
   pageTitle="Connecteur PowerShell | Microsoft Azure"
   description="Cet article décrit la configuration Windows PowerShell connecteur de Microsoft."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="windows-powershell-connector-technical-reference"></a>Guide de référence technique Windows PowerShell connecteur
Cet article décrit connecteur Windows PowerShell. L’article s’applique aux produits suivants :

- Gestionnaire d’identité Microsoft 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   Devez utiliser correctif 4.1.3671.0 ou version ultérieure [KB3092178](https://support.microsoft.com/kb/3092178).

Pour MIM2016 et FIM2010R2, le lien n’est disponible au téléchargement à partir du [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-powershell-connector"></a>Vue d’ensemble du connecteur PowerShell
Le connecteur PowerShell vous permet d’intégrer le service de synchronisation avec les systèmes externes qui proposent des API basées sur Windows PowerShell. Le connecteur fournit un pont entre les fonctionnalités de l’agent de gestion extensible connectivity basé sur un appel 2 framework (ECMA2) et Windows PowerShell. Pour plus d’informations sur l’infrastructure d’ECMA, voir la [Extensible Connectivity 2.2 Gestion Agent de référence](https://msdn.microsoft.com/library/windows/desktop/hh859557.aspx).

### <a name="prerequisites"></a>Conditions préalables
Avant d’utiliser le connecteur, vérifiez que vous disposez des actions suivantes sur le serveur de synchronisation :

- 4.5.2 de Microsoft .NET Framework ou version ultérieure
- Windows PowerShell 2.0, 3.0 ou 4.0

La stratégie d’exécution sur le serveur de Service de synchronisation doit être configurée pour autoriser le connecteur à exécuter des scripts Windows PowerShell. Sauf si les scripts de l’exécution du connecteur est signées numériquement, configurez la stratégie d’exécution en exécutant cette commande :  
`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

## <a name="create-a-new-connector"></a>Créer un connecteur
Pour créer un connecteur de Windows PowerShell dans le service de synchronisation, vous devez fournir une série de scripts Windows PowerShell qui s’exécutent les étapes requises par le service de synchronisation. En fonction de vous connectez à la source de données et les fonctionnalités que vous avez besoin, les scripts que vous devez implémenter varie. Cette section décrit chacun des scripts qui peuvent être implémentées et lorsqu’ils sont requis.

Le connecteur Windows PowerShell est conçu pour stocker chacune des scripts à l’intérieur de la base de données de Service de synchronisation. S’il est possible d’exécuter des scripts qui sont stockées sur le système de fichiers, il est plus simple d’insérer le corps de chaque script directement dans à la configuration du connecteur.

Pour créer un connecteur de PowerShell, sélectionnez **Agent de gestion** et **créer**dans **Le Service de synchronisation** . Sélectionnez le lien **PowerShell (Microsoft)** .

![Créer le lien](./media/active-directory-aadconnectsync-connector-powershell/createconnector.png)

### <a name="connectivity"></a>Connectivité
Fournir les paramètres de configuration pour vous connecter à un système distant. Ces valeurs sont en toute sécurité stockés par le Service de synchronisation et mis à disposition pour vos scripts Windows PowerShell lorsque celui-ci est exécuté.

![Connectivité](./media/active-directory-aadconnectsync-connector-powershell/connectivity.png)

Vous pouvez configurer les paramètres de connectivité suivants :

**Connectivité**

Paramètre | Valeur par défaut | Objectif
--- | --- | ---
Serveur | <Blank> | Nom du serveur auquel le lien doit se connecter à.
Domaine | <Blank> | Domaine de l’information d’identification pour stocker pour une utilisation lorsque le connecteur est exécuté.
Utilisateur | <Blank> | Nom d’utilisateur de l’information d’identification pour stocker pour une utilisation lorsque le connecteur est exécuté.
Mot de passe | <Blank> | Mot de passe de l’information d’identification pour stocker pour une utilisation lorsque le connecteur est exécuté.
Imiter le compte de connecteur | Faux | Valeur True indique que le service de synchronisation exécute les scripts Windows PowerShell dans le contexte des informations d’identification fournies. Si possible, il est recommandé que le paramètre **$Credentials** est passé à chaque script est utilisé à la place de l’emprunt d’identité. Pour plus d’informations sur les autorisations supplémentaires qui sont requises pour utiliser cette option, voir [Configuration supplémentaires pour l’emprunt d’identité](#additional-configuration-for-impersonation).
Charger le profil utilisateur lors de l’emprunt d’identité | Faux | Force Windows pour charger le profil d’utilisateur d’informations d’identification du connecteur au cours de l’emprunt d’identité. Si l’utilisateur représenté a un profil itinérant, le connecteur ne charge pas le profil d’itinérant. Pour plus d’informations sur les autorisations supplémentaires qui sont requises pour utiliser ce paramètre, voir [Configuration supplémentaires pour l’emprunt d’identité](#additional-configuration-for-impersonation).
Type d’ouverture de session lorsque l’emprunt d’identité | Aucun | Type de connexion au cours de l’emprunt d’identité. Pour plus d’informations, reportez-vous à la [dwLogonType] [ dw] documentation.
Scripts signés uniquement | Faux | Valeur True indique que le connecteur Windows PowerShell valide que chaque script possède une signature numérique valide. Si faux, assurez-vous que stratégie d’exécution du serveur de Service de synchronisation Windows PowerShell est RemoteSigned ou non restreint.

**Module commun**  
Le lien vous permet de stocker un module Windows PowerShell partagé dans la configuration. Lorsque le connecteur exécute un script, le module Windows PowerShell est extrait dans le système de fichiers de sorte qu’il peut être importé par chaque script.

Pour les scripts importer, exporter et la synchronisation de mot de passe, le module commun est extrait dans le dossier du connecteur MAData. Pour les scripts de découverte de schéma, la Validation, hiérarchie et Partition, le module commun est extrait dans le dossier % Temp%. Dans les deux cas, le script Module commun extrait est nommé en fonction du paramètre nom de Script Module commun.

Pour charger un module appelé FIMPowerShellConnectorModule.psm1 à partir du dossier MAData, utilisez l’instruction suivante :`Import-Module (Join-Path -Path [Microsoft.MetadirectoryServices.MAUtils]::MAFolder -ChildPath "FIMPowerShellConnectorModule.psm1")`

Pour charger un module appelé FIMPowerShellConnectorModule.psm1 dans le dossier % Temp%, utilisez l’instruction suivante :`Import-Module (Join-Path -Path $env:TEMP -ChildPath "FIMPowerShellConnectorModule.psm1")`

**Validation des paramètres**  
Le Script de Validation est un script Windows PowerShell facultatif qui peut être utilisé pour vous assurer que les paramètres de configuration des connecteurs fournis par l’administrateur sont valides. Le serveur de validation, les informations d’identification de connexion et les paramètres de connectivité sont usages courants du script de validation. Le script de validation est appelé après les onglets suivants et boîtes de dialogue sont modifiés :

- Connectivité
- Paramètres globaux
- Configuration des partitions

Le script de validation reçoit les paramètres suivants du connecteur :

Nom | Type de données | Description
--- | --- | ---
ConfigParameterPage | [ConfigParameterPage][cpp] | La boîte de dialogue qui s’affiche suite à la demande de validation ou l’onglet configuration.
ConfigParameters | [KeyedCollection] [ keyk] [de chaîne, [ConfigParameter][cp]] | Table des paramètres de configuration du connecteur.
Informations d’identification | [PSCredential][pscred] | Contient des informations d’identification entrées par l’administrateur de l’onglet connectivité.

Le script de validation doit renvoyer un seul objet ParameterValidationResult au pipeline.

**Découverte de schéma**  
Le script de découverte de schéma est obligatoire. Ce script renvoie les types d’objets, les attributs et les contraintes de l’attribut par le Service de synchronisation lors de la configuration des règles de flux des attributs. Le script de découverte de schéma s’exécute lors de la création de connecteur et remplit schéma du connecteur. Il est également utilisée par l’action Actualiser le schéma dans le Gestionnaire de services de synchronisation.

Le script de découverte de schéma reçoit les paramètres suivants du connecteur :

Nom | Type de données | Description
--- | --- | ---
ConfigParameters | [KeyedCollection] [ keyk] [de chaîne, [ConfigParameter][cp]] | Table des paramètres de configuration du connecteur.
Informations d’identification | [PSCredential][pscred] | Contient des informations d’identification entrées par l’administrateur de l’onglet connectivité.

Le script doit renvoyer un seul [schéma] [ schema] objet dans le pipeline. L’objet de schéma est composée de [SchemaType] [ schemaT] objets qui représentent des types d’objet (par exemple : utilisateurs et groupes). L’objet SchemaType contient une collection de [SchemaAttribute] [ schemaA] objets qui représentent les attributs (par exemple : prénom, nom et adresse postale) du type.

**Paramètres supplémentaires**  
Outre les paramètres de configuration standard, vous pouvez définir des paramètres de configuration personnalisés supplémentaires qui sont spécifiques à l’instance du connecteur. Ces paramètres peuvent être définis au niveau du connecteur, partition, ou exécuter étape niveaux et accessible à partir du script Windows PowerShell pertinent. Paramètres de configuration personnalisés peuvent être stockés dans la base de données de Service de synchronisation au format texte brut ou qu’ils peuvent être chiffrés. Le Service de synchronisation automatiquement chiffre et déchiffre sécuriser les paramètres de configuration lorsque cela est nécessaire.

Pour spécifier les paramètres de configuration personnalisés, séparez le nom de chaque paramètre par une virgule (,).

Pour accéder aux paramètres de configuration personnalisés à partir d’un script, vous devez suffixe le nom avec un trait de soulignement ( \_ ) et l’étendue du paramètre (Global, Partition ou RunStep). Par exemple, pour accéder au paramètre nom de fichier Global, utilisez cet extrait de code :`$ConfigurationParameters["FileName_Global"].Value`

### <a name="capabilities"></a>Fonctionnalités
L’onglet fonctionnalités du Concepteur de l’Agent de gestion de définit le comportement et les fonctionnalités du connecteur. Les sélections effectuées dans cet onglet ne sont pas modifiables lorsque le connecteur a été créé. Ce tableau répertorie les paramètres de fonction.

![Fonctionnalités](./media/active-directory-aadconnectsync-connector-powershell/capabilities.png)

Fonction | Description |
--- | --- |
[Style de nom unique][dnstyle] | Indique si le connecteur prend en charge des noms uniques et si Oui, le style.
[Type d’exportation][exportT] | Détermine le type d’objets qui sont présentées au script d’exportation. <li>AttributeReplace – inclut l’ensemble des valeurs d’un attribut à valeurs multiples lorsque l’attribut change.</li><li>AttributeUpdate – inclut uniquement les delta à un attribut à valeurs multiples lors de l’attribut change.</li><li>MultivaluedReferenceAttributeUpdate - comprend un ensemble complet de valeurs pour les attributs à valeurs multiples non-référence et delta uniquement pour les attributs de référence à valeurs multiples.</li><li>ObjectReplace – inclut tous les attributs d’un objet lorsque les modifications d’attribut</li>
[Normalisation des données][DataNorm] | Indique le Service de synchronisation pour normaliser les attributs d’ancrage avant qu’ils sont fournis aux scripts.
[Confirmation d’objet][oconf] | Configure le comportement d’importation en attente dans le Service de synchronisation. <li>Normal – le comportement par défaut qui attend exportées toutes les modifications apportées à être confirmé via importation</li><li>NoDeleteConfirmation – lorsqu’un objet est supprimé, il n’est pas générée d’importation en attente.</li><li>NoAddAndDeleteConfirmation – lorsqu’un objet est créé ou supprimé, il n’est pas générée d’importation en attente.</li>
Utiliser un nom unique en tant que point d’ancrage | Si le Style de nom unique est défini sur LDAP, l’attribut d’ancrage pour l’espace de connecteur est également le nom unique.
Opérations simultanées de plusieurs connecteurs | Lors de la vérification, plusieurs connecteurs de Windows PowerShell peuvent s’exécuter simultanément.
Partitions | Lors de la vérification, le connecteur prend en charge plusieurs partitions et découverte partition.
Hiérarchie | Lors de la vérification, le connecteur prend en charge une structure hiérarchique du style LDAP.
Activer l’importation | Lors de la vérification, le connecteur importe des données via des scripts importer.
Activer l’importation Delta | Lors de la vérification, le connecteur peut demander delta à partir des scripts importer.
Activer l’exportation | Lors de la vérification, le connecteur exporte les données via des scripts d’exportation.
Activer l’exportation complète | Lors de la vérification, les scripts d’exportation prend en charge l’exportation de l’espace connecteur entière. Pour utiliser cette option, l’activer exporter doit également être activée.
Aucune valeur de référence dans la première exportation passe | Lors de la vérification, les attributs de référence sont exportés dans un deuxième passage d’exportation.
Activer objet renommer | Lors de la vérification, des noms uniques peuvent être modifiés.
Ajouter supprimer que de le remplacer | Lors de la vérification, supprimer-ajouter opérations sont exportées sous forme d’une seule valeur de remplacement.
Activer les opérations de mot de passe | Lors de la vérification, les scripts de synchronisation de mot de passe sont pris en charge.
Activer le mot de passe dans la première passe exportation | Lors de la vérification, les mots de passe défini pendant la mise en service sont exportés lorsque l’objet est créé.

### <a name="global-parameters"></a>Paramètres globaux
L’onglet Paramètres globaux dans le Concepteur de l’Agent de gestion vous permet de configurer les scripts Windows PowerShell qui sont exécutés par le connecteur. Vous pouvez également configurer des valeurs globales pour les paramètres de configuration personnalisés définis sous l’onglet connectivité.

**Découverte de partition**  
Une partition est un espace de noms distinct au sein d’un schéma partagé. Par exemple, dans Active Directory, chaque domaine est une partition au sein d’une forêt. Une partition est le regroupement logique pour importer et exporter des opérations. Importer et exporter ont partition que s’est-il passé un contexte et toutes les opérations dans ce contexte. Partitions sont supposés pour représenter une hiérarchie dans l’annuaire LDAP. Le nom d’une partition unique est utilisé pour vérifier que tous les objets retournés sont dans le cadre d’une partition dans importer. Nom unique partition est également utilisé lors de la configuration du métaverse vers l’espace connecteur pour déterminer la partition de qu'un objet doit être associé lors de l’exportation.

Le script de découverte partition reçoit les paramètres suivants du connecteur :

Nom | Type de données | Description
--- | --- | ---
ConfigParameters  | [KeyedCollection][keyk][de chaîne, [ConfigParameter][cp]] | Table des paramètres de configuration pour le lien.
Informations d’identification | [PSCredential][pscred] | Contient des informations d’identification entrées par l’administrateur de l’onglet connectivité.

Le script doit renvoyer un soit une seule [Partition] [ part] objet ou une liste [T] d’objets Partition dans le pipeline.

**Découverte de la hiérarchie**  
Le script de découverte de hiérarchie est utilisé uniquement lorsque la fonctionnalité de Style de nom unique est LDAP. Le script est utilisé pour vous permettent de parcourir et de sélectionner un ensemble de conteneurs est considéré comme dans ou hors de portée pour importer et exporter des opérations. Le script doit fournir uniquement une liste des nœuds qui sont des enfants directs du nœud racine fourni pour le script.

Le script de découverte de hiérarchie reçoit les paramètres suivants du connecteur :

Nom | Type de données | Description
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][de chaîne, [ConfigParameter][cp]] | Table des paramètres de configuration du connecteur.
Informations d’identification | [PSCredential][pscred] | Contient des informations d’identification entrées par l’administrateur de l’onglet connectivité.
ParentNode | [HierarchyNode][hn] | Le nœud racine de la hiérarchie dans lesquelles le script doit renvoyer des enfants directs.

Le script doit renvoyer un soit un objet HierarchyNode enfant unique ou une liste [T] d’objets HierarchyNode enfants au pipeline.

#### <a name="import"></a>Importation
Connecteurs qui prennent en charge les opérations d’importation doivent implémenter trois scripts.

**Commencer l’importation**  
Le script d’importation de début est exécuté au début d’une étape d’importation exécuter. Au cours de cette étape, vous pouvez établir une connexion au système source et effectuez les étapes de préparation avant d’importer des données à partir du système connecté.

Le script d’importation début reçoit les paramètres suivants du connecteur :

Nom | Type de données | Description
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][de chaîne, [ConfigParameter][cp]] | Table des paramètres de configuration du connecteur.
Informations d’identification | [PSCredential][pscred] | Contient des informations d’identification entrées par l’administrateur de l’onglet connectivité.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Vous informe le script sur le type d’importation exécuter (delta ou version complète), partition, hiérarchie, filigrane et la taille de page attendue.
Types de | [Schéma][schema] | Schéma de l’espace de connecteur est importée.

Le script doit renvoyer une seule [OpenImportConnectionResults] [ oicres] objet dans le pipeline, par exemple :`Write-Output (New-Object Microsoft.MetadirectoryServices.OpenImportConnectionResults)`

**Importer des données**  
Le script d’importation de données est appelé par le connecteur jusqu'à ce que le script indique qu’il n’existe plus aucune donnée à importer. Le connecteur Windows PowerShell possède une taille de page de 9 999 objets. Si votre script renvoie plus de 9 999 objets pour l’importation, vous devez prend en charge pagination. Expose de connecteur une propriété de données personnalisée que vous pouvez utiliser pour un magasin un filigrane afin que chaque fois que le script d’importation de données est appelée, votre script reprend importation d’objets dans lequel il étiez arrêté.

Le script d’importation de données reçoit les paramètres suivants du connecteur :

Nom | Type de données | Description
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][de chaîne, [ConfigParameter][cp]] | Table des paramètres de configuration du connecteur.
Informations d’identification | [PSCredential][pscred] | Contient des informations d’identification entrées par l’administrateur de l’onglet connectivité.
GetImportEntriesRunStep | [ImportRunStep][irs] | Contient le filigrane (CustomData) qui peut être utilisé pendant pagination importations et delta importe.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Vous informe le script sur le type d’importation exécuter (delta ou version complète), partition, hiérarchie, filigrane et la taille de page attendue.
Types de | [Schéma][schema] | Schéma de l’espace de connecteur est importée.

Le script d’importation de données doit écrire une liste [[CSEntryChange][csec]] objet dans le pipeline. Cette collection est composée des attributs CSEntryChange qui représentent chaque objet à importer. Lors d’une exécution importation complète, cette collection de sites doit avoir un ensemble complet d’objets CSEntryChange ayant tous les attributs de chaque objet. Au cours d’une importation Delta, l’objet CSEntryChange doit contenir le delta niveau attribut pour chaque objet à importer, ou une représentation complète des objets qui ont été modifiées (mode remplacer).

**Importation de fin**  
À l’issue de l’importation exécuter, le script importer de fin est exécuté. Ce script doit effectuer les tâches de nettoyage nécessaires (par exemple, fermer les connexions aux systèmes) et y répondre aux échecs.

Le script d’importation fin reçoit les paramètres suivants du connecteur :

Nom | Type de données | Description
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][de chaîne, [ConfigParameter][cp]] | Table des paramètres de configuration du connecteur.
Informations d’identification | [PSCredential][pscred] | Contient des informations d’identification entrées par l’administrateur de l’onglet connectivité.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Vous informe le script sur le type d’importation exécuter (delta ou version complète), partition, hiérarchie, filigrane et la taille de page attendue.
CloseImportConnectionRunStep | [CloseImportConnectionRunStep][cecrs] | Vous informe le script sur la raison pour laquelle que l’importation a été interrompue.

Le script doit renvoyer une seule [CloseImportConnectionResults] [ cicres] objet dans le pipeline, par exemple :`Write-Output (New-Object Microsoft.MetadirectoryServices.CloseImportConnectionResults)`

#### <a name="export"></a>Exporter
Identique à l’architecture d’importation du connecteur, connecteurs qui prennent en charge d’exportation doivent implémenter trois scripts.

**Commencer l’exportation**  
Le script d’exportation de début est exécuté au début d’une étape d’exportation exécuter. Au cours de cette étape, vous pouvez établir une connexion au système source et réaliser toutes les étapes de préparation avant d’exporter des données dans le système connecté.

Le script d’exportation de début reçoit les paramètres suivants du connecteur :

Nom | Type de données | Description
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][de chaîne, [ConfigParameter][cp]] | Table des paramètres de configuration du connecteur.
Informations d’identification | [PSCredential][pscred] | Contient des informations d’identification entrées par l’administrateur de l’onglet connectivité.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Vous informe le script sur le type d’exportation exécuter (delta ou version complète), partition, hiérarchie ou la taille de page attendue.
Types de | [Schéma][schema] | Schéma de l’espace de connecteur est exporté.

Le script doit renvoie aucun résultat dans le pipeline.

**Exporter des données**  
Le Service de synchronisation appelle le script d’exportation de données aussi souvent que nécessaire traiter toutes les exportations en attente. Si l’espace de connecteur a plus exportations en attente à la taille de la page du connecteur, le script d’exportation des données peut être appelé plusieurs fois et éventuellement plusieurs fois pour le même objet.

Le script d’exportation des données reçoit les paramètres suivants du connecteur :

Nom | Type de données | Description
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][de chaîne, [ConfigParameter][cp]] | Table des paramètres de configuration pour le lien.
Informations d’identification | [PSCredential][pscred] | Contient des informations d’identification entrées par l’administrateur de l’onglet connectivité.
CSEntries | IList[CSEntryChange][csec] | Liste de tout l’espace de connecteur objets avec en attente exportations soient traités pendant cette étape.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Vous informe le script sur le type d’exportation exécuter (delta ou version complète), partition, hiérarchie ou la taille de page attendue.
Types de | [Schéma][schema] | Schéma de l’espace de connecteur est exporté.

Le script d’exportation des données doit renvoyer un [PutExportEntriesResults] [ peeres] objet dans le pipeline. Cet objet n’a pas besoin d’inclure les informations de résultat pour chaque connecteur exporté à moins d’avoir une erreur ou une modification de l’attribut d’ancrage se produit. Par exemple, pour renvoyer un objet PutExportEntriesResults au pipeline :`Write-Output (New-Object Microsoft.MetadirectoryServices.PutExportEntriesResults)`

**Exportation de fin**  
À l’issue de l’exportation exécute, le script d’exportation de fin à exécuter. Ce script doit effectuer les tâches de nettoyage nécessaires (par exemple, fermer les connexions aux systèmes) et y répondre aux échecs.

Le script d’exportation fin reçoit les paramètres suivants du connecteur :

Nom | Type de données | Description
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][de chaîne, [ConfigParameter][cp]] | Table des paramètres de configuration du connecteur.
Informations d’identification | [PSCredential][pscred] | Contient des informations d’identification entrées par l’administrateur de l’onglet connectivité.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Vous informe le script sur le type d’exportation exécuter (delta ou version complète), partition, hiérarchie ou la taille de page attendue.
CloseExportConnectionRunStep | [CloseExportConnectionRunStep][cecrs] | Vous informe le script sur la raison pour laquelle que l’exportation a été interrompue.

Le script doit renvoie aucun résultat dans le pipeline.

#### <a name="password-synchronization"></a>Synchronisation de mot de passe
Windows PowerShell connecteurs peuvent être utilisés comme cible de modifications/réinitialisations de mot de passe.

Le script de mot de passe reçoit les paramètres suivants du connecteur :

Nom | Type de données | Description
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][de chaîne, [ConfigParameter][cp]] | Table des paramètres de configuration du connecteur.
Informations d’identification | [PSCredential][pscred] | Contient des informations d’identification entrées par l’administrateur de l’onglet connectivité.
Partition | [Partition][part] | Partition d’annuaire figurant dans la CSEntry.
CSEntry | [CSEntry][cse] | Entrée d’espace connecteur pour l’objet est reçu un changement de mot de passe ou le réinitialiser.
Type d’opération | Chaîne | Indique si l’opération est une réinitialisation (**SetPassword**) ou une modification (**ChangePassword**).
PasswordOptions | [PasswordOptions][pwdopt] | Indicateurs qui spécifient le mot de passe initial réinitialiser comportement. Ce paramètre est uniquement disponible si le type d’opération est **SetPassword**.
OldPassword | Chaîne | Renseignée à l’aide ancien mot de passe de l’objet pour que les modifications de mot de passe. Ce paramètre est uniquement disponible si le type d’opération est **ChangePassword**.
NouveauMotDePasse | Chaîne | Renseignée à l’aide nouveau mot de passe de l’objet que le script doit définir.

Le script de mot de passe n’est pas attendu pour retourner des résultats dans le pipeline de Windows PowerShell. Si une erreur se produit dans le script de mot de passe, le script doit lever parmi les exceptions suivantes pour informer le Service de synchronisation sur le problème :

- [PasswordPolicyViolationException] [ pwdex1] – levée si le mot de passe ne répond pas à la stratégie de mot de passe dans le système connecté.
- [PasswordIllFormedException] [ pwdex2] – levée si le mot de passe n’est pas valide pour le système connecté.
- [PasswordExtension] [ pwdex3] – levée pour toutes les autres erreurs dans le script de mot de passe.

## <a name="sample-connectors"></a>Exemple connecteurs
Pour une vue d’ensemble complet des connecteurs exemple disponible, voir [Windows PowerShell connecteur exemple Connecteur Collection][samp].

## <a name="other-notes"></a>Autres notes

### <a name="additional-configuration-for-impersonation"></a>Configuration supplémentaires pour l’emprunt d’identité
Autoriser l’utilisateur qui est représenté les autorisations suivantes sur le serveur de Service de synchronisation :

Accès en lecture aux clés de Registre suivantes :

- HKEY_USERS\\\Software\Microsoft\PowerShell [SynchronizationServiceServiceAccountSID]
- HKEY_USERS\\\Environment [SynchronizationServiceServiceAccountSID]

Pour déterminer l’identificateur de sécurité (SID) du compte de service Service de synchronisation, exécutez les commandes PowerShell suivantes :

```
$account = New-Object System.Security.Principal.NTAccount "<domain>\<username>"
$account.Translate([System.Security.Principal.SecurityIdentifier]).Value
```

Accès en lecture dans les dossiers de système de fichiers suivants :

- %ProgramFiles%\Microsoft forefront identité Manager\2010\Synchronization Service\Extensions
- %ProgramFiles%\Microsoft forefront identité Manager\2010\Synchronization Service\ExtensionsCache
- %ProgramFiles%\Microsoft forefront identité Manager\2010\Synchronization Service\MaData\\{ConnectorName}

Remplacez le nom du connecteur Windows PowerShell pour l’espace réservé {ConnectorName}.

## <a name="troubleshooting"></a>Résolution des problèmes

-   Pour plus d’informations sur la façon d’activer la journalisation résoudre les problèmes le connecteur, voir [comment activer le suivi ETW des connecteurs](http://go.microsoft.com/fwlink/?LinkId=335731).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[cpp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameterpage.aspx
[keyk]: https://msdn.microsoft.com/library/ms132438.aspx
[cp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameter.aspx
[pscred]: https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx
[schema]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schema.aspx
[schemaT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schematype.aspx
[schemaA]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schemaattribute.aspx
[dnstyle]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.madistinguishednamestyle.aspx
[exportT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maexporttype.aspx
[DataNorm]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.manormalizations.aspx
[oconf]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maobjectconfirmation.aspx
[dw]: https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx
[part]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.partition.aspx
[hn]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.hierarchynode.aspx
[oicrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionrunstep.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[oicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionresults.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[cicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeimportconnectionresults.aspx
[oecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openexportconnectionrunstep.aspx
[irs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.importrunstep.aspx
[cse]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentry.aspx
[csec]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentrychange.aspx
[peeres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.putexportentriesresults.aspx
[pwdopt]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordoptions.aspx
[pwdex1]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordpolicyviolationexception.aspx
[pwdex2]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordillformedexception.aspx
[pwdex3]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordextensionexception.aspx
[samp]: http://go.microsoft.com/fwlink/?LinkId=394291
