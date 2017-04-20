<properties
   pageTitle="Azure AD Connect synchronisation : extensions Directory | Microsoft Azure"
   description="Cette rubrique décrit la fonctionnalité d’extensions répertoire dans Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/19/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect synchronisation : extensions Directory
Extensions Directory vous permet d’étendre le schéma dans Azure AD avec vos propres attributs d’Active Directory local. Cette fonctionnalité permet de créer des applications métier par d’autres programmes attributs que vous continuer à gérer en local. Les attributs peuvent être consommées par le biais [extensions directory Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) ou [Microsoft Graph](https://graph.microsoft.io/). Vous pouvez voir les attributs disponibles à l’aide de [l’Explorateur Azure AD Graph](https://graphexplorer.cloudapp.net) et [explorer Microsoft Graph](https://graphexplorer2.azurewebsites.net/) respectivement.

À l’heure actuelle aucune charge de travail Office 365 n’utilise les attributs.

Vous configurez les autres attributs que vous souhaitez synchroniser dans le chemin d’accès des paramètres personnalisés dans l’Assistant installation.
![Assistant d’Extension schéma](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png) l’installation affiche les attributs suivants, qui sont des candidats valides :

- Types d’objets utilisateur et de groupe
- Attributs à valeur unique : chaîne, booléen, entier, en nombre binaire.
- Attributs à valeurs multiples : chaîne, binaire

La liste des attributs est en lecture à partir du cache créé pendant l’installation de Azure AD Connect. Si vous avez étendu le schéma Active Directory avec des attributs supplémentaires, [schéma doit être actualisé](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema) avant ces nouveaux attributs sont visibles.

Jusqu'à 100 attributs d’extensions annuaire peut avoir un objet. La longueur maximale est de 250 caractères. Si une valeur de l’attribut est plus longue, il est tronqué par le moteur de synchronisation.

Pendant l’installation de Azure AD Connect, l’application est enregistrée à l’endroit où ces attributs sont disponibles. Vous pouvez voir cette application dans le portail Azure.  
![Application d’Extension de schéma](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3.png)

Les attributs sont désormais disponibles via Graph :  
![Graphique](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Les attributs sont précédés d’extension\_{AppClientId}\_. Le AppClientId a la même valeur pour tous les attributs dans votre annuaire Azure AD.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la configuration [Azure AD Connect synchroniser](active-directory-aadconnectsync-whatis.md) .

En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
