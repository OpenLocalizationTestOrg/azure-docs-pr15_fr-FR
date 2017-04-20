<properties
    pageTitle="Sérialiser des données avec Microsoft Avro Library | Microsoft Azure"
    description="Découvrez comment Azure HDInsight utilise Avro pour sérialiser des données volumineux."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="jgao"/>


# <a name="serialize-data-in-hadoop-with-the-microsoft-avro-library"></a>Sérialiser des données dans Hadoop avec Microsoft Avro Library

Cette rubrique vous explique comment utiliser la <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Bibliothèque Avro Microsoft</a> pour sérialiser des objets et autres structures de données dans des flux afin de les conserver en mémoire, une base de données ou un fichier, mais également comment désérialiser les pour récupérer les objets d’origine.

[AZURE.INCLUDE [windows-only](../../includes/hdinsight-windows-only.md)]

##<a name="apache-avro"></a>Apache Avro
La <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Bibliothèque Avro Microsoft</a> met en œuvre le système de sérialisation Apache Avro données pour l’environnement Microsoft.NET. Apache Avro fournit un format d’échange de données binaires compact pour la sérialisation. Il utilise <a href="http://www.json.org" target="_blank">JSON</a> pour définir un schéma indépendant du langage qui souscrit interopérabilité entre les langages. Données sérialisées dans une langue peuvent être lus dans un autre. Actuellement C, C++, c#, Java, PHP, Python et Ruby sont prises en charge. Vous trouverez des informations détaillées sur la mise en forme dans la <a href="http://avro.apache.org/docs/current/spec.html" target="_blank">Spécification de Avro Apache</a>. Notez que la version actuelle de Microsoft Avro Library ne prend pas en charge la procédure distante (RPC) partie de cette spécification des appels.

La représentation série d’un objet dans le système Avro se compose de deux parties : schéma et une valeur réelle. Le schéma Avro décrit le modèle de données indépendamment de la langue des données séries avec JSON. Il est présent côte à côte avec une représentation binaire des données. Ayant le schéma distinct de la représentation binaire permet à chaque objet soient écrits avec aucune transparents par valeur, effectuer sérialisation rapidement la représentation petite.

##<a name="the-hadoop-scenario"></a>Le scénario Hadoop
Le format de sérialisation Avro Apache est largement utilisé dans les autres environnements Hadoop Apache et Azure HDInsight. Avro offre un moyen pratique pour représenter des structures de données complexes au sein d’un travail Hadoop MapReduce. Le format des fichiers Avro (fichier du conteneur objet Avro) a été conçu pour prendre en charge le modèle de programmation MapReduce distribué. La fonctionnalité clé qui permet de la distribution est que les fichiers sont « fractionnables » dans le sens qu’il est possible de recherche n’importe quel point dans un fichier et démarrer la lecture à partir d’un bloc particulier.

##<a name="serialization-in-avro-library"></a>Sérialisation dans une bibliothèque de Avro
La bibliothèque .NET pour Avro prend en charge deux façons de sérialisation d’objets :

- **réflexion** - schéma le JSON pour les types de créé automatiquement à partir des données les attributs de contrat des types .NET doit être sérialisé.
- **enregistrement générique** - schéma JSON A explicitement spécifié dans un enregistrement représenté par la classe [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) lorsqu’aucun type .NET n’est présents pour décrire le schéma pour les données à sérialiser.

Lorsque vous connaissez le schéma de données pour le writer et la lecture du flux de données, les données peuvent être envoyées sans son schéma. Dans les cas lorsqu’un fichier Avro objet conteneur est utilisé, le schéma est stocké dans le fichier. D’autres paramètres, tels que le codec utilisé pour la compression des données, peuvent être spécifiés. Ces scénarios sont décrites plus en détail et illustrées dans les exemples de code ci-dessous.


## <a name="install-avro-library"></a>Installer Avro bibliothèque

Les éléments suivants sont nécessaires avant d’installer la bibliothèque :

- <a href="http://www.microsoft.com/download/details.aspx?id=17851" target="_blank">Microsoft .NET Framework 4</a>
- <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a> (6.0.4 ou version ultérieure)

Notez que la dépendance Newtonsoft.Json.dll est téléchargée automatiquement à l’installation de Microsoft Avro Library. La procédure pour cela est indiquée dans la section suivante.


