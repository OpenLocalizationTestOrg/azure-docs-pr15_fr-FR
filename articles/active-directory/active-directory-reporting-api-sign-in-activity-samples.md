<properties
    pageTitle="Exemples de rapport API Azure Active Directory connexion activité | Microsoft Azure"
    description="Comment se familiariser avec l’API de création de rapports de répertoire Azure Active"
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

# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Exemples de rapport API Azure Active Directory activité de connexion

Cette rubrique fait partie d’un ensemble de rubriques Azure Active Directory API de création de rapports.  
Création de rapports Azure AD vous offre une API qui vous permet d’accéder aux données activité de se connecter à l’aide de code ou les outils associés.  
Le cadre de cette rubrique consiste à vous fournir des exemples de code de l' **activité de connexion à l’API**.

Voir :

- [Journaux d’audit](active-directory-reporting-azure-portal.md#audit-logs) pour plus d’informations conceptuelles
- Pour plus d’informations sur la création de rapports API de [mise en route avec l’API de création de rapports de répertoire Azure Active](active-directory-reporting-api-getting-started.md) .

Vous avez des questions, problèmes ou des commentaires, veuillez contacter [AAD Reporting aide](mailto:aadreportinghelp@microsoft.com).


## <a name="prerequisites"></a>Conditions préalables
Avant de pouvoir utiliser les exemples dans cette rubrique, vous devez suivre les [conditions préalables pour accéder à l’annonce Azure API de création de rapports](active-directory-reporting-api-prerequisites.md).  


##<a name="powershell-script"></a>Script PowerShell

    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID       = "<clientId>"             # Should be a ~35 character string insert your info here
    $ClientSecret   = "<clientSecret>"         # Should be a ~44 character string insert your info here
    $loginURL       = "https://login.windows.net/"
    $tenantdomain   = "<tenantDomain>"
    $ daterange            # For example, contoso.onmicrosoft.com

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}

    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    $url = "https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&`$filter=signinDateTime ge $7daysago"
    
    $i=0
    
    Do{
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        Write-Output "Save the output to a file SigninActivities$i.json"
        Write-Output "---------------------------------------------"
        $myReport.Content | Out-File -FilePath SigninActivities$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)

    } else {
    
        Write-Host "ERROR: No Access Token"
    }




## <a name="executing-the-script"></a>L’exécution du script
Une fois que vous avez terminé le script d’édition, exécutez-le et vérifier que les données à partir de l’Audit attendues connecte rapport est renvoyée.

Le script renvoie la sortie de l’état de connexion au format JSON. Il crée également un `SigninActivities.json` fichier avec le même résultat. Vous pouvez essayer en modifiant le script pour renvoyer les données à partir d’autres rapports et commentez les formats de sortie que vous n’avez pas besoin.



## <a name="next-steps"></a>Étapes suivantes

- Vous voulez personnaliser les exemples dans cette rubrique ? Consultez [référence de l’API Azure Active Directory connexion activité](active-directory-reporting-api-sign-in-activity-reference.md). 

- Si vous voulez voir une présentation complète de l’utilisation de Azure Active Directory API de création de rapports, voir [prise en main Azure Active Directory API de signalement](active-directory-reporting-api-getting-started.md).

- Si vous voulez en savoir plus sur les rapports d’Azure Active Directory, consultez le [Guide Azure Active Directory création de rapports](active-directory-reporting-guide.md).  