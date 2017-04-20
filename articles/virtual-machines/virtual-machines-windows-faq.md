<properties
    pageTitle="Forum aux questions pour les machines virtuelles Windows | Microsoft Azure"
    description="Fournit des réponses à certaines questions courantes sur les machines virtuelles Windows créées avec le modèle de gestionnaire de ressources."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="cynthn"/>

# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Forum aux questions sur le Machines virtuelles Windows 


Cet article répond à certaines questions fréquentes concernant une machine virtuelle Windows créée dans Azure à l’aide du modèle de déploiement Gestionnaire de ressources. Pour la version Linux de cette rubrique, voir [Forum aux questions sur le Machines virtuelles Linux](virtual-machines-linux-faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>Que puis-je exécuter sur une machine virtuelle Azure ?

Tous les abonnés peuvent exécuter le logiciel serveur sur une machine virtuelle Azure. Pour plus d’informations sur la politique de support pour l’exécution de logiciels Microsoft server dans Azure, voir [logiciel serveur Microsoft prend en charge pour le Machines virtuelles Azure](https://support.microsoft.com/kb/2721672)

Certaines versions de Windows 7 et Windows 8.1 sont disponibles pour les abonnés MSDN Azure avantage et les abonnés MSDN développement et tester le paiement, pour les tâches de développement et de test. Pour plus d’informations, y compris les instructions et limitations, voir [images Client Windows pour les abonnés MSDN](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 


## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quelle quantité de stockage puis-je utiliser avec une machine virtuelle ?

Chaque disque de données peut être jusqu'à 1 To. Le nombre de disques de données que vous pouvez utiliser dépend de la taille de la machine virtuelle. Pour plus d’informations, voir [formats pour les Machines virtuelles](virtual-machines-windows-sizes.md).

Un compte de stockage Azure fournit un stockage pour le disque système d’exploitation et des disques de données. Chaque disque est un fichier .vhd stocké comme un objet blob de page. Pour connaître les prix, voir [Détails sur le stockage tarifs](https://azure.microsoft.com/pricing/details/storage/).


## <a name="how-can-i-access-my-virtual-machine"></a>Comment puis-je accéder à mon ordinateur virtuel ?

Établir une connexion à distance à l’aide de la connexion RDP (Remote Desktop) pour une machine virtuelle Windows. Pour plus d’informations, voir [comment vous connecter et ouvrir une session sur une machine virtuelle Azure exécutant Windows](virtual-machines-windows-connect-logon.md). Un maximum de deux connexions simultanées sont prises en charge, à moins que le serveur est configuré comme un hôte de la session Services Bureau à distance.  


Si vous rencontrez des problèmes avec Bureau à distance, consultez [résoudre les problèmes de bureau à distance connexions à un Windows Azure Machine virtuelle](virtual-machines-windows-troubleshoot-rdp-connection.md). 

Si vous avez l’habitude d’Hyper-V, vous risquez de rechercher les un outil semblable à VMConnect. Azure n’offre pas un outil similaire, car l’accès à la console à une machine virtuelle n’est pas pris en charge.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Puis-je utiliser le disque temporaire (le lecteur d : par défaut) pour stocker des données ?

N’utilisez pas le disque temporaire pour stocker les données. Il n’est stockage temporaire, afin de vous risque de perte de données qui ne peuvent pas être récupérées. Perte de données peut se produire lorsque la machine virtuelle se déplace vers un autre hôte. Redimensionnement d’une machine virtuelle, mise à jour de l’hôte, ou une défaillance matérielle sur l’hôte est quelques-unes des raisons qu'une machine virtuelle peut déplacer.

Si vous avez une application qui a besoin d’utiliser le lecteur d :, vous pouvez réattribuer des lettres de lecteur afin que le disque temporaire utilise un numéro autre que d. Pour plus d’informations, voir [Modifier la lettre de lecteur du disque temporaire Windows](virtual-machines-windows-classic-change-drive-letter.md).

## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Comment puis-je modifier la lettre du lecteur de disque temporaire ?

Vous pouvez modifier la lettre en déplaçant le fichier d’échange et réaffectation des lettres de lecteur, mais vous devez vous assurer que vous effectuez les étapes dans un ordre spécifique. Pour plus d’informations, voir [Modifier la lettre de lecteur du disque temporaire Windows](virtual-machines-windows-classic-change-drive-letter.md).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Puis-je ajouter une machine virtuelle existante à un ensemble de disponibilité ?

Non. Si vous souhaitez que votre ordinateur virtuel à faire partie d’un ensemble de disponibilité, vous devez créer la machine virtuelle au sein du jeu. Il n’existe actuellement pas ajouter une machine virtuelle à une disponibilité définie une fois qu’il a été créé.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Puis-je télécharger une machine virtuelle dans Azure ?

Oui. Pour plus d’informations, voir [télécharger une image machine virtuelle Windows Azure](virtual-machines-windows-upload-image.md)

## <a name="can-i-resize-the-os-disk"></a>Puis-je redimensionner le disque OS ?

Oui. Pour plus d’informations, voir [comment développer le lecteur système d’exploitation d’une Machine virtuelle dans un groupe de ressources Azure](virtual-machines-windows-expand-os-disk.md).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Puis-je copier ou cloner une machine virtuelle Azure existant ?

Oui. Pour plus d’informations, voir [comment créer une copie d’une machine virtuelle Windows dans le modèle de déploiement du Gestionnaire de ressources](virtual-machines-windows-vhd-copy.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Pourquoi est-ce que je ne vois pas Canada Central et Canada orientale régions via le Gestionnaire de ressources Azure ?

Les deux nouvelles zones de Canada Central et Moyen-Orient Canada ne sont pas automatiquement enregistrés pour la création de machine virtuelle pour les abonnements Azure existants. Cet enregistrement s’effectue automatiquement lorsqu’une machine virtuelle est déployée à partir du portail Azure à n’importe quelle autre région à l’aide du Gestionnaire de ressources Azure. Après qu’une machine virtuelle est déployée sur n’importe quelle autre région Azure, de nouvelles zones doivent être disponibles pour les autres machines virtuelles.

## <a name="does-azure-support-linux-vms"></a>Azure ne prend en charge les machines virtuelles Linux ?

Oui. Pour créer rapidement un VM Linux à essayer, voir [créer une machine virtuelle Linux sur Azure à l’aide du portail](virtual-machines-linux-quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Puis-je ajouter une carte réseau à mon machine virtuelle après sa création ?

Non. Ajout d’une carte réseau peut uniquement être exécuté au moment de la création.

## <a name="are-there-any-computer-name-requirements"></a>Existe-t-il des conditions nom ordinateur ?

Oui. Le nom d’ordinateur peut être de 15 caractères au maximum. Pour plus d’informations d’attribution de vos ressources, voir [instructions d’appellation Infrastructure](virtual-machines-windows-infrastructure-naming-guidelines.md) .

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quelles sont les conditions de nom d’utilisateur lorsque vous créez une machine virtuelle ?

Noms d’utilisateur peut contenir un maximum de 20 caractères et ne peut pas se terminer par un point («. »). 

Les noms d’utilisateurs suivants ne sont pas autorisés :

<table>
    <tr>
        <td style="text-align:center">administrateur </td><td style="text-align:center"> administrateur </td><td style="text-align:center"> utilisateur </td><td style="text-align:center"> utilisateur 1</td>
    </tr>
    <tr>
        <td style="text-align:center">test </td><td style="text-align:center"> utilisateur 2 </td><td style="text-align:center"> Test1 </td><td style="text-align:center"> utilisateur 3</td>
    </tr>
    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> un</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> ADM </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> ASPNET</td>
    </tr>
    <tr>
        <td style="text-align:center">sauvegarde </td><td style="text-align:center"> console </td><td style="text-align:center"> David </td><td style="text-align:center"> invité</td>
    </tr>
    <tr>
        <td style="text-align:center">John </td><td style="text-align:center"> propriétaire </td><td style="text-align:center"> racine </td><td style="text-align:center"> serveur</td>
    </tr>
    <tr>
        <td style="text-align:center">SQL </td><td style="text-align:center"> prise en charge </td><td style="text-align:center"> Support_388945a0 </td><td style="text-align:center"> Sys</td>
    </tr>
    <tr>
        <td style="text-align:center">Test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> utilisateur 4 </td><td style="text-align:center"> user5</td>
    </tr>
</table>

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Quelles sont les exigences de mot de passe lorsque vous créez une machine virtuelle ?

Les mots de passe doivent être 8-123 caractères et répondre aux 3 déconnecter la complexité 4 suivants :

- Que les caractères inférieurs
- Que les caractères supérieures
- Un chiffre
- Ont un caractère spécial (Regex correspond à [\W_])

Les mots de passe suivantes ne sont pas autorisés :

<table>
    <tr>
        <td style="text-align:center">abc@123</td><td style="text-align:center">P@$$w0rd</td><td style="text-align:center">P@ssw0rd</td><td style="text-align:center">P@ssword123</td><td style="text-align:center">PA$ $word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td><td style="text-align:center">Mot de passe !</td><td style="text-align:center">Mot de passe1</td><td style="text-align:center">Password22</td><td style="text-align:center">ILOVEYOU !</td>
    </tr>
</table>
