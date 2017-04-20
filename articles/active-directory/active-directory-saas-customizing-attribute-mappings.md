<properties
    pageTitle="Personnalisation des mappages d’attribut | Microsoft Azure"
    description="Les mappages d’attributs pour les applications SaaS dans Azure Active Directory Découvrez comment vous pouvez modifier les pour répondre à vos besoins professionnels."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi"/>


# <a name="customizing-attribute-mappings"></a>Personnalisation des mappages d’attributs


Microsoft Azure AD prend en charge la configuration des utilisateurs aux applications de SaaS tiers tels que force de vente, Google Apps et d’autres personnes. Si vous avez mis en service de l’utilisateur pour une application SaaS activée tierce, le portail de gestion Azure contrôles ses valeurs d’attribut sous forme d’une configuration appelée « mappage des attributs ».

Il existe un jeu de mappages d’attributs entre les objets d’utilisateur Azure Active Directory et de chaque application SaaS utilisateur préconfiguré. Certaines applications gérer d’autres types d’objets, tels que des groupes ou des Contacts. <br> 
Vous pouvez personnaliser les mappages d’attributs par défaut en fonction des besoins de votre entreprise. Cela signifie, vous pouvez modifier ou supprimer les mappages attribut existants ou créer des mappages de l’attribut.

Dans le portail Azure AD, vous pouvez accéder à cette fonctionnalité en cliquant sur les attributs dans la barre d’outils d’une application SaaS.

> [AZURE.NOTE] Le lien **attributs** est uniquement disponible si vous avez activé pour une application SaaS approvisionnement de l’utilisateur. 


![Force de vente][1] 


Lorsque vous cliquez sur attributs dans la barre d’outils, la liste des mappages actuels sont configurés pour une application SaaS.

La capture d’écran suivante montre un exemple pour cela :



![Force de vente][2]  


Dans l’exemple ci-dessus, vous pouvez voir que l’attribut **prénom** d’un objet géré dans Salesforce est remplie avec la valeur **prénom** de le lié objet Azure AD.

Si vous voulez personnaliser vos mappages attribut ou personnalisé si vous voulez rétablir les paramètres revenir à la configuration par défaut, vous pouvez faire ceci en cliquant sur le bouton connexe dans la barre d’outils en bas d’une application.


![Force de vente][3]  


Il existe des mappages attribut requis par une application SaaS fonctionne correctement. Dans la table attributs, les mappages d’attributs connexes ont **Oui** comme valeur pour l’attribut **obligatoire** . Si un mappage d’attributs est requis, vous ne pouvez pas le supprimer. Dans ce cas, **supprimez** la fonctionnalité n’est pas disponible.

Pour modifier un mappage d’attribut existant, sélectionnez le mappage, puis cliquez sur **Modifier**. Cela affiche une page de dialogue qui permet de modifier le mappage des attributs sélectionné.


![Modifier le mappage d’attributs][4]  



## <a name="understanding-attribute-mapping-types"></a>Présentation des Types au mappage d’attributs


Des mappages d’attributs, vous pouvez contrôler comment les attributs sont remplis dans un troisième partie SaaS application. Il existe quatre types différents de mappage pris en charge :

- **Direct** – l’attribut cible est remplie avec la valeur d’un attribut de l’objet lié dans Azure Active Directory.


- **Constante** – l’attribut cible est remplie avec une chaîne spécifique que vous avez spécifié.


- **Expression** - l’attribut cible est remplie en fonction du résultat d’une expression de script similaire. Pour plus d’informations, voir [Écrire des Expressions pour les mappages attribut dans Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md).


- **Aucun** : l’attribut cible est inchangée gauche. Toutefois, si l’attribut cible n’est jamais vide, il sera remplie avec la valeur par défaut que vous spécifiez.



Outre ces quatre types de mappage de base des attributs, mappages d’attributs personnalisés prennent en charge le concept d’une affectation de valeur **par défaut** . L’affectation de valeur par défaut garantit qu’un attribut target est rempli avec une valeur s’il existe aucune valeur dans Azure AD ni sur l’objet cible.

Microsoft Azure AD fournit une implémentation très efficace d’un processus de synchronisation. Dans un environnement initialisé, seuls les objets nécessitant des mises à jour sont traités pendant un cycle de synchronisation. Mise à jour des mappages d’attributs a un impact sur les performances d’un cycle de synchronisation. Il s’agit, car une mise à jour la configuration du mappage attribut requiert tous les objets managés à être recalculées. Pour cette raison, il est recommandé pour conserver le nombre de modifications consécutives à vos mappages attribut au minimum.


##<a name="related-articles"></a>Articles connexes

- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
- [Automatiser utilisateur mise en service / d’annulation aux applications SaaS](active-directory-saas-app-provisioning.md)
- [Écriture d’Expressions pour les mappages d’attributs](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Étendue de filtres de mise en service de l’utilisateur](active-directory-saas-scoping-filters.md)
- [À l’aide de SCIM pour activer la configuration automatique d’utilisateurs et groupes d’Azure Active Directory aux applications](active-directory-scim-provisioning.md)
- [Notifications de mise en service du compte](active-directory-saas-account-provisioning-notifications.md)
- [Liste des didacticiels sur l’intégration des applications SaaS](active-directory-saas-tutorial-list.md)


<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png
