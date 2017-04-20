<properties
   pageTitle="Didacticiel : Traiter les factures EDIFACT à l’aide de Services BizTalk Azure | Services BizTalk Microsoft Azure"
   description="Comment créer et configurer l’application de la zone lien ou API et l’utiliser dans une application logique dans le Service d’application Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="msftman"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="deonhe"/>

# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>Didacticiel : Processus EDIFACT factures à l’aide des Services BizTalk Azure
Vous pouvez utiliser le portail de Services BizTalk pour configurer et déployer X12 et accords EDIFACT. Dans ce didacticiel, nous examinons la création d’un contrat EDIFACT pour échanger des factures entre les partenaires. Ce didacticiel écrit autour d’une solution métier de bout en bout impliquant deux partenaires commerciaux, Northwind et Contoso qui échanger des messages EDIFACT.  

## <a name="sample-based-on-this-tutorial"></a>Exemple en fonction de ce didacticiel
Ce didacticiel est écrit autour d’un échantillon, **Envoi EDIFACT factures à l’aide de BizTalk Services**, qui est disponible au téléchargement à partir de la [Galerie de Code MSDN](http://go.microsoft.com/fwlink/?LinkId=401005). Vous pouvez utiliser l’échantillon et utilisez ce didacticiel pour comprendre comment l’exemple a été généré. Ou, vous pouvez utiliser ce didacticiel pour créer votre propre solution sol en haut. Ce didacticiel ciblé sur la deuxième approche afin que vous comprenez comment cette solution a été créée. En outre, autant que possible, le didacticiel est cohérent avec l’échantillon et utilise les mêmes noms d’objets (par exemple, des schémas, des transformations) que celle utilisée dans l’échantillon.  

>[AZURE.NOTE] Étant donné que cette solution implique l’envoi d’un message à partir d’un pont de messages à un pont EDI, il réutilise l’échantillon [BizTalk Services pont chaîne exemple](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) .  

## <a name="what-does-the-solution-do"></a>Quelle est la solution ?

Dans cette solution, Northwind reçoit les factures EDIFACT de Contoso. Ces factures ne sont pas dans un format standard EDI. Par conséquent, avant d’envoyer la facture à Northwind, il doit être transformé à un document de la facture (également appelé INVOIC) EDIFACT. À la réception, Northwind doit traiter la facture EDIFACT et renvoyer un message de contrôle (également appelé commande) avec Contoso.

![][1]  

Pour obtenir ce scénario, Contoso utilise les fonctionnalités fournies avec Microsoft Azure BizTalk Services.

*   Contoso utilise des ponts de messages pour transformer la facture initiale EDIFACT INVOIC.

*   La passerelle de messages puis envoie le message à un pont envoyer EDI déployé dans le cadre d’un accord configuré dans le portail de Services BizTalk.

*   La passerelle d’envoyer EDI traite la EDIFACT INVOIC et itinéraires à Northwind.

*   Après la réception de la facture, Northwind renvoie message d’une commande à la EDI recevoir pont déployé dans le cadre de l’accord.  

> [AZURE.NOTE] Vous pouvez également cette solution montre également comment utiliser le traitement par lots pour envoyer les factures par lots, au lieu d’envoyer chaque facture séparément.  

Pour terminer le scénario, nous utilisons files d’attente Bus des services pour envoyer facture de Contoso au Northwind ou recevoir un accusé de réception à partir de Northwind. Ces files d’attente pouvant être créées à l’aide d’une application cliente, qui est disponible au téléchargement et est incluse dans l’exemple de package est disponible dans le cadre de ce didacticiel.  

## <a name="prerequisites"></a>Conditions préalables

*   Vous devez disposer d’un espace de noms Bus des services. Pour obtenir des instructions sur la création d’un espace de noms, voir [How To : créer ou modifier un Namespace de Service Service Bus](https://msdn.microsoft.com/library/azure/hh674478.aspx). Supposons que vous disposez déjà d’un espace de noms de Service Bus sa mise en service, appelé **edifactbts**.

*   Vous devez posséder un abonnement BizTalk Services. Pour plus d’informations, voir [créer un BizTalk Service à l’aide Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=302280). Dans ce didacticiel, supposons que vous avez un abonnement Services BizTalk, appelé **contosowabs**.

*   Enregistrez votre abonnement BizTalk Services du portail de Services BizTalk. Pour plus d’informations, consultez [inscription d’un déploiement de Service BizTalk sur le portail de Services BizTalk](https://msdn.microsoft.com/library/hh689837.aspx)

*   Vous devez disposer de Visual Studio est installé.

*   Vous devez avoir installé SDK des Services BizTalk. Vous pouvez télécharger le Kit de développement à partir de [http://go.microsoft.com/fwlink/?LinkId=235057](http://go.microsoft.com/fwlink/?LinkId=235057)  

## <a name="step-1-create-the-service-bus-queues"></a>Étape 1 : Créer les files d’attente Bus des services  
Cette solution utilise des files d’attente Bus des services pour échanger des messages entre les partenaires. Contoso et Northwind envoient des messages pour les files d’attente à partir de l’endroit où les messages et/ou EDI ponts consomment. Pour cette solution, vous devez trois files d’attente de Bus de Service :

*   **northwindreceive** – Northwind reçoit la facture de Contoso via cette file d’attente.

*   **contosoreceive** – Contoso reçoit l’accusé de réception de Northwind sur cette file d’attente.

*   **suspendue** – suspendue tous les messages sont routés vers cette file d’attente. Les messages sont suspendus si elles ne pendant le traitement.

Vous pouvez créer ces files d’attente Bus des services à l’aide d’une application cliente incluse dans l’exemple de package.  

1.  À partir de l’emplacement où vous avez téléchargé l’échantillon, ouvrez **Didacticiel envoi factures à l’aide de BizTalk Services EDI Bridges.sln**.

2.  Appuyez sur **F5** pour générer et lancer l’application **Cliente didacticiel** .

3.  Dans l’écran, entrez l’espace de noms, nom de l’émetteur et clé émetteur Service Bus ACS.

    ![][2]  
4.  Un message vous demande que trois files d’attente seront créés dans votre espace de noms Bus des services. Cliquez sur **OK**.

5.  Laissez le Client didacticiel en cours d’exécution. Ouvrir le, cliquez sur **Service Bus** > **_votre espace de noms Bus des services_** > **files d’attente**et vérifiez que les trois files d’attente sont créés.  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>Étape 2 : Créer et déployer contrat partenaire commercial
Créer un contrat partenaire commercial entre Contoso et Northwind. Un contrat partenaire commercial définit un contrat trade entre les deux partenaires, tels que le schéma de message à utiliser le protocole de messagerie à utiliser, etc.. Un contrat partenaire commercial inclut deux ponts EDI, un pour envoyer des messages aux partenaires (appelés le **pont EDI envoyer**) et un pour recevoir des messages à partir de partenaires (appelés le **pont réception EDI**).

Dans le cadre de cette solution, la passerelle envoyer EDI correspond à côté de l’envoi de l’accord et est utilisée pour envoyer la facture EDIFACT de Contoso à Northwind. De même, le pont de réception EDI correspond à la partie de réception de l’accord et est utilisé pour les accusés de réception de Northwind.  

### <a name="create-the-trading-partners"></a>Créer les partenaires commerciaux

Commencez par créer des partenaires pour Contoso et Northwind.  

1.  Dans le portail de Services BizTalk, sous l’onglet **partenaires** , cliquez sur **Ajouter**.

2.  Dans la page Nouveau partenaire, entrez **Contoso** en tant que partenaire nom, puis cliquez sur **Enregistrer**.

3.  Répétez l’étape pour créer le deuxième partenaire, **Northwind**.  

### <a name="create-the-agreement"></a>Créer le contrat
Contrats avec les partenaires commerciaux sont créés entre les profils d’entreprise des partenaires commerciaux. Cette solution utilise les profils de partenaire par défaut qui sont automatiquement créés lorsque nous avons créé les partenaires.  

1.  Dans le portail de Services BizTalk, cliquez sur **accords** > **Ajouter**.

2.  Dans la page **Paramètres généraux** du nouvel accord, spécifiez les valeurs comme indiqué dans l’image ci-dessous, puis cliquez sur **Continuer**.

    ![][3]  

    Après avoir cliqué sur **Continuer**, deux onglets, **Les paramètres recevoir** et **Envoyer** deviennent disponibles.

3.  Créer l’accord envoyer entre Contoso et Northwind. Ce contrat régit comment Contoso envoie la facture EDIFACT à Northwind.

    1.  Cliquez sur **paramètres d’envoi**.

    2.  Conserver les valeurs par défaut dans les onglets **URL entrant**, **transformer**et **Batching** .

    3.  Sous l’onglet **protocole** , sous la section **schémas** , téléchargez le schéma **EFACT_D93A_INVOIC.xsd** . Ce schéma est disponible avec l’exemple de package.

        ![][4]  
    4.  Sous l’onglet **Transport** , spécifiez les détails pour les files d’attente Bus des services. Pour l’accord envoyer côté, nous utilisons la file d’attente **northwindreceive** pour envoyer la facture EDIFACT à Northwind et la file **d’attente** pour acheminer les messages qui échouent pendant le traitement et sont suspendus. Vous avez créé ces files d’attente dans **étape 1 : créer les files d’attente Service Bus** (dans cette rubrique).

        ![][5]  

        Sous **paramètres de Transport > type de Transport** et **Message Suspension Paramètres > type de Transport**, sélectionnez Bus des services Azure et fournir les valeurs comme indiqué dans l’image.

4.  Créer l’accord réception entre Contoso et Northwind. Ce contrat régit comment Contoso reçoit l’accusé de réception de Northwind.

    1.  Cliquez sur **paramètres**.

    2.  Conserver les valeurs par défaut dans les onglets de **Transport** et **transformer** .

    3.  Sous l’onglet **protocole** , sous la section **schémas** , téléchargez le schéma **EFACT_4.1_CONTRL.xsd** . Ce schéma est disponible avec l’exemple de package.

    4.  Sous l’onglet **itinéraire** , créer un filtre pour vous assurer que seuls les accusés de réception à partir de Northwind sont routés vers Contoso. Sous **Paramètres itinéraire**, cliquez sur **Ajouter** pour créer le filtre de routage.

        ![][6]  
        1.  Indiquez les valeurs pour **Nom de la règle**, **acheminer**et **destination de l’itinéraire** , comme illustré dans l’image.

        2.  Cliquez sur **Enregistrer**.

    5.  Sous l’onglet **itinéraire** à nouveau, spécifiez où les accusés de réception suspendus (accusés de réception qui échouent pendant le traitement) sont routés vers. Définir le type de transport à Bus des services Azure, acheminer le type de destination vers **file d’attente**, type d’authentification à la **Signature de l’accès partagé** (sa), fournir la chaîne de connexion associations de sécurité pour l’espace de noms Bus des services, puis entrez le nom de la file d’attente comme **suspendue**.

5.  Pour finir, cliquez sur **déployer** pour déployer le contrat. Notez les points de terminaison où l’envoyer et recevoir des accords déployées.

    *   Sous l’onglet **Paramètres d’envoi** , sous **URL entrant**, notez le point de terminaison. Pour envoyer un message à partir de Contoso à Northwind à l’aide de la passerelle envoyer EDI, vous devez envoyer un message à ce point de terminaison.

    *   Sous l’onglet **Paramètres de réception** , sous **Transport**, notez le point de terminaison. Pour envoyer un message à partir de Northwind à Contoso à l’aide de l’EDI recevoir pont, vous devez envoyer un message à ce point de terminaison.  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>Étape 3 : Créer et déployer le projet BizTalk Services

Dans l’étape précédente, vous déployé l’envoi EDI et recevez des accords à traiter les factures EDIFACT et les accusés de réception. Ces accords peuvent uniquement traiter les messages qui sont conformes au schéma message EDIFACT standard. Toutefois, par le scénario de cette solution, Contoso envoie une facture à Northwind dans un schéma propriétaire interne. Par conséquent, avant que le message est envoyé à la passerelle envoyer EDI, il doit être transformé à partir du schéma interne au schéma facture EDIFACT standard. Le projet BizTalk Services messages cela.

Le projet BizTalk Services, **InvoiceProcessingBridge**, qui transforme le message est également inclus dans le cadre de l’échantillon que vous avez téléchargé. Le projet inclut les objets suivants :

*   **INHOUSEINVOICE. XSD** – schéma de la facture interne est envoyée à Northwind.

*   **EFACT_D93A_INVOIC. XSD** – schéma de la facture EDIFACT standard.

*   **EFACT_4.1_CONTRL. XSD** – schéma de l’accusé de réception EDIFACT Northwind envoie à Contoso.

*   **INHOUSEINVOICE_to_D93AINVOIC. TRFM** – la transformation mappant le schéma de facture en interne au schéma facture EDIFACT standard.  

### <a name="create-the-biztalk-services-project"></a>Créer le projet BizTalk Services
1.  Dans la solution Visual Studio, développez le projet InvoiceProcessingBridge et ouvrez le fichier **MessageFlowItinerary.bcs** .

2.  Cliquez n’importe où dans la zone de dessin et définir l' **URL du Service BizTalk** dans la zone de propriété pour spécifier le nom de votre abonnement BizTalk Services. Par exemple, `https://contosowabs.biztalk.windows.net`.

    ![][7]  
3.  À partir de la boîte à outils, faites glisser un **Pont d’à sens unique Xml** vers la zone de dessin. Définir les propriétés de **Nom de l’entité** et **l’Adresse Relative** du pont à **ProcessInvoiceBridge**. Double-cliquez sur **ProcessInvoiceBridge** pour ouvrir la surface de configuration de passerelle.

4.  Dans la zone **Types de messages** , cliquez sur le signe plus (**+**) pour spécifier le schéma de message entrant. Étant donné que le message entrant pour le pont de messages est toujours la facture interne, attribuez à **INHOUSEINVOICE**.

    ![][8]  
5.  Cliquez sur la forme **Transformer Xml** et dans la zone de propriété pour la propriété **cartes** , cliquez sur le bouton points de suspension (...****). Dans la boîte de dialogue **Sélection des cartes** , sélectionnez le fichier de transformation **INHOUSEINVOICE_to_D93AINVOIC** , puis cliquez sur **OK**.

    ![][9]  
6.  Retournez à **MessageFlowItinerary.bcs**et à partir de la boîte à outils, faites glisser un **Point de terminaison de Service externe bidirectionnel** à droite de la **ProcessInvoiceBridge**. Attribuez à **EDIBridge**sa propriété **Nom de l’entité** .

7.  Dans l’Explorateur de solutions, développez le **MessageFlowItinerary.bcs** et double-cliquez sur le fichier **EDIBridge.config** . Remplacez le contenu de **EDIBridge.config** avec les éléments suivants.

    > [AZURE.NOTE] Pourquoi dois-je modifier le fichier .config ? Le point de terminaison de service externe que nous avons ajouté à la zone du Concepteur de pont représente les ponts EDI que nous avons déployé précédemment. Ponts EDI sont à double sens ponts, avec un envoyer et recevoir des côté. Toutefois, la passerelle de messages que nous avons ajoutés au concepteur pont est un pont à sens unique. Par conséquent, pour gérer les modèles d’échange de message différent des deux ponts, nous utilisons un comportement pont personnalisé en incluant sa configuration dans le fichier .config. En outre, le comportement personnalisé gère également l’authentification au point de terminaison de pont envoyer EDI. Ce comportement personnalisé est disponible en tant qu’un échantillon séparé en [BizTalk Services pont chaîne exemple - messages à EDI](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104). Cette solution réutilise l’échantillon.  
    
    ```
<?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <extensions>
          <behaviorExtensions>
            <add name="BridgeAuthentication" 
                  type="Microsoft.BizTalk.Bridge.Behaviour.BridgeBehaviorElement, Microsoft.BizTalk.Bridge.Behaviour, Version=1.0.0.0, Culture=neutral, PublicKeyToken=ae58f69b69495c05" />
          </behaviorExtensions>
        </extensions>
        <behaviors>
          <endpointBehaviors>
            <behavior name="BridgeAuthenticationConfiguration">
              <!-- Enter the ACS namespace, issuer name and issuer secret of the BizTalk Services deployment -->
              <BridgeAuthentication acsnamespace="[YOUR ACS NAMESPACE]" 
                                    issuername="owner" 
                                    issuersecret="[YOUR ACS SECRET]" />
              <webHttp />
            </behavior>
          </endpointBehaviors>
        </behaviors>
        <bindings>
          <webHttpBinding>
            <binding name="BridgeBindingConfiguration">
              <security mode="Transport" />
            </binding>
          </webHttpBinding>
        </bindings>
        <client>
          <clear />
          <!--
            Go BizTalk Portal > Agreement > Send Settings > Inbound URL
            Copy the Endpoint URL and paste it in the below address field
          -->
          <endpoint name="TwoWayExternalServiceEndpointReference1" 
                    address="[YOUR EDI BRIDGE SEND URI]" 
                    behaviorConfiguration="BridgeAuthenticationConfiguration" 
                    binding="webHttpBinding" 
                    bindingConfiguration="BridgeBindingConfiguration" 
                    contract="System.ServiceModel.Routing.IRequestReplyRouter" />
        </client>
      </system.serviceModel>
    </configuration>

    ```
8.  Mettre à jour le fichier EDIBridge.config pour inclure les détails de configuration

    *   Sous _<behaviors>_, fournissent l’espace de noms ACS et la clé associée à l’abonnement BizTalk Services.

    *   Sous _<client>_, fournissent le point de terminaison où l’accord envoyer EDI est déployé.

    Enregistrer les modifications et fermez le fichier de configuration.

9.  À partir de la boîte à outils, cliquez sur le **lien** et rejoindre les composants **ProcessInvoiceBridge** et **EDIBridge** . Sélectionnez le lien, puis dans zone de propriétés, définissez la **Condition de filtre** pour **Faire correspondre toutes les**. Cela garantit que tous les messages traités par le pont de messages sont routés vers la passerelle EDI.

    ![][10]  
10.  Enregistrer les modifications à la solution.  

### <a name="deploy-the-project"></a>Déployer le projet

1.  Sur l’ordinateur où vous avez créé le projet BizTalk Services, téléchargez et installez le certificat SSL pour votre abonnement BizTalk Services. À partir de, sous Services BizTalk, cliquez sur **tableau de bord**, puis cliquez sur **Télécharger le certificat SSL**. Double-cliquez sur le certificat et suivez l’invite pour terminer l’installation. Veillez à qu'installer le certificat sous magasin de certificats des **Autorités de Certification racine de confiance** .

2.  Dans l’Explorateur de solutions Visual Studio, droit sur le projet **InvoiceProcessingBridge** , puis cliquez sur **déployer**.

3.  Fournir les valeurs, comme le montre l’image, puis cliquez sur **déployer**. Vous pouvez obtenir les informations d’identification ACS pour les Services BizTalk en cliquant sur **Informations de connexion** dans le tableau de bord BizTalk Services.

    ![][11]  

    Dans le volet de sortie, copiez le point de terminaison dans lequel la passerelle de messages est déployée, par exemple, `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`. Vous aurez besoin ultérieurement cette URL de point de terminaison.  

## <a name="step-4-test-the-solution"></a>Étape 4 : Tester la solution


Dans cette rubrique, nous examinons comment tester la solution à l’aide de l’application **Cliente de didacticiel** fournie dans le cadre de l’échantillon.  

1.  Dans Visual Studio, appuyez sur F5 pour démarrer le **Didacticiel Client**.

2.  L’écran doit avoir les valeurs préremplies à partir de l’étape où que nous avons créé les files d’attente Bus des services. Cliquez sur **suivant**.

3.  Dans la fenêtre suivante, fournissent des informations d’identification ACS d’abonnement aux Services BizTalk et les points de terminaison où messages et EDI (recevoir) ponts sont déployés.

    Vous aviez copiée le point de terminaison de pont de messages à l’étape précédente. Pour les standards EDI reçu pont de point de terminaison, dans le portail de Services BizTalk, accédez à l’accord > Paramètres de réception > Transport > point de terminaison.

    ![][12]  
4.  Dans la fenêtre suivante, sous Contoso, cliquez sur le bouton **Envoyer la facture en interne** . Dans le fichier de boîte de dialogue, ouvrez le fichier INHOUSEINVOICE.txt. Examiner le contenu du fichier, puis sur **OK** pour envoyer la facture.

    ![][13]  
5.  En quelques secondes réception de la facture à Northwind. Cliquez sur le lien **Afficher le Message** pour afficher la facture reçue par Northwind. Observez comment la facture reçue par Northwind schéma EDIFACT standard tandis que celui envoyé par Contoso a été un schéma interne.

    ![][14]  
6.  Sélectionnez la facture, puis sur **Envoyer un accusé de réception**. Dans la boîte de dialogue qui s’affiche, notez que l’ID de l’échange est même dans la facture reçue et l’accusé de réception envoyé. Cliquez sur OK dans la boîte de dialogue **Envoyer un accusé de réception** .

    ![][15]  
7.  En quelques secondes, l’accusé de réception a été reçue correctement chez Contoso.

    ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>Étape 5 (facultatif) : facture EDIFACT envoyer par lots 
Ponts BizTalk Services EDI prennent également en charge le traitement par lots de messages sortants. Cette fonctionnalité est utile pour la réception des partenaires qui préfèrent recevoir un lot de messages (critère de réunion) au lieu des messages individuels.

L’aspect le plus important lorsque vous travaillez avec des lots est la version du lot, également appelée les critères de publication. Les critères de publication peuvent être basés sur la manière dont le partenaire destinataire souhaite recevoir des messages. Si le traitement par lots est activé, la passerelle EDI n’envoie pas de message sortant vers le partenaire de réception jusqu'à ce que les critères de publication est remplie. Par exemple, un critère de traitement par lots basé sur message taille interventions un lot uniquement lorsque « n » messages sont regroupés. Critères lot peuvent également être temporelles, telles qu’un lot est envoyé à un moment fixe quotidiennement. Dans cette solution, nous nous efforçons des taille du message en fonction des critères.

1.  Dans le portail de Services BizTalk, cliquez sur le contrat que vous avez créée. Cliquez sur paramètres d’envoi > le traitement par lots > Ajouter lot.

2.  Pour nom de la feuille, entrez **InvoiceBatch**, fournissent une description, puis sur **suivant**.

3.  Spécifier des critères lot, qui définit les messages doivent être regroupés par lots. Dans cette solution, nous lot tous les messages. Par conséquent, sélectionnez l’utilisation avancée des définitions, puis entrez **1 = 1**. Il s’agit d’une condition qui est toujours true, et donc regroupés par lots tous les messages. Cliquez sur **suivant**.

    ![][17]  
4.  Spécifier des critères release lot. Dans la zone de dépôt, sélectionnez **MessageCountBased**et pour **compter**, spécifiez **3**. Cela signifie qu’un lot de trois messages sera envoyé à Northwind. Cliquez sur **suivant**.

    ![][18]  
5.  Consultez le résumé, puis cliquez sur **Enregistrer**. Cliquez sur **déployer** pour redéployez le contrat.

6.  Revenez au **Client didacticiel**, cliquez sur **Envoyer la facture en interne**et suivez les invites pour envoyer la facture. Vous remarquerez qu’aucune facture n’est reçu sur Northwind, car la taille du lot n’est pas remplie. Répétez cette étape deux fois de plus, afin que vous ayez trois messages envoyés à Northwind de la facture. Il satisfait aux critères release lot de 3 messages et vous devez maintenant voir une facture en Northwind.


<!--Image references-->
[1]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-1.PNG  
[2]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-2.PNG  
[3]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-3.PNG
[4]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-4.PNG  
[5]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-5.PNG  
[6]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-6.PNG  
[7]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-7.PNG  
[8]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-8.PNG
[9]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-9.PNG  
[10]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-10.PNG  
[11]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-11.PNG  
[12]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-12.PNG  
[13]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-13.PNG
[14]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-14.PNG  
[15]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-15.PNG  
[16]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-16.PNG  
[17]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-17.PNG  
[18]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-18.PNG

