<properties
   pageTitle="Conseils supplémentaires sur la résilience du service | Microsoft Azure"
   description="Liens vers sinistre et proactive conseils résilience et de disponibilité pour les services Microsoft Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

# <a name="microsoft-azure-service-resiliency-guidance"></a>Aide à la résilience des services Microsoft Azure
Microsoft Azure est conçu pour vous fournir les ressources que nécessaires, lorsque vous en avez besoin. Dans le cadre d’une bonne conception et pratiques opérationnelles, vous devez connaître les méthodes d’architecture de votre utilisation des services Azure disponibilité élevée ainsi que ce qu’il faut faire si votre application affectée par une interruption de service. Pour vous aider dans ce processus, ce document contient des liens vers des instructions de récupération d’urgence, ainsi que des conseils de conception pour différents services Azure.

##<a name="disaster-recovery-guidance"></a>Instructions de récupération d’urgence
Les instructions de récupération d’urgence liens ci-dessous sont peuvent vous fournir les informations nécessaires pour vous aider à rapidement votre application en ligne qui vous sont affectés par une interruption de service Azure. Ces liens ont été créés pour vous aider à répondre à la question, « Je suis en cours affectés par une interruption de service Azure, que puis-je faire ? »

##<a name="design-guidance"></a>Conseils de conception
Les liens du Guide de conception ci-dessous sont création et des conseils architecturale qui a été créé pour vous aider à comprendre la meilleure façon d’utiliser chaque service Azure d’une manière qui optimise la disponibilité de votre application. Ces liens ont été créés pour vous aider à répondre à la question « Comment puis-je assurer qu’un bogue, défaillance matérielle, interruption de service ou autres n’affecte pas la disponibilité globale de mon application ? » S’il n’existe aucune recommandation spécifique pour le service que vous cherchez actuellement, l’article de la [disponibilité des applications basées sur Microsoft Azure](./resiliency-high-availability-azure-applications.md) des informations supplémentaires qui peuvent vous aider dans votre conception. 

##<a name="service-guidance"></a>Conseils de service
| Service  | Instructions de récupération d’urgence | Conseils de conception |
|:---------|:--------------------------:|:------------------:|
| [Services en nuage] (https://azure.microsoft.com/services/cloud-services/ "Services en nuage Azure")       | [lien] (../cloud-services/cloud-services-disaster-recovery-guidance.md "Instructions de récupération Azure Cloud Services d’urgence")   | Non disponible |
| [Archivage sécurisé clé] (https://azure.microsoft.com/services/key-vault/ "Archivage sécurisé clé Azure")                      | [lien] (../key-vault/key-vault-disaster-recovery-guidance.md "Instructions de récupération d’urgence l’archivage sécurisé de clé Azure")        | Non disponible |
| [Espace de stockage] (https://azure.microsoft.com/services/storage/ "Stockage Azure")                            | [lien] (../storage/storage-disaster-recovery-guidance.md "Instructions de récupération d’urgence stockage Azure")          | Non disponible |
| [Bases de données SQL] (https://azure.microsoft.com/services/sql-database/ "Bases de données SQL Azure")           | [lien] (../sql-database/sql-database-disaster-recovery.md  "Instructions de récupération d’urgence de base de données SQL Azure")    | [lien] (../sql-database/sql-database-business-continuity.md "Vue d’ensemble de la continuité avec la base de données SQL Azure") |
| [Machines virtuelles] (https://azure.microsoft.com/services/virtual-machines/ "Machines virtuelles Azure") | [lien] (../virtual-machines/virtual-machines-disaster-recovery-guidance.md "Instructions de récupération d’urgence Machines virtuelles Azure") | Non disponible |
| [Réseau virtuel] (https://azure.microsoft.com/services/virtual-network/ "Réseau virtuel Azure")    | [lien] (../virtual-network/virtual-network-disaster-recovery-guidance.md "Instructions de récupération d’urgence réseau virtuel Azure")  | Non disponible |

##<a name="next-steps"></a>Étapes suivantes
Si vous recherchez pour obtenir des instructions qui met l’accent sur les solutions et les systèmes plus large, lisez [sinistre et la disponibilité des applications basées sur Microsoft Azure](https://aka.ms/drtechguide).
