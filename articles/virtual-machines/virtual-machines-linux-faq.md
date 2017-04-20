<properties
    pageTitle="Forum aux questions pour les machines virtuelles Linux | Microsoft Azure"
    description="Fournit des réponses à certaines questions courantes sur une machine virtuelle Linux créée avec le modèle de gestionnaire de ressources."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="cynthn"/>

# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Forum aux questions sur le Machines virtuelles Linux

Cet article répond à certaines questions fréquentes concernant une machine virtuelle Linux créée dans Azure à l’aide du modèle de déploiement Gestionnaire de ressources. Pour la version Windows de cette rubrique, voir [Forum aux questions sur le Machines virtuelles Windows](virtual-machines-windows-faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>Que puis-je exécuter sur une machine virtuelle Azure ?

Tous les abonnés peuvent exécuter le logiciel serveur sur une machine virtuelle Azure. Pour plus d’informations, voir [Linux sur répartitions Azure-Endorsed](virtual-machines-linux-endorsed-distros.md)


## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quelle quantité de stockage puis-je utiliser avec une machine virtuelle ?

Chaque disque de données peut être jusqu'à 1 To. Le nombre de disques de données que vous pouvez utiliser dépend de la taille de la machine virtuelle. Pour plus d’informations, voir [formats pour les Machines virtuelles](virtual-machines-linux-sizes.md).

Un compte de stockage Azure fournit un stockage pour le disque système d’exploitation et des disques de données. Chaque disque est un fichier .vhd stocké comme un objet blob de page. Pour connaître les prix, voir [Détails sur le stockage tarifs](https://azure.microsoft.com/pricing/details/storage/).


## <a name="how-can-i-access-my-virtual-machine"></a>Comment puis-je accéder à mon ordinateur virtuel ?

Établir une connexion à distance pour vous connecter à la machine virtuelle, à l’aide de SSH (Secure Shell). Consultez les instructions sur la connexion [à partir de Windows](virtual-machines-linux-ssh-from-windows.md) ou [Linux et Mac](virtual-machines-linux-mac-create-ssh-keys.md). Par défaut, SSH autorise un maximum de 10 connexions simultanées. Vous pouvez augmenter ce nombre en modifiant le fichier de configuration.


Si vous rencontrez des problèmes, consultez [résoudre les problèmes Secure Shell (SSH) connexions](virtual-machines-linux-troubleshoot-ssh-connection.md).


## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Puis-je utiliser le disque temporaire (/ développement/sdb1) pour stocker des données ?

N’utilisez pas le disque temporaire (/ développement/sdb1) pour stocker les données. C’est là uniquement pour le stockage temporaire. Vous risquez de perdre des données qui ne peuvent pas être récupérées.


## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Puis-je copier ou cloner une machine virtuelle Azure existant ?

Oui. Pour plus d’informations, voir [comment créer une copie d’une machine virtuelle Linux dans le modèle de déploiement du Gestionnaire de ressources](virtual-machines-linux-copy-vm.md).


## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Pourquoi est-ce que je ne vois pas Canada Central et Canada orientale régions via le Gestionnaire de ressources Azure ?

Les deux nouvelles zones de Canada Central et Moyen-Orient Canada ne sont pas automatiquement enregistrés pour la création de machine virtuelle pour les abonnements Azure existants. Cet enregistrement s’effectue automatiquement lorsqu’une machine virtuelle est déployée à partir du portail Azure à n’importe quelle autre région à l’aide du Gestionnaire de ressources Azure. Après qu’une machine virtuelle est déployée sur n’importe quelle autre région Azure, de nouvelles zones doivent être disponibles pour les autres machines virtuelles.


## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Puis-je ajouter une carte réseau à mon machine virtuelle après sa création ?

Non. Ajout d’une carte réseau peut uniquement être exécuté au moment de la création.


## <a name="are-there-any-computer-name-requirements"></a>Existe-t-il des conditions nom ordinateur ?

Oui. Le nom d’ordinateur peut être un maximum de 64 caractères. Pour plus d’informations d’attribution de vos ressources, voir [instructions d’appellation Infrastructure](virtual-machines-linux-infrastructure-naming-guidelines.md) .


## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quelles sont les conditions de nom d’utilisateur lorsque vous créez une machine virtuelle ?

Les noms d’utilisateur doivent être 1-64 caractères.

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

Les mots de passe doivent être 6-72 caractères et répondre aux 3 déconnecter la complexité 4 suivants :

- Que les caractères inférieurs
- Que les caractères supérieures
- Un chiffre
- Ont un caractère spécial (Regex correspond à [\W_])

Les mots de passe suivantes ne sont pas autorisés :

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">PA$ $word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Mot de passe !</td>
        <td style="text-align:center">Mot de passe1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">ILOVEYOU !</td>
    </tr>
</table>
