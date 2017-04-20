<properties
   pageTitle="Installer Endpoint Protection dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment mettre en œuvre la recommandation centre de sécurité Azure **Installer Endpoint Protection**."
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
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="install-endpoint-protection-in-azure-security-center"></a>Installer Endpoint Protection dans le centre de sécurité Azure

Centre de sécurité Azure recommande de mise en service un programme contre les logiciels malveillants à vos machines virtuelles Azure (machines virtuelles) si contre les logiciels malveillants ne sont pas déjà activée. Cette recommandation s’applique uniquement aux machines virtuelles Windows.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement.  Il s’agit pas d’un guide étape par étape.

## <a name="implement-the-recommendation"></a>Mettre en œuvre la recommandation

1. Dans la carte de **recommandations** , sélectionnez **Installer Endpoint Protection**.
![Sélectionnez Installer Endpoint Protection][1]

2. La carte **Installer Endpoint Protection** s’ouvre et affiche la liste des machines virtuelles sans contre les logiciels malveillants activé. Dans la liste, sélectionnez les ordinateurs virtuels que vous voulez installer contre les logiciels malveillants sur et cliquez sur **installer sur machines virtuelles**.
![Sélectionnez machines virtuelles installer contre les logiciels malveillants sur][2]

3. La carte **Sélectionnez Endpoint Protection** s’ouvre pour vous permettre de choisir la solution contre les logiciels malveillants que vous voulez utiliser. Dans cet exemple, nous allons sélectionnez **Microsoft contre les logiciels malveillants**.
![Sélectionner une protection par point de terminaison][3]

4. Informations supplémentaires sur la solution contre les logiciels malveillants sont affiche. Sélectionnez **créer**.
![Créer des solutions contre les logiciels malveillants][4]

5. Entrez les paramètres de configuration nécessaires sur la carte de **l’Ajout d’une Extension** , puis sélectionnez **OK**. Pour en savoir plus sur les paramètres de configuration, consultez [Configuration contre les logiciels malveillants personnalisée et par défaut](../security/azure-security-antimalware.md#default-and-custom-antimalware-configuration).

[Microsoft contre les logiciels malveillants](../azure-security-antimalware.md) est maintenant actif sur les ordinateurs virtuels sélectionnés.

## <a name="see-also"></a>Voir aussi

Cet article vous montrer comment mettre en œuvre la recommandation centre de sécurité « Installer Endpoint Protection ». Pour plus d’informations sur l’activation d’un programme contre les logiciels malveillants dans Azure, voir les rubriques suivantes :

- [Microsoft contre les logiciels malveillants pour les Services de Cloud et Machines virtuelles](../azure-security-antimalware.md) --Découvrez comment déployer Microsoft contre les logiciels malveillants.

Pour en savoir plus sur le centre de sécurité, voir les rubriques suivantes :

- [Définition de stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) , découvrez comment configurer des stratégies de sécurité.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources Azure.
- [Analyse du fonctionnement de la sécurité dans le centre de sécurité Azure](security-center-monitoring.md) , découvrez comment contrôler l’état de vos ressources Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) --apprendre à gérer et répondre aux alertes de sécurité.
- [Solutions des partenaires surveillance avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment contrôler l’état d’intégrité de votre solutions des partenaires.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher Forum aux questions sur l’utilisation du service.
- Billets de [blog sur la sécurité azure](http://blogs.msdn.com/b/azuresecurity/) --rechercher blog sur la conformité et la sécurité Azure.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/select-install-endpoint-protection.png
[2]:./media/security-center-install-endpoint-protection/install-endpoint-protection-blade.png
[3]:./media/security-center-install-endpoint-protection/select-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/create-antimalware-solution.png
