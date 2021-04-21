---
title: Ridimensionare le risorse di database singoli
description: Questo articolo illustra come ridimensionare le risorse di calcolo e di archiviazione disponibili per un database singolo nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, references_regions
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 04/09/2021
ms.openlocfilehash: ae1b3cc41d709c28ba517d672eb98cb60a837a8d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779076"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Ridimensionare le risorse di database singoli nel database SQL di Azure

Questo articolo descrive come ridimensionare le risorse di calcolo e archiviazione disponibili per un database SQL di Azure nel livello di calcolo con provisioning. In alternativa, il livello [di calcolo serverless](serverless-tier-overview.md) fornisce la scalabilità automatica del calcolo e le fatture al secondo per il calcolo usato.

Dopo aver inizialmente impostato il numero di vCore o DTO, è possibile aumentare o ridimensionare dinamicamente un singolo database in base all'esperienza effettiva usando:

* [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)
* [Portale di Azure](single-database-manage.md#the-azure-portal)
* [PowerShell](/powershell/module/az.sql/set-azsqldatabase)
* [Interfaccia della riga di comando di Azure](/cli/azure/sql/db#az_sql_db_update)
* [REST API](/rest/api/sql/databases/update)


Il video seguente mostra come modificare in modo dinamico il livello di servizio e le dimensioni di calcolo per aumentare le DTU disponibili per un singolo database.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file in database SQL di Azure](file-space-manage.md).

## <a name="impact"></a>Impatto

La modifica del livello di servizio o delle dimensioni di calcolo di comporta principalmente l'esecuzione della procedura seguente:

1. Creare una nuova istanza di calcolo per il database. 

    Viene creata una nuova istanza di calcolo con il livello di servizio e le dimensioni di calcolo richiesti. Per alcune combinazioni di modifiche del livello di servizio e delle dimensioni di calcolo, è necessario creare una replica del database nella nuova istanza di calcolo, che comporta la copia dei dati e può influire fortemente sulla latenza complessiva. Indipendentemente dal fatto che il database rimanga online durante questo passaggio e le connessioni continuino a essere indirizzate al database nell'istanza di calcolo originale.

2. Passare il routing delle connessioni a una nuova istanza di calcolo.

    Le connessioni esistenti al database nell'istanza di calcolo originale vengono eliminate. Tutte le nuove connessioni vengono stabilite al database nella nuova istanza di calcolo. Per alcune combinazioni di modifiche del livello di servizio e delle dimensioni di calcolo, i file di database vengono scollegati e ricollegare durante l'opzione .  Indipendentemente dal fatto che l'opzione possa causare una breve interruzione del servizio quando il database non è disponibile in genere per meno di 30 secondi e spesso solo per pochi secondi. Se sono presenti transazioni a esecuzione lunga in esecuzione quando le connessioni vengono eliminate, la durata di questo passaggio potrebbe richiedere più tempo per recuperare le transazioni interrotte. [ripristino accelerato del database](../accelerated-database-recovery.md) possibile ridurre l'impatto dell'interruzione di transazioni a esecuzione lunga.

> [!IMPORTANT]
> Nessun dato viene perso durante qualsiasi passaggio del flusso di lavoro. Assicurarsi di aver implementato una logica di [ripetizione](troubleshoot-common-connectivity-issues.md) dei tentativi nelle applicazioni e nei componenti che usano database SQL di Azure durante la modifica del livello di servizio.

## <a name="latency"></a>Latenza

La latenza stimata per modificare il livello di servizio, ridimensionare le dimensioni di calcolo di un database singolo o di un pool elastico, spostare un database in/fuori da un pool elastico o spostare un database tra pool elastici viene parametrizzata come segue:

|Livello di servizio|Database singolo di base,</br>Standard (S0-S1)|Pool elastico di base,</br>Standard (S2-S12), </br>per utilizzo generico database singolo o pool elastico|Database singolo o pool elastico Premium o business critical singolo|Hyperscale
|:---|:---|:---|:---|:---|
|**Database singolo Basic, </br> Standard (S0-S1)**|&bull;&nbsp;Latenza di tempo costante indipendente dello spazio usato</br>&bull;&nbsp;In genere, meno di 5 minuti|&bull;&nbsp;Latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio usato|&bull;&nbsp;Latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio usato|&bull;&nbsp;Latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio usato|
|**Pool elastico </br> Basic, Standard (S2-S12), per utilizzo generico </br> database singolo o pool elastico**|&bull;&nbsp;Latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio usato|&bull;&nbsp;Per i database singoli, latenza temporale costante indipendentemente da spazio usato</br>&bull;&nbsp;In genere, meno di 5 minuti per i database singoli</br>&bull;&nbsp;Per i pool elastici, proporzionale al numero di database|&bull;&nbsp;Latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio usato|&bull;&nbsp;Latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio usato|
|**Premium o business critical database singolo o pool elastico**|&bull;&nbsp;Latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio usato|&bull;&nbsp;Latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio usato|&bull;&nbsp;Latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio usato|&bull;&nbsp;Latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio usato|
|**Hyperscale**|N/D|N/D|N/D|&bull;&nbsp;Latenza temporale costante indipendentemente da spazio usato</br>&bull;&nbsp;In genere, meno di 2 minuti|

> [!NOTE]
> Inoltre, per i database Standard (S2-S12) e per utilizzo generico, la latenza per lo spostamento di un database in/fuori da un pool elastico o tra pool elastici sarà proporzionale alle dimensioni del database se il database usa l'archiviazione[PFS](../../storage/files/storage-files-introduction.md)(Premium File Share).
>
> Per determinare se un database usa l'archiviazione PFS, eseguire la query seguente nel contesto del database. Se il valore nella colonna AccountType è `PremiumFileStorage` o , il database usa `PremiumFileStorage-ZRS` l'archiviazione PFS.

```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!NOTE]
> La proprietà con ridondanza della zona rimarrà invariata per impostazione predefinita quando si esegue il ridimensionamento dal livello business critical al per utilizzo generico dati. La latenza per questo downgrade quando è abilitata la ridondanza della zona, nonché la latenza per il passaggio alla ridondanza della zona per il livello per utilizzo generico saranno proporzionali alle dimensioni del database.

> [!TIP]
> Per monitorare le operazioni in corso, vedere: Gestire le operazioni usando [l'API REST SQL](/rest/api/sql/operations/list), Gestire le operazioni tramite l'interfaccia della riga di comando , Monitorare le operazioni con [T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e questi due comandi di PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) e [Stop-AzSqlDatabaseActivity.](/powershell/module/az.sql/stop-azsqldatabaseactivity) [](/cli/azure/sql/db/op)

## <a name="cancelling-changes"></a>Annullamento delle modifiche

Un'operazione di modifica del livello di servizio o di scalabilità del calcolo può essere annullata.

### <a name="the-azure-portal"></a>Portale di Azure

Nel pannello panoramica del database passare a **Notifiche e** fare clic sul riquadro che indica che è in corso un'operazione:

![Funzionamento in corso](./media/single-database-scale/ongoing-operations.png)

Fare quindi clic sul pulsante Annulla **questa operazione.**

![Annullare l'operazione in corso](./media/single-database-scale/cancel-ongoing-operation.png)

### <a name="powershell"></a>PowerShell

Da un prompt dei comandi di PowerShell impostare `$resourceGroupName` , e , quindi eseguire il comando `$serverName` `$databaseName` seguente:

```azurecli
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

## <a name="additional-considerations"></a>Altre considerazioni

- Se si esegue l'aggiornamento a un livello di servizio o a dimensioni di calcolo superiori, le dimensioni massime del database non aumentano a meno che non si specificano esplicitamente dimensioni maggiori (maxsize).
- Per effettuare il downgrade di un database, la relativa quantità di spazio usato deve essere inferiore alle dimensioni massime consentite per il livello di servizio e le dimensioni di calcolo di destinazione.
- Quando si effettua il downgrade dal livello **Premium** al livello **Standard**, viene applicato un costo per le risorse di archiviazione extra se (1) le dimensioni massime del database sono supportate nelle dimensioni di calcolo di destinazione e (2) le dimensioni massime superano lo spazio di archiviazione incluso delle dimensioni di calcolo di destinazione. Ad esempio, se un database P1 con dimensioni massime di 500 GB viene ridimensionato a S3, si applica un costo di archiviazione aggiuntivo poiché S3 supporta una dimensione massima di 1 TB e la quantità di archiviazione inclusa è di soli 250 GB. Lo spazio di archiviazione extra è quindi 500 GB - 250 GB = 250 GB. Per i prezzi dello spazio di archiviazione aggiuntivo, [database SQL di Azure prezzi.](https://azure.microsoft.com/pricing/details/sql-database/) Se la quantità effettiva di spazio usato è inferiore allo spazio di archiviazione incluso, questo costo aggiuntivo può essere evitato riducendo le dimensioni massime del database fino allo spazio incluso.
- Quando si aggiorna un database con la [replica geografica](active-geo-replication-configure-portal.md) abilitata, aggiornare i database secondari al livello di servizio e alle dimensioni di calcolo desiderati prima di aggiornare il database primario (indicazione generale per ottenere prestazioni ottimali). Quando si esegue l'aggiornamento a un'edizione diversa, è un requisito che il database secondario viene aggiornato per primo.
- Quando si effettua il downgrade di un database con la [replica geografica](active-geo-replication-configure-portal.md) abilitata, eseguire il downgrade dei database primari al livello di servizio e alle dimensioni di calcolo desiderati prima del downgrade del database secondario (indicazione generale per ottenere prestazioni ottimali). Quando si esegue il downgrade a un'edizione diversa, è necessario eseguire prima il downgrade del database primario.
- Le offerte per il ripristino del servizio sono diverse per i vari livelli di servizio. Se si sta per eseguire il downgrade al **livello Basic,** il periodo di conservazione dei backup è inferiore. Vedere l'articolo relativo ai [backup del database SQL di Azure](automated-backups-overview.md).
- Le nuove proprietà per il database non vengono applicate fino al completamento delle modifiche.
- Quando la copia dei dati è necessaria per ridimensionare un database (vedere [Latenza](#latency)) quando si modifica il livello di servizio, un utilizzo elevato delle risorse simultaneo all'operazione di ridimensionamento può causare tempi di ridimensionamento più lunghi. Con [ripristino accelerato del database (ADR),](/sql/relational-databases/accelerated-database-recovery-concepts)il rollback delle transazioni a esecuzione lunga non è una fonte significativa di ritardo, ma un utilizzo elevato delle risorse simultanee può lasciare meno risorse di calcolo, archiviazione e larghezza di banda di rete per il ridimensionamento, in particolare per dimensioni di calcolo inferiori.

## <a name="billing"></a>Fatturazione

Viene addebitata ogni ora in cui un database esiste usando il livello di servizio e le dimensioni di calcolo più alti applicati durante tale ora, indipendentemente dall'utilizzo o dal fatto che il database sia stato attivo per meno di un'ora. Ad esempio, se si crea un database singolo che viene eliminato cinque minuti dopo, in fattura viene riportato l'addebito relativo a un'ora di database.

## <a name="change-storage-size"></a>modifica delle dimensioni di archiviazione

### <a name="vcore-based-purchasing-model"></a>Modello di acquisto basato su vCore

- È possibile eseguire il provisioning dello spazio di archiviazione fino al limite di dimensioni massime di archiviazione dei dati usando incrementi di 1 GB. L'archiviazione dei dati minima configurabile è di 1 GB. Per i limiti delle dimensioni massime di archiviazione dei dati in ogni obiettivo di servizio, vedere le pagine della documentazione sui limiti delle risorse per i limiti delle risorse per database singoli che usano il modello di acquisto [vCore](resource-limits-vcore-single-databases.md) e Limiti delle risorse per i database singoli che usano il modello di acquisto [DTU.](resource-limits-dtu-single-databases.md)
- È possibile effettuare il provisioning dell'archiviazione dei dati per un database singolo aumentandone o riducendone le dimensioni massime [usando portale di Azure,](https://portal.azure.com) [Transact-SQL,](/sql/t-sql/statements/alter-database-transact-sql#examples-1) [PowerShell,](/powershell/module/az.sql/set-azsqldatabase)l'interfaccia della riga di comando di [Azure](/cli/azure/sql/db#az_sql_db_update)o l'API [REST.](/rest/api/sql/databases/update) Se il valore della dimensione massima è specificato in byte, deve essere un multiplo di 1 GB (1073741824 byte).
- La quantità di dati che possono essere archiviati nei file di dati di un database è limitata dalle dimensioni massime di archiviazione dei dati configurate. Oltre a tale spazio di archiviazione, database SQL di Azure alloca automaticamente il 30% di spazio di archiviazione in più da usare per il log delle transazioni.
- database SQL di Azure alloca automaticamente 32 GB per ogni vCore per il `tempdb` database. `tempdb` si trova nell'archiviazione SSD locale in tutti i livelli di servizio.
- Il prezzo dell'archiviazione per un database singolo o un pool elastico è la somma degli importi di archiviazione dei dati e di archiviazione del log delle transazioni moltiplicati per il prezzo unitario di archiviazione del livello di servizio. Il costo di `tempdb` è incluso nel prezzo. Per informazioni dettagliate sul prezzo di archiviazione, vedere database SQL di Azure [prezzi.](https://azure.microsoft.com/pricing/details/sql-database/)

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file in database SQL di Azure](file-space-manage.md).

### <a name="dtu-based-purchasing-model"></a>modello di acquisto basato su DTU

- Il prezzo DTU per un singolo database include una determinata quantità di risorse di archiviazione senza costi aggiuntivi. Le risorse di archiviazione extra rispetto alla quantità inclusa possono essere sottoposte a provisioning per un costo aggiuntivo fino alla quantità massima in incrementi di 250 GB fino a 1 TB e quindi in incrementi di 256 GB oltre 1 TB. Per gli spazi di archiviazione inclusi e i limiti di dimensioni massime, vedere [Database singolo: dimensioni di archiviazione e dimensioni di calcolo](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- È possibile effettuare il provisioning di spazio di archiviazione aggiuntivo per un singolo database aumentandone le dimensioni massime usando portale di Azure, [Transact-SQL,](/sql/t-sql/statements/alter-database-transact-sql#examples-1) [PowerShell,](/powershell/module/az.sql/set-azsqldatabase)l'interfaccia della riga di comando di [Azure](/cli/azure/sql/db#az_sql_db_update)o l'API [REST](/rest/api/sql/databases/update).
- Il prezzo delle risorse di archiviazione extra per un singolo database corrisponde allo spazio di archiviazione extra moltiplicato per il prezzo unitario del livello di servizio. Per informazioni dettagliate sul prezzo dello spazio di archiviazione aggiuntivo, vedere database SQL di Azure [prezzi.](https://azure.microsoft.com/pricing/details/sql-database/)

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file in database SQL di Azure](file-space-manage.md).

### <a name="geo-replicated-database"></a>Database con replica geografica

Per modificare le dimensioni del database di un database secondario replicato, modificare le dimensioni del database primario. Questa modifica verrà quindi replicata e implementata anche nel database secondario.

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>Vincoli P11 e P15 quando le dimensioni massime sono maggiori di 1 TB

Più di 1 TB di spazio di archiviazione nel livello Premium è attualmente disponibile in tutte le aree ad eccezione di Cina orientale, Cina settentrionale, Germania centrale e Germania nord-orientale. In queste aree la quantità massima di spazio di archiviazione nel livello Premium è limitata a 1 TB. Ai database P11 e P15 con dimensioni massime maggiori di 1 TB vengono applicate le considerazioni e le limitazioni seguenti:

- Se le dimensioni massime per un database P11 o P15 sono state impostate su un valore maggiore di 1 TB, è possibile ripristinarlo o copiarlo solo in un database P11 o P15.  Successivamente, il database può essere ridimensionato a dimensioni di calcolo diverse, a condizione che la quantità di spazio allocato al momento dell'operazione di ridimensionamento non superi i limiti massimi delle nuove dimensioni di calcolo.
- Per gli scenari di replica geografica attiva:
  - Configurazione di una relazione di replica geografica: se il database primario è P11 o P15, anche il database secondario deve essere P11 o P15. Le dimensioni di calcolo inferiori vengono rifiutate come secondarie perché non sono in grado di supportare più di 1 TB.
  - Aggiornamento del database primario in una relazione di replica geografica: portando a oltre 1 TB le dimensioni massime di un database primario, viene attivata la stessa modifica nel database secondario. Entrambi gli aggiornamenti devono avere esito positivo per applicare la modifica al database primario. Vengono applicate limitazioni per l'opzione da oltre 1 TB. Se il database secondario si trova in un'area che non supporta più di 1 TB, il database primario non viene aggiornato.
- L'uso del servizio Importazione/Esportazione per il caricamento di database P11/P15 con più di 1 TB non è supportato. Usare SqlPackage.exe per [importare](database-import.md) ed [esportare](database-export.md) i dati.

## <a name="next-steps"></a>Passaggi successivi

Per i limiti complessivi delle risorse, database SQL di Azure limiti delle risorse basate su [vCore](resource-limits-vcore-single-databases.md) database SQL di Azure - database singoli e limiti delle risorse basate su [DTU](resource-limits-dtu-single-databases.md)- database singoli .
