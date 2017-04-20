<properties
   pageTitle="Présentation de la sécurité stockage Azure | Microsoft Azure"
   description=" Stockage Azure est la solution de stockage cloud pour les applications modernes qui s’appuient sur durabilité, la disponibilité et extensibilité élevées en fonction des besoins de leurs clients. Cet article fournit une vue d’ensemble des principales fonctionnalités de sécurité Azure qui peuvent être utilisées avec le stockage Azure. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/16/2016"
   ms.author="terrylan"/>

# <a name="azure-storage-security-overview"></a>Présentation de la sécurité stockage Azure

Stockage Azure est la solution de stockage cloud pour les applications modernes qui s’appuient sur durabilité, la disponibilité et extensibilité élevées en fonction des besoins de leurs clients. Stockage Azure fournit un ensemble complet de fonctionnalités de sécurité :

- Le compte de stockage peut être sécurisé à l’aide de contrôle d’accès basé sur un rôle et Azure Active Directory.
- Les données peuvent être sécurisées lors des transferts entre une application et Azure en utilisant le chiffrement côté Client, HTTPS ou PME 3.0.
- Données peuvent être définies à chiffrer automatiquement lors de l’écriture au stockage Azure à l’aide de chiffrement de Service de stockage.
- Système d’exploitation et données disques utilisés par les machines virtuelles peuvent être définies pour être chiffrés à l’aide de chiffrement des disques Azure.
- Accès délégué aux objets de données dans le stockage Azure peuvent être accordées à l’aide de Signatures d’accès partagé.
- La méthode d’authentification utilisée par une personne lorsqu’ils accèdent à stockage peut être suivie à l’aide d’analytique de stockage.

Pour une analyse plus détaillée de sécurité dans le stockage Azure, voir le [guide de sécurité de stockage Azure](../storage/storage-security-guide.md). Ce guide approfondie de fonctionnalités de sécurité de stockage Azure tels que des clés de compte de stockage, chiffrement des données de transfert et reste et analytique de stockage.

Cet article fournit une vue d’ensemble des fonctionnalités de sécurité Azure qui peuvent être utilisés avec le stockage Azure. Des liens sont fournis vers des articles qui donnent des détails de chaque fonctionnalité, vous pouvez en savoir plus.

Voici les principales fonctionnalités qui seront traités dans cet article :

- Contrôle d’accès basé sur un rôle
- Accès délégué aux objets de stockage
- Chiffrement lors des transferts
- Chiffrement au reste/stockage chiffrement de Service
- Chiffrement de disque Azure
- Archivage sécurisé clé Azure

## <a name="role-based-access-control-rbac"></a>Contrôle d’accès basé sur un rôle (RBAC)

