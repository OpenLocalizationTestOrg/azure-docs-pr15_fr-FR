<properties
    pageTitle="Utilisation des domaines personnalisés dans le Proxy d’Application Azure AD | Microsoft Azure"
    description="Décrit comment fonctionne les avec des domaines personnalisés dans Azure AD Application Proxy."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>Utilisation des domaines personnalisés dans Azure AD Application Proxy

À l’aide d’un domaine par défaut vous permet de définir la même URL que l’URL interne et externe pour accéder à l’application de sorte que vos utilisateurs aient uniquement une URL à retenir accéder à l’application, quel que soit l’endroit où ils accèdent à partir de. Cela vous permet également de créer un seul raccourci clavier dans le panneau d’accès de l’application. Si vous utilisez le domaine par défaut fourni par le Proxy d’Application Azure AD, il n’existe aucune configuration supplémentaire, que vous devez activer votre domaine. Dans le cas où vous utilisez un domaine personnalisé, il existe plusieurs choses que vous devez faire pour vous assurer que le Proxy d’Application reconnaît votre domaine et valide ses certificats.

## <a name="selecting-your-custom-domain"></a>Sélection de votre domaine personnalisé

1. Publiez votre application conformément aux instructions dans les [applications publier avec Proxy de l’Application](active-directory-application-proxy-publish.md).
2. Une fois que l’application apparaît dans la liste des applications, sélectionnez-le, puis cliquez sur **configurer**.
3. Sous **URL externe**, entrez votre domaine personnalisé.
4. Si votre URL externe est https, vous devrez télécharger un certificat afin que Azure peut valider l’URL de l’application. Vous pouvez également télécharger un certificat générique qui correspond à l’URL externe de l’application. Ce domaine doit être dans la liste de vos [Azure vérifié domaines](https://msdn.microsoft.com/library/azure/jj151788.aspx). Azure doit disposer d’un certificat pour l’URL de domaine de l’application ou un certificat générique qui correspond à l’URL externe pour l’application.
5. Veillez à ajouter un enregistrement DNS qui achemine l’URL interne à l’application qui vous permet d’avoir la même URL pour l’accès interne et externe et un seul raccourci clavier à l’application dans la liste des applications de l’utilisateur.

## <a name="frequently-asked-questions-about-working-with-custom-domains"></a>Forum aux questions sur l’utilisation des domaines personnalisés

Q : puis-je sélectionner un certificat déjà téléchargée sans télécharger à nouveau ?  
A : précédemment téléchargés certificats sont automatiquement liés à l’application et il est exactement un certificat correspondant à nom d’hôte de l’application.  

Q : comment ajouter un certificat et dans quel format le certificat exporté à télécharger ?  
R : le certificat doit être téléchargé à partir de la page de configuration d’application. Le certificat doit être un fichier PFX.  

Q : certificats ECC utilisable ?  
R : il n’existe aucune limitation explicite sur les méthodes de signature.  

Q : certificats SAN utilisable ?  
R : Oui.  

Q : certificats génériques utilisable ?  
R : Oui.  

Q : un autre certificat utilisé sur chaque application ?  
R : Oui, à moins que les deux applications partagent le même hôte externe.  

Q : si j’ai enregistré un nouveau domaine, puis-je utiliser ce domaine ?  
R : Oui, la liste des domaines est chargée à partir de la liste des domaines vérifiés du client.  

Q : que se passe-t-il lorsqu’un certificat a expiré ?  
A : vous recevez un message d’avertissement dans la section certificat dans la page de configuration d’application. Lorsqu’un utilisateur tente d’accéder à l’application, un avertissement de sécurité s’affiche.  

Q : que dois-je faire si vous voulez remplacer un certificat pour une application donnée ?  
R : télécharger un nouveau certificat à partir de la page de configuration d’application.  

Q : puis-je supprimer un certificat et remplacez-le ?  
R : lorsque vous téléchargez un nouveau certificat, si l’ancien certificat n’est pas utilisé par une autre application, il sera automatiquement supprimé.  

Q : que se passe-t-il lorsqu’un certificat est révoqué ?  
A : tests de révocation de certificats ne sont pas exécutées les certificats. Lorsqu’un utilisateur tente d’accéder à l’application, en fonction du navigateur, un avertissement de sécurité peut s’afficher.  

Q : puis-je utiliser un certificat auto-signé ?  
R : Oui, les certificats auto-signé sont autorisées. Notez que si vous utilisez une autorité de certification privée, la protection continue des données (point de distribution de point de révocation de certificats) pour le certificat doivent être publique.  

Q : est-il un emplacement pour afficher tous les certificats pour mon client ?  
R : il n’est pas pris en charge dans la version actuelle.  


## <a name="see-also"></a>Voir aussi

- [Publier des applications avec le Proxy d’Application](active-directory-application-proxy-publish.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
- [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)
- [Ajoutez votre nom de domaine personnalisé à Azure AD](active-directory-add-domain.md)

Pour les dernières nouvelles et mises à jour, consultez le [blog de Proxy de l’Application](http://blogs.technet.com/b/applicationproxyblog/)
