<properties   
    pageTitle="Migration des Solutions EDI BizTalk Server au Guide technique BizTalk Services | Microsoft Azure"
    description="Migrer EDI vers MABS ; Services BizTalk Microsoft Azure"
    services="biztalk-services"
    documentationCenter="na"
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags 
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="mandia"/>


# <a name="migrating-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>Migration des Solutions EDI BizTalk Server aux Services BizTalk : Guide technique

Auteur : Tim Wieman et Nitin Mehrotra

Relecteurs : Karthik Bharthy

Écrites à l’aide : Microsoft Azure BizTalk Services – février 2014 relâchez.

## <a name="introduction"></a>Introduction

EDI Electronic Data Interchange () est un des moyens plus fréquentes par les données exchange entreprises électroniquement, également appelée transactions-commerciaux ou B2B. BizTalk Server a été prise en charge EDI pour plus de dix ans, depuis la version serveur BizTalk initiale. Avec les Services BizTalk, Microsoft continue la prise en charge pour les solutions EDI sur la plateforme Microsoft Azure. Transactions B2B sont principalement externes à une organisation, et, par conséquent, il est plus facile à mettre en œuvre si elle a été implémentée sur une plateforme cloud. Microsoft Azure fournit cette fonctionnalité via les Services BizTalk.

Tandis que certains clients examiner Services BizTalk comme une plateforme « nouveau » pour les nouvelles solutions EDI, de nombreux clients ont des solutions EDI BizTalk Server actuelles souhaite migrer vers Azure. Comme BizTalk Services EDI est conçu basé sur les mêmes entités clées qu’architecture EDI BizTalk Server (cotation partenaires, entités, accords), il est possible de migrer des objets EDI BizTalk Server aux Services BizTalk.

