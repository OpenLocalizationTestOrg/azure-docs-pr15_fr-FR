<properties
    pageTitle="Azure AD Connect synchronisation : référence des fonctions | Microsoft Azure"
    description="Référence des expressions de mise en service déclaratives de synchronisation Azure AD Connect."
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
    ms.date="08/23/2016"
    ms.author="andkjell;markvi"/>


# <a name="azure-ad-connect-sync-functions-reference"></a>Azure AD Connect synchronisation : référence des fonctions

Dans Azure AD Connect, les fonctions sont utilisées pour manipuler une valeur de l’attribut lors de la synchronisation.  
La syntaxe des fonctions est exprimée en utilisant le format suivant :  
`<output type> FunctionName(<input type> <position name>, ..)`

Si une fonction est surchargée et accepte comprend plusieurs, tous les comprend valides sont indiqués.  
Les fonctions sont fortement typées et ils vérifient que le type passé dans les correspondances le type de documentation.  
Si le type ne correspond pas, une erreur est générée.

Les types sont exprimés avec la syntaxe suivante :

- **emplacement** – en nombre binaire.
- **bool** – booléen
- **dt** – UTC Date/heure
- **énumération** – énumération des constantes connus
- **exp** – Expression, ce qui est censée produire un résultat booléen
- **mvbin** – binaire à valeurs multiples
- **mvstr** – chaîne à valeurs multiples
- **mvref** – référence à valeurs multiples
- **num** – numérique
- **ref** – référence
- **str** – chaîne
- **var** – une variante du (presque) tout autre type
- **void** – ne renvoie pas une valeur

Les fonctions avec les types **mvbin**, **mvstr**et **mvref** ne peuvent travailler sur les attributs à valeurs multiples. Fonctions avec **bin**, **str**et **ref** travailler sur des attributs à valeur unique et à valeurs multiples.

## <a name="functions-reference"></a>Référence des fonctions

