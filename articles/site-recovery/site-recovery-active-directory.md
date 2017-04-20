<properties
    pageTitle="Protéger Active Directory et DNS avec récupération de Site Azure | Microsoft Azure"
    description="Cet article décrit comment mettre en œuvre une solution de récupération d’urgence pour Active Directory à l’aide de la récupération de Site Azure."
    services="site-recovery"
    documentationCenter=""
    authors="prateek9us"
    manager="abhiag"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="08/31/2016"
    ms.author="pratshar"/>

# <a name="protect-active-directory-and-dns-with-azure-site-recovery"></a>Protéger Active Directory et DNS avec récupération de Site Azure

Applications d’entreprise tels que SharePoint et SAP Dynamics AX dépendent de Active Directory et une infrastructure DNS fonctionnent correctement. Lorsque vous créez une solution de récupération d’urgence pour les applications, il est important n’oubliez pas que vous devez protéger et récupérer Active Directory et DNS avant les autres composants d’application, pour vous assurer que les éléments fonctionnent correctement lorsque sinistre.

Récupération de site est un service Azure fournissant sinistre par organiser réplication, basculement et la restauration des machines virtuelles. Récupération de site prend en charge un nombre de scénarios de réplication de manière cohérente à travers protéger et en toute transparence machines virtuelles basculement des applications et aux nuages hébergeur, public ou privé.

Utilisation de la récupération de Site, vous pouvez créer un plan de récupération automatique complète pour Active Directory. En cas d’interruption, vous pouvez lancer un basculement quelques secondes où que vous soyez et Active Directory devenir opérationnel en quelques minutes. Si vous avez déployé Active Directory pour plusieurs applications telles que SharePoint et SAP sur votre site principal, et que vous voulez basculer vers le site terminé, vous pouvez basculer vers Active Directory à l’aide de récupération de Site et puis basculer sur les autres applications à l’aide de plans de récupération spécifiques à l’application.

Cet article explique comment créer une solution de récupération d’urgence pour Active Directory, comment effectuer planifiée, et non planifiée et basculement de test à l’aide d’un plan de récupération d’un seul clic, les configurations prises en charge et les composants requis.  Vous devez être familiarisé avec Active Directory et de récupération de Site Azure avant de commencer.

Il existe deux options recommandées en fonction de la complexité de votre environnement.

### <a name="option-1"></a>Option 1

Si vous avez un petit nombre d’applications et un contrôleur de domaine et que vous voulez basculer sur l’ensemble du site, puis nous recommandons d’utiliser récupération de Site pour répliquer le contrôleur de domaine sur le site secondaire (si vous êtes basculement vers Azure ou sur un site secondaire). La même machine virtuelle répliquée peut servir pour le basculement de test trop.

### <a name="option-2"></a>Option 2

Si vous avez un grand nombre d’applications et il existe plus d’un contrôleur de domaine dans l’environnement, ou si vous souhaitez basculer sur quelques applications à la fois, nous vous recommandons en plus de la réplication de l’ordinateur virtuel contrôleur de domaine avec récupération de Site, vous devez également configurer un contrôleur de domaine supplémentaires sur le site cible (Azure ou un centre de données secondaire en local).

