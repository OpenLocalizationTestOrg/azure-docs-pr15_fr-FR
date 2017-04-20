<properties
    pageTitle="Résolution des problèmes de l’Extension du Panneau de configuration Access pour Internet Explorer | Microsoft Azure"
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

#<a name="troubleshooting-the-access-panel-extension-for-internet-explorer"></a>Résolution des problèmes de l’Extension du Panneau de configuration Access pour Internet Explorer

Cet article vous aideront à résoudre les problèmes suivants :

- Vous ne pouvez pas accéder à vos applications à partir du portail de mes applications lors de l’utilisation d’Internet Explorer.
- Vous voyez le message « Installer le logiciel » même si vous avez déjà installé le logiciel.

Si vous êtes un administrateur, voir aussi : [comment déployer l’Extension du Panneau de configuration de Access pour Internet Explorer à l’aide de la stratégie de groupe](active-directory-saas-ie-group-policy.md)

##<a name="run-the-diagnostic-tool"></a>Exécuter l’outil de Diagnostic

Vous pouvez diagnostiquer les problèmes d’installation avec l’Extension du Panneau de configuration Access en téléchargeant et en exécutant l’outil de diagnostic panneau d’accès :

1. [Cliquez ici pour télécharger l’outil de diagnostic.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)

2. Ouvrez le fichier et appuyez sur le bouton **extraire tous les** .

    ![Appuyez sur Extraire tous les](./media/active-directory-saas-ie-troubleshooting/extract1.png)

3. Appuyez sur le bouton **Extraire** pour continuer.

    ![Extraire Press](./media/active-directory-saas-ie-troubleshooting/extract2.png)

4. Pour exécuter l’outil, cliquez sur le fichier nommé **AccessPanelExtensionDiagnosticTool**, puis sélectionnez **Ouvrir avec > Microsoft Windows Based Script Host**.

    ![Ouvrir avec > Microsoft Windows en fonction d’exécution de scripts](./media/active-directory-saas-ie-troubleshooting/open_tool.png)

5. Vous verrez ensuite la fenêtre de diagnostic suivante, qui décrit ce qui peut être erroné votre installation.

    ![Un échantillon de la fenêtre de diagnostic](./media/active-directory-saas-ie-troubleshooting/tool_preview.png)

6. Cliquez sur «**Oui**» pour permettre le programme à résoudre les problèmes qui ont été trouvés.

7. Pour enregistrer ces modifications, fermez toutes les fenêtres Internet Explorer et rouvrez Internet Explorer.<br />Si vous ne pouvez toujours pas accéder à vos applications, essayez les étapes ci-dessous.

##<a name="check-that-the-access-panel-extension-is-enabled"></a>Vérifiez que l’Extension du Panneau de configuration accès est activée

Pour vérifier que l’Extension du Panneau de configuration accès est activée dans Internet Explorer :

1. Dans Internet Explorer, cliquez sur l' **icône d’engrenage** dans le coin supérieur droit de la fenêtre. Puis sélectionnez **options Internet**.<br />(Dans les versions antérieures d’Internet Explorer vous pouvez trouver cette sous **Outils > options Internet**.

    ![Accédez à outils > Options Internet](./media/active-directory-saas-ie-troubleshooting/internetoptions.png)

2. Cliquez sur l’onglet **programmes** , puis cliquez sur le bouton **Gérer les modules complémentaires** .

    ![Cliquez sur Gérer les modules complémentaires](./media/active-directory-saas-ie-troubleshooting/internetoptions_programs.png)

3. Dans cette boîte de dialogue, sélectionnez **Accès Extension du Panneau de configuration** , puis sur le bouton **Activer** .

    ![Cliquez sur Activer](./media/active-directory-saas-ie-troubleshooting/enableaddon.png)

4. Pour enregistrer ces modifications, fermez toutes les fenêtres Internet Explorer et rouvrez Internet Explorer.

##<a name="enable-extensions-for-inprivate-browsing"></a>Activer les Extensions la navigation InPrivate

Si vous utilisez le mode de navigation InPrivate :

1. Dans Internet Explorer, cliquez sur l' **icône d’engrenage** dans le coin supérieur droit de la fenêtre. Puis sélectionnez **options Internet**.<br />(Dans les versions antérieures d’Internet Explorer vous pouvez trouver cette sous **Outils > options Internet**.

    ![Un échantillon de la fenêtre de diagnostic](./media/active-directory-saas-ie-troubleshooting/inprivateoptions.png)

2. Accédez à l’onglet **confidentialité** , puis et **décochez** la case à cocher **désactiver les barres d’outils et extensions de démarrage de la navigation InPrivate**</p>

    ![Décochez la case extensions et barres d’outils de désactiver au démarrage de la navigation InPrivate](./media/active-directory-saas-ie-troubleshooting/enabletoolbars.png)

3. Pour enregistrer ces modifications, fermez toutes les fenêtres Internet Explorer et rouvrez Internet Explorer.

##<a name="uninstall-the-access-panel-extension"></a>Désinstaller l’Extension du Panneau de configuration Access

Pour désinstaller l’extension du Panneau de configuration de l’accès à partir de votre ordinateur :

1. Sur votre clavier, appuyez sur la **touche Windows** pour ouvrir le menu Démarrer. Lorsque le menu est ouvert, vous pouvez taper quelque chose pour effectuer une recherche. Tapez « Control Panel », puis ouvrez le **Panneau de configuration** lorsqu’il apparaît dans les résultats de recherche.

    ![Recherchez le panneau de configuration](./media/active-directory-saas-ie-troubleshooting/search_sm.png)

2. Dans le coin supérieur droit du Panneau de configuration, modifiez l’option **Afficher par** sur **grandes icônes**. Rechercher, puis cliquez sur le bouton de **programmes et fonctionnalités** .

    ![Chang l’affichage pour afficher les grandes icônes](./media/active-directory-saas-ie-troubleshooting/control_panel.png)

3. Dans la liste, sélectionnez **Access Extension du Panneau de configuration**, puis sur le bouton **désinstaller** .

    ![Cliquez sur Désinstaller](./media/active-directory-saas-ie-troubleshooting/uninstall.png)

4. Vous pouvez puis essayez d’installer l’extension à nouveau pour voir si le problème est résolu.

Si vous rencontrez des problèmes de désinstallation de l’extension, vous pouvez également supprimer à l’aide de l’outil [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) .

## <a name="related-articles"></a>Articles connexes

- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
- [Accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md)
- [Comment déployer l’Extension du Panneau de configuration Access pour Internet Explorer à l’aide de la stratégie de groupe](active-directory-saas-ie-group-policy.md)
