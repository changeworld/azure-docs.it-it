---
title: Come gestire i Sincronizzazione file di Azure a livelli | Microsoft Docs
description: Suggerimenti e commandlet di PowerShell per gestire i file a livelli
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bd740c773998450ef6e8bb95c4df3a1abadaceed
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796871"
---
# <a name="how-to-manage-tiered-files"></a>Come gestire i file a livelli

Questo articolo fornisce indicazioni per gli utenti che hanno domande relative alla gestione dei file a livelli. Per domande concettuali sulla distribuzione a livelli nel cloud, vedere File di Azure [domande frequenti.](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)

## <a name="how-to-check-if-your-files-are-being-tiered"></a>Come verificare se i file vengono a livelli

Se i file devono essere o meno a livelli per ogni set di criteri viene valutato una volta all'ora. Quando viene creato un nuovo endpoint server, è possibile verificarsi due situazioni:

Quando si aggiunge per la prima volta un nuovo endpoint server, spesso esistono file in tale percorso del server. È necessario caricarlo prima di poter iniziare la distribuzione a livelli nel cloud. I criteri di spazio libero del volume non inizieranno il lavoro fino al completamento del caricamento iniziale di tutti i file. Tuttavia, i criteri di data facoltativi inizieranno a funzionare su un singolo file, non appena un file è stato caricato. Anche l'intervallo di un'ora si applica qui. 

Quando si aggiunge un nuovo endpoint server, è possibile che sia stato connesso un percorso server vuoto a una condivisione file di Azure con i dati in esso presenti. Se si sceglie di scaricare lo spazio dei nomi e richiamare il contenuto durante il download iniziale nel server, al termine dello spazio dei nomi, i file verranno richiamati in base all'ultimo timestamp modificato fino a quando non vengono raggiunti i criteri di spazio disponibile del volume e i limiti facoltativi dei criteri relativi alla data.

