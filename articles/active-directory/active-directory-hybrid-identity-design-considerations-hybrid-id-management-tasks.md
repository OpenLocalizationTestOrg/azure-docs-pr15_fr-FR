<properties
    pageTitle="Identité de hybride Azure Active Directory considérations sur la conception : déterminer les tâches de gestion des identités hybride | Microsoft Azure"
    description="Contrôle d’accès conditionnelle, Azure Active Directory vérifie les conditions spécifiques que vous avez choisi lors de l’authentification de l’utilisateur et avant d’autoriser l’accès à l’application. Une fois que ces conditions sont remplies, l’utilisateur est authentifié et autorisé à accéder à l’application."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="plan-for-hybrid-identity-lifecycle"></a>Plan du cycle de vie d’identité hybride 

Identité figure parmi les bases de votre stratégie d’accès entreprise mobilité et d’application. Si vous vous connectez à votre appareil mobile ou d’une application SaaS, votre identité est la clé pour accéder à tous les éléments. Au niveau plus élevé, une solution de gestion des identités englobe unifier et la synchronisation entre vos référentiels identité qui inclut l’automatisation et centraliser le processus de mise en service des ressources. La solution d’identité doit être une identité centralisée dans plusieurs locaux et cloud et également utiliser une forme de fédération des identités pour maintenir l’authentification centralisée et en toute sécurité partager et collaborer avec des utilisateurs externes et les entreprises. Ressources comprises entre les systèmes d’exploitation et les applications à des personnes dans ou affilié à une organisation. Structure organisationnelle peut être modifiée pour prendre en compte les stratégies et les procédures de mise en service.

Il est également important de disposer d’une solution d’identité adaptée pour permettre aux utilisateurs en leur fournissant des expériences libre-service pour les garder en productivité. Votre solution identité est plus robuste si celle-ci permet de l’authentification unique pour les utilisateurs pour toutes les ressources de que dont elles ont besoin d’accéder à tous les administrateurs niveaux pouvant utilisez des procédures standard de gestion des informations d’identification utilisateur. Certains niveaux d’administration peut être réduites ou supprimée, en fonction de la largeur de la solution de gestion de mise en service. Par ailleurs, vous pouvez en toute sécurité répartir capacités d’administration, manuellement ou automatiquement, entre diverses organisations. Par exemple, un administrateur de domaine peut être utilisé uniquement les personnes et les ressources de ce domaine. Cet utilisateur peut effectuer les tâches administratives et mise à disponibilité, mais n’est pas autorisé à effectuer les tâches de configuration, telles que la création de flux de travail.


## <a name="determine-hybrid-identity-management-tasks"></a>Déterminer les tâches de gestion des identités hybride
Distribuer les tâches administratives dans votre organisation améliore la précision et l’efficacité de l’administration et améliore la solde de la charge de travail d’une organisation. Voici les données croisées dynamiques qui définissent un système de gestion des identités robuste.

 ![](./media/hybrid-id-design-considerations/Identity_management_considerations.png)


Pour définir des tâches de gestion des identités hybride, vous devez connaître certaines caractéristiques essentielles de l’organisation qui va adopter identité hybride. Il est important de comprendre les référentiels actuels utilisés pour les sources d’identité. En connaissant ces éléments fondamentaux, vous aurez la configuration requise notions de base et selon que vous devez poser des questions plus granulaires que vous vous aboutissez à une meilleure décision de conception de votre solution identité.  

Lorsque vous définissez ces exigences, assurez-vous qu’au moins les questions suivantes sont traitées

- Options de mise en service : 
 - La solution d’identité hybride ne prend en charge un système mise en service et la gestion de l’accès robuste ?
 - Comment les utilisateurs, groupes et accédant à gérer les mots de passe ?
 - La gestion du cycle de vie des identités est injoignable ? 
      - Combien de temps faut-il suspension du compte mises à jour votre mot de passe ?
      
- Gestion des licences : 
 - Signifie la gestion des licences hybride identité solution poignées ?
     - Si Oui, quelles sont les fonctionnalités sont disponibles ?
- La solution gère la gestion des licences basée sur un groupe ? 
      -Si Oui, est-il possible de lui affecter un groupe de sécurité ? 
       -Si Oui, le répertoire cloud affecte automatiquement des licences à tous les membres du groupe ? 
        -Que se passe-t-il si un utilisateur est par la suite ajouté à ou supprimé du groupe, sera une licence automatiquement affectée ou supprimée selon le cas ? 

- Intégration avec d’autres fournisseurs d’identité tiers :
- Cette solution hybride peut être intégrée aux fournisseurs d’identité de tiers pour implémenter l’authentification unique ?
- Est-il possible d’unifier tous les fournisseurs d’identité différente dans un système d’identité et cohérente ?
- Si Oui, comment et qui sont elles et quelles sont les fonctionnalités sont disponibles ?

## <a name="synchronization-management"></a>Gestion de la synchronisation
Un des objectifs d’un gestionnaire d’identités, pour pouvoir afficher tous les fournisseurs d’identité et maintenez-les synchronisées. Conserver les données synchronisées basé sur un fournisseur d’identité maître faisant autorité. Dans un scénario d’identité hybride, avec un modèle d’administration synchronisée, vous gérer toutes les identités utilisateur et l’équipement dans un serveur local et synchroniserez les comptes et, éventuellement, les mots de passe dans le cloud. L’utilisateur entre le même mot de passe local comme il ou elle effectue dans le cloud, et à se connecter, le mot de passe est vérifié par la solution d’identité. Ce modèle utilise un outil de synchronisation d’annuaires.
 
![](./media/hybrid-id-design-considerations/Directory_synchronization.png)Pour une conception appropriée la synchronisation de votre solution identité hybride vous assurer que les questions suivantes sont a répondu : • quelles sont les solutions de synchronisation disponibles pour la solution d’identité hybride ?
• Quels sont l’authentification unique sur les fonctionnalités disponibles ?
• Quelles sont les options pour la fédération des identités entre B2B et B2C ?

## <a name="next-steps"></a>Étapes suivantes
[Déterminer la stratégie adoption de gestion des identités hybride](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)


## <a name="see-also"></a>Voir aussi
[Vue d’ensemble des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)

