<properties
   pageTitle="Date d’expiration des données dans DocumentDB avec durée de vie | Microsoft Azure"
   description="TTL, Microsoft Azure DocumentDB offre la possibilité d’avoir documents automatiquement supprimés à partir du système après une période donnée."
   services="documentdb"
   documentationCenter=""
   keywords="durée de vie"
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2016"
   ms.author="kipandya"/>

# <a name="expire-data-in-documentdb-collections-automatically-with-time-to-live"></a>Expiration des données dans des collections DocumentDB automatiquement avec la durée de vie

Applications peuvent générer et stocker de grandes quantités de données. Certaines de ces données, telles qu’informations ne sont utiles que pour une période limitée du session d’utilisateur, les journaux et les données d’événement générée par machine. Une fois que les données devient excédentaire aux besoins de l’application, il est possible sans purge ces données et réduire les besoins de stockage d’une application.

Avec « time to live » ou TTL, Microsoft Azure DocumentDB offre la possibilité d’avoir documents automatiquement supprimés à partir de la base de données après une période donnée. La durée de vie défaut pouvant être définie au niveau de la collection de sites et substituée sur une base par document. Une fois que la durée de vie est définie, comme une valeur par défaut de la collection de sites ou à un niveau de document, DocumentDB supprime automatiquement les documents qui existent après ce laps de temps, en secondes, dans la mesure où ils ont été modifiés en dernier.

Durée de vie dans DocumentDB utilise un décalage par rapport aux lorsque le document a été modifié en dernier. Pour effectuer cette opération qu’il utilise le champ DTS qui se trouve sur tous les documents. Le champ DTS est un horodatage d’origine de style unix représentant la date et l’heure. Le champ DTS est mis à jour chaque fois qu’un document est modifié. 

## <a name="ttl-behavior"></a>Comportement de la durée de vie

La fonctionnalité de durée de vie est contrôlée par propriétés TTL à deux niveaux - le niveau de collection de sites et le niveau du document. Les valeurs sont définies en secondes et sont traités comme un delta de DTS que le document a été modifié en dernier en.

 1.  DefaultTTL pour la collection de sites
  * S’il est manquant (ou la valeur null), les documents ne sont pas supprimées automatiquement.
  
  * Si l’heure actuelle et la valeur est « -1 » = infini – documents n’expirent pas par défaut
  
  * Si présenter et la valeur correspond à un nombre (« n ») – documents expirent « n » secondes après la dernière modification

 2.  Durée de vie pour les documents : 
  * Propriété s’applique uniquement si DefaultTTL est présente pour la collection parent.
  
  * Remplace la valeur DefaultTTL pour la collection parent.

Dès que le document a expiré (ttl + DTS > = heure actuelle du serveur), le document est marqué comme « expiré ». Aucune opération ne sera autorisée sur ces documents après cette date, et ils seront exclues les résultats des requêtes effectuées. Les documents sont supprimés physiquement dans le système et sont supprimés en arrière-plan façon opportuniste ultérieurement. Il n’utilise pas les [Unités demander (RUs)](documentdb-request-units.md) à partir du budget de la collection de sites.

La logique ci-dessus peut être affichée dans le tableau suivant :

|       | DefaultTTL manquants / n’est pas définie sur la collection de sites | DefaultTTL = -1 dans la collection de sites | DefaultTTL = « n » dans la collection de sites|
| ------------- |:-------------|:-------------|:-------------|
| Durée de vie manquant dans le document| Rien à passer outre au niveau du document dans la mesure où le document et la collection de sites n’ont aucun concept de durée de vie. | Aucune expirent dans cette collection de sites. | Les documents dans cette collection va expirer lorsque n intervalle s’écoule. |
| TTL = -1 sur le document | Rien à remplacer au niveau du document, étant donné que la collection de sites ne définissez la propriété DefaultTTL un document pouvez ignorer. Durée de vie d’un document est non interprétée par le système. | Aucune expirent dans cette collection de sites. | Le document avec TTL =-1 dans cette collection de sites n’expire jamais. Tous les autres documents expire après l’intervalle « n ». |
|  TTL = n sur document | Rien à remplacer au niveau du document. Durée de vie d’un document dans non interprétés par le système. | Le document avec TTL = n expirera après n intervalle, en secondes. D’autres documents hériteront intervalle de -1 et n’expirent jamais. | Le document avec TTL = n expirera après n intervalle, en secondes. D’autres documents héritent intervalle « n » de la collection de sites. |


