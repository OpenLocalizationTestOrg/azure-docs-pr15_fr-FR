<properties
   pageTitle="Créer une passerelle d’application avec pare-feu pour applications web à l’aide du portail | Microsoft Azure"
   description="Apprenez à créer une passerelle d’Application avec pare-feu pour applications web à l’aide du portail"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="create-an-application-gateway-with-web-application-firewall-by-using-the-portal"></a>Créer une passerelle d’application avec pare-feu pour applications web à l’aide du portail

> [AZURE.SELECTOR]
- [Portail Azure](application-gateway-web-application-firewall-portal.md)
- [Azure PowerShell Gestionnaire de ressources](application-gateway-web-application-firewall-powershell.md)

Le pare-feu d’application web (WAF) dans Azure Application Gateway protège les applications web contre les attaques courantes sur le web comme injection SQL, les attaques de script intersites et détournements de session. Application Web protège contre la plupart d'entre l’OWASP avoir supérieure 10 courantes web.

Azure Application passerelle est un équilibrage de charge de calque à 7. Il propose basculement, routage des performances des requêtes HTTP entre différents serveurs, qu’ils soient sur le nuage ou sur site.
Application fournit de nombreuses fonctionnalités d’Application remise contrôleur (ADC), y compris l’équilibrage de charge HTTP, affinité session basée sur les cookies, couche SSL (Secure Sockets) décharger, état personnalisé sondes, prise en charge de plusieurs sites et bien d’autres.
Pour rechercher une liste complète des fonctionnalités prises en charge, consultez [Vue d’ensemble de la passerelle Application](application-gateway-introduction.md)

## <a name="scenarios"></a>Scénarios

Dans cet article, il existe deux scénarios :

