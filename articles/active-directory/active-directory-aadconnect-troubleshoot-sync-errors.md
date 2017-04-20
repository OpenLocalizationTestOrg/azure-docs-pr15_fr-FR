<properties
    pageTitle="Azure AD Connect : Résolution des erreurs lors de la synchronisation | Microsoft Azure"
    description="Explique comment résoudre les erreurs lors de la synchronisation avec Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="troubleshooting-errors-during-synchronization"></a>Résolution des erreurs lors de la synchronisation
Erreurs peuvent se produire lorsque les données d’identité sont synchronisées à partir de Windows Server Active Directory (AD DS) à Azure Active Directory (AD Azure). Cet article fournit une vue d’ensemble des différents types d’erreurs de synchronisation, certains des scénarios occasionnant ces erreurs et potentiels façons de corriger les erreurs. Cet article inclut les types d’erreur courants et ne peut porter sur toutes les erreurs possibles.

 Cet article suppose que le lecteur doit être familiarisé avec sous-jacent [concevoir des concepts clés d’Azure AD et Azure AD Connect](active-directory-aadconnect-design-concepts.md).

Avec la dernière version d’Azure AD Connect \(août 2016 ou une version ultérieure\), un rapport d’erreurs de synchronisation est disponible dans le [Portail Azure](https://aka.ms/aadconnecthealth) dans le cadre d’Azure AD se connecter au niveau pour la synchronisation.


Démarrage 1 septembre 2016 [Azure Active Directory dupliquer attribut résilience](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) fonctionnalité sera activée par défaut pour tous les *nouveaux* Azure Active Directory clients. Cette fonctionnalité est automatiquement activée pour les clients existants dans les mois à venir.

Azure AD Connect effectue 3 types d’opérations à partir des répertoires il reste synchronisée : importation et exportation, de la synchronisation. Erreurs peuvent avoir lieu dans toutes les opérations. Cet article se concentre principalement sur les erreurs lors de l’exportation vers Azure Active Directory.

## <a name="errors-during-export-to-azure-ad"></a>Erreurs lors de l’exportation vers Azure Active Directory
Après section décrit les différents types d’erreurs de synchronisation peuvent se produire pendant l’opération d’exportation vers Azure Active Directory à l’aide du connecteur Azure AD. Ce connecteur peut être identifié par le format du nom en cours « contoso. *onmicrosoft.com*».
Erreurs lors de l’exportation vers Azure AD indiquent que l’opération \(ajouter, mettre à jour, supprimer etc.\) tenté d’Azure AD Connect \(moteur de synchronisation\) sur Azure Active Directory a échoué.

![Présentation des erreurs de l’exportation](.\media\active-directory-aadconnect-troubleshoot-sync-errors\Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Erreurs d’incompatibilité de données
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Description
- Lorsque Azure AD Connect \(moteur de synchronisation\) indique à Azure Active Directory pour ajouter ou mettre à jour des objets, Azure AD correspond à l’objet entrant à l’aide de l’attribut **sourceAnchor** à l’attribut **immutableId** d’objets dans Azure Active Directory. Cette correspondance s’appelle un **Disque correspond à**.
- Lorsque Azure AD **ne trouve pas** les objets qui correspond à **immutableId** attribut avec l’attribut **sourceAnchor** de l’objet entrante, avant de configurer un nouvel objet, il revient pour utiliser les attributs ProxyAddresses et UserPrincipalName pour trouver une correspondance. Cette correspondance est appelée un **Correspondent bordures**. Les bordures correspondent est conçu pour faire correspondre les objets déjà présents dans Azure Active Directory (qui sont générés dans Azure Active Directory) avec les nouveaux objets est ajouté ou mis à jour lors de la synchronisation qui représentent la même entité (utilisateurs, groupes) en local.
- Erreur **InvalidSoftMatch** se produit lorsque la correspondance exacte ne trouve pas de n’importe quel objet correspondante **et** bordures correspondent trouve un objet correspondant, mais cet objet comporte une valeur différente *immutableId* que *SourceAnchor*, suggérant que l’objet correspondant a été synchronisé avec un autre objet dans Active Directory local l’objet entrant.

En d’autres termes, pour la correspondance souple pour l’utiliser, l’objet doit être mis en correspondance souple avec ne doit pas avoir toute valeur pour l' *immutableId*. Si la valeur de n’importe quel objet avec *immutableId* avec une valeur est ignore la correspondance-dur mais aux critères de correspondance de bordures, que l’opération de donner une erreur de synchronisation InvalidSoftMatch.

Azure Active Directory schéma n’autorise pas de deux ou plusieurs objets à ont la même valeur les attributs suivants. \(Il s’agit pas d’une liste exhaustive.\)

- ProxyAddresses
- UserPrincipalName
- onPremisesSecurityIdentifier
- ObjectId

>[AZURE.NOTE] Fonctionnalité [Azure AD attribut dupliquer attribut résilience](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) est également être transférée en tant que le comportement par défaut d’Azure Active Directory.  Cela sera réduire le nombre d’erreurs de synchronisation vu par les Azure AD Connect (ainsi que d’autres clients synchronisation) à l’aide d’Azure AD plus résistant de la manière dont il gère les dupliqué attributs ProxyAddresses et UserPrincipalName présents dans les environnements locaux AD. Cette fonctionnalité ne résout pas les erreurs de duplication. Les données doivent donc être fixe. Mais il permet de mise en service de nouveaux objets qui sont bloqués dans le cas contraire est mis en service en raison de doublons dans Azure AD. Cela sera également réduire le nombre d’erreurs de synchronisation renvoyée pour le client de synchronisation.
Si cette fonctionnalité est activée pour votre client, vous ne verrez pas les erreurs de synchronisation InvalidSoftMatch vus au cours de la mise en service de nouveaux objets.


#### <a name="example-scenarios-for-invalidsoftmatch"></a>Scénarios d’exemple pour InvalidSoftMatch
1. Il existe deux ou plusieurs objets avec la même valeur de l’attribut ProxyAddresses dans Active Directory local. Un seul est prise configuré dans Azure AD.
2. Il existe deux ou plusieurs objets avec la même valeur userPrincipalName dans Active Directory local. Un seul est prise configuré dans Azure AD.
3. Un objet a été ajouté dans les locaux sur Active Directory avec la même valeur de l’attribut ProxyAddresses que celle d’un objet dans Azure Active Directory existant. L’objet ajouté en local n’est pas prise configuré dans Azure Active Directory.
4. Un objet a été ajouté en local Active Directory avec la même valeur de l’attribut userPrincipalName que celle d’un compte dans Azure Active Directory. L’objet n’est pas prise configuré dans Azure Active Directory.
5. Un compte synchronisé a été déplacé à partir de la forêt A à forêt b Azure AD Connect (moteur de synchronisation) utilisait attribut GUID d’objet pour calculer la SourceAnchor. Après le déplacement de la forêt, la valeur de la SourceAnchor est différente. Le nouvel objet (à partir de forêt B) ne parvient pas à synchroniser avec l’objet existant dans Azure Active Directory.
6. Un objet synchronisé avez accidentellement supprimé à partir d’Active Directory et un nouvel objet a été créé dans Active Directory pour la même entité (par exemple, l’utilisateur) sans supprimer le compte dans Azure Active Directory local. Le nouveau compte ne parvient pas à synchroniser avec l’objet Azure Active Directory existant.
7. Azure AD Connect a été désinstallé et ré-installé. Pendant la réinstallation, un attribut différent a été choisi comme le SourceAnchor. Tous les objets qui ont été synchronisées précédemment arrêter la synchronisation avec l’erreur InvalidSoftMatch.

#### <a name="example-case"></a>Exemple de cas :
1. **Bob Smith** est un utilisateur synchronisé dans Azure Active Directory local Active Directory *contoso.com*
2. Bob Smith **UserPrincipalName** est définie comme **bobs@contoso.com**.
3. **« abcdefghijklmnopqrstuv == «** est le **SourceAnchor** calculés par Azure AD Connect à l’aide de Bob Smith **GUID d’objet** sous locaux Active Directory, qui est **immutableId** pour Bob Smith dans Azure Active Directory.
4. Alexandre est également suivre les valeurs pour l’attribut **proxyAddresses** :
    - smtp:bobs@contoso.com
    - smtp:bob.smith@contoso.com
    - **smtp:bob@contoso.com**
5.  Un nouvel utilisateur, **Bob Taylor**, est ajouté aux locaux sur Active Directory.
6. De Bob Taylor **UserPrincipalName** est définie comme **bobt@contoso.com**.
7. **« abcdefghijkl0123456789 == » «** est le **sourceAnchor** calculé par Azure AD Connect à l’aide de Bob Taylor **GUID d’objet** sous locaux Active Directory. Objet de Bob Taylor n’a pas encore synchronisés avec Azure Active Directory.
8. Bob Taylor possède les valeurs suivantes pour l’attribut proxyAddresses
    - smtp:bobt@contoso.com
    - smtp:bob.taylor@contoso.com
    - **smtp:bob@contoso.com**
9. Lors de la synchronisation, Azure AD Connect reconnaît l’ajout de Bob Taylor dans Active Directory local et demander Azure AD pour effectuer la même modification.
10. Azure AD effectue tout d’abord correspondance exacte. En d’autres termes, elle recherche s’il est n’importe quel objet avec l’immutableId égal à » abcdefghijkl0123456789 == ». Correspondance exacte échoue comme aucun autre objet dans Azure AD n’aura ce immutableId.
11. Azure AD tentera puis flottant-match Bob Taylor. En d’autres termes, elle recherche s’il est n’importe quel objet avec proxyAddresses égal à trois valeurs, y comprissmtp:bob@contoso.com
12. Azure AD recherche objet Bob Smith à faire correspondre les critères de correspondance de bordures. Mais cet objet a la valeur immutableId = « abcdefghijklmnopqrstuv == ». qui indique cet objet a été synchronisé à partir d’un autre objet dans Active Directory local. Par conséquent, Azure AD ne peut pas bordures match ces objets et provoquera une erreur de synchronisation **InvalidSoftMatch** .

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Comment corriger l’erreur InvalidSoftMatch
Le plus souvent l’erreur InvalidSoftMatch est deux objets avec différent SourceAnchor \(immutableId\) ont la même valeur pour les attributs ProxyAddresses et/ou UserPrincipalName, qui sont utilisées pendant le processus de bordures correspondance sur Azure AD. Afin de résoudre la correspondance logicielle non valide

1.  Identifier le proxyAddresses dupliqué, userPrincipalName ou autre valeur de l’attribut à l’origine de l’erreur. Également identifier les deux \(ou plusieurs\) objets impliqués dans le conflit. Le rapport généré par [Azure AD se connecter au niveau pour la synchronisation](https://aka.ms/aadchsyncerrors) peut vous aider à identifier les deux objets.
2. Identifier l’objet qui doit toujours avoir la valeur dupliquée et objet qui ne doit pas.
3. Supprimez la valeur dupliquée de l’objet qui ne doit pas avoir cette valeur. Notez qu’il doit faire la modification dans le répertoire où l’objet est provenant de. Dans certains cas, vous devrez peut-être supprimer un des objets en conflit.
4. Si vous avez apporté la modification dans les locaux sur AD, permettent d’Azure AD Connect synchroniser les modifications.

Notez que le rapport d’erreurs de synchronisation dans Azure AD se connecter au niveau pour la synchronisation est mis à jour toutes les 30 minutes et inclut les erreurs de la dernière tentative de synchronisation.

>[AZURE.NOTE] ImmutableId, par définition, ne modifiez pas dans la durée de vie de l’objet. Si Azure AD Connect n’a pas été configuré avec des scénarios à l’esprit dans la liste ci-dessus, vous pouvez vous retrouver dans une situation où Azure AD Connect calcule une valeur différente de la SourceAnchor pour l’objet Active Directory qui représente la même entité (même utilisateur/groupe/contact etc.) qui comporte un objet AD Azure existant que vous voulez continuer à utiliser.

#### <a name="related-articles"></a>Articles connexes
- [Attributs en double ou non valides empêchent la synchronisation d’annuaires dans Office 365](https://support.microsoft.com/en-us/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Description
Azure AD essaie d’adoucie correspondre à deux objets, il est possible que deux objets différentes « type d’objet » (par exemple, utilisateur, groupe, Contact etc.) ont les mêmes valeurs pour les attributs permettant d’effectuer la correspondance bordures. Comme duplication de ces attributs n’est pas autorisée dans Azure AD, l’opération peut entraîner erreur de synchronisation « ObjectTypeMismatch ».

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Scénarios d’exemple pour ObjectTypeMismatch d’erreur
- Un groupe de sécurité de messagerie est créé dans Office 365. Administrateur ajoute un nouvel utilisateur ou un contact dans local AD (qui n’est pas synchronisé avec Azure AD encore) avec la même valeur pour l’attribut ProxyAddresses que celle du groupe Office 365.

#### <a name="example-case"></a>Exemple de cas

1. Administrateur crée un nouveau groupe de sécurité de messagerie dans Office 365 pour le service de taxe et fournit une adresse de messagerie en tant que tax@contoso.com. Cela affecte l’attribut ProxyAddresses pour ce groupe avec la valeur de**smtp:tax@contoso.com**
2. Un nouvel utilisateur rejoint Contoso.com et un compte est créé pour l’utilisateur en local avec la proxyAddress en tant que**smtp:tax@contoso.com**
3. Lorsque Azure AD Connect synchronise le nouveau compte d’utilisateur, il obtenez l’erreur « ObjectTypeMismatch ».

#### <a name="how-to-fix-objecttypemismatch-error"></a>Comment corriger l’erreur ObjectTypeMismatch
Le plus souvent l’erreur ObjectTypeMismatch est deux objets de type différent (utilisateur, groupe, Contact etc.) ont la même valeur pour l’attribut ProxyAddresses. Pour corriger le ObjectTypeMismatch :

1.  Identifier le proxyAddresses dupliqué (ou un autre attribut) valeur qui est à l’origine de l’erreur. Également identifier les deux \(ou plusieurs\) objets impliqués dans le conflit. Le rapport généré par [Azure AD se connecter au niveau pour la synchronisation](https://aka.ms/aadchsyncerrors) peut vous aider à identifier les deux objets.
2. Identifier l’objet qui doit toujours avoir la valeur dupliquée et objet qui ne doit pas.
3. Supprimez la valeur dupliquée de l’objet qui ne doit pas avoir cette valeur. Notez que vous devez apporter la modification dans le répertoire où l’objet est provenant de. Dans certains cas, vous devrez peut-être supprimer un des objets en conflit.
4. Si vous avez apporté la modification dans les locaux sur AD, permettent d’Azure AD Connect synchroniser les modifications. Rapport d’erreurs de synchronisation dans Azure AD se connecter au niveau pour la synchronisation est mis à jour toutes les 30 minutes et inclut les erreurs de la dernière tentative de synchronisation.


## <a name="duplicate-attributes"></a>Attributs en double
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Description
Azure Active Directory schéma n’autorise pas de deux ou plusieurs objets à ont la même valeur les attributs suivants. Il s’agit de que chaque objet dans Azure Active Directory est obligé d’avoir une valeur unique de ces attributs à une instance donnée.

- ProxyAddresses
- UserPrincipalName

Si Azure AD Connect tente d’ajouter un nouvel objet ou mettre à jour d’un objet existant avec une valeur pour les attributs ci-dessus déjà affecté à un autre objet dans Azure Active Directory, l’opération provoquera erreur de synchronisation « AttributeValueMustBeUnique ».
#### <a name="possible-scenarios"></a>Scénarios possibles :
1. Valeur en double est affectée à un objet déjà synchronisé, qui est en conflit avec un autre objet synchronisé.

#### <a name="example-case"></a>Exemple de cas :
1. **Bob Smith** est un utilisateur synchronisé dans Azure Active Directory local Active Directory contoso.com
2. Bob Smith **UserPrincipalName** local est définie comme **bobs@contoso.com**.
3. Alexandre est également suivre les valeurs pour l’attribut **proxyAddresses** :
    - smtp:bobs@contoso.com
    - smtp:bob.smith@contoso.com
    - **smtp:bob@contoso.com**
4. Un nouvel utilisateur, **Bob Taylor**, est ajouté aux locaux sur Active Directory.
5. De Bob Taylor **UserPrincipalName** est définie comme **bobt@contoso.com**.
6. **Alexandre Taylor** possède les valeurs suivantes pour l’attribut **ProxyAddresses** i. smtp:bobt@contoso.comII. smtp:bob.taylor@contoso.com
7. Objet de Bob Taylor est synchronisé avec Azure AD avec succès.
8. Administrateur décidé mettre à jour l’attribut **ProxyAddresses** de Bob Taylor avec la valeur suivante : j’ai. **smtp:bob@contoso.com**
9. Azure AD tentera de mettre à jour objet de Bob Taylor dans Azure AD avec la valeur ci-dessus, mais cette opération échouera en tant que que valeur ProxyAddresses est déjà affecté à Bob Smith, résultant erreur « AttributeValueMustBeUnique ».

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Comment corriger l’erreur AttributeValueMustBeUnique
Le plus souvent l’erreur AttributeValueMustBeUnique est deux objets avec différent SourceAnchor \(immutableId\) ont la même valeur pour les attributs ProxyAddresses et/ou UserPrincipalName. Pour corriger l’erreur AttributeValueMustBeUnique

1.  Identifier le proxyAddresses dupliqué, userPrincipalName ou autre valeur de l’attribut à l’origine de l’erreur. Également identifier les deux \(ou plusieurs\) objets impliqués dans le conflit. Le rapport généré par [Azure AD se connecter au niveau pour la synchronisation](https://aka.ms/aadchsyncerrors) peut vous aider à identifier les deux objets.
2. Identifier l’objet qui doit toujours avoir la valeur dupliquée et objet qui ne doit pas.
3. Supprimez la valeur dupliquée de l’objet qui ne doit pas avoir cette valeur. Notez qu’il doit faire la modification dans le répertoire où l’objet est provenant de. Dans certains cas, vous devrez peut-être supprimer un des objets en conflit.
4. Si vous avez apporté la modification dans les locaux sur AD, permettent d’Azure AD Connect synchroniser la modification de l’erreur pour le résoudre.

#### <a name="related-articles"></a>Articles connexes
-[Attributs en double ou non valides empêchent la synchronisation d’annuaires dans Office 365](https://support.microsoft.com/en-us/kb/2647098)


## <a name="data-validation-failures"></a>Échecs de Validation de données
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Description
Azure Active Directory applique les restrictions diverses des données avant d’autoriser ces données soient écrites dans l’annuaire. Il s’agit de vous assurer que les utilisateurs finaux les meilleures expériences possibles lors de l’utilisation des applications qui dépendent de ces données.
#### <a name="scenarios"></a>Scénarios
un. La valeur de l’attribut UserPrincipalName comporte des caractères non valide/non prises en charge.
b. L’attribut UserPrincipalName ne respecte pas le format requis.
#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Comment corriger l’erreur IdentityDataValidationFailed

un. Assurez-vous que l’attribut userPrincipalName prend en charge des caractères et format requis.

#### <a name="related-articles"></a>Articles connexes
- [Préparer la mise en service des utilisateurs via la synchronisation d’annuaires vers Office 365]( https://support.office.com/en-us/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)


### <a name="datavalidationfailed"></a>DataValidationFailed
#### <a name="description"></a>Description
Il s’agit d’un cas très spécifique génère une erreur de synchronisation **« DataValidationFailed »** lorsque le suffixe UserPrincipalName d’un utilisateur est modifié d’un domaine fédéré vers un autre domaine fédéré.

#### <a name="scenarios"></a>Scénarios
Pour un utilisateur synchronisé, le suffixe UserPrincipalName a été modifié à partir d’un domaine fédéré vers un autre domaine fédéré en local. Par exemple, *UserPrincipalName = bob@contoso.com * a été remplacé par *UserPrincipalName = bob@fabrikam.com *.

#### <a name="example"></a>Exemple
1. Bob Smith, un compte pour Contoso.com, obtient ajouté sous la forme d’un nouvel utilisateur dans Active Directory avec UserPrincipalNamebob@contoso.com
2. Alexandre se déplace vers une autre division de Contoso.com appelé Fabrikam.com et son UserPrincipalName est modifiébob@fabrikam.com
3. Les domaines contoso.com et fabrikam.com sont des domaines fédérés avec Azure Active Directory.
4. UserPrincipalName Bob n’est pas obtenir mis à jour et génère une erreur de synchronisation « DataValidationFailed ».

#### <a name="how-to-fix"></a>Comment résoudre le problème
Si suffixe de UserPrincipalName d’un utilisateur a été mis à jour à partir de bob@ **contoso.com** à bob@ **fabrikam.com**, où **contoso.com** et **fabrikam.com** sont **les domaines fédérés**, puis suivez les opérations suivantes pour résoudre l’erreur de synchronisation

1. Mettre à jour UserPrincipalName de l’utilisateur dans Azure Active Directory à partir de bob@contoso.com à bob@contoso.onmicrosoft.com. Vous pouvez utiliser la commande PowerShell suivante avec du Module Azure Active Directory PowerShell :`Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`

2. Autoriser la prochaine synchronisation essayer de synchronisation. Synchronisation de l’heure sera réussie et il met à jour la UserPrincipalName de Robert à bob@fabrikam.com comme prévu.


## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Description
Lorsqu’un attribut dépasse la limite de taille autorisée, la longueur maximale ou la limite du nombre définis par schéma Azure Active Directory, l’opération de synchronisation entraîne l’erreur de synchronisation **LargeObject** ou **ExceededAllowedLength** . Cette erreur se produit en général pour les attributs suivants

- userCertificate
- thumbnailPhoto
- proxyAddresses

### <a name="possible-scenarios"></a>Scénarios possibles
1. Attribut userCertificate Bob est le stockage de certificats trop affectées à Bob. Il peut s’agir de certificats plus anciens, qui a expiré.
2. ThmubnailPhoto Bob défini dans Active Directory est trop volumineux pour être synchronisés dans Azure Active Directory.
3. Au cours de remplissage automatique de l’attribut ProxyAddresses d’Active Directory, un objet est-il attribué > 500 ProxyAddresses.

### <a name="how-to-fix"></a>Comment résoudre le problème

1. Vérifiez que l’attribut à l’origine de l’erreur est dans la limite autorisée.

## <a name="related-links"></a>Liens connexes
- [Trouver les objets Active Directory dans le centre d’administration Active Directory] (https://technet.microsoft.com/library/dd560661.aspx)
- [Comment faire pour interroger Azure Active Directory pour un objet à l’aide d’Azure Active Directory PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
