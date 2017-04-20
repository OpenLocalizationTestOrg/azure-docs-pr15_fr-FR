<properties
    pageTitle="Comment déployer l’Extension du Panneau de configuration Access pour Internet Explorer à l’aide de la stratégie de groupe | Microsoft Azure"
    description="Découvrez comment utiliser la stratégie de groupe pour déployer le module complémentaire Internet Explorer pour le portail de mes applications."
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>
<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/16/2016"
    ms.author="markvi"/>

#<a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Comment déployer l’Extension du Panneau de configuration Access pour Internet Explorer à l’aide de la stratégie de groupe

Ce didacticiel montre comment utiliser la stratégie de groupe pour installer à distance l’extension du panneau d’accès pour Internet Explorer sur les ordinateurs de vos utilisateurs. Cette extension est requise pour les utilisateurs d’Internet Explorer qui ont besoin de vous connecter à des applications qui sont configurées à l’aide de [mot de passe de l’authentification unique](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

Il est recommandé que les administrateurs automatisent le déploiement de cette extension. Dans le cas contraire, les utilisateurs devront télécharger et installer l’extension eux-mêmes, laquelle est propice aux erreurs des utilisateurs et nécessite des autorisations d’administrateur. Ce didacticiel couvre une méthode d’automatisation des déploiements de logiciels à l’aide de stratégie de groupe. [En savoir plus sur la stratégie de groupe.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

L’extension du panneau d’accès est également disponible pour [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) et [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), qui ne nécessitent des autorisations d’administrateur pour installer.

##<a name="prerequisites"></a>Conditions préalables

- Vous avez configuré [Les Services de domaine Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), et vous avez rejoint machines de vos utilisateurs à votre domaine.
- Vous devez disposer de l’autorisation « Modifier les paramètres » pour modifier des objets de stratégie de groupe (stratégie de groupe). Par défaut, les membres de groupes de sécurité suivants ont cette autorisation : les administrateurs de domaine, les administrateurs d’entreprise et propriétaires créateurs de la stratégie de groupe. [Pour en savoir plus.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

##<a name="step-1-create-the-distribution-point"></a>Étape 1 : Créer le Point de Distribution

Tout d’abord, vous devez placer le package d’installation sur un emplacement réseau qui sont accessibles à partir de tous les ordinateurs que vous souhaitez installer à distance l’extension sur. Pour ce faire, procédez comme suit :

1. Ouvrez une session sur le serveur en tant qu’administrateur

2. Dans la fenêtre **Gestionnaire de serveur** , accédez aux **fichiers et Services de stockage**.

    ![Ouvrir des fichiers et des Services de stockage](./media/active-directory-saas-ie-group-policy/files-services.png)

3. Accédez à l’onglet **actions** . Puis cliquez sur **tâches** > **Nouveau partage...**

    ![Ouvrir des fichiers et des Services de stockage](./media/active-directory-saas-ie-group-policy/shares.png)

4. Exécuter l' **Assistant Nouveau partage** et définissez les autorisations pour vous assurer qu’elle accessible à partir d’ordinateurs de vos utilisateurs. [Apprenez-en davantage sur Actions.](https://technet.microsoft.com/library/cc753175.aspx)

5. Téléchargez le package Microsoft Windows Installer suivant (fichier .msi) : [Extension.msi Panneau de configuration de l’accès](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access Panel Extension.msi)

6. Copiez le package d’installation vers l’emplacement souhaité sur le partage.

    ![Copier le fichier .msi, vous êtes le partage.](./media/active-directory-saas-ie-group-policy/copy-package.png)

8. Vérifiez que vos ordinateurs clients sont en mesure d’accéder le package d’installation à partir du partage. 

##<a name="step-2-create-the-group-policy-object"></a>Étape 2 : Créer l’objet de stratégie de groupe

1. Ouvrez une session sur le serveur qui héberge votre installation de Services de domaine Active Directory (AD DS).

2. Dans le Gestionnaire de serveur, accédez à **Outils** > **Gestion des stratégies de groupe**.

    ![Cliquez sur Outils > Gestion de la stratégie de groupe](./media/active-directory-saas-ie-group-policy/tools-gpm.png)

3. Dans le volet gauche de la fenêtre de **Gestion des stratégies de groupe** , affichez votre hiérarchie d’unité d’organisation (OU) et déterminer à quelle étendue que vous voulez appliquer la stratégie de groupe. Par exemple, vous pouvez décider de choisir une petite unité d’organisation à déployer quelques utilisateurs de test, ou vous pouvez choisir une unité d’organisation de niveau supérieur à déployer votre organisation entière.

    > [AZURE.NOTE] Si vous voulez créer ou modifier votre organisation d’unités (ou), passer au serveur gestionnaire et accédez à **Outils** > **utilisateurs Active Directory et ordinateurs**.

4. Une fois que vous avez sélectionné une unité d’organisation, avec le bouton droit dessus, puis sélectionnez **créer un objet de stratégie de groupe dans ce domaine et le lier ici...**

    ![Créer une nouvelle stratégie de groupe](./media/active-directory-saas-ie-group-policy/create-gpo.png)

5. Dans l’invite de **Stratégie de groupe nouveau** , tapez un nom pour le nouvel objet de stratégie de groupe.

    ![Nom du nouvel objet](./media/active-directory-saas-ie-group-policy/name-gpo.png)

6. Avec le bouton droit sur l’objet de stratégie de groupe que vous venez de créer, puis sélectionnez **Modifier**.

    ![Modifier l’objet de stratégie de groupe](./media/active-directory-saas-ie-group-policy/edit-gpo.png)

##<a name="step-3-assign-the-installation-package"></a>Étape 3 : Attribuer le Package d’Installation

1. Déterminer si vous voulez déployer l’extension en fonction de la **Configuration de l’ordinateur** ou de **Configuration de l’utilisateur**. Lorsque vous utilisez la [configuration de l’ordinateur](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), l’extension sera installée sur l’ordinateur quelle que soit la session utilisateurs. En revanche, avec la [configuration de l’utilisateur](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx), les utilisateurs auront l’extension installée pour les indépendamment quels ordinateurs ils ouvrent une session sur.

2. Dans le volet gauche de la fenêtre de **l’Éditeur de gestion de stratégie de groupe** , accédez à un des chemins d’accès dossier suivants, selon le type de configuration que vous avez choisi :
    - `Computer Configuration/Policies/Software Settings/`
    - `User Configuration/Policies/Software Settings/`

3. Avec le bouton droit sur **installation du logiciel**, puis sélectionnez **Nouveau** > **Package...**

    ![Créer un package d’installation logiciel](./media/active-directory-saas-ie-group-policy/new-package.png)

4. Accédez au dossier partagé contenant le package d’installation à partir de [étape 1 : créer le Point de Distribution](#step-1-create-the-distribution-point), sélectionnez le fichier .msi, puis cliquez sur **Ouvrir**.

    > [AZURE.IMPORTANT] Si l’option partager se trouve sur ce même serveur, vérifiez que vous accédez le fichier .msi via le chemin d’accès réseau, plutôt que le chemin d’accès local.

    ![Sélectionnez le package d’installation à partir du dossier partagé.](./media/active-directory-saas-ie-group-policy/select-package.png)

5. Dans l’invite de **Déploiement du logiciel** , sélectionnez **affecté** pour votre méthode de déploiement. Cliquez ensuite sur **OK**.

    ![Sélectionnez affecté, puis cliquez sur OK.](./media/active-directory-saas-ie-group-policy/deployment-method.png)

L’extension est désormais déployée sur l’unité d’organisation que vous avez sélectionné. [En savoir plus sur l’Installation de logiciels de stratégie de groupe.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

##<a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Étape 4 : Automatique-activer l’Extension pour Internet Explorer 

En plus d’exécuter le programme d’installation, toutes les extensions pour Internet Explorer doivent être activée explicitement avant de pouvoir être utilisée. Suivez les étapes ci-dessous pour activer l’Extension du Panneau de configuration de l’accès à l’aide de la stratégie de groupe :

1. Dans la fenêtre de **l’Éditeur de gestion de stratégie de groupe** , accédez à un des chemins suivants, selon le type de configuration que vous avez choisi dans [étape 3 : affecter le Package d’Installation](#step-3-assign-the-installation-package):
    - `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
    - `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

2. Avec le bouton droit sur la **Liste des modules complémentaires**, puis sélectionnez **Modifier**.
    ![Modifier la liste des modules complémentaires.](./media/active-directory-saas-ie-group-policy/edit-add-on-list.png)

3. Dans la fenêtre de la **Liste des modules complémentaires** , sélectionnez **activé**. Puis, sous la section **Options** , cliquez sur **diaporama...**.

    ![Cliquez sur Activer, puis cliquez sur Afficher...](./media/active-directory-saas-ie-group-policy/edit-add-on-list-window.png)

4. Dans la fenêtre **Afficher le contenu** , procédez comme suit :

    1. Pour la première colonne (le champ **Nom de la valeur** ), copiez et collez le code de classe suivants :`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`

    2. Pour la deuxième colonne (le champ de **valeur** ), tapez la valeur suivante :`1`

    3. Cliquez sur **OK** pour fermer la fenêtre **Afficher le contenu** .

    ![Remplissez les valeurs comme indiqué ci-dessus.](./media/active-directory-saas-ie-group-policy/show-contents.png)

5. Cliquez sur **OK** pour appliquer vos modifications et fermez la fenêtre de la **Liste des modules complémentaires** .

L’extension doit maintenant être activée pour les ordinateurs dans l’unité d’organisation sélectionnée. [En savoir plus sur l’utilisation de la stratégie de groupe pour activer ou désactiver les modules complémentaires Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

##<a name="step-5-optional-disable-remember-password-prompt"></a>Étape 5 (facultatif) : désactiver l’invite de « Mémoriser le mot de passe »

Lorsque les utilisateurs se connecter à des sites Web à l’aide de l’Extension du Panneau de configuration Access, Internet Explorer peut afficher le message suivant demandant « Voulez-vous stocker votre mot de passe ? »

![](./media/active-directory-saas-ie-group-policy/remember-password-prompt.png)

Si vous souhaitez empêcher les utilisateurs de voir cette invite, puis suivez les étapes ci-dessous pour empêcher la saisie semi-automatique ne mémorise pas les mots de passe :

1. Dans la fenêtre de **l’Éditeur de gestion de stratégie de groupe** , accédez au chemin d’accès ci-dessous. Notez que ce paramètre de configuration est uniquement disponible sous **Configuration de l’utilisateur**.
    - `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`

2. Recherchez le paramètre nommé **Activer la fonctionnalité de saisie semi-automatique pour les noms d’utilisateur et les mots de passe sur les formulaires**.

    > [AZURE.NOTE] Les versions précédentes d’Active Directory peuvent répertorier ce paramètre avec le nom de **ne pas autoriser la saisie semi-automatique pour enregistrer les mots de passe**. La configuration de ce paramètre est différent du paramètre décrites dans ce didacticiel.

    ![N’oubliez pas de recherchez cette sous paramètres de l’utilisateur.](./media/active-directory-saas-ie-group-policy/disable-auto-complete.png)

3. Cliquez avec le bouton droit sur le paramètre ci-dessus, puis sélectionnez **Modifier**.

4. Dans la fenêtre intitulée **Activer la fonctionnalité de saisie semi-automatique pour les noms d’utilisateur et les mots de passe sur les formulaires**, sélectionnez **désactivé**.

    ![Sélectionnez désactiver](./media/active-directory-saas-ie-group-policy/disable-passwords.png)

5. Cliquez sur **OK** pour appliquer ces modifications et fermer la fenêtre.

Les utilisateurs pourront n’est plus stocker leurs informations d’identification ou l’utilisation de saisie semi-automatique pour accéder aux informations d’identification stockées précédemment. Cependant, cette stratégie permet aux utilisateurs de continuer à utiliser la saisie semi-automatique pour les autres types de champs de formulaire, telles que les champs de recherche.

> [AZURE.WARNING] Si cette stratégie est activée une fois que les utilisateurs ont choisi de stocker certaines informations d’identification, cette stratégie ne *pas* supprimer les informations d’identification qui ont déjà été enregistrées.

##<a name="step-6-testing-the-deployment"></a>Étape 6 : Tester le déploiement

Suivez les étapes ci-dessous pour vérifier si le déploiement d’une extension a réussi :

1. Si vous avez déployé à l’aide de la **Configuration de l’ordinateur**, connectez-vous à un ordinateur client appartenant à l’unité d’organisation que vous avez sélectionné dans [étape 2 : créer l’objet de stratégie de groupe](#step-2-create-the-group-policy-object). Si vous avez déployé à l’aide de la **Configuration de l’utilisateur**, veillez à se connecter en tant qu’utilisateur appartenant à cette unité d’organisation.

2. Il peut prendre quelques se mettre à jour de la stratégie de groupe devient complètement de ins avec cet ordinateur. Pour forcer la mise à jour, ouvrez une fenêtre **d’invite de commandes** et exécutez la commande suivante :`gpupdate /force`

3. Vous devez redémarrer l’ordinateur pour l’installation se produise. Démarrage peut prendre beaucoup plus de temps installe habituel lors de l’extension.

4. Après avoir redémarré, ouvrez **Internet Explorer**. Dans le coin supérieur droit de la fenêtre, cliquez sur **Outils** (l’icône d’engrenage), puis sur **Gérer les modules complémentaires**.

    ![Accédez à outils > Gérer les modules complémentaires](./media/active-directory-saas-ie-group-policy/manage-add-ons.png)

5. Dans la fenêtre **Gérer les modules complémentaires** , vérifiez que l' **Extension du Panneau de configuration Access** a été installé et que son **statut** a été défini sur **activé**.

    ![Vérifiez que l’Extension du Panneau de configuration Access est installée et activée.](./media/active-directory-saas-ie-group-policy/verify-install.png)

## <a name="related-articles"></a>Articles connexes

- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
- [Accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md)
- [Résolution des problèmes de l’Extension du Panneau de configuration Access pour Internet Explorer](active-directory-saas-ie-troubleshooting.md)
