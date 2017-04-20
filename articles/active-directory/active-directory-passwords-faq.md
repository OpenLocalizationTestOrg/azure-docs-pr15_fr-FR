<properties
    pageTitle="Forum aux questions : Azure AD Password Management | Microsoft Azure"
    description="Forum aux questions (FAQ) sur la gestion de mot de passe dans Azure Active Directory, y compris mot de passe réinitialisé, l’inscription, rapports et écriture différée à Active Directory local."
    services="active-directory"
    documentationCenter=""
    authors="asteen"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="asteen"/>

# <a name="password-management-frequently-asked-questions"></a>Gestion des mots de passe Forum aux Questions

> [AZURE.IMPORTANT] **Vous êtes ici parce que vous rencontrez des problèmes en vous connectant ?** Si c’est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).

Voici quelques questions fréquemment posées pour tous les éléments liés à la gestion de mot de passe.

Si vous vous trouvez avec une question à laquelle vous ne connaissez pas la réponse à, ou recherchez un problème particulier, vous êtes confronté à l’aide, vous pouvez lire sur ci-dessous pour voir si nous l’avons couverts déjà.  Si nous n’est déjà fait, ne vous inquiétez pas ! N’hésitez pas à poser des questions, vous avez qui n’est pas couverte ici sur les [Forums AD Azure](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD) et nous allons revenir à vous dès que possible.

Ce forum aux questions est divisé en sections suivantes :

