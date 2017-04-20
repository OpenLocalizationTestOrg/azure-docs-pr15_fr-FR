<properties 
    pageTitle="Serveur de l’authentification multifacteur l’authentification LDAP et Azure"
    description="Il s’agit de la page de l’authentification multifacteur Azure qui aidera dans le déploiement de l’authentification LDAP et serveur de l’authentification multifacteur Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>Serveur de l’authentification multifacteur l’authentification LDAP et Azure


Par défaut, le serveur de l’authentification multifacteur Azure est configuré pour importer ou synchroniser les utilisateurs d’Active Directory. Toutefois, il peut être configuré pour créer une liaison dans des répertoires LDAP différents, par exemple un annuaire ADAM ou contrôleur de domaine Active Directory spécifique. Lorsqu’il est configuré pour vous connecter à un répertoire via LDAP, le serveur de l’authentification multifacteur Azure peut être configuré pour agir en tant que LDAP proxy pour effectuer une authentification. Il permet également de l’utilisation de liaison LDAP comme cible RADIUS, avant l’authentification des utilisateurs lors de l’utilisation de l’authentification IIS, ou pour l’authentification principale dans le portail d’utilisateur d’authentification multifacteur Azure.

Lorsque vous utilisez l’authentification multifacteur Azure comme un proxy LDAP, le serveur de l’authentification multifacteur Azure est inséré entre le client LDAP (par exemple, appareil VPN, application) et le serveur d’annuaire LDAP afin d’ajouter l’authentification multifacteur. Pour l’authentification multifacteur Azure fonction, le serveur de l’authentification multifacteur Azure doit être configuré pour communiquer avec les serveurs de client et de l’annuaire LDAP. Dans cette configuration, le serveur de l’authentification multifacteur Azure accepte les demandes LDAP de serveurs client et les applications et les transmet au serveur d’annuaire LDAP cible pour valider les informations d’identification primaires. Si la réponse de l’annuaire LDAP affiche qu’ils les informations d’identification primaires sont valides, l’authentification multifacteur Azure seconde facteurs authentifie et envoie une réponse au client LDAP. L’authentification entière fonctionnera uniquement si l’authentification au serveur LDAP et l’authentification multifacteur réussissent.





## <a name="ldap-authentication-configuration"></a>Configuration de l’authentification LDAP


Pour configurer l’authentification LDAP, installez le serveur de l’authentification multifacteur Azure sur Windows server. Procédez comme suit :

