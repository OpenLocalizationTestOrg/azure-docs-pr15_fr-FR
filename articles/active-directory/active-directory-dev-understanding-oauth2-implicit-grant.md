<properties
   pageTitle="Comprendre les oauth2 n’implicite accorder flux dans Azure Active Directory | Microsoft Azure"
   description="En savoir plus sur l’implémentation de l’oauth2 n’implicite de Azure Active Directory accorder flux, et si elle est appropriée pour votre application."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="vibronet"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/17/2016"
   ms.author="vittorib;bryanla"/>

# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Présentation du flux de grant implicite oauth2 ne dans Azure Active Directory (AD)

L’octroi implicite oauth2 n’est connu pour devenir l’octroi avec la liste des problèmes de sécurité dans la spécification oauth2 ne plus longue. Et encore, c’est l’approche implémentée par ADAL JS et celle dans laquelle qu'il est recommandé lorsque vous écrivez SPA applications. Ce que cela signifie ? Il s’agit d’une question de compromis : il s’avère, l’octroi implicite est la meilleure approche, vous pouvez poursuivre pour les applications qui utilisent une API Web via JavaScript à partir d’un navigateur.

## <a name="what-is-the-oauth2-implicit-grant"></a>Quel est l’octroi implicite oauth2 ne ?

