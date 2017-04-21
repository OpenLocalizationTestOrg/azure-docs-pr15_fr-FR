
Le Gestionnaire de connexions hybride permet à votre ordinateur local pour se connecter à Azure et relais le trafic TCP. Vous devez installer le gestionnaire à un ordinateur local qui peut se connecter à la l’instance SQL Server.

1. La connexion que vous venez de créer doit avoir un **état** **sur premesis Configuration incomplète**. Cliquez sur cette connexion, puis cliquez sur **le programme d’installation locale**.

    ![Programme d’installation locale](./media/hybrid-connections-install-connection-manager/5-1.png)

2. Cliquez sur **installer et configurer**.

    Cette procédure installe une instance personnalisée du Gestionnaire de connexion, ce qui est déjà préconfiguré pour fonctionner avec la connexion hybride que vous venez de créer.

3. Complétez le reste des étapes d’installation pour le Gestionnaire de connexions.

    Une fois l’installation terminée, l’état de connexion hybride remplacera par **1 Instance connecté**. Vous devrez peut-être actualiser le navigateur et patientez quelques minutes. 

La configuration de la connexion hybride est terminée.