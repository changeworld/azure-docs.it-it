---
title: Livello di elaborazione serverless
description: Questo articolo descrive il nuovo livello di calcolo serverless e lo confronta con il livello di calcolo con provisioning esistente per database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 4/15/2021
ms.openlocfilehash: ea9d5a5c39bf73ede2391c586f09dd95ff79b63c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531978"
---
# <a name="azure-sql-database-serverless"></a>database SQL di Azure serverless
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Il livello serverless è un livello di calcolo per database singoli in Database SQL di Azure che ridimensiona automaticamente il calcolo in base alle esigenze dei carichi di lavoro, addebitando la quantità di calcolo usata al secondo. Il livello di calcolo serverless inoltre sospende automaticamente i database durante i periodi di inattività, in cui viene addebitata solo l'archiviazione, e li riprende automaticamente quando necessario.

## <a name="serverless-compute-tier"></a>Livello di elaborazione serverless

Il livello di calcolo serverless per database singoli database SQL di Azure parametrizzato da un intervallo di scalabilità automatica di calcolo e da un ritardo di sospensione automatica. La configurazione di questi parametri modella l'esperienza delle prestazioni del database e il costo di calcolo.

![Fatturazione serverless](./media/serverless-tier-overview/serverless-billing.png)

### <a name="performance-configuration"></a>Configurazione delle prestazioni

- I **vCore minimi e** il numero massimo di **vCore sono** parametri configurabili che definiscono l'intervallo di capacità di calcolo disponibile per il database. I limiti di memoria e I/O sono proporzionali all'intervallo vCore specificato.  
- Il **ritardo di sospensione automatica** è un parametro configurabile che definisce il periodo di tempo in cui il database deve essere inattivo prima di essere automaticamente sospeso. Il database viene ripreso automaticamente quando si verifica l'accesso successivo o un'altra attività.  In alternativa, la sospensione automatica può essere disabilitata.

### <a name="cost"></a>Costo

- Il costo di un database serverless è la somma dei costi di calcolo e di archiviazione.
- Quando l'utilizzo del calcolo è compreso tra i limiti minimo e massimo configurati, il costo di calcolo è basato su vCore e memoria usata.
- Quando l'utilizzo del calcolo è inferiore ai limiti minimi configurati, il costo di calcolo è basato sul numero minimo di vCore e sulla quantità minima di memoria configurata.
- Quando il database viene sospeso, il costo di calcolo è zero e vengono sostenuti solo i costi di archiviazione.
- Il costo di archiviazione viene determinato come nel livello di calcolo con provisioning.

