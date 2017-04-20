<properties
    pageTitle="Exécutez l’outil de planificateur de capacité Hyper-V récupération de Site | Microsoft Azure"
    description="Cet article contient des instructions sur l’utilisation de l’outil de planificateur de capacité Hyper-V Azure récupération de Site"
    services="site-recovery"
    documentationCenter="na"
    authors="rayne-wiselman"
    manager="jwhit"
    editor="" />
<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="07/12/2016"
    ms.author="raynew" />

# <a name="run-the-hyper-v-capacity-planner-tool-for-site-recovery"></a>Exécutez l’outil de planificateur de capacité Hyper-V récupération de Site

Dans le cadre de votre déploiement de récupération de Site Azure, vous devez connaître votre réplication et la bande passante. L’outil de planificateur de capacité Hyper-V récupération de Site vous aide à identifier à vos besoins en matière de réplication et la bande passante de la réplication de machine virtuelle Hyper-V.


Cet article décrit comment faire pour exécuter l’outil de planificateur de capacité Hyper-V. Cet outil doit être utilisé avec les autres outils de planification et les informations décrites dans la [planification de la capacité de récupération de Site](site-recovery-capacity-planner.md).


## <a name="before-you-start"></a>Avant de commencer

Vous exécutez l’outil sur un nœud de serveur ou un cluster Hyper-V sur votre site principal. Pour exécuter l’outil les serveurs hôtes Hyper-V doit :

- Système d’exploitation : Windows Server® 2012 ou Windows Server® 2012 R2
- Mémoire : 20 Mo (minimum)
- UC : surcharge 5 pour cent de le (minimum)
- Espace disque : 5 Mo (minimum)

Avant d’exécuter l’outil vous devez préparer le site principal. Si vous êtes la réplication entre deux locaux sites et que vous souhaitez vérifier la bande passante, vous devez préparer un serveur réplica également.


## <a name="step-1-prepare-the-primary-site"></a>Étape 1 : Préparer le site principal
1. Dressez la liste de tous les ordinateurs virtuels Hyper-V que vous souhaitez répliquer et les hôtes/groupes Hyper-V sur lequel ils se trouvent sur le site principal. L’outil peut exécuter chaque fois pour plusieurs hôtes autonomes, ou pour un seul cluster mais pas les deux ensemble. Il doit également exécuter séparément pour chaque système d’exploitation, il est conseillé collecter et Notez vos serveurs Hyper-V comme suit :

  - Serveurs autonomes Windows Server® 2012
  - Windows Server® 2012 clusters
  - Serveurs autonomes Windows Server® 2012 R2
  - Windows Server® 2012 R2 clusters

3. Activer l’accès à distance à WMI sur tous les hôtes Hyper-V et clusters. Exécutez la commande sur chaque cluster/serveur pour vérifier que règles de pare-feu et les autorisations des utilisateurs sont définies :

        netsh firewall set service RemoteAdmin enable

5. Activer l’analyse des performances sur les serveurs et clusters, comme suit :

  - Ouvrez le pare-feu Windows avec le composant logiciel enfichable **Fonctions avancées de sécurité** et activer les règles de trafic entrant suivantes : **Accès réseau COM + (DCOM-IN)** et toutes les règles dans le **groupe d’administration de journal des événements à distance**.

## <a name="step-2-prepare-a-replica-server-on-premises-to-on-premises-replication"></a>Étape 2 : Préparer un serveur réplica (en local pour la réplication en local)

Vous n’avez pas besoin de le faire si vous êtes réplication vers Azure.

Nous vous recommandons de que configurer un seul hôte Hyper-V comme un serveur de récupération afin qu’une machine virtuelle factice peut être répliquée vers afin de vérifier la bande passante.  Vous pouvez ignorer cette étape, mais vous ne pourrez mesurer la bande passante sauf si vous le faites.

