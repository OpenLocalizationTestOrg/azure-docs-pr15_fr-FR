<properties 
    pageTitle="États de Microsoft Azure multifacteur d’authentification utilisateur"
    description="En savoir plus sur les États utilisateur dans l’authentification Multifacteur Azure."
    services="multi-factor-authentication"
    documentationCenter=""
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

# <a name="user-states-in-azure-multi-factor-authentication"></a>États utilisateur dans l’authentification multifacteur Azure

Comptes d’utilisateurs dans l’authentification multifacteur Azure disposez les trois états distincts suivants :

État | Description |Applications de navigateur de non affectées| Notes
:-------------: | :-------------: |:-------------: |:-------------: |
Désactivé | L’état par défaut pour un nouvel utilisateur ne pas inscrit dans l’authentification multifacteur.|N°|L’utilisateur n’utilise pas l’authentification multifacteur.
Activé |L’utilisateur a été inscrite dans l’authentification multifacteur.|Non.  Ils continuent à fonctionner jusqu'à ce que le processus d’inscription est terminé.|L’utilisateur est activé, mais n’a pas terminé le processus d’inscription. Il devra pour terminer le processus à la connexion suivante.
Mises en œuvre|L’utilisateur a été inscrite et a terminé le processus d’inscription pour l’utilisation de l’authentification multifacteur.|Oui.  Applications requièrent des mots de passe de l’application. | L’utilisateur peut ou ne peut-être pas encore terminé l’enregistrement. Si ils ont terminé le processus d’inscription, ils utilisent l’authentification multifacteur. Dans le cas contraire, l’utilisateur sera invité à procéder à la connexion suivante.

## <a name="changing-a-user-state"></a>Modifier l’état de l’utilisateur
Modifications d’un état aux utilisateurs en fonction d’ou non, il a été le programme d’installation pour l’authentification Multifacteur et si l’utilisateur a terminé le processus.  Lorsque vous activez l’authentification Multifacteur pour un utilisateur, les utilisateurs ne pourra état désactivé à activé.  Une fois que l’utilisateur, dont l’état a été modifié en activé, se connecte et termine le processus, leur état remplacera par appliquée.  

### <a name="to-view-a-users-state"></a>Pour afficher l’état d’un utilisateur
--------------------------------------------------------------------------------
1.  Connectez-vous au **portail classique Azure** en tant qu’administrateur.
2.  Sur la gauche, cliquez sur **Active Directory**.
3.  Sous, **répertoire** , cliquez sur le répertoire pour l’utilisateur que vous souhaitez activer.
![Cliquez sur le répertoire](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Dans la partie supérieure, cliquez sur **utilisateurs**.
5.  Dans la partie inférieure de la page, cliquez sur **Gérer les authentification multifacteur**.
![Cliquez sur le répertoire](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Un nouvel onglet de navigateur s’ouvre.  Vous ne pourrez pas afficher l’état des utilisateurs.
![Cliquez sur le répertoire](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

###<a name="to-change-the-state-from-disabled-to-enabled"></a>Pour modifier l’état à partir de désactivé à activé
1.  Connectez-vous au **portail classique Azure** en tant qu’administrateur.
2.  Sur la gauche, cliquez sur **Active Directory**.
3.  Sous, **répertoire** , cliquez sur le répertoire pour l’utilisateur que vous souhaitez activer.
![Cliquez sur le répertoire](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Dans la partie supérieure, cliquez sur **utilisateurs**.
5.  Dans la partie inférieure de la page, cliquez sur **Gérer les authentification multifacteur**.
![Cliquez sur le répertoire](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Un nouvel onglet de navigateur s’ouvre.  Recherchez l’utilisateur que vous souhaitez activer pour l’authentification multifacteur. Vous devrez peut-être modifier l’affichage dans la partie supérieure. Vérifiez que l’état est **désactivé.** 
 ![Autoriser l’utilisateur](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  Placez une **vérification** dans la zone en regard de son nom.
7.  Sur la droite, cliquez sur **Activer**.
![Utilisateur](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Cliquez sur **Activer l’authentification multifacteur**.
![Utilisateur](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  Vous devriez remarquer de que état de l’utilisateur a changé de **désactivé** à **activé**.
![Permettre aux utilisateurs](./media/multi-factor-authentication-get-started-cloud/user.png)
10.  Une fois que vous avez activé vos utilisateurs, il est recommandé d’informer par courrier électronique.  Il doit également informer comment ils peuvent utiliser leurs applications de navigateur non pour éviter d’être verrouillé.

### <a name="to-change-the-state-from-enabledenforced-to-disabled"></a>Pour modifier l’état d’activé/appliquées sur désactivé
1.  Connectez-vous au **portail classique Azure** en tant qu’administrateur.
2.  Sur la gauche, cliquez sur **Active Directory**.
3.  Sous, **répertoire** , cliquez sur le répertoire pour l’utilisateur que vous souhaitez activer.
![Cliquez sur le répertoire](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Dans la partie supérieure, cliquez sur **utilisateurs**.
5.  Dans la partie inférieure de la page, cliquez sur **Gérer les authentification multifacteur**.
![Cliquez sur le répertoire](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Un nouvel onglet de navigateur s’ouvre.  Recherchez l’utilisateur que vous souhaitez désactiver. Vous devrez peut-être modifier l’affichage dans la partie supérieure. Vérifiez que l’état est soit **activée** ou **appliquées.**
7.  Placez une **vérification** dans la zone en regard de son nom.
7.  Sur la droite, cliquez sur **désactiver**.
![Désactiver un utilisateur](./media/multi-factor-authentication-get-started-user-states/userstate2.png)
8.  Vous devrez le confirmer.  Cliquez sur **Oui**.
![Désactiver un utilisateur](./media/multi-factor-authentication-get-started-user-states/userstate3.png)
9.  Vous devez ensuite voir si elle a réussi.  Cliquez sur **Fermer.** 
 ![Désactiver l’utilisateur](./media/multi-factor-authentication-get-started-user-states/userstate4.png)
