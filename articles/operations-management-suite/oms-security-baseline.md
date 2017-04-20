<properties
   pageTitle="Opérations de sécurité Suite de gestion et d’Audit Solution référence | Microsoft Azure"
   description="Ce document explique comment utiliser la sécurité OMS et d’Audit solution pour effectuer une évaluation de référence de tous les ordinateurs analysés fins de conformité et sécurité."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/08/2016"
   ms.author="yurid"/>

# <a name="baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Évaluation de référence dans opérations gestion Suite Solution de sécurité et d’Audit

Ce document vous aide à utiliser les fonctionnalités d’évaluation référence [opérationnelle gestion de la sécurité Suite (OMS) et Solution d’Audit](operations-management-suite-overview.md) pour accéder à l’état de vos ressources analysées sécurisé.

## <a name="what-is-baseline-assessment"></a>Nouveautés d’évaluation de référence ?

Microsoft, ainsi que les organisations pour le gouvernement et secteur dans le monde, définit une configuration de Windows qui représente les déploiements serveur hautement sécurisé. Cette configuration est un ensemble de clés de Registre, les paramètres de stratégie d’audit et les paramètres de sécurité ainsi que des valeurs recommandées de Microsoft pour ces paramètres. Cet ensemble de règles est appelé référence de sécurité. Fonctionnalité d’évaluation référence OMS sécurité et d’Audit peut en toute transparence analyser tous vos ordinateurs pour la conformité. 

Il existe trois types de règles :

- **Règles de Registre**: vérifier que les clés de Registre sont définis correctement.
- **Règles de stratégie d’audit**: règles concernant votre stratégie d’audit.
- **Règles de stratégie de sécurité**: règles concernant les autorisations de l’utilisateur sur l’ordinateur.

