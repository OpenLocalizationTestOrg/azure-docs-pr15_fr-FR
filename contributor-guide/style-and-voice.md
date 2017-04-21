<properties title="" pageTitle="Rédaction de documentation Azure - voix et style de la feuille de fraude" description="Informations de style et vocales pour vous aider à créer du contenu technique pour le centre de documentation Azure." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="required" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="12/16/2014" ms.author="glenga" />

#<a name="writing-azure-documentation---style-and-voice-cheat-sheet"></a>Rédaction de documentation Azure - voix et style de la feuille de fraude

Voici une Fiche récapitulative qui contient des pointeurs sur l’écriture des articles techniques pour les services Azure et de technologies d’assistance. Ces instructions s’appliquent si vous créez de nouveaux éléments de documentation ou mise à jour de la documentation existante.

Au minimum, veuillez :

- Vérification de l’orthographe et la grammaire vérifier vos rubriques, même si vous avez couper et coller dans Word pour le faire.
- Utiliser une voix occasionnel et convivial--comme il était question à une autre personne en tête-à-tête.
- Utilisez des phrases simples. Il s’agit plus faciles à comprendre, et ils sont convertis plus facilement par les traducteurs humaines et postes.

Les sections suivantes contiennent plus d’informations :

+ [Utiliser une voix conviviales]
+ [Envisagez de localisation et traduction automatique]
+ [Autres problèmes de voix et de style à surveiller]


##<a name="use-a-customer-friendly-voice"></a>Utiliser une voix conviviales

Nous aspirons suivre ces principes lors de la rédaction technique pour Azure. Nous ne pouvons pas toujours faire, mais nous devons d’essayer !

- **Utilisez des mots au quotidien**: essayez d’utiliser le langage naturel, les mots vos clients utilisent ; être moins formel, mais pas moins technique ; Donnez des exemples qui expliquent nouveaux concepts.

- **Type**: ne perdez pas de mots et ne plus expliquer. Être positive et n’utilisez pas de mots ou un nombre important de délimiteurs. Conserver les phrases courts et concis. Conserver votre sujet axée. Si une tâche a délimiteurs de placer au début de la phrase ou un paragraphe. En outre, réduisez le nombre de notes au minimum. Utilisez une capture d’écran lorsqu’il peut enregistrer des mots.

- **Facile à analyser**: placer tout d’abord les actions les plus importantes. Utiliser des sections pour segment longues procédures en groupes plus facile à gérer des étapes (procédures avec plus de 12 étapes sont probablement trop de temps). Utilisez une capture d’écran lors de l’ajout de clarté.

- **Compassion**: utilisez un ton appui dans l’article et conserver la limitation de responsabilité au minimum. Bonne foi appelez les zones qui seront est frustrant de devoir clients. Vérifiez que l’article se concentre sur ce qui est important pour le client, ne donnent simplement une conférence technique.

##<a name="consider-localization-and-machine-translation"></a>Envisagez de localisation et traduction automatique
Notre articles techniques sont converties en de nombreuses autres langues et certains sont modifiés pour marchés particuliers. Personnes peuvent également être à l’aide de traduction automatique sur le web pour lire les articles techniques. Écrire, avec les conseils suivants à l’esprit :

- **Vérifiez que l’article ne contient aucune grammaire, l’orthographe ou des erreurs de ponctuation**: il s’agit de ce que nous devons faire en général. Pavé de numérotation démarque 2.0 propose un vérificateur d’orthographe de base, mais vous devez également coller (rendu HTML) contenu à partir de l’article dans Word, c'est-à-dire un vérificateur de grammaire et orthographe plus robuste.

- **Vérifiez votre phrases autant que possible**: phrases composites ou des chaînes de clauses rendent traduction difficile. Diviser les phrases Si vous pouvez le faire sans être trop redondants ni étrange à consonance. Il n’est vraiment articles rédigés dans une langue ne sonne pas naturelle soit.

- **Construction de phrase simple et cohérente d’utilisation**: la cohérence est préférable pour la traduction. Évitez de parentheticals et côté et ont l’objet en tant que vers le début de la phrase que possible. Consultez quelques rubriques publiés - si le sujet a un convivial, facile à lire style, utiliser en tant que modèle.

- **Mise en majuscule et une formulation cohérente utilisation**: là encore, la cohérence est clé. Azure utilise la casse phrase pour les titres, n’est jamais mettre en majuscule un mot si elle n’est pas au début d’une phrase ou d’un substantif NOMPROPRE.

- **Inclure les « mots petites »**: mots que nous considérez petites et a pas d’importance en anglais, car ils sont comprises pour le contexte (tels que « a », « the », « qui » et « est ») sont essentiels pour traduction automatique - veillez à inclure les !

##<a name="other-style-and-voice-issues-to-watch-for"></a>Autres problèmes de voix et de style à surveiller

- Ne divisez étapes commentaire ou côté.

- Pour obtenir la procédure comprenant des extraits de code, placez plus d’informations sur l’étape dans le code sous forme de commentaires. Cela permet de réduire la quantité de texte personnes doivent en revue et les informations de clé sont alors copiées dans le projet de code pour rappeler personnes ce que le code fait lorsqu’ils référer ultérieurement.

- Le nom du produit officiel est « Microsoft Azure », mais nous pouvons presque toujours dire comme « Azure », comme dans « Azure Mobile Services ».

- Ne créez pas acronymes qui commencent par « MA » ou « a ». Utilisez simplement « Azure » en première référence avant un nom de service ou la fonctionnalité et faites-le glisser (par exemple, « Azure Mobile Services » devient « Services mobiles » après la première utilisation). Tenter d’éviter que des acronymes en général - ils confondez simplement personnes.

- Azure utilise la casse phrase pour tous les titres.

- Utilisez « connexion » et pas « reconnectez-vous. »

- Inclure les mots « suivre » ou « comme suit » dans chaque phrase qui précède un extrait de code ou liste.

- « Base de données SQL » est une fonctionnalité Azure. Une « base de données SQL » est une instance de base de données s’exécutant sur base de données SQL.

- Stockage Azure inclut plusieurs « gestion des services de données » qui incluent le service de Table, le service Blob et le service de file d’attente. (Il s’agit pas le « service de stockage de Table Azure ».)




###<a name="contributors-guide-links"></a>Liens de Guide des collaborateurs

- [Article de présentation](./../README.md)
- [Index des articles sur les instructions](./contributor-guide-index.md)



<!--Anchors-->
[Utiliser une voix conviviales]: #use-a-customer-friendly-voice
[Envisagez de localisation et traduction automatique]: #consider-localization-and-machine-translation
[autres problèmes de voix et de style à surveiller]: #other-style-and-voice-issues-to-watch-for
