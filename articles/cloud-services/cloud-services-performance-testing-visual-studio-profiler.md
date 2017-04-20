<properties 
    pageTitle="Profil d’un Service Cloud localement dans l’émulateur cluster | Microsoft Azure" 
    services="cloud-services"
    description="Déterminer l’origine des problèmes de performances dans les services en nuage avec le Générateur de profils Visual Studio" 
    documentationCenter=""
    authors="TomArcher" 
    manager="douge" 
    editor=""
    tags="" 
    />

<tags 
    ms.service="cloud-services" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="07/30/2016" 
    ms.author="tarcher"/>

# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Tester les performances d’un Service Cloud localement dans l’émulateur cluster Azure à l’aide du Générateur de profils Visual Studio

Divers outils et techniques sont disponibles pour tester les performances des services cloud.
Lorsque vous publiez un service cloud sur Azure, vous pouvez installer Visual Studio collecter les données de profilage et analyser localement, comme décrit dans le [profil d’une Application Azure][1].
Vous pouvez également utiliser diagnostics pour effectuer le suivi d’une variété de compteurs de performance, comme décrit dans [les compteurs de performance à l’aide dans Azure][2].
Vous souhaiterez également profil de votre application localement dans l’émulateur cluster avant de déployer dans le cloud.

Cet article décrit la méthode d’échantillonnage de l’UC de profil utilisateur, qui peut être effectue localement de l’émulateur. Échantillonnage de l’UC est une méthode de profil qui n’est pas très apporter. Sur un intervalle d’échantillonnage désigné, le Générateur de profils prend un instantané de la pile d’appel. Les données sont collectées sur une période de temps et affichées dans un rapport. Cette méthode de profil généralement pour indiquer l’emplacement dans une application calculs la plupart du travail du processeur est effectuée.  Cela vous donne la possibilité de se concentrer sur le « chemin réactif » où votre application passe le plus de temps.



## <a name="1-configure-visual-studio-for-profiling"></a>1 : configurer Visual Studio pour profil

Tout d’abord, il existe plusieurs options de configuration de Visual Studio qui peuvent être utiles lorsque le profil. Pour représentatives des rapports profilage, vous devez symboles (fichiers .pdb) pour votre application, ainsi que des symboles pour les bibliothèques système. Vous souhaiterez pour vous assurer que vous référencez les serveurs de symboles disponibles. Pour ce faire, dans le menu **Outils** dans Visual Studio, choisissez **Options**, puis choisissez **débogage**, puis **symboles**. Assurez-vous que les serveurs de symboles Microsoft est répertorié sous **emplacement des fichiers (.pdb) symbole**.  Vous pouvez également référencer http://referencesource.microsoft.com/symbols, laquelle peut avoir des fichiers de symboles supplémentaires.

![Options de symbole][4]

Si vous le souhaitez, vous pouvez simplifier les rapports que le Générateur de profils génère en définissant uniquement mon Code. Avec uniquement mon Code est activé, piles d’appel de fonction sont simplifiées afin que les appels entièrement internes aux bibliothèques et .NET Framework sont masquées dans les rapports. Dans le menu **Outils** , sélectionnez **Options**. Développez le nœud **Outils de performances** , puis cliquez sur **Général**. Activez la case à cocher pour **Activer uniquement mon Code pour les rapports du Générateur de profils**.

![Options de mon Code uniquement][17]

Vous pouvez utiliser ces instructions avec un projet existant ou un nouveau projet.  Si vous créez un nouveau projet pour essayer les techniques décrites ci-dessous, choisissez un projet c# **Azure Cloud Service** , puis sélectionnez un **Rôle Web** et un **Rôle de travail**.

![Rôles de projet de Service Cloud Azure][5]

Par exemple à des fins, ajouter du code à votre projet qui prend beaucoup de temps et illustre quelques problème de performance évidentes. Par exemple, ajoutez le code suivant à un projet de rôle de collaborateur :

    public class Concatenator
    {
        public static string Concatenate(int number)
        {
            int count;
            string s = "";
            for (count = 0; count < number; count++)
            {
                s += "\n" + count.ToString();
            }
            return s;
        }
    }