Microsoft Avro Library est distribué sous forme de package NuGet qui peut être installé à partir de Visual Studio via la procédure suivante :

1. Sélectionnez l’onglet **projet** -> **Gérer les Packages NuGet...**
2. Recherchez « Microsoft.Hadoop.Avro » dans la zone de **Recherche en ligne** .
3. Cliquez sur le bouton **installer** en regard de **Microsoft Azure HDInsight Avro de la bibliothèque**.

Notez que la Newtonsoft.Json.dll (> = 6.0.4) dépendance est également téléchargé automatiquement avec Microsoft Avro Library.

Vous voudrez peut-être visitez la <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">page d’accueil Microsoft Avro bibliothèque</a> pour lire les notes de publication.


Le code source Microsoft Avro Library est disponible dans la <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">page d’accueil Microsoft Avro bibliothèque</a>.

##<a name="compile-schemas-using-avro-library"></a>Compiler les schémas à l’aide de la bibliothèque Avro

Microsoft Avro Library contient un utilitaire de génération de code qui permet de créer des types c# automatiquement basés sur le schéma JSON défini précédemment. L’utilitaire de génération de code n’est pas distribué comme un fichier exécutable binaire, mais peut être facilement créé via la procédure suivante :

1. Téléchargez le fichier .zip avec la dernière version de code source HDInsight SDK à partir de <a href="http://hadoopsdk.codeplex.com/SourceControl/latest#" target="_blank">Microsoft .NET SDK pour Hadoop</a>. (Cliquez sur l’icône **Télécharger** , pas l’onglet **téléchargements** .)

2. Extraire le SDK HDInsight dans un répertoire sur l’ordinateur avec .NET Framework 4 installé et connecté à Internet pour le téléchargement des packages NuGet dépendance nécessaire. Ci-dessous suppose que le code source est extraite vers C:\SDK.

3. Accédez au dossier C:\SDK\src\Microsoft.Hadoop.Avro.Tools et exécutez build.bat. (Le fichier est appelle MSBuild à partir de la distribution de la version 32 bits du .NET Framework. Si vous voulez utiliser la version 64 bits, modifier build.bat, suivant les commentaires dans le fichier.) Assurez-vous que la génération a réussi. (Sur certains systèmes, MSBuild peut-être produire des avertissements. Ces avertissements ne concernent pas l’utilitaire tant qu’il n’existe aucune erreur de génération.)

4. L’utilitaire compilée se trouve dans C:\SDK\Bin\Unsigned\Release\Microsoft.Hadoop.Avro.Tools.


Pour vous familiariser avec la syntaxe de la ligne de commande, exécutez la commande suivante à partir du dossier dans lequel se trouve l’utilitaire de génération de code :`Microsoft.Hadoop.Avro.Tools help /c:codegen`

Pour tester l’utilitaire, vous pouvez générer des classes c# à partir de l’exemple de fichier de schéma JSON fourni avec le code source. Exécutez la commande suivante :

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

Il est supposé pour produire deux fichiers c# dans le répertoire actif : SensorData.cs et Location.cs.

Pour mieux comprendre la logique à l’aide de l’utilitaire de génération de code lors de la conversion du schéma JSON aux types c#, consultez le fichier que generationverification.Feature situé dans C:\SDK\src\Microsoft.Hadoop.Avro.Tools\Doc.

Notez que les espaces de noms sont extraites du schéma JSON, à l’aide de la logique décrite dans le fichier mentionné dans le paragraphe précédent. Espaces de noms extraites du schéma ont la priorité sur tout ce qui est fourni avec le paramètre /n dans la ligne de commande utilitaire. Si vous voulez remplacer les espaces de noms contenus dans le schéma, utilisez le paramètre /nf. Par exemple, pour transformer tous les espaces de noms à partir de la SampleJSONSchema.avsc my.own.nspace, exécutez la commande suivante :

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

## <a name="samples"></a>Exemples
Six fournies dans cette rubrique illustre différents scénarios pris en charge par Microsoft Avro Library. Microsoft Avro Library est conçu pour fonctionner avec n’importe quel flux. Dans ces exemples, manipulation de données via les flux de mémoire plutôt que de flux de fichiers ou bases de données pour simplifier et la cohérence. L’approche suivie dans un environnement de production dépendent de la configuration requise de scénario précis, source de données et en volume, des contraintes de performance et d’autres facteurs.

