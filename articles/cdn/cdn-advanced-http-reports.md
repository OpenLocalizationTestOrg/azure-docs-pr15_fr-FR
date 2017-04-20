<properties
    pageTitle="Azure CDN HTTP rapports avancés | Microsoft Azure"
    description="Rapports HTTP avancées dans Microsoft Azure CDN. Ces rapports fournissent des informations détaillées sur l’activité CDN."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

# <a name="advanced-http-reports-in-microsoft-azure-cdn"></a>Rapports HTTP avancés dans Microsoft Azure CDN

## <a name="overview"></a>Vue d’ensemble

Ce document explique HTTP génération de rapports avancée dans Microsoft Azure CDN. Ces rapports fournissent des informations détaillées sur l’activité CDN.

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>L’accès aux rapports HTTP avancées

1. À partir de la carte de profil CDN, cliquez sur le bouton **Gérer** .

    ![Bouton gérer de carte de profil CDN](./media/cdn-advanced-http-reports/cdn-manage-btn.png)

    Le portail de gestion CDN s’ouvre.

2. Placez le curseur sur l’onglet **Analytique** , puis placez le curseur sur le Lanceur de **Rapports HTTP avancés** .  Cliquez sur la **plateforme grande HTTP**.

    ![Portail de gestion CDN - menu rapports avancés](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)

    Options de rapport sont affichées.

## <a name="geography-reports-map-based"></a>Rapports de géographie (basée sur une carte)

Il existe cinq rapports qui tirer parti d’une carte pour indiquer les régions à partir duquel votre contenu est demandé. Ces rapports sont carte du monde, carte États-Unis, Canada carte, carte d’Europe et Asie Pacifique carte.

