<properties
    pageTitle="Azure Active Directory hybride identité conception - définir une stratégie d’adoption d’identité hybride | Microsoft Azure"
    description="Contrôle d’accès conditionnelle, Azure Active Directory vérifie les conditions spécifiques que vous avez choisi lors de l’authentification de l’utilisateur et avant d’autoriser l’accès à l’application. Une fois que ces conditions sont remplies, l’utilisateur est authentifié et autorisé à accéder à l’application."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>


# <a name="define-a-hybrid-identity-adoption-strategy"></a>Définir une stratégie d’adoption identité hybride

Dans cette tâche, vous devez définir la stratégie hybride identité adoption de votre solution identité hybride répondre aux besoins de l’entreprise qui ont été présentés dans :

- [Déterminer les besoins professionnels](active-directory-hybrid-identity-design-considerations-business-needs.md)
- [Déterminer les besoins de synchronisation d’annuaire](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
- [Déterminer la configuration requise pour l’authentification multifacteur](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Définition de la stratégie des besoins professionnels
Premières adresses tâche détermination de l’entreprise organisations a besoin.  Cela peut être très large et glissement étendue peut se produire si vous n’êtes pas prudent.  Au début qu’il soit simple, mais toujours n’oubliez pas de planifier une conception qui doit s’adapter à faciliter le modifier ultérieurement.  Qu’il s’agisse d’un simple de conception ou un identifiant extrêmement complexe, Azure Active Directory est la plate-forme Microsoft Identity qui prend en charge d’Office 365, Microsoft Online Services et le cloud applications prenant en charge.

## <a name="define-an-integration-strategy"></a>Définir une stratégie d’intégration
Microsoft a trois scénarios d’intégration principale qui sont cloud identités identités synchronisées et des identités fédérées.  Vous devez planifier adoptant une de ces stratégies d’intégration.  La stratégie que vous choisissez peut varier et les décisions lors du choix d’une peut inclure, quel type de l’expérience utilisateur que vous voulez fournir, offrez-vous une partie de l’infrastructure existant déjà en place, et quels sont les plus rentables.  
 
![](./media/hybrid-id-design-considerations/integration-scenarios.png)

Les scénarios définis dans l’illustration ci-dessus sont :

- **Identités cloud**: il s’agit des identités qui existent uniquement dans le cloud.  Dans le cas d’Azure AD, ils peut résider spécifiquement dans votre annuaire Azure AD.
- **Synchronisé**: il s’agit des identités qui existent en local et dans le cloud.  À l’aide de Azure AD Connect, ces utilisateurs créés ou rejoint avec des comptes Azure AD existants.  Hachage de mot de passe de l’utilisateur est synchronisé à partir de l’environnement local dans le cloud sur ce qui est appelé un hachage de mot de passe.  Lors de la synchronisation à l’aide de l’inconvénient est que si un utilisateur est désactivé dans l’environnement local, il peut prendre jusqu'à 3 heures pour cet état du compte s’affiche dans Azure AD.  Il s’agit en raison de l’intervalle de temps de synchronisation.
- **Fédéré**: ces identités existent à la fois en local et dans le cloud.  À l’aide de Azure AD Connect, ces utilisateurs créés ou rejoint avec des comptes Azure AD existants.  
 
>[AZURE.NOTE]
Pour plus d’informations sur la synchronisation options lisez [intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

Le tableau suivant aidera à déterminer les avantages et inconvénients de chacun des stratégies suivantes :

| Stratégie         | Avantages                                                                                                                                                                                                                                                  | Inconvénients liés                                                                                                                                                                                                                                                                                                                                                  |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Identités cloud** | Plus facile à gérer pour petite entreprise. <br> Rien à installer du matériel supplémentaire non locaux nécessité<br>Si l’utilisateur quitte l’entreprise facilement désactivée                                                                                                   | Les utilisateurs doivent se connecter lorsqu’ils accèdent à charges de travail dans le cloud <br> Les mots de passe peuvent être ou non identique pour les identités cloud et en local                                                                                                                                                                                                                     |
| **Synchronisé**     | Mot de passe en local s’authentifier à la fois en local et en nuage répertoires <br>Plus facile à gérer pour les entreprises de petites, moyennes ou grandes <br>Les utilisateurs peuvent avoir authentification unique (SSO) pour certaines ressources <br> Méthode de Microsoft par défaut pour la synchronisation <br> Plus facile à gérer | Certains utilisateurs peuvent se montrer réticents à synchroniser leurs répertoires avec le cloud échéance police spécifique de la société                                                                                                                                                                                                                                                  |
| **Fédéré**        | Les utilisateurs peuvent avoir authentification unique (SSO) <br>Si un utilisateur s’arrête ou quitte, le compte peut être immédiatement désactivé et accès révoqué,<br> Prend en charge des scénarios avancés qui ne peuvent pas être, effectuée avec synchronisés                                           | Installer et configurer en plusieurs étapes <br> Maintenance plus élevé <br> Peut nécessiter du matériel supplémentaire pour l’infrastructure STS <br> Peut nécessiter du matériel supplémentaire pour installer le serveur de fédération. Logiciel supplémentaire est obligatoire si AD FS est utilisé <br> Nécessitent une installation complète pour l’authentification unique <br> Point critique de panne si le serveur de fédération est vers le bas, les utilisateurs ne peuvent pas s’authentifier |

### <a name="client-experience"></a>Expérience client
La stratégie que vous utilisez détermine l’expérience de connexion utilisateur.  Les tableaux suivants vous fournissent des informations sur les utilisateurs doivent s’attendre leur expérience de connexion à être.  Notez que tous les fournisseurs d’identité fédérée prennent en charge l’authentification unique dans tous les scénarios.

**Applications réseau associés à un domaine et privées**:
 

|                              | Identité synchronisée      | Identité fédérée                                           |
|------------------------------|----------------------------|--------------------------------------------------------------|
| Navigateurs Web                 | Authentification basée sur les formulaires | authentification unique, parfois doit fournir des ID de l’organisation |
| Outlook                      | Invite d’identification     | Invite d’identification                                       |
| Skype entreprise (Lync)    | Invite d’identification     | connexion unique pour Lync, vous y êtes invité des informations d’identification pour Exchange   |
| SkyDrive Pro                 | Invite d’identification     | authentification unique                                               |
| Office Pro Plus abonnement | Invite d’identification     | authentification unique                                               |

**Externes ou non approuvée sources**:

|                              | Identité synchronisée      | Identité fédérée                                           |
|------------------------------|----------------------------|--------------------------------------------------------------|
| Navigateurs Web                 | Authentification basée sur les formulaires |  Authentification basée sur les formulaires |
| Outlook, Skype entreprise (Lync), Skydrive Pro, abonnement Office| Invite d’identification     | Invite d’identification                                       |
| Exchange ActiveSync    | Invite d’identification     | connexion unique pour Lync, vous y êtes invité des informations d’identification pour Exchange   |
| Applications mobiles                 | Invite d’identification     | Invite d’identification                                               |

Si vous avez déterminé à partir de la tâche 1 que vous avez un 3e partie IdP ou sont accéder pour utiliser un à fournir la fédération avec Azure Active Directory, vous devez tenir compte des fonctionnalités prises en charge suivantes :
- N’importe quel fournisseur SAML 2.0 qui est conforme pour le profil SP légère peut prendre en charge l’authentification Azure AD et applications associées
- Prend en charge l’authentification passive, qui facilite auth à OWA, SPO, etc..
- Clients Exchange Online peuvent être pris en charge par le biais du SAML 2.0 amélioré Client profil (ECP)

Vous devez également Sachez quelles sont les fonctionnalités ne seront pas disponibles :

- Sans prise en charge de la gestion de la confidentialité-Web/Federation, tous les autres clients actives sauts de page
 - Que signifie qu’aucun client Lync, le client OneDrive, abonnement Office, Office Mobile avant Office 2016
- Transition de Microsoft Office à l’authentification passive permettra pour prendre en charge intégral SAML 2.0 IdPs, mais reste d’une prise en charge sur une base de client par client


>[AZURE.NOTE]
Pour obtenir la liste la plus à jour lire l’article http://aka.ms/ssoproviders.

## <a name="define-synchronization-strategy"></a>Définition de la stratégie de synchronisation
Dans cette tâche vous définirez les outils qui seront utilisés pour synchroniser l’organisation locale données au cloud et vous devez utiliser la topologie.  Parce que la plupart des organisations utilisent Active Directory, les informations sur l’utilisation de Azure AD Connect pour régler les questions ci-dessus sont fournies en détail.  Pour les environnements qui n’ont pas Active Directory, il est informations sur l’utilisation de FIM 2010 R2 ou MIM 2016 afin de planifier cette stratégie.  Toutefois, les versions ultérieures de Azure AD Connect prend en charge les annuaires LDAP, donc selon votre barre de planning, ces informations peuvent être en mesure de vous aider.

###<a name="synchronization-tools"></a>Outils de synchronisation
Années, plusieurs outils de synchronisation ont existant et utilisés pour différents scénarios.  Azure AD Connect n’existe actuellement déplacement vers l’outil de choix pour tous les scénarios pris en charge.  AAD Sync et DirSync sont toujours également autour et peuvent même être présent dans votre environnement maintenant. 

>[AZURE.NOTE]
Pour les dernières informations concernant les fonctionnalités prises en charge de chaque outil, lisez article [comparaison des outils Directory integration](active-directory-hybrid-identity-design-considerations-tools-comparison.md) .  

### <a name="supported-topologies"></a>Topologies prises en charge
Lorsque vous définissez une stratégie de synchronisation, vous devez définir la topologie qui est utilisée. Selon les informations qui a été définies à l’étape 2, vous pouvez déterminer quelle topologie est l’outil à utiliser. La forêt unique, seule la topologie Azure AD est les plus courants et se compose d’une forêt Active Directory et une seule instance d’Azure AD.  Il doit être utilisé dans la majorité des scénarios et la topologie attendue lors de l’utilisation de l’installation Azure AD connecter Express comme indiqué dans l’illustration ci-dessous.
 
![](./media/hybrid-id-design-considerations/single-forest.png)Une seule forêt scénario il est très courant pour même les petites et grandes entreprises possèdent plusieurs forêts, comme illustré dans la Figure 5.

>[AZURE.NOTE]
Pour plus d’informations sur les différentes en local et topologies Azure AD avec Azure AD Connect synchronisation lire l’article [Topologies pour Azure AD Connect](active-directory-aadconnect-topologies.md).


![](./media/hybrid-id-design-considerations/multi-forest.png) 

Scénario multi-forêt

Si cela le cas, la topologie multi-forest-simples Azure AD doit être pris en considération si les éléments suivants sont remplies :

- Les utilisateurs ont uniquement 1 identité dans toutes les forêts : la section utilisateurs identification unique ci-dessous décrit plus en détail.
- L’utilisateur s’authentifie à la forêt dans lequel se trouve son identité
- UPN et point d’ancrage Source (id immuable) proviendra cette forêt
- Toutes les forêts sont accessibles par Azure AD Connect, cela signifie qu’il n’a pas besoin être domaine rejoint et peut être placé dans une DMZ si cela facilite cela.
- Les utilisateurs n'ont qu’une seule boîte aux lettres
- La forêt qui héberge la boîte aux lettres d’un utilisateur possède la meilleure qualité de données pour les attributs visibles dans la liste d’adresses globale Exchange (LAG)
- S’il n’existe aucune boîte aux lettres de l’utilisateur, puis une forêt peut servir à contribuer ces valeurs
- Si vous avez une boîte aux lettres lié, puis vous trouverez également un autre compte dans un autre domaine utilisé pour se connecter.

>[AZURE.NOTE]
Les objets qui existent dans les deux en local et dans le cloud sont « connectés » via un identificateur unique. Dans le cadre de la synchronisation d’annuaires, cet identificateur unique est appelé la SourceAnchor. Dans le contexte de l’authentification unique, il est appelé la ImmutableId. [Azure AD Connect les concepts](active-directory-aadconnect-design-concepts.md#sourceanchor) de plus de considérations relatives à l’utilisation de SourceAnchor.

Si ce qui précède n’est pas remplie et que vous avez plusieurs comptes actif ou plusieurs boîtes aux lettres, Azure AD Connect sera sélectionnez-en un et ignorer l’autre.  Si vous avez lié des boîtes aux lettres, mais pas d’autre compte, ces comptes ne seront pas exportées vers Azure Active Directory et cet utilisateur ne sera pas membre de tous les groupes.  Ceci est différent de comment il a été par le passé avec DirSync et est volontaire pour une meilleure prise en charge ces scénarios forêts multiples. Un scénario de forêt multiples est présenté dans l’illustration ci-dessous.
 
![](./media/hybrid-id-design-considerations/multiforest-multipleAzureAD.png) 
 
**Forêt multiples scénario Azure AD multiples**

Il est recommandé d’avoir qu’un seul répertoire dans Azure AD pour une organisation, mais il est pris en charge il une relation 1:1 est conservée entre un serveur de synchronisation Azure AD Connect et un répertoire Azure AD.  Pour chaque instance d’Azure AD, vous avez besoin d’une installation d’Azure AD Connect.  En outre, Azure AD, par la conception est isolé et les utilisateurs dans une instance de Azure AD ne seront pas en mesure de voir les utilisateurs dans une autre instance.

Il est possible et pris en charge pour vous connecter une instance locale d’Active Directory à plusieurs annuaires Azure AD comme indiqué dans l’illustration ci-dessous :

![](./media/hybrid-id-design-considerations/single-forest-flitering.png) 
 

**Scénario de filtrage basée sur une forêt**

Pour ce faire les éléments suivants doit être réunies :

- Serveurs de synchronisation Azure AD Connect doivent être configurés pour le filtrage de sorte qu’ils ont un jeu d’objets qui s’excluent mutuellement.  Cela fait, par exemple, par étendue chaque serveur d’un domaine particulier ou d’une unité d’organisation.
- Un domaine DNS peut uniquement être enregistré dans un seul répertoire Azure AD afin que l’UPN des utilisateurs dans les locaux AD doit utiliser les espaces de noms distincts
- Les utilisateurs dans une instance de Azure AD sera uniquement en mesure de voir les utilisateurs de leur instance.  Ils ne seront pas en mesure de voir les utilisateurs dans les autres cas
- Seul l’un des répertoires Azure AD peut activer Exchange hybride avec les locaux AD
- Exclusivité commun s’applique également aux écriture différée.  Cela permet de certaines fonctionnalités d’écriture différée non pris en charge avec cette topologie dans la mesure où ils s’appuient sur une configuration unique en local.  Cela inclut :
 - Groupe écriture différée avec configuration par défaut
 - APPAREIL écriture différée


N’oubliez pas que les éléments suivants ne sont pas prise en charge et ne doivent pas être choisies en tant qu’implémentation :

- Il n’est pas pris en charge pour avoir plusieurs serveurs de synchronisation Azure AD Connect qui se connectent à l’annuaire Active Directory Azure même même s’ils sont configurés pour synchroniser l’ensemble s’excluent mutuellement d’objet
- Il est non prises en charge pour synchroniser l’utilisateur même à plusieurs annuaires Azure AD. 
- Il est également pris en charge pour modifier une configuration pour que les utilisateurs dans un seul Azure AD apparaissent comme des contacts dans un autre répertoire Azure AD. 
- Il est également non prises en charge pour la modification de synchronisation Azure AD Connect pour vous connecter à plusieurs annuaires Azure AD.
- Azure AD répertoires sont par nature isolé. Il est non prises en charge pour modifier la configuration de synchronisation Azure AD Connect pour lire les données à partir d’un autre répertoire Azure AD lors d’une tentative de créer une liste d’adresses globale courantes et unifiée entre les annuaires. Il est également non prises en charge pour exporter les utilisateurs en tant que contacts vers un autre en local annonce à l’aide de synchronisation Azure AD Connect.


>[AZURE.NOTE]
Si votre organisation restreint des ordinateurs sur votre réseau de se connecter à Internet, cet article répertorie les points de terminaison (noms de domaine complets, IPv4 et IPv6 plages d’adresses) que vous devez inclure dans votre sortant autorise les listes et Internet Explorer des Sites approuvés du client ordinateurs pour vous assurer que vos ordinateurs peuvent correctement Office 365. Pour plus d’informations, lisez [Office 365 URL et plages d’adresses IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).

## <a name="define-multi-factor-authentication-strategy"></a>Définition de la stratégie de l’authentification multifacteur
Dans cette tâche, vous allez définir la stratégie de l’authentification multifacteur à utiliser.  L’authentification multifacteur Azure est disponible en deux versions différentes.  Une est un sur le nuage et l’autre est locale en fonction de l’utilisation du serveur de l’authentification Multifacteur Azure.  En fonction de l’évaluation ci-dessus, vous pouvez déterminer quelle solution est celui qui convient pour votre stratégie.  Utilisez le tableau ci-dessous pour déterminer quelle option Création meilleures répondre aux exigences de sécurité de votre entreprise :

Options de création de plusieurs facteurs :

| Pour sécuriser les biens                                               | Authentification Multifacteur dans le cloud | L’authentification Multifacteur locales |
|---------------------------------------------------------------|------------------|----------------|
| Applications Microsoft                                                | Oui              | Oui            |
| Applications SaaS dans la galerie d’application                                  | Oui              | Oui            |
| Applications IIS publiées via Azure AD application Proxy         | Oui              | Oui            |
| Applications IIS ne pas publiées via le Proxy d’application Azure AD | aucune               | Oui            |
| Accès à distance en tant que VPN, distance                                     | aucune               | Oui            |

Même si vous pouvez avez réglé une solution pour votre stratégie, vous devez toujours utiliser l’évaluation à partir du haut sur où se trouvent vos utilisateurs.  Cela peut entraîner la solution à modifier.  Utilisez le tableau ci-dessous pour vous aider à déterminer ceci :

| Emplacement de l’utilisateur                                                       | Option de conception par défaut                 |
|---------------------------------------------------------------------|-----------------------------------------|
| Azure Active Directory                                              | Multi-FactorAuthentication dans le cloud |
| Azure AD et annonce à l’aide de la fédération avec AD FS local             | Les deux                                    |
| Azure AD et en local annonce à l’aide d’Azure AD connecter sans synchronisation de mot de passe | Les deux                                    |
| Azure AD et locales avec Azure AD Connect synchronisation de mot de passe  | Les deux                                    |
| Active Directory local                                                      | Serveur de l’authentification multifacteur      |

>[AZURE.NOTE]
Vous devez également vous assurer que l’option de création de l’authentification multifacteur que vous avez sélectionné prend en charge les fonctionnalités qui sont nécessaires pour votre conception.  Pour plus d’informations, lisez [Choisir la solution de sécurité multifacteur pour vous](../multi-factor-authentication-get-started.md#what-am-i-trying-to-secure).

## <a name="multi-factor-auth-provider"></a>Fournisseur d’authentification multifacteur
L’authentification multifacteur est disponible par défaut pour les administrateurs globaux ayant un client Azure Active Directory. Toutefois, si vous souhaitez étendre l’authentification multifacteur à l’ensemble de vos utilisateurs et/ou voulez votre administrateur global pour pouvoir tirer parti fonctionnalités telles que le portail de gestion, le message d’accueil personnalisé et des rapports, vous devez acheter et configurer le fournisseur d’authentification multifacteur.

>[AZURE.NOTE]
Vous devez également vous assurer que l’option de création de l’authentification multifacteur que vous avez sélectionné prend en charge les fonctionnalités qui sont nécessaires pour votre conception. 

##<a name="next-steps"></a>Étapes suivantes
[Déterminer les besoins de protection des données](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Voir aussi
[Vue d’ensemble des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)
