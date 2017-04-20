<properties
    pageTitle="Gestion des identités Azure AD privilégié | Microsoft Azure"
    description="Une rubrique qui décrit les nouveautés de la gestion des identités Azure AD dotés de privilèges et comment utiliser PIM pour améliorer votre sécurité cloud."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="kgremban"/>

# <a name="azure-ad-privileged-identity-management"></a>Gestion des identités Azure AD privilégié

Avec la gestion des identités dotés de privilèges Azure Active Directory (AD), vous pouvez gérer, de contrôler et surveiller l’accès au sein de votre organisation. Cela inclut l’accès aux ressources dans Azure Active Directory et d’autres services en ligne Microsoft, tels que Office 365 ou Microsoft Intune.  

> [AZURE.NOTE] Gestion des identités privilégié est disponible qu’avec l’édition Premium P2 d’Azure Active Directory. Pour plus d’informations, voir [éditions Azure Active Directory](active-directory-editions.md).

Organisations souhaitent réduire le nombre de personnes qui ont accès à des informations sécurisées ou des ressources, car afin de réduire les risques d’un utilisateur malveillant prise par access. Toutefois, les utilisateurs doivent toujours effectuer les opérations dotés de privilèges dans les applications Azure, Office 365 ou SaaS. Organisations accorder l’accès dotés de privilèges utilisateurs dans Azure AD sans surveillance ce que font les utilisateurs avec leurs privilèges d’administrateur. Gestion des identités Azure AD privilégié permet de résoudre ces risques.  

Gestion des identités Azure AD privilégié permet de :  

- Voir quels utilisateurs sont administrateurs Azure AD
- Activer à la demande, « juste à temps « accès administratif à Microsoft Online Services tels qu’Office 365 et Intune
- Générez des rapports sur l’historique de l’accès administrateur et les modifications apportées aux affectations de l’administrateur
- Recevoir des alertes sur l’accès à un rôle dotés de privilèges

Azure AD dotés de privilèges gestion des identités peuvent gérer les intégrées de rôles d’organisation Azure AD, y compris :  

- Administrateur global
- Administrateur de facturation
- Administrateur de service  
- Administrateur d’utilisateurs
- Mot de passe administrateur

## <a name="just-in-time-administrator-access"></a>Seulement dans les droits d’administrateur de temps

Traditionnellement, vous pouvez affecter un utilisateur à un rôle d’administrateur via le portail classique Azure ou Windows PowerShell. Par conséquent, cet utilisateur devient un **administrateur permanent**, toujours actif dans le rôle a été attribué. Gestion des identités Azure AD privilégié introduit le concept d’un **administrateur éligible**. Les administrateurs éligibles doivent être utilisateurs nécessitant un accès privilégié maintenant, puis, mais pas tous les jours. Le rôle est inactif jusqu'à ce que l’utilisateur doit avoir accès, puis terminer un processus d’activation et de devenir administrateur actif pendant un laps de temps prédéterminé.

## <a name="enable-privileged-identity-management-for-your-directory"></a>Activer la gestion des identités dotés de privilèges pour votre annuaire

