---
title: Funzioni di sistema nei log di monitoraggio di Azure
description: Scrivere query personalizzate nei log di monitoraggio di Azure usando le funzioni di sistema
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: acb45e6ad0250a1f8d10377fdd509e40051f25b9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564909"
---
# <a name="system-functions-on-azure-monitor-logs"></a>Funzioni di sistema nei log di monitoraggio di Azure

Backup di Azure fornisce un set di funzioni, denominate funzioni di sistema o funzioni di soluzione, che sono disponibili per impostazione predefinita nelle aree di lavoro di Log Analytics (LA).
 
Queste funzioni operano sui dati nelle [tabelle di backup di Azure non elaborate](./backup-azure-reports-data-model.md) in la e restituiscono dati formattati che consentono di recuperare facilmente le informazioni di tutte le entità correlate al backup, usando query semplici. Gli utenti possono passare parametri a queste funzioni per filtrare i dati restituiti da queste funzioni. 

È consigliabile usare funzioni di sistema per eseguire query sui dati di backup nelle aree di lavoro di per LA creazione di report personalizzati, in quanto forniscono diversi vantaggi, come descritto nella sezione seguente.

## <a name="benefits-of-using-system-functions"></a>Vantaggi dell'utilizzo delle funzioni di sistema

* **Query più semplici**: l'utilizzo di funzioni consente di ridurre il numero di join necessari nelle query. Per impostazione predefinita, le funzioni restituiscono schemi "flat", che incorporano tutte le informazioni relative all'entità (istanza di backup, processo, insieme di credenziali e così via) su cui viene eseguita una query. Se, ad esempio, è necessario ottenere un elenco di processi di backup riusciti tramite il nome dell'elemento di backup e il contenitore associato, una semplice chiamata alla funzione **_AzureBackup_getJobs ()** fornirà tutte queste informazioni per ogni processo. D'altra parte, l'esecuzione diretta di query sulle tabelle non elaborate richiederebbe l'esecuzione di più join tra le tabelle [AddonAzureBackupJobs](./backup-azure-reports-data-model.md#addonazurebackupjobs) e [CoreAzureBackup](./backup-azure-reports-data-model.md#coreazurebackup) .

* **Transizione più liscia dall'evento di diagnostica legacy**: l'uso di funzioni di sistema consente di eseguire la transizione senza problemi dall' [evento di diagnostica legacy](./backup-azure-diagnostic-events.md#legacy-event) (AzureBackupReport in modalità AzureDiagnostics) agli [eventi specifici della risorsa](./backup-azure-diagnostic-events.md#diagnostics-events-available-for-azure-backup-users). Tutte le funzioni di sistema fornite da backup di Azure consentono di specificare un parametro che consente di scegliere se la funzione deve eseguire query sui dati solo dalle tabelle specifiche delle risorse o eseguire query sui dati della tabella legacy e delle tabelle specifiche delle risorse (con la deduplicazione dei record).
    * Se è stata eseguita correttamente la migrazione alle tabelle specifiche delle risorse, è possibile scegliere di escludere la tabella legacy dalla quale viene eseguita una query tramite la funzione.
    * Se si è attualmente in fase di migrazione e si dispone di alcuni dati nelle tabelle legacy richieste per l'analisi, è possibile scegliere di includere la tabella legacy. Quando la transizione è completa e non sono più necessari dati della tabella legacy, è possibile aggiornare semplicemente il valore del parametro passato alla funzione nelle query, per escludere la tabella legacy.
    * Se si sta ancora usando solo la tabella legacy, le funzioni funzioneranno comunque se si sceglie di includere la tabella legacy tramite lo stesso parametro. Tuttavia, si consiglia di [passare alle tabelle specifiche delle risorse](./backup-azure-diagnostic-events.md#steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace) al più presto.

* **Riduzione della possibilità di interruzioni delle query personalizzate**: se il servizio backup di Azure introduce miglioramenti allo schema delle tabelle la sottostante per supportare scenari di Reporting futuri, la definizione delle funzioni verrà aggiornata anche per tenere in considerazione le modifiche dello schema. Pertanto, se si utilizzano le funzioni di sistema per la creazione di query personalizzate, le query non verranno arrestate, anche in caso di modifiche nello schema sottostante delle tabelle.

> [!NOTE]
> Le funzioni di sistema vengono gestite da Microsoft e le relative definizioni non possono essere modificate dagli utenti. Se sono necessarie funzioni modificabili, è possibile creare [funzioni salvate](../azure-monitor/logs/functions.md) in la.

## <a name="types-of-system-functions-offered-by-azure-backup"></a>Tipi di funzioni di sistema offerte da backup di Azure

* **Funzioni di base**: si tratta di funzioni che consentono di eseguire query su qualsiasi entità di backup di Azure chiave, ad esempio istanze di backup, insiemi di credenziali, criteri, processi e entità di fatturazione. Ad esempio, la funzione **_AzureBackup_getBackupInstances** restituisce un elenco di tutte le istanze di backup presenti nell'ambiente al giorno dell'ultimo completamento (in UTC). I parametri e lo schema restituito per ognuna di queste funzioni di base sono riepilogati di seguito in questo articolo.

* **Funzioni di tendenza**: si tratta di funzioni che restituiscono i record cronologici per le entità correlate al backup, ad esempio le istanze di backup, i gruppi di fatturazione, e consentono di ottenere informazioni sulle tendenze giornaliere, settimanali e mensili per le metriche chiave (ad esempio, conteggio, archiviazione utilizzata) relative a tali entità. I parametri e lo schema restituito per ognuna di queste funzioni di tendenza sono riepilogati di seguito in questo articolo.

> [!NOTE]
> Attualmente, le funzioni di sistema restituiscono i dati fino al giorno dell'ultimo completamento (in UTC). Non vengono restituiti i dati per il giorno parziale corrente. Pertanto, se si desidera recuperare i record per il giorno corrente, sarà necessario utilizzare le tabelle di LA non elaborate.


## <a name="list-of-system-functions"></a>Elenco delle funzioni di sistema

### <a name="core-functions"></a>Funzioni di sistema

#### <a name="_azurebackup_getvaults"></a>_AzureBackup_GetVaults ()

Questa funzione restituisce l'elenco di tutti gli insiemi di credenziali dei servizi di ripristino nell'ambiente Azure associati all'area di lavoro LA.

**Parametri**

| **Nome parametro** | **Descrizione** | **Obbligatorio?** | **Valore di esempio** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Usare questo parametro insieme al parametro RangeEnd solo se è necessario recuperare tutti i record correlati all'insieme di credenziali nel periodo di tempo da RangeStart a RangeEnd. Per impostazione predefinita, il valore di RangeStart e RangeEnd è null, che farà in modo che la funzione recuperi solo il record più recente per ogni insieme di credenziali. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Usare questo parametro insieme al parametro RangeStart solo se è necessario recuperare tutti i record correlati all'insieme di credenziali nel periodo di tempo da RangeStart a RangeEnd. Per impostazione predefinita, il valore di RangeStart e RangeEnd è null, che farà in modo che la funzione recuperi solo il record più recente per ogni insieme di credenziali. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Utilizzare questo parametro per filtrare l'output della funzione per un determinato set di sottoscrizioni in cui esistono dati di backup. Specificando un elenco delimitato da virgole di ID sottoscrizione come parametro di questa funzione, è possibile recuperare solo gli insiemi di credenziali presenti nelle sottoscrizioni specificate. Per impostazione predefinita, il valore di questo parametro è' *', che rende la funzione la ricerca di record in tutte le sottoscrizioni. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Utilizzare questo parametro per filtrare l'output della funzione per un determinato set di aree in cui esistono dati di backup. Specificando un elenco delimitato da virgole di aree come parametro per questa funzione, è possibile recuperare solo gli insiemi di credenziali che si trovano nelle aree specificate. Per impostazione predefinita, il valore di questo parametro è' *', che rende la funzione la ricerca di record in tutte le aree. | N | "eastus, westus"|
| VaultList    |Usare questo parametro per filtrare l'output della funzione per un determinato set di insiemi di credenziali. Specificando un elenco delimitato da virgole di nomi di insiemi di credenziali come parametro per questa funzione, è possibile recuperare i record che riguardano solo gli insiemi di credenziali specificati. Per impostazione predefinita, il valore di questo parametro è' *', che rende la funzione la ricerca di record in tutti gli insiemi di credenziali. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Usare questo parametro per filtrare l'output della funzione in record relativi a un determinato tipo di insieme di credenziali. Attualmente l'unico tipo di insieme di credenziali supportato è "Microsoft. RecoveryServices/Vaults", che è il valore predefinito di questo parametro | N | "Microsoft. RecoveryServices/Vaults"|
| ExcludeLegacyEvent  | Utilizzare questo parametro per scegliere se eseguire una query sui dati nella tabella AzureDiagnostics legacy. Se il valore di questo parametro è false, la funzione esegue una query sui dati della tabella AzureDiagnostics e delle tabelle specifiche delle risorse. Se il valore di questo parametro è true, la funzione esegue una query sui dati solo dalle tabelle specifiche delle risorse. Il valore predefinito è true. | N | true |

**Campi restituiti**

| **Nome campo** | **Descrizione** |
| -------------- | --------------- |
| UniqueId | Chiave primaria che indica l'ID univoco dell'insieme di credenziali |
| Id | ID Azure Resource Manager (ARM) dell'insieme di credenziali |
| Nome | Nome dell'insieme di credenziali |
| SubscriptionId | ID della sottoscrizione in cui è presente l'insieme di credenziali |
| Location | Località in cui è presente l'insieme di credenziali |
| VaultStore_StorageReplicationType | Tipo di replica di archiviazione associato all'insieme di credenziali |
| Tag | Tag dell'insieme di credenziali |
| TimeGenerated | Timestamp del record |
| Tipo |  Tipo di insieme di credenziali, ovvero "Microsoft. RecoveryServices/Vaults"|

#### <a name="_azurebackup_getpolicies"></a>_AzureBackup_GetPolicies ()

Questa funzione restituisce l'elenco dei criteri di backup usati nell'ambiente Azure insieme a informazioni dettagliate su ogni criterio, ad esempio il tipo di origine dati, il tipo di replica di archiviazione e così via.

**Parametri**

| **Nome parametro** | **Descrizione** | **Obbligatorio?** | **Valore di esempio** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Usare questo parametro insieme al parametro RangeStart solo se è necessario recuperare tutti i record correlati ai criteri nel periodo di tempo da RangeStart a RangeEnd. Per impostazione predefinita, il valore di RangeStart e RangeEnd è null, che farà in modo che la funzione recuperi solo il record più recente per ogni criterio. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Usare questo parametro insieme al parametro RangeStart solo se è necessario recuperare tutti i record correlati ai criteri nel periodo di tempo da RangeStart a RangeEnd. Per impostazione predefinita, il valore di RangeStart e RangeEnd è null, che farà in modo che la funzione recuperi solo il record più recente per ogni criterio. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Utilizzare questo parametro per filtrare l'output della funzione per un determinato set di sottoscrizioni in cui esistono dati di backup. La specifica di un elenco delimitato da virgole di ID sottoscrizione come parametro di questa funzione consente di recuperare solo i criteri presenti nelle sottoscrizioni specificate. Per impostazione predefinita, il valore di questo parametro è' *', che rende la funzione la ricerca di record in tutte le sottoscrizioni. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Utilizzare questo parametro per filtrare l'output della funzione per un determinato set di aree in cui esistono dati di backup. Specificando un elenco delimitato da virgole di aree come parametro di questa funzione, è possibile recuperare solo i criteri presenti nelle aree specificate. Per impostazione predefinita, il valore di questo parametro è' *', che rende la funzione la ricerca di record in tutte le aree. | N | "eastus, westus"|
| VaultList    |Usare questo parametro per filtrare l'output della funzione per un determinato set di insiemi di credenziali. Specificando un elenco delimitato da virgole di nomi di insiemi di credenziali come parametro di questa funzione, è possibile recuperare i record dei criteri relativi solo agli insiemi di credenziali specificati. Per impostazione predefinita, il valore di questo parametro è' *', che fa in modo che la funzione cerchi record di criteri in tutti gli insiemi di credenziali. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Usare questo parametro per filtrare l'output della funzione in record relativi a un determinato tipo di insieme di credenziali. Attualmente l'unico tipo di insieme di credenziali supportato è "Microsoft. RecoveryServices/Vaults", che è il valore predefinito di questo parametro. | N | "Microsoft. RecoveryServices/Vaults"|
| ExcludeLegacyEvent  | Utilizzare questo parametro per scegliere se eseguire una query sui dati nella tabella AzureDiagnostics legacy. Se il valore di questo parametro è false, la funzione esegue una query sui dati della tabella AzureDiagnostics e delle tabelle specifiche delle risorse. Se il valore di questo parametro è true, la funzione esegue una query sui dati solo dalle tabelle specifiche delle risorse. Il valore predefinito è true. | N | true |
| BackupSolutionList | Usare questo parametro per filtrare l'output della funzione per un determinato set di soluzioni di backup usate nell'ambiente Azure. Ad esempio, se si specifica "backup macchina virtuale di Azure, SQL in backup VM di Azure, DPM" come valore di questo parametro, la funzione restituisce solo i record correlati agli elementi di cui è stato eseguito il backup tramite il backup delle macchine virtuali di Azure, SQL in backup di VM di Azure o DPM in backup di Azure. Per impostazione predefinita, il valore di questo parametro è' *', che fa sì che la funzione restituisca record relativi a tutte le soluzioni di backup supportate dai report di backup (i valori supportati sono "backup della macchina virtuale di Azure", "SQL nel backup di VM di Azure", "SAP HANA nel backup di macchine virtuali di Azure", "backup di archiviazione di Azure (File di Azure)", "agente di backup di Azure", "DPM", "server di Backup di Azure" o una combinazione di uno o più valori separati da virgole. | N | "Backup di macchine virtuali di Azure, SQL in backup di VM di Azure, DPM, agente di backup di Azure" |

**Campi restituiti**

| **Nome campo** | **Descrizione** |
| -------------- | --------------- |
| UniqueId | Chiave primaria che indica l'ID univoco del criterio |
| Id | ID Azure Resource Manager (ARM) del criterio |
| Nome | Nome del criterio |
| Soluzione di backup | Soluzione di backup a cui sono associati i criteri. Ad esempio, backup di macchine virtuali di Azure, SQL in backup di macchine virtuali di Azure e così via. |
| TimeGenerated | Timestamp del record |
| VaultUniqueId | Chiave esterna che fa riferimento all'insieme di credenziali associato al criterio |
| VaultResourceId | ID Azure Resource Manager (ARM) dell'insieme di credenziali associato ai criteri |
| VaultName | Nome dell'insieme di credenziali associato al criterio |
| VaultTags | Tag dell'insieme di credenziali associato al criterio |
| VaultLocation | Percorso dell'insieme di credenziali associato al criterio |
| VaultSubscriptionId | ID sottoscrizione dell'insieme di credenziali associato al criterio |
| VaultStore_StorageReplicationType | Tipo di replica di archiviazione dell'insieme di credenziali associato ai criteri |
| VaultType | Tipo di insieme di credenziali, ovvero "Microsoft. RecoveryServices/Vaults" |
| ExtendedProperties | Proprietà aggiuntive del criterio |

#### <a name="_azurebackup_getjobs"></a>_AzureBackup_GetJobs ()

Questa funzione restituisce un elenco di tutti i processi correlati al backup e al ripristino attivati in un intervallo di tempo specificato, insieme a informazioni dettagliate su ogni processo, ad esempio lo stato del processo, la durata del processo, i dati trasferiti e così via.

**Parametri**

| **Nome parametro** | **Descrizione** | **Obbligatorio?** | **Valore di esempio** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Usare questo parametro insieme al parametro RangeEnd per recuperare l'elenco di tutti i processi avviati nel periodo di tempo da RangeStart a RangeEnd. | S | "2021-03-03 00:00:00" |
| RangeEnd     | Usare questo parametro insieme al parametro RangeStart per recuperare l'elenco di tutti i processi avviati nel periodo di tempo da RangeStart a RangeEnd. | S |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Utilizzare questo parametro per filtrare l'output della funzione per un determinato set di sottoscrizioni in cui esistono dati di backup. Specificando un elenco delimitato da virgole di ID sottoscrizione come parametro di questa funzione, è possibile recuperare solo i processi associati agli insiemi di credenziali nelle sottoscrizioni specificate. Per impostazione predefinita, il valore di questo parametro è' *', che rende la funzione la ricerca di record in tutte le sottoscrizioni. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Utilizzare questo parametro per filtrare l'output della funzione per un determinato set di aree in cui esistono dati di backup. Specificando un elenco delimitato da virgole di aree come parametro per questa funzione, è possibile recuperare solo i processi associati agli insiemi di credenziali nelle aree specificate. Per impostazione predefinita, il valore di questo parametro è' *', che rende la funzione la ricerca di record in tutte le aree. | N | "eastus, westus"|
| VaultList    | Usare questo parametro per filtrare l'output della funzione per un determinato set di insiemi di credenziali. Specificando un elenco delimitato da virgole di nomi di insiemi di credenziali come parametro per questa funzione, è possibile recuperare i processi che riguardano solo gli insiemi di credenziali specificati. Per impostazione predefinita, il valore di questo parametro è' *', che fa in modo che la funzione cerchi processi in tutti gli insiemi di credenziali. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Usare questo parametro per filtrare l'output della funzione in record relativi a un determinato tipo di insieme di credenziali. Attualmente l'unico tipo di insieme di credenziali supportato è "Microsoft. RecoveryServices/Vaults", che è il valore predefinito di questo parametro. | N | "Microsoft. RecoveryServices/Vaults"|
| ExcludeLegacyEvent  | Utilizzare questo parametro per scegliere se eseguire una query sui dati nella tabella AzureDiagnostics legacy. Se il valore di questo parametro è false, la funzione esegue una query sui dati della tabella AzureDiagnostics e delle tabelle specifiche delle risorse. Se il valore di questo parametro è true, la funzione esegue una query sui dati solo dalle tabelle specifiche delle risorse. Il valore predefinito è true. | N | true |
| BackupSolutionList | Usare questo parametro per filtrare l'output della funzione per un determinato set di soluzioni di backup usate nell'ambiente Azure. Ad esempio, se si specifica "backup macchina virtuale di Azure, SQL in backup VM di Azure, DPM" come valore di questo parametro, la funzione restituisce solo i record correlati agli elementi di cui è stato eseguito il backup tramite il backup delle macchine virtuali di Azure, SQL in backup di VM di Azure o DPM in backup di Azure. Per impostazione predefinita, il valore di questo parametro è' *', che fa sì che la funzione restituisca record relativi a tutte le soluzioni di backup supportate dai report di backup (i valori supportati sono "backup della macchina virtuale di Azure", "SQL nel backup di VM di Azure", "SAP HANA nel backup di macchine virtuali di Azure", "backup di archiviazione di Azure (File di Azure)", "agente di backup di Azure", "DPM", "server di Backup di Azure" o una combinazione di uno o più valori separati da virgole. | N | "Backup di macchine virtuali di Azure, SQL in backup di VM di Azure, DPM, agente di backup di Azure" |
| JobOperationList | Usare questo parametro per filtrare l'output della funzione per un tipo specifico di processo. Ad esempio, backup o Restore. Per impostazione predefinita, il valore di questo parametro è "*", che rende la funzione la ricerca dei processi di backup e ripristino. | N | Backup | 
| JobStatusList | Utilizzare questo parametro per filtrare l'output della funzione per uno stato di processo specifico. Ad esempio, Completed, Failed e così via. Per impostazione predefinita, il valore di questo parametro è "*", che fa in modo che la funzione cerchi tutti i processi indipendentemente dallo stato. | N | "Failed, CompletedWithWarnings" |
| JobFailureCodeList | Utilizzare questo parametro per filtrare l'output della funzione per un codice di errore specifico. Per impostazione predefinita, il valore di questo parametro è "*", che fa in modo che la funzione cerchi tutti i processi indipendentemente dal codice di errore. | N | "Esito positivo"
| DatasourceSetName | Utilizzare questo parametro per filtrare l'output della funzione in una determinata risorsa padre. Ad esempio, per restituire SQL nelle istanze di backup di VM di Azure appartenenti alla macchina virtuale "testvm", specificare _testvm_ come valore di questo parametro. Per impostazione predefinita, il valore è "*", che rende la funzione la ricerca di record in tutte le istanze di backup. | N | testvm |
| BackupInstanceName | Utilizzare questo parametro per cercare i processi in una particolare istanza di backup in base al nome. Per impostazione predefinita, il valore è "*", che rende la funzione la ricerca di record in tutte le istanze di backup. | N | testvm |
| ExcludeLog | Usare questo parametro per escludere i processi di log restituiti dalla funzione (aiuta a eseguire le query). Per impostazione predefinita, il valore di questo parametro è true, che fa sì che la funzione escluda i processi di log. | N | true


**Campi restituiti**

| **Nome campo** | **Descrizione** |
| -------------- | --------------- |
| UniqueId | Chiave primaria che indica l'ID univoco del processo |
| OperationCategory | Categoria dell'operazione eseguita. Ad esempio, backup, Restore |
| Operazione | Dettagli dell'operazione eseguita. Ad esempio, log (per il backup del log)|
| Stato | Stato del processo. Ad esempio, Completed, failed, CompletedWithWarnings |
| ErrorTitle | Codice di errore del processo |
| StartTime | Data e ora di inizio del processo |
| DurationInSecs | Durata del processo in secondi |
| DataTransferredInMBs | Dati trasferiti dal processo in MB |
| RestoreJobRPDateTime | Data e ora in cui è stato creato il punto di ripristino da ripristinare |
| RestoreJobRPLocation | Il percorso in cui è stato archiviato il punto di ripristino da ripristinare |
| BackupInstanceUniqueId | Chiave esterna che fa riferimento all'istanza di backup associata al processo |
| BackupInstanceId | ID Azure Resource Manager (ARM) dell'istanza di backup associata al processo |
| BackupInstanceFriendlyName | Nome dell'istanza di backup associata al processo |
| DatasourceResourceId | ID Azure Resource Manager (ARM) dell'origine dati sottostante associata al processo. Ad esempio, Azure Resource Manager (ARM) ID della macchina virtuale |
| DatasourceFriendlyName | Nome descrittivo dell'origine dati sottostante associata al processo |
| DatasourceType | Tipo di origine dati associata al processo. Ad esempio, "Microsoft. Compute/virtualMachines" |
| BackupSolution | Soluzione di backup a cui è associato il processo. Ad esempio, backup di macchine virtuali di Azure, SQL in backup di macchine virtuali di Azure e così via. |
| DatasourceSetResourceId | Azure Resource Manager (ARM) ID della risorsa padre dell'origine dati (laddove applicabile). Ad esempio, per un'origine dati VM SQL in Azure, questo campo conterrà l'ID Azure Resource Manager (ARM) della macchina virtuale in cui è presente il database SQL |
| DatasourceSetType | Tipo di risorsa padre dell'origine dati (laddove applicabile). Per un SAP HANA nell'origine dati della macchina virtuale di Azure, ad esempio, questo campo sarà Microsoft. Compute/virtualMachines poiché la risorsa padre è una macchina virtuale di Azure |
| VaultResourceId | ID Azure Resource Manager (ARM) dell'insieme di credenziali associato al processo |
| VaultUniqueId | Chiave esterna che fa riferimento all'insieme di credenziali associato al processo |
| VaultName | Nome dell'insieme di credenziali associato al processo |
| VaultTags | Tag dell'insieme di credenziali associato al processo |
| VaultSubscriptionId | ID sottoscrizione dell'insieme di credenziali associato al processo |
| VaultLocation | Percorso dell'insieme di credenziali associato al processo |
| VaultStore_StorageReplicationType | Tipo di replica di archiviazione dell'insieme di credenziali associato al processo |
| VaultType | Tipo di insieme di credenziali, ovvero "Microsoft. RecoveryServices/Vaults" |
| TimeGenerated | Timestamp del record |

#### <a name="_azurebackup_getbackupinstances"></a>_AzureBackup_GetBackupInstances ()

Questa funzione restituisce l'elenco delle istanze di backup associate agli insiemi di credenziali dei servizi di ripristino, insieme a informazioni dettagliate su ogni istanza di backup, ad esempio l'utilizzo della memoria cloud, i criteri associati e così via.

**Parametri**

| **Nome parametro** | **Descrizione** | **Obbligatorio?** | **Valore di esempio** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Usare questo parametro insieme al parametro RangeEnd solo se è necessario recuperare tutti i record correlati all'istanza di backup nel periodo di tempo da RangeStart a RangeEnd. Per impostazione predefinita, il valore di RangeStart e RangeEnd è null, che farà in modo che la funzione recuperi solo il record più recente per ogni istanza di backup. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Usare questo parametro insieme al parametro RangeStart solo se è necessario recuperare tutti i record correlati all'istanza di backup nel periodo di tempo da RangeStart a RangeEnd. Per impostazione predefinita, il valore di RangeStart e RangeEnd è null, che farà in modo che la funzione recuperi solo il record più recente per ogni istanza di backup. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Utilizzare questo parametro per filtrare l'output della funzione per un determinato set di sottoscrizioni in cui esistono dati di backup. La specifica di un elenco delimitato da virgole di ID sottoscrizione come parametro di questa funzione consente di recuperare solo le istanze di backup presenti nelle sottoscrizioni specificate. Per impostazione predefinita, il valore di questo parametro è' *', che rende la funzione la ricerca di record in tutte le sottoscrizioni. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Utilizzare questo parametro per filtrare l'output della funzione per un determinato set di aree in cui esistono dati di backup. La specifica di un elenco delimitato da virgole di aree come parametro per questa funzione consente di recuperare solo le istanze di backup presenti nelle aree specificate. Per impostazione predefinita, il valore di questo parametro è' *', che rende la funzione la ricerca di record in tutte le aree. | N | "eastus, westus"|
| VaultList    |Usare questo parametro per filtrare l'output della funzione per un determinato set di insiemi di credenziali. Specificando un elenco delimitato da virgole di nomi di insiemi di credenziali come parametro di questa funzione, è possibile recuperare i record delle istanze di backup che riguardano solo gli insiemi di credenziali specificati. Per impostazione predefinita, il valore di questo parametro è' *', che fa in modo che la funzione cerchi record di istanze di backup in tutti gli insiemi di credenziali. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Usare questo parametro per filtrare l'output della funzione in record relativi a un determinato tipo di insieme di credenziali. Attualmente l'unico tipo di insieme di credenziali supportato è "Microsoft. RecoveryServices/Vaults", che è il valore predefinito di questo parametro. | N | "Microsoft. RecoveryServices/Vaults"|
| ExcludeLegacyEvent  | Utilizzare questo parametro per scegliere se eseguire una query sui dati nella tabella AzureDiagnostics legacy. Se il valore di questo parametro è false, la funzione esegue una query sui dati della tabella AzureDiagnostics e delle tabelle specifiche delle risorse. Se il valore di questo parametro è true, la funzione esegue una query sui dati solo dalle tabelle specifiche delle risorse. Il valore predefinito è true. | N | true |
| BackupSolutionList | Usare questo parametro per filtrare l'output della funzione per un determinato set di soluzioni di backup usate nell'ambiente Azure. Ad esempio, se si specifica "backup macchina virtuale di Azure, SQL in backup VM di Azure, DPM" come valore di questo parametro, la funzione restituisce solo i record correlati agli elementi di cui è stato eseguito il backup tramite il backup delle macchine virtuali di Azure, SQL in backup di VM di Azure o DPM in backup di Azure. Per impostazione predefinita, il valore di questo parametro è' *', che fa sì che la funzione restituisca record relativi a tutte le soluzioni di backup supportate dai report di backup (i valori supportati sono "backup della macchina virtuale di Azure", "SQL nel backup di VM di Azure", "SAP HANA nel backup di macchine virtuali di Azure", "backup di archiviazione di Azure (File di Azure)", "agente di backup di Azure", "DPM", "server di Backup di Azure" o una combinazione di uno o più valori separati da virgole. | N | "Backup di macchine virtuali di Azure, SQL in backup di VM di Azure, DPM, agente di backup di Azure" |
| ProtectionInfoList | Utilizzare questo parametro per scegliere se includere solo le istanze di backup attivamente protette o includere anche le istanze per le quali la protezione è stata arrestata e le istanze per le quali è in sospeso il backup iniziale. I valori supportati sono "Protected", "Protected o protectionstopped", "InitialBackupPending" o una combinazione di uno o più valori separati da virgole. Per impostazione predefinita, il valore è "*", che fa in modo che la funzione cerchi tutte le istanze di backup indipendentemente dai dettagli della protezione. | N | Protetto |
| DatasourceSetName | Utilizzare questo parametro per filtrare l'output della funzione in una determinata risorsa padre. Ad esempio, per restituire SQL nelle istanze di backup di VM di Azure appartenenti alla macchina virtuale "testvm", specificare _testvm_ come valore di questo parametro. Per impostazione predefinita, il valore è "*", che rende la funzione la ricerca di record in tutte le istanze di backup. | N | testvm |
| BackupInstanceName | Utilizzare questo parametro per cercare una specifica istanza di backup in base al nome. Per impostazione predefinita, il valore è "*", che fa sì che la funzione cerchi tutte le istanze di backup. | N | testvm |
| DisplayAllFields | Utilizzare questo parametro per scegliere se recuperare solo un subset dei campi restituiti dalla funzione. Se il valore di questo parametro è false, la funzione Elimina le informazioni relative all'archiviazione e al punto di conservazione dall'output della funzione. Questa operazione è utile se si utilizza questa funzione come passaggio intermedio in una query di dimensioni maggiori ed è necessario ottimizzare le prestazioni della query eliminando le colonne non necessarie per l'analisi. Per impostazione predefinita, il valore di questo parametro è true, che fa sì che la funzione restituisca tutti i campi relativi all'istanza di backup. | N | true |

**Campi restituiti**

| **Nome campo** | **Descrizione** |
| -------------- | --------------- |
| UniqueId | Chiave primaria che indica l'ID univoco dell'istanza di backup |
| Id | ID Azure Resource Manager (ARM) dell'istanza di backup |
| FriendlyName | Nome descrittivo dell'istanza di backup |
| ProtectionInfo | Informazioni sulle impostazioni di protezione dell'istanza di backup. Ad esempio, protezione configurata, protezione arrestata, backup iniziale in sospeso |
| LatestRecoveryPoint | Data e ora del punto di ripristino più recente associato all'istanza di backup |
| OldestRecoveryPoint | Data e ora del punto di ripristino meno recente associato all'istanza di backup |
| SourceSizeInMBs | Dimensioni front-end dell'istanza di backup in MB |
| VaultStore_StorageConsumptionInMBs | Totale spazio di archiviazione cloud utilizzato dall'istanza di backup nell'insieme di credenziali-livello standard |
| DataSourceFriendlyName | Nome descrittivo dell'origine dati corrispondente all'istanza di backup |
| BackupSolution | Soluzione di backup a cui è associata l'istanza di backup. Ad esempio, backup di macchine virtuali di Azure, SQL in backup di macchine virtuali di Azure e così via. |
| DatasourceType | Tipo di origine dati corrispondente all'istanza di backup. Ad esempio, "Microsoft. Compute/virtualMachines" |
| DatasourceResourceId | ID Azure Resource Manager (ARM) dell'origine dati sottostante corrispondente all'istanza di backup. Ad esempio, Azure Resource Manager (ARM) ID della macchina virtuale |
| DatasourceSetFriendlyName | Nome descrittivo della risorsa padre dell'origine dati (laddove applicabile). Ad esempio, per un'origine dati VM SQL in Azure, questo campo conterrà il nome della macchina virtuale in cui è presente il database SQL |
| DatasourceSetResourceId | Azure Resource Manager (ARM) ID della risorsa padre dell'origine dati (laddove applicabile). Ad esempio, per un'origine dati VM SQL in Azure, questo campo conterrà l'ID Azure Resource Manager (ARM) della macchina virtuale in cui è presente il database SQL |
| DatasourceSetType | Tipo di risorsa padre dell'origine dati (laddove applicabile). Per un SAP HANA nell'origine dati della macchina virtuale di Azure, ad esempio, questo campo sarà Microsoft. Compute/virtualMachines poiché la risorsa padre è una macchina virtuale di Azure |
| PolicyName | Nome dei criteri associati all'istanza di backup |
| PolicyUniqueId | Chiave esterna che fa riferimento ai criteri associati all'istanza di backup  |
| PolicyId | ID Azure Resource Manager (ARM) del criterio associato all'istanza di backup |
| VaultResourceId | ID Azure Resource Manager (ARM) dell'insieme di credenziali associato all'istanza di backup |
| VaultUniqueId | Chiave esterna che fa riferimento all'insieme di credenziali associato all'istanza di backup |
| VaultName | Nome dell'insieme di credenziali associato all'istanza di backup |
| VaultTags | Tag dell'insieme di credenziali associato all'istanza di backup |
| VaultSubscriptionId | ID sottoscrizione dell'insieme di credenziali associato all'istanza di backup |
| VaultLocation | Percorso dell'insieme di credenziali associato all'istanza di backup |
| VaultStore_StorageReplicationType | Tipo di replica di archiviazione dell'insieme di credenziali associato all'istanza di backup |
| VaultType | Tipo di insieme di credenziali, ovvero "Microsoft. RecoveryServices/Vaults" |
| TimeGenerated | Timestamp del record |

#### <a name="_azurebackup_getbillinggroups"></a>_AzureBackup_GetBillingGroups ()

Questa funzione restituisce un elenco di tutte le entità di fatturazione correlate al backup (gruppi di fatturazione) insieme alle informazioni sui componenti di fatturazione principali, ad esempio le dimensioni del front-end e l'archiviazione cloud totale.

**Parametri**

| **Nome parametro** | **Descrizione** | **Obbligatorio?** | **Valore di esempio** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Usare questo parametro insieme al parametro RangeEnd solo se è necessario recuperare tutti i record correlati al gruppo di fatturazione nel periodo di tempo da RangeStart a RangeEnd. Per impostazione predefinita, il valore di RangeStart e RangeEnd è null, che farà in modo che la funzione recuperi solo il record più recente per ogni gruppo di fatturazione. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Usare questo parametro insieme al parametro RangeStart solo se è necessario recuperare tutti i record correlati al gruppo di fatturazione nel periodo di tempo da RangeStart a RangeEnd. Per impostazione predefinita, il valore di RangeStart e RangeEnd è null, che farà in modo che la funzione recuperi solo il record più recente per ogni gruppo di fatturazione. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Utilizzare questo parametro per filtrare l'output della funzione per un determinato set di sottoscrizioni in cui esistono dati di backup. La specifica di un elenco delimitato da virgole di ID sottoscrizione come parametro di questa funzione consente di recuperare solo i gruppi di fatturazione presenti nelle sottoscrizioni specificate. Per impostazione predefinita, il valore di questo parametro è' *', che rende la funzione la ricerca di record in tutte le sottoscrizioni. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Utilizzare questo parametro per filtrare l'output della funzione per un determinato set di aree in cui esistono dati di backup. Specificando un elenco delimitato da virgole di aree come parametro di questa funzione, è possibile recuperare solo i gruppi di fatturazione presenti nelle aree specificate. Per impostazione predefinita, il valore di questo parametro è' *', che rende la funzione la ricerca di record in tutte le aree. | N | "eastus, westus"|
| VaultList    |Usare questo parametro per filtrare l'output della funzione per un determinato set di insiemi di credenziali. Specificando un elenco delimitato da virgole di nomi di insiemi di credenziali come parametro di questa funzione, è possibile recuperare i record delle istanze di backup che riguardano solo gli insiemi di credenziali specificati. Per impostazione predefinita, il valore di questo parametro è' *', che fa in modo che la funzione cerchi record dei gruppi di fatturazione in tutti gli insiemi di credenziali. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Usare questo parametro per filtrare l'output della funzione in record relativi a un determinato tipo di insieme di credenziali. Attualmente l'unico tipo di insieme di credenziali supportato è "Microsoft. RecoveryServices/Vaults", che è il valore predefinito di questo parametro. | N | "Microsoft. RecoveryServices/Vaults"|
| ExcludeLegacyEvent  | Utilizzare questo parametro per scegliere se eseguire una query sui dati nella tabella AzureDiagnostics legacy. Se il valore di questo parametro è false, la funzione esegue una query sui dati della tabella AzureDiagnostics e delle tabelle specifiche delle risorse. Se il valore di questo parametro è true, la funzione esegue una query sui dati solo dalle tabelle specifiche delle risorse. Il valore predefinito è true. | N | true |
| BackupSolutionList | Usare questo parametro per filtrare l'output della funzione per un determinato set di soluzioni di backup usate nell'ambiente Azure. Ad esempio, se si specifica "backup macchina virtuale di Azure, SQL in backup VM di Azure, DPM" come valore di questo parametro, la funzione restituisce solo i record correlati agli elementi di cui è stato eseguito il backup tramite il backup delle macchine virtuali di Azure, SQL in backup di VM di Azure o DPM in backup di Azure. Per impostazione predefinita, il valore di questo parametro è' *', che fa sì che la funzione restituisca record relativi a tutte le soluzioni di backup supportate dai report di backup (i valori supportati sono "backup della macchina virtuale di Azure", "SQL nel backup di VM di Azure", "SAP HANA nel backup di macchine virtuali di Azure", "backup di archiviazione di Azure (File di Azure)", "agente di backup di Azure", "DPM", "server di Backup di Azure" o una combinazione di uno o più valori separati da virgole. | N | "Backup di macchine virtuali di Azure, SQL in backup di VM di Azure, DPM, agente di backup di Azure" |
| BillingGroupName | Utilizzare questo parametro per cercare un particolare gruppo di fatturazione in base al nome. Per impostazione predefinita, il valore è "*", che fa sì che la funzione cerchi tutti i gruppi di fatturazione. | N | testvm |

**Campi restituiti**

| **Nome campo** | **Descrizione** |
| -------------- | --------------- |
| UniqueId | Chiave primaria che indica l'ID univoco del gruppo di fatturazione |
| FriendlyName | Nome descrittivo del gruppo di fatturazione |
| Nome | Nome del gruppo di fatturazione |
| Tipo | Tipo di gruppo di fatturazione. Ad esempio, ProtectedContainer o BackupItem |
| SourceSizeInMBs | Dimensioni front-end del gruppo di fatturazione in MB |
| VaultStore_StorageConsumptionInMBs | Totale dello spazio di archiviazione cloud utilizzato dal gruppo di fatturazione nell'insieme di credenziali-livello standard |
| BackupSolution | Soluzione di backup a cui è associato il gruppo di fatturazione. Ad esempio, backup di macchine virtuali di Azure, SQL in backup di macchine virtuali di Azure e così via. |
| VaultResourceId | ID Azure Resource Manager (ARM) dell'insieme di credenziali associato al gruppo di fatturazione |
| VaultUniqueId | Chiave esterna che fa riferimento all'insieme di credenziali associato al gruppo di fatturazione |
| VaultName | Nome dell'insieme di credenziali associato al gruppo di fatturazione |
| VaultTags | Tag dell'insieme di credenziali associato al gruppo di fatturazione |
| VaultSubscriptionId | ID sottoscrizione dell'insieme di credenziali associato al gruppo di fatturazione |
| VaultLocation | Percorso dell'insieme di credenziali associato al gruppo di fatturazione |
| VaultStore_StorageReplicationType | Tipo di replica di archiviazione dell'insieme di credenziali associato al gruppo di fatturazione |
| VaultType | Tipo di insieme di credenziali, ovvero "Microsoft. RecoveryServices/Vaults" |
| TimeGenerated | Timestamp del record |
| ExtendedProperties | Proprietà aggiuntive del gruppo di fatturazione |

### <a name="trend-functions"></a>Funzioni di tendenza

#### <a name="_azurebackup_getbackupinstancestrends"></a>_AzureBackup_GetBackupInstancesTrends ()

Questa funzione restituisce i record cronologici per ogni istanza di backup, consentendo di visualizzare le tendenze giornaliere, settimanali e mensili correlate al conteggio delle istanze di backup e al consumo di spazio di archiviazione, a più livelli di granularità.

**Parametri**

| **Nome parametro** | **Descrizione** | **Obbligatorio?** | **Valore di esempio** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Usare questo parametro insieme al parametro RangeEnd per recuperare tutti i record correlati all'istanza di backup nel periodo di tempo da RangeStart a RangeEnd. | S | "2021-03-03 00:00:00" |
| RangeEnd     | Usare questo parametro insieme al parametro RangeStart per recuperare tutti i record correlati all'istanza di backup nel periodo di tempo da RangeStart a RangeEnd. | S |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Utilizzare questo parametro per filtrare l'output della funzione per un determinato set di sottoscrizioni in cui esistono dati di backup. La specifica di un elenco delimitato da virgole di ID sottoscrizione come parametro di questa funzione consente di recuperare solo le istanze di backup presenti nelle sottoscrizioni specificate. Per impostazione predefinita, il valore di questo parametro è' *', che rende la funzione la ricerca di record in tutte le sottoscrizioni. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Utilizzare questo parametro per filtrare l'output della funzione per un determinato set di aree in cui esistono dati di backup. La specifica di un elenco delimitato da virgole di aree come parametro per questa funzione consente di recuperare solo le istanze di backup presenti nelle aree specificate. Per impostazione predefinita, il valore di questo parametro è' *', che rende la funzione la ricerca di record in tutte le aree. | N | "eastus, westus"|
| VaultList    |Usare questo parametro per filtrare l'output della funzione per un determinato set di insiemi di credenziali. Specificando un elenco delimitato da virgole di nomi di insiemi di credenziali come parametro di questa funzione, è possibile recuperare i record delle istanze di backup che riguardano solo gli insiemi di credenziali specificati. Per impostazione predefinita, il valore di questo parametro è' *', che fa in modo che la funzione cerchi record di istanze di backup in tutti gli insiemi di credenziali. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Usare questo parametro per filtrare l'output della funzione in record relativi a un determinato tipo di insieme di credenziali. Attualmente l'unico tipo di insieme di credenziali supportato è "Microsoft. RecoveryServices/Vaults", che è il valore predefinito di questo parametro. | N | "Microsoft. RecoveryServices/Vaults"|
| ExcludeLegacyEvent  | Utilizzare questo parametro per scegliere se eseguire una query sui dati nella tabella AzureDiagnostics legacy. Se il valore di questo parametro è false, la funzione esegue una query sui dati della tabella AzureDiagnostics e delle tabelle specifiche delle risorse. Se il valore di questo parametro è true, la funzione esegue una query sui dati solo dalle tabelle specifiche delle risorse. Il valore predefinito è true. | N | true |
| BackupSolutionList | Usare questo parametro per filtrare l'output della funzione per un determinato set di soluzioni di backup usate nell'ambiente Azure. Ad esempio, se si specifica "backup macchina virtuale di Azure, SQL in backup VM di Azure, DPM" come valore di questo parametro, la funzione restituisce solo i record correlati agli elementi di cui è stato eseguito il backup tramite il backup delle macchine virtuali di Azure, SQL in backup di VM di Azure o DPM in backup di Azure. Per impostazione predefinita, il valore di questo parametro è' *', che fa sì che la funzione restituisca record relativi a tutte le soluzioni di backup supportate dai report di backup (i valori supportati sono "backup della macchina virtuale di Azure", "SQL nel backup di VM di Azure", "SAP HANA nel backup di macchine virtuali di Azure", "backup di archiviazione di Azure (File di Azure)", "agente di backup di Azure", "DPM", "server di Backup di Azure" o una combinazione di uno o più valori separati da virgole. | N | "Backup di macchine virtuali di Azure, SQL in backup di VM di Azure, DPM, agente di backup di Azure" |
| ProtectionInfoList | Utilizzare questo parametro per scegliere se includere solo le istanze di backup attivamente protette o includere anche le istanze per le quali la protezione è stata arrestata e le istanze per le quali è in sospeso il backup iniziale. I valori supportati sono "Protected", "Protected o protectionstopped", "InitialBackupPending" o una combinazione di uno o più valori separati da virgole. Per impostazione predefinita, il valore è "*", che fa in modo che la funzione cerchi tutte le istanze di backup indipendentemente dai dettagli della protezione. | N | Protetto |
| DatasourceSetName | Utilizzare questo parametro per filtrare l'output della funzione in una determinata risorsa padre. Ad esempio, per restituire SQL nelle istanze di backup di VM di Azure appartenenti alla macchina virtuale "testvm", specificare _testvm_ come valore di questo parametro. Per impostazione predefinita, il valore è "*", che rende la funzione la ricerca di record in tutte le istanze di backup. | N | testvm |
| BackupInstanceName | Utilizzare questo parametro per cercare una specifica istanza di backup in base al nome. Per impostazione predefinita, il valore è "*", che fa sì che la funzione cerchi tutte le istanze di backup. | N | testvm |
| DisplayAllFields | Utilizzare questo parametro per scegliere se recuperare solo un subset dei campi restituiti dalla funzione. Se il valore di questo parametro è false, la funzione Elimina le informazioni relative all'archiviazione e al punto di conservazione dall'output della funzione. Questa operazione è utile se si utilizza questa funzione come passaggio intermedio in una query di dimensioni maggiori ed è necessario ottimizzare le prestazioni della query eliminando le colonne non necessarie per l'analisi. Per impostazione predefinita, il valore di questo parametro è true, che fa sì che la funzione restituisca tutti i campi relativi all'istanza di backup. | N | true |
| AggregationType | Utilizzare questo parametro per specificare la granularità dell'ora in cui devono essere recuperati i dati. Se il valore di questo parametro è "Daily", la funzione restituisce un record per ogni istanza di backup al giorno, consentendo di analizzare le tendenze quotidiane del numero di istanze di backup e di utilizzo dello spazio di archiviazione. Se il valore di questo parametro è "Weekly", la funzione restituisce un record per ogni istanza di backup alla settimana, consentendo di analizzare le tendenze settimanali. Analogamente, è possibile specificare "Monthly" per analizzare le tendenze mensili. Il valore predefinito è "Daily". Se si visualizzano i dati in intervalli di tempo più grandi, è consigliabile usare "settimanale" o "mensile" per migliorare le prestazioni delle query e semplificare l'analisi delle tendenze. | N | Settimanale |

**Campi restituiti**

| **Nome campo** | **Descrizione** |
| -------------- | --------------- |
| UniqueId | Chiave primaria che indica l'ID univoco dell'istanza di backup |
| Id | ID Azure Resource Manager (ARM) dell'istanza di backup |
| FriendlyName | Nome descrittivo dell'istanza di backup |
| ProtectionInfo | Informazioni sulle impostazioni di protezione dell'istanza di backup. Ad esempio, protezione configurata, protezione arrestata, backup iniziale in sospeso |
| LatestRecoveryPoint | Data e ora del punto di ripristino più recente associato all'istanza di backup |
| OldestRecoveryPoint | Data e ora del punto di ripristino meno recente associato all'istanza di backup |
| SourceSizeInMBs | Dimensioni front-end dell'istanza di backup in MB |
| VaultStore_StorageConsumptionInMBs | Totale spazio di archiviazione cloud utilizzato dall'istanza di backup nell'insieme di credenziali-livello standard |
| DataSourceFriendlyName | Nome descrittivo dell'origine dati corrispondente all'istanza di backup |
| BackupSolution | Soluzione di backup a cui è associata l'istanza di backup. Ad esempio, backup di macchine virtuali di Azure, SQL in backup di macchine virtuali di Azure e così via. |
| DatasourceType | Tipo di origine dati corrispondente all'istanza di backup. Ad esempio, "Microsoft. Compute/virtualMachines" |
| DatasourceResourceId | ID Azure Resource Manager (ARM) dell'origine dati sottostante corrispondente all'istanza di backup. Ad esempio, Azure Resource Manager (ARM) ID della macchina virtuale |
| DatasourceSetFriendlyName | Nome descrittivo della risorsa padre dell'origine dati (laddove applicabile). Ad esempio, per un'origine dati VM SQL in Azure, questo campo conterrà il nome della macchina virtuale in cui è presente il database SQL |
| DatasourceSetResourceId | Azure Resource Manager (ARM) ID della risorsa padre dell'origine dati (laddove applicabile). Ad esempio, per un'origine dati VM SQL in Azure, questo campo conterrà l'ID Azure Resource Manager (ARM) della macchina virtuale in cui è presente il database SQL |
| DatasourceSetType | Tipo di risorsa padre dell'origine dati (laddove applicabile). Per un SAP HANA nell'origine dati della macchina virtuale di Azure, ad esempio, questo campo sarà Microsoft. Compute/virtualMachines poiché la risorsa padre è una macchina virtuale di Azure |
| PolicyName | Nome dei criteri associati all'istanza di backup |
| PolicyUniqueId | Chiave esterna che fa riferimento ai criteri associati all'istanza di backup  |
| PolicyId | ID Azure Resource Manager (ARM) del criterio associato all'istanza di backup |
| VaultResourceId | ID Azure Resource Manager (ARM) dell'insieme di credenziali associato all'istanza di backup |
| VaultUniqueId | Chiave esterna che fa riferimento all'insieme di credenziali associato all'istanza di backup |
| VaultName | Nome dell'insieme di credenziali associato all'istanza di backup |
| VaultTags | Tag dell'insieme di credenziali associato all'istanza di backup |
| VaultSubscriptionId | ID sottoscrizione dell'insieme di credenziali associato all'istanza di backup |
| VaultLocation | Percorso dell'insieme di credenziali associato all'istanza di backup |
| VaultStore_StorageReplicationType | Tipo di replica di archiviazione dell'insieme di credenziali associato all'istanza di backup |
| VaultType | Tipo di insieme di credenziali, ovvero "Microsoft. RecoveryServices/Vaults" |
| TimeGenerated | Timestamp del record |

#### <a name="_azurebackup_getbillinggroupstrends"></a>_AzureBackup_GetBillingGroupsTrends ()

Questa funzione restituisce i record cronologici per ogni entità di fatturazione, consentendo di visualizzare le tendenze giornaliere, settimanali e mensili correlate alle dimensioni del front-end e al consumo di spazio di archiviazione, a più livelli di granularità.

**Parametri**

| **Nome parametro** | **Descrizione** | **Obbligatorio?** | **Valore di esempio** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Usare questo parametro insieme al parametro RangeEnd per recuperare tutti i record correlati al gruppo di fatturazione nel periodo di tempo da RangeStart a RangeEnd. | S | "2021-03-03 00:00:00" |
| RangeEnd     | Usare questo parametro insieme al parametro RangeStart per recuperare tutti i record correlati al gruppo di fatturazione nel periodo di tempo da RangeStart a RangeEnd. | S |"2021-03-10 00:00:00"|
| VaultSubscriptionList  | Utilizzare questo parametro per filtrare l'output della funzione per un determinato set di sottoscrizioni in cui esistono dati di backup. La specifica di un elenco delimitato da virgole di ID sottoscrizione come parametro di questa funzione consente di recuperare solo i gruppi di fatturazione presenti nelle sottoscrizioni specificate. Per impostazione predefinita, il valore di questo parametro è' *', che rende la funzione la ricerca di record in tutte le sottoscrizioni. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Utilizzare questo parametro per filtrare l'output della funzione per un determinato set di aree in cui esistono dati di backup. Specificando un elenco delimitato da virgole di aree come parametro di questa funzione, è possibile recuperare solo i gruppi di fatturazione presenti nelle aree specificate. Per impostazione predefinita, il valore di questo parametro è' *', che rende la funzione la ricerca di record in tutte le aree. | N | "eastus, westus"|
| VaultList    |Usare questo parametro per filtrare l'output della funzione per un determinato set di insiemi di credenziali. Specificando un elenco delimitato da virgole di nomi di insiemi di credenziali come parametro di questa funzione, è possibile recuperare i record delle istanze di backup che riguardano solo gli insiemi di credenziali specificati. Per impostazione predefinita, il valore di questo parametro è' *', che fa in modo che la funzione cerchi record dei gruppi di fatturazione in tutti gli insiemi di credenziali. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Usare questo parametro per filtrare l'output della funzione in record relativi a un determinato tipo di insieme di credenziali. Attualmente l'unico tipo di insieme di credenziali supportato è "Microsoft. RecoveryServices/Vaults", che è il valore predefinito di questo parametro. | N | "Microsoft. RecoveryServices/Vaults"|
| ExcludeLegacyEvent  | Utilizzare questo parametro per scegliere se eseguire una query sui dati nella tabella AzureDiagnostics legacy. Se il valore di questo parametro è false, la funzione esegue una query sui dati della tabella AzureDiagnostics e delle tabelle specifiche delle risorse. Se il valore di questo parametro è true, la funzione esegue una query sui dati solo dalle tabelle specifiche delle risorse. Il valore predefinito è true. | N | true |
| BackupSolutionList | Usare questo parametro per filtrare l'output della funzione per un determinato set di soluzioni di backup usate nell'ambiente Azure. Ad esempio, se si specifica "backup macchina virtuale di Azure, SQL in backup VM di Azure, DPM" come valore di questo parametro, la funzione restituisce solo i record correlati agli elementi di cui è stato eseguito il backup tramite il backup delle macchine virtuali di Azure, SQL in backup di VM di Azure o DPM in backup di Azure. Per impostazione predefinita, il valore di questo parametro è' *', che fa sì che la funzione restituisca record relativi a tutte le soluzioni di backup supportate dai report di backup (i valori supportati sono "backup della macchina virtuale di Azure", "SQL nel backup di VM di Azure", "SAP HANA nel backup di macchine virtuali di Azure", "backup di archiviazione di Azure (File di Azure)", "agente di backup di Azure", "DPM", "server di Backup di Azure" o una combinazione di uno o più valori separati da virgole. | N | "Backup di macchine virtuali di Azure, SQL in backup di VM di Azure, DPM, agente di backup di Azure" |
| BillingGroupName | Utilizzare questo parametro per cercare un particolare gruppo di fatturazione in base al nome. Per impostazione predefinita, il valore è "*", che fa sì che la funzione cerchi tutti i gruppi di fatturazione. | N | testvm |
| AggregationType | Utilizzare questo parametro per specificare la granularità dell'ora in cui devono essere recuperati i dati. Se il valore di questo parametro è "Daily", la funzione restituisce un record per ogni gruppo di fatturazione al giorno, consentendo di analizzare le tendenze giornaliere del consumo di spazio di archiviazione e delle dimensioni del front-end. Se il valore di questo parametro è "Weekly", la funzione restituisce un record per ogni istanza di backup alla settimana, consentendo di analizzare le tendenze settimanali. Analogamente, è possibile specificare "Monthly" per analizzare le tendenze mensili. Il valore predefinito è "Daily". Se si visualizzano i dati in intervalli di tempo più grandi, è consigliabile usare "settimanale" o "mensile" per migliorare le prestazioni delle query e semplificare l'analisi delle tendenze. | N | Settimanale |

**Campi restituiti**

| **Nome campo** | **Descrizione** |
| -------------- | --------------- |
| UniqueId | Chiave primaria che indica l'ID univoco del gruppo di fatturazione |
| FriendlyName | Nome descrittivo del gruppo di fatturazione |
| Nome | Nome del gruppo di fatturazione |
| Tipo | Tipo di gruppo di fatturazione. Ad esempio, ProtectedContainer o BackupItem |
| SourceSizeInMBs | Dimensioni front-end del gruppo di fatturazione in MB |
| VaultStore_StorageConsumptionInMBs | Totale dello spazio di archiviazione cloud utilizzato dal gruppo di fatturazione nell'insieme di credenziali-livello standard |
| BackupSolution | Soluzione di backup a cui è associato il gruppo di fatturazione. Ad esempio, backup di macchine virtuali di Azure, SQL in backup di macchine virtuali di Azure e così via. |
| VaultResourceId | ID Azure Resource Manager (ARM) dell'insieme di credenziali associato al gruppo di fatturazione |
| VaultUniqueId | Chiave esterna che fa riferimento all'insieme di credenziali associato al gruppo di fatturazione |
| VaultName | Nome dell'insieme di credenziali associato al gruppo di fatturazione |
| VaultTags | Tag dell'insieme di credenziali associato al gruppo di fatturazione |
| VaultSubscriptionId | ID sottoscrizione dell'insieme di credenziali associato al gruppo di fatturazione |
| VaultLocation | Percorso dell'insieme di credenziali associato al gruppo di fatturazione |
| VaultStore_StorageReplicationType | Tipo di replica di archiviazione dell'insieme di credenziali associato al gruppo di fatturazione |
| VaultType | Tipo di insieme di credenziali, ovvero "Microsoft. RecoveryServices/Vaults" |
| TimeGenerated | Timestamp del record |
| ExtendedProperties | Proprietà aggiuntive del gruppo di fatturazione |

## <a name="sample-queries"></a>Query di esempio

Di seguito sono riportate alcune query di esempio che consentono di iniziare a usare le funzioni di sistema.

- Tutti i processi di backup delle macchine virtuali di Azure non riusciti in un intervallo di tempo specifico

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06") //call function with RangeStart and RangeEnd parameters set, and other parameters with default value
    | where BackupSolution=="Azure Virtual Machine Backup" and Status=="Failed"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- Tutti i processi di backup del log SQL in un intervallo di tempo specifico

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06","*","*","*","*",true,"*","*","*","*","*","*",false) //call function with RangeStart and RangeEnd parameters set, ExcludeLog parameter as false, and other parameters with default value
    | where BackupSolution=="SQL in Azure VM Backup" and Operation=="Log"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- Tendenza settimanale dell'archiviazione di backup utilizzata per la macchina virtuale "testvm"

    ````Kusto
    _AzureBackup_GetBackupInstancesTrends("2021-01-01", "2021-03-06","*","*","*","*",false,"*","*","*","*",true, "Weekly") //call function with RangeStart and RangeEnd parameters set, AggregationType parameter as Weekly, and other parameters with default value
    | where BackupSolution == "Azure Virtual Machine Backup"
    | where FriendlyName == "testvm"
    | project TimeGenerated, VaultStore_StorageConsumptionInMBs
    | render timechart 
    ````

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni sui report di backup](./configure-reports.md)