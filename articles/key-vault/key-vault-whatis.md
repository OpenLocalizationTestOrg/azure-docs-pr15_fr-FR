<properties
    pageTitle="Quel est l’archivage sécurisé de clé Azure ? | Microsoft Azure"
    description="L’archivage sécurisé clé Azure permet aux clés de chiffrement divulgation d’informations et secrets utilisés par les services et d’applications cloud. À l’aide de l’archivage sécurisé de clé Azure, les clients peuvent chiffrer clés et secrets (par exemple, l’authentification clés clés de compte de stockage, les clés de chiffrement de données. Fichiers PFX et les mots de passe) en utilisant les touches qui sont protégés par les modules de sécurité matérielle (HSM)."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/10/2016"
    ms.author="cabailey"/>



# <a name="what-is-azure-key-vault"></a>Quel est l’archivage sécurisé de clé Azure ?

L’archivage sécurisé clé Azure est disponible dans la plupart des régions. Pour plus d’informations, voir la [clé de l’archivage sécurisé tarifs page](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introduction

L’archivage sécurisé clé Azure permet aux clés de chiffrement divulgation d’informations et secrets utilisés par les services et d’applications cloud. À l’aide de l’archivage sécurisé clé, vous pouvez chiffrer clés et secrets (par exemple, l’authentification clés clés de compte de stockage, les clés de chiffrement de données. Fichiers PFX et les mots de passe) en utilisant les touches qui sont protégés par les modules de sécurité matérielle (HSM). Pour assurance ajouté, vous pouvez importer ou générer des clés dans HSM. Si vous choisissez pour ce faire, processus Microsoft vos clés dans FIPS140-2 de niveau 2 HSM validées (matériel et microprogramme).  

Clé de l’archivage sécurisé simplifie le processus de gestion de clés et vous permet de garder le contrôle des clés qui accèdent et chiffrer vos données. Les développeurs peuvent créer des clés de développement et de test en quelques minutes et ensuite en toute transparence les migrer vers clés de production. Les administrateurs de sécurité peuvent accorder (et révoquer) autorisations sur les clés, selon vos besoins.

Utiliser le tableau suivant pour mieux comprendre comment l’archivage sécurisé clé peut vous aider à répondre aux besoins des développeurs et les administrateurs de sécurité.





| Rôle        | Description du problème           | Résolu en l’archivage sécurisé clé Azure  |
| ------------- |-------------|-----|
| Développeur pour une application Azure      | « Je souhaite écrire une application pour Azure qui utilise des clés de signature et de chiffrement, mais je souhaite ces touches pour être externes à partir de mon application afin que la solution est adaptée à une application qui est distribuée géographiquement. <br/><br/>Je veux également ces touches et secrets à protéger, sans avoir à écrire du code moi-même. J’ai également ces touches et secrets soit facile pour moi à utiliser mes applications, avec des performances optimales. » | √ Clés sont stockées dans un archivage sécurisé et appelés par URI lorsque cela est nécessaire.<br/><br/> Touches √ sont protégés par Azure, à l’aide de modules de sécurité matériel (HSM), longueurs de clé et algorithmes standard.<br/><br/> Touches √ sont traités dans HSM qui résident dans les centres de données Azure même que les applications. Cela permet une meilleure fiabilité et latence réduite que si les clés résident dans un autre endroit, par exemple en local.|
| Développeur pour les logiciels en tant que Service (SaaS)      |« Je ne souhaite la responsabilité ou responsabilité potentielle pour les clés de client de mes clients et secrets. <br/><br/>Je veux les clients qui possède et gère leur clé de sorte que je peux concentrez-vous à ce sujet que faire meilleur des cas, qui fournit les principales fonctionnalités du logiciel ». | √ Clients peuvent importer leurs propres clés dans Azure et les gérer. Lorsqu’une application SaaS doit effectuer les opérations de chiffrement à l’aide des touches de leurs clients, l’archivage sécurisé clé effectue ces opérations au nom de l’application. L’application ne voit pas les clés de clients.|
| Responsable de sécurité en uniforme (responsable de la sécurité) | « Je veux savoir que nos applications sont conformes FIPS 140-2 niveau 2 HSM pour la gestion de clés sécurisée. <br/><br/>Je veux pour vous assurer que mon organisation a le contrôle du cycle de vie clé et pouvez contrôler l’utilisation de la clé. <br/><br/>Et bien que nous utilisons plusieurs services Azure et des ressources, je veux gérer les touches à partir d’un emplacement unique dans Azure. »     |√ HSM sont FIPS 140-2 niveau 2 validées.<br/><br/>√ l’archivage sécurisé clé est conçu pour que Microsoft ne pas voir ou extraire vos clés.<br/><br/>√ Près de journalisation en temps réel d’utilisation de la clé.<br/><br/>√ L’archivage sécurisé fournit une interface unique, chambres fortes combien vous disposiez dans Azure, les régions ils prise en charge et les applications les utilisent. |


Toute personne disposant d’un abonnement Azure peut créer et utiliser des clés chambres fortes. Bien que les avantages de l’archivage sécurisé clé les développeurs et les administrateurs de la sécurité, peut être mis en œuvre et gérée par l’administrateur d’une organisation qui gère les autres services Azure pour une organisation. Par exemple, cet administrateur serait souscrivez un abonnement Azure, créez un archivage sécurisé pour l’organisation dans lesquelles vous pouvez stocker les clés et puis responsable des tâches opérationnelles, telles que :

+ Créer ou importer une clé ou un code secret
+ Révoquer ou supprimer une clé ou un code secret
+ Autoriser des utilisateurs ou des applications à accéder à l’archivage sécurisé clé, elles peuvent puis gérer ou utiliser ses clés et secrets
+ Configurer l’utilisation de clés (par exemple, signer ou chiffrer)
+ Surveiller l’utilisation de clés

Cet administrateur serait puis fournir les développeurs MU pour appeler à partir de leurs applications et fournissez leur administrateur de sécurité avec les informations de journalisation de l’utilisation de la clé. 

   ![Vue d’ensemble de l’archivage sécurisé clé Azure][1]

Les développeurs peuvent également gérer les clés directement, en utilisant des API. Pour plus d’informations, voir le [guide du développeur de l’archivage sécurisé clé](key-vault-developers-guide.md).

## <a name="next-steps"></a>Étapes suivantes

Pour un didacticiel mise en route lors de l’obtention d’un administrateur, voir [Prise en main l’archivage sécurisé de clé Azure](key-vault-get-started.md).

Pour plus d’informations sur l’utilisation de la journalisation pour l’archivage sécurisé clé, consultez [Enregistrement de Azure clé l’archivage sécurisé](key-vault-logging.md).

Pour plus d’informations sur l’utilisation de clés et secrets avec l’archivage sécurisé de clé Azure, voir [à propos des touches, Secrets et certificats](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx).


<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
