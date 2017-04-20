<properties
   pageTitle="Surveiller les opérations, les événements et compteurs pour NSGs | Microsoft Azure"
   description="Apprenez à activer des compteurs, événements et journalisation opérationnelle pour NSGs"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/14/2016"
   ms.author="jdial" />

#<a name="log-analytics-for-network-security-groups-nsgs"></a>Journal analytique pour les groupes de sécurité réseau (NSGs)

Vous pouvez utiliser différents types de journaux dans Azure pour gérer et dépanner NSGs. Certaines de ces journaux sont accessibles via le portail et tous les journaux peuvent être extraits à partir d’un stockage d’objets blob Azure et affichés dans différents outils, tels que [Journal Analytique](../log-analytics/log-analytics-azure-networking-analytics.md), Excel et PowerBI. Vous pouvez en savoir plus sur les différents types de journaux dans la liste ci-dessous.

- **Des journaux d’audit :** Vous pouvez utiliser [Les journaux d’Audit Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (auparavant appelé journaux opérationnelles) pour afficher toutes les opérations envoyées vers vos abonnements Azure et leur statut. Journaux d’audit sont activées par défaut et peuvent être affichés dans le portail Azure preview.
- **Les journaux d’événements :** Vous pouvez utiliser ce journal pour afficher les règles NSG sont appliqués aux machines virtuelles et rôles instance basés sur adresse MAC. L’état de ces règles est collectée toutes les 60 secondes.
- **Journaux de compteur :** Vous pouvez utiliser ce journal pour afficher le nombre de fois chaque règle NSG a été appliquée pour refuser ou autoriser le trafic.

>[AZURE.WARNING] Les journaux ne sont disponibles pour les ressources déployées dans le modèle de déploiement du Gestionnaire de ressources. Vous ne pouvez pas utiliser les journaux des ressources dans le modèle de déploiement classique. Pour mieux comprendre les deux modèles, faire référence à l’article [déploiement comprendre le Gestionnaire de ressources et déploiement classique](../resource-manager-deployment-model.md) .

##<a name="enable-logging"></a>Activer la journalisation
Enregistrement d’audit est activée automatiquement à tout moment pour chaque ressource Gestionnaire de ressources. Vous devez activer les événements et compteur de journalisation pour commencer à collecter les données disponibles via les journaux. Pour activer la journalisation, suivez les étapes ci-dessous.

1.  Connectez-vous au [portail Azure](https://portal.azure.com). Si vous n’avez pas un groupe de sécurité réseau existant, [créez un NSG](virtual-networks-create-nsg-arm-ps.md) avant de poursuivre.

2.  Dans le portail d’aperçu, cliquez sur **Parcourir** >> **groupes de sécurité réseau**.

    ![Portail Preview - groupes de sécurité réseau](./media/virtual-network-nsg-manage-log/portal-enable1.png)

3. Sélectionnez un groupe de sécurité réseau existant.

    ![Portail Preview - paramètres de groupe de sécurité réseau](./media/virtual-network-nsg-manage-log/portal-enable2.png)

4. Dans la carte de **paramètres** , cliquez sur **Diagnostics**et puis, dans le volet des **Diagnostics** , en regard de l' **état**, cliquez **sur**
5. Dans la carte de **paramètres** , cliquez sur **Compte de stockage**, puis sélectionnez un stockage existant du compte, ou créez-en une.  

>[AZURE.INFORMATION] journaux d’Audit ne nécessitent pas un compte de stockage distincte. L’utilisation du stockage des événements et journalisation règle implique des frais de service.

6. Dans la liste déroulante sous **Compte de stockage**, indiquez si vous souhaitez connecter des événements, des compteurs ou les deux, puis cliquez sur **Enregistrer**.

    ![Portail Preview - journaux des Diagnostics](./media/virtual-network-nsg-manage-log/portal-enable3.png)

## <a name="audit-log"></a>Journal d’audit
Ce journal (auparavant appelé « le journal opérationnel ») est généré par Azure par défaut.  Les journaux sont conservées pendant 90 jours Store des journaux d’événements d’Azure. Apprenez-en davantage sur ces journaux en lisant l’article [Afficher les événements et les journaux d’audit](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="counter-log"></a>Journal de compteur
Ce journal est généré uniquement si vous l’avez activé sur une base par NSG comme décrit ci-dessus. Les données sont stockées dans le compte de stockage que vous avez spécifié lorsque vous avez activé la journalisation. Chaque règle soit appliquée à des ressources est enregistré au format JSON, comme indiqué ci-dessous.

    {
        "time": "2015-09-11T23:14:22.6940000Z",
        "systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
        "category": "NetworkSecurityGroupRuleCounter",
        "resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
        "operationName": "NetworkSecurityGroupCounters",
        "properties": {
            "vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
            "subnetPrefix":"10.0.0.0/24",
            "macAddress":"001517D9C43C",
            "ruleName":"DenyAllOutBound",
            "direction":"Out",
            "type":"block",
            "matchedConnections":0
            }
    }

## <a name="event-log"></a>Journal des événements
Ce journal est généré uniquement si vous l’avez activé sur une base par NSG comme décrit ci-dessus. Les données sont stockées dans le compte de stockage que vous avez spécifié lorsque vous avez activé la journalisation. Les données suivantes sont enregistrées :

    {
        "time": "2015-09-11T23:05:22.6860000Z",
        "systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
        "category": "NetworkSecurityGroupEvent",
        "resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
        "operationName": "NetworkSecurityGroupEvents",
        "properties": {
            "vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
            "subnetPrefix":"10.0.0.0/24",
            "macAddress":"001517D9C43C",
            "ruleName":"AllowVnetOutBound",
            "direction":"Out",
            "priority":65000,
            "type":"allow",
            "conditions":{
                "destinationPortRange":"0-65535",
                "sourcePortRange":"0-65535",
                "destinationIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32",
                "sourceIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32"
            }
        }
    }

## <a name="view-and-analyze-the-audit-log"></a>Afficher et analyser le journal d’audit
Vous pouvez afficher et analyser les données du journal d’audit à l’aide d’une des méthodes suivantes :

- **Outils azure :** Récupérer des informations à partir des journaux d’audit via PowerShell Azure, l’Interface de ligne de commande Azure (CLI), l’API REST Azure ou le portail Azure preview.  Des instructions détaillées pour chaque méthode sont détaillées dans l’article [opérations d’Audit avec le Gestionnaire de ressources](../resource-group-audit.md) .
- **Power BI :** Si vous n’avez pas un compte de [Power BI](https://powerbi.microsoft.com/pricing) , vous pouvez l’essayer gratuitement. À l’aide du [pack pour Power BI du contenu des journaux d’Audit de Azure](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/) vous pouvez analyser vos données avec des tableaux de bord préconfiguré que vous pouvez utiliser en tant que-, ou personnaliser.

## <a name="view-and-analyze-the-counter-and-event-log"></a>Afficher et analyser le compteur et le journal des événements

Azure [Journal Analytique](../log-analytics/log-analytics-azure-networking-analytics.md) recueille le compteur et journal des événements des fichiers à partir de votre compte de stockage Blob et inclut des visualisations et puissantes fonctionnalités de recherche pour analyser vos journaux.

Vous pouvez également vous connecter à votre compte de stockage et récupérer les entrées du journal JSON pour les journaux d’événements et compteur. Une fois que vous téléchargez les fichiers JSON, vous pouvez les convertir en CSV et les afficher dans Excel, PowerBI ou tout autre outil de visualisation des données.

>[AZURE.TIP] Si vous êtes habitué à Visual Studio et concepts de base de la modification des valeurs de constantes et de variables en c#, vous pouvez utiliser les [journaux convertisseur outils](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibles sur Github.

## <a name="next-steps"></a>Étapes suivantes

- Visualiser compteur et les journaux d’événements avec [Journal Analytique](../log-analytics/log-analytics-azure-networking-analytics.md)
- Billet de blog [visualiser vos journaux d’Audit Azure avec Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) .
- [Afficher et analyser les journaux d’Audit Azure dans Power BI et bien plus encore](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) billet de blog.
