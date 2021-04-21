---
title: Monitorare Sincronizzazione file di Azure | Microsoft Docs
description: Esaminare come monitorare la distribuzione Sincronizzazione file di Azure usando Monitoraggio di Azure, il servizio di sincronizzazione archiviazione e Windows Server.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8e9fbd5fd8fc90681432ee8403b6dd139d02a5a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796998"
---
# <a name="monitor-azure-file-sync"></a>Monitorare Sincronizzazione file di Azure

Usare Sincronizzazione file di Azure per centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Il servizio Sincronizzazione file di Azure trasforma Windows Server in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS (Network File System) e FTPS (File Transfer Protocol Service). Si può usare qualsiasi numero di cache necessario in tutto il mondo.

Questo articolo descrive come monitorare la distribuzione Sincronizzazione file di Azure usando Monitoraggio di Azure, il servizio di sincronizzazione archiviazione e Windows Server.

In questa guida vengono trattati gli scenari seguenti: 
- Visualizzare Sincronizzazione file di Azure metriche in Monitoraggio di Azure.
- Creare avvisi in Monitoraggio di Azure per notificare in modo proattivo le condizioni critiche.
- Visualizzare l'integrità della Sincronizzazione file di Azure distribuzione usando il portale di Azure.
- Come usare i registri eventi e i contatori delle prestazioni nei server Windows per monitorare l'integrità della Sincronizzazione file di Azure distribuzione. 

## <a name="azure-monitor"></a>Monitoraggio di Azure

Usare [Monitoraggio di Azure](../../azure-monitor/overview.md) visualizzare le metriche e configurare gli avvisi per la sincronizzazione, il cloud a livelli e la connettività del server.  

### <a name="metrics"></a>Metriche

Le metriche per Sincronizzazione file di Azure sono abilitate per impostazione predefinita e vengono inviate al Monitoraggio di Azure ogni 15 minuti.

**Come visualizzare le Sincronizzazione file di Azure metriche in Monitoraggio di Azure**
1. Passare al servizio di **sincronizzazione archiviazione** nel **portale di Azure** fare clic su **Metriche.**
2. Fare clic **sull'elenco** a discesa Metrica e selezionare la metrica che si vuole visualizzare.

![Screenshot delle Sincronizzazione file di Azure metriche](media/storage-sync-files-troubleshoot/file-sync-metrics.png)

Le metriche seguenti per la Sincronizzazione file di Azure sono disponibili in Monitoraggio di Azure:

| Nome metrica | Descrizione |
|-|-|
| Byte sincronizzati | Dimensioni dei dati trasferiti (caricamento e scaricamento).<br><br>Unità: Byte<br>Tipo di aggregazione: Somma<br>Dimensioni applicabili: Nome endpoint server, Direzione sincronizzazione, Nome gruppo di sincronizzazione |
| Richiamo cloud a livelli | Dimensioni dei dati richiamati.<br><br>**Nota:** questa metrica verrà rimossa in futuro. Usare la metrica Cloud tiering recall size (Dimensioni richiamo cloud a livelli) per monitorare le dimensioni dei dati richiamati.<br><br>Unità: Byte<br>Tipo di aggregazione: Somma<br>Dimensione applicabile: Nome server |
| Cloud tiering recall size (Dimensioni richiamo cloud a livelli) | Dimensioni dei dati richiamati.<br><br>Unità: Byte<br>Tipo di aggregazione: Somma<br>Dimensioni applicabili: Nome server, Nome gruppo di sincronizzazione |
| Cloud tiering recall size by application (Dimensioni richiamo cloud a livelli per applicazione) | Dimensioni dei dati richiamati dall'applicazione.<br><br>Unità: Byte<br>Tipo di aggregazione: Somma<br>Dimensioni applicabili: Nome applicazione, Nome server, Nome gruppo di sincronizzazione |
| Cloud tiering recall throughput (Velocità effettiva richiamo cloud a livelli) | Dimensioni della velocità effettiva di richiamo dei dati.<br><br>Unità: Byte<br>Tipo di aggregazione: Somma<br>Dimensioni applicabili: Nome server, Nome gruppo di sincronizzazione |
| File non sincronizzati | Numero di file che non è possibile sincronizzare.<br><br>Unità: Conteggio<br>Tipi di aggregazione: Media, Somma<br>Dimensioni applicabili: Nome endpoint server, Direzione sincronizzazione, Nome gruppo di sincronizzazione |
| File sincronizzati | Numero di file trasferiti (caricamento e scaricamento)<br><br>Unità: Conteggio<br>Tipo di aggregazione: Somma<br>Dimensioni applicabili: Nome endpoint server, Direzione sincronizzazione, Nome gruppo di sincronizzazione |
| Stato online del server | Numero di heartbeat ricevuti dal server.<br><br>Unità: Conteggio<br>Tipo di aggregazione: Massimo<br>Dimensione applicabile: Nome server |
| Risultato della sessione di sincronizzazione | Risultato della sessione di sincronizzazione (1 = sessione di sincronizzazione con esito positivo; 0 = sessione di sincronizzazione con esito negativo)<br><br>Unità: Conteggio<br>Tipi di aggregazione: massimo<br>Dimensioni applicabili: Nome endpoint server, Direzione sincronizzazione, Nome gruppo di sincronizzazione |

