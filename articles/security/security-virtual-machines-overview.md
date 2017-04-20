<properties
   pageTitle="Présentation de la sécurité Azure Machines virtuelles | Microsoft Azure"
   description=" Machines virtuelles Azure vous offre la possibilité de virtualisation sans avoir à acheter et mettre à jour le matériel qui s’exécute la machine virtuelle.  Cet article fournit une vue d’ensemble des principales fonctionnalités de sécurité Azure qui peuvent être utilisées avec le Machines virtuelles Azure. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="azure-virtual-machines-security-overview"></a>Présentation de la sécurité Machines virtuelles Azure

Machines virtuelles Azure vous permet de déployer un large éventail de solutions informatiques d’une manière agile. Prend en charge Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP et Azure BizTalk Services, vous pouvez déployer les charges de travail et n’importe quelle langue sur pratiquement n’importe quel système d’exploitation.

Une machine virtuelle Azure vous offre la possibilité de virtualisation sans avoir à acheter et mettre à jour le matériel qui s’exécute la machine virtuelle.  Vous pouvez créer et déployer vos applications avec l’assurance que vos données sont protégées et sont-ils en sécurité dans nos centres de données hautement sécurisé.

Grâce Azure, vous pouvez créer des solutions sécurité renforcée, respecter les exigences qui :

- Protéger vos machines virtuelles contre les virus et les logiciels malveillants
- Chiffrer vos données sensibles
- Sécuriser le trafic réseau
- Identifier et détecter des menaces
- Répondre aux exigences de conformité

L’objectif de cet article consiste à fournir une vue d’ensemble des principales fonctionnalités de sécurité Azure qui peuvent être utilisées avec des machines virtuelles. Nous fournissons également des liens vers des articles qui donnent des détails de chaque fonctionnalité afin que vous pouvez en savoir plus.  

Les fonctionnalités de sécurité core Machine virtuelle Azure qui seront traités dans cet article :

- Contre les logiciels malveillants
- Module de sécurité matériel
- Chiffrement de disque machine virtuelle
- Sauvegarde des machines virtuelles
- Récupération de Site Azure
- Mise en réseau virtuelle
- Gestion des stratégies de sécurité et de création de rapports
- Conformité

## <a name="antimalware"></a>Contre les logiciels malveillants

Avec Azure, vous pouvez utiliser le logiciel contre les logiciels malveillants des fournisseurs de sécurité tels que Microsoft Symantec, tendance Micro, McAfee et Kaspersky pour protéger vos machines virtuelles à partir de fichiers malveillants, publicitaires et d’autres menaces. Consultez la section plus d’informations ci-dessous pour accéder à des articles sur partenaire de solutions.

Microsoft Antimalware pour les Services en nuage Azure et Machines virtuelles est une fonctionnalité de protection en temps réel qui vous aide à identifier et supprimer les virus, les logiciels espions et autres logiciels malveillants.  Microsoft Antimalware fournit des alertes configurables Lorsque connu logiciels malveillants ou indésirables essaie d’installer ou de s’exécuter sur vos systèmes Azure.

Microsoft Antimalware est une solution approchant pour les applications et les environnements client, conçus pour s’exécuter en arrière-plan sans intervention humaine. Vous pouvez déployer une protection en fonction des besoins de votre application les charges de travail, avec deux base sécurisés par défaut ou configuration personnalisée, y compris la surveillance contre les logiciels malveillants avancée.

Lorsque vous déployez et activez Microsoft Antimalware, les principales fonctionnalités suivantes sont disponibles :

- Protection en temps réel - surveille l’activité dans les Services en nuage et sur des Machines virtuelles pour détecter et bloquer l’exécution des programmes malveillants.
- Analyse programmée - effectue régulièrement des analyses ciblées pour détecter les programmes malveillants, y compris les programmes en cours d’exécution.
- Mise à jour les programmes malveillants - intervient automatiquement sur les programmes malveillants détectés, telles que la suppression ou la mise en quarantaine des fichiers malveillants et nettoyage des entrées de Registre malveillants.
- Mises à jour de la signature - automatiquement installations les dernières signatures de protection (définitions de virus) pour assurer une protection est à jour sur une fréquence prédéfinie.
- Moteur contre les logiciels malveillants – met automatiquement à jour le moteur Microsoft Antimalware mis à jour.
- Plateforme contre les logiciels malveillants – met automatiquement à jour mises à jour de la plateforme Microsoft Antimalware.
- Protection active - signale les métadonnées de télémétrie Azure menaces détectées et ressources suspects pour assurer une réponse rapide et permet de fournir des signatures synchrone en temps réel par le biais du système Microsoft Active Protection (MAPS).
- Exemples de création de rapports - fournit les rapports et exemples au service Microsoft Antimalware pour affiner le service et activer la résolution des problèmes.
- Exclusions – permet d’application et les administrateurs de service pour configurer certains fichiers, processus et lecteurs cachés les exclure de la protection et analyse des performances et d’autres raisons.
- Collecte des événements contre les logiciels malveillants - enregistre l’intégrité du service contre les logiciels malveillants, les activités suspectes et les actions de mise à jour spécifiées dans le journal des événements système d’exploitation et les collecte en considération de stockage Azure du client.

