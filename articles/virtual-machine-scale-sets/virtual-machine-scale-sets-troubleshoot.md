<properties
    pageTitle="Résoudre les problèmes d’échelle avec des jeux d’échelle Machine virtuelle | Microsoft Azure"
    description="Résoudre les problèmes d’échelle avec des jeux d’échelle Machine virtuelle. Comprendre les problèmes courants rencontrés et comment les résoudre."
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="guybo"/>

# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Résolution des problèmes d’échelle avec des jeux d’échelle Machine virtuelle

**Problème** : vous avez créé une infrastructure autoscaling dans Gestionnaire de ressources Azure à l’aide des jeux d’échelle machine virtuelle –, par exemple en déploiement d’un modèle à ceci : https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale – vous disposez vos règles échelle définis et qu’il fonctionne parfaitement, sauf que quel que soit la quantité de charge vous placez sur les ordinateurs virtuels, il n’échelle automatique.

## <a name="troubleshooting-steps"></a>Étapes de dépannage

Quelques points à prendre en compte sont les suivants :

- Combien cœurs a-t-il chaque machine virtuelle, et vous chargez chaque cœur ?
 Le modèle de démarrage rapide Azure exemple ci-dessus comporte un script do_work.php qui charge un seul cœur. Si vous utilisez une machine virtuelle supérieure à un seul cœur taille de la mémoire virtuelle, tels que Standard_A1 ou D1, vous devez exécuter cette charge plusieurs fois. Vérifier combien cœurs vos ordinateurs virtuels en passant [machines virtuelles de tailles pour Windows dans Azure](../virtual-machines/virtual-machines-windows-sizes.md)

- Machines virtuelles combien dans l’ensemble d’échelle machine virtuelle, faites-vous travail sur chacun d’eux ?

    Une événement évolution uniquement exécutée lorsque la moyenne de l’UC de **tous les** ordinateurs virtuels dans un jeu d’échelle dépasse la valeur seuil, en fonction du temps interne défini dans les règles d’échelle.

- Vous avez manqué tous les événements échelle ?

    Vérifier que les journaux d’audit dans le portail Azure pour les événements d’échelle. Peut-être est survenu une échelle vers le haut et une échelle vers le bas qui a été manquée. Vous pouvez filtrer par « Échelle »...

    ![Journaux d’audit][audit]

- Sont les seuils d’échelle et horizontale suffisamment différents ?

    Supposons que vous définissez une règle à l’échelle lorsque processeur moyen est supérieur à 50 % plus de 5 minutes et à l’échelle dans où UC moyenne est inférieure à 50 %. Cela entraînera un problème « ailes » lors de l’UC est éloigné de ce seuil, avec les actions d’échelle constamment augmenter et réduire la taille de l’ensemble. Pour cette raison, le service échelle tente d’empêcher « représentant », ce qui peut manifeste comme ne pas mise à l’échelle. Par conséquent, assurez-vous que votre horizontale et seuils d’échelle sont suffisamment différents pour autoriser certaines espace entre les groupes mise à l’échelle.

- Que vous rédigez votre propre modèle JSON ?

    Il est facile de commettre, donc commencez avec un modèle comme celui au-dessus de laquelle preuve à utiliser et apportez les modifications incrémentielles petites. 

- Pouvez-vous manuellement étendre ou arrière ?

    Essayez redéployez la ressource machine virtuelle échelle définie avec un facteur de « capacité » différente pour modifier le nombre de machines virtuelles manuellement. Un exemple de modèle pour effectuer cette action est ici : https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – vous devrez peut-être modifier le modèle pour vous assurer qu’il possède la même taille de l’ordinateur comme votre échelle définie utilise. Si vous pouvez modifier avec succès manuellement le nombre de machines virtuelles, vous savez que le problème est isolé sur échelle automatique.

- Vérifier votre Microsoft.Compute/virtualMachineScaleSet et des ressources Microsoft.Insights dans l' [Explorateur de ressources Azure](https://resources.azure.com/)

    Il s’agit d’un outil de dépannage indispensable qui vous indique l’état de vos ressources Azure le Gestionnaire de ressources. Cliquez sur votre abonnement, observez le groupe de ressources vous résolvez. Sous le fournisseur de ressources cluster examiner l’échelle machine virtuelle ensemble que vous avez créé et consultez la vue d’Instance, qui vous indique l’état d’un déploiement. Vérifiez également la vue d’instance de machines virtuelles dans l’ensemble de l’échelle de machine virtuelle. Puis, sous dans le fournisseur de ressources Microsoft.Insights, cochez que les règles échelle automatique s’affichent correctement.