Appelez ce code à partir de la méthode RunAsync dans classe dérivée de RoleEntryPoint du rôle Collaborateur. (Ignorer les avertissements concernant la méthode exécuter de façon synchrone).

        private async Task RunAsync(CancellationToken cancellationToken)
        {
            // TODO: Replace the following with your own logic.
            while (!cancellationToken.IsCancellationRequested)
            {
                Trace.TraceInformation("Working");
                Concatenator.Concatenate(10000);
            }
        }

Créer et exécuter votre service cloud localement sans débogage (Ctrl + F5), avec la configuration de solution à **Release**. Cela garantit que tous les fichiers et dossiers créés pour exécuter l’application localement et garantit que tous les émulateurs sont démarrés. Démarrez l’interface utilisateur émulateur de calcul à partir de la barre des tâches pour vérifier que votre rôle de travail est en cours d’exécution.

## <a name="2-attach-to-a-process"></a>2 : attacher à un processus

Au lieu de profil de l’application en commençant à partir de l’IDE Visual Studio 2010, vous devez associer le Générateur de profils à un processus en cours d’exécution. 

Pour joindre le Générateur de profils à un processus, dans le menu **analyser** , cliquez sur **Générateur de profils** et **Attacher/Détacher**.

![Joindre l’option de profil][6]

Pour un rôle de collaborateur, recherchez le processus WaWorkerHost.exe.

![Processus WaWorkerHost][7]

Si votre dossier de projet se trouve sur un lecteur réseau, le Générateur de profils vous demandera à fournir un autre emplacement pour enregistrer les rapports profilage.

 Vous pouvez également joindre à un rôle web en attachant à WaIISHost.exe.
S’il existe plusieurs processus de rôle de travail dans votre application, vous devez utiliser la valeur processID pour distinguer. Vous pouvez interroger la valeur processID par programme en accédant à l’objet de processus. Par exemple, si vous ajoutez ce code à la méthode d’exécution de la classe dérivée de RoleEntryPoint dans un rôle, vous pouvez examiner le journal dans l’interface utilisateur émulateur calculer savoir quels processus pour vous connecter à.

    var process = System.Diagnostics.Process.GetCurrentProcess();
    var message = String.Format("Process ID: {0}", process.Id);
    Trace.WriteLine(message, "Information");

Pour afficher le journal, démarrez l’interface utilisateur émulateur de calcul.

![Démarrer l’émulateur cluster l’interface utilisateur][8]

Ouvrez la fenêtre de console de journal rôle Collaborateur dans l’interface utilisateur émulateur de calcul en cliquant sur la barre de titre de la fenêtre de la console. Vous pouvez afficher l’ID du processus dans le journal.

![Afficher l’ID processus][9]

Une que vous avez joint, effectuez les étapes dans l’interface utilisateur de votre application (si nécessaire) pour reproduire le scénario.

Lorsque vous souhaitez arrêter de profil, cliquez sur le lien **Arrêter de profil** .

![Arrêter l’option de profil][10]

## <a name="3-view-performance-reports"></a>3 : afficher des rapports de performances

Le rapport de performance pour votre application s’affiche.

À ce stade, le Générateur de profils s’interrompt, enregistre les données dans un fichier .vsp et affiche un état qui affiche une analyse de ces données.

![Rapport du Générateur de profils][11]


Si vous voyez String.wstrcpy dans le chemin d’accès rapide, cliquez sur uniquement mon Code pour modifier l’affichage pour afficher uniquement du code utilisateur.  Si vous voyez String.Concat, essayez d’appuyer sur le bouton Afficher tout le Code.

Vous devriez voir la méthode CONCATENER et String.Concat occuper une grande partie de la durée d’exécution.

![Analyse de rapport][12]

