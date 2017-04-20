<properties 
    pageTitle="Tâches autorisées dans les différents États ou statuts dans les Services BizTalk | Microsoft Azure" 
    description="Les actions/opérations autorisées dans les différents statuts MABS : arrêter, Démarrer, redémarrez, suspendre, reprendre, supprimer, mettre à l’échelle, mettez à jour la configuration et sauvegarde" 
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



# <a name="biztalk-services-service-state-chart"></a>BizTalk Services : Diagramme état des services
En fonction de l’état actuel du service BizTalk, il existe des opérations que vous pouvez ou ne pouvez pas effectuer sur le service BizTalk.

Par exemple, vous devez configurer un nouveau service BizTalk dans le portail classique Azure. Lorsqu’elle est terminée avec succès, le service BizTalk est dans un état actif. Dans l’état actif, vous pouvez arrêter le service BizTalk. Si arrêter réussit, le service BizTalk accède à un état arrêté. En cas d’échec, arrêter le service BizTalk accède à un état StopFailed. Dans l’état StopFailed, vous pouvez redémarrer le service BizTalk. Si vous essayez d’une opération qui n’est pas autorisée, comme reprendre le service BizTalk, l’erreur suivante se produit :

**Opération non autorisée**

Pour configurer un BizTalk Service, accédez à [Services BizTalk : portail classique de mise en service des Azure à l’aide de](http://go.microsoft.com/fwlink/p/?LinkID=302280).

Les tableaux suivants répertorient les opérations ou les actions pouvant être effectuées lorsque le BizTalk Service est dans un état spécifique. Un ✔ indique l’opération est autorisée dans cet état. Une entrée vide signifie que l’opération ne peut pas être effectuée dans cet état.

## <a name="start-stop-restart-suspend-resume-and-delete-operations"></a>Démarrer, arrêter, redémarrez, suspendre, reprendre et les opérations de suppression
<table border="1">
<tr>
        <th colspan="15">Opération ou une Action</th>
</tr>

<tr>
        <th rowspan="18">État du Service BizTalk</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Démarrer</th>
        <th>Arrêter</th>
        <th>Redémarrez</th>
        <th>Suspendre</th>
        <th>CV</th>
        <th>Supprimer</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Active</b></td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Désactivé</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Suspendue</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Arrêté</b></td>
<td><center>✔</center></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Mise à jour de service a échoué</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
</table>
<br/>

## <a name="scale-update-configuration-and-backup-operations"></a>Échelle, la Configuration de la mise à jour et les opérations de sauvegarde
<table border="1">
<tr>
        <th colspan="15">Opération ou une Action</th>
</tr>

<tr>
        <th rowspan="18">État du Service BizTalk</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Échelle</th>
        <th>Configuration de la mise à jour</th>
        <th>Sauvegarde</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Active</b></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Désactivé</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Suspendue</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Arrêté</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Mise à jour de service a échoué</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
</tr>
</table>

## <a name="see-also"></a>Voir aussi
- [Services BizTalk : Portail classique Azure à l’aide de la mise en service](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk Services : Onglets de tableau de bord, surveiller et échelle](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk Services : Pour les développeurs, basique, Standard et Premium éditions graphique](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Les Services BizTalk : Sauvegarder et restaurer](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Services BizTalk : la limitation](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [BizTalk Services : Nom de l’émetteur et la clé de l’émetteur](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [Comment puis-je à l’aide de démarrer le SDK Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)


 