- L’extension de Diagnostic fonctionne et émettre des données de performance ?

    __Mise à jour :__ Échelle Azure a été améliorée pour utiliser un pipeline de mesures basée sur l’hôte qui n’a plus besoin une extension diagnostics doit être installé. Cela signifie que la prochaine que plusieurs paragraphes ne s’appliquent plus si vous créez une application autoscaling en utilisant le nouveau pipeline. Exemples de modèles Azure qui ont été convertis pour utiliser le pipeline hôte : https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale, https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale. 

    À l’aide d’indicateurs basée sur l’hôte pour échelle automatique est préférable pour les raisons suivantes :

    - Moins de pièces mobiles en tant qu’aucune extensions diagnostics doivent être installées.
    - Modèles plus simples. Simplement ajouter des règles d’échelle idées dans un modèle de jeu échelle existant.
    - Création de rapports plus fiable et le lancement plus rapide de nouvelles machines virtuelles.

    Les raisons uniquement, que vous souhaiterez peut-être continuer à utiliser une extension de diagnostic peuvent être si vous avez besoin de mémoire diagnostics reporting/mise à l’échelle. Indicateurs basée sur l’hôte ne signaler la quantité de mémoire.

    Dans cette optique, suivez uniquement le reste de cet article si vous utilisez toujours extensions de diagnostics pour votre autoscaling.

    Échelle dans le Gestionnaire de ressources Azure peuvent fonctionner (mais n’a plus à) au moyen d’une machine virtuelle extension appelée l’Extension Diagnostics. Il émet des données de performance à un compte de stockage que vous définissez dans le modèle. Ces données sont regroupées puis par le service Moniteur Azure.

    Si le service de perspectives ne peut pas lire les données à partir d’ordinateurs virtuels, il est supposé envoyer un message électronique, par exemple si les ordinateurs virtuels ont été vers le bas, reportez-vous à votre messagerie électronique (celui spécifié lors de la création d’un compte Azure).

    Vous pouvez également accéder et examiner les données vous-même. Examinez le compte de stockage Azure à l’aide d’un Explorateur cloud. Exemple d’utilisation de l' [Explorateur de Cloud Visual Studio](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), connectez-vous et sélectionnez l’abonnement Azure vous utilisez, et le nom de compte de stockage Diagnostics référencées dans la définition d’extension Diagnostics dans votre modèle de déploiement...

    ![Explorer cloud][explorer]

    Ici, vous verrez un certain nombre de tables où les données de chaque machine virtuelle sont stockées. Tirant Linux et la mesure de l’UC par exemple, recherchez les lignes les plus récentes. Explorateur de cloud Visual Studio prend en charge un langage de requête afin de pouvoir exécuter une requête comme « horodatage gt datetime'2016-02-02T21:20:00Z » « pour vous assurer que vous obtenez les événements plus récents (en supposant heure est au format UTC). Signifie les données que vous voyez dans il correspondent aux règles échelle que vous avez configuré ? Dans l’exemple ci-dessous, l’UC pour machine 20 démarré augmenter à 100 % sur les cinq minutes...

    ![Tables de stockage][tables]

    Si les données ne sont pas visible, cela signifie que le problème est avec l’extension de diagnostic en cours d’exécution sur les ordinateurs virtuels. Si les données sont conservées, cela signifie que soit un problème avec vos règles échelle, ou le service de perspectives. Vérifier [l’état Azure](https://azure.microsoft.com/status/).

    Une fois que vous avez été ces étapes, si vous rencontrez toujours des problèmes pour échelle automatique vous pourriez consultez les forums sur [MSDN](https://social.msdn.microsoft.com/forums/azure/home?category=windowsazureplatform%2Cazuremarketplace%2Cwindowsazureplatformctp)ou [dépassement de pile](http://stackoverflow.com/questions/tagged/azure)ou se connecter à un appel de prise en charge. Soyez prêt à partager le modèle et une vue des données de performance.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
