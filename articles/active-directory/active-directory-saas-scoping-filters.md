<properties
    pageTitle="Application basée sur les attributs de mise en service avec étendue filtres | Microsoft Azure"
    description="Découvrez comment utiliser les filtres portées pour empêcher les objets dans les applications qui prennent en charge la mise en service automatisé utilisateur de réellement en cours sa mise en service si un objet ne répondent aux besoins de votre entreprise."
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


# <a name="attribute-based-app-provisioning-with-scoping-filters"></a>Application basée sur les attributs de mise en service avec étendue des filtres

L’objectif de cette section est d’expliquer comment portées filtres permet de définir des règles basées sur des attributs qui déterminent les utilisateurs qui seront déployés à l’application.





## <a name="clauses-and-scope-groups"></a>Clauses et des groupes d’étendue


![Étendue de filtre][1] 




Filtres portées sont définies par un ou plusieurs **groupes étendue**, chacun d'entre eux contenir une ou plusieurs **clauses**. Pour afficher les clauses d’un groupe de la zone en particulier, vous devez le développer en cliquant sur la flèche vers la gauche du nom du groupe.

Une **clause** détermine les utilisateurs qui sont autorisés à passer à travers le filtre étendue par l’évaluation des attributs de chaque utilisateur. Par exemple, vous pouvez avoir une clause qui requiert qu’attribut « état » d’un utilisateur s’égal à New York, ce qui signifie que seuls les utilisateurs de votre Saint-Étienne seront déployés dans l’application.

![Nom du groupe étendue][2] 



Chaque **groupe étendue** commence par obligatoire une **clause**, comme le montre la capture d’écran ci-dessus. Cette clause indique simplement que l’utilisateur doit être préalablement assigné à l’application avant d’être évalué par vos filtres étendues. Cette clause ne peut pas être supprimée ou modifiée.

Vous pouvez ajouter de nouvelles clauses ou nouveaux groupes étendue en appuyant sur le bouton approprié. Vous pouvez nommer chaque groupe étendue en modifiant sa propriété **Nom du groupe étendue** .





## <a name="how-scoping-filters-are-evaluated"></a>L’évaluation des filtres étendue

Au cours de la mise en service, nous tester chaque utilisateur affecté par rapport à vos filtres portées pour déterminer si cet utilisateur voulez attribuer à accéder à l’application. Vous pouvez considérer chaque clause comme étant un test qui doit être passé afin que l’utilisateur afin d’éviter l’obtention filtrés. 

Si vous avez plusieurs groupes étendue définis, chaque utilisateur doit passer au moins une d’elles afin d’accéder à l’application. Au sein de chaque groupe étendue, cependant, l’utilisateur doit passer chaque clause unique afin de transmettre ce groupe étendue spécifique. 

En d’autres termes, vous pouvez considérer groupes étendue comme étant ou combinaison, et vous pouvez vous représenter les clauses qu’ils contiennent comme étant en combinaison. Par exemple, considérez le filtre portée ci-dessous :


![Nom du groupe étendue][2]  


En fonction de ce filtre étendue, les utilisateurs doivent répondre aux critères suivants, afin d’être mis en service :

1. Ils doivent être affectées à l’application.

2. Ils doivent travailler dans le service d’ingénierie

3. Ils doivent être travail San Francisco ou au Canada.


##<a name="related-articles"></a>Articles connexes

- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
- [Automatiser utilisateur et annulation aux Applications SaaS](active-directory-saas-app-provisioning.md)
- [Personnalisation des mappages d’attributs de mise en service de l’utilisateur](active-directory-saas-customizing-attribute-mappings.md)
- [Écriture d’Expressions pour les mappages d’attributs](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Notifications de mise en service du compte](active-directory-saas-account-provisioning-notifications.md)
- [À l’aide de SCIM pour activer la configuration automatique d’utilisateurs et groupes d’Azure Active Directory aux applications](active-directory-scim-provisioning.md)
- [Liste des didacticiels sur l’intégration des applications SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-scoping-filters/ic782811.png
[2]: ./media/active-directory-saas-scoping-filters/ic782812.png
[3]: ./active-directory-saas-scoping-filters/ic782813.png
