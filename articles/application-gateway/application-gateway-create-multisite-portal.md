<properties
   pageTitle="Configurer une passerelle d’application existant pour l’hébergement de plusieurs sites dans le portail Azure | Microsoft Azure"
   description="Cette page fournit des instructions pour configurer une passerelle d’application Azure existant pour l’hébergement de plusieurs applications web sur la même passerelle grâce au portail Azure."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>


# <a name="configure-an-existing-application-gateway-for-hosting-multiple-web-applications"></a>Configurer une passerelle d’application existant pour l’hébergement de plusieurs applications web

> [AZURE.SELECTOR]
- [Portail Azure](application-gateway-create-multisite-portal.md)
- [Azure PowerShell Gestionnaire de ressources](application-gateway-create-multisite-azureresourcemanager-powershell.md)

Hébergement de plusieurs sites vous permet de déployer plus d’une application web sur la même passerelle d’application. Il s’appuie sur la présence d’en-tête de l’hôte dans la demande HTTP entrante, afin de déterminer quel récepteur reçoit le trafic. Que l’auditeur dirige ensuite le trafic vers principaux appropriés pool tel que configuré dans la définition des règles de la passerelle. Dans les applications web SSL est activé, passerelle d’application dépend de l’extension Indication de nom de serveur (SNI) pour choisir le récepteur approprié pour le trafic web. Souvent utilisée pour l’hébergement de plusieurs sites consiste à charger des demandes de solde pour les domaines web différentes pour des pools autre serveur principal. De même plusieurs sous-domaines du domaine racine même pourraient également être hébergés sur la même passerelle d’application.

## <a name="scenario"></a>Scénario

Dans l’exemple suivant, passerelle d’application sert le trafic de contoso.com et fabrikam.com avec deux pools serveur principal : contoso serveur pool et pool de serveur de fabrikam. Le programme d’installation similaire peut servir à sous-domaines hôte comme app.contoso.com et blog.contoso.com.

![scénario multisite][multisite]

## <a name="before-you-begin"></a>Avant de commencer

Ce scénario prend en charge de plusieurs sites pour une passerelle d’application existant. Pour effectuer ce scénario scénario une passerelle d’application existant doit être disponibles pour configurer. Visitez [créer une passerelle d’application à l’aide du portail](./application-gateway-create-gateway-portal.md) pour apprendre à créer une passerelle d’application de base dans le portail.

## <a name="requirements"></a>Configuration requise

- **Pool du serveur principal :** La liste d’adresses IP des serveurs principale. Présente les adresses IP doivent appartenir soit au sous-réseau virtuel ou doivent être un IP/VIP publique. Nom de domaine complet peut également être utilisé.
- **Paramètres du pool serveur principal :** Chaque pool inclut des paramètres, tels qu’affinité basée sur les cookies, port et protocole. Ces paramètres sont liés à un pool et sont appliquées à tous les serveurs au sein du pool.
- **Port frontal :** Ce port est le port public est ouvert sur la passerelle d’application. Le trafic appuie sur ce port et puis redirigé à un des serveurs principale.
- **Récepteur :** Le récepteur inclut un port frontal, un protocole (Http ou Https, ces valeurs respectent la casse) et le nom du certificat SSL (si la configuration de SSL décharge). Pour les passerelles application activée sur plusieurs sites, nom d’hôte et indicateurs SNI sont également ajoutées.
- **Règle :** La règle lie le récepteur, le pool de serveur principal et définit le pool de serveur principal le trafic doit être dirigé vers lorsqu’il rencontre un récepteur particulier.
- **Certificats :** Chaque récepteur nécessite un certificat unique, dans cet exemple 2 récepteurs sont créés pour plusieurs sites. Deux certificats .pfx et les mots de passe pour les doivent être créées.

## <a name="create-an-application-gateway"></a>Créer une passerelle d’application

Voici les étapes nécessaires pour mettre à jour la passerelle d’application :

1. Créez des pools principale à utiliser pour chaque site.
2. Créer un pour chaque passerelle d’application site prend en charge.
3. Créer des règles pour mapper chaque récepteur avec le serveur principal approprié.

## <a name="create-back-end-pools-for-each-site"></a>Créer des pools principale pour chaque site

Un pool principale pour chaque site passerelle application sera prise en charge est nécessaire, dans ce cas 2 est créé, une pour contoso11.com et l’autre pour fabrikam11.com.

### <a name="step-1"></a>Étape 1

