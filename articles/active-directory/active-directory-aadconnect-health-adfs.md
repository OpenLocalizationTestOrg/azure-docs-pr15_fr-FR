
<properties
    pageTitle="À l’aide de Azure AD connectez santé avec AD FS | Microsoft Azure"
    description="Il s’agit de la page Azure AD connecter santé comment contrôler vos localement infrastructure AD FS."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="using-azure-ad-connect-health-with-ad-fs"></a>À l’aide de Azure AD connectez santé avec AD FS
La documentation suivante est spécifique à votre infrastructure AD FS dont l’état de connexion AD Azure d’analyse. Pour plus d’informations sur la surveillance Azure AD Connect (synchronisation) dont l’état de connexion AD Azure, reportez-vous [à l’aide de Azure AD se connecter santé pour la synchronisation](active-directory-aadconnect-health-sync.md). En outre, pour plus d’informations sur le contrôle des Services de domaine Active Directory dont l’état de connexion AD Azure, reportez-vous [à l’aide de Azure AD connecter santé avec les services AD DS](active-directory-aadconnect-health-adds.md).

## <a name="alerts-for-ad-fs"></a>Alertes pour AD FS
La section Azure AD connecter les alertes de fonctionnement fournit la liste des alertes actives. Chaque alerte inclut des informations pertinentes, procédure de résolution et des liens vers la documentation associée.

Vous pouvez double-cliquer sur une alerte active ou résolue, pour ouvrir une nouvelle carte avec des informations supplémentaires, étapes à suivre pour résoudre l’alerte et des liens vers la documentation. Vous pouvez également afficher les données historiques sur les alertes qui ont été résolus par le passé.

![Azure AD Connect portail sur la santé](./media/active-directory-aadconnect-health/alert2.png)



## <a name="usage-analytics-for-ad-fs"></a>L’utilisation Analytique pour AD FS
Azure AD connecter santé l’utilisation Analytique analyse le trafic d’authentification de vos serveurs de fédération. Vous pouvez double-cliquer sur la zone analytique de l’utilisation, pour ouvrir la carte l’utilisation analytique, qui affiche plusieurs métriques et regroupements.

>[AZURE.NOTE] Pour utiliser l’utilisation Analytique avec AD FS, vous devez vous assurer que AD FS l’audit est activé. Pour plus d’informations, voir [Activer l’audit pour AD FS](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs).

![Azure AD Connect portail sur la santé](./media/active-directory-aadconnect-health/report1.png)

Pour sélectionner des mesures supplémentaires, spécifiez un intervalle de temps, ou pour modifier le regroupement, avec le bouton droit sur le graphique analytique à l’utilisation et sélectionnez Modifier le graphique. Puis vous pouvez spécifier une plage horaire, sélectionnez une mesure de l’autre et modifier le regroupement. Vous pouvez afficher la distribution du trafic d’authentification basée sur les différentes « métriques » et regrouper chaque métrique à l’aide de paramètres pertinents « regrouper par » décrits dans le tableau suivant :

