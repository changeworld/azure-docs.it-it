---
title: Note sulla versione dell'agente Sincronizzazione file di Azure | Microsoft Docs
description: Leggere le note sulla versione dell'Sincronizzazione file di Azure, che consente di centralizzare le condivisioni file dell'organizzazione in File di Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 4/7/2021
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 9c00b2d4d30ac417d58f2b69e4ba460789cf6583
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796823"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Note sulla versione dell'agente Sincronizzazione file di Azure
Sincronizzazione file di Azure consente di centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Le installazioni Windows Server vengono trasformate in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS e FTPS. Si può usare qualsiasi numero di cache in tutto il mondo.

Questo articolo illustra le note sulla versione per le versioni supportate dell'agente Sincronizzazione file di Azure.

## <a name="supported-versions"></a>Versioni supportate
Sono supportate Sincronizzazione file di Azure seguenti versioni dell'agente:

| Attività cardine | Numero di versione dell'agente | Data di rilascio | Stato |
|----|----------------------|--------------|------------------|
| Versione V12 - [KB4568585](https://support.microsoft.com/topic/b9605f04-b4af-4ad8-86b0-2c490c535cfd)| 12.0.0.0 | 26 marzo 2021 | Supportato - Flighting |
| Versione V11.3 - [KB4539953](https://support.microsoft.com/topic/f68974f6-bfdd-44f4-9659-bf2d8a696c26)| 11.3.0.0 | 7 aprile 2021 | Supportato |
| Versione V11.2 - [KB4539952](https://support.microsoft.com/topic/azure-file-sync-agent-v11-2-release-february-2021-c956eaf0-cd8e-4511-98c0-e5a1f2c84048)| 11.2.0.0 | 2 febbraio 2021 | Supportato |
| Versione V11.1 - [KB4539951](https://support.microsoft.com/help/4539951)| 11.1.0.0 | 4 novembre 2020 | Supportato |
| Versione 10.1 - [KB4522411](https://support.microsoft.com/help/4522411)| 10.1.0.0 | 5 giugno 2020 | Supportato: la versione dell'agente scadrà il 7 giugno 2021 |
| Aggiornamento cumulativo di maggio 2020 - [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 19 maggio 2020 | Supportato: la versione dell'agente scadrà il 7 giugno 2021 |
| Versione 10 - [KB4522409](https://support.microsoft.com/help/4522409)| 10.0.0.0 | 9 aprile 2020 | Supportato: la versione dell'agente scadrà il 7 giugno 2021 |

## <a name="unsupported-versions"></a>Versioni non supportate
Le seguenti Sincronizzazione file di Azure agente sono scadute e non sono più supportate:

| Attività cardine | Numero di versione dell'agente | Data di rilascio | Stato |
|----|----------------------|--------------|------------------|
| Versione V9 | 9.0.0.0 - 9.1.0.0 | N/D | Non supportato : la versione dell'agente è scaduta il 16 febbraio 2021 |
| Versione V8 | 8.0.0.0 | N/D | Non supportato : la versione dell'agente è scaduta il 12 gennaio 2021 |
| Versione V7 | 7.0.0.0 - 7.2.0.0 | N/D | Non supportato : le versioni dell'agente sono scadute il 1° settembre 2020 |
| Versione 6 | 6.0.0.0 - 6.3.0.0 | N/D | Non supportato: la versione dell'agente è scaduta il 21 aprile 2020 |
| Versione 5 | 5.0.2.0 - 5.2.0.0 | N/D | Non supportato: la versione dell'agente è scaduta il 18 marzo 2020 |
| Versione v4 | 4.0.1.0 - 4.3.0.0 | N/D | Non supportato: la versione dell'agente è scaduta il 6 novembre 2019 |
| Versione 3 | 3.1.0.0 - 3.4.0.0 | N/D | Non supportato: la versione dell'agente è scaduta il 19 agosto 2019 |
| Agenti pre-disponibilità generale | 1.1.0.0 - 3.0.13.0 | N/D | Non supportato: versione dell'agente scaduta il 1° ottobre 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Criteri di aggiornamento dell'agente Sincronizzazione file di Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-12000"></a>Agent versione 12.0.0.0
Le note sulla versione seguenti sono relative alla versione 12.0.0.0 dell'agente Sincronizzazione file di Azure (rilasciata il 26 marzo 2021).

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti
- Nuova esperienza del portale per configurare i criteri di accesso alla rete e le connessioni agli endpoint privati
    - È ora possibile usare il portale per disabilitare l'accesso all'endpoint pubblico del servizio di sincronizzazione archiviazione e approvare, rifiutare e rimuovere le connessioni dell'endpoint privato. Per configurare i criteri di accesso alla rete e le connessioni degli endpoint privati, aprire il portale del servizio di sincronizzazione archiviazione, passare alla sezione Impostazioni e fare clic su Rete.
 
- Supporto del cloud a livelli per dimensioni del cluster di volumi maggiori di 64KiB
    - La creazione di livelli cloud supporta ora dimensioni del cluster di volumi fino a 2MiB in Server 2019. Per altre informazioni, vedere [Qual è la dimensione minima del file a livello?](https://docs.microsoft.com/azure/storage/files/storage-sync-choose-cloud-tiering-policies#minimum-file-size-for-a-file-to-tier).
 
- Misurare la larghezza di banda e la latenza Sincronizzazione file di Azure servizio e l'account di archiviazione
    - Il Test-StorageSyncNetworkConnectivity cmdlet può ora essere usato per misurare la latenza e la larghezza di banda per il Sincronizzazione file di Azure e l'account di archiviazione. La latenza per il servizio Sincronizzazione file di Azure e l'account di archiviazione viene misurata per impostazione predefinita quando si esegue il cmdlet .  La larghezza di banda per il caricamento e il download nell'account di archiviazione viene misurata quando si usa il parametro "-MeasureBandwidth".
 
        Ad esempio, per misurare la larghezza di banda e la latenza per il servizio Sincronizzazione file di Azure e l'account di archiviazione, eseguire i comandi di PowerShell seguenti:
 
        ```powershell
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
        Test-StorageSyncNetworkConnectivity -MeasureBandwidth 
        ``` 
 
- Miglioramento dei messaggi di errore nel portale quando la creazione dell'endpoint server non riesce
    - Sono stati ricevuti commenti e suggerimenti e sono stati migliorati i messaggi di errore e le linee guida quando la creazione dell'endpoint server non riesce.
 
- Miglioramenti vari delle prestazioni e dell'affidabilità
    - Miglioramento delle prestazioni di rilevamento delle modifiche per rilevare i file modificati nella condivisione file di Azure.
    - Miglioramenti delle prestazioni per le sessioni di sincronizzazione di riconciliazione. 
    - Miglioramenti della sincronizzazione per ridurre ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED e ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED errori.
    - Correzione di un bug che causa il danneggiamento dei dati se è abilitata la funzionalità cloud a livelli e i file a livelli vengono copiati usando Robocopy con il parametro /B.
    - Correzione di un bug che può causare l'errore di livelli dei file in Server 2019 se la deduplicazione dati è abilitata nel volume.
    - Correzione di un bug che può causare la mancata compressione dei file da parte di AFSDiag se un file è più grande di 2GiB.

### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](file-sync-planning.md#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](file-sync-planning.md) e [Come distribuire Sincronizzazione file di Azure](file-sync-deployment-guide.md).

- È necessario un riavvio per i server che dispongono di un'installazione Sincronizzazione file di Azure'agente di distribuzione.
- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nell'opzione di distribuzione Nano Server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria. Per altre informazioni, vedere [Risorse di sistema consigliate](file-sync-planning.md#recommended-system-resources).
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](file-sync-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può portare a risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- File con caratteri non supportati. Vedere [Guida alla risoluzione dei problemi](file-sync-troubleshoot.md#handling-unsupported-characters) per un elenco dei caratteri non supportati.
- File o directory che terminano con un punto.
- Percorsi che contengono più di 2.048 caratteri.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza usato per il controllo.
- Attributi estesi.
- Flussi dei dati alternativi.
- Reparse point.
- Collegamenti reali.
- La compressione (se impostata in un file server) non viene mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint.
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati.

    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati vengono sempre crittografati quando sono inattivi in Azure.
 
### <a name="server-endpoint"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e altri file system non sono attualmente supportati da Sincronizzazione file di Azure.
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Per sincronizzare immediatamente i file modificati nella condivisione file di Azure, è possibile usare il cmdlet di PowerShell [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection), in modo da avviare manualmente il rilevamento delle modifiche nella condivisione file di Azure. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- Il servizio di sincronizzazione archiviazione e/o l'account di archiviazione possono essere spostati in un gruppo di risorse, una sottoscrizione o un tenant Azure AD diverso. Dopo lo spostamento del servizio di sincronizzazione archiviazione o dell'account di archiviazione, è necessario concedere all'applicazione Microsoft.StorageSync l'accesso all'account di archiviazione (vedere Verificare che Sincronizzazione file di Azure abbia accesso [all'account di archiviazione).](file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)

    > [!Note]  
    > Quando si crea l'endpoint cloud, il servizio di sincronizzazione archiviazione e l'account di archiviazione devono trovarsi nello stesso tenant di Azure AD. Dopo aver creato l'endpoint cloud, il servizio di sincronizzazione archiviazione e l'account di archiviazione possono essere spostati in tenant di Azure AD diversi.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.

## <a name="agent-version-11300"></a>Versione dell'agente 11.3.0.0
Le note sulla versione seguenti sono relative alla versione 11.3.0.0 dell'agente Sincronizzazione file di Azure rilasciato il 7 aprile 2021. Queste note si sommano alle note sulla versione elencate per la versione 11.1.0.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti 
Correzione di un bug che causa il danneggiamento dei dati se è abilitata la funzionalità cloud a livelli e i file a livelli vengono copiati usando Robocopy con il parametro /B.

## <a name="agent-version-11200"></a>Versione dell'agente 11.2.0.0
Le note sulla versione seguenti sono relative alla versione 11.2.0.0 dell'agente Sincronizzazione file di Azure rilasciato il 2 febbraio 2021. Queste note sono oltre alle note sulla versione elencate per la versione 11.1.0.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti 
- Se una sessione di sincronizzazione viene annullata a causa di un numero elevato di errori per elemento, la sincronizzazione può passare attraverso la riconciliazione all'avvio di una nuova sessione se il servizio Sincronizzazione file di Azure determina che è necessaria una sessione di sincronizzazione personalizzata per correggere gli errori per elemento.
- La registrazione di un server tramite il cmdlet Register-AzStorageSyncServer potrebbe non riuscire con l'errore "Eccezione non gestita".
- Nuovo cmdlet di PowerShell (Add-StorageSyncAllowedServerEndpointPath) per configurare i percorsi degli endpoint server consentiti in un server. Questo cmdlet è utile per gli scenari in cui la distribuzione di Sincronizzazione file di Azure è gestita da un provider di servizi o un provider di servizi Cloud Solution Provider (Cloud Solution Provider) e il cliente vuole configurare i percorsi degli endpoint server consentiti in un server. Quando si crea un endpoint server, se il percorso specificato non è presente nell'elenco allowlist, la creazione dell'endpoint server avrà esito negativo. Si noti che si tratta di una funzionalità facoltativa e tutti i percorsi supportati sono consentiti per impostazione predefinita quando si crea un endpoint server.  

    
    - Per aggiungere un percorso di endpoint server consentito, eseguire i comandi di PowerShell seguenti nel server:

    ```powershell
    Import-Module 'C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll' -verbose
    Add-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  

    - Per ottenere l'elenco dei percorsi supportati, eseguire il comando di PowerShell seguente:
    
    ```powershell
    Get-StorageSyncAllowedServerEndpointPath
    ```     
    - Per rimuovere un percorso, eseguire il comando di PowerShell seguente:
    
    ```powershell
    Remove-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  
## <a name="agent-version-11100"></a>Agent versione 11.1.0.0
Le note sulla versione seguenti sono relative alla versione 11.1.0.0 dell'agente Sincronizzazione file di Azure (rilasciata il 4 novembre 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti
- Nuove modalità di tiering cloud per controllare il download iniziale e il richiamo proattivo
    - Modalità di download iniziale: è ora possibile scegliere come si vuole che i file siano scaricati inizialmente nel nuovo endpoint server. Si vuole che tutti i file a livelli o il maggior numero possibile di file scaricati nel server dal timestamp dell'ultima modifica? È possibile farlo. Non è possibile usare la distribuzione a livelli nel cloud? È ora possibile scegliere di evitare file a livelli nel sistema. Per altre informazioni, vedere [la sezione Creare un endpoint server](../file-sync/file-sync-deployment-guide.md?tabs=azure-portal%252cproactive-portal#create-a-server-endpoint) nella documentazione Sincronizzazione file di Azure distribuzione.
    - Modalità di richiamo proattivo: ogni volta che un file viene creato o modificato, è possibile richiamarlo in modo proattivo nei server specificati all'interno dello stesso gruppo di sincronizzazione. In questo modo il file è immediatamente disponibile per l'utilizzo in ogni server specificato. I team di tutto il mondo lavorano sugli stessi dati? Abilitare il richiamo proattivo in modo che, quando il team arriva la mattina successiva, tutti i file aggiornati da un team in un fuso orario diverso siano scaricati e pronti per iniziare. Per altre informazioni, vedere la sezione Richiamare in modo proattivo i file nuovi e modificati da una condivisione file di [Azure](../file-sync/file-sync-deployment-guide.md?tabs=azure-portal%252cproactive-portal#proactively-recall-new-and-changed-files-from-an-azure-file-share) nella documentazione Sincronizzazione file di Azure distribuzione.

- Escludere le applicazioni dal rilevamento dell'ora dell'ultimo accesso a livelli nel cloud È ora possibile escludere le applicazioni dal rilevamento dell'ora dell'ultimo accesso. Quando un'applicazione accede a un file, l'ora dell'ultimo accesso per il file viene aggiornata nel database cloud a livelli. Le applicazioni che analizzano file system come l'antivirus causano la stessa ora dell'ultimo accesso di tutti i file che influisce sul momento in cui i file sono a livelli.

    Per escludere le applicazioni dal rilevamento dell'ora dell'ultimo accesso, aggiungere il nome del processo all'impostazione del Registro di sistema HeatTrackingProcessNameExclusionList che si trova in HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

    Esempio: reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNameExclusionList /t REG_MULTI_SZ /d "SampleApp.exe\0AnotherApp.exe" /f

    > [!Note]  
    > I processi di deduplicazione dati e file server Resource Manager (FSRM) sono esclusi per impostazione predefinita (hard coded) e l'elenco di esclusione dei processi viene aggiornato ogni 5 minuti.

- Miglioramenti vari delle prestazioni e dell'affidabilità
    - Miglioramento delle prestazioni di rilevamento delle modifiche per rilevare i file modificati nella condivisione file di Azure.
    - Miglioramento delle prestazioni di caricamento della sincronizzazione.
    - Il caricamento iniziale viene ora eseguito da uno snapshot vss che riduce gli errori per elemento e gli errori della sessione di sincronizzazione.
    - Miglioramenti dell'affidabilità della sincronizzazione per determinati modelli di I/O.
    - Correzione di un bug per impedire che il database di sincronizzazione torni indietro nel tempo nei cluster di failover quando si verifica un failover.
    - Miglioramento delle prestazioni di richiamo quando si accede a un file a livelli.

### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](../file-sync/file-sync-planning.md#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](../file-sync/file-sync-planning.md) e [Come distribuire Sincronizzazione file di Azure](../file-sync/file-sync-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nell'opzione di distribuzione Nano Server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria. Per altre informazioni, vedere [Risorse di sistema consigliate](../file-sync/file-sync-planning.md#recommended-system-resources).
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](../file-sync/file-sync-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può portare a risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- File con caratteri non supportati. Vedere [Guida alla risoluzione dei problemi](../file-sync/file-sync-troubleshoot.md#handling-unsupported-characters) per un elenco dei caratteri non supportati.
- File o directory che terminano con un punto.
- Percorsi che contengono più di 2.048 caratteri.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza usato per il controllo.
- Attributi estesi.
- Flussi dei dati alternativi.
- Reparse point.
- Collegamenti reali.
- La compressione (se impostata in un file server) non viene mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint.
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati.

    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati vengono sempre crittografati quando sono inattivi in Azure.
 
### <a name="server-endpoint"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e altri file system non sono attualmente supportati da Sincronizzazione file di Azure.
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Per sincronizzare immediatamente i file modificati nella condivisione file di Azure, è possibile usare il cmdlet di PowerShell [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection), in modo da avviare manualmente il rilevamento delle modifiche nella condivisione file di Azure. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- Il servizio di sincronizzazione archiviazione e/o l'account di archiviazione possono essere spostati in un gruppo di risorse, una sottoscrizione o un tenant Azure AD diverso. Dopo aver spostato il servizio di sincronizzazione archiviazione o l'account di archiviazione, è necessario concedere all'applicazione Microsoft.StorageSync l'accesso all'account di archiviazione (vedere Assicurarsi che Sincronizzazione file di Azure abbia accesso [all'account di archiviazione](../file-sync/file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Quando si crea l'endpoint cloud, il servizio di sincronizzazione archiviazione e l'account di archiviazione devono trovarsi nello stesso tenant di Azure AD. Dopo aver creato l'endpoint cloud, il servizio di sincronizzazione archiviazione e l'account di archiviazione possono essere spostati in tenant di Azure AD diversi.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.
    > [!Warning]  
    > L'opzione Robocopy /B non è supportata con Sincronizzazione file di Azure. L'uso dell'opzione Robocopy /B con un endpoint Sincronizzazione file di Azure server come origine può causare il danneggiamento dei file.

## <a name="agent-version-10100"></a>Agent versione 10.1.0.0
Le note sulla versione seguenti sono relative alla versione 10.1.0.0 dell'agente Sincronizzazione file di Azure rilasciato il 5 giugno 2020. Queste note sono oltre alle note sulla versione elencate per le versioni 10.0.0.0 e 10.0.2.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Supporto degli endpoint privati di Azure
    - Il traffico di sincronizzazione con il servizio di sincronizzazione archiviazione può ora essere inviato a un endpoint privato. Ciò consente il tunneling su una connessione ExpressRoute o VPN. Per altre informazioni, vedere [Configurazione di Sincronizzazione file di Azure endpoint di rete.](../file-sync/file-sync-networking-endpoints.md)
- La metrica File sincronizzati ora visualizza lo stato di avanzamento durante l'esecuzione di una sincronizzazione di grandi dimensioni anziché alla fine.
- Miglioramenti dell'affidabilità vari per l'installazione dell'agente, la tiering cloud, la sincronizzazione e i dati di telemetria

## <a name="agent-version-10020"></a>Versione dell'agente 10.0.2.0
Le note sulla versione seguenti si riferiscono alla versione 10.0.2.0 dell'agente di Sincronizzazione file di Azure (data di rilascio 19 maggio 2020). Queste note si aggiungono a quelle elencate per la versione 10.0.0.0.

Problema risolto in questa versione:  
- L'agente di sincronizzazione archiviazione (FileSyncSvc) si interrompe di frequente dopo l'installazione dell'agente Sincronizzazione file di Azure versione 10.

> [!Note]  
>Questa versione non è stata distribuita in anteprima ai server configurati per l'aggiornamento automatico quando è stata resa disponibile una nuova versione. Per installare questo aggiornamento, usare Microsoft Update o Microsoft Update Catalog. Per istruzioni di installazione, vedere [KB4522412](https://support.microsoft.com/help/4522412).

## <a name="agent-version-10000"></a>Versione dell'agente 10.0.0.0
Le note sulla versione seguenti si riferiscono alla versione 10.0.0.0 dell'agente di Sincronizzazione file di Azure (data di rilascio 9 aprile 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Miglioramento dello stato di sincronizzazione nel portale
    - Con la versione 10 dell'agente, il portale di Azure inizierà a visualizzare il tipo di sessione di sincronizzazione in esecuzione: ad esempio download iniziale, download normale, richiamo in background (casi di ripristino di emergenza rapido) e simili. 

- Miglioramento dell'esperienza del portale del cloud a livelli
    - Se si dispone di file che non riescono a eseguire la suddivisione in livelli o il richiamo, è ora possibile visualizzare gli errori di suddivisione in livelli nelle proprietà dell'endpoint server.
    - Per un endpoint server sono disponibili informazioni aggiuntive sul cloud a livelli:
        - Dimensione cache locale
        - Efficienza di utilizzo della cache
        - Dettagli dei criteri del cloud a livelli: dimensioni del volume, spazio disponibile corrente o data e ora dell'ultimo accesso del file meno recente nella cache locale.
    - Queste modifiche verranno trasferite nel portale di Azure subito dopo il rilascio della versione 10 dell'agente iniziale.

- Supporto per lo spostamento del servizio di sincronizzazione archiviazione e/o dell'account di archiviazione in un tenant Azure Active Directory diverso
    - Sincronizzazione file di Azure supporta ora lo spostamento del servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse, in una sottoscrizione o in un tenant di Azure AD differente.
    
- Miglioramenti vari delle prestazioni e dell'affidabilità
    - Il rilevamento delle modifiche nella condivisione file di Azure potrebbe non riuscire se le regole della rete virtuale (VNET) e del firewall sono configurate nell'account di archiviazione.
    - Consumo di memoria ridotto associato al richiamo. 
    - Miglioramento delle prestazioni quando si usa il cmdlet [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection).
    - Altri miglioramenti vari dell'affidabilità. 
    
### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](../file-sync/file-sync-planning.md#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](../file-sync/file-sync-planning.md) e [Come distribuire Sincronizzazione file di Azure](../file-sync/file-sync-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nell'opzione di distribuzione Nano Server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](../file-sync/file-sync-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può portare a risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- File con caratteri non supportati. Vedere [Guida alla risoluzione dei problemi](../file-sync/file-sync-troubleshoot.md#handling-unsupported-characters) per un elenco dei caratteri non supportati.
- File o directory che terminano con un punto.
- Percorsi che contengono più di 2.048 caratteri.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza usato per il controllo.
- Attributi estesi.
- Flussi dei dati alternativi.
- Reparse point.
- Collegamenti reali.
- La compressione (se impostata in un file server) non viene mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint.
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati.

    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati vengono sempre crittografati quando sono inattivi in Azure.
 
### <a name="server-endpoint"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e altri file system non sono attualmente supportati da Sincronizzazione file di Azure.
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Per sincronizzare immediatamente i file modificati nella condivisione file di Azure, è possibile usare il cmdlet di PowerShell [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection), in modo da avviare manualmente il rilevamento delle modifiche nella condivisione file di Azure. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- Il servizio di sincronizzazione archiviazione e/o l'account di archiviazione possono essere spostati in un gruppo di risorse, una sottoscrizione o un tenant Azure AD diverso. Dopo aver spostato il servizio di sincronizzazione archiviazione o l'account di archiviazione, è necessario concedere all'applicazione Microsoft.StorageSync l'accesso all'account di archiviazione (vedere Assicurarsi che Sincronizzazione file di Azure abbia accesso [all'account di archiviazione](../file-sync/file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Quando si crea l'endpoint cloud, il servizio di sincronizzazione archiviazione e l'account di archiviazione devono trovarsi nello stesso tenant di Azure AD. Dopo aver creato l'endpoint cloud, il servizio di sincronizzazione archiviazione e l'account di archiviazione possono essere spostati in tenant di Azure AD diversi.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.
