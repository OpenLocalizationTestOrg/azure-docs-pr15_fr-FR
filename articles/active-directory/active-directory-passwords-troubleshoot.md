<properties
    pageTitle="Résolution des problèmes : La gestion de mot de passe Azure AD | Microsoft Azure"
    description="Étapes de dépannage courantes pour la gestion de mot de passe Azure AD, y compris les réinitialiser, modification, écriture différée, l’enregistrement, et les informations à inclure lorsque vous recherchez de l’aide."
    services="active-directory"
    documentationCenter=""
    authors="asteen"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="asteen"/>

# <a name="how-to-troubleshoot-password-management"></a>Comment résoudre les problèmes de gestion de mot de passe

> [AZURE.IMPORTANT] **Vous êtes ici parce que vous rencontrez des problèmes en vous connectant ?** Si c’est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).

Si vous rencontrez des problèmes avec la gestion de mot de passe, nous sommes là pour vous aider. La plupart des problèmes que vous pouvez rencontrer peuvent être résolus avec quelques étapes simples de dépannage qui vous pouvez en savoir plus sur ci-dessous pour résoudre les problèmes de votre déploiement :

* [**Informations à inclure lorsque vous avez besoin d’aide**](#information-to-include-when-you-need-help)
* [**Problèmes liés à la configuration de la gestion de mot de passe dans le portail de gestion Azure**](#troubleshoot-password-reset-configuration-in-the-azure-management-portal)
* [**Problèmes avec les rapports de gestion de mot de passe dans le portail de gestion Azure**](#troubleshoot-password-management-reports-in-the-azure-management-portal)
* [**Problèmes avec le mot de passe réinitialisé portail de l’enregistrement**](#troubleshoot-the-password-reset-registration-portal)
* [**Problèmes avec le mot de passe réinitialisé portail**](#troubleshoot-the-password-reset-portal)
* [**Problèmes de synchronisation d’écriture différée de mot de passe**](#troubleshoot-password-writeback)
  - [Codes d’erreur de mot de passe d’écriture différée journal des événements](#password-writeback-event-log-error-codes)
  - [Problèmes de connectivité d’écriture différée de mot de passe](#troubleshoot-password-writeback-connectivity)

Si vous avez essayé les étapes de dépannage ci-dessous et sont toujours en cours d’exécution des problèmes, vous pouvez publier une question sur les [Forums AD Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) ou contactez le support technique et nous allons résoudre votre problème dès que possible.

## <a name="information-to-include-when-you-need-help"></a>Informations à inclure lorsque vous avez besoin d’aide

Si vous ne pouvez pas résoudre votre problème avec les conseils ci-dessous, vous pouvez contacter nos ingénieurs de support. Lorsque vous contactez, il est recommandé d’inclure les informations suivantes :

 - **Description générale de l’erreur** – quel message d’erreur exact avez-vous l’utilisateur Voir ?  Si aucun message d’erreur s’est produite, décrivez le comportement inattendu remarqué, dans le détail.
 - **Page** – quelle page avez-vous sur lorsque vous l’avez vu l’erreur (inclure l’URL) ?
 - **Date / heure / fuseau horaire** – ce qui a été précis date et heure que vous avez vu l’erreur (inclure le fuseau horaire) ?
 - **Prend en charge le Code** – en ce qui a été le code de prise en charge généré lorsque l’utilisateur vu l’erreur (pour trouver cette, reproduire l’erreur, puis cliquez sur le lien Code prend en charge dans la partie inférieure de l’écran et envoyer le technicien GUID qui résulte).
   - Si vous êtes sur une page sans code prise en charge dans la partie inférieure, appuyez sur F12 et effectuez une recherche identificateur de sécurité et CID et envoyer ces deux résultats au technicien.

    ![][001]

 - **ID d’utilisateur** – quel a été l’ID de l’utilisateur qui unes (par exemple, l’erreuruser@contoso.com)?
 - **Informations sur l’utilisateur** – a été l’utilisateur fédéré, hachage de mot de passe synchronisé, cloud uniquement ?  L’utilisateur disposait d’une licence AAD Premium ou AAD base affectée ?
 - **Journal des événements applications** – si vous utilisez le mot de passe en écriture différée et l’erreur est dans votre infrastructure en local, veuillez zip une copie de votre journal des événements applications à partir de votre serveur Azure AD Connect et envoyer en même temps que votre requête.

Inclure les informations va nous aider à résoudre votre problème aussi rapidement que possible.


## <a name="troubleshoot-password-reset-configuration-in-the-azure-management-portal"></a>Résoudre les problèmes de configuration de réinitialisation de mot de passe dans le portail de gestion Azure
Si vous rencontrez une erreur lors de la configuration du mot de passe réinitialisé, vous pourrez peut-être résoudre ce problème en suivant les étapes de dépannage ci-dessous :

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Cas d’erreur</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Quel message d’erreur un utilisateur peut-il consulter ?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solution</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Je ne voyez pas la section <strong>Stratégie de réinitialiser votre mot de passe utilisateur </strong>sous l’onglet <strong>configurer</strong> dans le portail de gestion Azure</p>
            </td>
            <td>
              <p>La section <strong>Stratégie de réinitialiser votre mot de passe utilisateur </strong>n’est pas visible dans l’onglet <strong>configurer</strong> dans le portail de gestion Azure.</p>
            </td>
            <td>
              <p>Cela peut se produire si vous ne disposez pas d’une licence AAD Premium ou AAD base affectée à l’administrateur d’effectuer cette opération. </p>
              <p>Pour corriger cette erreur, attribuer une licence AAD Premium ou AAD base du compte d’administrateur en question en accédant à l’onglet <strong>licences</strong> et réessayez.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Je ne vois pas une des options de configuration dans la section <strong>Stratégie de réinitialiser votre mot de passe utilisateur</strong> qui sont décrites dans la documentation.</p>
            </td>
            <td>
              <p>La section <strong>Stratégie de réinitialiser votre mot de passe utilisateur </strong>est visible, mais le seul indicateur qui s’affiche sous celui-ci est l’indicateur <strong>Les utilisateurs activés pour réinitialiser votre mot de passe</strong> .</p>
            </td>
            <td>
              <p>Le reste de l’interface utilisateur s’affichent lorsque vous basculez l’indicateur pour <strong>Les utilisateurs activés pour réinitialiser votre mot de passe</strong> <strong>Oui.</strong></p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Je ne vois pas une option de configuration particulier.</p>
            </td>
            <td>
              <p>Par exemple, je ne vois pas l’option <strong>nombre de jours avant que l’utilisateur doit confirmer leurs données de contact</strong> lorsque j’ai faites défiler la section <strong>Stratégie de réinitialiser votre mot de passe utilisateur</strong> (ou d’autres exemples de la même émission).</p>
            </td>
            <td>
              <p>Nombre d’éléments de l’interface utilisateur est masqué jusqu'à ce qu’ils sont nécessaires. Essayez d’activer toutes les options de la page si vous voulez afficher.</p>
              <p>Pour plus d’informations sur tous les contrôles qui sont disponibles pour vous, consultez <a href="active-directory-passwords-customize.md#password-management-behavior">comportement de la gestion de mot de passe</a> .</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Je ne vois pas l’option de configuration <strong>Écrire revenir les mots de passe en local</strong></p>
            </td>
            <td>
              <p>L’option <strong>Écrire revenir les mots de passe locaux</strong> n’est pas visible sous l’onglet <strong>configurer</strong> dans le portail de gestion Azure</p>
            </td>
            <td>
              <p>Cette option est visible uniquement si vous avez téléchargé Azure AD Connect et configuré d’écriture différée de mot de passe. Lorsque vous l’avez fait, cette option s’affiche et vous permet d’activer ou désactiver l’écriture différée à partir du cloud.</p>
              <p>Pour plus d’informations sur la façon de procéder, voir <a href="active-directory-passwords-getting-started.md#step-2-enable-password-writeback-in-azure-ad-connect">Activer le mot de passe écriture différée dans Azure AD Connect</a> .</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-management-reports-in-the-azure-management-portal"></a>Résoudre les problèmes de rapports de gestion de mot de passe dans le portail de gestion Azure
Si vous rencontrez une erreur lorsque vous utilisez les rapports de gestion de mot de passe, vous pourrez peut-être résoudre ce problème en suivant les étapes de dépannage ci-dessous :

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Cas d’erreur</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Quel message d’erreur un utilisateur peut-il consulter ?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solution</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Je ne vois pas les rapports de gestion de mot de passe</p>
            </td>
            <td>
              <p>Les rapports <strong>d’activité de réinitialisation du mot de passe</strong> et <strong>l’activité d’enregistrement de réinitialisation du mot de passe</strong> ne sont pas visibles dans les rapports du <strong>Journal d’activité</strong> dans l’onglet <strong>rapports</strong> .</p>
            </td>
            <td>
              <p>Cela peut se produire si vous ne disposez pas d’une licence AAD Premium ou AAD base affectée à l’administrateur d’effectuer cette opération. </p>
              <p>Pour corriger cette erreur, attribuer une licence AAD Premium ou AAD base du compte d’administrateur en question en accédant à l’onglet <strong>licences</strong> et réessayez.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Les enregistrements utilisateur afficher plusieurs fois</p>
            </td>
            <td>
              <p>Lorsqu’un utilisateur enregistre messagerie de secours, téléphone mobile et les questions de sécurité, qu’ils apparaissent comme des lignes séparées au lieu d’une seule ligne.</p>
            </td>
            <td>
              <p>Pour l’instant, lorsqu’un utilisateur s’inscrit, nous ne pouvons pas part du principe que leur enregistre tous les éléments présents sur la page d’inscription. Par conséquent, nous allons nous connecter actuellement chaque élément de données sont enregistrées comme un événement distinct.</p>
              <p>Si vous voulez agréger ces données, vous pouvez télécharger le rapport et ouvrez les données comme un tableau croisé dynamique dans excel pour avoir plus de flexibilité.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Résoudre les problèmes le portail d’inscription de réinitialiser votre mot de passe
Si vous rencontrez une erreur lors de l’enregistrement d’un utilisateur pour réinitialiser votre mot de passe, vous pourrez peut-être résoudre ce problème en suivant les étapes de dépannage ci-dessous :

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Cas d’erreur</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Quel message d’erreur un utilisateur peut-il consulter ?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solution</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Répertoire n’est pas activée pour réinitialiser votre mot de passe</p>
            </td>
            <td>
              <p>Votre administrateur n’a pas activé vous permet d’utiliser cette fonctionnalité.</p>
            </td>
            <td>
              <p>Atteindre l’indicateur <strong>Les utilisateurs activés pour réinitialiser votre mot de passe</strong> <strong>Oui</strong> et appuyez sur <strong>Enregistrer</strong> sous l’onglet configuration de répertoire portail de gestion Azure. Vous devez disposer d’un Azure AD Premium ou base licence attribuée à l’administrateur d’effectuer cette opération.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Utilisateur ne dispose pas d’une licence AAD Premium ou AAD base affectée</p>
            </td>
            <td>
              <p>Votre administrateur n’a pas activé vous permet d’utiliser cette fonctionnalité.</p>
            </td>
            <td>
              <p>Attribuer une licence Azure AD Premium ou Azure AD base à l’utilisateur sous l’onglet <strong>licences</strong> dans le portail de gestion Azure. Vous devez disposer d’un Azure AD Premium ou base licence attribuée à l’administrateur d’effectuer cette opération.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erreur de traitement de la demande</p>
            </td>
            <td>
              <p>Utilisateur voit une erreur indiquant que :</p>
              <p>

              </p>
              <p>Erreur de traitement de la demande </p>
              <p>Lorsque vous essayez de réinitialiser un mot de passe.</p>
            </td>
            <td>
              <p>Cela peut être dû à de nombreux problèmes, mais généralement cette erreur est due à un service configuration ou une panne problèmes qui ne peuvent pas être résolus. </p>
              <p>Si vous voyez cette erreur et qu’il est ayant un impact sur votre entreprise, contactez le support technique et nous vous aidera DQP. Afficher les <a href="#information-to-include-when-you-need-help">informations à inclure lorsque vous avez besoin d’aide</a> pour voir ce que vous devez fournir au technicien de support pour vous aider à une résolution rapide.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-the-password-reset-portal"></a>Résoudre les problèmes du portail de réinitialiser votre mot de passe
Si vous rencontrez une erreur lors de la réinitialisation de mot de passe d’un utilisateur, vous pourrez peut-être résoudre ce problème en suivant les étapes de dépannage ci-dessous :

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Cas d’erreur</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Quel message d’erreur un utilisateur peut-il consulter ?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solution</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Répertoire n’est pas activée pour réinitialiser votre mot de passe</p>
            </td>
            <td>
              <p>Votre compte n’est pas activé pour réinitialiser votre mot de passe</p>
              <p>Nous sommes désolés, mais votre administrateur n’a pas configuré votre compte pour une utilisation avec ce service. </p>
              <p>

              </p>
              <p>Si vous le souhaitez, nous pouvons contacter un administrateur de votre organisation pour réinitialiser votre mot de passe pour vous.</p>
            </td>
            <td>
              <p>Atteindre l’indicateur <strong>Les utilisateurs activés pour réinitialiser votre mot de passe</strong> <strong>Oui</strong> et appuyez sur <strong>Enregistrer</strong> sous l’onglet configuration de répertoire portail de gestion Azure. Vous devez disposer d’un Azure AD Premium ou base licence attribuée à l’administrateur d’effectuer cette opération.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Utilisateur ne dispose pas d’une licence AAD Premium ou AAD base affectée</p>
            </td>
            <td>
              <p>Tandis que nous ne pouvons pas réinitialiser les mots de passe non-admin automatiquement, nous pouvons contacter l’administrateur de votre organisation pour le faire pour vous.</p>
            </td>
            <td>
              <p>Attribuer une licence Azure AD Premium ou Azure AD base à l’utilisateur sous l’onglet <strong>licences</strong> dans le portail de gestion Azure. Vous devez disposer d’un Azure AD Premium ou base licence attribuée à l’administrateur d’effectuer cette opération.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Annuaire est activée pour réinitialiser votre mot de passe, mais utilisateur possède des informations d’authentification manquant ou incorrect</p>
            </td>
            <td>
              <p>Votre compte n’est pas activé pour réinitialiser votre mot de passe</p>
              <p>Nous sommes désolés, mais votre administrateur n’a pas configuré votre compte pour une utilisation avec ce service. </p>
              <p>

              </p>
              <p>Si vous le souhaitez, nous pouvons contacter un administrateur de votre organisation pour réinitialiser votre mot de passe pour vous.</p>
            </td>
            <td>
              <p>Vérifiez que cet utilisateur a correcte des données de contact sur un fichier dans le répertoire avant de poursuivre. Pour plus d’informations sur la façon de configurer les informations d’authentification dans l’annuaire afin que les utilisateurs ne voient pas cette erreur, consultez <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">quelles données sont utilisées par la réinitialisation de mot de passe</a> .</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Annuaire est activée pour réinitialiser votre mot de passe, mais un utilisateur a uniquement une partie des données de contact sur un fichier lors de la stratégie est définie sur nécessite deux étapes de vérification</p>
            </td>
            <td>
              <p>Votre compte n’est pas activé pour réinitialiser votre mot de passe</p>
              <p>Nous sommes désolés, mais votre administrateur n’a pas configuré votre compte pour une utilisation avec ce service. </p>
              <p>

              </p>
              <p>Si vous le souhaitez, nous pouvons contacter un administrateur de votre organisation pour réinitialiser votre mot de passe pour vous.</p>
            </td>
            <td>
              <p>Avant de poursuivre, vérifiez qu’utilisateur dispose d’au moins deux méthodes contacts correctement configurées (par exemple, téléphone Mobile et téléphone de bureau). Pour plus d’informations sur la façon de configurer les informations d’authentification dans l’annuaire afin que les utilisateurs ne voient pas cette erreur, consultez <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">quelles données sont utilisées par la réinitialisation du mot de passe</a> .</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Annuaire est activée pour réinitialiser votre mot de passe et utilisateur est correctement configuré, mais utilisateur ne peut pas être contacté </p>
            </td>
            <td>
              <p>Rappeler un message  Nous avons rencontré une erreur inattendue lors de vous contacter.</p>
            </td>
            <td>
              <p>Cela peut être le résultat d’une erreur de service temporaire ou mal configuré les données de contact que nous pourrions détecte pas correctement. Si l’utilisateur attend 10 secondes, un réessayez et lien « contacter votre administrateur » s’affiche. Essayez de recliquer ré-distribue l’appel, alors que, cliquez sur « contacter votre administrateur » pour envoyer un message électronique de formulaire utilisateur, mot de passe ou les administrateurs généraux (dans cet ordre de priorité) demander un mot de passe réinitialisé à effectuer pour ce compte d’utilisateur.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Utilisateur reçoit jamais la réinitialisation de mot de passe SMS ou appel téléphonique</p>
            </td>
            <td>
              <p>Utilisateur clique sur « texte moi » ou « m’appeler », puis reçoit jamais rien.</p>
            </td>
            <td>
              <p>Ceci peut être le résultat d’un numéro de téléphone incorrect dans l’annuaire. Vérifiez que le numéro de téléphone est au format « + ccc xxxyyyzzzzXeeee ». Pour en savoir plus sur la mise en forme de téléphone numéros à utiliser avec de réinitialisation du mot de passe, reportez-vous à <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">quelles données sont utilisées par la réinitialisation de mot de passe</a>.</p>
              <p>Si vous avez besoin d’extension routés vers l’utilisateur en question, notez cette réinitialisation de mot de passe ne prend pas en charge extensions, même si vous spécifiez une dans le répertoire (ils sont supprimés avant l’appel est distribué). Essayez d’utiliser un nombre sans extension, ou intégrer l’extension dans le numéro de téléphone dans votre système PBX.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Utilisateur reçoit jamais un e-mail de réinitialiser votre mot de passe</p>
            </td>
            <td>
              <p>Utilisateur clique sur « Envoyer par courrier électronique moi », puis reçoit jamais rien.</p>
            </td>
            <td>
              <p>La cause la plus courante pour ce problème est que le message est rejeté par un filtre de courrier indésirable. Vérifiez votre courrier indésirable, le dossier éléments de courrier indésirable ou supprimés pour le message électronique.</p>
              <p>Assurez-vous également que vous voulez vérifier le bon message électronique pour le message... grand nombre de personnes ont beaucoup adresses de messagerie et mettre fin à la vérification de la boîte de réception appropriée pour le message. Si aucune de ces options ne fonctionne pas, il est également possible que l’adresse de messagerie dans l’annuaire est incorrect, cochez pour vérifier que l’adresse de messagerie est le bon et essayez à nouveau. Pour en savoir plus sur la mise en forme de messagerie adresses pour une utilisation avec réinitialiser votre mot de passe voir <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">quelles données sont utilisées par la réinitialisation de mot de passe</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>J’ai défini une stratégie de réinitialiser votre mot de passe, mais lorsqu’un compte d’administrateur utilise réinitialisation du mot de passe, cette stratégie n’est pas appliquée</p>
            </td>
            <td>
              <p>Comptes d’administrateur réinitialiser les mots de passe voir les mêmes options activées pour la réinitialisation du mot de passe, de messagerie et de téléphone mobile, quel que soit la stratégie est définie sous la section <strong>Stratégie de réinitialiser votre mot de passe utilisateur</strong> de l’onglet <strong>configurer</strong> .</p>
            </td>
            <td>
              <p>Les options configurées sous la section <strong>Stratégie de réinitialiser votre mot de passe utilisateur</strong> de l’onglet <strong>configurer</strong> s’applique uniquement aux utilisateurs finaux de votre organisation.</p>
              <p>Microsoft gère et contrôles le mot de passe d’administrateur réinitialiser stratégie afin de garantir le plus haut niveau de sécurité</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Utilisateur empêché d’essayer de mot de passe trop de fois par jour</p>
            </td>
            <td>
              <p>Utilisateur voit une erreur indiquant :</p>
              <p>

              </p>
              <p>Utilisez une autre option.</p>
              <p>Vous avez essayé de vérifier votre compte trop de fois dans la dernière 1 heure (s). Pour des raisons de sécurité, vous devrez patienter 24 heures avant que vous pouvez essayer à nouveau. </p>
              <p>Si vous le souhaitez, nous pouvons contacter un administrateur de votre organisation pour réinitialiser votre mot de passe pour vous.</p>
            </td>
            <td>
              <p>Nous implémenter un mécanisme de limitation automatique pour empêcher les utilisateurs d’essayer de réinitialiser les mots de passe trop souvent dans un laps de temps. Ce problème se produit lorsque :</p>
              <ol class="ordered">
                <li>
Utilisateur tente de valider un numéro de téléphone 5 fois en une heure.<br\><br\></li>
                <li>
Utilisateur tente d’utiliser le portail de questions de sécurité 5 fois en une heure.<br\><br\></li>
                <li>
Utilisateur tente de réinitialiser un mot de passe pour le compte d’utilisateur même 5 fois en une heure.<br\><br\></li>
              </ol>
              <p>Pour résoudre ce problème, demandez à l’utilisateur 24 heures après la dernière tentative et l’utilisateur sera puis réinitialiser son mot de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Utilisateur voit un message d’erreur lors de la validation de son numéro de téléphone</p>
            </td>
            <td>
              <p>Lorsque vous tentez de vérifier un téléphone à utiliser comme une méthode d’authentification, l’utilisateur voit une erreur indiquant :</p>
              <p>

              </p>
              <p>Numéro de téléphone incorrect spécifié.</p>
            </td>
            <td>
              <p>Cette erreur se produit lorsque le numéro de téléphone entré ne correspond pas le numéro de téléphone sur le fichier.</p>
              <p>Vérifiez que l’utilisateur entre le numéro de téléphone complet, y compris le code zone et le pays, lorsque vous tentez d’utiliser une méthode basée sur un téléphone pour réinitialiser votre mot de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erreur de traitement de la demande</p>
            </td>
            <td>
              <p>Utilisateur voit une erreur indiquant que :</p>
              <p>

              </p>
              <p>Erreur de traitement de la demande </p>
              <p>Lorsque vous essayez de réinitialiser un mot de passe.</p>
            </td>
            <td>
              <p>Cela peut être dû à de nombreux problèmes, mais généralement cette erreur est due à un service configuration ou une panne problèmes qui ne peuvent pas être résolus. </p>
              <p>Si vous voyez cette erreur et qu’il est ayant un impact sur votre entreprise, contactez le support technique et nous vous aidera DQP. Afficher les <a href="#information-to-include-when-you-need-help">informations à inclure lorsque vous avez besoin d’aide</a> pour voir ce que vous devez fournir au technicien de support pour vous aider à une résolution rapide.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-writeback"></a>Résoudre les problèmes de mot de passe d’écriture différée
Si vous rencontrez une erreur lors de l’activation, la désactivation ou à l’aide d’écriture différée de mot de passe, vous pourrez peut-être résoudre ce problème en suivant les étapes de dépannage ci-dessous :

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Cas d’erreur</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Quel message d’erreur un utilisateur peut-il consulter ?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solution</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Échecs de démarrage et d’intégration général</p>
            </td>
            <td>
              <p>Service de réinitialisation du mot de passe ne démarre pas en local avec l’erreur 6800 dans journal des événements de l’ordinateur Azure AD Connect.</p>
              <p>

              </p>
              <p>Après avoir d’intégration, fédérés ou hachage de mot de passe utilisateurs synchronisés ne peuvent pas réinitialiser les mots de passe.</p>
            </td>
            <td>
              <p>Lorsque l’écriture différée de mot de passe est activée, le moteur de synchronisation appelle la bibliothèque d’écriture différée pour réaliser la configuration (de intégration) en contactant le service d’intégration cloud. Les erreurs détectées au cours d’intégration ou lors du démarrage du point de terminaison WCF pour l’écriture différée de mot de passe provoquera des erreurs dans le journal des événements dans le journal des événements de votre ordinateur Azure AD Connect.</p>
              <p>Lors du redémarrage du service ADSync, si l’écriture différée a été configurée, le point de terminaison WCF sera démarré vers le haut. Toutefois, si le démarrage du point de terminaison échoue, nous simplement enregistrer l’événement 6800 et laisser le démarrage du service synchronisation. Présence de cet événement signifie que le point de terminaison n’a pas commencé jusqu'à écriture différée non validées mot de passe. Génèrent des détails de journal des événements pour cet événement (6800) ainsi que des entrées de journal des événements en PasswordResetService composant indiquera pourquoi le point de terminaison n’a pas pu démarrer vers le haut. Passez en revue ces erreurs de journal des événements et essayez de relancer la Azure AD Connect si différée non validées mot de passe ne fonctionne toujours pas. Si le problème persiste, essayez de désactiver et réactiver le mot de passe d’écriture différée.</p>
            </td>
          </tr>
                    <tr>
            <td>
              <p>Lorsqu’un utilisateur tente de réinitialiser un mot de passe ou déverrouiller un compte avec un mot de passe d’écriture différée activée, l’opération échoue. En outre, vous voyez un événement dans le journal des événements Azure AD Connect contenant : « moteur de synchronisation renvoyé une erreur hr = 800700CE, message = le nom de fichier ou extension est trop longue » après l’opération de déverrouillage se produit.
                            </p>
            </td>
            <td>
              <p>Cela peut se produire si vous aviez mis à niveau à partir de versions antérieures d’Azure AD Connect ou DirSync. La mise à niveau d’anciennes versions de Azure AD Connect définie un mot de passe 254 caractères du compte Azure AD Management Agent (versions plus récentes définirez un mot de passe de longueur de 127 caractère). Ces mots de passe longs fonctionnent pour les opérations de connecteur Active Directory importer et exporter, mais ils ne sont pas pris en charge par l’opération de déverrouillage.
                            </p>
            </td>
            <td>
              <p>[Trouver le compte Active Directory](active-directory-aadconnect-accounts-permissions.md#active-directory-account) pour Azure AD Connect et réinitialiser le mot de passe contenir pas plus de 127 caractères. Ouvrez **Le Service de synchronisation** à partir du menu Démarrer. Accédez aux **liens** et recherchez le **Connecteur Active Directory**. Sélectionnez-le, puis cliquez sur **Propriétés**. Accédez à la page **informations d’identification** et entrez le nouveau mot de passe. Cliquez sur **OK** pour fermer la page.
                            </p>
            </td>
          </tr>
                    <tr>
            <td>
              <p>Erreur de configuration d’écriture différée pendant l’installation Azure AD Connect.</p>
            </td>
            <td>
              <p>À la dernière étape du processus d’installation Azure AD Connect, vous voyez une erreur indiquant que votre mot de passe en écriture différée pas pu être configurée.</p>
              <p>

              </p>
              <p>Le journal des événements Application de connexion à Azure AD contient erreur 32009 avec le texte « Erreur prise auth jeton ».</p>
            </td>
            <td>
              <p>Cette erreur se produit dans les deux cas suivants :</p>
              <ul>
                <li class="unordered">
Vous avez spécifié un mot de passe pour le compte d’administrateur global spécifié au début du processus d’installation Azure AD Connect.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Vous avez tenté d’utiliser un utilisateur fédéré pour le compte d’administrateur global spécifié au début du processus d’installation Azure AD Connect.<br\><br\></li>
              </ul>
              <p>Pour corriger cette erreur, vérifiez que vous n’êtes pas à l’aide d’un compte fédéré pour l’administrateur global spécifié au début du processus d’installation Azure AD Connect, et que le mot de passe est correct.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erreur de configuration d’écriture différée pendant l’installation Azure AD Connect.</p>
            </td>
            <td>
              <p>Le journal des événements machine Azure AD Connect contient l’erreur 32002 levée par le PasswordResetService.</p>
              <p>

              </p>
              <p>Le message d’erreur : « erreur de connexion à ServiceBus, le fournisseur de jetons n’a pas pu fournir un jeton de sécurité... »</p>
              <p>

              </p>
            </td>
            <td>
              <p>La cause de cette erreur est que le service de réinitialisation du mot de passe en cours d’exécution dans votre environnement local n’est pas en mesure de vous connecter au point de terminaison service bus dans le cloud. Cette erreur est normalement généralement provoquée par une règle de pare-feu bloque une connexion sortante vers une adresse web ou de port particulière.</p>
              <p>

              </p>
              <p>Vérifiez que votre pare-feu autorise les connexions sortantes pour les éléments suivants :</p>
              <ul>
                <li class="unordered">
Tout le trafic via TCP 443 (HTTPS)<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Connexions sortantes à <br\><br\></li>
              </ul>
              <p>

              </p>
              <p>Une fois que vous avez mis à jour les règles suivantes, redémarrez l’ordinateur Azure AD Connect et mot de passe en écriture différée doit reprendre votre travail.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Mot de passe d’écriture différée point de terminaison local n’est pas accessible</p>
            </td>
            <td>
              <p>Après avoir travaillé pour certains heure, fédérés ou hachage de mot de passe utilisateurs synchronisés ne peuvent pas réinitialiser les mots de passe.</p>
            </td>
            <td>
              <p>Dans certains cas rares, le service de mot de passe d’écriture différée peut échouer à nouveau s’exécuter quand Azure AD Connect a commencé à nouveau. Dans ce cas, tout d’abord, vérifiez si votre mot de passe en écriture différée semble être montant activé Cela peut être effectuée à l’aide de l’Assistant de Azure AD Connect ou powershell (section HowTos voir ci-dessus). Si la fonctionnalité semble être activé, essayez d’activer ou de désactiver la fonctionnalité à nouveau via l’interface utilisateur ou PowerShell. Voir « étape 2 : activer l’écriture différée de mot de passe sur votre ordinateur de la synchronisation d’annuaires &amp; configurer des règles de pare-feu » à la section <a href="active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords">comment activer/désactiver le mot de passe d’écriture différée</a> pour plus d’informations sur la procédure à suivre.</p>
              <p>

              </p>
              <p>Si cela ne fonctionne pas, essayez de désinstaller complètement Azure AD Connect la réinstallation.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erreurs d’autorisations</p>
            </td>
            <td>
              <p>Fédérés ou mot de passe hachage synchronisés les utilisateurs essaient de réinitialiser leur voir les mots de passe d’un message d’erreur après avoir envoyé le mot de passe indiquant qu'un problème est survenu service.</p>
              <p>

              </p>
              <p>En outre, pendant les opérations de réinitialisation de mot de passe, que vous voyiez qu'un message d’erreur concernant l’agent de gestion a été refusée access dans votre sur les journaux d’événements locaux.</p>
            </td>
            <td>
              <p>Si vous voyez ces erreurs dans le journal des événements, vérifiez que le compte AD MA (qui a été spécifié dans l’Assistant au moment de la configuration) dispose des autorisations nécessaires pour l’écriture différée de mot de passe.</p>
              <p>

              </p>
              <p>Notez qu’une fois que cette autorisation est donnée cela peut prendre jusqu'à 1 heure pour les autorisations diffusée via la tâche d’arrière-plan sdprop sur le contrôleur de domaine. </p>
              <p>Pour le mot de passe pour travailler de manière, l’autorisation doit être apposée sur le descripteur de sécurité de l’objet utilisateur dont mot de passe est réinitialisé. Jusqu'à ce que cette autorisation s’affiche sur l’objet utilisateur, mot de passe réinitialisé continuent à échouer avec accès refusé.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erreur lors de la configuration d’écriture différée de mot de passe de l’Assistant de configuration Azure AD Connect </p>
            </td>
            <td>
              <p>Erreur « Impossible de localiser MA » dans l’Assistant lors de l’activation/désactivation d’écriture différée de mot de passe</p>
            </td>
            <td>
              <p>Il existe un problème connu dans la version finale d’Azure AD Connect qui se manifeste dans les situations suivantes :</p>
              <ol class="ordered">
                <li>
Vous configurez Azure AD Connect pour abc.com client (domaine vérifié) à l’aide de réf.. Cela se traduit par connecteur AAD avec nom « abc.com – AAD » en cours de création.<br\><br\></li>
                <li>
Puis, vous modifiez puis la Réf. DAS pour le connecteur (à l’aide de la synchronisation de l’ancien l’interface utilisateur) pour (Notez qu’il est le même client mais le nom de domaine différent). <br\><br\></li>
                <li>
À présent que vous essayez activer/désactiver l’écriture différée de mot de passe. L’Assistant créer le nom du connecteur à l’aide de la Réf., comme « abc.onmicrosoft.com – AAD » et passer à l’applet de commande d’écriture différée de mot de passe. Une erreur se produit, car il n’existe aucun connecteur créé avec ce nom.<br\><br\></li>
              </ol>
              <p>Ce problème a été corrigé dans notre versions les plus récentes. Si vous avez une version plus ancienne, l’une solution de contournement consiste à utiliser l’applet de commande powershell pour activer/désactiver la fonctionnalité. Voir « étape 2 : activer l’écriture différée de mot de passe sur votre ordinateur de la synchronisation d’annuaires &amp; configurer des règles de pare-feu » à la section <a href="active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords">comment activer/désactiver le mot de passe d’écriture différée</a> pour plus d’informations sur la procédure à suivre.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Impossible de réinitialiser votre mot de passe pour les utilisateurs dans les groupes spéciaux tels que Domain Admins / etc. des administrateurs de l’entreprise.</p>
            </td>
            <td>
              <p>Fédérés ou mot de passe hachage synchronisés les utilisateurs qui font partie de groupes protégés et essaient de réinitialiser leur voir les mots de passe d’un message d’erreur après avoir envoyé le mot de passe indiquant qu'un problème est survenu service.</p>
            </td>
            <td>
              <p>Les utilisateurs dotés de privilèges dans Active Directory sont protégés à l’aide de AdminSDHolder. Voir <a href="https://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx">http://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx</a> pour plus d’informations. </p>
              <p>

              </p>
              <p>Cela signifie que les descripteurs de sécurité sur ces objets sont vérifiées régulièrement pour correspondre à la AdminSDHolder spécifié dans un seul et sont réinitialisés s’ils sont différents. Par conséquent, les autorisations supplémentaires sont nécessaires pour l’écriture différée de mot de passe segmenter pas à ces utilisateurs. Cela peut entraîner d’écriture différée de mot de passe ne fonctionne ne pas pour ces utilisateurs. Par conséquent, nous ne sont pas compatibles gestion des mots de passe pour les utilisateurs au sein de ces groupes, car il s’interrompt le modèle de sécurité AD.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Réinitialiser opérations échoue avec objet n’a pas pu être trouvé</p>
            </td>
            <td>
              <p>Fédérés ou mot de passe hachage synchronisés les utilisateurs essaient de réinitialiser leur voir les mots de passe d’un message d’erreur après avoir envoyé le mot de passe indiquant qu'un problème est survenu service.</p>
              <p>

              </p>
              <p>En outre, pendant les opérations de réinitialisation de mot de passe, vous pouvez voir un message d’erreur dans vos journaux d’événements à partir du service Azure AD Connect indiquant qu’une erreur « Objet ne peut pas être trouvé ».</p>
            </td>
            <td>
              <p>Cette erreur indique généralement qu’il ne peut pas trouver l’objet utilisateur dans l’espace de connecteur AAD ou la MV liée ou objet espace de connecteur AD le moteur de synchronisation. </p>
              <p>

              </p>
              <p>Pour résoudre ce problème, vérifiez que l’utilisateur est bien synchronisé à partir de local à AAD via l’instance actuelle d’Azure AD Connect et vérifiez l’état des objets dans les espaces de connecteur et MV. Vérifiez que l’objet AD SC est le connecteur à l’objet MV via la règle « Microsoft.InfromADUserAccountEnabled.xxx ».</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Réinitialiser opérations échoue avec plusieurs correspondances trouvé erreur</p>
            </td>
            <td>
              <p>Fédérés ou mot de passe hachage synchronisés les utilisateurs essaient de réinitialiser leur voir les mots de passe à un message d’erreur après avoir envoyé le mot de passe indiquant qu'un problème est survenu service.</p>
              <p>

              </p>
              <p>En outre, pendant les opérations de réinitialisation de mot de passe, vous pouvez voir un message d’erreur dans vos journaux d’événements à partir du service Azure AD Connect indiquant une erreur « Maches plusieurs trouvés ».</p>
            </td>
            <td>
              <p>Cela indique que le moteur de synchronisation a détecté que l’objet MV est connecté à plusieurs objets AD SC via le « Microsoft.InfromADUserAccountEnabled.xxx ». Cela signifie que l’utilisateur a un compte activé dans plus d’une forêt. </p>
              <p>

              </p>
              <p>Ce scénario n’est actuellement pas pris en charge pour l’écriture différée de mot de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Opérations de mot de passe échouer avec une erreur de configuration.</p>
            </td>
            <td>
              <p>Opérations de mot de passe échouer avec une erreur de configuration. Le journal des événements contient Azure AD Connect erreur 6329 avec du texte : 0x8023061f (l’opération a échoué car la synchronisation de mot de passe n’est pas activée sur cet Agent de gestion).</p>
            </td>
            <td>
              <p>Ce problème survient si la configuration Azure AD Connect est modifiée pour ajouter&nbsp;une nouvelle forêt AD (ou à supprimer et rajouter une forêt existante) <strong>une fois</strong> la fonctionnalité d’écriture différée de mot de passe a déjà été activée. Opérations de mot de passe pour les utilisateurs de ces forêts nouvellement ajoutés échoue. Pour résoudre le problème, désactiver et réactiver la fonctionnalité d’écriture différée de mot de passe après ont effectué ces modifications forêt.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Réécriture des mots de passe qui ont été remis correctement par works utilisateurs, mais réécriture des mots de passe modifiées par un utilisateur et réinitialiser pour un utilisateur par un administrateur échoue.</p>
            </td>
            <td>
              <p>Lorsque vous essayez de réinitialiser un mot de passe au nom d’un utilisateur à partir du portail de gestion Azure, vous voyez un message indiquant : « le service de réinitialisation du mot de passe en cours d’exécution dans votre environnement local ne reconnaît pas les administrateurs de la réinitialisation des mots de passe utilisateur. Mettez à niveau vers la dernière version d’Azure AD Connect pour résoudre ce problème. »</p>
            </td>
            <td>
              <p>Cela se produit lorsque la version du moteur de synchronisation ne prend pas en charge l’opération d’écriture différée de mot de passe particulier qui a été utilisée. Les versions d’Azure AD Connect plus tard 1.0.0419.0911 prend en charge toutes les opérations de gestion de mot de passe, y compris votre mot de passe réinitialiser d’écriture différée d’écriture différée du changement de mot de passe et en écriture différée de réinitialiser votre mot de passe initialisé par l’administrateur à partir du portail de gestion Azure.&nbsp; Versions DirSync ultérieurement à 1.0.6862 prennent en charge écriture différée de réinitialiser votre mot de passe uniquement. Pour résoudre ce problème, nous vous recommandons vivement que vous installez la dernière version d’Azure AD Connect ou Azure Active Directory Connect (pour plus d’informations, voir <a href="active-directory-aadconnect">Outils d’intégration des annuaires</a>) pour résoudre ce problème et pour tirer le meilleur parti d’écriture différée de mot de passe de votre organisation.</p>
            </td>
          </tr>
        </tbody></table>


## <a name="password-writeback-event-log-error-codes"></a>Codes d’erreur de mot de passe d’écriture différée journal des événements
Une meilleure pratique lors de la résolution des problèmes avec votre mot de passe d’écriture différée consiste à inspecter ce journal des événements applications sur votre ordinateur Azure AD Connect. Ce journal des événements contiendra des événements à partir de deux sources utiles pour l’écriture différée de mot de passe. La source PasswordResetService décrira opérations et les problèmes liés à l’opération d’écriture différée de mot de passe. La source ADSync décrira opérations et les problèmes liés à définir les mots de passe de votre environnement Active Directory.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Code</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Nommez / du Message</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Source</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Description</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>6329</p>
            </td>
            <td>
              <p>CROCHET : MMS(4924) 0x80230619 – « une restriction empêche le mot de passe soit modifié à celle affichée spécifié. »</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>Cet événement se produit lorsque le service de mot de passe d’écriture différée tente de définir un mot de passe dans votre annuaire local ne répond pas à l’âge de mot de passe, l’historique, la complexité ou condition de filtrage du domaine.</p>
              <ul>
                <li class="unordered">
Si vous avez une durée de vie minimale et que vous avez récemment changé le mot de passe dans cette fenêtre de temps, vous ne pourrez pas modifier le mot de passe à nouveau jusqu'à ce qu’elle atteigne la durée de vie spécifiée dans votre domaine. Pour effectuer un test, âge minimal doit être défini sur 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Si vous avez des besoins de l’historique de mot de passe activés, puis vous devez sélectionner un mot de passe qui n’a pas été utilisé dans les dernières fois N, où N est le paramètre de l’historique de mot de passe. Si vous sélectionnez un mot de passe qui a été utilisée dans les dernières fois N, puis vous verrez une défaillance dans ce cas. Pour des fins de test, l’historique doit être défini à 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Si vous avez des exigences de complexité de mot de passe, tous les seront appliquée lorsque l’utilisateur essaie de modifier ou réinitialiser un mot de passe.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Si vous disposez de filtres de mot de passe activés et un utilisateur sélectionne un mot de passe ne répondent aux critères de filtrage, puis le réinitialiser ou modifier l’opération échouera.<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>RESSOURCES HUMAINES 8023042</p>
            </td>
            <td>
              <p>Moteur de synchronisation renvoyé une erreur hr = 80230402, message = une tentative pour obtenir un objet a échoué car il existe des doublons avec le même point d’ancrage</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>Cet événement se produit lorsque le même id utilisateur est activé dans plusieurs domaines.  Par exemple, si vous synchronisez forêts compte/ressource et avez le même id utilisateur présent et activé dans chaque, cette erreur peut se produire.  </p>
              <p>Cette erreur peut également se produire si vous utilisez un attribut d’ancrage non unique (par exemple, alias ou nom UPN) et deux utilisateurs partagent ce même attribut de point d’ancrage.</p>
              <p>Pour résoudre ce problème, vérifiez que vous ne disposez pas de tous les utilisateurs dupliquées dans vos domaines et que vous utilisez un attribut d’ancrage unique pour chaque utilisateur.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31001</p>
            </td>
            <td>
              <p>PasswordResetStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le service en local a détecté un mot de passe réinitialisé demande pour un fédérés ou mot de passe hachage synchronisés utilisateur d’origine à partir du cloud. Cet événement est que le premier événement dans chaque mot de passe réinitialisé opération d’écriture différée.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31002</p>
            </td>
            <td>
              <p>PasswordResetSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique qu’un utilisateur sélectionné un nouveau mot de passe lors d’une opération de réinitialisation du mot de passe, nous avons déterminé que ce mot de passe répond aux exigences de mot de passe d’entreprise, et ce mot de passe a été enregistrée précédent dans l’environnement Active Directory local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31003</p>
            </td>
            <td>
              <p>PasswordResetFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique qu’un utilisateur sélectionné un mot de passe et que votre mot de passe est arrivé correctement dans l’environnement local, mais lorsque nous avons essayé de définir le mot de passe dans l’environnement Active Directory local, une erreur est survenue. Cela peut se produire pour plusieurs raisons :</p>
              <ul>
                <li class="unordered">
Mot de passe de l’utilisateur ne pas répondre à l’âge, l’historique, la complexité ou filtrer la configuration requise pour le domaine. Essayez un tout nouveau mot de passe pour résoudre ce problème.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Le compte de service MA n’a pas les autorisations appropriées pour configurer le nouveau mot de passe sur le compte d’utilisateur en question.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Le compte d’utilisateur est dans un groupe protégé, tels que les administrateurs entreprise ou de domaine, ce qui n’autorise pas les opérations de jeu de mot de passe.<br\><br\></li>
              </ul>
              <p>Voir <a href="#troubleshoot-password-writeback">Résoudre les problèmes d’écriture différée mot de passe</a> sur en savoir plus sur les autres situtions peuvent provoquer cette erreur.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31004</p>
            </td>
            <td>
              <p>OnboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement se produit si vous activez le mot de passe d’écriture différée avec Azure AD Connect et indique que nous avons commencé d’intégration votre organisation au service web d’écriture différée de mot de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31005</p>
            </td>
            <td>
              <p>OnboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique le processus d’intégration a réussi et que les capacités d’écriture différée de mot de passe sont prête à utiliser.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31006</p>
            </td>
            <td>
              <p>ChangePasswordStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le service en local a détecté une demande de modification de mot de passe pour un fédéré ou mot de passe hachage synchronisés utilisateur d’origine à partir du cloud. Cet événement est le premier événement dans chaque opération d’écriture différée de modification du mot de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31007</p>
            </td>
            <td>
              <p>ChangePasswordSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique qu’un utilisateur sélectionné un nouveau mot de passe au cours d’une opération de modification du mot de passe, nous avons déterminé que ce mot de passe répond aux exigences de mot de passe d’entreprise, et ce mot de passe a été enregistrée précédent dans l’environnement Active Directory local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31008</p>
            </td>
            <td>
              <p>ChangePasswordFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique qu’un utilisateur sélectionné un mot de passe et que votre mot de passe est arrivé correctement dans l’environnement local, mais lorsque nous avons essayé de définir le mot de passe dans l’environnement Active Directory local, une erreur est survenue. Cela peut se produire pour plusieurs raisons :</p>
              <ul>
                <li class="unordered">
Mot de passe de l’utilisateur ne pas répondre à l’âge, l’historique, la complexité ou filtrer la configuration requise pour le domaine. Essayez un tout nouveau mot de passe pour résoudre ce problème.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Le compte de service MA n’a pas les autorisations appropriées pour configurer le nouveau mot de passe sur le compte d’utilisateur en question.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Le compte d’utilisateur est dans un groupe protégé, tels que les administrateurs entreprise ou de domaine, ce qui n’autorise pas les opérations de jeu de mot de passe.<br\><br\></li>
              </ul>
              <p>Voir <a href="#troubleshoot-password-writeback">Résoudre les problèmes d’écriture différée mot de passe</a> sur en savoir plus sur les autres situations peuvent provoquer cette erreur.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31009</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Le service local a détecté un mot de passe réinitialisé demande pour un fédéré ou mot de passe hachage synchronisés utilisateur provenant de l’administrateur au nom d’un utilisateur. Cet événement est le premier événement dans chaque opération d’écriture différée de réinitialiser votre mot de passe initialisé par l’administrateur.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31010</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>L’administrateur sélectionnée un nouveau mot de passe lors d’une opération de réinitialisation du mot de passe initialisé par l’administrateur, nous avons déterminé que ce mot de passe répond aux exigences de mot de passe d’entreprise, et ce mot de passe a été enregistrée précédent dans l’environnement Active Directory local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31011</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>L’administrateur sélectionné un mot de passe au nom d’un utilisateur et ce mot de passe est arrivé correctement dans l’environnement local, mais lorsque nous avons essayé de définir le mot de passe dans l’environnement Active Directory local, une erreur est survenue. Cela peut se produire pour plusieurs raisons :</p>
              <ul>
                <li class="unordered">
Mot de passe de l’utilisateur ne pas répondre à l’âge, l’historique, la complexité ou filtrer la configuration requise pour le domaine. Essayez un tout nouveau mot de passe pour résoudre ce problème.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Le compte de service MA n’a pas les autorisations appropriées pour configurer le nouveau mot de passe sur le compte d’utilisateur en question.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Le compte d’utilisateur est dans un groupe protégé, tels que les administrateurs entreprise ou de domaine, ce qui n’autorise pas les opérations de jeu de mot de passe.<br\><br\></li>
              </ul>
              <p>Voir <a href="#troubleshoot-password-writeback">Résoudre les problèmes d’écriture différée mot de passe</a> sur en savoir plus sur les autres situtions peuvent provoquer cette erreur.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31012</p>
            </td>
            <td>
              <p>OffboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement se produit si vous désactivez le mot de passe d’écriture différée avec Azure AD Connect et indique que nous avons commencé offboarding votre organisation au service web d’écriture différée de mot de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31013</p>
            </td>
            <td>
              <p>OffboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le processus offboarding a réussi et que les capacités d’écriture différée de mot de passe a été désactivée avec succès.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31014</p>
            </td>
            <td>
              <p>OffboardingEventFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique le processus offboarding n’a pas réussi. Cela peut être dû à une erreur d’autorisations sur le compte d’administrateur cloud ou en local spécifié lors de la configuration, ou parce que vous essayez d’utiliser un administrateur global cloud fédérés lors de la désactivation d’écriture différée de mot de passe. Pour résoudre ce problème, vérifiez votre administration autorisations et que vous n’utilisez pas un fédérés compte lors de la configuration de la fonctionnalité d’écriture différée de mot de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31015 date</p>
            </td>
            <td>
              <p>WriteBackServiceStarted </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le service de mot de passe d’écriture différée a démarré et est prêt à accepter des demandes de gestion de mot de passe à partir du cloud.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31016</p>
            </td>
            <td>
              <p>WriteBackServiceStopped </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le service d’écriture différée de mot de passe est arrêté et que les demandes de gestion de mot de passe à partir du cloud ne sera pas réussies.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31017</p>
            </td>
            <td>
              <p>AuthTokenSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que nous avons récupéré un jeton d’autorisation de l’administrateur global spécifié lors de l’installation Azure AD Connect pour démarrer le processus offboarding ou d’intégration.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31018</p>
            </td>
            <td>
              <p>KeyPairCreationSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que nous avons créé correctement la clé de chiffrement de mot de passe qui sera utilisée pour chiffrer les mots de passe à partir du cloud soient envoyées à votre environnement local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32000</p>
            </td>
            <td>
              <p>UnknownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique une erreur inconnue lors d’une opération de gestion de mot de passe. Examinez le texte d’exception dans l’événement pour plus d’informations. Si vous rencontrez des difficultés, essayez de désactiver et réactiver le mot de passe d’écriture différée. Si cela ne permet pas, inclure une copie de votre journal des événements en même temps que l’id de suivi spécifié initiés au votre technicien de support technique.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32001</p>
            </td>
            <td>
              <p>Constatez </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique il y a une erreur de connexion au mot de passe cloud réinitialiser service et se produit généralement lorsque le service local n’a pas pu se connecter au service web de réinitialisation de mot de passe. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32002</p>
            </td>
            <td>
              <p>ServiceBusError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique une erreur se connectant à service bus instance votre locataire s’est produite. Cela peut se produire car vous bloquez les connexions sortantes dans votre environnement local. Vérifiez votre pare-feu pour vous assurer que vous autorisez les connexions via TCP 443 et vers <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>, puis réessayez. Si vous rencontrez toujours des problèmes, essayez de désactiver et réactiver le mot de passe d’écriture différée.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32003</p>
            </td>
            <td>
              <p>InPutValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que l’entrée passée à notre API de service web n’est pas valide. Recommencez l’opération.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32004</p>
            </td>
            <td>
              <p>DecryptionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique qu’il y a une erreur déchiffrer le mot de passe est arrivé à partir du cloud. Il peut s’agir en raison d’une incompatibilité de clé déchiffrement entre le service cloud et votre environnement local. Pour résoudre ce problème, désactiver et réactiver votre mot de passe d’écriture différée dans votre environnement local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32005</p>
            </td>
            <td>
              <p>ConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Au cours d’intégration, nous enregistrer des informations spécifiques au client dans un fichier de configuration dans votre environnement local. Cet événement indique une erreur d’enregistrement de ce fichier est survenu ou que lorsque le service a été démarré il a été une erreur en train de lire le fichier. Pour résoudre ce problème, essayez la désactivation et la réactivation d’écriture différée de mot de passe pour forcer une réécriture de ce fichier de configuration. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32006</p>
            </td>
            <td>
              <p>EndPointConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DÉCONSEILLÉ – cet événement ne figure pas dans Azure AD Connect, uniquement très tôt crée de synchronisation d’annuaire pris en charge d’écriture différée.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32007</p>
            </td>
            <td>
              <p>OnBoardingConfigUpdateError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Au cours d’intégration, nous envoyer des données à partir du cloud pour le mot de passe local réinitialisation du service. Que les données sont ensuite écrites dans un fichier en mémoire avant d’être envoyées au service de synchronisation pour stocker ces informations en toute sécurité sur le disque. Cet événement indique un problème lié à écrire ou mise à jour des données en mémoire. Pour résoudre ce problème, essayez la désactivation et la réactivation d’écriture différée de mot de passe pour forcer une réécriture de cette configuration.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32008</p>
            </td>
            <td>
              <p>ValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que nous avons reçu une réponse non valide à partir du service web de réinitialiser votre mot de passe. Pour résoudre ce problème, essayez la désactivation et la réactivation d’écriture différée de mot de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32009</p>
            </td>
            <td>
              <p>AuthTokenError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que nous ne pourrions pas obtenir une autorisation jeton pour le compte d’administrateur global spécifié lors de l’installation Azure AD Connect. Cette erreur peut être provoquée par un nom d’utilisateur incorrecte ou mot de passe défini pour le compte d’administrateur général ou parce que le compte d’administrateur général spécifié est fédéré. Pour résoudre ce problème, exécutez à nouveau configuration avec le nom d’utilisateur correct et un mot de passe et assurez-vous que l’administrateur est géré (exclusivement le nuage, ou mot de passe synchronisés) compte.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32010</p>
            </td>
            <td>
              <p>CryptoError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique une erreur s’est produite lors de la génération le mot de passe clé de chiffrement ou déchiffrement un mot de passe reçus à partir du service cloud. Cette erreur probablement indique un problème avec votre environnement. Examinez les détails de votre journal des événements pour en savoir plus et résoudre ce problème. Vous pouvez également essayer désactiver et réactiver le service d’écriture différée de mot de passe pour résoudre ce problème.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32011</p>
            </td>
            <td>
              <p>OnBoardingServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le service local ne peut pas correctement communiquer avec du service web de réinitialisation de mot de passe pour lancer le processus d’intégration. Cela peut être en raison d’une règle de pare-feu ou d’un problème d’obtention d’un jeton d’authentification pour votre client. Pour résoudre ce problème, vérifiez que vous ne bloquent pas les connexions sortantes sur TCP 443 et TCP 9350 9354 ou <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>, et que le compte d’administrateur AAD vous utilisez pour intégré n’est pas fédéré. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32012</p>
            </td>
            <td>
              <p>OnBoardingServiceDisableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DÉCONSEILLÉ – cet événement ne figure pas dans Azure AD Connect, uniquement très tôt crée de synchronisation d’annuaire pris en charge d’écriture différée.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32013</p>
            </td>
            <td>
              <p>OffBoardingError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le service local ne peut pas correctement communiquer avec du service web de réinitialisation de mot de passe pour lancer le processus offboarding. Cela peut être en raison d’une règle de pare-feu ou d’un problème d’obtention d’un jeton d’autorisation pour votre client. Pour résoudre ce problème, vérifiez que vous ne bloquent pas les connexions sortantes sur 443 ou sur <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>, et que le compte d’administrateur AAD que vous utilisez pour hors cote n’est pas fédéré. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32014</p>
            </td>
            <td>
              <p>ServiceBusWarning </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que nous ont réessayer de se connecter à service bus instance votre locataire. Dans des conditions normales, cela ne doit pas être un problème, mais si vous voyez cet événement autant de fois, envisagez de vérification de votre connexion réseau au bus des services, surtout si elle est une latence élevée ou une connexion de faible bande passante.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32015</p>
            </td>
            <td>
              <p>ReportServiceHealthError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Afin de contrôler l’état de votre service d’écriture différée de mot de passe, nous envoyer les données pulsation à notre mot de passe réinitialisation du service web 5 minutes. Cet événement indique qu’une erreur s’est produite lors de l’envoi de ces informations d’intégrité au service web cloud. Ces informations d’intégrité n’incluent pas un OII ou les informations d’identification personnelle de données et sont purement une pulsation ainsi que les statistiques de base du service afin que nous puissions fournissent des informations d’état de service dans le cloud.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33001</p>
            </td>
            <td>
              <p>ADUnKnownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique qu’il y a une erreur inconnue renvoyée par Active Directory, consultez le journal des événements serveur Azure AD Connect des événements à partir de la source ADSync pour plus d’informations sur cette erreur.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33002</p>
            </td>
            <td>
              <p>ADUserNotFoundError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que l’utilisateur qui tente de réinitialiser ou modifier un mot de passe est introuvable dans le répertoire local. Cela peut se produire lorsque l’utilisateur a été supprimé localement, mais pas dans le cloud, ou s’il existe un problème de synchronisation. Vérifier les journaux de synchronisation, ainsi que la dernière synchronisation quelques détails pour plus d’informations de l’exécution.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33003</p>
            </td>
            <td>
              <p>ADMutliMatchError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Lorsqu’un mot de passe réinitialisé ou provient de demande de modification à partir du cloud, nous utilisons le point d’ancrage cloud spécifié pendant le processus de configuration de Azure AD Connect pour déterminer comment lier cette requête renvoie à un utilisateur dans votre environnement local. Cet événement indique que nous avons trouvé deux utilisateurs dans votre annuaire local avec le même attribut de point d’ancrage cloud. Vérifier les journaux de synchronisation, ainsi que la dernière synchronisation quelques détails pour plus d’informations de l’exécution.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33004</p>
            </td>
            <td>
              <p>ADPermissionsError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le compte de service Agent de gestion n’a pas les autorisations appropriées sur le compte en question pour définir un nouveau mot de passe. Assurez-vous que le compte MA forêt de l’utilisateur dispose des autorisations de mot de passe réinitialiser et de modification sur tous les objets de la forêt.  Pour plus d’informations sur la façon de faire à cet option, voir <a href="active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions">étape 4 : configurer les autorisations appropriées Active Directory</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33005</p>
            </td>
            <td>
              <p>ADUserAccountDisabled </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que nous tenté réinitialiser ou modifier un mot de passe pour un compte qui a été désactivé en local. Activer le compte et réessayez.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33006</p>
            </td>
            <td>
              <p>ADUserAccountLockedOut </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Événement indique que nous tenté réinitialiser ou modifier un mot de passe pour un compte qui a été verrouillé en local. Verrouillage peut se produire quand un utilisateur a essayé d’une modification ou la réinitialisation de mot de passe trop de fois sur une courte période. Déverrouiller le compte, puis recommencez l’opération.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33007</p>
            </td>
            <td>
              <p>ADUserIncorrectPassword </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que l’utilisateur spécifié un mot de passe actuel lors de l’exécution d’un mot de passe opération de modification. Spécifiez le mot de passe actuel, puis réessayez.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33008</p>
            </td>
            <td>
              <p>ADPasswordPolicyError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement se produit lorsque le service de mot de passe d’écriture différée tente de définir un mot de passe dans votre annuaire local ne répond pas à l’âge de mot de passe, l’historique, la complexité ou condition de filtrage du domaine.</p>
              <ul>
                <li class="unordered">
Si vous avez une durée de vie minimale et que vous avez récemment changé le mot de passe dans cette fenêtre de temps, vous ne pourrez pas modifier le mot de passe à nouveau jusqu'à ce qu’elle atteigne la durée de vie spécifiée dans votre domaine. Pour effectuer un test, âge minimal doit être défini sur 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Si vous avez des besoins de l’historique de mot de passe activés, puis vous devez sélectionner un mot de passe qui n’a pas été utilisé dans les dernières fois N, où N est le paramètre de l’historique de mot de passe. Si vous sélectionnez un mot de passe qui a été utilisée dans les dernières fois N, puis vous verrez une défaillance dans ce cas. Pour des fins de test, l’historique doit être défini à 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Si vous avez des exigences de complexité de mot de passe, tous les seront appliquée lorsque l’utilisateur essaie de modifier ou réinitialiser un mot de passe.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Si vous disposez de filtres de mot de passe activés et un utilisateur sélectionne un mot de passe ne répondent aux critères de filtrage, puis le réinitialiser ou modifier l’opération échouera.<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>33009</p>
            </td>
            <td>
              <p>ADConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique une écriture problème un mot de passe en retour vers votre annuaire local en raison d’un problème de configuration avec Active Directory s’est produite. Vérifier le journal des événements applications de l’ordinateur Azure AD Connect pour les messages à partir du service ADSync pour plus d’informations sur l’erreur qui s’est produite. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34001</p>
            </td>
            <td>
              <p>ADPasswordPolicyOrPermissionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DÉCONSEILLÉ – cet événement ne figure pas dans Azure AD Connect, uniquement très tôt crée de synchronisation d’annuaire pris en charge d’écriture différée.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34002</p>
            </td>
            <td>
              <p>ADNotReachableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DÉCONSEILLÉ – cet événement ne figure pas dans Azure AD Connect, uniquement très tôt crée de synchronisation d’annuaire pris en charge d’écriture différée.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34003</p>
            </td>
            <td>
              <p>ADInvalidAnchorError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DÉCONSEILLÉ – cet événement ne figure pas dans Azure AD Connect, uniquement très tôt crée de synchronisation d’annuaire pris en charge d’écriture différée.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-writeback-connectivity"></a>Résoudre les problèmes de connectivité d’écriture différée de mot de passe

Si vous rencontrez des interruptions de service avec le composant d’écriture différée de mot de passe d’Azure AD Connect, voici quelques actions rapides, que vous pouvez prendre pour résoudre ce problème :

 - [Redémarrer l’annonce Azure connecter Service de synchronisation](#restart-the-azure-AD-Connect-sync-service)
 - [Désactiver et réactiver la fonctionnalité d’écriture différée de mot de passe](#disable-and-re-enable-the-password-writeback-feature)
 - [Installer la dernière version d’Azure AD Connect](#install-the-latest-azure-ad-connect-release)
 - [Résoudre les problèmes de mot de passe d’écriture différée](#troubleshoot-password-writeback)

En règle générale, nous vous conseillons d’exécuter ces étapes dans l’ordre ci-dessus afin de récupérer votre service de la manière la plus rapide.

### <a name="restart-the-azure-ad-connect-sync-service"></a>Redémarrer l’annonce Azure connecter Service de synchronisation
Redémarrer le Service de synchronisation se connecter AD Azure peut vous aider à résoudre les problèmes de connectivité ou autres problèmes temporaires perturbent le service.

 1. En tant qu’administrateur, cliquez sur **Démarrer** sur le serveur exécutant **Azure AD Connect**.
 2. Tapez **« services.msc »** dans la zone de recherche et appuyez sur **entrée**.
 3. Recherchez l’entrée de **Microsoft Azure AD Connect** .
 4. Avec le bouton droit sur l’entrée du service, cliquez sur **redémarrer**et attendez que l’opération se termine.

    ![][002]

Ces étapes seront rétablir votre connexion avec le service cloud et résoudre toute interruption, que vous pouvez être confronté.  Si le redémarrage du Service de synchronisation ne résout pas votre problème, nous vous recommandons de que vous essayez de désactiver et réactiver la fonctionnalité de mot de passe d’écriture différée comme une étape suivante.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Désactiver et réactiver la fonctionnalité d’écriture différée de mot de passe
Désactiver et réactiver la fonctionnalité d’écriture différée de mot de passe peuvent vous aider à résoudre les problèmes de connectivité.

 1. En tant qu’administrateur, ouvrez l' **Assistant de configuration Azure AD Connect**.
 2. Dans la boîte de dialogue **se connecter à Active Directory Azure** , entrez vos **informations d’identification d’administrateur général Azure AD**
 3. Dans la boîte de dialogue **se connecter aux services AD DS** , entrez vos **informations d’identification d’administration de Services de domaine Active Directory**.
 4. Dans la boîte de dialogue **identifiant de vos utilisateurs** , cliquez sur le bouton **suivant** .
 5. Dans la boîte de dialogue **fonctionnalités facultatives** , décochez la case **différée mot de passe** .

    ![][003]

 6. Cliquez sur **suivant** dans les pages restantes de la boîte de dialogue sans rien modifier jusqu'à atteindre la page **prêt à configurer** .
 7. Vérifiez que la page Configurer indique le **mot de passe en écriture différée option comme désactivée** , puis sur le bouton **configurer** vert pour valider les modifications.
 8. Dans la boîte de dialogue **terminé** , désélectionnez l’option **Synchroniser maintenant** , puis cliquez sur **Terminer** pour fermer l’Assistant.
 9. Ouvrez de nouveau l' **Assistant de configuration Azure AD Connect**.
 10.    **Répétez les étapes 2 à 8**, sauf ce qui vous **activez l’option d’écriture différée de mot de passe** sur les **fonctionnalités facultatives** écran pour réactiver le service.

    ![][004]

Ces étapes seront rétablir votre connexion grâce à notre service cloud et résoudre toute interruption, que vous pouvez être confronté.

Si désactiver et réactiver la fonctionnalité d’écriture différée de mot de passe ne résout pas votre problème, nous vous recommandons de vous essayez de réinstaller Azure AD Connect comme une étape suivante.

### <a name="install-the-latest-azure-ad-connect-release"></a>Installer la dernière version d’Azure AD Connect
Réinstaller le package Azure AD Connect permet de résoudre les problèmes de configuration qui peuvent affecter votre capacité à des options vous connecter à nos services cloud ou pour gérer les mots de passe dans votre environnement Active Directory local.
Il est préférable d’effectuer cette étape uniquement après avoir tenté les deux premières étapes décrites ci-dessus.

 1. Téléchargez la dernière version d’Azure AD Connect [ici](active-directory-aadconnect.md#install-azure-ad-connect).
 2. Étant donné que vous avez déjà installé Azure AD Connect, vous avez uniquement besoin d’effectuer une mise à niveau sur place pour mettre à jour votre installation Azure AD Connect vers la dernière version.
 3. Exécutez le package téléchargé et suivez l’à l’écran instructions pour mettre à jour votre ordinateur Azure AD Connect.  Aucune étape manuelle supplémentaire est requise, sauf si vous avez personnalisé l’absence de règles de synchronisation de zone, dans ce cas, vous devez **sauvegarder ces fichiers avant de poursuivre la mise à niveau et redéployez manuellement les lorsque vous avez terminé**.

Ces étapes seront rétablir votre connexion grâce à notre service cloud et résoudre toute interruption, que vous pouvez être confronté.

Si l’installation de la dernière version du serveur Azure AD Connect ne résout pas votre problème, nous vous conseillons la désactivation et la réactivation d’écriture différée de mot de passe en tant que dernière étape après avoir installé la dernière synchronisation QFE.

Si qui ne résout pas votre problème, nous recommandons que vous consultez [Résoudre les problèmes d’écriture différée mot de passe](#troubleshoot-password-writeback) et le [mot de passe Azure AD gestion Forum aux questions](active-directory-passwords-faq.md) pour voir si votre problème peut être abordé il.


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>Des liens vers votre mot de passe réinitialisé documentation
Voici des liens vers toutes les pages de documentation de réinitialisation du mot de passe Azure AD :

* **Vous êtes ici parce que vous rencontrez des problèmes en vous connectant ?** Si c’est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [**Mode de fonctionnement**](active-directory-passwords-how-it-works.md) - en savoir plus sur les différents composants de service et de six chacun a
* [**Prise en main**](active-directory-passwords-getting-started.md) - obtenir des informations pour vous permettre aux utilisateurs de réinitialiser et modifier leur mot de passe cloud ou en local
* [**Personnaliser**](active-directory-passwords-customize.md) - Apprenez à personnaliser l’apparence et la convivialité et le comportement du service aux besoins de votre organisation
* [**Meilleures pratiques**](active-directory-passwords-best-practices.md) - Apprenez à déployer rapidement et efficacement gérer les mots de passe de votre organisation
* [**Obtenir des informations**](active-directory-passwords-get-insights.md) - en savoir plus sur notre fonctions intégrées de reporting
* [**Forum aux questions**](active-directory-passwords-faq.md) - obtenir des réponses aux questions fréquemment posées
* [**En savoir plus**](active-directory-passwords-learn-more.md) - accédez profondeur dans les détails techniques de fonctionnement du service



[001]: ./media/active-directory-passwords-troubleshoot/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-troubleshoot/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-troubleshoot/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-troubleshoot/004.jpg "Image_004.jpg"
