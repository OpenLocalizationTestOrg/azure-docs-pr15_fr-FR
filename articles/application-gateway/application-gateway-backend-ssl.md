<properties
   pageTitle="Activation de la stratégie SSL et SSL de bout en bout sur Application passerelle | Microsoft Azure"
   description="Cette page fournit une vue d’ensemble de la passerelle Application SSL de bout en bout prend en charge."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="amsriva"/>

# <a name="enabling-ssl-policy-and-end-to-end-ssl-on-application-gateway"></a>Activation de la stratégie SSL et SSL de bout en bout sur Application passerelle

## <a name="overview"></a>Vue d’ensemble

Passerelle d’application prend en charge les frais de résiliation SSL au niveau de la passerelle, une fois que le trafic qui passe en général clair vers les serveurs principaux. Cela permet de serveurs web pour être unburdened à partir de chiffrement/déchiffrement coûteuses. Cependant pour certains clients communication non chiffrée aux serveurs principaux n’est pas une option acceptable. Ceci peut être dû à des exigences de sécurité et de conformité ou l’application peut accepter uniquement une connexion sécurisée. Pour des applications, passerelle d’application prend désormais en charge le chiffrement SSL de bout en bout.

Fin à fin SSL vous permet de transmettre en toute sécurité des données sensibles à la version serveur chiffré toujours profiter des avantages des fonctionnalités d’équilibrage de charge couche 7 quelle passerelle application offre, par exemple affinité des cookies, prise en charge du routage, basée sur les URL pour le routage basés sur des sites ou possibilité d’injection de X-transférés-* en-têtes.

Lorsqu’il est configuré avec le mode de communication SSL de bout en bout, application passerelle interrompt utilisateur SSL sessions au niveau de la passerelle et déchiffre le trafic des utilisateurs. Il applique ensuite les règles configurées pour sélectionner une instance de pool principaux appropriés pour acheminer le trafic vers. Passerelle d’application démarre une nouvelle connexion SSL au serveur principal, puis nouveau chiffre les données à l’aide du certificat de clé publique du serveur principal avant de transmettre la demande sur le serveur principal. SSL de bout en bout est activée en définissant le protocole de BackendHTTPSetting en Https, qui est ensuite appliqué à un pool de serveur principal. Chaque serveur principal du pool principal avec SSL de bout en bout activé doit être configuré avec un certificat pour sécuriser les communications.

![scénario de bout en bout ssl][1]

Dans cet exemple, les requêtes qui utilisent TLS1.2 sont routés vers les serveurs principaux dans Pool1 à l’aide de bout en bout SSL.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Bout en bout SSL et création de listes autorisées de certificats

Passerelle d’application communique uniquement avec les instances de serveur principal connus qui ont autorisation leur certificat avec la passerelle d’application. Pour activer la création de listes autorisées de certificats, vous devez télécharger la clé publique de certificats de serveur principal à la passerelle d’application (et non le certificat racine). Seules les connexions pour les serveurs principaux connues et liste d’autorisation sont autorisées puis. Le restant les serveurs principaux génère une erreur de passerelle. Les certificats auto-signé sont fins de test uniquement et non recommandé pour les charges de travail. Ces certificats doivent également être autorisés avec la passerelle d’application comme décrit dans les étapes précédentes avant de pouvoir être utilisés.

## <a name="application-gateway-ssl-policy"></a>Application passerelle SSL stratégie

Passerelle d’application prend en charge utilisateur configurable SSL négociation stratégies, qui permettent aux clients davantage de contrôle sur des connexions SSL auprès de la passerelle d’application.

1. SSL 2.0 et 3.0 désactivée par défaut pour toutes les passerelles d’Application. Ils ne sont pas configurables du tout.
2. Permet de définition de stratégie SSL vous option pour désactiver les protocoles suivants 3 - TLSv1\_0, TLSv1\_1, TLSv1\_2.
3. Si aucune stratégie SSL est définie trois (TLSv1\_0, TLSv1\_1, TLSv1_2) sont activées.

## <a name="next-steps"></a>Étapes suivantes

Après la découverte des fin à fin SSL et stratégie SSL, accédez à [Activer le protocole SSL de bout en bout sur passerelle d’application](application-gateway-end-to-end-ssl-powershell.md) créer une passerelle d’application possibilité pour acheminer le trafic vers les serveurs principaux dans un format chiffré.

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png