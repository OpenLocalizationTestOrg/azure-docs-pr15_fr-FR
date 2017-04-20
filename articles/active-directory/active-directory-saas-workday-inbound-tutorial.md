<properties 
    pageTitle="Didacticiel : Configuration journée de travail pour la synchronisation entrante | Microsoft Azure" 
    description="Découvrez comment utiliser journée de travail en tant que source de données d’identité pour Azure Active Directory." 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/10/2016" 
    ms.author="markvi" />


#<a name="tutorial-configuring-workday-for-inbound-synchronization"></a>Didacticiel : Configuration journée de travail pour la synchronisation entrante


L’objectif de ce didacticiel consiste à vous montrer les étapes que vous devez effectuer dans la journée de travail et Azure AD pour importer des personnes à partir de la journée de travail à Azure Active Directory. 

Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure AD Premium valide
-   Un client dans la journée de travail
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1. L’activation de l’intégration des applications pour la journée de travail 


2. Création d’un utilisateur de système d’intégration 


3. Création d’un groupe de sécurité 


4. Affectation de l’utilisateur de système d’intégration au groupe de sécurité 


5. Configuration des options de groupe de sécurité 


6. L’activation des modifications de stratégie de sécurité 


7. Configuration utilisateur importer dans Azure Active Directory 



##<a name="enabling-the-application-integration-for-workday"></a>L’activation de l’intégration des applications pour la journée de travail
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour la journée de travail.

### <a name="steps"></a>Étapes suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-workday-inbound-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-workday-inbound-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-workday-inbound-tutorial/IC749321.png "Application d’ajouter")

  
5. Dans la zone Rechercher, tapez **journée de travail**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-workday-inbound-tutorial/IC701021.png "Ajouter une application à partir de gallerry")

6. Dans le volet résultats, sélectionnez journée de travail, puis cliquez sur Terminer pour ajouter l’application.

    ![Galerie d’applications] (./media/active-directory-saas-workday-inbound-tutorial/IC701022.png "Galerie d’applications")





## <a name="creating-an-integration-system-user"></a>Création d’un utilisateur de système d’intégration

### <a name="steps"></a>Étapes suivantes :


1. Dans la **Journée de travail atelier**, entrez créer utilisateur dans la zone de recherche, puis cliquez sur **Créer un utilisateur intégration système**. 

    ![Créer un utilisateur] (./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "Créer un utilisateur")



2. Exécuter la tâche **Créer un utilisateur intégration système** en fournissant un nom d’utilisateur et mot de passe pour un utilisateur intégration système.  Laissez nécessitent nouveau mot de passe à la connexion suivante discrétion est désactivé, étant donné que cet utilisateur se connectent par programme. Conservez les Minutes de délai d’expiration de Session avec sa valeur par défaut de 0, ce qui empêchera prématurément sessions de l’utilisateur d’arriver à expiration. 

    ![Créer l’intégration système utilisateur] (./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "Créer l’intégration système utilisateur")
 




## <a name="creating-a-security-group"></a>Création d’un groupe de sécurité

Pour le scénario décrit dans ce didacticiel, vous devez créer un groupe de sécurité du système sans contrainte intégration et lui affecter l’utilisateur.

### <a name="steps"></a>Étapes suivantes :

1. Entrez créer un groupe de sécurité dans la zone Rechercher, puis cliquez sur **Créer un groupe de sécurité**. 

    ![Groupe CreateSecurity] (./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "Groupe CreateSecurity")
 

2. Exécuter la tâche créer un groupe de sécurité.  Sélectionnez le groupe de sécurité intégration système — pas limités à partir de la liste déroulante Type de groupe de sécurité avec clients, pour créer un groupe de sécurité à laquelle les membres doit être ajoutés explicitement. 

    ![Groupe CreateSecurity] (./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "Groupe CreateSecurity")
 



## <a name="assigning-the-integration-system-user-to-the-security-group"></a>Affectation de l’utilisateur de système d’intégration au groupe de sécurité

### <a name="steps"></a>Étapes suivantes :


1. Entrez modifier le groupe de sécurité dans la zone de recherche, puis cliquez sur **Modifier le groupe de sécurité**. 

    ![Modifier le groupe de sécurité] (./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "Modifier le groupe de sécurité")
 
 

2. Recherchez, puis sélectionnez Nouveau groupe de sécurité intégration par son nom. 

    ![Modifier le groupe de sécurité] (./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "Modifier le groupe de sécurité")

 

3. Ajouter le nouvel utilisateur système intégration vers le nouveau groupe de sécurité. 

    ![Groupe de sécurité du système] (./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "Groupe de sécurité du système")  




## <a name="configuring-security-group-options"></a>Configuration des options de groupe de sécurité

Dans cette étape, vous accordez aux nouvelles autorisations de groupe de sécurité pour **obtenir** et **placer** les opérations sur les objets sécurisés par les stratégies de sécurité de domaine suivantes :

- Mise en service de compte externe

- Collaborateur données : Rapports de travail Public

- Travail données : Tous les postes

- Travail données : En cours diminution des effectifs d’informations

