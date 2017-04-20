<properties 
    pageTitle="Guide du langage de spécification de réseaux neuronaux Net # | Microsoft Azure" 
    description="Syntaxe de la neural Net # réseaux langue spécification, ainsi que des exemples de la façon de créer un modèle personnalisé réseau neuronal dans Microsoft Azure ML à l’aide de Net#" 
    services="machine-learning" 
    documentationCenter="" 
    authors="jeannt" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="jeannt"/>



# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning"></a>Guide de langage de spécification de réseau neuronal Net # d’apprentissage automatique Azure

## <a name="overview"></a>Vue d’ensemble
NET # est un langage développé par Microsoft, qui permet de définir des architectures de réseau neuronal pour les modules de réseau neuronal dans l’apprentissage de Microsoft Azure automatique. Dans cet article, vous allez apprendre :  

-   Concepts de base liés à réseaux neuronaux
-   Configuration requise de réseau neuronal et comment définir les principaux composants
-   La syntaxe et les mots clés du langage de spécification Net #
-   Exemples de réseaux neuronaux personnalisés créés à l’aide de Net# 
    
[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  

## <a name="neural-network-basics"></a>Concepts de base de réseau neuronal
Une structure de réseau neuronal se compose des ***nœuds*** qui sont organisées en ***couches***et pondérée ***connexions*** (ou ***bords***) entre les nœuds. Les connexions sont directionnelles, et chaque connexion comporte un nœud ***source*** et un nœud de ***destination*** .  

Chaque ***calque capable d’apprentissage*** (un masquées ou un calque sortie) contient une ou plusieurs ***offres de connexion***. Un ensemble de guides de connexion se compose d’un calque source et spécification des connexions à partir de ce calque source. Toutes les connexions dans un faisceau donné partagent le même ***calque source*** et la même ***calque de destination***. En Net #, un ensemble de guides de connexion est considéré comme appartenant au calque de destination de l’offre.  
 
NET # prend en charge différents types de connexion offres, qui vous permet de personnaliser les entrées de façon sont mappés aux calques masqués et mappés aux sorties.   

La valeur par défaut ou la botte standard est une **offre complète**, dans lequel chaque nœud dans le calque source est connecté à tous les nœuds du calque de destination.  

En outre, Net # prend en charge les quatre types d’offres de connexion avancés suivants :  

-   **Offres filtrée**. L’utilisateur peut définir un prédicat en utilisant les emplacements du nœud calque source et le nœud de calque de destination. Les nœuds sont connectés à chaque fois que le prédicat a la valeur vrai.
-   **Offres à convolution**. Vous pouvez définir les petits cercles des nœuds dans le calque source. Chaque nœud du calque de destination est connecté à un Club des nœuds dans le calque source.
-   **Regroupement offres** et les **offres de normalisation réponse**. Ils sont semblables aux offres à convolution dans la mesure où l’utilisateur définit les petits cercles des nœuds dans le calque source. La différence est que les pondérations des bords dans ces offres ne sont pas capable d’apprentissage. En revanche, une fonction prédéfinie est appliquée aux valeurs nœud source pour déterminer la valeur de nœud de destination.  

À l’aide Net # pour définir la structure d’un réseau neuronal rend possible de définir des structures complexes tels que les réseaux neuronaux profondeur ou les structures de dimensions arbitraires, qui sont connues pour améliorer la formation sur les données comme image, audio ou vidéo.  

## <a name="supported-customizations"></a>Personnalisations prises en charge
L’architecture des modèles réseau neuronal que vous créez dans l’apprentissage automatique Azure peut être largement personnalisé à l’aide de Net #. Vous pouvez :  

-   Créer des calques masqués et contrôler le nombre de nœuds dans chaque calque.
-   Spécifiez comment les couches doivent être connectés entre eux.
-   Définir des structures de connectivité spéciaux, tels que des structures et épaisseur partage offres.
-   Spécifier les différentes fonctions d’activation.  

Pour plus d’informations de la syntaxe de la spécification de la langue, voir [Spécification de Structure](#Structure-specifications).  
 
Pour obtenir des exemples de définition de réseaux neuronaux pour certains machine courantes d’apprentissage tâches, à partir PL aux plus complexes, voir des [exemples](#Examples-of-Net#-usage).  

## <a name="general-requirements"></a>Configuration requise
-   Il faut sortie exactement un calque, au moins un calque d’entrée et zéro, un ou plusieurs calques masqués. 
-   Chaque calque possède un nombre constant de nœuds, conceptuel organisés dans un tableau rectangulaire de dimensions arbitraires. 
-   Couches d’entrée n’ont pas paramètres formés associés et représentent des données de l’instance après l’entrée du réseau. 
-   Capable d’apprentissage couches (couches masquées et de sortie) sont associés à des paramètres formés, appelés poids et des écarts. 
-   Les nœuds source et de destination doivent être placé dans des couches distinctes. 
-   Connexions doivent être acycliques ; en d’autres termes, il ne peut pas être une chaîne de connexions et causer le nœud source initial.
-   La couche de sortie ne peut pas être un calque source d’un lot de connexion.  

## <a name="structure-specifications"></a>Spécifications de structure
Spécification structure réseau neuronal est composée de trois sections : la **déclaration de constante**, la **déclaration de calque**, la **déclaration de connexion**. Il existe également une section facultative **partager déclaration** . Les sections peuvent être indiquées dans n’importe quel ordre.  

## <a name="constant-declaration"></a>Déclaration de constante 
Une déclaration de constante est facultative. Elle permet de définir les valeurs utilisées ailleurs dans la définition du réseau neuronal. L’instruction de déclaration est constitué d’un identificateur suivi d’un signe égal et d’une expression de valeur.   

Par exemple, l’instruction suivante définit une constante **x**:  


    Const X = 28;  

Pour définir des constantes deux ou plusieurs simultanément, encadrer les indiquer les noms et les valeurs entre accolades, en les séparant par des points-virgules. Par exemple :  

    Const { X = 28; Y = 4; }  

La partie droite de chaque expression d’affectation peut être un nombre entier, un nombre réel, une valeur booléenne (vrai ou faux) ou une expression mathématique. Par exemple :  

    Const { X = 17 * 2; Y = true; }  

## <a name="layer-declaration"></a>Déclaration de calque
La déclaration de calque est requise. Il définit la taille et la source du calque, y compris ses attributs et les offres de connexion. L’instruction de déclaration commence par le nom du calque (d’entrée, masqué ou de sortie), suivi par les dimensions du calque (un uplet d’entiers positifs). Par exemple :  

    input Data auto;
    hidden Hidden[5,20] from Data all;
    output Result[2] from Hidden all;  

-   Le produit des dimensions est le nombre de nœuds dans le calque. Dans cet exemple, il existe deux dimensions [5,20], ce qui signifie qu’il n’y 100 nœuds dans le calque.
-   Les couches peuvent être déclarés dans n’importe quel ordre, avec une exception : Si plusieurs calques d’entrée est définie, l’ordre dans lequel elles sont déclarées doit correspondre à l’ordre des fonctions dans les données d’entrée.  


Pour spécifier que le nombre de nœuds dans un calque détermine automatiquement, utilisez le mot-clé **automatique** . Le mot clé **auto** comporte des effets différents, selon la couche :  

-   Dans une déclaration calque d’entrée, le nombre de nœuds est le nombre de fonctionnalités dans les données d’entrée.
-   Dans une déclaration calque masqué, le nombre de nœuds est le nombre spécifié par la valeur du paramètre nombre **de nœuds masquées**. 
-   Dans une déclaration de calque sortie, le nombre de nœuds est 2 pour deux cours classification, 1 pour régression et égal au nombre de nœuds de sortie pour le classement multiclass.   

Par exemple, la définition de réseau suivante permet la taille de tous les calques à déterminer automatiquement :  

    input Data auto;
    hidden Hidden auto from Data all;
    output Result auto from Hidden all;  


Une déclaration calque pour un calque capable d’apprentissage (les couches masquées ou de sortie) peut éventuellement inclure la sortie fonction (également appelée une fonction d’activation), qui utilise par défaut **sigmoïde** pour les modèles de classement et **linéaire** pour les modèles de régression. (Même si vous utilisez la valeur par défaut, vous pouvez explicitement indiquer la fonction de l’activation, si vous le souhaitez pour plus de clarté.)

Les fonctions de sortie suivantes sont prises en charge :  

-   sigmoïde
-   linéaire
-   Softmax
-   rlinear
-   carré
-   racine
-   srlinear
-   ABS
-   TANH 
-   brlinear  

Par exemple, la déclaration suivante utilise la fonction **softmax** :  

    output Result [100] softmax from Hidden all;  

## <a name="connection-declaration"></a>Déclaration de connexion
Immédiatement après avoir défini la couche capable d’apprentissage, vous devez déclarer des connexions entre les couches que vous avez défini. La déclaration de faisceau connexion commence par le mot clé **à partir de**, suivi du nom de calque de source de l’offre et le type d’offre groupée de connexion à créer.   

Pour l’instant, les cinq types d’offres de connexion sont prises en charge :  

-   Offres **complet** , indiqués par le mot clé **toutes les**
-   Offres **filtré** , indiqués par le mot clé **où**, suivi d’une expression prédicat
-   Offres **Convolutional** , indiqués par le mot clé **convolve**, suivis par les attributs convolution
-   Offres de **regroupement** , indiqués par les mots clés **maximale du pool** ou **signifie pool**
-   Offres **normalisation de réponse** , indiqués par le mot clé **norme de réponse**      

## <a name="full-bundles"></a>Offres complètes  

Un ensemble de guides de connexion complète inclut une connexion à partir de chaque nœud dans le calque source à chaque nœud du calque de destination. Il s’agit du type de connexion réseau par défaut.  

## <a name="filtered-bundles"></a>Offres filtrées
Un ensemble de guides connexion filtrée inclut une bien prédicat, exprimée syntaxique, comme une expression lambda c#. L’exemple suivant définit deux offres filtrées :  

    input Pixels [10, 20];
    hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
    hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;  

-   Dans le prédicat pour _ByRow_, **s** correspond à un paramètre représentant un index dans le tableau rectangulaire des nœuds de la couche d’entrée, _Pixels_, et **d** est un paramètre représentant un index dans le tableau des nœuds de la couche masquée, _ByRow_. Le type de **s** et **d** est un uplet de nombres entiers de longueur deux. Point de vue conceptuel, **s** plages sur toutes les paires de nombres entiers avec _0 < = s [0] < 10_ et _0 < = s[1] < 20_, et **d** plages sur toutes les paires de nombres entiers, avec _0 < = [0] d < 10_ et _0 < = d[1] < 12_. 
-   Sur le côté droit de l’expression prédicat, il existe une condition. Dans cet exemple, pour chaque valeur de **et **d** ** , tels que la condition est vraie, il existe une bordure à partir du nœud de calque source vers le nœud de calque de destination. Par conséquent, cette expression de filtre indique que l’offre inclut une connexion à partir du nœud défini par **s** pour le nœud défini par **d** dans tous les cas où s [0] est égal à d [0].  

Si vous le souhaitez, vous pouvez spécifier un ensemble de poids d’un ensemble de guides filtrée. La valeur pour l’attribut **poids** doit être un uplet des valeurs à virgule flottante avec une longueur qui correspond au nombre de connexions définies par l’offre. Par défaut, épaisseurs sont générés de manière aléatoire.  

Les valeurs de poids sont regroupées par l’index de nœud de destination. Autrement dit, si le premier nœud de destination est connecté à des nœuds de source K, les premiers éléments _K_ du **poids** tuple sont les pondérations pour le premier nœud de destination, dans l’ordre d’index source. Vaut également pour les nœuds de destination restants.  

Il est possible d’indiquer épaisseurs directement en tant que valeurs de constante. Par exemple, si vous avez appris les pondérations précédemment, vous pouvez les spécifier comme des constantes à l’aide de la syntaxe suivante :

    const Weights_1 = [0.0188045055, 0.130500451, ...]


## <a name="convolutional-bundles"></a>Offres à convolution
Lorsque les données d’apprentissage possèdent une structure homogène, connexions à convolution sont fréquemment utilisées pour découvrir les fonctionnalités de base de données. Par exemple, dans l’image, les données audio ou vidéo, une dimension spatiale ou temporelle peuvent être relativement uniformes.  

Offres à convolution utilisent les rectangulaire **noyaux** renverser via les dimensions. Pour l’essentiel, chaque noyau définit un ensemble de poids appliqué dans cercles locales, appelées **applications kernel**. Chaque application noyau correspond à un nœud dans la couche source, qui est appelé le **nœud central**. Poids d’un noyau sont partagés entre plusieurs connexions. Dans un groupe à convolution, chaque noyau est rectangulaire et toutes les applications noyau ont la même taille.  

Offres à convolution prennent en charge les attributs suivants :

**InputShape** définit la dimension du calque source dans le cadre de cette offre à convolution. La valeur doit être un uplet d’entiers positifs. Le produit des entiers doit être égale au nombre de nœuds dans le calque source, mais dans le cas contraire, il n’a pas besoin correspondre à la dimension déclarée pour le calque source. La longueur de ce tuple devient la valeur **arité** pour l’offre à convolution. (Généralement arité fait référence au nombre d’arguments ou opérandes une fonction peut prendre.)  

Pour définir la forme et les emplacements des grains, utilisez les attributs **KernelShape**, **Stride**, **marge intérieure**, **LowerPad**et **UpperPad**:   

-   **KernelShape**: (obligatoire) définit la dimension de chaque noyau pour l’offre à convolution. La valeur doit être un uplet d’entiers positifs avec une durée égale à l’arité de l’offre. Chaque composant de ce tuple doit être pas dépasser le composant correspondant de **InputShape**. 
-   **STRIDE**: (facultatif) définit les tailles étape décalée de la convolution (taille d’une seule étape pour chaque dimension), qui correspond à la distance entre les nœuds central. La valeur doit être un uplet d’entiers positifs avec une longueur qui correspond à l’arité de l’offre. Chaque composant de ce tuple doit être pas dépasser le composant correspondant de **KernelShape**. La valeur par défaut est un uplet avec tous les composants égales à 1. 
-   **Partage**: (facultatif) définit l’épaisseur de partage pour chaque dimension de la convolution. La valeur peut être une valeur booléenne unique ou un uplet de valeurs booléennes avec une longueur qui correspond à l’arité de l’offre. Une seule valeur booléenne est étendue pour être un uplet de la longueur correcte avec tous les composants égales à la valeur spécifiée. La valeur par défaut est un tuple qui se compose de toutes les valeurs True. 
-   **MapCount**: (facultatif) définit le nombre de fonctionnalité maps pour l’offre à convolution. La valeur peut être un nombre entier positif unique ou un uplet d’entiers positifs avec une longueur qui correspond à l’arité de l’offre. Une seule valeur entière est étendue pour être un uplet de la longueur correcte avec les premiers composants égales à la valeur spécifiée et tous les composants restants est égal à un. La valeur par défaut est 1. Le nombre total de cartes de fonctionnalité est le produit des composants du tuple. La conception de ce nombre total entre les composants détermine comment les valeurs de mappage fonctionnalité sont regroupés dans les nœuds de destination. 
-   **Poids**: (facultatif) définit les poids initiales de l’offre. La valeur doit être un uplet de valeurs à virgule flottante avec une longueur qui est le nombre de fois noyaux le nombre de poids par noyau, tel que défini plus loin dans cet article. Les poids par défaut sont générés aléatoirement.  

Il existe deux jeux de propriétés qui contrôlent la marge intérieure, les propriétés étant s’excluent mutuellement :

-   **Marge intérieure**: (facultatif) détermine si l’entrée doit être remplie à l’aide d’un **modèle de remplissage par défaut**. La valeur peut être une valeur booléenne unique, ou elle peut être un uplet de valeurs booléennes avec une longueur qui correspond à l’arité de l’offre. Une seule valeur booléenne est étendue pour être un uplet de la longueur correcte avec tous les composants égales à la valeur spécifiée. Si la valeur d’une dimension est vrai, la source est remplie logiquement dans cette dimension avec les cellules de valeur zéro pour prendre en charge des applications noyau supplémentaires, telles que les nœuds centrale des premières et dernières grains dans cette dimension sont les premier et derniers nœuds dans cette dimension dans le calque source. Par conséquent, le nombre de nœuds « factices » dans chaque dimension est déterminé automatiquement pour s’adapter exactement à _(InputShape [d] - 1) / Stride [d] + 1_ noyaux dans le calque source complétée. Si la valeur d’une dimension est faux, les noyaux sont définis pour que le nombre de nœuds de chaque côté qui ne sont pas indiqués soit la même (jusqu'à une différence de 1). La valeur par défaut de cet attribut est un uplet avec tous les composants égales à False.
-   **UpperPad** et **LowerPad**: (facultatif) fournir mieux contrôler la quantité de remplissage à utiliser. **Importantes :** Ces attributs peuvent être définis uniquement si la propriété **remplissage** ci-dessus n’est ***pas*** définie. Les valeurs doivent être des valeurs entières tuples dont les longueurs sont l’arité de l’offre. Lorsque les attributs sont spécifiés, « factices » nœuds sont ajoutés aux extrémités inférieures et supérieures de chaque dimension du calque d’entrée. Le nombre de nœuds ajoutés aux extrémités inférieures et supérieures dans chaque dimension est déterminé respectivement par **LowerPad**[i] et **UpperPad**[i]. Pour vous assurer que les noyaux correspondent uniquement aux nœuds « réels » et non à « factices » nœuds, les conditions suivantes doivent être remplies :
    -   Chaque composant de **LowerPad** doit être strictement inférieure à KernelShape [d] / 2. 
    -   Chaque composant de **UpperPad** doit être pas dépasser KernelShape [d] / 2. 
    -   La valeur par défaut de ces attributs est un uplet avec tous les composants égales à 0. 

Le paramètre de **marge intérieure** = true permet autant remplissage est nécessaire pour maintenir le « centre » du noyau à l’intérieur de la « réel » d’entrée. Cette opération modifie la mathématiques un peu pour calculer la taille de sortie. En règle générale, la taille de sortie _D_ est calculée comme étant _D = (I - K) / S + 1_, où _I_ est la taille d’entrée, _K_ est la taille du noyau, _S_ correspond à la largeur de numérisation, et _/_ est division entière (arrondi vers zéro). Si vous définissez UpperPad = [1, 1], la taille de l’entrée __ j’équivaut effectivement 29 et donc _D = (29-5) / 2 + 1 = 13_. Toutefois, lorsque **marge intérieure** = true, pour l’essentiel __ j’obtient augmenté par _K - 1_; Par conséquent, _D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14_. En spécifiant des valeurs pour **UpperPad** et **LowerPad** vous contrôlez davantage la marge intérieure que si vous venez de définir **marge intérieure** = true.

Pour plus d’informations sur les réseaux à convolution et leurs applications, voir les articles suivants :  

-   [http://deeplearning.NET/Tutorial/lenet.HTML](http://deeplearning.net/tutorial/lenet.html )
-   [http://Research.Microsoft.com/pubs/68920/icdar03.PDF](http://research.microsoft.com/pubs/68920/icdar03.pdf) 
-   [http://People.csail.MIT.edu/jvb/Papers/cnn_tutorial.PDF](http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf)  

## <a name="pooling-bundles"></a>Regroupement des offres
Un **ensemble de guides de regroupement** s’applique géométrie similaire à la connectivité à convolution, mais elle utilise des fonctions prédéfinies à des valeurs de nœud source pour obtenir la valeur de nœud de destination. Par conséquent, offres regroupement n’ont aucun état capable d’apprentissage (poids ou écarts). Offres regroupement prend en charge tous les attributs à convolution à l’exception de **partage**, **MapCount**et des **épaisseurs**.  

En règle générale, les noyaux synthétisées par unités regroupement adjacentes ne se chevauchent pas. Si la numérisation [d] est égale à KernelShape [d] dans chaque dimension, la couche obtenue est la traditionnelle couche regroupement locale, qui est couramment utilisée dans les réseaux neuronaux à convolution. Chaque nœud de destination calcule la valeur maximale ou la moyenne des activités de son noyau dans le calque source.  

L’exemple suivant montre un lot de regroupement : 

    hidden P1 [5, 12, 12]
      from C1 max pool {
        InputShape  = [ 5, 24, 24];
        KernelShape = [ 1,  2,  2];
        Stride      = [ 1,  2,  2];
      }  

-   L’arité de l’offre est 3 (la longueur tuples **InputShape**, **KernelShape**et **Stride**). 
-   Le nombre de nœuds dans le calque source est _5 *24* 24 = 2880_. 
-   Il s’agit d’un calque regroupement local traditionnel car **KernelShape** et **Stride** sont identiques. 
-   Le nombre de nœuds dans le calque de destination est _5 *12* 12 = 1440_.  
    
Pour plus d’informations sur les couches de regroupement, voir les articles suivants :  

-   [http://www.cs.Toronto.edu/~hinton/absps/imagenet.PDF](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Section 3.4)
-   [http://cs.NYU.edu/~koray/publis/lecun-iscas-10.PDF](http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf) 
-   [http://cs.NYU.edu/~koray/publis/jarrett-iccv-09.PDF](http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)
    
## <a name="response-normalization-bundles"></a>Offres de normalisation de réponse
**Normalisation de réponse** est un jeu de normalisation local qui a été introduit par Geoffrey Hinton, et al., dans le document [ImageNet Classiﬁcation avec les réseaux neuronaux à convolution approfondie](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf). Normalisation de réponse est utilisée pour faciliter généralisation dans NEURONALES nets. Lorsqu’un neurone se déclenche selon un niveau d’activation très haute, un calque de normalisation réponse local supprime le niveau de l’activation des neurones environnants. Pour cela, à l’aide de trois paramètres (***α***, ***β***et ***k***) et une structure à convolution (ou une forme de cercle). Chaque neurone du calque de destination ***y*** correspond à un neurone ***x*** dans le calque source. Le niveau de l’activation ***d’y*** est donné par la formule suivante, où ***f*** est le niveau de l’activation d’un neurone et ***Nx*** est le noyau (ou l’ensemble qui contient les neurones aux alentours de ***x***), telle que définie par la structure à convolution suivante :  

![][1]  

Offres de normalisation réponse prend en charge tous les attributs à convolution à l’exception de **partage**, **MapCount**et des **épaisseurs**.  
 
-   Si le noyau contient neurones dans la même carte sous la forme ***x***, le jeu de normalisation correspond au **même normalisation carte**. Pour définir le même normalisation de mappage, le premier repère dans **InputShape** doit avoir la valeur 1.
-   Si le noyau contient neurones dans la même position spatiale sous la forme ***x***, mais les neurones se trouvent dans d’autres cartes, le jeu de normalisation est appelé **au-delà normalisation cartes**. Ce type de normalisation réponse met en œuvre un formulaire d’inhibition latérale inspiration par le type figurant dans neurones réels, création de concurrence pour les niveaux d’activation volumineux entre des sorties de neurone calculées sur les différentes cartes. Pour définir au sein de normalisation cartes, le premier repère doit être un nombre entier supérieur à 1 et ne supérieur au nombre de cartes, et le reste des coordonnées doit avoir la valeur 1.  

Étant donné que les offres de normalisation réponse appliquent une fonction prédéfinie à des valeurs de nœud source pour déterminer la valeur de nœud de destination, ils ne disposent d’aucun état capable d’apprentissage (poids ou écarts).   

**Alerte**: les nœuds du calque de destination correspondent aux neurones sont les nœuds centrale des grains. Par exemple, si KernelShape [d] est impair, puis _KernelShape [d] / 2_ correspond au nœud noyau central. Si _KernelShape [d]_ est pair, le nœud central est au _KernelShape [d] / 2-1_. Par conséquent, si la **marge intérieure**[d] est faux, la première et la dernière _KernelShape [d] / 2_ nœuds n’ont pas de nœuds correspondants dans le calque de destination. Pour éviter cette situation, définissez **marge intérieure** en tant que [vrai, vrai,..., vrai].  

En plus des quatre attributs décrites plus haut, offres de normalisation réponse prennent également en charge les attributs suivants :  

-   **Alpha**: (obligatoire) indique une valeur à virgule flottante qui correspond au ***α*** dans la formule précédente. 
-   **Bêta**: (obligatoire) indique une valeur à virgule flottante qui correspond au ***β*** dans la formule précédente. 
-   **Décalage**: (facultatif) indique une valeur à virgule flottante qui correspond au ***k*** dans la formule précédente. 1 par défaut.  

L’exemple suivant définit un ensemble de guides de normalisation de réponse à l’aide de ces attributs :  

    hidden RN1 [5, 10, 10]
      from P1 response norm {
        InputShape  = [ 5, 12, 12];
        KernelShape = [ 1,  3,  3];
        Alpha = 0.001;
        Beta = 0.75;
      }  

-   Le calque source inclut des mappages de cinq, chacune avec unede dimension de 12 x 12, totalisation dans les nœuds 1440. 
-   La valeur de **KernelShape** indique qu’il s’agit d’un même normalisation calque, où le cercle est un rectangle de 3 x 3. 
-   La valeur par défaut de **marge intérieure** est faux, et donc le calque de destination comporte seulement 10 nœuds dans chaque dimension. Pour inclure un nœud dans le calque de destination qui correspond à tous les nœuds dans le calque source, ajouter le remplissage = [vrai, vrai, vrai] ; et modifier la taille de RN1 à [5, 12, 12].  

## <a name="share-declaration"></a>Déclaration de partage 
NET # éventuellement prend en charge définition plusieurs offres épaisseurs partagés. Poids de n’importe quel deux offres peuvent être partagées si leurs structures sont identiques. La syntaxe suivante définit offres épaisseurs partagés :  

    share-declaration:
        share    {    layer-list    }
        share    {    bundle-list    }
       share    {    bias-list    }
    
    layer-list:
        layer-name    ,    layer-name
        layer-list    ,    layer-name
    
    bundle-list:
       bundle-spec    ,    bundle-spec
        bundle-list    ,    bundle-spec
    
    bundle-spec:
       layer-name    =>     layer-name
    
    bias-list:
        bias-spec    ,    bias-spec
        bias-list    ,    bias-spec
    
    bias-spec:
        1    =>    layer-name
    
    layer-name:
        identifier  

Par exemple, la déclaration de partager suivante indique les noms de calque, indiquant que poids et écarts doivent être partagées :  

    Const {
      InputSize = 37;
      HiddenSize = 50;
    }
    input {
      Data1 [InputSize];
      Data2 [InputSize];
    }
    hidden {
      H1 [HiddenSize] from Data1 all;
      H2 [HiddenSize] from Data2 all;
    }
    output Result [2] {
      from H1 all;
      from H2 all;
    }
    share { H1, H2 } // share both weights and biases  

-   Les fonctionnalités d’entrée sont répartis dans deux couches d’entrée dimensionnés égales. 
-   Couches cachées calculent des fonctionnalités de niveau supérieures sur les deux couches d’entrée. 
-   La déclaration de partager indique que _H1_ et _H2_ doivent être calculé de la même façon à partir de leurs entrées respectives.  
 
Par ailleurs, il peut être spécifiée avec deux déclarations de partager séparées comme suit :  

    share { Data1 => H1, Data2 => H2 } // share weights  

<!-- -->

    share { 1 => H1, 1 => H2 } // share biases  

Vous pouvez utiliser la forme abrégée uniquement lorsque les couches contiennent un ensemble unique. En règle générale, le partage est possible uniquement lorsque la structure pertinente est identique, ce qui signifie qu’ils ont la même taille, même géométrie à convolution et ainsi de suite.  

## <a name="examples-of-net-usage"></a>Exemples d’utilisation nette #
Cette section fournit des exemples de comment vous pouvez utiliser Net # pour ajouter les calques masqués, définissez la façon dont les calques masqués interagissent avec d’autres calques et créer des réseaux à convolution.   

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Définir un réseau neuronal personnalisé simple : exemple « Hello World »
Cet exemple simple montre comment créer un modèle de réseau neuronal qui comporte un seul calque masqué.  

    input Data auto;
    hidden H [200] from Data all;
    output Out [10] sigmoid from H all;  

L’exemple illustre certains des commandes de base comme suit :  

-   La première ligne définit la couche d’entrée (nommée _données_). Lorsque vous utilisez le mot clé **auto** , le réseau neuronal inclut automatiquement toutes les colonnes de fonctionnalité dans les exemples d’entrée. 
-   La deuxième ligne crée la couche masquée. Le nom _H_ est affecté à la couche masquée, c'est-à-dire 200 nœuds. Cette couche est entièrement connectée à la couche d’entrée.
-   La troisième ligne définit la couche de sortie (appelée _O_), qui contient 10 postes de sortie. Si le réseau neuronal est utilisé pour le classement, est un nœud de sortie par cours. Le mot-clé **sigmoïde** indique que la fonction de sortie est appliquée à la couche de sortie.   

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Définir plusieurs couches masquées : exemple de vision ordinateur
L’exemple suivant montre comment définir un réseau neuronal légèrement plus complexe, avec plusieurs calques masqués personnalisés.  

    // Define the input layers 
    input Pixels [10, 20];
    input MetaData [7];
    
    // Define the first two hidden layers, using data only from the Pixels input
    hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
    hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
    
    // Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
    hidden Gather [100] 
    {
      from ByRow all;
      from ByCol all;
    }
    
    // Define the output layer and its sources
    output Result [10]  
    {
      from Gather all;
      from MetaData all;
    }  

Cet exemple illustre plusieurs fonctionnalités du langage de spécification réseaux neuronaux :  

-   La structure a deux couches d’entrée, _Pixels_ et les _métadonnées_.
-   La couche _Pixels_ est un calque source pour les deux offres de connexion, avec les couches de destination, _ByRow_ et _ByCol_.
-   Les couches _collecter_ et _résultat_ sont couches de destination dans plusieurs offres de connexion.
-   La couche de sortie, _résultat_, est un calque de destination dans deux offres connexion ; une avec la deuxième niveau masquée (recueillez) comme un calque de destination et l’autre avec la couche d’entrée (métadonnées) comme un calque de destination.
-   Les couches masquées, _ByRow_ et _ByCol_, spécifiez connectivité filtrée en utilisant des expressions prédicats. Plus précisément, le nœud dans _ByRow_ à [x, y] est connecté aux nœuds en _Pixels_ qui possèdent le premier repère index égal à coordonnées première du nœud, x. De même, le nœud dans _ByCol à [x, y] est connecté aux nœuds en _Pixels_ qui possèdent le deuxième index coordonner au sein d’une des coordonnées deuxième du nœud, y.  

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Définir un réseau à convolution pour le classement multiclass : exemple de reconnaissance vocale de chiffres
La définition du réseau suivant est conçue pour reconnaître les chiffres, et illustre quelques techniques avancées pour la personnalisation d’un réseau neuronal.  

    input Image [29, 29];
    hidden Conv1 [5, 13, 13] from Image convolve 
    {
       InputShape  = [29, 29];
       KernelShape = [ 5,  5];
       Stride      = [ 2,  2];
       MapCount    = 5;
    }
    hidden Conv2 [50, 5, 5]
    from Conv1 convolve 
    {
       InputShape  = [ 5, 13, 13];
       KernelShape = [ 1,  5,  5];
       Stride      = [ 1,  2,  2];
       Sharing     = [false, true, true];
       MapCount    = 10;
    }
    hidden Hid3 [100] from Conv2 all;
    output Digit [10] from Hid3 all;  


-   La structure a un calque d’entrée unique, _Image_.
-   Le mot clé **convolve** indique que les couches nommées _Conv1_ et _Conv2_ sont à convolution couches. Chacune de ces déclarations de calque est suivi d’une liste des attributs convolution.
-   Filet a un tiers qui masquée des calques, _Hid3_, qui est entièrement connecté au second calque masqué, _Conv2_.
-   La couche de sortie, _chiffres_, est connectée uniquement à la troisième couche masquée, _Hid3_. Le mot clé **tous les** indique que la couche de sortie est entièrement connectée à _Hid3_.
-   L’arité de la convolution est trois (la longueur tuples **InputShape**, **KernelShape**, **Stride**et **partage**). 
-   Le nombre de poids par noyau est _1 + **KernelShape**\[0] * * *KernelShape**\[1]* **KernelShape**\[2] = 1 + 1 *5* 5 = 26. Ou 26 * 50 = 1300_.
-   Vous pouvez calculer les nœuds dans chaque calque masqué comme suit :
    -   **NodeCount**\[0] = (5 - 1) / 1 + 1 = 5.
    -   **NodeCount**\[1] = (13-5) / 2 + 1 = 5. 
    -   **NodeCount**\[2] = (13-5) / 2 + 1 = 5. 
-   Le nombre total de nœuds peut être calculé à l’aide de le dimension déclarée du calque, [50, 5, 5], comme suit : _ **MapCount** * * *NodeCount**\[0]* **NodeCount**\[1] * * *NodeCount**\[2] = 10* 5 *5* 5_
-   Étant donné que **partage**[d] est faux uniquement pour _d == 0_, le nombre de grains est _ **MapCount** * * *NodeCount**\[0] = 10* 5 = 50_. 


## <a name="acknowledgements"></a>Accusés de réception

La langue pour la personnalisation de l’architecture de réseaux neuronaux Net # a été développée à Microsoft par Shon Katzenberger (architecte, apprentissage automatique) et Alexey Kamenev (logiciel technicien, Microsoft Research). Elle est utilisée en interne pour apprentissage projets et applications allant de détection d’images à analytique de texte de l’ordinateur. Pour plus d’informations, voir [Neuronal Nets dans Azure ML - Introduction à Net #](http://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)


[1]:./media/machine-learning-azure-ml-netsharp-reference-guide/formula_large.gif
 
