<properties
   pageTitle="Connecteur LDAP générique | Microsoft Azure"
   description="Cet article décrit comment configurer le connecteur LDAP générique de Microsoft."
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

# <a name="generic-ldap-connector-technical-reference"></a>Référence technique connecteur LDAP générique
Cet article décrit le connecteur LDAP générique. L’article s’applique aux produits suivants :

- Gestionnaire d’identité Microsoft 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   Devez utiliser daté 4.1.3671.0 ou version ultérieure [KB3092178](https://support.microsoft.com/kb/3092178).

Pour MIM2016 et FIM2010R2, le lien n’est disponible au téléchargement à partir du [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

Lorsque vous faites référence aux RFC IETF, ce document à l’aide de la mise en forme (RFC [numéro RFC] / [section dans le document RFC]), par exemple (RFC 4512/4.3).
Vous pouvez trouver plus d’informations sur http://tools.ietf.org/html/rfc4500 (vous devez remplacer 4500 par le nombre RFC correct).

## <a name="overview-of-the-generic-ldap-connector"></a>Vue d’ensemble du connecteur LDAP générique
Le connecteur LDAP générique vous permet d’intégrer le service de synchronisation avec un serveur v3 LDAP.

Certaines opérations et les éléments de schéma, tels que ceux nécessaires pour effectuer l’importation delta, ne sont pas spécifiés dans les RFC IETF. Pour effectuer ces opérations, seuls les annuaires LDAP explicitement spécifiés sont pris en charge.

Du point de vue globale, les fonctionnalités suivantes sont prises en charge par la version actuelle du connecteur :

Fonctionnalité | Prise en charge
--- | --- |
Source de données connectée | Le connecteur est pris en charge avec tous les serveurs v3 LDAP (RFC 4510 compatible). Il a été testé avec les éléments suivants : <li>Microsoft Active Directory Lightweight Directory Services (l)</li><li>Catalogue Global Microsoft Active Directory (AD catalogue global)</li><li>Serveur d’annuaire 389</li><li>Serveur d’annuaire Apache</li><li>IBM Tivoli DS</li><li>Répertoire Isode</li><li>NetIQ eDirectory</li><li>Novell eDirectory</li><li>DJ ouvert</li><li>Service d’annuaire ouvert</li><li>Ouvrir LDAP (openldap.org)</li><li>Oracle (précédemment Dim) Directory Server Enterprise Edition</li><li>Serveur d’annuaire virtuel RadiantOne (VDS)</li><li>Dim un serveur d’annuaire</li>**Annuaires remarquables ne pas pris en charge :** <li>Microsoft Active Directory Domain Services (AD DS) [utiliser le connecteur Active Directory intégrés à la place]</li><li>Annuaire Internet Oracle (OID)</li>
Scénarios   | <li>Gestion du cycle de vie des objets</li><li>Gestion de groupe</li><li>Gestion de mot de passe</li>
Opérations |Les opérations suivantes sont prises en charge sur tous les annuaires LDAP : <li>Importation complète</li><li>Exporter</li>Les opérations suivantes sont uniquement pris en charge dans des répertoires spécifiés :<li>Importation delta</li><li>Définir le mot de passe, modifier le mot de passe</li>
Schéma | <li>Schéma est détecté à partir du schéma LDAP (RFC3673 et RFC4512/4.2)</li><li>Prend en charge les classes structurelles, aux classes et extensibleObject objet classe (RFC4512/4.3)</li>

### <a name="delta-import-and-password-management-support"></a>Prise en charge gestion delta importer et mot de passe
Répertoires pris en charge pour l’importation de Delta et gestion de mot de passe :

- Microsoft Active Directory Lightweight Directory Services (l)
    - Prend en charge toutes les opérations pour l’importation delta
    - Prend en charge définie le mot de passe
- Catalogue Global Microsoft Active Directory (AD catalogue global)
    - Prend en charge toutes les opérations pour l’importation delta
    - Prend en charge définie le mot de passe
- Serveur d’annuaire 389
    - Prend en charge toutes les opérations pour l’importation delta
    - Prend en charge définie le mot de passe et modification
- Serveur d’annuaire Apache
    - Ne reconnaît pas importer delta ce répertoire n’ayant pas un journal des modifications permanente
    - Prend en charge définie le mot de passe
- IBM Tivoli DS
    - Prend en charge toutes les opérations pour l’importation delta
    - Prend en charge définie le mot de passe et modification
- Répertoire Isode
    - Prend en charge toutes les opérations pour l’importation delta
    - Prend en charge définie le mot de passe et modification
- Novell eDirectory et eDirectory NetIQ
    - Prend en charge l’ajout, mise à jour et renommer les opérations pour l’importation delta
    - Ne prend pas en charge les opérations de suppression pour l’importation delta
    - Prend en charge définie le mot de passe et modification
- DJ ouvert
    - Prend en charge toutes les opérations pour l’importation delta
    - Prend en charge définie le mot de passe et modification
- Service d’annuaire ouvert
    - Prend en charge toutes les opérations pour l’importation delta
    - Prend en charge définie le mot de passe et modification
- Ouvrir LDAP (openldap.org)
    - Prend en charge toutes les opérations pour l’importation delta
    - Prend en charge définie le mot de passe
    - Ne prend pas en charge la modification mot de passe
- Oracle (précédemment Dim) Directory Server Enterprise Edition
    - Prend en charge toutes les opérations pour l’importation delta
    - Prend en charge définie le mot de passe et modification
- Serveur d’annuaire virtuel RadiantOne (VDS)
    - Doit être à l’aide de version 7.1.1 ou une version ultérieure
    - Prend en charge toutes les opérations pour l’importation delta
    - Prend en charge définie le mot de passe et modification
-  Dim un serveur d’annuaire
    - Prend en charge toutes les opérations pour l’importation delta
    - Prend en charge définie le mot de passe et modification

### <a name="prerequisites"></a>Conditions préalables
Avant d’utiliser le connecteur, vérifiez que vous disposez des actions suivantes sur le serveur de synchronisation :

- 4.5.2 de Microsoft .NET Framework ou version ultérieure

### <a name="detecting-the-ldap-server"></a>Détecter le serveur LDAP
Le lien dépend de différentes techniques permettant de détecter et d’identifier le serveur LDAP. Le connecteur utilise le DSE racine, le nom/version du fournisseur, et elle examine le schéma pour rechercher des objets uniques et les attributs connus exister dans certains serveurs LDAP. Ces données, si trouvé, est utilisé pour les insérées dans les options de configuration dans le lien.

### <a name="connected-data-source-permissions"></a>Autorisations de Source de données connectées
Pour effectuer l’importation et exportation opérations sur les objets dans l’annuaire connecté, le compte de connecteur doit disposer des autorisations nécessaires. Le lien doit écrire des autorisations pour pouvoir exporter et droits de lecture pour pouvoir importer. Configuration d’autorisation s’effectue au sein de l’expérience de gestion du répertoire cible lui-même.

### <a name="ports-and-protocols"></a>Ports et protocoles
Le connecteur utilise le numéro de port spécifié dans la configuration, qui est 389 pour LDAP et 636 pour LDAPS par défaut.

Pour LDAPS, vous devez utiliser SSL 3.0 ou TLS. SSL 2.0 n’est pas prise en charge et ne peut pas être activé.

### <a name="required-controls-and-features"></a>Fonctionnalités et contrôles nécessaires
Les contrôles/fonctionnalités LDAP suivantes doivent être disponibles sur le serveur LDAP pour le connecteur fonctionne correctement :  
`1.3.6.1.4.1.4203.1.5.3`Filtres vrai/faux

Le filtre vrai/faux est souvent déclarée comme non pris en charge par les annuaires LDAP et peut apparaît dans la **Page Général** sous **Fonctionnalités obligatoire introuvable**. Il est utilisé pour créer des filtres **ou** dans les requêtes LDAP, par exemple lors de l’importation de plusieurs types d’objets. Si vous pouvez importer plusieurs type d’objet, votre serveur LDAP prend en charge cette fonctionnalité.

Si vous utilisez un répertoire où un identificateur unique est le point d’ancrage les conditions suivantes doivent être également disponible (voir la section [Configurer les points d’ancrage](#configure-anchors) plus loin dans cet article pour plus d’informations) :  
`1.3.6.1.4.1.4203.1.5.1`Tous les attributs opérationnels

Si le répertoire a davantage d’objets que celui pouvant être affiché dans un appel à l’annuaire, il est recommandé d’utiliser la pagination. Pour la pagination pour l’utiliser, vous devez une des options suivantes :

**Option 1 :**  
`1.2.840.113556.1.4.319`pagedResultsControl

**Option 2 :**  
`2.16.840.1.113730.3.4.9`VLVControl  
`1.2.840.113556.1.4.473`SortControl

Si les deux options sont activées dans la configuration du connecteur, pagedResultsControl est utilisé.

`1.2.840.113556.1.4.417`ShowDeletedControl

ShowDeletedControl est utilisé uniquement avec la méthode d’importation delta USNChanged puissent voir les objets supprimés.

Le connecteur essaie de détecter les options présentes sur le serveur. Si les options ne peuvent pas être détectées, un avertissement est présent dans la page générale dans les propriétés du connecteur. Pas tous les serveurs LDAP présenter tous les contrôles/fonctionnalitésent ils prennent en charge et même si ce message d’avertissement est présente, le connecteur peut fonctionner sans problème.

### <a name="delta-import"></a>Importation delta
Importation delta est disponible uniquement lorsqu’un répertoire de prise en charge a été détecté. Les méthodes suivantes sont utilisées pour le moment :

- Accesslog LDAP. Consultez [http://www.openldap.org/doc/admin24/overlays.html#Access de l’enregistrement](http://www.openldap.org/doc/admin24/overlays.html#Access Logging)
- Changelog LDAP. Voir [http://tools.ietf.org/html/draft-good-ldap-changelog-04](http://tools.ietf.org/html/draft-good-ldap-changelog-04)
- Horodatage. Pour Novell/NetIQ eDirectory, le connecteur utilise la dernière date/heure pour obtenir créé et mises à jour des objets. Novell/NetIQ eDirectory ne propose pas que d’équivalent signifie récupérer des objets supprimés. Cette option peut également être utilisée si aucune autre méthode d’importation delta n’est actif sur le serveur LDAP. Cette option n’est pas en mesure d’importer supprimé des objets.
- USNChanged. Voir : [https://msdn.microsoft.com/library/ms677627.aspx](https://msdn.microsoft.com/library/ms677627.aspx)

### <a name="not-supported"></a>Non pris en charge
Les fonctionnalités LDAP suivantes ne sont pas prises en charge :

- Références LDAP entre les serveurs (RFC 4511/4.1.10)

## <a name="create-a-new-connector"></a>Créer un connecteur
Pour créer un lien LDAP générique, sélectionnez **Agent de gestion** et **créer**dans **Le Service de synchronisation** . Sélectionnez le lien **LDAP générique (Microsoft)** .

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericldap/createconnector.png)

### <a name="connectivity"></a>Connectivité
Dans la page de connexion, vous devez spécifier les informations d’hôte, Port et liaison. Selon laquelle liaison est sélectionné, d’autres informations peuvent être fournies dans les sections suivantes.

![Connectivité](./media/active-directory-aadconnectsync-connector-genericldap/connectivity.png)

- Le paramètre de délai de connexion est utilisé uniquement pour la première connexion au serveur lors de la détection le schéma.
- Si la liaison est anonyme, puis ni nom d’utilisateur / mot de passe ni certificat sont utilisés.
- Pour les autres liaisons, entrez les informations soit en nom d’utilisateur / mot de passe ou sélectionnez un certificat.
- Si vous utilisez Kerberos pour l’authentification, fournissent également le domaine/domaine de l’utilisateur.

La zone de texte **alias attribut** est utilisée pour les attributs définis dans le schéma de syntaxe RFC4522. Les attributs ne peuvent pas être détectés pendant la détection de schéma et le connecteur a besoin d’aide pour identifier les attributs. Par exemple ce qui suit est nécessaire pour renseigner dans la zone alias attribut à identifier correctement l’attribut userCertificate en tant qu’un attribut binaire :

`userCertificate;binary`

Voici un exemple de comment cette configuration peut ressembler à :

![Connectivité](./media/active-directory-aadconnectsync-connector-genericldap/connectivityattributes.png)

Activez la case à cocher **inclure les attributs opérationnels schéma** également inclure des attributs créés par le serveur. Ils incluent les attributs tels que lorsque l’objet a été créé et dernière mise à jour de temps.

Sélectionnez **inclure des attributs extensibles dans schéma** si objets extensibles (RFC4512/4.3) sont utilisées et l’activation de cette option permet à chaque attribut à utiliser dans tous les objets. Cette option vous permet le schéma de grands, à moins que l’annuaire connecté à l’aide de cette fonctionnalité est recommandé de conserver l’option cette option est désactivée.

### <a name="global-parameters"></a>Paramètres globaux
Dans la page Paramètres globaux, vous configurez le nom de domaine au journal des modifications delta et autres fonctionnalités LDAP. La page est pré-rempli avec les informations fournies par le serveur LDAP.

![Connectivité](./media/active-directory-aadconnectsync-connector-genericldap/globalparameters.png)

La partie supérieure affiche les informations fournies par le serveur lui-même, telles que le nom du serveur. Le connecteur vérifie également que les contrôles obligatoires sont présents dans le DSE racine. Si ces contrôles ne sont pas répertoriés, un avertissement s’affiche. Certains répertoires LDAP ne pas répertorient toutes les fonctionnalités de la DSE racine et il est possible que le lien fonctionne sans problème, même si un message d’avertissement est présente.

Les cases à cocher **pris en charge les contrôles de** contrôlent le comportement de certaines opérations :

- Avec l’arborescence de la commande Supprimer est sélectionnée, une hiérarchie est supprimée avec un appel LDAP. Avec suppression de l’arborescence cette option est désactivée, le connecteur effectue une suppression récursive si nécessaire.
- Avec les résultats paginés sélectionnés, le connecteur effectue une importation paginée avec la taille spécifiée dans les étapes d’exécution.
- La VLVControl et SortControl est une alternative à le pagedResultsControl pour lire les données à partir de l’annuaire LDAP.
- Si les trois options (pagedResultsControl, VLVControl et SortControl) sont désactivées le connecteur importe tous les objets en une seule opération, lequel peut échouer s’il s’agit d’un répertoire volumineux.
- ShowDeletedControl est utilisé uniquement lorsque la méthode d’importation Delta est USNChanged.

Le journal des modifications de nom de domaine est le contexte d’appellation utilisé par le journal des modifications delta, par exemple **cn = changelog**. Cette valeur est obligatoire pour pouvoir effectuer l’importation delta.

Voici une liste par défaut de journal des modifications DNs :

Répertoire | Journal des modifications delta
--- | ---
Microsoft ADAM et AD catalogue global | Détecté automatiquement. USNChanged.
Serveur d’annuaire Apache | N’est pas disponible.
Répertoire 389 | Journal des modifications. Valeur à utiliser par défaut : **cn = changelog**
IBM Tivoli DS | Journal des modifications. Valeur à utiliser par défaut : **cn = changelog**
Répertoire Isode | Journal des modifications. Valeur à utiliser par défaut : **cn = changelog**
Novell/NetIQ eDirectory | N’est pas disponible. Horodatage. Les utilisations de connecteur dernière mise à jour de date/heure pour obtenir des mises à jour des enregistrements.
Ouvrir DJ/DS | Journal des modifications.  Valeur à utiliser par défaut : **cn = changelog**
Ouvrir LDAP | Journal d’accès. Valeur à utiliser par défaut : **cn = accesslog**
Oracle DSEE | Journal des modifications. Valeur à utiliser par défaut : **cn = changelog**
RadiantOne VDS | Répertoire virtuel. Dépend de l’annuaire connecté à VDS.
Dim un serveur d’annuaire | Journal des modifications. Valeur à utiliser par défaut : **cn = changelog**

L’attribut de mot de passe est le nom de l’attribut que le lien doit utiliser pour définir le mot de passe dans le nouveau mot de passe et mot de passe défini opérations.
Cette valeur par défaut est défini à **userPassword** mais peut être modifiée lorsque cela est nécessaire pour un système LDAP particulier.

Dans la liste des partitions supplémentaires, il est possible d’ajouter des espaces de noms supplémentaires ne détectés pas automatiquement. Par exemple, ce paramètre peut être utilisé si plusieurs serveurs constituent un cluster logique, qui doit être importé en même temps. Tout comme Active Directory peut avoir plusieurs domaines d’une forêt mais tous les domaines partagent un schéma, le même pouvez simulé en entrant les espaces de noms supplémentaires dans cette zone. Chaque espace de noms peut importer à partir de différents serveurs et est configuré davantage sur la page de configuration des Partitions et des hiérarchies. Utilisez Ctrl + Entrée pour obtenir une nouvelle ligne.

### <a name="configure-provisioning-hierarchy"></a>Configurer la hiérarchie mise en service
Cette page est utilisée pour établir une correspondance entre le composant de nom de domaine, par exemple unité d’organisation, le type d’objet qui doit être mis en service, par exemple unité d’organisation.

![Mise en service de la hiérarchie](./media/active-directory-aadconnectsync-connector-genericldap/provisioninghierarchy.png)

En configurant hiérarchie mise à disponibilité, vous pouvez configurer le connecteur pour créer automatiquement une structure lorsque cela est nécessaire. Par exemple, s’il existe un espace de noms dc = contoso, dc = com et un nouvelle cn objet = Jean, unité d’organisation = Seattle, c = US, dc = contoso, dc = com est mis en service, puis le lien peut créer un objet de pays type pour la France et une unité d’organisation pour Seattle si ceux ne sont pas déjà présents dans l’annuaire.

### <a name="configure-partitions-and-hierarchies"></a>Configurez des Partitions et hiérarchies
Dans la page partitions et hiérarchies, sélectionnez tous les espaces de noms avec objets que vous envisagez d’importer et exporter.

![Partitions](./media/active-directory-aadconnectsync-connector-genericldap/partitions.png)

Pour chaque espace de noms, il est également possible de configurer les paramètres de connectivité qui seraient remplacer les valeurs spécifiées dans l’écran de connexion. Si ces valeurs restent leur valeur vide par défaut, les informations à partir de l’écran de connexion sont utilisées.

Il est également possible de choisir les conteneurs et unités d’organisation le lien doit importer à partir d’et exporter.

### <a name="configure-anchors"></a>Configurer les points d’ancrage
Cette page a toujours une valeur préconfigurée et ne peuvent pas être modifiée. Si le fournisseur du serveur a été identifié, le point d’ancrage peut renseignée à l’aide un attribut immuable, par exemple le GUID d’un objet. Si elle n’a pas été détecté ou est connu pour ne pas avoir un attribut immuable, le connecteur utilise le nom de domaine (nom unique) en tant que le point d’ancrage.

![points d’ancrage](./media/active-directory-aadconnectsync-connector-genericldap/anchors.png)

Voici une liste des serveurs LDAP et le point d’ancrage utilisé :

Répertoire | Attribut de point d’ancrage
--- | ---
Microsoft ADAM et AD catalogue global | GUID d’objet
Serveur d’annuaire 389 | nom de domaine
Répertoire Apache | nom de domaine
IBM Tivoli DS | nom de domaine
Répertoire Isode | nom de domaine
Novell/NetIQ eDirectory | GUID
Ouvrir DJ/DS | nom de domaine
Ouvrir LDAP | nom de domaine
Oracle ODSEE | nom de domaine
RadiantOne VDS | nom de domaine
Dim un serveur d’annuaire | nom de domaine

## <a name="other-notes"></a>Autres notes
Cette section fournit des informations des aspects qui sont spécifiques à ce connecteur ou pour d’autres raisons est important de connaître.

### <a name="delta-import"></a>Importation delta
Le filigrane delta dans l’annuaire LDAP Open est UTC date/heure. Pour cette raison, l’horloge entre le Service de synchronisation FIM et le protocole LDAP Open doit être synchronisé. Si ce n’est pas le cas, certaines entrées dans le journal des modifications delta sont peut-être omises.

Pour Novell eDirectory, l’importation delta ne détecte pas les suppressions d’objets. Pour cette raison, il est nécessaire exécuter une importation complète régulièrement pour trouver tous les objets supprimés.

Pour les répertoires avec un journal des modifications delta basé sur date/heure, il est recommandé pour exécuter une importation complète à des moments périodiques. Ce processus permet au moteur de synchronisation pour rechercher et les différences entre le serveur LDAP et qu’est actuellement dans l’espace de connecteur.

## <a name="troubleshooting"></a>Résolution des problèmes

-   Pour plus d’informations sur la façon d’activer la journalisation résoudre les problèmes le connecteur, voir [comment activer le suivi ETW des connecteurs](http://go.microsoft.com/fwlink/?LinkId=335731).
