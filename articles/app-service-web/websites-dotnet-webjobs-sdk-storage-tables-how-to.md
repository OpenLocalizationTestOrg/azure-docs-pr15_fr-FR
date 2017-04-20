<properties 
    pageTitle="L’utilisation de stockage de table Azure avec WebJobs SDK" 
    description="Apprenez à utiliser le stockage de table Azure avec WebJobs SDK. Créer des tables, ajouter des entités aux tables et lire les tableaux existants." 
    services="app-service\web, storage" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="how-to-use-azure-table-storage-with-the-webjobs-sdk"></a>L’utilisation de stockage de table Azure avec WebJobs SDK

## <a name="overview"></a>Vue d’ensemble

Ce guide fournit des exemples de code c# qui montrent comment lire et écrire des tables de stockage Azure à l’aide [WebJobs SDK](websites-dotnet-webjobs-sdk.md) version 1.x.

Part du principe que vous savez [comment créer un projet WebJob dans Visual Studio avec les chaînes de connexion qui pointent vers votre compte de stockage](websites-dotnet-webjobs-sdk-get-started.md) ou entre [plusieurs comptes de stockage](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs).
        
Parmi l’extraits de code montrent la `Table` attribut utilisé dans des fonctions qui sont [appelés manuellement](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual), c'est-à-dire, pas en utilisant l’un des attributs déclencheur. 

## <a id="ingress"></a>Comment ajouter des entités à une table

Pour ajouter des entités dans un tableau, utilisez la `Table` attribuer avec un `ICollector<T>` ou `IAsyncCollector<T>` paramètre où `T` Spécifie le schéma des entités que vous voulez ajouter. Le constructeur attribut prend un paramètre de chaîne qui spécifie le nom de la table. 

L’exemple de code suivant ajoute `Person` entités dans une table nommée *pénétration*.

        [NoAutomaticTrigger]
        public static void IngressDemo(
            [Table("Ingress")] ICollector<Person> tableBinding)
        {
            for (int i = 0; i < 100000; i++)
            {
                tableBinding.Add(
                    new Person() { 
                        PartitionKey = "Test", 
                        RowKey = i.ToString(), 
                        Name = "Name" }
                    );
            }
        }

En général le type que vous utilisez avec `ICollector` dérivée de `TableEntity` ou mettent en œuvre, `ITableEntity`, mais n’a pas besoin. Une des opérations suivantes `Person` classes travail avec le code indiqué dans le précédent `Ingress` méthode.

        public class Person : TableEntity
        {
            public string Name { get; set; }
        }

        public class Person
        {
            public string PartitionKey { get; set; }
            public string RowKey { get; set; }
            public string Name { get; set; }
        }

Si vous voulez travailler directement avec l’API de stockage Azure, vous pouvez ajouter un `CloudStorageAccount` paramètre à la signature de méthode.

## <a id="monitor"></a>Surveillance en temps réel

Fonctions de pénétration de données traitant souvent grands volumes de données, le tableau de bord WebJobs SDK fournit des données d’analyse en temps réel. La section du **Journal d’appel** vous indique si la fonction est toujours en cours d’exécution.

![Fonction pénétration en cours d’exécution](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressrunning.png)

La page **Détails de l’appel** signale la progression de la fonction (nombre d’entités écrit) pendant qu’il est en cours d’exécution et vous donne la possibilité pour l’abandonner. 

![Fonction pénétration en cours d’exécution](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressprogress.png)

Lorsque la fonction est terminée, la page **Détails de l’appel** indique le nombre de lignes écrites.

![Fonction pénétration terminée](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingresssuccess.png)

## <a id="multiple"></a>Comment lire plusieurs entités à partir d’un tableau

Pour lire un tableau, utilisez la `Table` attribuer avec un `IQueryable<T>` paramètre où taper `T` dérivée de `TableEntity` ou mettent en œuvre, `ITableEntity`.

L’exemple de code suivant lit et enregistre toutes les lignes de la `Ingress` table :
 
        public static void ReadTable(
            [Table("Ingress")] IQueryable<Person> tableBinding,
            TextWriter logger)
        {
            var query = from p in tableBinding select p;
            foreach (Person person in query)
            {
                logger.WriteLine("PK:{0}, RK:{1}, Name:{2}", 
                    person.PartitionKey, person.RowKey, person.Name);
            }
        }

### <a id="readone"></a>Comment lire une seule entité à partir d’un tableau

Il y a un `Table` constructeur d’attribut avec deux paramètres supplémentaires qui vous permettent de spécifier les touches de ligne et partition lorsque vous souhaitez lier à une seule table entité.

L’exemple de code suivant lit une ligne de tableau pour une `Person` entité basé sur partition clé et ligne valeurs clés reçus dans un message file d’attente :  

        public static void ReadTableEntity(
            [QueueTrigger("inputqueue")] Person personInQueue,
            [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
            TextWriter logger)
        {
            if (personInTable == null)
            {
                logger.WriteLine("Person not found: PK:{0}, RK:{1}",
                        personInQueue.PartitionKey, personInQueue.RowKey);
            }
            else
            {
                logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
                        personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
            }
        }


La `Person` classe dans cet exemple n’a pas à mettre en œuvre `ITableEntity`.

## <a id="storageapi"></a>Comment utiliser l’API de stockage .NET directement avec un tableau

Vous pouvez également utiliser la `Table` attribuer avec un `CloudTable` objet pour plus de flexibilité utilisation d’une table.

Exemples d’utilisation de code suivantes un `CloudTable` objet pour ajouter une seule entité à la table *pénétration* . 
 
        public static void UseStorageAPI(
            [Table("Ingress")] CloudTable tableBinding,
            TextWriter logger)
        {
            var person = new Person()
                {
                    PartitionKey = "Test",
                    RowKey = "100",
                    Name = "Name"
                };
            TableOperation insertOperation = TableOperation.Insert(person);
            tableBinding.Execute(insertOperation);
        }

Pour plus d’informations sur l’utilisation de la `CloudTable` objet, voir [comment utiliser le stockage de Table à partir de .NET](../storage/storage-dotnet-how-to-use-tables.md). 

## <a id="queues"></a>Rubriques connexes couverts par l’article d’apprentissage files d’attente

Pour plus d’informations sur la façon de traiter les traitement de tableau qui s’affiche suite à un message file d’attente, ou pour les scénarios WebJobs SDK non spécifiques au traitement de table, voir [comment utiliser le stockage Azure file d’attente avec le Kit de développement WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Rubriques traitées dans cet article sont les suivants :

* Fonctions asynchrones
* Plusieurs instances
* Arrêt
* Utiliser les attributs WebJobs SDK dans le corps d’une fonction
* Définir les chaînes de connexion SDK dans le code
* Définir les valeurs pour WebJobs SDK paramètres constructeur dans le code
* Déclencher manuellement une fonction
* Écrire des journaux

## <a id="nextsteps"></a>Étapes suivantes

Ce guide a fourni des exemples de code qui montrent comment gérer des scénarios courants pour l’utilisation de tables Azure. Pour plus d’informations sur l’utilisation d’Azure WebJobs et WebJobs SDK, voir [Azure WebJobs recommandé de ressources](http://go.microsoft.com/fwlink/?linkid=390226).
 
