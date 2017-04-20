<properties
    pageTitle="Les Services de domaine Active Directory Azure : Activer la synchronisation de mot de passe | Microsoft Azure"
    description="Prise en main Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="maheshu"/>

# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Activer la synchronisation de mot de passe pour les Services de domaine Active Directory Azure
Dans tâches précédentes, vous avez activé Azure Active Directory Domain Services pour votre client Azure AD. La tâche suivante consiste à activer hachage d’informations d’identification requises pour l’authentification NTLM et Kerberos à synchroniser avec Azure Active Directory Domain Services. Une fois que la synchronisation d’informations d’identification a été configurée, les utilisateurs peuvent se connecter au domaine géré à l’aide de leurs informations d’identification d’entreprise.

Les étapes à suivre sont différentes selon que votre organisation possède une annonce Azure exclusivement le nuage, du client ou est configuré pour synchroniser avec votre annuaire local à l’aide d’Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Client AD Azure exclusivement le nuage](active-directory-ds-getting-started-password-sync.md)
- [Synchronisé client Azure AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>


## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-cloud-only-azure-ad-tenant"></a>Étape 5 : Activer la synchronisation de mot de passe aux Services de domaine AAD pour un Azure exclusivement le nuage, client AD
Services de domaine Active Directory Azure a besoin des informations d’identification hachage dans un format approprié pour l’authentification NTLM et Kerberos, pour authentifier les utilisateurs sur le domaine géré. Si vous n’activez DAS les Services de domaine pour votre client, Azure AD ne pas générer ou stocker les informations d’identification hachage au format requis pour l’authentification Kerberos ou NTLM. Pour des raisons de sécurité évidente, Azure AD également ne pas stocke des informations d’identification sous forme de texte clair. Par conséquent, Azure AD n’a pas de moyen de générer ces hachage d’informations d’identification NTLM ou Kerberos en fonction des informations d’identification existantes des utilisateurs.

> [AZURE.NOTE] Si votre organisation possède un exclusivement le nuage, client Azure AD, les utilisateurs ayant besoin d’utiliser les Services de domaine Active Directory Azure doit modifier leur mot de passe.

Ce processus de changement de mot de passe entraîne les informations d’identification hachage requis par les Services de domaine Active Directory Azure pour l’authentification Kerberos et à générer dans Azure AD. Vous pouvez soit expirer les mots de passe pour tous les utilisateurs dans le client qu’amené à utiliser les Services de domaine Active Directory Azure ou demandez à ces utilisateurs de modifier leur mot de passe.


### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-azure-ad-tenant"></a>Activer la NTLM et Kerberos génération de hachage d’informations d’identification pour une Azure exclusivement le nuage, client AD
Voici des instructions que vous avez besoin fournir aux utilisateurs finaux, afin qu’ils peuvent modifier leur mot de passe :

1. Accédez à la page d’accès Azure AD pour votre organisation en [http://myapps.microsoft.com](http://myapps.microsoft.com).

2. Sélectionnez l’onglet **profil** sur cette page.

3. Cliquez sur la vignette **Modifier mot de passe** sur cette page.

    ![Créer un réseau virtuel pour les Services de domaine Active Directory Azure.](./media/active-directory-domain-services-getting-started/user-change-password.png)

    > [AZURE.NOTE] Si vous ne voyez pas l’option **Modifier mot de passe** dans la page d’accès, assurez-vous que votre organisation a configuré la [Gestion des mots de passe dans Azure Active Directory](../active-directory/active-directory-passwords-getting-started.md).

4. Dans la page **Modifier le mot de passe** , tapez votre mot de passe (ancien) et tapez un nouveau mot de passe et confirmez-le. Cliquez sur **Envoyer**.

    ![Créer un réseau virtuel pour les Services de domaine Active Directory Azure.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Une fois que vous avez modifié votre mot de passe, le nouveau mot de passe sera bientôt utilisable dans Azure Active Directory Domain Services. Après quelques minutes (en règle générale, environ 20 minutes,), vous pouvez vous connecter à des ordinateurs rejoint le domaine géré en utilisant le mot de passe nouvellement modifié.

<br>

## <a name="related-content"></a>Contenu associé

- [Comment mettre à jour votre mot de passe](../active-directory/active-directory-passwords-update-your-own-password.md)

- [Mise en route de la gestion des mots de passe dans Azure Active Directory](../active-directory/active-directory-passwords-getting-started.md).

- [Activer la synchronisation de mot de passe aux Services de domaine AAD pour un Azure synchronisé client AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)

- [Administrer un domaine géré Azure Active Directory Domain Services](active-directory-ds-admin-guide-administer-domain.md)

- [Participer à une machine virtuelle Windows à un domaine géré Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)

- [Participer à une machine virtuelle Red chapeau Enterprise Linux à un domaine géré Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
