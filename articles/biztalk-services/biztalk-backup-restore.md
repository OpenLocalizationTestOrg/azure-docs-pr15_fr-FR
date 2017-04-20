<properties 
    pageTitle="Créer et restaurer une sauvegarde dans les Services BizTalk | Microsoft Azure" 
    description="Services BizTalk inclut la sauvegarde et restauration. Découvrez comment créer et restaurer une sauvegarde et déterminer ce qui est sauvegardé. MABS, WABS" 
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


# <a name="biztalk-services-backup-and-restore"></a>Les Services BizTalk : Sauvegarder et restaurer

Azure BizTalk Services inclut des fonctionnalités de sauvegarde et de restauration. Cette rubrique décrit comment faire pour sauvegarder et restaurer des Services BizTalk en utilisant le portail classique Azure.

Vous pouvez également sauvegarder des Services BizTalk à l’aide de l' [API REST de Services BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584). 

> [AZURE.NOTE] Connexions hybride ne sont pas sauvegardées, quelle que soit l’édition. Vous devez recréer vos connexions hybride.

## <a name="before-you-begin"></a>Avant de commencer

- Sauvegarder et restaurer ne peuvent pas être disponible pour toutes les éditions. Voir [Services BizTalk : graphique des éditions](biztalk-editions-feature-chart.md).

- À l’aide du portail Azure classique, vous pouvez créer une sauvegarde à la demande ou créer une sauvegarde planifiée. 

- Contenu de la sauvegarde peut être restauré au même BizTalk Service ou à un nouveau BizTalk Service. Pour restaurer le BizTalk Service en utilisant le même nom, le BizTalk Service existant doit être supprimé et le nom doit être disponible. Après avoir supprimé un BizTalk Service, il peut prendre plus de temps que souhaité pour le même nom soit disponible. Si vous ne pouvez pas attendre le même nom soit disponible, puis restaurer vers un nouveau BizTalk Service.

- Services BizTalk peut être restaurés à la même édition ou une édition supérieure. Restauration des Services BizTalk vers une édition en bas, à partir de laquelle la sauvegarde a été évaluée, n'est pas pris en charge.

    Par exemple, une sauvegarde à l’aide de l’édition de base peut être restaurée pour l’édition Premium. Une sauvegarde à l’aide de l’édition Premium ne peut pas être restaurée à la version Standard Edition.

- Les numéros de contrôle EDI sont sauvegardés assurer la continuité des numéros de contrôle. Si les messages sont traités après la dernière sauvegarde, ce contenu de la sauvegarde en cours de restauration peuvent entraîner des numéros de contrôle en double.

- Si un lot de messages actives, traite le lot **avant** d’exécuter une sauvegarde. Lorsque vous créez une sauvegarde (en tant que nécessaire ou planifiée), les messages par lots ne sont jamais stockés. 

    **Si une sauvegarde est considérée avec les messages actives dans un lot, ces messages ne sont pas sauvegardées et sont donc perdues.**

- Facultatif : Dans le portail de Services BizTalk, arrêtez les opérations de gestion.


## <a name="create-a-backup"></a>Créer une sauvegarde

Une sauvegarde peut accéder à tout moment et est entièrement contrôlée par vous. Cette section répertorie les étapes pour créer des sauvegardes à l’aide du portail classique Azure, notamment :