Per altri dettagli sui costi, vedere [Fatturazione.](serverless-tier-overview.md#billing)

## <a name="scenarios"></a>Scenari

Il modello serverless è caratterizzato da un rapporto qualità-prezzo ottimizzato per database singoli con modelli di utilizzo intermittenti e imprevedibili che possono permettersi qualche ritardo all'avvio del calcolo dopo periodi di inattività. Al contrario, il livello di calcolo con provisioning è caratterizzato da un rapporto prezzo-prestazioni ottimizzato per database singoli o per più database in pool elastici con un utilizzo medio più elevato che non possono tollerare ritardi nella fase di avvio del calcolo.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scenari adatti per il calcolo serverless

- Database singoli con modelli di utilizzo intermittenti e imprevedibili invasi da periodi di inattività e minore utilizzo medio delle risorse di calcolo nel tempo.
- Database singoli nel livello di calcolo con provisioning che vengono spesso ridimensionati e clienti che preferiscono delegare il ridimensionamento del calcolo al servizio.
- Nuovi database singoli senza cronologia di utilizzo in cui il dimensionamento del calcolo è difficile o non è possibile stimare prima della distribuzione nel database SQL.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scenari adatti per il calcolo con provisioning

- Database singoli con modelli di utilizzo più regolari e prevedibili e un utilizzo medio di calcolo più elevato nel tempo.
- Database che non possono tollerare compromessi in termini di prestazioni derivanti da un taglio della memoria più frequente o da ritardi nella ripresa da uno stato di sospensione.
- Più database con modelli di utilizzo intermittenti e imprevedibili che possono essere consolidati in pool elastici per una migliore ottimizzazione delle prestazioni dei prezzi.

## <a name="comparison-with-provisioned-compute-tier"></a>Confronto con il livello di calcolo con provisioning

La tabella seguente riepiloga le differenze tra il livello di calcolo serverless e il livello di calcolo con provisioning:

| | **Calcolo serverless** | **Calcolo con provisioning** |
|:---|:---|:---|
|**Modello di utilizzo del database**| Utilizzo intermittente e imprevedibile con un utilizzo di calcolo medio inferiore nel tempo. | Modelli di utilizzo più regolari con un utilizzo medio di calcolo più elevato nel tempo o più database che usano pool elastici.|
| **Impegno per la gestione delle prestazioni** |Minore|Maggiore|
|**Ridimensionamento delle risorse di calcolo**|Automatico|Manuale|
|**Tempo di risposta per le risorse di calcolo**|Inferiore dopo periodi di inattività|Immediato|
|**Granularità della fatturazione**|Al secondo|All'ora|

## <a name="purchasing-model-and-service-tier"></a>Modello di acquisto e livello di servizio

Il database SQL serverless è attualmente supportato solo nel livello per utilizzo generico su hardware di quinta generazione nel modello di acquisto vCore.

## <a name="autoscaling"></a>Scalabilità automatica

### <a name="scaling-responsiveness"></a>Tempo di risposta per il ridimensionamento

In generale, i database serverless vengono eseguiti in un computer con capacità sufficiente per soddisfare la richiesta di risorse senza interruzioni per qualsiasi quantità di calcolo richiesta entro i limiti impostati dal valore vCore massimo. Di tanto in tanto, viene eseguito automaticamente il bilanciamento del carico se il computer non è in grado di soddisfare la richiesta di risorse entro pochi minuti. Ad esempio, se la richiesta di risorse è di 4 vCore, ma sono disponibili solo 2 vCore, il bilanciamento del carico potrebbe richiedere fino a pochi minuti prima che siano forniti 4 vCore. Durante il bilanciamento del carico il database rimane online ad eccezione di un breve periodo al termine dell'operazione, quando vengono rilasciate le connessioni.

### <a name="memory-management"></a>Gestione della memoria

La memoria per i database serverless viene recuperata più frequentemente rispetto ai database di calcolo con provisioning. Questo comportamento è importante per controllare i costi in serverless e può influire sulle prestazioni.

#### <a name="cache-reclamation"></a>Recupero della cache

A differenza dei database di calcolo di cui è stato effettuato il provisioning, la memoria dalla cache SQL viene recuperata da un database serverless quando l'utilizzo della CPU o della cache attiva è basso.

- L'utilizzo attivo della cache è considerato basso quando le dimensioni totali delle voci della cache usate più di recente sono inferiori a una soglia per un periodo di tempo.
- Quando viene attivato il recupero della cache, le dimensioni della cache di destinazione vengono ridotte in modo incrementale a una frazione delle dimensioni precedenti e il recupero continua solo se l'utilizzo rimane basso.
- Quando si verifica il recupero della cache, i criteri per la selezione delle voci della cache da sfratto sono gli stessi criteri di selezione dei database di calcolo di cui è stato effettuato il provisioning quando la quantità di memoria è elevata.
- Le dimensioni della cache non vengono mai ridotte al di sotto del limite minimo di memoria, come definito da min vCores, che possono essere configurati.

Nei database di calcolo serverless e con provisioning è possibile che le voci della cache siano esenti se viene usata tutta la memoria disponibile.

Si noti che quando l'utilizzo della CPU è basso, l'utilizzo della cache attiva può rimanere elevato a seconda del modello di utilizzo e impedire il recupero della memoria.  Possono inoltre verificarsi ritardi aggiuntivi dopo l'arresto dell'attività dell'utente prima che si verifichi il recupero della memoria a causa di processi in background periodici che rispondono all'attività dell'utente precedente.  Ad esempio, le operazioni di eliminazione e le attività di pulizia QDS generano record fantasma contrassegnati per l'eliminazione, ma che non vengono eliminati fisicamente fino all'esecuzione del processo di pulizia fantasma che può comportare la lettura delle pagine di dati nella cache.

#### <a name="cache-hydration"></a>Idrazione della cache

La cache SQL aumenta man mano che i dati vengono recuperati dal disco nello stesso modo e con la stessa velocità dei database di cui è stato effettuato il provisioning. Quando il database è occupato, la cache può aumentare fino al limite massimo di memoria.

## <a name="auto-pausing-and-auto-resuming"></a>Sospensione automatica e ripresa automatica

### <a name="auto-pausing"></a>Sospensione automatica

La sospensione automatica viene attivata se tutte le condizioni seguenti sono vere per la durata del ritardo di sospensione automatica:

- Numero di sessioni = 0
- CPU = 0 per il carico di lavoro utente in esecuzione nel pool di utenti

Se si desidera, è disponibile un'opzione per disabilitare la sospensione automatica.

Le funzionalità seguenti non supportano la sospensione automatica, ma supportano il ridimensionamento automatico.  Se si usa una delle funzionalità seguenti, la sospensione automatica deve essere disabilitata e il database rimarrà online indipendentemente dalla durata dell'inattività del database:

- Replica geografica (replica geografica attiva e gruppi di failover automatico).
- Conservazione dei backup a lungo termine.
- Database di sincronizzazione usato nella sincronizzazione dati SQL.  A differenza dei database di sincronizzazione, i database hub e membri supportano la sospensione automatica.
- Alias DNS
- Database del processo usato nei processi elastici (anteprima).

La sospensione automatica viene temporaneamente impedita durante la distribuzione di alcuni aggiornamenti del servizio che richiedono che il database sia online.  In questi casi, la sospensione automatica viene nuovamente consentita al termine dell'aggiornamento del servizio.

### <a name="auto-resuming"></a>Ripresa automatica

La ripresa automatica viene attivata se una delle condizioni seguenti è vera in qualsiasi momento:

|Funzionalità|Trigger di ripresa automatica|
|---|---|
|Autenticazione e autorizzazione|Accedi|
|Rilevamento delle minacce|Abilitazione/disabilitazione delle impostazioni di rilevamento delle minacce a livello di database o di server.<br>Modifica delle impostazioni di rilevamento delle minacce a livello di database o di server.|
|Individuazione e classificazione dei dati|Aggiunta, modifica, eliminazione o visualizzazione delle etichette di riservatezza|
|Controllo|Visualizzazione dei record di controllo<br>Aggiornamento o visualizzazione dei criteri di controllo.|
|Maschera dati|Aggiunta, modifica, eliminazione o visualizzazione delle regole di maschera dati|
|Transparent Data Encryption|Visualizzazione dello stato o dello stato della crittografia dei dati trasparente|
|Valutazione della vulnerabilità|Analisi ad hoc e analisi periodiche, se abilitate|
|Esecuzione di query sulle prestazioni dell'archivio dati|Modifica o visualizzazione delle impostazioni dell'archivio query|
|Raccomandazioni per le prestazioni|Visualizzazione o applicazione di raccomandazioni sulle prestazioni|
|Ottimizzazione automatica|Applicazione e verifica delle raccomandazioni di ottimizzazione automatica, ad esempio l'indicizzazione automatica|
|Copia del database|Creare il database come copia.<br>Esportare in un file BACPAC.|
|Sincronizzazione dati SQL|Sincronizzazione tra database hub e membro che viene eseguita secondo un calendario configurabile o manualmente|
|Modifica di alcuni metadati del database|Aggiunta di nuovi tag di database.<br>Modifica di max vCores, min vCores o autopause delay.|
|SQL Server Management Studio (SSMS)|L'uso delle versioni di SSMS precedenti alla 18.1 e l'apertura di una nuova finestra di query per qualsiasi database nel server riprenderanno qualsiasi database sospeso automaticamente nello stesso server. Questo comportamento non si verifica se si usa SSMS versione 18.1 o successiva.|

Il monitoraggio, la gestione o altre soluzioni che eseguono una delle operazioni elencate sopra attiveranno la ripresa automatica.

La ripresa automatica viene attivata anche durante la distribuzione di alcuni aggiornamenti del servizio che richiedono che il database sia online.

### <a name="connectivity"></a>Connettività

Se un database serverless viene sospeso, il primo account di accesso riprenderà il database e restituirà un errore che indica che il database non è disponibile con codice di errore 40613. Dopo la ripresa del database è necessario ripetere l'accesso per stabilire la connettività. I client del database con la logica di ripetizione dei tentativi di connessione non devono essere modificati.

### <a name="latency"></a>Latenza

La latenza per la ripresa automatica e la sospensione automatica di un database serverless è in genere di 1 minuto per la ripresa automatica e di 1-10 minuti per la sospensione automatica.

### <a name="customer-managed-transparent-data-encryption-byok"></a>Customer Managed Transparent Data Encryption (BYOK)

Se si usa [la](transparent-data-encryption-byok-overview.md) crittografia dei dati trasparente gestita dal cliente e il database serverless viene sospeso automaticamente quando si verifica l'eliminazione o la revoca della chiave, il database rimane nello stato di sospensione automatica.  In questo caso, dopo la successiva ripresa del database, il database diventa inaccessibile entro circa 10 minuti.  Quando il database diventa inaccessibile, il processo di ripristino è lo stesso dei database di calcolo con provisioning.  Se il database serverless è online quando si verifica l'eliminazione o la revoca delle chiavi, anche il database diventa inaccessibile entro circa 10 minuti allo stesso modo dei database di calcolo di cui è stato effettuato il provisioning.

## <a name="onboarding-into-serverless-compute-tier"></a>Onboarding nel livello di calcolo serverless

La creazione di un nuovo database o lo spostamento di un database esistente in un livello di calcolo serverless segue lo stesso modello della creazione di un nuovo database nel livello di calcolo con provisioning e prevede i due passaggi seguenti.

1. Specificare l'obiettivo di servizio. L'obiettivo del servizio indica il livello di servizio, la generazione di hardware e il numero massimo di vCore. Per le opzioni degli obiettivi di servizio, vedere [Limiti delle risorse serverless](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)


2. Facoltativamente, specificare il numero minimo di vCore e il ritardo di riempimento automatico per modificare i valori predefiniti. La tabella seguente illustra i valori disponibili per questi parametri.

   |Parametro|Valori disponibili|Valore predefinito|
   |---|---|---|---|
   |Numero minimo di vCore|Dipende dal numero massimo di vCore configurati. Vedere [limiti delle risorse.](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)|0,5 vCore|
   |Ritardo di sospensione automatica|Minimo: 60 minuti (1 ora)<br>Massimo: 10080 minuti (7 giorni)<br>Incrementi: 10 minuti<br>Disabilita la sospensione automatica: -1|60 minuti|


### <a name="create-a-new-database-in-the-serverless-compute-tier"></a>Creare un nuovo database nel livello di calcolo serverless

Gli esempi seguenti creano un nuovo database nel livello di calcolo serverless.

#### <a name="use-the-azure-portal"></a>Usare il portale di Azure

Vedere [Avvio rapido: Creare un database singolo in database SQL di Azure usando il portale di Azure](single-database-create-quickstart.md).


#### <a name="use-powershell"></a>Usare PowerShell

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 --min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>Usare Transact-SQL (T-SQL)

Quando si usa T-SQL, i valori predefiniti vengono applicati per il numero minimo di vcore e il ritardo di attesa automatica.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

Per informazioni dettagliate, vedere [CREATE DATABASE.](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true)  

### <a name="move-a-database-from-the-provisioned-compute-tier-into-the-serverless-compute-tier"></a>Spostare un database dal livello di calcolo con provisioning al livello di calcolo serverless

Gli esempi seguenti spostano un database dal livello di calcolo con provisioning al livello di calcolo serverless.

#### <a name="use-powershell"></a>Usare PowerShell


```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```


#### <a name="use-transact-sql-t-sql"></a>Usare Transact-SQL (T-SQL)

Quando si usa T-SQL, i valori predefiniti vengono applicati per il numero minimo di vcore e il ritardo di sospensione automatica.

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

Per informazioni dettagliate, vedere [ALTER DATABASE.](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true)

### <a name="move-a-database-from-the-serverless-compute-tier-into-the-provisioned-compute-tier"></a>Spostare un database dal livello di calcolo serverless al livello di calcolo con provisioning

La procedura per spostare un database serverless in un livello di calcolo con provisioning è analoga a quella per spostare un database di calcolo con provisioning in un livello di calcolo serverless.

## <a name="modifying-serverless-configuration"></a>Modifica della configurazione serverless

### <a name="use-powershell"></a>Usare PowerShell

La modifica del numero massimo o minimo di vCore e del ritardo di interruzione automatica viene eseguita usando il [comando Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) in PowerShell usando gli argomenti `MaxVcore` , e `MinVcore` `AutoPauseDelayInMinutes` .

### <a name="use-the-azure-cli"></a>Usare l'interfaccia della riga di comando di Azure

La modifica del numero massimo o minimo di vCore e del ritardo di attesa automatica viene eseguita usando il [comando az sql db update](/cli/azure/sql/db#az-sql-db-update) nell'interfaccia della riga di comando di Azure usando gli argomenti , e `capacity` `min-capacity` `auto-pause-delay` .


## <a name="monitoring"></a>Monitoraggio

### <a name="resources-used-and-billed"></a>Risorse usate e fatturate

Le risorse di un database serverless sono incapsulate dalle entità del pacchetto dell'app, dell'istanza di SQL e del pool di risorse utente.

#### <a name="app-package"></a>Pacchetto dell'app

Il pacchetto app è il limite più esterno di gestione delle risorse per un database, indipendentemente dal fatto che il database si trovi in un livello di calcolo serverless o con provisioning. Il pacchetto dell'app contiene i servizi esterni e l'istanza di SQL che definiscono congiuntamente l'ambito di tutte le risorse utente e di sistema usate da un database nel database SQL. R e la ricerca full-text sono esempi di servizi esterni. L'istanza di SQL è in genere la voce più onerosa nell'utilizzo complessivo delle risorse nel pacchetto dell'app.

#### <a name="user-resource-pool"></a>Pool di risorse utente

Il pool di risorse utente è il limite più interno di gestione delle risorse per un database, indipendentemente dal fatto che il database si trovi in un livello di calcolo serverless o con provisioning. Il pool di risorse utente ha come ambito CPU e I/O per il carico di lavoro utente generato da query DDL, ad esempio query CREATE e ALTER e DML come SELECT, INSERT, UPDATE e DELETE. Queste domande rappresentano in genere la percentuale più consistente di utilizzo all'interno del pacchetto dell'app.

### <a name="metrics"></a>Metriche

Le metriche per il monitoraggio dell'utilizzo delle risorse del pacchetto dell'app e del pool di utenti di un database serverless sono elencate nella tabella seguente:

|Entità|Metrica|Descrizione|Unità|
|---|---|---|---|
|Pacchetto dell'app|app_cpu_percent|Percentuale del numero di vCore usati dall'app rispetto al numero massimo di vCore consentito per l'app.|Percentuale|
|Pacchetto dell'app|app_cpu_billed|Quantità di risorse di calcolo fatturata per l'app durante il periodo di riferimento. L'importo pagato durante questo periodo è dato dal prodotto di questa metrica per il prezzo unitario dei vCore. <br><br>I valori di questa metrica sono determinati dall'aggregazione nel tempo del numero massimo di CPU usate e dalla memoria usata al secondo. Se la quantità usata è inferiore a quella minima di cui è stato effettuato il provisioning in base al valore impostato per il numero minimo di vCore e la quantità minima di memoria, viene fatturata la quantità minima di cui è stato effettuato il provisioning.Per confrontare la quantità di CPU e di memoria ai fini della fatturazione, la memoria viene normalizzata in unità di vCore ridimensionando la quantità di memoria in GB in base a 3 GB per vCore.|Secondi per vCore|
|Pacchetto dell'app|app_memory_percent|Percentuale di memoria usata dall'app rispetto alla memoria massima consentita per l'app.|Percentuale|
|Pool di utenti|cpu_percent|Percentuale di vCore usati dal carico di lavoro utente rispetto al numero massimo di vCore consentiti per il carico di lavoro utente.|Percentuale|
|Pool di utenti|data_IO_percent|Percentuale di operazioni di I/O al secondo sui dati usate dal carico di lavoro utente rispetto al numero massimo di operazioni di I/O al secondo sui dati consentite per il carico di lavoro utente.|Percentuale|
|Pool di utenti|log_IO_percent|Percentuale di MB/s di log usati dal carico di lavoro utente rispetto al numero massimo di MB/s di log consentiti per il carico di lavoro utente.|Percentuale|
|Pool di utenti|workers_percent|Percentuale di ruoli di lavoro usati dal carico di lavoro utente rispetto al numero massimo di ruoli di lavoro consentiti per il carico di lavoro utente.|Percentuale|
|Pool di utenti|sessions_percent|Percentuale di sessioni usate dal carico di lavoro utente rispetto al numero massimo di sessioni consentite per il carico di lavoro utente.|Percentuale|

### <a name="pause-and-resume-status"></a>Stato di sospensione e ripresa

Nel portale di Azure lo stato del database è visualizzato nel riquadro della panoramica del server in cui sono elencati i database in esso contenuti. Lo stato del database è anche visualizzato nel riquadro della panoramica relativo al database.

Usare i comandi seguenti per eseguire query sullo stato di sospensione e ripresa di un database:

#### <a name="use-powershell"></a>Usare PowerShell

```powershell
Get-AzSqlDatabase -ResourceGroupName $resourcegroupname -ServerName $servername -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

#### <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```


## <a name="resource-limits"></a>Limiti delle risorse

Per i limiti delle risorse, vedere [Livello di calcolo serverless.](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)

## <a name="billing"></a>Fatturazione

La quantità di risorse di calcolo fatturata corrisponde alla quantità massima di CPU e di memoria usata ogni secondo. Se la quantità di CPU e di memoria usata è inferiore alla quantità minima del provisioning per ognuna, viene fatturata la quantità del provisioning. Per confrontare la quantità di CPU e di memoria ai fini della fatturazione, la memoria viene normalizzata in unità di vCore ridimensionando la quantità di memoria in GB in base a 3 GB per vCore.

- **Risorsa fatturata:** CPU e memoria
- **Importo fatturato:** vCore unit price * max (min vCores, vCores used, min memory GB * 1/3, memory GB used * 1/3) 
- **Frequenza di fatturazione:** al secondo

Il prezzo unitario vCore è il costo per vCore al secondo. Per i prezzi unitari in una determinata area, vedere la [pagina dei prezzi del database SQL di Azure](https://azure.microsoft.com/pricing/details/sql-database/single/).

La quantità di risorse di calcolo fatturata è esposta dalla metrica seguente:

- **Metrica**: app_cpu_billed (secondi per vCore)
- **Definizione**: massimo (numero minimo di vCore, numero di vCore usati, quantità minima di memoria in GB * 1/3, quantità di memoria in GB usata * 1/3)
- **Frequenza di creazione** report: al minuto

Questa quantità viene calcolata ogni secondo e aggregata in un minuto.

### <a name="minimum-compute-bill"></a>Fattura di calcolo minima

Se un database serverless viene sospeso, la fattura di calcolo è zero.  Se un database serverless non viene sospeso, la fattura di calcolo minima non è inferiore alla quantità di vCore in base al valore massimo (min vCores, min memory GB * 1/3).

Esempi:

- Si supponga che un database serverless non sia sospeso e configurato con 8 vCore max e 1 min vCore corrispondente a 3,0 GB di memoria minima.  La fattura di calcolo minima è quindi basata su max (1 vCore, 3,0 GB * 1 vCore/3 GB) = 1 vCore.
- Si supponga che un database serverless non sia sospeso e configurato con 4 vCore max e 0,5 min vCore corrispondenti a 2,1 GB di memoria minima.  La fattura di calcolo minima è quindi basata su max (0,5 vCore, 2,1 GB * 1 vCore/3 GB) = 0,7 vCore.

Il [database SQL di Azure calcolatore](https://azure.microsoft.com/pricing/calculator/?service=sql-database) dei prezzi per serverless può essere usato per determinare la quantità minima di memoria configurabile in base al numero di vCore max e min configurati.  Di norma, se il numero minimo di vCore configurati è maggiore di 0,5 vCore, la fattura di calcolo minima è indipendente dalla memoria minima configurata e basata solo sul numero di vCore minimi configurati.

### <a name="example-scenario"></a>Scenario di esempio

Si consideri un database serverless configurato con 1 min vCore e 4 vCore massimi.  Corrisponde a circa 3 GB di memoria minima e 12 GB di memoria massima.  Si supponga che il ritardo di sospensione automatica sia impostato su 6 ore e che il carico di lavoro del database sia attivo durante le prime 2 ore di un periodo di 24 ore e in caso contrario inattivo.    

In questo caso, il database viene fatturato per il calcolo e l'archiviazione durante le prime 8 ore.  Anche se il database è inattivo a partire dalla seconda ora, viene comunque fatturato per il calcolo nelle 6 ore successive in base al calcolo minimo di cui è stato effettuato il provisioning mentre il database è online.  Solo l'archiviazione viene fatturata durante il resto del periodo di 24 ore durante la sospensione del database.

Più precisamente, la fattura di calcolo in questo esempio viene calcolata come segue:

|Intervallo di tempo|vCore usati ogni secondo|GB usati ogni secondo|Dimensione di calcolo fatturata|Secondi vCore fatturati nell'intervallo di tempo|
|---|---|---|---|---|
|0:00-1:00|4|9|vCore usati|4 vCore * 3600 secondi = 14400 vCore secondi|
|1:00-2:00|1|12|Memoria usata|12 GB * 1/3 * 3600 secondi = 14400 vCore secondi|
|2:00-8:00|0|0|Numero minimo di memoria di cui è stato effettuato il provisioning|3 GB * 1/3 * 21600 secondi = 21600 vCore secondi|
|8:00-24:00|0|0|Nessun calcolo fatturato durante la sospensione|0 vCore secondi|
|Totale secondi vCore fatturati in 24 ore||||50400 vCore secondi|

Si supponga che il prezzo unitario di calcolo sia di $ 0,000145/vCore/secondo.  Il calcolo fatturato per questo periodo di 24 ore è quindi il prodotto del prezzo unitario di calcolo e dei secondi vCore fatturati: $0,000145/vCore/secondo * 50400 secondi vCore ~ $ 7,31

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Vantaggio Azure Hybrid e capacità riservata

Vantaggio Azure Hybrid (AHB) e di capacità riservata non si applicano al livello di calcolo serverless.

## <a name="available-regions"></a>Aree disponibili

Il livello di calcolo serverless è disponibile in tutto il mondo, ad eccezione delle aree seguenti: Cina orientale, Cina settentrionale, Germania centrale, Germania nord-orientale e US Gov centrale (Iowa).

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [Avvio rapido: Creare un database singolo nel database SQL di Azure usando il portale di Azure](single-database-create-quickstart.md).
- Per i limiti delle risorse, vedere [Limiti delle risorse del livello di calcolo serverless](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).
