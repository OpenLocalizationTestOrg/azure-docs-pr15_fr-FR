<properties
    pageTitle="Azure AD Connect synchronisation : présentation de la mise en service déclarative | Microsoft Azure"
    description="Décrit le modèle de configuration de mise à disponibilité déclarative dans Azure AD Connect."
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
    ms.date="08/29/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Azure AD Connect synchronisation : présentation de la mise en service déclarative
Cette rubrique explique le modèle de configuration dans Azure AD Connect. Le modèle est appelé approvisionnement déclarative et vous permet d’apporter une modifications en toute simplicité. Bien opérations décrites dans cette rubrique sont avancées et n’est nécessaire pour la plupart des scénarios de client.

## <a name="overview"></a>Vue d’ensemble
Mise en service déclarative est le traitement des objets provenant d’un annuaire connecté source et détermine la manière dont l’objet et les attributs doivent être transformées à partir d’une source à une cible. Traitement d’un objet dans un pipeline de synchronisation et le pipeline d’est le même pour les règles entrants et sortants. Une règle de trafic entrant est comprise entre un espace de connecteur et méta-verse et une règle sortante est comprise entre le méta-verse et un espace de connecteur.

![Pipeline de synchronisation](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/sync1.png)  

Le pipeline comporte plusieurs modules. Chacun d'entre eux est responsable particulier, le concept de la synchronisation de l’objet.

![Pipeline de synchronisation](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/pipeline.png)  

