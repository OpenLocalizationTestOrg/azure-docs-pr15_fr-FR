<properties
   pageTitle="Activer le chiffrement Transparent des données dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment mettre en œuvre la recommandation centre de sécurité Azure **Activer le chiffrement de données Transparent**."
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

# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Activer le chiffrement Transparent des données dans le centre de sécurité Azure

Centre de sécurité Azure recommande activer le chiffrement Transparent des données (TDE) sur les bases de données SQL si TDE n’est pas déjà activée. TDE protège vos données et vous permet de répondre aux exigences de conformité en chiffrant votre base de données, les sauvegardes associés et les fichiers journaux des transactions inactives, sans avoir besoin de modifications apportées à votre application. Pour en savoir plus, voir [Chiffrement Transparent des données avec la base de données SQL Azure](https://msdn.microsoft.com/library/dn948096).

Cette recommandation s’applique au service SQL Azure uniquement ; n’inclut pas SQL en cours d’exécution sur vos ordinateurs virtuels.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement.  Il s’agit pas d’un guide étape par étape.

## <a name="implement-the-recommendation"></a>Mettre en œuvre la recommandation

1. Dans la carte de **recommandations** , sélectionnez **Activer le chiffrement de données Transparent**.
![Activer le chiffrement de données Transparent][1]

2. Cette action ouvre la carte **Activer le chiffrement Transparent des données sur les bases de données SQL** . Sélectionnez une base de données SQL pour activer TDE sur.
![Sélectionnez la base de données SQL pour activer TDE sur][2]
3. Dans la carte de **chiffrement de données Transparent** , sélectionnez **ON** sous chiffrement des données et sélectionnez **Enregistrer** dans le ruban de la cuillère supérieur.
![Activer TDE][3]

  Une fois que TDE est activé sur la base de données SQL sélectionnée, l' **état de chiffrement** remplacera par **chiffré**.    

  ![État de chiffrement][4]

## <a name="see-also"></a>Voir aussi

Cet article vous montrer comment mettre en œuvre la recommandation centre de sécurité « Activer le chiffrement Transparent des données ». Pour en savoir plus sur SQL TDE, voir les rubriques suivantes :

- [Chiffrement de données transparent avec la base de données SQL Azure](https://msdn.microsoft.com/library/dn948096)
- [Prise en main avec chiffrement Transparent des données (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

Pour en savoir plus sur le centre de sécurité, voir les rubriques suivantes :

- [Définition de stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) --Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources Azure.
- [Analyse du fonctionnement de la sécurité dans le centre de sécurité Azure](security-center-monitoring.md) , découvrez comment contrôler l’état de vos ressources Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) --apprendre à gérer et répondre aux alertes de sécurité.
- [Solutions des partenaires surveillance avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment contrôler l’état d’intégrité de votre solutions des partenaires.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher Forum aux questions sur l’utilisation du service.
- [Blog sur la sécurité azure](http://blogs.msdn.com/b/azuresecurity/) --obtenir les dernières informations de sécurité Azure et des informations.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
