<properties
    pageTitle="Azure Active Directory Domain Services : Administrer le DNS sur domaines gérés | Microsoft Azure"
    description="Gérer DNS sur domaines gérés Azure Active Directory Domain Services"
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
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>Gérer DNS sur un domaine géré Azure Active Directory Domain Services
Azure Active Directory Domain Services comprend un serveur DNS (résolution du nom de domaine) qui fournit la résolution DNS pour le domaine géré. Parfois, vous devrez peut-être configurer le système DNS sur le domaine géré. Vous devrez peut-être créer des enregistrements DNS pour les ordinateurs qui ne sont pas joints au domaine, la configuration des adresses IP virtuelles pour les programmes d’équilibrage de charge ou configuration redirecteurs DNS externe. Pour cette raison, les utilisateurs qui appartiennent au groupe « AAD DC administrateurs » sont accordées des privilèges d’administration DNS sur le domaine géré.


## <a name="before-you-begin"></a>Avant de commencer
Pour effectuer les tâches mentionnées dans cet article, vous devez :

1. Un **abonnement Azure**valide.

2. Un **répertoire Azure AD** - soit synchronisé avec un répertoire local ou un répertoire exclusivement le nuage.

3. **Services de domaine Active Directory Azure** doit être activé pour le répertoire Azure AD. Si vous n’avez pas fait, suivez toutes les tâches décrites dans le [guide de mise en route](./active-directory-ds-getting-started.md).

4. Un **ordinateur virtuel lié au domaine** à partir duquel vous gérez le domaine géré Azure Active Directory Domain Services. Si vous n’avez pas une telle machine virtuelle, suivez toutes les tâches décrites dans l’article intitulé [participer à une machine virtuelle Windows à un domaine géré](./active-directory-ds-admin-guide-join-windows-vm.md).

5. Vous devez les informations d’identification d’un **compte d’utilisateur appartenant au groupe « AAD DC administrateurs »** dans votre annuaire, d’administrer le DNS pour votre domaine géré.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>Tâche 1 : mise en service une machine virtuelle à un domaine pour administrer à distance DNS pour le domaine géré
Azure domaines gérés les Services de domaine Active Directory peuvent être gérés à distance à l’aide des outils d’administration Active Directory familiers tel que Active Directory Administration Centre (ADAC) ou AD PowerShell. De même, DNS pour le domaine géré gérables à distance en utilisant les outils d’administration serveur DNS.

Administrateurs dans votre annuaire Azure AD n’ont pas de privilèges pour vous connecter risquez sur le domaine géré via le Bureau à distance. Membres du groupe « Administrateurs de DC AAD » peuvent administrer le DNS pour les domaines gérés à distance à l’aide des outils de serveur DNS à partir d’un ordinateur client Windows Server est joint au domaine géré. Outils de serveur DNS peuvent être installés dans le cadre de la fonctionnalité facultative Server Outils (administration distant) sur Windows Server et des ordinateurs clients rejoint le domaine géré.

La première tâche consiste à fournir une machine virtuelle Windows Server qui relie le domaine géré. Pour obtenir des instructions, reportez-vous à l’article intitulé [participer à une machine virtuelle Windows Server pour un domaine de Services de domaine Active Directory Azure gérées](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>Tâche 2 : outils installer DNS Server sur la machine virtuelle
Procédez comme suit pour installer les outils d’Administration DNS sur l’ordinateur virtuel joint au domaine. Pour plus d’informations sur [l’installation et l’utilisation des outils d’Administration de serveur distant](https://technet.microsoft.com/library/hh831501.aspx), voir Technet.

1. Accédez au nœud **Machines virtuelles** dans le portail classique Azure. Sélectionnez la machine virtuelle que vous avez créé dans la tâche 1 et cliquez sur **se connecter** dans la barre de commandes en bas de la fenêtre.

    ![Se connecter à machine virtuelle Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Le portail classique vous invite à ouvrir ou enregistrer un fichier avec une extension « .rdp », qui est utilisée pour vous connecter à la machine virtuelle. Lorsque le téléchargement est terminé, cliquez sur le fichier.

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

9. Dans la page **fonctionnalités** , cliquez pour développer le nœud **Outils d’Administration de serveur distant** , puis développez le nœud **Outils d’Administration de rôle** . Sélectionnez fonctionnalité **Outils du serveur DNS** dans la liste des outils d’administration de rôle.

    ![Page fonctionnalités](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

10. Dans la page de **Confirmation** , cliquez sur **installer** pour installer la fonctionnalité Outils serveur DNS sur l’ordinateur virtuel. Une fois l’installation de fonctionnalité terminée, cliquez sur **Fermer** pour quitter l’Assistant **Ajouter des rôles et fonctionnalités** .

    ![Page de confirmation](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)


## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>Tâche 3 - lancer la console de gestion DNS pour administrer DNS
À présent que la fonctionnalité outils du serveur DNS est installée sur le domaine rejoint machine virtuelle, nous pouvons utiliser les outils de DNS pour administrer le DNS sur le domaine géré.

> [AZURE.NOTE] Vous devez être membre du groupe « Administrateurs de DC AAD », d’administrer le DNS sur le domaine géré.

1. À partir de l’écran d’accueil, cliquez sur **Outils d’administration**. Vous devriez voir la console **DNS** est installée sur l’ordinateur virtuel.

    ![Outils d’administration - Console DNS](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)

2. Cliquez sur **DNS** pour lancer la console de gestion DNS.

3. Dans la boîte de dialogue **connexion au serveur DNS** , cliquez sur l’option intitulée de **l’ordinateur suivant**et entrez le nom de domaine DNS du domaine géré (par exemple, « contoso100.com »).

    ![Console DNS - se connecter au domaine](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

4. La Console DNS se connecte au domaine géré.

    ![Console DNS - administrer domaine](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

5. Vous pouvez désormais utiliser la console DNS pour ajouter des entrées DNS pour les ordinateurs au sein du réseau virtuel dans lequel vous avez activé les Services de domaine AAD.

> [AZURE.WARNING] Soyez prudent lors de l’administration DNS pour le domaine géré à l’aide des outils d’administration DNS. Vous assurer que vous **ne pas supprimer ou modifier les enregistrements DNS intégrés qui sont utilisés par les Services de domaine dans le domaine**. Enregistrements DNS intégrés incluent des enregistrements DNS du domaine, les enregistrements de serveur et les autres enregistrements utilisés pour DC emplacement. Si vous modifiez ces enregistrements, les services de domaine sont interrompues sur le réseau virtuel.


Consultez l' [article d’outils DNS sur Technet](https://technet.microsoft.com/library/cc753579.aspx) pour plus d’informations sur la gestion DNS.


## <a name="related-content"></a>Contenu associé

- [Services de domaine Azure AD - guide de mise en route](./active-directory-ds-getting-started.md)

- [Participer à une machine virtuelle Windows Server pour un domaine géré Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)

- [Administrer un domaine géré Azure Active Directory Domain Services](active-directory-ds-admin-guide-administer-domain.md)

- [Outils d’administration DNS](https://technet.microsoft.com/library/cc753579.aspx)