- Source, l’objet source
- [Étendue](#scope), recherche toutes les règles de synchronisation qui se trouvent dans l’étendue
- [Rejoindre](#join), détermine les relations entre l’espace de connecteur et méta-verse
- [Transformer](#transform), calcule modalités de transformation des attributs et le flux
- [Priorité](#precedence), résout dons attribut en conflit
- Cible, l’objet cible

## <a name="scope"></a>Étendue
Le module de l’étendue est l’évaluation d’un objet et détermine les règles qui sont dans la portée et doivent être inclus dans le traitement. Selon les valeurs des attributs sur l’objet, les règles de synchronisation différents sont évaluées pour être dans l’étendue. Par exemple, un utilisateur désactivé sans boîte aux lettres Exchange ne possède des règles différentes à un utilisateur activé avec une boîte aux lettres.  
![Étendue](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope1.png)  

L’étendue est définie comme des clauses et groupes. Les clauses sont à l’intérieur d’un groupe. Un et logique est utilisé entre toutes les clauses dans un groupe. Par exemple, (service = informatiques et pays = Danemark). Un ou logique est utilisé entre les groupes.

![Étendue](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope2.png)  
L’étendue dans cette image doit être lu comme (service = informatiques et pays = Danemark) ou (pays = Suède). Si le groupe 1 ou du groupe 2 est évalué à la valeur true, puis la règle est dans l’étendue.

Le module étendue prend en charge les opérations suivantes.

Opération | Description
--- | ---
ÉGAL, PAS ÉGALES | Comparaison de chaînes qui prend la valeur si valeur est égale à la valeur de l’attribut. Pour les attributs à valeurs multiples, voir ISIN et ISNOTIN.
LESSTHAN, LESSTHAN_OR_EQUAL | Une comparaison de chaînes qui prend la valeur si la valeur est inférieure de la valeur de l’attribut.
CONTIENT, NOTCONTAINS | Comparaison de chaînes qui prend la valeur si la valeur peut être trouvée quelque part à l’intérieur de valeur dans l’attribut.
STARTSWITH, NOTSTARTSWITH | Comparaison de chaînes qui prend la valeur si valeur est en début de la valeur de l’attribut.
ENDSWITH, NOTENDSWITH | Comparaison de chaînes qui prend la valeur si valeur est dans la fin de la valeur de l’attribut.
SUPÉRIEUR, GREATERTHAN_OR_EQUAL | Comparaison de chaînes qui prend la valeur si la valeur est supérieure à la valeur de l’attribut.
ISNULL, ISNOTNULL | Évalue si l’attribut est absent de l’objet. Si l’attribut n’est pas présenter et par conséquent null, la règle est dans l’étendue.
ISIN, ISNOTIN | Évalue si la valeur est présente dans l’attribut défini. Cette opération est la variation d’égal et pas égales à valeurs multiples. L’attribut est censée pour être un attribut à valeurs multiples et si la valeur peut être trouvée dans une des valeurs d’attributs, puis la règle se trouve dans l’étendue.
ISBITSET, ISNOTBITSET | Évalue si un bit particulier est défini. Par exemple, peut être utilisé pour évaluer les bits dans userAccountControl pour voir si un utilisateur est activé ou désactivé.
ISMEMBEROF, ISNOTMEMBEROF | La valeur doit contenir un nom unique à un groupe dans l’espace de connecteur. Si l’objet est un membre du groupe spécifié, la règle est dans l’étendue.

## <a name="join"></a>Jointure
Le module de jointure dans le pipeline de synchronisation est responsable de la recherche de la relation entre l’objet dans la source et un objet dans la cible. Sur une règle entrante, cette relation serait un objet dans un espace de connecteur recherche d’une relation à un objet dans le méta-verse.  
![Participer à entre SC et mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join1.png)  
L’objectif consiste à voir que si un objet est déjà dans le méta-verse, créé par un autre connecteur, il doit être associé. Par exemple, dans une forêt compte ressource l’utilisateur de la forêt compte doit être jointes avec l’utilisateur de la forêt ressource.

Jointures sont utilisés principalement sur les règles de trafic entrant pour joindre les objets espace de connecteur au même objet métaverse.

Les jointures sont définies comme un ou plusieurs groupes. À l’intérieur d’un groupe, vous avez des clauses. Un et logique est utilisé entre toutes les clauses dans un groupe. Un ou logique est utilisé entre les groupes. Les groupes sont traités dans l’ordre de haut en bas. Lorsqu’un groupe a trouvé exactement une correspondance avec un objet dans la cible, aucune autre règle de jointure n’est évaluées. Si zéro ou plus d’un objet est trouvé, traitement continue pour le groupe suivant de règles. Pour cette raison, les règles doivent être créés dans l’ordre premier plus explicite et plus floue à la fin.  
![Participer à la définition](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join2.png)  
Les jointures dans cette image sont traitées du haut vers le bas. Tout d’abord le pipeline de synchronisation voit il s'existe une correspondance sur employeeID. Si ce n’est pas le cas, la deuxième règle voit si le nom du compte peut être utilisé pour joindre les objets. Si tel n’est pas soit une correspondance, la règle troisième et dernière est une correspondance approximative plus en utilisant le nom d’utilisateur.

Si toutes les règles de jointure ont été évaluées et n’est pas exactement une correspondance, le **Type de liaison** dans la page **Description** est utilisé. Si cette option est définie pour la **mise en service**, un nouvel objet dans la cible est créé.  
![Mise en service ou participer à](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join3.png)  

Un objet doit contenir qu’une seule règle sync unique avec des règles de jointure étendue. S’il existe plusieurs règles de synchronisation dans lequel la jointure est définie, une erreur se produit. Priorité n’est pas utilisée pour résoudre les conflits de jointure. Un objet doit avoir une règle participer dans l’étendue des attributs de flux avec la même direction entrant/sortant. Si vous devez attributs de flux entrant et sortant au même objet, vous devez disposer entrante et une règle sortante synchroniser avec une jointure.

Jointure sortant a un comportement spécial lorsqu’il tente de mise en service d’un objet à un espace de connecteur cible. L’attribut de nom de domaine est utilisé d’abord une jointure inverse. S’il existe déjà un objet dans l’espace de connecteur cible avec le même nom de domaine, les objets sont jointes.

Le module de jointure est évalué uniquement une fois que lorsqu’une nouvelle règle de synchronisation est fourni dans l’étendue. Lorsqu’un objet est joint, il n’est pas disjonction même si les critères de jointure n’est plus remplie. Si vous souhaitez disjoindre un objet, la règle de synchronisation qui joint les objets devez vous déconnecter de l’étendue.

### <a name="metaverse-delete"></a>Supprimer métaverse
Un objet de méta-verse reste que tant qu’est une seule règle de synchronisation dans l’étendue avec le **Type de liaison** définie sur **mise en service** ou **StickyJoin**. Un StickyJoin est utilisé lorsqu’un connecteur n’est pas autorisé à mettre en service un nouvel objet dans le métaverse, mais si elle fait partie, doit être supprimé de la source avant la suppression de l’objet du métaverse.

Lorsqu’un objet de méta-verse est supprimé, tous les objets associés à une règle sortante synchronisation sont marquée pour la **mise en service** sont marqués pour une suppression.

## <a name="transformations"></a>Transformations
Les transformations permettent de définir comment les attributs doivent flux provenant de la source à la cible. Les flux peuvent avoir l’un des **types de flux**suivants : Direct, constante ou Expression. Un flux direct, passe une valeur de l’attribut en tant que-est sans transformations supplémentaires. Une valeur constante définit la valeur spécifiée. Une expression utilise le langage d’expression mise à disponibilité déclarative pour exprimer la manière dont la transformation doit être. Vous trouverez les détails pour le langage d’expression dans la rubrique [langage d’expression mise à disponibilité déclarative compréhension](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) .

![Mise en service ou participer à](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/transformations1.png)  

La case à cocher **appliquer une seule fois** définit que l’attribut doit être défini uniquement lorsque l’objet est initialement créé. Par exemple, cette configuration peut être utilisée pour définir un mot de passe initial d’un nouvel objet utilisateur.

### <a name="merging-attribute-values"></a>Fusion de valeurs d’attribut
Dans les flux d’attributs, il existe un paramètre pour déterminer si les attributs à valeurs multiples doivent être fusionnées à partir de plusieurs liens différents. La valeur par défaut est **mise à jour**, qui indique que la règle de synchronisation avec la priorité la plus élevée doit gagner.

![Fusionner des Types](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/mergetype.png)  

Il existe également **Fusionner** et **MergeCaseInsensitive**. Ces options permettent de fusionner les valeurs issues de différentes sources. Par exemple, il peut être utilisé pour fusionner l’attribut proxyAddresses ou de membres à partir de plusieurs forêts différentes. Lorsque vous utilisez cette option, toutes les règles de synchronisation dans l’étendue d’un objet doivent utiliser le même type de fusion. Vous ne pouvez pas définir **mise à jour** à partir d’un connecteur et **Fusionner** à partir d’un autre. Si vous essayez, vous recevez une erreur.

La différence entre **Fusionner** et **MergeCaseInsensitive** est comment traiter les valeurs des attributs en double. Le moteur de synchronisation vérifie que les valeurs en double ne sont pas insérés dans l’attribut cible. Avec **MergeCaseInsensitive**, valeurs en double avec une différence uniquement en cas de ne passent ne pas être présents. Par exemple, vous devez voir pas les deux "SMTP:bob@contoso.com" et "smtp:bob@contoso.com" dans l’attribut cible. **Fusionner** est uniquement examiner les valeurs exactes et plusieurs valeurs lorsqu’il existe uniquement une différence de cas peuvent être présentes.

L’option **Remplacer** est identique à la **mise à jour**, mais il n’est pas utilisé.

### <a name="control-the-attribute-flow-process"></a>Contrôler le processus de flux attribut
Lorsque plusieurs règles de la synchronisation entrante sont configurés pour contribuer à l’attribut du métaverse même, priorité est utilisée pour déterminer le gagnant. La règle de synchronisation avec la priorité la plus élevée (valeur numérique la plus faible) va contribuer la valeur. La même se produisent pour les règles de trafic sortant. La synchronisation règle avec wins priorité plus élevées et influencent la valeur à l’annuaire connecté.

Dans certains cas, au lieu d’envoyer une valeur, la règle de synchronisation doit déterminer le comportement d’autres règles. Il existe certains littéraux spéciales utilisés pour ce cas.

Pour les règles de synchronisation entrant, la **valeur NULL** littéral peut servir à indiquer ne qu’aucune valeur contribuer pour le flux. Une autre règle avec une priorité élevée peut contribuer à une valeur. Si aucune règle ne collaboré à une valeur, l’attribut du métaverse est supprimé. Pour une règle sortante, si **NULL** est la valeur finale après le traitement de toutes les règles de synchronisation, la valeur est supprimée dans l’annuaire connecté.

valeur littérale **AuthoritativeNull** est semblable, mais à la différence qu’aucune règle de priorité inférieure ne peut contribuer à une valeur **null** .

Un flux d’attribut permet également **IgnoreThisFlow**. Il est similaire à la valeur NULL dans la mesure où elle indique rien à contribuer. La différence est qu’elle ne supprime pas une valeur déjà existante dans la cible. C’est comme le flux des attributs n’a jamais été il.

Voici un exemple :

Vous pouvez trouver le flux suivant dans *les AD - utilisateur Exchange hybride* :  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Cette expression doit être lu comme : si la boîte aux lettres de l’utilisateur se trouve dans Azure AD, puis flux l’attribut à partir d’Azure AD pour AD. Dans le cas contraire, ne passent pas rien dans Active Directory. Dans ce cas, il conserve la valeur existante dans Active Directory.

### <a name="importedvalue"></a>ImportedValue
La fonction ImportedValue est différente de celle de toutes les autres fonctions dans la mesure où le nom de l’attribut doit être placés entre guillemets au lieu des crochets :  
`ImportedValue("proxyAddresses")`.

En règle générale, lors de la synchronisation un attribut utilise la valeur attendue, même si elle n’a pas encore été exportée ou une erreur a été reçue lors de l’exportation ( » en haut de la tour »). Une synchronisation entrante suppose qu’un attribut qui n’a pas encore atteint un annuaire connecté éventuellement atteint. Dans certains cas, il est important de synchroniser uniquement une valeur qui a été confirmée par l’annuaire connecté (« hologramme et delta importer tour »).

Exemple de cette fonction sont accessibles dans la règle de la synchronisation de prédéfinies *dans depuis Active Directory – utilisateur courantes à partir d’Exchange*. Dans Exchange hybride, la valeur ajoutée par Exchange online doit être synchronisée uniquement lorsqu’il a été confirmé que la valeur a été correctement exportée :  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Priorité
Lorsque plusieurs règles de synchronisation essaie de contribuer la même valeur de l’attribut à la cible, la valeur prioritaire est utilisée pour déterminer le gagnant. La règle avec la priorité la plus élevée, plus petite valeur numérique, va contribuer l’attribut dans un conflit.

![Fusionner des Types](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/precedence1.png)  

Ce type de classement peut servir à définir les flux d’attributs plus précis pour un petit sous-ensemble des objets. Par exemple, hors-de-zone-règles Assurez-vous que les attributs d’un compte activé (**Utilisateur AccountEnabled**) ont la priorité d’autres comptes.

Priorité peut être définie entre les connecteurs. Qui autorise les connecteurs avec meilleures données à envoyer tout d’abord les valeurs.

### <a name="multiple-objects-from-the-same-connector-space"></a>Plusieurs objets à partir de l’espace de connecteur même
Si vous avez plusieurs objets dans le même espace connecteur rejoint au même objet métaverse, priorité doit être ajustée. Si plusieurs objets sont dans l’étendue de la même règle de synchronisation, le moteur de synchronisation n’est pas en mesure de déterminer la priorité. Il est ambiguë quel objet source doit contribuer à la valeur au métaverse. Cette configuration est signalée comme ambiguë même si les attributs de la source ont la même valeur.  
![Plusieurs objets liés au même objet mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple1.png)  

Dans ce scénario, vous devez modifier l’étendue des règles de synchronisation pour les objets source disposent de règles de synchronisation différents dans l’étendue. Vous permet de définir la priorité différente.  
![Plusieurs objets liés au même objet mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le langage d’expression dans les [Expressions de mise en service déclarative compréhension](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
- Voir comment déclarative mise en service est utilisé de prédéfinies à [comprendre la configuration par défaut](active-directory-aadconnectsync-understanding-default-configuration.md).
- Découvrez comment effectuer un changement de pratique à l’aide de la mise en service déclarative comment [effectuer un changement de la configuration par défaut](active-directory-aadconnectsync-change-the-configuration.md).
- Continuer à lire le fonctionnement des utilisateurs et des contacts dans la [compréhension des utilisateurs et des Contacts](active-directory-aadconnectsync-understanding-users-and-contacts.md).

**Rubriques de présentation**

- [Azure AD Connect synchronisation : comprendre et personnaliser la synchronisation](active-directory-aadconnectsync-whatis.md)
- [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

**Rubriques de référence**

- [Azure AD Connect synchronisation : référence des fonctions](active-directory-aadconnectsync-functions-reference.md)
