<properties
    pageTitle="Azure AD Connect : Forum aux questions | Microsoft Azure"
    description="Cette page compte Forum aux questions sur Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-faq"></a>Azure AD Connect Forum aux questions

## <a name="general-installation"></a>Installation générale
**Q : installation fonctionnera que si l’administrateur Global AD Azure a 2FA activé ?**  
Avec les versions de février 2016, cela est pris en charge.

**Q : est-il possible d’installer Azure AD Connect automatisé ?**  
Il est uniquement prise en charge pour installer Azure AD Connect à l’aide de l’Assistant installation. Une installation sans assistance et en mode silencieux n’est pas pris en charge.

**Q : j’ai une forêt où un domaine ne peut pas être contacté. Comment installer Azure AD Connect ?**  
Avec les versions de février 2016, cela est pris en charge.

**Q : l’agent d’intégrité AD DS fonctionne sur server core ?**  
Oui. Après avoir installé l’agent, vous pouvez effectuer le processus d’inscription à l’aide de l’applet de commande PowerShell suivante : 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

## <a name="network"></a>Réseau
**Q : j’ai un pare-feu, un périphérique réseau ou autre chose qui limite les connexions de durée maximale rester ouvert sur mon réseau. Combien de temps mon seuil de délai d’attente côté client convient lorsque vous utilisez Azure AD Connect ?**  
Tous les logiciels de mise en réseau, périphériques physiques ou rien d’autre que les limites de la durée maximale connexions peuvent rester ouvertes doit utiliser un seuil d’au moins 5 minutes (300 secondes) pour la connectivité entre le serveur où le client Azure AD Connect est installé et Azure Active Directory. Cela s’applique également à tous les outils de synchronisation Microsoft Identity publiées précédemment.

**Q : est SLD (domaines en une seule) pris en charge ?**  
Non, Azure AD Connect ne reconnaît pas locaux forêts/domaines à l’aide de SLD.

**Q : sont « en pointillés » NetBios nommés pris en charge ?**  
Non, Azure AD Connect ne reconnaît pas locaux forêts/domaines dont le nom NetBios contient une période «. » dans le nom.

## <a name="federation"></a>Fédération
**Q : que faire si je reçois un message électronique qui me demande de renouveler mon certificat Office 365**  
Utilisez les instructions décrites dans la rubrique [renouveler des certificats](active-directory-aadconnect-o365-certs.md) comment renouveler le certificat.

**Q : j’ai « Mettre à jour automatiquement partie de confiance » définie pour la partie de confiance d’Office 365. Je dois aucune action lorsque mon jeton automatiquement le certificat de signature bascule ?**  
Utilisez les instructions présentées dans l’article [renouveler des certificats](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Environnement
**Q : il pris en charge pour renommer le serveur après l’installation d’Azure AD Connect ?**  
Non. Changer le nom du serveur entraînera le moteur de synchronisation pour ne pas être en mesure de vous connecter à la base de données SQL et le service ne sera pas en mesure de démarrer.

## <a name="identity-data"></a>Données d’identité
**Q : l’attribut UPN (userPrincipalName) dans Azure AD ne correspond pas au nom UPN local - pourquoi ?**  
Voir les articles suivants :

- [Noms d’utilisateurs dans Office 365, Azure ou Intune ne correspondent pas le local UPN ou l’ID de connexion secondaire](https://support.microsoft.com/en-us/kb/2523192)
- [Modifications ne sont pas synchronisées par l’outil Azure Active Directory Sync après avoir modifié le nom UPN d’un compte d’utilisateur pour utiliser un autre domaine fédéré](https://support.microsoft.com/en-us/kb/2669550)

Vous pouvez également configurer Azure AD pour permettre au moteur de synchronisation à jour userPrincipalName comme décrit dans les [fonctionnalités de service de synchronisation Azure AD Connect](active-directory-aadconnectsyncservice-features.md).

## <a name="custom-configuration"></a>Configuration personnalisée
**Q : où figurent les applets de commande PowerShell pour Azure AD Connect ?**  
À l’exception des applets de commande présentées sur ce site, autres applets de commande PowerShell figurant dans Azure AD Connect ne sont pas prises en charge par le client.

* *Q : utilisation de « exportation serveur importer « figurant dans *le Gestionnaire de Service de synchronisation* pour déplacer configuration entre servers? **  
Non. Cette option ne récupère pas tous les paramètres de configuration et ne doit pas être utilisée. Vous devez utiliser à la place de l’Assistant pour créer la configuration de base sur le deuxième serveur et utiliser l’éditeur de règles de synchronisation pour générer des scripts PowerShell pour déplacer une règle personnalisée entre les serveurs. Voir [déplacer configuration personnalisée d’actif à serveur intermédiaire](active-directory-aadconnect-upgrade-previous-version.md#move-custom-configuration-from-active-to-staging-server).

## <a name="troubleshooting"></a>Résolution des problèmes
**Q : Comment puis-je obtenir de l’aide avec Azure AD Connect ?**

[Effectuer une recherche dans la Base de connaissances Microsoft (Ko)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- Effectuer une recherche dans la Base de connaissances Microsoft (KB) pour les solutions aux problèmes courants de réparation sur le Support pour Azure AD Connect techniques.

[Microsoft Azure Active Directory Forums](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

- Vous pouvez rechercher et recherchez technique questions et réponses de la Communauté ou demandez à votre propre question en cliquant sur [ici](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Support de client Azure AD Connect](https://manage.windowsazure.com/?getsupport=true)

- Utilisez ce lien pour obtenir une assistance technique via le portail d’Azure.