- [**Questions sur l’inscription de réinitialiser votre mot de passe**](#password-reset-registration)
- [**Questions sur la réinitialisation du mot de passe**](#password-reset)
- [**Questions sur les rapports de gestion de mot de passe**](#password-management-reports)
- [**Questions sur le mot de passe d’écriture différée**](#password-writeback)

## <a name="password-reset-registration"></a>Mot de passe réinitialisé l’enregistrement
 - **Q : Mes utilisateurs enregistrer leurs propres données de réinitialiser votre mot de passe ?**

 > **A:** Oui, tant que réinitialisation du mot de passe est activée et leur contrat de licence, ils peuvent accéder au portail d’enregistrement de réinitialisation de mot de passe à http://aka.ms/ssprsetup d’enregistrer leurs informations d’authentification pour être utilisé avec réinitialisation du mot de passe. Les utilisateurs peuvent également enregistrer en accédant au panneau d’accès au http://myapps.microsoft.com, en cliquant sur l’onglet profil, en cliquant sur le Registre pour l’option de réinitialiser votre mot de passe. Découvrez comment obtenir vos utilisateurs configurés pour le mot de passe en lisant l’article comment obtenir les utilisateurs configurés pour réinitialiser votre mot de passe.

 - **Q : puis-je définir des données de réinitialiser votre mot de passe au nom de mes utilisateurs ?**

 > **A:** Oui, vous pouvez le faire avec DirSync ou PowerShell, ou via le portail d’administration d’Office [Portail de gestion Azure](https://manage.windowsazure.com) . En savoir plus sur cette fonctionnalité sur le billet de blog confidentialité améliorée pour l’authentification Multifacteur de Azure AD et numéros de téléphone de réinitialiser votre mot de passe et en lecture Découvrez comment les données sont utilisées par mot de passe réinitialisé.

 - **Q : puis-je synchroniser des données pour les questions de sécurité à partir d’en local ?**

 > **A:** Non, ce n’est pas possible aujourd'hui, mais nous allons la considérer.

 - **Q : Mes utilisateurs enregistrer les données de façon à ce que d’autres utilisateurs ne peuvent pas voir ces données ?**

 > **A:** Oui, lorsque les utilisateurs enregistrent des données à l’aide du portail de l’enregistrement de réinitialiser votre mot de passe il est enregistré dans des champs authentification privé qui ne sont visibles que par un administrateur Global et de l’utilisateur lui-même. En savoir plus sur cette fonctionnalité sur le billet de blog confidentialité améliorée pour l’authentification Multifacteur de Azure AD et numéros de téléphone de réinitialiser votre mot de passe et en lecture Découvrez comment les données sont utilisées par mot de passe réinitialisé.

 - **Q : les utilisateurs doivent être enregistrés avant de pouvoir utiliser réinitialisation du mot de passe ?**

 > **A:** Non, si vous définissez suffisamment d’informations d’authentification en leur nom, les utilisateurs n’ont pas à enregistrer. Réinitialisation du mot de passe fonctionnent correctement dans la mesure où vous avez correctement mis en forme des données stockées dans les champs appropriés dans l’annuaire. En savoir que plus sur en lisant Découvrez comment les données sont utilisées par la réinitialisation de mot de passe.

 - **Q : puis-je synchroniser ou définir les champs de message électronique d’authentification ou autre numéro de téléphone d’authentification, le téléphone de l’authentification au nom de mes utilisateurs ?**

 > **A:** Pas actuellement, mais nous allons considérer l’activation de cette fonctionnalité.

 - **Q : comment le portail d’inscription détermine-t-il les options à afficher mes utilisateurs ?**

 > **A:** Le portail d’inscription de réinitialiser votre mot de passe affiche uniquement les options que vous avez activé pour vos utilisateurs sous la section stratégie de réinitialiser votre mot de passe utilisateur de l’onglet de configuration de votre annuaire. Cela signifie que si vous n’activez pas, par exemple, questions sur la sécurité, puis utilisateurs pas sera en mesure d’enregistrer pour cette option.

 - **Q : lorsqu’un utilisateur est considéré enregistré ?**

 > **A:** Un utilisateur est considéré comme enregistré lorsqu’il contient au moins pièces N des informations d’authentification défini, où N est le nombre d’authentification méthodes requises que vous avez définies dans le [Portail de gestion Azure](https://manage.windowsazure.com). Pour plus d’informations, voir Personnalisation utilisateur mot de passe réinitialisé stratégie.


## <a name="password-reset"></a>Réinitialisation du mot de passe

 - **Q : combien de temps dois-je patienter pour recevoir un courrier électronique, SMS ou appel téléphonique à partir de réinitialisation du mot de passe ?**

 > **A:** Courrier électronique, des messages SMS et des appels téléphoniques doit arrivent dans sous une minute, avec la casse en cours 5-20 secondes. Si vous ne recevez pas la notification à cette période, vérifiez votre dossier courrier indésirable, que le numéro / messagerie contacté est celle que vous soupçonnez, et que les données d’authentification de l’annuaire sont correctement mis en forme. Pour en savoir plus sur la mise en forme de numéros de téléphone et la messagerie électronique adresses pour une utilisation avec réinitialisation du mot de passe voir en savoir comment les données sont utilisées par la réinitialisation de mot de passe.

 - **Q : quelles langues sont prises en charge par la réinitialisation de mot de passe ?**

 > **A:** Le mot de passe réinitialiser l’interface utilisateur, messages SMS et les appels vocaux sont localisées dans les mêmes 40 langues prises en charge dans Office 365. Ce sont : arabe, bulgare, chinois simplifié, chinois traditionnel, croate, tchèque, danois, néerlandais, anglais, estonien, finnois, Français, allemand, grec, hébreu, Hindi, hongrois, Indonésien, italien, japonais, Kazakh, coréen, letton, lituanien, Malais (Malaisie), norvégien (Bokmål), polonais, portugais (Brésil), portugais (Portugal), roumain, russe, serbe (Latin), slovaque, slovène, espagnol, suédois, thaï, turc, ukrainien et vietnamien.

 - **Q : quels composants de l’expérience de réinitialiser votre mot de passe obtenir marque quand je définis d’organisation personnalisation dans mon répertoire de configurer onglet ?**

 > **A:** Le portail de réinitialiser votre mot de passe s’affichent le logo de votre organisation et également vous permettra configuration du Contact de votre lien administrateur pointer vers un e-mail personnalisé ou une URL. N’importe quel message électronique qui est envoyée par la réinitialisation de mot de passe inclura le logo de votre organisation, couleurs (dans ce cas rouges), nom dans le corps du message et personnalisés à partir de nom. Consultez un exemple avec tous les éléments personnalisés ci-dessous. Pour en savoir plus, lisez aspect réinitialisation de mot de passe de personnalisation.

  ![][001]

 - **Q : Comment puis-je former mes utilisateurs sur accéder à réinitialiser les mots de passe ?**

 > **A:** Vous pouvez envoyer vos utilisateurs à https://passwordreset.microsoftonline.com directement, ou vous pouvez leur demander de cliquer sur le ne peut pas accéder à votre lien compte situé sur n’importe quel scolaire ou ID de travail écran de connexion. Vous pouvez n’hésitez pas à publier ces liens (ou créer des redirections URL leur) dans n’importe quel endroit facilement accessible à vos utilisateurs.

 - **Q : puis-je utiliser cette page à partir d’un appareil mobile ?**

 > **A:** Oui, cette page fonctionne sur les appareils mobiles.

 - **Q : ne vous prennent en charge les comptes active directory local déverrouillage quand les utilisateurs réinitialisons son mot de passe ?**

 > **A:** Oui, la lorsqu’un utilisateur réinitialise son mot de passe et mot de passe en écriture différée a été déployé à toutes les versions d’Azure AD Connect, ou les versions de la synchronisation Active Directory Azure 1.0.0485.0222 ou version ultérieure, puis compte de cet utilisateur sera automatiquement déverrouillée lorsque cet utilisateur réinitialise son mot de passe.

 - **Q : Comment puis-je intégrer du mot de passe directement dans mon bureau connexion expérience utilisateur ?**

 > **A:** Ce n’est pas possible aujourd'hui. Toutefois, si vous avez absolument besoin de cette fonctionnalité et êtes un client Azure AD Premium, vous pouvez installer le Gestionnaire d’identité Microsoft sans frais supplémentaires et déployer la solution de réinitialisation de mot de passe local trouvée pour résoudre cette exigence.

 - **Q : puis-je définir des questions sur la sécurité différentes pour des langues différentes ?**

 > **A:** Non, ce n’est pas possible aujourd'hui, mais nous allons la considérer.

 - **Q : combien de questions nous pouvons configurer pour l’option d’authentification de sécurité Questions ?**

 > **A:** Vous pouvez configurer jusqu'à 20 questions sur la sécurité personnalisée dans le [Portail de gestion Azure](https://manage.windowsazure.com).

 - **Q : combien de temps peuvent être questions de sécurité ?**

 > **A:** Questions sur la sécurité peuvent être compris entre 200 et 3 caractères.

 - **Q : combien de temps peuvent être réponses aux questions sur la sécurité ?**

 > **A:** Réponses peuvent être 3 à 40 caractères.

 - **Q : les en double réponses aux questions sur la sécurité refusées ?**

 > **A:** Oui, nous rejeter en double réponses aux questions sur la sécurité.

 - **Q : mai un utilisateur s’inscrire plusieurs de la même question de sécurité ?**

 > **A:** Non, une fois qu’un utilisateur enregistre une question particulière, il ne peut pas s’inscrire à cette question une seconde fois.

 - **Q : est-il possible de définir une limite minimale de questions de sécurité pour l’enregistrement et réinitialiser ?**

 > **A:** Oui, une limite peut être définie pour l’enregistrement et un autre pour le réinitialiser. questions sur la sécurité 3 à 5 peuvent être nécessaires pour l’enregistrement et 3 à 5 peuvent être nécessaires pour le réinitialiser.

 - **Q : si un utilisateur a été enregistré plus que le nombre maximal de questions requises pour réinitialiser, mode questions sur la sécurité de sélection lors de la réinitialisation ?**

 > **A:** Questions sur la sécurité N sont sélectionnées au hasard déconnecter le nombre total de questions qu'un utilisateur est enregistrée, où N est le nombre minimal de questions requis pour réinitialiser votre mot de passe. Par exemple, si un utilisateur a 5 questions sur la sécurité enregistrées, mais seuls 3 sont requises pour réinitialiser, 3 sur ces 5 est sélectionné aléatoirement et présenté à l’utilisateur au moment de la réinitialisation. Si l’utilisateur reçoit les réponses aux questions incorrect, le processus de sélection se répète pour éviter la question martelage.

 - **Q : empêcher les utilisateurs d’essayer de mot de passe autant de fois dans peu de temps ?**

 > **A:** Oui, il existe plusieurs fonctionnalités de sécurité intégrées à réinitialisation du mot de passe. Les utilisateurs peuvent uniquement essayer 5 tentatives de réinitialisation de mot de passe au sein d’une heure avant d’être verrouillé un délai de 24 heures. Les utilisateurs peuvent uniquement essaie de valider un numéro de téléphone 5 fois au sein d’une heure avant d’être verrouillé un délai de 24 heures. Les utilisateurs peuvent uniquement essayer une méthode d’authentification unique 5 fois au sein d’une heure avant d’être verrouillé un délai de 24 heures.

 - **Q : pendant combien de temps valides la messagerie et le code secret unique SMS ?**

 > **A:** La durée de vie de session pour réinitialiser votre mot de passe est 105 minutes. Cela signifie qu’à partir du début du mot de passe réinitialisé opération, l’utilisateur a 105 minutes pour réinitialiser son mot de passe. La messagerie et le code secret unique SMS ne sont pas valides après cette période.


## <a name="password-management-reports"></a>Rapports sur la gestion de mot de passe

 - **Q : combien de temps faut-il pour les données apparaissent sur les rapports de gestion de mot de passe ?**

 > **A:** Données doivent apparaître dans les rapports de gestion de mot de passe 5 à 10 minutes. Il arrive peut prendre jusqu'à une heure apparaisse.

 - **Q : Comment puis-je filtrer les rapports de gestion de mot de passe ?**

 > **A:** Vous pouvez filtrer les rapports de gestion de mot de passe en cliquant sur la Loupe à l’extrême droite des étiquettes de colonne, en haut du rapport (voir la capture d’écran). Si vous souhaitez effectuer un filtrage plus riche, vous pouvez télécharger le rapport excel et créer un tableau croisé dynamique.

  ![][002]

 - **Q : quelle est le nombre maximal d’événements sont stockés dans les rapports de gestion de mot de passe ?**

 > **A:** Jusqu'à 1 000 mot de passe les événements d’enregistrement de réinitialisation réinitialisation ou mot de passe sont stockés dans les rapports de gestion de mot de passe.  Nous travaillons pour développer ce numéro pour inclure des événements supplémentaires.

 - **Q : comment bientôt résoudre ce problème faire les rapports de gestion de mot de passe ?**

 > **A:** Les rapports de gestion de mot de passe indiquent opérations qui se produisent dans les 30 derniers jours. Nous étudions actuellement comment transformer une durée supérieure. Pour l’instant, si vous devez archiver ces données, vous pouvez télécharger régulièrement les rapports et les enregistrer dans un emplacement distinct.

 - **Q : y a-t-il un nombre maximal de lignes peuvent apparaître sur les rapports de gestion de mot de passe ?**

 > **A:** Oui, un maximum de 1 000 lignes peut-être apparaître dans une ou des rapports de la gestion de mot de passe si elles sont sont présentées dans l’interface utilisateur ou en cours de téléchargement. Nous étudions actuellement comment faire pour augmenter la taille limite.

 - **Q : y a-t-il une API pour accéder à la réinitialisation du mot de passe ou l’enregistrement de données du signalement ?**

 > **A:** Oui, voir la documentation suivante pour savoir comment accéder à la réinitialisation du mot de passe rapports de flux de données.  [Découvrez comment accéder aux mot de passe réinitialiser événements par programme](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).

## <a name="password-writeback"></a>Mot de passe en écriture différée
 - **Q : comment écriture différée de mot de passe fonctionne en arrière-plan ?**

 > **A:** Pour plus d’informations sur ce qui survient lorsque vous activez d’écriture différée du mot de passe, ainsi que la manière dont le flux de données au sein du système dans votre environnement local, voir [que comment votre mot de passe écriture différée fonctionne](active-directory-passwords-learn-more.md#how-password-writeback-works) . Voir [modèle de sécurité d’écriture différée de mot de passe](active-directory-passwords-learn-more.md#password-writeback-security-model) dans le mot de passe écriture différée fonctionne pour découvrir comment nous assurer que écriture différée de mot de passe est un service hautement sécurisé.

 - **Q : combien de temps écriture différée mot de passe faut-il pour l’utiliser ?  Existe-t-il un délai de synchronisation comme avec la synchronisation hachage mot de passe ?**

 > **A:** Écriture différée de mot de passe est instantanée. Il s’agit d’un pipeline synchrone qui fonctionne fondamentalement différemment de la synchronisation de mot de passe hachage. Mot de passe en écriture différée permet aux utilisateurs d’obtenir des commentaires en temps réel sur le succès de leur réinitialisation de mot de passe ou l’opération de modification. La durée moyenne d’une d’écriture différée réussie d’un mot de passe est de 500 ms sous.

 - **Q : quels types de comptes en écriture différée de mot de passe fonctionne pour les ?**

 > **A:** Mot de passe en écriture différée fonctionne pour fédérés et mot de passe hachage synchronisés des utilisateurs.

 - **Q : ne mot de passe en écriture différée applique des stratégies de mot de passe de mon domaine ?**

 > **A:** Oui, mot de passe en écriture différée applique âge de mot de passe, l’historique, la complexité, filtres et toute autre restriction que vous pouvez mettre en place sur les mots de passe de votre domaine local.

 - **Q : est en écriture différée de mot de passe sécurisée ?  Comment puis-je être sûr que je ne pirater ?**

 > **A:** Oui, en écriture différée de mot de passe est extrêmement sécurisée. Pour en savoir plus sur les 4 couches de sécurité implémentée par le service d’écriture différée de mot de passe, consultez le [modèle de sécurité de mot de passe d’écriture différée](active-directory-passwords-learn-more.md#password-writeback-security-model) dans works comment d’écriture différée de mot de passe.




## <a name="links-to-password-reset-documentation"></a>Des liens vers votre mot de passe réinitialisé documentation
Voici des liens vers toutes les pages de documentation de réinitialisation du mot de passe Azure AD :

* **Vous êtes ici parce que vous rencontrez des problèmes en vous connectant ?** Si c’est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [**Mode de fonctionnement**](active-directory-passwords-how-it-works.md) - en savoir plus sur les différents composants de service et de six chacun a
* [**Prise en main**](active-directory-passwords-getting-started.md) - obtenir des informations pour vous permettre aux utilisateurs de réinitialiser et modifier leur mot de passe cloud ou en local
* [**Personnaliser**](active-directory-passwords-customize.md) - Apprenez à personnaliser l’apparence et la convivialité et le comportement du service aux besoins de votre organisation
* [**Meilleures pratiques**](active-directory-passwords-best-practices.md) - Apprenez à déployer rapidement et efficacement gérer les mots de passe de votre organisation
* [**Obtenir des informations**](active-directory-passwords-get-insights.md) - en savoir plus sur notre fonctions intégrées de reporting
* [**Résolution des problèmes**](active-directory-passwords-troubleshoot.md) - Apprenez à rapidement résoudre les problèmes avec le service
* [**En savoir plus**](active-directory-passwords-learn-more.md) - accédez profondeur dans les détails techniques de fonctionnement du service


[001]: ./media/active-directory-passwords-faq/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-faq/002.jpg "Image_002.jpg"
