Azure déterminera la version de Python à utiliser pour son environnement virtuel avec la priorité suivante :

1. version spécifiée dans runtime.txt dans le dossier racine
1. version spécifiée par le paramètre Python dans la configuration d’application web (les **paramètres** > carte de**Paramètres de l’Application** pour votre application web dans le portail Azure)
1. Python 2.7 est la valeur par défaut si aucune des réponses précédentes sont spécifiées

Valeurs valides pour le contenu de 

    \runtime.txt

sont les suivantes :

- Python 2.7
- Python 3.4

Si la version micro (troisième chiffre) est spécifié, elle est ignorée.
