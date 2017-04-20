## <a name="download-and-understand-the-arm-template"></a>Télécharger et comprendre le modèle de processeur

Vous pouvez télécharger le modèle de processeur existant pour créer un VNet et des sous deux réseaux à partir de github, apportez les modifications peuvent souhaité, puis le réutiliser. Pour ce faire, suivez les étapes ci-dessous.

1. Accédez à [la page de modèle d’exemple](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Cliquez sur **azuredeploy.json**, puis cliquez sur **première**.
3. Enregistrer le fichier à un un dossier local sur votre ordinateur.
4. Si vous êtes habitué avec les modèles de processeur, passez à l’étape 7.
5. Ouvrez le fichier que vous venez d’enregistrer et consulter le contenu sous l’onglet **paramètres** dans la ligne 5. Paramètres de modèle processeur fournissent un espace réservé pour les valeurs qui peuvent être remplis pendant le déploiement.

    | Paramètre | Description |
    |---|---|
    | **emplacement** | Région Azure où la VNet est créé |
    | **vnetName** | Nom pour la nouvelle VNet |
    | **addressPrefix** | Espace d’adressage pour la VNet, au format CIDR |
    | **subnet1Name** | Nom pour la première VNet |
    | **subnet1Prefix** | Bloc CIDR pour le premier sous-réseau |
    | **subnet2Name** | Nom de la deuxième VNet |
    | **subnet2Prefix** | Bloc CIDR du deuxième sous-réseau |

    >[AZURE.IMPORTANT] Modèles de processeur conservées dans github peuvent changer dans le temps. Vérifiez que vous vérifiez le modèle avant de l’utiliser.
    
6. Vérifier le contenu sous **ressources** et observez les points suivants :

    - **type**. Type de ressource créé par le modèle. Dans ce cas, **Microsoft.Network/virtualNetworks**, qui représentent un VNet.
    - **nom**. Nom de la ressource. Notez l’utilisation de **[parameters('vnetName')]**, ce qui signifie que le nom est fournies comme entrée par l’utilisateur ou un fichier de paramètres pendant le déploiement.
    - **Propriétés**. Liste des propriétés de la ressource. Ce modèle utilise les propriétés d’espace et sous-réseau adresse lors de la création de VNet.

7. Accédez à [la page de modèle d’exemple](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Cliquez sur **azuredeploy paremeters.json**, puis cliquez sur **premières**.
9. Enregistrer le fichier à un un dossier local sur votre ordinateur.
10. Ouvrez le fichier que vous venez d’enregistrer et modifiez les valeurs pour les paramètres. Utiliser les valeurs ci-dessous pour déployer la VNet décrite dans notre scénario.

        {
          "location": {
            "value": "Central US"
          },
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }

11. Enregistrez le fichier.
  