> [AZURE.NOTE] Lisez [Utiliser la sécurité OMS afin d’évaluer la sécurité de Configuration de base](https://blogs.technet.microsoft.com/msoms/2016/08/12/use-oms-security-to-assess-the-security-configuration-baseline/) pour un bref aperçu de cette fonctionnalité.

## <a name="security-baseline-assessment"></a>Évaluation de référence de sécurité

Vous pouvez consulter votre évaluation de référence de sécurité en cours pour tous les ordinateurs qui sont analysés par OMS sécurité et d’Audit à l’aide du tableau de bord.  Exécutez les étapes suivantes pour accéder au tableau de bord de sécurité référence évaluation :

1. Dans le tableau de bord de principale de **Microsoft Operations Management Suite** , cliquez sur **sécurité et Audit** vignette.
2. Dans le tableau de bord de **sécurité et Audit** , cliquez sur **Évaluation de référence** sous **Domaines de sécurité**. Le tableau de bord **d’Évaluation de référence de sécurité** s’affiche comme illustré dans l’image suivante :
    
    ![Sécurité OMS et l’évaluation de référence d’Audit](./media/oms-security-baseline/oms-security-baseline-fig1.png)

Ce tableau de bord est divisée en trois zones principales :

- **Ordinateurs par rapport au planning de référence**: cette section fournit un résumé du nombre d’ordinateurs qui ont été accessibles et le pourcentage d’ordinateurs qui réussi l’évaluation. Il fournit également les 10 ordinateurs supérieure et le résultat de pourcentage pour l’évaluation.
- **État de règles obligatoire**: cette section contient l’objectif pour faire apparaître sensibilisation des règles échecs par gravité et échecs des règles par type. En recherchant le premier graphique que vous pouvez identifier rapidement si la plupart des règles échecs critiques, ou non. Elle donne également une liste des 10 règles échecs supérieure et de leur gravité. Le deuxième graphique indique le type de règle a échoué lors de l’évaluation. 
- **Ordinateurs n’apparaissant pas évaluation de référence**: cette section répertorient les ordinateurs qui n’ont pas accessibles en raison d’incompatibilité de système d’exploitation ou défaillances. 

### <a name="accessing-computers-compared-to-baseline"></a>L’accès à des ordinateurs par rapport au planning de référence

Dans l’idéal tous vos ordinateurs sont soit conforme à l’évaluation de référence de sécurité. Toutefois il est prévu que dans certaines circonstances cela ne se produit. Dans le cadre du processus de gestion de sécurité, il est important inclure Examinez les ordinateurs qui n’a pas pu passer tous les tests d’évaluation de sécurité. Une méthode rapide pour visualiser qui se trouve en sélectionnant l’option **ordinateurs accessible** situé dans la section **ordinateurs par rapport au planning de référence** . Vous devriez voir le résultat de recherche de journal indiquant la liste des ordinateurs comme le montre dans l’écran suivant :

![Résultats de l’ordinateur accédé](./media/oms-security-baseline/oms-security-baseline-fig2.png)

Le résultat de recherche apparaît dans un format de tableau, où la première colonne comporte le nom d’ordinateur et la deuxième couleur comporte le nombre de règles qui a échoué. Pour récupérer les informations concernant le type de règle en échec, cliquez sur le nombre de règles en échec outre le nom de l’ordinateur. Vous devez voir un résultat similaire à celui illustré dans l’image suivante :

![Détails des résultats ordinateur accédé](./media/oms-security-baseline/oms-security-baseline-fig3.png)

Dans ce résultat de recherche, vous devez le total des règles d’accès, le nombre de règles critiques qui a échoué, les règles d’avertissement et les règles d’informations sur a échoué.

### <a name="accessing-required-rules-status"></a>L’accès au statut de règles requises

Après avoir recueilli les informations concernant le nombre de pourcentage d’ordinateurs réussi l’évaluation, vous souhaiterez peut-être obtenir plus d’informations sur les règles sont ignore en fonction de l’importance. Cette visualisation vous aide à classer par priorité les ordinateurs qui doivent être traités tout d’abord pour vous assurer qu’ils sont compatibles dans la prochaine évaluation. Pointez sur la partie essentielle du graphique situé dans la vignette **a échoué règles par gravité** , sous **état règles requise** et cliquez dessus. Vous devez voir un résultat semblable à l’écran suivant :

![Échec de règles les détails de la gravité](./media/oms-security-baseline/oms-security-baseline-fig4.png) 

Dans ce résultat journal le type de règle de référence qui a échoué, la description de cette règle et l’ID courantes Configuration énumération (CCE) de cette règle de sécurité s’affiche. Ces attributs doivent être suffisant pour effectuer une action verres pour résoudre ce problème dans l’ordinateur cible.

> [AZURE.NOTE] Pour plus d’informations sur CCE, accéder à la [Base de données vulnérabilité nationale](https://nvd.nist.gov/cce/index.cfm).

### <a name="accessing-computers-missing-baseline-assessment"></a>L’accès à des ordinateurs n’apparaissant pas évaluation de référence

OMS prend en charge le profil de référence de membre de domaine sur Windows Server 2008 R2 jusqu'à Windows Server 2012 R2. Windows Server 2016 référence n’est pas encore final et est ajouté dès qu’il est publié. Tous les autres systèmes d’exploitation numérisées via OMS sécurité et Audit évaluation référence apparaît sous la section **ordinateurs n’apparaissant pas évaluation de référence** .

## <a name="see-also"></a>Voir aussi

Dans ce document, vous avez appris OMS sécurité et Audit évaluation de référence. Pour en savoir plus sur la sécurité OMS, consultez les articles suivants :

- [Vue d’ensemble de la gestion des Suite (OMS) opérations](operations-management-suite-overview.md)
- [Surveiller et répondre aux alertes de sécurité dans opérations gestion Suite Solution de sécurité et d’Audit](oms-security-responding-alerts.md)
- [Suivi des ressources dans opérations gestion Suite Solution de sécurité et d’Audit](oms-security-monitoring-resources.md)

