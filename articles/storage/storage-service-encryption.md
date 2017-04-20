<properties
    pageTitle="Chiffrement de Service de stockage Azure des données au repos | Microsoft Azure"
    description="Utiliser la fonctionnalité de chiffrement de Service de stockage Azure pour chiffrer votre stockage d’objets Blob Azure sur le côté de service lorsque vous stockez les données et déchiffrer lors de la récupération des données."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="robinsh"/>

# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Chiffrement de Service de stockage Azure des données au repos

Azure stockage Service chiffrement (SSE) des données au repos vous permet de protéger et protéger vos données afin de répondre à votre sécurité de l’organisation et les engagements de conformité. Avec cette fonctionnalité, le stockage Azure chiffre vos données avant de conservation au stockage automatiquement et déchiffre avant récupération. Le chiffrement, le déchiffrement et gestion de clés sont complètement transparentes pour les utilisateurs.

Les sections suivantes fournissent des expériences d’instructions détaillées sur la façon d’utiliser les fonctionnalités de chiffrement de Service de stockage, ainsi que les scénarios pris en charge et utilisateur.

## <a name="overview"></a>Vue d’ensemble

Stockage Azure propose un ensemble complet de fonctionnalités de sécurité qui ensemble permettent aux développeurs de créer des applications sécurisées. Les données peuvent être sécurisées lors des transferts entre une application et Azure en utilisant [Le chiffrement côté Client](storage-client-side-encryption.md), HTTPs ou PME 3.0. Chiffrement de Service de stockage fournit chiffrement inactives, du chiffrement gestion, déchiffrement et gestion des clés de manière totalement transparente. Toutes les données sont chiffrées à l’aide de 256 bits [le chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), effectuez le chiffrement bloc plus puissante disponible.

SSE fonctionne en chiffrant les données lorsqu’il est enregistré dans le stockage Azure et peut être utilisé pour les objets BLOB bloc, des objets BLOB de page et ajoute des objets BLOB. Cela fonctionne pour les éléments suivants :

-   Objectif général stockage comptes et stockage Blob
-   Stockage standard et Premium 
-   Redondance tous les niveaux (LRS, ZRS, GRS, RA GRS)
-   Stockage de gestionnaire de ressources Azure comptes (mais pas classique) 
-   Toutes les régions

