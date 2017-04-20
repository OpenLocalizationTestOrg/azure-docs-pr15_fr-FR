<properties
   pageTitle="Passerelle de données locale | Microsoft Azure"
   description="Une passerelle locale est nécessaire si votre serveur Analysis Services dans Azure doit se connecter aux sources de données locale."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="owend"/>

# <a name="on-premises-data-gateway"></a>Passerelle de données locale

La passerelle de données locale sert de pont, garantissant transfert de données sécurisée entre les sources de données locales et de votre serveur Azure Analysis Services dans le nuage.

Une passerelle est installée sur un ordinateur de votre réseau. Une passerelle doit être installée pour chaque serveur Azure Analysis Services que vous avez dans votre abonnement Azure. Par exemple, si vous avez deux serveurs dans votre abonnement Azure qui se connectent à des sources de données locales, une passerelle doit être installée sur deux ordinateurs distincts de votre réseau.

## <a name="requirements"></a>Configuration requise

**Configuration minimale requise :**

- 4.5 .NET framework
- version 64 bits de Windows 7 / Windows Server 2008 R2 (ou version ultérieure)

**Recommandé :**

- Processeur core 8
- 8 Go de mémoire
- version 64 bits de Windows 2012 R2 (ou version ultérieure)

**Considérations importantes :**

- La passerelle ne peut pas être installée sur un contrôleur de domaine.

- Qu’une seule passerelle peut être installée sur un seul ordinateur.

- Installez la passerelle sur un ordinateur sur lequel est conservé sur et ne se met en veille. Si l’ordinateur n’apparaît pas dans, votre serveur Azure Analysis Services ne peut pas se connecter à vos sources de données locales pour actualiser les données.

- N’installez pas la passerelle sur un ordinateur sans fil connecté à votre réseau. Performances peuvent être réduit.

- Pour modifier le nom du serveur pour une passerelle qui a déjà été configuré, vous devez réinstaller et configurer une nouvelle passerelle.

- Dans certains cas, les modèles tabulaires connexion aux sources de données à l’aide de fournisseurs natifs tels que SQL Server Native Client (SQLNCLI11) peuvent renvoyer une erreur. Pour plus d’informations, voir [connexions de source de données](analysis-services-datasource.md).

## <a name="supported-on-premises-data-sources"></a>Sources de données prises en charge en local
Pour l’aperçu, la passerelle prend en charge les connexions entre votre serveur Azure Analysis Services et les sources de données locales suivantes :

- SQL Server
- Data Warehouse SQL
- POINTS D’ACCÈS
- Oracle
- Teradata


