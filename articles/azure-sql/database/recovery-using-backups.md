---
title: Ripristinare un database da un backup
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Informazioni sul ripristino temporato, che consente di eseguire il rollback di un database in database SQL di Azure o in un'istanza in Istanza gestita di SQL di Azure fino a 35 giorni.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein, danil
ms.date: 11/13/2020
ms.openlocfilehash: 670176d7478ddab3d17e15526df512dfa7e99fd4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762084"
---
# <a name="recover-using-automated-database-backups---azure-sql-database--sql-managed-instance"></a>Eseguire il ripristino usando backup automatici del database - database SQL di Azure & SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Le opzioni seguenti sono disponibili per il ripristino del database tramite [backup automatici del database](automated-backups-overview.md). È possibile:

- Creare un nuovo database nello stesso server, ripristinato fino a un punto nel tempo specificato entro il periodo di conservazione.
- Creare un database nello stesso server, ripristinato fino all'ora di eliminazione per un database eliminato.
- Creare un nuovo database in qualsiasi server nella stessa area, ripristinato fino al punto dei backup più recenti.
- Creare un nuovo database in qualsiasi server in qualsiasi altra area, ripristinato fino al punto dei backup replicati più recenti.

Se è stata [configurata la conservazione a lungo](long-term-retention-overview.md)termine dei backup, è anche possibile creare un nuovo database da qualsiasi backup con conservazione a lungo termine in qualsiasi server.

> [!IMPORTANT]
> Non è possibile sovrascrivere un database esistente durante il ripristino.

Quando si usa il livello di servizio Standard o Premium, il ripristino del database potrebbe comportare costi di archiviazione aggiuntivi. Il costo aggiuntivo viene generato quando le dimensioni massime del database ripristinato sono maggiori della quantità di spazio di archiviazione incluso nel livello di servizio e nel livello di prestazioni del database di destinazione. Per i dettagli dei prezzi delle risorse di archiviazione aggiuntive, vedere la [pagina dei prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se la quantità effettiva di spazio usato è inferiore alla quantità di spazio di archiviazione inclusa, è possibile evitare questo costo aggiuntivo impostando le dimensioni massime del database sulla quantità inclusa.

## <a name="recovery-time"></a>Tempo di ripristino

Il tempo di ripristino di un database tramite backup automatici del database è influenzato da diversi fattori:

- Dimensioni del database.
- Dimensioni di calcolo del database.
- Numero di log delle transazioni coinvolti.
- Quantità di attività che deve essere riprodotta per il ripristino fino al punto di ripristino.
- Larghezza di banda di rete se il ripristino si trova in un'area diversa.
- Il numero di richieste simultanee di ripristino in corso di elaborazione nell'area di destinazione.

Per un database di grandi dimensioni o molto attivo, il ripristino potrebbe richiedere diverse ore. Se si verifica un'interruzione prolungata in un'area, è possibile che venga avviato un numero elevato di richieste di ripristino geografico per il ripristino di emergenza. In presenza di numerose richieste, i tempi di recupero dei database in tale area possono aumentare. La maggior parte dei ripristini del database viene completata in meno di 12 ore.

Per una singola sottoscrizione sussistono alcune limitazioni al numero di richieste di ripristino simultanee. Queste limitazioni si applicano a qualsiasi combinazione di ripristini temporizzati, ripristini geografici e ripristini da backup con conservazione a lungo termine.

| **Opzione di distribuzione** | **Numero massimo di richieste simultanee elaborate** | **Numero massimo di richieste simultanee inviate** |
| :--- | --: | --: |
|**Database singolo (per sottoscrizione)**|30|100|
|**Pool elastico (per pool)**|4|2000|


Non esiste un metodo predefinito per ripristinare l'intero server. Per un esempio di come eseguire questa attività, vedere [database SQL di Azure: Ripristino completo del server](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666).

> [!IMPORTANT]
> Per eseguire il ripristino tramite backup automatizzati, è necessario essere membri del ruolo Collaboratore SQL Server o Collaboratore SQL Istanza gestita (a seconda della destinazione di ripristino) nella sottoscrizione oppure essere il proprietario della sottoscrizione. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo di Azure: ruoli predefiniti.](../../role-based-access-control/built-in-roles.md) È possibile eseguire il ripristino usando portale di Azure, PowerShell o l'API REST. Non è possibile usare Transact-SQL.

## <a name="point-in-time-restore"></a>Ripristino temporizzato

