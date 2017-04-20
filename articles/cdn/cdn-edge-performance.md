<properties
    pageTitle="Analyser les performances de bord dans Azure CDN | Microsoft Azure"
    description="Analyser les performances de nœud de bord dans Microsoft Azure CDN. Bord performances Analytique fournit des informations granulaires le trafic et la bande passante utilisé pour le CDN."
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

# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analyser les performances de nœud de bord dans Microsoft Azure CDN

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Vue d’ensemble
Analytique des performances de bord fournit des informations granulaires le trafic et la bande passante utilisé pour le CDN. Ces informations puis peuvent être utilisées pour générer des statistiques actuels, qui vous permettent d’obtenir un aperçu sur la façon dont vos ressources sont en cours mis en cache et remis à vos clients. À son tour, cette option permet une stratégie du formulaire dans l’optimisation de la remise de votre contenu et pour déterminer quels problèmes doivent être traités pour mieux tirer parti du CDN. Par conséquent, non seulement vous pourrez améliorer les performances de remise de données, mais vous pourrez également réduire les coûts de votre fournisseur.

> [AZURE.NOTE] Tous les rapports utilisent notation UTC/GMT lorsque vous spécifiez une date/heure.

## <a name="reports-and-log-collection"></a>Rapports et journal de collection de sites

CDN activité les données sont collectées par le module de bord performances Analytique avant qu’il peut générer des rapports dessus. Ce processus de collection de sites se produit une fois par jour et qu’il traite l’activité ayant eu lieu pendant la journée précédente. Cela signifie que les statistiques d’un état représentent un échantillon des statistiques du jour au moment, il a été traitée et ne pas nécessairement contenir l’ensemble complet des données pour le jour actuel. La fonction principale de ces rapports consiste à évaluer les performances. Ils ne doivent pas être utilisés à des fins facturation ou statistiques numériques exactes.

> [AZURE.NOTE] Les données brutes à partir de laquelle les rapports d’analyse des performances de bord sont générées sont disponibles au moins 90 jours.

## <a name="dashboard"></a>Tableau de bord

Le tableau de bord bord performances Analytique effectue le suivi du trafic CDN en cours et historique via un graphique ainsi que les statistiques. Utilisez ce tableau de bord pour détecter récentes et à long terme des tendances sur les performances du trafic CDN pour votre compte.

Ce tableau de bord se compose de :

* Un graphique interactif qui permet la visualisation des mesures clés et des tendances.
* Une chronologie qui fournit une logique de modèles à long terme les tendances et les mesures clés.
* Mesures principales statistiques et des informations sur la façon de notre réseau CDN améliore le trafic du site en termes de performances globales, l’utilisation et d’efficacité.

### <a name="accessing-the-edge-performance-dashboard"></a>Accéder au tableau de bord de performances de bord

1. À partir de la carte de profil CDN, cliquez sur le bouton **Gérer** .

    ![Bouton gérer de carte de profil CDN](./media/cdn-edge-performance/cdn-manage-btn.png)

    Le portail de gestion CDN s’ouvre.

2. Placez le curseur sur l’onglet **Analytique** , puis placez le curseur sur le Lanceur de **Bord performance Analytique** .  Cliquez sur **tableau de bord**.

    Le tableau de bord bord nœud analytique s’affiche.

### <a name="chart"></a>Graphique

Le tableau de bord contient un graphique qui suit une métrique pendant la période sélectionnée dans la chronologie qui apparaît juste en dessous.  Une barre de planning que graphiques jusqu'à la dernière deux ans d’activité CDN s’affiche juste en dessous du graphique.

#### <a name="using-the-chart"></a>Utilisation du graphique

* Par défaut, sera tracer le taux de rendement du cache pour les 30 derniers jours.
* Ce graphique est généré à partir des données sur une base quotidienne.
* Vous pointez sur un jour dans le graphique en courbes indiquera une date et la valeur de la métrique à cette date.
* Cliquez sur Mettre en surbrillance les week-ends pour activer ou désactiver une superposition de barres de secteur verticales gris clair qui représentent les week-ends sur le graphique. Ce type de superposition est utile pour identifier les modèles de trafic via les week-ends.
* Cliquez sur Afficher une année auparavant pour activer ou désactiver une superposition de l’activité de l’année précédente sur la même période de temps dans le graphique. Ce type de comparaison fournit un aperçu des modèles d’utilisation CDN à long terme. Le coin supérieur droit du graphique contient une légende qui indique le code de couleur pour chaque graphique en courbes.