Chaque rapport cartographiques classe entités géographiques (c'est-à-dire, pays, États et provinces) en fonction du pourcentage d’accès qui a été créée à partir de cette zone. En outre, une carte est fournie pour vous aider à visualiser les emplacements à partir duquel votre contenu est demandé. Il est en mesure de le faire en codage par couleur de chaque région en fonction de la quantité de la demande expérimentée dans cette zone. Plus claire les zones ombrées indiquent inférieur à la demande pour votre contenu, tandis que les zones plus foncées indiquent le niveau de la demande pour votre contenu.

Vous trouverez des informations détaillées du trafic et la bande passante pour chaque région directement en dessous de la carte. Cela permet de vous permet d’afficher le nombre total d’accès, le pourcentage d’accès, le montant total des données transférées (en gigaoctets) et le pourcentage de données transférés pour chaque région. Afficher une description pour chacun de ces indicateurs. Enfin, lorsque vous survolez une région (c'est-à-dire, pays, région ou département), le nom et le pourcentage d’accès qui s’est produite dans la région s’afficheront sous la forme d’une info-bulle.

Une brève description est fournie ci-dessous pour chaque type de rapport basée sur une carte géographique.

Nom du rapport | Description
------------|------------
Carte du monde | Ce rapport permet de vous permettent de visualiser la demande dans le monde pour votre contenu CDN. Chaque pays utilise des codes couleur sur la carte du monde pour indiquer le pourcentage d’accès qui a été créée à partir de cette zone.
Carte des États-Unis | Ce rapport vous permet d’afficher la demande de votre contenu CDN aux États-Unis. Chaque état est codé en couleur sur cette carte pour indiquer le pourcentage d’accès qui a été créée à partir de cette zone.
Carte Canada | Ce rapport vous permet d’afficher la demande de votre contenu CDN au Canada. Chaque province est codé en couleur sur cette carte pour indiquer le pourcentage d’accès qui a été créée à partir de cette zone.
Carte d’Europe | Ce rapport vous permet d’afficher la demande de votre contenu CDN en Europe. Chaque pays est codé en couleur sur cette carte pour indiquer le pourcentage d’accès qui a été créée à partir de cette zone.
Pays d’Asie Pacifique carte | Ce rapport vous permet d’afficher la demande de votre contenu CDN en Asie. Chaque pays est codé en couleur sur cette carte pour indiquer le pourcentage d’accès qui a été créée à partir de cette zone.

## <a name="geography-reports-bar-charts"></a>Rapports de géographie (graphiques à barres)

Il existe deux rapports supplémentaires qui fournissent des informations statistiques en fonction de la géographie, qui sont des villes haut et pays haut. Ces rapports classement les villes et pays, respectivement, en fonction du nombre d’accès qui a été créée à partir de ces régions. Lors de la génération de ce type de rapport, un graphique à barres indique les 10 premières villes ou pays que demandées du contenu sur une plateforme spécifique. Ce graphique à barres vous permet d’évaluer rapidement les régions qui génèrent le plus grand nombre de requêtes pour votre contenu.

Le côté gauche du graphique (axe des ordonnées) indique le nombre d’accès s’est produite dans la zone spécifiée. Directement en dessous du graphique (axe des abscisses), vous trouverez une étiquette pour chacune des 10 régions supérieure.

### <a name="using-the-bar-charts"></a>À l’aide des graphiques à barres

* Si vous pointez sur une barre, le nom et le nombre total d’accès qui s’est produite dans la région s’afficheront sous la forme d’une info-bulle.
* L’info-bulle pour le rapport villes haut identifie une ville par son nom, le département et l’abréviation du pays.
* Si la ville ou région (par exemple, état/province) à partir de laquelle une demande a été créée n’a pas pu être déterminée, puis elle indiquera qu’elles sont inconnues. Si le pays est inconnu, puis deux points d’interrogation (c'est-à-dire ??), s’affichera.
* Un rapport peut contenir des mesures pour « Europe » ou « Région/Asie-Pacifique ». Ces éléments ne sont pas destinés à fournir des informations statistiques sur toutes les adresses IP dans ces régions. Au lieu de cela, ils s’applique uniquement aux demandes qui proviennent des adresses IP qui sont répartis dans Europe ou Pacifique plutôt que sur une ville spécifique ou un pays.

Les données qui a été utilisées pour générer le graphique à barres peuvent être affichées juste en dessous. Vous y trouverez le nombre total d’accès, le pourcentage d’accès, la quantité de données transférées (en gigaoctets), ainsi que le pourcentage de données transférées pour les zones haut 250. Afficher une description pour chacun de ces indicateurs.

Une brève description est fournie pour les deux types de rapports ci-dessous.

Nom du rapport | Description
------------|------------
Villes haut | Ce rapport classe villes en fonction du nombre d’accès qui a été créée à partir de cette zone.
Principaux pays | Ce rapport classe pays en fonction du nombre d’accès qui a été créée à partir de cette zone.

## <a name="daily-summary"></a>Résumé quotidien

Le rapport résumé quotidien vous permet de vous permet d’afficher le nombre total d’accès et les données transférées sur une plateforme particulière au quotidien. Ces informations peuvent servir à identifier rapidement des motifs CDN activité. Par exemple, ce rapport peut vous aider à détecter les jours expérimentés une valeur supérieure ou inférieures à celle de trafic attendus.

Lors de la génération de ce type de rapport, un graphique à barres constituent une indication visuelle en ce qui concerne la quantité de la demande spécifique à la plateforme expérimentée sur une base quotidienne pendant la période couverte par le rapport. Il fera en affichant une barre pour chaque jour dans le rapport. Par exemple, la sélection de la période de temps appelée « Semaine dernière » génère un graphique à barres avec des barres de sept. Chaque barre indique le nombre total d’accès expérimentés le jour même.

Le côté gauche du graphique (axe des ordonnées) indique le nombre d’accès s’est produite sur la date spécifiée. Directement en dessous du graphique (axe des abscisses), vous trouverez une étiquette qui indique la date (Format : AAAA-MM-JJ) pour chaque jour inclus dans le rapport.

> [AZURE.TIP] Si vous pointez sur une barre, le nombre total d’accès qui s’est produite sur cette date s’affichera comme une info-bulle.

Les données qui a été utilisées pour générer le graphique à barres peuvent être affichées juste en dessous. Vous y trouverez le nombre total d’accès et la quantité de données transférées (en gigaoctets) pour chaque jour couverte par le rapport.

## <a name="by-hour"></a>Par heure

Le rapport par heure vous permet de vous permet d’afficher le nombre total d’accès et les données transférées sur une plateforme particulière toutes les heures. Ces informations peuvent servir à identifier rapidement des motifs CDN activité. Par exemple, ce rapport peut vous aider à détecter les périodes pendant la journée expérience supérieur ou inférieur le trafic attendus.

Lors de la génération de ce type de rapport, un graphique à barres constituent une indication visuelle en ce qui concerne la quantité de la demande spécifique à la plateforme rencontrée sur toutes les heures pendant la période couverte par le rapport. Il fera en affichant une barre pour chaque heure couverte par le rapport. Par exemple, sélection d’un 24 heures période générera un graphique à barres avec des barres de vingt quatre. Chaque barre indique le nombre total d’accès expérimentés au cours de cette heure.

Le côté gauche du graphique (axe des ordonnées) indique le nombre d’accès s’est produite sur l’heure spécifiée. Directement en dessous du graphique (axe des abscisses), vous trouverez une étiquette qui indique la date/heure (Format : AAAA-MM-JJ HH : mm) pour chaque heure inclus dans le rapport. Le temps est déclaré à l’aide de format 24 heures et il est spécifié par le fuseau horaire UTC/GMT.

> [AZURE.TIP] Si vous pointez sur une barre, le nombre total d’accès qui s’est produite au cours de cette heure s’affichera comme une info-bulle.

Les données qui a été utilisées pour générer le graphique à barres peuvent être affichées juste en dessous. Vous y trouverez le nombre total d’accès et la quantité de données transférées (en gigaoctets) pour chaque heure couverte par le rapport.

## <a name="by-file"></a>Par fichier

Le rapport par fichier vous permet de vous permettent de visualiser le montant de la demande et le trafic prévu sur une plateforme particulière pour les biens plus demandées. Lors de la génération de ce type de rapport, un graphique à barres est généré 10 immobilisations plus demandées supérieure sur une période spécifiée.

> [AZURE.NOTE] Aux fins de ce rapport, bord CNAME URL sont convertis en leur équivalent URL CDN. Cela permet un comptage précise pour le nombre total d’accès associés liés une ressource quelle que soit le CDN ou le bord URL CNAME utilisé pour le demander.

Le côté gauche du graphique (axe des ordonnées) indique le nombre de requêtes pour chaque ressource pendant la période spécifiée. Directement au-dessous du graphique (axe des abscisses), vous trouverez une étiquette qui indique le nom de fichier pour chacune des 10 actifs demandées supérieure.

Les données qui a été utilisées pour générer le graphique à barres peuvent être affichées juste en dessous. Vous y trouverez les informations suivantes pour chacun des 250 actifs demandées supérieure : chemin d’accès relatif, le nombre total d’accès, le pourcentage d’accès, la quantité de données transférées (en gigaoctets), ainsi que le pourcentage de données transférées.

## <a name="by-file-detail"></a>En détail de fichier

Le rapport en détail de fichier vous permet de vous permettent de visualiser le montant de la demande et le trafic prévu sur une plateforme spécifique pour une ressource spécifique. Tout en haut de ce rapport est l’option fichier détails pour. Cette option fournit la liste de vos ressources plus demandées concernant la plateforme sélectionnée. Afin de générer un rapport en détail de fichier, vous devrez sélectionner l’élément souhaité à partir de l’option fichier détails pour. Après quoi, un graphique à barres indique la quantité de demande quotidienne il généré pendant la période spécifiée.

Le côté gauche du graphique (axe des ordonnées) indique le nombre total de demandes qu’un bien a rencontré un jour donné. Directement en dessous du graphique (axe des abscisses), vous trouverez une étiquette qui indique la date (Format : AAAA-MM-JJ) pour le CDN à la demande de l’actif a été signalée.

Les données qui a été utilisées pour générer le graphique à barres peuvent être affichées juste en dessous. Vous y trouverez le nombre total d’accès et la quantité de données transférées (en gigaoctets) pour chaque jour couverte par le rapport.

## <a name="by-file-type"></a>Par Type de fichier

Le rapport par Type de fichier permet d’afficher le montant de la demande et le trafic prévu par type de fichier. Lors de la génération de ce type de rapport, un graphique en anneau indique le pourcentage d’accès générés par les types de 10 fichiers supérieure.

> [AZURE.TIP] Si vous pointez sur un secteur du graphique couronne, type de média Internet de que type de fichier s’affiche comme une info-bulle.

Les données qui a été utilisées pour générer le graphique peuvent être affichées juste en dessous. Vous y trouverez le type de média Internet/extension de fichier nom, le nombre total d’accès, le pourcentage d’accès, la quantité de données transférées (en gigaoctets), ainsi que le pourcentage de données transférées pour chacun des types de fichiers haut 250.

## <a name="by-directory"></a>Par répertoire

Le rapport par répertoire vous permet de vous permettent de visualiser le montant de la demande et le trafic prévu sur une plateforme spécifique du contenu d’un répertoire spécifique. Lors de la génération de ce type de rapport, un graphique à barres indique le nombre total d’accès générés par contenu dans les 10 répertoires supérieure.

### <a name="using-the-bar-chart"></a>Utiliser le graphique à barres

* Pointez sur une barre pour afficher le chemin d’accès relatif à l’annuaire correspondant.
* Le contenu stocké dans un sous-dossier d’un répertoire ne compte pas lors du calcul à la demande par répertoire. Ce calcul repose uniquement sur le nombre de requêtes générées pour le contenu stocké dans le répertoire réel.
* Aux fins de ce rapport, bord CNAME URL sont convertis en leur équivalent URL CDN. Cela permet un comptage précise pour toutes les statistiques associés liés une ressource quelle que soit le CDN ou le bord URL CNAME utilisé pour le demander.

Le côté gauche du graphique (axe des ordonnées) indique le nombre total de requêtes pour le contenu stocké dans vos répertoires 10 premiers. Chaque barre du graphique représente un répertoire. Utilisez le schéma de codage de couleur pour correspondent pas à une barre dans un répertoire listé dans la section répertoires complète haut 250.

Les données qui a été utilisées pour générer le graphique à barres peuvent être affichées juste en dessous. Vous y trouverez les informations suivantes pour chacun des répertoires haut 250 : chemin d’accès relatif, le nombre total d’accès, le pourcentage d’accès, la quantité de données transférées (en gigaoctets), ainsi que le pourcentage de données transférées.

## <a name="by-browser"></a>Par navigateur

Le rapport par navigateur vous permet de vous permettent de visualiser les navigateurs ont été utilisés pour demander du contenu. Lors de la génération de ce type de rapport, un graphique en secteurs indique le pourcentage de requêtes gérées par les 10 navigateurs.

### <a name="using-the-pie-chart"></a>Utiliser le graphique en secteurs

* Pointez sur un secteur du graphique en secteurs pour afficher le nom et la version d’un navigateur.
* Aux fins de ce rapport, chaque combinaison unique/version de navigateur est considéré comme un autre navigateur.
* Le secteur appelé « Autres » indique le pourcentage de demandes traitées par tous les autres navigateurs et les versions.

Les données qui a été utilisées pour générer le graphique en secteurs peuvent être affichées juste en dessous. Vous y trouverez le numéro de type/version de navigateur, le nombre total d’accès et le pourcentage d’accès pour chacun des navigateurs haut 250.

## <a name="by-referrer"></a>Par point d’accès

Le rapport par Referrer vous permet de vous permettent de visualiser les principales références à contenu sur la plateforme sélectionnée. Un point d’accès indique le nom d’hôte à partir duquel une demande a été générée. Lors de la génération de ce type de rapport, un graphique à barres indique la quantité de la demande (c'est-à-dire, accès) générée par les 10 principales références.

Le côté gauche du graphique (axe des ordonnées) indique le nombre total de demandes qu’a rencontré un bien pour chaque point d’accès. Chaque barre du graphique représente un point d’accès. Utilisez le schéma de codage de couleur pour correspondre à l’une barre à un point d’accès répertorié dans la section haut 250 Referrer.

Les données qui a été utilisées pour générer le graphique à barres peuvent être affichées juste en dessous. Vous y trouverez l’URL, le nombre total d’accès et le pourcentage d’accès généré à partir de chacun des 250 principales références.

## <a name="by-download"></a>Par téléchargement

Le rapport en télécharger vous permet d’analyser des modèles de téléchargement pour votre contenu plus demandée. La partie supérieure du rapport contient un graphique à barres que compare lancée téléchargements avec téléchargements terminés pour les 10 biens demandées supérieure. Chaque barre utilise des codes couleur selon qu’il s’agit d’un tentative de téléchargement (bleu) ou un téléchargement terminé (vert).

> [AZURE.NOTE] Aux fins de ce rapport, bord CNAME URL sont convertis en leur équivalent URL CDN. Cela permet un comptage précise pour toutes les statistiques associés liés une ressource quelle que soit le CDN ou le bord URL CNAME utilisé pour le demander.

Le côté gauche du graphique (axe des ordonnées) indique le nom de fichier pour chacune des 10 actifs demandées supérieure. Directement en dessous du graphique (axe des abscisses), vous trouverez des étiquettes indiquant le nombre total de téléchargements a tenté/terminé.

Directement en dessous du graphique à barres, les informations suivantes sont répertoriées pour les biens demandées 250 supérieure : chemin d’accès relatif (y compris le nom de fichier), le nombre de fois qu’il a été téléchargé jusqu'à la fin, le nombre de fois qu’il a été demandé et le pourcentage de requêtes qui a donné lieu dans un téléchargement complet.

> [AZURE.TIP] Notre CDN n’est pas informé par un client HTTP (c'est-à-dire navigateur) quand un actif a été entièrement téléchargé. Par conséquent, nous avons calculer si une ressource a été téléchargée en fonction des codes d’état et de plage d’octets demandes. La première chose que nous recherchez ce calcul est indique si la demande aboutit à un code d’état OK 200. Si c’est le cas, puis nous examinons demandes de plage d’octets pour vous assurer qu’elles couvrent les biens entière. Pour finir, nous comparer le volume de données transférées à la taille de la ressource demandée. Si les données transférées sont égale ou supérieure à la taille de fichier et les demandes de plage d’octets sont adaptées à cet actif, l’accès à compter en téléchargement terminé.
>
>En raison de la nature interprétation de ce rapport, à garder à l’esprit les points suivants peuvent modifier la cohérence et la précision de ce rapport.
>
>* Modèles de trafic ne peut pas être prédites avec précision lorsque agents utilisateurs de se comportent différemment. Cela peut entraîner des résultats de téléchargement terminé qui sont supérieures à 100 %.
>* Biens tirer parti de téléchargement Progressive HTTP ne peuvent pas être précisément représentées par ce rapport. Cela est dû à des utilisateurs cherchant à différents endroits dans une vidéo.

## <a name="by-404-errors"></a>Par 404 erreurs

Le rapport d’erreurs par 404 vous permet d’identifier le type de contenu qui génère le plus grand nombre 404 introuvable des codes d’état. La partie supérieure du rapport contient un graphique à barres pour les biens 10 premiers pour lequel un code d’état 404 introuvable a été retourné. Ce graphique à barres compare le nombre total de demandes de requêtes qui a donné lieu à un code d’état introuvable 404 pour ces actifs. Chaque barre utilise des codes couleur. Une barre jaune est utilisée pour indiquer que la demande a donné lieu à un code d’état 404 introuvable. Une barre rouge est utilisée pour indiquer le nombre total de demandes de l’actif.

> [AZURE.NOTE] Aux fins de ce rapport, notez les points suivants :
>
>* Un accès représente toute demande d’un bien quel que soit le code d’état.
>* Bord CNAME URL sont convertis en leur équivalent URL CDN. Cela permet un comptage précise pour toutes les statistiques associés liés une ressource quelle que soit le CDN ou le bord URL CNAME utilisé pour le demander.

Le côté gauche du graphique (axe des ordonnées) indique le nom de fichier pour chacune des ressources demandées 10 supérieure qui a donné lieu à un code d’état 404 introuvable. Directement en dessous du graphique (axe des abscisses), vous trouverez les étiquettes qui indiquent le nombre total de demandes et le nombre de requêtes qui a donné lieu à un code d’état 404 introuvable.

Directement en dessous du graphique à barres, les informations suivantes sont répertoriées pour les biens demandées 250 supérieure : chemin d’accès relatif (y compris le nom de fichier), le nombre de requêtes qui a donné lieu à un code d’état 404 introuvable, le nombre total de fois qu’une requête a été l’actif et le pourcentage de requêtes qui a donné lieu à un code d’état 404 introuvable.

## <a name="see-also"></a>Voir aussi
* [Vue d’ensemble CDN Azure](cdn-overview.md)
* [Statistiques en temps réel dans Microsoft Azure CDN](cdn-real-time-stats.md)
* [Le comportement par défaut HTTP à l’aide du moteur de règles](cdn-rules-engine.md)
* [Analyser les performances de bord](cdn-edge-performance.md)
