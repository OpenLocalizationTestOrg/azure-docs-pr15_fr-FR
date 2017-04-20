<properties
    pageTitle="Basculement dans récupération de Site | Microsoft Azure" 
    description="Récupération de Site Azure coordonnées de la réplication, le basculement et la restauration des machines virtuelles et des serveurs physiques. Apprenez à basculement vers Azure ou un centre de données secondaire." 
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
    ms.date="10/05/2016" 
    ms.author="raynew"/>

# <a name="failover-in-site-recovery"></a>Basculement dans récupération de Site

Le service de récupération de Site Azure contribue à votre stratégie d’entreprise continuité et de reprise récupération (BCDR) en organiser réplication, basculement et la restauration des machines virtuelles et des serveurs physiques. Machines peuvent être répliquées vers Azure, ou à un centre de données secondaire en local. Pour un bref aperçu lisez [Nouveautés récupération de Site Azure ?](site-recovery-overview.md)

## <a name="overview"></a>Vue d’ensemble

Cet article fournit des informations et des instructions pour la récupération (ignore plus et défaillants précédent) machines virtuelles et des serveurs physiques qui sont protégés avec récupération de Site. 

Publier des commentaires ou des questions en bas de cet article, ou sur le [Forum de Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="types-of-failover"></a>Types de basculement

Une fois que la protection est activée pour machines virtuelles et des serveurs physiques et qu’ils vous réplication vous pouvez exécuter basculement en cas de besoin. Récupération de site prend en charge un certain nombre de types de basculement.

**Basculement** | **Moment de l’exécution** | **Plus d’informations** | **Processus**
---|---|---|---
**Basculement de test** | Exécuter pour valider votre stratégie de réplication ou effectuer une exploration de récupération d’urgence | Aucune perte de données ou le temps d’arrêt.<br/><br/>Aucun impact sur la réplication<br/><br/>Aucun impact sur votre environnement de production | Démarrer le basculement<br/><br/>Spécifier comment machines de test seront connectés aux réseaux après le basculement<br/><br/>Suivre la progression de l’onglet **tâches** . Ordinateurs de test sont créées et commencer à l’emplacement secondaire<br/><br/>Azure - se connecter à l’ordinateur dans le portail Azure<br/><br/>Secondaire site - accéder à l’ordinateur sur le même hôte et cloud<br/><br/>Test terminé et nettoyer automatiquement les paramètres de basculement test.
**Basculement planifié** | Exécuter pour répondre aux exigences de conformité<br/><br/>Exécuter pour la maintenance planifiée<br/><br/>Exécuter pour basculer des données à conserver les charges de travail en cours d’exécution des coupures connus - par exemple, une coupure attendu ou météorologiques difficiles<br/><br/>Exécuter à basculement après le basculement à partir de principal vers secondaire | Aucune perte de données<br/><br/>Temps d’arrêt est prévu pendant la durée que nécessaire pour arrêter la machine virtuelle sur le serveur principal et faire apparaître sur le site secondaire.<br/><br/>Machines virtuelles est l’impact qu’ordinateurs cibles devient machines source après le basculement. | Démarrer le basculement<br/><br/>Suivre la progression de l’onglet **tâches** . Machines d’origine ne sont pas arrêtés<br/><br/>Réplica ordinateurs démarrent à l’emplacement secondaire<br/><br/>Azure - se connecter à l’ordinateur réplica dans le portail Azure<br/><br/>Secondaire site - accéder à l’ordinateur sur le même hôte et dans le cloud même<br/><br/>Valider le basculement
**Basculement non planifié** | Exécuter ce type de basculement de façon réactive lorsqu’un site principal est inaccessible en raison d’un incident inattendu, par exemple une attaque de virus ou une panne power <br/><br/> Vous pouvez exécuter un non planifiée basculement peut être exécuté même si le site principal n’est pas disponible. | Perte de données dépendant de paramètres de fréquence de réplication<br/><br/>Données sont mises à jour conformément à la dernière synchronisation | Démarrer le basculement<br/><br/>Suivre la progression de l’onglet **tâches** . Vous pouvez également essayer arrêter de machines virtuelles et synchroniser des données les plus récentes<br/><br/>Réplica ordinateurs démarrent à l’emplacement secondaire<br/><br/>Azure - se connecter à l’ordinateur réplica dans le portail Azure<br/><br/>L’ordinateur sur le même hôte et dans le cloud même l’accès au site secondaire<br/><br/>Valider le basculement


Les types de basculement prises en charge dépendent de votre scénario de déploiement.

**Basculement direction** | **Basculement de test** | **Basculement planifié** | **Basculement non planifié**
---|---|---|---
Site VMM principal au site VMM secondaire | Prise en charge | Prise en charge | Prise en charge 
Site VMM secondaire au site VMM principal | Prise en charge | Prise en charge | Prise en charge
Nuage de Cloud (VMM serveur unique) |  Prise en charge | Prise en charge | Prise en charge
Site VMM vers Azure | Prise en charge | Prise en charge | Prise en charge 
Azure au site VMM | Non pris en charge | Prise en charge | Non pris en charge 
Site Hyper-V vers Azure | Prise en charge | Prise en charge | Prise en charge
Azure au site Hyper-V | Non pris en charge | Prise en charge | Non pris en charge
Site VMware vers Azure | Prise en charge (scénario amélioré)<br/><br/> Non pris en charge (scénario hérité) |Non pris en charge | Prise en charge
Serveur physique d’Azure | Prise en charge (scénario amélioré)<br/><br/> Non pris en charge (scénario hérité) | Non pris en charge | Prise en charge

## <a name="failover-and-failback"></a>Basculement et restauration

Vous ne parvenez pas machines virtuelles vers un site secondaire en local ou sur Azure, selon votre déploiement. Un ordinateur qui bascule vers Azure est créé comme une machine virtuelle Azure. Vous pouvez basculer vers une seule machine virtuelle ou serveur physique ou un plan de récupération. Plans de récupération se compose d’un ou plusieurs classés groupes qui contiennent des machines virtuelles protégées serveurs physiques. Elles sont utilisées pour organiser le basculement de plusieurs machines qui basculent en fonction du groupe ils se trouvent. [Pour en savoir plus](site-recovery-create-recovery-plans.md) sur les offres de récupération. 

Après l’exécution de basculement et vos ordinateurs sont en cours d’exécution dans un emplacement secondaire Notez que :

- Si vous n’avez pas sur Azure, une fois que les ordinateurs de basculement ne sont pas protégée ou la réplication à l’emplacement principal ou secondaire. Dans Azure machines virtuelles sont stockées dans le stockage répliquée geo qui fournit la résilience, mais pas la réplication.
- Si vous avez effectué un basculement non planifié pour un site secondaire, une fois que les ordinateurs de basculement à l’emplacement secondaire ne sont pas protégées ou la réplication.
- Si vous avez effectué un basculement planifié vers un site secondaire, une fois que les ordinateurs de basculement à l’emplacement secondaire sont protégés.
 

### <a name="failback-from-secondary-site"></a>Retour arrière depuis le site secondaire

Restauration d’un site secondaire à un serveur principal utilise la même procédure sous forme de basculement du site principal vers le site secondaire. Une fois que le basculement de principal au centre de données secondaire est validée et terminée, vous pouvez lancer la réplication inverse quand votre site principal devient disponible. Inverse réplication lance la réplication entre le site secondaire et principal en synchronisant les données delta. Réplication inverse intègre les machines virtuelles dans un état protégé, mais le centre de données secondaire est toujours l’emplacement actif. Afin de pouvoir modifier le site principal dans l’emplacement actif vous lancez un basculement planifié à partir de secondaire à principale, suivi d’une autre réplication inverse.

### <a name="failback-from-azure"></a>Retour arrière à partir d’Azure

Si vous avez basculé vers Azure vos machines virtuelles sont protégés par les fonctionnalités de résilience Azure pour les machines virtuelles. Pour rendre le site principal d’origine à l’emplacement actif vous exécutez un basculement planifié. Si votre site d’origine n’est pas disponible, vous pouvez échouer vers l’emplacement d’origine ou vers un autre emplacement. Pour commencer la réplication après retour arrière vers l’emplacement principal vous lancer une réplication inverse.

### <a name="failover-considerations"></a>Considérations relatives à basculement

- **Adresse IP après basculement**— par défaut un échec sur ordinateur aura une adresse IP différente de l’ordinateur source. Si vous souhaitez conserver la même, voir adresses IP : 
    - **Site secondaire**— si vous êtes basculement vers un site secondaire et que vous voulez conserver une adresse IP [lire](http://blogs.technet.com/b/scvmm/archive/2014/04/04/retaining-ip-address-after-failover-using-hyper-v-recovery-manager.aspx) cet article. Notez que vous pouvez conserver une adresse IP publique si votre fournisseur de services Internet prend en charge.
    - **Azure**— si vous êtes basculement vers Azure vous pouvez spécifier l’adresse IP que vous voulez attribuer dans l’onglet **configurer** les propriétés de la machine virtuelle. Vous ne pouvez pas conserver une adresse IP publique après basculement vers Azure. Vous pouvez conserver les espaces d’adressage non - RFC 1918 utilisés comme adresses internes.
- **Basculement partielle**— si vous souhaitez basculer partie d’un site au lieu d’un site entier Notez que : 
    - **Site secondaire**— si vous ne parvenez pas sur une partie d’un site principal vers un site secondaire et que vous voulez vous connecter au site principal, utilisez un site à VPN pour vous connecter Échec sur applications sur le site secondaire aux composants d’infrastructure s’exécutant sur le site principal. Si un sous-réseau entier bascule vous permet de conserver l’adresse IP machine virtuelle. Si vous ne parvenez pas sur un sous-réseau partiel vous ne peut pas conserver l’adresse IP machine virtuelle car sous-réseaux ne peut pas être divisées entre les sites.
    - **Azure**— si vous basculer sur un site partiel vers Azure et que vous voulez vous connecter au site principal, vous pouvez utiliser un réseau privé virtuel de site à se pour connecter à un échec sur application dans Azure aux composants d’infrastructure s’exécutant sur le site principal. Notez que si la basculé sous-réseau entier vous permet de conserver l’adresse IP machine virtuelle. Si vous ne parvenez pas sur un sous-réseau partiel vous ne peut pas conserver l’adresse IP machine virtuelle car sous-réseaux ne peut pas être divisées entre les sites.
 
- **Lettre**— si vous souhaitez conserver la lettre sur des machines virtuelles après le basculement, vous pouvez définir la stratégie SAN pour la machine virtuelle sur **activé**. Machine virtuelle disques se mettent automatiquement en ligne. [Pour en savoir plus](https://technet.microsoft.com/library/gg252636.aspx).
- **Acheminer les demandes client**— récupération de Site fonctionne avec le Gestionnaire de trafic Azure pour acheminer les requêtes client à votre application après le basculement.




## <a name="run-a-test-failover"></a>Exécuter un basculement de test

Lorsque vous exécutez un basculement de test vous êtes invité à sélectionner des paramètres réseau pour ordinateurs de réplica de test. Vous avez un certain nombre d’options.  

**Option de basculement de test** | **Description** | **Basculement à cocher** | **Plus d’informations**
---|---|---|---
**Basculer vers Azure — sans réseau** | Ne sélectionnez pas une cible réseau Azure | Tests de basculement qui permettent de tester machine virtuelle démarre comme prévu dans Azure | Toutes les machines virtuelles test dans un plan de récupérer sont ajoutés dans un service cloud unique et peuvent se connecter à l’autre<br/><br/>Machines n’êtes pas connectés à un réseau Azure après le basculement.<br/><br/>Les utilisateurs peuvent se connecter à des ordinateurs de test avec une adresse IP publique
**Basculer vers Azure — avec réseau** | Sélectionnez une cible réseau Azure | Basculement vérifie que les ordinateurs de test sont connectés au réseau | Création d’un réseau Azure qui a isolé à partir de votre réseau de production Azure et configurer l’infrastructure de la machine virtuelle répliquée fonctionne comme prévu.<br/><br/>Le sous-réseau de la machine virtuelle test est basé sur sous-réseau sur lequel l’échec sur machine virtuelle est censée se connecter à un planifiée ou basculement.
**Basculer vers un site VMM secondaire — sans réseau** | Ne sélectionnez pas un réseau machine virtuelle | Basculement vérifie que les ordinateurs de test sont créés.<br/><br/>La machine virtuelle test sont créée sur le même hôte que l’hôte sur lequel se trouve la machine virtuelle réplica. Il n’est pas ajouté dans le cloud dans lequel se trouve la machine virtuelle réplica. | <p>L’échec sur un ordinateur n’est pas connecté à un réseau.<br/><br/>L’ordinateur peut être connecté à un réseau machine virtuelle après que qu’il a été créé
**Basculer vers un site VMM secondaire — avec réseau** | Sélectionnez un réseau existant de la machine virtuelle un | Basculement vérifie que les machines virtuelles sont créées | La machine virtuelle test sont créée sur le même hôte que l’hôte sur lequel se trouve la machine virtuelle réplica. Il n’est pas ajouté dans le cloud dans lequel se trouve la machine virtuelle réplica.<br/><br/>Créer un réseau de machine virtuelle a isolé à partir de votre réseau de production<br/><br/>Si vous utilisez un réseau basé sur un réseau local virtuel nous vous recommandons de que vous créez un réseau logique distinct (non utilisé en production) dans VMM à cet effet. Ce réseau logique est utilisé pour créer des réseaux machine virtuelle aux fins de test basculement.<br/><br/>Le réseau logique doit être associé au moins une des cartes réseau de tous les serveurs Hyper-V hébergeant machines virtuelles.<br/><br/>Pour les réseaux logiques réseau local virtuel, les sites de réseau que vous ajoutez au réseau logique doivent être isolés.<br/><br/>Si vous utilisez un réseau logique virtualisation du réseau Windows, récupération de Site Azure crée automatiquement des réseaux machine virtuelle isolés.
**Basculer vers un site VMM secondaire — créer un réseau** | Un réseau de test temporaire est créé automatiquement en fonction du paramètre que vous spécifiez dans **Réseau logique** et ses sites réseau associés | Basculement vérifie que les machines virtuelles sont créées | Utilisez cette option si le plan de récupération utilise plusieurs réseaux machine virtuelle. Si vous utilisez des réseaux virtualisation du réseau Windows, cette option peut créer automatiquement réseaux machine virtuelle avec les mêmes paramètres (sous-réseaux et pools d’adresses IP) dans le réseau de la machine virtuelle réplica. Ces réseaux machine virtuelle s’ajustent automatiquement une fois le basculement de test terminée.</p><p>La machine virtuelle test sont créée sur le même hôte que l’hôte sur lequel se trouve la machine virtuelle réplica. Il n’est pas ajouté dans le cloud dans lequel se trouve la machine virtuelle réplica.

>[AZURE.NOTE] L’adresse IP donné à une machine virtuelle pendant le basculement de test est identique à l’adresse IP qu'il reçoit quand effectuant un basculement planifié ou (en supposant que l’adresse IP est disponible dans le réseau de basculement test. Si la même adresse IP n’est pas disponible dans le réseau de basculement test machine virtuelle recevront une autre adresse IP disponible sur le réseau de basculement test.



### <a name="run-a-test-failover-from-on-premises-to-azure"></a>Exécuter un basculement test de local vers Azure

Cette procédure explique comment exécuter un basculement de test pour un plan de récupération. Vous pouvez également exécuter le basculement pour un seul ordinateur sous l’onglet **Machines virtuelles** .

1. Sélectionnez **Récupération Plans** > *recoveryplan_name*. Cliquez sur **Basculer** > **basculement de Test**.
2. Dans la page **Confirmer le basculement Test** , spécifiez comment machines réplica seront connectés à un réseau Azure après le basculement.
3. Si vous êtes basculement vers Azure et chiffrement des données est activé pour le cloud, dans la **Clé de chiffrement** , sélectionnez le certificat émis lorsque vous avez activé le chiffrement des données pendant l’installation du fournisseur. 
4. Suivre la progression de basculement sous l’onglet **tâches** . Vous devez être en mesure de voir l’ordinateur du réplica de test dans le portail Azure.
5. Vous pouvez accéder machines réplica dans Azure à partir de votre site local initier à une connexion RDP à la machine virtuelle. Vous devez être ouverts sur le point de terminaison de la machine virtuelle Port3389.
5. Une fois que vous avez terminé, lorsque le basculement atteint la phase de **test terminé** , cliquez sur **Test complet** pour terminer.
5. Dans **Notes** enregistrer et enregistrer des observations associées le basculement de test.
8. Cliquez sur **le basculement de test est achevé** pour nettoyer automatiquement l’environnement de test. Une fois cette étape terminée le basculement de test vous montrent l’état**effectuer** C.

> [AZURE.NOTE] Si un basculement test s’étend sur plus de deux semaines qu'allez aboutir en vigueur. Tous les éléments ou machines virtuelles créées automatiquement pendant le basculement de test seront supprimés.
  

### <a name="run-a-test-failover-from-a-primary-on-premises-site-to-a-secondary-on-premises-site"></a>Exécuter un basculement de test d’un site principal local vers un site secondaire en local

Vous devez faire un certain nombre d’éléments à exécuter un basculement de test, y compris créer une copie du contrôleur de domaine et placer des serveurs DHCP et DNS test dans votre environnement de test. Vous pouvez faire de deux façons :

- Si vous souhaitez exécuter un basculement de test à l’aide d’un réseau existant, préparer Active Directory, DHCP et DNS dans ce réseau.
- Si vous souhaitez exécuter un basculement de test à l’aide de l’option pour créer automatiquement des réseaux machine virtuelle, ajoutez une étape manuelle avant de groupe-1 dans le plan de récupération que vous allez utiliser pour le basculement de test, puis ajouter les ressources de l’infrastructure au réseau créé automatiquement avant d’exécuter le basculement de test.

#### <a name="things-to-note"></a>Éléments à noter

- Lors de la réplication vers un site secondaire, le type de réseau utilisé par l’ordinateur réplica ne doit correspondre au type de réseau logique utilisée pour le basculement de test, mais certaines combinaisons peuvent ne pas fonctionner. Si le réplica utilise DHCP et isolement basée sur le réseau local virtuel, le réseau machine virtuelle du réplica n’a pas besoin d’un pool d’adresses IP statique. À l’aide de la virtualisation réseau Windows pour le basculement de test ne fonctionnera pas, car aucune pools d’adresses ne sont disponibles. En outre test basculement ne fonctionne pas si le réseau réplica est aucun isolement et le réseau de test est virtualisation du réseau Windows. C’est parce que le réseau aucun isolement n’a pas les sous-réseaux nécessaires pour créer un réseau virtualisation du réseau Windows.
- La façon dans quel réplica machines virtuelles sont connectés à mappés réseaux machine virtuelle après que basculement dépend de la façon dont le réseau machine virtuelle est configuré dans la console :
    - **Réseau machine virtuelle configuré sans isolement ou isolement réseau local virtuel**— si DHCP est défini pour le réseau machine virtuelle, la machine virtuelle réplica sera connectée à l’ID de réseau local virtuel à l’aide de paramètres qui sont spécifiés pour le site de réseau dans le réseau logique associé. La machine virtuelle recevrez son adresse IP de celui-ci disponible. Vous n’avez pas besoin un pool d’adresses IP défini pour le réseau de machine virtuelle cible. Si un pool d’adresses IP statique est utilisé pour le réseau machine virtuelle la machine virtuelle réplica est connectée à l’ID de réseau local virtuel à l’aide de paramètres qui sont spécifiés pour le site de réseau dans le réseau logique associé. La machine virtuelle recevront son adresse IP à partir du pool défini pour le réseau machine virtuelle. Si un pool d’adresses IP n’est pas défini sur le réseau de machine virtuelle cible, allocation d’adresses IP échouera. Le pool d’adresses IP doit être créé sur des serveurs VMM source et cible que vous allez utiliser pour la protection et la restauration.
    - **Réseau machine virtuelle avec Windows réseau virtualisation**— si un réseau machine virtuelle est configuré avec ce paramètre un pool statique doit être défini pour le réseau de machine virtuelle cible, quel que soit si le réseau de machine virtuelle source est configuré pour utiliser DHCP ou une adresse IP statique pool d’adresses. Si vous définissez DHCP, le serveur VMM cible agir comme un serveur DHCP et fournir une adresse IP à partir du pool est défini pour le réseau de machine virtuelle cible. Si l’utilisation d’un pool d’adresses IP statique est définie pour le serveur source, le serveur VMM cible affecte une adresse IP à partir du pool. Dans les deux cas, allocation d’adresses IP échouera si un pool d’adresses IP n’est pas défini.

#### <a name="run-test"></a>Exécuter les tests

Cette procédure explique comment exécuter un basculement de test pour un plan de récupération. Vous pouvez également exécuter le basculement d’une seule machine virtuelle ou d’un serveur physique sous l’onglet **Machines virtuelles** .

1. Sélectionnez **Récupération Plans** > *recoveryplan_name*. Cliquez sur **Basculer** > **basculement de Test**.
2. Dans la page **Confirmer le basculement de Test** , spécifiez comment machines virtuelles doit être connectés à des réseaux après le basculement de test.
3. Suivre la progression de basculement sous l’onglet **tâches** . Lorsque le basculement atteint la phase de** test terminé** , cliquez sur **Tester terminé** pour terminer le basculement de test.
4. Cliquez sur **Notes** pour enregistrer et enregistrer des observations associées le basculement de test.
4. Une fois terminé, assurez-vous que les ordinateurs virtuels démarrer correctement.
5. Après avoir vérifié que machines virtuelles démarrer correctement, exécuter le basculement test pour nettoyer l’environnement isolé. Si vous avez choisi de créer automatiquement des réseaux machine virtuelle, nettoyage de disque supprime tous les ordinateurs virtuels de test et tester les réseaux.

> [AZURE.NOTE] Si un basculement test s’étend sur plus de deux semaines qu'allez aboutir en vigueur. Tous les éléments ou machines virtuelles créées automatiquement pendant le basculement de test seront supprimés.


#### <a name="prepare-dhcp"></a>Préparer DHCP

Si les machines virtuelles impliqués dans test basculement utiliser DHCP, un serveur DHCP test doit être créé au sein du réseau isolé est créé aux fins de test basculement.


### <a name="prepare-active-directory"></a>Préparation d’Active Directory
Pour exécuter un basculement de test pour tester les applications, vous devez une copie de l’environnement Active Directory de production dans votre environnement de test. Passez en revue la section [tester les considérations relatives à basculement pour active directory](site-recovery-active-directory.md#considerations-for-test-failover) pour plus d’informations. 


### <a name="prepare-dns"></a>Préparation de DNS

Préparation d’un serveur DNS pour le basculement de test comme suit :

- **DHCP**— si machines virtuelles utilisez DHCP, l’adresse IP de la tester le système DNS doit être mis à jour sur celui-ci test. Si vous utilisez un type de réseau de virtualisation du réseau Windows, le serveur VMM joue celui-ci. Par conséquent, l’adresse IP du serveur DNS doit être mis à jour dans le réseau de basculement test. Dans ce cas, les machines virtuelles sera s’inscrire au serveur DNS pertinents.
- **Adresse statique**— si machines virtuelles utiliser une adresse IP statique, l’adresse IP du serveur DNS test doit être mis à jour de réseau de basculement test. Vous devrez peut-être mettre à jour de DNS avec l’adresse IP des machines virtuelles test. Vous pouvez utiliser l’exemple de script suivantes à cet effet : 

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-planned-failover-primary-to-secondary"></a>Exécuter un basculement planifié (principal vers secondaire)

 Cette procédure explique comment exécuter un basculement planifié pour un plan de récupération. Vous pouvez également exécuter le basculement d’une seule machine virtuelle sous l’onglet **Machines virtuelles** .

1. Avant de commencer, vérifiez que tous les ordinateurs virtuels que vous souhaitez basculer sont terminées réplication initiale.
2. Sélectionnez **Récupération Plans** > *recoveryplan_name*. Cliquez sur **Basculer** > **planifiée basculement**. 
3. Dans la page **Confirmer le basculement planifiée **, choisissez les emplacements sources et cibles. Remarque la direction de basculement.

    - Si précédent basculement fonctionnent comme prévu et que tous les serveurs machine virtuelle sont situés dans emplacement source ou cible, les détails de la direction de basculement sont appliquent aux informations uniquement. 
    - Machines virtuelles s’actives dans des emplacements source et cible, le bouton **Modifier l’orientation** s’affiche. Utilisez ce bouton pour modifier et de spécifier la direction dans laquelle le basculement doit avoir lieu.

5. Si vous êtes basculement vers Azure et chiffrement des données est activé pour le cloud, dans la **Clé de chiffrement** , sélectionnez le certificat émis lorsque vous avez activé le chiffrement des données pendant l’installation de fournisseur sur le serveur VMM. 
6. Lorsqu’un basculement planifié commence la première étape consiste à arrêter les machines virtuelles pour éviter toute perte de données. Vous pouvez suivre la progression de basculement sous l’onglet **tâches** . Si une erreur se produit dans le basculement (soit sur une machine virtuelle ou dans un script qui est inclus dans le plan de récupération), le basculement d’un plan de récupération planifié s’arrête. Vous pouvez démarrer le basculement à nouveau.
8. Après la création de machines virtuelles réplica ils se trouvent une validation état d’attente. Cliquez sur **Valider** pour valider le basculement. 
9. Une fois la réplication est terminer le démarrage machines virtuelles à l’emplacement secondaire. 

## <a name="run-an-unplanned-failover"></a>Exécuter un basculement non planifié

Cette procédure explique comment exécuter un basculement non planifié pour un plan de récupération. Vous pouvez également exécuter le basculement d’une seule machine virtuelle ou d’un serveur physique sous l’onglet **Machines virtuelles** .

1. Sélectionnez **Récupération Plans** > *recoveryplan_name*. Cliquez sur **Basculer** > **basculement non planifié**. 
3. Dans la page **Confirmer le basculement non planifié **, choisissez les emplacements sources et cibles. Remarque la direction de basculement.

    - Si précédent basculement fonctionnent comme prévu et que tous les serveurs machine virtuelle sont situés dans emplacement source ou cible, les détails de la direction de basculement sont appliquent aux informations uniquement. 
    - Machines virtuelles s’actives dans des emplacements source et cible, le bouton **Modifier l’orientation** s’affiche. Utilisez ce bouton pour modifier et de spécifier la direction dans laquelle le basculement doit avoir lieu.

4. Si vous êtes basculement vers Azure et chiffrement des données est activé pour le cloud, dans la **Clé de chiffrement** , sélectionnez le certificat émis lorsque vous avez activé le chiffrement des données pendant l’installation de fournisseur sur le serveur VMM. 
5. Sélectionnez **Arrêter machines virtuelles et synchroniser des données les plus récentes** pour indiquer que doit essayer de récupération de Site arrêter les machines virtuelles protégés et synchroniser les données afin que la dernière version des données est alors basculée. Si vous ne sélectionnez pas cette option ou n’aboutit pas la tentative du basculement sera à partir du dernier point de récupération disponible pour la machine virtuelle.
6. Vous pouvez suivre la progression de basculement sous l’onglet **tâches** . Notez que même si des erreurs se produisent pendant un basculement non planifié, le plan de récupération s’exécute jusqu'à ce qu’elle soit terminée.
7. Après le basculement, les machines virtuelles sont dans un état **en attente de la validation** . Cliquez sur **Valider** pour valider le basculement.
8. Si vous définissez la réplication utiliser plusieurs points de récupération, dans le Point de récupération de modification, vous pouvez sélectionner à utiliser comme point de récupération qui n’est pas plus tard (le plus récent est utilisé par défaut). Une fois que vous vous engagez autres points de récupération sont été supprimés.
9. Une fois terminée la réplication machines virtuelles démarrer et en cours d’exécution sur le site secondaire. Toutefois, ils ne sont pas protégée ou la réplication. Lorsque le site principal est disponible à l’aide de la même infrastructure sous-jacente, cliquez sur **Répliquer inverse** pour commencer la réplication inverse. Cela garantit que toutes les données sont répliquées vers le site principal, et que la machine virtuelle est prête pour le basculement de nouveau. Inverser la réplication après qu’un basculement non planifié impose une surcharge transfert de données. Le transfert va utiliser la méthode qui est configurée pour les paramètres de réplication initiale pour le cloud.

## <a name="failback-from-secondary-to-primary"></a>Retour arrière depuis le secondaire principal

 Après le basculement du site principal vers emplacement secondaire, répliquées machines virtuelles ne sont pas protégés par récupération de Site et l’emplacement secondaire est maintenant agissant en tant que le serveur principal. Suivez les procédures basculer sur le site principal d’origine. Cette procédure explique comment exécuter un basculement planifié pour un plan de récupération. Vous pouvez également exécuter le basculement d’une seule machine virtuelle sous l’onglet **Machines virtuelles** .

1. Sélectionnez **Récupération Plans** > *recoveryplan_name*. Cliquez sur **Basculer** > **planifiée basculement**.
2. Dans la page **Confirmer le basculement planifiée **, choisissez les emplacements sources et cibles. Remarque la direction de basculement. Si le basculement de primaire travaillé en tant que s’attendre et toutes les machines virtuelles sont dans l’emplacement secondaire, qu'il s’agit de titre d’information uniquement.
3. Si vous êtes ignore revenir à partir d’Azure sélectionnez Paramètres de **La synchronisation des données**:

    - **Synchroniser des données avant le basculement (modifications delta synchroniser uniquement)**: cette option réduit indisponibilité des machines virtuelles comme il synchronise sans les fermer. Elle effectue les opérations suivantes :
        - Phase 1 : Prend instantané de la machine virtuelle dans Azure et la copie à l’hôte Hyper-V en local. L’ordinateur continue de s’exécuter Azure.
        - Phase 2 : Arrête la machine virtuelle dans Azure afin qu’aucune nouvelle modification se n’y produire. L’ensemble des modifications final sont transférés vers le serveur local et la machine virtuelle local est démarrée.
    

    - **Synchroniser les données pendant le basculement uniquement (téléchargement complet)**: utilisez cette option si vous avez déjà exécuté sur Azure pendant une longue période. Cette option est plus rapide car nous prévoyons que la plupart du disque a changé et nous ne voulez pas de temps dans le calcul de total de contrôle. Elle effectue un téléchargement du disque. Il est également utile lorsque la machine virtuelle local a été supprimée.
    
    > [AZURE.NOTE] Nous vous recommandons d’utiliser cette option si vous avez déjà exécuté Azure pendant un certain temps (un mois ou plus) ou l’ordinateur local virtuel a été supprimé. Cette option n’effectue pas les calculs de contrôle.
    
5. Si vous êtes basculement vers Azure et chiffrement des données est activé pour le cloud, dans la **Clé de chiffrement** , sélectionnez le certificat émis lorsque vous avez activé le chiffrement des données pendant l’installation de fournisseur sur le serveur VMM. 
5. Le dernier point de récupération est utilisé par défaut, mais dans **Point de récupération de modification** , vous pouvez spécifier un point de récupération différents. 
6. Cliquez sur la case à cocher pour démarrer le retour arrière.  Vous pouvez suivre la progression de basculement sous l’onglet **tâches** . 
7. f que vous avez sélectionné l’option pour synchroniser les données avant le basculement, une fois que la synchronisation de données initiale est terminée et que vous êtes prêt à arrêter les machines virtuelles dans Azure, cliquez sur **tâches**  >  <planned failover job name> **Réaliser le basculement**. Cela arrête l’ordinateur Azure, est transféré les dernières modifications apportées à la machine virtuelle en local et démarre.
8. Vous pouvez à présent, ouvrez une session sur l’ordinateur virtuel pour valider est disponible comme prévu. 
9. La machine virtuelle est dans une validation état d’attente. Cliquez sur **Valider** pour valider le basculement.
10. Maintenant pour terminer le retour arrière cliquez sur **Inverser répliquer** pour commencer à protéger la machine virtuelle dans le site principal.



## <a name="failback-to-an-alternate-location"></a>Retour arrière vers un autre emplacement

Si vous avez déployé protection entre un [site Hyper-V et Azure](site-recovery-hyper-v-site-to-azure.md) que vous avez capacité à basculement à partir d’Azure à une autre en local emplacement. Ceci est utile si vous devez définir un nouveau matériel en local. Voici comment procéder.

1. Si vous configurez de nouveaux matériels installer Windows Server 2012 R2 et le rôle Hyper-V sur le serveur.
2. Créer un commutateur réseau virtuel portant le même nom qui se trouvaient sur le serveur d’origine.
3. Sélectionner des **Éléments protégé** -> **Groupe Protection**  ->  <ProtectionGroupName>  ->  <VirtualMachineName> vous souhaitez restaurer, puis sélectionnez **Basculement planifiée**.
4. Dans **Confirmer le basculement planifiée** sélectionnez **Create local virtuelle ordinateur s’il n’existe pas**. 
5. Dans **Nom d’hôte** , sélectionnez le nouveau serveur hôte Hyper-V sur lequel vous souhaitez placer la machine virtuelle.
6. Synchronisation de données, nous vous recommandons de sélectionner l’option **synchroniser les données avant le basculement**. Cela permet de minimiser indisponibilité des machines virtuelles comme il synchronise sans les fermer. Elle effectue les opérations suivantes :

    - Phase 1 : Prend instantané de la machine virtuelle dans Azure et la copie à l’hôte Hyper-V en local. L’ordinateur continue de s’exécuter Azure.
    - Phase 2 : Arrête la machine virtuelle dans Azure afin qu’aucune nouvelle modification se n’y produire. L’ensemble des modifications final sont transférés vers le serveur local et la machine virtuelle local est démarrée.
    
7. Cliquez sur la case à cocher pour commencer le basculement (restauration).
8. Une fois la synchronisation initiale se termine et que vous êtes prêt à arrêter la machine virtuelle dans Azure, cliquez sur **tâches** > <planned failover job> > **Réaliser le basculement**. Cela arrête l’ordinateur Azure, est transféré les dernières modifications apportées à la machine virtuelle en local et démarre.
9. Vous pouvez ouvrir une session sur l’ordinateur virtuel en local pour vérifier que tout fonctionne comme prévu. Cliquez ensuite sur **Valider** pour terminer le basculement.
10. Cliquez sur **Annuler répliquer** pour commencer à protéger la machine virtuelle en local.

    >[AZURE.NOTE] Si vous annulez le travail retour arrière bien qu’il soit à l’étape de la synchronisation des données, la machine virtuelle locale sera dans un état endommagées. C’est parce que la synchronisation de données copies les dernières données Azure machine virtuelle disques sur les disques de données local et jusqu'à la synchronisation est terminée, le disque données peuvent ne pas être dans un état cohérent. Si la machine virtuelle activé prem est démarrée après que la synchronisation de données a été annulée, il ne peut pas démarrer. Nouveau déclencher le basculement pour terminer la synchronisation de données.
 
