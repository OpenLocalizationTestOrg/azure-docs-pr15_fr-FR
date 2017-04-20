<properties
    pageTitle="Azure Active Directory Domain Services : Participer à une VM RHEL à un domaine géré | Microsoft Azure"
    description="Participer à une machine virtuelle Red chapeau Enterprise Linux aux Services de domaine Active Directory Azure"
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

# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>Participer à une machine virtuelle rouge chapeau Enterprise Linux 7 à un domaine géré
Cet article vous explique comment participer à une machine virtuelle Red chapeau Enterprise Linux (RHEL) 7 à un domaine géré Azure Active Directory Domain Services.

## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>Configurer une machine virtuelle Red chapeau Enterprise Linux
Effectuez les opérations suivantes pour configurer une machine virtuelle RHEL 7 à l’aide du portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

    ![Tableau de bord du portail Azure](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-dashboard.png)

2. Cliquez sur **Nouveau** dans le volet gauche et tapez **Chapeau rouge** dans la barre de recherche comme illustré dans l’écran suivant. Entrées rouge chapeau Enterprise Linux s’affichent dans les résultats de recherche. Cliquez sur **chapeau rouge Enterprise Linux 7.2**.

    ![Sélectionnez RHEL dans les résultats](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-find-rhel-image.png)

3. Les résultats de recherche dans le volet **tout** doivent répertorier l’image Red chapeau Enterprise Linux 7.2. Cliquez sur **rouge chapeau Enterprise Linux 7.2** pour afficher plus d’informations sur l’image machines virtuelles.

    ![Sélectionnez RHEL dans les résultats](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-rhel-image.png)

4. Dans le volet **Red chapeau Enterprise Linux 7.2** , vous devez voir plus d’informations sur l’image machines virtuelles. Dans la liste déroulante **Sélectionner un modèle de déploiement** , sélectionnez **classique**. Cliquez ensuite sur le bouton **créer** .

    ![Afficher les détails d’image](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-clicked.png)

5. Dans le volet **Créer machine virtuelle** , entrez le **Nom d’hôte** pour le nouvel ordinateur virtuel. Aussi spécifier un nom d’utilisateur administrateur local dans le champ **nom d’utilisateur** et un **mot de passe**. Vous pouvez également choisir d’utiliser un code SSH pour authentifier l’utilisateur administrateur local. Sélectionnez également un **Niveau de tarification** pour la machine virtuelle.

    ![Créer la machine virtuelle - détails de base](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-basic-details.png)

6. Cliquez sur **Configuration facultative**. Dans le volet **config facultatif** , cliquez sur **réseau**.

    ![Créer la machine virtuelle : configurer réseau virtuel](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-configure-vnet.png)

7. Cela affiche un volet intitulé **réseau**. Dans le volet **réseau** , cliquez sur **Réseau virtuel** pour sélectionner le réseau virtuel auquel le Linux VM doivent être déployée. Cette action ouvre le volet **Réseau virtuel** . Dans le volet **Réseau virtuel** , choisissez l’option **utiliser un réseau virtuel** . Sélectionnez ensuite le réseau virtuel dans lequel les Services de domaine Active Directory Azure est disponible. Dans cet exemple, nous choisir le réseau virtuel « MyPreviewVNet ».

    ![Créer machine virtuelle - sélectionnez réseau virtuel](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-select-vnet.png)

8. Dans le volet **config facultatif** , cliquez sur le bouton **OK** .

    ![Créer la machine virtuelle - réseau virtuel sélectionné](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-vnet-selected.png)

9. Vous êtes maintenant prêt à créer la machine virtuelle. Dans le volet **Créer machine virtuelle** , cliquez sur le bouton **créer** .

    ![Créer la machine virtuelle - prêt](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm.png)

10. Déploiement de la nouvelle machine virtuelle en fonction de l’image RHEL 7.2 doit commencer.

  ![Créer la machine virtuelle - mise en route de déploiement](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployment-started.png)

11. Après quelques minutes, la machine virtuelle doivent être déployée correctement et prête à être à utiliser.

  ![Créer la machine virtuelle - déployé](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployed.png)



## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Se connecter à distance à la machine virtuelle Linux nouvellement générée
La machine virtuelle RHEL 7.2 a été configurée dans Azure. La tâche suivante consiste à se connecter à distance à la machine virtuelle.

**Se connecter à la machine virtuelle RHEL 7.2** Suivez les instructions de l’article [comment se connecter à une machine virtuelle Linux en cours d’exécution](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md).