Vous pouvez commencer à l’aide de la gestion des identités Azure AD dotés de privilèges dans le [portail Azure](https://portal.azure.com/).

>[AZURE.NOTE] Vous devez être un administrateur global avec un compte professionnel (par exemple, @yourdomain.com), pas un compte Microsoft (par exemple, @outlook.com), pour activer la gestion des identités Azure AD dotés de privilèges pour un répertoire.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre répertoire.
2. Si votre organisation possède plusieurs répertoires, sélectionnez votre nom d’utilisateur dans le coin supérieur droit du portail Azure. Sélectionnez le répertoire où vous allez utiliser la gestion des identités Azure AD dotés de privilèges.
3. Sélectionnez **plusieurs services** et utilisez la zone de texte de filtre pour rechercher **la gestion des identités Azure AD dotés de privilèges**.
4. Vérifier le **code confidentiel au tableau de bord** , puis sur **créer**. L’application de gestion des identités dotés de privilèges s’ouvre.

Si vous êtes le premier participant à utiliser la gestion des identités Azure AD dotés de privilèges dans votre annuaire, puis l' [Assistant sécurité](active-directory-privileged-identity-management-security-wizard.md) vous guide dans l’expérience d’affectation initiale. Une fois que vous devenez automatiquement le premier **administrateur de la sécurité** et l' **administrateur de rôles dotés de privilèges** du répertoire.

Seul l’administrateur du rôle de privilège peut gérer l’accès aux autres administrateurs. Vous pouvez [Autoriser d’autres utilisateurs la possibilité de gestion de PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="privileged-identity-management-dashboard"></a>Tableau de bord de gestion des identités dotés de privilèges

Gestionnaire d’identité AD privilégié Azure fournit un tableau de bord qui vous donne des informations importantes telles que :

- Alertes qui proposent des opportunités pour améliorer la sécurité
- Le nombre d’utilisateurs affectés à chaque rôle dotés de privilèges  
- Le nombre d’administrateurs éligibles et permanents
- Un accès en continu révisions

![Tableau de bord PIM - capture d’écran][2]

## <a name="privileged-role-management"></a>Gestion des rôles dotés de privilèges

Avec la gestion des identités Azure AD dotés de privilèges, vous pouvez gérer les administrateurs en ajoutant ou en supprimant les administrateurs permanents ou éligibles à chaque rôle.

![Administrateurs d’Ajout/Suppression PIM - capture d’écran][3]

## <a name="configure-the-role-activation-settings"></a>Configurer les paramètres de l’activation de rôle

En utilisant les [paramètres de rôle](active-directory-privileged-identity-management-how-to-change-default-settings.md) , vous pouvez configurer les propriétés de l’activation de rôle éligibles, notamment :

- La durée de la période de l’activation de rôle
- La notification de l’activation de rôle
- Les informations d’un utilisateur doivent fournir au cours du processus d’activation de rôle  

![Capture d’écran de paramètres - activation administrateur - PIM][4]

Notez que dans l’image, les boutons de **L’authentification multifacteur** sont désactivés. Pour certains, hautement privilégié rôles, nous avons besoin de l’authentification Multifacteur pour protection renforcée.

## <a name="role-activation"></a>Activation de rôle  

Pour [Activer un rôle](active-directory-privileged-identity-management-how-to-activate-role.md), un administrateur éligible demandes d’une heure liées aux « activation » pour le rôle. L’activation peut être demandée à l’aide de l’option **Activer mon rôle** dans la gestion des identités Azure AD dotés de privilèges.

Un administrateur qui souhaite activer un rôle doit initialisation de la gestion des identités Azure AD dotés de privilèges dans le portail Azure.

Activation de rôle est personnalisable. Dans les paramètres PIM, vous pouvez déterminer la longueur de l’activation et que l’administrateur doit fournir pour activer le rôle des informations.

![Activation de rôle demande PIM administrateur - capture d’écran][5]

## <a name="review-role-activity"></a>Activité de rôle de révision

Il existe deux moyens de suivre la façon dont vos employés et les administrateurs utilisez des rôles dotés de privilèges. La première option utilise [historique d’audit](active-directory-privileged-identity-management-how-to-use-audit-log.md). L’historique d’audit enregistre le suivi des modifications dans les attributions de rôle dotés de privilèges et l’historique de l’activation de rôle.

![Historique de l’activation PIM - capture d’écran][6]

La deuxième option consiste à configurer la régulières [access analyse](active-directory-privileged-identity-management-how-to-start-security-review.md). Ces révisions access peuvent être effectuées par et affectées réviseur (par exemple, un responsable d’équipe) ou les employés peuvent passer en revue eux-mêmes. Il s’agit de la meilleure façon de surveiller qui doit toujours avoir accès, et qui n’est plus le cas.


## <a name="next-steps"></a>Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png
