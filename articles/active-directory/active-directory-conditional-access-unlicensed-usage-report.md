<properties
    pageTitle="Rapport d’utilisation sans licence | Microsoft Azure"
    description="Le rapport permet de l’utilisation sans licence que vous identifiez les utilisateurs sans licence qui utilisent payé fonctionnalités Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="markvi"/>

# <a name="unlicensed-usage-report"></a>Rapport d’utilisation sans licence

Le rapport permet de l’utilisation sans licence que vous identifiez les utilisateurs sans licence qui utilisent payé fonctionnalités Azure AD. Ainsi, vous permet d’apporter une meilleure utilisez de licences que vous avez achetées et pour identifier que vous savez lorsque vous devrez peut-être des licences supplémentaires. 

Le rapport affiche l’utilisation active des fonctionnalités payées au cours des 30 derniers jours. 

## <a name="report-structure"></a>Structure de rapport
 
| Nom de colonne          |    Description |
| :--                  | :--         |
| Utilisateurs sans licence      |    Nom de l’utilisateur |
| Fonctionnalité              | Le nom de la fonctionnalité. Par exemple : accès conditionnel |
| Accédé à l’application | Le nom de l’application est accessible avec la fonctionnalité. Par exemple : Office 365 SharePoint Online |

 
> [AZURE.NOTE] Si un compte d’utilisateur a été supprimé la colonne « Utilisateur sans licence » est remplie avec un ID, comme 1003000090D8B285


## <a name="conditional-access-feature"></a>Fonctionnalité accès conditionnel

Utilisateurs sans licence sont sont signalés lorsqu’ils accèdent à un service qui comporte la stratégie d’accès conditionnelle appliquée s’ils ne disposent pas d’une licence Azure AD Premium. 

Cela s’applique à l’authentification Multifacteur / stratégies emplacement ainsi que dispositif de stratégies qui utilisent Intune.
 

## <a name="see-also"></a>Voir aussi

- [Utilisation d’Access conditionnelle avec Office 365 et autres Azure Active Directory connecté applications](active-directory-conditional-access.md)
- [Mise en route d’accès conditionnel à Azure Active Directory](active-directory-conditional-access-azuread-connected-apps.md) 


