---
title: Come gestire Sincronizzazione file di Azure file a livelli | Microsoft Docs
description: Suggerimenti e cmdlet di PowerShell per semplificare la gestione dei file a livelli
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dc61792da669cd5d2c928eec0fd412d86725fc8c
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204353"
---
# <a name="how-to-manage-tiered-files"></a>Come gestire i file a livelli

Questo articolo fornisce indicazioni per gli utenti che hanno domande correlate alla gestione dei file a livelli. Per domande concettuali sulla suddivisione in livelli nel cloud, vedere [file di Azure Domande frequenti](storage-files-faq.md).

## <a name="how-to-check-if-your-files-are-being-tiered"></a>Come verificare se i file sono a livelli

Il fatto che i file debbano o meno essere suddivisi in livelli per ogni criterio di set viene valutato una volta all'ora. Quando viene creato un nuovo endpoint server, è possibile passare in due situazioni:

Quando si aggiunge per la prima volta un nuovo endpoint server, spesso i file sono presenti nel percorso del server. È necessario caricarli prima di iniziare la suddivisione in livelli nel cloud. Il criterio di spazio disponibile del volume non inizierà a funzionare fino al completamento del caricamento iniziale di tutti i file. Tuttavia, il criterio di data facoltativo inizierà a funzionare in base a un singolo file, non appena un file viene caricato. L'intervallo di un'ora si applica anche qui. 

Quando si aggiunge un nuovo endpoint server, è possibile che sia stato connesso un percorso server vuoto a una condivisione file di Azure con i dati in esso contenuti. Se si sceglie di scaricare lo spazio dei nomi e di richiamare il contenuto durante il download iniziale sul server, dopo che lo spazio dei nomi viene disattivato, i file verranno richiamati in base all'ultimo timestamp modificato fino a quando non vengono raggiunti i criteri di spazio libero del volume e vengono raggiunti i limiti facoltativi relativi ai criteri

