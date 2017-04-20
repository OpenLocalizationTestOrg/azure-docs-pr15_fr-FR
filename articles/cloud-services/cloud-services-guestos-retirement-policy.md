<properties 
   pageTitle="Prise en charge et stratégie retraite guide pour Azure invité OS | Microsoft Azure" 
   description="Fournit des informations sur ce que Microsoft prend en charge en ce qui concerne pour le système d’exploitation Azure invité est utilisé par les Services Cloud." 
   services="cloud-services" 
   documentationCenter="na" 
   authors="raiye" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="10/24/2016"
   ms.author="raiye"/>

# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Stratégie de prise en charge et déclassement de système d’exploitation invité Azure
Les informations de cette page est lié au système d’exploitation de Azure invité ([Système d’exploitation invité](cloud-services-guestos-update-matrix.md)) pour les Services en nuage collaborateur et rôles web (PaaS). Il ne s’applique pas aux Machines virtuelles (IaaS). 

Microsoft a un publiés [prennent en charge la stratégie pour le système d’exploitation invité](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq). La page que vous lisez décrit à présent comment la stratégie est activée.

La stratégie est 

1. Microsoft prend en charge les **au moins les deux dernières gammes du système d’exploitation invité**. Quand une famille est supprimée, les clients ont 12 mois suivant la date de retraite officiel pour mettre à jour pour une famille de système d’exploitation invité pris en charge plus récente.
2. Microsoft prend en charge la **au moins deux dernières versions de la famille invité du système d’exploitation pris en charge**. 
3. Microsoft prend en charge au **minimum les deux versions plus récentes du Kit de développement Azure**. Quand une version du Kit de développement est supprimée, les clients auront 12 mois suivant la date de retraite officiel pour mettre à jour vers une version plus récente. 

Parfois plus de deux versions ou familles peuvent être prises en charge. Informations de support de système d’exploitation invité officielles seront affichent sur les [versions de système d’exploitation invité Azure et matrice de compatibilité SDK](cloud-services-guestos-update-matrix.md).


## <a name="when-a-guest-os-family-or-version-is-retired"></a>Lorsqu’une version ou famille de système d’exploitation invité est déclassée 


Un nouveau système d’exploitation invité **famille** est présenté un certain temps après la publication d’une nouvelle version officielle du système d’exploitation Windows Server. Chaque fois qu’une nouvelle famille de système d’exploitation invité est introduite, la Microsoft désactiver la plus ancienne famille de système d’exploitation invité. 

Nouveau système d’exploitation invité **versions** sont introduits sur chaque mois pour incorporer les dernières mises à jour MSRC. En raison des mises à jour mensuelles régulières, une version du système d’exploitation invité est désactivés normalement de 60 jours après sa publication. Cela permet de conserver au moins deux versions de système d’exploitation invité pour chaque famille disponible pour une utilisation. 

### <a name="process-during-a-guest-os-family-retirement"></a>Processus retraite famille un système d’exploitation invité 


Une fois que la retraite est annoncé, clients ont une période de 12 mois « transition » avant de la famille plus ancienne est supprimée embauche de service. Cette durée de transition peut être prolongée à la discrétion de Microsoft. Mises à jour sont validées dans les [versions de système d’exploitation invité Azure et matrice de compatibilité SDK](cloud-services-guestos-update-matrix.md).

Un processus retraite progressive commenceront à 6 mois dans la période de transition. Pendant ce temps :

1. Microsoft vous indiquera clients du déclassement. 
2. La nouvelle version du Kit de développement Azure ne prend en charge la famille de système d’exploitation invité déclassée.
3. Nouveaux déploiements et redéploiement des Services en nuage n’est pas autorisé sur la famille déclassée

Microsoft continuent à présenter la nouvelle version du système d’exploitation invité incorporant les dernières mises à jour MSRC jusqu’au dernier jour de la période de transition, appelé « la date d’expiration ». À ce stade, des Services de Cloud toujours en cours d’exécution est pris en charge sous le SLA Azure. Microsoft a la discrétion pour forcer la mise à niveau, supprimer ou arrêter ces services après cette date.



