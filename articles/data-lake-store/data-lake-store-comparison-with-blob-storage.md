<properties
   pageTitle="Comparaison de magasin de Lake données Azure avec Azure stockage Blob | Microsoft Azure"
   description="Comparaison de magasin de Lake données Azure avec Azure stockage Blob"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/15/2016"
   ms.author="nitinme"/>

# <a name="comparing-azure-data-lake-store-and-azure-blob-storage"></a>Comparaison entre Azure données Lake Store et stockage d’objets Blob Azure

Le tableau dans cet article résume les différences entre Azure Data Lake Store et stockage d’objets Blob Azure le long de certains aspects essentiels volumineux du traitement des données. Stockage d’objets Blob Azure est un objectif général, le magasin d’objets scalable est conçu pour un large éventail de scénarios de stockage. Azure Data Lake Store est un référentiel hyper-échelle qui est optimisé pour les charges de travail données volumineuses analytique.

|    | Magasin de Lake données Azure  | Stockage d’objets Blob Azure  |
|----|-----------------------|--------------------|
| Objectif  | Stockage optimisé pour les charges de travail données volumineuses analytique                                                                          | Objectif général objet stocker pour un large éventail de scénarios de stockage                                                                                |
| Exemples d’utilisation                                   | Traitement par lots interactifs, tels que la diffusion analytique et données d’apprentissage machine fichiers journaux, IoT données, cliquez sur flux, de grands volumes de données | N’importe quel type de données texte ou binaire, comme application retour à la fin, les données de sauvegarde, supports de stockage pour les données d’objectif de diffusion en continu et générales |
| Concepts clés                                | Compte de données Lake Store contient des dossiers, qui à son tour contient les données stockées en tant que fichiers | Compte de stockage a conteneurs, qui à son tour contient les données sous la forme d’objets BLOB |
| Structure | Système de fichiers hiérarchiques                                                                                                    | Magasin d’objets avec l’espace de noms plat  |
| API   | API REST sur HTTPS | API REST via HTTP/HTTPS                                                                                                                            |
| API côté serveur                             | [API REST WebHDFS compatibles](https://msdn.microsoft.com/library/azure/mt693424.aspx)                                                                                                 | [Stockage d’objets Blob Azure API REST](https://msdn.microsoft.com/library/azure/dd135733.aspx)                                                                                                                         |
| Fichier Hadoop système Client                   | Oui                                                                                                                         | Oui                                                                                                                                                 |
| Opérations de données - authentification            | En fonction [des identités Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md) | En fonction de secrets partagés - [Compte les touches d’accès](../storage/storage-create-storage-account.md#manage-your-storage-account) et les [Clés de Signature Access partagées](../storage/storage-dotnet-shared-access-signature-part-1.md).                                                                       |
| Opérations de données - protocole d’authentification     | OAuth 2.0. Appels doivent contenir un JWT valide (jeton Web JSON) émis par Azure Active Directory                     | Code d’authentification de Message basé sur hachage (HMAC). Appels doivent contenir un hachage ça-256 codé en base 64 sur une partie de la requête HTTP. |
| Opérations de données - d’autorisation               | Contrôle d’accès POSIX (listes).  Utilisateurs basés sur Azure Active Directory identités peuvent être définies à niveau des fichiers et des dossiers. | Pour les autorisations au niveau des comptes – utiliser les [Touches d’accès compte](../storage/storage-create-storage-account.md#manage-your-storage-account)<br>Pour compte, conteneur ou blob autorisation - utiliser les [Clés de Signature accès partagé](../storage/storage-dotnet-shared-access-signature-part-1.md) |
| Opérations de données - audit                    | Disponible. Voir [ici](data-lake-store-diagnostic-logs.md) pour plus d’informations.                                                                                                                   | Disponible                                                                                                                                           |
| Données de chiffrement inactives                     | Transparente, côté serveur (bientôt disponible)<ul><li>Avec touches gérés par le service</li><li>Avec touches gérés par le client dans Azure KeyVault</li></ul>| <ul><li>Transparente, côté serveur</li> <ul><li>Avec touches gérés par le service</li><li>Avec touches gérés par le client dans Azure KeyVault (bientôt disponible)</li></ul><li>Chiffrement côté client</li></ul> |
| Opérations de gestion (par exemple, créer un compte) | [Contrôle d’accès basé sur un rôle](../active-directory/role-based-access-control-what-is.md) (RBAC) fournie par Azure pour la gestion des comptes                                                                       | [Contrôle d’accès basé sur un rôle](../active-directory/role-based-access-control-what-is.md) (RBAC) fournie par Azure pour la gestion des comptes                                                                                               |
| Développeur SDK                              | .NET, Java, Node.js                                                                                                         | .NET, Java, Python, Node.js, C++, Ruby                                                                                                              |
| Performances Analytique              | Optimisation des performances pour les charges de travail analytique en parallèle. Haut débit et sorties par.                                           | Pas optimisé pour les charges de travail analytique                                                                                                               |
| Limites de taille                                 | Aucune limite sur tailles de compte, les tailles de fichiers ou nombre de fichiers                                                                   | Limites spécifiques documentation [ici](../azure-subscription-service-limits.md#storage-limits)                                                                                                                     |
| Geo redondance                              | Localement-redondantes (plusieurs copies de données dans une zone géographique Azure)                                                             | Localement redondantes (LRS), globalement redondantes (GRS), accès en lecture globalement redondantes (RA-GRS). Voir [ici](../storage/storage-redundancy.md) pour plus d’informations |
| État du service                               | Version d’évaluation                                                                                                              | Généralement disponibles                                                                                                                                 |
| Disponibilité régionale  | Voir [ici](https://azure.microsoft.com/regions/#services)| Voir [ici](https://azure.microsoft.com/regions/#services) |
| Prix                                       |     Consultez les [tarifs](https://azure.microsoft.com/pricing/details/data-lake-store/)| Consultez les [tarifs](https://azure.microsoft.com/pricing/details/storage/) |

### <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble Azure Lake de magasin de données](data-lake-store-overview.md)
- [Prise en main données Lake Store](data-lake-store-get-started-portal.md)



