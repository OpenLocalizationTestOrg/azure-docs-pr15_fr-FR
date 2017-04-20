<properties
    pageTitle="Comment utiliser le stockage de file d’attente à partir de Python | Microsoft Azure"
    description="Découvrez comment utiliser le service de file d’attente Azure à partir de Python pour créer et supprimer des files d’attente et insérer, obtenir et supprimer des messages."
    services="storage"
    documentationCenter="python"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-queue-storage-from-python"></a>Comment utiliser le stockage de Python file d’attente

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Vue d’ensemble

Ce guide vous montre comment effectuer des scénarios courants au moyen du service de stockage Azure file d’attente. Les exemples sont écrits dans Python et utilisent le [Kit de développement de Microsoft Azure stockage pour Python]. Les scénarios présentés incluent **Insertion**, **lecture**, **prise**et **Supprimer des** messages de file d’attente, ainsi que **créer et supprimer des files d’attente**. Pour plus d’informations sur les files d’attente, reportez-vous à la section [étapes suivantes].

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="how-to-create-a-queue"></a>Comment : Créer une file d’attente

L’objet **QueueService** vous permet de travailler avec des files d’attente. Le code suivant crée un objet **QueueService** . Ajoutez le code suivant dans la partie supérieure de n’importe quel fichier Python dans laquelle vous souhaitez accéder par programme stockage Azure :

    from azure.storage.queue import QueueService

Le code suivant crée un objet **QueueService** à l’aide de la clé de compte et nom de compte de stockage. Remplacez « mon compte » et « mykey » par votre nom de compte et la clé.

    queue_service = QueueService(account_name='myaccount', account_key='mykey')

    queue_service.create_queue('taskqueue')


## <a name="how-to-insert-a-message-into-a-queue"></a>Comment : Insérer un Message dans une file d’attente

Pour insérer un message dans une file d’attente, utiliser la **placer\_message** méthode pour créer un nouveau message et l’ajouter à la file d’attente.

    queue_service.put_message('taskqueue', u'Hello World')


## <a name="how-to-peek-at-the-next-message"></a>Comment : Lire le Message suivant

Vous pouvez lire le message situé à l’avant une file d’attente sans le supprimer de la file d’attente en appelant le **Aperçu\_messages** méthode. Par défaut, **Aperçu\_messages** lit un seul message.

    messages = queue_service.peek_messages('taskqueue')
    for message in messages:
        print(message.content)


## <a name="how-to-dequeue-messages"></a>Comment : File d’attente des Messages

Votre code supprime un message à partir d’une file d’attente en deux étapes. Lorsque vous appelez **obtenir\_messages**, vous obtenez le message suivant dans une file d’attente par défaut. Un message retourné par **obtenir\_messages** devient invisible à n’importe quel autre code lire des messages de cette file d’attente. Par défaut, ce message reste invisible pendant 30 secondes. Pour terminer la suppression du message de la file d’attente, vous devez également appeler **Supprimer\_message**. Ce processus en deux étapes de la suppression d’un message garantit que lorsque votre code ne parvient pas à traiter un message en raison de panne logicielle ou matérielle, une autre instance de votre code pouvez obtenir le même message et essayez à nouveau. Votre code appelle **Supprimer\_message** avec le bouton droit une fois que le message a été traité.

    messages = queue_service.get_messages('taskqueue')
    for message in messages:
        print(message.content)
        queue_service.delete_message('taskqueue', message.id, message.pop_receipt)

Il existe deux façons, vous pouvez personnaliser la récupération des messages à partir d’une file d’attente.
Tout d’abord, vous pouvez obtenir un lot de messages (jusqu'à 32). Ensuite, vous pouvez définir un délai d’expiration plus ou moins longtemps éléments temporairement, ce qui permet de votre code plus ou moins de temps à traiter entièrement chaque message. Le code suivant montre comment utiliser le **obtenir\_messages** méthode permettant d’obtenir 16 messages en un seul appel. Puis il traite chaque message en utilisant une boucle for. Il définit également un délai éléments temporairement de cinq minutes pour chaque message.

    messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
    for message in messages:
        print(message.content)
        queue_service.delete_message('taskqueue', message.id, message.pop_receipt)      


## <a name="how-to-change-the-contents-of-a-queued-message"></a>Comment : Modifier le contenu d’un Message en file d’attente

Vous pouvez modifier le contenu d’un message sur place dans la file d’attente. Si le message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour l’état de la tâche. Le code ci-dessous utilise le **mettre à jour\_message** pour mettre à jour un message. Le délai d’expiration de visibilité est défini à 0, ce qui signifie que le message s’affiche immédiatement et le contenu est mis à jour.

    messages = queue_service.get_messages('taskqueue')
    for message in messages:
        queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')

## <a name="how-to-get-the-queue-length"></a>Comment : Obtenir la file d’attente

Vous pouvez obtenir une estimation du nombre de messages dans une file d’attente. La **obtenir\_file d’attente\_métadonnées** méthode vous invite à fournir le service de file d’attente pour renvoyer les métadonnées relatives à la file d’attente et **approximate_message_count**. Le résultat est uniquement approximatif, car les messages peuvent être ajoutés ou supprimés une fois que le service de file d’attente répond à votre requête.

    metadata = queue_service.get_queue_metadata('taskqueue')
    count = metadata.approximate_message_count

## <a name="how-to-delete-a-queue"></a>Comment : Supprimer une file d’attente

Pour supprimer une file d’attente et tous les messages qu’elle contient, appelez le **Supprimer\_file d’attente** méthode.

    queue_service.delete_queue('taskqueue')

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base de stockage file d’attente, suivez ces liens pour en savoir plus.

- [Centre de développement Python](/develop/python/)
- [Services de stockage Azure API REST](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog de l’équipe stockage Azure]
- [Stockage de Microsoft Azure SDK pour Python]

[Blog de l’équipe stockage Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Stockage de Microsoft Azure SDK pour Python]: https://github.com/Azure/azure-storage-python
