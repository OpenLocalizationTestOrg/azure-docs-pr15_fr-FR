<properties
   pageTitle="Système d’exploitation intéressé dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment mettre en œuvre la recommandation centre de sécurité Azure **vulnérabilités mettre à jour du système d’exploitation**."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/17/2016"
   ms.author="terrylan"/>

# <a name="remediate-os-vulnerabilities-in-azure-security-center"></a>Système d’exploitation intéressé dans le centre de sécurité Azure

Centre de sécurité Azure analyse quotidiennement votre système d’exploitation de machine virtuelle () (OS) pour les configurations qui pourraient exposer la machine virtuelle plus à s’attaquer aux et recommande des modifications de configuration pour résoudre ces problèmes. Pour plus d’informations sur les configurations spécifiques en cours d’analyse, consultez la [liste des règles de configuration recommandée](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Centre de sécurité recommande résoudre vulnérabilités lors de la configuration de système d’exploitation de votre ordinateur virtuel ne correspond pas les règles de configuration recommandée.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement.  Il s’agit pas d’un guide étape par étape.

## <a name="implement-the-recommendation"></a>Mettre en œuvre la recommandation

1. Dans la carte de **recommandations** , sélectionnez **mettre à jour du système d’exploitation vulnérabilités**.
![Intéressé du système d’exploitation][1]

    La carte de **mettre à jour du système d’exploitation vulnérabilités** s’ouvre et répertorie vos ordinateurs virtuels avec des configurations système d’exploitation qui ne correspondent pas les règles de configuration recommandée.  Pour chaque machine virtuelle, la carte identifie :

   - **Échec de règles** --le nombre de règles de la configuration de la machine virtuelle du système d’exploitation a échoué.
   - **Durée de l’analyse dernière** --la date et l’heure que le centre de sécurité analysés dernière configuration du système d’exploitation de l’ordinateur.
   - **État** : l’état actuel du faille de :

        - Ouvrir : Cette vulnérabilité n'a pas été traitée encore
        - En cours : La vulnérabilité actuellement appliquée, aucune action n’est requise par vous
        - Résolu : La vulnérabilité a été déjà résolue (lorsque le problème a été résolu, l’entrée est grisée)
  - **Gravité** --toutes les vulnérabilités sont définies pour une gravité faible, c'est-à-dire un problème doit être traité, mais ne nécessite pas une attention immédiate.

Sélectionnez une machine virtuelle. Une carte pour cet ordinateur virtuel s’ouvre et affiche les règles qui n’ont pas.
   ![Règles de configuration qui n’ont pas pu][2]

Sélectionnez une règle. Dans cet exemple, permet de sélectionner le **mot de passe doit répondre aux exigences de complexité**. Une carte s’ouvre décrivant la règle en échec et l’impact. Examinez les détails et voyez comment configurations système d’exploitation seront appliquées.
  ![Description de la règle a échoué][3]

  Centre de sécurité utilise courantes Configuration énumération (CCE) pour affecter un identificateur unique pour les règles de configuration. Les informations suivantes sont fournies sur cette carte :

  - NOM : Nom de la règle
  - GRAVITÉ--Valeur de gravité CCE de critiques, importantes ou d’avertissement
  - CCIED--Identificateur unique CCE pour la règle
  - DESCRIPTION : Description de règle
  - VULNÉRABILITÉ--Explication vulnérabilité ou risque si la règle n’est pas appliquée
  - IMPACT : Impact sur l’activité lors de la règle est appliquée
  - VALEUR attendue--Valeur attendue lorsque le centre de sécurité analyse la configuration de votre système d’exploitation machine virtuelle par rapport à la règle
  - --RÈGLE règle opération utilisée par le centre de sécurité lors de l’analyse de la configuration de votre système d’exploitation machine virtuelle par rapport à la règle
  - VALEUR réelle--Valeur retournée après l’analyse de la configuration de votre système d’exploitation machine virtuelle par rapport à la règle
  - RÉSULTAT d’évaluation –-résultat de l’analyse : terminée, Fail


## <a name="see-also"></a>Voir aussi

Cet article vous montrer comment mettre en œuvre la recommandation centre de sécurité « Vulnérabilités correction du système d’exploitation ». Vous pouvez consulter l’ensemble de règles de configuration [ici](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Centre de sécurité utilise CCE (courantes Configuration énumération) pour affecter un identificateur unique pour les règles de configuration. Visitez le site [CCE](http://cce.mitre.org) pour plus d’informations.

Pour en savoir plus sur le centre de sécurité, voir les rubriques suivantes :

- [Définition de stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) --Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources Azure.
- [Analyse du fonctionnement de la sécurité dans le centre de sécurité Azure](security-center-monitoring.md) , découvrez comment contrôler l’état de vos ressources Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) --apprendre à gérer et répondre aux alertes de sécurité.
- [Solutions des partenaires surveillance avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment contrôler l’état d’intégrité de votre solutions des partenaires.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher Forum aux questions sur l’utilisation du service.
- Billets de [blog sur la sécurité azure](http://blogs.msdn.com/b/azuresecurity/) --rechercher blog sur la conformité et la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/recommendation.png
[2]:./media/security-center-remediate-os-vulnerabilities/vm-remediate-os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