Pour activer ou désactiver le chiffrement de Service de stockage pour un compte de stockage, connectez-vous au [portail Azure](https://azure.portal.com) et sélectionnez un compte de stockage. Sur la carte de paramètres, recherchez la section Service Blob comme indiqué dans cette capture d’écran, puis cliquez sur le chiffrement.

![Option de chiffrement portail capture d’écran illustrant](./media/storage-service-encryption/image1.png)

Après avoir cliqué sur le paramètre de chiffrement, vous pouvez activer ou désactiver le chiffrement de Service de stockage.

![Portail capture d’écran illustrant les propriétés de chiffrement](./media/storage-service-encryption/image2.png)

##<a name="encryption-scenarios"></a>Scénarios de chiffrement

Chiffrement de Service de stockage peut être activé au niveau du compte de stockage. Il prend en charge les scénarios client suivants :

-   Chiffrement des objets BLOB bloc, ajouter des objets BLOB et des objets BLOB de page.

-   Chiffrement des archivés disques durs virtuels et des modèles portées à Azure locales.

-   Chiffrement de système d’exploitation sous-jacent et disques de données pour les machines virtuelles IaaS créés à l’aide de vos disques durs virtuels.

SSE présente les limitations suivantes :

-   Le chiffrement des comptes de stockage classique n’est pas pris en charge.

-   Chiffrement de stockage classique comptes migrés vers le Gestionnaire de ressources les comptes de stockage n’est pas pris en charge.

-   Les données existantes - SSE chiffre uniquement les données nouvellement créées une fois que le chiffrement est activé. Si par exemple vous créez un nouveau compte de stockage du Gestionnaire de ressources mais que vous n’activez pas le chiffrement et puis vous téléchargez des objets BLOB ou disques durs virtuels archivés dans ce compte de stockage, puis activez SSE, ces objets BLOB ne sera pas chiffré, sauf si elles sont réécrits ou copiées.

-   Prise en charge Marketplace - activer le chiffrement des machines virtuelles créé à partir de l’aide du [portail Azure](https://portal.azure.com), PowerShell et infrastructure du langage commun Azure Marketplace. L’image de base du disque dur virtuel restera non chiffrée ; Toutefois, toutes les écritures terminés une fois que la machine virtuelle a effectué la seront chiffrés.

-   Table, files d’attente, et les données de fichiers ne seront pas chiffrées.

##<a name="getting-started"></a>Prise en main

###<a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>Étape 1 : [créer un nouveau compte de stockage](storage-create-storage-account.md).

###<a name="step-2-enable-encryption"></a>Étape 2 : Activer le chiffrement.

Vous pouvez activer le chiffrement à l’aide du [portail Azure](https://portal.azure.com).

> [AZURE.NOTE] Si vous souhaitez par programme activer ou désactiver le chiffrement de Service de stockage sur un compte de stockage, vous pouvez utiliser l' [API REST de Azure stockage ressource fournisseur](https://msdn.microsoft.com/library/azure/mt163683.aspx), la [Bibliothèque de Client de fournisseur de ressources de stockage pour .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx), [PowerShell Azure](../powershell-install-configure.md)ou [Azure infrastructure du langage commun](storage-azure-cli.md).

###<a name="step-3-copy-data-to-storage-account"></a>Étape 3 : Copier des données à un compte de stockage

Si vous activez SSE sur un compte de stockage, puis écrivez des objets BLOB dans ce compte de stockage, les objets BLOB seront chiffrés. Les objets BLOB que déjà se trouvant dans ce compte de stockage n’est pas chiffré jusqu'à ce qu’ils sont réécrits. Vous pouvez copier les données de compte d’un stockage à l’autre avec SSE chiffré, ou encore activer SSE et copier les objets BLOB à partir d’un seul conteneur vers un autre à assurer que les données précédentes sont chiffrées. Vous pouvez utiliser un des outils suivants pour effectuer cette opération.

#### <a name="using-azcopy"></a>À l’aide de AzCopy

AzCopy est un utilitaire de ligne de commande Windows conçu pour copier les données vers et depuis Microsoft Azure Blob, fichier et Table storage à l’aide de commandes simples avec des performances optimales. Cela permet de copier vos objets BLOB d’un compte de stockage à une autre contenant SSE activé. 

Pour plus d’informations, consultez [transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md).

#### <a name="using-the-storage-client-libraries"></a>En utilisant les bibliothèques de Client de stockage

Vous pouvez copier les données blob vers et depuis le stockage blob ou entre des comptes de stockage à l’aide de notre ensemble enrichi de stockage Client bibliothèques notamment .NET, C++, Java, Android, Node.js, PHP, Python et Ruby.

Pour en savoir plus, visitez notre [prise en main stockage d’objets Blob Azure à l’aide de .NET](storage-dotnet-how-to-use-blobs.md).

#### <a name="using-a-storage-explorer"></a>À l’aide d’un Explorateur de stockage

Vous pouvez utiliser un Explorateur de stockage pour créer des comptes de stockage, charger et télécharger des données, afficher le contenu des objets BLOB et parcourir les répertoires. Vous pouvez utiliser une des offres pour télécharger des objets BLOB à votre compte de stockage avec le chiffrement est activé. Avec certains explorateurs de stockage, vous pouvez également copier données depuis le stockage blob existant vers un autre conteneur dans le compte de stockage ou un nouveau compte de stockage qui a SSE activé.

Pour en savoir plus, visitez le site [Explorers de stockage Azure](storage-explorers.md).

###<a name="step-4-query-the-status-of-the-encrypted-data"></a>Étape 4 : Demander l’état des données chiffrées

Une version mise à jour des bibliothèques de stockage Client a été déployée qui permet de demander l’état d’un objet pour déterminer si elle est chiffré ou non. Exemples sont ajoutés à ce document dans un futur proche.

En attendant, vous pouvez appeler [Obtenir les propriétés de compte](https://msdn.microsoft.com/library/azure/mt163553.aspx) pour vérifier que le compte de stockage a le chiffrement est activé ou afficher les propriétés du compte de stockage dans le portail Azure.

##<a name="encryption-and-decryption-workflow"></a>Chiffrement et déchiffrement de flux de travail

Voici une brève description du flux de travail chiffrement/déchiffrement :

-   Le client Active le chiffrement sur le compte de stockage.

-   Lorsque le client écrit les nouvelles données (Blob placer, placer le bloc, placer la Page, etc.) au stockage Blob ; chaque écriture est chiffré à l’aide de 256 bits [le chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), effectuez le chiffrement bloc plus puissante disponible.

-   Lorsque le client a besoin accéder aux données (Blob obtenir, etc.), les données sont automatiquement déchiffrées avant de revenir à l’utilisateur.

-   Si le chiffrement est désactivé, nouvelles écritures ne sont plus chiffrés et données chiffrées existantes restent chiffrées jusqu'à réécrits par l’utilisateur. Quand le chiffrement est activé, écritures sur le stockage d’objets Blob seront chiffrés. L’état des données ne change pas avec l’utilisateur de basculer entre l’activation/désactivation de chiffrement pour le compte de stockage.

-   Toutes les clés de chiffrement sont stockées, chiffrés et gérés par Microsoft.

##<a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Forum aux questions sur le chiffrement de Service de stockage des données au repos

**Q : j’ai un compte de stockage classique existant. Puis-je activer SSE dessus ?**

R : non, SSE est uniquement prise en charge sur les comptes de stockage Gestionnaire de ressources.

**Q : Comment puis-je chiffrer des données dans mon compte de stockage classique ?**

A : vous pouvez créer un nouveau compte de stockage du Gestionnaire de ressources et copier vos données à l’aide de [AzCopy](storage-use-azcopy.md) à partir de votre compte de stockage classique existant à votre compte de stockage Gestionnaire de ressources nouvellement créé. 

Une autre option consiste à migrer votre compte de stockage classique à un compte de stockage de gérer les ressources. Pour plus d’informations, voir [Plates-formes prises en charge de IaaS des ressources de Migration de classique au Gestionnaire de ressources](https://azure.microsoft.com/blog/iaas-migration-classic-resource-manager/).

**Q : j’ai un compte de stockage Gestionnaire de ressources existant. Puis-je activer SSE dessus ?**

R : Oui, mais seules les nouvelles écrit BLOB est chiffré. Il vous ne pas revenir en arrière et chiffrer les données qui étaient déjà présentes. 

**Q : je souhaite chiffrer les données actuelles dans un compte de stockage Gestionnaire de ressources existant ?**

A : vous pouvez activer SSE à tout moment dans un compte de stockage du Gestionnaire de ressources. Toutefois, les objets BLOB qui étaient déjà présentes ne seront pas chiffrés. Pour chiffrer ces objets BLOB, vous pouvez copiez-les dans un autre nom ou un autre conteneur, puis supprimez les versions non chiffrées.

**Q : j’utilise le stockage Premium ; puis-je utiliser SSE ?**

R : Oui, SSE est pris en charge sur stockage Standard et stockage Premium.

**Q : si je créer un nouveau compte de stockage et activer SSE, puis créer une nouvelle machine virtuelle à l’aide de ce compte de stockage, que signifie que mon machine virtuelle est chiffré ?**

R : Oui. Aucun disque créé qui utilisent le nouveau compte de stockage sera chiffré, dans la mesure où ils sont créés après que SSE est activée. Si la machine virtuelle a été créée à l’aide de la Place de marché Azure, l’image de base du disque dur virtuel restera non chiffré ; Toutefois, toutes les écritures terminés une fois que la machine virtuelle a effectué la seront chiffrés.

**Q : puis-je créer de nouveaux comptes de stockage avec SSE activé à l’aide de PowerShell Azure et Azure infrastructure du langage commun ?**

R : Oui.

**Q : comment beaucoup plus stockage Azure coûte si SSE est activé ?**

R : il n’est sans frais supplémentaires.

**Q : qui gère les clés de chiffrement ?**

R : les clés sont gérés par Microsoft.

**Q : puis-je utiliser mes propres clés de chiffrement ?**

R : nous travaillons sur fournissant des fonctions pour les clients importer leurs propres clés de chiffrement.

**Q : puis-je révoquer l’accès à la clé de chiffrement ?**

R : pas pour le moment ; les touches sont entièrement gérés par Microsoft.

**Q : est SSE activé par défaut lorsque je crée un nouveau compte de stockage ?**

R : SSE n’est pas activée par défaut ; Vous pouvez utiliser le portail Azure pour l’activer. Vous pouvez aussi activer cette fonctionnalité à l’aide de l’API REST de stockage ressource fournisseur.

**Q : comment est-elle différente de chiffrement de lecteur Azure ?**

R : cette fonctionnalité est utilisée pour chiffrer les données dans le stockage Blob Azure. Le chiffrement de disque Azure est utilisé pour chiffrer les disques du système d’exploitation et les données de machines virtuelles IaaS. Pour plus d’informations, visitez notre [Guide de sécurité de stockage](storage-security-guide.md).

**Q : que se passe-t-il si j’activer SSE, puis accédez et activer le chiffrement de disque Azure sur les disques ?**

R : cela fonctionne en toute transparence. Vos données sont chiffrées par ces deux méthodes.

**Q : mon compte de stockage est configuré pour être répliquées geo redondants. Si j’active SSE, est ma copie redondant également chiffré ?**

R : Oui, toutes les copies du compte de stockage sont chiffrés, et toutes les options de redondance – stockage localement redondantes (LRS), Zone redondants stockage (ZRS), stockage Geo redondants (GRS) et le stockage de Geo redondants accès en lecture (RA GRS) – sont prises en charge.

**Q : je ne parviens pas à activer le chiffrement sur mon compte de stockage.**

R : il est un compte de stockage du Gestionnaire de ressources ? Comptes de stockage classique ne sont pas pris en charge. 

**Q : SSE n’est autorisé dans des régions spécifiques ?**

R : SSE est disponible dans toutes les régions. 

**Q : comment contacter une personne si j’ai déjà des problèmes ou que vous voulez fournir des commentaires ?**

R : Veuillez contacter [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) pour des problèmes liés au chiffrement de Service de stockage.

##<a name="next-steps"></a>Étapes suivantes

Stockage Azure propose un ensemble complet de fonctionnalités de sécurité qui ensemble permettent aux développeurs de créer des applications sécurisées. Pour plus d’informations, consultez le [Guide de sécurité de stockage](storage-security-guide.md).