### <a name="process-during-a-guest-os-version-retirement"></a>Processus retraite une Version du système d’exploitation invité 
Si les clients défini leur système d’exploitation invité à mettre à jour automatiquement, ne sont jamais à vous soucier en matière de gestion des versions de système d’exploitation invité. Il utilise toujours la dernière version du système d’exploitation invité.

Versions de système d’exploitation invité sont publiées chaque mois. En raison du taux de régulièrement des versions, chacune possède une durée de vie fixe.

Une version 60 jours dans la durée de vie est «*désactivé*». « Désactivé » signifie que la version est supprimée à partir du portail classique Azure. Il également n’est plus possible de définir à partir du fichier de configuration CSCFG. Déploiements existants restent en cours d’exécution, mais les déploiements de nouvelles et mises à jour code et la configuration des déploiements existants ne seront pas autorisés. 

À une date ultérieure, le système d’exploitation invité version «*expire*» et toutes les installations toujours en cours d’exécution cette version sont force mis à niveau et un jeu à mettre à jour automatiquement le système d’exploitation invité à l’avenir. Expiration est terminée par lots pour la période de temps à partir de désactivation d’expiration peut varier. 

Ces périodes peuvent être effectués plus de temps à discrétion de Microsoft pour faciliter les transitions de client. Les modifications seront communiquées sur les [versions de système d’exploitation invité Azure et matrice de compatibilité SDK](cloud-services-guestos-update-matrix.md).



### <a name="notifications-during-retirement"></a>Notifications pendant votre retraite 

* **Retraite famille** <br>Microsoft utilise des billets de blog et Azure notification portail classique. Clients qui utilisent toujours une famille de système d’exploitation invité déclassée seront informés par une communication directe (courrier électronique, les messages portails, appel téléphonique) pour les administrateurs de service affecté. Toutes les modifications sont validées dans cette page et le flux RSS répertoriés au début de cette page. 


* **Version retraite** <br>Toutes les modifications sont validées dans cette page et le flux RSS répertoriés au début de cette page, y compris la version, désactivé et les dates d’expiration. Les administrateurs des services recevront les messages électroniques s’ils ont des déploiements s’exécutant sur une version du système d’exploitation invité désactivée ou votre famille. Le minutage de ces messages électroniques peut varier. En règle générale sont au moins un mois avant la désactivation, bien que ce minutage n’est pas accord officiel. 


## <a name="frequently-asked-questions"></a>Forum aux questions

**Comment puis-je limiter l’impact de la migration ?**

Vous devez utiliser la dernière famille de système d’exploitation invité pour la conception de vos Services Cloud. 

1. Commencez à planifier votre migration pour une famille de plus récente au plus tôt. 
2. Configurer les déploiements test temporaire pour tester votre Service Cloud s’exécutant sur la nouvelle famille. 
3. Définir votre version du système d’exploitation invité sur **automatique** (osVersion = * dans le fichier [.cscfg](cloud-services-model-and-package.md#cscfg) ) afin de la migration vers de nouvelles versions de système d’exploitation invité s’effectue automatiquement.

**Que se passe-t-il si mon application web nécessite intégration plus étroite avec le système d’exploitation ?**

Si votre architecture d’application web requiert dépendance approfondie sur le système d’exploitation sous-jacent, plate-forme utiliser pris en charge les fonctionnalités, telles que les [tâches de démarrage](cloud-services-startup-tasks.md) ou d’autres mécanismes extensibilité qui peuvent exister à l’avenir. Par ailleurs, vous pouvez également utiliser [Machines virtuelles Azure](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – Infrastructure en tant que Service), où que vous soyez responsable de la maintenance du système d’exploitation sous-jacent.
 
## <a name="next-steps"></a>Étapes suivantes
Passez en revue les dernières [mises à jour de système d’exploitation invité](cloud-services-guestos-update-matrix.md).
