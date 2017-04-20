Organisations utilisent plusieurs applications [logicielles en tant que Service (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) pour la productivité, car la technologie cloud et outils deviennent plus accessibles. Lorsque le nombre d’applications SaaS augmente, il devient difficile pour les administrateurs à gérer les comptes et droits d’accès et pour les utilisateurs à mémoriser leur mot de passe différent. Gestion de ces applications individuellement crée un travail supplémentaire et est moins sûre.


- Employés ayant pour effectuer le suivi de nombreux mots de passe ont tendance à utiliser des méthodes moins sécurisé à retenir, l’écriture vers le bas les mots de passe ou à l’aide des mêmes mots de passe entre plusieurs comptes.

- Lors de l’arrivée d’un nouvel employé ou une quitte, tous les comptes de leurs doivent sa mise en service individuellement ou annuler sa mise en service.

- En outre, employés peuvent commencer à l’aide des applications SaaS pour leur travail sans passer par informatique, ce qui signifie qu’ils créent leurs propres comptes sur les systèmes que les administrateurs informatiques n’ont pas approuvée et ne sont pas surveillance.  

Une solution pour tous ces défis est authentification unique (SSO). Il est le moyen le plus simple à gérer plusieurs applications et fournir aux utilisateurs une expérience authentification cohérente. Azure Active Directory (AD Azure) fournit une solution SSO robuste et contient de nombreuses applications déjà intégrées disponibles, avec les didacticiels pour les administrateurs à configurer une nouvelle application rapidement et de commencer la mise en service des utilisateurs.


## <a name="how-does-azure-active-directory-integrate-apps"></a>Comment Azure Active Directory intégrer des applications ?  

Azure AD vous permet d’intégrer vos applications et mis en service des comptes. Pour cela, selon une des deux approches.

- Si l’application est déjà intégrée dans l’application galerie, vous pouvez faire défiler ce portail pour configurer des applications et configurer les paramètres pour autoriser l’authentification unique. Pour n’importe quelle application galerie, vous pouvez commencer par suivez les simples instructions étape par étape présentées dans la galerie d’application et dans le portail Azure pour activer l’authentification unique.

- Si l’application n’est pas dans la galerie, vous pouvez toujours configurer la plupart des applications dans Azure AD comme une application personnalisée. Cette fonctionnalité nécessite un peu plus technique compétences pour configurer. Vous pouvez ajouter n’importe quelle application prenant en charge SAML 2.0 comme une application fédérée, ou une application qui contient une basé sur HTML-page de connexion en tant qu’une application de l’authentification unique de mot de passe.

Dans le cas où les utilisateurs ont créé leurs propres comptes pour les applications SaaS qui ne sont pas gérées par informatique, l’outil de [Découverte d’application Cloud](../articles/active-directory/active-directory-cloudappdiscovery-whatis.md) fournit une solution. Cet outil surveille le trafic web pour identifier quelles applications sont utilisées dans l’ensemble de l’organisation et le nombre de personnes à l’aide de chacun d’eux. INFORMATIQUE peut utiliser ces informations pour savoir quelles applications les utilisateurs préfèrent, puis choisissez les intégrer dans Azure AD pour l’authentification unique.  

Lorsque vous intégrez une application dans Azure AD, vous pouvez mapper des identités d’application établis les utilisateurs à respectives identités Azure AD.  
