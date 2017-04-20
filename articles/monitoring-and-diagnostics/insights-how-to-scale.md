<properties
    pageTitle="Nombre d’instances de l’échelle manuellement ou automatiquement | Microsoft Azure"
    description="Découvrez comment mettre à l’échelle de vos services Azure."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2015"
    ms.author="robb"/>

# <a name="scale-instance-count-manually-or-automatically"></a>Nombre d’instances de l’échelle manuellement ou automatiquement

Dans le [Portail Azure](https://portal.azure.com/), vous pouvez définir manuellement le nombre d’instances de votre service, ou, vous pouvez définir des paramètres pour qu’il automatiquement échelle basée sur la demande. Ceci est généralement appelé *mise à l’échelle* ou *d’échelle en*.

Avant la mise à l’échelle basée sur le nombre d’instances, vous devez prendre en compte que mise à l’échelle est affecté par **niveau de tarification** outre le nombre d’instances. Différents niveaux de tarification peuvent avoir mémoire et cœurs différents nombres, et par conséquent, ils aura meilleures performances pour le même nombre d’instances (c'est-à-dire *échelle vers le haut* ou *échelle vers le bas*). Cet article traite spécifiquement *échelle en* entrée et *sortie*.

Vous pouvez mettre à l’échelle dans le portail et vous pouvez également utiliser [l’API REST](https://msdn.microsoft.com/library/azure/dn931953.aspx) ou le [Kit de développement .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) pour ajuster l’échelle manuellement ou automatiquement.

> [AZURE.NOTE] Cet article décrit comment créer un paramètre d’échelle dans le portail en [http://portal.azure.com](http://portal.azure.com). Paramètres d’échelle créés dans ce portail ne peut pas être modifié celle-ci le portail classique ([http://manage.windowsazure.com](http://manage.windowsazure.com)).

## <a name="scaling-manually"></a>Mise à l’échelle manuellement

1. Dans le [Portail Azure](https://portal.azure.com/), cliquez sur **Parcourir**, puis accédez à la ressource que vous voulez mettre à l’échelle, par exemple un **plan de services d’application**.

2. La vignette **échelle** dans les **opérations de** vous indiquer l’état de mise à l’échelle : **désactiver** pour lorsque vous soyez échelle manuellement, **sous** lorsque vous soyez mise à l’échelle par un ou plusieurs indicateurs de performance.
    ![Vignette de l’échelle](./media/insights-how-to-scale/Insights_UsageLens.png)

3. Cliquez sur la vignette vous accédez alors à la carte **d’échelle** . Dans la partie supérieure de la cuillère échelle, vous pouvez voir l’historique des actions de l’échelle du service.  
    ![Carte d’échelle](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)

>[AZURE.NOTE] Seules les actions qui sont effectuées par échelle apparaît dans ce graphique. Si vous ajustez manuellement le nombre d’instances, la modification n’est pas répercutée dans ce graphique.

4. Vous pouvez ajuster manuellement numéro **Instances** dont le curseur.
5. Cliquez sur la commande **Enregistrer** et vous devez être mise à l’échelle à ce nombre d’instances presque immédiatement.

## <a name="scaling-based-on-a-pre-set-metric"></a>Mise à l’échelle basée sur une métrique prédéfinie

Si vous souhaitez que le nombre d’instances pour ajuster automatiquement basé sur une mesure, sélectionnez la mesure souhaitée dans la liste déroulante **échelle par** . Par exemple, pour un **plan de services d’application** , vous pouvez évoluer par **Pourcentage processeur**.

1. Lorsque vous sélectionnez une métrique vous obtiendrez un curseur, et/ou, entrez le nombre d’instances que vous souhaitez à l’échelle entre les zones de texte :

    ![Carte d’échelle avec pourcentage processeur](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png)

    Échelle prendra jamais votre service en dessous ou au-dessus des limites que vous avez défini, quel que soit votre charge.

2. Ensuite, vous choisissez la plage cible pour la mesure. Par exemple, si vous avez choisi **pourcentage processeur**, vous pouvez définir une cible du processeur moyenne sur toutes les instances dans votre service. Une échelle à se produit lors de l’UC moyenne dépasse la limite maximale que vous définissez, de même, les conséquences une échelle dans chaque fois que la moyenne du processeur est inférieur à la valeur minimale.

3. Cliquez sur la commande **Enregistrer** . Échelle doit vérifier toutes les n minutes pour vous assurer que vous êtes dans la plage instance et cibles pour votre métrique. Lorsque votre service reçoit le trafic supplémentaire, vous obtiendrez plusieurs instances sans rien faire.

## <a name="scale-based-on-other-metrics"></a>Échelle basée sur d’autres mesures

Vous pouvez mettre à l’échelle en fonction des indicateurs que prédéfinies qui s’affichent dans la liste déroulante **d’échelle** et peuvent même ont un ensemble complexe de mise à l’échelle et mettre à l’échelle dans les règles.

### <a name="adding-or-changing-a-rule"></a>Ajout ou modification d’une règle

1. Choisissez les **règles de planification et de performances** dans la liste déroulante **échelle par** : ![les règles de Performance](./media/insights-how-to-scale/Insights_PerformanceRules.png)

2. Si vous avez précédemment échelle, sur vous verrez un affichage des règles exactes que vous aviez.

3. Pour mettre à l’échelle basée sur un autre clic métrique la ligne **Ajouter une règle** . Vous pouvez également cliquer sur une des lignes existantes pour modifier de la métrique que vous deviez d’abord la mesure que vous souhaitez mettre à l’échelle par.
![Ajouter une règle](./media/insights-how-to-scale/Insights_AddRule.png)

4. Maintenant vous devez sélectionner quel métrique que vous voulez mettre à l’échelle par. Lors du choix d’une métrique il existe quelques éléments à prendre en compte :
    * La *ressource* que provient la métrique. En règle générale, cela sera identique à la ressource que sont mise à l’échelle. Toutefois, si vous voulez mettre à l’échelle de la profondeur d’une file d’attente de stockage, la ressource est la file d’attente que vous souhaitez mettre à l’échelle par.
    * Le *nom métrique* proprement dit.
    * L' *agrégation de temps* de la métrique. Voici comment les données sont combiner sur la *durée*.

5. Après avoir choisi votre métrique vous choisissez le seuil pour la mesure et l’opérateur. Par exemple, vous pouvez dire **supérieure à** **80 %**.

6. Puis sélectionnez l’action que vous souhaitez supprimer. Il existe quelques différents types d’actions :
    * Augmenter ou diminuer selon - cela sera ajouter ou supprimer le numéro de **valeur** d’instances que vous définissez
    * Augmenter ou diminuer pourcentage : cette opération modifie le nombre d’instances par un pourcentage. Par exemple, vous pouvez placer 25 dans le champ de **valeur** , et si vous aviez actuellement 8 instances, 2 sera ajouté.
    * Augmenter ou diminuer à : cette option définit le nombre d’instances de la **valeur** que vous définissez.

7. Enfin, vous pouvez choisir froid vers le bas - la durée pendant laquelle cette règle doit attendre après l’action précédente échelle à l’échelle à nouveau.

8. Après avoir configuré votre règle, cliquez sur **OK**.

9. Une fois que vous avez configuré toutes les règles que vous souhaitez, veillez à cliquer sur la commande **Enregistrer** .

### <a name="scaling-with-multiple-steps"></a>Mise à l’échelle avec plusieurs étapes

Les exemples ci-dessus sont assez simples. Toutefois, si vous voulez être plus de temps à l’échelle vers le haut (ou vers le bas), vous pouvez même ajouter plusieurs règles d’échelle pour la même mesure. Par exemple, vous pouvez définir deux règles échelle sur Pourcentage processeur :

1. Évoluer par 1 instance si pourcentage processeur est supérieure à 60 %
2. Évoluer par 3 instances si pourcentage processeur est supérieur à 85 %

![Plusieurs règles d’échelle](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

Avec cette règle supplémentaire, si votre charge dépasse 85 % avant une action d’échelle, vous obtiendrez deux instances supplémentaires au lieu d’un.

## <a name="scale-based-on-a-schedule"></a>Échelle basée sur une planification


Par défaut, lorsque vous créez une règle d’échelle elle s’appliquera toujours. Vous pouvez constater que lorsque vous cliquez sur l’en-tête de profil :

![Profil](./media/insights-how-to-scale/Insights_Profile.png)

Toutefois, vous souhaiterez peut-être ont plus agressifs échelle de la journée, ou de la semaine, celle le week-end. Vous pouvez même arrêter votre service entièrement désactiver les heures de travail.

1. Pour ce faire, dans le profil que vous avez, sélectionnez **périodicité** au lieu de **toujours,** puis choisissez les heures que vous voulez le profil à appliquer.

2. Par exemple, pour qu’un profil qui s’applique au cours de la semaine, dans la liste déroulante **jours** Décochez **samedi** et **dimanche**.

3. Pour disposer d’un profil qui s’applique pendant la journée, définissez l' **heure de début** à l’heure du jour à laquelle vous voulez commencer à.
    ![Périodicité par défaut](./media/insights-how-to-scale/Insights_ProfileRecurrence.png)

4. Cliquez sur **OK**.

5. Ensuite, vous devrez ajouter le profil que vous souhaitez appliquer à d’autres moments. Cliquez sur la ligne **Ajouter le profil** .
    ![Désactiver le travail](./media/insights-how-to-scale/Insights_ProfileOffWork.png)

6. Nommez votre nouvel, deuxième, profil, par exemple vous pouvez appeler **hors travail**.

7. Puis sélectionnez à nouveau de **périodicité** , puis sélectionnez la plage de nombre instance à pendant cette période.

8. Comme avec le profil par défaut, sélectionnez les **jours** concernés à appliquer à ce profil, l' **heure de début** pendant la journée.

>[AZURE.NOTE] Échelle utilisera les règles d’épargne de l’heure d’été pour n’importe quel **fuseau horaire** que vous sélectionnez. Toutefois, pendant le décalage UTC affiche le décalage de fuseau horaire de base, pas le décalage de votre épargne au format UTC hiver heure.

9. Cliquez sur **OK**.

10. À présent, vous devrez ajouter quelque règles que vous voulez appliquer au cours de votre profil deuxième. Cliquez sur **Ajouter une règle**, et vous pouvez ensuite construire la même règle que vous avez pendant le profil par défaut.
    ![Ajouter une règle pour désactiver le travail](./media/insights-how-to-scale/Insights_RuleOffWork.png)

11. N’oubliez pas de créer une règle de mise à l’échelle et échelle dans, soit pendant le profil le nombre d’instances doit uniquement agrandir (ou diminuer).

12. Pour finir, cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

* [Indicateurs de service Moniteur](insights-how-to-customize-monitoring.md) pour vous assurer que votre service n’est disponible et injoignable.
* [Activer la surveillance et diagnostic](insights-how-to-use-diagnostics.md) pour recueillir des métriques haute fréquence détaillées sur votre service.
* [Recevoir des notifications alertes](insights-receive-alert-notifications.md) chaque fois que les événements opérationnels se produisent ou métriques croisées un seuil.
* [Performances de l’application Moniteur](../application-insights/app-insights-azure-web-apps.md) si vous voulez comprendre exactement comment votre code s’exécute dans le cloud.
* [Afficher les événements et les journaux d’audit](insights-debugging-with-events.md) pour en savoir tout ce qui est arrivé dans votre service.
* [Analyser la disponibilité et la réactivité de n’importe quelle page web](../application-insights/app-insights-monitor-web-app-availability.md) avec des aperçus Application afin que vous pouvez déterminer si votre page ne fonctionne pas.
