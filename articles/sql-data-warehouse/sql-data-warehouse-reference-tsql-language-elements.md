<properties
   pageTitle="Éléments du langage SQL Data Warehouse Transact-SQL | Microsoft Azure"
   description="Liste de liens vers contenu de référence pour les éléments de langage Transact-SQL utilisée pour Data Warehouse SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/08/2016"
   ms.author="barbkess;sonyama;kevin"/>

# <a name="language-elements"></a>Éléments du langage

## <a name="core-elements"></a>Éléments fondamentaux

- [conventions de syntaxe](https://msdn.microsoft.com/library/ms177563.aspx)
- [règles d’appellation d’objet](https://msdn.microsoft.com/library/ms175874.aspx)
- [mots clés réservés](https://msdn.microsoft.com/library/ms189822.aspx)
- [classements](https://msdn.microsoft.com/library/ff848763.aspx)
- [commentaires](https://msdn.microsoft.com/library/ms181627.aspx)
- [constantes](https://msdn.microsoft.com/library/ms179899.aspx)
- [types de données](https://msdn.microsoft.com/library/ms187752.aspx)
- [EXÉCUTER](https://msdn.microsoft.com/library/ms188332.aspx)
- [expressions](https://msdn.microsoft.com/library/ms190286.aspx)
- [ARRÊT](https://msdn.microsoft.com/library/ms173730.aspx)
- [Solution de contournement propriété identité](https://msdn.microsoft.com/library/ms186775.aspx)
- [IMPRIMER](https://msdn.microsoft.com/library/ms176047.aspx)
- [UTILISATION](https://msdn.microsoft.com/library/ms188366.aspx)

## <a name="batches-control-of-flow-and-variables"></a>Lots de flux de contrôle et des variables

- [COMMENCER... FIN](https://msdn.microsoft.com/library/ms190487.aspx)
- [SAUT](https://msdn.microsoft.com/library/ms181271.aspx)
- [DÉCLARER@local_variable](https://msdn.microsoft.com/library/ms188927.aspx)
- [IF... AUTRE](https://msdn.microsoft.com/library/ms182717.aspx)
- [RAISERROR](https://msdn.microsoft.com/library/ms178592.aspx)
- [SET@local_variable](https://msdn.microsoft.com/library/ms189484.aspx)
- [LEVER](https://msdn.microsoft.com/library/ee677615.aspx)
- [ESSAYEZ... CAPTURES](https://msdn.microsoft.com/library/ms175976.aspx)
- [CERTAIN](https://msdn.microsoft.com/library/ms178642.aspx)

## <a name="operators"></a>Opérateurs
- [+ (Ajouter)](https://msdn.microsoft.com/library/ms178565.aspx)
- [+ (Concaténation de chaînes)](https://msdn.microsoft.com/library/ms177561.aspx)
- [-(Négatif)](https://msdn.microsoft.com/library/ms189480.aspx)
- [-(Soustraire)](https://msdn.microsoft.com/library/ms189518.aspx)
- [* (Multiplier)](https://msdn.microsoft.com/library/ms176019.aspx)
- [/ (Diviser)](https://msdn.microsoft.com/library/ms175009.aspx)
- [Modulo](https://msdn.microsoft.com/library/ms190279.aspx)

## <a name="wildcard-characters-to-match"></a>Caractères génériques pour mettre en correspondance
- [= (Signe égal)](https://msdn.microsoft.com/library/ms175118.aspx)
- [> (Supérieur à)](https://msdn.microsoft.com/library/ms178590.aspx)
- [< (Inférieur à)](https://msdn.microsoft.com/library/ms179873.aspx)
- [> = (Dynamics ou égal à)](https://msdn.microsoft.com/library/ms181567.aspx)
- [< = (inférieur ou égal à)](https://msdn.microsoft.com/library/ms174978.aspx)
- [<> (Non égal à)](https://msdn.microsoft.com/library/ms176020.aspx)
- [! = (Non égal à)](https://msdn.microsoft.com/library/ms190296.aspx)
- [ET](https://msdn.microsoft.com/library/ms188372.aspx)
- [ENTRE](https://msdn.microsoft.com/library/ms187922.aspx)
- [IL EXISTE](https://msdn.microsoft.com/library/ms188336.aspx)
- [IN](https://msdn.microsoft.com/library/ms177682.aspx)
- [[PAS] EST NULL](https://msdn.microsoft.com/library/ms188795.aspx)
- [J’AIME](https://msdn.microsoft.com/library/ms179859.aspx)
- [PAS](https://msdn.microsoft.com/library/ms189455.aspx)
- [OR](https://msdn.microsoft.com/library/ms188361.aspx)

### <a name="bitwise-operators"></a>Opérateurs de bits

- [& (Et bit à bit)](https://msdn.microsoft.com/library/ms174965.aspx)
- [| (Ou binaire)](https://msdn.microsoft.com/library/ms186714.aspx)
- [^ (Binaire ou exclusif)](https://msdn.microsoft.com/library/ms190277.aspx)
- [~ (NOT binaire)](https://msdn.microsoft.com/library/ms173468.aspx)
- [^ = (Exclusive binaire ou égal à)](https://msdn.microsoft.com/library/cc627413.aspx)
- [| = (Ou binaire est égal à)](https://msdn.microsoft.com/library/cc627409.aspx)
- [& = (signe égal et bit à bit)](https://msdn.microsoft.com/library/cc627427.aspx)

## <a name="functions"></a>Fonctions

- [@@DATEFIRST](https://msdn.microsoft.com/library/ms187766.aspx)
- [@@ERROR](https://msdn.microsoft.com/library/ms188790.aspx)
- [@@LANGUAGE](https://msdn.microsoft.com/library/ms177557.aspx)
- [@@SPID](https://msdn.microsoft.com/library/ms189535.aspx)
- [@@TRANCOUNT](https://msdn.microsoft.com/library/ms187967.aspx)
- [@@VERSION](https://msdn.microsoft.com/library/ms177512.aspx)
- [ABS](https://msdn.microsoft.com/library/ms189800.aspx)
- [ACOS](https://msdn.microsoft.com/library/ms178627.aspx)
- [ASCII](https://msdn.microsoft.com/library/ms177545.aspx)
- [ASIN](https://msdn.microsoft.com/library/ms181581.aspx)
- [ATAN](https://msdn.microsoft.com/library/ms181746.aspx)
- [ATN2](https://msdn.microsoft.com/library/ms173854.aspx)
- [BINARY_CHECKSUM](https://msdn.microsoft.com/library/ms173784.aspx)
- [CASSE](https://msdn.microsoft.com/library/ms181765.aspx)
- [CAST et CONVERT](https://msdn.microsoft.com/library/ms187928.aspx)
- [PLAFOND](https://msdn.microsoft.com/library/ms189818.aspx)
- [CAR](https://msdn.microsoft.com/library/ms187323.aspx)
- [CHARINDEX](https://msdn.microsoft.com/library/ms186323.aspx)
- [SOMME DE CONTRÔLE](https://msdn.microsoft.com/library/ms189788.aspx)
- [UNE FUSION](https://msdn.microsoft.com/library/ms190349.aspx)
- [NOM_COL](https://msdn.microsoft.com/library/ms174974.aspx)
- [COLLATIONPROPERTY](https://msdn.microsoft.com/library/ms190305.aspx)
- [CONCAT](https://msdn.microsoft.com/library/hh231515.aspx)
- [COS](https://msdn.microsoft.com/library/ms188919.aspx)
- [COT](https://msdn.microsoft.com/library/ms188921.aspx)
- [NOMBRE](https://msdn.microsoft.com/library/ms175997.aspx)
- [COUNT_BIG](https://msdn.microsoft.com/library/ms190317.aspx)
- [CUME_DIST](https://msdn.microsoft.com/library/hh231078.aspx)
- [CURRENT_TIMESTAMP](https://msdn.microsoft.com/library/ms188751.aspx)
- [CURRENT_USER](https://msdn.microsoft.com/library/ms176050.aspx)
- [DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx)
- [DATALENGTH](https://msdn.microsoft.com/library/ms173486.aspx)
- [DATEADD](https://msdn.microsoft.com/library/ms186819.aspx)
- [DATEDIFF](https://msdn.microsoft.com/library/ms189794.aspx)
- [DATEETHEUREDEPUISPARTIES](https://msdn.microsoft.com/library/hh213228.aspx)
- [DATENAME](https://msdn.microsoft.com/library/ms174395.aspx)
- [DATEPART](https://msdn.microsoft.com/library/ms174420.aspx)
- [DATETIME2FROMPARTS](https://msdn.microsoft.com/library/hh213312.aspx)
- [DATETIMEFROMPARTS](https://msdn.microsoft.com/library/hh213233.aspx)
- [DATETIMEOFFSETFROMPARTS](https://msdn.microsoft.com/library/hh231077.aspx)
- [JOUR](https://msdn.microsoft.com/library/ms176052.aspx)
- [DB_ID](https://msdn.microsoft.com/library/ms186274.aspx)
- [DB_NAME](https://msdn.microsoft.com/library/ms189753.aspx)
- [DEGRÉS](https://msdn.microsoft.com/library/ms178566.aspx)
- [DENSE_RANK](https://msdn.microsoft.com/library/ms173825.aspx)
- [DIFFÉRENCE](https://msdn.microsoft.com/library/ms188753.aspx)
- [FIN.MOIS](https://msdn.microsoft.com/library/hh213020.aspx)
- [MESSAGE_ERREUR](https://msdn.microsoft.com/library/ms190358.aspx)
- [NUMÉRO_ERREUR](https://msdn.microsoft.com/library/ms175069.aspx)
- [ERROR_PROCEDURE](https://msdn.microsoft.com/library/ms188398.aspx)
- [ERROR_SEVERITY](https://msdn.microsoft.com/library/ms178567.aspx)
- [ERROR_STATE](https://msdn.microsoft.com/library/ms180031.aspx)
- [EXP](https://msdn.microsoft.com/library/ms179857.aspx)
- [FIRST_VALUE](https://msdn.microsoft.com/library/hh213018.aspx)
- [PLANCHER](https://msdn.microsoft.com/library/ms178531.aspx)
- [GETDATE](https://msdn.microsoft.com/library/ms188383.aspx)
- [GETUTCDATE](https://msdn.microsoft.com/library/ms178635.aspx)
- [HAS_DBACCESS](https://msdn.microsoft.com/library/ms187718.aspx)
- [HASHBYTES](https://msdn.microsoft.com/library/ms174415.aspx)
- [INDEXPROPERTY](https://msdn.microsoft.com/library/ms187729.aspx)
- [ISDATE](https://msdn.microsoft.com/library/ms187347.aspx)
- [ISNULL](https://msdn.microsoft.com/library/ms184325.aspx)
- [ISNUMERIC](https://msdn.microsoft.com/library/ms186272.aspx)
- [DÉCALAGE](https://msdn.microsoft.com/library/hh231256.aspx)
- [LAST_VALUE](https://msdn.microsoft.com/library/hh231517.aspx)
- [AVANCE](https://msdn.microsoft.com/library/hh213125.aspx)
- [GAUCHE](https://msdn.microsoft.com/library/ms177601.aspx)
- [NBCAR](https://msdn.microsoft.com/library/ms190329.aspx)
- [JOURNAL](https://msdn.microsoft.com/library/ms190319.aspx)
- [LOG10](https://msdn.microsoft.com/library/ms175121.aspx)
- [INFÉRIEUR](https://msdn.microsoft.com/library/ms174400.aspx)
- [LTRIM](https://msdn.microsoft.com/library/ms177827.aspx)
- [MAX](https://msdn.microsoft.com/library/ms187751.aspx)
- [MIN](https://msdn.microsoft.com/library/ms179916.aspx)
- [MOIS](https://msdn.microsoft.com/library/ms187813.aspx)
- [NCHAR](https://msdn.microsoft.com/library/ms182673.aspx)
- [NTILE](https://msdn.microsoft.com/library/ms175126.aspx)
- [NULLIF](https://msdn.microsoft.com/library/ms177562.aspx)
- [OBJECT_ID](https://msdn.microsoft.com/library/ms190328.aspx)
- [NOM_OBJET](https://msdn.microsoft.com/library/ms186301.aspx)
- [OBJECTPROPERTY](https://msdn.microsoft.com/library/ms176105.aspx)
- [OIBJECTPROPERTYEX](https://msdn.microsoft.com/library/ms188390.aspx)
- [Fonctions scalaires ODBCS](https://msdn.microsoft.com/library/bb630290.aspx)
- [SUR la clause](https://msdn.microsoft.com/library/ms189461.aspx)
- [PARSENAME](https://msdn.microsoft.com/library/ms188006.aspx)
- [PATINDEX](https://msdn.microsoft.com/library/ms188395.aspx)
- [PERCENTILE_CONT](https://msdn.microsoft.com/library/hh231473.aspx)
- [PERCENTILE_DISC](https://msdn.microsoft.com/library/hh231327.aspx)
- [PERCENT_RANK](https://msdn.microsoft.com/library/hh213573.aspx)
- [PI](https://msdn.microsoft.com/library/ms189512.aspx)
- [PUISSANCE](https://msdn.microsoft.com/library/ms174276.aspx)
- [QUOTENAME](https://msdn.microsoft.com/library/ms176114.aspx)
- [RADIANS](https://msdn.microsoft.com/library/ms189742.aspx)
- [ALEA](https://msdn.microsoft.com/library/ms177610.aspx)
- [RANG](https://msdn.microsoft.com/library/ms176102.aspx)
- [REMPLACER](https://msdn.microsoft.com/library/ms186862.aspx)
- [RÉPLIQUER](https://msdn.microsoft.com/library/ms174383.aspx)
- [INVERSER](https://msdn.microsoft.com/library/ms180040.aspx)
- [Oui](https://msdn.microsoft.com/library/ms177532.aspx)
- [ARRONDI](https://msdn.microsoft.com/library/ms175003.aspx)
- [ROW_NUMBER](https://msdn.microsoft.com/library/ms186734.aspx)
- [RTRIM](https://msdn.microsoft.com/library/ms178660.aspx)
- [SCHEMA_ID](https://msdn.microsoft.com/library/ms188797.aspx)
- [SCHEMA_NAME](https://msdn.microsoft.com/library/ms175068.aspx)
- [SERVERPROPERTY](https://msdn.microsoft.com/library/ms174396.aspx)
- [SESSION_USER](https://msdn.microsoft.com/library/ms177587.aspx)
- [SIGNE](https://msdn.microsoft.com/library/ms188420.aspx)
- [SIN](https://msdn.microsoft.com/library/ms188377.aspx)
- [SMALLDATETIMEFROMPARTS](https://msdn.microsoft.com/library/hh213396.aspx)
- [SOUNDEX](https://msdn.microsoft.com/library/ms187384.aspx)
- [ESPACE](https://msdn.microsoft.com/library/ms187950.aspx)
- [SQL_VARIANT_PROPERTY](https://msdn.microsoft.com/library/ms178550.aspx)
- [RACINE](https://msdn.microsoft.com/library/ms176108.aspx)
- [CARRÉ](https://msdn.microsoft.com/library/ms173569.aspx)
- [FONCTION STATS_DATE](https://msdn.microsoft.com/library/ms190330.aspx)
- [ECARTYPE](https://msdn.microsoft.com/library/ms190474.aspx)
- [ECARTYPEP](https://msdn.microsoft.com/library/ms176080.aspx)
- [STR](https://msdn.microsoft.com/library/ms189527.aspx)
- [CONTENUS](https://msdn.microsoft.com/library/ms188043.aspx)
- [SOUS-CHAÎNE](https://msdn.microsoft.com/library/ms187748.aspx)
- [SOMME](https://msdn.microsoft.com/library/ms187810.aspx)
- [SUSER_SNAME](https://msdn.microsoft.com/library/ms174427.aspx)
- [SWITCHOFFSET](https://msdn.microsoft.com/library/bb677244.aspx)
- [SYSDATETIME](https://msdn.microsoft.com/library/bb630353.aspx)
- [SYSDATETIMEOFFSET](https://msdn.microsoft.com/library/bb677334.aspx)
- [SYSTEM_USER](https://msdn.microsoft.com/library/ms179930.aspx)
- [SYSUTCDATETIME](https://msdn.microsoft.com/library/bb630387.aspx)
- [TAN](https://msdn.microsoft.com/library/ms190338.aspx)
- [TERTIARY_WEIGHTS](https://msdn.microsoft.com/library/ms186881.aspx)
- [TIMEFROMPARTS](https://msdn.microsoft.com/library/hh213398.aspx)
- [TODATETIMEOFFSET](https://msdn.microsoft.com/library/bb630335.aspx)
- [ID](https://msdn.microsoft.com/library/ms181628.aspx)
- [TYPE_NAME](https://msdn.microsoft.com/library/ms189750.aspx)
- [TYPEPROPERTY](https://msdn.microsoft.com/library/ms188419.aspx)
- [UNICODE](https://msdn.microsoft.com/library/ms180059.aspx)
- [SUPÉRIEUR](https://msdn.microsoft.com/library/ms180055.aspx)
- [UTILISATEUR](https://msdn.microsoft.com/library/ms186738.aspx)
- [NOM_UTILISATEUR](https://msdn.microsoft.com/library/ms188014.aspx)
- [VAR](https://msdn.microsoft.com/library/ms186290.aspx)
- [VARP](https://msdn.microsoft.com/library/ms188735.aspx)
- [ANNÉE](https://msdn.microsoft.com/library/ms186313.aspx)
- [XACT_STATE](https://msdn.microsoft.com/library/ms189797.aspx)

## <a name="transactions"></a>Transactions

- [transactions](https://msdn.microsoft.com/library/mt204031.aspx)

## <a name="diagnostic-sessions"></a>Sessions de diagnostic

- [CRÉER UNE SESSION DE DIAGNOSTICS](https://msdn.microsoft.com/library/mt204029.aspx)

## <a name="procedures"></a>Procédures

- [sp_addrolemember](https://msdn.microsoft.com/library/ms187750.aspx)
- [sp_columns](https://msdn.microsoft.com/library/ms176077.aspx)
- [sp_configure](https://msdn.microsoft.com/library/ms188787.aspx)
- [sp_datatype_info_90](https://msdn.microsoft.com/library/mt204014.aspx)
- [sp_droprolemember](https://msdn.microsoft.com/library/ms188369.aspx)
- [sp_execute](https://msdn.microsoft.com/library/ff848746.aspx)
- [sp_executesql](https://msdn.microsoft.com/library/ms188001.aspx)
- [sp_fkeys](https://msdn.microsoft.com/library/ms175090.aspx)
- [sp_pdw_add_network_credentials](https://msdn.microsoft.com/library/mt204011.aspx)
- [sp_pdw_database_encryption](https://msdn.microsoft.com/library/mt219360.aspx)
- [sp_pdw_database_encryption_regenerate_system_keys](https://msdn.microsoft.com/library/mt204033.aspx)
- [sp_pdw_log_user_data_masking](https://msdn.microsoft.com/library/mt204023.aspx)
- [sp_pdw_remove_network_credentials](https://msdn.microsoft.com/library/mt204038.aspx)
- [sp_pkeys](https://msdn.microsoft.com/library/ms189813.aspx)
- [sp_prepare](https://msdn.microsoft.com/library/ff848808.aspx)
- [sp_spaceused](https://msdn.microsoft.com/library/ms188776.aspx)
- [sp_special_columns_100](https://msdn.microsoft.com/library/mt204025.aspx)
- [sp_sproc_columns](https://msdn.microsoft.com/library/ms182705.aspx)
- [sp_statistics](https://msdn.microsoft.com/library/ms173842.aspx)
- [sp_tables](https://msdn.microsoft.com/library/ms186250.aspx)
- [sp_unprepare](https://msdn.microsoft.com/library/ff848735.aspx)



## <a name="set-statements"></a>Instructions SET

- [DÉFINIR ANSI_DEFAULTS](https://msdn.microsoft.com/library/ms188340.aspx)
- [SET ANSI_NULL_DFLT_OFF](https://msdn.microsoft.com/library/ms187356.aspx)
- [SET ANSI_NULL_DFLT_ON](https://msdn.microsoft.com/library/ms187375.aspx)
- [SET ANSI_NULLS](https://msdn.microsoft.com/library/ms188048.aspx)
- [SET ANSI_PADDING](https://msdn.microsoft.com/library/ms187403.aspx)
- [DÉFINIR ANSI_WARNINGS](https://msdn.microsoft.com/library/ms190368.aspx)
- [DÉFINIR ARITHABORT](https://msdn.microsoft.com/library/ms190306.aspx)
- [SET ARITHIGNORE](https://msdn.microsoft.com/library/ms184341.aspx)
- [SET CONCAT_NULL_YIELDS_NULL](https://msdn.microsoft.com/library/ms176056.aspx)
- [SET DATEFIRST](https://msdn.microsoft.com/library/ms181598.aspx)
- [DÉFINIR LE FORMAT DE DATE](https://msdn.microsoft.com/library/ms189491.aspx)
- [SET FMTONLY](https://msdn.microsoft.com/library/ms173839.aspx)
- [JEU DE IMPLICIT_TRANSACITONS](https://msdn.microsoft.com/library/ms187807.aspx)
- [SET LOCK_TIMEOUT](https://msdn.microsoft.com/library/ms189470.aspx)
- [JEU DE NUMBERIC_ROUNDABORT](https://msdn.microsoft.com/library/ms188791.aspx)
- [DÉFINIR QUOTED_IDENTIFIER](https://msdn.microsoft.com/library/ms174393.aspx)
- [DÉFINIR LE NOMBRE DE LIGNES](https://msdn.microsoft.com/library/ms188774.aspx)
- [SET TEXTSIZE](https://msdn.microsoft.com/library/ms186238.aspx)
- [DÉFINIR LE NIVEAU D’ISOLEMENT TRANSACTION](https://msdn.microsoft.com/library/ms173763.aspx)
- [SET XACT_ABORT](https://msdn.microsoft.com/library/ms188792.aspx)


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, voir [vue d’ensemble de référence Data Warehouse SQL][].

<!--Image references-->

<!--Article references-->
[Vue d’ensemble de référence Data Warehouse SQL]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->
