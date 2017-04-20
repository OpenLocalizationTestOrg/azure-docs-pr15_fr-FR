<properties
    pageTitle="Ouvrez une session Analytique FAQ | Microsoft Azure"
    description="Réponses aux questions fréquemment posées sur le service de journal Analytique."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="log-analytics-faq"></a>Ouvrez une session Analytique Forum aux questions

Cette FAQ Microsoft est une liste des questions fréquemment posées sur journal Analytique dans Microsoft opérations gestion Suite (OMS). Si vous avez des questions supplémentaires sur journal Analytique, rendez-vous sur le [forum de discussion](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) et envoyez vos questions. Une personne de notre communauté vous aideront à vos réponses. Si une question est couramment posée, nous allons l’ajouter à cet article afin qu’il vous pouvez trouver rapidement et facilement.

## <a name="general"></a>Général

**Q : quelles vérifications sont effectuées par Active Directory et solutions d’évaluation SQL ?**

A. La requête suivante affiche une description de toutes les vérifications effectuées actuellement :

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Les résultats peuvent ensuite être exportés vers Excel pour une révision supplémentaire.

* *Q : Pourquoi Voir autre chose que *OMS* dans SCOM Administration? **

R : en fonction de quelle mise à jour report de SCOM utilisé, vous risquez de voir un nœud *System Center Advisor*, *Perspectives opérationnelles*ou *Journal Analytique*.

La mise à jour de chaîne de texte à *OMS* est inclus dans un pack d’administration, qui doit être importé manuellement. Suivez les instructions de l’article SCOM mise à jour cumulative Ko et actualisez la console OMS pour voir les dernières mises à jour dans le nœud *OMS* .

* *Q : Y a-t-il une *local* version d’OMS? **

R : non. Journal Analytique traite et stocke de très grandes quantités de données. En tant qu’un service cloud, Analytique journal est en mesure d’éviter tout impact sur les performances de votre environnement et d’échelle accès si nécessaire.

En outre, est un moyen de service cloud vous n’êtes pas obligé de conserver l’infrastructure Analytique journal vers le haut et en cours d’exécution et peuvent recevoir des mises à jour de la fonctionnalité fréquents et correctifs.

## <a name="configuration"></a>Configuration
**Q : puis-je modifier le nom du conteneur table/blob permettant de lire à partir d’Azure Diagnostics (WAD) ?**  

A.  Non, ce n’est pas possible actuellement, mais est planifié pour une version ultérieure.

**Q : qu’IP adresses faire l’utilisation de services OMS ? Comment vous assurer que mon pare-feu autorise uniquement le trafic vers les Services OMS ?**  