Esistono diversi modi per verificare se un file è archiviato a livelli in una condivisione file di Azure:
    
   *  **Controllare gli attributi del file nel file stesso.**
     Fare clic con il pulsante destro del mouse su un file, scegliere **Dettagli** e quindi scorrere verso il basso fino alla proprietà **Attributi**. Un file archiviato a livelli avrà il set di attributi seguente:     
        
        | Lettera di attributo | Attributo | Definizione |
        |:----------------:|-----------|------------|
        | Una | Archivio | Indica che deve essere eseguito un backup del file tramite il software di backup. Questo attributo è sempre impostato indipendentemente dal fatto che il file sia archiviato a livelli o archiviato completamente su disco. |
        | P | File sparse | Indica che il file è un file sparse. Un file sparse è un tipo specializzato di file offerto da NTFS per un uso efficiente quando il flusso di file su disco è pressoché vuoto. Sincronizzazione file di Azure usa i file sparse perché un file è completamente archiviato a livelli o parzialmente richiamato. In un file completamente archiviato a livelli, il flusso di file viene archiviato nel cloud. In un file parzialmente richiamato, tale parte del file è già su disco. Questa situazione può verificarsi quando i file vengono letti parzialmente da applicazioni come i lettori multimediali o le utilità di compressione. Se un file è completamente richiamato su disco, Sincronizzazione file di Azure lo converte da file sparse in un file regolare. Questo attributo è impostato solo in Windows Server 2016 e versioni precedenti.|
        | M | Richiamo per l'accesso ai dati | Indica che i dati del file non sono completamente presenti nell'archiviazione locale. La lettura del file causerà il recupero di almeno parte del contenuto del file da una condivisione file di Azure a cui è connesso l'endpoint server. Questo attributo è impostato solo in Windows Server 2019. |
        | L | Reparse point | Indica che il file contiene un reparse point. Un reparse point è un puntatore speciale utilizzabile da un filtro del file system. Sincronizzazione file di Azure usa i reparse point per definire la posizione nel cloud dove è archiviato il file per il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys). È supportato l'accesso facile. Non è necessario che gli utenti sappiano che è in uso Sincronizzazione file di Azure o come ottenere l'accesso al file nella condivisione file di Azure. Quando un file viene richiamato completamente, Sincronizzazione file di Azure rimuove il reparse point dal file. |
        | O | Offline | Indica che il contenuto del file non è archiviato interamente o parzialmente su disco. Quando un file viene richiamato completamente, Sincronizzazione file di Azure rimuove questo attributo. |

        ![Finestra di dialogo Proprietà per un file con la scheda Dettagli selezionata](../files/media/storage-files-faq/azure-file-sync-file-attributes.png)
        
    
        > [!NOTE]
        > È possibile visualizzare gli attributi per tutti i file in una cartella aggiungendo il campo **Attributi** alla visualizzazione tabella di Esplora file. A tale scopo, fare clic con il pulsante destro del mouse su una colonna esistente (ad esempio, **Dimensioni**), scegliere **Altro** e quindi selezionare **Attributi** nell'elenco a discesa.
        
        > [!NOTE]
        > Tutti questi attributi saranno visibili anche per i file richiamati parzialmente.
        
   * **Usare `fsutil` per verificare la presenza di reparse point in un file.**
       Come descritto per l'opzione precedente, per un file archiviato a più livelli è sempre impostato un reparse point. Un reparse point consente al driver Sincronizzazione file di Azure file system (StorageSync.sys) di recuperare il contenuto dalle condivisioni file di Azure non archiviate localmente nel server. 

       Per verificare se un file contiene un reparse point, eseguire l'utilità `fsutil` in un prompt dei comandi con privilegi elevati o in una sessione di PowerShell:

        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```
       Se il file contiene un reparse point, dovrebbe essere visualizzato **Reparse Tag Value: 0x8000001e** (Valore tag di reparse: 0x8000001e). Questo valore esadecimale è il valore del reparse point di proprietà di Sincronizzazione file di Azure. L'output contiene anche i dati di analisi che rappresentano il percorso del file nella condivisione file di Azure.
        
        > [!WARNING]  
        > Il comando dell'utilità `fsutil reparsepoint` offre anche la possibilità di eliminare un reparse point. Non eseguire questo comando, a meno che non venga richiesto dal team di progettazione di Sincronizzazione file di Azure. L'esecuzione di questo comando può causare la perdita di dati. 

## <a name="how-to-exclude-applications-from-cloud-tiering-last-access-time-tracking"></a>Come escludere le applicazioni dal rilevamento dell'ora dell'ultimo accesso a livelli nel cloud

Con Sincronizzazione file di Azure'agente versione 11.1, è ora possibile escludere le applicazioni dal rilevamento dell'ultimo accesso. Quando un'applicazione accede a un file, l'ora dell'ultimo accesso per il file viene aggiornata nel database cloud a livelli. Le applicazioni che analizzano file system come l'antivirus causano la stessa ora dell'ultimo accesso per tutti i file, che influisce sul momento in cui i file sono a livelli.

Per escludere le applicazioni dal rilevamento dell'ora dell'ultimo accesso, aggiungere il nome del processo all'impostazione del Registro di sistema HeatTrackingProcessNameExclusionList che si trova in HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

Esempio: reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNameExclusionList /t REG_MULTI_SZ /d "SampleApp.exe\0AnotherApp.exe" /f

> [!NOTE]
> I processi di deduplicazione dati e Resource Manager file server (FSRM) sono esclusi per impostazione predefinita. Le modifiche all'elenco di esclusione dei processi vengono rispettate dal sistema ogni 5 minuti.

## <a name="how-to-access-the-heat-store"></a>Come accedere all'archivio termico

Il cloud a livelli usa l'ora dell'ultimo accesso e la frequenza di accesso di un file per determinare quali file devono essere a livelli. Il driver del filtro di cloud a livelli (storagesync.sys) tiene traccia dell'ora dell'ultimo accesso e registra le informazioni nell'archivio termico cloud a livelli. È possibile recuperare l'archivio termico e salvarlo in un file CSV usando un cmdlet di PowerShell locale del server.

È disponibile un singolo archivio termico per tutti i file nello stesso volume. L'archivio termico può raggiungere dimensioni molto grandi. Se è necessario recuperare solo il numero di elementi più "cool", usare -Limit e un numero e valutare anche la possibilità di filtrare in base a un percorso secondario rispetto alla radice del volume.

- Importare il modulo PowerShell:   `Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'`

- VOLUME FREE SPACE: per ottenere l'ordine in cui i file verranno a livelli usando i criteri di spazio libero del volume:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredBySpacePolicy`

