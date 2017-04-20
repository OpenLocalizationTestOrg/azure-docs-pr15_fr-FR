<properties
    pageTitle="Capturer une image d’un Linux VM | Microsoft Azure"
    description="Découvrez comment faire pour capturer une image d’une basé sur Linux Azure machine virtuelle () créée avec le modèle de déploiement classique."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="iainfou"/>


# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>Comment faire pour capturer une machine virtuelle Linux classique en tant qu’image

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de gestionnaire de ressources](virtual-machines-linux-capture-image.md).

Cet article vous explique comment faire pour capturer une machine virtuelle Azure classique en cours d’exécution Linux en tant qu’image pour créer d’autres machines virtuelles. Cette image comprend le disque du système d’exploitation et des disques de données de la machine virtuelle. Il n’inclut pas la configuration réseau, vous devez configurer que lorsque vous créez les autres machines virtuelles à partir de l’image.

Azure stocke l’image sous **Images**, ainsi que toutes les images que vous avez téléchargée. Pour plus d’informations sur les images, voir [Sur les Images des machines virtuelles dans Azure] [].

## <a name="before-you-begin"></a>Avant de commencer

Cette procédure suppose que vous avez déjà créé un ordinateur virtuel Azure en utilisant le modèle de déploiement classique et configuré le système d’exploitation, notamment en joignant les disques de données. Si vous avez besoin créer une machine virtuelle, Découvrez [comment créer une Machine virtuelle Linux] [].


## <a name="capture-the-virtual-machine"></a>Capture de la machine virtuelle

1. [Se connecter à la machine virtuelle](virtual-machines-linux-mac-create-ssh-keys.md) à l’aide d’un clientSSH de votre choix.

2. Dans la fenêtre SSH, tapez la commande suivante. Le résultat de `waagent` peut varier légèrement en fonction de la version de cet utilitaire :

    `sudo waagent -deprovision+user`

    Cette commande tente de nettoyer le système et le rendre approprié pour le réapprovisionnement. Cette opération effectue les tâches suivantes :

    - Supprime les clés d’hôte SSH (si Provisioning.RegenerateSshHostKeyPair est « y » dans le fichier de configuration)
    - Efface configuration du serveur de noms dans /etc/resolv.conf
    - Supprime la `root` mot de passe utilisateur de/etc/ombre (si Provisioning.DeleteRootPassword est « y » dans le fichier de configuration)
    - Supprime mis en cache DHCP client location
    - Nom d’hôte réinitialisations localhost.localdomain
    - Supprime le dernier utilisateur généré compte (obtenu à partir de /var/lib/waagent) **et les données associées**.

    >[AZURE.NOTE] Annulation supprime les fichiers et les données à « generalize » de l’image. Exécuter uniquement cette commande sur une machine virtuelle que vous souhaitez capturer en tant que nouveau modèle image. Il ne garantit pas que l’image est désactivée de toutes les informations sensibles ou est approprié pour le redistribuer à des tiers.


3. Tapez **y** pour continuer. Vous pouvez ajouter la `-force` paramètre afin d’éviter cette étape de confirmation.

4. Tapez **Quitter** pour fermer le client SSH.

    >[AZURE.NOTE] Les étapes restantes part du principe que vous avez déjà [installé l’infrastructure du langage commun Azure](../xplat-cli-install.md) sur votre ordinateur client. Toutes les étapes suivantes sont également possible dans le [portail classique Azure] [].

5. Depuis votre ordinateur client, ouvrez Azure infrastructure du langage commun et connectez-vous à votre abonnement Azure. Pour plus d’informations, consultez [se connecter à un abonnement Azure depuis l’interface Azure](../xplat-cli-connect.md).

6. Vérifiez que vous êtes en mode de gestion des services :

    `azure config mode asm`

7. Arrêtez la machine virtuelle qui est déjà annulée dans les étapes précédentes avec :

    `azure vm shutdown <your-virtual-machine-name>`

    >[AZURE.NOTE] Vous pouvez déterminer tous les ordinateurs virtuels créés à l’aide de votre abonnement`azure vm list`

8. Lorsque la machine virtuelle est arrêtée, capturez l’image avec la commande :

    `azure vm capture -t <your-virtual-machine-name> <new-image-name>`

    Tapez le nom d’image à la place de _nouveau--nom de l’image_. Cette commande crée une image du système d’exploitation GRG. La `-t` sous-commande supprime la machine virtuelle d’origine.

9.  La nouvelle image est désormais disponible dans la liste d’images pouvant être utilisées pour configurer les nouvelles machines virtuelles. Vous pouvez l’afficher avec la commande :

    `azure vm image list`

    Dans le [portail classique Azure] [], il apparaît dans la liste **d’IMAGES** .

    ![Capture de l’image réussie](./media/virtual-machines-linux-classic-capture-image/VMCapturedImageAvailable.png)


## <a name="next-steps"></a>Étapes suivantes
L’image est prêt à être utilisé pour créer des machines virtuelles. Vous pouvez utiliser la commande Azure infrastructure du langage commun `azure vm create` et indiquez le nom de l’image que vous avez créé. Pour plus d’informations sur la commande, voir [utilisation de l’infrastructure du langage commun Azure avec le modèle de déploiement classique](../virtual-machines-command-line-tools.md) . Vous pouvez également utiliser le [portail classique Azure] [] pour créer une machine virtuelle personnalisée en utilisant la méthode **De la galerie** et en sélectionnant l’image que vous avez créé. Pour plus d’informations, voir [comment créer une Machine virtuelle personnalisée] [] .

**Voir aussi :** [Guide de l’utilisateur Azure Linux Agent](virtual-machines-linux-agent-user-guide.md)

[Portail classique Azure]: http://manage.windowsazure.com
[À propos des Images de Machine virtuelle dans Azure]: virtual-machines-linux-classic-about-images.md
[Comment créer une Machine virtuelle personnalisé]: virtual-machines-linux-classic-create-custom.md
[How to Attach a Data Disk to a Virtual Machine]: virtual-machines-windows-classic-attach-disk.md
[Comment créer une Machine virtuelle Linux]: virtual-machines-linux-classic-create-custom.md
