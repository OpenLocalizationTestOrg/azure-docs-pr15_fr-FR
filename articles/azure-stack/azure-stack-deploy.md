<properties
    pageTitle="Avant de déployer du contact pile Azure | Microsoft Azure"
    description="Afficher l’environnement et matérielle requise pour Azure pile du contact (administrateur du service)."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/12/2016"
    ms.author="erikje"/>

# <a name="azure-stack-deployment-prerequisites"></a>Conditions préalables au déploiement pile Azure

Avant de déployer Azure pile du contact ([Preuve de Concept](azure-stack-poc.md)), vérifiez que votre ordinateur satisfait la configuration requise suivante.
Les exigences de déploiement Technical Preview 2 pour cette phase sont les mêmes que ceux requis pour Technical Preview 1. Par conséquent, vous pouvez utiliser le même matériel que vous avez utilisé pour l’aperçu unique précédent.

## <a name="hardware"></a>Matériel

| Composant | Minimum  | Recommandé |
|---|---|---|
| Lecteurs de disques : système d’exploitation | 1 disque du système d’exploitation avec minimum de 200 Go disponible pour la partition système (SSD ou du disque dur) | 1 disque du système d’exploitation au minimum 200 Go disponible pour la partition système (SSD ou du disque dur) |
| Lecteurs de disques : les données du contact pile Azure générales | 4 disques. Chaque disque fournit un minimum de 140 Go de capacité (SSD ou du disque dur). Tous les disques disponibles seront utilisés. | 4 disques. Chaque disque fournit un minimum de 250 Go de capacité (SSD ou du disque dur). Tous les disques disponibles seront utilisés.|
| Calculer : processeur | Double Socket : 12 physique cœurs (total)  | Double Socket : 16 physique cœurs (total) |
| Calculer : mémoire | 96 GO DE RAM  | 128 GO DE RAM |
| Calculer : BIOS | Hyper-V activé (avec prise en charge lattes)  | Hyper-V activé (avec prise en charge lattes) |
| Réseau : carte réseau | Windows Server 2012 R2 Certification requis pour la carte réseau ; aucune des fonctionnalités spécialisées obligatoires | Windows Server 2012 R2 Certification requis pour la carte réseau ; aucune des fonctionnalités spécialisées obligatoires |
| Certification du logo matériel | [Certifié pour Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certifié pour Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0)|

**Configuration de lecteur de disque de données :** Tous les lecteurs de données doivent être du même type (toutes les associations de sécurité ou tous les SATA) et la capacité. Si les lecteurs de disques associations de sécurité sont utilisés, les lecteurs de disques doit être connectés via un chemin d’accès unique (aucune o, prise en charge de chemin multiples n’est fourni).

**Options de configuration HBA**
 
- (Recommandé) HBA simple
- RAID HBA – carte doit être configuré en mode « passer »
- HBA RAID – disques doivent être configurés comme un disque, RAID-0

**Combinaisons de type multimédia et bus pris en charge**

-   DISQUE DUR SATA

-   DISQUE DUR ASSOCIATIONS DE SÉCURITÉ

-   DISQUE DUR RAID

-   RAID SSD (si le type de média est n’est pas spécifié/inconnu\*)

-   SATA SSD + DISQUES DURS SATA

-   ASSOCIATIONS DE SÉCURITÉ SSD + DISQUE DUR ASSOCIATIONS DE SÉCURITÉ

\*Contrôleurs RAID sans fonctionnalité SQL directe ne reconnaissent pas le type de média. Ces contrôleurs marque disque dur et SSD comme non spécifié. Dans ce cas, le SSD est utilisée comme espace de stockage permanente au lieu de la mise en cache des appareils mobiles. Par conséquent, vous pouvez déployer le contrôle du concept Microsoft Azure pile sur ces SSDs.

**Exemple HBA**: LSI 9207 8i, LSI-9300-8i ou LSI-9265-8i en mode SQL direct

Exemples de configurations OEM sont disponibles.

## <a name="operating-system"></a>Système d'exploitation

| | **Configuration requise**  |
|---|---|
| **Version du système d’exploitation** | Windows Server 2012 R2 ou version ultérieure. La version du système d’exploitation n’est pas critique avant le démarrage de déploiement, que vous allez démarrez l’ordinateur hôte le disque dur virtuel qui est incluse dans zip d’installation pile Azure. Le système d’exploitation et tous les correctifs requis sont déjà intégrés à l’image. N’utilisez pas les touches à activer toutes les instances Windows Server utilisées dans le contrôle du concept.|

## <a name="deployment-requirements-check-tool"></a>Configuration requise de déploiement vérifier outil

Après avoir installé le système d’exploitation, vous pouvez utiliser le [Vérificateur de déploiement d’Azure pile Technical Preview 2](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) pour confirmer que votre matériel satisfait la configuration requise.



## <a name="microsoft-azure-active-directory-accounts"></a>Comptes Microsoft Azure Active Directory