#### <a name="updating-the-chart"></a>Mise à jour le graphique

* Plage horaire : Effectuez l’une des opérations suivantes :
    * Sélectionnez la région souhaitée dans la barre de planning. Le graphique est mise à jour avec les données qui correspond à la période sélectionnée.
    * Double-cliquez sur le graphique pour afficher toutes les données historiques disponibles avec un maximum de deux ans.
* Métrique : Cliquez sur l’icône du graphique qui apparaît en regard de la mesure souhaitée. Le graphique et la barre de planning seront actualisés avec les données de la métrique correspondante.


### <a name="key-metrics-and-statistics"></a>Statistiques et des mesures clés

#### <a name="efficiency-metrics"></a>Mesures de l’efficacité

L’objectif de ces métriques est pour voir si l’efficacité de cache peut être améliorée. Les principaux avantages dérivées de performances du cache sont :

* Charge réduite sur le serveur d’origine qui peut entraîner :
    * Meilleures performances du serveur web.
    * Réduction des coûts opérationnels.
* Améliorer l’accélération de remise de données depuis plus de requêtes seront servis directement à partir du CDN.

Champ | Description
------|------------
Efficacité du cache | Indique le pourcentage de données transférées qui a été servies à partir du cache. Cette mesures métriques lorsqu’une version mise en cache du contenu demandé a été pris en charge par le CDN (serveurs edge) aux demandeurs (par exemple, le navigateur web)
Taux de réussite | Indique le pourcentage de requêtes qui ont été servies à partir du cache. Cette mesures métriques lorsqu’une version mise en cache du contenu demandé a été pris en charge par le CDN (serveurs edge) aux demandeurs (par exemple, le navigateur web).
% d’octets à distance - aucune configuration de la mémoire Cache | Indique le pourcentage du trafic qui a été servi à partir de serveurs d’origine du CDN (serveurs edge) qui ne sont pas mis en cache à la suite de la fonctionnalité de Cache dérivation (moteur de règles HTTP).
% d’octets à distance - Cache qui a expiré | Indique le pourcentage du trafic qui a été servi à partir de serveurs d’origine du CDN (serveurs edge) résulte revalidation contenue obsolète.

#### <a name="usage-metrics"></a>Mesures de l’utilisation

L’objectif de ces métriques consiste à fournir un aperçu des réduction des coûts de mesures suivantes :

* Réduire les coûts d’exploitation par le biais du CDN.
* Réduction des dépenses CDN grâce à l’efficacité du cache et la compression.

> [AZURE.NOTE] Numéros des volumes le trafic représentent le trafic qui a été utilisé dans les calculs de ratios et de pourcentages et peut afficher uniquement une partie du trafic total pour les clients volumineuses.

Champ | Description
------|------------
Moyenne octets sortants | Indique le nombre d’octets transférés pour chaque demande servie à partir du CDN (serveurs edge) au demandeur (par exemple, le navigateur web).
Aucun taux Config octets Cache | Indique le pourcentage du trafic servi à partir du CDN (serveurs edge) au demandeur (par exemple, le navigateur web) qui n’est pas mis en cache en raison de la fonctionnalité de Cache de contournement.
Taux d’octets compressé | Indique le pourcentage du trafic envoyé par le CDN (serveurs edge) à demandeurs (par exemple, le navigateur web) dans un format compressé.
Octets sortants | Indique la quantité de données, en octets, qui ont été remis à partir du CDN (serveurs edge) au demandeur (par exemple, le navigateur web).  
Octets dans | Indique la quantité de données, en octets, transmis à partir de demandeurs (par exemple, le navigateur web) du CDN (serveurs edge).
Octets à distance | Indique la quantité de données, en octets, envoyés à partir de serveurs origine CDN et clients du CDN (serveurs edge).

#### <a name="performance-metrics"></a>Indicateurs de performance

L’objectif de ces métriques est d’effectuer le suivi des performances globales du fournisseur pour le trafic de votre.

