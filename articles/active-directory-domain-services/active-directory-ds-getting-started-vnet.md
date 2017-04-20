<properties
    pageTitle="Les Services de domaine Active Directory Azure : Créer ou sélectionner un réseau virtuel | Microsoft Azure"
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
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="create-or-select-a-virtual-network-for-azure-ad-domain-services"></a>Créer ou sélectionner un réseau virtuel pour les Services de domaine Active Directory Azure

## <a name="guidelines-to-select-an-azure-virtual-network"></a>Instructions pour sélectionner un réseau virtuel Azure
> [AZURE.NOTE] **Avant de commencer**: faire référence à la [mise en réseau considérations relatives à Azure Active Directory Domain Services](active-directory-ds-networking.md).


## <a name="task-2-create-an-azure-virtual-network"></a>Tâche 2 : Créer un réseau virtuel Azure
La tâche de configuration suivante consiste à créer un réseau virtuel Azure et un sous-réseau qu’il contient. Vous activez Azure Active Directory Domain Services dans ce sous-réseau au sein de votre réseau virtuel. Si vous avez déjà un réseau virtuel que vous préférez utiliser, vous pouvez ignorer cette étape.

> [AZURE.NOTE] Assurez-vous que le réseau virtuel Azure vous créez ou que vous choisissez d’utiliser avec les Services de domaine Active Directory Azure appartient à une zone Azure qui est pris en charge par les Services de domaine Active Directory Azure. Voir la page [services Azure par région](https://azure.microsoft.com/regions/#services/) pour connaître les régions Azure dans laquelle Azure Active Directory Domain Services est disponible.

Notez le nom du réseau virtuel afin de sélectionner le réseau vers la droite virtuel lors de l’activation d’Azure Active Directory Domain Services dans une étape de configuration suivantes.

Effectuer les étapes de configuration suivantes pour créer un réseau virtuel Azure dans lequel vous voulez activer les Services de domaine Active Directory Azure.

1. Accédez au **portail classique Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Sélectionnez le nœud de **réseaux** dans le volet gauche.

    ![Nœud réseaux](./media/active-directory-domain-services-getting-started/networks-node.png)

3. Dans le volet de tâches en bas de la page, cliquez sur **Nouveau** .

    ![Nœud de réseaux virtuels](./media/active-directory-domain-services-getting-started/virtual-networks.png)

4. Dans le nœud **Services de réseau** , sélectionnez **Réseau virtuel**.

5. Cliquez sur **Création rapide** pour créer un réseau virtuel.

    ![Créer réseau virtuel - rapide](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)

6. Spécifiez un **nom** pour votre réseau virtuel. Vous pouvez également choisir configurer **l’espace d’adressage** ou la **valeur de la mémoire virtuelle maximale** pour ce réseau. Vous pouvez laisser le **serveur DNS** défini sur « Aucun » pour l’instant. Vous pouvez mettre à jour le serveur DNS configuration après votre activer Azure Active Directory Domain Services.

7. Veillez à sélectionner une région Azure pris en charge dans la liste déroulante **emplacement** . Voir la page [services Azure par région](https://azure.microsoft.com/regions/#services/) pour connaître les régions Azure dans laquelle Azure Active Directory Domain Services est disponible.

8. Pour créer votre réseau virtuel, cliquez sur le bouton **créer un réseau virtuel** .

    ![Créer un réseau virtuel pour les Services de domaine Active Directory Azure.](./media/active-directory-domain-services-getting-started/create-vnet.png)

9. Une fois le réseau virtuel est créé, sélectionnez le réseau virtuel et cliquez sur l’onglet **configurer** .

    ![Créer un sous-réseau](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)

10. Accédez à la section **espaces d’adressage réseau virtuel** . Cliquez sur **Ajouter un sous-réseau** et spécifie un sous réseau avec le nom **AaddsSubnet**. Cliquez sur **Enregistrer** pour créer le sous-réseau.

    ![Créer un sous-réseau pour les Services de domaine Active Directory Azure.](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)


<br>

## <a name="task-3---enable-azure-ad-domain-services"></a>Tâche 3 - Activer Azure Active Directory Domain Services
La tâche de configuration suivante consiste à [Activer les Services de domaine Active Directory Azure](active-directory-ds-getting-started-enableaadds.md).
