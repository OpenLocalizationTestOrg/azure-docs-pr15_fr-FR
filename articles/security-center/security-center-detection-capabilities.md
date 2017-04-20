<properties
   pageTitle="Fonctionnalités de détection dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous aide à comprendre le fonctionnement des fonctionnalités de détection Azure le centre de sécurité."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-detection-capabilities"></a>Fonctionnalités de détection Azure le centre de sécurité
Ce document traite des fonctionnalités de détection avancée du centre de sécurité Azure qui vous aidera à identifier menace active un ciblage de vos ressources Microsoft Azure et vous fournit les informations nécessaires pour répondre rapidement.

> [AZURE.NOTE] Détection avancées est disponibles dans le Standard couche d’Azure centre de sécurité. Une version d’évaluation gratuite de 90 jours est disponible. Vous pouvez mettre à niveau à partir de la sélection de niveau de prix dans la [Stratégie de sécurité](security-center-policies.md). Visitez la [page Centre de sécurité](https://azure.microsoft.com/pricing/details/security-center/) pour en savoir plus sur les tarifs. 


## <a name="responding-to-todays-threats"></a>Répondre aux menaces
Des modifications ont été significatives dans le paysage des menaces 20 dernières années. Par le passé, sociétés généralement uniquement avaient à vous soucier de dégradation de site web par les pirates individuels qui ont été principalement intéressé voir « ce qu’il peuvent faire ». Les pirates d’aujourd'hui sont beaucoup plus sophistiquées et organisés. Ils ont souvent des objectifs financiers et stratégiques spécifiques. Ils ont également davantage de ressources disponibles, comme elles peuvent financés par les États de votre entreprise ou délits organisé.

Cette approche a donné lieu à un niveau de professionnalisme dans les rangs intrus sans précédent. N’est plus qu’ils souhaitent dégradation de pages web. Ils sont désormais intéresse vol d’informations, comptes financiers et données privées – ce qui ils peuvent utiliser pour générer des disponibilités sur le marché ouvert ou d’exploiter un métier particulier, position politique ou mise à jour. Encore plus relatif à ces intrus avec un objectif financier sont les pirates qui enfreint les réseaux à nuire à infrastructure et des personnes.

En réponse, les organisations déployer souvent diverses solutions de point de se concentrer sur défendre les points de terminaison ou périmètre de l’entreprise en recherchant des signatures d’attaques connues. Ces solutions ont tendance à générer un volume élevé d’alertes de fidélité faible, qui requièrent un analyste de sécurité trier et analyser. La plupart des organisations ne disposent pas le temps et les compétences requises pour répondre à ces alertes – autant atteindre unaddressed.  Pendant ce temps, les pirates ont évolué leurs méthodes pour renverser nombreux défense basée sur les signatures et [s’adapter aux environnements de cloud](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Nouvelles approches sont requises pour l’identification des nouvelles menaces plus rapidement et accélérer la détection et réponse. 

## <a name="how-azure-security-center-detects-and-responds-to-threats"></a>Comment le centre de sécurité Azure détecte et répond aux menaces

Microsoft chercheurs en sécurité sont constamment à l’affût des menaces. Ils ont accès à un vaste ensemble de télémétrie tirée de présence globale de Microsoft dans le nuage et en local. Cette collection large et centralisée des jeux de données permet à Microsoft découvrir des nouveaux modèles et tendances dans l’ensemble de ses produits grand public et d’entreprise en local, ainsi que ses services en ligne. Par conséquent, centre de sécurité peuvent rapidement mettre à jour ses algorithmes de détection des intrus relâchez attaques toujours plus sophistiquées et nouveau. Cette approche vous permet de rester en phase avec un environnement menace. 

Détection des menaces centre de sécurité fonctionne en regroupant automatiquement les informations de sécurité de vos ressources Azure, le réseau et les solutions des partenaires connecté. Il analyse ces informations, corrélation souvent des informations de plusieurs sources d’identifier les menaces. Alertes de sécurité sont classés par priorité dans le centre de sécurité ainsi que des recommandations sur la façon de résoudre la menace.

![Collecte de données de centre de sécurité et de présentation](./media/security-center-detection-capabilities/security-center-detection-capabilities-fig1.png)

Centre de sécurité emploie analytique de sécurité avancés, qui sont dépassent approches basée sur les signatures. Innovations dans big data et [d’apprentissage machine](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technologies exploiter pour évaluer les événements au sein de la structure de toute cloud – détection des menaces qu’il est impossibles d’identifier à l’aide des approches manuels et de prédiction de l’évolution des attaques. Ces analytique de sécurité est les suivantes : 

- **Analyse décisionnelle intégrée menace**: recherche connus bad intervenants en tirant parti des menaces globale à partir de produits Microsoft et services Microsoft numérique délits unité (DCU), la sécurité réponse centre MSRC (Microsoft) et flux externes.
- **Comportement analytique**: applique un motif connus pour découvrir les comportements malveillants. 
- **Détection des anomalies**: utilise un profil statistique pour générer un planning de référence historique. Il avertit sous déviations par rapport à établie plannings de référence qui sont conformes à une attaque potentielle.


### <a name="threat-intelligence"></a>Informations sur les menaces
Microsoft dispose d’un montant considérable d’informations sur les menaces global. Les flux de télémétrie de plusieurs sources, telles que Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft numérique délits unité (DCU) et sécurité réponse centre MSRC (Microsoft). Chercheurs recevoir également des informations intelligence menace sont partagées par les fournisseurs de services de cloud principales et permet de s’abonner à des flux d’intelligence menace provenant de tiers. Centre de sécurité Azure peut utiliser ces informations pour vous alerter de menaces intervenants bad connus. Voici quelques exemples :

- **Les communications sortantes vers une adresse IP malveillante**: le trafic sortant à un réseau de robots connu ou darknet probablement indique que la ressource a été déchiffrée et un intrus, en essayant d’exécuter des commandes sur ces données système ou exfiltrate. Centre de sécurité Azure compare le trafic réseau vers la base de données de menace global de Microsoft et vous avertit quand il détecte des communications vers une adresse IP malveillante.

## <a name="behavioral-analytics"></a>Comportement analytique

Comportement analytique est une technique qui analyse et compare les données à une collection de modèles connus. Toutefois, ces modèles ne sont pas signatures simples. Ils sont déterminées par le biais complexes d’apprentissage automatique algorithmes qui sont appliqués aux groupes de données importants. Ils sont également déterminés par une analyse prudent des comportements malveillants par analystes experts. Centre de sécurité Azure peut utiliser analytique comportement pour identifier les ressources compromis basées sur l’analyse des journaux machine virtuelle, des journaux de périphérique réseau virtuel, journaux tissu, blocage images et d’autres sources. 

Par ailleurs, il est corrélation avec les autres signaux pour vérifier les preuves d’une campagne étendue. Cette corrélation permet d’identifier les événements qui sont cohérentes avec indicateurs établis de compromis. Voici quelques exemples :

- **L’exécution de processus suspects**: pirates plusieurs techniques pour exécuter des logiciels malveillants sans détection. Par exemple, un intrus peut donner des programmes malveillants les mêmes noms en tant que fichiers système légitimes mais placez ces fichiers dans un autre emplacement, utilisez un nom qui ressemble beaucoup à un fichier sans gravité ou masquer l’extension du fichier true. Centre de sécurité modèles comportements de processus et moniteurs traitement les exécutions pour détecter les valeurs aberrantes tels que.  
- **Programmes malveillants masqué et exploitation tente**: des programmes malveillants sont en mesure de l’action contre les logiciels malveillants traditionnels des produits par jamais écrire sur le disque ou le chiffrement composants logiciels stockés sur un disque.  Toutefois, les logiciels malveillants peuvent être détectées à l’aide d’analyse de la mémoire, comme le logiciel malveillant devez laisser traces en mémoire pour fonctionner. Lorsque le logiciel se bloque, un vidage capture une partie de la mémoire au moment de l’incident.  En analysant la mémoire dans le vidage, centre de sécurité Azure détecte techniques utilisées pour exploiter les vulnérabilités des logiciels et accéder aux données confidentielles subrepticement persistent avec-dans un ordinateur compromis sans affecter les performances de votre ordinateur.
- **Latérale mouvement et reconnaissance interne**: pour conserver dans un compromis réseau et recherchez/extractions précieux des données, les pirates souvent tentent de déplacement latéralement à partir de l’ordinateur compromis à d’autres personnes au sein du même réseau. Centre de sécurité surveille les activités de processus et de la connexion afin de découvrir les tentatives pour développer brèche d’un utilisateur malveillant dans le réseau, telles que la commande distante exécution réseau détection et énumération du compte.
- **Des Scripts PowerShell malveillants**: PowerShell est utilisé par les pirates pour exécuter du code malveillant sur les machines virtuelles cible pour diverses raisons. Centre de sécurité examine activité PowerShell pour preuve d’activité suspecte. 
- **Sortant attaques**: les pirates ciblent souvent des ressources de cloud avec l’objectif de l’utilisation de ces ressources pour lancer d’autres attaques. Machines virtuelles compromis, par exemple, habitués lancer les attaques en force par rapport aux autres machines virtuelles, envoyer du courrier indésirable ou analyser les ports ouverts et les autres périphériques sur internet. En appliquant apprentissage automatique pour le trafic réseau, centre de sécurité détecte lorsque les communications sortantes réseau dépassent la norme. Dans le cas du courrier indésirable, le centre de sécurité corrélation inhabituels e-mails avec intelligence d’Office 365 pour déterminer si le message est probablement malveillant ou le résultat d’une campagne électroniques légitimes.  

### <a name="anomaly-detection"></a>Détection des anomalies

Centre de sécurité Azure utilise également détection des anomalies pour identifier les menaces. Contrairement à comportement analytique (qui dépend des modèles connus dérivées de grands ensembles de données), détection des anomalies est plus « personnalisée » et se concentre sur les plannings de référence qui sont spécifiques à votre déploiement. Apprentissage automatique est appliqué pour déterminer l’activité normale pour vos déploiements et ensuite les règles sont générées pour définir des conditions hors norme qui peuvent s’agir d’un événement de sécurité. Voici un exemple :

- **Entrant un brute RDP/SSH forcer attaques**: vos déploiements peut-être occupés machines virtuelles avec un grand nombre de connexions chaque machines virtuelles jour et d’autres qui ont très peu ou les connexions. Centre de sécurité Azure peut déterminer l’activité de connexion référence pour ces machines virtuelles et utiliser apprentissage définir ce qui est en dehors de l’activité des connexions normal de l’ordinateur. Si le nombre de connexions ou l’heure du jour de noms d’accès ou l’emplacement à partir duquel les connexions sont demandées ou autres caractéristiques relatifs à la connexion est très différente de la ligne de base, une alerte peut être générée. Là encore, apprentissage automatique détermine ce qui est important.

## <a name="continuous-threat-intelligence-monitoring"></a>Surveillance d’intelligence menaces en continu

Centre de sécurité Azure opère la recherche et données scientifique équipes de sécurité contrôlant en continu les modifications dans le paysage des menaces. Cela inclut les initiatives suivantes :

- **Menace intelligence surveillance**: menace intelligence inclut mécanismes, les indicateurs, les implications et les conseils pratiques sur existante ou nouvelle menaces. Ces informations sont partagées de la Communauté de la sécurité et Microsoft surveille en permanence menace intelligence flux à partir de sources internes et externes.
- **Partage de signal**: analyses à partir d’équipes de sécurité entre un large éventail de Microsoft des appareils point de terminaison client, les serveurs et les services cloud et en local sont partagés et analysés. 
- **Spécialistes de la sécurité Microsoft**: engagement en cours avec les équipes au sein de Microsoft qui fonctionnent dans les champs de sécurité spécialisés, comme post-mortem et web détection des attaques.
- **Optimisation de la détection**: algorithmes sont exécutés par rapport aux ensembles de données client réel et chercheurs en sécurité collaborer avec les clients pour valider les résultats. Vrais et faux positifs sont utilisées pour affiner algorithmes d’apprentissage automatique.

Ces efforts combinées cas par détection de nouvelles et améliorées, vous pouvez bénéficier d’instantanément : aucune action pour vous permettent de tirer.

## <a name="see-also"></a>Voir aussi
Dans ce document, vous avez appris à détection du centre de sécurité Azure capacités de fonctionnement. Pour en savoir plus sur le centre de sécurité, voir les rubriques suivantes :

- [Centre de sécurité Azure Guide de planification et opérations](security-center-planning-and-operations-guide.md)
- [Gestion et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md)
- [Alertes de sécurité par Type dans le centre de sécurité Azure](security-center-alerts-type.md)
- [Contrôle dans le centre de sécurité Azure d’état sécurité](security-center-monitoring.md) : Découvrez comment contrôler l’état de vos ressources Azure.
- [Surveillance solutions des partenaires avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment contrôler l’état d’intégrité de votre solutions des partenaires.
- [Forum aux questions sur Centre de sécurité Azure](security-center-faq.md) : Forum aux questions sur l’utilisation du service de recherche.
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : rechercher des billets de blog sur la sécurité Azure et de conformité.
