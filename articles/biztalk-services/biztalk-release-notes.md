<properties
    pageTitle="Notes de publication pour les Services BizTalk Azure | Services BizTalk Microsoft Azure"
    description="Répertorie les problèmes connus pour les Services BizTalk Azure" 
    services="biztalk-services"
    documentationCenter=""
    authors="msftman"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="deonhe"/>

# <a name="release-notes-for-azure-biztalk-services"></a>Notes de publication pour les Services BizTalk Azure

Les notes de publication pour les Services Microsoft Azure BizTalk contiennent les problèmes connus dans cette version.

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>Quelles sont les nouveautés dans la mise à jour de novembre des Services BizTalk
* Chiffrement inactives peut être activé dans le portail de Services BizTalk. Voir [Activer le chiffrement sur reste dans le portail de Services BizTalk](https://msdn.microsoft.com/library/azure/dn874052.aspx).

## <a name="update-history"></a>Mettre à jour de l’historique

### <a name="october-update"></a>Mise à jour d’octobre

* Comptes professionnels sont prises en charge :  
 * **Scénario**: vous avez enregistré un déploiement de BizTalk Service à l’aide d’un compte Microsoft (comme user@live.com). Dans ce scénario, seuls les utilisateurs Microsoft Account peuvent gérer le BizTalk Service à l’aide du portail de Services BizTalk. Compte d’organisation ne peuvent pas être utilisé.  
 * **Scénario**: vous avez enregistré un déploiement de BizTalk Service à l’aide d’un compte professionnel dans Azure Active Directory (comme user@fabrikam.com ou user@contoso.com). Dans ce scénario, seuls les utilisateurs Azure Active Directory dans la même organisation peuvent gérer le BizTalk Service à l’aide du portail de Services BizTalk. Impossible d’utiliser un compte Microsoft.  
* Lorsque vous créez un BizTalk Service dans le portail Azure classique, vous êtes automatiquement enregistré dans le portail de Services BizTalk.
 * **Scénario**: vous se connecter au portail classique Azure, créez un BizTalk Service, puis sélectionnez **Gérer** pour la première fois. Lorsque le portail Services BizTalk s’ouvre, le BizTalk Service enregistre automatiquement et est prêt pour vos déploiements.  
 Consultez [enregistrement et mettre à jour un déploiement BizTalk Service sur le portail de Services BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx).  

### <a name="august-14-update"></a>Mise à jour août 14
* Contrat et pont découplage – cotation partenaire accords et ponts sont désormais découplées dans le portail de Services BizTalk. Vous désormais créer des accords et ponts séparément, et lors de l’exécution des ponts résoudre à un accord basé sur les valeurs dans le message EDI. Voir [Créer des accords dans les Services BizTalk Azure](https://msdn.microsoft.com/library/azure/hh689908.aspx), [créez un pont EDI à l’aide du portail de Services BizTalk](https://msdn.microsoft.com/library/azure/dn793986.aspx), [créer un pont AS2 à l’aide du portail de Services BizTalk](https://msdn.microsoft.com/library/azure/dn793993.aspx), et [comment ponts résoudre accords en cours d’exécution ?](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* L’option pour créer des modèles pour les contrats est arrêtée.  
* Pour l’accord côté de l’envoi, vous pouvez à présent spécifier des ensembles de délimiteur différente pour chaque schéma. Cette configuration est spécifiée sous paramètres de protocole pour envoyer côté contrat. Pour plus d’informations, voir [créer une X12 contrat dans les Services BizTalk Azure](https://msdn.microsoft.com/library/azure/hh689847.aspx) et [créer un accord EDIFACT dans les Services BizTalk Azure](https://msdn.microsoft.com/library/azure/dn606267.aspx). Deux nouvelles entités sont également ajoutées à l’API OM plateforme sécurisée à cet effet. Voir [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) et [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx).  
* Constructions XSD standards, y compris les Types dérivés, sont désormais pris en charge. Voir [XSD standard utilisation construit dans vos cartes](https://msdn.microsoft.com/library/azure/dn793987.aspx) et [Des Types dérivés utilisables dans mappage des scénarios et des exemples](https://msdn.microsoft.com/library/azure/dn793997.aspx).  
* AS2 prend en charge les nouveaux algorithmes micro pour la signature de message et nouveaux algorithmes de chiffrement. Voir [créer un accord AS2 dans les Services BizTalk Azure](https://msdn.microsoft.com/library/azure/hh689890.aspx).  
## <a name="know-issues"></a>Problèmes connus

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>Problèmes de connectivité après mise à jour du portail de Services BizTalk

  Si vous avez le portail de Services BizTalk ouverte pendant que les Services BizTalk est mis à niveau pour la version précédente de modifications apportées au service, vous pouvez rencontrer des problèmes de connectivité dans le portail de Services BizTalk.  
  Pour résoudre ce problème, vous pouvez redémarrez le navigateur, supprimez le cache du navigateur ou démarrer le portail en mode privé.  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>IDE de Visual Studio ne trouve pas l’objet si vous cliquez sur une erreur ou un avertissement dans un projet BizTalk Services
Installez Visual Studio 2012 mise à jour 3 RC 1 pour résoudre le problème.  

### <a name="custom-binding-project-reference"></a>Référence de projet liaison personnalisée
Prenez en compte les situations suivantes avec un projet BizTalk Services dans une solution Visual Studio :  
* Dans la même solution Visual Studio, il existe un projet BizTalk Services et un projet de liaison personnalisée. Le projet BizTalk Service comporte une référence à ce fichier de projet de liaison personnalisée.
* Le projet BizTalk Service comporte une référence à une mise en registre liaison/comportement personnalisé.

Vous 'Build' la solution dans Visual Studio avec succès. Ensuite, « Reconstruire » ou « Nettoyer » la solution. Après cela, lorsque vous reconstruisez ou nettoyez à nouveau, l’erreur suivante se produit :  
  Impossible de copier le fichier <Path to DLL> à « bin\Debug\FileName.dll ». Le processus ne peut pas accéder au fichier 'bin\Debug\FileName.dll' car il est utilisé par un autre processus.  

#### <a name="workaround"></a>Solution de contournement
* Si [3 de mise à jour Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=39305) est installé, vous disposez des deux options suivantes :

  * Redémarrez Visual Studio, ou

  * Redémarrez la solution. Suivez ensuite les uniquement une génération sur la solution.  

* Si [Visual Studio 2012 mise à jour 3](https://www.microsoft.com/download/details.aspx?id=39305) n’est pas installé, ouvrez le Gestionnaire des tâches, cliquez sur l’onglet processus, cliquez sur le processus MSBuild.exe et puis cliquez sur le bouton Arrêter le processus.  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>Routage aux points de terminaison BasicHttpRelay n'est pas pris en charge de ponts et BizTalk Services portail si les caractères non imprimables promus en tant qu’en-têtes HTTP

Si vous utilisez des caractères non imprimables dans le cadre des propriétés promues pour les messages, ces messages ne peuvent pas être routés vers les destinations relais qui utilisent la liaison BasicHttpRelay. En outre, les propriétés promues qui sont disponibles comme partie de suivi sont codée au format URL pour les objets BLOB et non encodée pour les destinations.  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>MDN est envoyé de façon asynchrone même si l’option MDN asynchrone Send est désactivée  
Prendre en compte ce scénario – si vous activez la case à cocher **Envoyer MDN asynchrone** et spécifiez une URL pour envoyer l’asynchrone MDN et puis désactivez la case à cocher **Envoyer MDN asynchrone** à nouveau, la MDN est toujours envoyé à l’URL spécifiée même si l’option Envoyer MDNs asynchrone n’est pas sélectionnée.  
Pour résoudre ce problème, vous devez effacer l’URL spécifiée avant la désactivation de la case à cocher **Envoyer MDN asynchrone** , puis déployer le contrat AS2.  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>Caractères d’espace blanc au-delà un échange valide provoquent un message vide soient envoyées au point de terminaison de mise en veille  
S’il existe des espaces au-delà un segment IEA, le désassembleur il traite comme final d’échange en cours et examine le jeu suivant d’espaces blancs en tant que message suivant. Étant donné que ce n’est pas valide échange, vous pouvez observer qu’un message réussi est envoyé à la destination de l’itinéraire et un message vide est envoyé le point de terminaison de mise en veille.  
### <a name="tracking-in-biztalk-services-portal"></a>Suivi BizTalk Services portail  
Suivre des événements sont capturés jusqu'à le traitement des messages EDI et toute corrélation. Si un message échoue en dehors de la phase de protocole, suivi apparaît comme étant réussi. Dans cette situation, reportez-vous à la section journal sous la colonne **Détails** dans le **suivi** de détails de l’erreur.
La X12 recevoir et envoyer des paramètres ([créer une X12 contrat dans les Services BizTalk Azure](https://msdn.microsoft.com/library/azure/hh689847.aspx)) fournissent des informations sur la scène protocole.  

### <a name="update-agreement"></a>Accord de mise à jour  
Le portail de Services BizTalk vous permet de modifier l’identificateur d’une identité lorsqu’un accord est configuré. Cela peut entraîner dans les propriétés est incohérent. Par exemple, il existe un contrat à l’aide de ZZ:1234567 et ZZ:7654321 le qualificatif. Dans les paramètres de profil BizTalk Services portail, vous modifiez ZZ:1234567 pour être 01:ChangedValue. Vous ouvrez le contrat et 01:ChangedValue s’affiche au lieu de ZZ:1234567.
Pour modifier l’identificateur d’une identité, supprimez le contrat, mettre à jour des **identités** dans le profil de partenaire, puis recréez le contrat.  
> AZURE. AVERTISSEMENT ce problème affecte X12 et AS2.  

### <a name="as2-attachments"></a>Pièces jointes AS2  
Pièces jointes pour AS2 messages ne sont pas pris en charge dans envoyer ou recevoir. Plus précisément, les pièces jointes sont ignorées silencieusement et le corps du message est traité comme un message AS2 ordinaire.  
### <a name="resources-remembering-path"></a>Ressources : Mémoriser chemin d’accès  
Lorsque vous ajoutez des **ressources**, la boîte de dialogue peut se souvient pas le chemin d’accès utilisée précédemment pour ajouter une ressource. Pour vous souvenir le chemin d’accès précédemment utilisé, essayez d’ajouter le site web BizTalk Services portail aux **Sites de confiance** dans Internet Explorer.  
### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>Si vous renommez le nom de l’entité d’un pont et fermez le projet sans enregistrer les modifications, rouvrir l’entité génère une erreur
Prenons un scénario dans l’ordre suivant :  
* Ajouter un pont (par exemple un pont à sens unique XML) à un projet de BizTalk Service  

* Renommer le pont en spécifiant une valeur pour la propriété nom de l’entité. Cela renomme le fichier .bridgeconfig associé avec le nom que vous avez spécifié.  

* Fermez le fichier .bcs (par fermeture de l’onglet dans Visual Studio) sans enregistrer les modifications.  

* Ouvrez le fichier .bcs à nouveau à partir de l’Explorateur de solutions.  
Vous remarquerez que le fichier .bridgeconfig associé propose le nouveau nom que vous avez spécifié, le nom de l’entité sur l’aire de conception est toujours l’ancien nom. Si vous essayez d’ouvrir la Configuration du pont en double-cliquant sur le composant pont, vous obtenez l’erreur suivante :  
  '<old name>'Entité associé du fichier'<old name>.bridgeconfig' n’existe pas  
Pour éviter de rencontrer ce scénario, vérifiez que vous enregistrez des modifications une fois que vous renommez les entités dans un projet de BizTalk Service.  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>BizTalk Service projet est correctement généré même si un objet a été exclu d’un projet Visual Studio
Prenons un scénario où vous ajoutez un objet (par exemple, un fichier XSD) à un projet de BizTalk Service, inclure cet objet dans la Configuration du pont (par exemple, en spécifiant comme un type de message de demande) et puis exclure le projet Visual Studio. Dans ce cas, la création du projet pas offre une erreur dans la mesure où l’objet supprimé est disponible sur le disque au même emplacement à partir de l’endroit où il a été inclus dans le projet Visual Studio.
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>Le projet BizTalk Service ne vérifie pas la disponibilité du schéma lors de la configuration les ponts
Dans un projet BizTalk Service, si un schéma est ajouté au projet importe un autre schéma, le projet BizTalk Service ne vérifie pas si le schéma importé est ajouté au projet. Si vous essayez de créer un projet de ce type, vous n’obtenez pas les erreurs de génération.
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>Le message de réponse d’un pont de demande-réponse XML est toujours du jeu de caractères UTF-8
Pour cette version, le jeu de caractères du message de réponse à partir d’un pont de demande-réponse XML est toujours définie sur UTF-8.
### <a name="user-defined-datatypes"></a>Types de données définis par l’utilisateur
Les cartes BizTalk carte Pack au sein de la fonctionnalité de Service de la carte BizTalk peuvent utiliser les types de données définis par l’utilisateur pour les opérations de carte.
Lorsque vous utilisez les types de données définis par l’utilisateur, copiez les fichiers (.dll) au lecteur : \Program BizTalk carte Service\BAServiceRuntime\bin\ ou au Assembly Cache (global) sur le serveur qui héberge le service de carte BizTalk. Dans le cas contraire, l’erreur suivante peut se produire sur le client :  
```<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
  <faultcode>s:Client</faultcode>
  <faultstring xml:lang="en-US">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
  <detail>
    <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
    </AFConnectRuntimeFault>
  </detail>
</s:Fault> ```  
> [AZURE.IMPORTANT] Il est recommandé d’utiliser GACUtil.exe pour installer un fichier dans le Global Assembly Cache. GACUtil.exe explique comment utiliser cet outil et les options de ligne de commande Visual Studio.  

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>Redémarrer le Site Web du Service BizTalk carte
L’installation de l' **Exécution du Service de carte BizTalk** * crée la * *Service carte BizTalk* * site web dans IIS qui contient le * *BAService* * application.* *BAService** application utilise en interne la liaison de relais pour étendre la portée de point de terminaison de service sur site dans le cloud. Pour un service hébergé en local, le point de terminaison relais correspondant est inscrits sur le Service Bus uniquement lorsque le service local démarre.  

Si vous arrêtez, démarrez une application, la configuration de démarrage automatique une application n’est pas respectée. Donc lorsque **BAService** est arrêté, vous devez toujours redémarrer le site web **BizTalk carte Service** à la place. Ne pas démarrer ou arrêter l’application **BAService** .
### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>Caractères spéciaux ne doivent pas être utilisées pour les noms d’adresse et entité des composants métier
Vous ne devez pas utiliser des caractères spéciaux pour les noms d’adresse et entité des composants métier. Si vous procédez ainsi, vous obtenez une erreur lors du déploiement du projet de BizTalk Service. Pour certains caractères tels que « % », le site Web de Service de la carte BizTalk peut entrer en état arrêté et vous devrez démarrer manuellement.
### <a name="test-map-with-get-context-property"></a>Tester le mappage avec la propriété de contexte Get
Si une transformation contient une opération de mappage **Obtenir la propriété de contexte** , **Tester le mappage** échoue. Résoudre temporairement le problème, remplacez l’opération Map **Obtenir la propriété de contexte** par une opération chaîne concaténer carte contenant des données factices. Cela vous remplir le schéma cible et que tester les autres fonctionnalités de transformation.
### <a name="test-map-property-does-not-display"></a>Mapper la propriété test ne s’affiche pas
Les propriétés de **Test Map** ne s’affichent pas dans Visual Studio. Cela peut se produire si la fenêtre **Propriétés** et la fenêtre **Explorateur de solutions** ne sont pas simultanément ancrés. Pour résoudre ce problème, ancrer les **Propriétés** et les fenêtres de **L’Explorateur de solutions** .  
### <a name="datetime-reformat-drop-down-is-grayed-out"></a>Le menu déroulant DateTime remettre en forme est grisé
Lorsqu’une opération de mappage DateTime remettre en forme est ajoutée à l’aire de conception et configurée, la liste déroulante Format peut-être être grisée. Cela peut arriver si l’affichage de l’ordinateur est définie **moyen – 125 %** ou **grande – 150 %**. Pour résoudre le problème, définissez l’affichage **proportionnelle – 100 % (par défaut)** à l’aide de la procédure ci-dessous :  
1. Ouvrez le **Panneau de configuration** , puis cliquez sur **apparence et personnalisation**.
2. Cliquez sur **affichage**.
3. Cliquez sur **plus petite-100 % (par défaut)** et cliquez sur **Appliquer**.

La liste déroulante **Format** doit maintenant fonctionner comme prévu.
### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>Accords en double dans le portail de Services BizTalk
Considérez le scénario suivant :
1. Créer un contrat à l’aide de l’API OM de gestion cotation du partenaire.
2. Ouvrez le contrat dans le portail de Services BizTalk dans deux onglets différents.
3. Déployer le contrat à partir des deux onglets.
4. Par conséquent, les deux accords déployées résultant des entrées en double dans le portail de Services BizTalk

**Solution de contournement**. Ouvrez l’une des accords en double dans le portail de Services BizTalk et annuler le déploiement.  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>Ponts n’utilisent pas de certificat mis à jour même après qu’un certificat a été mis à jour dans le magasin d’objet
Examinons les scénarios suivants :  

**Scénario 1 : À l’aide de certificats basés sur l’empreinte numérique pour sécuriser le transfert de messages à partir d’un pont vers un point de terminaison de service**  
Envisagez un scénario où vous utilisez des certificats basés sur l’empreinte numérique dans votre projet BizTalk Service. Vous mettre à jour le certificat dans le portail de Services BizTalk avec le même nom mais une empreinte numérique différente, mais ne mettent pas à jour le projet BizTalk Service en conséquence. Dans ce cas, la passerelle peut continuer à traiter les messages, car les données de certificat anciennes peuvent être toujours dans le cache de canal. Après cela, le traitement des messages échoue.  

**Solution de contournement**: mettre à jour le certificat dans le projet de BizTalk Service et redéployez le projet.  

**Scénario 2 : À l’aide de comportements basés sur un nom pour identifier les certificats pour sécuriser le transfert de messages à partir d’un pont vers un point de terminaison de service**

Envisagez un scénario où vous utilisez comportements basés sur le nom pour identifier les certificats dans votre projet BizTalk Service. Mettre à jour le certificat dans le portail de Services BizTalk, mais ne mettent pas à jour le projet BizTalk Service en conséquence. Dans ce cas, la passerelle peut continuer à traiter les messages, car les données de certificat anciennes peuvent être toujours dans le cache de canal. Après cela, le traitement des messages échoue.  

**Solution de contournement**: mettre à jour le certificat dans le projet de BizTalk Service et redéployez le projet.  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>Ponts de continuent à traiter les messages même lorsque la base de données SQL est en mode hors connexion
Les ponts Services BizTalk continuer à traiter les messages depuis un certain temps, même si la Microsoft Azure SQL base de données (qui stocke les informations en cours d’exécution, telles que les objets déployées et pipelines) est en mode hors connexion. C’est parce que les Services BizTalk utilisent les objets mis en cache et la configuration de pont.
Si vous ne souhaitez pas les ponts pour traiter les messages lorsque la base de données SQL est en mode hors connexion, vous pouvez utiliser les applets de commande PowerShell de Services BizTalk pour arrêter ou interrompre le BizTalk Service. Voir [Azure BizTalk Service Gestion des exemples](http://go.microsoft.com/fwlink/p/?LinkID=329019) pour les applets de commande Windows PowerShell pour gérer les opérations.  
### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>Lire le message XML au sein de composant de code personnalisé d’un pont inclut un caractère nomenclature supplémentaire
Envisagez un scénario où vous souhaitez lire un message XML au sein d’un code personnalisé d’un pont. Si vous utilisez la System.Text.Encoding.UTF8.GetString(bytes) API .NET un caractère nomenclature supplémentaire est inclus dans la sortie au début du message. Par conséquent, si vous ne souhaitez pas la sortie d’inclure le caractère nomenclature supplémentaire, vous devez utiliser ```System.IO.StreamReader().ReadToEnd()```.
### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>Envoi de messages vers un pont à l’aide de WCF n’est pas adaptée
Les messages envoyés à un pont à l’aide de WCF n’est pas adaptée. Vous devez utiliser HttpWebRequest si vous souhaitez qu’un client scalable.
### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>Mise à niveau : Erreur de fournisseur de jeton après la mise à niveau à partir de BizTalk Services Preview à disponibilité générale
Il y a EDI ou AS2 contrat avec lots actives. Lorsque le BizTalk Service est mis à niveau à partir de l’aperçu disponibilité générale, les éléments suivants peuvent survenir :
* Erreur : Le fournisseur de jetons n’a pas pu fournir un jeton de sécurité. Fournisseur de jetons retourné message : nom distant n’a pas pu être résolu.

* Tâches de traitement par lots sont annulées.

**Solution de contournement**: après le BizTalk Service est mis à jour à disponibilité générale, redéployez le contrat.  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>Mise à niveau : Boîte à outils affiche les icônes de pont ancien après la mise à niveau le SDK
Une fois que vous mettez à niveau une version antérieure du SDK, qui avaient anciens icônes représentant les ponts, la boîte à outils continue à afficher les icônes anciens pour les ponts. Toutefois, si vous ajoutez un pont jusqu'à aire de conception de projet BizTalk Service, la surface affiche l’icône Nouveau.  

**Solution de contournement**. Vous pouvez contourner ce problème en supprimant les fichiers .tbd sous <system drive>: \Users\<utilisateur > \AppData\Local\Microsoft\VisualStudio\11.0.  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>Mise à niveau : Portail BizTalk mise à jour de l’aperçu disponibilité générale peut afficher un message d’erreur indiquant que la fonctionnalité EDI n’est pas disponible
Si vous êtes connecté au portail de Services BizTalk tandis que les Services BizTalk est mis à niveau à partir d’aperçu disponible, vous pouvez obtenir le message d’erreur suivant dans le portail :  

Cette fonctionnalité n’est pas disponible dans le cadre de cette version de Microsoft Azure BizTalk Services. Pour utiliser ces fonctionnalités basculez vers une édition appropriée.  

**Résolution**: déconnexion à partir du portail, fermer et ouvrir le navigateur, puis connectez-vous au portail.  
### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>Mise à niveau : Suivi des nouvelles données ne s’affiche pas une fois que les Services BizTalk est mis à niveau vers disponibilité générale
Prenons un scénario où vous disposez d’un pont XML déployé sur abonnement BizTalk Services Preview. Vous envoyez des messages à la passerelle et le suivi des données correspondant est disponible sur le portail de Services BizTalk. À présent, si les bits runtime BizTalk Services portail et les Services BizTalk sont mis à niveau vers disponibilité générale et que vous envoyez un message au même point de terminaison pont déployé précédemment, les données de suivi n’apparaissant pas pour les messages envoyés après mise à niveau.  

### <a name="pipelines-vs-bridges"></a>Pipelines v/s ponts
Dans ce document, le terme « pipelines » et « ponts » servent manière interchangeable. Les deux essentiellement la même signifient, qui est une unité de traitement de message déployée sur BizTalk Services.  

### <a name="concepts"></a>Concepts  

[Services BizTalk](https://msdn.microsoft.com/library/azure/hh689864.aspx)   
