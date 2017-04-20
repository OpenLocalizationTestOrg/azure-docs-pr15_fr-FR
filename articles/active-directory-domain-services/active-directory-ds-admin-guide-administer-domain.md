<properties
    pageTitle="Azure Active Directory Domain Services : Administrer un domaine géré | Microsoft Azure"
    description="Gérer les domaines gérés Azure Active Directory Domain Services"
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
    ms.topic="article"
    ms.date="10/02/2016"
    ms.author="maheshu"/>

# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>Administrer un domaine géré Azure Active Directory Domain Services
Cet article vous explique comment administrer un domaine géré Azure Active Directory (AD) les Services de domaine.


## <a name="before-you-begin"></a>Avant de commencer
Pour effectuer les tâches mentionnées dans cet article, vous devez :

1. Un **abonnement Azure**valide.

2. Un **répertoire Azure AD** - soit synchronisé avec un répertoire local ou un répertoire exclusivement le nuage.

3. **Services de domaine Active Directory Azure** doit être activé pour le répertoire Azure AD. Si vous n’avez pas fait, suivez toutes les tâches décrites dans le [guide de mise en route](./active-directory-ds-getting-started.md).

4. Un **ordinateur virtuel lié au domaine** à partir duquel vous gérez le domaine géré Azure Active Directory Domain Services. Si vous n’avez pas une telle machine virtuelle, suivez toutes les tâches décrites dans l’article intitulé [participer à une machine virtuelle Windows à un domaine géré](./active-directory-ds-admin-guide-join-windows-vm.md).

5. Vous devez les informations d’identification d’un **compte d’utilisateur appartenant au groupe « AAD DC administrateurs »** dans votre annuaire, pour administrer votre domaine géré.

<br>


## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Tâches d’administration, que vous pouvez effectuer sur un domaine géré
Les membres du groupe « Administrateurs de DC AAD » sont des privilèges sur le domaine géré qui leur permettre d’effectuer des tâches telles que :

- Joindre des ordinateurs pour le domaine géré.

- Configurer la stratégie de groupe intégrée pour les conteneurs « AADDC utilisateurs » et « AADDC ordinateurs » dans le domaine géré.

- Gérer DNS sur le domaine géré.

- Créer et gérer les unités organisationnelles personnalisé (ou) sur le domaine géré.

- Administration d’accéder à des ordinateurs rejoint le domaine géré.


## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>Privilèges d’administrateur, que vous ne disposez pas sur un domaine géré
Le domaine est géré par Microsoft, y compris les activités telles que les correctifs, surveillance et effectuer des sauvegardes. Par conséquent, le domaine est verrouillé et vous ne disposez pas de privilèges à effectuer certaines tâches administratives sur le domaine. Sont des exemples de tâches que vous ne pouvez pas effectuer en dessous.

- Vous disposez pas des privilèges administrateur de domaine ou administrateur d’entreprise pour le domaine géré.

- Vous ne pouvez pas étendre le schéma du domaine géré.

- Vous ne pouvez pas vous connecter à contrôleur de domaine pour le domaine géré à l’aide de bureau à distance.

- Vous ne pouvez pas ajouter de domaine pour le domaine géré.


## <a name="task-1---provision-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>Tâche 1 : mise en service un ordinateur à un domaine de virtuel Windows Server pour administrer à distance le domaine géré
Azure domaines gérés les Services de domaine Active Directory peuvent être gérés à l’aide des outils d’administration Active Directory familiers tel que Active Directory Administration Centre (ADAC) ou AD PowerShell. Administrateurs client n’ont pas de privilèges pour vous connecter risquez sur le domaine géré via le Bureau à distance. Par conséquent, les membres du groupe « Administrateurs de DC AAD » peuvent administrer domaines gérés à distance en utilisant les outils d’administration Active Directory à partir d’un ordinateur client Windows Server est joint au domaine géré. Outils d’administration AD peuvent être installés dans le cadre de la fonctionnalité facultative Server Outils (administration distant) sur Windows Server et des ordinateurs clients rejoint le domaine géré.

La première étape consiste à configurer une machine virtuelle Windows Server qui relie le domaine géré. Pour obtenir des instructions, reportez-vous à l’article intitulé [participer à une machine virtuelle Windows Server pour un domaine de Services de domaine Active Directory Azure gérées](active-directory-ds-admin-guide-join-windows-vm.md).

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>Administrer le domaine géré depuis un ordinateur client (par exemple, Windows 10)
Les instructions de cet article utilisent une machine virtuelle Windows Server pour administrer le DS DAS gèrent domaine. Toutefois, vous pouvez également choisir d’utiliser une machine virtuelle du client (par exemple, Windows 10) Windows pour le faire.