Champ | Description
------|------------
Taux de transfert | Indique le taux moyen auquel le contenu a été transféré depuis le CDN à un demandeur.
Durée | Indique la durée moyenne, en millisecondes, nécessaire à la remise d’un bien à un demandeur (par exemple, le navigateur web).
Taux de requêtes compressé | Indique le pourcentage d’accès qui ont été remis à partir du CDN (serveurs edge) au demandeur (par exemple, le navigateur web) dans un format compressé.
Taux d’erreur 4xx | Indique le pourcentage d’accès à l’origine d’un code d’état 4xx.
Taux d’erreur 5xx | Indique le pourcentage d’accès à l’origine d’un code d’état 5xx.
Accès | Indique le nombre de requêtes pour le contenu CDN.

#### <a name="secure-traffic-metrics"></a>Indicateurs de sécuriser le trafic

L’objectif de ces métriques consiste à suivre les performances CDN pour le trafic HTTPS.

Champ | Description
------|------------
Sécuriser le Cache l’efficacité | Indique le pourcentage de données pour les demandes HTTPS qui ont été traitées en provenance du cache. Cette mesures métriques lorsqu’une version mise en cache du contenu demandé a été pris en charge par le CDN (serveurs edge) aux demandeurs (par exemple, le navigateur web) via HTTPS.
Taux de transfert sécurisé | Indique le taux moyen auquel le contenu a été transféré depuis le CDN (serveurs edge) aux demandeurs (par exemple, les serveurs web) via HTTPS.
Durée sécurisée moyenne | Indique la durée moyenne, en millisecondes, nécessaire à la remise d’un bien à un demandeur (par exemple, le navigateur web) via HTTPS.
Accès sécurisés | Indique le nombre de requêtes HTTPS pour le contenu CDN.
Sécuriser octets sortants | Indique le volume de trafic HTTPS, en octets, qui ont été remis à partir du CDN (serveurs edge) au demandeur (par exemple, le navigateur web).

## <a name="reports"></a>Rapports

Chaque rapport dans ce module contient un graphique et les statistiques sur l’utilisation de la bande passante et le trafic pour différents types de mesures (par exemple, codes d’état HTTP, codes de statut de cache, demander l’URL, etc..). Ces informations peuvent être utilisées pour approfondir plus approfondie comment le contenu est servi à vos clients et pour affiner comportement CDN pour améliorer les performances de remise de données.

### <a name="accessing-the-edge-performance-reports"></a>Accès aux rapports de performances de bord

1. À partir de la carte de profil CDN, cliquez sur le bouton **Gérer** .

    ![Bouton gérer de carte de profil CDN](./media/cdn-edge-performance/cdn-manage-btn.png)

    Le portail de gestion CDN s’ouvre.

2. Placez le curseur sur l’onglet **Analytique** , puis placez le curseur sur le Lanceur de **Bord performance Analytique** .  Cliquez sur **objet volumineux HTTP**.

    L’écran de rapports bord nœud analytique s’affiche.

