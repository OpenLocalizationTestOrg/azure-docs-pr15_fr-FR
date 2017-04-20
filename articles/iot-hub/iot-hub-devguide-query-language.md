<properties
 pageTitle="Guide du développeur - langage de requête | Microsoft Azure"
 description="Guide du développeur IoT concentrateur Azure - description du langage de requête utilisée pour récupérer des informations jumeaux, méthodes et tâches à partir de votre plateforme IoT"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="elioda"/>

# <a name="reference---query-language-for-twins-and-jobs"></a>Référence - langage de requête pour jumeaux et les tâches

## <a name="overview"></a>Vue d’ensemble

Concentrateur IoT fournit un langage similaire à SQL puissant pour récupérer des informations concernant [jumeaux appareil] [ lnk-twins] et les [travaux][lnk-jobs]. Cet article présente :

* Présente les principales fonctionnalités de langage de requête du concentrateur IoT, et
* La description détaillée de la langue.

## <a name="getting-started-with-twin-queries"></a>Mise en route des requêtes double

[Jumeaux appareil] [ lnk-twins] peut contenir des objets JSON arbitraires comme balises et propriétés. Concentrateur IoT permet de jumeaux appareil de requête comme un seul document JSON contenant toutes les informations double.
Par exemple, supposons que votre jumeaux concentrateur IoT ont la structure suivante :

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                "location": {                                                      
                    "region": "US",                                                  
                    "plant": "Redmond43"                                             
                }                                                                  
            },                                                                   
            "properties": {                                                      
                "desired": {                                                       
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300                                          
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                     
                    },                                                               
                    "$version": 4                                                    
                },                                                                 
                "reported": {                                                      
                    "connectivity": {                                                
                        "type": "cellular"                            
                    },                                                               
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300,                                         
                        "status": "Success"                                            
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                
                    },                                                               
                    "$version": 7                                                    
                }                                                                  
            }                                                                    
        }

Concentrateur IoT expose la jumeaux appareil en tant que document collection appelée **appareils**.
Si la requête suivante extrait l’ensemble des jumeaux appareil :

        SELECT * FROM devices

> [AZURE.NOTE] [IoT concentrateur SDK] [ lnk-hub-sdks] prennent en charge la pagination des résultats de grande taille.

Concentrateur IoT permet de récupérer jumeaux en filtrant avec des conditions arbitraires. Par exemple,

        SELECT * FROM devices
        WHERE tags.location.region = 'US'

Récupère le jumeaux avec la balise **location.region** définie sur **nous**.
Opérateurs booléens et des comparaisons arithmétiques sont prises en charge, par exemple

        SELECT * FROM devices
        WHERE tags.location.region = 'US'
            AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60

Récupère tous les jumeaux situé dans le fuseau horaire configuré pour l’envoi de télémétrie moins souvent que toutes les minutes. Faciliter la tâche, il est également possible d’utiliser des constantes de matrice en association avec **les et les opérateurs **NDANS** (pas dans)** . Par exemple,

        SELECT * FROM devices
        WHERE property.reported.connectivity IN ['wired', 'wifi']

Récupère tous les jumeaux qui a signalé Wi-Fi ou câblé connectivity. Reportez-vous à la [clause WHERE] [ lnk-query-where] section pour la référence complète des capacités de filtrage.

Regroupement et les agrégations sont également pris en charge. Par exemple,

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

Renvoie le nombre de périphériques dans chaque état de la configuration télémétrie.

        [
            {
                "numberOfDevices": 3,
                "status": "Success"
            },
            {
                "numberOfDevices": 2,
                "status": "Pending"
            },
            {
                "numberOfDevices": 1,
                "status": "Error"
            }
        ]

L’exemple ci-dessus illustre une situation où trois périphériques signalés configuration réussie, deux sont en mettant en œuvre la configuration et un a signalé une erreur.

### <a name="c-example"></a>Exemple c#

