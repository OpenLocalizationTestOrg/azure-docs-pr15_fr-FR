## <a name="azure-storage-linked-service"></a>Stockage Azure lié Service

Le **stockage Azure lié service** permet de lier un compte de stockage Azure à une usine de données Azure à l’aide de la **clé de compte**. Ainsi, le factory de données avec accès global au stockage Azure. Le tableau suivant fournit la description pour les éléments JSON spécifiques au service de stockage Azure lié.

| Propriété | Description | Obligatoire |
| :-------- | :----------- | :-------- |
| type | La propriété doit être définie : **AzureStorage** | Oui |
| connectionString | Indiquez les informations nécessaires pour vous connecter au stockage Azure pour la propriété connectionString. | Oui |

Consultez l’article suivant les procédures d’affichage/copiez la clé de compte pour un stockage Azure : [Affichage, copie et stockage régénérer les touches d’accès](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

**Exemple :**  
  
    {  
        "name": "StorageLinkedService",  
        "properties": {  
            "type": "AzureStorage",  
            "typeProperties": {  
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
            }  
        }  
    }  


## <a name="azure-storage-sas-linked-service"></a>Stockage Azure sa lié Service  
Une signature accès partagé (sa) offre un accès délégué aux ressources dans votre compte de stockage. Cela signifie que vous pouvez accorder qu'un client des autorisations limitées aux objets de votre compte de stockage pour une période spécifiée de temps et avec un ensemble spécifique d’autorisations, sans avoir à partager les touches d’accès de votre compte. Les associations de sécurité sont un URI qui englobe dans ses paramètres de la requête toutes les informations nécessaires pour un accès à une ressource de stockage authentifié. Pour accéder à des ressources de stockage avec les associations de sécurité, le client doit passer à la méthode ou constructeur approprié dans les associations de sécurité. Pour plus d’informations sur les associations de sécurité, voir [partagés accès Signatures : présentation du modèle de sa](../articles/storage/storage-dotnet-shared-access-signature-part-1.md)
  
Le service associations de sécurité de stockage Azure lié permet de lier un compte de stockage Azure à une usine de données Azure à l’aide d’une Signature de Access partagé (sa). Ainsi, le factory de données avec l’accès restreint/liés au temps/spécifiques à toutes les ressources (blob/conteneur) dans le stockage. Le tableau suivant fournit la description pour les éléments JSON spécifiques au service associations de sécurité de stockage Azure lié. 

| Propriété | Description | Obligatoire |
| :-------- | :----------- | :-------- |
| type | La propriété doit être définie : **AzureStorageSas**  | Oui |
| sasUri | Spécifiez partagés accès Signature URI pour les ressources de stockage Azure comme blob, conteneur ou table. Consultez les remarques ci-après pour plus d’informations. | Oui | 


**Exemple :**
  
    {  
        "name": "StorageSasLinkedService",  
        "properties": {  
            "type": "AzureStorageSas",  
            "typeProperties": {  
                "sasUri": "<storageUri>?<sasToken>"   
            }  
        }  
    }  

Lorsque vous créez un **URI associations de sécurité**, envisagez les éléments suivants :  

- Azure Data Factory prend en charge uniquement **Les associations de sécurité Service**, pas les associations de sécurité compte. Pour plus d’informations sur ces deux types, consultez [Types de Signatures accès partagé](../articles/storage/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) .
- Vous avez besoin des **autorisations** en lecture/écriture appropriées puissent être définis sur les objets basés sur l’utilisation du service lié (lecture, écriture, en lecture/écriture) dans votre usine de données.
- **Délai d’expiration** doit être définis correctement. Assurez-vous que l’accès aux objets de stockage Azure n’expire pas au sein de la période active du pipeline.
- URI doit être créée au niveau du conteneur droite/blob ou Table selon qu’il soit nécessaire. Uri associations de sécurité d’un blob Azure permet au service de données usine accéder à ce blob particulier. Un Uri associations de sécurité à un conteneur blob Azure permet au service de données par défaut parcourir les objets BLOB dans ce conteneur. Si vous devez fournir un accès aux objets plus/moins ultérieurement, ou mettre à jour l’URI SAS, n’oubliez pas de mettre à jour le service lié avec le nouvel URI.   