Accédez à une passerelle d’application existant dans le portail Azure (https://portal.azure.com). Sélectionnez **pools principal** , cliquez sur **Ajouter**

![ajouter des pools principal][7]

### <a name="step-2"></a>Étape 2

Renseignez les informations relatives à principale pool **pool1**ajoutant les adresses ip ou les noms de domaine complets pour les serveurs de données principale, cliquez sur **OK**

![paramètres du serveur principal pool pool1][8]

### <a name="step-3"></a>Étape 3

Dans la carte des pools et cliquez sur **Ajouter** pour ajouter un pool principale supplémentaires **pool2**, ajouter les adresses ip ou les noms de domaine complets pour les serveurs principaux et cliquez sur **OK**

![paramètres du serveur principal pool pool2][9]

### <a name="create-listeners-for-each-back-end"></a>Créer des récepteurs pour chaque principale

Application passerelle repose sur les en-têtes d’hôte HTTP 1.1 pour héberger plusieurs sites Web dans la même adresse IP public et le port. Le récepteur de base créé dans le portail ne contient pas cette propriété.

### <a name="step-1"></a>Étape 1

Cliquez sur **récepteurs** sur la passerelle d’application existant, puis cliquez sur **plusieurs sites** pour ajouter le premier récepteur.

![carte de vue d’ensemble de récepteurs][1]

### <a name="step-2"></a>Étape 2

Remplissez les informations pour le récepteur, dans cet exemple arrêt de SSL est configuré, créez un nouveau port frontend. Téléchargez le certificat .pfx à utiliser pour les frais de résiliation SSL. La seule différence sur cette carte par rapport à la carte récepteur de base standard est le nom d’hôte.

![carte de propriétés récepteur][2]

### <a name="step-3"></a>Étape 3

Cliquez sur **plusieurs sites** et créer un autre récepteur comme décrit dans l’étape précédente pour le deuxième point. Vérifiez que vous utilisez un certificat autre que l’auditeur deuxième. La seule différence sur cette carte par rapport à la carte récepteur de base standard est le nom d’hôte. Renseignez les informations pour le récepteur, puis cliquez sur **OK**.

![carte de propriétés récepteur][3]

> [AZURE.NOTE] Création de processus d’écoute dans Azure portal pour la passerelle d’application est une tâche longue, cela peut prendre quelques instants pour créer les deux récepteurs dans ce scénario. Une fois terminée l’écoute de la présentation dans le portail comme illustré dans l’image suivante.

![vue d’ensemble récepteur][4]

### <a name="create-rules-to-map-listeners-to-backend-pools"></a>Créer des règles pour établir une correspondance entre récepteurs pools d’arrière-plan

### <a name="step-1"></a>Étape 1

Accédez à une passerelle d’application existant dans le portail Azure (https://portal.azure.com). Sélectionnez **les règles** et choisissez la règle d’existant par défaut **rule1** , puis cliquez sur **Modifier**.

### <a name="step-2"></a>Étape 2

Remplissez la carte de règles, comme illustré dans l’image suivante. Choisissez le premier récepteur et le premier pool et sur **Enregistrer** lorsque vous avez terminé.

![modifier la règle existante][6]

### <a name="step-3"></a>Étape 3

Cliquez sur la **règle de base** pour créer la règle 2ème. Remplissez le formulaire avec le deuxième récepteur et le deuxième pool de serveur principal, puis cliquez sur **OK** pour enregistrer.

![ajouter la carte de la règle de base][10]

Cela termine la configuration d’une passerelle d’application existant avec prise en charge de plusieurs sites via le portail d’Azure.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment protéger vos sites Web avec [Application passerelle - pare-feu pour applications Web](application-gateway-webapplicationfirewall-overview.md)

<!--Image references-->
[1]: ./media/application-gateway-create-multisite-portal/figure1.png
[2]: ./media/application-gateway-create-multisite-portal/figure2.png
[3]: ./media/application-gateway-create-multisite-portal/figure3.png
[4]: ./media/application-gateway-create-multisite-portal/figure4.png
[5]: ./media/application-gateway-create-multisite-portal/figure5.png
[6]: ./media/application-gateway-create-multisite-portal/figure6.png
[7]: ./media/application-gateway-create-multisite-portal/figure7.png
[8]: ./media/application-gateway-create-multisite-portal/figure8.png
[9]: ./media/application-gateway-create-multisite-portal/figure9.png
[10]: ./media/application-gateway-create-multisite-portal/figure10.png
[multisite]: ./media/application-gateway-create-multisite-portal/multisite.png