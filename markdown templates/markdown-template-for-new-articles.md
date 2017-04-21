<properties
   pageTitle="Titre de la page qui s’affiche dans les résultats d’onglet et recherche de navigateur"
   description="Description de l’article qui s’affichera dans les pages d’accueil et dans la plupart des résultats de recherche"
   services="service-name"
   documentationCenter="dev-center-name"
   authors="GitHub-alias-of-only-one-author"
   manager="manager-alias"
   editor=""/>

<tags
   ms.service="required"
   ms.devlang="may be required"
   ms.topic="article"
   ms.tgt_pltfrm="may be required"
   ms.workload="required"
   ms.date="mm/dd/yyyy"
   ms.author="Your MSFT alias or your full email address;semicolon separates two or more"/>

# <a name="markdown-template-article-heading-1-or-h1-heading-at-the-top-of-the-article"></a>Modèle démarque (article titre 1 ou H1) : titre en haut de l’article

Pour copier la démarque à partir de ce modèle, copiez l’article dans vos locaux mis en pension, ou cliquez sur le bouton brut dans la GitHub UI et copiez la démarque.

  ![Texte de remplacement ; ne pas laisser vide. Décrire l’image.][8]

Paragraphe d’introduction : Lorem dolor amet, adipiscing concerné. Phasellus interdum nulla risus, lacinia Portal nisl imperdiet fichier .sed. Mauris dolor mauris, tempus sed lacinia nec, felis non euismod. NUNC siège Portal ultrices. Maecenas neque nulla, Lorem ipsum condimentum curriculum vitae réunir amet, aliquet dignissim nisi.

## <a name="heading-2-h2"></a>Titre 2 (H2)

Aenean réunir amet dirigée nec purus placerat fermentum ac gravida odio. Aenean tellus lectus, faucibus dans rhoncus dans faucibus sed urna.  volutpat mi id purus ultrices iaculis nec non neque. [Texte du lien lien en dehors de azure.microsoft.com](http://weblogs.asp.net/scottgu). Dolor dictum de Nullam en aliquam pharetra. Vivamus ac hendrerit mauris [texte du lien exemple lien vers un article dans un dossier de service](../articles/expressroute/expressroute-bandwidth-upgrade.md).

Obtenir plus de 10 fois le trafic à partir de [Google]  [ gog] que depuis [Yahoo]  [ yah] ou [MSN] [msn].

> [AZURE.NOTE] Texte d’une note en retrait.  Le mot « note » est ajouté lors de la publication. Absence de l’UE pretium lacus. Nullam purus est iaculis sed est veau, euismod vehicula odio. Curabitur lacinia, erat tristique iaculis rutrum, erat Enterprise Manager sodales nisi, l’Union européenne condimentum turpis nisi un purus.

1. Aenean réunir amet dirigée nec **Purus** placerat fermentum ac gravida odio.

2. Aenean tellus lectus, faucius dans **Rhoncus** dans faucibus sed urna. Suspendisse volutpat mi id purus ultrices iaculis nec non neque.

    ![Texte de remplacement ; ne pas laisser vide. Voiture collecteur en rouge de course.][5]

3. Dolor dictum de Nullam en aliquam pharetra. Vivamus ac hendrerit mauris. Sed dolor dui, condimentum et varius a vehicula en nisl.

    ![Texte de remplacement ; ne pas laisser vide][6]


Suspendisse volutpat mi id purus ultrices iaculis nec non neque. Dolor dictum de Nullam en aliquam pharetra. Vivamus ac hendrerit mauris. Otrus informatus : [1 lien à une autre rubrique de documentation azure.microsoft.com](virtual-machines-windows-hero-tutorial.md)

## <a name="heading-h2"></a>Titre (H2)

Absence de l’UE pretium lacus. Nullam purus est iaculis sed est veau, euismod vehicula odio.

1. Curabitur lacinia, erat tristique iaculis rutrum, erat Enterprise Manager sodales nisi, l’Union européenne condimentum turpis nisi un purus.

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

2. Vestibulum ante Lorem ipsum primis dans faucibus orci luctus et ultrices posuere cubilia.

        // Because toast alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }


    > [AZURE.NOTE] Duis sed diam non <i>nisl molestie</i> pharetra eget une application est disponible. [Lien 2 à une autre rubrique de documentation azure.microsoft.com](web-sites-custom-domain-name.md)


Quisque commodo eros veau lectus euismod auctor eget réunir amet dirigée. Proin faucibus suscipit tellus dignissim ultrices.

## <a name="heading-2-h2"></a>Titre 2 (H2)

1. Condimentum de sed Maecenas nisi. Suspendisse potenti.

  + Fusce
  + Malesuada
  + Enterprise Manager

2. Nullam dans massa UE tellus tempus hendrerit.

    ![Texte de remplacement ; ne pas laisser vide. Exemple d’une vidéo Channel 9.][7]

3. Quisque felis enim, fermentum absence aliquam nec, pellentesque pulvinar magna.




<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

Vestibul ante Lorem ipsum primis dans faucibus orci luctus et ultrices posuere cubilia Curae ; Nullam ultricies, Lorem ipsum curriculum vitae volutpat hendrerit, eros de pretium purus diam, curriculum vitae tincidunt nulla lorem sed turpis : [3 lien à une autre rubrique documentation azure.microsoft.com](storage-whatis-account.md).

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/        
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/    
