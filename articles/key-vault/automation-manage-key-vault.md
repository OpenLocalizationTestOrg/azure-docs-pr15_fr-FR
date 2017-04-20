<properties
    pageTitle="Gérer l’archivage sécurisé de clé Azure à l’aide de Automation Azure | Microsoft Azure"
    description="Découvrez comment le service Azure Automation peut être utilisé pour gérer l’archivage sécurisé de clé Azure."
    services="Key-Vault, automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="magoedte;csand"/>

#<a name="managing-azure-key-vault-using-azure-automation"></a>Gestion de l’archivage sécurisé de clé Azure à l’aide de Automation Azure

Ce guide vous présenter au service Automation Azure et comment il peut être utilisé pour simplifier la gestion de vos clés et secrets dans l’archivage sécurisé de clé Azure.

## <a name="what-is-azure-automation"></a>Qu’est Automation Azure ?

[Azure Automation](../automation/automation-intro.md) est un service d’Azure pour simplifier la gestion de cloud à l’aide automatisation des processus de configuration de l’état de votre choix. À l’aide de Automation Azure, manuel, répétée, longue et erreurs tâches peuvent être automatisées pour augmenter la fiabilité, l’efficacité et la valeur de durée pour votre organisation.

Automatisation Azure fournit un moteur d’exécution du flux de travail très fiable et hautement disponibles qui s’adapte à vos besoins. Dans Azure Automation, processus est à l’abri manuellement, en systèmes 3ème tiers, ou à intervalles réguliers afin que les tâches se produisent exactement quand nécessaire.

Réduire la surcharge opérationnelle et libérer de l’informatique et le personnel DevOps pour vous concentrer sur travail valeur ajoutée, en déplaçant les tâches d’administration nuage pour être exécuté automatiquement par Automation Azure.


## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>Comment Automation Azure peut-il m’aider à gérer l’archivage sécurisé de clé Azure ?

Clé de l’archivage sécurisé pouvant être gérée dans Azure Automation à l’aide de la [applets de commande de l’archivage sécurisé de clé AzureRM] (https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) et les [applets de commande Azure l’archivage sécurisé clé classique](https://msdn.microsoft.com/library/azure/dn868052.aspx). Le module Azure pour la gestion de l’archivage sécurisé clé classique est disponible automatiquement dans Azure automatisation, et vous pouvez importer le [module AzureRM KeyVault](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) dans Azure automatisation, afin que vous puissiez effectuer la plupart de vos tâches de gestion de l’archivage sécurisé clé au sein du service. Vous pouvez également associer ces applets de commande dans Azure Automation avec les applets de commande pour les autres services Azure, pour automatiser les tâches complexes au sein des services Azure et 3e partie systèmes.

Avec les applets de commande de l’archivage sécurisé de clé Azure, vous pouvez effectuer entre autres les tâches suivantes : 

- Créer et configurer un archivage sécurisé clé
- Créer ou importer une clé
- Créer ou mettre à jour un code secret
- Mettre à jour les attributs d’une clé
- Obtenir une clé ou un code secret
- Supprimer une clé ou un code secret

Voici quelques exemples d’utilisation de PowerShell pour gérer l’archivage sécurisé clé :  

* [Azure clés l’archivage sécurisé - étape par étape](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [La configuration et la configuration d’un archivage sécurisé clé Azure](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)


## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base d’Azure Automation et comment elle peut être utilisée pour gérer l’archivage sécurisé de clé Azure, suivez ces liens pour en savoir plus sur automatisation Azure.

* Voir l’Automation Azure [didacticiels de mise en route](../automation/automation-first-runbook-graphical.md).
* Voir les [scripts PowerShell de l’archivage sécurisé Azure clé](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5).
