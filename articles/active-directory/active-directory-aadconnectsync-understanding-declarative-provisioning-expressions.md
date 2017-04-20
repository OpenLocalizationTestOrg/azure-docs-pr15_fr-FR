<properties
    pageTitle="Azure AD Connect synchronisation : présentation des Expressions de mise en service déclarative | Microsoft Azure"
    description="Explique les expressions de mise en service déclaratives."
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
    ms.date="08/31/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Azure AD Connect synchronisation : présentation des Expressions de mise en service déclarative
Azure AD Connect synchronisation s’appuie sur une mise en service déclarative introduite dans Forefront Identity Manager 2010. Il vous permet d’implémenter votre logique sans avoir à écrire du code compilé intégration identité complète.

Une partie essentielle de mise en service déclarative est le langage d’expression utilisé dans les flux d’attributs. La langue utilisée est un sous-ensemble de Microsoft® Visual Basic pour Applications (VBA). Cette langue est utilisée dans Microsoft Office et les utilisateurs ayant une expérience de VBScript reconnaît également. Le langage d’Expression déclarative mise en service est uniquement à l’aide de fonctions et n’est pas un langage structuré. Il n’existe aucune méthode ou les instructions. Fonctions sont imbriquées à la place sur le flux de programme express.

Pour plus d’informations, voir [Bienvenue dans Visual Basic pour référence du langage VBA pour Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Les attributs doivent être tapées fortement. Une fonction accepte uniquement les attributs du type correct. Il est également la casse. Les noms de fonctions et de noms de l’attribut doivent avoir une casse appropriée ou une erreur est générée.

## <a name="language-definitions-and-identifiers"></a>Identificateurs et les définitions de langue

- Fonctions contiennent un nom suivi d’arguments entre crochets : suivantes (l’argument 1, argument N).
- Les attributs sont identifiés par des crochets : [attributeName]
- Paramètres sont identifiés par des pourcentages : ParameterName %
- Les constantes de chaîne sont entourés de guillemets : par exemple, « Contoso » (Note : doit utiliser des guillemets « » et pas guillemets « »)
- Valeurs numériques sont exprimés sans guillemets et censées être décimale. Valeurs hexadécimales sont précédés & H. Par exemple, 98052 & HFF
- Valeurs booléennes sont exprimés avec des constantes : vrai, faux.
- Constantes intégrées et littéraux sont exprimés avec uniquement son nom : NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Fonctions
Mise en service déclarative utilise de nombreuses fonctions pour activer la possibilité de transformer des valeurs d’attribut. Ces fonctions peuvent être imbriquées afin que le résultat d’une fonction est passé à une autre fonction.

`Function1(Function2(Function3()))`

Vous trouverez la liste complète des fonctions dans la [référence des fonctions](active-directory-aadconnectsync-functions-reference.md).

### <a name="parameters"></a>Paramètres
Un paramètre est défini par un connecteur ou par un administrateur à l’aide de PowerShell. Paramètres généralement contiennent des valeurs qui sont différentes système, par exemple le nom du domaine l’utilisateur se trouve dans. Ces paramètres peuvent être utilisés dans les flux d’attributs.

Le connecteur Active Directory fourni les paramètres suivants pour les règles de synchronisation entrant :

| Nom du paramètre | Commentaire |
| --- | --- |
| Domain.Netbios | Format NetBIOS du domaine en cours d’importation, par exemple FABRIKAMSALES |
| Domain.FQDN | Format de nom de domaine complet du domaine en cours d’importation, par exemple ventes.fabrikam.com |
| Domain.LDAP | Format LDAP du domaine en cours d’importation, par exemple DC = ventes, DC = fabrikam, DC = com |
| Forest.Netbios | Format NetBIOS du nom de la forêt en cours d’importation, par exemple FABRIKAMCORP |
| Forest.FQDN | Format de nom de domaine complet du nom forêt en cours d’importation, par exemple fabrikam.com |
| Forest.LDAP | Format LDAP du nom de la forêt en cours d’importation, par exemple DC = fabrikam, DC = com |

Le système fournit le paramètre suivant, qui est utilisé pour obtenir l’identificateur du connecteur en cours d’exécution :  
`Connector.ID`

Voici un exemple qui remplit le domaine attribut métaverse par le nom netbios du domaine où se trouve l’utilisateur :  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Opérateurs
Les opérateurs suivants peuvent être utilisés :

- **Comparaison**: <, < =, <>, =, >, > =
- **Mathématiques**: +, -, \*, -
- **Chaîne**: & (concaténation)
- **Logique**: & & (et) || (ou)
- **Ordre d’évaluation**:)

Les opérateurs sont évalués de gauche à droite et ont la même priorité d’évaluation. En d’autres termes, la \* (multiplicateur) n’est pas évaluée avant - (soustraction). 2\*(5 + 3) n’est pas le même que 2\*5 + 3. Le (entre crochets) sont utilisées pour modifier l’ordre d’évaluation lors de la gauche pour l’ordre d’évaluation approprié n’est pas le cas.

## <a name="multi-valued-attributes"></a>Attributs à valeurs multiples
Les fonctions peuvent fonctionner sur les attributs à valeur unique et à valeurs multiples. Pour les attributs à valeurs multiples, la fonction fonctionne sur chaque valeur et applique la même fonction à chaque valeur.

Par exemple :  
`Trim([proxyAddresses])`Effectuez un ajustement de chaque valeur dans l’attribut proxyAddress.  
`Word([proxyAddresses],1,"@") & "@contoso.com"`Pour chaque valeur avec un @-sign, remplacer le domaine par @contoso.com.  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])`Recherchez l’adresse SIP et supprimer des valeurs.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le modèle de configuration dans la [Présentation de la mise en service déclarative](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
- Voir comment déclarative mise en service est utilisé de prédéfinies à [comprendre la configuration par défaut](active-directory-aadconnectsync-understanding-default-configuration.md).
- Découvrez comment effectuer un changement de pratique à l’aide de la mise en service déclarative comment [effectuer un changement de la configuration par défaut](active-directory-aadconnectsync-change-the-configuration.md).

**Rubriques de présentation**

- [Azure AD Connect synchronisation : comprendre et personnaliser la synchronisation](active-directory-aadconnectsync-whatis.md)
- [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

**Rubriques de référence**

- [Azure AD Connect synchronisation : référence des fonctions](active-directory-aadconnectsync-functions-reference.md)
