<properties
    pageTitle="AutoScaling et application Service environnement | Microsoft Azure"
    description="AutoScaling et environnement de Service d’application"
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor=""
/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="byvinyal"
/>

# <a name="autoscaling-and-app-service-environment"></a>AutoScaling et environnement de Service d’application

Les environnements de Service d’application Azure prend en charge *autoscaling*. Vous pouvez pools de travail individuels échelle automatique en fonction de mesures ou de la planification.

![Options d’échelle pour un pool de travail.][intro]

AutoScaling optimise votre utilisation des ressources en automatiquement augmente et réduction d’un environnement de Service d’application pour l’ajuster à votre budget et ou charger le profil.

## <a name="configure-worker-pool-autoscale"></a>Configurer l’échelle de pool de travail

Vous pouvez accéder à la fonctionnalité d’échelle de l’onglet **paramètres** de travail du pool.

![Onglet Paramètres de travail du pool.][settings-scale]

À partir de là, que l’interface doit être assez familier car il est la même expérience que vous voyez quand vous adapter un plan de services d’application. 

![Paramètres d’échelle manuel.][scale-manual]

Vous pouvez également configurer un profil d’échelle.

![Paramètres d’échelle.][scale-profile]

Profils d’échelle sont utiles définir des limites sur votre échelle. Ainsi, vous pouvez avoir une performances cohérentes rencontrer en définissant une valeur d’échelle limite inférieure (1) et une lettrine dépense prévisibles en définissant une limite supérieure (2).

![Paramètres d’échelle dans le profil.][scale-profile2]

Après avoir défini un profil, vous pouvez ajouter des règles d’échelle pour augmenter ou réduire le nombre d’instances dans le pool de travail dans les limites définies par le profil. Règles d’échelle sont basées sur les mesures.

![Règle d’échelle.][scale-rule]

 Métriques frontale ni pool de travail peut servir à définir des règles d’échelle. Les mesures sont les mêmes mesures que vous pouvez surveiller dans les graphiques de carte de ressource ou définir des alertes pour.

## <a name="autoscale-example"></a>Exemple d’échelle

Échelle d’un environnement de Service d’application peut être mieux illustrée en parcourant un scénario.

Cet article explique toutes les considérations nécessaires lorsque vous configurez échelle automatique. L’article vous guide dans les interactions qui entrent en action lorsque vous prendre en compte autoscaling environnements de Service d’application qui sont hébergés dans un environnement de Service d’application.

### <a name="scenario-introduction"></a>Présentation de scénario

David est un administrateur système pour une entreprise qui a migré une partie des charges de travail qu’il gère pour un environnement de Service d’application.

L’environnement d’application Service est configuré sur une échelle de manuelle comme suit :

* **Avant-plan extrémités :** 3
* **Pool de travail 1**: 10
* **Pool de travail 2**: 5
* **Pool de travail 3**: 5

Pool de travail 1 est utilisé pour les charges de travail, tandis que le pool de travail 2 et pool de travail 3 sont utilisées pour les charges de travail de développement et de contrôle qualité (QA).

Les offres de Service d’application pour q & a et développement sont configurés sur une échelle de manuelle. La plan de services d’application de production est définie à l’échelle de traiter des variations de charge et le trafic.

David est très familier avec l’application. Il sait que les heures de pointe de charge sont entre 9:00 AM et 6:00 PM, car il s’agit d’une application (professionnelle)-professionnelles que les employés utiliser lorsqu’ils sont au bureau. L’utilisation tombe après que lorsque les utilisateurs ont été effectuées pour ce jour. En dehors des heures de pointe, il est toujours une charge étant donné que les utilisateurs peuvent accéder à distance à l’application à l’aide de leurs appareils mobiles ou un PC à domicile. La plan de services d’application de production est déjà configurée pour échelle automatique en fonction de l’UC avec les règles suivantes :

![Paramètres spécifiques pour l’application métier.][asp-scale]

