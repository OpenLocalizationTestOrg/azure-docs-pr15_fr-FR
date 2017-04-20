<properties
   pageTitle="Connecteur Domino Lotus | Microsoft Azure"
   description="Cet article décrit la configuration Lotus Domino connecteur de Microsoft."
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

# <a name="lotus-domino-connector-technical-reference"></a>Guide de référence technique Lotus Domino connecteur
Cet article décrit le connecteur Domino Lotus. L’article s’applique aux produits suivants :

- Gestionnaire d’identité Microsoft 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   Devez utiliser correctif 4.1.3671.0 ou version ultérieure [KB3092178](https://support.microsoft.com/kb/3092178).

Pour MIM2016 et FIM2010R2, le lien n’est disponible au téléchargement à partir du [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-lotus-domino-connector"></a>Vue d’ensemble du connecteur Lotus Domino
Le Lotus Domino connecteur vous permet d’intégrer le service de synchronisation serveur Lotus Domino d’IBM.

Du point de vue globale, les fonctionnalités suivantes sont prises en charge par la version actuelle du connecteur :

Fonctionnalité | Prise en charge
--- | ---
Source de données connectée | Serveur : <li>Lotus Domino 8.5.x</li><li>Lotus Domino 9.x</li>Client :<li>Lotus Notes à 9.x</li>
Scénarios | <li>Gestion du cycle de vie des objets</li><li>Gestion de groupe</li><li>Gestion de mot de passe</li>
Opérations | <li>Complète et importer Delta</li><li>Exporter</li><li>Définir et modifier le mot de passe sur mot de passe HTTP</li>
Schéma | <li>Personne (itinérance avec l’utilisateur, Contact (personnes sans certificat))</li><li>Groupe</li><li>Ressource (réunion en ligne de la salle de ressource)</li><li>Courrier dans la base de données</li><li>Découverte dynamique des attributs pour les objets pris en charge</li>

Le connecteur Lotus Domino utilise le client Lotus Notes pour communiquer avec Lotus Domino Server. En raison de cette dépendance, un Client pris en charge de Lotus Notes doit être installé sur le serveur de synchronisation. La communication entre le client et le serveur est implémentée par l’interface de Lotus Notes .NET interopérabilité (Interop.domino.dll). Cette interface facilite la communication entre la plateforme Microsoft.NET et le client Lotus Notes et prend en charge l’accès aux documents Lotus Domino et affichages. Pour l’importation delta, il est également possible que l’interface native C++ est utilisé (selon la méthode d’importation delta sélectionné).

### <a name="prerequisites"></a>Conditions préalables
Avant d’utiliser le connecteur, vérifiez que vous disposez des actions suivantes sur le serveur de synchronisation :

- 4.5.2 de Microsoft .NET Framework ou version ultérieure
- Le client Lotus Notes doit être installé sur votre serveur de synchronisation
- Le connecteur Domino Lotus nécessite la par défaut Lotus Domino LDAP schéma de base de données (schema.nsf) doit se trouver sur le serveur d’annuaire Domino. Si elle n’est pas présent, vous pouvez l’installer en exécutant ou redémarrer le service LDAP sur le serveur Domino.

### <a name="connected-data-source-permissions"></a>Autorisations de Source de données connectées
Pour effectuer les tâches prises en charge de connecteur Lotus Domino, vous devez être membre de groupes suivants :

- Administrateurs d’accès complets
- Administrateurs
- Administrateurs de base de données

Le tableau suivant répertorie les autorisations nécessaires pour chaque opération :

Opération | Droits d’accès
--- | ---
Importation | <li>Lire des documents publics</li><li> Accès administrateur intégral (lorsque vous êtes membre du groupe Administrateurs accès complet, vous sont automatiquement avez à l’accès aux efficaces dans et.)</li>
Exporter et définir le mot de passe | Accès effectif : <li>Créer des documents</li><li>Supprimer des documents</li><li>Lire des documents publics</li><li>Écrire des documents publics</li><li>Dupliquer ou copier des documents</li>Pour des opérations d’exportation, vous devez également les rôles suivants : <li>CreateResource</li><li>GroupCreator</li><li>GroupModifier</li><li>UserCreator</li><li>UserModifier</li>

### <a name="direct-operations-and-adminp"></a>Opérations directes et AdminP
Opérations que soit passer directement à l’annuaire Domino ou à travers le processus AdminP. Les tableaux ci-après objets liste tous les pris en charge, opérations et, le cas échéant, la méthode d’implémentation associées :

**Carnet d’adresses principal**

Objet | Créer | Mise à jour | Supprimer
--- | --- | --- | ---
Personne | AdminP | Direct | AdminP
Groupe | AdminP | Direct | AdminP
MailInDB | Direct | Direct | Direct
Ressource | AdminP | Direct | AdminP

**Carnet d’adresses secondaire**

Objet | Créer | Mise à jour | Supprimer
--- | --- | --- | ---
Personne | N/A | Direct | Direct
Groupe | Direct | Direct | Direct
MailInDB | Direct | Direct | Direct
Ressource | N/A | N/A | N/A

Lorsqu’une ressource est créée, un document Notes est créé. De même, lorsqu’une ressource est supprimée, le document Notes est supprimé.

### <a name="ports-and-protocols"></a>Ports et protocoles
Client IBM Lotus Notes et serveurs Domino communiquent à l’aide des Notes à distance procédure appeler (NRPC) où NRPC doit utiliser TCP/IP. Le numéro de port par défaut est 1352, mais peut être modifié par l’administrateur Domino.

### <a name="not-supported"></a>Non pris en charge
Les opérations suivantes ne sont pas pris en charge par la version actuelle du connecteur Lotus Domino :

- Déplacer des boîtes aux lettres entre des serveurs.

## <a name="create-a-new-connector"></a>Créer un connecteur

### <a name="client-software-installation-and-configuration"></a>Configuration et Installation du logiciel client
Lotus Notes doit être installé sur le serveur **avant** que le connecteur est installé.

Lorsque vous installez, vérifiez que vous effectuez un **Seul utilisateur installer**. La valeur par défaut **Multi-utilisateurs installer** ne fonctionne pas.  
![Notes1](./media/active-directory-aadconnectsync-connector-domino/notes1.png)

Dans la page fonctionnalités, installez uniquement les fonctionnalités Lotus Notes et **l’Ouverture de session unique Client**. Ouverture de session unique est requis pour le connecteur à être en mesure d’ouvrir une session sur le serveur Domino.  
![Concernant l’installation2](./media/active-directory-aadconnectsync-connector-domino/notes2.png)

**Remarque :** Démarrez Lotus Notes immédiatement avec un utilisateur qui se trouve sur le même serveur que le compte que vous utilisez comme compte de service du connecteur. Assurez-vous également que fermer le client Lotus Notes sur le serveur. Il ne peut pas être exécuté en même temps que le connecteur essaie de se connecter au serveur Domino.

### <a name="create-connector"></a>Créer le lien
Pour créer un connecteur de Lotus Domino, sélectionnez **Agent de gestion** et **créer**dans **Le Service de synchronisation** . Sélectionnez le lien **Lotus Domino (Microsoft)** .  
![CreateConnector](./media/active-directory-aadconnectsync-connector-domino/createconnector.png)

Si votre version du service de synchronisation offre la possibilité de configurer **Architecture**, vérifiez que le connecteur est défini sur sa valeur par défaut pour s’exécuter dans le **processus**.

### <a name="connectivity"></a>Connectivité
Dans la page de connexion, vous devez spécifier le nom du serveur Lotus Domino et entrez les informations d’identification d’ouverture de session.  
![Connectivité](./media/active-directory-aadconnectsync-connector-domino/connectivity.png)

La propriété serveur Domino prend en charge deux mises en forme pour le nom du serveur :

- Nom du serveur
- Nom du serveur/nom du répertoire

Le format de **Nom du serveur/nom du répertoire** est le format par défaut pour cet attribut, car il offre une réponse plus rapide lorsque le connecteur contacte le serveur Domino.

Le fichier de nom d’utilisateur fourni est stocké dans la base de données de configuration du service de synchronisation.

Pour **l’Importation Delta** vous possède ces options :

- **Aucun**. Le connecteur ne les importations delta.
- **Ajouter/Mettre à jour**. L’importation de delta connecteur est l’ajouter et mettre à jour des opérations. Pour supprimer une opération **d’Importation complète** est requise. Cette opération est à l’aide de l’interopérabilité .net.
- **Ajouter/mise à jour/supprimer**. L’importation de delta connecteur est l’ajouter, mettre à jour et supprimer des opérations. Cette opération est à l’aide de l’interface C++ native.

**Options du schéma** vous propose les options suivantes :

- **Par défaut schéma**. Le connecteur détecte le schéma à partir du serveur Domino. Il s’agit de l’option par défaut.
- **Schéma de DSML**. Utilisé uniquement si le serveur Domino n’expose pas le schéma. Ensuite, vous pouvez créer un fichier DSML avec le schéma et importer à la place. Pour plus d’informations sur DSML, voir [OASIS](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=dsml).

Lorsque vous cliquez sur Suivant, les paramètres de configuration ID d’utilisateur et mot de passe sont vérifiées.

### <a name="global-parameters"></a>Paramètres globaux
Dans la page Paramètres globaux, vous configurez le fuseau horaire et l’importation et exportez option opération.  
![Paramètres globaux](./media/active-directory-aadconnectsync-connector-domino/globalparameters.png)

Le paramètre de **fuseau horaire du serveur Domino** définit l’emplacement de votre serveur Domino.

Cette option de configuration est nécessaire pour prendre en charge les opérations **delta importer** , car elle permet la synchronisation service identifier les modifications apportées entre les deux derniers importations.

#### <a name="import-settings-method"></a>Paramètres d’importation, méthode
**Effectuer une importation complète en** possède ces options :

- Recherche
- Affichage (recommandé)

**Recherche** est à l’aide de l’indexation en Domino mais il est courant que les index ne sont pas mises à jour en temps réel et les données renvoyées par le serveur ne sont pas toujours correctes. Pour un système comporte de nombreuses modifications, cette option ne fonctionne pas correctement avec généralement et fournit false supprime dans certains cas. Toutefois, **la recherche** est plus rapide que la **vue**.

**Affichage** est l’option recommandée, car il offre l’état correct de données. Il est légèrement plus lente que la recherche.

#### <a name="creation-of-virtual-contact-objects"></a>Création d’objets visite virtuelles
La **permettre la création de \_objet Contact** possède ces options :

- Aucun
- Valeurs non-référence
- Valeurs de référence et Non-référence

Dans Domino, les attributs de référence peuvent contenir différents formats pour faire référence à d’autres objets. Pour pouvoir représenter différentes variations, met en œuvre le connecteur \_objets, également connu sous **Contacts virtuel** (VC) de Contact. Ces objets sont créés afin qu’ils peuvent participer aux objets MV existants ou projetés en tant que nouveaux objets. De cette façon, attribut références peuvent être conservés.

Activez ce paramètre et si le contenu d’un attribut de référence n’est pas un format de nom de domaine, un \_objet Contact est créé. Par exemple, un attribut de membre d’un groupe peut contenir les adresses SMTP. Il est également possible d’avoir shortName et autres attributs présents dans les attributs de référence. Dans ce scénario, sélectionnez **Valeurs Non-référence**. Cette configuration est le paramètre le plus courant pour mises en œuvre Domino.

Lorsque Lotus Domino est configuré pour que les carnets d’adresses distincts avec différents noms uniques représentant le même objet, il est possible de créer également \_objets de Contact pour toutes les valeurs de référence qui sont trouvent dans un carnet d’adresses. Dans ce scénario, sélectionnez l’option de **référence et les valeurs Non-référence** .

Si vous avez plusieurs valeurs dans l’attribut **nom complet** dans Domino, puis que vous voulez également activer la création de Contacts virtuel afin que les références peuvent être résolues. Par exemple, cet attribut peut avoir plusieurs valeurs après un mariage ou divorce. Activez la case à cocher Activer **... Nom complet comporte plusieurs valeurs** pour ce scénario.

En joignant les attributs corrects, la \_objets Contact est joint à l’objet MV.

Ces objets ont VC =\_Contact ajouté à leur nom de domaine.

#### <a name="import-settings-conflict-object"></a>Importer les paramètres, sont en conflit d’objet
**Exclure l’objet de conflit**

Dans une grande implémentation Domino, il est possible que plusieurs objets ont le même nom unique en raison de problèmes de réplication. Dans ce cas, le connecteur voyez deux objets avec différents UniversalIDs mais le même nom de domaine. Ce conflit provoque un objet transitoire entraînerait la création de l’espace connecteur. Le connecteur pouvez ignorer les objets qui ont été sélectionnées dans Domino comme victime réplication. Il est recommandé de conserver cette case à cocher sélectionnée.

#### <a name="export-settings"></a>Exporter les paramètres
Si l’option **Utiliser AdminP pour mettre à jour les références** n’est pas cochée, exportation des attributs de référence, tels que les membres, est un appel direct et n’utilise pas le processus AdminP. Utilisez cette option uniquement lorsque AdminP n’a pas été configuré pour maintenir l’intégrité référentielle.

#### <a name="routing-information"></a>Informations de routage
Dans Domino, il est possible qu’un attribut de référence a routage informations incorporées comme suffixe pour le nom de domaine. Par exemple, l’attribut de membre d’un groupe peut contenir **CN=example/organization@ABC**. Le suffixe @ABC est les informations de routage. Les informations de routage permettant d’envoyer des messages électroniques sur le système Domino approprié, qui pourrait être un système dans une autre organisation en Domino. Dans le champ les informations de routage, vous pouvez spécifier les suffixes routage utilisées dans l’organisation dans l’étendue du connecteur. Si une de ces valeurs se trouve sous forme de suffixe dans un attribut de référence, les informations de routage sont supprimées de la référence. Si le suffixe routage sur une valeur de référence ne correspondent pas à un de ces valeurs spécifiées, un \_objet Contact est créé. Ces \_objets Contact sont créées avec ** RO=@ ** inséré dans le nom de domaine. Pour ces \_objets de contacts les attributs suivants sont également ajoutées pour permettre à joindre à un objet réel si nécessaire : \_routingName, \_contact, \_displayName et UniversalID.

#### <a name="additional-address-books"></a>Carnets d’adresses supplémentaires
Si vous n’avez pas d' **aide à l’annuaire** installé, qui fournit le nom de carnets d’adresses secondaire, vous pouvez entrer manuellement ces carnets d’adresses.

#### <a name="multivalued-transformation"></a>Transformation à plusieurs valeurs
Attributs de nombreux dans Lotus Domino sont à valeurs multiples. Les attributs correspondants métaverse sont généralement unique à plusieurs valeurs. En configurant l’importation et l’option opération d’exportation, vous activez le lien pour vous aider à la traduction requise des attributs concernés.

**Exporter**  
L’option opération d’exportation prend en charge deux modes :

- Ajouter l’élément
- Remplacer l’élément

**Remplacer l’élément** – lorsque vous sélectionnez cette option, le connecteur toujours supprimer les valeurs en cours de l’attribut dans Domino et les remplacer par les valeurs fournies. Fourni évaluer peut être une valeur unique ou à valeurs multiples.

Exemple : L’attribut Assistant d’un objet personne compose les valeurs suivantes :

- NC = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- NC = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Si un Assistant nouveau nommé **Alexandre David** est affecté à cet objet de la personne, le résultat est :

- NC = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf

**Ajouter un élément** – lorsque vous sélectionnez cette option, le connecteur conserve les valeurs existantes sur l’attribut dans Domino et insérer de nouvelles valeurs en haut de la liste de données.

Exemple : L’attribut Assistant d’un objet personne compose les valeurs suivantes :

- NC = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- NC = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Si un Assistant nouveau nommé **Alexandre David** est affecté à cet objet de la personne, le résultat est :

- NC = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
- NC = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- NC = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

**Importation**  
L’option Importer des opération prend en charge deux modes :

- Par défaut
- À plusieurs valeurs à valeur unique

**Par défaut** – lorsque vous sélectionnez l’option par défaut, toutes les valeurs de tous les attributs sont importées.

**Multivalued à valeur unique** – lorsque vous sélectionnez cette option, un attribut à valeurs multiples est converti en un attribut à valeur unique. En cas de plusieurs valeurs, la valeur dans la partie supérieure (cette valeur est généralement également la plus récente) est utilisée.

Exemple : L’attribut Assistant d’un objet personne compose les valeurs suivantes :

- NC = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
- NC = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- NC = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

La mise à jour la plus récente à cet attribut est **Alexandre David**. Étant donné que l’option opération d’importation est définie sur Multivalued à valeur unique, connecteur importe uniquement **Alexandre David** dans l’espace de connecteur.

La logique permettant de convertir les attributs à valeurs multiples dans des attributs à valeur unique ne s’applique pas à l’attribut de membre du groupe et à l’attribut de nom complet de la personne.

Il est également possible de configurer importer et exporter des règles de transformation des attributs à plusieurs valeurs par attribut, comme une exception à la règle globale. Pour configurer cette option, entrez [type d’objet]. [attributename] dans les zones de texte **Importer la liste des attributs exclusion** et **Exporter la liste des attributs d’exclusion** . Par exemple, si vous entrez Person.Assistant et l’indicateur global est défini sur Importer toutes les valeurs, uniquement la première valeur est importée pour l’assistant.

#### <a name="certifiers"></a>Certificateurs
Toutes les unités d’organisation/organisation sont répertoriées par le connecteur. Pour pouvoir exporter des objets de la personne dans le carnet d’adresses principal, une certification avec son mot de passe est nécessaire.

Si toutes les certificateurs ont le même mot de passe, le **mot de passe de tous les Certifers** peut être utilisé. Vous pouvez entrer le mot de passe et uniquement spécifier le fichier de certification.

Si vous importez uniquement, vous n’êtes pas obligé de spécifier les certificateurs.

### <a name="configure-provisioning-hierarchy"></a>Configurer la hiérarchie mise en service
Lorsque vous configurez le connecteur Lotus Domino, ignorez cette page de la boîte de dialogue. Le connecteur Lotus Domino ne prend pas en charge hiérarchie mise en service.  
![Mise en service de la hiérarchie](./media/active-directory-aadconnectsync-connector-domino/provisioninghierarchy.png)

### <a name="configure-partitions-and-hierarchies"></a>Configurez des Partitions et hiérarchies
Lorsque vous configurez des partitions et hiérarchies, vous devez sélectionner le carnet d’adresses principal appelé NAB=names.nsf. En plus de carnet d’adresses principal, vous pouvez sélectionner les carnets d’adresses secondaire s’ils existent.  
![Partitions](./media/active-directory-aadconnectsync-connector-domino/partitions.png)

### <a name="select-attributes"></a>Sélectionnez les attributs
Lorsque vous configurez votre attributs, vous devez sélectionner tous les attributs qui sont précédés ** \_MMS\_**. Ces attributs sont obligatoires lorsque vous configurez de nouveaux objets vers Lotus Domino

![Attributs](./media/active-directory-aadconnectsync-connector-domino/attributes.png)

## <a name="object-lifecycle-management"></a>Gestion du cycle de vie des objets
Cette section fournit une vue d’ensemble des différents objets dans Domino.

### <a name="person-objects"></a>Objets de personne
L’objet personne représente les utilisateurs dans l’organisation et unités d’organisation. Outre les attributs par défaut, l’administrateur Domino peut ajouter des attributs personnalisés à un objet de la personne. Au moins un objet personne doit inclure tous les attributs obligatoires. Pour une liste complète des attributs obligatoires, voir [Propriétés de Lotus Notes](#lotus-notes-properties). Pour enregistrer un objet de la personne, les conditions préalables suivantes doivent être réunies :

- Le carnet d’adresses (names.nsf) doit avoir été définie et il doit être le carnet d’adresses principal.
- Vous devez avoir l’autorité O/unité d’organisation Id et le mot de passe pour enregistrer un utilisateur particulier dans l’organisation / unité d’organisation.
- Vous devez définir un ensemble spécifique de propriétés de Lotus Notes pour un objet de la personne. Ces propriétés sont utilisées pour la mise en service de l’objet de la personne. Pour plus d’informations, consultez la section appelée [Propriétés Lotus Notes](#lotus-notes-properties) plus loin dans ce document.
- Mot de passe HTTP initial d’une personne est un attribut et un jeu au cours de la mise en service.
- L’objet de personne doit être un des trois types pris en charge suivants :
    1. Utilisateur normal présentant un fichier de messagerie et un fichier d’id utilisateur
    2. D’itinérance (utilisateur Normal qui inclut tous les fichiers de base de données itinérance)
    3. Contacts (utilisateur sans fichier id)

Personnes (à l’exception des contacts) peuvent davantage être regroupés en nous utilisateurs et International, telle que définie par la valeur de la \_MMS\_IDRegType propriété. Ces personnes utilisent le Client Notes pour accéder aux serveurs Lotus Domino, comporter un Id Notes et un document de la personne. Si ils utilisent courrier Notes, ils doivent également un fichier de courrier. L’utilisateur doit être enregistré pour devenir active. Pour plus d’informations, voir :

- [Configuration des utilisateurs de Notes](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_SETTING_UP_NOTES_USERS.html)
- [Enregistrement d’un utilisateur](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_REGISTERING_USERS.html)
- [Gestion des utilisateurs](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_USERS_5151.html)
- [Modification du nom des utilisateurs](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_USER_AUTOMATICALLY.html)

Toutes ces opérations sont effectuées dans Lotus Domino et puis importées dans le service de synchronisation.

### <a name="resources-and-rooms"></a>Ressources et salles
Une ressource est un autre type d’une base de données dans Lotus Domino. Ressources peuvent être des salles de conférence avec différents types d’équipement, tels que les projecteurs. Il existe des sous-types de ressources pris en charge par le connecteur Lotus Domino qui sont définies par l’attribut de Type de ressource :

Type de ressource | Attribut de Type de ressource
--- | ---
Salle | 1
Ressource (autres) | 2
Réunion en ligne | 3

Pour le type d’objet ressource fonctionne, les éléments suivants exigeant requis :

- Base de données de réservation de ressources doit déjà exister dans le serveur Domino connecté
- Le site est déjà défini pour la ressource

La base de données de réservation de ressources contient trois types de documents :

- Profil de site
- Ressource
- Réservation

Pour plus d’informations sur la configuration de base de données de réservation de ressources, voir [la configuration de la base de données de ressource réservations](https://www-01.ibm.com/support/knowledgecenter/SSKTMJ_8.0.1/com.ibm.help.domino.admin.doc/DOC/H_SETTING_UP_THE_RESOURCE_RESERVATIONS_DATABASE.html).

**Créer, mettre à jour et supprimer des ressources**  
Les opérations de créer, mettre à jour et supprimer sont effectuées par le connecteur Lotus Domino dans la base de données de réservation de ressources. Ressources sont créées en tant que documents dans Names.nsf (autrement dit, le carnet d’adresse principale). Pour plus d’informations sur la modification et suppression de ressources, voir [modification et suppression d’un document de ressources](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_EDITING_AND_DELETING_RESOURCE_DOCUMENTS.html).

**Importation et exportation pour les ressources**  
Les ressources peuvent être importés à et exportés à partir du service de synchronisation, comme tout autre type d’objet. Sélectionnez le type d’objet en tant que ressource lors de la configuration. Opération d’exportation réussie, vous devez disposer des détails pour le type de ressource, base de données de conférence et nom du Site.

### <a name="mail-in-databases"></a>Bases de données de messagerie
Une base de données de messagerie est une base de données est conçu pour recevoir des messages. Il s’agit d’une boîte aux lettres Lotus Domino qui n’est pas associé à un compte d’utilisateur Lotus Domino spécifique (autrement dit, il n’a pas son propre fichier d’ID et le mot de passe). Une base de données de messagerie a un utilisateur unique (« nom abrégé ») associé et sa propre adresse de messagerie.

Si il est nécessaire pour une boîte aux lettres distincte avec sa propre adresse de messagerie qui peut être partagé entre différents utilisateurs (par exemple, group@contoso.com), une base de données de messagerie est créé. L’accès à cette boîte aux lettres est contrôlée via son contrôle de liste d’accès, qui contient les noms des utilisateurs Notes qui sont autorisés à ouvrir la boîte aux lettres.

Pour obtenir la liste des attributs obligatoires, voir la section intitulée [Attributs obligatoires](#mandatory-attributes) plus loin dans cet article.

Lorsqu’une base de données est conçue pour recevoir un message, un document de publipostage dans la base de données est créé dans Lotus Domino. Ce document doit exister dans l’annuaire Domino de tous les serveurs qui enregistre une copie de la base de données. Pour une description détaillée sur la création d’un document de publipostage dans la base de données, voir [Création d’un document de publipostage dans la base de données](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_A_MAILIN_DATABASE_DOCUMENT_FOR_A_NEW_DATABASE_OVERVIEW.html).

Avant de créer une base de données dans courrier, la base de données doit déjà exister (doit ont été créés par l’administrateur Lotus) sur le serveur Domino.

### <a name="group-management"></a>Gestion de groupe
Vous pouvez obtenir une vue d’ensemble détaillée de la gestion de groupe Lotus Domino dans les ressources suivantes :

- [Utilisation de groupes](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_USING_GROUPS_OVER.html)
- [Création d’un groupe](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS_MIDTOPIC_55038956829238418.html)
- [Création et modification des groupes](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS.html)
- [Gestion des groupes](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_GROUPS_1804.html)
- [Renommer un groupe](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_GROUP_STEPS.html)

### <a name="password-management"></a>Gestion de mot de passe
Pour un utilisateur Lotus Domino enregistré, il existe deux types de mots de passe :

1. Mot de passe utilisateur (stocké dans le fichier User.id)
2. Internet / mot de passe HTTP

Le connecteur Lotus Domino prend en charge que les opérations avec mot de passe HTTP.

Pour effectuer la gestion de mot de passe, vous devez activer la gestion des mots de passe pour le connecteur dans le Concepteur de l’Agent de gestion. Pour activer la gestion de mot de passe, sélectionnez **Activer la gestion de mot de passe** dans la page de la boîte de dialogue **Configurer les Extensions** .  
![Configurer les Extensions](./media/active-directory-aadconnectsync-connector-domino/configureextensions.png)

La prise en charge connecteur Lotus Domino opérations sur mot de passe Internet suivantes :

- Définir le mot de passe : Définir le mot de passe définit un nouveau mot de passe HTTP/Internet sur l’utilisateur dans Domino. Par défaut le compte est également déverrouillé. L’indicateur de déverrouiller est indiquée dans l’interface WMI du moteur de synchronisation.
- Modifier le mot de passe : Dans ce scénario, un utilisateur changez le mot de passe ou est invité à modifier le mot de passe après un certain temps. Pour cette opération en place, à la fois (l’ancien et le nouveau mot de passe) sont obligatoires. Une fois modifiée, le nouveau mot de passe est mis à jour dans Lotus Domino.

Pour plus d’informations, voir :

- [À l’aide de la fonctionnalité de verrouillage Internet](http://www.ibm.com/developerworks/lotus/library/domino8-lockout/)
- [Gestion des mots de passe Internet](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_NOTES_AND_INTERNET_PASSWORD_SYNCHRONIZATION_7570_OVER.html)

## <a name="reference-information"></a>Informations de référence
Cette section répertorie telles que les descriptions des attributs et attribut configuration requise pour le connecteur Lotus Domino.

### <a name="lotus-notes-properties"></a>Propriétés de Lotus Notes
Lorsque vous configurez des objets de personne à votre annuaire Lotus Domino, vos objets doivent avoir un ensemble spécifique de propriétés avec des valeurs spécifiques remplies. Ces valeurs sont requises uniquement pour les opérations de création.

Le tableau suivant répertorie ces propriétés ainsi que leur description.

Propriété | Description
--- | ---
\_MMS_AltFullName | Autre nom complet d’utilisateur.
\_MMS_AltFullNameLanguage | La langue à utiliser pour spécifier l’autre nom complet d’utilisateur.
\_MMS_CertDaysToExpire | Le nombre de jours compris entre la date du jour avant que le certificat arrive à expiration. Le cas contraire, la date par défaut est deux ans à partir de la date du jour.
\_MMS_Certifier | Propriété qui contient le nom de la hiérarchie d’organisation de l’autorité. Par exemple : Unité d’organisation = OrganizationUnit, O = Org, C = pays.
\_MMS_IDPath | Si la propriété est vide, aucun fichier d’identification utilisateur n’est créé localement sur le serveur de synchronisation. Si la propriété contient un nom de fichier, un fichier d’ID utilisateur est créé dans le dossier madata. La propriété peut contenir un chemin d’accès complet.
\_MMS_IDRegType | Personnes peuvent être classés comme contacts, nous utilisateurs et les utilisateurs internationaux. Le tableau suivant répertorie les valeurs possibles : <li>0 - contact</li><li>1 - utilisateur US</li><li>2 - utilisateur international</li>
\_MMS_IDStoreType | Propriété Null interdit États-Unis et les utilisateurs internationaux. La propriété contient un nombre entier qui spécifie si l’identification de l’utilisateur est stockée en tant que pièce jointe dans le carnet de Notes ou dans le fichier de messagerie de la personne. Si le fichier d’ID utilisateur est une pièce jointe dans le carnet d’adresses, il peut être éventuellement créé en tant que fichier avec \_MMS_IDPath. <li>Vide - fichier ID magasin dans ID de l’archivage sécurisé, aucun fichier identification (utilisé pour les Contacts).</li><li> 1 - pièce jointe dans le carnet de Notes. La \_MMS_Password propriété doit être définie pour les fichiers d’identification utilisateur qui sont des pièces jointes</li><li>2 - stocker ID dans le fichier de messagerie de la personne. La \_MMS_UseAdminP doit être définie sur false pour laisser le fichier de courrier être créé lors de l’enregistrement de la personne. La \_MMS_Password propriété doit être définie pour les fichiers d’identification utilisateur.</li>
\_MMS_MailQuotaSizeLimit | Le nombre de mégaoctets qui sont autorisés pour la base de données du fichier de courrier électronique.
\_MMS_MailQuotaWarningThreshold | Le nombre de mégaoctets qui sont autorisés pour la base de données du fichier de courrier électronique avant un avertissement est émis.
\_MMS_MailTemplateName | Le fichier de modèle de courrier électronique qui est utilisé pour créer le fichier de courrier électronique de l’utilisateur. Si un modèle est spécifié, le fichier de courrier est créé en utilisant le modèle spécifié. Si aucun modèle est spécifié, le fichier de modèle par défaut est utilisé pour créer le fichier.
\_MMS_OU | Propriété facultative qui correspond au nom d’unité d’organisation sous l’autorité. Cette propriété doit être vide pour les contacts.
\_MMS_Password | Propriété obligatoire pour les utilisateurs. La propriété contient le mot de passe pour le fichier d’identification de l’objet.
\_MMS_UseAdminP | Propriété doit être définie sur true si le fichier de courrier doit être créé par le processus AdminP sur le serveur Domino (asynchrone pour le processus d’exportation). Si la propriété est définie sur false, le fichier de courrier est créé avec l’utilisateur Domino du (synchrone dans le processus d’exportation).

Pour un utilisateur avec un fichier d’identification, la \_MMS_Password propriété doit contenir une valeur. Pour accéder au courrier électronique via le client Lotus Notes, les propriétés MailServer et MailFile d’un utilisateur doivent contenir une valeur.

Pour accéder à messagerie via un navigateur Web, les propriétés suivantes doivent contenir des valeurs :

- MailFile - propriété Null interdit qui contient le chemin d’accès sur le serveur Lotus Domino où se trouve le fichier de courrier.
- MailServer - propriété Null interdit qui contient le nom du serveur Lotus Domino. Cette valeur est le nom à utiliser lorsque vous avez créé le fichier de courrier Lotus sur le serveur Domino.
- HTTPPassword - propriété facultative qui contient le mot de passe Web access pour l’objet.

Pour accéder au serveur Domino sans fonctionnalité de messagerie, la propriété HTTPPassword doit contenir une valeur. La propriété MailFile et la propriété MailServer peuvent être vides.

Avec \_MMS_ IDStoreType = 2 (store id dans le fichier de messagerie), la propriété MailSystem de NotesRegistrationclass est définie sur REG_MAILSYSTEM_INOTES (3).

### <a name="mandatory-attributes"></a>Attributs obligatoires
Le connecteur Lotus Domino principalement prend en charge que ces types d’objets (types de documents) :

- Groupe
- Base de données dans courrier
- Personne
- Contact (personne avec aucune autorité)
- Ressource

Cette section répertorie les attributs qui sont obligatoires pour chaque objet pris en charge exporter vers un serveur Domino.

Type d’objet | Attributs obligatoires
--- | ---
Groupe | <li>Nom de la liste</li>
Principaux dans la base de données | <li>Nom complet</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li>
Personne | <li>Nom</li><li>MailFile</li><li>ShortName</li><li>\_MMS_Password</li><li>\_MMS_IDStoreType</li><li>\_MMS_Certifier</li><li>\_MMS_IDRegType</li><li>\_MMS_UseAdminP</li>
Contact (personne avec aucune autorité) | <li>\_MMS_IDRegType</li>
Ressource | <li>Nom complet</li><li>Type de ressource</li><li>ConfDB</li><li>CapacitéRessource</li><li>Site</li><li>Nom complet</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li>

## <a name="common-issues-and-questions"></a>Questions et problèmes courants

### <a name="schema-detection-does-not-work"></a>Détection de schéma ne fonctionne pas
Pour être en mesure de détecter le schéma, il est nécessaire que le fichier schema.nsf est présent sur le serveur Domino. Ce fichier s’affiche uniquement si LDAP est installé sur le serveur. Si le schéma n’est pas détectable, vérifiez les points suivants :

- Le fichier schema.nsf est présent dans le dossier racine du serveur Domino
- L’utilisateur dispose des autorisations pour afficher le fichier schema.nsf.
- Forcer un redémarrage du serveur LDAP. Ouvrez **Lotus Domino Console** et utilisez **Dire LDAP ReloadSchema** commande pour recharger le schéma.

### <a name="not-all-secondary-address-books-are-visible"></a>Pas tous les carnets d’adresses secondaire sont visibles
Le connecteur Domino repose sur la fonctionnalité **Aide à l’annuaire** pour pouvoir retrouver les carnets d’adresses secondaire. Si les carnets d’adresses secondaire sont manquantes, vérifiez si [L’Assistance Directory](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_DIRECTORY_ASSISTANCE.html) a été activé et configuré sur le serveur Domino.

### <a name="custom-attributes-in-domino"></a>Attributs personnalisés dans Domino
Il existe plusieurs façons dans Domino pour étendre le schéma de sorte qu’il apparaît sous la forme d’un attribut personnalisé consommable par le connecteur.

**Approche 1 : Étendre le schéma de Lotus Domino**

1. Créer une copie du modèle d’annuaire Domino {PUBNAMES. NTF} en suivant [ces étapes](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (vous devez personnaliser pas le répertoire de IBM Lotus Domino modèle par défaut) :
2. Ouvrez le modèle de répertoire de copie de Domino {CONTOSO. Modèle NTF} qui a été créé dans le Concepteur de Domino et procédez comme suit :
    - Cliquez sur éléments partagés et développer des sous-formulaires
    - Double-cliquez sur le sous-formulaire de InheritableSchema ${nom_objet} (où {nom_objet} est le nom de la classe d’objet structurelle par défaut, par exemple : personne).
    - Nom de l’attribut à ajouter dans le schéma {MyPersonAtrribute} et correspondant à cet attribut. Créer un champ, sélectionnez le Menu **créer** , puis sélectionnez le **champ** à partir du menu.
    - Dans le champ ajouté, définissez ses propriétés en sélectionnant son Type, Style, la taille, polices et autres paramètres associés dans la fenêtre de propriétés de champ.
    - Conserver l’attribut valeur par défaut même en tant que nom donné pour cet attribut (par exemple, si le nom de l’attribut est MyPersonAttribute, conservez la valeur par défaut du même nom).
    - Enregistrez le sous-formulaire InheritableSchema ${nom_objet} avec les valeurs mises à jour.
3. Remplacer le modèle d’annuaire Domino {PUBNAMES. NTF} avec le nouveau modèle personnalisé {CONTOSO. NTF} en suivant [ces étapes](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
4. Fermez Domino d’administrateur et ouvrez Console Domino pour redémarrer le service LDAP et recharger le schéma LDAP :
    - Dans la Console Domino, insérer la commande sous le texte de la **Commande Domino** classé pour redémarrer le service LDAP - [Redémarrez tâche LDAP]( http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
    - Pour recharger LDAP schéma utiliser la commande LDAP dire - me LDAP ReloadSchema
5. Ouvrir Domino d’administration et cliquez sur onglet personnes et groupes pour afficher l’attribut ajouté est reflétée dans domino ajouter personne.
6. Ouvrez Schema.nsf à partir de l’onglet **fichiers** et attribut ajouté est reflétée dans classe d’objets LDAP dominoPerson.

**Approche 2 : Créer un auxClass avec attribut personnalisé et associer à la classe d’objet**

1. Créer une copie du modèle d’annuaire Domino {PUBNAMES. NTF} en suivant [ces étapes](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (jamais personnaliser le répertoire de IBM Lotus Domino modèle par défaut) :
2. Ouvrez le modèle de répertoire de copie de Domino {CONTOSO. Modèle NTF} qui a été créé, dans le Concepteur de Domino.
3. Dans le volet gauche, sélectionnez Code partagé, puis sous-formulaires.
4. Cliquez sur Nouveau formulaire
5. Procédez comme suit pour spécifier les propriétés pour le nouveau formulaire :
    - Le nouveau formulaire ouvert, sélectionnez création - propriétés de sous-formulaire
    - En regard de la propriété Name, entrez un nom pour la classe d’objet auxiliaire--par exemple, TestSubform.
    - Conserver la propriété Options « Inclure dans Insertion sous-formulaire... boîte de dialogue » sélectionné
    - Désactivez le Options propriété « rendu traversent HTML dans Notes. »
    - Laissez les autres propriétés de la même et fermer la boîte de dialogue Propriétés sous-formulaire.
    - Enregistrez et fermez le nouveau formulaire.
6. Procédez comme suit pour ajouter un champ pour définir la classe d’objet auxiliaire :
    - Ouvrez le sous-formulaire que vous avez créé.
    - Choisissez créer - de champ.
    - En regard de nom sous l’onglet concepts de base de la boîte de dialogue champ, spécifier un nom quelconque, par exemple : {MyPersonTestAttribute}.
    - Dans le champ ajouté, définissez ses propriétés en sélectionnant son Type, Style, la taille, police et les propriétés associées.
    - Conserver l’attribut valeur par défaut même en tant que nom donné pour cet attribut (par exemple, si le nom de l’attribut est MyPersonTestAttribute, conservez la valeur par défaut du même nom).
    - Enregistrez le sous-formulaire avec valeurs mises à jour et procédez comme suit :
        - Dans le volet gauche, sélectionnez Code partagé, puis sous-formulaires
        - Sélectionnez le nouveau formulaire, puis choisissez création - propriétés de création.
        - Cliquez sur l’onglet troisième à partir de la gauche, puis sélectionnez **Modifier propager cette interdiction de conception**.
7. Ouvrez le sous-formulaire ExtensibleSchema ${nom_objet}, (où {nom_objet} est le nom de la classe d’objet structurelle par défaut, par exemple – personne).
8. Insérer une ressource et sélectionnez le sous-formulaire (que vous avez créé, par exemple – TestSubform) et enregistrez le sous-formulaire ExtensibleSchema ${nom_objet}.
9. Remplacer le modèle d’annuaire Domino {PUBNAMES. NTF} avec le nouveau modèle personnalisé {CONTOSO. NTF} en suivant [ces étapes](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
10. Fermez Domino d’administrateur et ouvrez Console Domino pour redémarrer le service LDAP et recharger le schéma LDAP :
    - Dans la Console Domino, insérer la commande sous le texte de la **Commande Domino** classé pour redémarrer le service LDAP - [Redémarrez tâche LDAP](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
    - Pour recharger LDAP schéma utilisez commande LDAP savoir **Dire LDAP ReloadSchema**.
11. Ouvrez Domino d’administration et sélectionnez onglet personnes et groupes pour afficher l’attribut ajouté est reflétée dans domino ajouter une personne (sous d’autres personnes de l’onglet).
12. Ouvrez Schema.nsf à partir de l’onglet **fichiers** et attribut ajouté est indiqué sous TestSubform LDAP auxiliaire classe d’objet.

**Méthode 3 : Ajouter l’attribut personnalisé à la classe ExtensibleObject**

1. Ouvrir le fichier {Schema.nsf} placé dans le répertoire racine
2. Sélectionnez Classes d’objets LDAP dans le menu de gauche sous **Tous les Documents de schéma** et cliquez sur le bouton **Ajouter un objet classe** :
3. Attribuez nom LDAP sous forme de {zzzExtensibleSchema} (où zzz est le nom de la classe d’objet structurelle par défaut, par exemple personne). Par exemple, pour étendre le schéma de personne classe d’objet, fournissent nom LDAP {PersonExtensibleSchema}.
4. Fournir un nom de la classe supérieure objet, pour lequel vous souhaitez étendre le schéma. Par exemple, pour étendre le schéma de classe d’objet personne, fournissent nom de la classe supérieure objet {dominoPerson} :
5. Fournir un identificateur d’objets valide correspondant à la classe d’objet.
6. Sélectionnez les attributs Extended/personnalisé sous champs obligatoire ou facultatif Types d’attribut conformément à la configuration minimale requise :
7. Après avoir ajouté les attributs obligatoires à la ExtensibleObjectClass, cliquez sur **Enregistrer et fermer**.
8. Un ExtensibleObjectClass est créé pour la classe d’objet respectifs par défaut avec les attributs étendus.

## <a name="troubleshooting"></a>Résolution des problèmes

-   Pour plus d’informations sur la façon d’activer la journalisation résoudre les problèmes le connecteur, voir [comment activer le suivi ETW des connecteurs](http://go.microsoft.com/fwlink/?LinkId=335731).
