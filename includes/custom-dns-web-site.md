#<a name="configuring-a-custom-domain-name-for-an-azure-website"></a>Configuration d’un nom de domaine personnalisé pour un site Web Azure

Lorsque vous créez un site Web, Azure fournit un sous-domaine convivial sur le domaine azurewebsites.net pour que vos utilisateurs puissent accéder à votre site Web à l’aide d’une URL, par http://&lt;monsite >. azurewebsites.net. Toutefois, si vous configurez votre site Web pour partagé ou en mode Standard, vous pouvez mapper votre site Web à votre propre nom de domaine.

Si vous le souhaitez, vous pouvez utiliser Azure le trafic gestionnaire pour charger le trafic entrant solde vers votre site Web. Pour plus d’informations sur le fonctionnement du Gestionnaire de trafic avec des sites Web, voir [Contrôler Azure Sites Web le trafic avec le Gestionnaire de trafic Azure][trafficmanager].

> [AZURE.NOTE] Les procédures dans cette tâche s’appliquent aux sites Web Azure ; pour les Services en nuage, voir <a href="/develop/net/common-tasks/custom-dns/">configuration d’un nom de domaine personnalisé dans Azure</a>.

> [AZURE.NOTE] Les étapes de cette tâche requièrent vous permettent de configurer votre site Web pour partagé ou en mode Standard, ce qui peut changer combien vous êtes facturé pour votre abonnement. Pour plus d’informations, voir <a href="/pricing/details/web-sites/">Détails tarifs de sites Web</a> .

Dans cet article :

