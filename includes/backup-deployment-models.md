Le service de sauvegarde Azure comporte deux types de chambres fortes - l’archivage sécurisé de sauvegarde et l’archivage sécurisé aux Services de récupération. L’archivage sécurisé sauvegarde reçue en premier. Puis l’archivage sécurisé aux Services de récupération arrivée pour prendre en charge les déploiements Gestionnaire de ressources développée. Microsoft recommande l’utilisation des déploiements Gestionnaire de ressources, sauf si vous avez spécifiquement besoin d’un déploiement classique.

| **Déploiement** | **Portail** | **Archivage sécurisé** |
|-----------|------|-----|
|Classique|[Classique](https://manage.windowsazure.com)|Sauvegarde|
|Gestionnaire de ressources|[Azure](https://portal.azure.com)|Services de récupération|

> [AZURE.NOTE] Chambres fortes sauvegarde ne peut pas protéger solutions déployé le Gestionnaire de ressources. Toutefois, vous pouvez utiliser un archivage sécurisé aux Services de récupération pour protéger les serveurs variation déployée et machines virtuelles.  