1. Si vous souhaitez utiliser un nœud de cluster comme le réplica configurer intermédiaire réplica Hyper-V :

    - Dans le **Gestionnaire de serveur**, ouvrez le **Gestionnaire de Cluster de basculement**.
    - Connectez-vous au cluster, mettez en surbrillance le nom du cluster, puis cliquez sur **Actions** > **Rôle configurer** pour ouvrir l’Assistant haute disponibilité.
    - **Sélectionnez** le rôle, cliquez sur **Hyper-V réplica intermédiaire**. Dans l’Assistant fournir un **nom NetBIOS** et **l’adresse IP** à utiliser comme point de connexion pour le cluster (appelé un point d’accès client). Le **Hyper-V réplica intermédiaire** sera configuré, ce qui entraîne un nom de point d’accès client que vous devez noter.
    - Vérifiez que le rôle Hyper-V réplica intermédiaire est fourni en ligne et pouvez basculer entre tous les nœuds du cluster. Pour ce faire, cliquez avec le bouton droit sur le rôle, pointez sur **déplacer**, puis cliquez sur **Sélectionner un nœud**. Sélectionnez un nœud > **OK**.
    - Si vous utilisez l’authentification basée sur le certificat, vérifiez que chaque nœud de cluster et le client accès au point de tout le certificat sont installé.
2.  Activer un serveur réplica :

    - Pour un cluster ouvrir Gestionnaire Cluster des échecs, connectez-vous au cluster et cliquez sur **les rôles** > rôle select > **Réplication paramètre**s > **Activer ce cluster comme un serveur réplica**. Notez que si vous utilisez un cluster comme réplica vous doivent être le rôle Hyper-V réplica intermédiaire présent sur le cluster ainsi que le site principal.
    - Pour un serveur autonome Ouvrez Gestionnaire Hyper-V. Dans le volet **Actions** , cliquez sur **Paramètres de Hyper-V** pour le serveur que vous souhaitez activer et configurer la **Réplication** , cliquez sur **Activer cet ordinateur en tant que copie serveur**.
3. Configurer l’authentification :

    - Dans les **ports d’authentification et** sélectionnez la méthode d’authentification serveur principal et les ports d’authentification. Si vous utilisez un certificat cliquez sur **Sélectionner un certificat** pour en sélectionner un. Utiliser Kerberos si les hôtes Hyper-V principaux et de restauration se trouvent dans le même domaine ou des domaines approuvés. Utiliser des certificats pour différents domaines ou un déploiement de groupe de travail.
    - Dans la section **autorisation et stockage** , autoriser **n’importe quel** serveur authentifié (principale) envoyer des données de réplication pour ce serveur réplica. Cliquez sur **OK** ou **Appliquer**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

    - Exécutez **netsh http afficher servicestate** pour vérifier que le récepteur est en cours d’exécution pour le protocole/port spécifié :  
4. Configurer les pare-feu. Pendant l’installation de Hyper-V les règles de pare-feu sont créés pour autoriser le trafic sur la valeur par défaut ports (HTTPS sur 443, Kerberos sur 80). Activer ces règles comme suit :

        - Certificate authentication on cluster (443): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}**
        - Kerberos authentication on cluster (80): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}**
        - Certificate authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"**
        - Kerberos authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"**

## <a name="step-3-run-the-capacity-planner-tool"></a>Étape 3 : Exécuter l’outil de planificateur de capacité

Une fois que vous avez préparé votre site principal et configurer un serveur de récupération, vous pouvez exécuter l’outil.

