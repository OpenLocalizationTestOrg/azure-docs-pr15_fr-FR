<properties
    pageTitle="Azure Active Directory Domain Services : Paramètres de mise à jour DNS pour le réseau virtuel Azure | Microsoft Azure"
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
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services---update-dns-settings-for-the-azure-virtual-network"></a>Services de domaine Active Directory Azure - paramètres de mise à jour DNS pour le réseau virtuel Azure

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Étape 4 : Mettre à jour les paramètres DNS pour le réseau virtuel Azure
Précédent tâches de configuration, vous avez terminé l’activation Azure Active Directory Domain Services pour votre annuaire. La tâche suivante consiste à vous assurer que les ordinateurs au sein du réseau virtuel peuvent se connecter et utilisation de ces services. Mettre à jour les paramètres du serveur DNS pour votre réseau virtuel pour pointer vers les deux adresses IP pendant lequel Azure Active Directory Domain Services est disponible sur le réseau virtuel.

> [AZURE.NOTE] Notez les adresses IP pour les Services de domaine AD Azure affichées sous l’onglet **configuration** de votre annuaire, après avoir activé Azure Active Directory Domain Services pour l’annuaire.

Effectuer les étapes de configuration suivantes pour mettre à jour les paramètres de serveur DNS pour le réseau virtuel dans lequel vous avez activé Azure Active Directory Domain Services.

1. Accédez au **portail classique Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Sélectionnez le nœud de **réseaux** dans le volet gauche.

    ![Nœud de réseaux virtuels](./media/active-directory-domain-services-getting-started/virtual-network-select.png)

3. Dans l’onglet **Réseaux virtuels** , sélectionnez le réseau virtuel dans lequel vous avez activé Azure Active Directory Domain Services afficher ses propriétés.

4. Cliquez sur l’onglet **configurer** .

    ![Nœud de réseaux virtuels](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)

5. Dans la section **DNS servers** , entrez les adresses IP des Services de domaine Active Directory Azure.

6. Vérifiez que vous entrez à la fois les adresses IP qui ont été affichés dans la section **Services de domaine** sous l’onglet **configuration** de votre annuaire.

7. Pour enregistrer les paramètres du serveur DNS pour ce réseau virtuel, cliquez sur **Enregistrer** dans le volet Office dans la partie inférieure de la page.

   ![Mettre à jour les paramètres du serveur DNS pour le réseau virtuel.](./media/active-directory-domain-services-getting-started/update-dns.png)

> [AZURE.NOTE] Après mise à jour les paramètres du serveur DNS pour le réseau virtuel, il peut prendre un certain temps pour les machines virtuelles sur le réseau pour obtenir la configuration de DNS mis à jour. Si une machine virtuelle est impossible de se connecter au domaine, vous pouvez vider le cache DNS (par exemple. « ipconfig /flushdns ») sur l’ordinateur virtuel. Cette commande force la création d’une actualisation des paramètres DNS sur l’ordinateur virtuel.


## <a name="task-5---enable-password-synchronization-to-azure-ad-domain-services"></a>Tâche 5 - activer la synchronisation de mot de passe aux Services de domaine Active Directory Azure
La tâche de configuration suivante consiste à [Activer la synchronisation de mot de passe pour les Services de domaine Active Directory Azure](active-directory-ds-getting-started-password-sync.md).
