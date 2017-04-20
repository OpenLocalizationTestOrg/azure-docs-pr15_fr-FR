<properties
   pageTitle="Scénarios courants catalogue de données Azure | Microsoft Azure"
   description="Vue d’ensemble des scénarios courants pour Azure catalogue de données, y compris l’inscription et la découverte de sources de données de valeur élevée, l’activation de décisionnel libre-service et acquisition de connaissances communautaires existant sur les sources de données et processus."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/03/2016"
   ms.author="maroche"/>


# <a name="azure-data-catalog-common-scenarios"></a>Scénarios courants catalogue de données Azure

Cet article présente des scénarios courants où la catalogue de données Azure peut aider les organisations à tirer davantage de valeur à partir de leurs sources de données existantes.

## <a name="scenario-1---registration-of-central-data-sources"></a>Scénario #1 : inscription de sources de données central

Organisations ont souvent un nombre de sources de données de valeur élevée. Ces sources de données sont ligne de systèmes OLTP business data warehouse et à la décision / bases de données analytique. Souvent le nombre de systèmes et le chevauchement des systèmes, au fil du temps en fonction des besoins de l’evolve business, et de l’entreprise lui-même son évolution via acquisitions et.

Il est souvent difficile pour les utilisateurs de savoir où trouver les données dans ces sources de données. Questions comme ceux-ci sont tout trop courantes :

- Des systèmes de ressources humaines trois utilisés au sein de la société, qui dois-je utiliser pour créer ce type de rapport ?
- Où puis-je pour obtenir les numéros de ventes certifiés pour l’exercice que vous venez de mettre fin ?
- Qui dois-je demander ou processus que dois-je utiliser pour accéder au data warehouse ?
- Je ne sais pas si ces valeurs sont droite – qui puis-je demander pour un aperçu sur la façon dont ces données sont censée être utilisée avant de partager ce tableau de bord avec mon équipe ?

Dans ce scénario, le catalogue de données Azure peut aider. Les sources de données centrale, haute valeur, gérée par le service qui sont utilisés dans toute l’organisation sont souvent le point de départ logique pour remplir le catalogue. Bien que tous les utilisateurs peuvent s’inscrire une source de données, ayant le catalogue kick-started avec les sources de données qui sont très probablement liés à un apport précieux pour le plus grand nombre d’utilisateurs aidera incite et utilisation du système. Pour les clients prise en main catalogue de données Azure, identification et l’enregistrement des sources de données clés utilisés par de nombreuses équipes différents des consommateurs de données peuvent être la première étape de succès.

Ce scénario présente également une opportunité à annoter les sources de données de grande valeur pour les rendre plus facile à comprendre et y accéder. Un aspect clé de cet effort consiste à inclure les informations sur la façon dont les utilisateurs peuvent demander l’accès à la source de données. Catalogue de données Azure permet aux utilisateurs d’indiquer l’adresse de messagerie de l’utilisateur ou l’équipe en charge pour contrôler l’accès de source de données, des liens vers des outils existants ou documentation ou financières qui décrit le processus de demande d’accès. Grâce à ces informations incluses dans le catalogue, les utilisateurs qui découvrir les sources de données enregistré, mais qui n’ont pas encore des autorisations d’accès aux données peuvent demander facilement accès à l’aide des processus définis et contrôlés par les propriétaires de source de données.

## <a name="scenario-2---self-service-business-intelligence"></a>Scénario #2 : libre-service à la décision

Bien que les solutions d’analyse décisionnelle d’entreprise traditionnel continuent à faire partie particulièrement de paysages des données de nombreuses organisations, la modification rythme d’entreprise a été décisionnel libre-service plus important. Décisionnel libre-service permet aux travailleurs de l’information et aux analystes de créer leurs propres rapports, des classeurs et tableaux de bord sans compter sur une équipe informatique centrale – ou être restreintes par calendrier et la disponibilité de cette équipe informatique.

