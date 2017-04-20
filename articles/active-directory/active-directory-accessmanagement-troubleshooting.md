
<properties
    pageTitle="Résolution des problèmes d’appartenance dynamique pour les groupes | Microsoft Azure"
    description="Conseils de dépannage pour dynamique appartenance des groupes dans Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""
    />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="curtand"/>


# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Résolution des problèmes d’appartenance dynamique pour les groupes

**J’ai configuré une règle sur un groupe, mais aucune appartenance mises à jour dans le groupe**<br/>Vérifiez que le paramètre **Activer déléguer la gestion de groupe** est défini sur **Oui** dans l’onglet **configurer** . Vous verrez ce paramètre uniquement si vous êtes connecté tant qu’utilisateur auquel est affectée une licence Azure Active Directory Premium. Vérifier les valeurs des attributs de l’utilisateur sur la règle : quels sont les utilisateurs qui satisfont la règle ?

**J’ai configuré une règle, mais maintenant les membres existants de la règle sont supprimés**<br/>Ce comportement est normal. Les membres existants du groupe sont supprimés lorsqu’une règle est activée ou modifiée. Les utilisateurs renvoyées de l’évaluation de la règle sont ajoutés en tant que membres au groupe.     

**Je ne vois pas l’appartenance change instantanément lorsque ajouter ou modifier une règle, pourquoi pas ?**<br/>Évaluation de l’adhésion dédié est effectuée régulièrement un processus en arrière-plan asynchrone. La durée de la procédure est déterminé par le nombre d’utilisateurs dans votre annuaire et la taille du groupe créé à la suite de la règle. En règle générale, répertoires avec un petit nombre d’utilisateurs pourront voir les modifications de l’appartenance de groupe de moins de quelques minutes. Répertoires avec un grand nombre d’utilisateurs peuvent prendre 30 minutes ou plus de temps à remplir.

Les articles suivants fournissent des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources des groupes de Azure Active Directory](active-directory-manage-groups.md)
* [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Qu’est Azure Active Directory ?](active-directory-whatis.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