## <a name="configuring-ttl"></a>Configuration de durée de vie

Par défaut, la durée de vie est désactivée par défaut dans toutes les collections de DocumentDB et sur tous les documents.

## <a name="enabling-ttl"></a>L’activation de durée de vie

Pour activer la durée de vie sur une collection de sites ou documents dans une collection de sites, vous devez définir la propriété DefaultTTL d’une collection de sites à -1 ou un nombre positif non nulle. Définition de la DefaultTTL à-1 signifie que par défaut que tous les documents dans la collection résident indéfiniment mais la DocumentDB service doit surveiller cette collection de sites pour les documents qui ont remplacé cette valeur par défaut.

## <a name="configuring-default-ttl-on-a-collection"></a>Configuration par défaut TTL sur une collection de sites

Vous êtes en mesure de configurer une durée par défaut de vie à un niveau de collection de sites. 

Pour définir la durée de vie sur une collection de sites, vous devez fournir un nombre positif non nul qui indique la période, en secondes, le point d’expirer tous les documents dans la collection après le dernier horodatage modifié du document (DTS).

Ou bien, vous pouvez définir la valeur par défaut à -1, ce qui signifie que tous les documents insérés dans à la collection indéfiniment par défaut.

## <a name="setting-ttl-on-a-document"></a>Paramètre durée de vie d’un document

En plus de définir une durée de vie par défaut sur une collection de sites, vous pouvez définir TTL spécifique à un niveau de document. Cette opération remplace la valeur par défaut de la collection de sites.

Pour définir la durée de vie d’un document, vous devez fournir un nombre positif non nul qui indique la période, en secondes, le point d’expirer le document après le dernier horodatage modifié du document (DTS).

Pour définir ce décalage expiration, définir le champ durée de vie dans le document.

Si un document ne comporte aucun champ TTL, la valeur par défaut de la collection s’appliquent.

Si TTL est désactivé au niveau de la collection de sites, le champ durée de vie du document sont ignoré jusqu'à ce que la durée de vie est activée à nouveau sur la collection de sites.


## <a name="extending-ttl-on-an-existing-document"></a>Extension de durée de vie d’un document existant

Vous pouvez réinitialiser la durée de vie d’un document en effectuant une opération d’écriture sur le document. Cela définira la DTS à l’heure actuelle, et le compte à rebours avant l’expiration du document, tel que défini par la valeur ttl, commenceront à nouveau.

Si vous souhaitez modifier la durée de vie d’un document, vous pouvez mettre à jour le champ que vous pouvez faire avec un autre champ définissable.


## <a name="removing-ttl-from-a-document"></a>Suppression de durée de vie d’un document

Si une durée de vie a été définie sur un document et que vous n’avez plus besoin de ce document expire, puis vous pouvez extraire le document, supprimez le champ durée de vie et remplacer le document sur le serveur.

Lorsque le champ durée de vie est supprimé du document, la valeur par défaut de la collection est appliquée.

Pour arrêter un document à partir de la date d’expiration et hérite pas de la collection de sites puis vous devez définir la valeur TTL-1.


## <a name="disabling-ttl"></a>Désactivation de la durée de vie

Pour désactiver TTL entièrement sur une collection de sites et arrêter le processus d’arrière-plan à partir de rechercher des documents qui a expiré que la propriété DefaultTTL sur la collection de sites doit être supprimée.

