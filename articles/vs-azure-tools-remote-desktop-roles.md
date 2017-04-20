<properties 
   pageTitle="À l’aide de bureau à distance avec des rôles Azure | Microsoft Azure"
   description="À l’aide de bureau à distance avec les rôles d’Azure"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="using-remote-desktop-with-azure-roles"></a>À l’aide de bureau à distance avec les rôles d’Azure

En utilisant le Kit de développement logiciel Azure et les Services Bureau à distance, vous pouvez accéder rôles d’Azure et machines virtuelles qui sont hébergés par Azure. Dans Visual Studio, vous pouvez configurer les Services Bureau à distance à partir d’un projet Azure. Pour activer les Services Bureau à distance, vous devez créer un projet de travail qui contient un ou plusieurs rôles et puis le publier dans Azure.

>[AZURE.IMPORTANT] Vous devez accéder à un rôle Azure de résolution des problèmes ou de développement uniquement. L’objectif de chaque machine virtuelle consiste à exécuter un rôle spécifique dans votre application Azure, ne pas d’exécuter d’autres applications clientes. Si vous voulez utiliser Azure pour héberger une machine virtuelle que vous pouvez utiliser à des fins, voir accéder à des Machines virtuelles Azure à partir de l’Explorateur de serveurs.

## <a name="to-enable-and-use-remote-desktop-for-an-azure-role"></a>Pour activer et utiliser le Bureau à distance pour un rôle d’Azure

1. Dans l’Explorateur de solutions, ouvrez le menu contextuel pour votre projet, puis sur **Publier**.

    L’Assistant **Publier Azure Application** s’affiche.

    ![Commande d’un projet de Service Cloud publier](./media/vs-azure-tools-remote-desktop-roles/IC799161.png)

1. Dans la partie inférieure de la page **Paramètres de publication de Microsoft Azure** de l’Assistant, sélectionnez **Activer le Bureau à distance** s’inscrire tous les rôles. 

    La boîte de dialogue **Configuration de bureau à distance** s’affiche.

1. En bas de la boîte de dialogue **Configuration de bureau à distance** , cliquez sur le bouton **Autres Options** . 
 
    Cela permet d’afficher une zone de liste déroulante qui vous permet de créer ou choisir un certificat afin que vous pouvez chiffrer les informations d’identification lorsque vous vous connectez via le Bureau à distance.

1. Dans la liste déroulante, choisissez ** &lt;créer >**, ou choisissez une existante dans la liste. 

    Si vous choisissez un certificat existant, ignorez les étapes suivantes.

    >[AZURE.NOTE] Les certificats dont vous avez besoin d’une connexion Bureau à distance sont différents des certificats que vous utilisez pour d’autres opérations Azure. Le certificat d’accès à distance doit avoir une clé privée.

    La boîte de dialogue **Créer un certificat** s’affiche.

    1. Fournir un nom convivial pour le nouveau certificat, puis cliquez sur le bouton **OK** . Le nouveau certificat s’affiche dans la zone de liste déroulante.

    1. Dans la boîte de dialogue **Configuration de bureau à distance** , attribuez un nom d’utilisateur et un mot de passe.
    
        Vous ne pouvez pas utiliser un compte existant. Ne spécifiez Administrateur comme nom d’utilisateur pour le nouveau compte.

        >[AZURE.NOTE] Si le mot de passe ne répond pas à la complexité, une icône rouge apparaît en regard de la zone de texte mot de passe. Le mot de passe doit inclure des lettres majuscules, minuscules et les nombres ou des symboles.

    1. Choisissez une date dans laquelle le compte expirera et après les connexions de bureau à distance seront bloquées.

    1. Une fois que vous avez fourni toutes les informations requises, cliquez sur le bouton **OK** .
    
        Plusieurs paramètres qui permettent de Services d’accès distant sont ajoutées aux fichiers .cscfg et .csdef.

1. Dans l’Assistant **Paramètres de publication de Microsoft Azure** , cliquez sur le bouton **OK** lorsque vous êtes prêt à publier votre service cloud.

    Si vous n’êtes pas prêt à publier, cliquez sur le bouton **Annuler** . Les paramètres de configuration sont enregistrées, et vous pouvez publier votre service cloud ultérieurement.

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Se connecter à un rôle Azure à l’aide de bureau à distance

Une fois que vous publiez votre service cloud sur Azure, vous pouvez utiliser l’Explorateur de serveurs pour vous connecter aux machines virtuelles qui héberge Azure. 

1. Dans l’Explorateur de serveurs, développez le nœud **Azure** et puis développez le nœud pour un service cloud et un de ses rôles pour afficher une liste des instances.

1. Ouvrir le menu contextuel pour un nœud de l’instance, puis sur **Se connecter à l’aide de Remote Desktop**.

    ![Connexion via le Bureau à distance](./media/vs-azure-tools-remote-desktop-roles/IC799162.png)

1. Entrez le nom d’utilisateur et mot de passe que vous avez créé précédemment. Vous êtes maintenant connecté à votre session distante.


