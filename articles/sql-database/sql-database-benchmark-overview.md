<properties
    pageTitle="Vue d’ensemble de test d’évaluation de la base de données SQL Azure"
    description="Cette rubrique décrit la référence de base de données SQL Azure servi à mesurer les performances de base de données SQL Azure."
    services="sql-database"
    documentationCenter="na"
    authors="CarlRabeler"
    manager="jhubbard"
    editor="monicar" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="06/21/2016"
    ms.author="carlrab" />

# <a name="azure-sql-database-benchmark-overview"></a>Vue d’ensemble de test d’évaluation de la base de données SQL Azure

## <a name="overview"></a>Vue d’ensemble
Base de données SQL Microsoft Azure propose trois [niveaux de service](sql-database-service-tiers.md) avec plusieurs niveaux de performance. Chaque niveau de performance fournit un ensemble croissant de ressources, ou « puissance », conçus pour offrir débit toujours plus rapidement.

Il est important de pouvoir quantifiez comment la puissance croissante de chaque niveau de performance se traduit par une augmentation de la base de données. Pour effectuer cette Microsoft a développé la référence de base de données SQL Azure (BASD). Le test d’évaluation teste un mélange d’opérations de base figurant dans toutes les charges de travail OLTP. Nous mesurer le débit obtenu des bases de données en cours d’exécution dans chaque niveau de performance.