## <a name="download"></a>Télécharger
 [Télécharger la passerelle](https://aka.ms/azureasgateway)


## <a name="install-and-configure"></a>Installer et configurer

1. Exécutez le programme d’installation.

2. Choisissez un emplacement d’installation et acceptez le contrat de licence.

3. Se connecter à Azure.

4. Indiquer le nom de serveur d’analyse Azure. Vous ne pouvez spécifier un serveur par passerelle. Cliquez sur **configurer** et que vous êtes mention.

    ![se connecter à azure](./media\analysis-services-gateway\aas-gateway-configure-server.png)


## <a name="how-it-works"></a>Mode de fonctionnement
La passerelle fonctionne comme un service de Windows, **la passerelle de données en local**, sur un ordinateur dans le réseau de votre organisation. La passerelle que vous installez pour une utilisation avec Azure Analysis Services est basée sur la même passerelle utilisée pour d’autres services, tels que Power BI, mais avec quelques différences dans la manière dont il est configuré.

![Mode de fonctionnement](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Travail flux requêtes et les données comme suit :

1.  Une requête est créée par le service cloud avec les informations d’identification chiffrés pour la source de données locale. Elle est envoyée à une file d’attente de la passerelle à traiter.

2.  Le service en nuage passerelle analyse la requête et envoie la demande au [Bus des services Azure](https://azure.microsoft.com/documentation/services/service-bus/).

3.  La passerelle de données locale consulte le Bus des services Azure pour les demandes en attente.

4.  La passerelle qui obtient la requête, déchiffre les informations d’identification et se connecte aux sources de données avec les informations d’identification.

5.  La passerelle envoie la requête à la source de données pour l’exécution.

6.  Les résultats sont envoyés à partir de la source de données, à la passerelle, puis sur service cloud.

## <a name="windows-service-account"></a>Compte de Service Windows

La passerelle de données locale est configurée pour utiliser *NT SERVICE\PBIEgwService* pour les informations d’identification de connexion de service Windows. Par défaut, il a le droit d’ouverture de session en tant que service ; dans le contexte de l’ordinateur sur lequel vous installez la passerelle sur. Ces informations d’identification ne sont pas le même compte utilisé pour la connexion aux sources de données locales ou votre compte Azure.  

Si vous rencontrez des problèmes avec votre serveur proxy en raison de l’authentification, vous souhaiterez peut-être modifier le compte de service Windows à un utilisateur de domaine ou compte de service géré.

## <a name="ports"></a>Ports

La passerelle crée une connexion sortante à Bus des services Azure. Il communique sur les ports sortants : TCP 443 (par défaut), 5671, 5672, 9350 via 9354.  La passerelle ne nécessite pas de ports d’entrée.

Il est recommandé d’autorisation les adresses IP pour votre région de données dans votre pare-feu. Vous pouvez télécharger la [liste d’adresses IP de centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Cette liste est mis à jour toutes les semaines.

> [AZURE.NOTE]  Les adresses IP répertoriées dans la liste d’adresses IP du centre de données Azure sont en notation CIDR. Par exemple, 10.0.0.0/24 signifie pas 10.0.0.0 via 10.0.0.24. En savoir plus sur la [notation CIDR](http://whatismyipaddress.com/cidr).

Voici les noms de domaine complet utilisés par la passerelle.

|Noms de domaine|Ports de sortie|Description|
|---|---|---|
|*. powerbi.com|80|HTTP permet de télécharger le programme d’installation.|
|*. powerbi.com|443|HTTPS|
|*. analysis.windows.net|443|HTTPS|
|*. login.windows.net|443|HTTPS|
|*. servicebus.windows.net|5671 5672|Message avancés Queuing Protocol (AMQP)|
|*. servicebus.windows.net|443, 9350 9354|Récepteurs sur Service Bus relais sur TCP (nécessite 443 pour l’acquisition de jeton de contrôle d’accès)|
|*. frontend.clouddatahub.net|443|HTTPS|
|*. core.windows.net|443|HTTPS|
|Login.microsoftonline.com|443|HTTPS|
|*. msftncsi.com|443|Permet de tester la connectivité internet si la passerelle n’est pas accessible par le service Power BI.|
|*.microsoftonline p.com|443|Utilisé pour l’authentification en fonction de configuration.|


### <a name="forcing-https-communication-with-azure-service-bus"></a>Forcer la communication HTTPS Service à Azure

Vous pouvez forcer la passerelle pour communiquer à Azure Service à l’aide de HTTPS au lieu de TCP directe ; Toutefois, cela peut réduire les performances. Vous devez modifier le fichier *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* . Modifier la valeur de `AutoDetect` à `Https`. Ce fichier se trouve, par défaut, à *la passerelle de données C:\Program Files\On locaux*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```


## <a name="troubleshooting"></a>Résolution des problèmes
Les paramètres avancés, la passerelle de données locale utilisée pour la connexion Azure Analysis Services pour vos sources de données locale est la même passerelle utilisée avec Power BI.

Si vous rencontrez des difficultés pour installer et configurer une passerelle, veillez à voir [Dépannage de la passerelle BI Power](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem-tshoot/). Si vous pensez que vous rencontrez un problème avec votre pare-feu, reportez-vous aux sections pare-feu ou proxy.

Si vous pensez que vous rencontrez des problèmes de proxy, grâce à la passerelle, consultez [Configuration des paramètres de proxy pour Power BI passerelles](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy.md).

## <a name="next-steps"></a>Étapes suivantes
- [Gérer les Services d’analyse](analysis-services-manage.md)
- [Obtenir des données à partir d’Azure Analysis Services](analysis-services-connect.md)
