<properties 
    pageTitle="Rapports de l’authentification multifacteur Azure"
    description="Cela explique comment modifier les paramètres utilisateur tels qu’obliger les utilisateurs à effectuer le processus de preuve à distance à nouveau."
    documentationCenter=""
    services="multi-factor-authentication"
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="managing-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gérer les paramètres d’utilisateur avec l’authentification multifacteur Azure dans le cloud

En tant qu’administrateur, vous pouvez gérer les paramètres utilisateur et les appareils suivants.  

- [Obliger les utilisateurs sélectionnés à fournir à nouveau les modes de communication](#require-selected-users-to-provide-contact-methods-again)
- [Supprimer des utilisateurs existants les mots de passe application](#delete-users-existing-app-passwords)
- [Restaurer l’authentification Multifacteur sur tous les périphériques suspendus pour un utilisateur](#restore-mfa-on-all-suspended-devices-for-a-user)






Ceci est utile si un ordinateur ou appareil perte ou de vol ou que vous devez supprimer un accès aux utilisateurs.


## <a name="require-selected-users-to-provide-contact-methods-again"></a>Obliger les utilisateurs sélectionnés à fournir à nouveau les modes de communication

Ce paramètre va force l’utilisateur pour terminer le processus d’inscription à nouveau lorsqu’il se connecte. N’oubliez pas que les applications de navigateur non continueront à fonctionner si l’utilisateur possède les mots de passe application pour les.  Vous pouvez supprimer les mots de passe utilisateurs application en sélectionnant également **Supprimer tous les mots d’application existant générées par les utilisateurs sélectionnés**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Comment les utilisateurs doivent fournir à nouveau les modes de communication




1. Connectez-vous au portail classique Azure.
2. Sur la gauche, cliquez sur Active Directory.
3. Sous, répertoire cliquez sur l’annuaire de l’utilisateur que vous voulez fournir leur méthode de contact à nouveau.
4. Dans la partie supérieure, cliquez sur utilisateurs.
5. Dans la partie inférieure de la page, cliquez sur Gérer les authentification multifacteur La page de l’authentification multifacteur s’ouvre.
6. Recherchez l’utilisateur que vous souhaitez gérer et cochez la case située en regard du nom. Vous devrez peut-être modifier l’affichage dans la partie supérieure.
7. Cela fait apparaître le lien **Gérer les paramètres utilisateur** sur la droite. Cliquez dessus.
8. Activez **les utilisateurs sélectionnés doivent fournir les modes de communication à nouveau**.
![Fournir des modes de communication](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
10. Cliquez sur Enregistrer.
11. Cliquez sur Fermer

## <a name="delete-users-existing-app-passwords"></a>Supprimer des utilisateurs existants les mots de passe application

Cela supprime tous les mots de passe d’application qu’un utilisateur a créé. Les applications non-navigateur qui ont été associées à ces mots de passe d’application cesseront de fonctionner jusqu'à ce qu’un nouveau mot de passe d’application est créé.

### <a name="how-to-delete-users-existing-app-passwords"></a>Comment supprimer des utilisateurs existants les mots de passe application

1. Connectez-vous au portail classique Azure.
2. Sur la gauche, cliquez sur Active Directory.
3. Sous, répertoire cliquez sur l’annuaire de l’utilisateur que vous voulez supprimer les mots de passe application pour.
4. Dans la partie supérieure, cliquez sur utilisateurs.
5. Dans la partie inférieure de la page, cliquez sur Gérer les authentification multifacteur La page de l’authentification multifacteur s’ouvre.
6. Recherchez l’utilisateur que vous souhaitez gérer et cochez la case située en regard du nom. Vous devrez peut-être modifier l’affichage dans la partie supérieure.
7. Cela fait apparaître le lien **Gérer les paramètres utilisateur** sur la droite. Cliquez dessus.
8. Placez une coche dans la zone **Supprimer tous les mots d’application existant générées par les utilisateurs sélectionnés**.
![Supprimer les mots de passe application](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
10. Cliquez sur Enregistrer.
10. Cliquez sur Fermer.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Restaurer l’authentification Multifacteur sur tous les périphériques à retenir pour un utilisateur

Les administrateurs ont la possibilité pour restaurer l’authentification multifacteur sur les navigateurs et périphériques d’utilisateurs. Lors de cette opération, cette action supprime la mémoriser l’authentification Multifacteur tous les périphériques de l’utilisateur et navigateurs et l’utilisateur seront nécessaire d’utiliser l’authentification Multifacteur lorsque vous vous connectez la prochaine fois.

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Comment faire pour restaurer l’authentification Multifacteur sur tous les périphériques suspendus pour un utilisateur

1. Connectez-vous au portail classique Azure.
2. Sur la gauche, cliquez sur Active Directory.
3. Sous, répertoire cliquez sur l’annuaire de l’utilisateur que vous voulez restaurer l’authentification multifacteur sur.
4. Dans la partie supérieure, cliquez sur utilisateurs.
5. Dans la partie inférieure de la page, cliquez sur Gérer les authentification multifacteur La page de l’authentification multifacteur s’ouvre.
6. Recherchez l’utilisateur que vous souhaitez gérer et cochez la case située en regard du nom. Vous devrez peut-être modifier l’affichage dans la partie supérieure.
7. Cela fait apparaître le lien **Gérer les paramètres utilisateur** sur la droite. Cliquez dessus.
8. Cochez la case dans **restaurer l’authentification multifacteur sur tous les périphériques à retenir**
![supprimer les mots de passe application](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. Cliquez sur Enregistrer.
10. Cliquez sur Fermer.
