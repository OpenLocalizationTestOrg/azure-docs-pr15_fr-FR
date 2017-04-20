<properties
    pageTitle="Rapport d’activité de connexion Azure Active Directory référence de l’API | Microsoft Azure"
    description="Référence de l’API de rapport d’activité connexion Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="azure-active-directory-sign-in-activity-report-api-reference"></a>Rapport d’activité de connexion Azure Active Directory référence de l’API


Cette rubrique fait partie d’un ensemble de rubriques Azure Active Directory API de création de rapports.  
Création de rapports Azure AD vous offre une API qui vous permet d’accéder aux données de rapport activité de se connecter à l’aide de code ou les outils associés.
Le cadre de cette rubrique consiste à vous fournir des informations de référence sur la **connexion à l’API rapport activité**.

Voir :

- [Connexion à des activités](active-directory-reporting-azure-portal.md#sign-in-activities) pour plus d’informations conceptuelles
- Pour plus d’informations sur la création de rapports API de [mise en route avec l’API de création de rapports de répertoire Azure Active](active-directory-reporting-api-getting-started.md) .

Vous avez des questions, problèmes ou des commentaires, veuillez contacter [AAD Reporting aide](mailto:aadreportinghelp@microsoft.com).



## <a name="who-can-access-the-api-data"></a>Qui peut accéder aux données API ?

- Utilisateurs du rôle d’administrateur de sécurité ou un lecteur de sécurité

- Administrateurs généraux

- Une application qui est autorisé à accéder à l’API (application l’autorisation peut être le programme d’installation uniquement en fonction d’autorisations d’administrateur général)



## <a name="prerequisites"></a>Conditions préalables

Pour accéder à ce rapport via l’API de création de rapports, vous devez :

- Un [Azure Active Directory Premium P1 ou P2 edition](active-directory-editions.md)

- Terminer la [Configuration requise pour accéder à l’annonce Azure API de création de rapports](active-directory-reporting-api-prerequisites.md). 


##<a name="accessing-the-api"></a>Accès à l’API

Vous pouvez soit accéder à cette API via l' [Explorateur de graphique](https://graphexplorer2.cloudapp.net) ou par programme à l’aide, par exemple, PowerShell. Dans l’ordre de PowerShell pour interpréter correctement la syntaxe de filtre OData utilisée dans les appels AAD Graph reste, vous devez utiliser le backtick (aka : accent grave) caractère à « caractère d’échappement « la $. Le caractère backtick utilisée comme [caractère d’échappement de PowerShell](https://technet.microsoft.com/library/hh847755.aspx), ce qui permet de PowerShell pour effectuer une littérale interprétation du caractère $ et éviter la confusion entre comme un nom de la variable PowerShell (ie : $filter).

L’objectif de cette rubrique est dans l’Explorateur de graphique. Pour obtenir un exemple PowerShell, consultez ce [script PowerShell](active-directory-reporting-api-sign-in-activity-samples.md#powershell-script).


## <a name="api-endpoint"></a>Point de terminaison de l’API

Vous pouvez accéder à cette API à l’aide de l’URI de base suivant :  
    
    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta  



En raison du volume de données, cette API est limité à un million enregistrements retournés. 

Cet appel renvoie les données par lots. Chaque lot comporte un maximum de 1000 enregistrements.  
Pour obtenir le lot suivant d’enregistrements, cliquez sur le lien suivant. Obtenir les informations [skiptoken](https://msdn.microsoft.com/library/dd942121.aspx) provenant du premier jeu d’enregistrements renvoyés. Le jeton sauter sera à la fin du résultat défini.  

    https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&%24skiptoken=-1339686058


## <a name="supported-filters"></a>Filtres pris en charge

Vous pouvez réduire le nombre d’enregistrements renvoyés par une API appeler sous forme d’un filtre.  
Pour se connecter API liées des données, les filtres suivants sont pris en charge :

- **$top =\<nombre d’enregistrements à renvoyer\> ** - pour limiter le nombre d’enregistrements renvoyés. Il s’agit d’une opération coûteuse. Vous ne devez pas utiliser ce filtre si vous voulez renvoyer des milliers d’objets.  
- **$filter =\<votre relevé de filtre\> ** - permettant de spécifier, sur la base des champs de filtre pris en charge, les types d’enregistrements qui vous intéressent



## <a name="supported-filter-fields-and-operators"></a>Champs de filtre pris en charge et des opérateurs

Pour spécifier le type d’enregistrements que qui vous intéressent, vous pouvez créer une instruction de filtrage qui peut contenir une ou plusieurs des champs de filtre suivants :

- [signinDateTime](#signindatetime) - définit une date ou une plage de dates

- [ID d’utilisateur](#userid) - définit un spécifique utilisateur en fonction. de l’utilisateur

- [userPrincipalName](#userprincipalname) - définit spécifiques à un utilisateur en fonction principal nom_utilisateur de l’utilisateur (UPN)

- [identificateur](#appid) - définit spécifiques à une application en fonction des ID de l’application

- [appDisplayName](#appdisplayname) - définit un spécifiques au nom d’affichage de l’application en fonction de l’application

- [loginStatus](#loginStatus) - définit le statut des noms d’accès (réussite / échec)


> [AZURE.NOTE] Lorsque vous utilisez l’Explorateur de graphique, vous la nécessité d’utiliser la casse appropriée pour chaque lettre est vos champs de filtre.


Pour limiter l’étendue des données retournées, vous pouvez créer des combinaisons des filtres pris en charge et des champs de filtre. Par exemple, l’instruction suivante renvoie les 10 premiers enregistrements entre 2016 1er juillet et juillet 2016 6 :

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta&$top=10&$filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T00:00:00Z


----------

### <a name="signindatetime"></a>signinDateTime

**Opérateurs pris en charge**: eq, page, le, gt, lt

**Exemple**:

À l’aide d’une date spécifique

    $filter=signinDateTime+eq+2016-04-25T23:59:00Z  



À l’aide d’une plage de dates    

    $filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T17:05:21Z


**Notes**:

Le paramètre de date/heure doit être au format UTC 


----------

### <a name="userid"></a>ID d’utilisateur

**Opérateurs pris en charge**: eq

**Exemple**:

    $filter=userId+eq+’00000000-0000-0000-0000-000000000000’

**Notes**:

La valeur de nom d’utilisateur est une valeur de chaîne



----------

### <a name="userprincipalname"></a>userPrincipalName

**Opérateurs pris en charge**: eq

**Exemple**:

    $filter=userPrincipalName+eq+'audrey.oliver@wingtiptoysonline.com' 


**Notes**:

La valeur d’userPrincipalName est une valeur de chaîne

----------

### <a name="appid"></a>Identificateur

**Opérateurs pris en charge**: eq

**Exemple**:

    $filter=appId+eq+’00000000-0000-0000-0000-000000000000’



**Notes**:

La valeur d’identificateur est une valeur de chaîne

----------


### <a name="appdisplayname"></a>appDisplayName

**Opérateurs pris en charge**: eq

**Exemple**:

    $filter=appDisplayName+eq+'Azure+Portal' 


**Notes**:

La valeur d’appDisplayName est une valeur de chaîne

----------

### <a name="loginstatus"></a>loginStatus

**Opérateurs pris en charge**: eq

**Exemple**:

    $filter=loginStatus+eq+'1'  


**Notes**:

Il existe deux possibilités pour la loginStatus : 0 - opération réussie, 1 - Échec

----------



## <a name="next-steps"></a>Étapes suivantes

- Vous voulez voir des exemples d’activités de connexion filtrées ? Consultez les [exemples de rapports API de connexion activité Azure Active Directory](active-directory-reporting-api-sign-in-activity-samples.md).

- Vous voulez en savoir plus sur l’annonce Azure API de signalement ? Voir [prise en main avec l’API de création de rapports de répertoire Azure Active](active-directory-reporting-api-getting-started.md).