Il existe certaines limites du nombre de mesures et les événements par application (autrement dit, par clé instrumentation). 

Limites dépendent de la [couche de tarification](https://azure.microsoft.com/pricing/details/application-insights/) que vous choisissez.

**Ressource** | **Limite par défaut** | **Limite maximale**
-------- | ------------- | -------------
Points de données de session<sup>1, 2</sup> par mois | illimité | 
Points de données total par mois pour les demande, événement, dépendances, trace, exception et mode page | 5 millions | 50 millions<sup>3</sup>
[Journaux et suivi des](../articles/application-insights/app-insights-search-diagnostic-logs.md) taux de données | point de distribution 200/s | point de distribution 500/s
Taux de données [exception](../articles/application-insights/app-insights-asp-net-exceptions.md) | point de distribution 50/s | point de distribution 50/s
Taux de données total pour la demande, événements, dépendance et télémétrie du mode page | point de distribution 200/s | point de distribution 500/s
Conservation des données brutes pour [la recherche](../articles/application-insights/app-insights-diagnostic-search.md) et [Analytique](../articles/application-insights/app-insights-analytics.md) | 7 jours
Conservation des données agrégé pour [explorer les mesures](../articles/application-insights/app-insights-metrics-explorer.md) | 90 jours
[Propriété](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) nom nombre | 100 |
Longueur du nom de propriété | 150 | 
Longueur de la propriété valeur | 8192 | 
Longueur du message trace et Exception | 10000 |
[Métrique](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) nom nombre | 100 |
Longueur des noms métrique |  150 | 
[Tests de disponibilité](../articles/application-insights/app-insights-monitor-web-app-availability.md) | 10 | 

<sup>1</sup> un point de données est une valeur métrique individuel ou un événement, avec les propriétés attachées et les mesures.

<sup>2</sup> une session point de données connecte le début ou la fin d’une session et ouvre une identité de l’utilisateur.

<sup>3</sup> , vous pouvez acheter une capacité supplémentaire au-delà de 50 millions.
 
[Sur les prix et les quotas dans perspectives d’Application](../articles/application-insights/app-insights-pricing.md)
