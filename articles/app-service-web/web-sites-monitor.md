<properties
    pageTitle="Surveiller des applications dans le Service d’application Azure"
    description="Apprenez à surveiller des applications dans le Service d’application Azure à l’aide du portail Azure."
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="byvinyal"/>

# <a name="how-to-monitor-apps-in-azure-app-service"></a>Comment : surveiller des applications dans le Service d’application Azure

[Application Service](http://go.microsoft.com/fwlink/?LinkId=529714) fournit les fonctionnalités de surveillance intégrées dans le [Portail Azure](https://portal.azure.com).
Cela inclut la possibilité de passer en revue **les quotas** et **les mesures** pour une application, ainsi que le plan de services d’application, la configuration **d’alertes** et même **mise à l’échelle** automatiquement en fonction de ces indicateurs.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="understanding-quotas-and-metrics"></a>Mesures et les Quotas de présentation

### <a name="quotas"></a>Quotas

Applications hébergées dans le Service d’application sont soumis à certaines *limites* sur les ressources qu’ils peuvent utiliser. Les limites sont définies par le **plan de services d’application** associé à l’application.

Si l’application est hébergée dans un plan de **libre** ou **Shared** , les limites sur les ressources de que l’application peut utiliser sont définies par **les Quotas**.

Si l’application est hébergée dans une **base**, offre **Standard** ou **Premium** , puis les limites sur les ressources qu’ils peuvent utiliser sont définies par la **taille** (petite, moyenne ou grande) et le **nombre d’instances** (1, 2, 3,...) du **plan de services d’application**.

**Les quotas** de **libre** ou **Shared** applications sont :

* **CPU(short)**
   * Montant de l’UC autorisé pour cette application dans un intervalle de 3 minutes. Ce quota réinitialise toutes les trois minutes.
* **CPU(Day)**
   * Montant total de l’UC autorisé pour cette application dans un jour. Ce quota réinitialise toutes les 24 heures à minuit UTC.
* **Mémoire**
   * Montant total de mémoire autorisée pour cette application.
* **Bande passante**
   * Montant total de la bande passante sortante autorisée pour cette application dans un jour.
   Ce quota réinitialise toutes les 24 heures à minuit UTC.
* **Système de fichiers**
   * Montant total de stockage autorisée.

Le quota uniquement applicable aux applications hébergées sur **base**, **Standard** et les plans **Premium** est un **système de fichiers**.

Plus d’informations sur les quotas spécifiques, les limites et les fonctionnalités disponibles pour les références SKU du Service application différents sont accessibles ici : [Limites de Service d’abonnement Azure](../azure-subscription-service-limits.md#app-service-limits)

#### <a name="quota-enforcement"></a>Application des quotas

Si une application dans son utilisation dépasse l' **UC (court)**, **processeur (jour)**, ou le quota de **la bande passante** ensuite l’application sera arrêté jusqu'à ce que le quota de ré-configure. Pendant ce temps, toutes les demandes entrantes seront traduit par un **HTTP 403**.
![][http403]

En cas de dépasse du quota de **mémoire** d’application, l’application sera relancée.

En cas de dépasse du quota de **système de fichiers** , les écrire échoue, cela inclut l’écriture dans les journaux.

Les quotas peuvent augmentées ou supprimés de votre application en mettant à niveau votre plan de services d’application.

### <a name="metrics"></a>Indicateurs

**Métrique** fournissent des informations sur l’application, ou le comportement du plan de services d’application.

Pour une **Application**, les mesures disponibles sont :

* **Temps de réponse moyen**
   * La durée moyenne de l’application pour répondre à des demandes de ms.
* **Mémoire moyenne jeu de travail**
   * Le montant moyen de mémoire dans MiBs utilisé par l’application.
* **Temps processeur**
   * Le montant de l’UC de secondes utilisée par l’application. Pour plus d’informations sur cette métrique, voir : [pourcentage du processeur processeur temps vs](#cpu-time-vs-cpu-percentage)
* **Données dans**
   * La quantité de bande passante entrante utilisée par l’application dans MiBs.
* **Données**
   * La quantité de bande passante sortante utilisée par l’application dans MiBs.
* **2xx http**
   * Nombre de requêtes qui résulte dans un code d’état http > = 200 mais < 300.
* **3xx http**
   * Nombre de requêtes qui résulte dans un code d’état http > = 300 mais < 400.
* **HTTP 401**
   * Nombre de requêtes qui résulte dans code d’état HTTP 401.
* **HTTP 403**
   * Nombre de requêtes qui résulte dans code d’état HTTP 403.
* **HTTP 404**
   * Nombre de requêtes qui résulte dans code d’état HTTP 404.
* **HTTP 406**
   * Nombre de requêtes qui résulte dans code d’état HTTP 406.
* **4xx http**
   * Nombre de requêtes qui résulte dans un code d’état http > = 400 mais < 500.
* **Erreurs de serveur HTTP**
   * Nombre de requêtes qui résulte dans un code d’état http > = 500 mais < 600.
* **Utilisation de la mémoire**
   * Quantité de mémoire utilisée par l’application dans MiBs actuellement.
* **Demandes**
   * Nombre total de demandes quelle que soit leur code d’état HTTP qui en résulte.

Pour un **plan de services d’application**, les mesures disponibles sont :

>[AZURE.NOTE] Application Service plan métriques sont uniquement disponibles pour les plans de **base**, **Standard** et **Premium** référence (SKU).

* **Pourcentage processeur**
   * Moyenne UC utilisée par toutes les instances du plan.
* **Pourcentage de la mémoire**
   * La mémoire moyenne utilisée par toutes les instances du plan.
* **Données dans**
   * La bande passante entrante moyenne, utilisée pour toutes les instances du plan.
* **Données**
   * La moyenne sortant de bande passante utilisée par toutes les instances du plan.
* **Long.**
   * Nombre moyen de lire et d’écrire des requêtes qui ont été mises en attente sur le stockage. Un long. haute est une indication d’une application qui peut ralentir en raison d’un excès e/s de disque.
* **File d’attente http**
   * Nombre moyen de requêtes HTTP en se trouvent dans la file d’attente avant remplies. Une longueur file d’attente HTTP élevée ou augmente est une manifestation d’un plan de charge élevée.

### <a name="cpu-time-vs-cpu-percentage"></a>Pourcentage vs UC du temps processeur
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Il existe 2 métriques qui reflètent de l’UC. **Temps processeur** et le **pourcentage de processeur**

**Temps processeur** est utile pour les applications hébergées dans les plans **libre** ou **partagé** dans la mesure où l’un des leurs quotas est défini en minutes UC utilisés par l’application.

**Pourcentage processeur** quant à eux est utile pour les applications hébergées dans les plans de **base**, **standard** et **premium** dans la mesure où ils peuvent être améliorées et cette métrique est une bonne indication de l’utilisation par toutes les instances.

##<a name="metrics-granularity-and-retention-policy"></a>Précision d’indicateurs et stratégie de rétention

Métrique pour une application et le plan de services d’application n’est connecté et agrégé par le service des stratégies de rétention granularités suivantes :

 * Indicateurs de précision **minute** sont conservés à **48 heures**
 * Indicateurs de précision **heures** sont conservées pendant **30 jours**
 * Indicateurs de précision **jour** sont conservées pendant **90 jours**

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Surveiller les Quotas et indicateurs dans le portail Azure.

Vous pouvez vérifier l’état des différents **les quotas** et les **indicateurs** affectant une application dans le [Portail Azure](https://portal.azure.com).

![][quotas]
Vous trouverez **les quotas** sous Paramètres >**Quotas**. L’expérience utilisateur vous permet de passer en revue : (1) le nom des quotas, (2) sa réinitialiser d’intervalle, (3) son limite actuelle et sa valeur (4) en cours.

![][metrics]
**Indicateurs** peut être access directement à partir de la carte de ressources. Vous pouvez également personnaliser le graphique par : (1) **cliquez** sur et sélectionnez (2) **Modifier graphique**.
À partir de là, vous pouvez modifier la **plage horaire**(3), (4) **le type de graphique**et (5) **indicateurs** à afficher.  

Vous pouvez en savoir plus sur les mesures d’ici : [métriques de service Moniteur](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Alertes et échelle
Mesures pour un plan d’application ou un Service d’application peut être raccroché à des alertes, pour plus d’informations, voir [notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

Applications de Service d’application hébergées dans base, standard ou premium application Service Plans prend en charge **l’échelle**. Ainsi, vous permettent de configurer les règles qui surveiller les mesures de plan de Service d’application et peuvent augmenter ou diminuer le nombre d’instances fournissant des ressources supplémentaires selon vos besoins, ou enregistrement argent lorsque l’application est mise en service excédentaire. Vous pouvez en savoir plus sur l’échelle automatique ici : [comment échelle](../monitoring-and-diagnostics/insights-how-to-scale.md) et [meilleures pratiques pour analyseur Azure autoscaling](../monitoring-and-diagnostics/insights-autoscale-best-practices.md) ici

>[AZURE.NOTE] Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web starter courtes immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

## <a name="whats-changed"></a>Ce qui a changé
* Pour un guide à la modification de sites Web Application Service voir : [Azure Application Service et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)

[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169



<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
