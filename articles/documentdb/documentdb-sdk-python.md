<properties 
    pageTitle="API DocumentDB Python & SDK | Microsoft Azure" 
    description="Découvrez les Python API et SDK, y compris les dates de publication, les dates de retraite et les modifications apportées entre chaque version du Kit de développement Python DocumentDB." 
    services="documentdb" 
    documentationCenter="python" 
    authors="rnagpal" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>API DocumentDB et SDK

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [RESTE](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## <a name="documentdb-python-api-and-sdk"></a>Kit de développement logiciel et API Python DocumentDB

<table>
<tr><td>**Téléchargez le Kit de développement**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>
<tr><td>**Documentation de l’API**</td><td>[Documentation de référence API Python](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>
<tr><td>**Instructions d’installation du Kit de développement**</td><td>[Instructions d’installation du Kit de développement logiciel Python](http://azure.github.io/azure-documentdb-python/)</td></tr>
<tr><td>**Contribuer à SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>
<tr><td>**Prise en main**</td><td>[Prise en main du Kit de développement Python](documentdb-python-application.md)</td></tr>
<tr><td>**Plates-formes prises en charge en cours**</td><td>[Python 2.7](https://www.python.org/downloads/) et [Python 3.5](https://www.python.org/downloads/)</td></tr>
</table></br>

## <a name="release-notes"></a>Notes de publication

### <a name="a-name200200httpspypipythonorgpypipydocumentdb200"></a><a name="2.0.0"/>[2.0.0](https://pypi.python.org/pypi/pydocumentdb/2.0.0)
- Prise en charge pour Python 3.5.
- Prise en charge pour le regroupement de connexion à l’aide d’un module de requêtes.
- Prise en charge supplémentaire pour la cohérence de session.
- Prise en charge pour les requêtes haut/ORDERBY pour des collections de partitionnées.


### <a name="a-name190190httpspypipythonorgpypipydocumentdb190"></a><a name="1.9.0"/>[1.9.0](https://pypi.python.org/pypi/pydocumentdb/1.9.0)
- Stratégie réessayer ajouté prise en charge pour les demandes limités. (Demandes accélérées recevoir une exception trop volumineux taux demande, code d’erreur 429.) Par défaut, DocumentDB tentatives de neuf pour chaque demande lorsque le code d’erreur 429 est rencontré, en respectant la fois retryAfter dans l’en-tête de réponse. Un intervalle fixe peut désormais être définie dans le cadre de la propriété RetryOptions sur l’objet ConnectionPolicy si vous souhaitez ignorer l’heure retryAfter retournée par serveur entre les tentatives. DocumentDB maintenant attend pour un maximum de 30 secondes pour chaque demande à laquelle est limitée (quel que soit le nombre de tentatives) et renvoie la réponse avec code d’erreur 429. Cette fois peut également être remplacé dans la propriété RetryOptions ConnectionPolicy objet.

- DocumentDB retourne maintenant x-ms-limitation--nombre de tentatives et x-ms-throttle-retry-wait-time-ms telles que les en-têtes de réponse lors de chaque demande pour indiquer la limitation réessayer Nb et l’heure cummulative la demande ne soit entre les tentatives.

- Suppression de la classe RetryPolicy et la propriété correspondante (retry_policy) exposées sur la classe document_client et à la place introduit une classe RetryOptions exposant la propriété RetryOptions sur classe ConnectionPolicy pouvant être utilisées pour substituer certaines options réessayer par défaut.

### <a name="a-name180180httpspypipythonorgpypipydocumentdb180"></a><a name="1.8.0"/>[1.8.0](https://pypi.python.org/pypi/pydocumentdb/1.8.0)
  - Ajouter la prise en charge de base de données de la région à plusieurs comptes.

### <a name="a-name170170httpspypipythonorgpypipydocumentdb170"></a><a name="1.7.0"/>[1.7.0](https://pypi.python.org/pypi/pydocumentdb/1.7.0)
- Désormais en charge la fonctionnalité de temps à Live(TTL) pour les documents.

### <a name="a-name161161httpspypipythonorgpypipydocumentdb161"></a><a name="1.6.1"/>[1.6.1](https://pypi.python.org/pypi/pydocumentdb/1.6.1)
- Correctifs relatives à la division de côté serveur pour permettre à des caractères spéciaux de chemin d’accès partitionkey.

### <a name="a-name160160httpspypipythonorgpypipydocumentdb160"></a><a name="1.6.0"/>[1.6.0](https://pypi.python.org/pypi/pydocumentdb/1.6.0)
- Mise en œuvre [collections partitionnées](documentdb-partition-data.md) et [niveaux de performances définis par l’utilisateur](documentdb-performance-levels.md). 

### <a name="a-name150150httpspypipythonorgpypipydocumentdb150"></a><a name="1.5.0"/>[1.5.0](https://pypi.python.org/pypi/pydocumentdb/1.5.0)
- Ajouter une plage et hachage partition programmes de résolution pour vous aider à applications ont entre plusieurs partitions.

### <a name="a-name142142httpspypipythonorgpypipydocumentdb142"></a><a name="1.4.2"/>[1.4.2](https://pypi.python.org/pypi/pydocumentdb/1.4.2)
- Mettre en œuvre Upsert. Nouvelles méthodes de UpsertXXX ajoutés pour prendre en charge la fonctionnalité Upsert.
- ID de mettre en œuvre en fonction de routage. Aucune modification de l’API publique, toutes les modifications internes.

### <a name="a-name120120httpspypipythonorgpypipydocumentdb120"></a><a name="1.2.0"/>[1.2.0](https://pypi.python.org/pypi/pydocumentdb/1.2.0)
- Index de géospatiales prend en charge.
- Valide la propriété id pour toutes les ressources. ID des ressources ne peut pas contenir ?, /, #, \, caractères ou se terminent par un espace.
- Ajoute le nouvel en-tête « index progression de la transformation » à ResourceResponse.

### <a name="a-name110110httpspypipythonorgpypipydocumentdb110"></a><a name="1.1.0"/>[1.1.0](https://pypi.python.org/pypi/pydocumentdb/1.1.0)
- Met en œuvre V2 politique d’indexation.

### <a name="a-name101101httpspypipythonorgpypipydocumentdb101"></a><a name="1.0.1"/>[1.0.1](https://pypi.python.org/pypi/pydocumentdb/1.0.1)
- Prend en charge la connexion proxy.

### <a name="a-name100100httpspypipythonorgpypipydocumentdb100"></a><a name="1.0.0"/>[1.0.0](https://pypi.python.org/pypi/pydocumentdb/1.0.0)
- KIT DE DÉVELOPPEMENT LOGICIEL DISPONIBLE.

## <a name="release--retirement-dates"></a>Dates de publication et déclassement
Microsoft fournira notification au moins **12 mois** avant la suppression d’un kit de développement afin de faciliter la transition vers une version plus récente/pris en charge.

Nouvelles fonctionnalités et fonctionnalités et optimisations ne sont ajoutées dans le Kit de développement en cours, en tant que tel qu’il est recommandé que vous toujours mise à niveau vers la dernière version SDK dès que possible. 

Toute demande à DocumentDB à l’aide d’un SDK déclassé est rejetée par le service.

> [AZURE.WARNING]
Toutes les versions du Kit de développement DocumentDB Azure pour Python avant version **1.0.0** seront supprimées sur **29 février 2016**. 

<br/>

| Version | Date de publication | Date de déclassement 
| ---     | ---          | ---
| [2.0.0](#2.0.0) | 29 septembre 2016 |---
| [1.9.0](#1.9.0) | 07 juillet 2016 |---
| [1.8.0](#1.8.0) | 14 juin 2016 |---
| [1.7.0](#1.7.0) | 26 avril 2016 |---
| [1.6.1](#1.6.1) | 08 avril 2016 |---
| [1.6.0](#1.6.0) | 29 mars 2016 |---
| [1.5.0](#1.5.0) | 03 janvier 2016 |---
| [1.4.2](#1.4.2) | 06 octobre 2015 |---
| [1.4.1](#1.4.1) | 06 octobre 2015 |---
| [1.2.0](#1.2.0) | 06 août 2015 |---
| [1.1.0](#1.1.0) | 09 juillet 2015 |---
| [1.0.1](#1.0.1) | 25 mai 2015 |---
| [1.0.0](#1.0.0) | 07 avril 2015 |---
| 0.9.4-prelease | 14 janvier 2015 | 29 février 2016
| 0.9.3-prelease | 09 décembre 2014 | 29 février 2016
| 0.9.2-prelease | 25 novembre 2014 | 29 février 2016
| 0.9.1-prelease | 23 septembre 2014 | 29 février 2016
| 0.9.0-prelease | 21 août 2014 | 29 février 2016

## <a name="faq"></a>FAQ
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur DocumentDB, consultez la page de service [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) . 