|   **Profil échelle – jours ouvrés – plan de services d’application**     |   **Profil échelle – les week-ends – plan de services d’application**     |
|   ----------------------------------------------------    |   ----------------------------------------------------    |
|   **Nom :** Profil de jour de la semaine                               |   **Nom :** Profil de week-end                               |
|   **D’échelle :** Règles de planification et de performances            |   **D’échelle :** Règles de planification et de performances            |
|   **Profil :** Jours de la semaine                                   |   **Profil :** Week-end                                    |
|   **Type :** Périodicité                                    |   **Type :** Périodicité                                    |
|   **Plage cible :** instances de 5 à 20                     |   **Plage cible :** instances de 3 et 10                     |
|   **Jours :** Lundi, mardi, mercredi, jeudi, vendredi  |   **Jours :** Samedi, dimanche                              |
|   **Heure de début :** 9:00 AM                                 |   **Heure de début :** 9:00 AM                                 |
|   **Fuseau horaire :** UTC : 08                                   |   **Fuseau horaire :** UTC : 08                                   |
|                                                           |                                                           |
|   **Règle échelle automatique (échelle haut)**                           |   **Règle échelle automatique (échelle haut)**                           |
|   **Ressource :** Production (application Service environnement)      |   **Ressource :** Production (application Service environnement)      |
|   **Métrique :** PROCESSEUR %                                       |   **Métrique :** PROCESSEUR %                                       |
|   **Opération :** Supérieure à 60 %                         |   **Opération :** Supérieure à 80 %                         |
|   **Durée :** 5 Minutes                                 |   **Durée :** 10 Minutes                                |
|   **Heure d’agrégation :** Moyenne                           |   **Heure d’agrégation :** Moyenne                           |
|   **Action :** Augmenter le nombre de 2                         |   **Action :** Augmenter le nombre de 1                         |
|   **Pouvant être ajoutées vers le bas (en minutes) :** 15                             |   **Pouvant être ajoutées vers le bas (en minutes) :** 20                             |
|                                                           |                                                           |
  	|   **Règle échelle automatique (échelle vers le bas)**                     |   **Règle échelle automatique (échelle vers le bas)**                         |
|   **Ressource :** Production (application Service environnement)      |   **Ressource :** Production (application Service environnement)      |
|   **Métrique :** PROCESSEUR %                                       |   **Métrique :** PROCESSEUR %                                       |
|   **Opération :** Moins de 30 %                            |   **Opération :** Inférieures à 20 %                            |
|   **Durée :** 10 minutes                                |   **Durée :** 15 minutes                                |
|   **Heure d’agrégation :** Moyenne                           |   **Heure d’agrégation :** Moyenne                           |
|   **Action :** Réduire le nombre de 1                         |   **Action :** Réduire le nombre de 1                         |
|   **Pouvant être ajoutées vers le bas (en minutes) :** 20                             |   **Pouvant être ajoutées vers le bas (en minutes) :** 10                             |

### <a name="app-service-plan-inflation-rate"></a>Taux d’inflation application Service plan

Offres de Service d’application qui sont configurés à l’échelle le faire à un taux maximal par heure. Ce taux peut être calculé sur base les valeurs fournies sur la règle d’échelle.

Comprendre et calcul du *taux d’inflation application Service plan* sont important pour application Service environnement échelle car échelle les modifications apportées à un pool de travail ne sont pas instantanées.

L’application Service plan inflation se calcule comme suit :

![Calcul du taux d’inflation de plan de Service d’application.][ASP-Inflation]

En fonction de l’échelle – échelle la règle pour le profil Weekday de production plan de services d’application :

![Taux d’inflation application Service plan pour les jours ouvrés en fonction de l’échelle – échelle la règle.][Equation1]

Dans le cas de l’échelle – échelle la règle pour le profil de week-end de production plan de services d’application, la formule se résoudra en :

