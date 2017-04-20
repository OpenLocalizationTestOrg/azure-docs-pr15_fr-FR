<properties
    pageTitle="Planifier la capacité pour la protection des machines virtuelles et des serveurs physiques dans Azure Site récupération | Microsoft Azure"
    description="Récupération de Site Azure coordonnées de la réplication, le basculement et la restauration des machines virtuelles et des serveurs physiques situés en local sur Azure ou sur un site secondaire en local." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="07/12/2016" 
    ms.author="raynew"/>

# <a name="plan-capacity-for-protecting-virtual-machines-and-physical-servers-in-azure-site-recovery"></a>Planifier la capacité pour la protection des machines virtuelles et des serveurs physiques dans récupération de Site Azure

L’outil Planificateur de capacité de récupération de Site Azure vous aide à identifier vos besoins pour la protection des ordinateurs virtuels Hyper-V VMware machines virtuelles et des serveurs physiques Windows/Linux avec récupération de Site Azure.


## <a name="overview"></a>Vue d’ensemble

Utiliser le Planificateur de capacités de récupération de Site pour analyser l’environnement source et les charges de travail et identifier les besoins de bande passante, ressources du serveur que vous aurez besoin dans votre emplacement source et les ressources (machines virtuelles et stockage etc.), vous aurez besoin dans votre emplacement cible. 

Vous pouvez exécuter l’outil de modes deux :

- **Planification rapide**: exécuter l’outil dans ce mode pour obtenir projections réseau et serveur basé sur un nombre moyen de machines virtuelles, disques, le stockage et taux de modification.
- **Planification détaillé**: exécuter l’outil dans ce mode et fournir des détails de chaque charge de travail au niveau de la machine virtuelle. Analyser compatibilité machine virtuelle et obtenez des projections réseau et du serveur.

## <a name="before-you-start"></a>Avant de commencer

Avant d’exécuter l’outil :

1. Collecter les informations relatives à votre environnement, y compris les machines virtuelles, disques par machine virtuelle, espace de stockage par disque.
2. Identifier votre taux de modification (évolution du) quotidien pour les données répliquées. Pour cela :

    - Si vous êtes réplication des ordinateurs virtuels Hyper-V, puis téléchargez l' [outil de planification de capacité Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) pour obtenir le taux de modification. [En savoir plus](site-recovery-capacity-planning-for-hyper-v-replication.md) sur cet outil. Nous vous recommandons de qu'exécuter cet outil sur une semaine pour capturer des moyennes.
    - Si vous êtes réplication machines virtuelles VMware, utiliser l' [application de planification de la capacité vSphere](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance) pour calculer le taux de l’évolution du.
    - Si vous êtes la réplication des serveurs physiques, vous devez estimer manuellement.

