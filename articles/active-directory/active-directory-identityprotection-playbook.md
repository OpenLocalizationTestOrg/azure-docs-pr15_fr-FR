<properties
    pageTitle="Manuel Active Directory identité Protection Azure | Microsoft Azure"
    description="Découvrez comment Protection Azure AD identité vous permet de limiter la capacité d’un pirate pour exploiter un appareil ou l’identité compromise et sécuriser une identité ou un périphérique qui a été précédemment suspects ou connu pour être compromis."
    services="active-directory"
    keywords="protection d’identité Azure AD, la découverte application cloud, gestion des applications, sécurité, risque, le niveau de risque, vulnérabilité, stratégie de sécurité"
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
    ms.date="08/22/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection-playbook"></a>Azure manuel Protection d’identité Active Directory 

Ce manuel vous aidera à :

- Remplir des données dans l’environnement de Protection d’identité en vulnérabilités et événements risque simulation
- Configurer les stratégies d’accès conditionnelle risque et tester l’impact de ces politiques


## <a name="simulating-risk-events"></a>Simulation d’événements de risque

Cette section vous donne étapes permettant de simuler les types d’événements risque suivants :

- Signe-ins provenant d’adresses IP anonymes (simples)
- Signe-ins à partir d’emplacements expliquerons (modéré)
- Impossible de voyage vers des emplacements pas courant (difficiles)

Ne peut pas être simulés à d’autres événements risque de façon sécurisée.


### <a name="sign-ins-from-anonymous-ip-addresses"></a>Signe-ins provenant d’adresses IP anonymes

Ce type d’événement risque identifie les utilisateurs qui ont correctement connecté à partir d’une adresse IP qui a été identifiée comme une adresse IP du proxy anonyme. Ces proxys sont utilisés par les personnes autorisées want masquer l’adresse IP de leur appareil et peuvent être utilisés pour les utilisateurs malveillants.

**Pour simuler une connexion à partir d’une adresse IP anonyme, procédez comme suit**:

