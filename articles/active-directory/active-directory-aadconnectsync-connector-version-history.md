<properties
   pageTitle="Connecteur historique de version | Microsoft Azure"
   description="Cette rubrique répertorie toutes les versions des connecteurs pour Forefront Identity Manager (FIM) et le Gestionnaire d’identité Microsoft (MIM)"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/17/2016"
   ms.author="billmath"/>

# <a name="connector-version-release-history"></a>Connecteur historique de version
Les connecteurs Forefront Identity Manager (FIM) et le Gestionnaire d’identité Microsoft (MIM) sont mis à jour fréquemment.

>[AZURE.NOTE]
Cette rubrique concerne uniquement sur FIM et MIM. Ces liens ne sont pas prises en charge sur Azure AD Connect.

Cette rubrique répertorier toutes les versions des connecteurs qui ont été publiées.

Liens connexes :

- [Téléchargez la dernières connecteurs](http://go.microsoft.com/fwlink/?LinkId=717495)
- Documentation de référence de [Connecteur LDAP générique](active-directory-aadconnectsync-connector-genericldap.md)
- Documentation de référence de [Connecteur SQL générique](active-directory-aadconnectsync-connector-genericsql.md)
- Documentation de référence de [Connecteur de Services Web](http://go.microsoft.com/fwlink/?LinkID=226245)
- Documentation de référence de [Connecteur PowerShell](active-directory-aadconnectsync-connector-powershell.md)
- Documentation de référence [Lotus Domino connecteur](active-directory-aadconnectsync-connector-domino.md)

## <a name="111170"></a>1.1.117.0
De publication : 2016 mars

**Nouveau connecteur**  
Version initiale du [Connecteur SQL générique](active-directory-aadconnectsync-connector-genericsql.md).

**Nouvelles fonctionnalités :**

- Connecteur LDAP générique :
    - Prise en charge pour l’importation delta avec Isode.
- Connecteur de Services Web :
    - Mise à jour de l’activité csEntryChangeResult et setImportErrorCode pour autoriser les erreurs de niveau objet à retourner en le moteur de synchronisation.
    - Mises à jour les modèles SAP6 et SAP6User pour utiliser les nouvelles fonctionnalités d’erreur de niveau objet.
- Connecteur Domino Lotus :
    - Pour l’exportation, vous avez besoin d’une autorité par carnet d’adresses. Vous pouvez désormais utiliser le même mot de passe pour tous les certificateurs pour faciliter la gestion.

**Problèmes résolus :**

- Connecteur LDAP générique :
    - Pour IBM Tivoli DS, certains attributs de référence n’a ne étaient pas détectées correctement.
    - Pour ouvrir LDAP au cours d’une importation delta, les espaces situés au début et à la fin de chaînes ont été tronqués.
    - Pour Novell et NetIQ, une exportation que déplacé un objet entre des unités d’organisation/conteneurs et en même temps renommé l’objet a échoué.
- Connecteur de Services Web :
    - Si le service web a eu plusieurs points de terminaison pour la même liaison, puis le connecteur n’a pas correctement trouvé ces points de terminaison.
- Connecteur Domino Lotus :
    - Exportation de l’attribut de nom complet à une base de données dans courrier n’a pas fonctionné.
    - Une exportation qui ajouté et supprimé des membres d’un groupe exportés uniquement les membres ajoutées.
    - Si un Document Notes est non valide (l’attribut IsValid a la valeur false), puis la ne fonctionne pas de connecteur.

## <a name="older-releases"></a>Versions plus anciennes
Avant de mars 2016, les connecteurs ont été publiés dans les rubriques d’aide.

**LDAP générique**

- [KB3078617](https://support.microsoft.com/kb/3078617) - 1.0.0597, septembre 2015
- [KB3044896](https://support.microsoft.com/kb/3044896) - 1.0.0549, mars 2015
- [KB3031009](https://support.microsoft.com/kb/3031009) - 1.0.0534, janvier 2015
- [KB3008177](https://support.microsoft.com/kb/3008177) - 1.0.0419, septembre 2014
- [KB2936070](https://support.microsoft.com/kb/2936070) - 4.3.1082, mars 2014

**Services Web**

- [KB3008178](https://support.microsoft.com/kb/3008178) - 1.0.0419, septembre 2014

**PowerShell**

- [KB3008179](https://support.microsoft.com/kb/3008179) - 1.0.0419, septembre 2014

**Lotus Domino**

- [KB3096533](https://support.microsoft.com/kb/3096533) - 1.0.0597, septembre 2015
- [KB3044895](https://support.microsoft.com/kb/3044895) - 1.0.0549, mars 2015
- [KB2977286](https://support.microsoft.com/kb/2977286) - 5.3.0712, août 2014
- [KB2932635](https://support.microsoft.com/kb/2932635) - 5.3.1003, février 2014  
- [KB2899874](https://support.microsoft.com/kb/2899874) - 5.3.0721, octobre 2013
- [KB2875551](https://support.microsoft.com/kb/2875551) - 5.3.0534, août 2013

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la configuration [Azure AD Connect synchroniser](active-directory-aadconnectsync-whatis.md) .

En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
