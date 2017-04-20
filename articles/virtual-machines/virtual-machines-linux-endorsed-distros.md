<properties
    pageTitle="Approuvée répartitions de Linux | Microsoft Azure"
    description="En savoir plus sur Linux sur répartitions approuvée Azure, y compris les instructions pour Ubuntu, OpenLogic, Oracle et SUSE."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management,azure-resource-manager"
    />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="szark"/>



#<a name="linux-on-azure-endorsed-distributions"></a>Linux sur répartitions approuvée Azure

> [AZURE.NOTE] Si vous avez quelques instants, Aidez-nous à améliorer la documentation machine virtuelle Linux Azure en [vue d’ensemble](https://aka.ms/linuxdocsurvey) de votre expérience. Chaque réponse nous permet de vous aident à rendre votre travail.

Les images Linux dans la galerie Azure ou Marketplace sont fournies par un nombre des partenaires et nous travaillons avec différentes Communautés Linux pour ajouter davantage de types à la liste de Distribution approuvée. En attendant, pour répartition n’est pas disponible dans la galerie vous pouvez toujours mettre-Your-appartient-Linux en suivant les instructions de [cette page](virtual-machines-linux-classic-create-upload-vhd.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="supported-distributions--versions"></a>Versions prises en charge et les Versions ##

Le tableau suivant répertorie les versions de Linux et les versions prises en charge sur Azure. Reportez-vous à la [prise en charge des images Linux dans Microsoft Azure](https://support.microsoft.com/en-us/kb/2941892) pour plus d’informations.

Les pilotes Linux Services liste (d’intégration verticale) pour Hyper-V et Azure sont les modules de noyau Microsoft contribue directement au noyau Linux en amont.  Les pilotes de la liste de verticale soit intégrées noyau de la distribution par défaut, ou pour les ancien RHEL/CentOS-based répartitions sont disponibles sous forme de téléchargement séparé [ici](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409).  Consultez [cet article](virtual-machines-linux-create-upload-generic.md#linux-kernel-requirements) pour plus d’informations sur les pilotes de la liste de verticale.

L’Agent de Linux Azure est déjà préinstallé sur les images Galerie Azure et êtes généralement disponible à partir du référentiel de package de la distribution.  Vous trouverez de code source sur [GitHub](https://github.com/azure/walinuxagent).

Distribution|Version|Axes stratégiques|Agent
---|---|---|---
CentOS par OpenLogic | CentOS 6.3 + 7.0 + | CentOS 6.3 : [liste de verticale télécharger](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)<p>CentOS 6.4 + : Dans le noyau | : Du package [mis en pension OpenLogic](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) sous « WALinuxAgent » <br/>Code source : [GitHub](https://github.com/Azure/WALinuxAgent)
[CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) | 494.4.0+ | Dans le noyau | Code source : [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent)
Debian | Debian 7.9 +, 8.2 + | Dans le noyau | : Du package mis en pension sous « waagent » <br/>Code source : [GitHub](https://github.com/Azure/WALinuxAgent)
Linux Oracle | 6.4 +, 7.0 + | Dans le noyau | : Du package mis en pension sous « WALinuxAgent » <br/>Code source : [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
Chapeau rouge Enterprise Linux | RHEL 6,7 + 7.1 + | Dans le noyau|: Du package mis en pension sous « WALinuxAgent » <br/>Code source : [GitHub](https://github.com/Azure/WALinuxAgent)
SUSE Linux Enterprise | Le Service Pack 4 de SLES 11, SLES 12 SP1 + et <p> SLES pour SAP 11 SP3 + | Dans le noyau | Package : Dans le [Cloud : outils](https://build.opensuse.org/project/show/Cloud:Tools) mis en pension sous « agent d’azure python » <br/>Code source : [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
openSUSE | openSUSE 13.2 + | Dans le noyau | Package : Dans le [Cloud : outils](https://build.opensuse.org/project/show/Cloud:Tools) mis en pension sous « agent d’azure python » <br/>Code source : [GitHub](https://github.com/Azure/WALinuxAgent)
Ubuntu|Ubuntu 12.04, 14.04, 16.04, 16.10 | Dans le noyau | : Du package mis en pension sous « walinuxagent » <br/>Code source : [GitHub](https://github.com/Azure/WALinuxAgent)


## <a name="partners"></a>Partenaires

### <a name="openlogic"></a>OpenLogic
[http://www.openlogic.com/Azure](http://www.openlogic.com/azure)

OpenLogic est un leader ouvrir la source de solutions d’entreprise pour le cloud et le centre de données. OpenLogic vous aide à des centaines d’entreprise une prise au sein d’un large éventail de secteurs à acquérir en toute sécurité, prend en charge et contrôler les logiciels open source. OpenLogic offre le support technique de qualité commerciale et indemnisation pour 600 libres packages sauvegardée par la Communauté Expert OpenLogic, y compris support au niveau entreprise CentOS ainsi que le partenaire lancement permettant d’images basées sur CentOS sur Azure.

### <a name="coreos"></a>CoreOS
[https://CoreOS.com/docs/Running-CoreOS/cloud-Providers/Azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Depuis le site Web CoreOS :

*CoreOS est conçu pour la sécurité, la cohérence et la fiabilité. Au lieu de l’installation de packages via yum ou nature, CoreOS utilise Linux conteneurs pour gérer vos services à un niveau supérieur d’abstraction. Code d’un service unique et toutes les dépendances sont empaquetés dans un conteneur qui peut être exécuté sur un ou plusieurs ordinateurs CoreOS.*


### <a name="credativ"></a>Credativ
[http://www.credativ.co.uk/credativ-blog/Debian-images-Microsoft-Azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ est une société de services, spécialisé dans le développement et d’implémentation de solutions professionnelles au moyen de logiciels gratuits et de consulting indépendante. En tant que spécialistes Open Source non significatif, Credative a reconnaissance internationale avec nombreux services informatiques à l’aide de leur prise en charge. Conjointement avec Microsoft, Credativ prépare actuellement images Debian correspondantes pour Debian 8 (Jessica) et Debian avant 7 (Wheezy), qui sont spécialement conçu pour fonctionner sur Azure et peuvent être facilement gérés via la plateforme. Credativ prend également en charge la maintenance à long terme et mise à jour des images Debian pour Azure son ouvrir Source des centres de prise en charge.

### <a name="oracle"></a>Oracle
[http://www.Oracle.com/technetwork/topics/cloud/FAQ-1963009.HTML](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Stratégie d’Oracle consiste à proposer une large gamme de solutions pour les nuages privées et, tout en donnant des clients choix et une flexibilité dans comment déployer des logiciels Oracle nuages Oracle, ainsi que d’autres nuages.  Partenariat de Oracle avec Microsoft permet aux clients déployer des logiciels Oracle dans nuages publiques et privées Microsoft avec la confiance de certification et prend en charge d’Oracle.  Engagement et investissement dans les solutions de cloud publiques et privées Oracle Oracle est inchangée.

### <a name="red-hat"></a>Chapeau rouge
[http://www.redhat.com/en/partners/Strategic-Alliance/Microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

Premier fournisseur des solutions d’ouvrir la source du monde, chapeau rouge vous aide à plus de 90 % des sociétés Fortune 500 résoudre les problèmes, aligner leur informatique et les stratégies d’entreprise et le préparer pour l’avenir de la technologie. Pour cela, chapeau rouge fournissant des solutions sécurisées via un modèle commercial ouvert et un modèle d’abonnement économique et prévisibles.

### <a name="suse"></a>SUSE
[http://www.SuSE.com/SuSE-Linux-Enterprise-Server-on-Azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server sur Azure est une plateforme éprouvée qui offre une plus grande fiabilité et sécurité pour le cloud computing. Plate-forme Linux polyvalent de SUSE s’intègre en toute transparence avec les services cloud Azure pour offrir un environnement cloud facilement gérables. Et avec plus de 9,200 applications certifiées provenant de plus de 1 800 éditeurs de logiciels pour SUSE Linux Enterprise Server, SUSE garantit que les charges de travail en cours d’exécution pris en charge dans le centre de données peuvent être confiance déployés sur Azure.

### <a name="canonical"></a>Canonique
[http://www.Ubuntu.com/cloud/Azure](http://www.ubuntu.com/cloud/azure)

Ingénierie canonique et succès de Ubuntu lecteur Communauté ouverte gouvernance dans les clients et le cloud computing, y compris les services de cloud personnel pour les consommateurs. Vision de canoniques d’une plate-forme unifiée gratuite sous Ubuntu, à partir de téléphone vers le cloud, avec une gamme d’interfaces cohérents de téléphone, tablette, TV et de bureau, rend Ubuntu le premier choix pour les établissements centralisées à partir des fournisseurs de nuage public pour les fabricants d’activités consumer electronics et un favori parmi les informaticiens individuels.

Les développeurs et les ingénieurs centres dans le monde entier, canoniques est positionnement unique pour collaborer avec les fabricants de matériel, les fournisseurs de contenu et les développeurs de logiciels pour fournir des solutions Ubuntu sur le marché, à partir d’ordinateurs aux serveurs et périphériques de poche.