Esistono diversi modi per verificare se un file è archiviato a livelli in una condivisione file di Azure:
    
   *  **Controllare gli attributi del file nel file stesso.**
     Fare clic con il pulsante destro del mouse su un file, scegliere **Dettagli** e quindi scorrere verso il basso fino alla proprietà **Attributi**. Un file archiviato a livelli avrà il set di attributi seguente:     
        
        | Lettera di attributo | Attributo | Definizione |
        |:----------------:|-----------|------------|
        | A | Archiviazione | Indica che deve essere eseguito un backup del file tramite il software di backup. Questo attributo è sempre impostato indipendentemente dal fatto che il file sia archiviato a livelli o archiviato completamente su disco. |
        | P | File sparse | Indica che il file è un file sparse. Un file sparse è un tipo specializzato di file offerto da NTFS per un uso efficiente quando il flusso di file su disco è pressoché vuoto. Sincronizzazione file di Azure usa i file sparse perché un file è completamente archiviato a livelli o parzialmente richiamato. In un file completamente archiviato a livelli, il flusso di file viene archiviato nel cloud. In un file parzialmente richiamato, tale parte del file è già su disco. Questa situazione può verificarsi quando i file vengono letti parzialmente da applicazioni come i lettori multimediali o le utilità di compressione. Se un file è completamente richiamato su disco, Sincronizzazione file di Azure lo converte da file sparse in un file regolare. Questo attributo viene impostato solo in Windows Server 2016 e versioni precedenti.|
        | M | Richiama nell'accesso ai dati | Indica che i dati del file non sono completamente presenti nella risorsa di archiviazione locale. La lettura del file provocherà il recupero di almeno parte del contenuto del file da una condivisione file di Azure a cui è connesso l'endpoint server. Questo attributo è impostato solo in Windows Server 2019. |
        | L | Reparse point | Indica che il file contiene un reparse point. Un reparse point è un puntatore speciale utilizzabile da un filtro del file system. Sincronizzazione file di Azure usa i reparse point per definire la posizione nel cloud dove è archiviato il file per il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys). È supportato l'accesso facile. Non è necessario che gli utenti sappiano che è in uso Sincronizzazione file di Azure o come ottenere l'accesso al file nella condivisione file di Azure. Quando un file viene richiamato completamente, Sincronizzazione file di Azure rimuove il reparse point dal file. |
        | O | Offline | Indica che il contenuto del file non è archiviato interamente o parzialmente su disco. Quando un file viene richiamato completamente, Sincronizzazione file di Azure rimuove questo attributo. |

        ![Finestra di dialogo Proprietà per un file con la scheda Dettagli selezionata](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
    
        > [!NOTE]
        > È possibile visualizzare gli attributi per tutti i file in una cartella aggiungendo il campo **Attributi** alla visualizzazione tabella di Esplora file. A tale scopo, fare clic con il pulsante destro del mouse su una colonna esistente (ad esempio, **Dimensioni**), scegliere **Altro** e quindi selezionare **Attributi** nell'elenco a discesa.
        
        > [!NOTE]
        > Tutti questi attributi saranno visibili anche per i file parzialmente richiamati.
        
   * **Usare `fsutil` per verificare la presenza di reparse point in un file.**
       Come descritto per l'opzione precedente, per un file archiviato a più livelli è sempre impostato un reparse point. Un reparse point consente all'Sincronizzazione file di Azure file system driver di filtro (StorageSync.sys) di recuperare il contenuto da condivisioni file di Azure non archiviate localmente nel server. 

       Per verificare se un file contiene un reparse point, eseguire l'utilità `fsutil` in un prompt dei comandi con privilegi elevati o in una sessione di PowerShell:

        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```
       Se il file contiene un reparse point, dovrebbe essere visualizzato **Reparse Tag Value: 0x8000001e** (Valore tag di reparse: 0x8000001e). Questo valore esadecimale è il valore del reparse point di proprietà di Sincronizzazione file di Azure. L'output contiene anche i dati di reparse che rappresentano il percorso del file nella condivisione file di Azure.
        
        > [!WARNING]  
        > Il comando dell'utilità `fsutil reparsepoint` offre anche la possibilità di eliminare un reparse point. Non eseguire questo comando, a meno che non venga richiesto dal team di progettazione di Sincronizzazione file di Azure. L'esecuzione di questo comando può causare la perdita di dati. 

## <a name="how-to-exclude-applications-from-cloud-tiering-last-access-time-tracking"></a>Come escludere le applicazioni dal monitoraggio dell'ora dell'ultimo accesso a livello cloud

Con Sincronizzazione file di Azure Agent versione 11,1, è ora possibile escludere le applicazioni dall'ultimo rilevamento degli accessi. Quando un'applicazione accede a un file, l'ora dell'ultimo accesso per il file viene aggiornata nel database di suddivisione in livelli cloud. Le applicazioni che analizzano il file system come antivirus fanno sì che tutti i file abbiano lo stesso tempo di accesso ultimo, che influisca sul momento in cui i file sono suddivisi in livelli.

Per escludere le applicazioni dal rilevamento dell'ora dell'ultimo accesso, aggiungere il nome del processo all'impostazione del registro di sistema HeatTrackingProcessNameExclusionList che si trova in HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

Esempio: REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync"/v HeatTrackingProcessNameExclusionList/t REG_MULTI_SZ/d "SampleApp.exe\0AnotherApp.exe"/f

> [!NOTE]
> I processi di deduplicazione dati e file server Gestione risorse (FSRM) sono esclusi per impostazione predefinita. Le modifiche apportate all'elenco di esclusioni dei processi vengono rispettate dal sistema ogni 5 minuti.

## <a name="how-to-access-the-heat-store"></a>Come accedere a Heat Store

La suddivisione in livelli nel cloud usa l'ora dell'ultimo accesso e la frequenza di accesso di un file per determinare i file da a livelli. Il driver del filtro di suddivisione in livelli cloud (storagesync.sys) tiene traccia dell'ora dell'ultimo accesso e registra le informazioni nell'archivio termico di suddivisione in livelli nel cloud. È possibile recuperare l'archivio di calore e salvarlo in un file CSV usando un cmdlet di PowerShell locale del server.

Per tutti i file nello stesso volume è disponibile un singolo archivio di riscaldamento. L'archivio termico può ottenere dimensioni molto elevate. Se è necessario recuperare solo il numero di elementi più interessanti, utilizzare-Limit e un numero e considerare anche il filtro in base a un sottopercorso rispetto alla radice del volume.

- Importare il modulo di PowerShell:   `Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'`

- SPAZIO disponibile nel VOLUME: per ottenere l'ordine in cui i file verranno suddivisi in livelli usando i criteri di spazio disponibile del volume:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredBySpacePolicy`

- Data POLICY: per ottenere l'ordine in cui i file verranno suddivisi in livelli usando i criteri di data:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredByDatePolicy`

- Trovare le informazioni sull'archivio termico per un file specifico:   `Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'`

- Visualizza tutti i file in ordine decrescente in base all'ora dell'ultimo accesso:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName DescendingLastAccessTime`

- Vedere l'ordine in base al quale i file a livelli verranno richiamati tramite il richiamo in background o il richiamo su richiesta tramite PowerShell:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName OrderTieredFilesWillBeRecalled`

## <a name="how-to-force-a-file-or-directory-to-be-tiered"></a>Come forzare la suddivisione in livelli di un file o di una directory

> [!NOTE]
> Quando si seleziona una directory a livelli, solo i file attualmente presenti nella directory vengono suddivisi in livelli. Tutti i file creati dopo tale periodo non vengono automaticamente suddivisi in livelli.

Quando la funzionalità di suddivisione in livelli nel cloud è abilitata, i file vengono archiviati automaticamente a livelli in base all'ora dell'ultimo accesso e dell'ultima modifica per ottenere la percentuale di spazio disponibile nel volume specificata nell'endpoint cloud. Tuttavia, a volte potrebbe essere necessario forzare manualmente l'archiviazione a livelli di un file. Ciò potrebbe essere utile se si salva un file di grandi dimensioni che non si intende riutilizzare per molto tempo e si vuole destinare lo spazio libero sul volume ad altri file e cartelle. È possibile forzare la suddivisione in livelli con i comandi seguenti di PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="how-to-recall-a-tiered-file-to-disk"></a>Come richiamare un file a livelli su disco

Il modo più semplice per richiamare un file su disco è aprirlo. Il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys) scarica automaticamente il file dalla condivisione file di Azure senza che sia necessario eseguire alcuna operazione. Per i tipi di file che possono essere letti o trasmessi parzialmente, ad esempio i file con estensione zip o multimediali, l'apertura di un file non garantisce che venga scaricato l'intero file.

