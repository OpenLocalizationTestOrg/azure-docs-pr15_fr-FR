<properties 
    pageTitle="L’authentification Windows et Azure serveur de l’authentification multifacteur"
    description="Il s’agit de la page de l’authentification multifacteur Azure qui aidera déploiement de l’authentification Windows et serveur de l’authentification multifacteur Azure."
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

# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>L’authentification Windows et Azure serveur de l’authentification multifacteur

La section authentification Windows permet à l’administrateur activer et configurer l’authentification Windows pour une ou plusieurs applications.  Voici la liste des éléments à prendre en compte avant de configurer l’authentification Windows.

-  redémarrage est nécessaire avant que l’authentification multifacteur Azure pour les Services Terminal Server ne soit en vigueur.
-  Si 'Match d’utilisateur nécessite l’authentification multifacteur Azure' est activée et que vous n’êtes pas dans la liste des utilisateurs, vous ne pourrez pas à vous connecter à l’ordinateur après le redémarrage.
-  Adresses IP autorisées dépend de si l’application peut fournir l’IP du client avec l’authentification. Actuellement, seuls les Services Terminal Server est pris en charge.  







>[AZURE.NOTE]Cette fonctionnalité n’est pas prise en charge pour les Services Terminal Server sécurisés sur Windows Server 2012 R2.




## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Pour sécuriser une application avec l’authentification Windows, procédez comme suit.

1. Dans le serveur de l’authentification multifacteur Azure cliquez sur l’icône de l’authentification Windows.
![Authentification Windows](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png)
2. Cochez la case à cocher de l’authentification Windows activer. Par défaut, cette case est désactivée.
3. L’onglet Applications permet à l’administrateur configurer un ou plusieurs des applications pour l’authentification Windows.
4. Sélectionnez un serveur ou une application : permet de spécifier si l’application/serveur est activée. Cliquez sur OK.
5. Cliquez sur Ajouter... bouton.
6. L’onglet adresses IP approuvés vous permet d’ignorer les sessions Azure l’authentification multifacteur pour Windows provenant d’adresses IP spécifiques. Par exemple, si les employés utilisent l’application à partir du bureau et d’accueil, vous pouvez décider de que vous ne voulez pas leur téléphone sonne pour l’authentification multifacteur Azure au bureau. Pour ce faire, vous devez spécifier le sous-réseau office comme entrée d’adresses IP approuvés.
7. Cliquez sur Ajouter... bouton.
8. Sélectionnez l’adresse IP unique si vous souhaitez ignorer une seule adresse IP.
9. Sélectionnez la plage d’adresses IP si vous souhaitez ignorer une plage IP entière. Exemple 10.63.193.1-10.63.193.100.
10. Sélectionnez sous-réseau si vous souhaitez spécifier une plage d’adresses IP à l’aide de la notation sous-réseau. Entrez l’adresse IP de début du sous-réseau et choisissez le masque de réseau approprié dans la liste déroulante.
11. Cliquez sur le bouton OK.
