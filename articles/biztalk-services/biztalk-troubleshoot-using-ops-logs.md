<properties 
    pageTitle="Résoudre les problèmes des Services BizTalk en utilisant les journaux opération | Microsoft Azure" 
    description="Résoudre les problèmes des Services BizTalk en utilisant les journaux d’opération. MABS, WABS" 
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


# <a name="biztalk-services-troubleshoot-using-operation-logs"></a>Services BizTalk : Résoudre les problèmes à l’aide des journaux d’opération

## <a name="what-are-the-operation-logs"></a>Quelles sont les journaux opération
Journaux d’opération est une fonctionnalité de gestion des Services disponible dans le portail classique Azure qui vous permet d’afficher les journaux historiques des opérations effectuées dans vos services Azure, y compris les Services BizTalk. Cela vous permet d’afficher les données historiques associées aux opérations de gestion de votre abonnement BizTalk Service jusqu’au premier 180 jours.

> [AZURE.NOTE]Cette fonctionnalité capture uniquement les journaux pour les opérations de gestion sur BizTalk Services, tels que lorsque le service a été démarré, sauvegardée, et ainsi de suite. Ces actions sont suivies sans tenir compte si elles sont effectuées à partir du portail classique Azure ou à l’aide de l' [API REST du Service BizTalk](http://msdn.microsoft.com/library/azure/dn232347.aspx). Pour une liste complète des opérations qui sont suivies à l’aide de la gestion des Services, voir [Opérations de suivi à l’aide de gestion des Services Azure](#bizops).<br/><br/>
Cela ne capture pas les journaux pour des activités liées au BizTalk Service runtime (par exemple, message traitée par des ponts et ainsi de suite.). Pour afficher ces fichiers journaux, utilisez l’affichage de suivi à partir du portail de Services BizTalk. Pour plus d’informations, voir [Suivi des Messages](http://msdn.microsoft.com/library/azure/hh949805.aspx).

## <a name="view-biztalk-services-operation-logs"></a>Affichage BizTalk Services opération journaux
1. Dans le portail classique Azure, sélectionnez **Gestion des Services**, puis l’onglet **Journaux opération** .
2. Vous pouvez filtrer les journaux en fonction des paramètres différents comme abonnement, plage de dates, le type de service (par exemple, les Services BizTalk), nom de service ou l’état de l’opération (a réussi, a échoué).
3. Activez la case à cocher pour afficher la liste filtrée. L’image suivante montre les activités liées à la testbiztalkservice :  ![afficher les journaux d’opération][ViewLogs] 
4. Pour visualiser davantage de données sur une opération spécifique, sélectionnez la ligne, puis cliquez sur **Détails** dans la barre des tâches en bas.


## <a name="bizops"></a>Opérations assurer le suivi des Services de gestion Azure
Le tableau suivant répertorie les opérations sont suivies à l’aide des Services de gestion Azure :

Nom de l’opération | Tâche
--- | ---
CreateBizTalkService | Opération pour créer un nouveau BizTalk Service
DeleteBizTalkService | Opération de suppression d’un BizTalk Service
RestartBizTalkService | Opération de redémarrer un BizTalk Service
StartBizTalkService | Opération de démarrage d’un BizTalk Service
StopBizTalkService | Opération pour arrêter un BizTalk Service
DisableBizTalkService | Opération pour désactiver un BizTalk Service
EnableBizTalkService | Opération pour activer un BizTalk Service
BackupBizTalkService | Opération pour sauvegarder un BizTalk Service
RestoreBizTalkService | Opération pour restaurer un BizTalk Service à partir de la sauvegarde spécifiée
SuspendBizTalkService | Opération permettant de suspendre un BizTalk Service
ResumeBizTalkService | Opération à reprendre un BizTalk Service
ScaleBizTalkService | Opération à l’échelle d’un BizTalk Service vers le haut ou vers le bas
ConfigUpdateBizTalkService | Opération de mise à jour de la configuration d’un BizTalk Service
ServiceUpdateBizTalkService | Opération de mise à niveau ou mettre à niveau un BizTalk Service vers une autre version
PurgeBackupBizTalkService | Opération pour supprimer définitivement les sauvegardes du BizTalk Service en dehors de la période de rétention


## <a name="see-also"></a>Voir aussi
- [Service de sauvegarde BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [Restaurer le Service BizTalk à partir de sauvegarde](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [BizTalk Services : Pour les développeurs, basique, Standard et Premium éditions graphique](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [Services BizTalk : Portail classique Azure à l’aide de la mise en service](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [Services BizTalk : Graphique de l’état de la mise en service](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [BizTalk Services : Onglets de tableau de bord, surveiller et échelle](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [Services BizTalk : la limitation](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [BizTalk Services : Nom de l’émetteur et la clé de l’émetteur](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Comment puis-je à l’aide de démarrer le SDK Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png
 
