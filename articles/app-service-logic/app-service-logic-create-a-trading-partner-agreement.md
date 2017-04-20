<properties 
   pageTitle="Créer un contrat partenaire commercial dans le Service d’application Azure | Microsoft Azure" 
   description="Créer cotation accords partenaire" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
    ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="08/23/2016"
   ms.author="rajram"/>

# <a name="creating-a-trading-partner-agreement"></a>Création d’un contrat partenaire commercial   

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Partenaires sont les entités impliqués dans B2B communications (Business-to-Business). Lorsque deux partenaires établissent une relation, il est appelé un *contrat*. Le contrat défini est basé sur la communication deux partenaires souhaite atteindre et est protocole ou transport spécifique. Les différents protocoles B2B et transports pris en charge par le Service d’application Azure sont les suivantes :

- AS2 (instruction d’application 2)
- EDIFACT (United Nations/Electronic Data Interchange For Administration, Commerce and Transport (UN/EDIFACT))
- X12 (ASC X12)

### <a name="biztalk-api-apps-that-support-b2b-scenarios"></a>Applications de l’API BizTalk qui prennent en charge les scénarios B2B
Les applications de l’API suivant activer ces fonctionnalités à l’aide d’une expérience riche et intuitive dans le portail Azure :


## <a name="biztalk-trading-partner-management-tpm"></a>BizTalk cotation partenaire gestion
- La création et la gestion des partenaires, des profils et des identités
- Stockage et la gestion des schémas EDI
- Stockage et gestion des certificats (utilisé dans AS2 protocol)
- Création et la gestion des accords AS2
- Création et la gestion des accords EDIFACT (y compris le traitement par lots sur le côté de l’envoi)
- Création et gestion de X12 accords (y compris le traitement par lots sur le côté de l’envoi)

![][1]


## <a name="as2-connector"></a>Connecteur AS2
- AS2 accords se déroule comme définie dans l’instance de l’application API plateforme sécurisée connexe
- Informations de traitement/suivi AS2 surfaces de résoudre les problèmes


## <a name="biztalk-edifact"></a>BizTalk EDIFACT
- Accords EDIFACT se déroule comme définie dans l’instance de l’application API plateforme sécurisée connexe
- Informations de traitement/suivi EDIFACT surfaces de résoudre les problèmes
- Fournit la gestion de l’état des lots (start et stop) comme défini dans EDIFACT ou des contrats dans l’instance de l’application API plateforme sécurisée connexe


## <a name="biztalk-x12"></a>BizTalk X12
- Exécute X12 accords, telle que définie dans l’instance de l’application API plateforme sécurisée connexe 
- Surfaces X12 traitement/suivi des informations pour le dépannage
- Fournit la gestion de l’état des lots (start et stop) comme défini dans X12 ou des contrats dans l’instance de l’application API plateforme sécurisée connexe

Comme indiqué auparavant, le AS2, X 12 et EDIFACT API applications nécessitent une application de l’API de plateforme sécurisée de la fonction comme prévu.


## <a name="getting-started"></a>Prise en main
Pour créer des contrats avec les partenaires commerciaux :

1. Créer une instance du connecteur de **Gestion des partenaires commerciaux BizTalk** . Cette fonctionnalité nécessite une base de données SQL vide de la fonction. Avant de démarrer Assurez-vous de disposer d’une base de données vide disponible et prête à être utilisée.
2. Télécharger des schémas et des certificats requis par les accords. Pour ce faire, l’instance de plateforme sécurisée créé et pas à pas détaillé dans la partie « Schémas » et/ou 'Certificats' de navigation
3. Naviguez jusqu'à l’instance de plateforme sécurisée créé et pas à pas détaillé le composant **partenaires**
4. Créer des partenaires comme vous le souhaitez. Modifier le profil selon le cas également et ajouter les identités requises
5. Maintenant, utilisez le composant **accords** pour créer des accords. Lorsque vous créez un contrat, vous devez sélectionner le protocole qui sera utilisé. Les autres options de configuration sont basées sur le protocole que vous avez sélectionné.

![][2]

![][3]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-trading-partner-agreement/TPMResourceView.png
[2]: ./media/app-service-logic-create-a-trading-partner-agreement/ProtocolSelection.png
[3]: ./media/app-service-logic-create-a-trading-partner-agreement/X12AgreementCreation.png
 