-   [Présentation des enregistrements CNAME et A](#understanding-records)
-   [Configurer vos sites web en mode standard ou partagées](#bkmk_configsharedmode)
-   [Ajoutez vos sites web vers le Gestionnaire de trafic](#trafficmanager)
-   [Ajouter un enregistrement CNAME pour votre domaine personnalisé](#bkmk_configurecname)
-   [Ajouter un enregistrement A pour votre domaine personnalisé](#bkmk_configurearecord)

<h2><a name="understanding-records"></a>Comprendre les enregistrements CNAME et A</h2>

CNAME (ou alias enregistrement) et un enregistrements autoriser vous pouvez associer un nom de domaine à un site Web, mais ils fonctionnent différemment.

###<a name="cname-or-alias-record"></a>Enregistrement CNAME ou Alias

Un enregistrement CNAME mappe un domaine *spécifique* , tel que **contoso.com** ou **www.contoso.com**, à un nom de domaine complet. Dans ce cas, le nom de domaine canonique est l’une la ** &lt;myapp >. azurewebsites.net** nom de domaine de votre site Web Azure ou les ** &lt;myapp >. trafficmgr.com** nom de domaine de votre profil le trafic Manager. Une fois créé, l’enregistrement CNAME crée un alias pour la ** &lt;myapp >. azurewebsites.net** ou ** &lt;myapp >. trafficmgr.com** nom de domaine. L’entrée CNAME résoudra à l’adresse IP de votre ** &lt;myapp >. azurewebsites.net** ou ** &lt;myapp >. trafficmgr.com** nom de domaine automatiquement, donc si l’adresse IP du site Web est modifiée, vous n’avez pas à exécuter l’action.

> [AZURE.NOTE] Certains bureaux autorise uniquement mapper des sous-domaines lors de l’utilisation d’un enregistrement CNAME, par exemple www.contoso.com et non les noms de racine, tel que contoso.com. Pour plus d’informations sur les enregistrements CNAME, consultez la documentation fournie par votre bureau d’enregistrement, <a href="http://en.wikipedia.org/wiki/CNAME_record">l’entrée Wikipédia dans l’enregistrement CNAME</a>ou le document <a href="http://tools.ietf.org/html/rfc1035">IETF de noms de domaine - implémentation et spécification</a> .

###<a name="a-record"></a>Un enregistrement

Un enregistrement A mappe un domaine, tel que **contoso.com** ou **www.contoso.com**, *ou un domaine génériques* telles que ** \*. contoso.com**, vers une adresse IP. Dans le cas d’un site Web Azure, l’adresse IP virtuelle du service ou une adresse IP spécifique adresse que vous avez acheté pour votre site Web. Le principal avantage d’un enregistrement A sur un enregistrement CNAME est que vous pouvez avoir une entrée qui utilise un caractère générique, tel que * **. contoso.com**, qui serait gérer des requêtes de plusieurs sous-domaines tel que * *mail.contoso.com**, * *login.contoso.com**, ou * *www.contso.com**.

> [AZURE.NOTE] Dans la mesure où un enregistrement A est mappé à une adresse IP statique, il ne peut pas résoudre automatiquement les modifications à l’adresse IP de votre site Web. Une adresse IP pour une utilisation avec les enregistrements A est fournie lorsque vous configurez des paramètres de noms de domaine personnalisé pour votre site Web ; Toutefois, cette valeur peut changer si vous supprimez et recréez votre site Web ou modifiez le mode de site Web à l’arrière-plan pour libérer.

> [AZURE.NOTE] Enregistrements a ne peuvent pas être utilisés pour le trafic avec le Gestionnaire de périphériques d’équilibrage de charge. Pour plus d’informations, voir [Contrôler Azure Sites Web le trafic avec le Gestionnaire de trafic Azure][trafficmanager].

<a name="bkmk_configsharedmode"></a><h2>Configurer votre site Web en mode standard ou partagées</h2>

Définition d’un nom de domaine personnalisé sur un site Web est disponible uniquement pour les modes Standard pour les sites Web Azure et de partagé. Avant de passer d’un site Web à partir du mode de site Web gratuit pour le partagé ou en mode Standard de site Web, vous devez d’abord supprimer VERR. MAJ dépenses en place pour votre abonnement de site Web. Pour plus d’informations sur les prix de mode partagé et Standard, voir [Détails tarifs][PricingDetails].

1. Dans votre navigateur, ouvrez le [Portail de gestion][portal].
2. Sous l’onglet **sites Web** , cliquez sur le nom de votre site.

    ![][standardmode1]

3. Cliquez sur l’onglet **échelle** .

    ![][standardmode2]


4. Dans la section **Général** , définissez le mode de site Web en cliquant sur **partagé**.

    ![][standardmode3]

    > [AZURE.NOTE] Si vous utilisez le trafic gestionnaire avec ce site Web, vous devez utiliser sélectionnez mode Standard à la place de partagé.

5. Cliquez sur **Enregistrer**.
6. Lorsque vous êtes invité à propos de l’augmentation des coûts de mode partagé (ou en mode Standard si vous choisissez Standard), cliquez sur **Oui** si vous êtes d’accord.

    <!--![][standardmode4]-->

    **Remarque**<br />
   Si vous recevez une erreur « Échelle de configuration de votre site Web 'nom de site Web' a échoué », vous pouvez utiliser le bouton Détails pour obtenir plus d’informations.

<a name="trafficmanager"></a><h2>(Facultatif) Ajoutez vos sites Web vers le Gestionnaire de trafic</h2>

Si vous voulez utiliser votre site Web avec le Gestionnaire de trafic, procédez comme suit.

1. Si vous n’avez pas déjà un profil le trafic Manager, utilisez les informations dans [créer un profil de trafic Manager à l’aide de création rapide] [ createprofile] en créer un. Remarque le **. trafficmgr.com** nom de domaine associé à votre profil le trafic Manager. Cela sera utilisé dans une étape ultérieure.

2. Utilisez les informations dans [Ajouter ou supprimer des points de terminaison de] [ addendpoint] pour ajouter votre site Web comme un point de terminaison dans votre profil le trafic Manager.

    > [AZURE.NOTE] Si votre site Web n’est pas répertorié lors de l’ajout d’un point de terminaison, vérifiez qu’elle est configurée pour le mode Standard. Pour travailler avec le Gestionnaire de trafic, vous devez utiliser le mode Standard pour votre site Web.

3. Connectez-vous au site Web de votre bureau d’enregistrement DNS, puis accédez à la page de gestion DNS. Recherchez les liens ou les zones du site comme **Nom de domaine**, de **DNS**ou de **Gestion des noms de serveur**.

4. À présent la trouver l’endroit où vous pouvez sélectionnez ou entrez des enregistrements CNAME. Vous devrez peut-être sélectionner le type d’enregistrement dans une liste déroulante vers le bas, ou accéder à une page de paramètres avancés. Vous recherchez les mots **CNAME**, **Alias**ou **sous-domaines**.

5. Vous devez également fournir l’alias de domaine ou un sous-domaine pour l’enregistrement CNAME. Par exemple, **www** si vous voulez créer un alias pour **www.customdomain.com**.

5. Vous devez également fournir un nom d’hôte est le nom de domaine canonique pour cet alias CNAME. Il s’agit de la **. trafficmgr.com** nom pour votre site Web.

Par exemple, l’enregistrement CNAME suivant transmet tout le trafic de **www.contoso.com** vers **contoso.trafficmgr.com**, le nom de domaine d’un site Web :

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Nom d’hôte/alias/sous-domaine</strong></td>
<td><strong>Domaine canonique</strong></td>
</tr>
<tr>
<td>www</td>
<td>Contoso.trafficmgr.com</td>
</tr>
</table>

Visiteur **www.contoso.com** verront jamais l’hôte true (contoso.azurewebsite.net), afin que le processus de transfert est invisible à l’utilisateur final.

> [AZURE.NOTE] Si vous utilisez le Gestionnaire de trafic avec un site Web, vous n’avez pas besoin de suivre les étapes décrites dans les sections suivantes, «**Ajouter un enregistrement CNAME pour votre domaine personnalisé**» et «**Ajouter un enregistrement A pour votre domaine personnalisé**». L’enregistrement CNAME créé lors des étapes précédentes achemine le trafic entrant pour le trafic Gestionnaire de, puis achemine le trafic vers le point de terminaison de site Web.

<a name="bkmk_configurecname"></a><h2>Ajouter un enregistrement CNAME pour votre domaine personnalisé</h2>

Pour créer un enregistrement CNAME, vous devez ajouter une nouvelle entrée dans la table DNS pour votre domaine personnalisé à l’aide des outils fournis par votre bureau d’enregistrement. Chaque registrar a une méthode similaire, mais légèrement différente de spécification d’un enregistrement CNAME, mais les concepts sont identiques.

1. Utilisez une des méthodes suivantes pour rechercher la **. azurewebsite.net** nom de domaine affecté à votre site Web.

    * Connexion au [Portail de gestion Azure][portal], sélectionnez votre site Web, sélectionnez **tableau de bord**et choisissez l’entrée de **L’URL du Site** dans la section **coup de œil** .

    * Installer et configurer [Azure Powershell](/manage/install-and-configure-windows-powershell/)et ensuite utiliser la commande suivante :

            get-azurewebsite yoursitename | select hostnames

    * Installer et configurer l' [Interface de ligne de commande Azure](/manage/install-and-configure-cli/)puis utilisez la commande suivante :

            azure site domain list yoursitename

    Enregistrer ce **. azurewebsite.net** nom, tel qu’il sera utilisé dans les étapes suivantes.

3. Connectez-vous au site Web de votre bureau d’enregistrement DNS, puis accédez à la page de gestion DNS. Recherchez les liens ou les zones du site comme **Nom de domaine**, de **DNS**ou de **Gestion des noms de serveur**.

4. À présent la trouver l’endroit où vous pouvez sélectionnez ou entrez des enregistrements CNAME. Vous devrez peut-être sélectionner le type d’enregistrement dans une liste déroulante vers le bas, ou accéder à une page de paramètres avancés. Vous recherchez les mots **CNAME**, **Alias**ou **sous-domaines**.

5. Vous devez également fournir l’alias de domaine ou un sous-domaine pour l’enregistrement CNAME. Par exemple, **www** si vous voulez créer un alias pour **www.customdomain.com**. Si vous voulez créer un alias pour le domaine racine, il peut être répertoriée en tant que la '**@**' symbole dans les outils de votre bureau d’enregistrement DNS.

5. Vous devez également fournir un nom d’hôte est le nom de domaine canonique pour cet alias CNAME. Il s’agit de la **. azurewebsite.net** nom pour votre site Web.

Par exemple, l’enregistrement CNAME suivant transmet tout le trafic de **www.contoso.com** vers **contoso.azurewebsite.net**, le nom de domaine d’un site Web :

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Nom d’hôte/alias/sous-domaine</strong></td>
<td><strong>Domaine canonique</strong></td>
</tr>
<tr>
<td>www</td>
<td>Contoso.azurewebsite.NET</td>
</tr>
</table>

Visiteur **www.contoso.com** verront jamais l’hôte true (contoso.azurewebsite.net), afin que le processus de transfert est invisible à l’utilisateur final.

> [AZURE.NOTE] L’exemple ci-dessus ne s’applique qu’aux circulations en le sous-domaine __www__ . Dans la mesure où vous ne pouvez pas utiliser des caractères génériques avec des enregistrements CNAME, vous devez créer un CNAME pour chaque domaine/sous-domaine. Si vous voulez rediriger le trafic de sous-domaine, tel que *. contoso.com, à votre adresse de azurewebsite.net, vous pouvez configurer une entrée de __Redirection d’URL__ ou __URL suivante__ dans vos paramètres DNS, ou créez un enregistrement A.

> [AZURE.NOTE] Cela peut prendre quelques instants pour votre CNAME de se propager dans le système DNS. Vous ne pouvez pas définir l’enregistrement CNAME pour le site Web jusqu'à ce que l’enregistrement CNAME s’est propagée. Vous pouvez utiliser un service tel que <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> pour vérifier que l’enregistrement CNAME est disponible.

###<a name="add-the-domain-name-to-your-website"></a>Ajouter le nom de domaine à votre site Web

Une fois que l’enregistrement CNAME pour nom de domaine est propagée, vous devez associer votre site Web. Vous pouvez ajouter le nom de domaine personnalisé défini par l’enregistrement CNAME pour votre site Web à l’aide de l’Interface de ligne de Azure (commande Azure) ou à l’aide du portail de gestion Azure.

**Pour ajouter un nom de domaine en utilisant les outils de ligne de commande**

Installer et configurer l' [Interface de ligne Azure](/manage/install-and-configure-cli/)puis utilisez la commande suivante :

    azure site domain add customdomain yoursitename

Par exemple, les éléments suivants ajoutera un nom de domaine personnalisé de **www.contoso.com** au site Web **contoso.azurewebsite.net** :

    azure site domain add www.contoso.com contoso

Vous pouvez vérifier que le nom de domaine personnalisé a été ajouté au site Web à l’aide de la commande suivante :

    azure site domain list yoursitename

La liste renvoyée par cette commande doit contenir le nom de domaine personnalisé, ainsi que la valeur par défaut **. azurewebsite.net** entrée.

**Pour ajouter un nom de domaine à l’aide du portail de gestion Azure**

1. Dans votre navigateur, ouvrez le [Portail de gestion Azure][portal].

2. Sous l’onglet **sites Web** , cliquez sur le nom de votre site, sélectionnez **tableau de bord**, puis **Gérer les domaines** à partir du bas de la page.

    ![][setcname2]

6. Dans la zone de texte **Noms de domaine** , tapez le nom de domaine que vous avez configurées.

    ![][setcname3]

6. Activez la case à cocher pour accepter le nom de domaine.

Une fois la configuration terminée, le nom de domaine personnalisé apparaîtront dans la section **noms de domaine** de la page **configuration** de votre site Web.

<a name="bkmk_configurearecord"></a><h2>Ajouter un enregistrement A pour votre domaine personnalisé</h2>

Pour créer un enregistrement A, vous devez tout d’abord rechercher l’adresse IP de votre site Web. Puis ajoutez une entrée dans la table DNS pour votre domaine personnalisé en utilisant les outils fournis par votre bureau d’enregistrement. Chaque registrar a une méthode similaire, mais légèrement différente de la spécification d’un enregistrement A, mais les concepts sont identiques. En plus de la création d’un enregistrement A, vous devez également créer un enregistrement CNAME qui Azure utilise pour vérifier l’enregistrement A.

1. Dans votre navigateur, ouvrez le [Portail de gestion Azure][portal].

2. Sous l’onglet **sites Web** , cliquez sur le nom de votre site, sélectionnez **tableau de bord**, puis **Gérer les domaines** à partir du bas de l’écran.

    ![][setcname2]

5. Dans la boîte de dialogue **Gérer les domaines personnalisés** , recherchez **L’adresse IP à utiliser lors de la configuration des enregistrements A**. Copiez l’adresse IP. Il sera utilisé lors de la création de l’enregistrement A.

5. Dans la boîte de dialogue **Gérer les domaines personnalisés** , notez le nom de domaine awverify à la fin du texte en haut de la boîte de dialogue. Il doit être **awverify.mysite.azurewebsites.net** où **monsite** est le nom de votre site Web. Copier, comme c’est le nom de domaine utilisé lors de la création de la vérification enregistrement CNAME.

6. Connectez-vous au site Web de votre bureau d’enregistrement DNS, puis accédez à la page de gestion DNS. Recherchez les liens ou les zones du site comme **Nom de domaine**, de **DNS**ou de **Gestion des noms de serveur**.

6. Rechercher l’endroit où vous pouvez sélectionner ou entrer des enregistrements A et CNAME. Vous devrez peut-être sélectionner le type d’enregistrement dans une liste déroulante vers le bas, ou accéder à une page de paramètres avancés.

7. Procédez comme suit pour créer l’enregistrement A :

    1. Sélectionnez ou entrez le domaine ou le sous-domaine qui utilisera l’enregistrement A. Par exemple, sélectionnez **www** si vous voulez créer un alias pour **www.customdomain.com**. Si vous voulez créer une entrée de caractères génériques pour tous les sous-domaines, entrez «__*__». Couvre tous les sous-domaines tels que **mail.customdomain.com**et **login.customdomain.com** **www.customdomain.com**.

        Si vous souhaitez créer un enregistrement A pour le domaine racine, il peut être répertoriée en tant que la '**@**' symbole dans les outils de votre bureau d’enregistrement DNS.

    2. Entrez l’adresse IP de votre service cloud dans le champ fourni. Associez l’entrée de domaine utilisée dans l’enregistrement A avec l’adresse IP du déploiement de votre service cloud.

        Par exemple, suit Qu'un enregistrement transmet tout le trafic de **contoso.com** vers **137.135.70.239**, l’adresse IP de notre application déployée :

        <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
        <tr>
        <td><strong>Nom d’hôte/sous-domaine</strong></td>
        <td><strong>Adresse IP</strong></td>
        </tr>
        <tr>
        <td>@</td>
        <td>137.135.70.239</td>
        </tr>
        </table>

        Cet exemple illustre la création d’un enregistrement A pour le domaine racine. Si vous souhaitez créer une entrée de caractères génériques pour couvrir tous les sous-domaines, vous devez entrer «__*__» en tant que le sous-domaine.

7. Ensuite, créez un enregistrement CNAME ayant un alias de **awverify**et le domaine canonique **awverify.mysite.azurewebsites.net** que vous avez obtenu précédemment.

    > [AZURE.NOTE] Si un alias d’awverify peut fonctionner pour certains bureaux d’enregistrement, d’autres personnes peuvent nécessiter le nom de domaine complet alias d’awverify.www.customdomainname.com ou awverify.customdomainname.com.

    Par exemple, les éléments suivants créera un enregistrement CNAME Azure permettent de vérifier la configuration de l’enregistrement A.

    <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
    <tr>
    <td><strong>Nom d’hôte/alias/sous-domaine</strong></td>
    <td><strong>Domaine canonique</strong></td>
    </tr>
    <tr>
    <td>awverify</td>
    <td>awverify.contoso.azurewebsites.NET</td>
    </tr>
    </table>

> [AZURE.NOTE] Cela peut prendre quelques instants pour l’awverify CNAME de se propager dans le système DNS. Vous ne pouvez pas définir le nom de domaine personnalisé défini par l’enregistrement A pour le site Web jusqu'à ce que l’awverify CNAME s’est propagée. Vous pouvez utiliser un service tel que <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> pour vérifier que l’enregistrement CNAME est disponible.

###<a name="add-the-domain-name-to-your-website"></a>Ajouter le nom de domaine à votre site Web

Une fois que l’enregistrement CNAME **awverify** pour nom de domaine est propagée, vous pouvez ensuite associer le domaine personnalisé défini par l’enregistrement A de votre site Web. Vous pouvez ajouter le nom de domaine personnalisé défini par l’enregistrement A pour votre site Web en utilisant soit la Azure infrastructure du langage commun ou à l’aide du portail de gestion Azure.

**Pour ajouter un nom de domaine à l’aide de l’Interface de ligne Azure (commande Azure)**

Installer et configurer l' [Infrastructure du langage commun Azure](/manage/install-and-configure-cli/)et ensuite utiliser la commande suivante :

    azure site domain add customdomain yoursitename

Par exemple, les éléments suivants ajoutera un nom de domaine personnalisé **contoso.com** au site Web **contoso.azurewebsite.net** :

    azure site domain add contoso.com contoso

Vous pouvez vérifier que le nom de domaine personnalisé a été ajouté au site Web à l’aide de la commande suivante :

    azure site domain list yoursitename

La liste renvoyée par cette commande doit contenir le nom de domaine personnalisé, ainsi que la valeur par défaut **. azurewebsite.net** entrée.

**Pour ajouter un nom de domaine à l’aide du portail de gestion Azure**

1. Dans votre navigateur, ouvrez le [Portail de gestion Azure][portal].

2. Sous l’onglet **sites Web** , cliquez sur le nom de votre site, sélectionnez **tableau de bord**, puis **Gérer les domaines** à partir du bas de la page.

    ![][setcname2]

6. Dans la zone de texte **Noms de domaine** , tapez le nom de domaine que vous avez configurées.

    ![][setcname3]

6. Activez la case à cocher pour accepter le nom de domaine.

Une fois la configuration terminée, le nom de domaine personnalisé apparaîtront dans la section **noms de domaine** de la page **configuration** de votre site Web.

> [AZURE.NOTE] Une fois que vous avez ajouté le nom de domaine personnalisé défini par l’enregistrement A pour votre site Web, vous pouvez supprimer l’enregistrement CNAME awverify à l’aide des outils fournis par votre bureau d’enregistrement. Toutefois, si vous souhaitez ajouter un autre enregistrement à l’avenir, vous devrez recréer l’awverify enregistrement avant que vous pouvez associer le nouveau nom de domaine définies par le nouvel enregistrement avec le site Web.

## <a name="next-steps"></a>Étapes suivantes

-   [Comment gérer les sites web](/manage/services/web-sites/how-to-manage-websites/)

-   [Configurer un certificat SSL pour les Sites Web](/develop/net/common-tasks/enable-ssl-web-site/)


<!-- Bookmarks -->

[Configure your web sites for shared mode]: #bkmk_configsharedmode
[Configure the CNAME on your domain registrar]: #bkmk_configurecname
[Configure a CNAME verification record on your domain registrar]: #bkmk_configurecname
[Configure an A record for the domain name]:#bkmk_configurearecord
[Set the domain name in management portal]: #bkmk_setcname

<!-- Links -->

[PricingDetails]: /pricing/details/
[portal]: http://manage.windowsazure.com
[digweb]: http://www.digwebinterface.com/
[cloudservicedns]: ../articles/custom-dns.md
[trafficmanager]: ../articles/app-service-web/web-sites-traffic-manager.md
[addendpoint]: ../articles/traffic-manager/traffic-manager-endpoints.md
[createprofile]: ../articles/traffic-manager/traffic-manager-manage-profiles.md

<!-- images -->

[setcname1]: ../media/dncmntask-cname-5.png


<!-- images -->
[standardmode1]: ./media/custom-dns-web-site/dncmntask-cname-1.png
[standardmode2]: ./media/custom-dns-web-site/dncmntask-cname-2.png
[standardmode3]: ./media/custom-dns-web-site/dncmntask-cname-3.png
[standardmode4]: ./media/custom-dns-web-site/dncmntask-cname-4.png


[setcname2]: ./media/custom-dns-web-site/dncmntask-cname-6.png
[setcname3]: ./media/custom-dns-web-site/dncmntask-cname-7.png
