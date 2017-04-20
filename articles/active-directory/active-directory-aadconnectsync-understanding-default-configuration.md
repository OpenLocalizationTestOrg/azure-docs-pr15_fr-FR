<properties
    pageTitle="Azure AD Connect synchronisation : présentation de la configuration par défaut | Microsoft Azure"
    description="Cet article décrit la configuration par défaut dans Azure AD Connect synchroniser."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>
<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Azure AD Connect synchronisation : présentation de la configuration par défaut
Cet article explique les règles prédéfinies de configuration. Il décrit les règles et les règles suivantes impact sur la configuration. Il vous guide également dans la configuration par défaut de synchronisation Azure AD Connect. L’objectif est que le lecteur comprenne comment fonctionne le modèle de configuration, nommé mise en service déclarative, dans un exemple concret. Cet article suppose que vous avez déjà installé et configurez synchronisation Azure AD Connect à l’aide de l’Assistant installation.

Pour mieux comprendre les détails du modèle de configuration, lisez [Comprendre la mise en service déclarative](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Règles de prédéfinies locales à Azure Active Directory
Les expressions suivantes sont accessibles dans la configuration prête à l’emploi.

### <a name="user-out-of-box-rules"></a>Règles de prédéfinies de l’utilisateur
Les règles suivantes s’appliquent également au type d’objet iNetOrgPerson.

Un objet utilisateur doit répondre aux éléments suivants pour être synchronisés :

- Doit avoir un sourceAnchor.
- Une fois que l’objet a été créé dans Azure AD, sourceAnchor ne pouvez pas modifier. Si la valeur est modifié en local, l’objet cesse de synchronisation jusqu'à ce que le sourceAnchor est modifié en sa valeur précédente.
- Doit avoir l’attribut accountEnabled (userAccountControl) rempli. Avec Active Directory local, celui-ci n’est toujours remplis et présenter.

Les objets utilisateur suivants ne sont **pas** synchronisés avec Azure AD :

- `IsPresent([isCriticalSystemObject])`. Assurez-vous de nombreux objets de prédéfinies dans Active Directory, tels que le compte d’administrateur intégré, ne sont pas synchronisés.
- `IsPresent([sAMAccountName]) = False`. Vérifiez que les objets utilisateur sans attribut sAMAccountName ne sont pas synchronisés. Le cas se produit uniquement pratiquement dans un domaine mis à niveau à partir de NT 4.
- `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Ne pas synchroniser le compte de service utilisé par synchronisation Azure AD Connect et ses versions antérieures.
- Ne pas synchroniser les comptes Exchange qui ne fonctionnent pas dans Exchange Online.
    - `[sAMAccountName] = "SUPPORT_388945a0"`
    - `Left([mailNickname], 14) = "SystemMailbox{"`
    - `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
    - `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
- Ne pas synchroniser les objets qui ne fonctionnent pas dans Exchange Online.
`CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
Ce masque binaire (& H21C07000) seraient filtrer les objets suivants :
    - Dossiers publics à extension messagerie
    - Boîte aux lettres du service Surveillance du système
    - Boîte aux lettres de base de données de boîte aux lettres (boîte aux lettres système)
    - Groupe de sécurité universel (ne s’appliquent pour un utilisateur, mais n’est présent pour des raisons d’héritage)
    - Groupe non universel (ne s’appliquent pour un utilisateur, mais n’est présent pour des raisons d’héritage)
    - Plan de boîte aux lettres
    - Boîte aux lettres de détection
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Ne synchronisez pas les objets de victime de réplication.

Les règles d’attribut suivantes s’appliquent :

- `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. L’attribut sourceAnchor n’est pas contribué à partir d’une boîte aux lettres liée. Il est supposé égal que si une boîte aux lettres liée a été trouvée, le compte réel est joint ultérieurement.
- Échange connexes attributs synchronisés uniquement si l' attribut **mailNickName** comporte une valeur.
- Lorsqu’il y a plusieurs forêts, attributs sont consommées dans l’ordre suivant :
    1. Attributs liés à la connexion (par exemple userPrincipalName) sont fournis à partir de la forêt avec un compte activé.
    2. Attributs que vous pouvez trouver dans une liste d’adresses globale Exchange (liste d’adresses globale) sont fournis à partir de la forêt avec une boîte aux lettres Exchange.
    3. Si aucune boîte aux lettres ne peut être trouvé, les attributs peuvent provenir d’une forêt.
    4. Échange connexes attributs (attributs techniques non visibles dans la liste d’adresses globale) sont fournis à partir de la forêt où `mailNickname ISNOTNULL`.
    5. S’il existe plusieurs forêts qui pourraient satisfaire à une de ces règles, l’ordre de création (date/heure) des connecteurs (forêts) est utilisé pour déterminer quelle forêt contribue les attributs.

### <a name="contact-out-of-box-rules"></a>Règles prédéfinies de contact
Un objet contact doit répondre aux éléments suivants pour être synchronisés :

- Le contact doit être à extension messagerie. Il est vérifié avec les règles suivantes :
    - `IsPresent([proxyAddresses]) = True)`. L’attribut proxyAddresses doit être rempli.
    - Vous trouverez une adresse de messagerie principale dans l’attribut proxyAddresses ou l’attribut mail. La présence d’un @ est utilisé pour vérifier que le contenu est une adresse de messagerie. Une de ces deux règles doit être évaluée pour True.
        - `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Existe-t-il une entrée avec « SMTP : » et s’il existe, pouvez un @ se trouve dans la chaîne ?
        - `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. Est l’attribut mail remplis et s’il s’agit, pouvez un @ se trouve dans la chaîne ?

Les objets de contact suivants ne sont **pas** synchronisés avec Azure AD :

- `IsPresent([isCriticalSystemObject])`. Assurer la synchronisation sans objets contact marquées comme critiques. Ne doivent pas être un avec une configuration par défaut.
- `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
- `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Ces objets ne fonctionnent pas dans Exchange Online.
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Ne synchronisez pas les objets de victime de réplication.

### <a name="group-out-of-box-rules"></a>Grouper les règles de prédéfinies
Un objet de groupe doit répondre aux éléments suivants pour être synchronisés :

- Doit avoir moins de 50 000 membres. Ce nombre est le nombre de membres dans le groupe local.
    - S’il comporte plus de membres avant la synchronisation démarre la première fois, le groupe n’est pas synchronisé.
    - Si le nombre de membres de l’évolution de lorsqu’il a été créé, puis lorsqu’elle atteint 50 000 membres l’arrêt synchronisation jusqu'à ce que le nombre de l’appartenance est inférieur à 50 000 à nouveau.
    - Remarque : Le nombre d’appartenance 50 000 est appliqué également par Azure AD. Vous n’êtes pas en mesure de synchroniser groupes avec les autres membres, même si vous modifiez ou supprimez cette règle.
- Si le groupe est un **Groupe de Distribution**, il doit également être activé pour la messagerie. Voir [les règles prédéfinies de Contact](#contact-out-of-box-rules) à cette règle est appliquée.

Les objets de groupe suivants ne sont **pas** synchronisés avec Azure AD :

- `IsPresent([isCriticalSystemObject])`. Assurez-vous de nombreux objets de prédéfinies dans Active Directory, par exemple du groupe Administrateurs intégrés, ne sont pas synchronisés.
- `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Groupe héritée utilisé par la synchronisation d’annuaire.
- `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Groupe de rôles.
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Ne synchronisez pas les objets de victime de réplication.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>Règles de prédéfinies ForeignSecurityPrincipal
FSP est joints à « tout » (\*) objet dans le méta-verse. En réalité, ce type de jointure se produit uniquement pour les utilisateurs et groupes de sécurité. Cette configuration garantit que les appartenances forêt croisée sont résolus et représentées correctement dans Azure AD.

### <a name="computer-out-of-box-rules"></a>Règles de prédéfinies ordinateur
Un objet ordinateur doit répondre aux éléments suivants pour être synchronisés :

- `userCertificate ISNOTNULL`. Seuls les ordinateurs Windows 10 remplissent cet attribut. Tous les objets ordinateur avec une valeur dans cet attribut sont synchronisés.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Comprendre le scénario prédéfinies de règles
Dans cet exemple, nous utilisons un déploiement avec une seule forêt compte (A), une forêt de ressources (R) et un répertoire Azure AD.

![Image avec une description du scénario](./media/active-directory-aadconnectsync-understanding-default-configuration/scenario.png)

Dans cette configuration, il est supposé égal qu'il y a un compte de la forêt compte activé et un compte désactivé dans la forêt ressource avec une boîte aux lettres liée.

Notre objectif avec la configuration par défaut est la suivante :

- Attributs liés à la connexion sont synchronisés à partir de la forêt avec le compte activé.
- Attributs que vous pouvez trouver dans la liste d’adresses globale (liste d’adresses globale) sont synchronisés à partir de la forêt avec la boîte aux lettres. Si aucune boîte aux lettres ne peut être trouvé, n’importe quel autre forêt est utilisé.
- Si une boîte aux lettres liée est trouvé, le compte activé lié doit trouvé pour l’objet à exporter vers Azure AD.

### <a name="synchronization-rule-editor"></a>Éditeur de règles de synchronisation
La configuration peut être affichée et les changements induite par l’outil de synchronisation règles de l’éditeur (SRE) et un raccourci vers celui-ci se trouve dans le menu Démarrer.

![Éditeur de règles de l’icône](./media/active-directory-aadconnectsync-understanding-default-configuration/sre.png)

Le SRE est un outil du kit de ressources et qu’il est installé avec la synchronisation Azure AD Connect. Pour pouvoir le démarrer, vous devez être membre du groupe ADSyncAdmins. Lors du démarrage, vous voyez ressembler à ceci :

![Règles de synchronisation entrant](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

Dans ce volet pour afficher toutes les règles de synchronisation créées pour votre configuration. Chaque ligne de la table est une règle de synchronisation. À gauche, sous Types de règles, les deux types sont répertoriés : entrant et sortant. Entrant et sortant est dans l’affichage du méta-verse. Vous vous apprêtez principalement pour se concentrer sur les règles de trafic entrant dans cette vue d’ensemble. La liste des règles de synchronisation dépend du schéma détecté dans Active Directory. Dans l’image ci-dessus, la forêt compte (fabrikamonline.com) n’a pas tous les services, tels que Exchange et Lync, et aucune règle synchronisation n’ont été créés pour ces services. Toutefois, dans la forêt de ressources (res.fabrikamonline.com) vous recherchez des règles de synchronisation pour ces services. Le contenu des règles est différent en fonction de la version détectée. Par exemple, dans un déploiement avec Exchange 2013, il existe plusieurs flux attribut configurés que dans Exchange 2010/2007.

### <a name="synchronization-rule"></a>Règle de synchronisation
Une règle de synchronisation est un objet de configuration avec un ensemble d’attributs s’étalant lorsqu’une condition est remplie. Il est également utilisé pour décrire la façon dont un objet dans un espace de connecteur est lié à un objet dans le méta-verse, appelé **jointure** ou **correspond à**. Les règles de synchronisation ont une valeur de priorité indiquant comment ils sont liés entre eux. Une règle de synchronisation avec une valeur numérique inférieure a la priorité et un conflit de flux attribut, priorité wins la résolution du conflit.

Par exemple, examinez la règle synchronisation **dans depuis Active Directory – utilisateur AccountEnabled**. Cochez cette ligne dans le SRE et sélectionnez **Modifier**.

Étant donné que cette règle est une règle de prédéfinies, vous recevez un avertissement s’affiche lorsque vous ouvrez la règle. Vous ne devez pas vous toutes les [modifications apportées aux règles de prédéfinies](active-directory-aadconnectsync-best-practices-changing-default-configuration.md), afin que vous êtes invité à confirmer que vos intentions. Dans ce cas, vous souhaitez afficher la règle. Sélectionnez **non**.

![Synchronisation règles d’avertissement](./media/active-directory-aadconnectsync-understanding-default-configuration/warningeditrule.png)

Une règle de synchronisation comprend quatre sections configuration : Description, étendue filtrer, les règles de jointure et les Transformations.

#### <a name="description"></a>Description
La première section fournit des informations de base telles que nom et une description.

![Description de l’onglet Éditeur de règles synchronisés ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruledescription.png)

Vous informations sont également disponibles sur le système connecté cette règle est liée à, dont le type d’objet dans le système connecté à que s’applique et le type d’objet du métaverse. Le type d’objet métaverse est toujours personne utilisiez lorsque le type d’objet source est un utilisateur, iNetOrgPerson ou contact. Le type d’objet métaverse doit jamais modifier afin qu’il est créé comme un type générique. Le Type de lien peuvent être défini sur Join, StickyJoin ou mise en service. Ce paramètre fonctionne avec la section règles de rejoindre et est décrite ultérieurement.

Vous pouvez également voir que cette règle de synchronisation est utilisée pour la synchronisation de mot de passe. Si un utilisateur se trouve à portée de cette règle de synchronisation, le mot de passe est synchronisé locales au cloud (en supposant que vous avez activé la fonctionnalité de synchronisation de mot de passe).

#### <a name="scoping-filter"></a>Étendue de filtre
La section Filtrer étendue est utilisée pour configurer lorsqu’une règle de synchronisation doit être appliqué. Étant donné que le nom de la règle de synchronisation vous examinez indique qu’il doit être appliqué uniquement pour les utilisateurs activés, l’étendue est configurée pour l' attribut de AD **userAccountControl** ne doit pas avoir le bit 2 définir. Lorsque le moteur de synchronisation trouve un utilisateur dans Active Directory, elle s’applique cette règle synchronisation lorsque **userAccountControl** est défini sur la valeur décimale 512 (utilisateurs activés normal). Il ne s’applique pas la règle lorsque l’utilisateur a **userAccountControl** définie sur 514 (utilisateur normal désactivé).

![Étendue onglet dans l’éditeur de règle de synchronisation ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilter.png)

Le filtre portée comporte des Clauses qui peuvent être imbriquées et groupes. Toutes les clauses à l’intérieur d’un groupe doivent être remplies pour une règle de synchronisation à appliquer. Lorsque plusieurs groupes sont définies, au moins un groupe doit être remplie pour la règle soit appliquée. Autrement dit, un ou logique est évalué entre les groupes et une logique et est évalué à l’intérieur d’un groupe. Exemple de cette configuration sont accessibles dans de synchronisation règle sortante **les AAD – Group Join**. Il existe plusieurs groupes de filtre de synchronisation, par exemple une pour les groupes de sécurité (`securityEnabled EQUAL True`) et l’autre pour les groupes de distribution (`securityEnabled EQUAL False`).

![Étendue onglet dans l’éditeur de règle de synchronisation ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilterout.png)

Cette règle est utilisée pour définir les groupes auxquels doivent être mis en service à Azure Active Directory. Groupes de distribution doivent être activé pour être synchronisé avec Azure AD pour la messagerie, mais pour les groupes de sécurité un message électronique n’est pas obligatoire.

#### <a name="join-rules"></a>Participer à des règles
La troisième section est utilisée pour configurer comment les objets dans l’espace de connecteur sont liés aux objets du métaverse. La règle que vous avez précédemment étudié configuration n’est pas tout pour participer à des règles, au lieu de cela vous vous apprêtez à examiner **dans depuis Active Directory – utilisateur joindre**.

![Onglet Règles de jointure dans l’éditeur de règle de synchronisation ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulejoinrules.png)

Le contenu de la règle de jointure dépend de l’option correspondante sélectionnée dans l’Assistant installation. Pour une règle de trafic entrant, l’évaluation commence par un objet dans l’espace de connecteur source et chaque groupe dans les règles de jointure est évaluée dans l’ordre. Si un objet source est évalué pour faire correspondre exactement d’un objet dans le méta-verse à l’aide de l’une des règles de jointure, les objets sont jointes. Si toutes les règles ont été évaluées et il n’existe aucune correspondance, puis le Type de lien dans la page description s’affiche. Si cette configuration est définie pour la **mise en service**, un nouvel objet est créé dans la cible, le métaverse. Pour configurer un nouvel objet au métaverse est également connue sous au **projet** un objet vers le métaverse.

Les règles de jointure ne sont évaluées une seule fois. Lorsqu’un objet espace connecteur et un objet de méta-verse sont jointes, ils restent joints dans la mesure où l’étendue de la règle de synchronisation est toujours satisfait.

Lors de l’évaluation des règles de synchronisation, qu’une seule règle de synchronisation avec les règles de jointure définis doit être placé dans l’étendue. Si plusieurs règles de la synchronisation avec des règles de jointure est trouvées pour un objet, une erreur est générée. Pour cette raison, la meilleure solution consiste à n'avoir qu’une seule règle de synchronisation avec jointure définie lorsque plusieurs règles de synchronisation se trouvent dans l’étendue d’un objet. Dans la configuration de prédéfinies pour la synchronisation Azure AD Connect, les règles suivantes peuvent se trouve en consultant le nom et recherchez ceux dont le mot **participer** à la fin du nom. Une règle de la synchronisation sans aucune règle de jointure définis s’applique les flux attribut lorsqu’une autre règle synchronisation de joindre les objets ou sa mise en service un nouvel objet dans la cible.

Si vous observez l’image ci-dessus, vous pouvez voir que la règle est tentez de rejoindre **objectSID** avec **msExchMasterAccountSid** (Exchange) et **msRTCSIP-OriginatorSid** (Lync), qui est ce que nous prévoyons dans une topologie de forêt compte ressource. Vous recherchez la même règle sur toutes les forêts. Il est supposé que chaque forêt peut être forêt un compte ou une ressource. Cette configuration fonctionne également si vous avez des comptes d’utilisateurs résident dans une seule forêt et n’ont pas à joindre.

#### <a name="transformations"></a>Transformations
La section transformation définit tous les flux attribut qui s’appliquent à l’objet cible lorsque les objets sont jointes et que l’étendue du filtre est remplie. Pour revenir à la **dans depuis Active Directory – utilisateur AccountEnabled** règle de synchronisation, vous avez trouvé les transformations suivantes :

![Transformations de l’onglet Éditeur de règles synchronisés ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruletransformations.png)

Pour mettre cette configuration en contexte, dans un déploiement de forêt compte ressource, il est prévu pour trouver un compte activé dans la forêt de compte et un compte désactivé dans la forêt ressource avec les paramètres Exchange et Lync. La règle de synchronisation vous examinez contient les attributs requis pour se connecter et les attributs s’écrit de la forêt lorsqu’il existe un compte activé. Tous les flux de ces attribut sont réunis dans une règle de synchronisation.

Une transformation peut avoir différents types : constante, Direct et Expression.

- Un flux constant est toujours transmise une valeur codé en dur. Dans ce cas, il définit toujours la valeur **True** dans le méta-verse attribut nommé **accountEnabled**.
- Un flux direct passe toujours la valeur de l’attribut de la source à l’attribut cible en tant que-est.
- La troisième flux est de type Expression et qu’il permet des configurations plus avancées.

Le langage d’expression est VBA (Visual Basic pour Applications), donc personnes ayant une expérience de Microsoft Office ou VBScript reconnaîtront le format. Attributs sont placés entre crochets, [attributeName]. Attribut et les noms de fonction respectent la casse, mais l’éditeur de règles de synchronisation évalue les expressions et fournir un message d’avertissement si l’expression n’est pas valide. Toutes les expressions sont exprimées sur une seule ligne avec des fonctions imbriquées. Pour afficher la puissance de la langue de configuration, voici le flux pour pwdLastSet, mais avec les commentaires supplémentaires insérés :

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Pour plus d’informations sur le langage d’expression pour les flux d’attributs, voir [Présentation des Expressions de mise en service déclarative](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) .

### <a name="precedence"></a>Priorité
Vous avez maintenant recherché dans certaines règles de synchronisation individuelles, mais les règles de travaillent ensemble dans la configuration. Dans certains cas, une valeur de l’attribut est contribuée à partir de plusieurs règles de synchronisation à l’attribut cible même. Dans ce cas, la priorité des attributs est utilisée pour déterminer quel attribut wins. Par exemple, examinez l’attribut sourceAnchor. Cet attribut est un attribut importantes doivent pouvoir se connecter à Azure AD. Vous pouvez trouver un flux des attributs de cet attribut dans deux règles de synchronisation différent, **dans depuis Active Directory – utilisateur AccountEnabled** et **dans depuis Active Directory – utilisateur commun**. En raison de la priorité des règles de la synchronisation, l’attribut sourceAnchor est contribué à partir de la forêt avec un compte activé tout d’abord lorsqu’il y a plusieurs objets liés à l’objet du métaverse. Si aucun compte activé, puis le moteur de synchronisation utilise la règle de synchronisation à tout **dans depuis Active Directory – utilisateur courantes**. Cette configuration garantit que même pour les comptes qui sont désactivés, il est toujours un sourceAnchor.

![Règles de synchronisation entrant](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

La priorité des règles de synchronisation est définie dans les groupes par l’Assistant installation. Toutes les règles d’un groupe ont le même nom, mais ils sont connectés à différents annuaires connectés. L’Assistant installation donne la règle **dans depuis Active Directory – utilisateur joindre** priorité plus élevée et il produit une itération sur tous les connecté répertoires AD. Ensuite, il continue avec les groupes de règles dans un ordre prédéfini suivantes. À l’intérieur d’un groupe, les règles sont ajoutés dans l’ordre que les connecteurs ont été ajoutés dans l’Assistant. Si un autre lien est ajouté dans l’Assistant, les règles de synchronisation sont réorganisées et règles du connecteur nouveau sont insérés dernière dans chaque groupe.

### <a name="putting-it-all-together"></a>Vue d’ensemble
Nous savent maintenant sur les règles de la synchronisation peut comprendre le fonctionne de la configuration avec les différentes règles de synchronisation. Si vous examinez un utilisateur et les attributs sont impliquées dans le métaverse, les règles sont appliquées dans l’ordre suivant :

Nom | Commentaire
:------------- | :-------------
Dans depuis Active Directory – utilisateur jointure | Règle pour joindre les objets d’espace de connecteur avec métaverse.
Dans depuis Active Directory – Compte_utilisateur activé | Attributs obligatoires pour se connecter à Active Directory Azure et Office 365. Nous voulons les attributs à partir du compte activé.
Dans depuis Active Directory – commun utilisateur d’Exchange | Attributs figurant dans la liste d’adresses globale. Nous part du principe que la qualité des données est préférable de la forêt dans lequel nous avons rencontré des boîtes aux lettres de l’utilisateur.
Dans depuis Active Directory – utilisateur courants | Attributs figurant dans la liste d’adresses globale. Au cas où nous n’avons trouvé une boîte aux lettres, tout autre objet joint peut contribuer à la valeur de l’attribut.
Dans depuis Active Directory – utilisateur Exchange | Il existe uniquement si Exchange a été détecté. Le texte passe tous les attributs Exchange infrastructure.
Dans depuis Active Directory – utilisateur Lync | Il existe uniquement si Lync n’a été détecté. Le texte passe tous les attributs de Lync infrastructure.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le modèle de configuration dans la [Présentation de la mise en service déclarative](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
- En savoir plus sur le langage d’expression dans les [Expressions de mise en service déclarative compréhension](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
- Poursuivez la lecture de fonctionne de la configuration de prédéfinies dans [compréhension des utilisateurs et des Contacts](active-directory-aadconnectsync-understanding-users-and-contacts.md)
- Découvrez comment effectuer un changement de pratique à l’aide de la mise en service déclarative comment [effectuer un changement de la configuration par défaut](active-directory-aadconnectsync-change-the-configuration.md).

**Rubriques de présentation**

- [Azure AD Connect synchronisation : comprendre et personnaliser la synchronisation](active-directory-aadconnectsync-whatis.md)
- [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