Le déploiement de Microsoft Azure pile du contact doit est relié à Azure. Par conséquent, vous devez préparer un compte Microsoft Azure Active Directory avant d’exécuter le script PowerShell déploiement. Ce compte devienne l’administrateur Global pour le client Azure Active Directory. Il est utilisée à la mise en service et déléguer des applications et des identités de service pour tous les services Azure pile qui interagissent avec Azure Active Directory et API du graphique. Il permet également en tant que le propriétaire de l’abonnement de fournisseur par défaut (que vous pouvez modifier ultérieurement). Vous pouvez vous connecter au portail d’administration de votre système de pile Azure à l’aide de ce compte.

1. Créez un compte Azure AD qui se trouve l’administrateur d’annuaire pour au moins un Azure Active Directory. Si vous avez déjà une, vous pouvez l’utiliser. Dans le cas contraire, vous pouvez créer un gratuitement en [http://azure.microsoft.com/en-us/pricing/free-trial/](http://azure.microsoft.com/pricing/free-trial/) (en Chine, visitez à la place <http://go.microsoft.com/fwlink/?LinkID=717821> .)

    Enregistrer ces informations d’identification pour une utilisation à l’étape 6 de la commande [exécuter le script de déploiement PowerShell](azure-stack-run-powershell-script.md#run-the-powershell-deployment-script). Ce compte *d’administrateur de service* peut configurer et gérer les nuages de ressources, les comptes d’utilisateurs, client offres, les quotas et les prix. Dans le portail, ils peuvent créer nuages de site Web, nuages privés machine virtuelle, créer des plans et gérer les abonnements utilisateur.

2. [Créer](azure-stack-add-new-user-aad.md) au moins un compte afin que vous pouvez vous connecter à la démonstration de pile Azure comme un client.

  	| **Compte d’Azure Active Directory**  | **Prise en charge ?** |
  	|---|---| 
  	| Compte professionnel ou scolaire avec abonnement Azure Public valide  | Oui |
  	| Account Microsoft avec abonnement Azure Public valide  | N° |
  	| Compte professionnel ou scolaire avec abonnement à Azure valide Chine  | Oui |
  	| Compte professionnel ou scolaire avec nous pour le gouvernement Azure abonnement valide  | Oui |


## <a name="network"></a>Réseau

### <a name="switch"></a>Commutateur

Un port disponible sur un commutateur de l’ordinateur du contact.  

L’ordinateur du contact Azure pile prend en charge la connexion à un commutateur access port ou ligne. Aucune des fonctionnalités spécialisées ne sont nécessaires sur le commutateur. Si vous utilisez un port jonction ou si vous devez configurer un ID de réseau local virtuel, vous devez fournir l’ID de réseau local virtuel comme un paramètre de déploiement. Vous pouvez voir les exemples dans la [liste des paramètres de déploiement](azure-stack-run-powershell-script.md).

### <a name="subnet"></a>Sous-réseau

Ne pas connecter l’ordinateur du contact aux sous-réseaux suivants :
- 192.168.200.0/24
- 192.168.100.0/27
- 192.168.101.0/26
- 192.168.102.0/24
- 192.168.103.0/25
- 192.168.104.0/25

Ces sous-réseaux est réservés pour les réseaux internes au sein de l’environnement de Microsoft Azure pile du contact.

### <a name="ipv4ipv6"></a>IPv4/IPv6

IPv4 uniquement est pris en charge. Vous ne pouvez pas créer des réseaux IPv6.

### <a name="dhcp"></a>DHCP

Vérifiez qu’un serveur DHCP est disponible sur le réseau la carte réseau se connecte à. Si DHCP n’est pas disponible, vous devez préparer un réseau IPv4 statique supplémentaire outre celui utilisé par l’hôte. Vous devez fournir cette adresse IP et la passerelle comme un paramètre de déploiement. Vous pouvez voir les exemples dans la [liste des paramètres de déploiement](azure-stack-run-powershell-script.md).

### <a name="internet-access"></a>Accès à Internet

Pile Azure requiert l’accès à Internet, directement ou via un proxy transparent. Pile Azure ne prend pas en charge la configuration d’un proxy web pour activer l’accès Internet. L’adresse IP hôte et le nouveau IP affectée à la BGPNAT01 MAS (par DHCP ou adresse IP statique) doivent être en mesure d’accéder à Internet. Ports 80 et 443 sont utilisées dans les domaines graph.windows.net et login.windows.net.

### <a name="telemetry"></a>Télémétrie

Pour prendre en charge des flux de données de télémétrie, le port 443 (HTTPS) doit être ouvert dans votre réseau. Le point de terminaison client est https://vortex-win.data.microsoft.com.


## <a name="next-steps"></a>Étapes suivantes

[Télécharger le package de déploiement d’Azure pile du contact](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[Déploiement d’Azure pile du contact](azure-stack-run-powershell-script.md)