Pour supprimer cette propriété est différente de la valeur -1. Paramètre à-1 signifie que les nouveaux documents ajoutés à la collection résident toujours, mais vous pouvez remplacer ceci sur des documents spécifiques dans la collection de sites.

Suppression de cette propriété entièrement à partir de la collection signifie qu’aucun document n’arrive à expiration, même s’il existe des documents qui ont explicitement remplacée par défaut précédent.


## <a name="faq"></a>FAQ

**Ce que TTL coûteront moi ?**

Il est sans frais supplémentaires pour définir une durée de vie d’un document.

**Combien de temps faut-il pour supprimer mon document une fois la durée de vie ?**

Les documents sont marqués comme non disponibles dès que le document a expiré (ttl + DTS > = heure actuelle du serveur). Aucune opération ne sera autorisée sur ces documents après cette date, et ils seront exclues les résultats des requêtes effectuées. Les documents sont supprimés physiquement par le système en arrière-plan. Cela ne consomme pas toute RUs à partir du budget de la collection de sites.

**S’il faut une période de temps de supprimer les documents, ils comptera vers Mon quota (et poudre.) jusqu'à ce qu’ils sont supprimés ?**

Non, une fois que le document a expiré vous ne sera pas facturée pour le stockage de ces documents et la taille des documents n'est pas pris en compte le quota de stockage pour une collection de sites.

**Durée de vie d’un document aura un impact sur frais demandeur ?**

Non, il sera aucun impact sur les frais de demandeur pour les opérations effectuées sur n’importe quel document dans DocumentDB.

**Sera la suppression de l’impact de documents sur le débit que j’ai configuré sur ma collection ?**

Non, servir les requêtes par rapport à votre collection de sites ne recevrez priorité sur le processus d’arrière-plan pour supprimer vos documents en cours d’exécution. Ajout de durée de vie à n’importe quel document aura aucun impact sur ce.

**Lorsqu’un document arrive à expiration, combien de temps il reste dans ma collection jusqu'à sa suppression ?**

Dès que le document expire il ne sera plus accessible. L’heure exacte à un document reste dans votre collection de sites avant de suppression réelle n’est pas déterministe et dépendra lorsque le processus d’arrière-plan est en mesure de supprimer le document.

**Documents qui a expiré sont supprimés sur tous les nœuds, ou bien « finalement consistent? »**

Le document ne sera plus disponible en même temps sur tous les nœuds et dans toutes les régions.

**Existe-t-il un coût demandeur d’analyse de durée de vie des tâches en arrière-plan ?**

Non, il n’existe aucune demandeur le coût.

**À quelle fréquence les délais d’expiration TTL sont activées ?**

Vérification des délais d’expiration TTL ne se produisent en arrière-plan. Lors de la réponse à une demande du service et effectuez les tests inline et exclure tous les documents qui ont expiré. La suppression du document physique est le seul processus asynchrone exécuté en arrière-plan. La fréquence de ce processus est déterminée par le RUs disponible dans la collection de sites.

**La fonctionnalité TTL s’applique-t-elle uniquement aux documents entiers ou puis-je expiration des valeurs de propriétés de document individuel ?**

TTL s’applique à l’ensemble du document. Si vous souhaitez que le point d’expirer uniquement une partie d’un document, il est recommandé que vous extrayez la partie du document principal dans vers un document distinct « lié », puis utilisez TTL sur ce document extrait.

**La fonctionnalité TTL a-t-il des exigences indexation spécifiques ?**

Oui. La collection de sites doit être [indexation stratégie définie](documentdb-indexing-policies.md) sur lent ou cohérent. Vous essayez de définir DefaultTTL sur une collection de sites avec l’indexation défini sur aucun provoquera une erreur, comme apparaîtront essayez de désactiver l’indexation sur une collection qui est déjà défini pour un DefaultTTL.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur DocumentDB Azure, reportez-vous à la page de [*documentation*](https://azure.microsoft.com/documentation/services/documentdb/) de service.




