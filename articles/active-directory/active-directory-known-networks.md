<properties 
    pageTitle="Connus réseaux | Microsoft Azure" 
    description="En configurant des réseaux connus, vous pouvez éviter des adresses IP appartenant à votre organisation incluse dans les modules de connexion à partir de plusieurs zones géographiques et ins se provenant d’adresses IP au moyen de rapports suspect." 
    services="active-directory" 
    documentationCenter="" 
    authors="markusvi" 
    manager="femila"  
    editor=""/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="markvi"/>

# <a name="known-networks"></a>Réseaux connus


Vous pouvez utiliser access Azure d’Active Directory et les rapports d’utilisation visibilité sur l’intégrité référentielle et la sécurité de l’annuaire de votre organisation. Grâce à ces informations, un administrateur de l’annuaire peut mieux déterminer où les risques de sécurité est peut-être dû afin qu’ils puissent correctement à limiter ces risques.

Il est possible que les rapports «*ins s’à partir de plusieurs zones géographiques*» et «*ins se provenant d’adresses IP avec une activité suspecte*» signale incorrectement adresses IP réellement appartenant à votre organisation. 

Cela peut, par exemple, se produire lorsque : 

- Un utilisateur dans votre Boston office a connecté à distance à votre centre de données à San Francisco déclenche le rapport « Se ins à partir de plusieurs zones géographiques » 

- Un utilisateur de votre organisation essaye d’authentification plusieurs fois avec un déclencheurs de mot de passe incorrect le rapport « Se ins provenant d’adresses IP avec une activité suspecte » 

Pour empêcher ces cas de génération de rapports de sécurité confusion, vous devez ajouter les plages d’adresses IP connus à la liste d’adresses IP public de votre organisation.    


###<a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>Pour ajouter les plages d’adresses IP publics de votre organisation, effectuez les opérations suivantes : 

1.  Ouverture de session sur le [portail de gestion Azure](https://manage.windowsazure.com).

2.  Dans le volet gauche, cliquez sur **Active Directory**. <br><br>![Fonctionnement de découverte d’application Cloud](./media/active-directory-known-networks/known-netwoks-01.png)

3.  Dans l’onglet **annuaire** , sélectionnez votre annuaire.

4.  Dans le menu dans la partie supérieure, cliquez sur **configurer**. <br><br>![Fonctionnement de découverte d’application Cloud](./media/active-directory-known-networks/known-netwoks-02.png)

5.  Sous l’onglet Configurer, accédez à **votre plages d’adresses IP publics organisations** <br><br>![Fonctionnement de découverte d’application Cloud](./media/active-directory-known-networks/known-netwoks-03.png)

6.  Cliquez sur **Ajouter des plages d’adresses IP connu**.

7.  Ajouter votre plages d’adresses dans la boîte de dialogue qui s’affiche, puis cliquez sur le bouton de vérification lorsque vous avez terminé. <br><br>![Fonctionnement de découverte d’application Cloud](./media/active-directory-known-networks/known-netwoks-04.png)


**Ressources supplémentaires**


* [Afficher vos rapports access et l’utilisation](active-directory-view-access-usage-reports.md)
* [Se ins provenant d’adresses IP avec une activité suspecte](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [Compléments d’authentification à partir de plusieurs zones géographiques](active-directory-reporting-sign-ins-from-multiple-geographies.md)


