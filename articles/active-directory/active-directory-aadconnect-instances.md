<properties
    pageTitle="Azure AD Connect : Synchroniser des instances de service | Microsoft Azure"
    description="Cette page documents considérations particulières instances Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect : Considérations particulières pour instances
Azure AD Connect est plus fréquemment utilisé avec l’instance dans le monde d’Azure AD et Office 365. Mais il existe également d’autres instances et ces ont des exigences différentes pour les URL et d’autres considérations particulières.

## <a name="microsoft-cloud-germany"></a>Allemagne Microsoft Cloud
[Microsoft Cloud Allemagne](http://www.microsoft.de/cloud-deutschland) est un nuage souverain géré par un tiers de confiance données allemand.

Ce nuage est en cours d’aperçu. La plupart des scénarios que normalement possibles par vous-même, telles que vérifier les domaines, doit être effectuée par l’opérateur. Veuillez contacter votre représentant Microsoft local pour plus d’informations sur la participation dans l’aperçu.

URL à ouvrir dans serveur proxy |
--- |
\*. microsoftonline.de |
\*. Windows.NET |
+ Listes de révocation de certificats |

Lorsque vous êtes connecté à votre répertoire Azure AD vous devez utiliser un compte dans le domaine onmicrosoft.de.

Fonctionnalités qui ne sont pas présentes dans l’Allemagne Microsoft Cloud :

- Azure AD connecter santé n’est pas disponible.
- Mises à jour automatiques n’est pas disponible.
- Mot de passe en écriture différée n’est pas disponible.

## <a name="microsoft-azure-government-cloud"></a>Nuage de Microsoft Azure pour le gouvernement
Le [cloud Microsoft Azure pour le gouvernement](https://azure.microsoft.com/features/gov/) est un nuage pour le gouvernement des États-Unis.

Cette cloud a été pris en charge par les versions antérieures de synchronisation d’annuaire. À partir de la version 1.1.180 d’Azure AD Connect la prochaine génération du cloud est pris en charge. Cette génération à l’aide de points de terminaison en fonction des États-Unis uniquement et disposez d’une autre liste d’URL pour l’ouvrir dans votre serveur proxy.

URL à ouvrir dans serveur proxy |
--- |
\*. microsoftonline.com |
\*. gov.us.microsoftonline.com |
+ Listes de révocation de certificats |

Azure AD Connect ne seront pas en mesure de détecter automatiquement que votre répertoire Azure AD se trouve dans le cloud pour le gouvernement. Au lieu de cela, vous devez effectuer les opérations suivantes lorsque vous installez Azure AD Connect.

1. Redémarrez l’installation Azure AD Connect.
2. Dès que vous voyez la première page où vous êtes supposé accepter le CLUF, ne pas continuer tout en conservant l’Assistant installation en cours d’exécution.
3. Démarrez regedit, puis modifier la clé de Registre `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` à la valeur `2`.
4. Accédez à l’Assistant installation Azure AD Connect, acceptez le CLUF et continuer. Pendant l’installation, veillez à utiliser le chemin d’installation **configuration personnalisée** (et non Express installation). Poursuivez ensuite l’installation comme d’habitude.

Fonctionnalités qui ne sont pas présentes dans le cloud Microsoft Azure gouvernement :

- Azure AD connecter santé n’est pas disponible.
- Mises à jour automatiques n’est pas disponible.
- Mot de passe en écriture différée n’est pas disponible.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