1.  Téléchargez le [Navigateur à](https://www.torproject.org/projects/torbrowser.html.en).
2.  À l’aide de la Tor Browser, accédez à [https://myapps.microsoft.com](https://myapps.microsoft.com).   
3.  Entrez les informations d’identification du compte que vous voulez voir apparaître dans le rapport **se-ins provenant d’adresses IP anonymes** .

La connexion apparaît dans le tableau de bord identité Protection dans les 5 minutes. 


###<a name="sign-ins-from-unfamiliar-locations"></a>Signe-ins à partir d’emplacements inconnus

Le risque emplacements expliquerons est un mécanisme évaluation de connexion en temps réel qui prend en compte les emplacements de connexion (IP, Latitude / Longitude et ASN) afin de déterminer les emplacements nouveau / expliquerons. Le système stocke les adresses IP précédente, Latitude / Longitude et les APE d’un utilisateur et considère qu’ils soient familiers emplacements. Un emplacement de connexion est considéré comme mal si l’emplacement de connexion ne correspond pas à aucun des emplacements familiers existants.

Protection d’identité Azure Active Directory :  

 - offre une période d’apprentissage initial de 14 jours pendant lequel il ne signale pas les nouveaux emplacements comme emplacements inhabituels.
 - ignore signe-ins appareils familières et d’emplacements qui sont géographiquement près un emplacement familier existant.

Pour simuler expliquerons emplacements, vous devez se connecter à partir d’un emplacement et un périphérique le compte n’a pas signé dans à partir d’avant. 


**Pour simuler une connexion à partir d’un emplacement inconnu, procédez comme suit**:

1.  Choisir un compte qui comporte au moins un historique de connexion à 14 jours. 

2.  Effectuez des options :
    
    un. Lorsque vous utilisez un réseau privé virtuel, accédez à [https://myapps.microsoft.com](https://myapps.microsoft.com) et entrez les informations d’identification du compte que vous souhaitez simuler l’événement risque pour.

    b. Demandez à un de vos collaborateurs dans un autre emplacement pour vous connecter à l’aide des informations d’identification du compte (non recommandées).

La connexion apparaît dans le tableau de bord Protection d’identité dans les 5 minutes.
 
### <a name="impossible-travel-to-atypical-location"></a>Impossible de voyage à emplacement pas courant
Simulation de la condition de voyage signalée est difficile, car l’algorithme utilise apprentissage afin d’éliminer les fausses tels qu’impossible de voyage à partir d’appareils familiers ou signe-ins de VPN qui sont utilisés par d’autres utilisateurs dans l’annuaire de l’ordinateur. En outre, l’algorithme requiert un connexion de l’historique des 3 à 14 jours pour l’utilisateur avant qu’il commence à générer des événements de risque.

**Pour simuler un voyage signalée dans emplacement pas courant, effectuez les opérations suivantes**:

1.  À l’aide de votre navigateur standard, accédez à [https://myapps.microsoft.com](https://myapps.microsoft.com).  

2.  Entrez les informations d’identification du compte que vous souhaitez générer un événement de risque de voyage signalée pour.

3.  Modifier votre agent utilisateur. Vous pouvez modifier un agent utilisateur dans Internet Explorer à partir des outils de développement, ou modifier votre agent utilisateur dans Firefox ou Chrome à l’aide d’un module complémentaire de vues agent utilisateur.

4.  Modifier votre adresse IP. Vous pouvez modifier votre adresse IP à l’aide d’un réseau privé virtuel, un module complémentaire Tor, ou révolution un nouvel ordinateur dans Azure dans un centre de données différents.

5.  Se connecter à [https://myapps.microsoft.com](https://myapps.microsoft.com) utilisant les mêmes informations d’identification comme avant et après quelques minutes après la connexion précédente.

La connexion apparaît dans le tableau de bord Protection d’identité au sein de 2 à 4 heures.<br>
En raison de l’ordinateur complexe modèles concernés d’apprentissage, il est probable qu’il n’est pas obtenir récupéré.<br> Vous souhaiterez peut-être répliquer ces étapes pour plusieurs comptes Azure AD.


## <a name="simulating-vulnerabilities"></a>Simulation de vulnérabilités 
Vulnérabilités sont faiblesses dans un environnement Azure AD qui peuvent être exploitées par un acteur bad. 3 types d'entre sont actuellement exposés dans Protection Azure AD identité qui s’appuient sur d’autres fonctionnalités de Azure AD. Ces problèmes de sécurité seront affiche automatiquement dans le tableau de bord de Protection d’identité une fois que ces fonctionnalités sont configurées.

-   Azure AD [l’authentification multifacteur ?](../multi-factor-authentication/multi-factor-authentication.md)
-   Azure AD [Cloud application découverte](active-directory-cloudappdiscovery-whatis.md).
-   Azure AD [Gestion des identités dotés de privilèges](active-directory-privileged-identity-management-configure.md). 



##<a name="user-compromise-risk"></a>Risque de compromis utilisateur

**Pour tester le risque de compromis utilisateur, effectuez les opérations suivantes**:

1.  Se connecter au [https://portal.azure.com](https://portal.azure.com) en tant qu’administrateur global pour votre client.

2.  Accédez à **la Protection d’identité**. 

3.  Dans la carte de **Protection Azure AD identité** principale, cliquez sur **paramètres**. 

4.  Dans la carte de **Paramètres du portail** , sous **règles de sécurité**, cliquez sur **utilisateur compromettre risque**. 

5.  Sur la carte **se connecter risque** , désactiver le **Activer la règle** , puis cliquez sur **Enregistrer** les paramètres.

6.  Pour un compte d’utilisateur donné, simuler un expliquerons emplacements ou un événement de risque IP anonyme. Cela sera élever le niveau de risque utilisateur pour cet utilisateur à **support**.

7.  Patientez quelques minutes et vérifiez que niveau utilisateur pour l’utilisateur est **adaptée au support**.

8.  Accédez à la carte de **Paramètres du portail** .

9.  **Sur la carte **Utilisateur compromis risque** , sous **Activer la règle**, sélectionnez.** 

10. Sélectionnez une des options suivantes :

    un. Pour bloquer, sélectionnez **support** sous **bloc se connecter**.

    b. Pour appliquer la modification du mot de passe sécurisé, activez **support** sous **Exiger l’authentification multifacteur**.

13. Cliquez sur **Enregistrer**.

14. Vous pouvez maintenant tester accès conditionnel en fonction du risque en vous connectant à l’aide d’un utilisateur avec un niveau de risque élevé. Si le risque utilisateur moyenne, en fonction de la configuration de votre stratégie, votre connexion s’être bloqué ou que vous êtes obligé de modifier votre mot de passe. 
<br><br>
![Manuel](./media/active-directory-identityprotection-playbook/201.png "Playbook")
<br>

 
##<a name="sign-in-risk"></a>Connexion à risque

 
**Pour tester un signe risque, effectuez les opérations suivantes :**

1.  Se connecter au [https://portal.azure.com](https://portal.azure.com) en tant qu’administrateur global pour votre client.

2.  Accédez à **la Protection d’identité**.

3.  Dans la carte de **Protection Azure AD identité** principale, cliquez sur **paramètres**. 

4.  Dans la carte de **Paramètres du portail** , sous **règles de sécurité**, cliquez sur **se connecter risque**.

5.  Dans la carte **se connecter risque **, sélectionnez **sur** sous **Activer la règle**. 

7.  Sélectionnez une des options suivantes :

    un. Pour bloquer, sélectionnez **support** sous **bloc se connecter**

    b. Pour appliquer la modification du mot de passe sécurisé, activez **support** sous **Exiger l’authentification multifacteur**.

8.  Pour bloquer, sélectionnez moyen sous se bloquer dans.

9.  Pour appliquer l’authentification multifacteur, sélectionnez **support** sous **Exiger l’authentification multifacteur**.

10. Cliquez sur **Enregistrer**.

11. Vous pouvez maintenant tester accès conditionnel en fonction du risque en simuler les emplacements inhabituels ou les événements de risque IP anonymes, car ils sont les deux événements risque de **support** .

<br>
![Manuel](./media/active-directory-identityprotection-playbook/200.png "Playbook")
<br>


## <a name="see-also"></a>Voir aussi

 - [Protection d’identité Azure Active Directory](active-directory-identityprotection.md)