È possibile ripristinare un database autonomo, in pool o di istanza in un momento precedente usando portale di Azure, [PowerShell](/powershell/module/az.sql/restore-azsqldatabase)o [l'API REST](/rest/api/sql/databases/createorupdate#creates-a-database-from-pointintimerestore.). La richiesta può specificare qualsiasi livello di servizio o dimensione di calcolo per il database ripristinato. Assicurarsi di disporre di risorse sufficienti nel server in cui si sta ripristinando il database. 

Al termine, il ripristino crea un nuovo database nello stesso server del database originale. Il database ripristinato viene addebitato a tariffe normali, in base al livello di servizio e alle dimensioni di calcolo. Non vengono sostenuti addebiti fino al completamento del ripristino del database.

In genere si ripristina un database a un punto precedente per scopi di ripristino. È possibile considerare il database ripristinato come sostituzione del database originale o usarlo come origine dati per aggiornare il database originale.

- **Sostituzione del database**

  Se si prevede che il database ripristinato sia una sostituzione del database originale, è necessario specificare le dimensioni di calcolo e il livello di servizio del database originale. È quindi possibile rinominare il database originale e assegnare al database ripristinato il nome originale usando il [comando ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) in T-SQL.

- **Ripristino dei dati**

  Se si prevede di recuperare dati dal database ripristinato per il ripristino da un errore dell'utente o dell'applicazione, è necessario scrivere ed eseguire uno script di recupero dati che estrae i dati dal database ripristinato e si applica al database originale. Anche se il completamento dell'operazione di ripristino può richiedere molto tempo, il database in fase di ripristino è visibile nell'elenco dei database per tutto il processo. Se si elimina il database durante il ripristino, l'operazione di ripristino verrà annullata e non verranno addebitati costi per il database che non ha completato il ripristino.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Ripristino temporato tramite portale di Azure

È possibile ripristinare un database a istanza singola fino a un punto nel tempo dal pannello panoramica del database che si vuole ripristinare nel portale di Azure.

#### <a name="sql-database"></a>Database SQL

Per ripristinare un database fino a un punto nel tempo usando il portale di Azure, aprire la pagina di panoramica del database e **selezionare Ripristina** sulla barra degli strumenti. Scegliere l'origine di backup e selezionare il punto di backup tempormente da cui verrà creato un nuovo database.

  ![Screenshot delle opzioni di ripristino del database per il database SQL.](./media/recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>Istanza gestita di SQL

Per ripristinare un database dell'istanza gestita a un punto nel tempo usando il portale di Azure, aprire la pagina di panoramica del database e selezionare Ripristina **sulla** barra degli strumenti. Scegliere il punto di backup tempormente da cui verrà creato un nuovo database.

  ![Screenshot delle opzioni di ripristino del database per Istanza gestita di SQL.](./media/recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Per ripristinare un database da un backup a livello di codice, vedere Ripristino a livello [di codice tramite backup automatici.](recovery-using-backups.md)

## <a name="deleted-database-restore"></a>Ripristino di un database eliminato

È possibile ripristinare un database eliminato all'ora di eliminazione o a un momento precedente nello stesso server o nella stessa istanza gestita. A tale scopo, è possibile portale di Azure, [PowerShell](/powershell/module/az.sql/restore-azsqldatabase)o [REST (createMode=Restore).](/rest/api/sql/databases/createorupdate) Per ripristinare un database eliminato, creare un nuovo database dal backup.

> [!IMPORTANT]
> Se si elimina un server o un'istanza gestita, anche tutti i relativi database vengono eliminati e non possono essere ripristinati. Non è possibile ripristinare un server o un'istanza gestita eliminata.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Ripristino del database eliminato tramite il portale di Azure

I database eliminati vengono ripristinati dal portale di Azure dal server o dalla risorsa dell'istanza gestita.

> [!TIP]
> La visualizzazione dei database eliminati di recente  nella pagina Database eliminati in portale di Azure o a livello di codice potrebbe richiedere [alcuni minuti.](#programmatic-recovery-using-automated-backups)

#### <a name="sql-database"></a>Database SQL

Per ripristinare un database eliminato fino all'ora di eliminazione usando il portale di Azure, aprire la pagina di panoramica del server e selezionare **Database eliminati**. Selezionare un database eliminato da ripristinare e digitare il nome del nuovo database che verrà creato con i dati ripristinati dal backup.

  ![Screenshot del ripristino del database eliminato](./media/recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>Istanza gestita di SQL

Per ripristinare un database gestito usando il portale di Azure, aprire la pagina di panoramica dell'istanza gestita e selezionare **Database eliminati.** Selezionare un database eliminato da ripristinare e digitare il nome del nuovo database che verrà creato con i dati ripristinati dal backup.

  ![Screenshot del ripristino del database Istanza gestita di SQL di Azure eliminato](./media/recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Ripristino di database eliminato tramite PowerShell

Usare gli script di esempio seguenti per ripristinare un database eliminato per il database SQL o l'Istanza gestita SQL tramite PowerShell.

#### <a name="sql-database"></a>Database SQL

Per uno script di PowerShell di esempio che illustra come ripristinare un database eliminato in database SQL di Azure, vedere Ripristinare [un database con PowerShell.](scripts/restore-database-powershell.md)

#### <a name="sql-managed-instance"></a>Istanza gestita di SQL

Per uno script di PowerShell di esempio che illustra come ripristinare un database dell'istanza eliminata, vedere [Ripristinare un database dell'istanza eliminata con PowerShell](../managed-instance/point-in-time-restore.md#restore-a-deleted-database)

> [!TIP]
> Per ripristinare un database eliminato a livello di codice, vedere Esecuzione del ripristino a livello di [codice tramite backup automatici.](recovery-using-backups.md)

## <a name="geo-restore"></a>Ripristino geografico

> [!IMPORTANT]
> Il ripristino geografico è disponibile solo per i database SQL o le istanze gestite configurate con l'archiviazione di [backup con ridondanza geografica.](automated-backups-overview.md#backup-storage-redundancy)

È possibile ripristinare un database in qualsiasi server di database SQL o un database di istanza in qualsiasi istanza gestita in qualsiasi area di Azure dai backup con replica geografica più recenti. Il ripristino geografico usa un backup con replica geografica come origine. È possibile richiedere il ripristino geografico anche se il database o il data center non è accessibile a causa di un'interruzione del servizio.

Il ripristino geografico è l'opzione di ripristino predefinita quando il database non è disponibile a causa di un evento imprevisto nell'area di hosting. È possibile ripristinare il database in un server in qualsiasi altra area. Esiste un ritardo tra il momento in cui un backup viene creato e quando ne viene eseguita la replica geografica in un BLOB di Azure in un'area diversa. Di conseguenza, il database ripristinato può essere fino a un'ora indietro rispetto al database originale. Nella figura seguente viene illustrato un ripristino del database dall'ultimo backup disponibile in un'altra area.

![Immagine del ripristino geografico](./media/recovery-using-backups/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Ripristino geografico tramite il portale di Azure

Nell'portale di Azure si crea un nuovo database di istanza singola o gestita e si seleziona un backup di ripristino geografico disponibile. Il database appena creato contiene i dati di backup con ripristino geografico.

#### <a name="sql-database"></a>Database SQL

Per eseguire il ripristino geografico di un database singolo portale di Azure nell'area e nel server di propria scelta, seguire questa procedura:

1. Nel **dashboard** selezionare **Aggiungi Crea** database  >  **SQL.** Nella scheda **Informazioni di** base immettere le informazioni necessarie.
2. Seleziona **Impostazioni aggiuntive**.
3. Per **Usa dati esistenti** selezionare **Backup.**
4. Per **Backup** selezionare un backup dall'elenco dei backup di ripristino geografico disponibili.

    ![Screenshot delle opzioni di creazione del database SQL](./media/recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Completare il processo di creazione di un nuovo database dal backup. Quando si crea un database in database SQL di Azure, questo contiene il backup di ripristino geografico ripristinato.

#### <a name="sql-managed-instance"></a>Istanza gestita di SQL

Per eseguire il ripristino geografico di un database dell'istanza gestita dal portale di Azure a un'istanza gestita esistente in un'area di propria scelta, selezionare un'istanza gestita in cui si vuole ripristinare un database. Seguire questa procedura:

1. Selezionare **Nuovo database.**
2. Digitare il nome del database desiderato.
3. In **Usa dati esistenti** selezionare **Backup.**
4. Selezionare un backup dall'elenco dei backup di ripristino geografico disponibili.

    ![Screenshot delle opzioni nuovo database](./media/recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Completare il processo di creazione di un nuovo database. Quando si crea il database dell'istanza, questo contiene il backup di ripristino geografico ripristinato.

### <a name="geo-restore-by-using-powershell"></a>Ripristino geografico tramite PowerShell

#### <a name="sql-database"></a>Database SQL

Per uno script di PowerShell che illustra come eseguire il ripristino geografico per un singolo database, vedere Usare PowerShell per ripristinare un database singolo a un [momento precedente.](scripts/restore-database-powershell.md)

#### <a name="sql-managed-instance"></a>Istanza gestita di SQL

Per uno script di PowerShell che illustra come eseguire il ripristino geografico per un database di istanza gestita, vedere Usare PowerShell per ripristinare un database di istanza gestita [in un'altra area geografica.](../managed-instance/scripts/restore-geo-backup.md)

### <a name="geo-restore-considerations"></a>Considerazioni sul ripristino geografico

Non è possibile eseguire un ripristino temporizzazione in un database geo-secondario. È possibile eseguire questa operazione solo in un database primario. Per informazioni dettagliate sull'uso del ripristino geografico per il ripristino da un'interruzione, vedere Eseguire il [ripristino da un'interruzione.](../../key-vault/general/disaster-recovery-guidance.md)

> [!IMPORTANT]
> Il ripristino geografico è la soluzione di ripristino di emergenza più semplice disponibile nel database SQL e in SQL Istanza gestita. Si basa su backup con replica geografica creati automaticamente con un obiettivo del punto di ripristino (RPO) fino a 1 ora e un tempo di ripristino stimato fino a 12 ore. Non garantisce che l'area di destinazione abbia la capacità di ripristinare i database dopo un'interruzione a livello di area, perché è probabile un aumento significativo della domanda. Se l'applicazione usa database relativamente piccoli e non è fondamentale per l'azienda, il ripristino geografico è una soluzione di ripristino di emergenza appropriata. 
>
> Per le applicazioni business critical che richiedono database di grandi dimensioni e devono garantire la continuità aziendale, usare [gruppi di failover automatico](auto-failover-group-overview.md). Offre un obiettivo di tempo di ripristino e RPO molto più basso e la capacità è sempre garantita. 
>
> Per altre informazioni sulle scelte di continuità aziendale, vedere [Panoramica della continuità aziendale.](business-continuity-high-availability-disaster-recover-hadr-overview.md)

## <a name="programmatic-recovery-using-automated-backups"></a>Ripristino a livello di codice tramite backup automatizzati

È anche possibile usare Azure PowerShell o l'API REST per il ripristino. Le tabelle seguenti descrivono il set di comandi disponibili.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager PowerShell è ancora supportato dal database SQL e da SQL Istanza gestita, ma tutto lo sviluppo futuro è per il modulo Az.Sql. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo Az e nei moduli Azure Resource Manager sono in gran parte identici.

#### <a name="sql-database"></a>Database SQL

Per ripristinare un database autonomo o in pool, vedere [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Cmdlet | Descrizione |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Ottiene uno o più database. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Ottiene un database eliminato che è possibile ripristinare. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Ottiene una copia di backup con ridondanza geografica di un database. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Ripristina un database. |

  > [!TIP]
  > Per uno script di PowerShell di esempio che illustra come eseguire un ripristino temporato di un database, vedere Ripristinare un [database tramite PowerShell.](scripts/restore-database-powershell.md)

#### <a name="sql-managed-instance"></a>Istanza gestita di SQL

Per ripristinare un database di istanza gestita, [vedere Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Cmdlet | Descrizione |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Ottiene una o più istanze gestite. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Ottiene un database di istanza. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Ripristina un database dell'istanza. |

### <a name="rest-api"></a>API REST

Per ripristinare un database usando l'API REST:

| API | Descrizione |
| --- | --- |
| [REST (createMode=Recovery)](/rest/api/sql/databases) |Ripristina un database. |
| [Get Create or Update Database Status](/rest/api/sql/operations) |Restituisce lo stato durante un'operazione di ripristino. |

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

#### <a name="sql-database"></a>Database SQL

Per ripristinare un database tramite l'interfaccia della riga di comando di Azure, vedere [az sql db restore](/cli/azure/sql/db#az_sql_db_restore).

#### <a name="sql-managed-instance"></a>Istanza gestita di SQL

Per ripristinare un database di istanza gestita tramite l'interfaccia della riga di comando di Azure, vedere [az sql midb restore](/cli/azure/sql/midb#az_sql_midb_restore).

## <a name="summary"></a>Riepilogo

I backup automatici proteggono i database da errori dell'utente e delle applicazioni, dall'eliminazione accidentale e da interruzioni prolungate. Questa funzionalità incorporata è disponibile per tutti i livelli di servizio e le dimensioni di calcolo.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica sulla continuità aziendale](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- [Backup automatici del database SQL](automated-backups-overview.md)
- [Conservazione a lungo termine](long-term-retention-overview.md)
- Per altre informazioni su opzioni di ripristino più veloci, vedere [Replica geografica attiva](active-geo-replication-overview.md) o [Gruppi di failover automatico](auto-failover-group-overview.md).
