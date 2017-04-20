<properties
   pageTitle="Connecteur SQL générique | Microsoft Azure"
   description="Cet article décrit comment configurer le connecteur SQL générique de Microsoft."
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

# <a name="generic-sql-connector-technical-reference"></a>Référence technique connecteur SQL générique

Cet article décrit le connecteur SQL générique. L’article s’applique aux produits suivants :

- Gestionnaire d’identité Microsoft 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   Devez utiliser correctif 4.1.3671.0 ou version ultérieure [KB3092178](https://support.microsoft.com/kb/3092178).

Pour MIM2016 et FIM2010R2, le lien n’est disponible au téléchargement à partir du [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

Pour afficher ce connecteur en action, voir l’article [Connecteur SQL générique étape par étape](active-directory-aadconnectsync-connector-genericsql-step-by-step.md) .

## <a name="overview-of-the-generic-sql-connector"></a>Vue d’ensemble du connecteur SQL générique

Le connecteur SQL générique vous permet d’intégrer le service de synchronisation avec un système de base de données qui offre une connectivité ODBC.  

Du point de vue globale, les fonctionnalités suivantes sont prises en charge par la version actuelle du connecteur :

Fonctionnalité | Prise en charge
--- | ---
Source de données connectée | Le connecteur est pris en charge avec tous les pilotes ODBC 64 bits. Il a été testé avec les éléments suivants : <li>Microsoft SQL Server et SQL Azure</li><li>IBM DB2 10.x</li><li>IBM DB2 9.x</li><li>Oracle 10 et 11 g</li><li>MySQL 5.x</li>
Scénarios   | <li>Gestion du cycle de vie des objets</li><li>Gestion de mot de passe</li>
Opérations | <li>Importation complète et Delta importer, exporter</li><li>Pour exporter : Ajouter, supprimer, mettre à jour et remplacer</li><li>Définir le mot de passe, modifier le mot de passe</li>
Schéma | <li>Découverte dynamique des attributs et objets</li>

### <a name="prerequisites"></a>Conditions préalables
Avant d’utiliser le connecteur, vérifiez que vous disposez des actions suivantes sur le serveur de synchronisation :

- 4.5.2 de Microsoft .NET Framework ou version ultérieure
- pilotes de client ODBC 64 bits

### <a name="permissions-in-connected-data-source"></a>Autorisations dans la source de données connectée
Pour créer ou effectuez l’une des tâches pris en charge dans connecteur SQL générique, vous devez :

- db_datareader
- db_datawriter

### <a name="ports-and-protocols"></a>Ports et protocoles
Pour les ports requis pour le pilote ODBC à utiliser, consultez la documentation de base de données.

## <a name="create-a-new-connector"></a>Créer un connecteur
Pour créer un connecteur SQL générique, sélectionnez **Agent de gestion** et **créer**dans **Le Service de synchronisation** . Sélectionnez le connecteur **SQL générique (Microsoft)** .

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/createconnector.png)

### <a name="connectivity"></a>Connectivité
Le connecteur utilise un fichier DSN ODBC pour la connectivité. Créer le fichier DSN à l’aide de **Sources de données ODBC** figurant dans le menu Démarrer, sous **Outils d’administration**. Dans l’outil d’administration, créez un **Fichier DSN** afin qu’il peut être fourni pour le lien.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/connectivity.png)

L’écran de connexion est le premier lorsque vous créez un nouveau connecteur SQL générique. Vous devez tout d’abord fournir les informations suivantes :

- Chemin d’accès du fichier DSN
- Authentification
    - Nom d’utilisateur
    - Mot de passe

La base de données doit prendre en charge une de ces méthodes d’authentification :