## <a name="run-the-quick-planner"></a>Exécuter le planificateur rapide
1.  Télécharger et ouvrir l’outil [Planificateur de capacité de récupération de Site Azure](http://aka.ms/asr-capacity-planner-excel) . Vous devez exécuter des macros ainsi Sélectionnez cette option pour activer l’édition et activer le contenu lorsque vous y êtes invité. 
2.  Dans la zone **Sélectionner un type de planificateur** sélectionnez **Planificateur rapide** dans la zone de liste.

    ![Prise en main](./media/site-recovery-capacity-planner/getting-started.png)

3.  Dans la feuille de calcul **Capacité planificateur** Entrez les informations requises. Vous devez renseigner tous les champs entourées en rouge dans la capture d’écran ci-dessous.

    - Dans la zone **sélectionner votre scénario** choisissez **Hyper-V pour Azure** ou **VMware/physique vers Azure**.
    - Dans la **moyenne des données quotidiennes modifier taux (%)** placer les informations vous collecter à l’aide de l' [outil de planification de capacité Hyper-V](site-recovery-capacity-planning-for-hyper-v-replication.md) ou la [capacité vSphere planification matériel](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance).  
    - **Compression** s’applique uniquement à la compression proposée lors de la réplication des machines virtuelles VMware ou serveurs physiques vers Azure. Nous estimer les 30 % ou plus, mais vous pouvez modifier le paramètre selon vos besoins. Pour la réplication des ordinateurs virtuels Hyper-V à la compression Azure, vous pouvez utiliser un appareil tiers comme Riverbed. 
    -  Dans **Les entrées de rétention** spécifier combien réplicas doivent être conservées. Si vous êtes la réplication VMware ou la valeur d’entrée de serveurs physiques en jours. Si vous êtes réplication Hyper-V spécifier la durée en heures.
    -  Dans **nombre d’heures dans lequel la réplication initiale du lot de machines virtuelles doit se terminer** et le **nombre de machines virtuelles par lot de réplication initiale** d’entrer les paramètres utilisés pour calculer les besoins de réplication initial.  Lors de la récupération de Site est déployée le jeu de données initial doit être téléchargé. 

    ![Entrées](./media/site-recovery-capacity-planner/inputs.png)

2.  Une fois que vous avez créée dans les valeurs de l’environnement source, sortie affichée inclut :

    - **La bande passante requise pour la réplication delta** (Mo/s). La bande passante réseau pour la réplication delta est calculée sur la fréquence de modification de données quotidienne moyenne.
    - **La bande passante requise pour la réplication initiale** (Mo/s). La bande passante réseau pour la réplication initiale est calculée sur les valeurs de la réplication initiale dans que vous stockez. 
    - **Stockage prévues (Go)** est le stockage Azure total requis.
    - **Sorties par total sur les comptes de stockage standard** est calculé sur base taille de l’unité 8 Ko sorties par sur les comptes d’espace de stockage total standard.  Pour le planificateur rapide que le nombre est calculé en fonction de tous les disques machines virtuelles de sources de données quotidiennes modifier taux. Pour le planificateur détaillées que le nombre est calculé en fonction du nombre total de machines virtuelles qui sont mappés à standard Azure machines virtuelles et des données Modifier taux sur ces machines virtuelles. 
    - **Nombre de comptes de stockage standard** fournit le nombre total de comptes de stockage standard nécessaire pour protéger les ordinateurs virtuels. Notez qu’un compte de stockage standard peut contenir jusqu'à 20 000 sorties par sur tous les ordinateurs virtuels dans un espace de stockage standard et sorties 500 par maximales pris en charge par disque. 
    - **Nombre de disques blob obligatoire** indique le nombre de disques qui seront créés sur le stockage Azure.
    - **Nombre de comptes de stockage premium obligatoire** fournit le nombre total de comptes de stockage premium nécessaire pour protéger les ordinateurs virtuels. Notez qu’une source machine virtuelle avec sorties de par haute (plus de 20000) a besoin d’un compte de stockage premium. Un compte de stockage premium peut contenir jusqu'à 80 000 sorties par.
    - **Sorties par total sur le stockage premium** est calculé sur base taille de l’unité sorties par 256 Ko sur les comptes de stockage total premium.  Pour le planificateur rapide que le nombre est calculé en fonction de tous les disques machines virtuelles de sources de données quotidiennes modifier taux. Pour le planificateur détaillées que le nombre est calculé en fonction du nombre total de machines virtuelles qui sont mappés aux premium Azure machine virtuelle (série DS et GS) et les données Modifier taux sur ces machines virtuelles. 
    - **Nombre de serveurs configuration requise** indique combien de serveurs configuration requises pour le déploiement (1)
    - **Nombre de serveurs supplémentaires requises** indique si les serveurs de traitement supplémentaires sont nécessaires en plus du serveur de processus est configuré sur le serveur de configuration par défaut.
    - **100 % un espace de stockage supplémentaire sur la source** indique si l’espace de stockage supplémentaire est requise dans l’emplacement source.
            
    ![Sortie](./media/site-recovery-capacity-planner/output.png)
 
## <a name="run-the-detailed-planner"></a>Exécuter le planificateur détaillé


1.  Télécharger et ouvrir l’outil [Planificateur de capacité de récupération de Site Azure](http://aka.ms/asr-capacity-planner-excel) . Vous devez exécuter des macros ainsi Sélectionnez cette option pour activer l’édition et activer le contenu lorsque vous y êtes invité. 
2.  Dans la zone **Sélectionner un type de planificateur** sélectionnez **Planificateur détaillées** à partir de la zone de liste.

    ![Prise en main](./media/site-recovery-capacity-planner/getting-started-2.png)

3.  Dans la feuille de calcul de **La charge de travail Qualification** Entrez les informations requises. Vous devez renseigner tous les champs marqués.

    - Dans **cœurs** spécifier le nombre total de cœurs sur un serveur source.
    - Dans **l’allocation de mémoire en Mo** spécifier la taille de RAM d’un serveur source. 
    - Le **Nombre de cartes réseau** spécifier le nombre de cartes réseau sur un serveur source. 
    -  Dans **l’espace de stockage Total (en Go)** spécifiez la taille totale de la mémoire virtuelle. Par exemple, si le serveur source a 3 disques 500 Go, la taille de stockage total est 1500 go.
    -  Nombre **de disques attachés** spécifier le nombre total de disques d’un serveur source.
    -  **Utilisation de la capacité disque** spécifier l’utilisation moyenne.
    -  Dans **tous les jours modifier taux (%)** spécifiez que les données quotidiennes modifier taux d’un serveur source.
    -  Taille de **Mappage Azure** Entrez taille de mémoire virtuelle Azure que vous souhaitez mapper. Si vous ne voulez pas effectuer cette opération manuellement, cliquez sur le**Calcul des machines virtuelles IaaS**. Notez que si un paramètre manuel de saisie, puis cliquez sur Calculer des machines virtuelles IaaS vos paramètres manuels peuvent être remplacée, car le processus de cluster identifie automatiquement la meilleure solution sur taille de mémoire virtuelle Azure.

    ![Qualification de charge de travail](./media/site-recovery-capacity-planner/workload-qualification.png)

4.  Si vous cliquez sur **Calculer des machines virtuelles IaaS** ici est qu’il est :

    - Valide les entrées obligatoires.
    - Calcule e/s et suggère la machine virtuelle Azure meilleures aize correspondance pour chaque machines virtuelles qui est autorisé à acheter la réplication Azure. Si une taille appropriée de machine virtuelle Azure ne peut pas être détectée qu'une erreur est générée. Par exemple, si le nombre de disques 65 joint dans une erreur est problèmes depuis la taille de la plus élevée machine virtuelle Azure est 64.
    - Propose un compte de stockage pouvant être utilisées pour une machine virtuelle Azure.
    - Calcule le nombre total de stockage standard comptes et premium stockage requis pour la charge de travail. Faites défiler vers le bas à droite pour afficher le type de stockage Azure et du compte de stockage qui peut être utilisé pour un serveur source
    - Valide et trie le reste de la table basé sur un type de stockage requis (standard ou premium) affecté pour une machine virtuelle et le nombre de disques associés. Pour tous les ordinateurs virtuels qui répondent à la configuration requise pour la sauvegarde Azure, colonne A (est machine virtuelle compatibles avec ?) affiche Oui. Si une machine virtuelle ne peut pas être sauvegardée sur Azure une erreur s’affiche.

Colonnes AA à CP sont sortie et fournissent des informations à chaque machine virtuelle.

![Qualification de charge de travail](./media/site-recovery-capacity-planner/workload-qualification-2.png)


### <a name="example"></a>Exemple
Par exemple, pour les six machines virtuelles avec les valeurs indiquées dans le tableau, l’outil calcule et affecte la meilleure solution machine virtuelle Azure et les besoins de stockage Azure.

![Qualification de charge de travail](./media/site-recovery-capacity-planner/workload-qualification-3.png)

- Dans le résultat de l’exemple, notez les points suivants :
    
    - La première colonne contient une colonne de validation pour les ordinateurs virtuels, disques et l’évolution du.
    - Deux comptes de stockage standard et compte de stockage qu’une seule prime sont nécessaires pour les cinq machines virtuelles. 
    -  VM3 ne bénéficier de protection, car un ou plusieurs disques sont plus de 1 To.
    -  VM1 et ordinateur virtuel 2 peuvent utiliser le premier compte de stockage standard
    -  VM4 pouvez utiliser le deuxième compte de stockage standard.
    -  VM5 et VM6 besoin d’un compte de stockage premium et pouvez utilisent un seul compte.

    >[AZURE.NOTE]  Sorties par sur le stockage standard et premium sont calculées au niveau de la machine virtuelle et non au niveau de disque. Une machine virtuelle standard peut gérer jusqu'à 500 disque par seconde. Si sorties par pour un disque sont supérieures à 500, vous devez stockage premium. Cependant si sorties par pour un disque de plus de 500 mais sorties par pour les disques machine virtuelle total sont dans les limites de mémoire virtuelle Azure standards prise en charge (taille de mémoire virtuelle, nombre de disques, nombre de mémoire cartes, processeur,) puis le planificateur choisit une norme série machine virtuelle et non le service d’annuaire ou GS. Vous devez mettre à jour manuellement la cellule taille Azure mappage avec la série DS ou GS appropriées machine virtuelle.

5. Une fois tous les détails sont en place, cliquez sur **Envoyer les données à l’outil Planificateur** pour ouvrir le **Planificateur de capacité** charges de travail sont mis en surbrillance pour indiquer si elles sont éligibles pour la protection ou non.


### <a name="submit-data-in-the-capacity-planner"></a>Envoyer des données dans le Planificateur de capacité

1.  Lorsque vous ouvrez la feuille de calcul **Capacité planificateur** elle est remplie en fonction des paramètres que vous avez spécifiés. Le mot « La charge de travail » apparaît dans la cellule **source d’entrées Infra** pour indiquer que la feuille de calcul **Qualification la charge de travail** d’entrée. 
2.  Si vous souhaitez apporter des modifications, que vous devez modifier la feuille de calcul **Qualification la charge de travail** , cliquez sur Envoyer les données à l’outil planificateur à nouveau.  

    ![Planificateur de capacité](./media/site-recovery-capacity-planner/capacity-planner.png)