Rapport | Description
-------|------------
Résumé quotidien | Vous permet d’afficher les tendances du trafic quotidienne pendant une période spécifiée. Ce graphique chaque barre représente une date particulière. La taille de la barre indique le nombre total d’accès qui s’est produite sur cette date.
Synthèse horaire | Permet d’afficher les tendances de trafic toutes les heures sur une période donnée. Chaque barre sur ce graphique représente une seule heure à une date donnée. La taille de la barre indique le nombre total d’accès qui s’est produite au cours de cette heure.
Protocoles | Affiche la répartition du trafic entre les protocoles HTTP et HTTPS. Un graphique en anneau indique le pourcentage d’accès qui s’est produite pour chaque type de protocole.
Méthodes HTTP | Permet de découvrir rapidement quels HTTP méthodes sont utilisées pour demander vos données. En règle générale, les méthodes de demande HTTP plus courants sont GET, tête et POST. Un graphique en anneau indique le pourcentage d’accès qui s’est produite pour chaque type de méthode de demande HTTP.
URL | Contient un graphique qui affiche les 10 URL demandées supérieure. Une barre s’affiche pour chaque URL. La hauteur de la barre indique le nombre d’accès URL spécifique ayant généré sur l’intervalle de temps couvert par le rapport. Statistiques pour les 100 premières demandé Qu'url s’affichent directement en dessous de ce graphique.
Enregistrements CNAME | Contient un graphique qui s’affiche en haut s’étalent sur 10 enregistrements CNAME qui permet de demander des éléments de la durée d’un rapport. Statistiques pour les 100 premières demandé qu'enregistrements CNAME s’affichent directement en dessous de ce graphique.
Origines | Contient un graphique qui affiche le CDN 10 supérieure ou les serveurs d’origine client à partir duquel les biens ont été demandés sur une période spécifiée. Statistiques pour les 100 premières demandé CDN ou client serveurs d’origine sont affichés directement en dessous de ce graphique. Serveurs d’origine client sont identifiés par le nom défini dans l’option nom de l’annuaire.
POP geo | Indique la quantité de votre trafic est routé via une particulier point de présence (POP). L’abréviation de trois lettres représente un POP dans notre réseau CDN.
Clients | Contient un graphique qui affiche les 10 clients demandées actifs sur une période donnée. Aux fins de ce rapport, toutes les demandes qui proviennent de la même adresse IP sont considérés comme du même client. Statistiques pour les 100 clients s’affichent directement en dessous de ce graphique. Ce rapport est utile pour déterminer les modèles d’activité de téléchargement pour vos clients supérieure.
Statuts de cache | Vous donne une description détaillée de comportement de cache, qui peut révéler approches pour améliorer l’expérience utilisateur final globale. Dans la mesure où les meilleures performances provient d’accès au cache, vous pouvez optimiser vitesses de remise de données en réduisant échecs du cache et accès au cache qui a expiré.
Aucun détail | Contient un graphique qui affiche toutes les 10 premières URL d’éléments pour lesquels l’actualisation du contenu de cache n'a pas été activée sur une période spécifiée. Statistiques pour toutes les 100 premières URL de ces types de biens s’affichent directement en dessous de ce graphique.
Détails CONFIG_NOCACHE | Contient un graphique qui affiche les URL 10 premiers des éléments qui n’ont pas mis en cache en raison de la configuration du client CDN. Ces types de biens ont été pris en charge par le serveur d’origine. Statistiques pour toutes les 100 premières URL de ces types de biens s’affichent directement en dessous de ce graphique.
Détails UNCACHEABLE | Contient un graphique qui affiche toutes les 10 premières URL d’actifs pas peuvent être mis en cache en raison des données d’en-tête de demande. Statistiques pour toutes les 100 premières URL de ces types de biens s’affichent directement en dessous de ce graphique.
Détails TCP_HIT | Contient un graphique qui affiche toutes les 10 premières URL d’actifs qui sont servies immédiatement à partir du cache. Statistiques pour toutes les 100 premières URL de ces types de biens s’affichent directement en dessous de ce graphique.
Détails TCP_MISS | Contient un graphique qui affiche toutes les 10 premières URL d’actifs qui ont un état de cache de TCP_MISS. Statistiques pour toutes les 100 premières URL de ces types de biens s’affichent directement en dessous de ce graphique.
Détails TCP_EXPIRED_HIT | Contient un graphique qui affiche toutes les 10 premières URL d’obsolètes actifs qui ont été servies directement à partir de la fenêtre contextuelle. Statistiques pour toutes les 100 premières URL de ces types de biens s’affichent directement en dessous de ce graphique.
Détails TCP_EXPIRED_MISS | Contient un graphique qui affiche toutes les 10 premières URL d’actifs obsolètes pour lequel une nouvelle version devait être récupérées à partir du serveur d’origine. Statistiques pour toutes les 100 premières URL de ces types de biens s’affichent directement en dessous de ce graphique.
Détails TCP_CLIENT_REFRESH_MISS | Contient un graphique à barres qui affiche les 10 premières URL pour les biens ont été extraites à partir d’un serveur d’origine en raison d’une demande d’aucun cache à partir du client. Statistiques pour toutes les 100 premières URL de ces types de requêtes s’affichent directement en dessous de ce graphique.
Types de clients de requête | Indique le type de requêtes qui ont été apportées par les clients HTTP (par exemple, les navigateurs). Ce rapport inclut un graphique en anneau qui fournit un sens en ce qui concerne le traitement des demandes. Informations de la bande passante et le trafic pour chaque type de demande sont affichées sous le graphique.
Agent utilisateur | Contient un graphique à barres affichant les agents 10 utilisateurs supérieure pour demander votre contenu via notre CDN. En règle générale, un agent utilisateur est un navigateur web, lecteur multimédia ou un navigateur de téléphone mobile. Statistiques pour les agents 100 utilisateurs premières s’affichent directement en dessous de ce graphique.
Références | Contient un graphique à barres affichant les 10 principales références au contenu accédé via notre CDN. En règle générale, un point d’accès est l’URL de la page web ou une ressource qui est lié à votre contenu. Des informations détaillées sont fournies sous le graphique pour les renvois 100 premiers.
Types de compression | Contient un graphique en anneau qui répartit les biens demandées par si elles ont été compressés à notre serveurs edge. Le pourcentage de biens compressés est divisé par le type de compression utilisé. Des informations détaillées sont fournies sous le graphique pour chaque type de compression et l’état.
Types de fichiers | Contient un graphique à barres qui affiche les types de 10 fichiers supérieure qui ont été demandées à notre CDN pour votre compte. Aux fins de ce rapport, un type de fichier défini par l’extension de nom de fichier de l’actif et type de média Internet (par exemple, .html \[texte/html\], .htm \[texte/html\], .aspx \[texte/html\], etc..). Des informations détaillées sont fournies sous le graphique pour les types de 100 fichiers supérieure.
Fichiers uniques | Contient un graphique qui représente le nombre total d’éléments uniques qui ont été demandées à un jour donné sur une période spécifiée.
Jetons Auth résumé | Contient un graphique en secteurs qui fournit un aperçu rapide sur si demandées étaient protégées par l’authentification basée sur un jeton. Actifs protégés sont affichés dans le graphique en fonction des résultats de leur tentative d’authentification.
Jeton Auth refuser détails | Contient un graphique à barres qui vous permet d’afficher les 10 premières requêtes qui ont été refusées en raison de l’authentification basée sur un jeton.
Codes de réponse HTTP | Fournit une liste des codes d’état HTTP (par exemple, 200 OK, 403 interdit, 404 non trouvé, etc.) qui ont été remis à vos clients HTTP en notre serveurs edge. Un graphique en secteurs vous permet d’évaluer rapidement la façon dont vos biens ont été pris en charge. Données statistiques détaillées sont fournies pour chaque code réponse sous le graphique.
404 erreurs | Contient un graphique à barres qui vous permet d’afficher les 10 premières requêtes qui a donné lieu à un code de réponse introuvable 404.
403 erreurs | Contient un graphique à barres qui vous permet d’afficher les 10 premières requêtes qui a donné lieu à un code de réponse 403 Interdit. Un code de réponse 403 Interdit se produit lorsqu’une demande est refusée par un serveur d’origine du client ou un serveur de périphérie sur notre POP.
Erreurs 4xx | Contient un graphique à barres qui vous permet d’afficher les 10 premières requêtes qui a donné lieu à un code de réponse dans la plage 400. Sont exclus de ce rapport 403 introuvable et 404 codes de réponse interdit. En règle générale, un code de réponse 4xx se produit lorsqu’une requête est refusée suite à une erreur client.
504 erreurs | Contient un graphique à barres qui vous permet d’afficher les 10 premières requêtes qui a donné lieu à un code de réponse 504 délai de la passerelle. Un code de réponse délai de la passerelle 504 se produit lorsqu’un délai d’attente se produit lorsqu’un proxy HTTP tente de communiquer avec un autre serveur. Dans le cas de notre CDN, un code de réponse délai de la passerelle 504 s’affiche lorsqu’un serveur de périphérie ne parvient pas à communiquer avec un serveur d’origine du client.
502 erreurs | Contient un graphique à barres qui vous permet d’afficher les 10 premières requêtes qui a donné lieu à un code de réponse 502 Passerelle incorrecte. Un code de réponse 502 Passerelle incorrecte se produit en cas d’échec du protocole HTTP entre un serveur et un proxy HTTP. Dans le cas de notre CDN, un code de réponse 502 Passerelle incorrecte se produit généralement lorsqu’un serveur d’origine client renvoie une réponse à un serveur de périphérie non valide. Une réponse n’est pas valide si elle ne peut pas être analysé ou si elle est incomplète.
Erreurs 5xx | Contient un graphique à barres qui vous permet d’afficher les 10 premières requêtes qui a donné lieu à un code de réponse dans la plage 500.  502 Passerelle incorrecte et 504 codes de réponse délai de la passerelle sont exclus de ce rapport.

## <a name="see-also"></a>Voir aussi
* [Vue d’ensemble CDN Azure](cdn-overview.md)
* [Statistiques en temps réel dans Microsoft Azure CDN](cdn-real-time-stats.md)
* [Le comportement par défaut HTTP à l’aide du moteur de règles](cdn-rules-engine.md)
* [Rapports avancées HTTP](cdn-advanced-http-reports.md)
