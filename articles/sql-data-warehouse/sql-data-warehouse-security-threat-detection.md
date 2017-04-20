<properties
   pageTitle="Prise en main détection de menace de magasin de données SQL"
   description="Comment prendre en main détection des menaces"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/24/2016"
   ms.author="lodipalm;sonyama;barbkess"/>


# <a name="get-started-with-threat-detection"></a>Prise en main de détection

> [AZURE.SELECTOR]
- [L’audit](sql-data-warehouse-auditing-overview.md)
- [Détection des menaces](sql-data-warehouse-security-threat-detection.md)

## <a name="overview"></a>Vue d’ensemble

Détection des menaces détecte les activités de base de données anormale indiquant menaces de sécurité potentielles à la base de données. Détection des menaces est en mode Aperçu avant et est pris en charge pour SQL Data Warehouse.

Détection des menaces fournit un calque de sécurité, ce qui permet aux clients détecter et répondre aux menaces potentielles qu’ils se produisent en fournissant des alertes de sécurité sur activités anormales. Les utilisateurs peuvent Explorer les événements suspects à l’aide [d’Audit de magasin de données SQL Azure](sql-data-warehouse-auditing-overview.md) afin de déterminer si elles résultant d’une tentative d’accès, enfreint ou exploiter les données dans le data warehouse.
Détection des menaces facile à menaces potentielles vers le data warehouse sans avoir à être un expert en sécurité ou gérer les systèmes de contrôle de sécurité avancée.

Par exemple, détection des menaces détecte certaines activités de base de données anormale indiquant potentiels SQL injection tentatives. Injection SQL figure parmi les problèmes courants de sécurité application Web sur Internet, utilisée pour attaquer des applications pilotées par les données. Intrus tirer parti d'entre application injecte malveillantes instructions SQL dans les champs d’entrée d’application, de violation ou modification des données dans la base de données.


## <a name="set-up-threat-detection-for-your-database"></a>Configurer la détection des menaces pour votre base de données

1. Lancez le portail Azure à [https://portal.azure.com](https://portal.azure.com).

2. Accédez à la cuillère configuration du SQL Data Warehouse que vous voulez analyser. Dans la carte de paramètres, sélectionnez **audit et détection des menaces**.

    ![Volet de navigation][1]

3. Dans la carte de configuration **de détection de menace et audit** transformer **activé** l’audit, qui affiche les paramètres de détection de menace.

    ![Volet de navigation][2]

4. Activation de la détection de menace **activé** .

5. Configurer la liste des messages électroniques qui recevra les alertes de sécurité lors de la détection des activités entrepôt données anormale.

6. Cliquez sur **Enregistrer** dans la carte de configuration de **détection audit & menace** pour enregistrer le nouveau ou mis à jour l’audit et stratégie de détection des menaces.

    ![Volet de navigation][3]


## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>Explorer les activités de magasin de données anormale lors de la détection d’un événement suspect

1. Vous recevrez une notification par courrier électronique lors de la détection des activités de base de données anormale. <br/>
Le message électronique fournit des informations sur l’événement de sécurité suspects, y compris la nature des activités anormales, nom de la base de données, le nom de serveur et l’heure de l’événement. Par ailleurs, il vous fournit des informations sur les causes possibles et les mesures recommandées pour examiner et limiter la menace potentielle à la base de données.<br/>

    ![Volet de navigation][4]

2. Dans le message, cliquez sur le lien **Le journal d’audit SQL Azure** , ce qui lancera le portail classique Azure et afficher les enregistrements d’audit appropriés au moment de l’événement suspect.

    ![Volet de navigation][5]

3. Cliquez sur les enregistrements d’audit pour afficher des détails sur les activités de base de données suspects telles que l’instruction SQL, l’échec du motif et le client IP.

    ![Volet de navigation][6]

4. Dans la carte d’audit des enregistrements, cliquez sur **Ouvrir dans Excel** pour ouvrir un préconfiguré modèle pour importer et exécuter une analyse plus approfondie du journal d’audit autour de l’heure de l’événement suspect excel.<br/>
**Remarque :** Dans Excel 2010 ou version ultérieure, Power Query et le paramètre de **Regroupement rapide** est requis

    ![Volet de navigation][7]

5. Pour configurer le paramètre **Regroupement** - dans l’onglet de ruban **POWER QUERY** , sélectionnez les **Options** pour afficher la boîte de dialogue Options. Sélectionnez la section confidentialité et choisissez la deuxième option - « Ignorer les niveaux de confidentialité et éventuellement améliorer les performances » :

    ![Volet de navigation][8]

6. Pour charger les journaux d’audit SQL, vérifiez que les paramètres dans les paramètres onglet sont correctement puis sélectionnez le ruban « Données » et cliquez sur le bouton Actualiser tout.

    ![Volet de navigation][9]

7. Les résultats s’affichent dans la feuille des **Journaux d’Audit SQL** qui vous permet de lancer une analyse plus approfondie des activités anormales qui a été détectée et limiter l’impact de l’événement de sécurité dans votre application.


<!--Image references-->
[1]: ./media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-data-warehouse-security-threat-detection/4_td_email.png
[5]: ./media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png
[6]: ./media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png
[7]: ./media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png
[8]: ./media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png
[9]: ./media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png
