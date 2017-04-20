<properties 
   pageTitle="StorSimple localement épinglées Forum aux questions sur les volumes | Microsoft Azure"
   description="Fournit des réponses aux questions fréquemment posées sur les volumes StorSimple épinglée localement."
   services="storsimple"
   documentationCenter="NA"
   authors="manuaery"
   manager="syadav"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/16/2016"
   ms.author="manuaery" />

# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple localement épinglées volumes : Forum aux questions (FAQ)

## <a name="overview"></a>Vue d’ensemble

Voici des questions et réponses que vous pourriez rencontrer lorsque vous créez un volume StorSimple épinglée localement, convertissez un volume hiérarchisé vers un volume localement épinglé (et vice versa), ou sauvegardez et restaurez un volume localement épinglé.

Questions et réponses sont classées dans les catégories suivantes

- Création d’un volume localement épinglé
- Sauvegarder une localement épinglé
- Conversion d’un volume hiérarchisé vers un volume localement épinglé
- Restauration d’un volume localement épinglé
- Basculer sur un volume localement épinglé

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Questions sur la création d’un volume localement épinglé

**QUESTIONS/RÉPONSES.** Quelle est la taille maximale d’un volume localement épinglé puis-je créer sur les appareils 8000 série ?

**A** vous pouvez configurer des volumes localement épinglés volumes hiérarchisés ou 8,5 To maximum 200 To maximum sur l’appareil 8100. Sur l’appareil 8600 plus grande, vous pouvez configurer des volumes localement épinglées jusqu'à 22,5 To ou volumes hiérarchisés 500 To maximum.

**QUESTIONS/RÉPONSES.** J’ai récemment mis à niveau mon périphérique 8100 à 2 mise à jour et lorsque j’essaie de créer un volume épinglé localement, la taille maximale disponible est uniquement 6 et pas 8,5 To. Pourquoi ne puis-je pas créer un volume de 8,5 to ?

**A** , vous pouvez configurer localement épinglés volumes jusqu'à 8.5 ou to hiérarchisé volumes 200 To maximum sur l’appareil 8100. Si votre appareil a déjà hiérarchisé volumes, puis l’espace disponible pour la création d’un volume localement épinglé sera proportionnellement inférieur à cette limite maximale. Par exemple, si 100 To des volumes hiérarchisés ont déjà été configuré sur votre appareil 8100 (ce qui correspond à la moitié de la capacité hiérarchisée), puis la taille maximale d’un volume local que vous pouvez créer sur le périphérique 8100 est en conséquence ramenée à 4 To (environ la moitié de la valeur maximale localement épinglées capacité de volume).

Étant donné que certains espace local sur l’appareil est utilisé pour héberger le jeu de travail des volumes hiérarchisés, l’espace disponible pour la création d’un volume localement épinglé est réduite si le périphérique a hiérarchisé volumes. À l’inverse, la création d’un volume localement épinglé proportionnellement réduit l’espace disponible pour les volumes hiérarchisés. Le tableau suivant résume la capacité hiérarchisée disponible sur les appareils 8100 et 8600 lors de la création des volumes localement épinglés.

|Capacité généré volumes localement épinglé|Capacité disponible pour être configuré pour les volumes hiérarchisés - 8100|Capacité disponible pour être configuré pour les volumes hiérarchisés - 8600|
|-----|------|------|
|0 | 200 TO | 500 TO |
|1 TO | 176.5 TO | 477.8 TO|
|4 TO | 105.9 TO | 411.1 TO |
|8.5 TO | TO 0 | 311.1 TO|
|10 TO | NA | 277.8 TO |
|15 TO | NA | 166.7 TO |
|22.5 TO | NA | TO 0 |


**QUESTIONS/RÉPONSES.** Pourquoi y-création d’un volume localement épinglé une opération longue ? 