Le reste des étapes part du principe que vous utilisez le client PuTTY SSH pour se connecter à la machine virtuelle RHEL. Pour plus d’informations, consultez la [page de téléchargement PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Ouvrez le programme mastic.

2. Entrez le **Nom d’hôte** de la machine virtuelle RHEL nouvellement créée. Dans cet exemple, notre machine virtuelle contient le nom d’hôte « contoso-rhel.cloudapp .net ». Si vous n’êtes pas sûr du nom d’hôte de votre machine virtuelle, reportez-vous au tableau de bord machine virtuelle sur le portail Azure.

    ![Se connecter puTTY](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-connect.png)

3. Ouvrez une session sur l’ordinateur virtuel avec les informations d’identification d’administrateur local que vous avez spécifié lors de la création de la machine virtuelle. Dans cet exemple, nous avons utilisé le compte d’administrateur local « mahesh ».

    ![Connexion mastic](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-login.png)


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Installer des packages requis sur la machine virtuelle Linux
Après vous être connecté à la machine virtuelle, l’étape suivante consiste à installer packages requis pour la jointure de domaines sur l’ordinateur virtuel. Effectuez les opérations suivantes :

1. **Installer realmd :** Le package realmd est utilisé pour la jointure de domaines. Dans votre terminal mastic, tapez la commande suivante :

    sudo yum installer realmd

    ![Installer realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-realmd.png)

    Après quelques minutes, le package realmd doit obtenir installé sur l’ordinateur virtuel.

    ![realmd installé](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-installed.png)

3. **Installer sssd :** Le package realmd dépend de sssd pour effectuer les opérations de jointure de domaine. Dans votre terminal mastic, tapez la commande suivante :

    sudo yum installer sssd

    ![Installer sssd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-sssd.png)

    Après quelques minutes, le package sssd doit obtenir installé sur l’ordinateur virtuel.

    ![realmd installé](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-sssd-installed.png)

4. **Installer kerberos :** Dans votre terminal mastic, tapez la commande suivante :

    sudo yum installer krb5-poste de travail krb5-et les bibliothèques

    ![Installer kerberos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-kerberos.png)

    Après quelques minutes, le package realmd doit obtenir installé sur l’ordinateur virtuel.

    ![Kerberos installé](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kerberos-installed.png)


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Participer à la machine virtuelle Linux pour le domaine géré
Maintenant que les packages requis sont installés sur la machine virtuelle Linux, l’étape suivante consiste à participer à la machine virtuelle pour le domaine géré.

1. Découvrir le domaine géré DAS les Services de domaine. Dans votre terminal mastic, tapez la commande suivante :

    domaine sudo découvrir CONTOSO100.COM

    ![Découverte de domaine](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-discover.png)

    Si vous **Découvrez de domaine** ne peut pas trouver votre domaine géré, vous assurer que le domaine est accessible à partir de la machine virtuelle (try ping). Assurez-vous également que la machine virtuelle a bien été déployée sur le même réseau virtuel dans laquelle le domaine géré est disponible.

2. Initialisation kerberos. Dans votre terminal mastic, tapez la commande suivante. Assurez-vous que vous spécifiez un utilisateur qui fait partie d’un « AAD DC administrateurs ». Uniquement ces utilisateurs peuvent rejoindre des ordinateurs pour le domaine géré.

    kinitbob@CONTOSO100.COM

    ![Kinit](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kinit.png)

    Assurez-vous que vous spécifiez le nom de domaine en majuscules, kinit autre échoue.

3. Participer à l’ordinateur sur le domaine. Dans votre terminal mastic, tapez la commande suivante. Spécifiez le même utilisateur que vous avez indiquée à l’étape précédente ('kinit »).

    jointure de domaine sudo--CONTOSO100.COM -U détaillée'bob@CONTOSO100.COM'

    ![Jointure de domaine](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-join.png)

Vous devez obtenir un message (« est correctement inscrit machine dans domaine ») quand l’ordinateur est joint correctement pour le domaine géré.


## <a name="verify-domain-join"></a>Vérifier la jointure de domaines
Vous pouvez rapidement vérifier si l’ordinateur a été joint correctement pour le domaine géré. Se connecter à la nouvellement domaine rejoint RHEL VM à l’aide de SSH et un compte d’utilisateur de domaine et puis sélectionnez pour voir si le compte d’utilisateur est résolu correctement.

1. Dans votre terminal mastic, tapez la commande suivante pour vous connecter à la nouvellement domaine rejoint machine virtuelle RHEL à l’aide de SSH. Utilisez un compte de domaine auquel appartient le domaine géré (par exemple, 'bob@CONTOSO100.COM' dans ce cas.)

    SSH -l bob@CONTOSO100.COM rhel.cloudapp.net de contoso

2. Dans votre terminal mastic, tapez la commande suivante pour voir si le répertoire de base a été correctement initialisé.

    mot de passe

3. Dans votre terminal mastic, tapez la commande suivante pour voir si l’appartenance aux groupes est résolus correctement.

    ID

Exemple de sortie de ces commandes suit :

![Vérifier la jointure de domaines](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-verify-domain-join.png)


## <a name="troubleshooting-domain-join"></a>Résolution des problèmes de jonction de domaine
Consultez l’article de [résolution des problèmes la jointure de domaines](active-directory-ds-admin-guide-join-windows-vm.md#troubleshooting-domain-join) .


## <a name="related-content"></a>Contenu associé
- [Services de domaine Azure AD - guide de mise en route](./active-directory-ds-getting-started.md)

- [Participer à une machine virtuelle Windows Server pour un domaine géré Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)

- [Comment se connecter à une machine virtuelle Linux en cours d’exécution](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md).

- [L’installation de Kerberos](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)

- [Chapeau rouge Enterprise Linux 7 - Guide d’intégration Windows](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
