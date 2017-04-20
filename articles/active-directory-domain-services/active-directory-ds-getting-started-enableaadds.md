<properties
    pageTitle="Services de domaine Active Directory Azure : Activer les Services de domaine Active Directory Azure | Microsoft Azure"
    description="Prise en main Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/19/2016"
    ms.author="maheshu"/>

# <a name="enable-azure-ad-domain-services"></a>Activer les Services de domaine Active Directory Azure

## <a name="task-3-enable-azure-ad-domain-services"></a>Tâche 3 : Activer les Services de domaine Active Directory Azure
Dans cette tâche, vous activez Azure Active Directory Domain Services pour votre annuaire. Effectuer les étapes de configuration suivantes pour activer les Services de domaine AD Azure pour votre annuaire.

1. Accédez au **portail classique Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Sélectionnez le nœud **Active Directory** dans le volet gauche.

3. Sélectionnez le client Azure AD (répertoire) pour laquelle vous voulez activer les Services de domaine Active Directory Azure.

    ![Sélectionnez Azure AD répertoire](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Cliquez sur l’onglet **configurer** .

    ![Configurer l’onglet du répertoire](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. Faites défiler jusqu'à la section intitulée **les services de domaine**.

    ![Section de configuration de Services de domaine](./media/active-directory-domain-services-getting-started/domain-services-configuration.png)

6. Activer ou désactiver l’option intitulée **Activer les services de domaine pour ce répertoire** sur **Oui**. Vous remarquerez quelques autres options de configuration pour les services de domaine Active Directory Azure apparaissent sur la page.

    ![Activer les Services de domaine](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

    > [AZURE.NOTE] Lorsque vous activez Azure Active Directory Domain Services pour votre client, Azure AD génère et stocke les valeurs de d’informations d’identification Kerberos et NTLM hachage requis pour l’authentification des utilisateurs.

7. Spécifiez le **nom de domaine DNS des services de domaine**.

   - Le nom de domaine par défaut du répertoire (autrement dit, se terminant par le **. onmicrosoft.com** suffixe de domaine) est activée par défaut.

   - La liste contient tous les domaines qui ont été configurés pour votre annuaire Azure AD – notamment vérifié ainsi que des domaines non vérifiés que vous configurez dans l’onglet « Les domaines ».

   - En outre, vous pouvez également taper un nom de domaine personnalisé. Dans cet exemple, nous avons tapé dans un nom de domaine personnalisé « contoso100.com ».

     > [AZURE.WARNING] Vérifiez que le préfixe de domaine du nom du domaine que vous spécifiez (par exemple, « contoso100 » dans le nom de domaine « contoso100.com ») est moins de 15 caractères. Vous ne pouvez pas créer un domaine Azure Active Directory Domain Services avec un préfixe de domaine plu de 15 caractères.

8. Vérifiez que le nom de domaine DNS que vous avez choisi pour le domaine géré n’existe pas déjà dans le réseau virtuel. Plus précisément, vérifiez si :

   - vous disposez déjà d’un domaine avec le même nom de domaine DNS sur le réseau virtuel.

   - le réseau virtuel que vous avez sélectionné dispose d’une connexion VPN avec votre réseau local et que vous avez un domaine portant le même nom de domaine DNS de votre réseau local.

   - vous avez un service cloud existant portant le même nom sur le réseau virtuel.

9. L’étape suivante consiste à sélectionner un réseau virtuel dans lequel vous souhaitez que les Services de domaine Active Directory Azure soit disponible. Sélectionnez le réseau virtuel et sous-réseau dédié que vous avez créé dans le menu déroulant intitulé des **services de domaine de se connecter à ce réseau virtuel**.

   - Assurez-vous que le réseau virtuel que vous avez spécifié appartient à une région Azure pris en charge par les Services de domaine Active Directory Azure. Reportez-vous à la page [services Azure par région](https://azure.microsoft.com/regions/#services/) pour connaître les régions Azure dans laquelle Azure Active Directory Domain Services est disponible.

   - Réseaux virtuels appartenant à une zone dans laquelle Azure Active Directory Domain Services n’est pas prise en charge n’apparaissent pas dans la liste déroulante.
   
   - Utiliser un sous-réseau dédié au sein du réseau virtuel pour les Services de domaine Active Directory Azure. Vérifiez que vous ne sélectionnez pas le sous-réseau passerelle. Voir [Considérations relatives à la mise en réseau](active-directory-ds-networking.md). 

   - De même, les réseaux virtuels qui ont été créés à l’aide du Gestionnaire de ressources Azure n’apparaissent pas dans la liste déroulante. Réseaux virtuels basés sur le Gestionnaire de ressources ne sont actuellement pas pris en charge par les Services de domaine Active Directory Azure.

10. Pour activer les Services de domaine Active Directory Azure, cliquez sur **Enregistrer** dans le volet de tâches en bas de la page.

11. La page affiche un « en attente... » état, tandis que les Services de domaine Active Directory Azure est activée pour votre annuaire.

    ![Activer les Services de domaine - état d’attente](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

    > [AZURE.NOTE] Services de domaine Active Directory Azure fournit une haute disponibilité pour votre domaine géré. Après avoir activé Azure Active Directory Domain Services, notez les adresses IP à partir duquel les Services de domaine sont disponibles sur le réseau virtuel s’affichent un par un. La deuxième adresse IP s’affiche peu de temps, comme dès le service permet de disponibilité pour votre domaine. Lors de la disponibilité est configuré et active de votre domaine, vous devez voir deux adresses IP dans la section **services de domaine** de l’onglet **configurer** .

12. Après avoir d’environ 20 à 30 minutes, vous voyez la première adresse IP à laquelle est disponible sur votre réseau virtuel dans le champ **adresse IP** de la page **configurer** les Services de domaine.

    ![Sa mise en service de première IP activés : les Services de domaine](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)

13. Lors de la disponibilité est opérationnelle pour votre domaine, vous voyez deux adresses IP affichées dans la page. Votre domaine géré est disponible sur votre réseau virtuel sélectionné à ces deux adresses IP. Notez les adresses IP et vous pouvez mettre à jour les paramètres DNS pour votre réseau virtuel. Cette étape permet de machines virtuelles sur le réseau virtuel pour vous connecter au domaine pour les opérations de jointure de domaines.

    ![Les Services de domaine activé - sa mise en service des deux adresses IP](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [AZURE.NOTE] Selon la taille de votre client Azure AD (nombre d’utilisateurs et groupes etc.), la synchronisation à votre domaine géré prend un certain temps. Cette procédure de synchronisation se produit en arrière-plan. Pour les clients de grande taille avec des milliers d’objets, il peut prendre un jour ou deux pour tous les utilisateurs, les appartenances à un groupe et les informations d’identification à synchroniser.

<br>

## <a name="task-4---update-dns-settings-for-the-azure-virtual-network"></a>Tâche 4 : mettre à jour les paramètres DNS pour le réseau virtuel Azure
La tâche de configuration suivante consiste à [mettre à jour les paramètres DNS pour le réseau virtuel Azure](active-directory-ds-getting-started-dns.md).
