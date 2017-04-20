<properties
    pageTitle="API DocumentDB Node.js & SDK | Microsoft Azure"
    description="Découvrez la Node.js API et SDK, y compris les dates de publication, les dates de retraite et les modifications apportées entre chaque version du Kit de développement Node.js DocumentDB."
    services="documentdb"
    documentationCenter="nodejs"
    authors="rnagpal"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>API DocumentDB et SDK

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [RESTE](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

##<a name="documentdb-nodejs-api-and-sdk"></a>Kit de développement logiciel et API DocumentDB Node.js

<table>
<tr><td>**Téléchargez le Kit de développement**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>
<tr><td>**Documentation de l’API**</td><td>[Documentation de référence Node.js API](http://azure.github.io/azure-documentdb-node/DocumentClient.html)</td></tr>
<tr><td>**Instructions d’installation du Kit de développement**</td><td>[Instructions d’installation](http://azure.github.io/azure-documentdb-node/)</td></tr>
<tr><td>**Contribuer à SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>
<tr><td>**Exemples**</td><td>[Exemples de code Node.js](documentdb-nodejs-samples.md)</td></tr>
<tr><td>**Didacticiel mise en route Get**</td><td>[Prise en main du Kit de développement Node.js](documentdb-nodejs-get-started.md)</td></tr>
<tr><td>**Didacticiel d’application Web**</td><td>[Créer une application web Node.js à l’aide de DocumentDB](documentdb-nodejs-application.md)</td></tr>
<tr><td>**Plates-formes prises en charge en cours**</td><td>[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

##<a name="release-notes"></a>Notes de publication

###<a name="1.10.0"/>1.10.0</a>

- Prise en charge supplémentaire pour croisée partition des requêtes en parallèle.
- Prise en charge pour les requêtes haut/ORDER BY pour des collections de partitionnées.

###<a name="1.9.0"/>1.9.0</a>

- Stratégie réessayer ajouté prise en charge pour les demandes limités. (Demandes accélérées recevoir une exception trop volumineux taux demande, code d’erreur 429.) Par défaut, DocumentDB tentatives de neuf pour chaque demande lorsque le code d’erreur 429 est rencontré, en respectant la fois retryAfter dans l’en-tête de réponse. Un intervalle fixe peut désormais être définie dans le cadre de la propriété RetryOptions sur l’objet ConnectionPolicy si vous souhaitez ignorer l’heure retryAfter retournée par serveur entre les tentatives. DocumentDB maintenant attend pour un maximum de 30 secondes pour chaque demande à laquelle est limitée (quel que soit le nombre de tentatives) et renvoie la réponse avec code d’erreur 429. Cette fois peut également être remplacé dans la propriété RetryOptions ConnectionPolicy objet.

- DocumentDB retourne maintenant x-ms-limitation--nombre de tentatives et x-ms-throttle-retry-wait-time-ms telles que les en-têtes de réponse lors de chaque demande pour indiquer la limitation réessayer Nb et l’heure cummulative la demande ne soit entre les tentatives.

- La classe RetryOptions a été ajoutée, exposant la propriété RetryOptions sur la classe ConnectionPolicy qui peut être utilisée pour substituer certaines options réessayer par défaut.

###<a name="1.8.0"/>1.8.0</a>

 - Ajouter la prise en charge de base de données de la région à plusieurs comptes.

###<a name="1.7.0"/>1.7.0</a>

- Désormais en charge la fonctionnalité de temps à Live(TTL) pour les documents.

###<a name="1.6.0"/>1.6.0</a>

- Mise en œuvre [collections partitionnées](documentdb-partition-data.md) et [niveaux de performances définis par l’utilisateur](documentdb-performance-levels.md).

###<a name="1.5.6"/>1.5.6</a>

- RangePartitionResolver.resolveForRead bogue dans lequel il a été ne renvoyant pas les liens en raison d’une mauvaise concat des résultats.

###<a name="1.5.5"/>1.5.5</a>

- Fixe hashParitionResolver resolveForRead() : lorsqu’aucune clé partition fournie a été lever exception, au lieu de renvoi d’une liste de tous les liens enregistrés.

###<a name="1.5.4"/>1.5.4</a>

- Correctifs émettre [100 #](https://github.com/Azure/azure-documentdb-node/issues/100) - dédié HTTPS Agent : éviter l’agent global DocumentDB à des fins de modification. Utiliser un agent dédié pour toutes les demandes de la bibliothèque.

###<a name="1.5.3"/>version 1.5.3</a>

- Correctifs de problème [#81](https://github.com/Azure/azure-documentdb-node/issues/81) - correctement poignée tirets dans ID de média.

###<a name="1.5.2"/>1.5.2</a>

- Correctifs de problème [95 #](https://github.com/Azure/azure-documentdb-node/issues/95) - EventEmitter récepteur perdre un avertissement.

###<a name="1.5.1"/>1.5.1</a>

- Correctifs de problème [#92](https://github.com/Azure/azure-documentdb-node/issues/90) - renommer dossier hachage au hachage pour les systèmes respectant la casse.

### <a name="1.5.0"/>1.5.0</a>

- Mettre en œuvre ont la prise en charge en ajoutant des programmes de résolution de partition hachage & plage.

### <a name="1.4.0"/>1.4.0</a>

- Mettre en œuvre Upsert. Nouvelles méthodes d’upsertXXX sur documentClient.

### <a name="1.3.0"/>version 1.3.0</a>

- Ignoré pour transférer des numéros de version alignement avec d’autres SDK.

### <a name="1.2.2"/>1.2.2</a>

- Fractionnement Q promet emballage à nouveau référentiel.
- Mettre à jour fichier de package pour npm du Registre.

### <a name="1.2.1"/>1.2.1</a>

- Met en œuvre ID routage basé sur.
- Résout problème [n ° 49](https://github.com/Azure/azure-documentdb-node/issues/49) - actuel propriété est en conflit avec la méthode current().

### <a name="1.2.0"/>1.2.0</a>

- Prise en charge pour l’index géospatiales.
- Valide la propriété id pour toutes les ressources. ID des ressources ne peut pas contenir ?, /, #, #47 ; & #47 ; caractères ou se terminent par un espace.
- Ajoute le nouvel en-tête « index progression de la transformation » à ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>

- Met en œuvre V2 politique d’indexation.

### <a name="1.0.3"/>1.0.3</a>

- Configurations eslint et grunt problème [#40] (https://github.com/Azure/azure-documentdb-node/issues/40) - implémentée dans les principaux et promesses SDK.

### <a name="1.0.2"/>1.0.2</a>

- De problème [n ° 45](https://github.com/Azure/azure-documentdb-node/issues/45) - groupes d’emballage promesses n’inclut pas d’en-tête avec l’erreur.

### <a name="1.0.1"/>1.0.1</a>

- Mise en œuvre de possibilité d’interrogation des conflits en ajoutant readConflicts, readConflictAsync et queryConflicts.
- Documentation API mise à jour.
- Émettre [#41](https://github.com/Azure/azure-documentdb-node/issues/41) - erreur client.createDocumentAsync.

### <a name="1.0.0"/>1.0.0</a>

- KIT DE DÉVELOPPEMENT LOGICIEL DISPONIBLE.

## <a name="release--retirement-dates"></a>Version & Dates de retraite
Microsoft fournira notification au moins **12 mois** avant la suppression d’un kit de développement afin de faciliter la transition vers une version plus récente/pris en charge.

Nouvelles fonctionnalités et fonctionnalités et optimisations ne sont ajoutées dans le Kit de développement en cours, en tant que tel qu’il est recommandé que vous toujours mise à niveau vers la dernière version SDK dès que possible.

Toute demande à DocumentDB à l’aide d’un SDK déclassé est rejetée par le service.

> [AZURE.WARNING]
Toutes les versions du Kit de développement DocumentDB Azure pour Node.js avant version **1.0.0** seront supprimées sur **29 février 2016**.

<br/>

| Version | Date de publication | Date de déclassement
| ---     | ---          | ---
| [1.10.0](#1.10.0) | 03 octobre 2016 |---
| [1.9.0](#1.9.0) | 07 juillet 2016 |---
| [1.8.0](#1.8.0) | 14 juin 2016 |---
| [1.7.0](#1.7.0) | 26 avril 2016 |---
| [1.6.0](#1.6.0) | 29 mars 2016 |---
| [1.5.6](#1.5.6) | 08 mars 2016 |---
| [1.5.5](#1.5.5) | 02 février 2016 |---
| [1.5.4](#1.5.4) | 01 février 2016 |---
| [1.5.2](#1.5.2) | 26 janvier 2016 |---
| [1.5.2](#1.5.2) | 22 janvier 2016 |---
| [1.5.1](#1.5.1) | 4 janvier 2016 |---
| [1.5.0](#1.5.0) | 31 décembre 2015 |---
| [1.4.0](#1.4.0) | 06 octobre 2015 |---
| [version 1.3.0](#1.3.0) | 06 octobre 2015 |---
| [1.2.2](#1.2.2) | 10 septembre 2015 |---
| [1.2.1](#1.2.1) | 15 août 2015 |---
| [1.2.0](#1.2.0) | 05 août 2015 |---
| [1.1.0](#1.1.0) | 09 juillet 2015 |---
| [1.0.3](#1.0.3) | 04 juin 2015 |---
| [1.0.2](#1.0.2) | 23 mai 2015 |---
| [1.0.1](#1.0.1) | 15 mai 2015 |---
| [1.0.0](#1.0.0) | 08 avril 2015 |---
| 0.9.4-Prerelease | 06 avril 2015 | 29 février 2016
| 0.9.3-Prerelease | 14 janvier 2015 | 29 février 2016
| 0.9.2-Prerelease | 18 décembre 2014 | 29 février 2016
| 0.9.1-Prerelease | 22 août 2014 | 29 février 2016
| 0.9.0-Prerelease | 21 août 2014 | 29 février 2016


## <a name="faq"></a>FAQ
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur DocumentDB, consultez la page de service [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) .