- **L’authentification Windows**: la base de données authentification utilise les informations d’identification Windows pour vérifier l’utilisateur. Le nom/mot de passe utilisateur spécifié est utilisé pour vous authentifier avec la base de données. Ce compte a besoin d’autorisations pour la base de données.
- **Authentification SQL**: l’authentification utilise de base de données le nom d’utilisateur/mot de passe défini une l’écran de connexion pour vous connecter à la base de données. Si vous stockez le nom d’utilisateur/mot de passe dans le fichier DSN, les informations d’identification fournies sur l’écran de connectivité ont la priorité.
- **Authentification de base de données SQL Azure**: pour plus d’informations, voir [se connecter à SQL de base de données en utilisant Azure authentification Active Directory](..\sql-database\sql-database-aad-authentication.md).

**Nom de domaine est ancre**: Si vous sélectionnez cette option, le nom de domaine est également utilisé comme l’attribut d’ancrage. Il peut être utilisée pour une mise en œuvre simple mais présente également les restrictions suivantes :

-   Connecteur prend en charge qu’un seul type d’objet. Par conséquent, les attributs de référence ne peuvent référencer le même type d’objet.

**Type d’exportation : objet remplacer**: lors de l’exportation, lorsque uniquement certains attributs ont changé, l’objet entier avec tous les attributs est exporté et remplace l’objet existant.

### <a name="schema-1-detect-object-types"></a>Schéma 1 (types d’objets détecter)
Dans cette page, vous allez à configurer la manière dont le connecteur va rechercher les différents types d’objets dans la base de données.

Chaque type d’objet est présenté comme une partition et configuré supplémentaire sur la **Configuration des Partitions et des hiérarchies**.

![schema1a](./media/active-directory-aadconnectsync-connector-genericsql/schema1a.png)

**Méthode de détection de Type d’objet**: le connecteur prend en charge ces méthodes de détection de type objet.

- **Valeur fixe**: vous fournissez la liste des types d’objets avec une liste séparée par des virgules. Par exemple : `User,Group,Department`.  
![Schema1b](./media/active-directory-aadconnectsync-connector-genericsql/schema1b.png)
- **Procédure table/affichage/stockée**: indiquez le nom de la procédure de table/vue/stockée, puis sur le nom de colonne qui fournit la liste des types d’objets. Si vous utilisez une procédure stockée, puis également fournir des paramètres de celui-ci au format **[nom] : [Direction] : [valeur]**. Fournir chaque paramètre sur une ligne distincte (utilisez Ctrl + Entrée pour obtenir une nouvelle ligne).  
![schema1c](./media/active-directory-aadconnectsync-connector-genericsql/schema1c.png)
- **Requête SQL**: cette option vous permet de fournir une requête SQL qui retourne une seule colonne avec des types d’objets, par exemple `SELECT [Column Name] FROM TABLENAME`. La colonne renvoyée doit être de type chaîne (varchar).

### <a name="schema-2-detect-attribute-types"></a>Schéma 2 (types d’attribut détecter)
Dans cette page, vous allez à configurer la façon dont les noms des attributs et les types vont être détectées. Les options de configuration sont répertoriées pour chaque type d’objet détecté sur la page précédente.

![schema2a](./media/active-directory-aadconnectsync-connector-genericsql/schema2a.png)

**Méthode de détection de Type d’attribut**: le connecteur prend en charge ces méthodes de détection de type attribut avec chaque type d’objet détecté dans écran schéma 1.

- **Procédure table/affichage/stockée**: indiquez le nom de la procédure de table/vue/stockée qui doit être utilisé pour rechercher les noms des attributs. Si vous utilisez une procédure stockée, puis également fournir des paramètres de celui-ci au format **[nom] : [Direction] : [valeur]**. Fournir chaque paramètre sur une ligne distincte (utilisez Ctrl + Entrée pour obtenir une nouvelle ligne). Pour détecter les noms des attributs dans un attribut à valeurs multiples, fournissent une liste de Tables ou vues séparées par des virgules. Scénarios à plusieurs valeurs ne sont pas prises en charge lorsque table parent / enfant ont les mêmes noms de colonne.
- **Requête SQL**: cette option vous permet de fournir une requête SQL qui retourne une seule colonne avec les noms d’attributs, par exemple `SELECT [Column Name] FROM TABLENAME`. La colonne renvoyée doit être de type chaîne (varchar).