Les deux premiers exemples illustrent comment sérialiser et désérialiser des données en mémoire tampon de flux de données à l’aide de réflexion et enregistrements génériques. Le schéma dans ces deux cas est censé être partagé entre les lecteurs et les auteurs de hors.

Les exemples de troisième et quatrième montrent comment sérialiser et désérialiser des données en utilisant les fichiers Avro objet conteneur. Lorsque les données sont stockées dans un fichier de conteneur Avro, son schéma est toujours stocké avec lui, car le schéma doit être partagé pour la désérialisation.

L’échantillon contenant les quatre premiers exemples peut être téléchargé à partir du site <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-86055923" target="_blank">d’exemples de code Azure</a> .

L’exemple cinquième montre comment l’utilisation d’un codec compression personnalisé pour les fichiers de conteneur Avro objet. Un exemple contenant le code de cet exemple peut être téléchargé à partir du site <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111" target="_blank">d’exemples de code Azure</a> .

L’échantillon sixième montre comment utiliser la sérialisation Avro pour télécharger des données sur le stockage d’objets Blob Azure et analyser puis en utilisant Hive avec un cluster HDInsight (Hadoop). Il peut être téléchargé à partir du site <a href="https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3" target="_blank">d’exemples de code Azure</a> .

Voici des liens vers les exemples de six décrits dans la rubrique :

 * <a href="#Scenario1">**Sérialisation avec la réflexion**</a> - schéma JSON The des types doit être sérialisé créé automatiquement à partir des données les attributs de contrat.
 * <a href="#Scenario2">**Sérialisation avec enregistrement générique**</a> - JSON le schéma est explicitement spécifié dans un enregistrement lorsqu’aucun type .NET n’est disponible pour la réflexion.
 * <a href="#Scenario3">**Sérialisation à l’aide des fichiers du conteneur objet avec la réflexion**</a> - schéma JSON l’est automatiquement créé et partagé avec la série de données via un fichier Avro objet conteneur.
 * <a href="#Scenario4">**Sérialisation à l’aide des fichiers du conteneur objet avec enregistrement générique**</a> - schéma le JSON est explicitement spécifié avant la sérialisation et partagées avec les données via un fichier Avro objet conteneur.
 * <a href="#Scenario5">**Sérialisation à l’aide des fichiers du conteneur objet avec un codec compression personnalisé**</a> - l’exemple montre comment créer un fichier Avro objet conteneur avec une implémentation .NET personnalisée du codec de compression de données Deflate.
 * <a href="#Scenario6">**Avro à l’aide de télécharger des données pour le service Microsoft Azure HDInsight**</a> - l’exemple illustre comment Avro sérialisation interagit avec le service HDInsight. Un abonnement Azure active et l’accès à un cluster Azure HDInsight sont requis pour exécuter cet exemple.

###<a name="Scenario1"></a>Exemple 1 : Sérialisation avec réflexion

Le schéma JSON pour les types de peut être automatiquement créé par Microsoft Avro Library via réflexion à partir des données des attributs de contrat des objets c# doit être sérialisé. Microsoft Avro Library crée un [**IAvroSeralizer<T> **](http://msdn.microsoft.com/library/dn627341.aspx) d’identifier les champs doit être sérialisé.

Dans cet exemple, les objets (une classe **SensorData** avec une structure **emplacement** membre) sont sérialisés dans un flux de mémoire, et ce flux de données est désérialisé à son tour. Le résultat est alors comparé à l’instance initiale pour confirmer que l’objet **SensorData** récupéré est identique à l’original.

Le schéma dans cet exemple est supposé égal à partager entre les lecteurs et les auteurs, le format de conteneur objet Avro n’est pas nécessaire. Pour obtenir un exemple montrant comment sérialiser et désérialiser des données en mémoire tampon à l’aide de réflexion avec le format de conteneur objet lorsque le schéma doit être partagé avec les données, voir <a href="#Scenario3">sérialisation à l’aide des fichiers du conteneur objet avec la réflexion</a>.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serialize and deserialize sample data set represented as an object using reflection.
            //No explicit schema definition is required - schema of serialized objects is automatically built.
            public void SerializeDeserializeObjectUsingReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION\n");
                Console.WriteLine("Serializing Sample Data Set...");

                //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
                //for serializing only properties attributed with DataContract/DateMember
                var avroSerializer = AvroSerializer.Create<SensorData>();

                //Create a memory stream buffer
                using (var buffer = new MemoryStream())
                {
                    //Create a data set by using sample class and struct
                    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };

                    //Serialize the data to the specified stream
                    avroSerializer.Serialize(buffer, expected);


                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Deserialize data from the stream and cast it to the same type used for serialization
                    var actual = avroSerializer.Deserialize(buffer);

                    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                    //Finally, verify that deserialized data matches the original one
                    bool isEqual = this.Equal(expected, actual);

                    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);

                }
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }



            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization to memory using reflection
                Sample.SerializeDeserializeObjectUsingReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###<a name="sample-2-serialization-with-a-generic-record"></a>Exemple 2 : Sérialisation avec un enregistrement générique

