<properties 
    pageTitle="Bus des services Azure | Microsoft Azure" 
    description="Introduction à l’utilisation de Bus des services pour connecter des applications Azure vers d’autres logiciels." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/31/2016" 
    ms.author="sethm"/>

# <a name="azure-service-bus"></a>Bus des services Azure

Si une application ou un service s’exécute dans le cloud ou en local, elle doit souvent interagir avec d’autres applications ou services. Pour offrir un moyen largement utile pour ce faire, Microsoft Azure offre Bus des services. Cet article aborde cette technologie décrivant ce qu’il est et pourquoi vous souhaiterez peut-être utiliser.

## <a name="service-bus-fundamentals"></a>Principes de base de Bus des services

Situations d’appels différentes pour les différents styles de communication. Parfois, laissant les applications envoyer et recevoir des messages via une file d’attente simple est la meilleure solution. Dans d’autres situations, une file d’attente ordinaire n’est pas suffisamment ; une file d’attente avec un mécanisme de publication et d’abonnement est préférable. Dans certains cas, tout ce qui est vraiment nécessaire est une connexion entre les applications ; files d’attente ne sont pas obligatoires. Bus des services fournit les trois options, l’activation de vos applications d’interagir de différentes manières.

Bus des services est un service cloud de client multiples, ce qui signifie que le service est partagé par plusieurs utilisateurs. Chaque utilisateur, par exemple, un développeur de l’application crée un *espace de noms*, puis définit les mécanismes de communication qu'elle doit au sein de cet espace de noms. Figure 1 montre à quoi il ressemble.

![][1]
 
**Figure 1 : Bus des services fournit un service client multiples pour connecter des applications dans le cloud.**

Dans un espace de noms, vous pouvez utiliser une ou plusieurs instances de quatre divers mécanismes de communication, chacun d'entre eux connecte à des applications dans une autre façon. Les choix possibles sont :

- *Files d’attente*, qui permettent de communication bidirectionnelle celui. Chaque file d’attente se comporte comme un intermédiaire (parfois appelé un *intermédiaire*) qui stocke les messages envoyés jusqu'à ce qu’ils sont reçus. Chaque message est reçu par un seul destinataire.
- *Rubriques*, qui offrent une communication bidirectionnelle celui à l’aide *d’abonnements*- une seule rubrique peut avoir plusieurs abonnements. Comme une file d’attente, une rubrique se comporte comme un intermédiaire, mais chaque abonnement peut vous pouvez également utiliser un filtre pour recevoir uniquement les messages qui répondent à des critères spécifiques.
- *Relais*, qui offrent une communication bidirectionnelle. Contrairement aux files d’attente et rubriques, un relais ne stockent les messages en vol ; Il n’est pas un agent. Au lieu de cela, il les transmet simplement à l’application de destination.

Lorsque vous créez une file d’attente, une rubrique ou relais, vous lui donner un nom. Combiné avec un autre que vous avez appelé votre espace de noms, ce nom crée un identificateur unique pour l’objet. Applications peuvent fournir ce nom à Bus des services, puis utilisez cette file d’attente, sujet ou relais de communiquer avec eux. 

Pour utiliser un de ces objets dans le scénario de relais, les applications Windows peuvent utiliser Windows Communication Foundation (WCF). Pour les files d’attente et rubriques, Windows applications peuvent utiliser définis par Bus des services de messagerie API. Pour rendre ces objets plus facile à utiliser à partir d’applications non Windows, Microsoft fournit des SDK pour Java, Node.js et d’autres langues. Vous pouvez également accéder files d’attente et rubriques à l’aide des API REST sur HTTP. 

Il est important de comprendre que même si le Service de Bus lui-même s’exécute dans le cloud (autrement dit, dans les centres de données de Microsoft Azure), applications qui l’utilisent anywhere peuvent être exécuté. Vous pouvez utiliser Bus des services pour vous connecter applications qui s’exécutent sur Azure, par exemple, ou des applications qui s’exécutent à l’intérieur de votre propre centre de données. Vous pouvez également l’utiliser pour vous connecter à une application qui s’exécute sur Azure ou une autre plateforme en nuage avec une application locale ou tablettes et téléphones. Il est même possible de connecter des appareils électroménagers, capteurs et autres périphériques à une application centrale ou à un autre. Service Bus est un mécanisme de communication dans le nuage est accessible à partir de pratiquement n’importe où. Comment vous utilisez cela dépend de ce que vos applications doivent faire.

## <a name="queues"></a>Files d’attente

Supposons que vous décidez de connecter deux applications à l’aide d’une file d’attente Bus des services. Figure 2 illustre cette situation.

![][2]
 
**Figure 2 : Files d’attente Bus des services constituent queuing asynchrone à sens unique.**

