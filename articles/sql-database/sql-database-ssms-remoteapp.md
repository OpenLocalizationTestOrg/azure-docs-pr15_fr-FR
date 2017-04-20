<properties
    pageTitle="Se connecter à la base de données SQL à l’aide de SQL Server Management Studio dans Azure RemoteApp | Microsoft Azure"
    description="Utilisez ce didacticiel pour apprendre à utiliser SQL Server Management Studio dans Azure RemoteApp pour la sécurité et de performances lors de la connexion à la base de données SQL"
    services="sql-database"
    documentationCenter=""
    authors="adhurwit"
    manager="jhubbard"/>

<tags
    ms.service="sql-database"
    ms.workload="data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="adhurwit"/>

# <a name="use-sql-server-management-studio-in-azure-remoteapp-to-connect-to-sql-database"></a>Utiliser SQL Server Management Studio dans Azure RemoteApp pour vous connecter à la base de données SQL

## <a name="introduction"></a>Introduction  
Ce didacticiel montre comment utiliser SQL Server Management Studio (SSMS) dans Azure RemoteApp pour vous connecter à la base de données SQL. Il vous guide dans le processus de configuration SQL Server Management Studio dans Azure RemoteApp, explique les avantages et présente les fonctionnalités de sécurité que vous pouvez utiliser dans Azure Active Directory.

**Durée estimée d’exécution :** 45 minutes

## <a name="ssms-in-azure-remoteapp"></a>SSMS dans Azure RemoteApp

RemoteApp Azure est un service RDS dans Azure qui fournit des applications. Vous pouvez en savoir plus ici : [Nouveautés RemoteApp ?](../remoteapp/remoteapp-whatis.md)

SSMS en cours d’exécution dans Azure RemoteApp vous donne la même expérience que l’exécution de SSMS localement.

![Capture d’écran montrant SSMS en cours d’exécution dans Azure RemoteApp][1]



## <a name="benefits"></a>Avantages

Il existe de nombreux avantages à l’aide de SSMS dans Azure RemoteApp, y compris :

- Port 1433 sur Azure SQL Server n’a pas à être exposé en externe (en dehors d’Azure).
- Sans avoir à conserver Ajout et suppression des adresses IP dans le pare-feu Azure SQL Server.
- Toutes les connexions RemoteApp Azure se produisent sur HTTPS sur le port 443 à l’aide chiffrées protocole Bureau à distance
- Il est multi-utilisateurs et que vous pouvez mettre à l’échelle.
- Il existe un gain de performances d’avoir SSMS dans la même région en tant que la base de données SQL.
- Vous pouvez auditer l’utilisation de RemoteApp Azure avec l’édition Premium d’Azure Active Directory qui contient des rapports sur les activités utilisateur.
- Vous pouvez activer l’authentification multifacteur (MFA).
- Accès SSMS n’importe où lorsque vous utilisez une des clients Azure RemoteApp pris en charge qui inclut iOS, Android, Mac, Windows Phone et Windows PC.


## <a name="create-the-azure-remoteapp-collection"></a>Créer la collection RemoteApp Azure

Voici les étapes de création de votre collection de sites Azure RemoteApp avec SSMS :


### <a name="1-create-a-new-windows-vm-from-image"></a>1. créer une nouvelle machine virtuelle Windows à partir d’Image
Utiliser l’Image « Windows Server Remote Desktop Session hôte Windows Server 2012 R2 » à partir de la galerie pour rendre votre nouvelle machine virtuelle.


### <a name="2-install-ssms-from-sql-express"></a>2. Installer SSMS à partir de SQL Express

