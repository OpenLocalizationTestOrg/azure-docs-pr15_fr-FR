<properties
    pageTitle="Unité pellicule un didacticiel boule"
    description="Étapes de création de l’unité classique opérationnelle un jeu de balle qui est requis pour tous les didacticiels unité d’Engagement Mobile"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

#<a id="unity-roll-a-ball"></a>Créer une unité opérationnelle un jeu de balle

Ce didacticiel décrit les principales étapes pour [unité pellicule un didacticiel coudre](http://unity3d.com/learn/tutorials/projects/roll-ball-tutorial)légèrement modifiées. Cet exemple de partie comporte un objet sphérique 'lecteur' qui est contrôlé par l’utilisateur de l’application et l’objectif du jeu est « collecter » les objets pouvant être collectés par colliding l’objet du lecteur avec ces objets pouvant être collectés. Cela suppose une connaissance de base avec un environnement de l’éditeur unité. Si vous rencontrez des problèmes vous devez faire le didacticiel complet. 

### <a name="setting-up-the-game"></a>La configuration du jeu
Les étapes suivantes sont dans le [didacticiel unité](https://unity3d.com/learn/tutorials/projects/roll-a-ball/set-up?playlist=17141)

1. Ouvrez **L’éditeur de l’unité** et cliquez sur **Nouveau**. 
    
    ![][51] 
    
2. Indiquez un **nom de projet** & **emplacement**, sélectionnez **3D** , cliquez sur **créer un projet**.
    
    ![][52]

3. Enregistrer la scène par défaut venez de créer dans le cadre du nouveau projet comme avec le nom **MiniGame** dans un nouvel ** \_scènes** sous **éléments de** dossier :
    
    ![][53]

4. Créer un **objet 3D -> plan** en tant que le champ de jeu et renommer cet objet plan au **sol**

    ![][1]

5. Réinitialiser le composant de transformation de cet objet **sol** afin qu’il soit à l’origine. 

    ![][3]

6. Décochez la case **Afficher la grille** du **menu trucs** pour l’objet de **sol** .

    ![][4]

7. Mettre à jour le composant **d’échelle** pour l’objet **sol** [X = 2, Y = 1, Z = 2]. 

    ![][5]

8. Ajouter un nouvel **objet 3D -> cercle** au projet et renommer cet objet cercle sous **le lecteur**. 

    ![][6]

9. Sélectionnez l’objet de **lecteur** et cliquez sur **Rétablir la transformation** similaire à l’objet de plan. 

10. Mise à jour **Transformation -> Position -> coordonnées Y** composant pour la valeur Y du lecteur par 0,5.  

    ![][7]

11. Créer un nouveau dossier nommé **supports de cours** dans le projet, dans laquelle vous allez créer du contenu vers le lecteur de couleurs. 

12. Créer un nouveau **matériel** appelé **arrière-plan** dans ce dossier. 

    ![][8]

13. Mettre à jour la couleur de la matière en mettant à jour la propriété **Albedo** de celle-ci. Vous pouvez sélectionner les valeurs RVB de [0,32,64]. 

    ![][9]

14. Faites glisser ce document dans la vue de la scène pour appliquer une couleur à l’objet de **sol** . 

    ![][10]

17. Mettre à jour pour finir le **Transformation -> Rotation -> Y** 60 sur l’objet de lumière directionnelle pour plus de clarté. 

    ![][12]

### <a name="moving-the-player"></a>Déplacement du lecteur
Les étapes suivantes sont dans le [didacticiel unité](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-player?playlist=17141)

1. Ajouter un composant **RigidBody** à l’objet **lecteur** . 

    ![][13]

2. Créer un nouveau dossier nommé **Scripts** dans le projet. 

3. Cliquez sur **Ajouter composant -> Nouveau Script -> Script c#**. Nommez-la **PlayerController**, puis cliquez sur **créer et ajouter**. Cela crée et joindre un script à l’objet du lecteur.  

    ![][14]

5. Déplacer ce script sous le dossier **Scripts** dans le projet. 

6. Ouvrez le script à modifier dans votre éditeur de script favori, mettre à jour le code de script par le code suivant et enregistrez-le. 

        using UnityEngine;
        using System.Collections;
        
        public class PlayerController : MonoBehaviour 
        {
            public float speed;
            private Rigidbody rb;
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
                rb.AddForce (movement * speed);
            }
        }
    
8. Notez que le script ci-dessus utilise une propriété de **vitesse** . Dans l’éditeur d’unité, mettez à jour la propriété vitesse 10.  

    ![][15]

9. Appuyez sur **lire** dans l’éditeur de l’unité. Désormais, vous devez être en mesure de contrôler le curseur à l’aide du clavier et il doit faire pivoter et déplacer. 

### <a name="moving-the-camera"></a>Déplacement de la caméra
Les étapes suivantes sont issues le [didacticiel unité](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-camera?playlist=17141) et va lier la **Caméra principaux** à l’objet **lecteur** . 

1. Mettre à jour **Transform.Position** pour être X = 0, Y = 10.5, Z =-10.  
2. Mettre à jour **Transform.Rotation** pour être X = 45, Y = 0, Z = 0.  

    ![][16]

2. Ajouter un nouveau script appelé **CameraController** à la **MainCamera** et placer sous le dossier de Scripts. 

    ![][17]

3. Ouvrez le script pour modification et ajoutez le code suivant qu’il contient :

        using UnityEngine;
        using System.Collections;
        
        public class CameraController : MonoBehaviour {
        
            public GameObject player;
        
            private Vector3 offset;
        
            void Start ()
            {
                offset = transform.position - player.transform.position;
            }
            
            void LateUpdate ()
            {
                transform.position = player.transform.position + offset;
            }
        }
    
5. Dans l’environnement unité - faites glisser la variable du lecteur dans l’emplacement du lecteur de l’objet caméra principaux afin que les deux sont associées à un autre. 

    ![][18]

6. Maintenant si vous atteignez Play dans l’éditeur d’unité et faire pivoter l’objet du lecteur coudre puis vous verrez l’appareil photo suit le mouvement.  

### <a name="setting-up-the-play-area"></a>La configuration de la zone de lecture
Les étapes suivantes sont compris le [didacticiel unité](https://unity3d.com/learn/tutorials/projects/roll-a-ball/setting-up-the-play-area?playlist=17141). Nous allons créer les murs qui entoure le sol, afin que l’objet du lecteur coudre ne supprime hors de la zone lecture dans son mouvement. 

1. Cliquez sur **Créer -> créer un élément vide -> objet jeu** et nommez-la **murs**

    ![][19]

2. Sous cet objet murs - créer un nouvel **objet 3D -> Cube** et nommez-la « Mur ouest ». 

    ![][20]

3. Mettre à jour la **Transformation -> Position** et la **Transformation -> échelle** pour cet objet mur ouest. 

    ![][21]

4. Dupliquer le mur ouest pour créer un **mur orientale** avec la position de transformation mis à jour et les proportions. 

    ![][22]

5. Dupliquer le mur orientale pour créer un **mur Amérique du Nord** avec la position de transformation mis à jour et échelle. 

    ![][23]

6. Dupliquer le mur Amérique du Nord et créez un **mur Sud** avec la position de transformation mis à jour et échelle. 

    ![][24]

### <a name="creating-collectible-objects"></a>Création d’objets pouvant être collectés
Les étapes suivantes sont compris le [didacticiel unité](https://unity3d.com/learn/tutorials/projects/roll-a-ball/creating-collectables?playlist=17141). Nous allons créer certains objets aspect attrayants formant le jeu d’objets pouvant être collectés dont l’objet du lecteur coudre a besoin pour recueillir en collision avec les. 

1. Créer un nouvel **objet Cube 3D** et nommez-la collecte. 

2. Ajuster la **Transformation -> Rotation** & **Transformation -> échelle** de l’objet collecte. 

    ![][25]

3. Créer et joindre un **Nouveau Script c#** appelé **rotation** à l’objet collecte. Veillez à placer le script sous le dossier de Scripts. 

    ![][26]

4. Ouvrez ce script pour le modifier et mettre à jour pour être le suivant : 

        using UnityEngine;
        using System.Collections;
        
        public class Rotator : MonoBehaviour {
        
            void Update () 
            {
                transform.Rotate (new Vector3 (15, 30, 45) * Time.deltaTime);
            }
        }

5. Le mode lecture dans l’éditeur d’unité et votre diaporama puisse passer objet collecte atteint maintenant être rotation sur son axe.

6. Créer un nouveau dossier nommé **Prefabs** 

    ![][27]

7. Faites glisser l’objet de **collecte** et le placer dans le dossier Prefabs.

    ![][28]

8. Créer un nouvel **objet jeu vide** appelé **collectes**. Rétablir sa position de départ, puis faites glisser l’objet collecte sous cet objet « Match ».  

    ![][29]

9. Dupliquer l’objet de **collecte** et se propager sur l’objet de **sol** autour de l’objet **lecteur** en mettant à jour les valeurs **X et Z de Transform.Position** manière appropriée. 

    ![][30]

10. Créer un **nouveau matériau** appelé **collecte** et mettre à jour pour être rouge en couleur en mettant à jour la **propriété Albedo** semblable à ce que nous l’avons fait pour mettre à jour l’objet de sol. 

    ![][31]

11. Appliquer le matériau à tous les objets collecte 4.

    ![][32]

### <a name="collecting-the-pickup-objects"></a>Collecter les objets collecte
Les étapes suivantes sont compris le [didacticiel unité](https://unity3d.com/learn/tutorials/projects/roll-a-ball/collecting-pick-up-objects?playlist=17141). Nous met à jour le lecteur afin qu’il soit en mesure de « collecter » les objets collecte par colliding avec eux. 

1. Ouvrez le script **PlayerController** associé à l’objet du lecteur pour le modifier et mettre à jour à ce qui suit :  

        using UnityEngine;
        using System.Collections;
        
        public class PlayerController : MonoBehaviour {
        
            public float speed;
        
            private Rigidbody rb;
        
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
        
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
        
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
        
                rb.AddForce (movement * speed);
            }
        
            void OnTriggerEnter(Collider other) 
            {
                if (other.gameObject.CompareTag ("Pick Up"))
                {
                    other.gameObject.SetActive (false);
                }
            }
        }

2. Créer une nouvelle **balise** appelée **Sélectionnez vers le haut** (elle doit correspondre à ce qui figure dans le script)  

    ![][33]
    
    ![][34]

3. Appliquez cette **balise** à l’objet collecte Prefab. 

    ![][35]

4. Activer la case à cocher **IsTrigger** pour l’objet PREFABRIQUE polyvalent.

    ![][36]

5. Ajouter un corps rigide à collecte Prefab objet. Pour optimiser les performances nous mettent à jour la collider statique que nous avons utilisé un collider dynamique. 

    ![][37]
  
6. Enfin, recherchez la propriété **IsKinematic** pour l’objet prefab. 

    ![][38]

7. Positionnement **lire** dans l’éditeur d’unité et vous pourrez jouer à ce jeu **opérationnelle une ballon** en déplaçant l’objet de lecteur en utilisant les touches du clavier pour la saisie de direction. 

### <a name="updating-the-game-for-mobile-play"></a>Mise à jour le jeu de lecture mobile
Les sections précédentes conclure le didacticiel à partir de l’unité de base. Maintenant, nous allons modifier le jeu afin de pouvoir appareil mobile convivial. Notez que nous avons utilisé le clavier pour le jeu jusqu'à présent pour le test. Maintenant nous allons modifier afin que nous puissions contrôler le lecteur à l’aide de la trajectoire du téléphone c'est-à-dire à l’aide d’accéléromètre comme entrée. 

Ouvrez le script **PlayerController** pour le modifier et mettre à jour de la méthode **FixedUpdate** pour utiliser le mouvement à partir de l’accéléromètre pour déplacer l’objet du lecteur. 

        void FixedUpdate()
        {
            //float moveHorizontal = Input.GetAxis("Horizontal");
            //float moveVertical = Input.GetAxis("Vertical");
            //Vector3 movement = new Vector3(moveHorizontal, 0.0f, moveVertical);
            rb.AddForce(Input.acceleration.x * Speed, 0, -Input.acceleration.z * Speed);
        }

Ce didacticiel conclut la création d’un jeu simple avec unité puis vous pouvez déployer sur un périphérique de votre choix pour le jeu. 

<!-- Images -->
[1]: ./media/mobile-engagement-unity-roll-a-ball/1.png  
[2]: ./media/mobile-engagement-unity-roll-a-ball/2.png
[3]: ./media/mobile-engagement-unity-roll-a-ball/3.png
[4]: ./media/mobile-engagement-unity-roll-a-ball/4.png
[5]: ./media/mobile-engagement-unity-roll-a-ball/5.png
[6]: ./media/mobile-engagement-unity-roll-a-ball/6.png
[7]: ./media/mobile-engagement-unity-roll-a-ball/7.png
[8]: ./media/mobile-engagement-unity-roll-a-ball/8.png
[9]: ./media/mobile-engagement-unity-roll-a-ball/9.png  
[10]: ./media/mobile-engagement-unity-roll-a-ball/10.png    
[11]: ./media/mobile-engagement-unity-roll-a-ball/11.png    
[12]: ./media/mobile-engagement-unity-roll-a-ball/12.png
[13]: ./media/mobile-engagement-unity-roll-a-ball/13.png
[14]: ./media/mobile-engagement-unity-roll-a-ball/14.png
[15]: ./media/mobile-engagement-unity-roll-a-ball/15.png
[16]: ./media/mobile-engagement-unity-roll-a-ball/16.png
[17]: ./media/mobile-engagement-unity-roll-a-ball/17.png
[18]: ./media/mobile-engagement-unity-roll-a-ball/18.png
[19]: ./media/mobile-engagement-unity-roll-a-ball/19.png    
[20]: ./media/mobile-engagement-unity-roll-a-ball/20.png    
[21]: ./media/mobile-engagement-unity-roll-a-ball/21.png    
[22]: ./media/mobile-engagement-unity-roll-a-ball/22.png    
[23]: ./media/mobile-engagement-unity-roll-a-ball/23.png    
[24]: ./media/mobile-engagement-unity-roll-a-ball/24.png    
[25]: ./media/mobile-engagement-unity-roll-a-ball/25.png    
[26]: ./media/mobile-engagement-unity-roll-a-ball/26.png    
[27]: ./media/mobile-engagement-unity-roll-a-ball/27.png    
[28]: ./media/mobile-engagement-unity-roll-a-ball/28.png    
[29]: ./media/mobile-engagement-unity-roll-a-ball/29.png    
[30]: ./media/mobile-engagement-unity-roll-a-ball/30.png    
[31]: ./media/mobile-engagement-unity-roll-a-ball/31.png    
[32]: ./media/mobile-engagement-unity-roll-a-ball/32.png    
[33]: ./media/mobile-engagement-unity-roll-a-ball/33.png    
[34]: ./media/mobile-engagement-unity-roll-a-ball/34.png    
[35]: ./media/mobile-engagement-unity-roll-a-ball/35.png    
[36]: ./media/mobile-engagement-unity-roll-a-ball/36.png    
[37]: ./media/mobile-engagement-unity-roll-a-ball/37.png    
[38]: ./media/mobile-engagement-unity-roll-a-ball/38.png    
[51]: ./media/mobile-engagement-unity-roll-a-ball/new-project.png
[52]: ./media/mobile-engagement-unity-roll-a-ball/new-project-properties.png
[53]: ./media/mobile-engagement-unity-roll-a-ball/save-scene.png

    
    
    
    
    
    
    
    
    
    
    
    