1. Au sein du serveur de l’authentification multifacteur Azure cliquez sur l’icône de l’authentification LDAP dans le menu de gauche.
2. Cochez la case Activer l’authentification LDAP.![Authentification LDAP](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)
3. Sous l’onglet Clients modifier les ports TCP et SSL si le service LDAP de l’authentification multifacteur Azure doit être relié ports non standard pour écouter les demandes LDAP à partir des clients qui seront configurés.
4. Si vous prévoyez d’utiliser LDAPS à partir du client sur le serveur de l’authentification multifacteur Azure, un certificat SSL doit être installé sur le serveur qui exécute sur le serveur. Cliquez sur Parcourir... bouton en regard de la zone certificat SSL et sélectionnez le certificat installé qui sera utilisé pour la connexion sécurisée.
5. Cliquez sur Ajouter... bouton.
6. Dans la boîte de dialogue Ajouter un Client LDAP, entrez l’adresse IP du matériel, serveur ou l’application doit effectuer l’authentification au serveur et un nom de l’Application (facultatif). Le nom de l’Application apparaît dans les rapports de l’authentification multifacteur Azure et peut être affiché dans les messages d’authentification SMS ou une application Mobile.
7. Activer la case nécessite l’authentification multifacteur Azure utilisateur correspondance si tous les utilisateurs ont été ou importées sur le serveur et soumis à authentification multi-utilisateurs facteurs. Si un nombre important d’utilisateurs n’ont pas encore été importé dans le serveur et/ou sera exempt de l’authentification multi-utilisateurs facteurs, laissez la case désactivée. Voir le fichier d’aide pour plus d’informations sur cette fonctionnalité.
8. Vous pouvez répéter les étapes 5 à 7 pour ajouter des clients LDAP supplémentaires.
9. Lorsque l’authentification multifacteur Azure est configurée pour recevoir des authentifications LDAP, il doit être proxy ces authentifications à l’annuaire LDAP. Par conséquent, l’onglet cible s’affiche uniquement un seul, grisée option pour utiliser une cible LDAP. Pour configurer la connexion d’annuaire LDAP, cliquez sur l’icône d’intégration d’annuaire.
10. Sous l’onglet Paramètres, sélectionnez le bouton d’option utiliser spécifique LDAP configuration.
11. Cliquez sur la modification... bouton.
12. Dans la boîte de dialogue Modifier la Configuration LDAP, remplissez les champs avec les informations requises pour vous connecter à l’annuaire LDAP. Descriptions des champs sont incluses dans le tableau ci-dessous. Remarque : Ces informations sont également incluses dans le fichier d’aide serveur de l’authentification multifacteur Azure.![Intégration d’annuaire](./media/multi-factor-authentication-get-started-server-ldap/ldap.png)
13. Tester la connexion LDAP en cliquant sur le bouton Test.
14. Si le test de connexion LDAP a réussi, cliquez sur le bouton OK.
15. Cliquez sur l’onglet filtres. Le serveur est préconfigurée de manière à charger conteneurs, les groupes de sécurité et les utilisateurs à partir d’Active Directory. Si une liaison à un autre service d’annuaire LDAP, vous devrez probablement modifier les filtres affichées. Cliquez sur le lien aide pour plus d’informations sur les filtres.
16. Cliquez sur onglet attributs. Le serveur est préconfigurée de manière à mapper les attributs de Active Directory.
17. Si vous liez à un autre service d’annuaire LDAP ou pour modifier les mappages d’attributs préconfiguré, cliquez sur la modification... bouton.
18. Dans la boîte de dialogue Modifier les attributs, modifiez les mappages d’attributs pour votre annuaire LDAP. Noms des attributs pouvant être saisis ou sélectionnés en cliquant sur le... bouton en regard de chaque champ.
19. Cliquez sur le lien aide pour plus d’informations sur les attributs.
20. Cliquez sur le bouton OK.
21. Cliquez sur l’icône Paramètres de la société et sélectionnez l’onglet résolution du nom d’utilisateur.
22. Si la connexion à Active Directory à partir d’un serveur à un domaine, vous devriez pouvoir laisser l’utilisation de Windows sécurité identificateurs pour la correspondance des noms d’utilisateur case d’option sélectionnée. Dans le cas contraire, sélectionnez l’attribut identificateur unique utiliser LDAP de case d’option noms d’utilisateur correspondant. Une fois sélectionné, le serveur de l’authentification multifacteur Azure tentera de résoudre chaque nom d’utilisateur vers un identificateur unique dans l’annuaire LDAP. Une recherche LDAP à effectuer sur le nom d’utilisateur attributs définis dans l’intégration d’annuaire -> onglet attributs. Lorsqu’un utilisateur s’authentifie, le nom d’utilisateur est résolu à l’identificateur unique dans l’annuaire LDAP et l’identificateur unique sera utilisé pour la correspondance de l’utilisateur dans le fichier de données de l’authentification multifacteur Azure. Ainsi, pour les comparaisons sans respecter la casse, comme nom d’utilisateur bien comme versions longue et courte formats cela a terminé la configuration du serveur de l’authentification multifacteur Azure. Le serveur est maintenant à l’écoute sur les ports configurés pour les demandes d’accès LDAP à partir des clients configurés et configuré pour serveur proxy ces demandes à l’annuaire LDAP pour l’authentification.


## <a name="ldap-client-configuration"></a>Configuration des clients LDAP

Pour configurer le client LDAP, suivez les instructions :

- Configurer votre appareil, serveur ou une application pour l’authentification via LDAP sur le serveur de l’authentification multifacteur Azure comme s’il s’agissait dans l’annuaire LDAP. Vous devez utiliser les mêmes paramètres que vous utiliseriez normalement pour se connecter directement à votre annuaire LDAP, à l’exception du nom du serveur ou l’adresse IP qui correspond à celui du serveur de l’authentification multifacteur Azure.
- Configurer le délai d’attente LDAP à 30 à 60 secondes pour qu’il est temps de valider les informations d’identification de l’utilisateur avec l’annuaire LDAP, effectuer l’authentification seconde facteurs, recevez sa réponse et puis répondre à la demande d’accès LDAP.
- Si vous utilisez LDAPS, le matériel ou un serveur effectue les requêtes LDAP doit approuver le certificat SSL installé sur le serveur de l’authentification multifacteur Azure.