![Taux d’inflation application Service plan pour les week-ends basé sur échelle – échelle la règle.][Equation2]

Cette valeur peut également être calculée pour les opérations d’échelle en bas.

En fonction de l’échelle – règle échelle vers le bas pour le profil Weekday de production plan de services d’application, il se présente comme suit :

![Taux d’inflation application Service plan pour les jours ouvrés en fonction de l’échelle – règle échelle vers le bas.][Equation3]

Dans le cas de l’échelle – règle échelle vers le bas pour le profil de week-end de production plan de services d’application, la formule se résoudra en :  

![Taux d’inflation application Service plan pour les week-ends basé sur échelle – règle échelle vers le bas.][Equation4]

La plan de services d’application de production peut aller à un taux maximal de huit instances/heure au cours de la semaine et quatre heures instances pendant le week-end. Elle puisse libérer des instances à un taux maximal de quatre instances/heure au cours de la semaine et six instances/heures pendant les week-ends.

Si plusieurs offres de Service d’application sont hébergés dans un pool de travail, vous devez calculer le *total du taux d’inflation* comme la somme du taux d’inflation pour toutes les offres de Service d’application qui sont en cours d’hébergement dans ce pool de travail.

![Calcul du taux d’inflation total pour plusieurs offres de Service d’application hébergé dans un pool de travail.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Utiliser l’application Service plan inflation pour définir des règles de travail pool échelle automatique

Employé consultant regroupe hébergeant des offres de Service d’application qui sont configurés à l’échelle doivent être alloués une mémoire tampon de la capacité. Permet de la mémoire tampon pour les opérations d’échelle agrandir et réduire le plan de services d’application selon vos besoins. La mémoire tampon minimale serait Total application Service planifier Inflation taux calculé.

Étant donné que les opérations d’échelle application Service environnement prennent un certain temps à appliquer, toute modification apportée devez tenir compte des davantage les modifications à la demande peuvent se produire pendant qu’une opération d’échelle est en cours. Pour prendre en charge cette latence, nous vous recommandons d’utiliser le calculé Total application Service planifier inflation comme étant le nombre minimal d’instances qui sont ajoutés pour chaque opération échelle.

Grâce à ces informations, David pouvez définir les règles et le profil d’échelle suivant :

![Règles de profil échelle par exemple professionnelles.][Worker-Pool-Scale]

|   **Profil échelle – jours ouvrés**                        |   **Profil échelle – les week-ends**                |
|   ----------------------------------------------------    |   --------------------------------------------    |
|   **Nom :** Profil de jour de la semaine                               |   **Nom :** Profil de week-end                       |
|   **D’échelle :** Règles de planification et de performances            |   **D’échelle :** Règles de planification et de performances    |
|   **Profil :** Jours de la semaine                                   |   **Profil :** Week-end                            |
|   **Type :** Périodicité                                    |   **Type :** Périodicité                            |
|   **Plage cible :** instances de 13 et 25                    |   **Plage cible :** instances de 6 à 15             |
|   **Jours :** Lundi, mardi, mercredi, jeudi, vendredi  |   **Jours :** Samedi, dimanche                      |
|   **Heure de début :** 7:00 AM                                 |   **Heure de début :** 9:00 AM                         |
|   **Fuseau horaire :** UTC : 08                                   |   **Fuseau horaire :** UTC : 08                           |
|                                                           |                                                   |
|   **Règle échelle automatique (échelle haut)**                           |   **Règle échelle automatique (échelle haut)**                   |
|   **Ressource :** Pool de travail 1                             |   **Ressource :** Pool de travail 1                     |
|   **Métrique :** WorkersAvailable                            |   **Métrique :** WorkersAvailable                    |
|   **Opération :** Inférieur à 8                              |   **Opération :** Inférieur à 3                      |
|   **Durée :** 20 minutes                                |   **Durée :** 30 minutes                        |
|   **Heure d’agrégation :** Moyenne                           |   **Heure d’agrégation :** Moyenne                   |
|   **Action :** Augmenter le nombre de 8                         |   **Action :** Augmenter le nombre de 3                 |
|   **Pouvant être ajoutées vers le bas (en minutes) :** 180                            |   **Pouvant être ajoutées vers le bas (en minutes) :** 180                    |
|                                                           |                                                   |
|   **Règle échelle automatique (échelle vers le bas)**                         |   **Règle échelle automatique (échelle vers le bas)**                 |
|   **Ressource :** Pool de travail 1                             |   **Ressource :** Pool de travail 1                     |
|   **Métrique :** WorkersAvailable                            |   **Métrique :** WorkersAvailable                    |
|   **Opération :** Supérieures à 8                           |   **Opération :** Égal à 3                   |
|   **Durée :** 20 minutes                                |   **Durée :** 15 minutes                        |
|   **Heure d’agrégation :** Moyenne                           |   **Heure d’agrégation :** Moyenne                   |
|   **Action :** Réduire le nombre de 2                         |   **Action :** Réduire le nombre de 3                 |
|   **Pouvant être ajoutées vers le bas (en minutes) :** 120                            |   **Pouvant être ajoutées vers le bas (en minutes) :** 120                    |

La plage cible définie dans le profil est calculée par les instances minimales définis dans le profil pour le plan de services d’application + mémoire tampon.

La plage Maximum serait la somme de toutes les plages maximales pour toutes les offres de Service d’application hébergés dans le pool de travail.

Le nombre d’augmentation pour l’échelle des règles doit être configuré au moins 1 X le taux d’Inflation application Service Plan d’échelle.

Nombre négatif peut être définie pour qu’elle soit entre 1/2 X ou 1 X le taux d’Inflation application Service Plan pour échelle vers le bas.

### <a name="autoscale-for-front-end-pool"></a>Échelle automatique pour le pool frontal

Règles pour échelle frontale sont plus simples que pour des pools concerné. À l’origine, vous devez  
Assurez-vous que la durée de la mesure et les compteurs ralentissement prendre en compte que les opérations d’échelle dans un plan de services d’application ne sont pas instantanées.

Dans ce scénario, David sache que le taux d’erreur augmente lorsque frontaux parviennent à 80 % de l’UC et définit la règle d’échelle pour augmenter instances comme suit :

![Paramètres d’échelle pour le pool frontal.][Front-End-Scale]

|   **Profil de l’échelle – frontaux**              |
|   --------------------------------------------    |
|   **Nom :** Échelle – frontaux                |
|   **D’échelle :** Règles de planification et de performances    |
|   **Profil :** Tous les jours                           |
|   **Type :** Périodicité                            |
|   **Plage cible :** instances de 3 et 10             |
|   **Jours :** Tous les jours                              |
|   **Heure de début :** 9:00 AM                         |
|   **Fuseau horaire :** UTC : 08                           |
|                                                   |
|   **Règle échelle automatique (échelle haut)**                   |
|   **Ressource :** Pool frontal                    |
|   **Métrique :** PROCESSEUR %                               |
|   **Opération :** Supérieure à 60 %                 |
|   **Durée :** 20 minutes                        |
|   **Heure d’agrégation :** Moyenne                   |
|   **Action :** Augmenter le nombre de 3                 |
|   **Pouvant être ajoutées vers le bas (en minutes) :** 120                    |
|                                                   |
|   **Règle échelle automatique (échelle vers le bas)**                 |
|   **Ressource :** Pool de travail 1                     |
|   **Métrique :** PROCESSEUR %                               |
|   **Opération :** Moins de 30 %                    |
|   **Durée :** 20 Minutes                        |
|   **Heure d’agrégation :** Moyenne                   |
|   **Action :** Réduire le nombre de 3                 |
|   **Pouvant être ajoutées vers le bas (en minutes) :** 120                    |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png
