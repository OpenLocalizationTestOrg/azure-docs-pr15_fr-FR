<properties
    pageTitle="Présentation de l’archivage sécurisé de clé pile Azure | Microsoft Azure"
    description="Découvrez comment l’archivage sécurisé Azure pile clé gère les clés et secrets"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="introduction-to-key-vault-in-azure-stack"></a>Présentation de l’archivage sécurisé clé dans une pile Azure #

## <a name="before-you-start"></a>Avant de commencer

Cet article suppose que les éléments suivants :

- Le lecteur a accès à un abonnement qui a l’archivage sécurisé de clé Azure activé.
- Le Kit de développement de PowerShell Azure est configuré et disponible.
- Pour la version TP2, toutes les opérations côté client peuvent être effectuées uniquement à partir de PowerShell.

## <a name="key-vault-basics"></a>Concepts de base de l’archivage sécurisé clé

Clé de l’archivage sécurisé dans Azure pile protège les clés de chiffrement et utilisent des codes secrets cloud services et applications. À l’aide de l’archivage sécurisé clé, vous pouvez chiffrer clés et secrets (par exemple, les clés d’authentification, clés de compte de stockage, les clés de chiffrement de données, fichiers .pfx et les mots de passe).

Clé de l’archivage sécurisé simplifie le processus de gestion de clés et vous permet de garder le contrôle des clés qui accèdent et chiffrer vos données. Les développeurs peuvent créer des clés de développement et de test en quelques minutes et ensuite en toute transparence les migrer vers clés de production. Les administrateurs de sécurité peuvent accorder (et révoquer) autorisations sur les clés, selon vos besoins.

Toute personne disposant d’un abonnement Azure pile peut créer et utiliser des clés chambres fortes. Bien que les avantages de l’archivage sécurisé clé les développeurs et les administrateurs de la sécurité, pouvoir être mis en œuvre et géré par un administrateur qui gère les autres services Azure pile pour une organisation. Par exemple, cet administrateur peut se connecter avec un abonnement Azure pile, créez un archivage sécurisé pour l’organisation dans laquelle vous voulez stocker les clés et puis être responsables des tâches opérationnelles :

- Créer ou importer une clé ou un code secret
- Révoquer ou supprimer une clé ou un code secret
- Autoriser des utilisateurs ou des applications à accéder à l’archivage sécurisé clé, elles peuvent puis gérer ou utiliser ses clés et secrets
- Configurer l’utilisation de clés (par exemple, signer ou chiffrer)

Cet administrateur peut ensuite fournir les développeurs MU pour appeler à partir de leurs applications et fournissez un administrateur de sécurité avec les informations de journalisation de l’utilisation de la clé.

Les développeurs peuvent également gérer les clés directement, en utilisant des API. Pour plus d’informations, voir le guide du développeur de l’archivage sécurisé clé.

## <a name="scenarios"></a>Scénarios

Le tableau suivant décrit des scénarios là l’archivage sécurisé clé peut vous aider à répondre aux besoins des développeurs et les administrateurs de sécurité :


### <a name="developer-for-an-azure-stack-application"></a>Développeur pour une application Azure pile

**Problème**: je souhaite écrire une application pour pile Azure qui utilise des clés de signature et de chiffrement, mais je souhaite qu’ils soient externes à partir de mon application afin que la solution est adaptée à une application qui est distribuée géographiquement.

**Instruction**: clés sont stockées dans un archivage sécurisé et appelés par URI lorsque cela est nécessaire.


### <a name="developer-for-software-as-a-service-saas"></a>Développeur pour les logiciels en tant que service (SaaS)

**Problème :** Je ne souhaite pas la responsabilité ou responsabilité potentielle pour les clés de client de mes clients et secrets.

**Instruction :** Clients peuvent importer leurs propres clés dans Azure pile et les gérer. Je veux clients qui possède et gère leur clé de sorte que je peux concentrez-vous à ce sujet que faire meilleur des cas, qui fournit les principales fonctionnalités du logiciel.


### <a name="chief-security-officer-cso"></a>Sécurité directeur (responsable de la sécurité)

**Problème :** Je veux pour vous assurer que mon organisation a le contrôle du cycle de vie clé et pouvez contrôler l’utilisation de la clé.

**Instruction** Clé de l’archivage sécurisé est conçu pour que Microsoft ne pas voir ou extraire vos clés.  Lorsqu’une application doit effectuer les opérations de chiffrement à l’aide des touches de clients, l’archivage sécurisé clé procède au nom de l’application. L’application ne voit pas les clés de clients.  Bien que nous utilisons plusieurs services de pile Azure et des ressources, je veux gérer les touches à partir d’un emplacement unique dans une pile Azure. L’archivage sécurisé fournit une interface unique, chambres fortes combien vous disposiez d’Azure empilés, les régions ils prise en charge et les applications les utilisent.

## <a name="next-steps"></a>Étapes suivantes

[Prise en main l’archivage sécurisé clé](azure-stack-kv-getting-started.md)