La fonctionnalité de requête est exposée par le [service c# SDK] [ lnk-hub-sdks] dans le cours **RegistryManager** .
Voici un exemple d’une requête simple :

        var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
        while (query.HasMoreResults)
        {
            var page = await query.GetNextAsTwinAsync();
            foreach (var twin in page) 
            {
                // do work on twin object
            }
        }

Remarque : comment instanciation de l’objet de **requête** avec une taille de page (jusqu'à 1000), et puis plusieurs pages peuvent être récupérés en appelant les méthodes **GetNextAsTwinAsync** plusieurs fois.
Il est important de noter que l’objet de requête expose multiple **Suivant\***, en fonction de l’option désérialisation requise par la requête, c'est-à-dire à deux ou objets ou Json simple à utiliser lors de l’utilisation de projections des tâches.

### <a name="node-example"></a>Exemple de nœuds

La fonctionnalité de requête est exposée par le [service de nœud SDK] [ lnk-hub-sdks] dans la l’objet **du Registre** .
Voici un exemple d’une requête simple :

        var query = registry.createQuery('SELECT * FROM devices', 100);
        var onResults = function(err, results) {
            if (err) {
                console.error('Failed to fetch the results: ' + err.message);
            } else {
                // Do something with the results
                results.forEach(function(twin) {
                    console.log(twin.deviceId);
                });

                if (query.hasMoreResults) {
                    query.nextAsTwin(onResults);
                }
            }
        };
        query.nextAsTwin(onResults);

Remarque : comment instanciation de l’objet de **requête** avec une taille de page (jusqu'à 1000), et puis plusieurs pages peuvent être récupérés en appelant les méthodes **nextAsTwin** plusieurs fois.
Il est important de noter que l’objet de requête expose multiple **Suivant\***, en fonction de l’option désérialisation requise par la requête, c'est-à-dire à deux ou objets ou Json simple à utiliser lors de l’utilisation de projections des tâches.

### <a name="limitations"></a>Limitations

Pour l’instant, projections sont uniquement pris en charge lorsque vous utilisez des agrégations, c'est-à-dire non agrégé requêtes peuvent uniquement utiliser `SELECT *`. En outre, agrégation sont uniquement pris en charge conjointement avec le regroupement.

## <a name="getting-started-with-jobs-queries"></a>Mise en route des requêtes de tâches

[Tâches] [ lnk-jobs] permet d’exécuter des opérations sur les ensembles de périphériques. Chaque double appareil contient les informations des tâches dont il fait partie d’une collection de **tâches**.
Logiquement,

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                ...                                                              
            },                                                                   
            "properties": {                                                      
                ...                                                                 
            },
            "jobs": [
                { 
                    "deviceId": "myDeviceId",
                    "jobId": "myJobId",    
                    "jobType": "scheduleTwinUpdate",            
                    "status": "completed",                    
                    "startTimeUtc": "2016-09-29T18:18:52.7418462",
                    "endTimeUtc": "2016-09-29T18:20:52.7418462",
                    "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
                    "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
                    "outcome": {
                        "deviceMethodResponse": null   
                    }                                         
                },
                ...
            ]                                                             
        }

Pour l’instant, cette collection est queriable en tant que **devices.jobs** dans le langage de requête IoT concentrateur.

Par exemple, pour accéder à toutes les tâches (passés et planifiées) qui affectent un seul périphérique, vous pouvez utiliser la requête suivante :

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'

Notez comment cette requête fournit l’état spécifiques à l’appareil (et éventuellement la réponse méthode directe) de chaque tâche renvoyée.
Il est également possible de filtrer avec des conditions booléennes arbitraires sur toutes les propriétés des objets dans la collection **devices.jobs** .
Par exemple, la requête suivante :

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'
            AND devices.jobs.jobType = 'scheduleTwinUpdate'
            AND devices.jobs.status = 'completed'
            AND devices.jobs.createdTimeUtc > '2016-09-01'

Récupère toutes les double finale mise à jour de tâches pour appareil **myDeviceId** qui ont été créées après septembre 2016.

Il est également possible de récupérer les résultats par périphérique d’une tâche unique.

        SELECT * FROM devices.jobs
        WHERE devices.jobs.jobId = 'myJobId'

### <a name="limitations"></a>Limitations
Pour l’instant, les requêtes sur **devices.jobs** ne pas prennent en charge :

* Projections, c'est-à-dire uniquement `SELECT *` est possible ;
* Conditions qui font référence à la double appareil en plus des propriétés de la tâche comme indiqué ci-dessus.
* Réaliser des agrégations, par exemple, nombre, Moy, regrouper par.

## <a name="basics-of-an-iot-hub-query"></a>Concepts de base d’une requête IoT concentrateur

Chaque requête IoT concentrateur est constitué d’une instruction SELECT et à partir de clauses et par WHERE (facultatif) et GROUP BY clauses. Chaque requête est exécutée sur un ensemble de documents JSON, par exemple, des jumeaux appareil. La clause FROM indique la collection de documents itération sur (**appareils** ou **devices.jobs**). Ensuite, le filtre dans la clause WHERE est appliqué. Dans le cas d’agrégations, les résultats de cette étape sont regroupées en tant que spécifié dans la clause GROUP BY et, pour chaque groupe, une ligne est générée comme indiqué dans la clause SELECT.

        SELECT <select_list>
        FROM <from_specification>
        [WHERE <filter_condition>]
        [GROUP BY <group_specification>]

## <a name="from-clause"></a>Clause FROM

La clause **FROM < from_specification >** pouvez part du principe que deux valeurs : **appareils FROM**, jumeaux appareil de requête ou **devices.jobs FROM**, à la requête par périphérique détails.

## <a name="where-clause"></a>Clause WHERE

La **où < filter_condition >** clause est facultative. Il spécifie les conditions que les documents JSON dans la collection FROM doivent satisfaire pour être inclus dans le cadre du résultat. N’importe quel document JSON doit correspondre les conditions spécifiées « True » pour être inclus dans le résultat.

Les conditions autorisées sont décrites dans la section [Expressions et conditions][lnk-query-expressions].

## <a name="select-clause"></a>Clause SELECT

La clause SELECT (**Sélectionnez < liste_de_sélection >**) est obligatoire et spécifie les valeurs qui seront extraits de la requête. Il indique les valeurs JSON à être utilisé pour générer des nouveaux objets JSON pour chaque élément du sous-ensemble filtré (et éventuellement groupé) de la collection de la phase de projection génère un nouvel objet JSON, construit avec les valeurs spécifiées dans la clause SELECT.

Il s’agit de la grammaire de la clause SELECT :

        SELECT [TOP <max number>] <projection list>

        <projection_list> ::=
            '*'
            | <projection_element> AS alias [, <projection_element> AS alias]+

        <projection_element> :==
            attribute_name
            | <projection_element> '.' attribute_name
            | <aggregate>

        <aggregate> :==
            count(<projection_element>) | count()
            | avg(<projection_element>) | avg()
            | sum(<projection_element>) | sum()
            | min(<projection_element>) | min()
            | max(<projection_element>) | max()

où **attribute_name** fait référence à n’importe quelle propriété du document dans la collection de JSON. Vous trouverez quelques exemples des clauses SELECT dans la [mise en route des requêtes double en] [ lnk-query-getstarted] section.

Pour l’instant, les clauses de sélection différents de celui **Sélectionnez \* ** sont uniquement pris en charge dans les requêtes agrégation sur jumeaux.

## <a name="group-by-clause"></a>Clause GROUP BY

La clause **GROUP BY < group_specification >** est une étape facultative est pouvant être exécutée après le filtre spécifié dans la clause WHERE et avant la projection spécifiée dans la sélectionner. Il regroupe les documents basés sur la valeur d’un attribut. Ces groupes sont utilisés pour générer des valeurs agrégées comme indiqué dans la clause SELECT.

Exemple d’une requête à l’aide de GROUP BY est la suivante :

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

La syntaxe de la formel pour GROUP BY est la suivante :

        GROUP BY <group_by_element>
        <group_by_element> :==
            attribute_name
            | < group_by_element > '.' attribute_name

où **attribute_name** fait référence à n’importe quelle propriété du document dans la collection de JSON. 

Pour l’instant, la clause GROUP BY est uniquement prise en charge lors de l’interrogation jumeaux.

## <a name="expressions-and-conditions"></a>Expressions et conditions

À un niveau élevé, une *expression*:

* Prend la valeur d’une instance d’un type de JSON (c'est-à-dire booléen, nombre, chaîne, tableau ou objet), et
* Défini par manipulation des données provenant d’appareil JSON document et constantes à l’aide des fonctions et des opérateurs intégrés.

*Conditions* sont des expressions qui prend la valeur d’une valeur booléenne, c'est-à-dire n’importe quelle constante différent de celui booléenne **true** est considéré comme **false** (incluse **null**, **non définie**, toute instance d’objet ou un tableau, une chaîne et clairement la **valeur false**booléenne).

La syntaxe des expressions est la suivante :

        <expression> ::=
            <constant> |
            attribute_name |
            unary_operator <expression> |
            <expression> binary_operator <expression> |
            <create_array_expression> |
            '(' <expression> ')'

        <constant> ::=
            <undefined_constant>
            | <null_constant> 
            | <number_constant> 
            | <string_constant> 
            | <array_constant> 

        <undefined_constant> ::= undefined
        <null_constant> ::= null
        <number_constant> ::= decimal_literal | hexadecimal_literal
        <string_constant> ::= string_literal
        <array_constant> ::= '[' <constant> [, <constant>]+ ']'

Dans cet exemple :

| Symbole | Définition |
| -------------- | -----------------|
| nom_attribut | Toutes les propriétés du document dans la collection de JSON. |
| UNARY_OPERATOR | Tout opérateur unaire en fonction de la section opérateurs. |
| Opérateur_binaire | Tout opérateur binaire en fonction de la section opérateurs. |
| decimal_literal | Un élément flottant exprimé sous forme décimale. |
| hexadecimal_literal | Un nombre exprimé par la chaîne « 0 x » suivi d’une chaîne de chiffres nombre hexadécimales. |
| string_literal | Littéraux de chaîne sont des chaînes Unicode représentés par une séquence de zéro ou plusieurs caractères Unicode ou de séquences d’échappement. Littéraux de chaîne sont placées entre guillemets simples (apostrophe : ») ou par des guillemets (guillemet : »). Autorisé d’échappement : `\'`, `\"`, `\\`, `\uXXXX` pour les caractères Unicode définies par 4 chiffres nombre hexadécimales. |

### <a name="operators"></a>Opérateurs

Les opérateurs suivants sont pris en charge :

| Famille | Opérateurs |
| -------------- | -----------------|
| Arithmétique | +,-,*,/,% |
| Logique | ET, OU NON |
| Comparaison |  =, ! =, <>,, < =, > =, <> |

## <a name="next-steps"></a>Étapes suivantes

Apprenez à exécuter des requêtes dans vos applications à l’aide de [IoT concentrateur SDK][lnk-hub-sdks].

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#getting-started-with-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md