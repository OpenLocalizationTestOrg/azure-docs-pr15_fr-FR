<properties 
    pageTitle="En savoir plus sur la limitation dans les Services BizTalk | Microsoft Azure" 
    description="Découvrez les seuils de limitation et résultant comportements d’exécution pour les Services BizTalk. La limitation est basée sur l’utilisation de la mémoire et le nombre de messages. MABS, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
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





# <a name="biztalk-services-throttling"></a>Services BizTalk : la limitation

Azure BizTalk Services met en œuvre la limitation de service basé sur deux conditions : utilisation de la mémoire et le nombre de messages sonneris de traitement. Cette rubrique répertorie les seuils limitation et décrit le comportement d’exécution lorsqu’une condition limitation se produit.

## <a name="throttling-thresholds"></a>Seuils de limitation

Le tableau suivant répertorie la limitation de la source et les seuils :

||Description|Seuil inférieur|Seuil élevé|
|---|---|---|---|
|Mémoire|% du total du système de mémoire disponible/PageFileBytes. <p><p>Total PageFileBytes disponible est d’environ 2 fois la RAM du système.|60 %|est de 70 %|
|Traitement des messages|Nombre de messages de traitement simultanément|40 * nombre de cœurs|100 * nombre de cœurs|

Lorsqu’un seuil élevé est atteint, Azure BizTalk Services commence à limiter. La limitation s’arrête quand le seuil bas est atteint. Par exemple, votre service est à l’aide mémoire système 65 %. Dans ce cas, le service ne limite pas. Démarrage de votre service à l’aide de la mémoire système est de 70 %. Dans ce cas, le service accélère répondre et continue à limiter jusqu'à ce que le service utilise la mémoire de système de 60 % (seuil inférieur).

Services BizTalk Azure effectue le suivi de l’état de limitation (état normal et état accélérée) et la limitation de la durée.


## <a name="runtime-behavior"></a>Comportement d’exécution

Azure BizTalk Services quand un état de limitation, les événements suivants se produisent :

- La limitation s’effectue par instance de rôle. Par exemple :<br/>
Limitation de RoleInstanceA. RoleInstanceB n’est pas la limitation. Dans ce cas, les messages dans RoleInstanceB sont traités comme prévu. Les messages dans RoleInstanceA sont ignorés et échouent avec l’erreur suivante :<br/><br/>
**Le serveur est occupé (e). Essayez à nouveau.**<br/><br/>
- Toutes les sources d’extraire n’interroger ou télécharger un message. Par exemple :<br/>
Un pipeline extrait les messages à partir d’une source externe FTP. L’instance de rôle effectuant l’extraction est dans un état de limitation. Dans ce cas, le pipeline d’arrête le téléchargement des messages supplémentaires jusqu'à ce que l’instance de rôle arrête la limitation.
- Une réponse est envoyée au client afin que le client pouvez renvoyer ce message.
- Vous devez attendre que la limitation est résolue. En particulier, vous devez attendre que le seuil bas est atteint.

## <a name="important-notes"></a>Notes importantes
- La limitation ne peut pas être désactivée.
- Seuils de limitation ne peut pas être modifiée.
- La limitation est activée au niveau du système.
- Le serveur de base de données SQL Azure est également la limitation intégrés.

## <a name="additional-azure-biztalk-services-topics"></a>Rubriques Azure BizTalk Services supplémentaires

-  [Installer les Services BizTalk Azure SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Didacticiels : Services d’Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Comment puis-je à l’aide de démarrer le SDK Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Services BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Voir aussi
- [BizTalk Services : Pour les développeurs, basique, Standard et Premium éditions graphique](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Services BizTalk : Portail classique Azure à l’aide de la mise en service](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Services BizTalk : Graphique de l’état de la mise en service](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [BizTalk Services : Onglets de tableau de bord, surveiller et échelle](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Les Services BizTalk : Sauvegarder et restaurer](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [BizTalk Services : Nom de l’émetteur et la clé de l’émetteur](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
 
