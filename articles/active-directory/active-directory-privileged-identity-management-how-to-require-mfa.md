<properties
   pageTitle="Comment faire pour exiger l’authentification multifacteur | Microsoft Azure"
   description="Découvrez comment afin d’exiger l’authentification multifacteur (MFA) pour les identités dotés de privilèges avec l’extension Azure Active Directory dotés de privilèges gestion des identités."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/01/2016"
   ms.author="kgremban"/>

# <a name="how-to-require-mfa-in-azure-ad-privileged-identity-management"></a>Comment faire pour exiger l’authentification Multifacteur dans la gestion des identités Azure AD dotés de privilèges

Nous vous recommandons d’exiger l’authentification multifacteur (MFA) pour l’ensemble de vos administrateurs. Cela permet de réduire le risque d’une attaque en raison d’un mot de passe compromis.

Vous pouvez exiger que les utilisateurs effectuer un défi l’authentification Multifacteur lorsqu’ils se connecter. Le billet de blog [l’authentification Multifacteur pour Office 365 et l’authentification Multifacteur pour Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) compare ce qui est inclus dans les abonnements Office et Azure, avec les fonctionnalités contenues dans l’offre de l’authentification multifacteur Microsoft Azure.

Vous pouvez également exiger que les utilisateurs effectuer un défi l’authentification Multifacteur lorsqu’ils activer un rôle dans Azure AD PIM. De cette façon, si l’utilisateur n’a pas été exécuter un défi l’authentification Multifacteur quand ils connecté, il devra faire par PIM.

## <a name="requiring-mfa-in-azure-ad-privileged-identity-management"></a>Exiger l’authentification Multifacteur dans la gestion des identités Azure AD privilégié

Lorsque vous gérez des identités PIM comme un rôle dotés de privilèges administrateur, vous pouvez voir alertes qui vous recommandons l’authentification Multifacteur pour les comptes dotés de privilèges. Cliquez sur l’alerte de sécurité dans le tableau de bord PIM et une nouvelle carte s’ouvre avec une liste des comptes d’administrateur qui nécessite l’authentification Multifacteur.  Vous pouvez exiger l’authentification Multifacteur en sélectionnant plusieurs rôles, puis en cliquant sur le bouton **Fix** , ou vous pouvez les points de suspension en regard de chaque rôle, puis cliquez sur le bouton **corriger** .

> [AZURE.IMPORTANT] Avec le bouton droit maintenant, l’authentification Multifacteur Azure ne fonctionne qu’avec travail ou comptes scolaires, pas les comptes Microsoft (généralement un compte personnel utilisé pour se connecter aux services Microsoft, tels que Skype, Xbox, Outlook.com, etc..). Pour cette raison, tout le monde à l’aide d’un compte Microsoft ne peut pas être un administrateur éligible, car ils ne peuvent pas utiliser l’authentification Multifacteur pour activer leur rôle. Si ces utilisateurs devront continuer à gérer les charges de travail à l’aide d’un compte Microsoft, les élever aux administrateurs permanents pour l’instant.

En outre, vous pouvez modifier l’exigence de l’authentification Multifacteur pour un rôle spécifique en cliquant dessus dans la section rôles du tableau de bord PIM. Ensuite, cliquez sur **paramètres** dans la carte de rôle, puis cliquez sur **Activer** sous l’authentification multifacteur.

## <a name="how-azure-ad-pim-validates-mfa"></a>Comment Azure AD PIM valide l’authentification Multifacteur

Il existe deux possibilités pour valider l’authentification Multifacteur lorsqu’un utilisateur Active un rôle.

L’option la plus simple consiste à s’appuient sur l’authentification Multifacteur Azure pour les utilisateurs qui sont l’activation d’un rôle dotés de privilèges. Pour ce faire, vérifiez d’abord que ces utilisateurs sont d’une licence, le cas échéant et ont enregistré pour l’authentification Multifacteur Azure. Plus d’informations sur la façon de procéder est [prise en](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)main de l’authentification multifacteur Azure dans le cloud. Il est recommandé, mais pas obligatoire, que vous configurez Azure AD pour appliquer l’authentification Multifacteur pour ces utilisateurs lorsqu’ils se connecter. C’est parce que les tests de l’authentification Multifacteur doivent être effectués par Azure AD PIM lui-même.

Si l’utilisateur authentifié local vous pouvez également avoir votre fournisseur d’identité responsable de l’authentification Multifacteur. Par exemple, si vous avez configuré Active Directory Federation Services afin d’exiger l’authentification basée sur les cartes à puce avant d’accéder à Azure AD, [sécurisation des ressources de cloud avec l’authentification multifacteur Azure et AD FS](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md) inclut des instructions pour la configuration AD FS pour envoyer des demandes à Azure AD. Lorsqu’un utilisateur essaie d’activer un rôle, Azure AD PIM acceptera que l’authentification Multifacteur a déjà été validé pour l’utilisateur une fois qu’elle reçoit les revendications appropriées.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