Dans le premier scénario, vous apprenez à [Ajouter pare-feu pour applications web à une passerelle d’application existant](#add-web-application-firewall-to-an-existing-application-gateway).

Dans le second scénario, vous apprenez à [créer une passerelle d’application avec pare-feu pour applications web](#create-an-application-gateway-with-web-application-firewall)

![Exemple de scénario][scenario]

>[AZURE.NOTE] Teste une configuration supplémentaire de la passerelle d’application, y compris leur santé personnalisée, adresses de pool version serveur et règles supplémentaires sont configurés après avoir configuré la passerelle d’application et non lors du déploiement initial.

## <a name="before-you-begin"></a>Avant de commencer

Azure Application passerelle nécessite son propre sous-réseau. Lorsque vous créez un réseau virtuel, assurez-vous que vous laissez suffisamment d’espace adresse pour avoir plusieurs sous réseaux. Une fois que vous déployez une passerelle d’application à un sous-réseau, passerelles d’application uniquement supplémentaires sont en mesure d’être ajoutés au sous-réseau.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Ajouter les pare-feu pour applications web à une passerelle d’application existant

Ce scénario met à jour une passerelle d’application existant pour prendre en charge de pare-feu pour applications web en mode de prévention.

### <a name="step-1"></a>Étape 1

Accédez au portail Azure, sélectionnez une passerelle d’Application existant.

![Création d’Application passerelle][1]

### <a name="step-2"></a>Étape 2

Cliquez sur **la Configuration** et mettre à jour les paramètres de la passerelle application. Lorsque vous avez terminé cliquez sur **Enregistrer**

Mettre à jour une passerelle d’application existant pour prendre en charge de pare-feu pour applications web sont les suivantes :

- **Niveau** - la couche sélectionnée doit être **WAF** pour prendre en charge de pare-feu pour applications web
- **Taille de référence (SKU)** : ce paramètre est la taille de la passerelle d’application avec pare-feu pour applications web, les options disponibles sont (**moyennes** et **grandes**).
- **État du pare-feu** : ce paramètre désactive ou Active le pare-feu d’application web.
- **Mode pare-feu** - ce paramètre est la façon dont pare-feu pour applications web traite le trafic malveillant. Mode de **détection** enregistre uniquement les événements, où le mode de **prévention** enregistre les événements et s’arrête le trafic malveillant.

![paramètres de base d’affichage carte][2]

>[AZURE.NOTE] Pour afficher les journaux de pare-feu application web, diagnostics doit être activés et ApplicationGatewayFirewallLog sélectionné. Vous pouvez choisir un nombre d’instances de 1 à des fins de test. Il est important de savoir que n’importe quel nombre d’instances sous deux instances n’est pas traité par le contrat SLA et sont donc pas recommandée. Petites passerelles ne sont pas disponibles lors de l’utilisation de pare-feu pour applications web.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Créer une passerelle d’application avec pare-feu pour applications web

Ce scénario sera :

- Créer une passerelle d’application web moyenne application pare-feu avec deux instances.
- Créer un réseau virtuel nommé AdatumAppGatewayVNET avec un bloc CIDR réservé de 10.0.0.0/16.
- Créer un sous-réseau appelé Appgatewaysubnet qui utilise 10.0.0.0/28 comme son bloc CIDR.
- Configurer un certificat pour déchargement SSL.

### <a name="step-1"></a>Étape 1

Accédez au portail Azure, cliquez sur **Nouveau** > **réseau** > **Passerelle d’Application**

![Création d’Application passerelle][1-1]

### <a name="step-2"></a>Étape 2

Remplissez les informations de base sur la passerelle d’application suivante. Assurez-vous de choisir **WAF** que la couche. Lorsque vous avez terminé, cliquez sur **OK**

Les informations requises pour les paramètres de base sont la suivante :

- **Nom** : le nom de la passerelle d’application.
- **Niveau** - le niveau de la passerelle d’application, les options disponibles sont (**Standard** et **WAF**). Pare-feu pour applications Web est disponible uniquement dans la couche WAF.
- **Taille de référence (SKU)** : ce paramètre est la taille de la passerelle d’application, les options disponibles sont (**moyennes** et **grandes**).
- **Nombre d’instances** : le nombre d’instances, cette valeur doit être un nombre compris entre **2** et **10**.
- **Groupe de ressources** - le groupe de ressources pour stocker la passerelle d’application, il peut être un groupe de ressources existant ou un nouvel identifiant.
- **Emplacement** - la région pour la passerelle d’application, il est le même emplacement que le groupe de ressources. *L’emplacement est important que le réseau virtuel et IP public doit être placé dans le même emplacement que la passerelle*.

![paramètres de base d’affichage carte][2-2]

>[AZURE.NOTE] Vous pouvez choisir un nombre d’instances de 1 à des fins de test. Il est important de savoir que n’importe quel nombre d’instances sous deux instances n’est pas traité par le contrat SLA et sont donc pas recommandée. Petites passerelles ne sont pas prises en charge pour les scénarios de pare-feu application web.

### <a name="step-3"></a>Étape 3

Une fois les paramètres de base sont définis, l’étape suivante consiste à définir le réseau virtuel à utiliser. Le réseau virtuel héberge l’application de la passerelle d’application équilibrage de la charge.

Cliquez sur **Choisir un réseau virtuel** pour configurer le réseau virtuel.

![carte affichant les paramètres de passerelle d’application][3]

### <a name="step-4"></a>Étape 4

Dans la carte **Réseau virtuel choisir** , cliquez sur **Créer un nouveau**

Tandis que ne pas expliqués dans ce scénario, un réseau virtuel pu être sélectionné à ce stade.  Si un réseau virtuel est utilisé, il est important de savoir que le réseau virtuel doit un sous-réseau vide ou un sous réseau d’uniquement les ressources de passerelle application à utiliser.

![Choisissez carte réseau virtuel][4]

### <a name="step-5"></a>Étape 5

Renseignez les informations de réseau dans la carte **Réseau virtuel créer** comme décrit dans la description du [scénario](#scenario) précédente.

![Créer la carte réseau virtuel avec les informations renseignées][5]

### <a name="step-6"></a>Étape 6

Une fois le réseau virtuel est créé, l’étape suivante consiste à définir l’adresse IP frontal de la passerelle d’application. À ce stade, le choix est comprise entre un public ou une adresse IP privée pour le serveur frontal. Le choix dépend de l’application internet en vis-à-vis ou internes uniquement. Ce scénario part du principe à l’aide d’une adresse IP publique. Pour choisir une adresse IP privée, vous pouvez être clique sur le bouton **privé** . Une adresse IP automatiquement affectée est sélectionnée, ou vous pouvez cliquer sur la case à cocher **Choisir une adresse IP privée spécifique** pour entrer une manuellement.

Cliquez sur **Choisir une adresse IP publique**. Si une adresse IP publique existante n’est disponible qu’il peut être choisi à ce stade, dans ce scénario, vous créez une nouvelle adresse IP publique. Cliquez sur **Créer nouveau**

![Choisissez carte d’adresse IP public][6]

### <a name="step-7"></a>Étape 7

Donnez un nom convivial à l’adresse IP publique suivant et cliquez sur **OK**

![Créer la carte adresse IP public][7]

### <a name="step-8"></a>Étape 8

Ensuite, vous installez la configuration de la réception.  Si **http** est utilisée, rien n’est laissé pour configurer et vous pouvez cliquer sur **OK** . Pour utiliser le **protocole https** configuration supplémentaires sont nécessaires.

Pour utiliser **https**, un certificat est requis. La clé privée du certificat est nécessaire pour exporter un fichier .pfx du certificat doit être fourni et le mot de passe pour le fichier.

Cliquez sur **HTTPS**, cliquez sur l’icône de **dossier** en regard de la zone de texte **certificat PFX télécharger** .
Accédez au fichier .pfx certificat sur votre système de fichiers. Une fois qu’il est sélectionné, donnez un nom convivial au certificat et tapez le mot de passe pour le fichier .pfx.

Une fois terminé, cliquez sur **OK** pour passer en revue les paramètres de la passerelle d’Application.

![Section de Configuration de récepteur sur carte de paramètres][8]

### <a name="step-9"></a>Étape 9

Configurer les paramètres spécifiques **WAF** .

- **État du pare-feu** : ce paramètre Active ou désactive la WAF.
- **Mode pare-feu** - ce paramètre détermine que le actions WAF prend en charge le trafic malveillant. Si **la détection** est accepté, le trafic est enregistré uniquement.  Si la **prévention de** l’est accepté, le trafic est connecté et arrêté avec un 403 non autorisé.

![paramètres de pare-feu l’application Web][9]

### <a name="step-10"></a>Étape 10

Examinez la page de résumé, cliquez sur **OK**.  La passerelle d’application est désormais la file d’attente et créée.

### <a name="step-12"></a>Étape 12

Une fois que la passerelle d’application a été créée, accédez à celui-ci dans le portail pour poursuivre la configuration de la passerelle d’application.

![Affichage des ressources application passerelle][10]

Ces étapes créent une passerelle application de base avec les paramètres par défaut pour le récepteur, pool principal, paramètres http version serveur et règles. Vous pouvez modifier ces paramètres pour l’adapter à votre déploiement une fois que la mise en service est réussie

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment configurer la journalisation des diagnostics, afin de vous connecter les événements qui sont détectées ou empêchés avec pare-feu pour applications Web, en visitant une [Application passerelle Diagnostics](application-gateway-diagnostics.md)

Apprenez à créer des sondes état personnalisé en visitant [créer une sonde état personnalisé](application-gateway-create-probe-portal.md)

Découvrez comment configurer SSL décharger et prendre le déchiffrement SSL coûteux désactiver vos serveurs web, visitez [Configurer décharger SSL](application-gateway-ssl-portal.md)

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[1-1]: ./media/application-gateway-web-application-firewall-portal/figure1-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[4]: ./media/application-gateway-web-application-firewall-portal/figure4.png
[5]: ./media/application-gateway-web-application-firewall-portal/figure5.png
[6]: ./media/application-gateway-web-application-firewall-portal/figure6.png
[7]: ./media/application-gateway-web-application-firewall-portal/figure7.png
[8]: ./media/application-gateway-web-application-firewall-portal/figure8.png
[9]: ./media/application-gateway-web-application-firewall-portal/figure9.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png