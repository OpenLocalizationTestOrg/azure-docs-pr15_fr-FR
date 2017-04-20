<properties 
   pageTitle="Sécurité StorSimple | Microsoft Azure" 
   description="Décrit les fonctionnalités de sécurité et confidentialité protègent vos données en local et dans le cloud, appareil et service StorSimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="05/03/2016"
   ms.author="v-sharos"/>

# <a name="storsimple-security-and-data-protection"></a>StorSimple sécurité et protection des données

## <a name="overview"></a>Vue d’ensemble

La sécurité est un problème majeur pour toute personne qui a adopté une nouvelle technologie, particulièrement quand la technologie est utilisée avec des données confidentielles ou propriétaires. Lorsque vous évaluez des technologies différentes, vous devez prendre en compte accrues risques et les coûts de protection des données. Microsoft Azure StorSimple fournit à la fois une solution de sécurité et confidentialité pour la protection des données, garantissant : 

- **Confidentialité** – seuls les entités autorisées verront vos données. 
- **Intégrité référentielle** : seules les entités autorisées peut modifier ou supprimer vos données.

La solution Microsoft Azure StorSimple est constitué de quatre composants principaux interagissent avec eux :

- **Service Manager StorSimple hébergé dans Microsoft Azure** – le service de gestion que vous utilisez pour configurer et configurer le périphérique StorSimple.
- **Appareil StorSimple** – un périphérique physique installé dans votre centre de données. Tous les hôtes et les clients qui génèrent des données vous connecter à l’appareil StorSimple et l’appareil gère les données et le déplace vers le cloud Azure selon le cas.
- **Clients/hôtes connectés à l’appareil** – les clients dans votre infrastructure qui se connectent à l’appareil StorSimple et générer des données qui doivent être protégées.
- **Stockage cloud** – l’emplacement dans le cloud Azure l’emplacement de stockage des données.

Les sections suivantes décrivent les fonctionnalités de sécurité StorSimple qui aident à protéger chacun de ces composants et les données qu’il contient. Il inclut également une liste de questions que vous vous posez sur Microsoft Azure StorSimple sécurité, ainsi que les réponses correspondantes.

## <a name="storsimple-manager-service-protection"></a>Protection contre le service Manager StorSimple

Le service Manager StorSimple est un service de gestion, hébergé dans Microsoft Azure et permet de gérer tous les appareils StorSimple que votre organisation a achetées. Vous pouvez accéder au service Manager StorSimple à l’aide de vos informations d’identification d’organisation pour vous connecter au portail Azure classique via un navigateur web. 

Accès au service Manager StorSimple requiert que votre organisation dispose d’un abonnement Azure incluant StorSimple. Votre abonnement régit les fonctionnalités que vous pouvez accéder dans le portail classique Azure. Si votre organisation ne dispose pas d’un abonnement Azure et que vous voulez en savoir plus sur les, voir [vous inscrire Azure comme une organisation](../active-directory/sign-up-organization.md). 

