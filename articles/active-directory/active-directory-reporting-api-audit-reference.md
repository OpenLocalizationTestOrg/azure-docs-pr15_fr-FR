<properties
    pageTitle="Audit Active Directory Azure référence de l’API | Microsoft Azure"
    description="Comment se familiariser avec l’API d’audit Azure Active Directory"
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
    ms.date="10/24/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="azure-active-directory-audit-api-reference"></a>Audit Active Directory Azure référence de l’API

Cette rubrique fait partie d’un ensemble de rubriques Azure Active Directory API de création de rapports.  
Création de rapports Azure AD vous offre une API qui vous permet d’accéder aux données d’audit à l’aide de code ou les outils associés.
Le cadre de cette rubrique consiste à vous fournir des informations de référence sur **Auditer API**.

Voir :

- [Journaux d’audit](active-directory-reporting-azure-portal.md#audit-logs) pour plus d’informations conceptuelles
- Pour plus d’informations sur la création de rapports API de [mise en route avec l’API de création de rapports de répertoire Azure Active](active-directory-reporting-api-getting-started.md) .

Vous avez des questions, problèmes ou des commentaires, veuillez contacter [AAD Reporting aide](mailto:aadreportinghelp@microsoft.com).


## <a name="who-can-access-the-data"></a>Qui peut accéder aux données ?

- Utilisateurs du rôle d’administrateur de sécurité ou un lecteur de sécurité

- Administrateurs généraux

- Une application qui est autorisé à accéder à l’API (application l’autorisation peut être le programme d’installation uniquement en fonction d’autorisations d’administrateur général)



## <a name="prerequisites"></a>Conditions préalables

Pour accéder à ce rapport via l’API de création de rapports, vous devez avoir :

- Un [Azure Active Directory gratuite ou une meilleure edition](active-directory-editions.md)

- Terminer la [Configuration requise pour accéder à l’annonce Azure API de création de rapports](active-directory-reporting-api-prerequisites.md). 
 

##<a name="accessing-the-api"></a>Accès à l’API

Vous pouvez soit accéder à cette API via l' [Explorateur de graphique](https://graphexplorer2.cloudapp.net) ou par programme à l’aide, par exemple, PowerShell. Dans l’ordre de PowerShell pour interpréter correctement la syntaxe de filtre OData utilisée dans les appels AAD Graph reste, vous devez utiliser le backtick (aka : accent grave) caractère à « caractère d’échappement « la $. Le caractère backtick utilisée comme [caractère d’échappement de PowerShell](https://technet.microsoft.com/library/hh847755.aspx), ce qui permet de PowerShell pour effectuer une littérale interprétation du caractère $ et éviter la confusion entre comme un nom de la variable PowerShell (Internet Explorer : $filter).

L’objectif de cette rubrique est dans l’Explorateur de graphique. Pour obtenir un exemple PowerShell, consultez ce [script PowerShell](active-directory-reporting-api-audit-samples.md#powershell-script).

 
 

## <a name="api-endpoint"></a>Point de terminaison de l’API


Vous pouvez accéder à cette API à l’aide de l’URI suivant :  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta

Il n’existe aucune limite sur le nombre d’enregistrements renvoyés par l’API d’audit Azure AD (à l’aide de la pagination OData).
Limites de rétention des données de création de rapports, consultez [Création de rapports de stratégies de rétention](active-directory-reporting-retention.md).

Cet appel renvoie les données par lots. Chaque lot comporte un maximum de 1000 enregistrements.  
Pour obtenir le lot suivant d’enregistrements, cliquez sur le lien suivant. Obtenir les informations skiptoken provenant du premier jeu d’enregistrements renvoyés. Le jeton sauter sera à la fin du résultat défini.  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## <a name="supported-filters"></a>Filtres pris en charge

Vous pouvez réduire le nombre d’enregistrements renvoyés par une API appeler sous forme d’un filtre.  
Pour se connecter API liées des données, les filtres suivants sont pris en charge :

- **$top =\<nombre d’enregistrements à renvoyer\> ** - pour limiter le nombre d’enregistrements renvoyés. Il s’agit d’une opération coûteuse. Vous ne devez pas utiliser ce filtre si vous voulez renvoyer des milliers d’objets.     
- **$filter =\<votre relevé de filtre\> ** - permettant de spécifier, sur la base des champs de filtre pris en charge, les types d’enregistrements qui vous intéressent



## <a name="supported-filter-fields-and-operators"></a>Champs de filtre pris en charge et des opérateurs

Pour spécifier le type d’enregistrements que qui vous intéressent, vous pouvez créer une instruction de filtrage qui peut contenir une ou plusieurs des champs de filtre suivants :

- [activityDate](#activitydate) - définit une date ou une plage de dates
- [activityType](#activitytype) - définit le type d’une activité
- [activité](#activity) - définit l’activité sous forme de chaîne  
- [nom de l’acteur /](#actorname) - définit l’acteur sous forme de nom de l’acteur
- [acteur/objectid](#actorobjectid) - définit l’acteur sous forme d’ID de l’acteur   
- [acteur/upn](#actorupn) - définit l’acteur sous forme principe de l’acteur nom d’utilisateur (UPN) 
- [cible/nom](#targetname) - définit la cible sous forme de nom de l’acteur
- [cible/objectid](#targetobjectid) - définit la cible sous forme de l’ID de cible  
- [cible/upn](#targetupn) - définit l’acteur sous forme principe de l’acteur nom d’utilisateur (UPN)   




----------

### <a name="activitydate"></a>activityDate

**Opérateurs pris en charge**: eq, page, le, gt, lt

**Exemple**:

    $filter=tdomain + 'activities/audit?api-version=beta&`$filter=eventTime gt ' + $7daysago    

**Notes**:

date/heure doit être au format UTC

----------

### <a name="activitytype"></a>activityType

**Opérateurs pris en charge**: eq

**Exemple**:

    $filter=activityType eq 'User'  

**Notes**:

la casse

----------

### <a name="activity"></a>activité

**Opérateurs pris en charge**: eq, contient, startsWith

**Exemple**:

    $filter=activity eq 'Add application' or contains(activity, 'Application') or startsWith(activity, 'Add')   

**Notes**:

la casse

----------

### <a name="actorname"></a>nom de l’acteur /

**Opérateurs pris en charge**: eq, contient, startsWith

**Exemple**:

    $filter=actor/name eq 'test' or contains(actor/name, 'test') or startswith(actor/name, 'test')  

**Notes**:

sans respecter la casse

    

----------
### <a name="actorobjectid"></a>acteur/objectId

**Opérateurs pris en charge**: eq

**Exemple**:

    $filter=actor/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'    

----------
### <a name="targetname"></a>nom/cible

**Opérateurs pris en charge**: eq, contient, startsWith

**Exemple**:

    $filter=targets/any(t: t/name eq 'some name')   

**Notes**:

Sans respecter la casse

----------

### <a name="targetupn"></a>cible/upn

**Opérateurs pris en charge**: eq, startsWith

**Exemple**:

    $filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc')) 

**Notes**:

- Sans respecter la casse
- Vous devez ajouter l’espace de noms complet lors de l’interrogation Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity

----------

### <a name="targetobjectid"></a>cible/objectId

**Opérateurs pris en charge**: eq

**Exemple**:

    $filter=targets/any(t: t/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba')    

----------

### <a name="actorupn"></a>acteur/upn

**Opérateurs pris en charge**: eq, startsWith

**Exemple**:

    $filter=startswith(actor/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity/userPrincipalName,'abc')  

**Notes**:

- Sans respecter la casse 
- Vous devez ajouter l’espace de noms complet lors de l’interrogation Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity

----------




## <a name="next-steps"></a>Étapes suivantes

- Vous voulez voir des exemples pour des activités système filtré ? Consultez [Azure Active Directory audit des exemples de l’API](active-directory-reporting-api-audit-samples.md).

- Vous voulez en savoir plus sur l’annonce Azure API de signalement ? Voir [prise en main avec l’API de création de rapports de répertoire Azure Active](active-directory-reporting-api-getting-started.md).