<properties
   pageTitle="Appliquer des mises à jour du système dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment mettre en œuvre les recommandations centre de sécurité Azure **appliquer des mises à jour système** , puis **redémarrez après mise à jour du système**."
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
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="apply-system-updates-in-azure-security-center"></a>Appliquer des mises à jour du système dans le centre de sécurité Azure

Centre de sécurité Azure surveille quotidiennement les ordinateurs Windows et Linux virtuelle (machines virtuelles) mises à jour du système d’exploitation manquantes. Centre de sécurité récupère une liste de sécurité disponibles et les mises à jour critiques à partir de Windows Update ou Windows Server Update Services (WSUS), en fonction de laquelle le service est configuré sur un ordinateur Windows virtuel.  Centre de sécurité vérifie également les dernières mises à jour dans les systèmes Linux. Si votre ordinateur virtuel ne dispose pas d’une mise à jour système, centre de sécurité est recommandé que vous appliquez les mises à jour système

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement.  Il s’agit pas d’un guide étape par étape.

## <a name="implement-the-recommendation"></a>Mettre en œuvre la recommandation

1. Dans la carte de **recommandations** , sélectionnez **appliquer les mises à jour**.
![Appliquer des mises à jour système][1]

2. La carte de **mises à jour du système appliquer** s’ouvre, affichant une liste des ordinateurs virtuels système mises à jour manquantes. Sélectionnez une machine virtuelle.
![Sélectionnez une machine virtuelle][2]

3. Une carte s’ouvre et affiche la liste des mises à jour manquantes pour cet ordinateur virtuel. Sélectionnez une mise à jour du système. Dans cet exemple, nous allons sélectionnez KB3156016.
![Mises à jour de sécurité manquantes][3]

4. Suivez les étapes décrites dans la carte **À jour de sécurité** pour appliquer la mise à jour manquante.
![Mise à jour de sécurité][4]

## <a name="reboot-after-system-updates"></a>Redémarrez après mise à jour du système

5. Revenir à la carte de **recommandations** . Une nouvelle entrée a été générée une fois que vous avez appliqué des mises à jour du système, appelés **redémarrer après mise à jour du système**. Cette entrée vous avertit que vous devez redémarrer l’ordinateur virtuel pour terminer le processus de l’application des mises à jour système.
![Redémarrez après mise à jour du système][5]

6. Sélectionnez **redémarrer après mise à jour du système**. Cette action ouvre **Qu'un redémarrage est en attente pour terminer les mises à jour système** carte affichant une liste des ordinateurs virtuels dont vous avez besoin pour redémarrer pour terminer le processus de mises à jour système appliquer.
![Redémarrez en attente][6]

Redémarrez la machine virtuelle à partir d’Azure pour terminer le processus.

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur le centre de sécurité, voir les rubriques suivantes :

- [Définition de stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) --Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources Azure.
- [Analyse du fonctionnement de la sécurité dans le centre de sécurité Azure](security-center-monitoring.md) , découvrez comment contrôler l’état de vos ressources Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) --apprendre à gérer et répondre aux alertes de sécurité.
- [Solutions des partenaires surveillance avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment contrôler l’état d’intégrité de votre solutions des partenaires.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher Forum aux questions sur l’utilisation du service.
- Billets de [blog sur la sécurité azure](http://blogs.msdn.com/b/azuresecurity/) --rechercher blog sur la conformité et la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/recommendation.png
[2]:./media/security-center-apply-system-updates/select-vm.png
[3]: ./media/security-center-apply-system-updates/missing-security-updates.png
[4]: ./media/security-center-apply-system-updates/security-update.png
[5]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[6]: ./media/security-center-apply-system-updates/restart-pending.png
