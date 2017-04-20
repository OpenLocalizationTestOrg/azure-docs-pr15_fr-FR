<properties
    pageTitle="Ajouter la Translator Microsoft dans les applications logique | Microsoft Azure"
    description="Vue d’ensemble du connecteur Microsoft Translator avec des paramètres de l’API REST"
    services=""
    suite=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# <a name="get-started-with-the-microsoft-translator-connector"></a>Prise en main du connecteur Microsoft Translator
Se connecter à Microsoft Translator pour traduire du texte, détecte une langue et bien plus encore. Avec Microsoft Translator, vous pouvez : 

- Créer votre flux d’activité basé sur les données que vous obtenez à partir de Microsoft Translator. 
- Utiliser les actions pour traduire du texte, détecte une langue et bien plus encore. Ces actions Obtient une réponse et apportez la sortie disponibles pour d’autres actions. Par exemple, lorsqu’un fichier est créé dans Dropbox, vous pouvez convertir le texte dans le fichier dans une autre langue à l’aide de Microsoft Translator.

Pour ajouter une opération dans les applications logique, voir [créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Microsoft Translator inclut les actions suivantes. Il n’existe aucun déclencheur.

Déclencheurs | Actions
--- | ---
Aucun | <ul><li>Détecter la langue</li><li>Conversion de texte par synthèse vocale</li><li>Traduire le texte</li><li>Obtenir des langues</li><li>Obtenir des langues de reconnaissance vocale</li></ul>

Tous les connecteurs prennent en charge les données aux formats JSON et XML.


## <a name="create-a-connection-to-microsoft-translator"></a>Créer une connexion à Microsoft Translator

>[AZURE.INCLUDE [Steps to create a connection to Microsoft Translator](../../includes/connectors-create-api-microsofttranslator.md)]


## <a name="swagger-rest-api-reference"></a>Référence des API REST swagger
S’applique aux versions : 1.0.

### <a name="detect-language"></a>Détecter la langue    
Détecte la langue source du texte.  
```GET: /Detect```

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|requête|chaîne|Oui|requête|Aucun |Texte dont la langue est identifiée|

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


### <a name="text-to-speech"></a>Conversion de texte par synthèse vocale    
Convertit un texte donné sous forme de flux audio au format vague vocale.  
```GET: /Speak```

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|requête|chaîne|Oui|requête|Aucun |Texte à convertir|
|langue|chaîne|Oui|requête|Aucun |Code de langue pour générer vocale (exemple : « en-us)|

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


### <a name="translate-text"></a>Traduire le texte    
Convertit du texte à une langue spécifiée à l’aide de Microsoft Translator.  
```GET: /Translate```

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|requête|chaîne|Oui|requête|Aucun |Texte à traduire|
|languageTo|chaîne|Oui|requête| Aucun|Code langue cible (exemple : 'fr')|
|languageFrom|chaîne|aucune|requête|Aucun |Source de langage ; Si ne pas fourni, Microsoft Translator essaie de détecter automatiquement. (exemple : US)|
|catégorie|chaîne|aucune|requête|général |Catégorie de traduction (par défaut : « général »)|

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


### <a name="get-languages"></a>Obtenir des langues    
Récupère toutes les langues qui prend en charge Microsoft Translator.  
```GET: /TranslatableLanguages```

Il n’existe pas de paramètres pour cet appel. 

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


### <a name="get-speech-languages"></a>Obtenir des langues de reconnaissance vocale    
Extrait les langues disponibles pour la synthèse vocale.  
```GET: /SpeakLanguages``` 

Il n’existe pas de paramètres pour cet appel.

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|

## <a name="object-definitions"></a>Définitions d’objets

#### <a name="language-language-model-for-microsoft-translator-translatable-languages"></a>Langue : modèle de langage pour les langues traduire Microsoft Translator

|Nom de la propriété | Type de données | Obligatoire|
|---|---|---|
|Code|chaîne|aucune|
|Nom|chaîne|aucune|


## <a name="next-steps"></a>Étapes suivantes

[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

Revenez à la [liste des API](apis-list.md).


<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/connectors-create-api-microsofttranslator/register-your-application.png