Vous pouvez [installer Server Outils (administration distant)](http://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) sur un ordinateur Windows client virtuel en suivant les instructions sur TechNet.


## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>Tâche 2 : outils d’administration installer Active Directory sur l’ordinateur virtuel
Procédez comme suit pour installer les outils d’Administration Active Directory sur l’ordinateur virtuel joint au domaine. Pour plus d' [informations sur l’installation et l’utilisation des outils d’Administration de serveur distant](https://technet.microsoft.com/library/hh831501.aspx), voir Technet.

1. Accédez au nœud **Machines virtuelles** dans le portail classique Azure. Sélectionnez la machine virtuelle que vous avez créé dans la tâche 1 et cliquez sur **se connecter** dans la barre de commandes en bas de la fenêtre.

    ![Se connecter à machine virtuelle Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Le portail classique vous invite à ouvrir ou enregistrer un fichier avec une extension « .rdp », qui est utilisée pour vous connecter à la machine virtuelle. Cliquez sur pour ouvrir le fichier lorsque le téléchargement est terminé.

3. À l’invite de connexion, utilisez les informations d’identification d’un utilisateur appartenant au groupe « AAD DC administrateurs ». Par exemple, nous utilisons 'bob@domainservicespreview.onmicrosoft.com' dans notre cas.

4. À partir de l’écran Démarrer, ouvrez le **Gestionnaire de serveur**. Cliquez sur **Ajouter des rôles et fonctionnalités** dans le volet central de la fenêtre Gestionnaire de serveur.

    ![Lancer le Gestionnaire de serveur sur machine virtuelle](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)

5. Dans la page **Avant de commencer** de l' **Ajout de rôles et les fonctionnalités de l’Assistant**, cliquez sur **suivant**.

    ![Avant de commencer la page](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)

6. Dans la page **Type d’Installation** , assurez-vous que l’option **d’installation basée sur les rôles ou basée sur la fonctionnalité** activée, puis cliquez sur **suivant**.

    ![Page Type d’installation](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)

7. Dans la page **Choix du serveur** , sélectionnez la machine virtuelle courante dans le pool du serveur, puis cliquez sur **suivant**.

    ![Page de sélection de serveur](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)

8. Dans la page **Rôles de serveur** , cliquez sur **suivant**. Nous ignorer cette page étant donné que nous ne sommes pas installer tous les rôles sur le serveur.

9. Dans la page **fonctionnalités** , cliquez pour développer le nœud **Outils d’Administration de serveur distant** , puis développez le nœud **Outils d’Administration de rôle** . Sélectionnez fonctionnalité **AD DS et outils ADAM** dans la liste des outils d’administration de rôle.

    ![Page fonctionnalités](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)

10. Dans la page de **Confirmation** , cliquez sur **installer** pour installer l’annonce et fonctionnalité d’outils ADAM sur l’ordinateur virtuel. Une fois l’installation de fonctionnalité terminée, cliquez sur **Fermer** pour quitter l’Assistant **Ajouter des rôles et fonctionnalités** .

    ![Page de confirmation](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)


## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>Tâche 3 : se connecter à et Explorer le domaine géré
À présent que les outils d’administration AD sont installés sur le domaine rejoint machine virtuelle, nous pouvons utiliser ces outils pour Explorer et administrer le domaine géré.

> [AZURE.NOTE] Vous devez être membre du groupe « Administrateurs de DC AAD », pour administrer le domaine géré.

1. À partir de l’écran d’accueil, cliquez sur **Outils d’administration**. Vous devriez voir les outils d’administration AD installés sur l’ordinateur virtuel.

    ![Outils d’administration installées sur serveur](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. Cliquez sur **Centre d’administration Active Directory**.

    ![Centre d’administration d’Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. Pour découvrir le domaine, cliquez sur le nom de domaine dans le volet gauche (par exemple, « contoso100.com »). Remarquez les deux conteneurs appelés « AADDC utilisateurs » et « AADDC ordinateurs » respectivement.

    ![ADAC - domaine d’affichage](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)

4. Cliquez sur le conteneur appelé **AADDC utilisateurs** pour afficher tous les utilisateurs et groupes qui appartiennent au domaine géré. Vous devez voir comptes d’utilisateurs et groupes à partir de votre annonce Azure client afficher la dans ce conteneur. Notez que dans cet exemple, un compte d’utilisateur pour l’utilisateur appelé « bob » et un groupe appelé « AAD DC administrateurs » sont disponibles dans ce conteneur.

    ![ADAC - utilisateurs du domaine](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)

5. Cliquez sur le conteneur appelé **Ordinateurs AADDC** pour afficher les ordinateurs joints à ce domaine géré. Vous devriez voir une entrée pour la machine virtuelle en cours, qui est joint au domaine. Comptes d’ordinateur pour tous les ordinateurs qui sont jointes au domaine géré Azure Active Directory Domain Services sont stockées dans ce conteneur « AADDC ordinateurs ».

    ![ADAC - domaine ordinateurs](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>Contenu associé

- [Services de domaine Azure AD - guide de mise en route](./active-directory-ds-getting-started.md)

- [Participer à une machine virtuelle Windows Server pour un domaine géré Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)

- [Déployer les outils d’Administration de serveur distant](https://technet.microsoft.com/library/hh831501.aspx)
