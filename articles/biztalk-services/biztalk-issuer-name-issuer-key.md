<properties 
    pageTitle="Nom de l’émetteur et la clé de l’émetteur dans les Services BizTalk | Microsoft Azure" 
    description="Découvrez comment récupérer l’émetteur nom et la clé de l’émetteur pour Bus des services ou de contrôle d’accès (ACS) dans les Services BizTalk. MABS, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="mandia"/>




# <a name="biztalk-services-issuer-name-and-issuer-key"></a>BizTalk Services : Nom de l’émetteur et la clé de l’émetteur

Les Services BizTalk Azure utilise le nom de l’émetteur de Bus de Service et émetteur clé et le nom de l’émetteur du contrôle Access et la clé de l’émetteur. Plus précisément :

Tâche | Quel nom de l’émetteur et la clé de l’émetteur
--- | ---
Déploiement de votre application à partir de Visual Studio | Nom de l’émetteur du contrôle Access et la clé de l’émetteur
Configuration du portail de Services BizTalk Azure | Nom de l’émetteur du contrôle Access et la clé de l’émetteur
Création de relais métier avec les Services de carte BizTalk dans Visual Studio | Nom de l’émetteur de Bus de service et la clé de l’émetteur

Cette rubrique répertorie les étapes pour extraire le nom de l’émetteur et la clé de l’émetteur. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Nom de l’émetteur du contrôle Access et la clé de l’émetteur
Le nom de l’émetteur du contrôle Access et la clé de l’émetteur sont utilisées par le texte suivant :

- Votre application de BizTalk Service Azure créée dans Visual Studio : pour déployer correctement votre application de BizTalk Service dans Visual Studio pour Azure, vous entrez le nom de l’émetteur du contrôle Access et la clé de l’émetteur. 
- Le portail de Services BizTalk Azure : Lorsque vous créez un BizTalk Service et ouvrez le portail de Services BizTalk, votre nom de l’émetteur du contrôle Access et la clé de l’émetteur sont automatiquement enregistrées pour vos déploiements avec les mêmes valeurs de contrôle d’accès.

### <a name="to-copy-and-paste-the-access-control-issuer-name-and-issuer-key"></a>Pour copier et coller le nom de l’émetteur du contrôle Access et la clé de l’émetteur

1. Connectez-vous au [portail classique Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation gauche, sélectionnez **BizTalk Services**.
3. Sélectionnez votre Service BizTalk. 
4. Sélectionnez les **Informations de connexion** dans la barre des tâches. Le Namespace contrôle Access, émetteur par défaut (nom de l’émetteur) et par défaut touche (émetteur) sont répertoriés et peuvent être copié et collé.  

En résumé  
Nom de l’émetteur = émetteur par défaut  
Clé de l’émetteur = clé par défaut


Vous pouvez également sélectionner **Ouvrir portail de gestion ACS** récupère les valeurs de contrôle d’accès :

1. Connectez-vous au [portail classique Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation gauche, sélectionnez **BizTalk Services**.
3. Sélectionnez votre Service BizTalk.
4. Sélectionnez le bouton informations de connexion et sélectionnez **Ouvrir portail de gestion ACS**.
5. Dans le portail sous **Paramètres du Service**, sélectionnez **Identités de Service**. Cela permet d’afficher votre identité du Service, ce qui correspond à la valeur de votre nom de l’émetteur du contrôle Access. Sélectionnez votre lien de l’identité du Service pour afficher le mot de passe, ce qui correspond à la valeur de votre clé de l’émetteur. Vous pouvez copier leurs valeurs.<br/><br/>
Par exemple, dans les **Identités de Service**, voir « propriétaire ». « Propriétaire » est votre nom de l’émetteur du contrôle Access. Lorsque vous cliquez sur le lien « propriétaire », vous voyez le **mot de passe**. Lorsque vous cliquez sur le lien « Mot de passe », vous voyez la valeur. Cette valeur de mot de passe est votre clé de l’émetteur du contrôle Access.  

En résumé  
Nom de l’émetteur = nom de l’identité du Service  
Clé de l’émetteur = valeur de mot de passe

Dans le volet de navigation de gauche, vous pouvez également sélectionner **Active Directory** pour récupérer les valeurs de contrôle d’accès. 

> [AZURE.IMPORTANT]Lorsqu’un Namespace de contrôle d’accès est créée à l’aide **d’Active Directory**, une identité de Service est **pas** automatiquement créée. Lorsque vous configurez un BizTalk Service, un Namespace de contrôle d’accès, l’identité du Service appelé « propriétaire » (nom de l’émetteur), mot de passe (clé émetteur), et clé symétrique sont automatiquement créés.<br /> 
[Comment : utiliser le Service de gestion des ACS avec les identités de Service configurer](http://go.microsoft.com/fwlink/p/?LinkID=303942) donne des informations supplémentaires sur les identités de Service de contrôle d’accès.


## <a name="service-bus-issuer-name-and-issuer-key"></a>Nom de l’émetteur de Bus de service et la clé de l’émetteur
Nom de l’émetteur de Bus de service et la clé de l’émetteur sont utilisés par les Services de carte BizTalk. Dans votre projet BizTalk Services dans Visual Studio, utilisez les Services de carte BizTalk pour vous connecter à un système de ligne-de-métier en local. Pour vous connecter, vous créez le relais professionnelles et entrez vos informations système métier. Lors de cette opération, vous entrez également le nom de l’émetteur de Bus de Service et la clé de l’émetteur.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Pour extraire le nom de l’émetteur de Bus de Service et la clé de l’émetteur

1. Connectez-vous au [portail classique Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation gauche, sélectionnez **Bus des services**.
3. Sélectionnez votre espace de noms. Dans la barre des tâches, sélectionnez **Informations de connexion**. Cela permet d’afficher l' **Émetteur par défaut** (nom de l’émetteur) et la **Clé par défaut** (émetteur clé). Vous pouvez copier leurs valeurs.  

En résumé  
Nom de l’émetteur = émetteur par défaut  
Clé de l’émetteur = clé par défaut

## <a name="next"></a>Suivant
Rubriques Azure BizTalk Services supplémentaires :

-  [Installer les Services BizTalk Azure SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Didacticiels : Services d’Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Comment puis-je à l’aide de démarrer le SDK Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Services BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>


## <a name="see-also"></a>Voir aussi
-  [Comment : Service de gestion des ACS permet de configurer des identités de Service](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
- [BizTalk Services : Pour les développeurs, basique, Standard et Premium éditions graphique](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Services BizTalk : Portail classique Azure à l’aide de la mise en service](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Services BizTalk : Graphique de l’état de la mise en service](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [BizTalk Services : Onglets de tableau de bord, surveiller et échelle](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Les Services BizTalk : Sauvegarder et restaurer](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Services BizTalk : la limitation](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
 