Si vous avez ajouté le code de concaténation de chaîne dans cet article, vous devez savoir un avertissement s’affiche dans la liste des tâches pendant ce. Vous pouvez également voir un message d’avertissement qu’il existe une quantité excessive de nettoyage, qui est le nombre de chaînes qui sont créés et supprimés.

![Avertissements liés aux performances][14]

## <a name="4-make-changes-and-compare-performance"></a>4 : apporter des modifications et comparer les performances

Vous pouvez également comparer les performances avant et après un changement de code.  Arrêter le processus en cours d’exécution et modifier le code pour remplacer l’opération de concaténation de chaînes à l’aide de StringBuilder :

    public static string Concatenate(int number)
    {
        int count;
        System.Text.StringBuilder builder = new System.Text.StringBuilder("");
        for (count = 0; count < number; count++)
        {
             builder.Append("\n" + count.ToString());
        }
        return builder.ToString();
    }

Effectuer une autre performances, puis comparez les performances. Dans l’Explorateur de performances, si les séries se trouvent dans la même session, vous pouvez simplement sélectionner les deux rapports, ouvrez le menu contextuel, puis sélectionnez **Comparer les rapports de performances**. Si vous souhaitez comparer avec une exécution dans une autre session de performance, ouvrez le menu **analyser** , puis sélectionnez **Comparer les rapports de performances**. Spécifier les deux fichiers dans la boîte de dialogue qui s’affiche.

![Comparer option des rapports de performances][15]

Les rapports soulignent les différences entre les deux séries.

![Rapport de comparaison][16]

Félicitations ! Vous avez obtenu disponibles en main le Générateur de profils.

## <a name="troubleshooting"></a>Résolution des problèmes

- Vérifiez que vous soyez profil une version Release et démarrez sans débogage.

- Si l’option Attacher/Détacher n’est pas activée dans le menu du Générateur de profils, exécutez l’Assistant de Performance.

- Utiliser l’interface utilisateur d’émulateur calculer pour afficher l’état de votre application. 

- Si vous rencontrez des problèmes pour démarrer des applications dans l’émulateur, ou en joignant le Générateur de profils, arrêté vers le bas de l’émulateur cluster et redémarrez-le. Si cela ne résout pas le problème, essayez de redémarrer. Ce problème peut se produire si vous utilisez l’émulateur de calcul pour suspendre et supprimer des déploiements en cours d’exécution.

- Si vous avez utilisé une des commandes profilage à partir de la ligne de commande, notamment les paramètres globaux, assurez-vous que VSPerfClrEnv /globaloff a été appelée et que VsPerfMon.exe a été arrêté.

- Si lors de l’échantillonnage, vous voyez le message « PRF0025 : aucune donnée ont été recueillie, « vérifiez que le processus vous attaché à dispose d’activité de l’UC. Les applications qui ne font pas votre travail calcul peut ne pas produisent des données d’échantillonnage.  Il est également possible que le processus s’est terminé avant que tout échantillonnage effectué. Vérifiez que la méthode Run pour un rôle qui vous soyez profil ne se termine pas.

## <a name="next-steps"></a>Étapes suivantes

L’instrumentation Azure binaires dans l’émulateur n’est pas pris en charge dans le Générateur de profils Visual Studio, mais si vous souhaitez tester l’allocation de mémoire, vous pouvez choisir cette option lorsque le profil. Vous pouvez également choisir accès concurrentiels au profil utilisateur, qui vous permet de déterminer si les threads sont perdre du temps en patinage pour verrous ou couche interaction profil, qui vous permet d’effectuer le suivi des problèmes de performances lors de l’interaction entre les différents niveaux d’une application, plus fréquemment entre la couche de données et un rôle de collaborateur.  Vous pouvez afficher les requêtes de base de données génère votre application et utiliser les données de profilage pour améliorer votre utilisation de la base de données. Pour plus d’informations sur la couche interaction profil, consultez le blog de publication [procédure pas à pas : à l’aide du Générateur de profils Interaction couche dans Visual Studio Team System 2010][3].



[1]: http://msdn.microsoft.com/library/azure/hh369930.aspx
[2]: http://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: http://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png
 