Ce document décrit quelques-unes des différences figurant dans les objets EDI BizTalk Server Migration aux Services BizTalk. Ce document suppose une connaissance de cotation partenaire accords et du traitement EDI BizTalk Server. Pour plus d’informations sur BizTalk Server, voir [Cotation partenaire gestion à l’aide de BizTalk Server](https://msdn.microsoft.com/library/bb259970.aspx).

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>Quelle version d’objets EDI BizTalk Server peut être migrée aux Services BizTalk ?

Le module EDI BizTalk Server a été considérablement amélioré pour BizTalk Server 2010, lorsqu’il a été REMODÉLISATION pour inclure les partenaires, des profils et des accords. Services BizTalk utilise le même modèle pour organiser les partenaires commerciaux et les divisions d’entreprise au sein de ces partenaires. Par conséquent, la migration objets EDI de BizTalk Server 2010 et les versions ultérieures aux Services BizTalk, est un processus beaucoup plus direct. Pour déplacer les objets EDI associés aux versions antérieures au BizTalk Server 2010, vous devez tout d’abord mettre à niveau vers BizTalk Server 2010 et puis migrer vos objets EDI aux Services BizTalk.

## <a name="scenariosmessage-flow"></a>Flux des scénarios/messages

Comme avec BizTalk Server, EDI traitement dans les Services BizTalk repose sur une solution de gestion des partenaires commerciaux (GPC). La solution plateforme sécurisée comporte les composants clés suivants :

- Partenaires, qui représentent des organisation dans une transaction B2B.
- Profils, qui représentent des divisions au sein d’un partenaire.
- Cotation partenaire contrats (ou), qui représentent l’accord entreprise entre deux partenaires/profils.

L’illustration suivante représente les similitudes, ainsi que les différences entre une solution EDI BizTalk Server et BizTalk Services EDI :

![][EDImessageflow]

Les principales différences et similitudes, entre un flux de solution EDI dans BizTalk Server et les Services BizTalk sont :

- Comme BizTalk Server utilise un pipeline EDIReceive pour recevoir un message EDI et un pipeline EDISend pour envoyer un message EDI, Services BizTalk utilise un pont de réception EDI pour recevoir et pont EDI envoyer pour envoyer des messages EDI. Dans BizTalk Server, les pipelines sont associées à un contrat à l’aide envoyer ou recevoir des ports. Dans les Services BizTalk, le contrat lui-même indique l’envoyer ou recevoir pont.
- Dans BizTalk Server, une fois que le pipeline de EDIReceive traite le message EDI, le message est exportée vers une base de données SQL Server. Le pipeline EdiSend puis récupère le message à partir de la base de données SQL Server, il traite et puis envoie au partenaire.

    Dans les Services BizTalk, une fois que l’EDI reçu pont traite le message EDI, il achemine le message vers un processus externe. Le processus externe peut s’exécuter sur Microsoft Azure ou en local. Le processus externe doit acheminer le message à la passerelle envoyer EDI. la passerelle envoyer n’extrait, par nature, le message. Une fois le message, le pont envoyer EDI achemine le message vers le partenaire commercial.

Services BizTalk offre une configuration facile à utiliser pour créer et déployer un accord B2B entre partenaires commerciaux sans configuration tout calculer de Microsoft Azure instances (rôles Web ou de travail), les bases de données Microsoft Azure SQL ou les comptes de stockage Microsoft Azure. Scénarios plus complexes nécessite en la liant dans les flux de travail ou autre traitement service « autour des bords » d’un accord partenaire commercial, c'est-à-dire avant ou après le traitement de pont cotation partenaire contrat EDI. Dans le détail, les séquences d’événements suivants se produisent pendant un message EDI traitement dans les Services BizTalk.

1. Un message EDI provenant de partenaire, Fabrikam commercial.  Pour recevoir des messages EDI à partir de partenaires, Services BizTalk prend en charge les protocoles de transport comme FTP, SFTP, AS2 et HTTP/S.

2. Traitement côté réception accord partenaire commercial que l’ensemble du message EDI au format XML.  Vous pouvez router le message EDI code machine (au format XML) aux points de terminaison de Service Bus comme un point de terminaison du Service Bus relais, Service Bus rubrique, file d’attente de Service Bus ou un pont BizTalk Services.

3. Les messages XML code machine peuvent ensuite être reçus du point de terminaison pour traitement complémentaire personnalisé.  Ces points de terminaison peuvent être traités par un composant local ou d’une instance de Microsoft Azure calculer pour poursuivre le traitement le message dans un service Windows Communication Foundation (WCF) ou de flux de travail WF (Windows), par exemple.

4. Le « traitement côté envoi » du partenaire commercial contrat puis assemble le message XML au format EDI et envoie au partenaire commercial, Contoso.  Pour envoyer des messages EDI aux partenaires, Services BizTalk prend en charge les mêmes protocoles que ceux qui sont utilisés pour la réception des messages EDI.

Ce document davantage fournit une aide conceptuelle sur la migration parmi les différents objets EDI BizTalk Server aux Services BizTalk.

## <a name="sendreceive-ports-to-trading-partners"></a>Envoi/réception Ports aux partenaires

Dans BizTalk Server vous définissez les emplacements de réception et les Ports de réception pour recevoir des messages EDI/XML à partir de partenaires et vous configurer les Ports d’envoi pour envoyer des messages EDI/XML vers partenaire commercial. Vous encombrer puis ces ports pour un contrat partenaire commercial à l’aide de la console Administration de BizTalk Server. Dans les Services BizTalk, les emplacements où vous recevez des messages de partenaires et où vous envoyez des messages à des partenaires commerciaux sont configurés dans le cadre de l’accord partenaire commercial lui-même (dans le cadre des paramètres de Transport) dans le portail de Services BizTalk.  Donc vraiment ne pas le concept de « ports d’envoi » et « emplacements de réception », par leur nature, dans les Services BizTalk. Pour plus d’informations, voir [Création accords](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx).

## <a name="pipelines-bridges"></a>Canalisations (ponts)

Dans EDI BizTalk Server, les pipelines sont entités de traitement de message qui peuvent également inclure une logique personnalisée pour les fonctionnalités de traitement spécifique comme requis par l’application. Pour les Services BizTalk, l’équivalent serait un pont EDI. Toutefois dans les Services BizTalk, pour l’instant, les ponts EDI sont « fermés ».  En d’autres termes, vous ne pouvez pas ajouter votre propre activités personnalisées à un pont EDI. Tout traitement personnalisé doit être effectuée à l’extérieur de la passerelle EDI dans votre application, avant ou après que le message accède à la passerelle configurée dans le cadre de l’accord partenaire commercial. Ponts de messages ont la possibilité d’effectuer un traitement personnalisé. Si vous souhaitez un traitement personnalisé, vous pouvez utiliser des ponts de messages avant ou après que le message est traité par le pont EDI. Pour plus d’informations, voir [comment inclure le Code personnalisé dans ponts](https://msdn.microsoft.com/library/azure/dn232389.aspx).

Vous pouvez insérer un flux de publication/abonnement avec code personnalisé et/ou à l’aide de Bus des services de messagerie files d’attente et rubriques avant l’accord partenaire commercial reçoit le message, ou après que l’accord traite le message et dirige vers un point de terminaison du Service Bus.

Consultez **Le flux des messages/scénarios** dans cette rubrique pour le modèle de flux de message.

## <a name="agreements"></a>Accords

Si vous êtes habitué à BizTalk Server 2010 échanger partenaire accords utilisé pour le traitement EDI, Services BizTalk cotation accords partenaire aspect très familiers. La plupart des paramètres de contrat est identiques et utiliser la même terminologie. Dans certains cas, les paramètres d’accord sont beaucoup plus simples par rapport aux mêmes paramètres dans BizTalk Server. Prend en charge de Microsoft Azure BizTalk Services X12 EDIFACT et AS2 transportent.

Microsoft Azure BizTalk Services fournit également un outil de **Migration de données de plateforme sécurisée** pour migrer des partenaires et des accords commerciaux de module BizTalk Server cotation partenaire au portail de Services BizTalk. L’outil de Migration de données de plateforme sécurisée n’est disponible dans le cadre d’un package d’outils, qui peut être téléchargé à partir du [Kit de développement logiciel MABS](http://go.microsoft.com/fwlink/p/?LinkId=235057). Le package inclut également un fichier Lisezmoi qui fournit des instructions sur l’utilisation de l’outil et informations de dépannage de base pour l’outil.

## <a name="schemas"></a>Schémas

Services BizTalk fournit des schémas EDI qui peuvent être utilisés dans les solutions de Services BizTalk.  En outre, schémas de EDI BizTalk Server peuvent également être utilisés avec les Services BizTalk, car le nœud racine du schéma EDI est identique à BizTalk Server, ainsi que les Services BizTalk. Par conséquent, vous pourrez directement prendre vos schémas EDI BizTalk Server et les utiliser dans les solutions EDI que vous développez à l’aide des Services BizTalk. Vous pouvez également télécharger les schémas depuis le [Kit de développement logiciel MABS](http://go.microsoft.com/fwlink/p/?LinkId=235057).

## <a name="maps-transforms"></a>Cartes (transformations)

Cartes dans BizTalk Server sont appelées transformations dans les Services BizTalk. Cartes de migration sur le serveur BizTalk aux Services BizTalk peuvent être une des tâches plus complexes pour atteindre (selon la complexité de carte). L’outil de mappage utilisé pour les Services BizTalk est différent de BizTalk mapper. Même si la mapper principalement le même aspect, le format de carte sous-jacent est différent. Les fonctoids (appelée **Carte opérations** dans les Services BizTalk) disponibles pour les utilisateurs sont également différentes.  En fait, vous ne pouvez pas utiliser directement un mappage BizTalk dans les Services BizTalk. En outre, pas tous les fonctoids disponibles dans BizTalk Server sont disponibles en tant qu’opérations de carte dans les Services BizTalk.

### <a name="new-transform-operations"></a>Nouvelles opérations de transformation

Lorsque la liste mappage d’opérations de transformation disponibles peut sembler très différente sur le mapper BizTalk Server, BizTalk Services transforme avez-vous nouvelles méthodes pour accomplir les mêmes tâches. Par exemple, des transformations de Services BizTalk avoir **Liste opérations** disponibles. Cela n’était pas disponible dans BizTalk mapper.  Les **Opérations de liste** permettent de créer et utiliser une « liste », où une liste est un ensemble d’éléments (également appelé « lignes ») et où chaque élément peut avoir plusieurs membres (également appelé « colonnes »).  Vous pouvez trier la liste, sélectionnez les éléments selon une condition, etc..

Un autre exemple de nouvelles fonctionnalités dans des transformations de Services BizTalk sont les **Opérations de boucle**.  Il est difficile de créer des boucles imbriquées dans le mapper BizTalk Server.  Par conséquent, les opérations de carte de boucle sont ajoutées pour les Services BizTalk des transformations.

Encore un autre exemple est l’opération de carte Expression **If-Then-Else** .  Effectuer une opération if-then-else était possible dans BizTalk mapper, mais il obligatoire plusieurs fonctoids pour l’accomplissement d’une tâche apparemment simple.

### <a name="migrating-biztalk-server-maps"></a>Cartes de migration BizTalk Server

Microsoft Azure BizTalk Services fournit un outil de migration BizTalk Server mappe aux Services BizTalk transformations. Le **BTMMigrationTool** est disponible dans le cadre du package des **Outils** fourni avec le [SDK des Services BizTalk télécharger](http://go.microsoft.com/fwlink/p/?LinkId=235057). Pour plus d’informations sur l’outil, voir [convertir un mappage BizTalk pour un transformer les Services BizTalk](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx).

Vous pouvez également consulter un échantillon par Sandro Pereira, MVP BizTalk, comment [migrer des cartes BizTalk Server aux Services BizTalk transformations](http://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx).

## <a name="orchestrations"></a>Orchestrations

Si vous avez besoin migrer orchestration BizTalk Server traitement à Microsoft Azure, les orchestrations nécessaires pour être réécrits, car Microsoft Azure ne prend pas en charge les orchestrations BizTalk Server en cours d’exécution.  Vous pouvez la réécriture de la fonctionnalité orchestration dans un service Windows Workflow Foundation 4.0 (WF4).  Il s’agit une réécriture complète car il n’existe actuellement aucune migration d’orchestrations BizTalk Server vers WF4. Voici quelques ressources du flux de travail de Windows :

- [*Comment intégrer un Service de flux de travail WCF avec files d’attente de Service Bus et rubriques*](https://msdn.microsoft.com/library/azure/hh709041.aspx) par Paolo Salvatori. 

- [ *Création d’applications avec Windows Workflow Foundation et Azure* session](http://go.microsoft.com/fwlink/p/?LinkId=237314) à partir de la conférence Build 2011.

- [*Centre de développement Windows Workflow Foundation*](http://go.microsoft.com/fwlink/p/?LinkId=237315) sur MSDN.

- [*Documentation de Windows Workflow Foundation 4 (WF4)*](https://msdn.microsoft.com/library/dd489441.aspx) sur MSDN.

## <a name="other-considerations"></a>Autres considérations

Voici quelques considérations que vous devez effectuer lors de l’utilisation des Services BizTalk.

### <a name="fallback-agreements"></a>Contrats de secours

Traitement EDI BizTalk Server comporte la notion de « Secours accords ».  Services BizTalk effectuent **pas** ont un concept de secours contrat jusqu'à présent.  Voir les rubriques de la documentation BizTalk [Rôle d’accords dans traitement EDI](http://go.microsoft.com/fwlink/p/?LinkId=237317) et [configuration globale ou propriétés de l’accord de secours](https://msdn.microsoft.com/library/bb245981.aspx) pour plus d’informations sur l’utilisation des contrats de secours dans BizTalk Server.

### <a name="routing-to-multiple-destinations"></a>Routage vers plusieurs destinations

Ponts Services BizTalk, dans son état actuel ne prend pas en charge routage des messages à plusieurs destinataires à l’aide de publier une version-s’abonner modèle. À la place vous pourriez routage des messages d’un pont de Services BizTalk à un sujet de Bus des services, lequel peut ensuite établir plusieurs abonnements à recevoir le message à plus d’un point de terminaison.

## <a name="conclusion"></a>Conclusion

Microsoft Azure BizTalk Services est mis à jour en jalons normales pour ajouter davantage de fonctionnalités et fonctionnalités. Chaque mise à jour, nous espérons prenant en charge des fonctionnalités améliorées pour faciliter la création des solutions de bout en bout à l’aide des Services BizTalk et autres technologies Azure.

## <a name="see-also"></a>Voir aussi

[Développement d’Applications d’entreprise avec Azure](https://msdn.microsoft.com/library/azure/hh674490.aspx)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png
