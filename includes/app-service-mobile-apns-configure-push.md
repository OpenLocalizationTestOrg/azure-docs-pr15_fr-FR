

1. Sur votre Mac, lancez **Trousseau d’accès**. Ouvrez **Mes certificats** sous **catégorie** dans la barre de navigationn gauche. Recherchez le certificat SSL que vous avez téléchargé dans la section précédente et indiquer son contenu. Sélectionnez uniquement le certificat (n’activez pas la clé privée) et de [l’Exporter](https://support.apple.com/kb/PH20122?locale=en_US).

2. Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Rechercher tout** > **Services application** > vos principaux de l’application Mobile. Sous **paramètres**, cliquez sur **Application Service Push**, puis cliquez sur votre nom de concentrateur de notification. Accédez à **Apple Push Notification Services** > **Télécharger le certificat**. Téléchargez le fichier .p12, en sélectionnant le **Mode** approprié (selon que votre client SSL certificat précédemment est Production ou Sandbox). Enregistrer les modifications.

Votre service est maintenant configuré pour fonctionner avec des notifications push sur iOS !

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
