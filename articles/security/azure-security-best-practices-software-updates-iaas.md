<properties
   pageTitle="Meilleures pratiques concernant les mises à jour logicielles sur Microsoft Azure IaaS | Microsoft Azure"
   description="Article regroupe les meilleures pratiques pour les mises à jour logicielles dans un environnement de Microsoft Azure IaaS.  Il est conçu pour les informaticiens et analystes de sécurité qui traitent modifier le contrôle, gestion des biens et mise à jour de logiciels sur une base quotidienne, y compris ceux responsables de la sécurité et conformité de leur organisation."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="best-practices-for-software-updates-on-microsoft-azure-iaas"></a>Meilleures pratiques pour les mises à jour logicielles sur Microsoft Azure IaaS

Avant de plonger dans n’importe quel type de discussion sur les meilleures pratiques pour un environnement Azure [IaaS](https://azure.microsoft.com/overview/what-is-iaas/) , il est important de comprendre les scénarios qui aura vous gestion des mises à jour logicielles et les responsabilités. L’illustration ci-dessous vous aideront à comprendre ces limites.

![Modèles de cloud et charge](./media/azure-security-best-practices-software-updates-iaas/sec-cloudstack-new.png)

La colonne la plus à gauche affiche sept responsabilités (définies dans les sections qui suivent) que les organisations doivent envisager, ce qui contribuent à la sécurité et la confidentialité d’un environnement informatique.
 
Classification des données et responsabilité et protection Client et point de terminaison sont les responsabilités qui se trouvent uniquement dans le domaine des clients et responsabilités physique hôte et de réseau se trouvent dans le domaine des fournisseurs de services de cloud dans les modèles PaaS et SaaS. 

Les responsabilités restantes sont partagées entre les clients et les fournisseurs de services en nuage. Certaines responsabilités nécessitent le fournisseur et le client gérer et administrer la responsabilité ensemble, notamment l’audit de leurs domaines. Par exemple, vous pouvez identité et accéder à la gestion lors de l’utilisation des Services Active Directory Azure ; la configuration des services tels que l’authentification multifacteur est le client, mais garantissant fonctionnalité efficace est la responsabilité de Microsoft Azure.

> [AZURE.NOTE] Pour plus d’informations sur les responsabilités partagées dans le cloud, lisez [Partagé responsabilités de Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf) 

Ces mêmes principes s’appliquent dans un scénario hybride où votre société utilise machines virtuelles IaaS Azure de communiquer avec les ressources locales comme le montre l’illustration ci-dessous.

![Scénario classique hybride avec Microsoft Azure](./media/azure-security-best-practices-software-updates-iaas/sec-azconnectonpre.png)

## <a name="initial-assessment"></a>Évaluation initiale

Même si votre société utilise déjà un système de gestion des mises à jour et que vous avez déjà des stratégies de mise à jour logicielle en place, il est important de fréquemment reprenez précédentes évaluations de stratégie et les mettre à jour en fonction de vos exigences actuelles. Cela signifie que vous devez être familiarisé avec l’état actuel des ressources de votre entreprise. Pour accéder à cet état, vous devez connaître :

-   Les ordinateurs physiques et virtuels de votre entreprise.

-   Systèmes d’exploitation et versions s’exécutant sur chacun de ces ordinateurs physiques et virtuels.

-   Mises à jour logicielles installés sur chaque ordinateur (versions des service packs, mises à jour logicielles et les autres modifications).

-   La fonction que chaque ordinateur exécute de votre entreprise.

-   Les applications et les programmes s’exécutant sur chaque ordinateur.

-   Informations de la propriété et de contact pour chaque ordinateur.

-   Les biens présentent dans votre environnement et leur valeur relative afin de déterminer quelles zones les plus attention et la protection.

-   Problèmes connus de sécurité et les processus de votre entreprise a mis en place pour identifier les problèmes de sécurité nouveaux ou des modifications de niveau de sécurité.

-   Mesures déployés pour sécuriser votre environnement.

Vous devez mettre à jour ces informations régulièrement, et il doit être accessibles aux personnes impliquées dans votre processus de gestion de mise à jour logicielles.

## <a name="establish-a-baseline"></a>Établir un planning de référence

Une partie importante du processus de gestion de mise à jour de logiciels est création initiales installations standard de versions des systèmes d’exploitation, des applications et le matériel pour les ordinateurs de votre entreprise ; Il s’agit des plannings de référence. Un planning de référence est la configuration d’un produit ou un système établie à un point précis dans le temps. Une application ou un système d’exploitation référence, par exemple, offre la possibilité pour recréer un ordinateur ou un service à un état spécifique.

Plannings de référence servent de base pour rechercher et résoudre les problèmes potentiels et simplifient le processus de gestion de mise à jour de logiciels, à la fois en réduisant le nombre de mises à jour logicielles que vous devez déployer dans votre entreprise et en augmentant la possibilité de contrôler la conformité.

Après avoir effectué l’audit initiale de votre entreprise, vous devez utiliser les informations provenant de l’audit pour définir un planning de référence opérationnel pour les composants informatiques au sein de votre environnement de production. Un certain nombre d'entre elles peut-être être nécessaire, selon les différents types de configuration matérielle et logicielle déployée en production.

Par exemple, certains serveurs nécessitent une mise à jour logicielle pour empêcher le retrait lorsqu’ils entrent le processus d’arrêt lors de l’exécution de Windows Server 2012. Un planning de référence pour ces serveurs doit inclure cette mise à jour logicielle.

Dans les grandes organisations, il est souvent judicieux de diviser les ordinateurs de votre entreprise en catégories de biens et conserver chaque catégorie à un planning de référence standard en utilisant les mêmes versions des logiciels et mises à jour logicielles. Vous pouvez ensuite utiliser ces catégories de biens de hiérarchiser une distribution de mise à jour logicielles.

## <a name="subscribe-to-the-appropriate-software-update-notification-services"></a>S’abonner aux services de notification de mise à jour de logiciel approprié

Une fois que vous effectuez un audit initial du logiciel en cours d’utilisation de votre entreprise, vous devez déterminer la méthode la plus appropriée pour recevoir des notifications de nouvelles mises à jour logicielles pour chaque produit logiciel et la version. En fonction du produit logiciel, la méthode de notification peut-être être des notifications par courrier électronique, les sites Web ou les publications informatique.

Par exemple, le centre de réponse sécurité Microsoft (MSRC) répond à tous les problèmes liés à la sécurité sur les produits Microsoft et fournit le Service de Bulletin de sécurité Microsoft, une notification par courrier électronique gratuit de vulnérabilités nouvellement identifiées et mises à jour logicielles qui sont publiées pour résoudre ces problèmes. Vous pouvez vous abonner à ce service en http://www.microsoft.com/technet/security/bulletin/notify.mspx.

## <a name="software-update-considerations"></a>Considérations relatives à la mise à jour logicielles

Une fois que vous effectuez un audit initial du logiciel en cours d’utilisation de votre entreprise, vous devez déterminer la configuration requise pour configurer votre système de gestion de mise à jour logicielle, qui dépend du système de gestion de mise à jour logicielle que vous utilisez. Pour WSUS lire les [Meilleures pratiques de Windows Server Update Services](https://technet.microsoft.com/library/Cc708536), pour System Center lisez [planification des mises à jour logicielles dans le Gestionnaire de Configuration](https://technet.microsoft.com/library/gg712696).

Il existe cependant certaines généralités et des pratiques recommandées que vous pouvez appliquer quelle que soit la solution que vous utilisez comme indiqué dans les sections qui suit.

### <a name="setting-up-the-environment"></a>La configuration de l’environnement

Vous pouvez mettre à jour les pratiques suivantes lorsque vous envisagez de configurer les logiciels environnement de gestion :

-   **Collections de mise à jour de logiciels de production créer en fonction de critères stables**: en règle générale, l’utilisation de critères stables pour créer des collections de sites pour votre inventaire de mise à jour de logiciels et de distribution aidera pour simplifier toutes les étapes du processus de gestion de mise à jour logicielles. Les critères stables peuvent inclure la version du système d’exploitation client installé et niveau de service pack, rôle système ou organisation cible.

-   **Créer des collections de production préliminaire qui incluent les ordinateurs de référence**: la collection de production pre inclut des configurations représentant des versions du système d’exploitation, ligne de logiciels d’entreprise et autres logiciels en cours d’exécution de votre entreprise.

Vous devez également envisager l’endroit où le serveur de mise à jour de logiciels seront situé, si elle se trouve dans l’infrastructure IaaS Azure dans le nuage ou si elle sera local. Il s’agit d’une décision importante, car vous devez évaluer la quantité de trafic entre les ressources locales et Azure infrastructure. Pour plus d’informations sur la connexion de votre infrastructure en local sur Azure, lisez [se connecter un réseau local à un réseau virtuel Microsoft Azure](https://technet.microsoft.com/library/Dn786406.aspx) .

Les options de conception qui détermineront à l’endroit où le serveur de mise à jour seront situé varient également selon votre infrastructure actuelle et système de mise à jour du logiciel que vous utilisez actuellement. Pour WSUS lisez [Déployer des Services de mise à jour de Windows Server dans votre organisation](https://technet.microsoft.com/library/hh852340.aspx) ainsi que pour le Gestionnaire de Configuration de System Center [planification des Sites et des hiérarchies dans le Gestionnaire de Configuration](https://technet.microsoft.com/library/Gg712681.aspx).

### <a name="backup"></a>Sauvegarde

Des sauvegardes régulières sont importantes non seulement pour la mise à jour de gestion des plate-forme logicielle lui-même mais également pour les serveurs est mise à jour. Qui ont un [processus de gestion des changements](https://technet.microsoft.com/library/cc543216.aspx) en place auront besoin informatique à justifier pour pourquoi le serveur doit être mis à jour, le temps d’arrêt estimée et impact éventuel. Pour vous assurer que vous avez une configuration annulation en place en cas d’échec d’une mise à jour, veillez à sauvegarder régulièrement le système.

Certaines options de sauvegarde pour Azure IaaS incluent :

-   [Protection de la charge de travail IaaS Azure à l’aide du Gestionnaire de Protection des données](https://azure.microsoft.com/blog/2014/09/08/azure-iaas-workload-protection-using-data-protection-manager/)

-   [Sauvegarder des machines virtuelles Azure](../backup/backup-azure-vms.md)

### <a name="monitoring"></a>Surveillance des mots clés

Vous devez exécuter des rapports normales pour contrôler le nombre de manquant ou installé les mises à jour, ou dont l’état incomplet, pour chaque mise à jour logicielle est autorisé. De même, création de rapports pour les mises à jour logicielles qui ne sont pas encore autorisé peut faciliter la plus facile de décisions.

Vous devez également prendre en compte les tâches suivantes :

-   Effectuer un audit des mises à jour de sécurité applicables et installées pour tous les ordinateurs de votre société.

-   Autoriser et déployer les mises à jour sur les ordinateurs appropriés.

-   Effectuer le suivi de l’inventaire et mettre à jour d’état de l’installation et l’avancement de tous les ordinateurs de votre entreprise.

En outre au Généralités qui ont été expliqués dans cet article, vous devez également envisager chaque produit beaucoup de meilleures de l’exercice pratique, par exemple : Si vous avez une machine virtuelle dans Azure avec SQL Server, vérifiez que vous suivez les recommandations relatives aux mises à jour de logiciels pour ce produit.

## <a name="next-steps"></a>Étapes suivantes

Suivez les instructions décrites dans cet article pour vous aider à déterminer les meilleures options pour les mises à jour logicielles pour machines virtuelles au sein d’Azure IaaS. Il existe de nombreuses similitudes entre les pratiques recommandées mise à jour du logiciel dans un centre de données traditionnel et Azure IaaS, par conséquent, il est recommandé que vous évaluez vos stratégies de mise à jour logicielle en cours pour incluent des machines virtuelles Azure et les meilleures pratiques pertinentes à partir de cet article dans le processus de mise à jour logicielles global.