Un schéma JSON peut être explicitement spécifié dans un enregistrement générique lors de la réflexion ne peuvent pas être utilisée, car les données ne peut pas être représentées par le biais de classes .NET avec un contrat de données. Cette méthode est généralement plus lente qu’avec réflexion. Dans ce cas, le schéma pour les données peuvent également être dynamiques, c'est-à-dire, ne pas être connu au moment de la compilation. Données représentées sous forme de fichiers de valeurs séparées par des virgules (CSV) dont le schéma est inconnu jusqu'à ce qu’il est transformé au format Avro en cours d’exécution sont un exemple de ce type de scénario dynamique.

Cet exemple montre comment créer et utiliser un [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) pour spécifier explicitement un schéma JSON, comment faire pour remplir avec les données, puis comment sérialiser et désérialiser. Le résultat est alors comparé à l’instance initiale pour confirmer que l’enregistrement récupéré est identique à l’original.

Le schéma dans cet exemple est supposé égal à partager entre les lecteurs et les auteurs, le format de conteneur objet Avro n’est pas nécessaire. Pour obtenir un exemple montrant comment sérialiser et désérialiser des données en mémoire tampon à l’aide d’un enregistrement générique avec le format de conteneur objet lorsque le schéma doit être inclus dans les données de série, voir l’exemple <a href="#Scenario4">sérialisation à l’aide des fichiers du conteneur objet avec enregistrement générique</a> .


    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    using Microsoft.Hadoop.Avro;

    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {

        //Serialize and deserialize sample data set by using a generic record.
        //A generic record is a special class with the schema explicitly defined in JSON.
        //All serialized data should be mapped to the fields of the generic record,
        //which in turn will be then serialized.
        public void SerializeDeserializeObjectUsingGenericRecords()
        {
            Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
            Console.WriteLine("Defining the Schema and creating Sample Data Set...");

            //Define the schema in JSON
            const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

            //Create a generic serializer based on the schema
            var serializer = AvroSerializer.CreateGeneric(Schema);
            var rootSchema = serializer.WriterSchema as RecordSchema;

            //Create a memory stream buffer
            using (var stream = new MemoryStream())
            {
                //Create a generic record to represent the data
                dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location.Floor = 1;
                location.Room = 243;

                dynamic expected = new AvroRecord(serializer.WriterSchema);
                expected.Location = location;
                expected.Value = new byte[] { 1, 2, 3, 4, 5 };

                Console.WriteLine("Serializing Sample Data Set...");

                //Serialize the data
                serializer.Serialize(stream, expected);

                stream.Seek(0, SeekOrigin.Begin);

                Console.WriteLine("Deserializing Sample Data Set...");

                //Deserialize the data into a generic record
                dynamic actual = serializer.Deserialize(stream);

                Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                //Finally, verify the results
                bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
                isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
                isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
                Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
            }
        }

        static void Main()
        {

            string sectionDivider = "---------------------------------------- ";

            //Create an instance of AvroSample class and invoke methods
            //illustrating different serializing approaches
            AvroSample Sample = new AvroSample();

            //Serialization to memory using generic record
            Sample.SerializeDeserializeObjectUsingGenericRecords();

            Console.WriteLine(sectionDivider);
            Console.WriteLine("Press any key to exit.");
            Console.Read();
        }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###<a name="sample-3-serialization-using-object-container-files-and-serialization-with-reflection"></a>Exemple 3 : Sérialisation à l’aide des fichiers du conteneur objet et sérialisation avec réflexion

Cet exemple est semblable au scénario dans <a href="#Scenario1">premier exemple</a>, où le schéma est implicitement spécifié avec la réflexion. La différence est qu’ici, le schéma n'est pas supposé être connu au lecteur qui désérialise. Les objets **SensorData** doit être sérialisé et leur schéma implicitement spécifié sont stockés dans un fichier de conteneur objet Avro représenté par la classe [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) .

Les données sont sérialisées dans cet exemple avec [**SequentialWriter<SensorData> **](http://msdn.microsoft.com/library/dn627340.aspx) et désérialisés avec [**SequentialReader<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx). Le résultat est alors comparé aux instances initiales afin d’identité.

Les données dans le fichier conteneur objet sont compressées via la valeur par défaut [**Deflate**] [ deflate-100] codec de compression de .NET Framework 4. Consultez l' <a href="#Scenario5">exemple cinquième</a> dans cette rubrique pour apprendre à utiliser une version plus récente et supérieure de la [**Deflate**] [ deflate-110] codec de compression disponible dans .NET Framework 4.5.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes the sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the Deflate codec.
            public void SerializeDeserializeUsingObjectContainersReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data will be compressed using the Deflate codec.
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true)))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            bool isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###<a name="sample-4-serialization-using-object-container-files-and-serialization-with-generic-record"></a>Exemple 4 : Sérialisation à l’aide des fichiers du conteneur objet et sérialisation avec enregistrement générique