Liste de fonctions | | | | |  
--------- | --------- | --------- | --------- | --------- | ---------
**Conversion** |  
[CBool](#cbool) | [CDate](#cdate) | [CGuid](#cguid) | [ConvertFromBase64](#convertfrombase64)
[ConvertToBase64](#converttobase64) | [ConvertFromUTF8Hex](#convertfromutf8hex) | [ConvertToUTF8Hex](#converttoutf8hex) | [CNum](#cnum)
[CRef](#cref) | [CStr](#cstr) | [StringFromGuid](#StringFromGuid) | [StringFromSid](#stringfromsid)
**Date / heure** |  
[DateAdd](#dateadd) | [DateFromNum](#datefromnum) | [FormatDateTime](#formatdatetime) | [Maintenant](#now)
[NumFromDate](#numfromdate) |  
**Répertoire** |  
[DNComponent](#dncomponent) | [DNComponentRev](#dncomponentrev) | [EscapeDNComponent](#escapedncomponent)
**Évaluation** |  
[IsBitSet](#isbitset) | [IsDate](#isdate) | [IsEmpty](#isempty) | [IsGuid](#isguid)
[IsNull](#isnull) | [IsNullOrEmpty](#isnullorempty) | [IsNumeric](#isnumeric) | [IsPresent](#ispresent) |
[IsString](#isstring) |  
**Mathématiques** |  
[Bitet](#bitand) | [Bitou](#bitor) | [RandomNum](#randomnum)
**Valeurs multiples** |  
[Contient](#contains) | [Nombre](#count) | [Élément](#item) | [ItemOrNull](#itemornull)
[Jointure](#join) | [RemoveDuplicates](#removeduplicates) | [Double affichage](#split) |
**Flux de programme** |  
[Erreur](#error) | [IIF](#iif)  | [Commutateur](#switch)
**Texte** |  
[GUID](#guid) | [InStr](#instr) | [InStrRev](#instrrev) | [LCase](#lcase)
[Gauche](#left) | [NBCAR](#len) | [LTrim](#ltrim) | [STXT](#mid)
[PadLeft](#padleft) | [PadRight](#padright) | [PCase](#pcase) | [Remplacer](#replace)
[ReplaceChars](#replacechars) | [Oui](#right) | [RTrim](#rtrim) | [Découper](#trim)
[UCase](#ucase) | [Word](#word)

----------
### <a name="bitand"></a>Bitet

**Description :**  
La fonction Bitet définit bits spécifiés sur une valeur.

**Syntaxe :**  
`num BitAnd(num value1, num value2)`

- valeur1, valeur2 : des valeurs numériques qui doivent être liées par and ensemble

**Remarques :**  
Cette fonction convertit les deux paramètres pour la représentation binaire et définit un peu à :

- 0 - si une ou les deux bits correspondants dans *le masque* et *indicateur* sont 0
- 1 - si les deux bits correspondants sont 1.

En d’autres termes, elle renvoie 0 dans tous les cas, sauf si les bits des deux paramètres correspondants sont 1.

**Exemple :**  
`BitAnd(&HF, &HF7)`  
Renvoie 7 car hexadécimale « F » et « F7 » correspondre à cette valeur.

----------
### <a name="bitor"></a>Bitou

**Description :**  
La fonction Bitou définit bits spécifiés sur une valeur.

**Syntaxe :**  
`num BitOr(num value1, num value2)`

- valeur1, valeur2 : des valeurs numériques qui doivent être or

**Remarques :**  
Cette fonction convertit les deux paramètres de la représentation binaire, définit un peu 1 si une ou les deux bits correspondants dans le masque et indicateur de 1 et 0 si les deux bits correspondants sont 0. En d’autres termes, elle renvoie 1 dans tous les cas sauf si les bits des deux paramètres correspondants sont 0.

----------
### <a name="cbool"></a>CBool

**Description :**  
La fonction CBool renvoie une valeur booléenne en fonction de l’expression évaluée

**Syntaxe :**  
`bool CBool(exp Expression)`

**Remarques :**  
Si l’expression correspond à une valeur différente de zéro, CBool renvoie la valeur True, sinon il retourne False.

**Exemple :**  
`CBool([attrib1] = [attrib2])`  

Retourne True si les deux attributs ont la même valeur.

----------
### <a name="cdate"></a>CDate

**Description :**  
Variant renvoie une valeur DateTime UTC d’une chaîne. Date/heure n’est pas un type d’attribut native synchronisé mais est utilisée par certaines fonctions.

**Syntaxe :**  
`dt CDate(str value)`

- Value : Une chaîne avec une date, heure et éventuellement fuseau horaire

**Remarques :**  
La chaîne retournée est toujours au format UTC.

**Exemple :**  
`CDate([employeeStartTime])`  
Heure de début renvoie qu'une valeur DateTime à partir de l’employé

`CDate("2013-01-10 4:00 PM -8")`  
Renvoie une date/heure indiquant « 2013-01-11 12:00 AM »

----------
### <a name="cguid"></a>CGuid

**Description :**  
La fonction CGuid convertit la chaîne d’un GUID en représentation binaire.

**Syntaxe :**  
`bin CGuid(str GUID)`

- Une chaîne mise en forme dans ce modèle : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ou {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

----------
### <a name="contains"></a>Contient

**Description :**  
La fonction Contains recherche une chaîne à l’intérieur d’un attribut à valeurs multiples

**Syntaxe :**  
`num Contains (mvstring attribute, str search)`-la casse  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)`-la casse

- attribut : l’attribut à valeurs multiples à rechercher.
- recherche : chaîne à rechercher dans l’attribut.
- Casetype : CaseInsensitive ou CaseSensitive.

Retourne index dans l’attribut à valeurs multiples où la chaîne a été trouvée. 0 est renvoyé si la chaîne est introuvable.

**Remarques :**  
Pour les attributs de chaîne à valeurs multiples, la recherche trouve sous-chaînes dans les valeurs.  
Pour les attributs de référence, la chaîne recherchée doit correspondre exactement à la valeur pour être considéré comme une correspondance.

**Exemple :**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Si l’attribut proxyAddresses possède une adresse de messagerie principale (indiqué par des lettres majuscules « SMTP : »), puis revenez à l’attribut proxyAddress, sinon renvoie une erreur.

----------
### <a name="convertfrombase64"></a>ConvertFromBase64

**Description :**  
La fonction ConvertFromBase64 convertit la valeur spécifiée en base 64 codé en une chaîne normale.

**Syntaxe :**  
`str ConvertFromBase64(str source)`-suppose Unicode pour le codage  
`str ConvertFromBase64(str source, enum Encoding)`

- source : chaîne encodée en base 64  
- Codage : Unicode, ASCII, UTF8

**Exemple**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Deux exemples renvoient «*Bonjour !*»

----------
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex

**Description :**  
La fonction ConvertFromUTF8Hex convertit la valeur Hex UTF8 codée spécifiée à une chaîne.

**Syntaxe :**  
`str ConvertFromUTF8Hex(str source)`

- source : chaîne encodée de 2 octets UTF8

**Remarques :**  
La différence entre cette fonction et ConvertFromBase64([],UTF8) dans que le résultat est convivial pour l’attribut de nom de domaine.  
Ce format est utilisé par Azure Active Directory comme nom de domaine.

**Exemple :**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Renvoie «*Bonjour !*»

----------
### <a name="converttobase64"></a>ConvertToBase64

**Description :**  
La fonction ConvertToBase64 convertit une chaîne en une chaîne en base 64 Unicode.  
Convertit la valeur d’un tableau de nombres entiers en sa représentation chaîne équivalente encodée avec des chiffres en base 64.

**Syntaxe :**  
`str ConvertToBase64(str source)`

**Exemple :**  
`ConvertToBase64("Hello world!")`  
Renvoie « SABlAGwAbABvACAAdwBvAHIAbABkACEA »

----------
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex

**Description :**  
La fonction ConvertToUTF8Hex convertit une chaîne à une valeur UTF8 hexadécimal codé.

**Syntaxe :**  
`str ConvertToUTF8Hex(str source)`

**Remarques :**  
Le format de sortie de cette fonction est utilisé par Azure Active Directory en tant que format de l’attribut nom de domaine.

**Exemple :**  
`ConvertToUTF8Hex("Hello world!")`  
Renvoie 48656C6C6F20776F726C6421

----------
### <a name="count"></a>Nombre

**Description :**  
La fonction NB renvoie le nombre d’éléments dans un attribut à valeurs multiples

**Syntaxe :**  
`num Count(mvstr attribute)`

----------
### <a name="cnum"></a>CNum

**Description :**  
La fonction CNum prend une chaîne et renvoie un type de données numérique.

**Syntaxe :**  
`num CNum(str value)`

----------
### <a name="cref"></a>CRef

**Description :**  
Convertit une chaîne en un attribut de référence

**Syntaxe :**  
`ref CRef(str value)`

**Exemple :**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

----------
### <a name="cstr"></a>CStr

**Description :**  
La fonction CStr convertit en un type de données chaîne.

**Syntaxe :**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

- valeur : peut être une valeur numérique, attribut de référence ou booléen.

**Exemple :**  
`CStr([dn])`  
Peut renvoyer « cn = Jean, dc = contoso, dc = com »

----------
### <a name="dateadd"></a>DateAdd

**Description :**  
Renvoie une valeur Date contenant une date à laquelle un intervalle de temps spécifié a été ajouté.

**Syntaxe :**  
`dt DateAdd(str interval, num value, dt date)`

- intervalle : expression chaîne qui est l’intervalle de temps que vous voulez ajouter. La chaîne doit avoir une des valeurs suivantes :
 - YYYY année
 - q : trimestre
 - m mois
 - y jour de l’année
 - d jour
 - w JOURSEM
 - ss semaine
 - h heure
 - n minutes
 - s deuxième
- valeur : le nombre d’unités que vous voulez ajouter. Il peut être positif (pour obtenir des dates futures) ou négatif (pour obtenir des dates dans le passé).
- date : DateTime représentant la date à laquelle l’intervalle est ajouté.

**Exemple :**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Ajoute 3 mois et renvoie une valeur DateTime qui représente « 2001-04-01 ».

----------
### <a name="datefromnum"></a>DateFromNum

**Description :**  
La convertit de fonction DateFromNum mettre en forme une valeur dans la date de l’annonce à un type de date/heure.

**Syntaxe :**  
`dt DateFromNum(num value)`

**Exemple :**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Renvoie une valeur DateTime représentant 2012-01-01 23:00:00

----------
### <a name="dncomponent"></a>DNComponent

**Description :**  
La fonction DNComponent renvoie la valeur d’un composant de nom de domaine spécifié allant de gauche.

**Syntaxe :**  
`str DNComponent(ref dn, num ComponentNumber)`

- nom de domaine : l’attribut de référence pour interpréter
- ComponentNumber : Le composant dans le nom de domaine pour renvoyer

**Exemple :**  
`DNComponent([dn],1)`  
Si le nom de domaine est « cn = Jean, unité d’organisation =..., « elle retourne Jean

----------
### <a name="dncomponentrev"></a>DNComponentRev

**Description :**  
La fonction DNComponentRev renvoie la valeur d’un composant de nom de domaine spécifié allant de droite (fin).

**Syntaxe :**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

- nom de domaine : l’attribut de référence pour interpréter
- ComponentNumber - le composant dans le nom de domaine pour renvoyer
- Options : DC – ignorer tous les composants avec « dc = »

**Exemple :**  
Si le nom de domaine est « cn = Jean, unité d’organisation = Atlanta, unité d’organisation = disponibilité générale, unité d’organisation = US, dc = contoso, dc = com » puis  
`DNComponentRev([dn],3)`  
`DNComponentRev([dn],1,"DC")`  
Les deux renvoient contactez-nous.

----------
### <a name="error"></a>Erreur

**Description :**  
La fonction d’erreur est utilisée pour renvoyer une erreur personnalisée.

**Syntaxe :**  
`void Error(str ErrorMessage)`

**Exemple :**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Si le nom de compte attribut n’est pas présente, génère une erreur sur l’objet.

----------
### <a name="escapedncomponent"></a>EscapeDNComponent

**Description :**  
La fonction EscapeDNComponent prend un composant d’un nom unique et la remplace afin qu’il peut être représenté dans l’annuaire LDAP.

**Syntaxe :**  
`str EscapeDNComponent(str value)`

**Exemple :**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Vérifie que l’objet peut être créé dans un annuaire LDAP même si l’attribut displayName comporte des caractères qui doivent être une séquence d’échappement dans l’annuaire LDAP.

----------
### <a name="formatdatetime"></a>FormatDateTime

**Description :**  
La fonction FormatDateTime est utilisée pour mettre en forme une valeur DateTime pour une chaîne avec une mise en forme spécifiée

**Syntaxe :**  
`str FormatDateTime(dt value, str format)`

- valeur : une valeur dans le format de date/heure
- format : une chaîne représentant le format à convertir.

**Remarques :**  
Les valeurs possibles pour la mise en forme sont accessibles ici : [Formats de Date/heure définis par l’utilisateur (Format, fonction)](http://msdn2.microsoft.com/library/73ctwf33\(VS.90\).aspx)

**Exemple :**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Résultats dans « 2007-12-25 ».

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Vous risquez « 20140905081453.0Z »

----------
### <a name="guid"></a>GUID

**Description :**  
La fonction GUID génère un nouveau GUID aléatoire

**Syntaxe :**  
`str GUID()`

----------
### <a name="iif"></a>IIF

**Description :**  
La fonction IIF renvoie un jeu de valeurs possibles selon une condition spécifiée.

**Syntaxe :**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

- condition : toute valeur ou expression qui peut donner true ou false.
- renvoie ValeurSiVrai : si la condition est vrai, la valeur renvoyée.
- ValeurSiFaux : si la condition est faux, la valeur renvoyée.

**Exemple :**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Si l’utilisateur est un interne, renvoie l’alias d’un utilisateur avec des « t-« ajouté au début de l’autre alias de l’utilisateur en l’état.

----------
### <a name="instr"></a>InStr

**Description :**  
La fonction InStr la première occurrence d’une sous-chaîne dans une chaîne

**Syntaxe :**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

- contrôle de chaîne : chaîne à rechercher
- correspondance de chaîne : chaîne est introuvable
- Démarrer : position pour trouver la sous-chaîne de départ
- comparer : vbTextCompare ou vbBinaryCompare

**Remarques :**  
Renvoie la position où la sous-chaîne a été trouvée ou 0 si non trouvé.

**Exemple :**  
`InStr("The quick brown fox","quick")`  
Dans la liste à 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Prend la valeur 7

----------
### <a name="instrrev"></a>InStrRev

**Description :**  
La fonction InStrRev recherche la dernière occurrence d’une sous-chaîne dans une chaîne

**Syntaxe :**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

- contrôle de chaîne : chaîne à rechercher
- correspondance de chaîne : chaîne est introuvable
- Démarrer : position pour trouver la sous-chaîne de départ
- comparer : vbTextCompare ou vbBinaryCompare

**Remarques :**  
Renvoie la position où la sous-chaîne a été trouvée ou 0 si non trouvé.

**Exemple :**  
`InStrRev("abbcdbbbef","bb")`  
Renvoie 7

----------
### <a name="isbitset"></a>IsBitSet

**Description :**  
La fonction IsBitSet teste si un bit est définie ou non

**Syntaxe :**  
`bool IsBitSet(num value, num flag)`

- valeur : une valeur numérique qui est evaluated.flag : une valeur numérique qui comporte le bit doit être évaluée

**Exemple :**  
`IsBitSet(&HF,4)`  
Renvoie vrai car bit « 4 » est défini dans la valeur hexadécimale « F »

----------
### <a name="isdate"></a>IsDate

**Description :**  
Si l’expression peut être évalue comme type de date/heure, la fonction IsDate prend la valeur True.

**Syntaxe :**  
`bool IsDate(var Expression)`

**Remarques :**  
Permet de déterminer si CDate() peut être réussie.

----------
### <a name="isempty"></a>IsEmpty

**Description :**  
Si l’attribut est présent dans la SC ou MV mais prend la valeur d’une chaîne vide, la fonction IsEmpty prend la valeur True.

**Syntaxe :**  
`bool IsEmpty(var Expression)`

----------
### <a name="isguid"></a>IsGuid

**Description :**  
Si la chaîne peut être convertie en GUID, la fonction IsGuid évalué à true.

**Syntaxe :**  
`bool IsGuid(str GUID)`

**Remarques :**  
Un GUID est défini comme une chaîne qui suit un de ces modèles : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ou {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Permet de déterminer si CGuid() peut être réussie.

**Exemple :**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Si la StrAttribute a un format GUID, renvoyer une représentation binaire, dans le cas contraire renvoient une valeur Null.

----------
### <a name="isnull"></a>IsNull

**Description :**  
Si l’expression est évaluée à Null, la fonction IsNull retourne true.

**Syntaxe :**  
`bool IsNull(var Expression)`

**Remarques :**  
Pour un attribut, une valeur Null est exprimée en l’absence de l’attribut.

**Exemple :**  
`IsNull([displayName])`  
Renvoie VRAI si l’attribut ne figure pas dans la SC ou MV.

----------
### <a name="isnullorempty"></a>IsNullOrEmpty

**Description :**  
Si l’expression est null ou une chaîne vide, la fonction IsNullOrEmpty retourne true.

**Syntaxe :**  
`bool IsNullOrEmpty(var Expression)`

**Remarques :**  
Pour un attribut, ce serait ont pour résultat vrai si l’attribut est absent ou est présente, mais est une chaîne vide.  
L’inverse de cette fonction est nommé IsPresent.

**Exemple :**  
`IsNullOrEmpty([displayName])`  
Renvoie VRAI si l’attribut ne figure pas ou est une chaîne vide dans la SC ou MV.

----------
### <a name="isnumeric"></a>IsNumeric

**Description :**  
La fonction IsNumeric renvoie une valeur booléenne indiquant si une expression peut être évaluée comme un type de numéro.

**Syntaxe :**  
`bool IsNumeric(var Expression)`

**Remarques :**  
Permet de déterminer si CNum() peut être réussi à analyser l’expression.

----------
### <a name="isstring"></a>IsString

**Description :**  
Si l’expression peut être évaluée à un type de chaîne, la fonction IsString prend la valeur True.

**Syntaxe :**  
`bool IsString(var expression)`

**Remarques :**  
Permet de déterminer si CStr() peut être réussie analyser l’expression.

----------
### <a name="ispresent"></a>IsPresent

**Description :**  
Si l’expression renvoie une chaîne qui n’est pas Null et n’est pas vide, la fonction IsPresent retourne true.

**Syntaxe :**  
`bool IsPresent(var expression)`

**Remarques :**  
L’inverse de cette fonction est nommé IsNullOrEmpty.

**Exemple :**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

----------
### <a name="item"></a>Élément

**Description :**  
La fonction Item renvoie un seul élément à partir d’un chaîne/attribut à valeurs multiples.

**Syntaxe :**  
`var Item(mvstr attribute, num index)`

- attribut : attribut à valeurs multiples
- index : index pour un élément dans la chaîne à valeurs multiples.

**Remarques :**  
La fonction de l’élément est utile avec la fonction Contains étant donné que la fonction de ce dernier retourne l’index pour un élément dans l’attribut à valeurs multiples.

Génère une erreur si l’index est en dehors des limites.

**Exemple :**  
`Mid(Item([proxyAddress],Contains([proxyAddress], "SMTP:")),6)`  
Renvoie l’adresse de messagerie principale.

----------
### <a name="itemornull"></a>ItemOrNull

**Description :**  
La fonction ItemOrNull renvoie un élément à partir d’un chaîne/attribut à valeurs multiples.

**Syntaxe :**  
`var ItemOrNull(mvstr attribute, num index)`

- attribut : attribut à valeurs multiples
- index : index pour un élément dans la chaîne à valeurs multiples.

**Remarques :**  
La fonction ItemOrNull est utile avec la fonction Contains étant donné que la fonction de ce dernier retourne l’index pour un élément dans l’attribut à valeurs multiples.

Si l’index est en dehors des limites, puis renvoie une valeur Null.

----------
### <a name="join"></a>Jointure

**Description :**  
La fonction de jointure prend une chaîne à plusieurs valeurs et retourne une chaîne à valeur unique avec séparateur spécifié insérée entre chaque élément.

**Syntaxe :**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

- attribut : attribut à valeurs multiples contenant des chaînes à joindre.
- le délimiteur : toute chaîne, utilisée pour séparer les sous-chaînes dans la chaîne retournée. En cas d’omission, le caractère espace (« ») est utilisé. Si le délimiteur est une chaîne de longueur nulle (« ») ou rien, tous les éléments dans la liste sont concaténées sans délimiteur.

**Remarques**  
Il y a disparités qui existent entre les fonctions joindre et fractionner. La fonction de jointure prend un tableau de chaînes et les joint à l’aide d’une chaîne de délimiteur, pour renvoyer une chaîne. La fonction Split prend une chaîne et la sépare au niveau du délimiteur, pour retourner un tableau de chaînes. Toutefois, une différence essentielle est que jointure peut concaténer des chaînes avec toute chaîne de délimiteur, fractionnement peut uniquement séparer des chaînes à l’aide d’un délimiteur de caractère unique.

**Exemple :**  
`Join([proxyAddresses],",")`  
Impossible de retour :"SMTP:john.doe@contoso.com,smtp:jd@contoso.com"

----------
### <a name="lcase"></a>LCase

**Description :**  
La fonction LCase convertit tous les caractères d’une chaîne en minuscules.

**Syntaxe :**  
`str LCase(str value)`

**Exemple :**  
`LCase("TeSt")`  
Retourne « test ».

----------
### <a name="left"></a>Gauche

**Description :**  
La fonction gauche renvoie un nombre spécifié de caractères à partir de la gauche d’une chaîne.

**Syntaxe :**  
`str Left(str string, num NumChars)`

- chaîne : la chaîne à renvoyer les caractères à partir de
- NumChars : un numéro identifiant le nombre de caractères à retourner à partir du début (à gauche) de chaîne

**Remarques :**  
Une chaîne contenant les premiers caractères numChars dans chaîne :

- Si numChars = 0, renvoyer une chaîne vide.
- Si numChars < 0, renvoient la chaîne d’entrée.
- Si la chaîne est null, renvoyer une chaîne vide.

Si la chaîne contient moins de caractères que la numChars spécifié dans nombre, une chaîne identique à la chaîne (c'est-à-dire, contenant tous les caractères dans le paramètre 1) est renvoyée.

**Exemple :**  
`Left("John Doe", 3)`  
Renvoie « Joh ».

----------
### <a name="len"></a>NBCAR

**Description :**  
La fonction NBCAR renvoie le nombre de caractères d’une chaîne.

**Syntaxe :**  
`num Len(str value)`

**Exemple :**  
`Len("John Doe")`  
Renvoie 8

----------
### <a name="ltrim"></a>LTrim

**Description :**  
La fonction LTrim supprime les espaces blancs à partir d’une chaîne.

**Syntaxe :**  
`str LTrim(str value)`

**Exemple :**  
`LTrim(" Test ")`  
Renvoie « Test »

----------
### <a name="mid"></a>STXT

**Description :**  
La fonction STXT renvoie un nombre spécifié de caractères à partir d’une position spécifiée dans une chaîne.

**Syntaxe :**  
`str Mid(str string, num start, num NumChars)`

- chaîne : la chaîne à renvoyer les caractères à partir de
- Démarrer : positionner un numéro identifiant le numéro de début dans la chaîne à renvoyer les caractères à partir de
- NumChars : un numéro identifiant le nombre de caractères à retourner à partir de la position dans une chaîne

**Remarques :**  
Renvoyer les caractères numChars en commençant à partir du début de position dans la chaîne.  
Une chaîne contenant des caractères numChars à partir du début de position dans la chaîne :

- Si numChars = 0, renvoyer une chaîne vide.
- Si numChars < 0, renvoient la chaîne d’entrée.
- Si Démarrer > la longueur de chaîne, renvoyer une chaîne d’entrée.
- Si Démarrer < = 0, renvoyer une chaîne d’entrée.
- Si la chaîne est null, renvoyer une chaîne vide.

S’il n’existe pas de caractères de NbCaractères restant dans la chaîne à partir du début de position, autant que possible est renvoyé.

**Exemple :**  
`Mid("John Doe", 3, 5)`  
Renvoie « hn ».

`Mid("John Doe", 6, 999)`  
Renvoie « Dupont »

----------
### <a name="now"></a>Maintenant

**Description :**  
La fonction maintenant renvoie une valeur DateTime spécifiant la date et l’heure, en fonction de la date système et l’heure de votre ordinateur.

**Syntaxe :**  
`dt Now()`

----------
### <a name="numfromdate"></a>NumFromDate

**Description :**  
La fonction NumFromDate renvoie une date au format de date de l’annonce.

**Syntaxe :**  
`num NumFromDate(dt value)`

**Exemple :**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Renvoie 129699324000000000

----------
### <a name="padleft"></a>PadLeft

**Description :**  
La PadLeft fonction gauche-remplit une chaîne à une longueur spécifiée à l’aide d’un caractère de remplissage fourni.

**Syntaxe :**  
`str PadLeft(str string, num length, str padCharacter)`

- chaîne : la chaîne de caractères de remplacement.
- longueur : un nombre entier représentant la longueur de chaîne souhaitée.
- padCharacter : une chaîne composée d’un caractère unique à utiliser comme caractère de remplissage

**Remarques :**

- Si la longueur de chaîne est inférieure à la longueur, padCharacter est ajouté à plusieurs reprises au début (à gauche) de la chaîne jusqu'à ce qu’il a une durée égale à la longueur.
- PadCharacter peut être un caractère d’espace, mais elle ne peut pas être une valeur null.
- Si la longueur de chaîne est égale ou supérieure à la longueur, la chaîne est renvoyée ne change pas.
- Si la chaîne a une longueur supérieure ou égale à la longueur, une chaîne identique à la chaîne est renvoyée.
- Si la longueur de chaîne est inférieure à la longueur, une nouvelle chaîne de longueur souhaitée est renvoyée chaîne contenant rempli avec une padCharacter.
- Si la chaîne est null, la fonction retourne une chaîne vide.

**Exemple :**  
`PadLeft("User", 10, "0")`  
Renvoie « 000000User ».

----------
### <a name="padright"></a>PadRight

**Description :**  
La PadRight fonction droite-remplit une chaîne à une longueur spécifiée à l’aide d’un caractère de remplissage fourni.

**Syntaxe :**  
`str PadRight(str string, num length, str padCharacter)`

- chaîne : la chaîne de caractères de remplacement.
- longueur : un nombre entier représentant la longueur de chaîne souhaitée.
- padCharacter : une chaîne composée d’un caractère unique à utiliser comme caractère de remplissage

**Remarques :**

- Si la longueur de chaîne est inférieure à la longueur, puis padCharacter est à plusieurs reprises ajouté à la fin (à droite) de la chaîne jusqu'à ce qu’il a une longueur égale à.
- padCharacter peut être un caractère d’espace, mais elle ne peut pas être une valeur null.
- Si la longueur de chaîne est égale ou supérieure à la longueur, la chaîne est renvoyée ne change pas.
- Si la chaîne a une longueur supérieure ou égale à la longueur, une chaîne identique à la chaîne est renvoyée.
- Si la longueur de chaîne est inférieure à la longueur, une nouvelle chaîne de longueur souhaitée est renvoyée chaîne contenant rempli avec une padCharacter.
- Si la chaîne est null, la fonction retourne une chaîne vide.

**Exemple :**  
`PadRight("User", 10, "0")`  
Renvoie « User000000 ».

----------
### <a name="pcase"></a>PCase

**Description :**  
La fonction PCase convertit le premier caractère de chaque mot séparés par des espaces d’une chaîne en majuscules, et tous les autres caractères sont convertis en minuscules.

**Syntaxe :**  
`String PCase(string)`

**Remarques :**

- Cette fonction n’offre pas actuellement une casse appropriée pour convertir un mot entièrement en majuscule, par exemple un acronyme.

**Exemple :**  
`PCase("TEsT")`  
Renvoie « Test ».

`PCase(LCase("TEST"))`  
Renvoie « Test »

----------
### <a name="randomnum"></a>RandomNum

**Description :**  
La fonction RandomNum renvoie un nombre aléatoire compris entre un intervalle spécifié.

**Syntaxe :**  
`num RandomNum(num start, num end)`

- Démarrer : un numéro identifiant la limite inférieure de la valeur aléatoire pour générer
- fin : un numéro identifiant la limite supérieure de la valeur aléatoire pour générer

**Exemple :**  
`Random(100,999)`  
Peut retourner 734.

----------
### <a name="removeduplicates"></a>RemoveDuplicates

**Description :**  
La fonction RemoveDuplicates prend une chaîne à valeurs multiples et vérifiez que chaque valeur est unique.

**Syntaxe :**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Exemple :**  
`RemoveDuplicates([proxyAddresses])`  
Retourne un attribut proxyAddress rectifié où toutes les valeurs en double ont été supprimés.

----------
### <a name="replace"></a>Remplacer

**Description :**  
La fonction Replace remplace toutes les occurrences d’une chaîne à une autre chaîne.

**Syntaxe :**  
`str Replace(str string, str OldValue, str NewValue)`

- chaîne : remplacer les valeurs dans une chaîne.
- OldValue : La chaîne à rechercher et remplacer.
- Nouvelle_valeur : La chaîne à remplacer.

**Remarques :**  
La fonction ne reconnaît les noms spéciaux suivants :

- \n – nouvelle ligne
- \r – retour chariot
- \t – onglet

**Exemple :**  
`Replace([address],"\r\n",", ")`  
Remplace CRLF par une virgule et un espace et mener à « One Microsoft Way, Redmond, WA, États-Unis »

----------
### <a name="replacechars"></a>ReplaceChars

**Description :**  
La fonction ReplaceChars remplace toutes les occurrences des caractères figurant dans la chaîne ReplacePattern.

**Syntaxe :**  
`str ReplaceChars(str string, str ReplacePattern)`

- chaîne : remplacer des caractères dans une chaîne.
- ReplacePattern : une chaîne qui contient un dictionnaire de caractères à remplacer.

Le format est {source1} : {target1}, {source2} : {target2}, {sourceN}, {targetN} où source est le caractère situé à rechercher et cibler la chaîne à remplacer par.

**Remarques :**

- La fonction prend chaque occurrence de sources définis et les remplace par les cibles.
- La source doit être le seul caractère (unicode).
- La source ne peut pas être vide ou supérieure au moins deux caractères (erreur d’analyse).
- La cible peut contenir plusieurs caractères, par exemple ö:oe, β:ss.
- La cible ne peut être vide indiquant que le caractère doit être supprimé.
- La source respecte la casse et doit être une correspondance exacte.
- (Virgule) et : (deux-points) sont des caractères réservés et ne peuvent pas être remplacés à l’aide de cette fonction.
- Espaces et autres caractères blancs dans la chaîne ReplacePattern sont ignorées.

**Exemple :**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Renvoie Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Renvoie « ONeil », la graduation est défini pour être supprimé.

----------
### <a name="right"></a>Oui

**Description :**  
La fonction DROITE renvoie un nombre spécifié de caractères à partir de la droite (fin) d’une chaîne.

**Syntaxe :**  
`str Right(str string, num NumChars)`

- chaîne : la chaîne à renvoyer les caractères à partir de
- NumChars : un numéro identifiant le nombre de caractères à retourner à partir de la fin (à droite) de chaîne

**Remarques :**  
NumChars est renvoyé à partir de la dernière position de la chaîne.

Une chaîne contenant les derniers caractères numChars dans chaîne :

- Si numChars = 0, renvoyer une chaîne vide.
- Si numChars < 0, renvoient la chaîne d’entrée.
- Si la chaîne est null, renvoyer une chaîne vide.

Si la chaîne contient moins de caractères que la NumChars spécifié dans nombre, une chaîne identique à la chaîne est renvoyée.

**Exemple :**  
`Right("John Doe", 3)`  
Renvoie « Dupont ».

----------
### <a name="rtrim"></a>RTrim

**Description :**  
La fonction RTrim supprime les espaces blancs à droite d’une chaîne.

**Syntaxe :**  
`str RTrim(str value)`

**Exemple :**  
`RTrim(" Test ")`  
Renvoie « Test ».

----------
### <a name="split"></a>Double affichage

**Description :**  
La fonction Split prend une chaîne séparée par un délimiteur et rend une chaîne à valeurs multiples.

**Syntaxe :**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

- valeur : la chaîne avec un séparateur pour séparer.
- le délimiteur : seul caractère à utiliser comme séparateur.
- limite : nombre maximal de valeurs qui peut retourner.

**Exemple :**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Renvoie une chaîne à valeurs multiples avec 2 tous les éléments utiles pour l’attribut proxyAddress.

----------
### <a name="stringfromguid"></a>StringFromGuid

**Description :**  
La fonction StringFromGuid accepte un GUID binaire et convertit une chaîne

**Syntaxe :**  
`str StringFromGuid(bin GUID)`

----------
### <a name="stringfromsid"></a>StringFromSid

**Description :**  
La fonction StringFromSid convertit un tableau d’octets contenant un identificateur de sécurité à une chaîne.

**Syntaxe :**  
`str StringFromSid(bin ObjectSID)`  

----------
### <a name="switch"></a>Commutateur

**Description :**  
La fonction Switch est utilisée pour renvoyer une valeur unique en fonction de conditions évaluées.

**Syntaxe :**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

- expr : expression de type variante que vous voulez évaluer.
- valeur : valeur à renvoyer si l’expression correspondante a la valeur vrai.

**Remarques :**  
La liste d’arguments de fonction Switch se compose de paires de valeurs et des expressions. Les expressions sont évaluées de gauche à droite et la valeur associée à la première expression qui donne True est renvoyée. Si les composants ne sont pas associés correctement, une erreur d’exécution se produit.

Par exemple, si expr1 est vrai, Switch retourne valeur1. Si expr-1 est faux, mais expr-2 a la valeur True, Switch renvoie la valeur-2 et ainsi de suite.

Commutateur renvoie un rien si :

- Aucune des expressions sont vraies.
- La première expression True possède une valeur correspondante a la valeur Null.

Fonction Switch évalue toutes les expressions, même si elle renvoie uniquement un d’eux. Pour cette raison, il est conseillé d’effets indésirables. Par exemple, si l’évaluation d’une expression entraîne une division par zéro, une erreur se produit.

Valeur peut également être la fonction d’erreur, qui retourne une chaîne personnalisée.

**Exemple :**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Retourne le langage parlé dans certaines villes principales, dans le cas contraire retourne une erreur.

----------
### <a name="trim"></a>Découper

**Description :**  
La fonction SUPPRESPACE supprime les espaces de tête et blanc à partir d’une chaîne.

**Syntaxe :**  
`str Trim(str value)`  

**Exemple :**  
`Trim(" Test ")`  
Renvoie « Test ».

`Trim([proxyAddresses])`  
Supprime les espaces de tête et pour chaque valeur de l’attribut proxyAddress.

----------
### <a name="ucase"></a>UCase

**Description :**  
La fonction UCase convertit tous les caractères d’une chaîne en majuscules.

**Syntaxe :**  
`str UCase(str string)`

**Exemple :**  
`UCase("TeSt")`  
Renvoie « TEST ».

----------
### <a name="word"></a>Word

**Description :**  
La fonction Word renvoie un mot contenu d’une chaîne de caractères, en fonction des paramètres qui décrivent les délimiteurs à utiliser et le nombre de word pour renvoyer.

**Syntaxe :**  
`str Word(str string, num WordNumber, str delimiters)`

- chaîne : la chaîne à retourner un mot à partir de.
- WordNumber : un numéro identifiant le numéro de word doit renvoyer.
- délimiteurs : une chaîne qui représente le delimiter(s) doit être utilisé pour identifier les mots

**Remarques :**  
Chaque chaîne de caractères dans la chaîne séparés par l’un des caractères de délimiteurs sont identifiés en tant que mots :

- Si nombre < 1, renvoie une chaîne vide.
- Si la chaîne est null, renvoie une chaîne vide.

Si la chaîne contient moins le nombre de mots ou chaîne ne contient pas tous les mots identifiées par des délimiteurs, une chaîne vide est renvoyée.

**Exemple :**  
`Word("The quick brown fox",3," ")`  
Renvoie « brun »

`Word("This,string!has&many separators",3,",!&#")`  
Retournerait « a »

## <a name="additional-resources"></a>Ressources supplémentaires

* [Présentation des Expressions de mise en service déclaratives](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
* [Synchronisation Azure Active Directory se connecter : Options de personnalisation de la synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
