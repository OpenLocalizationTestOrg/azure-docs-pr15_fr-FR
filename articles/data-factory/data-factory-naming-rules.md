<properties 
    pageTitle="Données Factory - règles d’appellation de | Microsoft Azure" 
    description="Décrit les règles d’appellation pour les entités Data Factory." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---naming-rules"></a>Données Azure Factory - règles de dénomination 
Le tableau suivant fournit des règles d’appellation pour les objets de données par défaut.



Nom | Unicité des noms | Contrôles de validation
:--- | :-------------- | :----------------
Données par défaut | Unique dans Microsoft Azure. Noms respectent la casse, c'est-à-dire, MyDF et mydf font référence à la même usine de données. |<ul><li>Chaque usine de données est lié à un abonnement Azure.</li><li>Noms des objets doivent commencer par une lettre ou un nombre et peuvent contenir uniquement des lettres, des chiffres et le caractère tiret (-).</li><li>Chaque caractère tiret (-) doit être immédiatement précédé et suivi par une lettre ou un chiffre. Tirets consécutifs ne sont pas autorisés dans les noms de conteneur.</li><li>Nom peut contenir 63 de 3 caractères.</li></ul>
Services/Tables/Pipelines liées | Unique à l’aide dans une usine de données. Noms respectent la casse. | <ul><li>Nombre maximal de caractères dans un nom de table : 260.</li><li>Noms des objets doivent commencer par un numéro de la lettre ou un trait de soulignement (_).</li><li>Caractères suivants ne sont pas autorisés : ». », « + », « ? », « / », « < », « > «, » * », « % », « & », « : «, »\\»</li></ul>
Groupe de ressources | Unique dans Microsoft Azure. Noms respectent la casse. | <ul><li>Nombre maximal de caractères : 1000.</li><li>Nom peut contenir des lettres, des chiffres et les caractères suivants : «- », « _ «, », « et ». ».</li></ul>