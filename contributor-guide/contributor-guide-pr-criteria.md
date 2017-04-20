# <a name="quality-criteria-for-pull-request-review"></a>Passez en revue les critères de qualité de demande d’extraction

Ces critères sont destinés aux auteurs créer et gérer des articles techniques et les extraire demande réviseurs examiner les demandes de contenu extraire. Si votre demande d’extraction n’indique pas pour [la fusion automatique](contributor-guide-pull-request-etiquette.md#in-a-hurry-submit-prs-that-can-be-accepted-automatically), il sera révisé par un relecteur demande humaine extraire. Extraire demande réviseurs réviser généralement uniquement ce qui est nouveau ou modifié. Extraire demande réviseurs évaluer les modifications dans une demande d’extraction selon les éléments de révision qualité bloquant et non bloquant répertoriés dans cet article.

## <a name="blocking-content-quality-items"></a>Bloquer les éléments de la qualité du contenu

Les mises à jour dans la demande d’extraction doivent respecter les critères suivants à fusionner. Relecteurs de requête d’extraction fournissent des commentaires dans les commentaires de demande d’extraction pour ces éléments et le type de `#hold-off` dans la demande d’extraction pour revenir à vous (l’auteur) avec les commentaires.

| Catégorie | Élément de révision de la qualité |
|----------|---------------------|
|Conditions préalables| La « prêt-à-fusion » et les étiquettes « la validation a réussi » sont affectés à la DA|
|Conditions préalables| La demande d’extraction ne peut pas être bloquée par un conflit de fusion.|
|Intégrité référentielle mis en pension|    Demande d’extraction ne contient aucuns régression contenus évidents.|
|Intégrité référentielle mis en pension|    Demande d’extraction n’inclut pas un mis en pension incorporé ou les fichiers inhabituels, superflus.|
|Intégrité référentielle mis en pension |Demande d’extraction contient moins de 100 fichiers modifiés, à moins que la Valeur_nominale intentionnellement est mise à jour une version branche du masque. (Vraiment, un Valeur_nominale doit contenir moins extrême à celle, mais après 100 fichiers modifiés, GitHub n’affiche pas le diffs).|
|Affectation des noms |Nom de fichier pour les nouveaux fichiers respecte les [règles de dénomination de fichier](file-names-and-locations.md).|
|Affectation des noms |Nouveaux dossiers introduits dans mis en pension suivez les [instructions d’attribution de nom de dossier](file-names-and-locations.md#folder-names-in-the-repo).|
|Contenu    |L’article est un document technique et par conséquent, dans le canal contenu correct. Voir la [ce qui va où conseils](content-channel-guidance.md).|
|Contenu    |Le sujet dans le document technique est approprié pour un article technique. Voir la [ce qui va où conseils](content-channel-guidance.md).|
|Contenu    |Cet article contient un paragraphe d’introduction et un corps procédural ou conceptuel de contenu. L’article doit contenir du contenu suffisant, complète d’éligibilité propre comme un article. Un petit fragment d’informations ne doit pas être. (Exception : une rubrique « Limites » s’il s’agit dans le cadre d’un article de grande taille qui répertorie tous les limites d’un service.)|
|Contenu| Éléments qui doivent être des listes numérotées sont numérotées, éléments qui doivent être non triées des listes à puces. Il s’agit de facilité d’utilisation de base.|
|Contenu| Graphiques inhabituels ou nouveaux, architecture d’informations ou structures ou conceptions visiblement non standard doivent être examinées avec le réviseur Valeur_nominale prospect. Équipes expérimentez avec les nouveaux éléments doivent être une zone de dessin problème/solution ou un plan en place pour l’évaluation des expériences.|
|Conception de site/fonctionnalité| Consoles servent uniquement pour passer entre plusieurs versions du même article.|
|Conception de site/fonctionnalité| Les titres des articles switchered contiennent des informations qui différencient chaque article les autres articles sur l’ensemble de switchered.|
|Conception de site/fonctionnalité| Créés manuellement tables des matières ne sont pas autorisés. L’article doit s’appuient sur H2s pour la table des matières sur la page.|
|Conception de site/fonctionnalité| Si H2 titres sont présents, cet article contient au moins deux en-têtes de H2. À l’aide d’un en-tête H2 crée un seul élément article de la table des matières. Titres H2 doivent être utilisées avant H3 titres pour assurer qu'une table des matières sont créée.|
|Démarque| HTML : Contenu Source ne contient pas de code HTML au niveau des blocs-inline mineure HTML est autorisé – tels qu’exposant, indice, des caractères spéciaux et autres éléments secondaires que vous ne pouvez pas faire avec démarque. Tableaux HTML est autorisées uniquement si la table contient des listes à puces ou numérotées, mais qui indique généralement que le contenu doit être simplifié afin que la source peut être codée dans démarque.|
|Démarque   |Éléments démarque personnalisés sont utilisés le cas échéant. Ex : Notes sont codés à l’aide de la AZURE. Notez le numéro de poste, pas en tant que texte brut.|
|SEO    |Le « & #124 ; Identificateur de site Microsoft Azure » est requis|
|SEO    |Le titre H1 contient des informations suffisantes pour décrire le contenu de l’article, pour le différencier d’autres articles Azure et de mapper des mots clés probable des clients. Par exemple, « Vue d’ensemble » comme titre H1 est fail.|
|Terminologie| L’utilisation de l’acronyme processeur, V1 ou V2 comme une référence à la classique et modèles de déploiement Gestionnaire de ressources est un élément de la terminologie bloquant.|
|Images |GIF animés ne sont pas acceptées dans le mis en pension.|
|Images | Images ont résolution claire, ne contiennent pas de fautes d’orthographe et ne contiennent aucune information privée | 
|Mise en attente|L’aperçu de l’article doit être propre sur mise en attente. Il ne peut pas contenir des problèmes de mise en forme évidents : <br><br>-Une liste à puce ou numérotée qui apparaît sous la forme d’un paragraphe <br> -Code dans un bloc de code qui apparaissent en partie dans le bloc de code et partiellement à l’extérieur <br>-Étapes numérotées numérotées incorrectement en raison de mise en retrait défectueux|

## <a name="non-blocking-content-quality-items"></a>Non bloquant articles de qualité de contenu

Pour ces éléments, les réviseurs extraire demande fournissent des commentaires et des instructions pour l’auteur d’assurer le suivi avec correctifs dans une demande d’extraction ultérieure. Toutefois, ces commentaires n’empêche pas la décision à fusionner. Auteurs doivent respecter sous 3 jours ouvrables avec correctifs.

| Catégorie | Élément de révision de la qualité |
|----------|---------------------|
|Contenu|Articles doivent avoir un « étapes suivantes » à la fin avec les étapes 1 à 3 pertinents et une qualité visuelle époustouflante. Texte court doit être inclus qui permettant aux clients de comprendre pourquoi les étapes suivantes sont pertinentes. (Nouveaux articles uniquement). Exemple : <https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-install/><br>![](media/contributor-guide-pr-criteria/nextstepsexample.PNG)|
|Contenu|Orthographe, grammaire et autres problèmes d’écriture - réviseurs de demande d’extraction peuvent fournir des commentaires sur quelques problèmes secondaires commentaires comme non bloquant. S’il existe plusieurs problèmes éditoriales, réviseurs connecter à une demande de modification de l’article pour une modification postérieures à la composition.|
|Images|Les images utilisent le style légende correct et la couleur et captures d’écran utilisent le style de bordure et d’espaces réservés correct. [Reportez-vous à l’aide de l’image](https://github.com/Azure/azure-content/blob/master/contributor-guide/create-images-markdown.md).|
|Images|Images incluent le texte de remplacement. [Reportez-vous à l’aide de l’image](https://github.com/Azure/azure-content/blob/master/contributor-guide/create-images-markdown.md).|
|Conception de site/fonctionnalité|Les titres H2, lors du rendu dans la table des matières sur la page, doivent être renvoyé préférence sur pas plus de 2 lignes. Titres de plus de temps facilitent l’article de la table des matières plus difficile à analyser.|
|Conventions de style|Tous les titres et les en-têtes sont casse de la phrase, par style Azure.|
|Processus|Si la demande d’extraction peut facilement ont été reconfigurée pour bénéficier d’automatisation PRmerger, réviseurs de demande d’extraction fournissent des commentaires à l’auteur sur l’utilisation des branches afin que les modifications peuvent être fusionnées automatiquement. Consultez [l’article étiquette Valeur_nominale](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-pull-request-etiquette.md#in-a-hurry-submit-prs-that-can-be-accepted-automatically).|
|Processus|Lorsque vous supprimez ou renommez un article, vérifiez que vous suivez la procédure. Extraire demande réviseurs devraient ajouter le commentaire et le lien suivant dans un commentaire :<br><br>*Vérifiez que vous avez suivi le processus de guide des collaborateurs pour la suppression des articles : <https://github.com/Azure/azure-content/blob/master/contributor-guide/retire-or-rename-an-article.md> .*|

## <a name="related"></a>Liés

- [Étiquette de requête d’extraction et des pratiques recommandées pour les collaborateurs de Microsoft](contributor-guide-pull-request-etiquette.md)

- [Extraire demande commentaire automation](contributor-guide-pull-request-comments.md)
