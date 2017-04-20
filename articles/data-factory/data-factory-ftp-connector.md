<properties 
    pageTitle="Déplacer des données d’un serveur FTP | Microsoft Azure" 
    description="Découvrez comment déplacer des données d’un serveur FTP à l’aide d’Azure Data Factory." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/20/2016" 
    ms.author="spelluru"/>

# <a name="move-data-from-an-ftp-server-using-azure-data-factory"></a>Déplacer des données d’un serveur FTP à l’aide d’usine de données Azure
Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour déplacer des données d’un serveur FTP dans un magasin de données récepteur pris en charge. Cet article s’appuie sur l’article [activités de déplacement de données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble de déplacement de données avec activité de copie et la liste de banques de données pris en charge en tant que sources/récepteurs. 

Usine données prend actuellement en charge uniquement les données à partir d’un serveur FTP déplacement aux autres banques de données, mais ne pas pour le déplacement de données provenant d’autres banques de données sur un serveur FTP. Il prend en charge les deux en local et en nuage serveurs FTP. 

Si vous déplacez des données d’un serveur **local** FTP dans un magasin de données du cloud (exemple : stockage d’objets Blob Azure), installer et utiliser la passerelle de gestion des données. La passerelle de gestion des données est un agent de client qui est installé sur votre ordinateur local, ce qui permet aux services cloud pour vous connecter à des ressources locales. Pour plus d’informations sur la passerelle, consultez la [Passerelle de gestion des données](data-factory-data-management-gateway.md) . Consultez l’article de [déplacement des données entre des emplacements de locaux et cloud](data-factory-move-data-between-onprem-and-cloud.md) pour des instructions détaillées sur la configuration de la passerelle et son utilisation. La passerelle vous permet de vous connecter à un serveur FTP, même si le serveur se trouve sur une Azure IaaS machine virtuelle (). 

Vous pouvez installer la passerelle sur le même ordinateur local ou la machine virtuelle IaaS Azure en tant que le serveur FTP. Toutefois, nous vous recommandons d’installer la passerelle sur un ordinateur distinct ou un ordinateur de IaaS Azure distincte virtuel pour éviter les conflits de ressources et pour améliorer les performances. Lorsque vous installez la passerelle sur un ordinateur distinct, l’ordinateur doit être en mesure d’accéder au serveur FTP. 

## <a name="copy-data-wizard"></a>Copier l’Assistant de données
Pour créer une opportunité qui copie les données à partir d’un serveur FTP, le plus simple consiste à utiliser l’Assistant copie de données. Voir [didacticiel : créer une opportunité à l’aide de l’Assistant copie](data-factory-copy-data-wizard-tutorial.md) pour un bref aperçu sur la création d’un pipeline à l’aide de l’Assistant copie de données. 

Les exemples suivants fournissent des exemples de définitions JSON que vous pouvez utiliser pour créer une opportunité à l’aide de [portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [PowerShell Azure](data-factory-copy-activity-tutorial-using-powershell.md). 

## <a name="sample-copy-data-from-ftp-server-to-azure-blob"></a>Exemple : Copier des données à partir du serveur FTP dans blob Azure

Cet exemple montre comment copier des données à partir d’un serveur FTP dans un stockage d’objets Blob Azure. Toutefois, les données peuvent être copiées **directement** à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.  
 
L’exemple comporte les entités usine données suivantes :

- Un service de type [Serveur_ftp](#ftp-linked-service-properties)lié.
- Un service de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)lié.
- Un [jeu de données](data-factory-create-datasets.md) d’entrée de type de [partage de fichiers](#fileshare-dataset-type-properties).
- Un résultat [jeu de données](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- [Pipeline](data-factory-create-pipelines.md) avec une activité copie qui utilise [FileSystemSource](#ftp-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L’exemple copie les données à partir d’un serveur FTP à un blob Azure chaque heure. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples. 

**FTP lié service** Cet exemple utilise l’authentification de base avec nom d’utilisateur et mot de passe au format texte brut. Vous pouvez également utiliser une des façons suivantes : 

- Authentification anonyme 
- Authentification de base avec les informations d’identification chiffrées
- FTP sur SSL/TLS (FTP)

Consultez la section [FTP liées service](#ftp-linked-service-properties) pour différents types d’authentification, vous pouvez utiliser. 

    {
        "name": "FTPLinkedService",
        "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",          
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
      }
    }

**Service de stockage lié Azure**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Jeu de données d’entrée FTP** Ce dataset fait référence au dossier FTP `mysharedfolder` et fichier `test.csv`. Le pipeline de copie du fichier vers la destination. 

Définition de « externes » : « true » vous informe le service de données par défaut que le jeu de données externe à l’usine de données et n’est pas produit par une activité dans le factory de données.
    
    {
      "name": "FTPFileInput",
      "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
          "folderPath": "mysharedfolder",
          "fileName": "test.csv",
          "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }


**Jeu de données de sortie Blob Azure**

Les données sont écrites dans un nouveau blob toutes les heures (fréquence : heure, l’intervalle : 1). Le chemin du dossier pour le blob est évalué dynamiquement en fonction de l’heure de début de la section en cours de traitement. Le chemin du dossier utilise l’année, mois, jour et composants heures de l’heure de début.

    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
                "partitionedBy": [
                    {
                        "name": "Year",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy"
                        }
                    },
                    {
                        "name": "Month",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "MM"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "dd"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**Pipeline avec une activité copie**

Le pipeline contient une activité de copie qui est configuré pour utiliser les jeux de données d’entrée et de sortie et planifiée pour chaque heure. Dans le pipeline de définition de JSON, le type de **source** est défini sur **FileSystemSource** et **récepteur de** type est défini sur **BlobSink**. 
    
    {
        "name": "pipeline",
        "properties": {
            "activities": [{
                "name": "FTPToBlobCopy",
                "inputs": [{
                    "name": "FtpFileInput"
                }],
                "outputs": [{
                    "name": "AzureBlobOutput"
                }],
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }],
            "start": "2016-08-24T18:00:00Z",
            "end": "2016-08-24T19:00:00Z"
        }
    }

## <a name="ftp-linked-service-properties"></a>Propriétés du Service liées FTP

Le tableau suivant fournit la description pour les éléments JSON spécifiques à FTP Services liés.

| Propriété | Description | Obligatoire | Par défaut |
| -------- | ----------- | -------- | ------- | 
| type | La propriété doit être définie à Serveur_ftp | Oui | &nbsp;
| hôte | Nom ou adresse IP du serveur FTP | Oui | &nbsp;
| authenticationType | Spécifier le type d’authentification | Oui | Base et anonyme |
| nom d’utilisateur | Utilisateur qui a accès au serveur FTP | N° | &nbsp;
| mot de passe | Mot de passe pour l’utilisateur (nom d’utilisateur) | N° | &nbsp;
| encryptedCredential | Informations d’identification chiffrée pour accéder au serveur FTP | N° | &nbsp;
| nom de la passerelle | Nom de la passerelle passerelle de gestion des données pour vous connecter à un serveur FTP en local | N°    | &nbsp;
| port | Port sur lequel le serveur FTP écoute | N° | 21 |
| enableSsl | Spécifier si vous souhaitez utiliser FTP sur un canal SSL/TLS | N° | Vrai | 
| enableServerCertificateValidation | Spécifier si vous souhaitez activer la validation du certificat SSL serveur lorsque vous utilisez FTP sur un canal SSL/TLS | N° | Vrai | 

### <a name="using-anonymous-authentication"></a>À l’aide de l’authentification anonyme

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {     
                "authenticationType": "Anonymous",
                "host": "myftpserver.com"
            }
        }
    }

### <a name="using-username-and-password-in-plain-text-for-basic-authentication"></a>À l’aide du nom d’utilisateur et mot de passe au format texte brut pour l’authentification de base
    
    {
        "name": "FTPLinkedService",
        "properties": {
        "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",
                "username": "Admin",
                "password": "123456"
            }
        }
    }


### <a name="using-port-enablessl-enableservercertificatevalidation"></a>À l’aide de port, enableSsl, enableServerCertificateValidation

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",  
                "username": "Admin",
                "password": "123456",
                "port": "21",
                "enableSsl": true,
                "enableServerCertificateValidation": true
            }
        }
    }

### <a name="using-encryptedcredential-for-authentication-and-gateway"></a>À l’aide d’encryptedCredential pour l’authentification et de la passerelle

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",
                "encryptedCredential": "xxxxxxxxxxxxxxxxx",
                "gatewayName": "mygateway"
            }
        }
    }

Pour plus d’informations sur la définition des informations d’identification pour une source de données FTP en local, voir [définir ces informations et sécurité](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

[AZURE.INCLUDE [data-factory-file-share-dataset](../../includes/data-factory-file-share-dataset.md)]   
[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]   
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="ftp-copy-activity-type-properties"></a>Propriétés de type d’activité FTP copie

Pour une liste complète des sections et les propriétés disponibles pour la définition des activités, voir l’article [Création Pipelines](data-factory-create-pipelines.md) . Propriétés telles que nom, description, entrée et sortie des tables et les stratégies sont disponibles pour tous les types d’activités. 

Propriétés disponibles dans la section typeProperties de l’activité dépendent quant à eux de chaque type d’activité. Activité de copie, les propriétés de type varient selon les types de sources et les récepteurs.

[AZURE.INCLUDE [data-factory-file-system-source](../../includes/data-factory-file-system-source.md)]   

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Performances et réglage  
Voir [Guide d’optimisation et des performances d’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les principaux facteurs impact sur les performances de déplacement de données (copie activité) dans Azure Data Factory et de différentes manières d’optimiser.

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants : 

- [Activité de copie didacticiel](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour obtenir des instructions étape par étape pour la création d’un pipeline avec une activité de copie. 