### <a name="alerts"></a>Avvisi

Gli avvisi notificano in modo proattivo quando vengono riscontrate importanti condizioni nei dati di monitoraggio. Per altre informazioni sulla configurazione degli avvisi in Monitoraggio di Azure, vedere [Panoramica degli avvisi in Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md).

**Come creare avvisi per Sincronizzazione file di Azure**

1. Passare al servizio **di sincronizzazione archiviazione** nel **portale di Azure**. 
2. Fare **clic su** Avvisi nella sezione Monitoraggio e quindi su + Nuova regola di **avviso.**
3. Fare **clic su Seleziona** condizione e specificare le informazioni seguenti per l'avviso: 
    - **Metrica**
    - **Nome dimensione**
    - **Logica avvisi**
4. Fare **clic su Seleziona gruppo** di azioni e aggiungere un gruppo di azioni (posta elettronica, SMS e così via) all'avviso selezionando un gruppo di azioni esistente o creando un nuovo gruppo di azioni.
5. Compilare i dettagli **dell'avviso,** **ad esempio Nome regola di avviso**, **Descrizione** **e Gravità**.
6. Fare **clic su Crea regola di** avviso per creare l'avviso.  

La tabella seguente elenca alcuni scenari di esempio da monitorare e la metrica appropriata da usare per l'avviso:

| Scenario | Metrica da usare per l'avviso |
|-|-|
| L'integrità dell'endpoint server mostra un errore nel portale | Risultato della sessione di sincronizzazione |
| Non è possibile sincronizzare i file con un server o un endpoint cloud | File non sincronizzati |
| Il server registrato non riesce a comunicare con il servizio di sincronizzazione archiviazione | Stato online del server |
| Le dimensioni di richiamo per il cloud a livelli hanno superato 500GiB in un giorno  | Cloud tiering recall size (Dimensioni richiamo cloud a livelli) |