Vous pouvez protéger votre compte de stockage avec contrôle d’accès basé sur un rôle (RBAC). Restriction d’accès basé sur les principes de sécurité [devez connaître](https://en.wikipedia.org/wiki/Need_to_know) et [moindre privilège](https://en.wikipedia.org/wiki/Principle_of_least_privilege) est impératif pour les organisations qui souhaitent d’appliquer des stratégies de sécurité pour accéder aux données. Ces droits d’accès sont accordées en affectant le rôle RBAC approprié à des groupes et des applications à une certaine étendue. Vous pouvez utiliser [les rôles RBAC intégrés](../active-directory/role-based-access-built-in-roles.md), tels que collaborateur du compte de stockage, pour affecter des privilèges aux utilisateurs.

Pour en savoir plus :

- [Contrôle d’accès basé sur un rôle Azure Active Directory](../active-directory/role-based-access-control-configure.md)

## <a name="delegated-access-to-storage-objects"></a>Accès délégué aux objets de stockage

Une signature accès partagé (sa) offre un accès délégué aux ressources dans votre compte de stockage. Les associations de sécurité signifie que vous pouvez accorder qu'un client des autorisations limitées d’objets dans votre compte de stockage pour une période spécifiée de temps et avec un jeu d’autorisations spécifié. Vous pouvez accorder ces autorisations limitées sans avoir à partager les touches d’accès de votre compte. Les associations de sécurité sont un URI qui englobe dans ses paramètres de la requête toutes les informations nécessaires pour l’accès authentifié pour une ressource de stockage. Pour accéder à des ressources de stockage avec les associations de sécurité, le client doit passer à la méthode ou constructeur approprié dans les associations de sécurité.

Pour en savoir plus :

- [Présentation du modèle associations de sécurité](../storage/storage-dotnet-shared-access-signature-part-1.md)
- [Créer et utiliser une associations de sécurité avec le stockage Blob](../storage/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>Chiffrement lors des transferts
Chiffrement lors des transferts est un mécanisme de protection des données lorsqu’il est transmis sur des réseaux. Vous pouvez protéger les données à l’aide avec le stockage Azure :

- [Chiffrement de niveau transport](../storage/storage-security-guide.md#encryption-in-transit), telles que HTTPS lorsque vous transférez des données ou disparition stockage Azure.
- [Chiffrement fils](../storage/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), telles que le chiffrement PME 3.0 Azure partages de fichiers.
- [Chiffrement côté client](../storage/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage), pour chiffrer les données avant leur transfert dans le stockage et déchiffrer les données après transfert se déconnecter de stockage.

Informations supplémentaires sur le chiffrement côté client :

- [Chiffrement côté client pour le stockage de Microsoft Azure](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
- [Série de contrôles de sécurité cloud : chiffrement de données lors des transferts](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Chiffrement inactives

Pour de nombreuses organisations, [chiffrement des données au repos](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) est une étape obligatoire vers souveraineté de données, conformité et confidentialité des données. Il existe trois fonctionnalités Azure qui fournissent le chiffrement des données qui se trouve « au reste » :

- [Chiffrement de Service de stockage](../storage/storage-security-guide.md#encryption-at-rest) vous permet de demander que le service de stockage chiffrer automatiquement les données lors de l’écriture au stockage Azure.
- [Chiffrement côté client](../storage/storage-security-guide.md#client-side-encryption) fournit également la fonctionnalité de chiffrement inactives.
- [Chiffrement de disque Azure](../storage/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) permet de chiffrer le système d’exploitation disques et données utilisées par une machine virtuelle IaaS.

Informations supplémentaires sur le chiffrement de Service de stockage :

- [Chiffrement de Service de stockage Azure](https://azure.microsoft.com/services/storage/) est disponible pour [Le stockage Blob Azure](https://azure.microsoft.com/services/storage/blobs/). Pour plus d’informations sur d’autres types de stockage Azure, voir [fichiers](https://azure.microsoft.com/services/storage/files/), [disque (stockage Premium)](https://azure.microsoft.com/services/storage/premium-storage/), [tables](https://azure.microsoft.com/services/storage/tables/)et [file d’attente](https://azure.microsoft.com/services/storage/queues/).
- [Chiffrement de Service de stockage Azure des données au repos](../storage/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Chiffrement de disque Azure

Azure chiffrement de disque pour les machines virtuelles (machines virtuelles) vous permet de sécurité de l’organisation adresse et aux exigences de conformité en chiffrant vos disques machine virtuelle (y compris les disques de démarrage et de données) avec des clés et des stratégies que vous contrôlent dans [L’archivage sécurisé de clé Azure](https://azure.microsoft.com/services/key-vault/).

Chiffrement de disque pour les machines virtuelles fonctionne pour les systèmes d’exploitation Linux et Windows. Il utilise également l’archivage sécurisé clé pour vous aider à protéger, gérer et auditer l’utilisation de vos clés de chiffrement de disque. Toutes les données de vos disques machine virtuelle est chiffré inactives à l’aide de la technologie de chiffrement normalisés dans vos comptes de stockage Azure. La solution de chiffrement de disque pour Windows est basée sur [Le chiffrement de lecteur BitLocker Microsoft](https://technet.microsoft.com/library/cc732774.aspx)et la solution Linux est basée sur [dm crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Pour en savoir plus :

- [Chiffrement de disque Azure pour Windows et Machines virtuelles Linux IaaS](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="azure-key-vault"></a>Archivage sécurisé clé Azure

Chiffrement de disque Azure utilise [L’archivage sécurisé de clé Azure](https://azure.microsoft.com/services/key-vault/) pour vous aider à contrôler et gérer des clés de chiffrement de disque et secrets dans votre abonnement de l’archivage sécurisé clés, tout en garantissant que toutes les données dans les disques machine virtuelle sont chiffrées au reste dans votre espace de stockage Azure. Vous devez utiliser l’archivage sécurisé clé à un audit clés et l’utilisation de stratégie.

Pour en savoir plus :

- [Quel est l’archivage sécurisé de clé Azure ?](../key-vault/key-vault-whatis.md)
- [Prise en main l’archivage sécurisé de clé Azure](../key-vault/key-vault-get-started.md)