| Métrique | Regrouper par | Quels le groupement : et pourquoi il est utile ? |
| ------ | -------- | -------------------------------------------- |
| Nombre total de demandes : Le nombre total de requêtes traitées par le service de fédération | Tous les | Indique le nombre de nombre total de demandes sans regroupement. |
|  | Application | Le nombre total de demandes en fonction de la partie de confiance ciblée des groupes. Ce regroupement est utile de comprendre quelle application reçoit le pourcentage du trafic total. |
|  | Serveur | Total de requêtes basé sur le serveur qui a traité la demande des groupes. Ce regroupement est utile de comprendre la distribution de la charge du trafic total. |
|  | Jointure poste de travail | Total de requêtes en fonction de si elles sont en provenance de périphériques qui sont jointe un espace de travail (connu) des groupes. Ce regroupement est utile de comprendre si vos ressources sont accessibles à l’aide d’appareils sont inconnus de l’infrastructure d’identité. |
|  | Méthode d’authentification | Le nombre total de demandes en fonction de la méthode d’authentification utilisée pour l’authentification des groupes. Ce regroupement est utile de comprendre la méthode d’authentification commune qui est utilisée pour l’authentification. Voici les méthodes d’authentification possibles <ol> <li>L’authentification Windows (Windows)</li> <li>Authentification (formulaires) basée sur les formulaires</li> <li>Authentification unique (authentification unique)</li> <li>X509 du certificat d’authentification (certificat)</li> <br>Si les serveurs de fédération reçoivent la demande avec une authentification des cookies, cette requête est considérée comme l’authentification unique (Single Sign On). Dans ce cas, si les cookies est valide, l’utilisateur n’est pas invité à fournir des informations d’identification et obtient un accès transparent à l’application. Ce comportement est courant si vous avez plusieurs parties de confiance protégés par les serveurs de fédération. |
|  | Emplacement réseau | Regroupe les requêtes total en fonction de l’emplacement réseau de l’utilisateur. Il peut s’agir soit intranet ou extranet. Ce regroupement est utile de savoir à quel pourcentage du trafic arrive à partir de l’intranet ou extranet. |
| Nombre total de demandes a échoué : Le nombre total Échec de requêtes traitées par le service de fédération. <br> (Cette métrique est uniquement disponible sur AD FS pour Windows Server 2012 R2)| Type d’erreur. | Affiche le nombre d’erreurs basées sur des types d’erreur prédéfinie. Ce regroupement est utile de comprendre les types d’erreurs courantes. <ul><li>Nom d’utilisateur ou de mot de passe incorrect : erreurs en raison d’incorrect nom d’utilisateur ou mot de passe.</li> <li>« Verrouillage extranet » : échecs en raison des demandes provenant d’un utilisateur qui a été verrouillé à partir d’extranet </li><li> « Expiré mot de passe » : échecs dû à des utilisateurs qui se connectent à l’aide d’un mot de passe.</li><li>« Désactivé compte » : échecs dû à des utilisateurs qui se connectent avec un compte désactivé.</li><li>« APPAREIL authentification » : échecs en raison des utilisateurs authentifie en utilisant l’authentification de l’appareil.</li><li>« Authentification par certificat utilisateur » : échecs en raison des utilisateurs authentifie en raison d’un certificat non valide.</li><li>« L’authentification Multifacteur » : échecs en raison de l’utilisateur ne parvient pas à s’authentifier à l’aide de l’authentification multifacteur.</li><li>« D’autres informations d’identification » : « Autorisation d’émission » : échecs en raison d’échecs d’autorisation.</li><li>« Délégation d’émission » : échecs en raison des erreurs de délégation d’émission.</li><li>« L’acceptation des jetons » : échecs en raison de ADFS en refusant le jeton à partir d’un fournisseur d’identité tiers.</li><li>« Protocole » : échec en raison des erreurs de protocole.</li><li>« Inconnue » : intercepter tous. Les autres échecs qui n’entrent pas dans les catégories définis.</li> |
|  | Serveur | Regroupe les erreurs basés sur le serveur. Ce regroupement est utile de comprendre la distribution d’erreur sur les serveurs. Inégale peut être un indicateur d’un serveur dans un état défectueux. |
|  | Emplacement réseau | Regroupe les erreurs selon l’emplacement réseau des requêtes (intranet vs extranet). Ce regroupement est utile de comprendre le type de requêtes qui échouent. |
|  | Application | Regroupe les échecs en fonction de l’application cible (partie de confiance). Ce regroupement est utile de comprendre quelle application ciblée voit au nombre d’erreurs. |
| Nombre d’utilisateurs : nombre moyen d’utilisateurs uniques actifs dans le système | Tous les | Cette métrique fournit un compteur de nombre moyen d’utilisateurs à l’aide du service de fédération dans le secteur de l’heure sélectionnée. Les utilisateurs ne sont pas regroupés. <br>La moyenne dépend de la tranche horaire sélectionnée. |
|  | Application | Nombre moyen d’utilisateurs en fonction de l’application cible (partie de confiance) des groupes. Ce regroupement est utile de connaître le nombre d’utilisateurs est à l’aide de l’application. |


## <a name="performance-monitoring-for-ad-fs"></a>Analyse des performances pour AD FS
Azure AD connecter santé analyse des performances fournit des informations d’analyse des métriques. Si vous sélectionnez l’option de surveillance, s’ouvre une nouvelle carte avec des informations détaillées sur les mesures.


![Azure AD Connect portail sur la santé](./media/active-directory-aadconnect-health/perf1.png)


En sélectionnant l’option de filtre en haut de la carte, vous pouvez filtrer par serveur pour afficher les mesures d’un serveur individuel. Pour modifier les mesures, avec le bouton droit sur le diagramme analyse sous la carte surveillance et sélectionnez Modifier le graphique. Puis, à partir de la nouvelle carte qui s’ouvre, vous pouvez sélectionner des mesures supplémentaires dans la liste déroulante et spécifier une plage horaire pour afficher les données de performance.

