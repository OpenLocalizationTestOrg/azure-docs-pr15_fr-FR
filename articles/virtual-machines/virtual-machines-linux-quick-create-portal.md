<properties
    pageTitle="Créer une VM Linux à l’aide du portail Azure | Microsoft Azure"
    description="Créer une VM Linux à l’aide du portail Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/25/2016"
    ms.author="v-livech"
/>

# <a name="create-a-linux-vm-on-azure-using-the-portal"></a>Créer une VM Linux sur Azure à l’aide du portail


Cet article vous explique comment utiliser le [portail Azure](https://portal.azure.com/) pour créer une Machine virtuelle Linux.

Les conditions sont :

- [un compte Azure](https://azure.microsoft.com/pricing/free-trial/)

- [Fichiers SSH clés privées et](virtual-machines-linux-mac-create-ssh-keys.md)


1. Connecté au portail avec votre identité compte Azure Azure, cliquez sur **+ Nouveau** dans le coin supérieur gauche :

    ![Écran1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

2. Cliquez sur **Machines virtuelles** sur le **Marketplace** puis liste d’images **Ubuntu Server 14.04 LTS** à partir d' **Applications proposées** .  Vérifier dans la partie inférieure que le modèle de déploiement est `Resource Manager` puis cliquez sur **créer**.

    ![écran2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

3. Dans la page **Concepts de base** , entrez :
    - un nom pour la machine virtuelle
    - un nom d’utilisateur pour l’utilisateur d’administration
    - définir le Type d’authentification à **clé publique SSH**
    - votre clé publique SSH sous forme de chaîne (à partir de votre `~/.ssh/` répertoire)
    - une ressource de nom de groupe ou sélectionnez un groupe existant

    Cliquez sur **OK** pour continuer et choisir la taille de la mémoire virtuelle ; Il doit ressembler à ce qui suit :

    ![écran 3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

4. Choisissez la taille **DS1** , qui installe Ubuntu sur un SSD Premium, puis cliquez sur **Sélectionner** pour configurer les paramètres.

    ![screen4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

5. Dans **paramètres**, laissez les valeurs par défaut pour les valeurs de stockage et le réseau et cliquez sur **OK** pour afficher le résumé.  Notez le type de disque a été défini pour Premium SSD en choisissant DS1, le **S** notates SSD.

    ![screen5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

6. Confirmer les paramètres pour votre nouvelle VM Ubuntu, puis cliquez sur **OK**.

    ![screen6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

7. Ouvrez le tableau de bord de portail et dans **interfaces réseau** , sélectionnez votre carte réseau

    ![screen7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

8. Ouvrir le menu d’adresses IP publiques sous les paramètres de carte réseau

    ![screen8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

9. SSH dans l’adresse IP publique à l’aide de votre clé publique SSH

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé un VM Linux rapidement à des fins de test ou une démonstration. Pour créer un Linux VM personnalisé pour votre infrastructure, vous pouvez suivre un des articles suivants.

- [Créer une VM Linux sur Azure à l’aide de modèles](virtual-machines-linux-cli-deploy-templates.md)
- [Créer un SSH sécurisé Linux machine virtuelle sur Azure à l’aide de modèles](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Créer une VM Linux à l’aide de l’infrastructure du langage commun Azure](virtual-machines-linux-create-cli-complete.md)
