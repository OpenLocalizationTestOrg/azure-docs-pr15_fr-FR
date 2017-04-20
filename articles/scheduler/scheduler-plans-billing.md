<properties
 pageTitle="Les offres et facturation dans le planificateur Azure"
 description="Les offres et facturation dans le planificateur Azure"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="plans-and-billing-in-azure-scheduler"></a>Les offres et facturation dans le planificateur Azure

## <a name="job-collection-plans"></a>Plan de collecte de travail

Les collections de travail sont l’entité facturable dans le planificateur Azure. Collections de travail contiennent un nombre de tâches et se présentent sous trois offres – libre, Standard et Premium – qui sont décrites ci-dessous.

|**Plan de collecte des tâches**|**Nombre maximum de tâches par collection de sites de projet**|**Max périodicité**|**Fonction MAX Collections par abonnement**|**Limites**|
|:---|:---|:---|:---|:---|
|**Gratuit**|5 tâches par collection de sites de projet|Une fois par heure. Impossible d’exécuter travaux plus fréquemment qu’une fois par heure|Un abonnement est autorisé jusqu'à 1 collection travail gratuit|Impossible d’utiliser [objet autorisation sortant HTTP](scheduler-outbound-authentication.md)
|**Standard**|50 tâches par collection de sites de projet|Une fois par minute. Impossible d’exécuter travaux plus fréquemment qu’une fois par minute|Un abonnement est autorisé jusqu'à 100 collections de travail standard|Accès à l’ensemble complet des fonctionnalités du planificateur|
|**P10 Premium**|50 tâches par collection de sites de projet|Une fois par minute. Impossible d’exécuter travaux plus fréquemment qu’une fois par minute|Un abonnement est autorisé jusqu'à 10 000 collections de travail P10 Premium. Pour plus d’informations, <a href="mailto:wapteams@microsoft.com">nous contacter</a> .|Accès à l’ensemble complet des fonctionnalités du planificateur|
|**P20 Premium**|tâches de 1000 par collection de sites de projet|Une fois par minute. Impossible d’exécuter travaux plus fréquemment qu’une fois par minute|Un abonnement est autorisé jusqu'à 10 000 collections de travail P20 Premium. Pour plus d’informations, <a href="mailto:wapteams@microsoft.com">nous contacter</a> .|Accès à l’ensemble complet des fonctionnalités du planificateur|

## <a name="upgrades-and-downgrades-of-job-collection-plans"></a>Mises à niveau et la page du plan de collecte de travail

Vous pouvez mettre à niveau ou mettre à niveau un plan de la collection de sites de travail à tout moment parmi les plans libre, Standard et Premium. Toutefois, lors de la mise à niveau vers une collection de travail gratuit, le déclassement peut échouer pour l’une des raisons suivantes :

- Une tâche gratuit collection existe déjà dans l’abonnement
- Une tâche dans la collection de travail avec une périodicité plus élevée que la limite autorisée pour les projets de collections de travail gratuit. La périodicité maximale autorisée dans une collection de travail gratuit est une fois par heure
- Il existe plus de 5 tâches dans la collection de travail
- Une tâche dans la collection de travail avec une action HTTP ou HTTPS qui utilise un [objet d’autorisation sortant HTTP](scheduler-outbound-authentication.md)

## <a name="billing-and-azure-plans"></a>Plans de facturation et Azure

Abonnements ne seront pas imputés gratuitement collections de travail. Si vous avez plus de 100 collections de travail standard (10 unités facturation standards), il est une offre une meilleure pour que toutes les collections de travail dans le plan premium.

Si vous avez une collection de sites de travail standard et une collection de sites de projet premium, vous sont facturée un standard facturation unité _et_ une prime facturation unité. Les factures de service Planificateur en fonction du nombre de collections de travail actif qui sont définis en standard ou premium ; Cela est expliqué plus loin dans les deux sections suivantes.

## <a name="standard-billable-units"></a>Unités facturables standard

