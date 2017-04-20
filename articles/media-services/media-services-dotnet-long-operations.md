<properties 
    pageTitle="L’interrogation opérations longues | Microsoft Azure" 
    description="Cette rubrique indique comment interroger opérations longues." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="delivering-live-streaming-with-azure-media-services"></a>Exécution d’une diffusion en continu Live avec Azure Media Services

##<a name="overview"></a>Vue d’ensemble

Microsoft Azure Media Services propose des API qui envoient des requêtes aux Services de support pour démarrer des opérations (par exemple : créer, Démarrer, arrêter ou supprimer un canal). Ces opérations sont longues.

Media Services .NET SDK fournit des API envoyez la demande et attendez que l’opération se termine (en interne, les API sont interrogation de l’avancement de l’opération à des intervalles). Par exemple, lorsque vous appelez un canal. Start(), elle renvoie une fois que le canal est démarré. Vous pouvez également utiliser la version asynchrone : attendre canal. StartAsync() (pour plus d’informations sur le modèle asynchrone basé sur une tâche, voir [Appuyez sur](https://msdn.microsoft.com/library/hh873175(v=vs.110).aspx)). API qui envoyer une demande d’opération et puis interrogation de l’état jusqu'à ce que l’opération terminée est appelées « méthodes d’interrogation ». Ces méthodes (notamment la version asynchrone) sont recommandées pour les applications clientes enrichies et/ou les services avec état.

Il existe des scénarios dans lesquels une application ne peuvent pas attendre une demande http longue et souhaite interroger la progression de l’opération manuellement. Voici un exemple type serait un navigateur interaction avec un service web sans état : lorsque le navigateur demande pour créer un canal, le service web démarre une opération longue et renvoie l’ID d’opération au navigateur. Le navigateur peut demander au service web pour obtenir l’état de l’opération en fonction de l’ID. Media Services .NET SDK fournit des API qui sont utiles pour ce scénario. Ces API est appelées « méthodes d’interrogation non ».
Les « méthodes d’interrogation non » ont le modèle d’appellation suivant : envoyer*NomOpération*opération (par exemple, SendCreateOperation). Envoyer les méthodes d’opération*NomOpération*renvoient l’objet **IOperation** ; l’objet retourné contient des informations pouvant être utilisées pour effectuer le suivi de l’opération. Les méthodes d’envoi*NomOpération*OperationAsync retournent **tâche<IOperation>**.

Pour l’instant, les classes suivantes prennent en charge les méthodes de sondage non : **canal**, **StreamingEndpoint**et **programme**.

Pour interroger l’état de l’opération, utilisez la méthode **GetOperation** sur la classe **OperationBaseCollection** . Utiliser les intervalles suivants pour vérifier l’état de l’opération : pour les opérations de **canal** et **StreamingEndpoint** , utilisez 30 secondes ; pour les opérations du **programme** , utilisez les 10 secondes.


##<a name="example"></a>Exemple

L’exemple suivant définit une classe appelée **ChannelOperations**. Cette définition de classe peut être un point de départ pour la définition de classe de service web. Pour simplifier, les exemples suivants utilisent les versions non asynchrone des méthodes.

L’exemple illustre également comment le client peut utiliser ce cours.

###<a name="channeloperations-class-definition"></a>Définition de la classe ChannelOperations

    /// <summary> 
    /// The ChannelOperations class only implements 
    /// the Channel’s creation operation. 
    /// </summary> 
    public class ChannelOperations
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;
    
        public ChannelOperations()
        {
                _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName,
                    _mediaServicesAccountKey);
    
                _context = new CloudMediaContext(_cachedCredentials);    }
    
        /// <summary>  
        /// Initiates the creation of a new channel.  
        /// </summary>  
        /// <param name="channelName">Name to be given to the new channel</param>  
        /// <returns>  
        /// Operation Id for the long running operation being executed by Media Services. 
        /// Use this operation Id to poll for the channel creation status. 
        /// </returns> 
        public string StartChannelCreation(string channelName)
        {
            var operation = _context.Channels.SendCreateOperation(
                new ChannelCreationOptions
                {
                    Name = channelName,
                    Input = CreateChannelInput(),
                    Preview = CreateChannelPreview(),
                    Output = CreateChannelOutput()
                });
    
            return operation.Id;
        }
    
        /// <summary> 
        /// Checks if the operation has been completed. 
        /// If the operation succeeded, the created channel Id is returned in the out parameter.
        /// </summary> 
        /// <param name="operationId">The operation Id.</param> 
        /// <param name="channel">
        /// If the operation succeeded, 
        /// the created channel Id is returned in the out parameter.</param>
        /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
        public bool IsCompleted(string operationId, out string channelId)
        {
            IOperation operation = _context.Operations.GetOperation(operationId);
            bool completed = false;
    
            channelId = null;
    
            switch (operation.State)
            {
                case OperationState.Failed:
                    // Handle the failure. 
                    // For example, throw an exception. 
                    // Use the following information in the exception: operationId, operation.ErrorMessage.
                    break;
                case OperationState.Succeeded:
                    completed = true;
                    channelId = operation.TargetEntityId;
                    break;
                case OperationState.InProgress:
                    completed = false;
                    break;
            }
            return completed;
        }
    
    
        private static ChannelInput CreateChannelInput()
        {
            return new ChannelInput
            {
                StreamingProtocol = StreamingProtocol.RTMP,
                AccessControl = new ChannelAccessControl
                {
                    IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelInput001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
                }
            };
        }
    
        private static ChannelPreview CreateChannelPreview()
        {
            return new ChannelPreview
            {
                AccessControl = new ChannelAccessControl
                {
                    IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelPreview001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
                }
            };
        }
    
        private static ChannelOutput CreateChannelOutput()
        {
            return new ChannelOutput
            {
                Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
            };
        }
    }

###<a name="the-client-code"></a>Le code du client

    ChannelOperations channelOperations = new ChannelOperations();
    string opId = channelOperations.StartChannelCreation("MyChannel001");
    
    string channelId = null;
    bool isCompleted = false;
    
    while (isCompleted == false)
    {
        System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
        isCompleted = channelOperations.IsCompleted(opId, out channelId);
    }
    
    // If we got here, we should have the newly created channel id.
    Console.WriteLine(channelId);
 


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
