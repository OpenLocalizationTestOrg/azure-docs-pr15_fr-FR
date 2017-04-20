### <a name="compression-support"></a>Prise en charge de la compression  
Traitement de grands ensembles de données peut provoquer des engorgements e/s et du réseau. Par conséquent, les données compressées dans stores pouvant non seulement accélérer le transfert de données sur le réseau et économiser de l’espace disque, mais également apportent des améliorations de performances de manière significative dans le traitement des données volumineuses. Pour l’instant, banques de données orientés fichier tels que les objets Blob Azure ou de système de fichiers en local est prise en charge la compression.  

> [AZURE.NOTE] Paramètres de compression ne sont pas prises en charge pour les données dans le **AvroFormat**, **OrcFormat**ou **ParquetFormat**. 

Pour spécifier la compression d’un jeu de données, utilisez la propriété **compression** du DataSet JSON comme dans l’exemple suivant :   

    {  
        "name": "AzureBlobDataSet",  
        "properties": {  
            "availability": {  
                "frequency": "Day",  
                "interval": 1  
            },  
            "type": "AzureBlob",  
            "linkedServiceName": "StorageLinkedService",  
            "typeProperties": {  
                "fileName": "pagecounts.csv.gz",  
                "folderPath": "compression/file/",  
                "compression": {  
                    "type": "GZip",  
                    "level": "Optimal"  
                }  
            }  
        }  
    }  
 
La section **compression** comporte deux propriétés :  
  
- **Type :** le codec de compression, qui peut être **GZIP**, **Deflate** ou **BZIP2**.  
- **Niveau :** le taux de compression, qui peut être **optimale** ou **plus rapide**. 
    - **Plus rapide :** L’opération de compression doit se terminer aussi rapidement que possible, même si le fichier qui en résulte n’est pas optimale compressé. 
    - **Optimale**: l’opération de compression doit être optimale compressée, même si l’opération prend plus de temps. 
    
    Pour plus d’informations, voir la rubrique [Niveau de Compression](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . 

Supposons que le jeu de données d’exemple ci-dessus est utilisée comme la sortie d’une activité de copie, l’activité de copie compresse les données de sortie avec codec GZIP à l’aide de rapport optimal et puis écrire les données compressées dans un fichier nommé pagecounts.csv.gz dans le stockage Blob Azure.   

Lorsque vous spécifiez propriété compression dans un jeu de données d’entrée JSON, le pipeline peut lire les données compressées provenant de la source et lorsque vous spécifiez la propriété dans un jeu de données de sortie JSON, l’activité de copie pouvez écrire des données compressées vers la destination. Voici quelques exemples de scénarios : 

- Lecture GZIP compressées les données à partir d’un blob Azure, décompresser et écrire des données de résultat dans une base de données SQL Azure. Vous définissez le dataset Blob Azure d’entrée avec la compression propriété JSON dans ce cas. 
- Lire les données d’un fichier texte brut du système de fichiers en local, à l’aide du format GZip compresser et écrire les données compressées dans un blob Azure. Vous définissez un jeu de données Azure Blob sortie avec la compression propriété JSON dans ce cas.  
- Lire un compressés GZIP de données à partir d’un blob Azure, décompresser, à l’aide de BZIP2 compresser et écrire des données de résultat dans un blob Azure. Vous définissez le jeu de données Blob Azure d’entrée avec le type de compression défini sur GZIP et le jeu de données de sortie avec le type de compression défini sur BZIP2 dans ce cas.   