**A.** Volumes localement épinglés thickly mise en service. Pour créer l’espace sur les couches locales du périphérique, des données à partir des volumes hiérarchisés existantes peuvent être poussées dans le cloud pendant le processus de mise en service. Et, car cela dépend de la taille du volume mis en service en cours, les données existantes sur votre appareil et la bande passante disponible dans le cloud, le temps nécessaire pour créer un volume local peut être plusieurs heures.

**QUESTIONS/RÉPONSES.** Combien de temps faut-il pour créer un volume localement épinglé ?

**A.** Car volumes localement épinglés thickly mise en service, des données existantes à partir des volumes hiérarchisés peuvent poussées dans le cloud pendant le processus de mise en service. Par conséquent, le temps nécessaire pour créer un volume localement épinglé dépend de plusieurs facteurs, notamment la taille du volume, les données sur votre appareil et la bande passante disponible. Sur un appareil nouvellement installé contenant aucun volume, le temps pour créer un volume localement épinglé est environ 10 minutes par To de données. Toutefois, la création de volumes locaux peut prendre quelques heures basées sur les facteurs expliqués ci-dessus sur un appareil est en cours d’utilisation.

**QUESTIONS/RÉPONSES.** Je veux créer un volume localement épinglé. Existe-t-il des pratiques recommandées qu'ai-je besoin importants ?

**A.** Volumes localement épinglés conviennent aux charges de travail qui nécessitent garanties locales de données à tout moment et sont sensibles vers le cloud latence. Considérer que l’utilisation de volumes locaux pour une de vos charges de travail, veuillez tenir compte des éléments suivants :

- Volumes localement épinglés thickly mise en service, et créer des volumes locales a un impact sur l’espace disponible pour les volumes hiérarchisés. Par conséquent, nous vous conseillons commencez par les volumes de petite taille et évoluer en tant que votre demande de stockage augmente.

- Mise en service des volumes locales est une opération longue susceptible d’impliquer transmettre des données existantes à partir des volumes hiérarchisés vers le cloud. Par conséquent, vous pouvez rencontrer des performances réduites sur ces volumes.

- Mise en service des volumes locales est une opération beaucoup de temps. Le temps effectif impliquées dépend de plusieurs facteurs : la taille du volume mis en service en cours, les données sur votre appareil et la bande passante disponible. Si vous n’avez pas sauvegardé vos volumes existants dans le cloud, la création du volume est plus lente. Nous vous recommandons de que vous prenez des instantanés cloud de vos volumes existants avant que vous devez configurer un volume local.
 
- Vous pouvez convertir des volumes hiérarchisés existants vers des volumes localement épinglés, et cette conversion entraîne la mise en service de l’espace sur le périphérique pour le volume localement épinglé résultant (outre interrompre hiérarchisée des données [le cas échéant] à partir du cloud). Là encore, il s’agit d’une opération longue qui dépend de facteurs que nous avons abordés. Nous vous recommandons de sauvegarder vos volumes existants avant la conversion que le processus sera même lent si volumes existants ne sont pas sauvegardées. Votre appareil rencontrés également baisse des performances au cours du processus.
    
