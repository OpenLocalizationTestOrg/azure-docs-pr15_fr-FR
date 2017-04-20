Le script de déploiement ignore la création de l’environnement virtuel sur Azure s’il détecte qu’il existe déjà un environnement virtuel compatible.  Cela peut considérablement accélérer le déploiement.  Packages qui sont déjà installés seront ignorés par pip.

Dans certains cas, vous pouvez souhaiter forcer supprimer cet environnement virtuel.  Vous souhaiterez effectuer cette opération si vous décidez d’inclure un environnement virtuel dans le cadre de votre référentiel.  Vous pouvez également procéder ainsi si vous devez supprimer certains packages ou tester les modifications requirements.txt.

Il existe plusieurs options pour gérer l’environnement virtuel existant sur Azure :

### <a name="option-1-use-ftp"></a>Option 1 : Utilisez FTP

Avec un client FTP, la connexion au serveur et vous pouvez supprimer le dossier environnement.  Notez que certains clients FTP (par exemple, les navigateurs web) peuvent être en lecture seule et ne vous permettent pas de supprimer des dossiers, afin que vous souhaiterez Vérifiez que vous utilisez un client FTP avec cette fonctionnalité.  Le nom d’hôte FTP et l’utilisateur sont affichent dans la carte de votre application web sur le [Portail Azure](https://portal.azure.com).

### <a name="option-2-toggle-runtime"></a>Option 2 : Activer/désactiver runtime

Voici un texte de remplacement qui tire parti du fait que le script de déploiement Supprimer le dossier environnement lorsqu’il ne correspondent pas à la version souhaitée de Python.  Cette opération efficacement supprime l’environnement existant et créer un nouveau.

1. Basculer vers une autre version de Python (via runtime.txt ou la carte de **Paramètres de l’Application** dans le portail Azure)
1. GIT transmission certaines modifications (ignorer toutes les erreurs installer pip le cas échéant)
1. Revenir à la version initiale de Python
1. GIT transmission certaines modifications à nouveau

### <a name="option-3-customize-deployment-script"></a>Option 3 : Personnaliser le script de déploiement

Si vous avez personnalisé le script de déploiement, vous pouvez modifier le code dans deploy.cmd forcer pour supprimer le dossier environnement.