Accédez à la nouvelle machine virtuelle et accédez à cette page de téléchargement : [Microsoft® SQL Server® 2014 Express](https://www.microsoft.com/en-us/download/details.aspx?id=42299)

Il existe une option pour télécharger uniquement SSMS. Après le téléchargement, allez dans le répertoire d’installation et exécuter le programme d’installation pour installer SSMS.

Vous devez également installer SQL Server 2014 Service Pack 1. Vous pouvez le télécharger ici : [Microsoft SQL Server 2014 Service Pack 1 (SP1)](https://www.microsoft.com/en-us/download/details.aspx?id=46694)

SQL Server 2014 Service Pack 1 inclut des fonctionnalités essentielles pour l’utilisation de la base de données SQL Azure.


### <a name="3-run-validate-script-and-sysprep"></a>3. Exécutez script valider et Sysprep

Sur le bureau de la machine virtuelle un script PowerShell est appelé valider. Effectuer cette opération en double-cliquant sur. Il vérifie que la machine virtuelle est prête à être utilisé pour l’hébergement à distance d’applications. Lors de la vérification terminée, il vous demande à exécuter sysprep - choisir de l’exécuter.

Lorsque sysprep s’achève, il va arrêter la machine virtuelle.

Pour en savoir plus sur la création d’une image RemoteApp Azure, voir : [comment créer une image du modèle RemoteApp dans Azure](http://blogs.msdn.com/b/rds/archive/2015/03/17/how-to-create-a-remoteapp-template-image-in-azure.aspx)


### <a name="4-capture-image"></a>4. capturer l’image

Lorsque la machine virtuelle a cessé de fonctionner, trouvez-le dans le portail actuel et capturer.

Pour en savoir plus sur la capture d’une image, voir [capturer une image d’un ordinateur virtuel Windows Azure créé avec le modèle de déploiement classique](../virtual-machines/virtual-machines-windows-classic-capture-image.md)


### <a name="5-add-to-azure-remoteapp-template-images"></a>5. ajouter aux images de modèle de RemoteApp Azure

Dans la section Azure RemoteApp du portail en cours, accédez à l’onglet Images de modèle et cliquez sur Ajouter. Dans la zone contextuelle, sélectionnez « Importation d’une image à partir de votre bibliothèque de Machines virtuelles », puis choisissez l’Image que vous venez de créer.



### <a name="6-create-cloud-collection"></a>6. créer une collection de sites cloud

Dans le portail en cours, créez une Collection de Cloud RemoteApp Azure. Sélectionnez l’Image du modèle que vous venez d’importer avec SSMS est installé.

![Créer la nouvelle collection cloud][2]


### <a name="7-publish-ssms"></a>7. publier des SSMS

Dans la publication onglet de votre nouvelle collection cloud, sélectionnez Publier une application dans le Menu Démarrer, puis sélectionnez SSMS dans la liste.

![Publication d’application][5]

### <a name="8-add-users"></a>8. ajouter des utilisateurs

Sous l’onglet de l’accès des utilisateurs, vous pouvez sélectionner les utilisateurs qui auront accès à cette collection RemoteApp Azure qui inclut uniquement les SSMS.

![Ajouter un utilisateur][6]


### <a name="9-install-the-azure-remoteapp-client-application"></a>9. installer l’application cliente RemoteApp Azure

Vous pouvez télécharger et installer un client Azure RemoteApp ici : [téléchargement | RemoteApp Azure](https://www.remoteapp.windowsazure.com/en/clients.aspx)



## <a name="configure-azure-sql-server"></a>Configurer Azure SQL Server

La seule configuration nécessitée consiste à vérifier que les Services Azure est activé pour le pare-feu. Si vous utilisez cette solution, vous n’avez pas besoin ajouter toutes les adresses IP pour ouvrir le pare-feu. Le trafic réseau qui est autorisé à SQL Server est à partir d’autres services Azure.


![Autoriser Azure][4]



## <a name="multi-factor-authentication-mfa"></a>Authentification multifacteur (MFA)

L’authentification Multifacteur peut être activée pour cette application en particulier. Accédez à l’onglet Applications de votre Azure Active Directory. Vous trouverez une entrée pour Microsoft Azure RemoteApp. Si vous cliquez sur cette application, puis configurez, vous verrez la page suivante dans laquelle vous pouvez activer l’authentification Multifacteur pour cette application.

![Activer l’authentification Multifacteur][3]



## <a name="audit-user-activity-with-azure-active-directory-premium"></a>Activité de l’utilisateur d’audit avec Azure Active Directory Premium

Si vous n’avez pas Azure AD Premium, vous devez activer dans la section des licences de votre annuaire. Avec Premium activé, vous pouvez affecter des utilisateurs à un niveau Premium.

Lorsque vous passez à un utilisateur dans votre Azure Active Directory, vous pouvez ensuite accéder à l’onglet activité pour afficher les informations de connexion RemoteApp Azure.



## <a name="next-steps"></a>Étapes suivantes

Une fois toutes les étapes ci-dessus, vous serez en mesure d’exécuter le client Azure RemoteApp puis reconnectez-vous avec un utilisateur affecté. Vous obtenez SSMS dans l’un de vos applications et vous pouvez l’exécuter comme vous le feriez s’il a été installé sur votre ordinateur avec accès au serveur SQL Azure.

Pour plus d’informations sur la façon d’établir la connexion à la base de données SQL, consultez [se connecter à la base de données SQL avec SQL Server Management Studio et effectuer un exemple de requête T-SQL](sql-database-connect-query-ssms.md).


C’est tout pour l’instant. Profitez !



<!--Image references-->
[1]: ./media/sql-database-ssms-remoteapp/ssms.png
[2]: ./media/sql-database-ssms-remoteapp/newcloudcollection.png
[3]: ./media/sql-database-ssms-remoteapp/mfa.png
[4]: ./media/sql-database-ssms-remoteapp/allowazure.png
[5]: ./media/sql-database-ssms-remoteapp/publish.png
[6]: ./media/sql-database-ssms-remoteapp/user.png