Le processus est simple : un expéditeur envoie un message à une file d’attente Bus des services, et un récepteur capte ce message ultérieurement. Une file d’attente peut avoir simplement un récepteur unique, comme le montre la Figure 2. Ou bien, plusieurs applications peuvent lire à partir de la même file d’attente. Dans ce dernier cas, chaque message est lu par seul destinataire. Pour un service cast multiples, vous devez utiliser une rubrique à la place.

Chaque message comporte deux parties : un ensemble de propriétés, chaque une paire clé/valeur et corps d’un message binaire. Comment ils sont utilisés varie selon qu’une application essaie de faire. Par exemple, une application qui envoie un message concernant une vente récente peut-être inclure les propriétés *vendeur = « Ava »* et *Montant = 10000*. Le corps du message peut contenir une image numérisée le contrat de vente signé ou, si il n’existe pas, simplement restent vide.

Un destinataire peut lire un message à partir d’une file d’attente Bus des services de deux façons différentes. La première option, appelée *ReceiveAndDelete*, supprime un message de la file d’attente et le supprime immédiatement. Il s’agit de simple, mais si le destinataire se bloque avant la fin de traitement du message, le message seront perdu. Car il a été supprimé de la file d’attente, aucun autre récepteur pour y accéder. 

La deuxième option, *PeekLock*, est conçue pour résoudre ce problème. Comme **ReceiveAndDelete**, une lecture **PeekLock** supprime un message de la file d’attente. Toutefois, il ne supprime le message. Au lieu de cela, il bloque le message, qu’il soit invisible à d’autres destinataires, puis attend un des trois événements :

- Si le destinataire traite le message avec succès, il appelle **achevé**et la file d’attente supprime le message. 
- Si le destinataire décide qu’il ne peut pas traiter le message avec succès, elle appelle **abandonner**. La file d’attente puis supprime le verrou à partir du message et rend disponible pour les autres destinataires.
- Si le destinataire appelle aucune de ces dans un délai configurable (par défaut, 60 secondes), la file d’attente suppose que le destinataire a échoué. Dans ce cas, il se comporte comme si le destinataire avait appelé **abandonner**, rendre le message disponibles aux autres destinataires.

Notez que peut se produire : le même message peut être remis à deux reprises, peut-être à deux récepteurs différents. Applications à l’aide des files d’attente Bus de Service doivent être préparées pour ce problème. Pour faciliter la détection des doublons, chaque message avec une propriété **MessageID** unique qui par défaut reste la même quel que soit l’autant de fois la lecture du message à partir d’une file d’attente. 

Files d’attente sont utiles dans les situations très peu. Ils permettent aux applications de communiquer même lorsque les deux ne sont pas en cours d’exécution en même temps, ce qui s’avère particulièrement utile avec les applications mobiles et lot. Une file d’attente avec plusieurs récepteurs fournit également l’équilibrage de charge automatique, dans la mesure où les messages envoyés sont réparties sur ces récepteurs.

## <a name="topics"></a>Rubriques

Utiles s’ils sont, files d’attente ne sont pas toujours la meilleure solution. Parfois, rubriques Bus des services sont meilleures. Figure 3 illustre cette idée.

![][3]
 
**Figure 3 : Basée sur le filtre spécifie une application abonnée, il peut recevoir tout ou partie des messages envoyés à un sujet de Bus des services.**

Une *rubrique* est similaire de nombreuses façons pour une file d’attente. Messages d’envoi expéditeurs à un sujet de la même façon qu’envoyer des messages vers une file d’attente et ces messages le même aspect que des files d’attente. La différence est que rubriques activer chaque application destinataire créer son propre *abonnement* en définissant un *filtre*. Un abonné s’affiche uniquement les messages qui correspondent à ce filtre. Par exemple, Figure 3 montre un expéditeur et un sujet avec trois d’abonnés, chacune avec sa propre filtre :

- Abonné 1 reçoit seulement les messages qui contiennent la propriété *vendeur = « Ava »*.
- Abonné 2 reçoit les messages qui contiennent la propriété *vendeur = « Ruby »* et/ou contiennent une propriété *Montant* dont la valeur est supérieure à 100 000. Peut-être Ruby est le responsable des ventes, afin qu’elle souhaite voir son propre ventes et toutes les ventes volumineux indépendamment qui crée.
- Abonné 3 a défini son filtre sur *True*, ce qui signifie qu’il reçoit tous les messages. Par exemple, cette application peut être responsable de piste d’audit et par conséquent, il a besoin de voir tous les messages.

Comme avec files d’attente, les abonnés à un sujet peuvent lire les messages à l’aide de **ReceiveAndDelete** ou **PeekLock**. Toutefois, à la différence des files d’attente, un message envoyé à un sujet pouvant être reçu par plusieurs abonnements. Cette approche, c'est-à-dire, *publier et s’abonner* (ou *publication/sub*), est utile lorsque plusieurs applications sont intéressées par les mêmes messages. En définissant le filtre droite, chaque abonné pouvez instantanément uniquement la partie du flux de message qu’il souhaite voir.