Cet exemple est semblable au scénario dans <a href="#Scenario2">second exemple</a>, où le schéma est explicitement spécifié avec JSON. La différence est qu’ici, le schéma n'est pas supposé être connu au lecteur qui désérialise.

Le jeu de données de test est collecté dans une liste d’objets [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) via un schéma JSON défini explicitement et ensuite stocké dans un fichier de conteneur objet représenté par la classe [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) . Ce fichier conteneur crée un writer qui est utilisé pour sérialiser les données, décompressées dans un flux de mémoire qui est enregistré dans un fichier. Le paramètre [**Codec.Null**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.codec.null.aspx) utilisé pour la création du lecteur indique que ces données ne seront pas compressées.

Les données sont ensuite lire à partir du fichier et désérialisées dans une collection d’objets. Cette collection est comparée à la liste initiale des enregistrements Avro pour vérifier qu’elles sont identiques.


    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro.Schema;
        using Microsoft.Hadoop.Avro;

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes a sample data set by using a generic record and Avro object container files.
            //Serialized data is not compressed.
            public void SerializeDeserializeUsingObjectContainersGenericRecord()
            {
                Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleGenericRecordNullCodec.avro";

                Console.WriteLine("Defining the Schema and creating Sample Data Set...");

                //Define the schema in JSON
                const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

                //Create a generic serializer based on the schema
                var serializer = AvroSerializer.CreateGeneric(Schema);
                var rootSchema = serializer.WriterSchema as RecordSchema;

                //Create a generic record to represent the data
                var testData = new List<AvroRecord>();

                dynamic expected1 = new AvroRecord(rootSchema);
                dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location1.Floor = 1;
                location1.Room = 243;
                expected1.Location = location1;
                expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
                testData.Add(expected1);

                dynamic expected2 = new AvroRecord(rootSchema);
                dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location2.Floor = 1;
                location2.Room = 244;
                expected2.Location = location2;
                expected2.Value = new byte[] { 6, 7, 8, 9 };
                testData.Add(expected2);

                //Serializing and saving data to file.
                //Create a MemoryStream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data will not be compressed (Null compression codec).
                    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
                    {
                        using (var streamWriter = new SequentialWriter<object>(writer, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(streamWriter.Write);
                        }
                    }

                    Console.WriteLine("Saving serialized data to file...");

                    //Save stream to file
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing the data.
                //Create a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = AvroContainer.CreateGenericReader(buffer))
                    {
                        using (var streamReader = new SequentialReader<object>(reader))
                        {
                            var results = streamReader.Objects;

                            Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                            //Finally, verify the results
                            var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
                            int count = 1;
                            foreach (var pair in pairs)
                            {
                                bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
                                isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
                                isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
                                Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                                count++;
                            }
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using the given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of the AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using generic record to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.




###<a name="sample-5-serialization-using-object-container-files-with-a-custom-compression-codec"></a>Exemple 5 : Sérialisation à l’aide des fichiers du conteneur objet avec un codec compression personnalisé

L’exemple cinquième montre comment l’utilisation d’un codec compression personnalisé pour les fichiers de conteneur Avro objet. Un exemple contenant le code de cet exemple peut être téléchargé à partir du site [d’exemples de code Azure](http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111) .

La [Spécification Avro](http://avro.apache.org/docs/current/spec.html#Required+Codecs) permet l’utilisation d’un codec compression facultatif (en plus de **Null** et **Deflate** les valeurs par défaut). Cet exemple ne met pas en œuvre un codec entièrement nouvel tels que Snappy (mentionnée apparaît sous forme d’un codec facultatif pris en charge dans la [Spécification Avro](http://avro.apache.org/docs/current/spec.html#snappy)). Il indique comment utiliser l’implémentation .NET Framework 4.5 de la [**Deflate**] [ deflate-110] codec, qui fournit un meilleur algorithme de compression basé sur la bibliothèque de compression [zlib](http://zlib.net/) que la version de .NET Framework 4 par défaut.


    //
    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of the Deflate compression algorithm is used.
    // Ensure your C# project is set up accordingly.
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.IO;
        using System.IO.Compression;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        #region Defining objects for serialization
        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }
        #endregion

        #region Defining custom codec based on .NET Framework V.4.5 Deflate
        //Avro.NET codec class contains two methods,
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed),
        //which are the key ones for data compression.
        //To enable a custom codec, one needs to implement these methods for the required codec.

        #region Defining Compression and Decompression Streams
        //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
        //cannot be directly used for Avro because it does not support vital operations like Seek.
        //Thus one needs to implement two classes inherited from stream
        //(one for compressed and one for decompressed stream)
        //that use Deflate compression and implement all required features.
        internal sealed class CompressionStreamDeflate45 : Stream
        {
            private readonly Stream buffer;
            private DeflateStream compressionStream;

            public CompressionStreamDeflate45(Stream buffer)
            {
                Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

                this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
                this.buffer = buffer;
            }

            public override bool CanRead
            {
                get { return this.buffer.CanRead; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return this.buffer.CanWrite; }
            }

            public override void Flush()
            {
                this.compressionStream.Close();
            }

            public override long Length
            {
                get { return this.buffer.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.buffer.Position;
                }

                set
                {
                    this.buffer.Position = value;
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.buffer.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                return this.buffer.Seek(offset, origin);
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                this.compressionStream.Write(buffer, offset, count);
            }

            protected override void Dispose(bool disposed)
            {
                base.Dispose(disposed);

                if (disposed)
                {
                    this.compressionStream.Dispose();
                    this.compressionStream = null;
                }
            }
        }

        internal sealed class DecompressionStreamDeflate45 : Stream
        {
            private readonly DeflateStream decompressed;

            public DecompressionStreamDeflate45(Stream compressed)
            {
                this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
            }

            public override bool CanRead
            {
                get { return true; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return false; }
            }

            public override void Flush()
            {
                this.decompressed.Close();
            }

            public override long Length
            {
                get { return this.decompressed.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.decompressed.Position;
                }

                set
                {
                    throw new NotSupportedException();
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.decompressed.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                throw new NotSupportedException();
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                throw new NotSupportedException();
            }

            protected override void Dispose(bool disposing)
            {
                base.Dispose(disposing);

                if (disposing)
                {
                    this.decompressed.Dispose();
                }
            }
        }
        #endregion

        #region Define Codec
        //Define the actual codec class containing the required methods for manipulating streams:
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed).
        //Codec class uses classes for compressed and decompressed streams defined above.
        internal sealed class DeflateCodec45 : Codec
        {

            //We merely use different IMPLEMENTATIONS of Deflate, so CodecName remains "deflate"
            public static readonly string CodecName = "deflate";

            public DeflateCodec45()
                : base(CodecName)
            {
            }

            public override Stream GetCompressedStreamOver(Stream decompressed)
            {
                if (decompressed == null)
                {
                    throw new ArgumentNullException("decompressed");
                }

                return new CompressionStreamDeflate45(decompressed);
            }

            public override Stream GetDecompressedStreamOver(Stream compressed)
            {
                if (compressed == null)
                {
                    throw new ArgumentNullException("compressed");
                }

                return new DecompressionStreamDeflate45(compressed);
            }
        }
        #endregion

        #region Define modified Codec Factory
        //Define modified codec factory to be used in the reader.
        //It will catch the attempt to use "Deflate" and provide  a custom codec.
        //For all other cases, it will rely on the base class (CodecFactory).
        internal sealed class CodecFactoryDeflate45 : CodecFactory
        {

            public override Codec Create(string codecName)
            {
                if (codecName == DeflateCodec45.CodecName)
                    return new DeflateCodec45();
                else
                    return base.Create(codecName);
            }
        }
        #endregion

        #endregion

        #region Sample Class with demonstration methods
        //This class contains methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the custom compression codec (Deflate of .NET Framework 4.5).
            //
            //This sample uses memory stream for all operations related to serialization, deserialization and
            //object container manipulation, though file stream could be easily used.
            public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate45.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Here the custom codec is introduced. For convenience, the next commented code line shows how to use built-in Deflate.
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Because of SequentialReader<T> constructor signature, an AvroSerializerSettings instance is required
                    //when codec factory is explicitly specified.
                    //You may comment the line below if you want to use built-in Deflate (see next comment).
                    AvroSerializerSettings settings = new AvroSerializerSettings();

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    //Here the custom codec factory is introduced.
                    //For convenience, the next commented code line shows how to use built-in Deflate
                    //(no explicit Codec Factory parameter is required in this case).
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        bool isEqual;
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }
        #endregion

            #region Helper Methods

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }
            #endregion

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file using custom codec
                Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

###<a name="sample-6-using-avro-to-upload-data-for-the-microsoft-azure-hdinsight-service"></a>Exemple 6 : À l’aide de Avro pour télécharger des données pour le service Microsoft Azure HDInsight

L’exemple sixième illustre quelques techniques de programmation liés à l’interaction avec le service Azure HDInsight. Un exemple contenant le code de cet exemple peut être téléchargé à partir du site [d’exemples de code Azure](https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3) .

L’exemple effectue les opérations suivantes :

* Se connecte à un cluster de service HDInsight existant.
* Sérialise plusieurs fichiers CSV et télécharge le résultat au stockage d’objets Blob Azure. (Les fichiers CSV sont distribués avec l’échantillon et représentent un extrait de données historiques AMEX Stock distribuées par [Infochimps](http://www.infochimps.com/) pour la période 1970 2010. L’exemple lit les données de fichier CSV, convertit les enregistrements sur des instances de la classe **Stock** et les sérialise ensuite en utilisant la réflexion. Définition de type boursier est créée à partir d’un schéma JSON via l’utilitaire de génération de code Microsoft Avro Library.
* Crée une nouvelle table externe appelée **Stocks** dans Hive et les liens pour les données téléchargée à l’étape précédente.
* Exécute une requête à l’aide de Hive sur la table des **Stocks** .

En outre, l’échantillon effectue une procédure de nettoyage avant et après l’exécution des principales opérations. Pendant le nettoyage, toutes les données d’objets Blob Azure associées et les dossiers sont supprimés et la table Hive est supprimée. Vous pouvez également appeler la procédure de nettoyage de l’exemple de ligne de commande.

L’exemple comporte les conditions préalables suivantes :

* Un abonnement Microsoft Azure actif et son ID de l’abonnement.
* Un certificat de gestion de l’abonnement avec la clé privée correspondante. Le certificat doit être installé dans le stockage privé utilisateur actuel sur l’ordinateur utilisé pour exécuter l’échantillon.
* Un cluster de HDInsight actif.
* Un compte de stockage Azure lié au cluster HDInsight à partir de la condition préalable précédente, avec la touche d’accès principal ou secondaire correspondante.

Toutes les informations dans les conditions préalables doivent être entrées à l’exemple de fichier de configuration avant l’exécution de l’échantillon. Il existe deux manières de procéder :

* Modifiez le fichier app.config dans le répertoire racine exemple avant de générer l’échantillon
* Tout d’abord créer l’échantillon et modifiez-la AvroHDISample.exe.config dans le répertoire de génération

Dans les deux cas, toutes les modifications doivent être effectuées de la **<appSettings>** section Paramètres. Suivez les commentaires dans le fichier.
L’exemple est exécuté à partir de la ligne de commande en exécutant la commande suivante (où le fichier .zip avec l’échantillon a été considéré comme extraits dans C:\AvroHDISample ; si dans le cas contraire, utilisez le chemin d’accès de fichier adéquat) :

    AvroHDISample run C:\AvroHDISample\Data

Pour nettoyer le cluster, exécutez la commande suivante :

    AvroHDISample clean

[deflate-100]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.110).aspx
