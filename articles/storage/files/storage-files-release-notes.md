---
title: Note sulla versione dell'agente Sincronizzazione file di Azure | Microsoft Docs
description: Leggere le note sulla versione per l'agente di Sincronizzazione file di Azure, che consente di centralizzare le condivisioni file dell'organizzazione in File di Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 3/3/2021
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 5549fc3b63b76c6158ae7399e6d94a43d2d4f28f
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435189"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Note sulla versione dell'agente Sincronizzazione file di Azure
Sincronizzazione file di Azure consente di centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Le installazioni Windows Server vengono trasformate in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS e FTPS. Si può usare qualsiasi numero di cache in tutto il mondo.

Questo articolo illustra le note sulla versione per le versioni supportate dell'agente Sincronizzazione file di Azure.

## <a name="supported-versions"></a>Versioni supportate
Sono supportate le seguenti versioni di Sincronizzazione file di Azure Agent:

| Attività cardine | Numero di versione dell'agente | Data di rilascio | Stato |
|----|----------------------|--------------|------------------|
| Versione 11.2- [KB4539952](https://support.microsoft.com/topic/azure-file-sync-agent-v11-2-release-february-2021-c956eaf0-cd8e-4511-98c0-e5a1f2c84048)| 11.2.0.0 | 2 febbraio 2021 | Supportato |
| Versione 11.1- [KB4539951](https://support.microsoft.com/help/4539951)| 11.1.0.0 | 4 novembre 2020 | Supportato |
| Versione 10.1- [KB4522411](https://support.microsoft.com/help/4522411)| 10.1.0.0 | 5 giugno 2020 | Supportato-la versione dell'agente scadrà il 7 giugno 2021 |
| Aggiornamento cumulativo di maggio 2020 - [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 19 maggio 2020 | Supportato-la versione dell'agente scadrà il 7 giugno 2021 |
| Versione 10 - [KB4522409](https://support.microsoft.com/help/4522409)| 10.0.0.0 | 9 aprile 2020 | Supportato-la versione dell'agente scadrà il 7 giugno 2021 |

## <a name="unsupported-versions"></a>Versioni non supportate
Le seguenti versioni di Sincronizzazione file di Azure Agent sono scadute e non sono più supportate:

| Attività cardine | Numero di versione dell'agente | Data di rilascio | Stato |
|----|----------------------|--------------|------------------|
| Versione v9 | 9.0.0.0-9.1.0.0 | N/D | Non supportato-la versione dell'agente è scaduta il 16 febbraio 2021 |
| Versione V8 | 8.0.0.0 | N/D | Non supportato-la versione dell'agente è scaduta il 12 gennaio 2021 |
| Versione v7 | 7.0.0.0-7.2.0.0 | N/D | Non supportato-le versioni dell'agente sono scadute il 1 ° settembre 2020 |
| Versione 6 | 6.0.0.0 - 6.3.0.0 | N/D | Non supportato: la versione dell'agente è scaduta il 21 aprile 2020 |
| Versione 5 | 5.0.2.0 - 5.2.0.0 | N/D | Non supportato: la versione dell'agente è scaduta il 18 marzo 2020 |
| Versione v4 | 4.0.1.0 - 4.3.0.0 | N/D | Non supportato: la versione dell'agente è scaduta il 6 novembre 2019 |
| Versione 3 | 3.1.0.0 - 3.4.0.0 | N/D | Non supportato: la versione dell'agente è scaduta il 19 agosto 2019 |
| Agenti pre-disponibilità generale | 1.1.0.0 - 3.0.13.0 | N/D | Non supportato: versione dell'agente scaduta il 1° ottobre 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Criteri di aggiornamento dell'agente Sincronizzazione file di Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-11200"></a>Versione dell'agente 11.2.0.0
Le note sulla versione seguenti sono relative alla versione 11.2.0.0 dell'agente Sincronizzazione file di Azure rilasciato il 2 febbraio 2021. Queste note si aggiungono alle note sulla versione elencate per la versione 11.1.0.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti 
- Se una sessione di sincronizzazione viene annullata a causa di un numero elevato di errori per elemento, la sincronizzazione può attraversare la riconciliazione quando viene avviata una nuova sessione se il servizio Sincronizzazione file di Azure determina che è necessaria una sessione di sincronizzazione personalizzata per correggere gli errori per singolo elemento.
- La registrazione di un server con il cmdlet Register-AzStorageSyncServer potrebbe non riuscire con l'errore "eccezione non gestita".
- Nuovo cmdlet di PowerShell (Add-StorageSyncAllowedServerEndpointPath) per configurare i percorsi degli endpoint server consentiti in un server. Questo cmdlet è utile per gli scenari in cui la distribuzione di Sincronizzazione file di Azure viene gestita da un provider di soluzioni cloud (CSP) o da un provider di servizi e il cliente desidera configurare i percorsi degli endpoint server consentiti in un server. Quando si crea un endpoint server, se il percorso specificato non è presente nell'elenco Consenti, la creazione dell'endpoint del server avrà esito negativo. Si noti che si tratta di una funzionalità facoltativa e tutti i percorsi supportati sono consentiti per impostazione predefinita durante la creazione di un endpoint server.  

    
    - Per aggiungere un percorso dell'endpoint server consentito, eseguire i comandi di PowerShell seguenti nel server:

    ```powershell
    Import-Module 'C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll' -verbose
    Add-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  

    - Per ottenere l'elenco dei percorsi supportati, eseguire il comando PowerShell seguente:
    
    ```powershell
    Get-StorageSyncAllowedServerEndpointPath
    ```     
    - Per rimuovere un percorso, eseguire il comando PowerShell seguente:
    
    ```powershell
    Remove-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  
## <a name="agent-version-11100"></a>Versione dell'agente 11.1.0.0
Le note sulla versione seguenti sono relative alla versione 11.1.0.0 dell'agente di Sincronizzazione file di Azure (rilasciato il 4 novembre 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti
- Nuove modalità di suddivisione in livelli nel cloud per controllare il download iniziale e il richiamo proattivo
    - Modalità di download iniziale: ora è possibile scegliere come si desidera che i file vengano scaricati inizialmente nel nuovo endpoint server. Si desidera che tutti i file siano archiviati a livelli o il maggior numero possibile di file scaricati nel server dal timestamp dell'Ultima modifica? Questa operazione può essere eseguita. Non è possibile usare la suddivisione in livelli nel cloud? È ora possibile scegliere di evitare i file a livelli nel sistema. Per ulteriori informazioni, vedere la sezione [creare un endpoint server](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#create-a-server-endpoint) nella documentazione relativa alla distribuzione di sincronizzazione file di Azure.
    - Modalità di richiamo proattivo: ogni volta che un file viene creato o modificato, è possibile richiamarlo in modo proattivo nei server specificati nello stesso gruppo di sincronizzazione. In questo modo il file sarà immediatamente disponibile per l'utilizzo in ogni server specificato. I team in tutto il mondo lavorano sugli stessi dati? Abilitare il richiamo proattivo, in modo che quando il team arriva al mattino successivo, tutti i file aggiornati da un team in un fuso orario diverso vengono scaricati e pronti per l'uso. Per altre informazioni, vedere [richiamare in modo proattivo i file nuovi e modificati da una condivisione file di Azure](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#proactively-recall-new-and-changed-files-from-an-azure-file-share) nella documentazione relativa alla distribuzione di sincronizzazione file di Azure.

- Escludi le applicazioni dal rilevamento dell'ora dell'ultimo accesso al cloud. è ora possibile escludere le applicazioni dal rilevamento dell'ora dell'ultimo accesso. Quando un'applicazione accede a un file, l'ora dell'ultimo accesso per il file viene aggiornata nel database di suddivisione in livelli cloud. Le applicazioni che analizzano il file system come anti-virus fanno sì che tutti i file abbiano la stessa ora dell'ultimo accesso, che influisca sul momento in cui i file sono a livelli.

    Per escludere le applicazioni dal rilevamento dell'ora dell'ultimo accesso, aggiungere il nome del processo all'impostazione del registro di sistema HeatTrackingProcessNameExclusionList che si trova in HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

    Esempio: REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync"/v HeatTrackingProcessNameExclusionList/t REG_MULTI_SZ/d "SampleApp.exe\0AnotherApp.exe"/f

    > [!Note]  
    > I processi di deduplicazione dati e file server Gestione risorse (FSRM) sono esclusi per impostazione predefinita (hardcoded) e l'elenco di esclusione del processo viene aggiornato ogni 5 minuti.

- Miglioramenti vari delle prestazioni e dell'affidabilità
    - Miglioramento delle prestazioni di rilevamento delle modifiche per rilevare i file che sono stati modificati nella condivisione file di Azure.
    - Miglioramento delle prestazioni di caricamento della sincronizzazione.
    - Il caricamento iniziale viene ora eseguito da uno snapshot VSS che riduce gli errori per singolo elemento e gli errori della sessione di sincronizzazione.
    - Miglioramenti dell'affidabilità di sincronizzazione per determinati modelli di I/O.
    - Correzione di un bug per impedire al database di sincronizzazione di tornare indietro nel tempo nei cluster di failover quando si verifica un failover.
    - Miglioramento delle prestazioni di richiamo durante l'accesso a un file a livelli.

### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](./storage-sync-files-planning.md#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nell'opzione di distribuzione Nano Server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria. Per altre informazioni, vedere [Risorse di sistema consigliate](./storage-sync-files-planning.md#recommended-system-resources).
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può portare a risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- File con caratteri non supportati. Vedere [Guida alla risoluzione dei problemi](storage-sync-files-troubleshoot.md#handling-unsupported-characters) per un elenco dei caratteri non supportati.
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
- Il servizio di sincronizzazione archiviazione e/o l'account di archiviazione possono essere spostati in un gruppo di risorse, una sottoscrizione o un tenant di Azure AD diversi. Dopo lo spostamento dell'account di archiviazione o del servizio di sincronizzazione archiviazione, è necessario concedere all'applicazione Microsoft. StorageSync l'accesso all'account di archiviazione (vedere [verificare che sincronizzazione file di Azure abbia accesso all'account di archiviazione](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Quando si crea l'endpoint cloud, il servizio di sincronizzazione archiviazione e l'account di archiviazione devono trovarsi nello stesso tenant di Azure AD. Dopo aver creato l'endpoint cloud, il servizio di sincronizzazione archiviazione e l'account di archiviazione possono essere spostati in tenant di Azure AD diversi.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.
    > [!Warning]  
    > L'opzione Robocopy/B non è supportata con Sincronizzazione file di Azure. L'uso dell'opzione Robocopy/B con un endpoint del server Sincronizzazione file di Azure come origine può causare il danneggiamento del file.

## <a name="agent-version-10100"></a>Versione dell'agente 10.1.0.0
Le note sulla versione seguenti sono relative alla versione 10.1.0.0 dell'agente Sincronizzazione file di Azure rilasciato il 5 giugno 2020. Queste note si aggiungono alle note sulla versione elencate per la versione 10.0.0.0 e 10.0.2.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Supporto per endpoint privati di Azure
    - È ora possibile inviare il traffico di sincronizzazione al servizio di sincronizzazione archiviazione a un endpoint privato. Questo consente il tunneling su una connessione VPN o ExpressRoute. Per altre informazioni, vedere [Configuring sincronizzazione file di Azure Network Endpoints](./storage-sync-files-networking-endpoints.md).
- Metrica file sincronizzati ora visualizzerà lo stato di avanzamento mentre è in esecuzione una sincronizzazione di grandi dimensioni, anziché alla fine.
- Vari miglioramenti dell'affidabilità per l'installazione dell'agente, la suddivisione in livelli cloud, la sincronizzazione e la telemetria

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

- Supporto per lo trasferimento del servizio di sincronizzazione archiviazione e/o dell'account di archiviazione in un tenant di Azure Active Directory diverso
    - Sincronizzazione file di Azure supporta ora lo spostamento del servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse, in una sottoscrizione o in un tenant di Azure AD differente.
    
- Miglioramenti vari delle prestazioni e dell'affidabilità
    - Il rilevamento delle modifiche nella condivisione file di Azure potrebbe non riuscire se le regole della rete virtuale (VNET) e del firewall sono configurate nell'account di archiviazione.
    - Consumo di memoria ridotto associato al richiamo. 
    - Miglioramento delle prestazioni quando si usa il cmdlet [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection).
    - Altri miglioramenti vari dell'affidabilità. 
    
### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](./storage-sync-files-planning.md#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nell'opzione di distribuzione Nano Server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può portare a risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- File con caratteri non supportati. Vedere [Guida alla risoluzione dei problemi](storage-sync-files-troubleshoot.md#handling-unsupported-characters) per un elenco dei caratteri non supportati.
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
- Il servizio di sincronizzazione archiviazione e/o l'account di archiviazione possono essere spostati in un gruppo di risorse, una sottoscrizione o un tenant di Azure AD diversi. Dopo lo spostamento dell'account di archiviazione o del servizio di sincronizzazione archiviazione, è necessario concedere all'applicazione Microsoft. StorageSync l'accesso all'account di archiviazione (vedere [verificare che sincronizzazione file di Azure abbia accesso all'account di archiviazione](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Quando si crea l'endpoint cloud, il servizio di sincronizzazione archiviazione e l'account di archiviazione devono trovarsi nello stesso tenant di Azure AD. Dopo aver creato l'endpoint cloud, il servizio di sincronizzazione archiviazione e l'account di archiviazione possono essere spostati in tenant di Azure AD diversi.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.