Les finances [code d’autorisation oauth2 ne accorder](https://tools.ietf.org/html/rfc6749#section-1.3.1) est l’octroi d’autorisations qui utilise deux points de terminaison séparés. Le point de terminaison d’autorisation est utilisé pour la phase d’interaction utilisateur, qui se traduit par un code d’autorisation. Le point de terminaison jeton est, utilisé par le client pour échanger le code d’un jeton d’accès et souvent un jeton d’actualisation. Applications Web sont requises pour présenter les informations d’identification de leurs propre application au point de terminaison jeton, afin que le serveur d’autorisation peut authentifier le client.

[oauth2 n’implicite accorder](https://tools.ietf.org/html/rfc6749#section-1.3.2) est une variante permettre à un client obtenir un jeton d’accès (et id_token, dans le cas [d’OpenId se connecter](http://openid.net/specs/openid-connect-core-1_0.html)) directement à partir de l’extrémité d’autorisation, sans contacter le point de terminaison jeton ni l’authentification de l’application cliente. Cette variante a été conçue spécialement pour les applications qui s’exécutent dans un navigateur Web à base de JavaScript : dans la spécification oauth2 ne d’origine, jetons sont retournés dans un fragment URI. Qui rend les jetons bits disponibles pour le code JavaScript dans le client, mais elle garantit qu’ils ne seront pas inclus dans des redirections vers le serveur. Renvoi jetons via un navigateur redirige directement à partir du point de terminaison d’autorisation. Il est également l’avantage d’éliminer les conditions requises pour croisée appels d’origine, qui sont nécessaires si l’application JavaScript est requis pour contacter le point de terminaison jeton.

Une caractéristique importante de l’octroi implicite oauth2 n’est le fait que ces flux jetons actualisation jamais retour au client. Comme nous verrons dans la section suivante, qui n’est pas réellement nécessaire et serait en fait un problème de sécurité.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Scénarios appropriées pour l’octroi implicite oauth2 ne

Comme la spécification oauth2 ne déclare, l’octroi implicite a été conçu pour permettre aux applications agent utilisateur – c'est-à-dire, applications JavaScript qui s’exécutent dans un navigateur. La caractéristique définition de ces applications est que le code JavaScript est utilisée pour accéder aux ressources du serveur (généralement une API Web) et de mise à jour de l’application UX en conséquence. Réflexion des applications tel Gmail ou Outlook Web Access : lorsque vous sélectionnez un message dans votre boîte de réception, uniquement le volet de visualisation message change pour afficher la nouvelle sélection, tandis que le reste de la page reste inchangée. Il s’agit par opposition aux traditionnelles applications Web basées sur Rediriger, où chaque interaction de l’utilisateur se traduit par une publication pleine page et un rendu en mode page entière de la réponse du serveur.

Applications qui utilisent l’approche JavaScript, en fonction de son extrême sont appelées Applications à Page unique ou SPAs : l’idée est que ces applications servent uniquement une page HTML initiale et JavaScript associé, avec toutes les interactions résultantes pilotées par les appels d’API Web effectuées via JavaScript. Toutefois, approches hybride, où l’application est principalement pilotés par la publication mais effectue les appels JS minimes, ne sont pas rares – la discussion sur l’utilisation des flux implicite est pertinente pour ceux également.

Applications basées sur les rediriger sécurisent généralement leurs demandes via les cookies, cependant, approche ne fonctionne pas ainsi que pour les applications JavaScript. Les cookies fonctionnent uniquement par rapport au domaine qu'ils ont été créés, tandis que les appels JavaScript peuvent être dirigés vers d’autres domaines. En fait, qui sera fréquemment la casse : réflexion des applications appel de l’API Azure Microsoft Graph API, API Office, – tous les résidant en dehors du domaine à partir de l’endroit où l’application est pris en charge. Tendance pour les applications JavaScript est avoir aucune version serveur du tout, compter 100 % 3e partie API Web pour implémenter leur fonction commerciale.

Pour l’instant, la méthode préférée de protection des appels à une API Web consiste à utiliser l’approche jeton PORTEUR oauth2 ne, où chaque appel est accompagnée d’un jeton d’accès oauth2 ne. L’API Web examine le jeton d’accès entrant et, s’il détecte qu’il contient les étendues nécessaires, elle donne accès à l’opération demandée. Le flux implicite fournit un mécanisme pratique pour les applications de JavaScript obtenir des jetons d’accès pour un API Web, offre de nombreux avantages en ce qui concerne les cookies :

- Jetons peuvent être obtenues fiable sans avoir aux croisée origin appels – l’inscription obligatoire de la redirection URI auquel les jetons sont retour garantit que les jetons ne sont pas déplacées
- Applications JavaScript peuvent obtenir des jetons d’accès autant que nécessaire, pour autant API Web ils ciblent – sans aucune restriction sur les domaines
- Fonctionnalités HTML5 comme la session ou stockage local accorder le contrôle total sur la mise en cache jeton et de gestion de la durée de vie, alors que la gestion des cookies est opaque à l’application
- Les jetons d’accès ne sont pas sensibles aux attaques de falsification (CSRF) demande intersites

Le flux grant implicite n’émet pas jetons d’actualisation, principalement pour des raisons de sécurité. Un jeton d’actualisation n’est pas une portée comme étroitement jetons d’accès, l’attribution de l’alimentation beaucoup plus donc causer des dommages beaucoup plus au cas où il est filtrer hors. Dans le flux implicite, jetons sont remis dans l’URL, donc le risque d’interception est supérieur dans le code d’autorisations d’autorisation.

Toutefois, notez qu’une application JavaScript par un autre mécanisme dont il dispose de renouvellement des jetons d’accès sans inviter à plusieurs reprises l’utilisateur pour les informations d’identification. L’application peut utiliser un iframe masqué pour effectuer des nouvelles demandes de jetons par rapport à l’extrémité de l’autorisation d’Azure AD : dans la mesure où le navigateur comporte toujours une session active (lire : a des cookies session) par rapport au domaine Azure AD, la demande d’authentification peut se produire avec succès sans aucune interaction de l’utilisateur. 

Ce modèle permet l’application de JavaScript pour renouveler indépendamment des jetons d’accès et même acquérir nouveaux pour une nouvelle API (à condition que l’utilisateur accepté précédemment pour eux. Cela permet d’éviter la charge ajoutée d’acquisition, la conservation et protection d’un objet de valeur élevée comme un jeton d’actualisation. L’objet qui permet au renouvellement en mode silencieux, les cookies de session Azure AD, est géré en dehors de l’application. Un autre avantage de cette approche est qu'un utilisateur peut se déconnecter d’Azure Active Directory, à l’aide de toutes les applications connectées à Azure AD, en cours d’exécution dans un des onglets de navigateur. Cela entraîne la suppression de la cookies de session Azure AD et l’application JavaScript perdra automatiquement la possibilité de renouveler jetons pour l’utilisateur connecté arrière.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>L’octroi implicite est adaptée à mon application ?

L’octroi implicite présente des risques plus que d’autres dons. Les domaines, vous devez prêter attention sont présentées bien (voir, par exemple [Une mauvaise utilisation du jeton d’accès au propriétaire de la ressource imiter implicite flux de] [ OAuth2-Spec-Implicit-Misuse] et [modèle de menace OAuth 2.0 et les considérations relatives à la sécurité][OAuth2-Threat-Model-And-Security-Implications]). Toutefois, le profil de risque supérieur est largement dû au fait qu’il est destiné à permettre aux applications d’exécuter du code actif, pris en charge par une ressource à distance sur un navigateur. Si vous envisagez une architecture SPA, n’ont aucun composants principaux ou envisagez d’appeler une API Web via JavaScript, recommandé d’utiliser le flux implicite pour l’acquisition jeton.

Si votre application est un client natif, le flux implicite n’est pas parfait. L’absence des cookies de session Azure AD dans le contexte d’un client natif PRIVE votre application à partir de la permettant de maintenir une session long terme. Ce qui signifie que votre application vous invite à plusieurs reprises l’utilisateur lors de l’obtention des jetons d’accès pour les nouvelles ressources.

Si vous développez une application Web qui inclut un serveur principal et l’utilisation d’une API à partir de son code serveur principal, le flux implicite est également pas une bonne cible. Autres accorde vous offre beaucoup plus power. Par exemple, l’octroi d’informations d’identification de client oauth2 ne fournit la possibilité d’obtenir les jetons reflètent les autorisations attribuées à l’application elle-même, plutôt que d’utiliser délégation utilisateur. Cela signifie que le client a la possibilité de conserver l’accès par programme aux ressources même lorsqu’un utilisateur n’est pas activement dans une session et ainsi de suite. Non seulement cela, mais ces dons garanties de sécurité à une version ultérieure. Par exemple, les jetons d’accès transit jamais à travers le navigateur par les utilisateurs, ils ne risque enregistrées dans l’historique du navigateur et ainsi de suite. L’application cliente pouvez également effectuer une authentification forte lorsque vous demandez un jeton.

## <a name="next-steps"></a>Étapes suivantes

- Pour une liste complète des ressources du développeur, y compris les informations de référence pour les protocoles et oauth2 n’autorisation accorder flux prise en charge par Azure AD, reportez-vous à la [Azure AD du développeur Guide][AAD-Developers-Guide]
- Découvrez [comment intégrer une application avec Azure AD]  [ ACOM-How-To-Integrate] pour la profondeur supplémentaire sur le processus d’intégration application.

<!--Image references-->

<!--Reference style links in use-->
[AAD-Developers-Guide]: active-directory-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16 
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

