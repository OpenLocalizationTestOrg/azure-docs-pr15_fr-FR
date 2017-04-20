<properties 
   pageTitle="Affichage et modification des noms d’hôtes | Microsoft Azure"
   description="Comment afficher et modifier les noms d’hôtes pour machines virtuelles Azure, web et les rôles de travail pour la résolution de noms"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="viewing-and-modifying-hostnames"></a>Affichage et modification des noms d’hôte

Pour permettre à vos instances de rôles à référencer par nom d’hôte, vous devez définir la valeur pour le nom d’hôte dans le fichier de configuration de service pour chaque rôle. Vous effectuez qui en ajoutant le nom d’hôte souhaité pour l’attribut **vmName** de l’élément de **rôle** . La valeur de l’attribut **vmName** est utilisée comme base pour le nom d’hôte de chaque instance de rôle. Par exemple, si **vmName** est *webrole* , il existe trois instances de ce rôle, les noms d’hôtes des instances seront présentera *webrole0*, *webrole1*et *webrole2*. Vous n’avez pas besoin de spécifier un nom d’hôte pour les machines virtuelles dans le fichier de configuration, car le nom d’hôte pour une machine virtuelle est rempli en fonction du nom de la machine virtuelle. Pour plus d’informations sur la configuration d’un service Microsoft Azure, consultez [Schéma de Configuration du Service Azure (.cscfg fichier)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Affichage des noms d’hôte

Vous pouvez afficher les noms d’hôtes des machines virtuelles et des instances de rôles dans un service cloud à l’aide d’un des outils ci-dessous.

### <a name="azure-portal"></a>Portail Azure

Vous pouvez utiliser le [portail Azure](http://portal.azure.com) pour afficher les noms d’hôte pour les machines virtuelles sur la carte de présentation pour une machine virtuelle. N’oubliez pas que la carte indique une valeur de **nom** et un **Nom d’hôte**. Bien qu’ils soient initialement identiques, la modification du nom d’hôte changera pas le nom de la machine virtuelle ou d’une instance de rôle.

Instances de rôle peuvent également être affichés dans le portail Azure, mais lorsque vous listez les instances dans un service cloud, le nom d’hôte n’est pas affiché. Vous verrez un nom pour chaque instance, mais ce nom ne représente pas le nom d’hôte.

### <a name="service-configuration-file"></a>Fichier de configuration de service

Vous pouvez télécharger le fichier de configuration de service d’un service déployé à partir de la carte de **configuration** du service dans le portail Azure. Vous pouvez ensuite rechercher l’attribut **vmName** pour l’élément de **nom de rôle** afficher le nom d’hôte. N’oubliez pas que ce nom d’hôte est utilisé comme base pour le nom d’hôte de chaque instance de rôle. Par exemple, si **vmName** est *webrole* , il existe trois instances de ce rôle, les noms d’hôtes des instances seront présentera *webrole0*, *webrole1*et *webrole2*.

### <a name="remote-desktop"></a>Bureau à distance

Après avoir activé Bureau à distance (Windows), Windows PowerShell accès distant (Windows) ou les connexions SSH (Linux et Windows) à votre machines virtuelles ou instances de rôle, vous pouvez afficher le nom d’hôte à partir d’une connexion Bureau à distance active de différentes manières :

- Tapez hostname à l’invite de commandes ou Terminal Server SSH.

- Tapez ipconfig/tout à la commande invite (Windows uniquement).

- Afficher le nom de l’ordinateur dans les paramètres système (Windows uniquement).

### <a name="azure-service-management-rest-api"></a>Gestion des services Azure API REST

À partir d’un client reste, suivez ces instructions :

1. Vérifiez que vous disposez d’un certificat client pour vous connecter au portail Azure. Pour obtenir un certificat client, suivez les étapes présentées dans [Comment : télécharger et importer les paramètres de publication et d’abonnement](https://msdn.microsoft.com/library/dn385850.aspx). 

1. Définir une entrée d’en-tête x-ms-version avec une valeur de 2013-11-01.

1. Envoyer une demande au format suivant : https://management.core.windows.net/\<subscrition id\>/services/hostedservices/\<nom-service\>?embed détail = true

1. Recherchez l’élément **HostName** pour chaque élément **RoleInstance** .

>[AZURE.WARNING] Vous pouvez également afficher le suffixe de domaine internes de votre service de nuage à partir de la réponse d’appel reste en vérifiant l’élément **InternalDnsSuffix** , ou en exécutant ipconfig/à partir d’une invite de commande dans une session de bureau à distance (Windows) ou en cours d’exécution /etc/resolv.conf Chat à partir d’un terminal SSH (Linux).

## <a name="modifying-a-hostname"></a>Modifier un nom d’hôte

Vous pouvez modifier le nom d’hôte une machine virtuelle ou d’une instance de rôle en téléchargeant un fichier de configuration du service modifiée, ou en renommant l’ordinateur à partir d’une session de bureau à distance.

## <a name="next-steps"></a>Étapes suivantes

[Résolution de noms (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Schéma de Configuration de Service Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Schéma de Configuration du réseau virtuel Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Spécifier les paramètres DNS à l’aide de fichiers de configuration réseau](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)