Per assicurarsi che un file venga scaricato completamente nel disco locale, è necessario usare PowerShell per forzare il richiamo completo di un file. Questa opzione può essere utile anche se si desidera richiamare più file contemporaneamente, ad esempio tutti i file in una cartella. Aprire una sessione di PowerShell per il nodo server in cui è installato Sincronizzazione file di Azure e quindi eseguire i comandi di PowerShell seguenti:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Parametri facoltativi:
* `-Order CloudTieringPolicy` richiamerà prima i file modificati o a cui si accede più di recente ed è consentito dai criteri di suddivisione in livelli correnti. 
    * Se è configurato un criterio di spazio disponibile nel volume, i file verranno richiamati finché non viene raggiunta l'impostazione dei criteri di spazio libero del volume. Se, ad esempio, l'impostazione dei criteri volume Free è 20%, il richiamo verrà interrotto quando lo spazio disponibile nel volume raggiunge il 20%.  
    * Se lo spazio disponibile nel volume e i criteri di data sono configurati, i file verranno richiamati fino a quando non viene raggiunta l'impostazione di spazio disponibile del volume o data. Se, ad esempio, l'impostazione dei criteri di volume Free è 20% e il criterio relativo alla data è 7 giorni, il richiamo verrà interrotto quando lo spazio disponibile nel volume raggiunge il 20% oppure tutti i file a cui si accede o modificati entro 7 giorni sono locali.
* `-ThreadCount` determina il numero di file che possono essere richiamati in parallelo.
* `-PerFileRetryCount`determina la frequenza con cui viene eseguito il tentativo di richiamo di un file attualmente bloccato.
* `-PerFileRetryDelaySeconds`determina il tempo in secondi tra i tentativi per richiamare i tentativi e deve essere sempre utilizzato in combinazione con il parametro precedente.

Esempio:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
>- Se il volume locale che ospita il server non ha abbastanza spazio libero per richiamare tutti i dati archiviati a livelli, il cmdlet `Invoke-StorageSyncFileRecall` ha esito negativo.  

> [!Note]
> Per richiamare i file a livelli, la larghezza di banda della rete deve essere di almeno 1 Mbps. Se la larghezza di banda di rete è inferiore a 1 Mbps, i file potrebbero non richiamarsi con un errore di timeout.

## <a name="next-steps"></a>Passaggi successivi
* [Azure Files FAQ](storage-files-faq.md) (Domande frequenti su File di Azure)
