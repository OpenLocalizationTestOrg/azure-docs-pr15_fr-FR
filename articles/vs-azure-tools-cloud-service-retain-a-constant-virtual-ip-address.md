<properties
   pageTitle="Comment faire pour conserver une adresse IP virtuelle constante d’un service cloud | Microsoft Azure"
   description="Découvrez comment s’assurer que l’adresse IP virtuelle (VIP) de votre service cloud Azure ne change pas."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="how-to-retain-a-constant-virtual-ip-address-for-a-cloud-service"></a>Comment faire pour conserver une adresse IP virtuelle constante d’un service cloud

Lorsque vous mettez à jour un service cloud qui est hébergé dans Azure, vous devrez peut-être vous assurer que l’adresse IP virtuelle (VIP) du service ne change pas. Les services de gestion de domaine utilisent le système DNS (Domain Name) pour l’inscription des noms de domaine. DNS est disponible uniquement si l’adresse IP virtuelle reste la même. Vous pouvez utiliser l' **Assistant Publication** dans Azure Tools pour vous assurer que l’adresse VIP de votre service cloud ne change pas lorsque vous mettez à jour. Pour plus d’informations sur l’utilisation de gestion des domaines DNS pour les services en nuage, voir [configuration d’un nom de domaine personnalisé pour un service cloud Azure](./cloud-services/cloud-services-custom-domain-name.md).

## <a name="publishing-a-cloud-service-without-changing-its-vip"></a>Publication d’un service cloud sans modifier ses VIP

L’adresse IP virtuelle d’un service cloud alloué lorsque vous tout d’abord déployez sur Azure dans un environnement particulier, telles que l’environnement de Production. L’adresse IP virtuelle ne modifie pas sauf si vous supprimez le déploiement explicitement ou sa suppression implicitement par le processus de mise à jour du déploiement. Pour conserver la VIP, vous ne devez pas supprimer votre déploiement, et vous devez également vous assurer que Visual Studio ne supprime pas votre déploiement automatiquement. Vous pouvez contrôler le comportement en spécifiant des paramètres de déploiement de l' **Assistant Publication**, qui prend en charge plusieurs options de déploiement. Vous pouvez spécifier un déploiement novateur ou un déploiement de mise à jour, qui peut être incrémentielles ou simultanées, et les deux types de mise à jour des déploiements conservent la VIP. Pour les définitions de ces différents types de déploiement, voir [Publier Assistant Création d’applications Azure](vs-azure-tools-publish-azure-application-wizard.md).  En outre, vous pouvez contrôler si le déploiement d’un service cloud précédent est supprimé si une erreur se produit. L’adresse VIP peut changer inopinément si vous ne définissez pas cette option correctement.

### <a name="to-update-a-cloud-service-without-changing-its-vip"></a>Pour mettre à jour un service cloud sans modifier ses VIP

1. Une fois que vous déployez au moins une fois votre service cloud, ouvrir le menu contextuel pour le nœud de votre projet Azure, puis sur **Publier**. L’Assistant **Publier Azure Application** s’affiche.

1. Dans la liste des abonnements, sélectionnez celle à laquelle vous voulez déployer, puis le bouton **suivant** . La page **paramètres** de l’Assistant s’affiche.

1. Sous l’onglet **Paramètres communs** , vérifiez que le nom du service cloud auquel vous déployez, l' **environnement**, la **Configuration de Build**et la **Configuration du Service** sont corrects.

1. Sous l’onglet **Paramètres avancés** , vérifiez que le compte de stockage et l’étiquette de déploiement sont corrects, que la case à cocher **Supprimer le déploiement en cas d’échec** est désactivée, et que la case à cocher de mise à jour de **déploiement** est sélectionnée. En activant la case à cocher de mise à jour de **déploiement** , vous assurez que votre déploiement n’est pas supprimé et votre VIP n’est pas perdue lorsque vous republiez votre application. En désactivant le **Supprimer le déploiement sur la case à cocher Échec**, vous assurez que votre VIP n’est pas perdue si une erreur se produit au cours du déploiement.

1. Pour spécifier comment vous souhaitez que les rôles à mettre à jour, sélectionnez le lien **paramètres** en regard de la zone **déploiement mise à jour** , puis l’option de mise à jour simultanée ou mise à jour incrémentiel dans la boîte de dialogue Paramètres de **déploiement mise à jour** . Si vous choisissez mise à jour incrémentiel, chaque instance est mis à jour une après l’autre, afin que l’application est toujours disponible. Si vous choisissez mise à jour simultanée, toutes les instances sont mises à jour en même temps. Mise à jour simultanée est plus rapide, mais votre service peut ne pas être disponible au cours du processus de mise à jour.

1. Lorsque vous êtes satisfait de vos paramètres, cliquez sur le bouton **suivant** .

1. Dans la page de **Résumé** de l’Assistant, vérifiez vos paramètres et cliquez sur le bouton **Publier** .

  >[AZURE.WARNING] Si le déploiement échoue, vous devez répondre la raison pour laquelle il a échoué et redéployez rapidement, pour éviter de laisser votre service cloud dans un état endommagé.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la publication sur Azure à partir de Visual Studio, voir [Assistant application Azure publier](vs-azure-tools-publish-azure-application-wizard.md).