[Sauvegarde à la demande](#backupnow)

[Planifier une sauvegarde](#backupschedule)

#### <a name="backupnow"></a>Sauvegarde à la demande
1. Dans le portail classique Azure, sélectionnez **Les Services BizTalk**, puis le BizTalk Service que vous voulez sauvegarder.
2. Dans l’onglet **tableau de bord** , sélectionnez **Sauvegarder** en bas de la page.
3. Entrez un nom de la sauvegarde. Par exemple, entrez *myBizTalkService*unité commerciale*Date*.
4. Choisissez un compte de stockage blob et sélectionnez la coche en regard de démarrer la sauvegarde.

Une fois la sauvegarde terminée, un conteneur avec le nom de la sauvegarde que vous entrez est créé dans le compte de stockage. Ce conteneur contient votre configuration de la sauvegarde BizTalk Service.

#### <a name="backupschedule"></a>Planifier une sauvegarde

1. Dans le portail classique Azure, sélectionnez **BizTalk Services**, sélectionnez le nom de BizTalk Service souhaité pour planifier la sauvegarde, puis sous l’onglet **configurer** .
2. Définir l' **état de la sauvegarde** **automatique**. 
3. Sélectionnez le **Compte de stockage** pour stocker la sauvegarde, entrez la **fréquence** pour créer les sauvegardes et la durée de conservation les sauvegardes (**Rétention jours**) :

    ![][AutomaticBU]

    **Notes**   
    - Dans la **Période de conservation**, la période de rétention doit être supérieure à la fréquence de sauvegarde.
    - Sélectionnez **toujours conserver au moins une sauvegarde**, même si elle est au-delà de la période de rétention.
    

4. Cliquez sur **Enregistrer**.


Exécution d’une sauvegarde planifiée, il crée un conteneur (pour stocker les données de sauvegarde) dans le compte de stockage que vous avez entré. Le nom du conteneur est nommé *BizTalk Service nom-date-heure*. 

Si le tableau de bord BizTalk Service affiche l’état **Échec** :

![Dernier état de la sauvegarde planifiée][BackupStatus] 

Le lien ouvre les journaux d’opération de gestion des Services pour aider à résoudre les problèmes. Voir [Services BizTalk : résoudre les problèmes à l’aide des journaux d’opération](http://go.microsoft.com/fwlink/p/?LinkId=391211).

## <a name="restore"></a>Restaurer

Vous pouvez restaurer des sauvegardes à partir du portail classique Azure ou à partir de l' [API REST restaurer du Service BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325582). Cette section répertorie les étapes pour restaurer le portail classique.

#### <a name="before-restoring-a-backup"></a>Avant de restaurer une sauvegarde

- Nouveau suivi, l’archivage et de surveillance stores peuvent être entrés lors de la restauration d’un BizTalk Service.

- Les mêmes données EDI Runtime sont restaurées. La sauvegarde EDI Runtime stocke les numéros de contrôle. Les numéros de contrôle restaurés sont dans l’ordre de l’heure de la sauvegarde. Si les messages sont traités après la dernière sauvegarde, ce contenu de la sauvegarde en cours de restauration peuvent entraîner des numéros de contrôle en double.

#### <a name="restore-a-backup"></a>Restaurer une sauvegarde

1. Dans le portail classique Azure, sélectionnez **Nouveau** > **Application Services** > **BizTalk Service** > **restaurer**:

    ![Restaurer une sauvegarde][Restore]

2. Dans l' **URL de sauvegarde**, sélectionnez l’icône de dossier et développer le compte de stockage Azure qui stocke la sauvegarde de la configuration BizTalk Service. Développez le conteneur, puis dans le volet droit, sélectionnez le fichier .txt de sauvegarde correspondant. 
<br/><br/>
Cliquez sur **Ouvrir**.

3. Dans la page **BizTalk Service restauration** , entrez un **Nom de Service BizTalk** et vérifiez **l’URL de domaine**, **Edition**et **région** pour le BizTalk Service restauré. **Créer une nouvelle instance de base de données SQL** pour la base de données de suivi :

    ![][RestoreBizTalkService]

    Cliquez sur la flèche suivante.

4.  Vérifiez le nom de la base de données SQL, entrez le serveur physique où la base de données SQL est créé et un nom d’utilisateur et mot de passe pour ce serveur.


    Si vous voulez configurer l’édition de base de données SQL, taille et autres propriétés, sélectionnez **Configurer les paramètres de base de données avancées**. 

    Cliquez sur la flèche suivante.

5. Créer un nouveau compte de stockage ou entrez un compte de stockage existant pour le BizTalk Service.

7. Activez la case à cocher pour lancer la restauration.

Une fois la restauration terminée, un nouveau BizTalk Service est répertorié dans un état suspendu dans la page Services BizTalk dans le portail classique Azure.



### <a name="postrestore"></a>Après la restauration d’une sauvegarde

Le BizTalk Service est toujours restauré dans un état **suspendu** . Dans cet état, vous pouvez apporter toute modification configuration avant le nouvel environnement est fonctionnel, y compris :

- Si vous avez créé des applications de BizTalk Service en utilisant le SDK Azure, vous devrez peut-être mettre à jour les informations d’identification de contrôle d’accès (ACS) dans les applications pour fonctionner avec l’environnement restauré.

- Vous restaurez un BizTalk Service pour répliquer un environnement de BizTalk Service existant. Dans ce cas, s’il existe accords configurés dans le portail Services BizTalk d’origine qui utilisent un dossier FTP source, vous devrez peut-être mettre à jour les accords dans l’environnement nouvellement restaurée à utiliser un dossier FTP autre source. Dans le cas contraire, il peut y avoir deux accords différents d’extraire le même message.

- Si vous avez restauré pour avoir plusieurs environnements de BizTalk Service, vérifiez que vous ciblez l’environnement approprié dans les applications Visual Studio, applets de commande PowerShell, API REST ou cotation interfaces API OM gestion partenaire.

- Il est recommandé de configurer des sauvegardes automatisées sur l’environnement BizTalk Service restauré.

Pour démarrer le BizTalk Service dans le portail Azure classique, sélectionnez le BizTalk Service restauré et sélectionnez **reprendre** dans la barre des tâches. 



## <a name="what-gets-backed-up"></a>Que sont sauvegardé

Lors de la création d’une sauvegarde, les éléments suivants sont sauvegardés :

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>Éléments sauvegardés</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Portail de Services BizTalk Azure</strong></td>
</tr> 
<tr>
<td>Configuration et Runtime</td> 
<td>
<ul>
<li>Détails de profil et de partenaire</li>
<li>Contrats avec les partenaires</li>
<li>Assemblys personnalisés déployés</li>
<li>Ponts déployés</li>
<li>Certificats</li>
<li>Transformations déployées</li>
<li>Pipelines</li>
<li>Modèles créé et enregistré dans le portail de Services BizTalk</li>
<li>X12 mappages ST01 et GS01</li>
<li>Numéros de contrôle (EDI)</li>
<li>Valeurs AS2 Message micro</li>
</ul>
</td>
</tr> 
 
<tr>
<td colspan="2">
 <strong>Service BizTalk Azure</strong></td>
</tr> 
<tr>
<td>Certificat SSL</td> 
<td>
<ul>
<li>Données du certificat SSL</li>
<li>Mot de passe de certificat SSL</li>
</ul>
</td>
</tr> 
<tr>
<td>Paramètres du Service BizTalk</td> 
<td>
<ul>
<li>Nombre d’unités échelle</li>
<li>Édition</li>
<li>Version du produit</li>
<li>Région/Centre de données</li>
<li>Clé et espace de noms access Control Service (ACS)</li>
<li>Chaîne de connexion de base de données de suivi</li>
<li>Chaîne de connexion de compte de stockage d’archivage</li>
<li>Chaîne de connexion de compte de stockage de surveillance</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>Autres éléments</strong></td>
</tr> 
<tr>
<td>Base de données de suivi</td> 
<td>Lorsque le BizTalk Service est créé, les détails de la base de données de suivi sont entrés, y compris le serveur de base de données SQL Azure et le nom de la base de données de suivi. La base de données de suivi n’est pas automatiquement sauvegardé.
<br/><br/>
<strong>Important</strong><br/>
Si la base de données de suivi est supprimée et les besoins en matière de base de données récupérés une sauvegarde précédente doit exister. Si une sauvegarde n’existe pas, la base de données de suivi et ses données ne sont pas récupérables. Dans ce cas, créez une nouvelle base de données de suivi portant le même nom de base de données. Geo réplication est recommandée.</td>
</tr> 
</table>

## <a name="next"></a>Suivant

Pour créer des Services BizTalk Azure dans le portail classique Azure, accédez à [Services BizTalk : portail classique de mise en service des Azure à l’aide de](http://go.microsoft.com/fwlink/p/?LinkID=302280). Pour commencer la création d’applications, accédez aux [Services BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=235197).

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

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png
 