### <a name="schema-3-define-anchor-and-dn"></a>Schéma 3 (point d’ancrage définir et nom de domaine)
Cette page vous permet de vous permettent de configurer le point d’ancrage et attribut nom unique pour chaque type d’objet détecté. Vous pouvez sélectionner plusieurs attribut pour rendre le point d’ancrage unique.

![schema3a](./media/active-directory-aadconnectsync-connector-genericsql/schema3a.png)

- Attributs à valeurs multiples et booléens ne sont pas répertoriées.
- Impossible d’utiliser une même attribut pour nom de domaine et point d’ancrage, à moins que le **nom de domaine est ancre** est sélectionné dans la page connectivité.
- Si le **nom de domaine est le point d’ancrage** est sélectionné dans la page de connexion, cette page nécessite uniquement l’attribut de nom de domaine. Cet attribut aurait également être utilisé comme l’attribut d’ancrage.
![schema3b](./media/active-directory-aadconnectsync-connector-genericsql/schema3b.png)

### <a name="schema-4-define-attribute-type-reference-and-direction"></a>Schéma 4 (type d’attribut définir, référence et direction)
Cette page vous permet de vous permettent de configurer le type d’attribut, tel qu’entier, binaire, ou booléen et direction de chaque attribut. Tous les attributs à partir de la page **schéma 2** sont répertoriés, y compris les attributs à valeurs multiples.

![schema4a](./media/active-directory-aadconnectsync-connector-genericsql/schema4a.png)

- **Type de données**: utilisé pour mapper le type d’attribut à ces types connus par le moteur de synchronisation. La valeur par défaut consiste à utiliser le même type comme détectée dans le schéma SQL, mais date/heure et référence ne sont pas facilement détectable. Pour ceux, vous devez spécifier **DateTime** ou une **référence**.
- **Direction**: vous pouvez définir l’orientation de l’attribut à importer, exporter ou ImportExport. ImportExport est la valeur par défaut.
![schema4b](./media/active-directory-aadconnectsync-connector-genericsql/schema4b.png)

Notes :

- Si un type d’attribut n’est pas détectable par le connecteur, elle utilise le type de données chaîne.
- **Tables imbriquées** peut être considéré comme des tables de base de données d’une colonne. Oracle stocke les lignes d’une table imbriquée dans aucun ordre en particulier. Toutefois, lorsque vous récupérez la table imbriquée dans une variable PL/SQL, les lignes sont associées indices consécutives commençant par 1. Qui vous permet d’accéder de type tableau à des lignes individuelles.
- **VARRYS** ne sont pas prises en charge dans le lien.

### <a name="schema-5-define-partition-for-reference-attributes"></a>Schéma 5 (partition définir des attributs de référence)
Sur cette page, vous configurez tous les attributs de référence (type d’objet) partition dans laquelle un attribut fait référence à.

![Schema5](./media/active-directory-aadconnectsync-connector-genericsql/schema5.png)

Si vous utilisez le **nom de domaine est ancre**, vous devez utiliser le même type d’objet que vous faites référence à partir de. Vous ne pouvez pas faire référence à un autre type d’objet.

### <a name="global-parameters"></a>Paramètres globaux
La page de paramètres globaux sert à configurer Delta importer, le format de Date/heure et méthode mot de passe.

![globalparameters1](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters1.png)

Le connecteur SQL générique prend en charge les méthodes suivantes pour l’importation Delta :