>[AZURE.NOTE] Même si vous implémentez Option-2, permettant d’effectuer un basculement de test que vous devez toujours répliquer le contrôleur de domaine à l’aide de la récupération de Site. Lire les [Considérations relatives à basculement de test](#considerations-for-test-failover) pour plus d’informations.


Les sections suivantes expliquent comment activer la protection d’un contrôleur de domaine de récupération de Site et comment configurer un contrôleur de domaine dans Azure.


## <a name="prerequisites"></a>Conditions préalables

- Un déploiement local de serveur Active Directory et DNS.
- Un archivage sécurisé de Services de récupération de Site Azure dans un abonnement Microsoft Azure.
- Si vous êtes réplication vers Azure exécuter l’outil d’évaluation de la préparation Azure Machine virtuelle sur machines virtuelles pour vous assurer qu’elles sont compatibles avec Azure machines virtuelles et des Services de récupération de Site Azure.


## <a name="enable-protection-using-site-recovery"></a>Activer la protection à l’aide de la récupération de Site


### <a name="protect-the-virtual-machine"></a>Protéger la machine virtuelle

Activer la protection de la machine domaine contrôleur/DNS virtuelle dans récupération de Site. Configurer les paramètres de récupération de Site en fonction du type de machine virtuelle (Hyper-V ou VMware). Nous vous recommandons une fréquence de réplication cohérente de blocage de 15 minutes.

###<a name="configure-virtual-machine-network-settings"></a>Configurer les paramètres réseau machine virtuelle

Pour la machine virtuelle DNS/contrôleur de domaine, configurer les paramètres réseau dans récupération de Site afin que la machine virtuelle est joints au réseau droite après le basculement. Par exemple, si vous êtes réplication machines virtuelles Hyper-V vers Azure vous pouvez sélectionner la machine virtuelle dans le nuage VMM ou dans le groupe de protection pour configurer les paramètres réseau comme indiqué ci-dessous

![Paramètres du réseau machine virtuelle](./media/site-recovery-active-directory/VM-Network-Settings.png)

## <a name="protect-active-directory-with-active-directory-replication"></a>Protéger Active Directory avec réplication Active Directory

### <a name="site-to-site-protection"></a>Protection de site à

Créer un contrôleur de domaine sur le site secondaire et spécifiez le nom du domaine même qui est utilisé sur le site principal lorsque vous convertissez le serveur un contrôleur de domaine. Vous pouvez utiliser le composant logiciel enfichable **Services et Sites Active Directory** pour configurer les paramètres sur l’objet de lien de site auquel sont ajoutés les sites. En configurant des paramètres sur un lien de site, vous pouvez contrôler quand la réplication intervient entre deux ou plusieurs sites et à quelle fréquence. Pour plus d’informations, voir [Planification de la réplication entre Sites](https://technet.microsoft.com/library/cc731862.aspx) .

###<a name="site-to-azure-protection"></a>Protection de site pour Azure

Suivez les instructions pour [créer un contrôleur de domaine dans un réseau virtuel Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Lorsque vous convertissez le serveur un contrôleur de domaine, spécifiez le même nom de domaine utilisé sur le site principal.

Puis [reconfigurer le serveur DNS pour le réseau virtuel](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network), pour utiliser le serveur DNS dans Azure.

![Réseau Azure](./media/site-recovery-active-directory/azure-network.png)

## <a name="test-failover-considerations"></a>Considérations relatives à basculement de test

Test basculement dans un réseau a isolé à partir du réseau de production pour qu’il n’existe aucun impact sur les charges de travail.

La plupart des applications requiert également la présence d’un contrôleur de domaine et un serveur DNS fonctionne, donc avant échec de l’application sur un contrôleur de domaine doit être créé dans le réseau isolé à utiliser pour le basculement de test. Le plus simple consiste à activer la protection sur l’ordinateur virtuel contrôleur/DNS du domaine avec récupération de Site et exécuter un basculement de test de cet ordinateur virtuel, avant d’exécuter un basculement de test du plan de récupération de l’application. Voici comment procéder :

1. Activer la protection de récupération de Site pour l’ordinateur virtuel contrôleur/DNS du domaine.
2. Créer un réseau isolé. Un réseau virtuel est créé par défaut dans Azure est isolé à partir d’autres réseaux. Nous vous recommandons que la plage d’adresses IP pour ce réseau est identique à celle de votre réseau de production. Ne pas activer la connectivité de site à sur ce réseau.
3. Fournir une adresse IP DNS dans le réseau créé, à l’adresse IP que vous attendez la machine virtuelle DNS pour obtenir. Si vous êtes réplication vers Azure, puis indiquez l’adresse IP de la machine virtuelle qui sera utilisée sur basculement paramètre **IP cible** dans les propriétés de la machine virtuelle. Si vous êtes réplication vers un autre en local site et que vous utilisez suivre DHCP les instructions pour [configurer DNS et DHCP pour le basculement de test](site-recovery-failover.md#prepare-dhcp)

>[AZURE.NOTE] L’adresse IP affectée à une machine virtuelle pendant une défaillance de test est identique à l’adresse IP qu'il obtiendrez lors d’un basculement planifié ou, si l’adresse IP est disponible dans le réseau de basculement test. Si elle n’est pas, la machine virtuelle reçoit une adresse IP différente est disponible dans le réseau de basculement test.

4. Sur l’ordinateur virtuel contrôleur de domaine s’exécutent un basculement test de celui-ci dans le réseau isolé. Utiliser dernier point de récupération cohérent application disponible de l’ordinateur virtuel contrôleur de domaine pour effectuer le basculement de test. 
5. Exécuter un basculement de test pour le plan de récupération d’application.
6. Une fois que le test est terminé, marquez la tâche basculement d’essai d’ordinateur virtuel contrôleur de domaine et du plan de récupération « Terminé » sous l’onglet **tâches** dans le portail de récupération de Site.

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS et contrôleur de domaine sur des ordinateurs différents

Si DNS n’est pas sur la même machine virtuelle comme contrôleur de domaine, vous devez créer une VM DNS pour le basculement de test. Si elles se trouvent sur la même machine virtuelle, vous pouvez ignorer cette section.

Vous pouvez utiliser un serveur DNS novateur et créer toutes les zones obligatoires. Par exemple, si votre domaine Active Directory est contoso.com, vous pouvez créer une zone DNS avec le nom contoso.com. Les entrées correspondant à Active Directory doivent être mis à jour dans le système DNS, comme suit :

1. Vérifiez que ces paramètres sont en place avant toute autre machine virtuelle dans le plan de récupération s’affiche :

    - La zone doit être nommée après le nom de la racine de forêt.
    - La zone doit être sauvegardé du fichier.
    - La zone doit être activée les mises à jour sécurisés et non sécurisés.
    - L’utilitaire de résolution de l’ordinateur virtuel contrôleur de domaine doit pointer vers l’adresse IP de la machine virtuelle DNS.

2. Sur l’ordinateur virtuel contrôleur de domaine, exécutez la commande suivante :

    `nltest /dsregdns`

3. Ajouter une zone sur le serveur DNS, autoriser les mises à jour non sécurisé et ajouter une entrée de celui-ci dans DNS :

        dnscmd /zoneadd contoso.com  /Primary
        dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1
        dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>
        dnscmd /config contoso.com /allowupdate 1


## <a name="next-steps"></a>Étapes suivantes

Lecture [les charges de travail puis-je protéger ?](../site-recovery/site-recovery-workload.md) pour en savoir plus sur la protection de charges de travail entreprise avec récupération de Site Azure.
