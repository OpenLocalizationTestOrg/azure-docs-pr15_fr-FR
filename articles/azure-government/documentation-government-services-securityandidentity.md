<properties
    pageTitle="Documentation pour le gouvernement Azure | Microsoft Azure"
    description="Cela fournit une comparaison des fonctionnalités et des recommandations sur le développement d’applications pour le gouvernement Azure"
    services="Azure-Government"
    cloud="gov"
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/12/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-security-and-identity"></a>Identité et sécurité pour le gouvernement azure

##  <a name="key-vault"></a>Archivage sécurisé clé
Pour plus d’informations sur ce service et comment l’utiliser, reportez-vous à la <a href="https://azure.microsoft.com/documentation/services/key-vault">documentation publique l’archivage sécurisé de clé Azure.</a>

### <a name="data-considerations"></a>Considérations relatives à des données
Les informations suivantes identifient la limite pour le gouvernement Azure pour l’archivage sécurisé de Azure clé :

| Données réglée/contrôlée autorisé | Données réglée/contrôlée non autorisé |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Toutes les données chiffrées avec une clé de l’archivage sécurisé de clé Azure peuvent contenir des données réglementés/contrôlée. | Métadonnées de l’archivage sécurisé clé Azure ne sont pas autorisée pour contenir contrôlé à l’exportation des données. Ces métadonnées incluent toutes les données de configuration entrées lors de la création et la gestion de votre l’archivage sécurisé clé.  Ne saisissez réglementés/contrôlé des données dans les champs suivants : noms de groupe de ressources, les noms de l’archivage sécurisé clé, nom de l’abonnement |

Clé de l’archivage sécurisé est généralement disponible dans Azure pour le gouvernement. Obtenus public, il n’existe aucune extension, l’archivage sécurisé clé est uniquement disponible via PowerShell et infrastructure du langage commun.

## <a name="next-steps"></a>Étapes suivantes

Pour des informations supplémentaires et des mises à jour, vous abonner à la <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog de Microsoft Azure pour le gouvernement.</a>