Les ressources et la puissance de chaque niveau de service niveau et les performances sont exprimés en termes [d’Unités de Transaction de base de données (DTUs)](sql-database-technical-overview.md#understand-dtus). DTUs permettent de décrire la capacité relative d’un niveau de performance basée sur une mesure combinée de l’UC, la mémoire et lire et écrire des taux offerts par chaque niveau de performance. Doubler l’évaluation DTU d’une base de données est égale à doubler la puissance de base de données. Le test d’évaluation permet d’évaluer l’impact sur les performances de base de données de la puissance croissante offertes par chaque niveau de performance par exercice opérations de base de données réelles, lors de la mise à l’échelle de taille de base de données, le nombre d’utilisateurs et de taux de transaction au prorata des ressources fournies pour la base de données.

En exprimant le débit du niveau de service de base à l’aide de transactions par heure, le niveau de service Standard à l’aide des transactions par minute et le niveau de service Premium à l’aide des transactions par seconde, elle facilite associer rapidement les performances potentiels de chaque niveau de service à la configuration requise d’une application.

## <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Corrélation des résultats de test d’évaluation des performances de base de données réels
Il est important de comprendre que BASD, telles que tous les tests d’évaluation, est représentatives et indicatifs uniquement. Les taux de transaction réalisées avec l’application de test d’évaluation ne sera pas les mêmes que ceux qui peuvent être obtenus avec d’autres applications. Le test d’évaluation comprend un ensemble de transactions différents types pour exécuter un schéma contenant une plage de tables et les types de données. Tandis que le test d’évaluation teste les mêmes opérations de base qui sont communes à toutes les charges de travail OLTP, il ne représente pas une classe spécifique de base de données ou une application. L’objectif de la référence est de servir de guide pour la performance relative d’une base de données qui peut se produire lors de la mise à l’échelle vers le haut ou vers le bas entre les niveaux de performance. En réalité, les bases de données sont de complexité et de différentes tailles, rencontrer différentes combinaisons de charges de travail et répondront de différentes manières. Par exemple, une application IO accrue peut-être atteint les seuils IO plus tôt, ou une application intensive du processeur peut-être atteint les limites processeur plus tôt. Il n’existe aucune garantie que les bases de données particulier mettra à l’échelle de la même façon que le test d’évaluation sous augmentation de charge.

Le test d’évaluation et sa méthodologie sont décrites plus en détail ci-dessous.

## <a name="benchmark-summary"></a>Résumé de test d’évaluation
BASD mesure les performances d’un mélange d’opérations de base de la base de données qui se produisent généralement dans les charges de travail (OLTP) de traitement des transactions en ligne. Bien que la référence est conçue pour le cloud computing l’esprit, le schéma de base de données, le remplissage des données et transactions ont été conçues pour être largement représentant des éléments de base plus couramment utilisés dans les charges de travail OLTP.

## <a name="schema"></a>Schéma
Le schéma est conçu pour avoir suffisamment diverses et la complexité pour prendre en charge un large éventail d’opérations. Le test d’évaluation s’exécute sur une base de données composé de six tables. Les tables appartiennent à trois catégories : taille fixe, mise à l’échelle et croissance. Il existe deux tables de taille fixe ; trois tables mise à l’échelle ; et une table croissante. Tables de taille fixe ont un nombre constant de lignes. Mise à l’échelle les tables ont une cardinalité qui est proportionnelle aux performances de base de données, mais ne change pas pendant le test d’évaluation. Le tableau croissant est dimensionné telles qu’une table de mise à l’échelle sur chargement initial, mais ensuite les modifications de cardinalité au cours d’exécution le test d’évaluation en tant que lignes sont insérés et supprimés.

Le schéma inclut un mélange des types de données, y compris entier, numériques, de caractère et date/heure. Le schéma inclut les clés principales et secondaires, mais pas toutes les clés étrangères – c'est-à-dire qu’il n’existe aucun contraintes d’intégrité référentielle entre les tables.

Un programme de génération de données génère les données pour la base de données initiale. Données numériques et entier sont générées avec diverses stratégies. Dans certains cas, les valeurs sont réparties aléatoirement sur une plage. Dans les autres cas, un ensemble de valeurs est permuté au hasard pour conserver une distribution spécifique. Champs de texte sont générés à partir d’une liste de mots pour produire des données aspect plausible pondérée.

La base de données est dimensionné selon un « facteur d’échelle ». Le facteur d’échelle (abrégé en DF) détermine la cardinalité de la mise à l’échelle et croissance des tables. Comme décrit ci-dessous dans la section utilisateurs et rythmée, la taille de la base de données, le nombre d’utilisateurs et optimiser les performances toutes les mettre à l’échelle proportionnellement à l’autre.

## <a name="transactions"></a>Transactions
La charge de travail se compose des types de transactions neuf, comme indiqué dans le tableau ci-dessous. Chaque transaction est conçue pour mettre en surbrillance un ensemble particulier de caractéristiques du système dans le matériel système et le moteur de base de données, avec l’option Contraste élevé dans les autres opérations. Cette approche facilite l’évaluer l’impact de différents composants de performances globales. Par exemple, la transaction « En lecture lourds » génère un nombre important d’opérations de lecture à partir du disque.

| Type de transaction | Description |
|---|---|
| Lire légère | SÉLECTIONNEZ ; en mémoire ; en lecture seule |
| Support de lecture | SÉLECTIONNEZ ; principalement en mémoire ; en lecture seule |
| Lourds de lecture | SÉLECTIONNEZ ; principalement pas en mémoire ; en lecture seule |
| Mise à jour légère | MISE À JOUR ; en mémoire ; en lecture / écriture |
| Mettre à jour lourds | MISE À JOUR ; principalement pas en mémoire ; en lecture / écriture |
| Insertion légère | INSÉRER ; en mémoire ; en lecture / écriture |
| Insérer lourds | INSÉRER ; principalement pas en mémoire ; en lecture / écriture |
| Supprimer | SUPPRIMER ; mélange d’en mémoire et non en mémoire ; en lecture / écriture |
| Processeur lourds | SÉLECTIONNEZ ; en mémoire ; charge du processeur relativement importante ; en lecture seule |

## <a name="workload-mix"></a>Combinaison de charge de travail
Transactions sont sélectionnées au hasard à partir d’une distribution pondérée avec la combinaison globale suivante. Un rapport en lecture/écriture à environ 2:1 est la combinaison globale.

| Type de transaction | % du Mix |
|---|---|
| Lire légère | 35 |
| Support de lecture | 20 |
| Lourds de lecture | 5 |
| Mise à jour légère | 20 |
| Mettre à jour lourds | 3 |
| Insertion légère | 3 |
| Insérer lourds | 2 |
| Supprimer | 2 |
| Processeur lourds | 10 |

## <a name="users-and-pacing"></a>Utilisateurs et régulation
La charge de travail de référence est disponible à partir d’un outil qui envoie des transactions au sein d’un ensemble de connexions pour simuler le comportement d’un nombre d’utilisateurs simultanément. Bien que toutes les connexions et les transactions sont générée par machine, pour simplifier nous faire référence à ces connexions en tant que « utilisateurs ». Bien que chaque utilisateur fonctionne indépendamment de celles tous les autres utilisateurs, tous les utilisateurs exécuter le même cycle des étapes ci-dessous :

1. Établir une connexion de base de données.
2. Répétez jusqu'à ce que signalé pour quitter :
    - Sélectionnez une transaction de manière aléatoire (à partir d’une distribution pondérée).
    - Effectuer la transaction sélectionnée et mesurer le temps de réponse.
    - Attendez un rythme.
3. Fermer la connexion de base de données.
4. Quitter.

Le rythme (à l’étape 2c) est sélectionnée de manière aléatoire, mais avec une distribution qui possède une moyenne de 1,0 seconde. Par conséquent chaque utilisateur peut, en moyenne, générer une transaction au maximum par seconde.

## <a name="scaling-rules"></a>Règles de mise à l’échelle
Le nombre d’utilisateurs est déterminé par la taille de la base de données (dans les unités suivantes facteur d’échelle). Il existe un utilisateur pour chaque cinq unités facteur d’échelle. En raison de rythme, un utilisateur peut générer une transaction au maximum par seconde, en moyenne.

Par exemple, un échelle-facteur de 500 (DF = 500) base de données contiendra 100 utilisateurs et pouvez obtenir un taux maximal de 100 support. Un support plus élevé au lecteur taux nécessite davantage d’utilisateurs et une base de données plus grande.

Le tableau ci-dessous indique le nombre d’utilisateurs réellement subies pour chaque niveau de performances et de niveau de service.

| Niveau de service (niveau de Performance) | Utilisateurs | Taille de la base de données |
|---|---|---|
| Base | 5 | 720 MO |
| Standard (S0) | 10 | 1 GO |
| Standard (S1) | 20 | 2.1 GO |
| Standard (S2) | 50 | 7.1 GO |
| Premium (P1) | 100 | 14 GO |
| Premium (P2) | 200 | 28 GO |
| Premium (P6/P3) | 800 | GO 114 |

## <a name="measurement-duration"></a>Durée de mesure
Une référence valide exécuter nécessite une durée à l’état stable mesure au moins une heure.

## <a name="metrics"></a>Indicateurs
Les indicateurs clés dans le test d’évaluation sont débit et temps de réponse.

- Débit est la mesure de performance essentiels dans le test d’évaluation. Débit est signalé transactions par unité de temps, en comptant tous les types de transaction.
- Temps de réponse est une mesure de prévisibilité de performances. La contrainte de temps de réponse varie en fonction de classe de service, avec des catégories supérieures de service présentant un délai réponse plus stricte, comme illustré ci-dessous.

| Classe de Service  | Mesure de débit | Délai de réponse |
|---|---|---|
| Premium | Transactions par seconde | 95e centile à 0,5 secondes |
| Standard | Transactions par minute | 90e centile à 1,0 seconde |
| Base | Transactions par heure | centile 80th en secondes 2.0 |

## <a name="conclusion"></a>Conclusion
La référence de base de données SQL Azure mesures de performances relative de base de données SQL Azure en cours d’exécution sur la plage de niveaux de service disponibles et de performances. Le test d’évaluation teste un mélange d’opérations de base de la base de données qui se produisent généralement dans les charges de travail (OLTP) de traitement des transactions en ligne. En mesurant la performance réel, le test d’évaluation fournit une évaluation plus significatif de l’impact sur le débit de changer le niveau de performance en répertoriant uniquement les ressources fournies par chaque niveau telles que la vitesse du processeur, la taille de la mémoire et sorties par que. À l’avenir, nous continuent d’évoluer le test d’évaluation pour élargir son étendue et développer les données fournies.

## <a name="resources"></a>Ressources
[Introduction à la base de données SQL](sql-database-technical-overview.md)

[Niveaux de service et de performances](sql-database-service-tiers.md)

[Conseils pour les performances des bases de données unique](sql-database-performance-guidance.md)
