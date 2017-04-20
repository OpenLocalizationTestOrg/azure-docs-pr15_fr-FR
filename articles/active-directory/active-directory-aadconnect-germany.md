<properties
    pageTitle="Azure AD Connect en Allemagne Microsoft Cloud"
    description="Azure AD Connect s’intègrent le Azure Active Directory de vos répertoires locaux. Ainsi, vous permet de fournir une identité courantes pour les applications Office 365 et Azure SaaS intégrées à Azure AD."
    keywords="Introduction à Azure AD Connect, vue d’ensemble Azure AD Connect, ce qui est Azure AD Connect, installation d’active directory, Allemagne, noir forêt"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/08/2016"
    ms.author="billmath"/>

#<a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Azure AD Connect dans Microsoft Cloud Allemagne - version d’évaluation

## <a name="introduction"></a>Introduction
Azure AD Connect assure la synchronisation entre votre Active Directory local et Azure Active Directory.
Pour l’instant, la plupart des scénarios dans [Microsoft Cloud Allemagne](https://www.microsoft.com/de-de/cloud/deutschland/default.aspx) doivent être effectuées par l’opérateur. Lorsque vous utilisez Microsoft Cloud Allemagne, vous devez tenir compte des éléments suivants :


- Les URL suivantes doivent être ouvert sur un serveur proxy pour la synchronisation se déroule correctement :
    - *. microsoftonline.de
    - *. Windows.NET
    - + Listes de révocation de certificats

- Lorsque vous êtes connecté à votre répertoire Azure AD, vous devez utiliser un compte dans le domaine onmicrosoft.de.
- Les fonctionnalités suivantes ne sont pas disponibles :
    - Azure AD Connect d’intégrité
    - Mises à jour automatiques
    - Mot de passe en écriture différée

## <a name="download"></a>Télécharger
Vous pouvez télécharger Azure AD Connect à partir de la carte Azure AD Connect au sein du portail.  Utilisez les instructions ci-dessous pour localiser la carte Azure AD Connect.

### <a name="the-azure-ad-connect-blade"></a>La Azure AD Connect carte

Une fois que vous avez connecté au portail Azure, procédez comme suit :

1. Accédez à parcourir
2.  Sélectionnez Azure Active Directory
3.  Puis sélectionnez Azure AD Connect

Vous devez voir les rubriques suivantes :

![Azure AD Connect carte](media\active-directory-aadconnect-germany\germany1.png)

 
Le tableau suivant décrit les fonctionnalités de la carte.


Titre|Description|
----- | ----- |
ÉTAT DE SYNCHRONISATION|Nous allons vous savez alors que la synchronisation est activée ou désactivée.|
DERNIÈRE SYNCHRONISATION|La dernière fois une synchronisation réussie terminée.|
DOMAINES FÉDÉRÉS|Affiche le nombre de domaines fédérés déjà configurées.|


## <a name="installation"></a>Installation
Pour installer Azure AD Connect, vous pouvez utiliser la documentation [ici](active-directory-aadconnect.md#install-azure-ad-connect).

## <a name="advanced-features-and-additional-information"></a>Fonctionnalités avancées et des informations supplémentaires
Pour plus d’informations et des recommandations sur les paramètres personnalisés ou des configurations avancées, commencez avec [intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).  Cette page fournit des informations et des liens vers des conseils supplémentaires.
