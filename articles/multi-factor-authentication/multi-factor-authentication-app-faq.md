<properties
    pageTitle="Forum aux questions sur l’application Microsoft authentificateur"
    description="Fournit une liste des Forum aux questions et réponses relatifs à l’application Microsoft Authentication et l’authentification multifacteur Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="pblachar, librown"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="kgremban"/>

# <a name="microsoft-authenticator-application-faq"></a>Forum aux questions sur application Microsoft Authenticator

L’application Microsoft Authenticator remplacé l’application Azure authentificateur et est l’application recommandée quand vous utilisez l’authentification multifacteur Azure. Cette application est disponible pour iOS, Android et Windows Phone.

## <a name="frequently-asked-questions"></a>Forum aux questions

- **Qu’est-il advenu de l’authentificateur Azure, authentification multifacteur et les applications de compte Microsoft ?**

    L’application Microsoft Authenticator remplace les uns des autres ces applications. Azure authentificateur mis à niveau vers Microsoft Authenticator. Si vous utilisez l’authentification multifacteur ou les applications de compte Microsoft, installez Microsoft Authenticator et ajoutez vos comptes à nouveau. Vérifiez que vous avez terminé d’ajouter vos comptes à la nouvelle application avant de supprimer les anciens.

- **J’utilise déjà l’application Microsoft Authenticator pour les codes de vérification. Comment atteindre les notifications push un clic ?**  

    Approuver une connexion via une notification push n’est disponible pour les comptes Microsoft et non pour les comptes tiers tel que Google ou Facebook. Pour les comptes Microsoft ou établissement d’enseignement, votre organisation peut choisir de désactiver cette option, cependant.

    Si vous utilisez un compte Microsoft pour votre compte personnel et que vous voulez basculer vers les notifications push, vous devez ajouter votre compte à nouveau. Réenregistrer le périphérique avec votre compte, puis configurer les notifications push.  

    Si votre compte ne comporte pas de la vérification activée, voir à [propos de la vérification en deux étapes](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) pour décider si elle est adaptée à vos besoins.  

- **Lorsque j’ai seront en mesure d’utiliser les notifications push un clic sur iPhone ou iPad ?**  

    Cette fonctionnalité est dans la version bêta jusqu'à la fin du mois d’août, quand il devient largement disponible pour les comptes Microsoft. Si vous souhaitez participer à notre programme bêta, envoyer des messages électroniques à msauthenticator@microsoft.com. Inclure votre prénom, nom et ID Apple dans votre message.  

- **Les notifications push un seul clic fonctionnent pour les comptes non Microsoft ?**  

    Non, les notifications push fonctionnent uniquement avec les comptes Microsoft et comptes Azure Active Directory. Si votre travail ou l’établissement scolaire utilise les comptes Azure Active Directory, ils peuvent désactiver cette fonctionnalité.  

- **J’ai restauré mon appareil à partir d’une sauvegarde, et les codes de mon compte sont manquants ou ne fonctionne ne pas. Que s'est-il passé ?**  

    Pour des raisons de sécurité, nous ne restaurer des comptes à partir de sauvegardes d’application. Si vous restaurez l’application iOS à partir d’une sauvegarde, vos comptes sont toujours affichés, mais ils ne fonctionnent pas pour recevoir des vérifications connexion ou pour générer les codes de sécurité. Après la restauration de l’application, supprimez vos comptes et ajoutez-les à nouveau.

- **Je reçois un nouvel appareil. Comment supprimer l’application Microsoft Authenticator de mon périphérique ancien et déplacer vers le nouveau ?**

    Ajout de l’application Microsoft Authenticator vers un nouvel appareil ne supprime pas automatiquement celle-ci à partir d’autres périphériques. Pour gérer les périphériques sont configurés pour votre compte, visitez le site Web même qui vous permet de gérer la vérification, puis sélectionnez Supprimer les anciennes applications.

    Pour les comptes Microsoft personnels, ce site Web est votre page de [sécurité du compte](https://account.microsoft.com/security) . Pour les comptes Microsoft Professionnel ou scolaire, ce site Web peut être [Mes applications](https://myapps.microsoft.com) ou un portail personnalisé votre organisation a configuré.

## <a name="contact-us"></a>Nous contacter

Si votre question n’a pas été réponse ici, laisser un commentaire dans la partie inférieure de la page. Ou, [contactez le support technique](https://support.microsoft.com/contactus) et nous allons répondre à votre problème dès que possible.

Si vous contactez le support, inclure plus grande partie des informations suivantes que vous pouvez :

- **ID d’utilisateur** – en quelle est l’adresse de messagerie que vous avez tenté de vous connecter avec ?
- **Description générale de l’erreur** – quel message d’erreur exact vouliez-vous dire voir ?  Si aucun message d’erreur s’est produite, décrivez le comportement inattendu remarqué, dans le détail.
- **Page** – quelle page avez-vous sur lorsque vous l’avez vu l’erreur (inclure l’URL) ?
- **Code d’erreur** - vous recevez le code d’erreur spécifique.
- **ID de session** - l’id de session spécifiques que vous recevez.
- **ID de corrélation** – en ce qui a été le code d’id de corrélation généré lorsque l’utilisateur vu l’erreur.
- **Horodatage** – ce qui a été précis date et heure que vous avez vu l’erreur (inclure le fuseau horaire) ?

La plupart de ces informations sont accessibles dans votre page de connexion. Lorsque vous ne vérifiez votre connexion dans le temps, sélectionnez **Afficher les détails**.

![Se connecter détails de l’erreur](./media/multi-factor-authentication-end-user-troubleshoot/view_details.png)

Inclure les informations nous permet de résoudre votre problème aussi rapidement que possible.

## <a name="related-topics"></a>Rubriques connexes

- [Forum aux questions sur l’authentification multifacteur Azure](multi-factor-authentication-faq.md)  
- [À propos de la vérification](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) pour les comptes Microsoft
- [Vous rencontrez des problèmes avec la vérification ?](multi-factor-authentication-end-user-troubleshoot.md)