- **Déclencheur**: consultez [génération affichages Delta à l’aide de déclencheurs](https://technet.microsoft.com/library/cc708665.aspx).
- **Filigrane**: une approche générique qui peut être utilisée avec une base de données. La requête filigrane est pré-rempli basées sur le fournisseur de base de données. Une colonne de filigrane doit être présente sur chaque table/vue utilisée. Cette colonne doit effectuer le suivi des insertions et mises à jour les tables en tant qu’et ses dépendant (valeurs multiples ou enfant) tables. L’horloge entre le Service de synchronisation et le serveur de base de données doit être synchronisé. Si ce n’est pas le cas, certaines entrées dans l’importation delta peuvent être omises.  
Limitation :
    - Stratégie de filigrane ne prend pas les objets de prise en charge supprimé.
- **Instantané**: (fonctionne uniquement avec Microsoft SQL Server) [génération Delta affichages à l’aide des instantanés](https://technet.microsoft.com/library/cc720640.aspx)
- **Suivi des modifications**: (fonctionne uniquement avec Microsoft SQL Server) [sur le suivi des modifications](https://msdn.microsoft.com/library/bb933875.aspx)  
Limitations :
    - Ancrer la & attribut de nom de domaine doivent faire partie de clé primaire de l’objet sélectionné dans la table.
    - Requête SQL non pris en charge pendant l’importation et exportation avec suivi des modifications.

**Paramètres supplémentaires**: spécifiez le fuseau horaire du serveur de base de données indiquant où se trouve votre serveur de base de données. Cette valeur est utilisée pour prendre en charge les différents formats de date et heure attributs.

Le connecteur stocke toujours les date et heure de la date au format UTC. Pour être en mesure de convertir correctement la date et l’heure, le fuseau horaire du serveur de base de données et le format utilisé doit être spécifié. Le format doit être exprimé au format .net.

Lors de l’exportation chaque attribut d’heure date doit être fourni pour le connecteur dans le format de l’heure au format UTC.

![globalparameters2](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters2.png)

**Configuration de mot de passe**: le connecteur comporte des fonctionnalités de synchronisation de mot de passe et prend en charge définie et modifier votre mot de passe.

Le connecteur propose deux méthodes pour prendre en charge de la synchronisation de mot de passe :

- **Procédure stockée**: cette méthode nécessite deux procédures stockées pour prendre en charge de définir et modifier votre mot de passe. Tapez tous les paramètres pour ajouter et modifier l’opération de mot de passe dans **Définir la SP de mot de passe** et les paramètres de **Modifier votre mot de passe SP** respectivement comme par exemple ci-dessous.
![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters3.png)
- **Extension de mot de passe**: cette méthode nécessite DLL (vous devez fournir le nom de la DLL Extension qui est mise en œuvre l’interface [IMAExtensible2Password](https://msdn.microsoft.com/library/microsoft.metadirectoryservices.imaextensible2password.aspx) ) d’extension de mot de passe. Assembly d’extension de mot de passe doit être placé dans le dossier d’extension pour que le connecteur puisse charger la DLL en cours d’exécution.
![globalparameters4](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters4.png)

Vous devez également activer la gestion de mot de passe dans la page **Configurer l’Extension** .
![globalparameters5](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters5.png)

### <a name="configure-partitions-and-hierarchies"></a>Configurez des Partitions et hiérarchies
Dans la page partitions et hiérarchies, sélectionnez tous les types d’objets. Chaque type d’objet est sa propre partition.

![partitions1](./media/active-directory-aadconnectsync-connector-genericsql/partitions1.png)

Vous pouvez également remplacer les valeurs définies dans la page de **connectivité** ou de **Paramètres globaux** .

![partitions2](./media/active-directory-aadconnectsync-connector-genericsql/partitions2.png)

### <a name="configure-anchors"></a>Configurer les points d’ancrage
Cette page est en lecture seule dans la mesure où le point d’ancrage a déjà été défini. L’attribut d’ancrage sélectionnés est toujours ajoutée avec le type d’objet pour vous assurer qu’il reste unique pour tous les types d’objets.

![points d’ancrage](./media/active-directory-aadconnectsync-connector-genericsql/anchors.png)

## <a name="configure-run-step-parameter"></a>Configurer le paramètre d’exécution étape
Ces étapes sont configurés sur les profils d’exécution sur le lien. Ces configurations faire le travail réel de l’importation et exportation de données.

### <a name="full-and-delta-import"></a>Complète et importer Delta
Générique prise en charge de connecteur SQL complète et importation de Delta à l’aide des méthodes suivantes :

- Table
- Affichage
- Procédure stockée
- Requête SQL

![runstep1](./media/active-directory-aadconnectsync-connector-genericsql/runstep1.png)

**Table ou une vue**  
Pour importer des attributs à valeurs multiples d’un objet, vous devez fournir le nom de table/vue séparées par des virgules dans les **affichages/tableau nom d’à valeurs multiples** et conditions de jointure correspondantes dans la **condition de jointure** avec la table parent.

Exemple : Que vous voulez importer l’objet employé et tous ses attributs à valeurs multiples. Il existe deux tables, appelées employé (table principale) et service (valeurs multiple).
Procédez comme suit :

- Type **employé** dans la **Table/affichage/SP**.
- Tapez département dans les **affichages/tableau nom d’à valeurs multiples**.
- Tapez la condition de jointure entre employés et service de **Condition de jointure**, par exemple `Employee.DEPTID=Department.DepartmentID`.
![runstep2](./media/active-directory-aadconnectsync-connector-genericsql/runstep2.png)

**Procédures stockées**  
![runstep3](./media/active-directory-aadconnectsync-connector-genericsql/runstep3.png)

- Si vous avez quantité de données, il est recommandé d’implémenter la pagination avec vos procédures stockées.
- Pour votre procédure stockée prendre en charge la pagination, vous devez fournir les Index de début et fin. Voir : [efficacement mise en page de grandes quantités de données](https://msdn.microsoft.com/library/bb445504.aspx).
- @StartIndexet @EndIndex sont remplacés au moment de l’exécution avec valeur de taille de page correspondante configurée sur la page **Configurer étape** . Par exemple, lorsque le connecteur récupère première page et la taille de page est définie 500, dans ce cas @StartIndex serait 1 et @EndIndex 500. Ces valeurs augmentent lorsque le connecteur récupère les pages suivantes et changez la @StartIndex & @EndIndex valeur.
- Pour exécuter la procédure stockée paramétrée, fournir les paramètres dans `[Name]:[Direction]:[Value]` format. Entrez chaque paramètre sur une ligne distincte (utiliser Ctrl + Entrée pour obtenir une nouvelle ligne).
- Connecteur SQL générique prend également en charge l’opération d’importation des serveurs liés dans Microsoft SQL Server. Si les informations doivent être récupérées à partir d’un tableau dans serveur lié, Table doit être fournie dans le format :`[ServerName].[Database].[Schema].[TableName]`
- Connecteur SQL générique prend en charge uniquement les objets qui ont une structure similaire (à la fois alias nom et type de données) entre exécuter des étapes de détection de schéma et des informations. Si l’objet sélectionné à partir de schéma et des informations fournies à l’étape exécution est différent, lien de SQL n’est pas en charge ce type de scénarios.

**Requête SQL**  
![runstep4](./media/active-directory-aadconnectsync-connector-genericsql/runstep4.png)

![runstep5](./media/active-directory-aadconnectsync-connector-genericsql/runstep5.png)

- Jeu de résultats plusieurs requêtes non pris en charge.
- Requête SQL prend en charge la pagination et fournir Démarrer Index et l’Index de fin comme étant une variable pour prendre en charge la pagination.

### <a name="delta-import"></a>Importation delta
![runstep6](./media/active-directory-aadconnectsync-connector-genericsql/runstep6.png)

Configuration de l’importation delta requiert une configuration plus par rapport à l’importation complète.

- Si vous choisissez l’approche déclencheur ou un instantané de suivi des modifications delta, puis indiquez tableau d’historique ou un instantané de base de données dans la zone **nom de base de données de capture instantanée ou tableau d’historique** .
- Vous devez également fournir jointure entre des tables de l’historique et Parent, par exemple`Employee.ID=History.EmployeeID`
- Pour suivre la transaction sur la table parent de la table de l’historique, vous devez fournir le nom de colonne qui contient les informations d’opération (ajouter/mise à jour/Supprimer).
- Si vous choisissez filigrane pour le suivi des modifications delta, puis indiquez le nom de colonne qui contient les informations de l’opération dans la zone **Nom de colonne d’interrogation de l’eau**.
- La colonne **attribut Type de modification** est requise pour le type de modification. Cette colonne mappe une modification qui se produit dans la table primaire ou à plusieurs valeurs à un type de modification dans la vue delta. Cette colonne peut contenir le type de modification Modify_Attribute pour les modifications au niveau de l’attribut ou un ajouter, modifier ou supprimer modifier type pour un type de modifications au niveau de l’objet. S’il est un numéro autre que la valeur par défaut ajouter, modifier ou supprimer, puis vous pouvez définir ces valeurs à l’aide de cette option.

### <a name="export"></a>Exporter
![runstep7](./media/active-directory-aadconnectsync-connector-genericsql/runstep7.png)

Connecteur SQL générique prend en charge l’exportation à l’aide de quatre méthodes prises en charge telles que :

- Table
- Affichage
- Procédure stockée
- Requête SQL

**Table ou une vue**  
Si vous choisissez l’option Table/vue, le connecteur génère les requêtes respectifs pour procéder à l’exportation.

**Procédures stockées**  
![runstep8](./media/active-directory-aadconnectsync-connector-genericsql/runstep8.png)

Si vous choisissez l’option de la procédure stockée, l’exportation nécessite trois procédures stockées différentes pour effectuer des opérations d’insertion/mise à jour/supprimer.

- **Ajouter un nom SP**: ce SP s’exécute si n’importe quel objet est fourni au connecteur de point d’insertion dans la table respective.
- **Mettre à jour le nom SP**: ce SP s’exécute si n’importe quel objet est fourni au connecteur de mise à jour dans la table respective.
- **Supprimer un nom de SP**: ce SP s’exécute si n’importe quel objet est fourni au connecteur de suppression dans la table respective.
- Attribut sélectionné à partir du schéma utilisé comme une valeur de paramètre à la procédure stockée. Par exemple, `EmployeeName: INPUT: @EmployeeName` (EmployeeName est sélectionnée dans le schéma de connecteur et le connecteur remplace la valeur correspondant tout en effectuant l’exporter)
- Pour exécuter la procédure stockée paramétrée, fournissent des paramètres dans `[Name]:[Direction]:[Value]` format. Entrez chaque paramètre sur une ligne distincte (utiliser Ctrl + Entrée pour obtenir une nouvelle ligne).

**Requête SQL**  
![runstep9](./media/active-directory-aadconnectsync-connector-genericsql/runstep9.png)

Si vous choisissez l’option de requête SQL, exportation nécessite trois requêtes différentes pour effectuer des opérations d’insertion/mise à jour/supprimer.

- **Insérer une requête**: cette requête est exécutée si n’importe quel objet est fourni au connecteur de point d’insertion dans la table respective.
- **Requête mise à jour**: cette requête est exécutée si n’importe quel objet est fourni au connecteur de mise à jour dans la table respective.
- **Supprimer une requête**: cette requête est exécutée si n’importe quel objet est fourni au connecteur de suppression dans la table respective.
- Attribut sélectionné à partir du schéma utilisé comme une valeur de paramètre à la requête, par exemple`Insert into Employee (ID, Name) Values (@ID, @EmployeeName)`

## <a name="troubleshooting"></a>Résolution des problèmes

-   Pour plus d’informations sur la façon d’activer la journalisation résoudre les problèmes le connecteur, voir [comment activer le suivi ETW des connecteurs](http://go.microsoft.com/fwlink/?LinkId=335731).
