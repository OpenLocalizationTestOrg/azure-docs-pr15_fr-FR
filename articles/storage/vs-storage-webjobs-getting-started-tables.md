<properties
    pageTitle="Prise en main de stockage Azure et Visual Studio connecté services (WebJob projets)"
    description="Comment procéder à l’aide de stockage de Table Azure dans un projet WebJobs Azure dans Visual Studio après vous être connecté à un compte de stockage à l’aide de Visual Studio services connectés"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Prise en main Azure stockage (WebJob Azure projets)

[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Vue d’ensemble

Cet article fournit des exemples de code c# qui montrent montrent comment utiliser la version Azure WebJobs SDK 1.x avec le service de stockage de table Azure. Les exemples de code utilisent la version [SDK WebJobs](../app-service-web/websites-dotnet-webjobs-sdk.md) 1.x.

Le service de stockage de Table Azure vous permet de stocker de grandes quantités de données structurées. Le service est un magasin de données NoSQL qui accepte les appels authentifiés, à l’intérieur et à l’extérieur du cloud Azure. Tables Azure sont parfaites pour le stockage de données structurées et non relationnelles.  Pour plus d’informations, voir [prise en main stockage de Table Azure à l’aide de .NET](storage-dotnet-how-to-use-tables.md#create-a-table) .

Certains extraits de code afficher l’attribut **Table** utilisé dans des fonctions qui sont appelées manuellement, c'est-à-dire, ne pas en utilisant l’un des attributs déclencheur.

## <a name="how-to-add-entities-to-a-table"></a>Comment ajouter des entités à une table

Pour ajouter des entités à une table, utilisez l’attribut **Table** avec une **ICollector<T> ** ou **IAsyncCollector<T> ** paramètre où **T** Spécifie le schéma des entités que vous voulez ajouter. Le constructeur attribut prend un paramètre de chaîne qui spécifie le nom de la table.

L’exemple de code suivant ajoute entités **personne** à une table nommée *pénétration*.

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

En général le type que vous utilisez avec **ICollector** dérivé de **TableEntity** ou mettent en œuvre, **ITableEntity**, mais n’a pas besoin. Une des classes **personne** suivantes fonctionnent avec le code indiqué dans la méthode de **pénétration** précédente.

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

Si vous souhaitez travailler directement avec l’API de stockage Azure, vous pouvez ajouter un paramètre **CloudStorageAccount** à la signature de méthode.

## <a name="real-time-monitoring"></a>Surveillance en temps réel

Fonctions de pénétration de données traitant souvent grands volumes de données, le tableau de bord WebJobs SDK fournit des données d’analyse en temps réel. La section du **Journal d’appel** vous indique si la fonction est toujours en cours d’exécution.

![Fonction pénétration en cours d’exécution](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

La page **Détails de l’appel** signale la progression de la fonction (nombre d’entités écrit) pendant qu’il est en cours d’exécution et vous donne la possibilité pour l’abandonner.

![Fonction pénétration en cours d’exécution](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Lorsque la fonction est terminée, la page **Détails de l’appel** indique le nombre de lignes écrites.

![Fonction pénétration terminée](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Comment lire plusieurs entités à partir d’un tableau

Pour lire un tableau, utilisez l’attribut **Table** avec une **IQueryable<T> ** paramètre où type **T** dérivé **TableEntity** ou mettent en œuvre, **ITableEntity**.

L’exemple de code suivant lit et enregistre toutes les lignes de la table **entrée** :

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

### <a name="how-to-read-a-single-entity-from-a-table"></a>Comment lire une seule entité à partir d’un tableau

Il existe un constructeur d’attribut **Table** avec deux paramètres supplémentaires qui vous permettent de spécifier les touches de ligne et partition lorsque vous souhaitez lier à une seule table entité.

L’exemple de code suivant lit une ligne de tableau pour une entité **personne** basée sur partition clé et ligne valeurs clés reçues dans un message file d’attente :  

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


Dans cet exemple, la classe **personne** n’a pas à mettre en œuvre **ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Comment utiliser l’API de stockage .NET directement avec un tableau

Vous pouvez également utiliser l’attribut **Table** avec un objet **CloudTable** pour plus de flexibilité utilisation d’une table.

L’exemple de code suivant utilise un objet **CloudTable** pour ajouter une seule entité à la table *pénétration* .

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

Pour plus d’informations sur l’utilisation de l’objet **CloudTable** , voir [prise en main stockage de Table Azure à l’aide de .NET](storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Rubriques connexes couverts par l’article d’apprentissage files d’attente

Pour plus d’informations sur la façon de traiter les traitement de tableau qui s’affiche suite à un message file d’attente, ou pour les scénarios WebJobs SDK non spécifiques au traitement de table, voir [que mise en route d’espace de stockage Azure file d’attente et Visual Studio services connectés (WebJob projets)](vs-storage-webjobs-getting-started-queues.md).



## <a name="next-steps"></a>Étapes suivantes

Cet article fournit des exemples de code qui montrent comment gérer des scénarios courants pour l’utilisation de tables Azure. Pour plus d’informations sur l’utilisation d’Azure WebJobs et WebJobs SDK, consultez [ressources de documentation WebJobs Azure](http://go.microsoft.com/fwlink/?linkid=390226).