Une unité facturable standard peut inclure jusqu'à 10 collections de travail standard. Dans la mesure où une collection de travail standard peut avoir jusqu'à 50 tâches par collection de sites de travail, une unité de facturation standard permet un abonnement d’avoir jusqu'à 500 tâches – jusqu'à 22 millions d’exécution de travaux par mois.

Si vous avez entre 1 et 10 collections de travail standard, vous seront facturés correspondant à 1 unité de facturation standard. Si vous avez entre 11 et 20 collections de travail standard, vous seront facturés pour 2 unités de facturation standards. Si vous avez entre 21 et les collections de 30 tâche standard, vous seront facturés pour 3 unités facturation standards et ainsi de suite.

## <a name="p10-premium-billable-units"></a>P10 Unités facturables Premium

Une unité de facturables P10 premium peut inclure jusqu'à 10 000 collections de travail P10 premium. Dans la mesure où une collection de travail P10 premium peut avoir jusqu'à 50 tâches par collection de sites de projet, une unité de facturation premium permet un abonnement d’avoir jusqu'à 500 000 tâches – jusqu'à presque 22 milliards d’exécution de travaux par mois.

Si vous avez entre 1 et 10 000 collections de travail premium, vous seront facturés correspondant à 1 unité facturation P10 premium. Si vous avez entre 10,001 et les collections de travail de 20 000 premium, vous seront facturés pour 2 unités facturation P10 premium et ainsi de suite.

Par conséquent, P10 premium travail collections ont les mêmes fonctionnalités que les collections de travail standard, mais fournissent un saut de prix au cas où votre application nécessite un grand nombre de collections de travail.

## <a name="p20-premium-billable-units"></a>P20 Unités facturables Premium

Une unité de facturables P20 premium peut inclure jusqu'à 5 000 collections de travail P20 premium. Dans la mesure où une collection de travail P20 premium peut avoir jusqu'à 1 000 projets par collection de sites de projet, une unité de facturation premium permet un abonnement d’avoir jusqu'à 5 000 000 tâches – jusqu'à presque 220 milliards d’exécution de travaux par mois.

P20 premium travail collections fournit les mêmes fonctionnalités sous forme de collections de travail P10 premium, mais également prend en charge un travaux nombre supérieur par collection de sites de projet et un plus grand nombre total de tâches générale que premium P10 autorisant que vous ayez plus extensibilité élevées.

## <a name="billing-and-active-status"></a>Facturation et Active l’état

Travail collections sont toujours actives, à moins que votre abonnement entière pouvez-vous dans un état désactivé temporaire en raison de problèmes de facturation. La seule façon de vous assurer qu’une collection de travail n’est pas facturée est soit lui affecter l’offre _gratuite_ ou supprimer la collection de sites de travail.

Bien que vous pouvez désactiver toutes les tâches dans une collection de travail en une seule opération, il ne modifie pas l’état de la collection de travail facturation – la collection de travail est _toujours_ seront facturés. De même, les collections de projet vides sont considérées comme actives et facturation.

## <a name="pricing"></a>Tarifs

Pour connaître les prix, voir [Planificateur tarifs](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="see-also"></a>Voir aussi


 [Quel est le planificateur ?](scheduler-intro.md)

 [Azure Scheduler concepts, terminologie et hiérarchie d’entités](scheduler-concepts-terms.md)

 [Commencer à utiliser le planificateur dans le portail Azure](scheduler-get-started-portal.md)

 [Référence de l’API REST Scheduler Azure](https://msdn.microsoft.com/library/mt629143)

 [Référence à des applets de commande PowerShell Scheduler Azure](scheduler-powershell-reference.md)

 [Azure Scheduler disponibilité et fiabilité](scheduler-high-availability-reliability.md)

 [Limites de planificateur Azure, les valeurs par défaut et codes d’erreur](scheduler-limits-defaults-errors.md)

 [Authentification sortante Scheduler Azure](scheduler-outbound-authentication.md)
