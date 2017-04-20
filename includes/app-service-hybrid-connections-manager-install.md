
1. Dans la carte de **connexions hybride** , cliquez sur la connexion hybride que vous venez de créer, puis cliquez sur **Le programme d’installation récepteur**.
    
    ![Cliquez sur le programme d’installation récepteur](./media/app-service-hybrid-connections-manager-install/D04ClickListenerSetup.png)
    
4. La carte de **Propriétés de connexion hybride** s’ouvre. Sous **Gestionnaire de connexions hybrides en local**, cliquez sur **Téléchargez et configurez manuellement**, enregistrer le package HybridConnectionManager.msi téléchargé et copiez la chaîne de connexion passerelle.
    
    ![Cliquez ici pour installer](./media/app-service-hybrid-connections-manager-install/D05ClickToInstallHCM.png)
    
5. À partir d’une invite de commandes administrateur, tapez la commande suivante pour démarrer le programme d’installation :

        start HybridConnectionManager.msi
 
7. Une fois que le programme d’installation s’exécute, cliquez sur **pas maintenant**, puis accédez au dossier %ProgramFiles%\Microsoft\HybridConnectionManager, exécuter HCMConfigWizard.exe et cliquez sur **Oui** dans la boîte de dialogue **Contrôle de compte d’utilisateur** .
        
7. Collez la chaîne de connexion hybride que vous avez copiée précédemment, puis cliquez sur **OK**. 
    
    ![L’installation](./media/app-service-hybrid-connections-manager-install/D08aHCMInstallManual.png)
    
8. Une fois l’installation terminée, cliquez sur **Fermer**.
    
    ![Cliquez sur Fermer](./media/app-service-hybrid-connections-manager-install/D09HCMInstallComplete.png)
    
    Sur la carte de **connexions hybride** , la colonne **statut** indique maintenant **connecté**. 
    
    ![État connecté](./media/app-service-hybrid-connections-manager-install/D10HCStatusConnected.png)