## <a name="reports-for-ad-fs"></a>Rapports d’AD FS
Azure AD connecter santé fournit des rapports sur l’activité et les performances de AD FS. Ces rapports aident les administrateurs Familiarisez-vous avec les activités dans leurs serveurs AD FS.

### <a name="top-50-users-with-failed-usernamepassword-logins"></a>Top 50 utilisateurs avec des échecs de connexion nom d’utilisateur et mot de passe

Une des raisons courantes pour une demande d’authentification échouées sur un serveur AD FS est une demande avec les informations d’identification non valides, c'est-à-dire un nom d’utilisateur erroné ou le mot de passe. En règle générale, qui se produit à des utilisateurs en raison des mots de passe complexes, les mots de passe oubliés ou de fautes de frappe.

Mais il existe d’autres raisons pouvant entraîner un nombre inattendu de requêtes traités par vos serveurs AD FS, telles que : une application qui cache les informations d’identification utilisateur et les informations d’identification d’expiration ou un utilisateur malveillant tente de vous connecter à un compte avec une série de mots de passe connus. Ces deux exemples sont valides raisons pouvant entraîner une augmentation dans les requêtes.

Azure AD connecter santé pour ADFS fournit un rapport sur 50 utilisateurs avec les tentatives de connexion a échoué en raison de nom d’utilisateur non valide ou mot de passe. Ce rapport est atteint en traitant les événements d’audit générés par tous les serveurs AD FS dans les batteries de serveurs

![Azure AD Connect portail sur la santé](./media/active-directory-aadconnect-health-adfs/report1a.png)

Dans ce rapport vous avez facilement aux éléments d’information suivantes :

- Nombre total d’échecs de requêtes avec nom d’utilisateur/mot de passe erroné au cours des 30 derniers jours
- Nb. moyen d’utilisateurs qui a échoué avec une connexion de nom d’utilisateur/mot de passe incorrect par jour.


Cliquez sur ce composant pour accéder à la carte état principal qui fournit des détails supplémentaires. Cette carte comprend un graphique avec informations de tendance à utiliser pour établir un planning de référence sur les requêtes avec le mauvais nom d’utilisateur ou mot de passe. En outre, il fournit la liste des utilisateurs de 50 premières avec le nombre de tentatives.

Le graphique fournit les informations suivantes :

- Le nombre total d’échecs de connexion en raison d’un nom d’utilisateur/mot de passe incorrect sur une base par jour.
- Le nombre total d’utilisateurs uniques qui échec des connexions sur une base par jour.
- Adresse IP du client de la dernière demande

![Azure AD Connect portail sur la santé](./media/active-directory-aadconnect-health-adfs/report3a.png)

Le rapport fournit les informations suivantes :

| Élément de rapport | Description
| ------ | -------- |
|ID d’utilisateur| Affiche l’ID utilisateur qui a été utilisé. Cette valeur est ce que l’utilisateur a tapé, c'est-à-dire dans certains cas, le mauvais utilisateur ID en cours d’utilisation.|
|Échecs| Indique le nombre total de tentatives pour cet ID utilisateur spécifique. Le tableau est trié avec le plus grand nombre d’échecs dans l’ordre décroissant.|
|Dernier échec| Indique les date et heure lors de la dernière erreur s’est produite.
|Dernière défaillance IP | Affiche l’adresse IP du Client à partir de la dernière demande incorrecte.|



>[AZURE.NOTE] Ce rapport est automatiquement mis à jour une fois toutes les deux heures avec les nouvelles informations recueillies dans ce délai. Par conséquent, ne peuvent pas figurer dans le rapport des tentatives de connexion dans les deux dernières heures.



## <a name="related-links"></a>Liens connexes

* [Azure AD Connect d’intégrité](active-directory-aadconnect-health.md)
* [Azure AD Connect Installation de l’Agent d’intégrité](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect opérations d’intégrité](active-directory-aadconnect-health-operations.md)
* [L’utilisation d’état connecter Azure AD pour la synchronisation](active-directory-aadconnect-health-sync.md)
* [À l’aide de Azure AD connectez santé avec les services AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect santé Forum aux questions](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect historique des versions d’intégrité](active-directory-aadconnect-health-version-history.md)
