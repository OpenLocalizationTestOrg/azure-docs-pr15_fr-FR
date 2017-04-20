<properties
    pageTitle="Résolution des problèmes SSH de détaillées pour une machine virtuelle Azure | Microsoft Azure"
    description="Plus de SSH dépannage pour les problèmes de connexion à une machine virtuelle Azure"
    keywords="SSH connexion refusée, ssh erreur, azure ssh, SSH Échec de la connexion"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="detailed-ssh-troubleshooting-steps"></a>Étapes de résolution détaillées SSH

Il existe de nombreuses raisons possibles qui la clientSSH n’est pas en mesure d’atteindre le service SSH sur l’ordinateur virtuel. Si vous avez suivi les [étapes de dépannage général SSH](virtual-machines-linux-troubleshoot-ssh-connection.md)plus, vous devez résoudre le problème de connexion. Cet article vous guide tout au long des étapes de résolution détaillées pour déterminer l’endroit où la connexion SSH ignore et comment faire pour résoudre ce problème.

## <a name="take-preliminary-steps"></a>Étapes préliminaires

Le diagramme suivant illustre les composants sont impliquées.

![Diagramme montrant les composants du service SSH](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Les étapes suivantes vous aident à isoler la source du problème et identifier les solutions.

Tout d’abord, vérifiez l’état de la machine virtuelle dans le portail.

Dans le [portail Azure](https://portal.azure.com):

1. Pour les machines virtuelles créées à l’aide du modèle de déploiement classique, sélectionnez **Parcourir** > **machines virtuelles (classique)** > *nom machine virtuelle*.

    -OU-

    Pour les machines virtuelles créées à l’aide du modèle de gestionnaire de ressources, sélectionnez **Parcourir** > **machines virtuelles** > *nom machine virtuelle*.

    Le volet d’état de la machine virtuelle doit figurer **en cours d’exécution**. Faites défiler jusqu'à afficher les activités récentes pour le cluster, le stockage et ressources réseau.

2. Sélectionnez **paramètres** pour examiner les points de terminaison, adresses IP et d’autres paramètres.

    Pour identifier les points de terminaison de machines virtuelles qui ont été créés à l’aide du Gestionnaire de ressources, vérifiez qu’un [groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) a été défini. Vérifiez également que les règles ont été appliqués au groupe de sécurité réseau et qu’ils soient référencées dans le sous-réseau.

Dans le [portail classique Azure](https://manage.windowsazure.com), pour les machines virtuelles qui ont été créées à l’aide du modèle de déploiement classique :

1. Sélectionnez **machines virtuelles** > *nom machine virtuelle*.
2. Sélectionnez l’ordinateur **tableau de bord** pour vérifier son état.
3. Sélectionnez **Moniteur** pour voir l’activité récente pour le cluster, le stockage et ressources réseau.
4. Sélectionnez les **points de terminaison** pour vous assurer qu’il y a un point de terminaison pour le trafic SSH.

Pour vérifier la connectivité réseau, vérifiez les points de terminaison configurés et déterminez si vous pouvez atteindre la machine virtuelle via un autre protocole, tel que HTTP ou un autre service.

Une fois ces étapes, réessayez de vous connecter SSH.


## <a name="find-the-source-of-the-issue"></a>Trouver la source du problème

Le client SSH sur votre ordinateur peut échouer atteindre le service SSH sur l’ordinateur virtuel Azure en raison de problèmes ou des erreurs de configuration dans les éléments suivants :

- [Ordinateur client SSH](#source-1-ssh-client-computer)
- [Appareil de bord d’organisation](#source-2-organization-edge-device)
- [Point de terminaison de service en nuage et accéder à la liste de contrôle (et)](#source-3-cloud-service-endpoint-and-acl)
- [Groupes de sécurité réseau](#source-4-network-security-groups)
- [Basé sur Linux machine virtuelle Azure](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Source de 1 : SSH ordinateur client

Afin d’éliminer la source de l’échec de votre ordinateur, vérifiez qu’il peut identifier les connexions SSH vers un autre local, ordinateur fonctionnant sous Linux.

![Diagramme qui met en surbrillance des composants de l’ordinateur client SSH](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Si la connexion échoue, vérifiez les points suivants sur votre ordinateur :

- Paramètre de pare-feu local bloque entrant ou sortant SSH le trafic (TCP 22)
- Localement installé le logiciel client proxy qui empêche les connexions SSH
- Localement installé un logiciel qui empêche les connexions SSH de surveillance
- Autres types de logiciels de sécurité qui surveillent le trafic ou autorisent/interdire les types de trafic spécifiques

Si une des conditions suivantes s’appliquent, désactivez le logiciel temporairement et essayez d’une connexion SSH sur un ordinateur local pour déterminer la raison pour laquelle que la connexion est bloquée sur votre ordinateur. Travailler avec votre administrateur réseau pour corriger les paramètres du logiciel pour autoriser les connexions SSH.

Si vous utilisez l’authentification par certificat, vérifiez que vous disposez des autorisations suivantes dans le dossier .ssh dans votre répertoire de base :

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/\*.pub
- Chmod 600 ~/.ssh/id_rsa (ou tout autre fichier ayant vos clés privées stockés dans ces)
- ~/.Ssh/known_hosts chmod 644 (contient des hôtes que vous avez connectés à viaSSH)

## <a name="source-2-organization-edge-device"></a>Source 2 : Dispositif de bord organisation

Afin d’éliminer votre appareil bord organisation la source du problème, vérifiez un ordinateur est connecté directement à Internet peut identifier les connexions SSH à votre machine virtuelle Azure. Si vous accédez à la machine virtuelle sur un réseau privé virtuel à un site ou une connexion Azure ExpressRoute, passez à le [Source 4 : réseau de groupes de sécurité](#nsg).

![Diagramme qui met en surbrillance le périphérique de périmètre organisation](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Si vous n’avez pas un ordinateur sur lequel est directement connecté à Internet, créez une nouvelle machine virtuelle Azure dans son propre groupe de ressources ou service en nuage et utilisez-le. Pour plus d’informations, voir [créer une machine virtuelle Linux dans Azure en cours d’exécution](virtual-machines-linux-quick-create-cli.md). Supprimer le groupe de ressources ou le service machine virtuelle et le cloud lorsque vous avez terminé avec votre test.

Si vous pouvez créer une connexion SSH avec un ordinateur est connecté directement à Internet, vérifiez votre appareil de bord d’organisation pour :

- Un pare-feu qui bloque le trafic SSH avec Internet
- Un serveur proxy qui empêche les connexions SSH
- Détection ou un logiciel en cours d’exécution sur les périphériques de votre réseau bord qui empêche les connexions SSH de surveillance

Travailler avec votre administrateur réseau pour corriger les paramètres de vos périphériques de bord d’organisation pour autoriser le trafic SSH avec Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Source 3 : Point de terminaison de service Cloud et et

> [AZURE.NOTE] Cette source s’applique uniquement aux machines virtuelles créées à l’aide du modèle de déploiement classique. Pour les machines virtuelles qui ont été créés à l’aide du Gestionnaire de ressources, passez à le [source 4 : réseau de groupes de sécurité](#nsg).

Afin d’éliminer le point de terminaison de service cloud et et la source du problème, vérifiez un autre Azure machine virtuelle dans le même réseau virtuel peut identifier les connexions SSH à votre ordinateur virtuel.

![Diagramme qui met en surbrillance le point de terminaison de service cloud et et](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Si vous n’avez pas une autre machine virtuelle dans le même réseau virtuel, vous pouvez facilement créer un nouveau. Pour plus d’informations, voir [créer une machine virtuelle Linux sur Azure à l’aide de l’infrastructure du langage commun](virtual-machines-linux-quick-create-cli.md). Supprimer la machine virtuelle supplémentaire lorsque vous avez terminé avec votre test.

Si vous pouvez créer une connexion SSH avec une machine virtuelle dans le même réseau virtuel, vérifiez les points suivants :

- **La configuration du point de terminaison pour le trafic SSH sur la machine virtuelle cible.** Le port TCP privé du point de terminaison doit correspondre le port TCP sur lequel le service SSH sur l’ordinateur virtuel est à l’écoute. (Le port par défaut est 22). Pour les machines virtuelles créées à l’aide du modèle de déploiement du Gestionnaire de ressources, vérifiez le numéro de port TCP SSH dans le portail Azure en sélectionnant **Parcourir** > **machines virtuelles (v2)** > *nom machine virtuelle* > **paramètres** > **points de terminaison**.

- **ET du point de terminaison du trafic SSH sur l’ordinateur virtuel cible.** Un et vous permet de spécifier autorisé ou refusé le trafic entrant à partir d’Internet, en fonction de son adresse IP source. Utilisateurs incorrectement configurés peuvent empêcher le trafic entrant SSH au point de terminaison. Vérifiez vos utilisateurs pour vous assurer que le trafic entrant à partir des adresses IP publiques de votre serveur proxy ou autres serveur edge est autorisée. Pour plus d’informations, voir [à propos de l’accès au réseau listes de contrôle ()](../virtual-network/virtual-networks-acl.md).

Pour éliminer le point de terminaison en tant que source du problème, supprimez le point de terminaison en cours, créer un point de terminaison et spécifiez le nom SSH (port TCP 22 pour le numéro de port publiques et privées). Pour plus d’informations, voir [configurer des points de terminaison sur une machine virtuelle dans Azure](virtual-machines-windows-classic-setup-endpoints.md).

<a id="nsg"></a>
## <a name="source-4-network-security-groups"></a>Source 4 : Groupes de sécurité de réseau

Groupes de sécurité réseau activer que vous ayez un contrôle plus précis du trafic entrant et sortant autorisé. Vous pouvez créer des règles qui s’étalent sous-réseaux et services dans un réseau virtuel Azure cloud. Vérifiez vos règles du groupe de sécurité réseau pour vous assurer que le trafic SSH vers et depuis Internet est autorisé.
Pour plus d’informations, voir [à propos des groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md).

## <a name="source-5-linux-based-azure-virtual-machine"></a>Source 5 : Linux Azure machine virtuelle

La dernière source d’éventuels problèmes est la machine virtuelle Azure lui-même.

![Diagramme qui met machine virtuelle Azure Linux](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Si vous n’avez pas déjà fait, suivez les instructions [pour réinitialiser un mot de passe ou SSH pour machines virtuelles basées sur Linux](virtual-machines-linux-classic-reset-access.md).

Essayez de vous reconnecter à partir de votre ordinateur. Si le problème persiste, voici quelques-uns des problèmes possibles :

- Le service SSH ne fonctionne pas sur l’ordinateur virtuel cible.
- Le service SSH n’écoute pas sur le port TCP 22. Pour tester cela, installez un client telnet sur votre ordinateur local et exécutez « telnet *cloudServiceName*. cloudapp.net 22 ». Ceci permet de déterminer si la machine virtuelle permet communications entrantes et sortantes au point de terminaison SSH.
- Le pare-feu local sur l’ordinateur virtuel cible comporte des règles qui empêchent le trafic SSH entrant ou sortant.
- Détection ou un logiciel est en cours d’exécution sur l’ordinateur virtuel Azure de surveillance empêche les connexions SSH.


## <a name="additional-resources"></a>Ressources supplémentaires
Pour plus d’informations sur la résolution des problèmes d’accès aux applications, voir [résoudre les problèmes accéder à l’application en cours d’exécution sur un ordinateur virtuel Azure](virtual-machines-linux-troubleshoot-app-connection.md)