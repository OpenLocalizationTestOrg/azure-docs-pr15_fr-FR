<properties
   pageTitle="Créer une passerelle d’application à l’aide de l’infrastructure du langage commun Azure dans le Gestionnaire de ressources | Microsoft Azure"
   description="Apprenez à créer une passerelle d’Application à l’aide de l’infrastructure du langage commun Azure dans le Gestionnaire de ressources"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
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

# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Créer une passerelle d’application à l’aide de l’infrastructure du langage commun Azure

> [AZURE.SELECTOR]
- [Portail Azure](application-gateway-create-gateway-portal.md)
- [Azure PowerShell Gestionnaire de ressources](application-gateway-create-gateway-arm.md)
- [Azure PowerShell classique](application-gateway-create-gateway.md)
- [Modèle de gestionnaire de ressources Azure](application-gateway-create-gateway-arm-template.md)
- [Azure infrastructure du langage commun](application-gateway-create-gateway-cli.md)

Azure Application passerelle est un équilibrage de charge de calque à 7. Il propose basculement, routage des performances des requêtes HTTP entre différents serveurs, qu’ils soient sur le nuage ou sur site. Passerelle d’application comporte les fonctionnalités de remise application suivantes : HTTP charger sondes état personnalisé équilibrage, affinité session basée sur les cookies et déchargement de la couche SSL (Secure Sockets) et prise en charge de plusieurs sites.

## <a name="prerequisite-install-the-azure-cli"></a>Au préalable : Installez l’infrastructure du langage commun Azure

Pour effectuer les étapes décrites dans cet article, vous devez [installer l’Interface de ligne de commande Azure pour Mac, Linux et Windows Azure infrastructure (du langage commun)](../xplat-cli-install.md) et vous devez [Ouvrir une session sur Azure](../xplat-cli-connect.md). 

> [AZURE.NOTE] Si vous n’avez pas un compte Azure, vous devez une. Remonter d’authentification pour une [version d’évaluation gratuite ici](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Scénario

Dans ce scénario, vous allez apprendre à créer une passerelle d’application à l’aide du portail Azure.

Ce scénario sera :

- Créer une passerelle application moyenne avec deux instances.
- Créer un réseau virtuel nommé AdatumAppGatewayVNET avec un bloc CIDR réservé de 10.0.0.0/16.
- Créer un sous-réseau appelé Appgatewaysubnet qui utilise 10.0.0.0/28 comme son bloc CIDR.
- Configurer un certificat pour déchargement SSL.

![Exemple de scénario][scenario]

>[AZURE.NOTE] Teste une configuration supplémentaire de la passerelle d’application, y compris leur santé personnalisée, adresses de pool version serveur et règles supplémentaires sont configurés après avoir configuré la passerelle d’application et non lors du déploiement initial.

## <a name="before-you-begin"></a>Avant de commencer

Azure Application passerelle nécessite son propre sous-réseau. Lorsque vous créez un réseau virtuel, assurez-vous que vous laissez suffisamment d’espace adresse pour avoir plusieurs sous réseaux. Une fois que vous déployez une passerelle d’application à un sous-réseau, passerelles d’application uniquement supplémentaires sont en mesure d’être ajoutés au sous-réseau.

## <a name="log-in-to-azure"></a>Se connecter à Azure

Ouvrez l' **invite de commandes de Microsoft Azure**et se connecter. 

    azure login

Une fois que vous tapez l’exemple précédent, un code est fourni. Accédez à https://aka.ms/devicelogin dans un navigateur pour continuer le processus de connexion.

![ouverture de session CMD affichant appareil][1]

Dans le navigateur, entrez le code que vous avez reçu. Vous êtes redirigé vers une page de connexion.

![navigateur pour entrer code][2]

Une fois que le code a été saisi vous êtes connecté, fermez le navigateur pour continuer sur le scénario.

![correctement connecté][3]

## <a name="switch-to-resource-manager-mode"></a>Basculer vers le Mode directeur des ressources

    azure config mode arm

## <a name="create-the-resource-group"></a>Créer le groupe de ressources

Avant de créer la passerelle d’application, un groupe de ressources est créé pour contenir la passerelle d’application. La figure suivante montre la commande.

    azure group create -n AdatumAppGatewayRG -l eastus

## <a name="create-a-virtual-network"></a>Créer un réseau virtuel

Une fois que le groupe de ressources est créé, un réseau virtuel est créé pour la passerelle d’application.  Dans l’exemple suivant, l’espace d’adressage a été comme 10.0.0.0/16, telle que définie dans les notes du scénario précédent.

    azure network vnet create -n AdatumAppGatewayVNET -a 10.0.0.0/16 -g AdatumAppGatewayRG -l eastus

## <a name="create-a-subnet"></a>Créer un sous-réseau

Une fois le réseau virtuel est créé, un sous-réseau est ajouté pour la passerelle d’application.  Si vous prévoyez d’utiliser passerelle d’application avec une application web hébergée dans le même réseau virtuel que la passerelle d’application, vous devez disposer de suffisamment d’espace pour un autre sous-réseau.

    azure network vnet subnet create -g AdatumAppGatewayRG -n Appgatewaysubnet -v AdatumAppGatewayVNET -a 10.0.0.0/28 

## <a name="create-the-application-gateway"></a>Créer la passerelle d’application

Une fois que le réseau virtuel et sous-réseau sont créés, les composants requis pour la passerelle d’application sont terminées. En outre un certificat précédemment exporté .pfx et le mot de passe pour le certificat sont requises pour l’étape suivante : adresses IP utilisées pour le système principal correspondent aux adresses IP pour votre serveur principal. Ces valeurs peuvent être des adresses IP privée dans le réseau virtuel, adresses IP public ou noms de domaine complet pour vos serveurs principaux.

    azure network application-gateway create -n AdatumAppGateway -l eastus -g AdatumAppGatewayRG -e AdatumAppGatewayVNET -m Appgatewaysubnet -r 134.170.185.46,134.170.188.221,134.170.185.50 -y c:\AdatumAppGateway\adatumcert.pfx -x P@ssw0rd -z 2 -a Standard_Medium -w Basic -j 443 -f Enabled -o 80 -i http -b https -u Standard

> [AZURE.NOTE] Pour obtenir la liste des paramètres qui peuvent être fournis lors de la création, exécutée la commande suivante : **passerelle d’application réseau azure créer--aide**.

Cet exemple crée une passerelle d’application de base avec les paramètres par défaut pour le récepteur, pool principal, paramètres http version serveur et règles. Il configure également déchargement SSL. Vous pouvez modifier ces paramètres pour l’adapter à votre déploiement une fois que la mise en service a réussi.
Si vous avez déjà votre application web défini à l’aide du pool de serveur principal dans les étapes précédentes, une fois créé, l’équilibrage de charge commence.

## <a name="next-steps"></a>Étapes suivantes

Apprenez à créer des sondes état personnalisé en visitant [créer une sonde état personnalisé](application-gateway-create-probe-portal.md)

Découvrez comment configurer SSL décharger et prendre le déchiffrement SSL coûteux désactiver vos serveurs web, visitez [Configurer décharger SSL](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png