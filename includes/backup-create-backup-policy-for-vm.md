## <a name="defining-a-backup-policy"></a>Définir une stratégie de sauvegarde

Une stratégie de sauvegarde définit une matrice des lorsque les données des instantanés, et combien de temps ces instantanés sont conservés. Lorsque vous définissez une stratégie pour sauvegarder une machine virtuelle, vous pouvez déclencher la lecture d’un travail de sauvegarde *une fois par jour*. Lorsque vous créez une nouvelle stratégie, elle est appliquée à l’archivage sécurisé. L’interface de stratégie de sauvegarde ressemble à ceci :

![Stratégie de sauvegarde](./media/backup-create-policy-for-vms/backup-policy.png)

Pour créer une stratégie :

1. Entrez un nom pour le **nom de la stratégie**.

2. Des instantanés de vos données peuvent être effectuées à intervalles quotidienne ou hebdomadaire. Le menu déroulant **Fréquence de sauvegarde** permet de choisir si données des instantanés quotidienne ou hebdomadaire.

    - Si vous choisissez un intervalle quotidien, utilisez le contrôle en surbrillance pour sélectionner l’heure du jour de la capture instantanée. Pour modifier l’heure, désélectionnez l’heure, puis sélectionnez la nouvelle heure.

    ![Stratégie de sauvegarde quotidien](./media/backup-create-policy-for-vms/backup-policy-daily.png) <br/>

    - Si vous choisissez un intervalle hebdomadaire, utilisez les contrôles mis en surbrillance pour sélectionner l’ou les jours de la semaine et l’heure du jour à prendre l’instantané. Dans le menu jour, sélectionnez un ou plusieurs jours. Dans le menu heure, sélectionnez une heure. Pour modifier l’heure, désélectionnez l’heure sélectionnée, puis sélectionnez la nouvelle heure.

    ![Stratégie de sauvegarde hebdomadaire](./media/backup-create-policy-for-vms/backup-policy-weekly.png)

3. Par défaut, toutes les options de **Durée de rétention** sont sélectionnées. Décochez une limite de plage de rétention que vous ne souhaitez pas utiliser. Ensuite, spécifiez l’interval(s) à utiliser.

    Mensuel ou annuel plages de rétention permettent de spécifier des instantanés basés sur un intervalle quotidien ou hebdomadaire.

    >[AZURE.NOTE] Lorsque vous protégez une machine virtuelle, une opération de sauvegarde s’exécute une fois par jour. L’heure d’exécution de la sauvegarde est identique pour chaque plage de rétention.

4. Après avoir défini toutes les options de la stratégie, en haut de la carte, cliquez sur **Enregistrer**.

    La nouvelle stratégie est appliquée immédiatement à l’archivage sécurisé.