- CRITERI DATA: per ottenere l'ordine in cui i file verranno a livelli usando i criteri di data:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredByDatePolicy`

- Trovare le informazioni sull'archivio termico per un file specifico:   `Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'`

- Visualizzare tutti i file in ordine decrescente in base all'ora dell'ultimo accesso:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName DescendingLastAccessTime`

- Vedere l'ordine in base al quale i file a livelli verranno richiamati dal richiamo in background o dal richiamo su richiesta tramite PowerShell:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName OrderTieredFilesWillBeRecalled`

## <a name="how-to-force-a-file-or-directory-to-be-tiered"></a>Come forzare il livello di un file o di una directory

> [!NOTE]
> Quando si seleziona una directory da aggiungere a livelli, solo i file attualmente presenti nella directory vengono a livelli. Tutti i file creati dopo tale periodo non vengono automaticamente a livelli.

Quando la funzionalità di suddivisione in livelli nel cloud è abilitata, i file vengono archiviati automaticamente a livelli in base all'ora dell'ultimo accesso e dell'ultima modifica per ottenere la percentuale di spazio disponibile nel volume specificata nell'endpoint cloud. Tuttavia, a volte potrebbe essere necessario forzare manualmente l'archiviazione a livelli di un file. Ciò potrebbe essere utile se si salva un file di grandi dimensioni che non si intende riutilizzare per molto tempo e si vuole destinare lo spazio libero sul volume ad altri file e cartelle. È possibile forzare la suddivisione in livelli con i comandi seguenti di PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="how-to-recall-a-tiered-file-to-disk"></a>Come richiamare un file a livelli su disco

Il modo più semplice per richiamare un file su disco è aprirlo. Il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys) scarica automaticamente il file dalla condivisione file di Azure senza che sia necessario eseguire alcuna operazione. Per i tipi di file che possono essere parzialmente letti o trasmessi in streaming, ad esempio file multimediali o zip, la semplice apertura di un file non garantisce che l'intero file sia scaricato.

Per assicurarsi che un file sia completamente scaricato sul disco locale, è necessario usare PowerShell per forzare il richiamo completo di un file. Questa opzione può essere utile anche se si vogliono richiamare più file contemporaneamente, ad esempio tutti i file in una cartella. Aprire una sessione di PowerShell per il nodo server in cui è installato Sincronizzazione file di Azure e quindi eseguire i comandi di PowerShell seguenti:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Parametri facoltativi:
* `-Order CloudTieringPolicy` richiama prima i file modificati o a cui si accede più di recente ed è consentito dai criteri di livelli correnti. 
    * Se sono configurati i criteri di spazio disponibile nel volume, i file verranno richiamati fino a quando non viene raggiunta l'impostazione dei criteri di spazio libero del volume. Ad esempio, se l'impostazione dei criteri volume free è 20%, il richiamo verrà interrotta quando lo spazio disponibile del volume raggiunge il 20%.  
    * Se sono configurati i criteri per la data e lo spazio disponibile nel volume, i file verranno richiamati fino a quando non viene raggiunta l'impostazione dei criteri di data o spazio disponibile nel volume. Ad esempio, se l'impostazione dei criteri volume free è 20% e il criterio di data è di 7 giorni, il richiamo verrà interrotta quando lo spazio disponibile del volume raggiunge il 20% o tutti i file a cui si accede o modificati entro 7 giorni sono locali.
* `-ThreadCount` determina il numero di file che possono essere richiamati in parallelo.
* `-PerFileRetryCount`determina la frequenza con cui verrà eseguito un tentativo di richiamo di un file attualmente bloccato.
* `-PerFileRetryDelaySeconds`determina il tempo in secondi tra i tentativi di richiamo e deve essere sempre usato in combinazione con il parametro precedente.

Esempio:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
>- Se il volume locale che ospita il server non ha abbastanza spazio libero per richiamare tutti i dati archiviati a livelli, il cmdlet `Invoke-StorageSyncFileRecall` ha esito negativo.  

> [!Note]
> Per richiamare i file a livelli, la larghezza di banda di rete deve essere di almeno 1 Mbps. Se la larghezza di banda di rete è inferiore a 1 Mbps, i file potrebbero non essere richiamati con un errore di timeout.

## <a name="next-steps"></a>Passaggi successivi

* [Domande frequenti su File di Azure](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)