Per istruzioni su come creare avvisi per questi scenari, vedere la [sezione Esempi di](#alert-examples) avvisi.

## <a name="storage-sync-service"></a>Servizio di sincronizzazione archiviazione

Per visualizzare l'integrità della distribuzione Sincronizzazione file di Azure nel portale di Azure **,** passare al servizio di sincronizzazione **archiviazione** e sono disponibili le informazioni seguenti:

- Integrità del server registrato
- Integrità dell'endpoint server
    - File non sincronizzati
    - Attività di sincronizzazione
    - Efficienza del cloud a livelli
    - File non a livelli
    - Errori di richiamo
- Metriche

### <a name="registered-server-health"></a>Integrità del server registrato

Per visualizzare **l'integrità del server registrato** nel portale, passare alla sezione Server **registrati** del servizio **di sincronizzazione archiviazione**.

![Screenshot dell'integrità dei server registrati](media/storage-sync-files-troubleshoot/file-sync-registered-servers.png)

- Se lo **stato del server** registrato è **Online**, il server comunica correttamente con il servizio.
- Se lo **stato del server** registrato è Visualizzato offline, il processo di Monitoraggio sincronizzazione archiviazione (AzureStorageSyncMonitor.exe) non è in esecuzione o il server non è in grado di accedere al servizio Sincronizzazione file di Azure archiviazione.  Per [indicazioni, vedere la documentazione](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#server-endpoint-noactivity) sulla risoluzione dei problemi.

### <a name="server-endpoint-health"></a>Integrità dell'endpoint server

Per visualizzare l'integrità di un **endpoint server** nel portale,  passare alla sezione **Gruppi** di sincronizzazione del servizio di sincronizzazione archiviazione e selezionare un gruppo **di sincronizzazione.**

![Screenshot dell'integrità dell'endpoint server](media/storage-sync-files-troubleshoot/file-sync-server-endpoint-health.png)

- **L'integrità dell'endpoint server** e l'attività di sincronizzazione nel portale si basano sugli eventi di sincronizzazione registrati nel registro eventi di telemetria nel server (ID 9102 e 9302).  Se una sessione di sincronizzazione ha esito negativo a causa di un  errore temporaneo, ad esempio l'errore annullato, l'endpoint server continuerà a essere visualizzato come integro nel portale, purché la sessione di sincronizzazione corrente sia in corso (i file vengono applicati). L'ID evento 9302 è l'evento di stato della sincronizzazione e l'ID evento 9102 viene registrato al termine di una sessione di sincronizzazione.  Per altre informazioni, vedere [Stato di integrità e](file-sync-troubleshoot.md?tabs=server%252cazure-portal#broken-sync) [sincronizzazione della sincronizzazione](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session). Se l'integrità dell'endpoint server mostra **un errore** o **nessuna attività,** vedere la documentazione sulla risoluzione dei [problemi](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#common-sync-errors) per indicazioni.
- Il **numero di file non** sincronizzati nel portale è basato sull'ID evento 9121 registrato nel registro eventi di telemetria nel server. Questo evento viene registrato per ogni errore per elemento al termine della sessione di sincronizzazione. Per risolvere gli errori per elemento, vedere Ricerca per categorie se sono presenti file o cartelle specifici che non [sono sincronizzati?](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).
- Per visualizzare **l'efficienza del cloud** a livelli nel portale, passare a Proprietà endpoint **server** e passare alla sezione **Livelli cloud.** I dati forniti per l'efficienza del cloud a livelli si basano sull'ID evento 9071 registrato nel registro eventi di telemetria nel server. Per altre informazioni, vedere [Monitorare il cloud a livelli.](file-sync-monitor-cloud-tiering.md)
- Per visualizzare **i file non a** livelli e richiamare gli errori nel portale, passare a Proprietà endpoint **server** e passare alla sezione Cloud **a livelli.**  **I file** non a livelli sono basati sull'ID evento 9003  registrato nel registro eventi di telemetria nel server e gli errori di richiamo si basano sull'ID evento 9006. Per esaminare i file che non [](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#how-to-troubleshoot-files-that-fail-to-tier) riescono a livelli o che non vengono richiamati, vedere Come risolvere i problemi dei file che non riescono a livelli e Come risolvere i problemi relativi ai file che [non vengono richiamati.](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#how-to-troubleshoot-files-that-fail-to-be-recalled)

### <a name="metric-charts"></a>Grafici delle metriche

- I grafici delle metriche seguenti sono visualizzabili nel portale del servizio di sincronizzazione archiviazione:

  | Nome metrica | Descrizione | Nome pannello |
  |-|-|-|
  | Byte sincronizzati | Dimensioni dei dati trasferiti (caricamento e scaricamento) | Gruppo di sincronizzazione, Endpoint server |
  | Richiamo cloud a livelli | Dimensione dei dati richiamati | Server registrati |
  | File non sincronizzati | Numero di file che non è possibile sincronizzare | Endpoint server |
  | File sincronizzati | Numero di file trasferiti (caricamento e scaricamento) | Gruppo di sincronizzazione, Endpoint server |
  | Stato online del server | Numero di heartbeat ricevuti dal server | Server registrati |

- Per altre informazioni, vedere [Monitoraggio di Azure](#azure-monitor).

  > [!Note]  
  > I grafici nel portale di Servizi di sincronizzazione archiviazione sono basati su un intervallo di tempo di 24 ore. Per visualizzare dimensioni o intervalli di tempo diversi, usare Monitoraggio di Azure.

## <a name="windows-server"></a>Windows Server

In **Windows Server in** cui è installato l'agente Sincronizzazione file di Azure, è possibile visualizzare l'integrità degli endpoint server in tale server utilizzando i registri eventi e i contatori **delle prestazioni**. 

### <a name="event-logs"></a>Log eventi

Usare il registro eventi di telemetria nel server per monitorare l'integrità del server registrato, della sincronizzazione e del cloud a livelli. Il registro eventi di telemetria si trova Visualizzatore eventi *in Applicazioni e servizi\Microsoft\FileSync\Agent.*

Integrità sincronizzazione

- L'ID evento 9102 viene registrato una volta completata una sessione di sincronizzazione. Usare questo evento per determinare se le sessioni di sincronizzazione hanno esito positivo (**HResult = 0**) e se sono presenti errori di sincronizzazione per elemento (**PerItemErrorCount**). Per altre informazioni, vedere la documentazione [relativa all'integrità della sincronizzazione](file-sync-troubleshoot.md?tabs=server%252cazure-portal#broken-sync) [e gli errori per elemento.](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)

  > [!Note]  
  > In alcuni casi le sessioni di sincronizzazione non riescono in generale o hanno un valore PerItemErrorCount diverso da zero. Tuttavia, continuano a procedere e alcuni file vengono sincronizzati correttamente. È possibile visualizzare questo valore nei campi Applied (Applicato), ad esempio AppliedFileCount, AppliedDirCount, AppliedTombstoneCount e AppliedSizeBytes. Questi campi illustrano il numero di sessioni riuscite. Se più sessioni di sincronizzazione hanno esito negativo in una riga e hanno un conteggio applicato crescente, assegnare alla sincronizzazione il tempo necessario per riprovare prima di aprire un ticket di supporto.

- L'ID evento 9121 viene registrato per ogni errore per elemento al termine della sessione di sincronizzazione. Usare questo evento per determinare il numero di file che non riescono a eseguire la sincronizzazione con questo errore (**PersistentCount** e **TransientCount**). È consigliabile eseguire un'analisi degli errori persistenti per ogni elemento. Vedere Ricerca per categorie verificare se sono presenti file o cartelle specifici che non [vengono sincronizzati?](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

- L'ID evento 9302 viene registrato ogni 5-10 minuti se è presente una sessione di sincronizzazione attiva. Usare questo evento per determinare il numero di elementi da sincronizzare (**TotalItemCount**), il numero di elementi sincronizzati finora (**AppliedItemCount**) e il numero di elementi che non sono stati sincronizzati a causa di un errore per elemento (**PerItemErrorCount**). Se la sincronizzazione non viene eseguita (**AppliedItemCount=0**), la sessione di sincronizzazione avrà esito negativo e verrà registrato un ID evento 9102 con l'errore. Per altre informazioni, vedere la documentazione [sullo stato di sincronizzazione](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Integrità del server registrata

- L'ID evento 9301 viene registrato ogni 30 secondi quando un server esegue query sul servizio per i processi. Se GetNextJob termina con **lo stato = 0,** il server è in grado di comunicare con il servizio. Se GetNextJob termina con un errore, consultare la documentazione [per la risoluzione dei problemi](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#server-endpoint-noactivity) per indicazioni.

Integrità del cloud a livelli

- Per monitorare l'attività di livelli in un server, usare gli ID evento 9003, 9016 e 9029 nel registro eventi di telemetria, che si trova in Visualizzatore eventi in Applicazioni e *servizi\Microsoft\FileSync\Agent*.

  - L'ID evento 9003 fornisce inoltre la distribuzione di un errore per un endpoint server. Ad esempio: Total Error Count e ErrorCode. Viene registrato un evento per ogni codice di errore.
  - L'ID evento 9016 fornisce inoltre risultati di ghosting per un volume. Ad esempio: Percentuale spazio disponibile è, Numero di file fantasma nella sessione e Numero di file non fantasma.
  - L'ID evento 9029 fornisce informazioni sulla sessione di ghosting per un endpoint server. Ad esempio: Numero di file tentati nella sessione, Numero di file a livelli nella sessione e Numero di file già a livelli.

- Per monitorare l'attività di richiamo in un server, usare l'ID evento 9005, 9006, 9009, 9059 e 9071 nel registro eventi di telemetria, che si trova in Visualizzatore eventi in Applicazioni e *servizi\Microsoft\FileSync\Agent*.

  - L'ID evento 9005 offre affidabilità di richiamo per un endpoint server. Ad esempio: Totale file univoci a cui si accede e Totale file univoci con accesso non riuscito.
  - L'ID evento 9006 fornisce inoltre la distribuzione di un errore di richiamo per un endpoint server. Ad esempio: Totale richieste non riuscite ed ErrorCode. Viene registrato un evento per ogni codice di errore.
  - L'ID evento 9009 fornisce informazioni sulla sessione di richiamo per un endpoint server. Ad esempio: DurationSeconds, CountFilesRecallSucceeded e CountFilesRecallFailed.
  - L'ID evento 9059 fornisce la distribuzione di richiamo delle applicazioni per un endpoint server. Ad esempio: ShareId, Nome applicazione e TotalEgressNetworkBytes.
  - L'ID evento 9071 garantisce l'efficienza del cloud a livelli per un endpoint server. Ad esempio: TotalDistinctFileCountCacheHit, TotalDistinctFileCountCacheMiss, TotalCacheHitBytes e TotalCacheMissBytes.

### <a name="performance-counters"></a>Contatori delle prestazioni

Usare i contatori delle prestazioni di Sincronizzazione file di Azure nel server per monitorare l'attività di sincronizzazione.

Per visualizzare Sincronizzazione file di Azure contatori delle prestazioni nel server, aprire Monitor prestazioni (Perfmon.exe). È possibile trovare i contatori sotto gli oggetti **Byte trasferiti AFS** e Operazioni **di sincronizzazione AFS.**

I contatori delle prestazioni seguenti per Sincronizzazione file di Azure sono disponibili in Monitoraggio delle prestazioni:

| Oggetto prestazioni\Nome contatore | Descrizione |
|-|-|
| Byte AFS trasferiti\Byte scaricati/sec | Numero di byte scaricati al secondo. |
| Byte AFS trasferiti\Byte caricati/sec | Numero di byte caricati al secondo. |
| Byte AFS trasferiti\Byte totali/sec | Byte totali al secondo (caricamento e download). |
| Operazioni di sincronizzazione AFS\file scaricati per la sincronizzazione/sec | Numero di file scaricati al secondo. |
| Operazioni di sincronizzazione AFS\file caricati per la sincronizzazione/sec | Numero di file caricati al secondo. |
| Operazioni di sincronizzazione AFS\operazioni di sincronizzazione file totali/sec | Numero totale di file sincronizzati (caricamento e download). |

## <a name="alert-examples"></a>Esempi di avvisi
In questa sezione vengono forniti alcuni avvisi di esempio per Sincronizzazione file di Azure.

  > [!Note]  
  > Se si crea un avviso ed è troppo rumoroso, modificare il valore di soglia e la logica di avviso.

### <a name="how-to-create-an-alert-if-the-server-endpoint-health-shows-an-error-in-the-portal"></a>Come creare un avviso se l'integrità dell'endpoint server mostra un errore nel portale

1. Nel **portale di Azure** passare al rispettivo servizio **di sincronizzazione archiviazione**. 
2. Passare alla sezione **Monitoraggio** e fare clic su **Avvisi.** 
3. Fare clic **su + Nuova regola di avviso** per creare una nuova regola di avviso. 
4. Configurare la condizione facendo clic **su Seleziona condizione**.
5. Nel **pannello Configura logica dei segnali** fare clic su Risultato della sessione di **sincronizzazione** sotto il nome del segnale.  
6. Selezionare la configurazione della dimensione seguente: 
     - Nome dimensione: **Nome endpoint server**  
     - Operatore: **=** 
     - Valori dimensione: **tutti i valori correnti e futuri**  
7. Passare a **Logica avvisi** e completare le operazioni seguenti: 
     - Soglia impostata su **Statico** 
     - Operatore: **minore di** 
     - Tipo di aggregazione: **Massimo**  
     - Valore soglia: **1** 
     - Valutata in base a: Granularità delle aggregazioni = **24 ore** | Frequenza di valutazione = **Ogni ora** 
     - Fare clic **su Fine.** 
8. Fare **clic su Seleziona gruppo** di azioni per aggiungere un gruppo di azioni (posta elettronica, SMS e così via) all'avviso selezionando un gruppo di azioni esistente o creando un nuovo gruppo di azioni.
9. Compilare i dettagli **dell'avviso,** ad esempio **Nome regola di avviso,** **Descrizione** **e Gravità.**
10. Fare clic su **Crea regola di avviso**. 

### <a name="how-to-create-an-alert-if-files-are-failing-to-sync-to-a-server-or-cloud-endpoint"></a>Come creare un avviso se non è possibile sincronizzare i file con un server o un endpoint cloud

1. Nel **portale di Azure** passare al rispettivo **servizio di sincronizzazione archiviazione.** 
2. Passare alla sezione **Monitoraggio** e fare clic su **Avvisi**. 
3. Fare clic su **+ Nuova regola di avviso** per creare una nuova regola di avviso. 
4. Configurare la condizione facendo clic **su Seleziona condizione**.
5. Nel **pannello Configura logica del** segnale fare clic su File non **sincronizzati in** Nome segnale.  
6. Selezionare la configurazione della dimensione seguente: 
     - Nome dimensione: **Nome endpoint server**  
     - Operatore: **=** 
     - Valori di dimensione: **tutti i valori correnti e futuri**  
7. Passare a **Logica di avviso** e completare le operazioni seguenti: 
     - Soglia impostata su **Statico** 
     - Operatore: **Maggiore di** 
     - Tipo di aggregazione: **Media**  
     - Valore soglia: **100** 
     - Valutato in base a: Granularità aggregazione = **5 minuti |** Frequenza di valutazione = **ogni 5 minuti** 
     - Fare clic **su Fine.** 
8. Fare **clic su Seleziona gruppo** di azioni per aggiungere un gruppo di azioni (posta elettronica, SMS e così via) all'avviso selezionando un gruppo di azioni esistente o creando un nuovo gruppo di azioni.
9. Compilare i dettagli **dell'avviso,** **ad esempio Nome regola di avviso**, **Descrizione** **e Gravità**.
10. Fare clic su **Crea regola di avviso**. 

### <a name="how-to-create-an-alert-if-a-registered-server-is-failing-to-communicate-with-the-storage-sync-service"></a>Come creare un avviso se un server registrato non riesce a comunicare con il servizio di sincronizzazione archiviazione

1. Nel **portale di Azure** passare al rispettivo servizio **di sincronizzazione archiviazione**. 
2. Passare alla sezione **Monitoraggio** e fare clic su **Avvisi.** 
3. Fare clic **su + Nuova regola di avviso** per creare una nuova regola di avviso. 
4. Configurare la condizione facendo clic **su Seleziona condizione**.
5. Nel **pannello Configura logica dei segnali** fare clic su Stato server online **in** Nome segnale.  
6. Selezionare la configurazione della dimensione seguente: 
     - Nome dimensione: **Nome server**  
     - Operatore: **=** 
     - Valori dimensione: **tutti i valori correnti e futuri**  
7. Passare a **Logica avvisi** e completare le operazioni seguenti: 
     - Soglia impostata su **Statico** 
     - Operatore: **minore di** 
     - Tipo di aggregazione: **Massimo**  
     - Valore soglia (in byte): **1** 
     - Valutato in base a: Granularità aggregazione = **1 ora** | Frequenza di valutazione = **ogni 30 minuti** 
         - Si noti che le metriche vengono inviate Monitoraggio di Azure ogni 15-20 minuti. Non impostare La **frequenza di valutazione** su meno di 30 minuti (genererà falsi avvisi).
     - Fare clic **su Fine.** 
8. Fare **clic su Seleziona gruppo** di azioni per aggiungere un gruppo di azioni (posta elettronica, SMS e così via) all'avviso selezionando un gruppo di azioni esistente o creando un nuovo gruppo di azioni.
9. Compilare i dettagli **dell'avviso,** ad esempio **Nome regola di avviso,** **Descrizione** **e Gravità.**
10. Fare clic su **Crea regola di avviso**. 

### <a name="how-to-create-an-alert-if-the-cloud-tiering-recall-size-has-exceeded-500gib-in-a-day"></a>Come creare un avviso se le dimensioni di richiamo a livelli del cloud hanno superato 500GiB in un giorno

1. Nel **portale di Azure** passare al rispettivo **servizio di sincronizzazione archiviazione.** 
2. Passare alla sezione **Monitoraggio** e fare clic su **Avvisi**. 
3. Fare clic su **+ Nuova regola di avviso** per creare una nuova regola di avviso. 
4. Configurare la condizione facendo clic **su Seleziona condizione**.
5. Nel **pannello Configura logica del** segnale fare clic su Dimensioni richiamo a livelli **nel** nome del segnale.  
6. Selezionare la configurazione della dimensione seguente: 
     - Nome dimensione: **Nome server**  
     - Operatore: **=** 
     - Valori di dimensione: **tutti i valori correnti e futuri**  
7. Passare a **Logica di avviso** e completare le operazioni seguenti: 
     - Soglia impostata su **Statico** 
     - Operatore: **Maggiore di** 
     - Tipo di aggregazione: **Totale**  
     - Valore soglia (in byte): **67108864000** 
     - Valutato in base a: Granularità aggregazione = **24 ore** | Frequenza di valutazione = **Ogni ora** 
     - Fare clic **su Fine.** 
8. Fare **clic su Seleziona gruppo** di azioni per aggiungere un gruppo di azioni (posta elettronica, SMS e così via) all'avviso selezionando un gruppo di azioni esistente o creando un nuovo gruppo di azioni.
9. Compilare i dettagli **dell'avviso,** **ad esempio Nome regola di avviso**, **Descrizione** **e Gravità**.
10. Fare clic su **Crea regola di avviso**. 

## <a name="next-steps"></a>Passaggi successivi
- [Pianificazione per la distribuzione di Sincronizzazione file di Azure](file-sync-planning.md)
- [Impostazioni di proxy e firewall di Sincronizzazione file di Azure](file-sync-firewall-and-proxy.md)
- [Distribuire Sincronizzazione file di Azure](file-sync-deployment-guide.md)
- [Risolvere i problemi di Sincronizzazione file di Azure](file-sync-troubleshoot.md)