En savoir plus : pour plus d’informations sur les logiciels contre les logiciels malveillants pour protéger vos ordinateurs virtuels, voir :

- [Microsoft contre les logiciels malveillants pour les Services de Cloud Azure et Machines virtuelles](../security/azure-security-antimalware.md)
- [Déployer des Solutions contre les logiciels malveillants sur Machines virtuelles Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
- [Découvrez comment installer et configurer la sécurité complète du Micro tendance en tant que Service sur un ordinateur Windows virtuel](../virtual-machines/virtual-machines-windows-classic-install-trend.md)
- [Comment installer et configurer Symantec Endpoint Protection sur un ordinateur Windows virtuel](../virtual-machines/virtual-machines-windows-classic-install-symantec.md)
- [Nouvelles Options contre les logiciels malveillants pour la protection des Machines virtuelles Azure – McAfee Endpoint Protection](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)
- [Solutions de sécurité dans la Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

## <a name="hardware-security-module"></a>Sécurité matérielle Module

Chiffrement et authentification n’améliorent pas sécurité, à moins que les clés elles-mêmes sont protégés. Vous pouvez simplifier la gestion et la sécurité de vos secrets critiques et clés en les stockant dans l’archivage sécurisé de clé Azure. Clé de l’archivage sécurisé permet de stocker vos clés dans les modules de sécurité matérielle (HSM) certifiés FIPS 140-2 niveau 2 normes. Vos clés de chiffrement de SQL Server pour la sauvegarde ou [chiffrement de données transparent](https://msdn.microsoft.com/library/bb934049.aspx) peuvent tous être stockées dans l’archivage sécurisé clé avec les touches ou secrets à partir de vos applications. Autorisations et l’accès à ces éléments protégés sont gérés par le biais [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Pour en savoir plus :

- [Quel est l’archivage sécurisé de clé Azure ?](../key-vault/key-vault-whatis.md)
- [Prise en main l’archivage sécurisé de clé Azure](../key-vault/key-vault-get-started.md)
- [Blog de l’archivage sécurisé clé Azure](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Chiffrement de disque machine virtuelle

Chiffrement de disque Azure est une nouvelle fonctionnalité qui vous permet de chiffrer vos disques Windows et Linux Azure Virtual Machine. Le chiffrement des disques Azure utilise la fonction [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) standard du secteur de Windows et la fonctionnalité [dm crypt](https://en.wikipedia.org/wiki/Dm-crypt) de Linux pour fournir le chiffrement de volume pour le système d’exploitation et les disques de données.

La solution est intégrée à l’archivage sécurisé de clé Azure pour vous aider à contrôler et de gérer les clés de chiffrement de disque et secrets dans votre abonnement de l’archivage sécurisé clés, tout en garantissant que toutes les données dans les disques machine virtuelle sont chiffrées au reste dans votre espace de stockage Azure.

Pour en savoir plus :

- [Chiffrement de disque Azure pour Windows et Linux IaaS machines virtuelles](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)
- [Chiffrement de disque Azure pour Linux et Machines virtuelles Windows](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview-now-available/)
- [Chiffrer une machine virtuelle](../security-center/security-center-disk-encryption.md)

## <a name="virtual-machine-backup"></a>Sauvegarde des machines virtuelles

Sauvegarde Azure est une solution scalable qui protège vos données d’application avec zéro investissement de capitaux et les coûts d’exploitation minimales. Erreurs d’application peuvent endommager vos données et les erreurs humaines peuvent introduire des bogues dans vos applications. Avec sauvegarde Azure, vos machines virtuelles exécutant Windows et Linux sont protégés.

Pour en savoir plus :

- [Quelle est la sauvegarde Azure ?](../backup/backup-introduction-to-azure-backup.md)
- [Rubriques d’apprentissage sauvegarde Azure](https://azure.microsoft.com/documentation/learning-paths/backup/)
- [Service de sauvegarde Azure - Forum aux questions](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Récupération de Site Azure

Une partie importante de BCDR stratégie votre organisation est déterminer comment conserver les charges de travail d’entreprise et les applications vers le haut et en cours d’exécution lorsque les interruptions planifiées et se produisent. Azure récupération de Site vous permet d’organiser la réplication, le basculement et récupération de charges de travail et applications afin qu’ils soient disponibles à partir d’un emplacement secondaire si votre emplacement principal s’arrête.

Récupération de site :

- **Simplifie votre stratégie BCDR** — récupération de Site facilite la poignée de réplication, le basculement et récupération de plusieurs charges de travail métier et applications à partir d’un emplacement unique. Récupération de site orchestre réplication et basculement mais n’intercepter vos données d’application ou demandez à des informations.
- **Fournit la réplication flexible** — à l’aide de la récupération de Site, vous pouvez répliquer charges de travail en cours d’exécution sur les ordinateurs virtuels Hyper-V, machines virtuelles VMware et serveurs physiques Windows/Linux.
- **Basculement prend en charge et restauration** , récupération de Site fournit basculement test pour prendre en charge des exercices de récupération d’urgence sans toucher aux environnements de production. Vous pouvez également exécuter basculement planifié avec une perte de données zéro des coupures attendus ou non planifiée basculement avec perte de données minimale (selon la fréquence de réplication) pour incidents inattendus. Après le basculement, vous pouvez retour arrière à vos sites principales. Récupération de site fournit des plans de récupération comprenant des scripts et des classeurs automation Azure afin que vous pouvez personnaliser le basculement et la restauration des applications à plusieurs niveaux.
- **Centre de données secondaire supprime** — vous pouvez répliquer vers un site secondaire en local, ou sur Azure. L’utilisation d’Azure comme destination de récupération d’urgence élimine le coût et la complexité de la gestion d’un site secondaire. Les données répliquées sont stockées dans le stockage Azure.
- **Intégration avec les technologies BCDR existantes** — récupération de Site collabore avec d’autres fonctionnalités de BCDR l’application. Par exemple, vous pouvez utiliser la récupération de Site pour protéger SQL Server principaux dans les charges de travail d’entreprise. Cela inclut une prise en charge native pour SQL Server AlwaysOn gérer le basculement des groupes de disponibilité.

Pour en savoir plus :

- [Quelle est la récupération de Site Azure ?](../site-recovery/site-recovery-overview.md)
- [Comment fonctionne la récupération de Site Azure ?](../site-recovery/site-recovery-components.md)
- [Ce que les charges de travail sont protégés par récupération de Site Azure ?](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Mise en réseau virtuelle

Machines virtuelles besoin de la connectivité réseau. Pour prendre en charge cette exigence, Azure nécessite machines virtuelles être connecté à un réseau virtuel Azure. Un réseau virtuel Azure est une construction logique intégrée en haut de la structure de réseau Azure physique. Chaque réseau virtuel Azure logique est isolé de tous les autres réseaux virtuels Azure. Cet isolement permet de vous assurer que le trafic réseau dans vos déploiements n’est pas accessible aux autres utilisateurs de Microsoft Azure.

Pour en savoir plus :

- [Présentation de la sécurité réseau Azure](security-network-overview.md)
- [Vue d’ensemble du réseau virtuel](../virtual-network/virtual-networks-overview.md)
- [Fonctionnalités de mise en réseau et partenaires de scénarios d’entreprise](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Gestion des stratégies de sécurité et de création de rapports

Centre de sécurité Azure vous aide à prévenir, détecter et répondre aux menaces et fournit augmentée de visibilité et contrôler, la sécurité de vos ressources Azure. Il assure la gestion de surveillance et la stratégie de sécurité intégrée sur vos abonnements Azure, permettant de détecter les menaces qui pourraient passer inaperçus et fonctionne avec une large réseau de solutions de sécurité.

Centre de sécurité Azure vous permet d’optimiser et contrôler la sécurité des machines virtuelles par :

- Indication de machine virtuelle [recommandations de sécurité](../security-center/security-center-recommendations.md) tels qu’appliquer des mises à jour système, configurer des points de terminaison utilisateurs, activer contre les logiciels malveillants, activer groupes de sécurité réseau et appliquer le chiffrement des disques.
- Contrôle de l’état de vos machines virtuelles

Pour en savoir plus :

- [Introduction au centre de sécurité Azure](../security-center/security-center-intro.md)
- [Centre de sécurité Azure Forum aux Questions](../security-center/security-center-faq.md)
- [Centre de sécurité Azure planification et opérations](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Conformité

Machines virtuelles Azure est certifiée pour la loi FISMA, FedRAMP, HIPAA, PCI DSS niveau 1 et d’autres programmes de conformité clés. Cette certification facilite leur pour vos propres applications Azure répondre aux exigences de conformité et pour votre entreprise à répondent à un large éventail d’exigences réglementaires appels nationaux et internationaux.

Pour en savoir plus :

- [Centre de gestion de la confidentialité de Microsoft : la conformité](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)
- [Approuvé Cloud : Microsoft Azure sécurité, confidentialité et conformité](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)