Plus d’informations sur la [Création d’un volume localement épinglé](storsimple-manage-volumes-u2.md#add-a-volume)

**QUESTIONS/RÉPONSES.** Puis-je créer plusieurs volumes localement épinglées à la fois ?

**A.** Oui, mais les tâches de création et d’extension volume localement épinglé sont traités dans un ordre séquentiel.

Volumes localement épinglés thickly mise en service et cela nécessite la création d’un espace local sur l’appareil (ce qui peut entraîner des données existantes à partir des volumes hiérarchisés vers dans le cloud pendant le processus de mise en service). Par conséquent, si une tâche de mise en service est en cours, d’autres tâches de création de volume local seront file d’attente jusqu'à ce que cette tâche est terminée.

De même, si un volume local existant est en cours développé ou un volume hiérarchisé est converti en un volume localement épinglé, puis la création d’un nouveau volume localement épinglé est en file d’attente jusqu'à ce que la tâche précédente est terminée. Développer la taille d’un volume localement épinglé implique le développement de l’espace local existant pour ce volume. Conversion d’un hiérarchisé localement épinglée volume implique également la création d’un espace local pour résultant localement épinglées en volume. Dans ces deux de ces opérations, la création ou l’extension d’espace local est un long en cours d’exécution travail.

Vous pouvez afficher ces tâches dans la page **tâches** du service Azure StorSimple Manager. Le travail activement en cours de traitement est en permanence mis à jour l’avancement de l’espace de mise en service. Les tâches de volume localement épinglé restant est marqué comme étant en cours d’exécution, mais leur progression est bloquée et qu’ils sont sélectionnées dans l’ordre qu’ils ont été en file d’attente.

**QUESTIONS/RÉPONSES.** J’ai supprimé un volume localement épinglé. Pourquoi ne puis-je pas voir l’espace récupéré reflétée dans l’espace disponible lorsque j’essaie de créer un nouveau volume ? 

**A.** Si vous supprimez un volume localement épinglé, l’espace disponible pour les nouveaux volumes ne peut pas être mis à jour immédiatement. Le Manager StorSimple Service met à jour l’espace local disponible environ toutes les heures. Nous vous recommandons de que vous patientez une heure avant d’essayer de créer le nouveau volume.

**QUESTIONS/RÉPONSES.** Volumes localement épinglés sont pris en charge sur le matériel cloud ?

**A.** Volumes localement épinglés ne sont pas pris en charge du matériel cloud (périphériques 8010 et 8020 anciennement appelés le périphérique virtuel StorSimple).

**QUESTIONS/RÉPONSES.** Puis-je utiliser les applets de commande PowerShell Azure pour créer et gérer des volumes épinglés localement ? 

**A.** Non, vous ne pouvez pas créer localement épinglés volumes via les applets de commande PowerShell Azure (n’importe quel volume que vous créez via PowerShell Azure hiérarchisé). Il est conseillé de ne pas utiliser les applets de commande PowerShell Azure pour modifier les propriétés d’un volume localement épinglé, comme il aura pour effet de modifier le type de volume indésirable à plusieurs niveaux.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Questions sur la sauvegarde d’un volume localement épinglé

**QUESTIONS/RÉPONSES.** Sont des instantanés locales de volumes localement épinglés pris en charge ?

**A.** Oui, vous pouvez prendre des instantanés locales de vos volumes localement épinglés. Toutefois, nous vous recommandons vivement de sauvegarder régulièrement vos volumes localement épinglées avec des instantanés cloud pour vous assurer que vos données sont protégées dans l’éventualité d’un incident.

**QUESTIONS/RÉPONSES.** Quels sont les instructions pour la gestion des instantanés locales pour les volumes localement épinglés ?

**A.** Fréquents instantanés locales en parallèle avec un taux élevé d’évolution du données dans le volume localement épinglé peuvent provoquer un espace local sur le périphérique à utiliser rapidement et produire des données à partir des volumes hiérarchisés vous appuyez dans le cloud. Nous vous recommandons par conséquent, que vous réduisez le nombre d’instantanés locales.

**QUESTIONS/RÉPONSES.** J’ai reçu une alerte indiquant que mon instantanés locales des volumes localement épinglés peuvent être annulées. Lorsque cela peut se produire ?

**A.** Fréquents instantanés locales en parallèle avec un taux élevé d’évolution du données dans le volume localement épinglé peuvent provoquer un espace local sur le périphérique à utiliser rapidement. Si les couches locales de l’appareil sont très utilisées, une interruption de service cloud étendu peut entraîner le périphérique saturation et écritures entrantes au volume peuvent entraîner annulation d’instantanés (telle qu’aucun espace n’existe pour mettre à jour les instantanés les plus anciens blocs de données qui a été remplacées par le renvoi). Dans ce cas, l’écriture sur le volume continuent à être prises en charge, mais les instantanés locaux sont peut-être pas valides. Il n’existe aucun impact sur vos instantanés cloud existant.

L’avertissement d’alerte est de vous avertir que cette situation peut se produire et vérifier que la même manière au moment opportun par validation vos planifications instantanés local pour prendre des instantanés locales moins fréquentes ou de la suppression d’anciens instantanés locales qui ne sont plus requis.

Si les instantanés locaux sont annulées, vous recevrez une alerte d’information qui vous avertit que les instantanés locales pour la stratégie de sauvegarde spécifique ont été annulées à côté de la liste des horodatages des instantanés locales qui ont été annulées. Ces instantanés sera supprimé automatique et vous serez n’est plus en mesure de les afficher dans la page **Catalogues de sauvegarde** dans le portail classique Azure.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Questions concernant la conversion d’un volume hiérarchisé vers un volume localement épinglé

**QUESTIONS/RÉPONSES.** Je suis en observant certaines lenteur sur le périphérique lors de la conversion d’un volume hiérarchisé vers un volume localement épinglé. Que se passe-t-il ? 

**A.** Le processus de conversion implique deux étapes : 

  1. Mise en service de l’espace sur le périphérique pour le bientôt-à--convertie localement épinglée en volume.
  2. Le téléchargement des données hiérarchisées à partir du cloud pour garantir local garantit.

Ces deux étapes sont longues opérations qui dépendent de la taille du volume en cours de conversion, données sur le périphérique et la bande passante disponible en cours d’exécution. Comme des données à partir des volumes hiérarchisés existants peuvent renverser dans le cloud dans le cadre du processus de mise à disponibilité, votre appareil rencontrés baisse des performances pendant cette période. En outre, le processus de conversion peut être plus lent si :

- Volumes existants n’ont pas été sauvegardés dans le cloud ; afin que nous vous recommandons de vous sauvegardez vos volumes avant de lancer une conversion.

- Stratégies de limitation de bande passante ont été appliqués, qui peuvent limiter la bande passante disponible dans le cloud ; Nous vous recommandons par conséquent, que vous avez un 40 Mbps dédié ou connexion plus dans le cloud.

- Le processus de conversion peut prendre plusieurs heures en raison des plusieurs facteurs expliqués ci-dessus ; Par conséquent, nous suggérer que vous effectuez cette opération pendant les heures non pointes ou sur un week-end afin d’éviter l’impact sur les consommateurs de fin.

Plus d’informations sur la [conversion d’un volume hiérarchisé vers un volume localement épinglé](storsimple-manage-volumes-u2.md#change-the-volume-type)

**QUESTIONS/RÉPONSES.** Puis-je annuler l’opération de conversion de volume ?

**A.** Non, vous ne pouvez pas l’annuler l’opération de conversion lancée une seule fois. Comme indiqué dans la question précédente, veuillez tenir compte des problèmes de performances éventuels éventuellement rencontrés au cours du processus et suivez les recommandations ci-dessus lorsque vous planifiez la conversion.

**QUESTIONS/RÉPONSES.** Que se passe-t-il sur Mon volume cas d’échec de l’opération de conversion ?

**A.** Conversion du volume peut échouer en raison de problèmes de connectivité cloud. L’appareil peut finalement cesser du processus de conversion après une série d’échecs pour interrompre hiérarchisée des données à partir du cloud. Dans ce cas, le type de volume continuent à être le type de volume source avant la conversion, et :

- Une alerte critique est élevée pour être averti de l’échec de la conversion du volume. Plus d’informations sur les [alertes liées aux volumes localement épinglés](storsimple-manage-alerts.md#locally-pinned-volume-alerts)

- Si vous convertissez un hiérarchisé vers un volume localement épinglé, le volume continuent à présentent des propriétés d’un volume hiérarchisé que données peuvent toujours se trouvent sur le nuage. Nous vous recommandons que vous résolvez les problèmes de connectivité et recommencez l’opération de conversion.
 
- De même, en cas d’échec de conversion d’un localement épinglée vers un volume hiérarchisé, bien que le volume est marqué comme un volume épinglé localement, il fonctionnera comme un volume hiérarchisé (car les données peuvent avoir répandu dans le cloud). Toutefois, il continuera occupent de l’espace sur les couches locales du périphérique. Cet espace ne sera pas disponible pour les autres volumes localement épinglés. Nous vous recommandons de relancer cette opération pour vous assurer que la conversion du volume est terminée et que l’espace local sur l’appareil peut être récupéré.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Questions sur la restauration d’un volume localement épinglé

**QUESTIONS/RÉPONSES.** Sont restaurées instantanément des volumes épinglés localement ?

**A.** Oui, localement épinglés volumes sont restaurées instantanément. Dès que les informations de métadonnées pour le volume sont extrait à partir du cloud dans le cadre de l’opération de restauration, le volume est mis en ligne et sont accessibles par l’hôte. Toutefois, garanties locales pour le volume de données ne seront pas disponible jusqu'à ce que toutes les données a été téléchargé à partir du cloud, vous pouvez rencontrer réduire les performances de ces volumes de la durée de la restauration.

**QUESTIONS/RÉPONSES.** Combien de temps faut-il pour restaurer un volume localement épinglé ?

**A.** Les volumes localement épinglés sont restaurées instantanément et mis en ligne dès que les informations de métadonnées volume soient récupérées à partir du cloud, tandis que les données du volume continuent à être téléchargé en arrière-plan. Cette dernière partie de l’opération de restauration--retrouvez les garanties locales pour les données du volume--est une opération longue et peut prendre plusieurs heures pour toutes les données à apporter local à nouveau. Le temps nécessaire pour réaliser la même dépend de plusieurs facteurs, tels que la taille du volume en cours de restauration et la bande passante disponible. Si le volume d’origine est en cours de restauration a été supprimé, vous serez davantage de temps à créer l’espace local sur le périphérique dans le cadre de l’opération de restauration.

**QUESTIONS/RÉPONSES.** J’ai besoin de restaurer mon volume localement épinglé existant vers un instantané plus ancien (pris lorsque le volume a été hiérarchisé). Le volume être restauré comme hiérarchisé dans ce cas ?

**A.** Non, le volume sera restauré comme un volume localement épinglé. Bien que les dates de capture instantanée à la fois quand le volume a été hiérarchisé, lors de la restauration des volumes existants, StorSimple utilise toujours le type de volume sur le disque telles qu’elles existent actuellement.

**QUESTIONS/RÉPONSES.** J’ai étendu Mon volume localement épinglé récemment, mais je dois maintenant restaurer les données à un moment où le volume était plus petit taille. Restaurer redimensionner le volume actuel et dois-je augmenter la taille du volume une fois la restauration terminée ?

**A.** Oui, la restauration est redimensionné le volume, et vous devrez augmenter la taille du volume une fois la restauration terminée.

**QUESTIONS/RÉPONSES.** Puis-je modifier le type d’un volume pendant la restauration ?

**A.** Non, vous ne pouvez pas modifier le type de volume au cours de restauration.

- Volumes qui ont été supprimées sont restaurées comme type de stockés dans l’instantané.

- Volumes existants sont restaurés selon leur type actuelle, quel que soit le type stocké dans l’instantané (voir les deux questions précédente).
 
**QUESTIONS/RÉPONSES.** Je dois restaurer mon volume localement épinglé, mais j’ai choisi un point incorrect dans instantané. Puis-je annuler l’opération en cours de restauration ?

**A.** Oui, vous pouvez annuler une opération de restauration par la suite. L’état du volume sera restaurée à l’état au début de la restauration. Toutefois, toutes les écritures qui ont été apportées au volume lors de la restauration en cours seront perdues.

**QUESTIONS/RÉPONSES.** J’ai commencé une opération de restauration sur l’un de mes volumes localement épinglés, et maintenant je vois un instantané dans mon catalogue file d’attente que je n’équipe création. À quoi il sert ?

**A.** Il s’agit de l’instantané temporaire qui est créée avant la restauration et est utilisé pour la restauration au cas où la restauration est annulée ou échoue. Ne supprimez pas cet instantané ; Il est automatiquement supprimé lorsque la restauration est terminée. Ce problème peut se produire si votre tâche restaurer compte uniquement localement épinglé volumes ou un mélange des volumes localement épinglés et hiérarchisés. Si le travail de restauration inclut uniquement les volumes hiérarchisés, puis ce problème se produira pas.

**QUESTIONS/RÉPONSES.** Puis-je cloner un volume localement épinglé ?

**A.** Si, tu peux. Toutefois, le volume localement épinglé sera cloner comme un volume hiérarchisé par défaut. Plus d’informations sur la façon de [cloner un volume localement épinglé](storsimple-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Questions sur Basculer sur un volume localement épinglé

**QUESTIONS/RÉPONSES.** J’ai besoin de basculer sur mon appareil à un autre périphérique physique. Mon volumes localement épinglés peut être plus comme localement épinglés ou hiérarchisé refusés ?

**A.** Selon la version du logiciel de l’équipement cible, volumes localement épinglés seront alors basculés en tant que :

- Localement épinglées si l’équipement cible est en cours d’exécution StorSimple 8000 série mise à jour 2
- Niveaux si l’équipement cible est en cours d’exécution StorSimple 8000 série mettre à jour 1.x
- Niveaux si le périphérique cible est l’application cloud (version mise à jour 2 ou mettre à jour 1.x)

Plus d’informations sur [le basculement et DR de localement épinglées volumes entre les versions](storsimple-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**QUESTIONS/RÉPONSES.** Volumes localement épinglés instantanément restaurées au cours de sinistre (DR) ?

**A.** Oui, localement épinglés volumes sont restaurées instantanément au cours du basculement. Dès que les informations de métadonnées pour le volume sont extrait à partir du cloud dans le cadre de l’opération de basculement, le volume est mis en ligne sur le périphérique cible et sont accessibles par l’hôte. Pendant ce temps, les données du volume continuent à télécharger en arrière-plan, et vous pouvez rencontrer des performances réduites sur ces volumes pendant la durée du basculement.

**QUESTIONS/RÉPONSES.** Afficher la tâche de basculement terminée, comment puis-je suivre l’évolution du volume localement épinglé en cours de restauration sur le périphérique cible ?

**A.** Pendant une opération de basculement, le travail basculement est marqué comme terminé une seule fois tous les volumes dans le jeu de basculement ont été instantanément restaurés et mis en ligne sur le périphérique cible. Cela inclut les volumes localement épinglés qui peuvent avoir été basculées ; Cependant, garanties locales des données seront uniquement disponibles lorsque toutes les données pour le volume a été téléchargé. Vous pouvez suivre ce cours pour chaque volume localement épinglé a échoué en charge par les tâches restaurer correspondantes qui sont créés dans le cadre du basculement de surveillance. Ces travaux de restauration individuels être créé uniquement pour les volumes localement épinglés.

**QUESTIONS/RÉPONSES.** Puis-je modifier le type d’un volume pendant le basculement ?

**A.** Non, vous ne pouvez pas modifier le type de volume lors d’un basculement. Si vous ne pouvez pas sur vers un autre périphérique physique exécutant 8000 StorSimple série mise à jour 2, les volumes seront alors basculés en fonction du type de volume stocké dans l’instantané. Lors du basculement vers une autre version de l’appareil, reportez-vous à la question ci-dessus sur le type de volume après un basculement.

**QUESTIONS/RÉPONSES.** Puis-je basculer un conteneur de volume avec des volumes localement épinglées à l’application cloud ?

**A.** Si, tu peux. Les volumes localement épinglés seront alors basculées en tant que volumes hiérarchisés. Plus d’informations sur [le basculement et DR de localement épinglées volumes entre les versions](storsimple-device-failover-disaster-recovery.md#considerations-for-device-failover)


