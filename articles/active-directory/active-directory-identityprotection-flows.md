<properties
    pageTitle="Connexion à des expériences avec Protection d’Azure AD identité | Microsoft Azure"
    description="Fournit une vue d’ensemble de l’expérience utilisateur lorsque la Protection d’identité a réduits ou résolues un utilisateur ou lorsque l’authentification multifacteur est requis par une stratégie."
    services="active-directory"
    keywords="protection d’identité Azure AD, la découverte application cloud, gestion des applications, sécurité, risque, le niveau de risque, vulnérabilité, stratégie de sécurité"
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
    ms.date="08/16/2016"
    ms.author="markvi"/>

# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Connexion à des expériences avec Protection d’Azure AD identité

Avec Protection d’identité Azure Active Directory, vous pouvez :

- obliger les utilisateurs à s’inscrire à l’authentification multifacteur

- gérer les compléments d’authentification risquées et utilisateurs compromis

La réponse du système à ces problèmes a un impact sur l’expérience de connexion d’un utilisateur car simplement directement signature en fournissant un nom d’utilisateur et un mot de passe n’est pas possible plus. Étapes supplémentaires sont nécessaires pour obtenir un utilisateur en toute sécurité réinsérer dans l’entreprise.

Cette rubrique vous donne un aperçu de l’expérience de connexion d’un utilisateur pour tous les cas qui peuvent se produire.

**Authentification multifacteur**

- Inscription de l’authentification multifacteur



**Connectez-vous à risque**

- Récupération de connexion risquée

- Risquée connexion bloqué

- Inscription de l’authentification multifacteur pendant une connexion risquée
 

**Utilisateur exposés**

- Récupération de compte compromis

- Compte compromis bloqué




## <a name="multi-factor-authentication-registration"></a>Inscription de l’authentification multifacteur

La meilleure expérience utilisateur dans les deux cas, le flux de récupération de compte compromis et le flux connexion risqué, est lorsque l’utilisateur peut restaurer automatique. Si les utilisateurs sont enregistrés pour l’authentification multifacteur, ils ont déjà un numéro de téléphone associé à leur propre compte qui peut être utilisé pour passer des problèmes de sécurité. Aucun engagement de bureau ou votre administrateur aide n’est nécessaire pour récupérer du compromis de compte. Par conséquent, il est vivement recommandé pour obtenir vos utilisateurs enregistrés pour l’authentification multifacteur. 

Les administrateurs peuvent :

- définir une stratégie qui impose aux utilisateurs de configurer leurs comptes pour la vérification de sécurité supplémentaires. 
- Autoriser sauter l’enregistrement de l’authentification multifacteur pendant 30 jours, au cas où ils souhaiteraient donner aux utilisateurs une période de grâce avant d’enregistrer.

**L’inscription de l’authentification multifacteur implique trois étapes :**

1. Dans la première étape, l’utilisateur reçoit une notification sur la configuration minimale requise pour définir le compte pour l’authentification multifacteur. 

    ![Mise à jour] (./media/active-directory-identityprotection-flows/140.png "Mise à jour")


2. Pour configurer l’authentification multifacteur, vous devez indiquer le système de manière dont vous souhaitez être contacté.

    ![Mise à jour] (./media/active-directory-identityprotection-flows/141.png "Mise à jour")
 
3. Le système envoie un défi pour vous et que vous devez répondre.

    ![Mise à jour] (./media/active-directory-identityprotection-flows/142.png "Mise à jour")

 



## <a name="risky-sign-in-recovery"></a>Récupération de connexion risquée

Lorsqu’un administrateur a configuré une stratégie pour se connecter risques, les utilisateurs concernés sont avertis lorsqu’il tente de se connecter. 

**Le flux de connexion risqué comporte deux étapes :** 

1. L’utilisateur est informé que sur une chose inhabituelle a été détectée sur leur connexion, telles que la connexion à partir d’un nouvel emplacement, un appareil ou une application. 

    ![Mise à jour] (./media/active-directory-identityprotection-flows/120.png "Mise à jour")

2. L’utilisateur est nécessaire pour prouver son identité par résoudre un problème de sécurité. Si l’utilisateur est enregistré pour l’authentification multifacteur dont elles ont besoin pour effectuer un aller-retour un code de sécurité à son numéro de téléphone. Dans la mesure où il s’agit simplement une connexion risquée et pas un compte compromis, l’utilisateur ne dispose modifier le mot de passe dans ce flux. 

    ![Mise à jour] (./media/active-directory-identityprotection-flows/121.png "Mise à jour")



 
## <a name="risky-sign-in-blocked"></a>Risquée connexion bloqué
Les administrateurs peuvent également définir une stratégie de connexion risque à bloquer des utilisateurs lors de la connexion en fonction du niveau de risque. Pour accéder à débloquer, les utilisateurs finaux doit contacter un administrateur ou le support technique ou qu’ils peuvent réessayer de se connecter à partir d’un emplacement de votre choix ou appareil. Récupération automatique en résolvant l’authentification multifacteur n’est pas une option dans ce cas.

![Mise à jour] (./media/active-directory-identityprotection-flows/200.png "Mise à jour")




## <a name="compromised-account-recovery"></a>Récupération de compte compromis

Lorsqu’une stratégie de sécurité utilisateur risque a été configurée, les utilisateurs qui répondent à l’utilisateur risque de niveau spécifié dans la stratégie (et par conséquent par défaut sont compromis) doivent passer par le flux de récupération utilisateur compromis avant qu’ils peuvent se connecter. 

**Le flux de récupération utilisateur compromis implique trois étapes :**

1. L’utilisateur est informé que leur sécurité du compte est exposé en raison d’activité suspecte ou divulguées des informations d’identification.

    ![Mise à jour] (./media/active-directory-identityprotection-flows/101.png "Mise à jour")

2.  L’utilisateur est nécessaire pour prouver son identité par résoudre un problème de sécurité. Si l’utilisateur est enregistré pour l’authentification multifacteur ils peuvent récupérer automatique d’être compromis. Elles devront aller-retour un code de sécurité à son numéro de téléphone. 

    ![Mise à jour] (./media/active-directory-identityprotection-flows/110.png "Mise à jour")


3.  Enfin, l’utilisateur est obligé de modifier leur mot de passe, car une autre personne a pu avoir accès à leur propre compte. Captures d’écran de cette expérience sont en dessous.
 
    ![Mise à jour] (./media/active-directory-identityprotection-flows/111.png "Mise à jour")



## <a name="compromised-account-blocked"></a>Compte compromis bloqué 

Pour obtenir un utilisateur qui a été bloqué par une stratégie de sécurité utilisateur risque déblocage, l’utilisateur doit contacter un administrateur ou service d’assistance. Récupération automatique en résolvant l’authentification multifacteur n’est pas une option dans ce cas.


![Mise à jour] (./media/active-directory-identityprotection-flows/104.png "Mise à jour")



 
## <a name="reset-password"></a>Réinitialiser votre mot de passe

Si les utilisateurs compromis sont bloqués à partir de la connexion, un administrateur peut générer un mot de passe temporaire pour eux. Les utilisateurs disposeront modifier leur mot de passe au cours d’une connexion suivante.

![Mise à jour] (./media/active-directory-identityprotection-flows/160.png "Mise à jour")


 




 

## <a name="see-also"></a>Voir aussi

- [Protection d’identité Azure Active Directory](active-directory-identityprotection.md) 