A. Le service de journal Analytique repose sur Azure et les points de terminaison recevoir des adresses IP qui se trouvent dans les [Plages d’adresses IP centre de données Microsoft Azure](http://www.microsoft.com/download/details.aspx?id=41653).

Quand les déploiements de service sont effectuées, les adresses IP réels des services OMS changent. Les noms DNS pour permettre à travers le pare-feu sont présentées à [configurer les paramètres de proxy et de pare-feu dans journal Analytique](log-analytics-proxy-firewall.md).

**Q : j’ai utilisent ExpressRoute pour vous connecter à Azure. Mon trafic journal Analytique utiliseront ma connexion ExpressRoute ?**  

A. Les différents types de trafic ExpressRoute sont décrites dans la [documentation ExpressRoute](./expressroute/expressroute-faqs.md#supported-services).

Le trafic vers journal Analytique utilise le circuit ExpressRoute peering public.

**Q : y a-t-il un moyen simple et facile de déplacer un espace de travail Analytique journal existant à un autre abonnement d’espace de travail/Azure Analytique journal ?**  Nous avons des espaces de travail de plusieurs client OMS que nous avons Testez et instantanément dans notre abonnement Azure et ils sont déplacées en production nous voulons déplacez-les vers leur propre abonnement Azure/OMS.  

A. La `Move-AzureRmResource` applet de commande vous permet de déplacer un espace de travail Analytique journal, mais également un compte Automation d’un abonnement Azure à l’autre. Pour plus d’informations, voir [Déplacer AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx).

Cette modification peut également être effectuée dans le portail Azure.

Vous ne pouvez pas déplacer des données d’un espace de travail journal Analytique à l’autre, ou modifier le journal Analytique données sont stockées dans la région.

**Q : comment ajouter des OMS SCOM ?**

R : mise à jour vers la dernière mise à jour cumulative et importation de packs d’administration vous permettra pour vous connecter SCOM à journal Analytique.

Notez que la connexion SCOM au journal Analytique n’est disponible pour SCOM 2012 SP1 ou version ultérieure.

**Q : Comment puis-je vérifier qu’un agent est en mesure de communiquer avec Analytique journal ?**

R : pour vous assurer que l’agent de communiquer avec OMS, accédez à : le panneau de configuration, sécurité et paramètres, **Agent de surveillance Microsoft**.

Sous l’onglet **Azure journal Analytique (OMS)** , recherchez une coche verte. Une icône représentant une coche verte confirme que l’agent est en mesure de communiquer avec le service OMS.

Une icône d’avertissement jaune signifie que l’agent rencontre des problèmes communication avec OMS. L’une des raisons courantes sont le service Agent de surveillance Microsoft a été arrêté et doit être redémarré.

**Q : Comment puis-je arrêter un agent de communiquer avec les journaux Analytique ?**

R : dans SCOM, supprimez l’ordinateur à partir de la liste managée OMS. Toutes les communications via SCOM pour que l’agent s’arrête. Pour les agents connectés directement à OMS, vous pouvez empêcher les communiquer avec OMS via : le panneau de configuration, sécurité et paramètres, **Agent de surveillance Microsoft**.
Sous **Azure journal Analytique (OMS)**, supprimez tous les espaces de travail répertoriés.

## <a name="agent-data"></a>Données de l’agent

**Q : la quantité de données puis-je envoyer via l’agent au journal Analytique ? Existe-t-il une quantité maximale de données par client ?**  
A. L’offre gratuite définit une extrémité quotidienne de 500 Mo par espace de travail. Les offres standards et premium ne sont pas limitée sur la quantité de données sont téléchargées. Dans un service cloud, journal Analytique dans OMS conçue pour automatiquement une échelle jusqu'à poignée le volume provenant d’un client – même s’il s’agit to par jour.

L’agent de journal Analytique a été conçu pour vous assurer qu’il comporte un encombrement et effectue petite compression de base de données. Un de nos clients en fait écrit un blog sur les tests qu'ils effectuées par rapport à notre agent et voir le résultat comment ils ont été. Le volume de données varient selon les solutions permet à vos clients. Vous pouvez trouver des informations détaillées sur le volume de données et voir les pertes par solution sous **l’utilisation** de la vignette dans la page Vue d’ensemble OMS.

Pour plus d’informations, vous pouvez lire un [blog du client](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) sur l’encombrement de l’agent OMS faible.

**Q : la bande passante réseau est utilisé par l’Agent de gestion Microsoft (MMA) lors de l’envoi de données au journal Analytique ?**

A. La bande passante est une fonction de la quantité de données envoyées. Données sont compressées lorsqu’elles sont envoyées sur le réseau.

**Q : la quantité de données est envoyé par l’agent ?**

A. Cela dépend :

- les solutions que vous avez activé
- le nombre de journaux et compteurs de performances collectés
- le volume de données dans les journaux

Le niveau de tarification gratuit est un bon moyen de plusieurs intégré serveurs et jauge le volume de données typique. Globalement l’utilisation s’affiche dans la page de **l’utilisation** .
Pour les ordinateurs qui sont en mesure d’exécuter l’agent WireData, vous pouvez voir la quantité de données est envoyé à l’aide de la requête suivante :

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```



## <a name="next-steps"></a>Étapes suivantes

- [Prise en main Analytique journal](log-analytics-get-started.md) pour en savoir plus sur les journaux Analytique et get opérationnel en quelques minutes.
