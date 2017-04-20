<properties
   pageTitle="Gestion des unités d’administration dans Azure Active Directory"
   description="À l’aide d’unités administratives pour la délégation plus précise des autorisations dans Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="administrative-units-management-in-azure-ad---public-preview"></a>Gestion des unités d’administration dans Azure AD - version d’évaluation

Cet article décrit les unités d’administration – un nouveau conteneur Azure Active Directory des ressources peut être utilisée pour la délégation des autorisations administratives sur sous-ensembles d’utilisateurs et d’application des stratégies pour un sous-ensemble d’utilisateurs. Dans Azure Active Directory, unités administratives permettent aux administrateurs central pour déléguer des autorisations aux administrateurs régionaux ou pour définir la stratégie à un niveau fin.

Ceci est utile dans les organisations disposant de divisions indépendantes, par exemple, une grande université est composée de nombreuses écoles autonomes (scolaire Professionnel, scolaire ingénierie, etc.) qui sont indépendants les uns des autres. Ces divisions ont leurs propres les administrateurs informatiques qui contrôlent l’accès, gérer les utilisateurs et définir des stratégies spécifiquement pour leur division. Administrateurs central que vous souhaitez pouvoir accorder ces divisions autorisations des administrateurs sur les utilisateurs de leurs divisions particuliers. Plus précisément, à l’aide de cet exemple, un administrateur central pouvez, par exemple, créer une unité d’administration pour un établissement particulier (Business school) et remplissez-la avec uniquement les utilisateurs de l’école professionnels. Un administrateur central peut ajouter l’établissement d’entreprise votre service informatique à un rôle dans l’étendue, en d’autres termes, accordez ensuite les informaticiens professionnels scolaire d’autorisations d’administration uniquement via le centre d’administration de scolaire profit.

> [AZURE.IMPORTANT]
> Vous pouvez créer et utiliser des unités administratives uniquement si vous activez Azure Active Directory Premium. Pour plus d’informations, voir [mise en route d’Azure AD Premium](active-directory-get-started-premium.md).

Du point de vue de l’administrateur central, une unité d’administration est un objet d’annuaire qui peut être créé et rempli avec des ressources. **Dans cette version, ces ressources peuvent être seuls les utilisateurs.** Une fois créé et rempli, l’unité d’administration peut servir l’étendue pour restreindre les autorisations accordées uniquement sur les ressources contenues dans l’unité d’administration.

## <a name="managing-administrative-units"></a>Gestion des unités d’administration

Dans cette version préliminaire, vous pouvez créer et gérer des unités d’administration à l’aide des applets de commande Module Azure Active Directory pour Windows PowerShell.

Pour plus d’informations sur la configuration logicielle requise et l’installation du module Azure Active Directory et pour plus d’informations sur les applets de commande Module Azure AD pour la gestion des unités d’administration, y compris syntaxe, descriptions des paramètres et exemples, voir [Gérer Azure AD à l’aide de Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).


## <a name="next-steps"></a>Étapes suivantes
[Azure éditions Active Directory](active-directory-editions.md)