- Données de travail : Entreprise titre sur profil de travail

 
### <a name="steps"></a>Étapes suivantes :

1. Entrez les stratégies de sécurité de domaine dans la zone de recherche, puis cliquez sur le lien, stratégies de sécurité de domaine pour consacrée aux.  

    ![Stratégies de sécurité de domaine] (./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "Stratégies de sécurité de domaine")  
 

2. Rechercher du système, puis sélectionnez la rubrique consacrée à **système** .  Cliquez sur **OK**.  

    ![Stratégies de sécurité de domaine] (./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "Stratégies de sécurité de domaine")  


3. Dans la liste des stratégies de sécurité pour la rubrique consacrée aux système, développez Administration de la sécurité et sélectionnez la stratégie de sécurité, mise en service externe du compte.  

    ![Stratégies de sécurité de domaine] (./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "Stratégies de sécurité de domaine")  


4. Cliquez sur **Modifier les autorisations**et puis, dans la page de la boîte de dialogue **Modifier les autorisations**, ajouter à la liste des groupes de sécurité avec des autorisations de l’intégration **obtenir** et **placer** le nouveau groupe de sécurité. 

    ![Autorisations de modification] (./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "Autorisations de modification")  

 

5. Répétez l’étape 1, ci-dessus, pour revenir à l’écran de sélection des zones fonctionnelles, et l’heure actuelle, recherchez personnel, sélectionnez la zone fonctionnelle personnel, puis cliquez sur **OK**.

    ![Stratégies de sécurité de domaine] (./media/active-directory-saas-workday-inbound-tutorial/IC750990.png "Stratégies de sécurité de domaine")  
 

6. Dans la liste des stratégies de sécurité pour la zone fonctionnelle du recrutement, développez données collaborateur : personnel et répétez l’étape 4 ci-dessus pour chacun d'entre eux restant les stratégies de sécurité :

     - Collaborateur données : Rapports de travail Public

     - Travail données : Tous les postes

     - Travail données : En cours diminution des effectifs d’informations

     - Données de travail : Entreprise titre sur profil de travail


    ![Stratégies de sécurité de domaine] (./media/active-directory-saas-workday-inbound-tutorial/IC750991.png "Stratégies de sécurité de domaine")  







## <a name="activating-security-policy-changes"></a>L’activation des modifications de stratégie de sécurité

### <a name="steps"></a>Étapes suivantes :

1. Entrez activez dans la zone Rechercher, puis cliquez sur le lien, activer modifications stratégie de sécurité en attente. 

    ![Activer] (./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "Activer") 
 

2. Commencer la tâche Activer les modifications en attente sécurité stratégie en entrant un commentaire fins d’audit, puis cliquez sur **OK**. 

    ![Activer en attente de sécurité] (./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "Activer en attente de sécurité")   
 

3. Exécuter la tâche dans l’écran suivant en cochant la case à cocher étiqueté confirmer, puis cliquez sur **OK**. 

    ![Activer en attente de sécurité] (./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "Activer en attente de sécurité")  





## <a name="configuring-user-import-in-azure-ad"></a>Configuration utilisateur importer dans Azure Active Directory

L’objectif de cette section consiste à créer un plan comment configurer Azure AD pour importer des contacts à partir de la journée de travail.


### <a name="steps"></a>Étapes suivantes :


1. Dans la page de l’intégration d’application **journée de travail** , cliquez sur **Importer les utilisateurs de configurer** pour ouvrir la boîte de dialogue **Configurer la mise en service** .


2. Dans la page **paramètres et informations d’identification d’administration** , effectuez les opérations suivantes, puis cliquez sur **suivant**: 

    ![Paramètres et informations d’identification d’administration] (./media/active-directory-saas-workday-inbound-tutorial/IC750995.png "Paramètres et informations d’identification d’administration")  
 
    un. Dans la zone de texte journée de travail d’administration utilisateur nom, tapez le nom de l’utilisateur que vous avez créé dans la création une section intégration système utilisateur.

    b. Dans la zone de texte SERIE.jour.ouvre administrateur mot de passe, tapez le mot de passe de l’utilisateur que vous avez créé dans la création une section intégration système utilisateur.

    c. Dans la zone de texte jours ouvrés client URL, tapez l’URL ou votre client journée de travail.


3. Dans la page **tester la connexion** , cliquez sur **Démarrer le test** pour vérifier la connectivité, puis cliquez sur **suivant**. 

    ![Tester la connexion] (./media/active-directory-saas-workday-inbound-tutorial/IC750996.png "Tester la connexion")  
 

4. Dans la page options de **déploiement** , cliquez sur **suivant**. 

    ![Options de mise en service] (./media/active-directory-saas-workday-inbound-tutorial/IC750997.png "Options de mise en service")


5. Dans la boîte de dialogue **Démarrer la mise en service** , cliquez sur **terminé**. 

    ![Démarrer la mise en service] (./media/active-directory-saas-workday-inbound-tutorial/IC750998.png "Démarrer la mise en service")
 

Vous pouvez désormais accéder à la section **utilisateurs** et vérifier si l’utilisateur de votre journée de travail a été importée.



## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