Dans les scénarios d’analyse Décisionnelle libre-service, cette pratique est courante pour les utilisateurs combiner des données de plusieurs sources, dont un grand nombre ne peut-être pas ont déjà été utilisés pour BI et l’analyse. Bien que certaines de ces sources de données peuvent être déjà connu, il est souvent une procédure à suivre pour découvrir ce que doit avoir lieu pour localiser et évaluer potentiels sources de données pour une tâche donnée.

En règle générale, ce processus de découverte est un manuel : analystes utilisera leurs connexions réseau homologue pour identifier les autres personnes qui travaillent avec les données recherchées. Après avoir trouvé une source de données qu’il peut être utilisé, mais le processus se répète à nouveau pour chaque ultérieures libre-service BI effort, avec plusieurs utilisateurs effectuant le processus manuel redondant même de découverte.

Catalogue de données Azure utilisateurs peuvent interrompre ce cycle les efforts redondants. Une fois qu’une source de données a été découvert par le biais des moyens traditionnels, un analyste peut inscrire la source de données pour le rendre plus facilement identifiable à l’avenir. Bien que l’utilisateur peut ajouter davantage de valeur à annoter les biens de données enregistré, cela n’a pas besoin d’intervienne en même temps que l’inscription. Les utilisateurs peuvent contribuer au fil du temps, en tant que leur permis planifications, ajout progressivement valeur aux sources de données enregistrés dans le catalogue.

Cette croissance organique du contenu de catalogue est un complément naturel à l’enregistrement de sources de données centrales tout se déroule correctement. Le catalogue de données nécessitant à de nombreux utilisateurs remplissant au préalable peut être un facteur de motivation pour première utilisation et la découverte. Permettre aux utilisateurs de s’inscrire et d’annoter des sources supplémentaires peut être un moyen d’éviter les et leurs pairs engagés.

Il est également à noter que bien que ce scénario se concentre spécifiquement sur décisionnel libre-service, les modèles et les défis associés à une même ne s’appliquent à grande échelle projets BI d’entreprise. N’importe quel effort qui implique un processus manuel de découverte de source de données est un effort qui peut ajouter valeur à l’organisation à l’aide du catalogue de données Azure.

## <a name="scenario-3---capturing-tribal-knowledge"></a>Scénario #3 - capture connaissances en termes

Comment savoir si les données que vous devez effectuer votre travail et où trouver ces données ?

Si vous avez été dans votre projet depuis un certain temps, vous savez probablement uniquement. Vous avez parcouru progressive processus d’apprentissage et au fil du temps avez permis de découvrir les sources de données qui vous permettront de votre journée de travail.

Lorsqu’un nouvel employé rejoint votre équipe, comment sait-il que les données qu’il doit faire son travail et où la trouver ?

Il est fort probable qu’il vous demande.

Ce transfert en cours de connaissances en termes fait partie du processus de détection de source de données dans les entreprises de toutes tailles. Plus expérimentés et senior membres de l’équipe accumulé des connaissances années, et plus récentes membres de l’équipe ont appris à leur demander de poser des questions. Les informations critiques souvent existent uniquement dans les en-têtes de plusieurs personnes, et lorsque ceux-ci est en vacances ou laissez l’équipe, l’organisation sont affectées.

Parfois ces experts données fera l’effort au document leurs connaissances, le partager par courrier électronique ou dans des documents Word sur un site d’équipe SharePoint. Même si cela peut être utile, qu'il présente un nouveau problème de découverte – comment personnes savoir quels documentation existe et où la trouver...

Catalogue de données Azure fournit un emplacement pour le partage de connaissances en termes et pour le rendre facilement détectable. Données experts peuvent annoter directement des ressources de données et peuvent également inclure des liens vers la documentation existante. Ce n’est pas seulement cette capture la base de connaissances, mais il met également à la base de connaissances dans la même expérience qui est utilisée pour la découverte de source de données. Lorsque quelqu'un utilise le catalogue à découvrir une source de données non seulement s’il recherchez la source lui-même, il y trouverez également des connaissances d’expert qui se trouvait uniquement dans l’esprit de l’expert lui-même.
