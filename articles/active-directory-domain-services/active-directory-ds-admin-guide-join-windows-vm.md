<properties
    pageTitle="Azure Active Directory Domain Services : Participer à une machine virtuelle de Windows Server pour un domaine géré | Microsoft Azure"
    description="Participer à une machine virtuelle Windows Server aux Services de domaine Active Directory Azure"
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

# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Participer à une machine virtuelle Windows Server pour un domaine géré

> [AZURE.SELECTOR]
- [Azure portal classique - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
- [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)

<br>

Cet article vous explique comment participer à une machine virtuelle exécutant Windows Server 2012 R2 à un domaine géré Azure Active Directory Domain Services, à l’aide du portail classique Azure.


## <a name="step-1-create-the-windows-server-virtual-machine"></a>Étape 1 : Créer la machine virtuelle Windows Server
Suivez les instructions décrites dans le didacticiel [créer une machine virtuelle exécutant Windows dans le portail classique Azure](../virtual-machines/virtual-machines-windows-classic-tutorial.md) . Il est important de s’assurer que ce nouveau machine virtuelle est joint au même réseau virtuel dans lequel vous avez activé Azure Active Directory Domain Services. L’option « Créer rapide » ne vous autorise pas à rejoindre la machine virtuelle à un réseau virtuel. Par conséquent, vous devez utiliser l’option « De la galerie » pour créer la machine virtuelle.

Procédez comme suit pour créer une machine virtuelle Windows connectée au réseau virtuel dans lequel vous l’avez activé Azure Active Directory Domain Services.

1. Dans le portail classique Azure, dans la barre de commande en bas de la fenêtre, cliquez sur **Nouveau**.

2. Sous **Calculer**, cliquez sur **Machine virtuelle**, puis cliquez sur **à partir de la galerie**.

3. Le premier écran vous permet de **Choisir une Image** pour votre machine virtuelle dans la liste des images disponibles. Sélectionnez l’image appropriée.

    ![Choisissez l’image](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. Le deuxième écran vous permet de choisir un nom de l’ordinateur, le taille et le nom d’utilisateur d’administration et le mot de passe. Utiliser la couche et la taille nécessaire à l’exécution de votre application ou la charge de travail. Le nom d’utilisateur que vous avez choisi ici est un utilisateur administrateur local sur l’ordinateur. N’entrez pas les informations d’identification d’un compte utilisateur domaine ici.

    ![Configuration de machine virtuelle](./media/active-directory-domain-services-admin-guide/create-windows-vm-config.png)

5. Le troisième écran vous permet de configurer les ressources pour la mise en réseau, le stockage et disponibilité. Veillez à que sélectionner le réseau virtuel dans lequel vous avez activé Azure Active Directory Domain Services dans la liste déroulante **Région/affinité virtuelle/groupe réseau** . Spécifiez un **Nom de DNS Service Cloud** en fonction de la machine virtuelle.

    ![Sélectionnez réseau virtuel pour la machine virtuelle](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [AZURE.WARNING]
    Vérifiez que vous participez à la machine virtuelle au même réseau virtuel dans lequel vous l’avez activé Azure Active Directory Domain Services. Par conséquent, la machine virtuelle peut « voir » le domaine et effectuer des tâches comme le rejoindre. Si vous choisissez de créer la machine virtuelle dans un autre réseau virtuel, connectez ce réseau virtuel au réseau virtuel dans lequel vous l’avez activé Azure Active Directory Domain Services.

6. Le quatrième écran vous permet d’installer l’Agent machine virtuelle et configurer certains des extensions disponibles.

    ![Terminé](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)

7. Une fois la machine virtuelle est créée, le portail classique répertorie le nouvel ordinateur virtuel sous le nœud **Machines virtuelles** . À la fois la machine virtuelle et un service cloud démarre automatiquement et leur statut est répertorié comme **en cours d’exécution**.

    ![Machine virtuelle est en cours d’exécution](./media/active-directory-domain-services-admin-guide/create-windows-vm-running.png)


## <a name="step-2-connect-to-the-windows-server-virtual-machine-using-the-local-administrator-account"></a>Étape 2 : Se connecter à la machine virtuelle Windows Server à l’aide du compte d’administrateur local
À présent, nous connecter à la machine virtuelle Windows Server nouvellement créée, pour joindre au domaine. Utilisez les informations d’identification d’administrateur local que vous avez spécifié lors de la création de la machine virtuelle, vous y connecter.

Effectuez les opérations suivantes pour vous connecter à la machine virtuelle.

1. Accédez au nœud **Machines virtuelles** dans le portail classique. Sélectionnez la machine virtuelle que vous avez créé à l’étape 1, puis cliquez sur **se connecter** dans la barre de commandes en bas de la fenêtre.

    ![Se connecter à machine virtuelle Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Le portail classique vous invite à ouvrir ou enregistrer un fichier avec une extension « .rdp », qui est utilisée pour vous connecter à la machine virtuelle. Cliquez sur pour ouvrir le fichier lorsque le téléchargement est terminé.

3. À l’invite de connexion, entrez vos **informations d’identification d’administrateur local**, que vous avez spécifié lors de la création de la machine virtuelle. Par exemple, nous avons utilisé 'localhost\mahesh' dans cet exemple.

À ce stade, vous devez être connecté en sur l’ordinateur de virtuel Windows nouvellement créé à l’aide des informations d’identification d’administrateur locales. L’étape suivante consiste à rejoindre la machine virtuelle sur le domaine.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-aad-ds-managed-domain"></a>Étape 3 : Participer à la machine virtuelle Windows Server pour le domaine géré AAD DS
Effectuez les opérations suivantes pour participer à la machine virtuelle Windows Server pour le domaine géré AAD DS.

1. Se connecter à Windows Server comme indiqué à l’étape 2. À partir de l’écran Démarrer, ouvrez le **Gestionnaire de serveur**.

2. Cliquez sur **Serveur Local** dans le volet gauche de la fenêtre Gestionnaire de serveur.

    ![Lancer le Gestionnaire de serveur sur machine virtuelle](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. Sous la section **Propriétés** , cliquez sur **groupe de travail** . Dans la page de propriétés **Propriétés système** , cliquez sur **Modifier** pour joindre le domaine.

    ![Page de propriétés système](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

4. Spécifiez le nom de domaine de vos Services de domaine Active Directory Azure gérer domaine dans la zone de texte **domaine** et cliquez sur **OK**.

    ![Spécifier le domaine à joindre](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

5. Vous êtes invité à entrer vos informations d’identification pour joindre le domaine. Assurez-vous que vous **Spécifiez les informations d’identification d’un utilisateur appartenant aux administrateurs DC AAD** groupe. Seuls les membres de ce groupe de possèdent joindre des ordinateurs pour le domaine géré.

    ![Spécifier des informations d’identification pour la jointure de domaines](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

6. Vous pouvez spécifier des informations d’identification d’une des façons suivantes :

    - Format UPN : spécifiez le suffixe UPN pour le compte d’utilisateur, tel que configuré dans Azure AD. Dans cet exemple, le suffixe UPN de l’utilisateur « bob » est 'bob@domainservicespreview.onmicrosoft.com'.

    - Format SAMAccountName : vous pouvez spécifier le nom du compte dans le format SAMAccountName. Dans cet exemple, l’utilisateur 'bob' devront entrer « CONTOSO100\bob ».

        > [AZURE.NOTE] **Nous vous recommandons d’utiliser au format UPN pour spécifier les informations d’identification.** SAMAccountName peut être généré automatiquement si préfixe UPN d’un utilisateur est trop long (par exemple, « joereallylongnameuser »). Si plusieurs utilisateurs ont le même préfixe UPN (par exemple, « bob ») de votre client Azure AD, leur format SAMAccountName peut être généré automatiquement par le service. Dans ce cas, au format UPN peut servir parviennent à se connecter au domaine.

7. Après que jointure domaine réussit, vous voyez le message suivant pour vous accueillir dans le domaine. Redémarrez l’ordinateur virtuel pour l’opération de jointure domaine se termine.

    ![Bienvenue dans le domaine](./media/active-directory-domain-services-admin-guide/join-domain-done.png)


## <a name="troubleshooting-domain-join"></a>Résolution des problèmes de jonction de domaine
### <a name="connectivity-issues"></a>Problèmes de connectivité
Si la machine virtuelle ne peut pas trouver le domaine, reportez-vous à la procédure suivante :

- Vérifiez que la machine virtuelle est connectée au réseau virtuel même que celle que vous avez activé dans les Services de domaine. Si ce n’est pas le cas, la machine virtuelle ne peut pas se connecter au domaine et par conséquent ne parvient pas à rejoindre le domaine.

- Si la machine virtuelle est connectée à un autre réseau virtuel, assurez-vous que ce réseau virtuel est connecté au réseau virtuel dans lequel vous avez activé les Services de domaine.

- Essayez de taper le domaine en utilisant le nom de domaine du domaine géré (par exemple, « ping contoso100.com »). Si vous ne parvenez pas à le faire, essayez de taper les adresses IP pour le domaine affiché dans la page où vous avez activé Azure Active Directory Domain Services (par exemple, « ping 10.0.0.4 »). Si vous ne pouvez exécuter la commande ping l’adresse IP, mais pas sur le domaine, DNS peut être configuré correctement. Vous n’avez ne peut-être pas configuré les adresses IP du domaine comme serveurs DNS pour le réseau virtuel.

- Essayez de vider le cache de résolution DNS sur l’ordinateur virtuel (ipconfig /flushdns).

Si vous accédez à la boîte de dialogue qui vous demande des informations d’identification joindre le domaine, vous n’avez pas de problèmes de connectivité.


### <a name="credentials-related-issues"></a>Problèmes liés aux informations d’identification
Si vous rencontrez des difficultés avec les informations d’identification et que vous ne parvenez pas à joindre le domaine, reportez-vous aux étapes suivantes.

- Essayez d’utiliser le format UPN pour spécifier les informations d’identification. SAMAccountName pour votre compte peut être généré s’il existe plusieurs utilisateurs avec le même préfixe UPN dans votre client ou si votre préfixe UPN est trop long. Par conséquent, le format SAMAccountName pour votre compte peut être différent de ce que vous attendez ou utilisez dans votre domaine local.

- Essayez d’utiliser les informations d’identification d’un compte d’utilisateur appartenant au groupe « AAD DC administrateurs » pour joindre des ordinateurs pour le domaine géré.

- Assurez-vous d’avoir [activé la synchronisation de mot de passe](active-directory-ds-getting-started-password-sync.md) selon les étapes décrites dans le guide de mise en route.

- Vérifiez que vous utilisez le nom UPN de l’utilisateur comme configuré dans Azure AD (par exemple, 'bob@domainservicespreview.onmicrosoft.com') pour vous connecter.

- Vérifiez que vous avez attendu suffisamment de temps pour la synchronisation de mot de passe terminer comme indiqué dans le guide de mise en route.


## <a name="related-content"></a>Contenu associé

- [Services de domaine Azure AD - guide de mise en route](./active-directory-ds-getting-started.md)

- [Administrer un domaine géré Azure Active Directory Domain Services](./active-directory-ds-admin-guide-administer-domain.md)