Étant donné que le service Manager StorSimple est hébergé dans Azure, il est protégé par les fonctionnalités de sécurité Azure. Pour plus d’informations sur les fonctionnalités de sécurité fournies par Microsoft Azure, accédez au [Centre de gestion de la confidentialité de Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>Protection des équipements StorSimple

Le périphérique StorSimple est un dispositif de stockage local hybride qui contient des disques (SSDs) et des disques durs (disques durs), ainsi que la contrôleurs redondants et des fonctions de basculement automatique. Les contrôleurs de gérer le stockage hiérarchisation, en plaçant actuellement utilisées (ou active) des données sur le stockage local (dans les StorSimple appareil local serveurs ou), tout en les déplaçant moins fréquemment utilisées dans le cloud.

Seuls les autorisés StorSimple périphériques sont autorisés à rejoindre ce service Manager StorSimple que vous avez créé votre abonnement Azure. Si vous êtes un appareil, vous devez l’enregistrer avec le service Manager StorSimple grâce à la clé d’enregistrement de service. La clé d’inscription de service est une clé aléatoire 128 bits générée dans le portail classique Azure. 

![Clé d’enregistrement de service](./media/storsimple-security/ServiceRegistrationKey.png)

Pour savoir comment obtenir une clé d’inscription de service, accédez à [étape 2 : obtenir la clé d’inscription du service](storsimple-deployment-walkthrough.md#step-2-get-the-service-registration-key).

La clé d’inscription de service est une clé long qui contient plus de 100 caractères. Vous pouvez copier la clé et enregistrez-le dans un fichier texte dans un emplacement sécurisé afin que vous pouvez l’utiliser pour autoriser les autres appareils comme bon vous semble. Si la clé d’inscription de service est perdue après avoir enregistré votre premier appareil, vous pouvez générer une nouvelle clé à partir du service Manager StorSimple. Cela n’affecte pas le fonctionnement des équipements existants. 

Après avoir enregistré un périphérique, il utilise des jetons pour communiquer avec Microsoft Azure. La clé d’inscription de service n’est pas utilisée après l’inscription d’appareil.

> [AZURE.NOTE] Nous vous recommandons de régénérer la clé d’inscription service après chaque utilisation.

## <a name="protect-your-storsimple-solution-via-passwords"></a>Protéger votre solution StorSimple via les mots de passe

Les mots de passe sont un aspect important de la sécurité informatique et sont largement utilisées dans la solution StorSimple pour vous assurer que vos données sont accessibles aux utilisateurs autorisés uniquement. StorSimple permet de vous permettent de configurer les mots de passe suivantes :

- Mot de passe administrateur StorSimple appareil
- Défi négociation CHAP (Authentication Protocol) et cibles les mots de passe
- Gestionnaire d’instantanés StorSimple le mot de passe

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell pour StorSimple et le mot de passe administrateur de périphérique StorSimple

Windows PowerShell pour StorSimple est une interface de ligne de commande que vous pouvez utiliser pour gérer le périphérique StorSimple. Windows PowerShell pour StorSimple inclut des fonctionnalités qui vous permettent d’inscrire votre appareil, configurer l’interface réseau sur votre appareil, installer certains types de mises à jour, résoudre les problèmes de votre appareil en accédant à la session de support et modifier l’état du périphérique. Vous pouvez accéder Windows PowerShell pour StorSimple en vous connectant à la console série sur l’appareil ou à l’aide de Windows PowerShell distante.

Accès distant PowerShell peut être effectué sur HTTPS ou HTTP. Si la gestion à distance HTTPS est activée, vous devez télécharger le certificat de gestion à distance à partir de l’appareil et l’installer sur le client distant. Pour plus d’informations sur PowerShell distante, accédez à [se connecter à distance à votre appareil StorSimple](storsimple-remote-connect.md).

Une fois que vous utilisez Windows PowerShell pour StorSimple pour vous connecter à l’appareil, vous avez besoin fournir le mot de passe administrateur appareil pour vous connecter à l’appareil.

![Mot de passe administrateur](./media/storsimple-security/DeviceAdminPW.png)

N’oubliez pas les meilleures pratiques suivantes :

- Gestion à distance est désactivée par défaut. Vous pouvez utiliser le service Manager StorSimple pour l’activer. Pour des raisons de sécurité, accès à distance doivent être activées uniquement pendant la période que vous en avez réellement besoin.
- Si vous changez le mot de passe, veillez à notifier tous les utilisateurs d’accès à distance afin qu’ils ne rencontrent pas une perte de connectivité inattendu.
- Le service Manager StorSimple ne peut pas récupérer des mots de passe existants : il peut réinitialiser uniquement les. Nous vous recommandons de stocker les mots de passe dans un emplacement sécurisé afin que vous n’êtes pas obligé de réinitialiser un mot de passe si elle est oublié. Si vous n’avez pas besoin de réinitialiser un mot de passe, veillez à notifier tous les utilisateurs avant de le réinitialiser. 

Vous pouvez accéder à l’interface Windows PowerShell en utilisant une connexion série à l’appareil. Vous pouvez également y accéder à distance à l’aide de HTTP ou HTTPS, qui fournit une sécurité supplémentaire. HTTPS fournit un niveau supérieur de sécurité à une série ou une connexion HTTP. Toutefois, pour utiliser HTTPS, vous devez tout d’abord installer un certificat sur l’ordinateur client qui accède à l’appareil. Vous pouvez télécharger le certificat d’accès à distance à partir de la page de configuration de périphérique dans le service Manager StorSimple. Si le certificat pour l’accès à distance est perdu, vous devez télécharger un nouveau certificat et propager à tous les clients qui sont autorisés à utiliser la gestion à distance.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Défi négociation CHAP (Authentication Protocol) et cibles les mots de passe

CHAP est un schéma d’authentification utilisé par le périphérique StorSimple pour valider l’identité des clients à distance. La vérification est basée sur un mot de passe partagé. CHAP peut être à sens unique (unidirectionnelle) ou commun (bidirectionnel). Avec CHAP à sens unique, la cible (le périphérique StorSimple) authentifie un initiateur (hôte). CHAP commun ou inversée nécessite que la cible authentifier l’initiateur et puis l’initiateur authentifie la cible. Votre StorSimple peut être configuré pour utiliser ces deux méthodes.

Gardez les éléments suivants lorsque vous configurez :

- Le nom d’utilisateur CHAP doit contenir moins de 233 caractères.
- Le mot de passe CHAP doit être comprise entre 12 et 16 caractères. Essayez d’utiliser un nom d’utilisateur plu ou mot de passe entraîne un échec d’authentification sur l’hôte de Windows.
- Vous ne pouvez pas utiliser le même mot de passe pour à la fois le CHAP et les cibles CHAP.
- Après avoir défini le mot de passe, il peut être modifié, mais il ne peut pas être récupéré. Si le mot de passe, veillez à notifier tous les utilisateurs d’accès à distance afin qu’ils peuvent se connecter à l’appareil StorSimple.

Pour plus d’informations sur CHAP et la configuration de votre solution StorSimple, accédez à [Configuration CHAP pour votre appareil StorSimple](storsimple-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Gestionnaire d’instantanés StorSimple le mot de passe

Gestionnaire d’instantanés StorSimple est un composant logiciel enfichable Microsoft Management Console (MMC) qui utilise des groupes de volumes et le Service Windows Volume ombre pour générer des sauvegardes cohérentes avec les applications. En outre, vous pouvez utiliser le Gestionnaire d’instantanés StorSimple créer cloner et des plannings de sauvegarde ou restaurer des volumes.

Lorsque vous configurez un périphérique à utiliser le Gestionnaire d’instantanés StorSimple, vous devrez fournir le mot de passe Gestionnaire d’instantanés StorSimple. Ce mot de passe est tout d’abord défini dans Windows PowerShell pour StorSimple lors de l’enregistrement. Le mot de passe peut également être définie et modifiée à partir du service Manager StorSimple. Ce mot de passe authentifie le périphérique avec le Gestionnaire de capture instantanée StorSimple.

![Gestionnaire d’instantanés StorSimple le mot de passe](./media/storsimple-security/SnapshotMgrPassword.png)

Le mot de passe Gestionnaire d’instantanés StorSimple doit être 14 et 15 caractères et doit contenir au moins 3 d’une combinaison de majuscules, minuscules, numériques et caractères spéciaux. Après avoir défini le mot de passe Gestionnaire d’instantanés StorSimple, il peut être modifié, mais il ne peut pas être récupéré. Si vous changez le mot de passe, veillez à notifier tous les utilisateurs à distance.

Pour plus d’informations à propos du gestionnaire instantané StorSimple, accédez à [Nouveautés Gestionnaire d’instantanés StorSimple ?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Meilleures pratiques de mot de passe

Nous vous recommandons d’utiliser les instructions suivantes pour vous assurer que les mots de passe StorSimple sont bien protégés et fort :

- Modifier votre mot de passe de tous les trois mois. Modifier le mot de passe est appliquée par an.
- Utilisez des mots de passe forts. Pour plus d’informations, accédez à [créer des mots de passe sûrs et les protéger](http://blogs.microsoft.com/cybertrust/2014/08/25/create-stronger-passwords-and-protect-them/).
- Toujours utiliser les mots de passe différents pour les mécanismes d’accès différents ; chacun des mots de passe que vous spécifiez doit être unique.
- Ne pas partager les mots de passe avec toute personne qui n’est pas autorisé à accéder au périphérique StorSimple.
- Vous ne parlez sur un mot de passe devant d’autres personnes ou indice sur le format d’un mot de passe.
- Si vous pensez qu’un compte ou un mot de passe a été déchiffré, signaler l’incident à votre service de sécurité informatique.
- Traiter tous les mots de passe comme des informations sensibles, confidentielles. 

## <a name="storsimple-data-protection"></a>Protection des données StorSimple

Cette section décrit les fonctionnalités de sécurité StorSimple protègent les données lors des transferts et des données stockées.

Comme décrit dans d’autres sections, les mots de passe sont utilisés pour autoriser et authentifier les utilisateurs avant qu’ils peuvent accéder à votre solution StorSimple. Un autre compte de sécurité est protection des données des utilisateurs non autorisés pendant que celui-ci est transféré entre systèmes de stockage et lorsqu’il est stocké. Les sections suivantes décrivent les fonctionnalités de protection des données fournies avec StorSimple.

> [AZURE.NOTE] Déduplication offre une protection supplémentaire pour les données stockées sur le périphérique StorSimple et dans le stockage de Microsoft Azure. Lorsque des données sont dédupliquées, les objets de données sont stockées séparément à partir des métadonnées utilisée pour mapper et y accéder : aucun contexte de niveau de stockage disponible pour reconstruire les données en fonction de la structure du volume, système de fichiers ou nom de fichier.

## <a name="protect-data-flowing-through-the-service"></a>Protéger vos données s’étalant via le service

L’objectif principal du service Manager StorSimple consiste à gérer et configurer le périphérique StorSimple. Le service StorSimple Manager s’exécute dans Microsoft Azure. Le portail classique Azure vous permet d’entrer des données de configuration de périphérique, puis Microsoft Azure utilise le service StorSimple Manager pour envoyer les données à l’appareil. StorSimple utilise un système de paires de clés asymétriques pour vous assurer qu’un compromis du service Azure n’entraînera pas compromettre des informations stockées. 

![Chiffrement des données de vol](./media/storsimple-security/DataEncryption.png)

Le système de clés asymétrique permet de protéger les données que le service se déroule comme suit :

1. Certificat de chiffrement de données qui utilise une clé privée et asymétrique paire est généré sur l’appareil et permet de protéger les données. Les clés sont générées lorsque le premier périphérique est enregistré. 
2. Les clés de certificat de chiffrement de données sont exportées vers un fichier d’échange d’informations personnelles (.pfx) qui est protégé par la clé de chiffrement de données de service, qui est une clé de 128 bits forte est générée aléatoirement par le premier appareil pendant l’enregistrement.
3. La clé publique du certificat est créée en toute sécurité au service Manager StorSimple et la clé privée reste avec le périphérique.
4. Entrer le service de données sont chiffrées à l’aide du public clé et déchiffré à l’aide de la clé privée stockée sur l’appareil, s’assurer que le service Azure ne peut pas déchiffrer les données s’étalant à l’appareil.

La clé de chiffrement des données de service est générée sur le premier appareil inscrit auprès du service. Tous les appareils suivants qui sont enregistrés avec le service doivent utiliser la même clé de chiffrement de données de service. 

> [AZURE.IMPORTANT] 
> 
> Il est très important effectuer une copie de la clé de chiffrement des données de service et enregistrez-le dans un emplacement sécurisé. Une copie de la clé de chiffrement des données de service doit être stockée de telle sorte qu’il est accessible par une personne autorisée et peut être facilement communiquée à l’administrateur de l’appareil.
>
> Si la clé de chiffrement des données de service est perdue, une personne du support technique Microsoft peut vous aider à le récupérer autant que vous avez au moins un périphérique dans un état en ligne. Nous vous conseillons de modifier la clé de chiffrement des données de service après que qu’elle est récupérée. Pour plus d’informations, accédez à [Modifier la clé de chiffrement des données de service](storsimple-service-dashboard.md#change-the-service-data-encryption-key).

Vous pouvez modifier la clé de chiffrement des données de service et le certificat de chiffrement de données correspondant en sélectionnant l’option **Modifier la clé de chiffrement de données de service** dans le tableau de bord de service. Pour vous assurer que la sécurité des données n’est pas compromise, vous devez utiliser un périphérique StorSimple physique pour modifier la clé de chiffrement de données de service. Modifier les clés de chiffrement requiert que tous les périphériques être mis à jour avec la nouvelle clé. Par conséquent, nous vous recommandons de modifier la clé lorsque tous les périphériques sont en ligne. Si les périphériques sont en mode hors connexion, leur clé peut être modifiées à un moment différent. Les appareils avec touches obsolètes toujours seront en mesure d’exécuter des sauvegardes, mais ils ne pourront pas restaurer les données jusqu'à ce que la clé est mis à jour. Pour plus d’informations, accédez à [utiliser le tableau de bord de service Manager StorSimple](storsimple-service-dashboard.md).

La clé de chiffrement des données de service et le certificat de chiffrement de données n’expirent pas. Toutefois, nous vous recommandons de modifier la clé de chiffrement de données de service par an pour permettre d’éviter les compromis clés.

## <a name="protect-data-at-rest"></a>Protéger vos données inactives

Le périphérique StorSimple gère les données en stockant au niveau local et dans le cloud, en fonction de la fréquence d’utilisation. Tous les ordinateurs hôtes qui sont connectés à l’appareil envoyer des données à l’appareil, qui déplace ensuite les données dans le cloud, le cas échéant. Données sont transférées à partir du périphérique dans le cloud en toute sécurité via Internet. Chaque périphérique possède une cible iSCSI qui couvre tous les volumes partagés sur votre appareil. Toutes les données sont chiffrées avant d’être envoyé vers le cloud stockage. 

![Clé de chiffrement de stockage cloud](./media/storsimple-security/CloudStorageEncryption.png)

Pour vous assurer la sécurité et l’intégrité des données déplacées vers le cloud, StorSimple vous permet de définir des clés de chiffrement de stockage cloud comme suit :

- Vous spécifiez la clé de chiffrement de stockage cloud lorsque vous créez un conteneur en volume. La clé ne peut pas être modifiée ou ajoutée par la suite. 
- Tous les volumes dans un conteneur de volume partagent la même clé de chiffrement. Si vous voulez une autre forme de chiffrement pour un volume spécifique, nous vous conseillons de créer un nouveau conteneur de volume pour héberger ce volume.
- Lorsque vous entrez la clé de chiffrement de stockage cloud dans le service Manager StorSimple, la clé est chiffrée à l’aide de la partie publique de la clé de chiffrement des données de service et ensuite envoyé à l’appareil.
- La clé de chiffrement de stockage cloud n’est pas stockée dans le service et est connue uniquement à l’appareil.
- Spécification d’une clé de chiffrement de stockage cloud est facultative. Vous pouvez envoyer des données qui ont été chiffrées sur l’hôte à l’appareil.

### <a name="additional-security-best-practices"></a>Meilleures pratiques de sécurité supplémentaires

- Fractionner le trafic : isoler votre SAN iSCSI du trafic utilisateur sur un réseau d’entreprise en déployant un réseau totalement séparé et à l’aide de réseaux virtuels où isolement physique n’est pas une option. Un réseau dédié pour le stockage iSCSI garantit la sécurité et les performances de vos données critiques. Mélange de stockage et utilisateur le trafic via un réseau d’entreprise n’est pas recommandé et peut augmenter la latence et entraîner des erreurs de réseau.

- Pour une sécurité côté hôte réseau, utiliser les interfaces réseau qui prennent en charge TCP/IP décharger moteur (pincement). PINCEMENT réduit charge du processeur en traitement TCP sur la carte réseau.

## <a name="protect-data-via-storage-accounts"></a>Protéger vos données via comptes de stockage

Chaque abonnement Microsoft Azure peut créer un ou plusieurs comptes de stockage. (Un compte de stockage fournit un espace de noms unique pour travailler avec les données stockées dans le cloud Azure). Accès à un compte de stockage est contrôlée par l’abonnement et clés d’accès associés à ce compte de stockage. 

Lorsque vous créez un compte de stockage, Microsoft Azure génère deux touches d’accès de stockage de 512 bits, d'entre eux est utilisé pour l’authentification lorsque l’appareil StorSimple accède à du compte de stockage. Notez qu’un seul de ces touches est en cours d’utilisation. L’autre clé est conservé en réserve, qui vous permet de faire pivoter les clés périodiquement. Pour faire pivoter les clés, vous activer la clé secondaire, puis supprimez la clé primaire. Vous pouvez ensuite créer une nouvelle clé pour utiliser au cours de la rotation suivante. (Pour des raisons de sécurité, nombreux centres de données nécessitent la rotation des clés.) 

Nous vous recommandons de que vous respectez ces pratiques recommandées pour la rotation des clés :

- Vous devez faire pivoter clés de compte de stockage régulièrement pour vous assurer que votre compte de stockage n’est pas accessible par les utilisateurs non autorisés.
- Votre administrateur Azure doit régulièrement, modifiez ou recréez la clé primaire ou secondaire pour accéder directement au compte de stockage à l’aide de la section de stockage du portail classique Azure.


## <a name="protect-data-via-encryption"></a>Protéger vos données via le chiffrement

StorSimple utilise les algorithmes de chiffrement suivants pour protéger des données stockées dans ou en déplacement entre les composants de votre solution StorSimple.

| Algorithme | Longueur de la clé | Protocoles/applications/commentaires |
| --------- | ---------- | ------------------------------- |
| RSA       | 2048       | Version 1.5 RSA PKCS 1 est utilisé par le portail classique Azure pour chiffrer les données de configuration sont envoyées à l’appareil : par exemple, stockage informations d’identification, StorSimple configuration du périphérique, du compte et les clés de chiffrement de stockage en nuage. |
| AES       | 256        | AES avec CBC est utilisé pour chiffrer la partie publique de la clé de chiffrement des données de service avant d’être envoyé au portail classique Azure à partir du périphérique StorSimple. Il est également utilisé par le périphérique StorSimple pour chiffrer les données avant que les données sont envoyées sur le compte de stockage cloud. |


## <a name="storsimple-virtual-device-security"></a>Sécurité de l’appareil virtuel StorSimple

[AZURE.INCLUDE [storsimple virtual device security](../../includes/storsimple-virtual-device-security.md)]

## <a name="frequently-asked-questions-faq"></a>Forum aux questions (FAQ)

Voici quelques questions et réponses sur la sécurité et Microsoft Azure StorSimple.

**Q:** Mon service est compromis. Quelle doit être mon étapes suivantes ?

**A:** Vous devez modifier immédiatement la clé de chiffrement des données de service et les touches de compte de stockage pour le compte de stockage qui est utilisé pour les données de hiérarchisation. Pour plus d’informations, consultez : 

- [Modifier la clé de chiffrement de données de service](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
- [Rotation des clés de comptes de stockage](storsimple-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**Q:** J’ai un nouvel appareil StorSimple demandant la clé d’enregistrement de service. Comment récupérer il ?

**A:** Cette clé a été créée lorsque vous avez créé tout d’abord le service Manager StorSimple. Lorsque vous utilisez le service Manager StorSimple pour vous connecter à l’appareil, vous pouvez utiliser la page de démarrage rapide de service pour afficher ou régénérer la clé d’inscription du service. Création d’un service d’inscription n’affecte pas les périphériques inscrits existants. Pour plus d’informations, consultez :

- [Afficher ou régénérer la clé d’inscription de service](storsimple-service-dashboard.md#view-or-regenerate-the-service-registration-key)

**Q:** J’ai perdu ma clé de chiffrement de données de service. Que dois-je faire ?

**A:** Contacter le support technique Microsoft. Se connecter à une session de prise en charge sur votre appareil et aide vous récupérez la clé (à condition qu’au moins un périphérique est en ligne). Immédiatement après avoir obtenu la clé de chiffrement des données de service, vous devez modifier celle-ci pour vous assurer que la nouvelle clé est constitue uniquement pour vous. Pour plus d’informations, consultez :

- [Modifier la clé de chiffrement de données de service](storsimple-service-dashboard.md#change-the-service-data-encryption-key)

**Q:**  J’ai autorisé un dispositif de changement de clé de chiffrement de données service, mais n’a pas démarré le processus de changement de clé. Que dois-je faire ?

**A:** Si le délai d’attente a expiré, vous devez autoriser à nouveau le périphérique pour que la modification clé de chiffrement de données de service et démarrer le processus à nouveau.

**Q:**  J’ai modifié la clé de chiffrement des données de service, mais je n’a pas pu mettre à jour les autres appareils dans les 4 heures. Dois-je me recommencer ?

**A:** La période de 4 heures s’applique uniquement aux origine de la modification. Une fois que vous démarrez le processus de mise à jour sur le périphérique StorSimple autorisé, l’autorisation est valide jusqu'à ce que tous les périphériques sont mis à jour.

**Q:** Notre administrateur StorSimple a quitté la société. Que dois-je faire ?

**A:** Modifier et réinitialiser les mots de passe permettant d’accéder à l’appareil StorSimple et modifier la clé de chiffrement des données de service pour vous assurer que les nouvelles informations ne sont pas connues à des personnes non autorisées. Pour plus d’informations, consultez :

- [Utiliser le service Manager StorSimple pour modifier votre mot de passe storsimple](storsimple-change-passwords.md)
- [Modifier la clé de chiffrement de données de service](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
- [Configurer pour votre appareil StorSimple](storsimple-configure-chap.md)

**Q:** Je veux fournir le mot de passe Gestionnaire d’instantanés StorSimple à un hôte qui se connecte à l’appareil StorSimple, mais le mot de passe n’est pas disponible. Que puis-je faire ?

**A:** Si vous avez oublié le mot de passe, vous devez créer un nouvel identifiant. Ensuite, n’oubliez pas d’informer tous les utilisateurs existants que le mot de passe a été modifié et qu’ils doivent mettre à jour leurs clients pour utiliser le nouveau mot de passe. Pour plus d’informations, consultez :

- [Modifier le mot de passe Gestionnaire d’instantanés StorSimple](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password)
- [Permettent d’authentifier un appareil](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**Q:** Le certificat pour l’accès à distance à Windows PowerShell pour StorSimple a été modifié sur l’appareil. Comment mettre à jour mes clients d’accès à distance ?

**A:** Vous pouvez télécharger le nouveau certificat à partir du service Manager StorSimple et lui soient installés dans le magasin de certificats de vos clients d’accès à distance. Pour plus d’informations, consultez :

- [Applet de commande certificat d’importation](https://technet.microsoft.com/library/hh848630.aspx)

**Q:** Est Mes données protégée le Gestionnaire de StorSimple service est compromis ?

**A:** Données de configuration du service sont toujours chiffrées avec votre clé publique lorsque vous l’affichez dans un navigateur web. Étant donné que le service n’a pas accès à la clé privée, le service ne sera pas en mesure de voir toutes les données. Si le service Manager StorSimple est compromis, il n’existe aucun impact, qu’il n’y a aucune clé stockées dans le service Manager StorSimple.

**Q:** Si une personne accède au certificat de chiffrement des données, seront mes données compromises ?

**A:** Microsoft Azure stocke la clé de chiffrement de données du client (fichier .pfx) dans un format chiffré. Étant donné que le fichier .pfx est chiffré et le service StorSimple n’a pas la clé de chiffrement des données de service à déchiffrer le fichier .pfx, il suffit de récupérer l’accès au fichier .pfx expose pas aucun secret.

**Q:** Que se passe-t-il si une entité gouvernementale vous invite à indiquer Microsoft mes données ?

**A:** Étant donné que toutes les données est chiffré sur le service et la clé privée est conservé avec le périphérique, l’entité gouvernementale doit demander le client pour les données. 

## <a name="next-steps"></a>Étapes suivantes

[Déployer votre appareil StorSimple](storsimple-deployment-walkthrough.md).
 