1. [Téléchargez](https://www.microsoft.com/download/details.aspx?id=39057) l’outil à partir du Microsoft Download Center.
2. Exécutez l’outil parmi les serveurs principales (ou des nœuds du cluster primaire). Cliquez sur le fichier .exe et puis cliquez sur **Exécuter en tant qu’administrateur**.
3. Dans **avant de commencer** , spécifiez la durée pendant laquelle vous souhaitez collecter les données. Nous vous recommandons de que vous exécutez l’outil pendant les heures de production pour vous assurer que les données sont représentant. Si vous essayez d’uniquement valider la connectivité réseau, vous pouvez collecter pour une minute uniquement.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

4. Dans **les détails du site primaire** spécifier le nom du serveur ou le nom de domaine complet pour un hôte autonome ou pour un cluster spécifiez le nom de domaine complet du client accepter point, nom de cluster ou n’importe quel nœud dans le cluster, puis cliquez sur **suivant**. L’outil détecte automatiquement le nom du serveur sur qu'il s’exécute. L’outil sélectionne machines virtuelles pouvant être analysés pour les serveurs spécifiés.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)

5. Dans **Les détails du Site réplica** si vous êtes réplication vers Azure ou si vous êtes réplication vers un centre de données secondaire et que vous n’avez pas configuré un serveur réplica, sélectionnez **Ignorer les tests de site réplica**. Si vous effectuez la réplication sur un centre de données secondaire et que vous avez configuré un type de copie dans le nom de domaine complet du serveur autonome ou le point d’accès client pour le cluster dans **le nom de serveur (ou) Hyper-V réplica intermédiaire Lettrine**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

6. **Étendue réplica** détails activez **Ignorer les tests de site réplica étendu**. Ils ne sont pas pris en charge par la récupération de Site.
7. Dans **Sélectionnez machines virtuelles à répliquer** les outils se connecte au serveur ou cluster et indique machines virtuelles et des disques en cours d’exécution sur le serveur principal, selon les paramètres vous avez spécifié dans la page **Détails du Site principal** . Notez que les ordinateurs virtuels qui sont déjà activés pour la réplication ou qui ne sont pas en cours d’exécution n’est pas affiché. Sélectionnez les ordinateurs virtuels pour lequel vous souhaitez collecter des indicateurs. Sélection automatiquement les disques durs virtuels collecte également des données pour les ordinateurs virtuels.
9. Si vous avez configuré un serveur réplica ou un cluster, informations **réseau** spécifier la bande passante WAN approximative vous pensez sera utilisé entre les sites principal et réplica et sélectionnez les certificats que si vous avez configuré l’authentification par certificat.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

10. En **Résumé** vérifier les paramètres, puis cliquez sur **suivant** pour commencer la collecte des indicateurs. État et la progression de l’outil s’affiche dans la page de **Calculer la capacité** . Lorsque le terminé outil en cours d’exécution cliquez sur **Afficher le rapport** pour passer en revue la sortie. Par défaut des rapports et des journaux sont stockés dans **%systemdrive%\Users\Public\Documents\Capacity planificateur**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)


## <a name="step-4-interpret-the-results"></a>Étape 4 : Interpréter les résultats
Voici les mesures importantes. Vous pouvez ignorer des indicateurs qui ne sont pas répertoriés ici. Elles ne sont pas pertinents pour la récupération de Site.

### <a name="on-premises-to-on-premises-replication"></a>Local à la réplication en local
  - Impact sur le calcul de l’hôte principal, de mémoire
  - Impact de la réplication sur le système principal, espace disque du stockage des hôtes récupération, e/s
  - Bande passante totale requise pour la réplication delta (Mbps)
  - Bande passante réseau observée entre l’hôte principal et l’hôte de restauration (Mbps)
  - Suggestions pour le nombre idéal de transferts parallèles actives entre les deux hôtes/groupes

### <a name="on-premises-to-azure-replication"></a>Local réplication Azure
  - Impact sur le calcul de l’hôte principal, de mémoire
  - Impact de la réplication de l’hôte principal stockage espace disque libre, sorties par
  - Bande passante totale requise pour la réplication delta (Mbps)

## <a name="more-resources"></a>Ressources complémentaires

- Pour plus d’informations sur l’outil de lire le document qui accompagne le téléchargement de l’outil.
- Regardez une procédure pas à pas de l’outil de Keith Mayer [blog TechNet](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx).
- [Obtenir les résultats](site-recovery-performance-and-scaling-testing-on-premises-to-on-premises.md) de nos performances test pour local à la réplication de Hyper-V en local



## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez terminé de planification de la capacité, vous pouvez démarrer déploiement de récupération de Site :

- [Répliquer les machines virtuelles Hyper-V dans nuages VMM vers Azure](site-recovery-vmm-to-azure.md)
- [Répliquer les machines virtuelles Hyper-V (sans VMM) vers Azure](site-recovery-hyper-v-site-to-azure.md)
- [Répliquer les machines virtuelles Hyper-V entre sites VMM](site-recovery-vmm-to-vmm.md)
- [Répliquer les machines virtuelles Hyper-V entre sites VMM avec SAN](site-recovery-vmm-san.md)
- [Répliquer hyper-V machines virtuelles sur serveur VMM unique](site-recovery-single-vmm.md)