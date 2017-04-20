<properties
   pageTitle="Suivi des ressources dans les opérations de sécurité Suite de gestion et d’Audit Solution | Microsoft Azure"
   description="Ce document vous permet d’utiliser la sécurité OMS et Audit des fonctionnalités pour surveiller vos ressources et identifier les problèmes de sécurité."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="monitoring-resources-in-operations-management-suite-security-and-audit-solution"></a>Suivi des ressources dans opérations gestion Suite Solution de sécurité et d’Audit

Ce document vous permet d’utiliser OMS fonctions de sécurité et d’Audit pour surveiller vos ressources et identifier les problèmes de sécurité.

## <a name="what-is-oms"></a>Qu’est OMS ?

Suite de gestion des opérations Microsoft (OMS) repose sur cloud de Microsoft solution de gestion informatique qui vous permet de gérer et protéger vos localement et infrastructure en nuage. Pour plus d’informations sur OMS, consultez l’article [Opérations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="monitoring-resources"></a>Surveillance des ressources

Dès que possible, vous souhaiterez empêcher incidents de sécurité en premier lieu. Toutefois, il est impossible d’empêcher tous les incidents de sécurité. Lorsqu’un incident de sécurité se produit-il, vous devez vous assurer que son impact est réduit.  Il existe trois recommandations critiques pouvant être utilisées pour réduire le nombre et l’impact des incidents de sécurité :

- Évaluer régulièrement les vulnérabilités dans votre environnement.
- Vérifier régulièrement tous les systèmes informatiques et périphériques réseau pour vous assurer qu’ils disposent tous les derniers correctifs installé.
- Vérifier régulièrement tous les journaux et mécanismes de journalisation, y compris les journaux d’événements système d’exploitation, journaux spécifiques aux applications et journaux système de détection des intrusions.

Sécurité OMS et Audit solution permet au service informatique d’analyser activement toutes les ressources, ce qui permettant de minimiser l’impact des incidents de sécurité. Sécurité OMS et Audit comporte des domaines de sécurité qui peuvent servir de surveillance des ressources. Les domaines de sécurité offre un accès rapide à des options, pour contrôler la sécurité plus de détails doit porter les domaines suivants :

- Évaluation des programmes malveillants
- Évaluation de mise à jour
- Identité et accès

> [AZURE.NOTE] pour une vue d’ensemble de toutes ces options, lisez [mise en route d’opérations gestion Suite de sécurité et Solution d’Audit](oms-security-getting-started.md).

### <a name="monitoring-system-protection"></a>Protection du système de surveillance

Dans une approche défense en profondeur, chaque niveau de protection est importante pour l’état de sécurité générale de votre actif. Ordinateurs avec menaces détectées et bénéficiant d’une protection insuffisante apparaissent dans la vignette de l’évaluation des programmes malveillants sous domaines de sécurité. En utilisant les informations sur l’évaluation des programmes malveillants, vous pouvez identifier un plan pour appliquer une protection aux serveurs qui en ont besoin. Pour accéder à cette option, procédez comme suit :

1. Dans le tableau de bord principale **Microsoft Operations Management Suite** cliquez sur mosaïque de **sécurité et Audit** .

    ![Sécurité et Audit](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)

2. Dans le tableau de bord de **sécurité et Audit** , cliquez sur **Évaluation contre les logiciels malveillants** sous **Domaines de sécurité**. Le tableau de bord **Évaluation contre les logiciels malveillants** s’affiche, comme illustré ci-dessous :

![Évaluation des programmes malveillants](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig2-ga.png)

Vous pouvez utiliser le tableau de bord **Évaluation des programmes malveillants** pour identifier les problèmes de sécurité suivants :

- **Menace Active**: ordinateurs qui ont été compromis et disposent menace active dans le système.
- **Menaces Remediated**: ordinateurs qui ont été déchiffrés, mais les menaces ont été résolues.
- **Signature obsolètes**: ordinateurs dotées de protection contre les logiciels malveillants activée, mais la signature est mis à jour.
- **Aucune protection en temps réel**: ordinateurs qui n’ont pas installé contre les logiciels malveillants.

### <a name="monitoring-updates"></a>Mises à jour de surveillance 

Appliquer les dernières mises à jour de sécurité est des raisons de sécurité et il doit être incorporé dans votre stratégie de gestion des mises à jour. Service Agent de surveillance Microsoft (HealthService.exe) lit les informations de mise à jour d’ordinateurs gérés, puis envoyer ces informations mises à jour au service OMS dans le cloud pour traitement. Le service Agent de surveillance Microsoft est configuré comme un service automatique et il doit être toujours en cours d’exécution dans l’ordinateur cible.

![mises à jour de surveillance](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig3.png)

Logique est appliquée aux mise à jour des données et le service cloud enregistre les données. Si les mises à jour manquantes sont trouvées, elles sont affichées dans le tableau de bord **mises à jour** . Vous pouvez utiliser le tableau de bord **mises à jour** pour utiliser les mises à jour manquantes et développer un plan pour les appliquer aux serveurs qui en ont besoin. Suivez les étapes ci-dessous pour accéder au tableau de bord **mises à jour** :

1. Dans le tableau de bord principale **Microsoft Operations Management Suite** cliquez sur mosaïque de **sécurité et Audit** .
2. Dans le tableau de bord de **sécurité et Audit** , cliquez sur **Évaluation de mise à jour** sous **Domaines de sécurité**. Le tableau de bord de mise à jour s’affiche, comme illustré ci-dessous :

![évaluation de mise à jour](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig4.png)

Dans ce tableau de bord, vous pouvez effectuer une évaluation de mise à jour afin de comprendre l’état actuel de vos ordinateurs et menaces plus critiques. À l’aide de la vignette **critique ou mises à jour de sécurité** , les administrateurs informatiques seront en mesure d’accéder à des informations détaillées sur les mises à jour qui ne figurent pas comme indiqué ci-dessous :

![résultat de la recherche](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig5.png)

Ce rapport inclut des informations importantes qui peuvent être utilisées pour identifier le type de menace cet ordinateur est aux, qui inclut les articles KB Microsoft associés à la mise à jour de sécurité et le Bulletin MS qui contient plus de détails sur le problème de sécurité.

### <a name="monitoring-identity-and-access"></a>Surveillance identité et accès

Avec les utilisateurs qui travaillent à partir de n’importe où, à l’aide d’appareils différents et accéder à une grande quantité d’applications cloud et en local, vous devez impérativement que leurs informations d’identification sont protégées. Les attaques de vol d’informations d’identification sont celles dans lesquelles un intrus initialement accède aux informations d’identification d’un utilisateur normal pour accéder à un système au sein du réseau. De nombreux cas, cette attaque initiale consiste uniquement à accéder au réseau, l’objectif consiste à découvrir les privilèges comptes. 

Les pirates restera dans le réseau, à l’aide de gratuitement des outils pour extraire des informations d’identification des sessions d’autres comptes connectés. Selon la configuration système, ces informations d’identification peuvent être extraits sous forme de hachage, des billets ou des paires de mots de passe.  

> [AZURE.NOTE] machines directement exposés à Internet verra s’afficher plusieurs tentatives qui réessayez de vous connecter à l’aide de toutes sortes de noms d’utilisateur connus (par exemple, l’administrateur). Dans la plupart des cas, il est OK si les noms d’utilisateurs connus ne sont pas utilisées et si le mot de passe est assez fort.

Il est possible d’identifier ces intrus avant qu’elles affecter un compte disposant de privilèges. Vous pouvez tirer parti **OMS sécurité et Solution d’Audit** pour contrôler l’accès et identité. Suivez les étapes ci-dessous pour accéder au tableau de bord **d’identité et accès** :

1. Cliquez sur sécurité et Audit dans le tableau de bord principale **Microsoft Operations Management Suite** vignette.
2. Dans le tableau de bord de **sécurité et Audit** , cliquez sur **identité et accès** sous **Domaines de sécurité**. Le tableau de bord **d’identité et accès** s’affiche, comme illustré ci-dessous :

![identité et accès](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig6-ga.png)

Dans le cadre de votre stratégie d’analyse régulière, vous devez inclure identité surveillance. Administrateur informatique doit ressembler s’il existe une valeur valide spécifique qui comporte le nombre de tentatives. Cela peut indiquer l’intrus ayant acquis le nom d’utilisateur réel et essayez en force ou d’un outil automatique utilise codé en dur mot de passe qui a expiré.

Cette permettent au tableau de bord personnel informatique d’identifier rapidement des menaces potentielles liées à l’identité et d’accès aux ressources de la société. Il est notamment important d’également identifier les tendances potentiels, par exemple dans la vignette de l’ouverture de session dans le temps, vous pouvez voir sur période le nombre de fois une tentative d’ouverture de session a été effectuée. Dans ce cas, l’ordinateur **serveur de fichiers** reçus 35 tentatives d’ouverture de session. Vous pouvez explorer plus d’informations sur cet ordinateur en cliquant dessus. 

![obtenir plus d’informations](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig7-new.png)

Le rapport généré pour cet ordinateur rend disponibles les fonctions précieux plus d’informations sur ce modèle. Remarqué que la colonne de **compte** vous donne le compte d’utilisateur qui a été utilisé pour tenter d’accéder au système, la colonne **TIMEGENERATED** vous donne l’intervalle de temps dans lequel la tentative a été effectuée et la colonne **LOGONTYPENAME** vous indique l’emplacement où cette tentative a été effectuée. Si ces tentatives ont été effectuées localement dans le système par un programme, la colonne **processeur** lorsque vous souhaitez afficher le nom du processus. Dans les scénarios où la tentative d’ouverture de session arrive à partir d’un programme, vous avez déjà le nom du processus disponible et maintenant que vous pouvez effectuer d’autres enquête dans le système cible.

## <a name="see-also"></a>Voir aussi

Dans ce document, vous avez appris à utiliser la sécurité OMS et d’Audit pour surveiller les ressources de votre solution. Pour en savoir plus sur la sécurité OMS, consultez les articles suivants :

- [Vue d’ensemble de la gestion des Suite (OMS) opérations](operations-management-suite-overview.md)
- [Prise en main opérations gestion Suite Solution de sécurité et d’Audit](oms-security-getting-started.md)
- [Surveiller et répondre aux alertes de sécurité dans opérations gestion Suite Solution de sécurité et d’Audit](oms-security-responding-alerts.md)