## <a name="relays"></a>Relais

Files d’attente et rubriques offrent une communication asynchrone à sens unique par un intermédiaire. Le trafic passe dans les deux sens, et il n’existe aucune connexion directe entre les expéditeurs et destinataires. Mais que se passe-t-il si vous ne voulez pas cela ? Supposons que vos applications ont besoin envoyer et recevoir des messages, ou peut-être que vous souhaitez qu’un lien direct entre elles et vous n’avez pas besoin d’un intermédiaire pour stocker les messages. Pour les scénarios telles que, Bus des services fournit les *transmet*, comme le montre la Figure 4.

![][4]
 
**Figure 4 : Relais Bus des services fournit une communication bidirectionnelle, synchrone entre les applications.**

La question évidente pour demander de relais s’agit-il : quel cas doit-on utiliser une ? Même si je n’avez pas besoin files d’attente, pourquoi rendre les applications communiquer via un service cloud plutôt que simplement interagir directement ? La réponse est que parler directement peut être plus difficile que vous ne pensez.

Supposons que vous voulez vous connecter deux applications en local, les deux exécute à l’intérieur des centres de données d’entreprise. Chacune de ces applications placé derrière un pare-feu, et chaque centre de données utilise probablement traduction des adresses réseau (NAT). Le pare-feu bloque les données entrantes sur les ports quelques et NAT implique que l’ordinateur que s’exécute chaque application n’a pas une adresse IP fixe que vous pouvez atteindre directement à partir d’en dehors du centre de données. Sans une aide supplémentaire, la connexion de ces applications sur l’internet public est problématique.

Un relais Bus de Service peut vous aider. Pour communiquer bidirectionnelle via un relais, chaque application établit une connexion TCP sortante à Service, puis conserve ouverte. Toutes les communications entre les deux applications parcourt ces connexions. Étant donné que chaque connexion a été établie à l’intérieur du centre de données à partir de, le pare-feu autorise le trafic entrant à chaque application sans ouvrir nouveaux ports. Cette approche obtient également contourner le problème NAT, car chaque application possède un point de terminaison cohérente dans le cloud dans l’ensemble de la communication. En échangeant des données via le relais, les applications peuvent éviter les problèmes que dans le cas contraire communication difficile. 

Pour utiliser relais Bus des services, applications s’appuient sur Windows Communication Foundation (WCF). Service Bus fournit des liaisons WCF qui le rendent simple pour les applications Windows interagir via relais. Applications qui utilisent déjà WCF peuvent généralement simplement spécifier l’un de ces liaisons, puis communiquer avec eux via un relais. Contrairement aux files d’attente et rubriques, cependant, à l’aide de relais à partir d’applications non Windows, tout en possible, nécessite des efforts de programmation ; Aucune bibliothèque standard n’est fournis.

Contrairement aux files d’attente et rubriques, applications ne créent pas explicitement relais. En revanche, lorsqu’une application qui souhaite recevoir des messages établit une connexion TCP à Service, un relais est créé automatiquement. Lorsque la connexion est supprimée, le relais est supprimé. Pour activer une application rechercher le relais créé par un récepteur spécifique, Bus des services fournit un Registre qui permet aux applications de localiser un relais spécifique par son nom.

Relais sont la solution idéale lorsque vous avez besoin d’une communication directe entre les applications. Par exemple, considérez un système de réservation de billets d’avion en cours d’exécution dans un centre de données locale doit être accessibles à partir d’archivage bornes, les appareils mobiles et d’autres ordinateurs. Applications en cours d’exécution sur l’ensemble de ces systèmes peuvent s’appuient sur Bus de Service de relais dans le cloud pour communiquer, à l’endroit où ils peuvent exécuter.

## <a name="summary"></a>Résumé

Connecter des applications a toujours été partie de la création des solutions complètes et la plage de scénarios qui requièrent des applications et services pour communiquer avec eux est définie sur augmenter comme davantage d’applications et périphériques sont connectés à Internet. En fournissant des technologies d’assistance sur le nuage d’y parvenir via files d’attente, rubriques et relais, Bus des services a pour but d’apporter cette fonction essentielle plus facile à mettre en œuvre et plus accessibles.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base de Bus des services Azure, suivez ces liens pour en savoir plus.

- Comment utiliser les [files d’attente Bus des services](service-bus-dotnet-get-started-with-queues.md)
- Comment utiliser des [rubriques Bus des services](service-bus-dotnet-how-to-use-topics-subscriptions.md)
- L’utilisation de [relais Bus des services](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md)
- [Exemples de Bus des services](service-bus-samples.md)

[1]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_01_architecture.png
[2]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_02_queues.png
[3]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_03_topicsandsubscriptions.png
[4]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_04_relay.png
