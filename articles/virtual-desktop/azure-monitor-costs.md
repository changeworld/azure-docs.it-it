---
title: Monitorare le stime dei prezzi dei costi del desktop virtuale Windows-Azure
description: Come stimare i costi e i prezzi per l'uso di monitoraggio di Azure per desktop virtuale di Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 26262f83e14602d6ea93f96ec47630ef870c357d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309306"
---
# <a name="estimate-azure-monitor-costs"></a>Stimare i costi di monitoraggio di Azure

Log di monitoraggio di Azure è un servizio che raccoglie, indicizza e archivia i dati generati dall'ambiente. Per questo motivo, il modello di determinazione dei prezzi di monitoraggio di Azure è basato sulla quantità di dati introdotti ed elaborati (o inseriti) dall'area di lavoro di Log Analytics in gigabyte al giorno. Il costo di un'area di lavoro Log Analytics non è basato solo sul volume dei dati raccolti, ma anche sul piano di pagamento di Azure selezionato e sul periodo di tempo in cui si sceglie di archiviare i dati generati dall'ambiente.

In questo articolo vengono illustrati gli aspetti seguenti che consentono di comprendere il funzionamento dei prezzi di monitoraggio di Azure:

- Come stimare i costi di inserimento e archiviazione dei dati prima di abilitare questa funzionalità
- Come misurare e controllare l'inserimento e l'archiviazione per ridurre i costi quando si usa questa funzionalità

>[!NOTE]
> Tutte le dimensioni e i prezzi indicati in questo articolo sono solo esempi per illustrare il funzionamento della stima. Per una valutazione più accurata basata sul monitoraggio di Azure Log Analytics modello di determinazione dei prezzi e sull'area di Azure, vedere [prezzi di monitoraggio](https://azure.microsoft.com/pricing/details/monitor/)di Azure.

## <a name="estimate-data-ingestion-and-storage-costs"></a>Stimare i costi di inserimento e archiviazione dei dati

Si consiglia di usare un set predefinito di dati scritti come log nell'area di lavoro Log Analytics. Nell'esempio seguente vengono esaminate le stime dei dati fatturabili nella configurazione predefinita

I set di impostazioni predefiniti per il monitoraggio di Azure per desktop virtuale di Windows includono:

- Contatori delle prestazioni dagli host della sessione
- Registri eventi di Windows dagli host della sessione
- Diagnostica desktop virtuali Windows dall'infrastruttura del servizio

I costi di inserimento e archiviazione dei dati dipendono dalle dimensioni dell'ambiente, dall'integrità e dall'utilizzo. Le stime di esempio che verranno usate in questo articolo per calcolare gli intervalli di costo che è possibile prevedere sono basate su macchine virtuali integre che eseguono la luce per l'utilizzo energetico, in base alle [linee guida per il dimensionamento delle macchine virtuali](/windows-server/remote/remote-desktop-services/virtual-machine-recs), per calcolare una gamma di costi di inserimento e archiviazione dei dati che è possibile prevedere.

La VM di utilizzo leggero che verrà usata nell'esempio include i componenti seguenti:

- 4 vCPU, 1 disco
- 16 sessioni al giorno
- Durata media della sessione di 2 ore (120 minuti)
- 100 processi per sessione

La VM di utilizzo dell'energia elettrica che verrà usata nell'esempio include i componenti seguenti:

- 6 vCPU, 1 disco
- 6 sessioni al giorno
- Durata media della sessione di 4 ore (240 minuti)
- 200 processi per sessione

## <a name="estimating-performance-counter-ingestion"></a>Stima dell'inserimento del contatore delle prestazioni

I contatori delle prestazioni mostrano il modo in cui le risorse di sistema eseguono. L'inserimento di dati del contatore delle prestazioni dipende dalle dimensioni e dall'utilizzo dell'ambiente. Nella maggior parte dei casi, i contatori delle prestazioni devono essere costituiti da 80 al 99% dell'inserimento dei dati per il monitoraggio di Azure per desktop virtuale di Windows.

Prima di iniziare la stima, è importante comprendere che ogni contatore delle prestazioni invia dati a una frequenza specifica. È stata impostata una frequenza di campionamento predefinita al minuto (è anche possibile modificare questa frequenza nelle impostazioni), ma tale frequenza verrà applicata a fattori di moltiplicazione diversi a seconda del contatore. I fattori seguenti influiscono sulla frequenza:

- Per il fattore per macchina virtuale (VM), ogni contatore invia i dati per ogni macchina virtuale nell'ambiente alla frequenza di campionamento predefinita al minuto mentre la macchina virtuale è in esecuzione. È possibile stimare il numero di record che questi contatori inviano al giorno moltiplicando la frequenza di campionamento predefinita al minuto per il numero di VM nell'ambiente, quindi moltiplicando tale numero per il tempo medio di esecuzione della VM al giorno.

   Per concludere:

   Frequenza di campionamento predefinita al minuto × numero di core CPU nello SKU VM × numero di VM × tempo di esecuzione medio VM al giorno = numero di record inviati al giorno

- Per il fattore per CPU, ogni contatore invia alla frequenza di campionamento predefinita al minuto per ogni vCPU in ogni macchina virtuale nell'ambiente in cui è in esecuzione la macchina virtuale. È possibile stimare il numero di record che i contatori invieranno al giorno moltiplicando la frequenza di campionamento predefinita al minuto per il numero di core CPU nello SKU della macchina virtuale, moltiplicando quindi il numero per il numero di minuti di esecuzione della macchina virtuale e il numero di macchine virtuali nell'ambiente.

   Per concludere:
   
   Frequenza di campionamento predefinita al minuto × numero di core CPU nello SKU di VM × numero di minuti di esecuzione della macchina virtuale × numero di VM = numero di record inviati al giorno

- Per ogni fattore di disco, ogni contatore invia i dati in base alla frequenza di campionamento predefinita per ogni disco in ogni macchina virtuale nell'ambiente in uso. Il numero di record che questi contatori invieranno al giorno equivale alla frequenza di campionamento predefinita al minuto moltiplicata per il numero di dischi nello SKU della macchina virtuale, moltiplicato per 60 minuti all'ora e infine moltiplicato per le ore medie attive per una macchina virtuale.

   Per concludere:

   Frequenza di campionamento predefinita al minuto × numero di dischi nello SKU di VM × 60 minuti all'ora × numero di VM × tempo di esecuzione medio della VM al giorno = numero di record inviati al giorno

- Per il fattore per sessione, ogni contatore invia i dati alla frequenza di campionamento predefinita per ogni sessione nell'ambiente mentre la sessione è connessa. Per stimare il numero di record che questi contatori invieranno al giorno, è possibile moltiplicare la frequenza di campionamento predefinita al minuto per il numero medio di sessioni al giorno e la durata media della sessione.

   Per concludere:

   Frequenza di campionamento predefinita al minuto × sessioni al giorno × durata media sessione = numero di record inviati al giorno

- Per ogni fattore di processo, ogni contatore invia i dati alla frequenza predefinita per ogni processo in ogni sessione nell'ambiente in uso. È possibile stimare il numero di record che questi contatori invieranno al giorno moltiplicando la frequenza di campionamento predefinita al minuto per il numero medio di sessioni al giorno, moltiplicando quindi la durata media della sessione e il numero medio di processi per sessione.
  
   Per concludere:

   Frequenza di campionamento predefinita al minuto × sessioni al giorno × durata media della sessione × numero medio di processi per sessione = numero di record inviati al giorno

Nella tabella seguente sono elencati i 20 contatori delle prestazioni di monitoraggio di Azure per desktop virtuale Windows che raccoglie e le relative tariffe predefinite:

| Nome contatore | Frequenza di campionamento predefinita | Fattore di frequenza |
|--------------|---------------------|------------------|
| Disco logico (C:) \\ % di spazio disponibile | 60 secondi  | Per disco             |
| Disco logico (C:) \\ lunghezza media coda del disco   | 30 secondi    | Per disco             |
| Disco logico (C:) \\ media sec/trasferimento disco  | 60 secondi       | Per disco             |
| Disco logico (C:) \\ lunghezza corrente coda del disco  | 30 secondi      | Per disco             |
| Memoria ( \* ) \\ MByte disponibili | 30 secondi    | Per macchina virtuale  |
| Memoria ( \* ) \\ errori di pagina/sec | 30 secondi   | Per macchina virtuale  |
| Memoria ( \* ) \\ pagine/sec | 30 secondi   | Per macchina virtuale  |
| Memoria ( \* ) \\ % byte vincolati in uso | 30 secondi    | Per macchina virtuale  |
| PhysicalDisk ( \* ) \\ lunghezza media coda del disco | 30 secondi      | Per disco             |
| PhysicalDisk ( \* ) \\ Media letture disco/sec | 30 secondi  | Per disco             |
| PhysicalDisk ( \* ) \\ media sec/trasferimento disco | 30 secondi  | Per disco             |
| PhysicalDisk ( \* ) \\ Media scritture disco/sec | 30 secondi | Per disco             |
| Informazioni processore (_Total) \\ % tempo processore | 30 secondi | Per core/CPU         |
| Sessioni attive di Servizi terminal ( \* ) \\          | 60 secondi | Per macchina virtuale  |
| \*Sessioni inattive di Servizi terminal () \\        | 60 secondi | Per macchina virtuale  |
| Totale sessioni Servizi terminal ( \* ) \\ | 60 secondi | Per macchina virtuale  |
| Ritardo input massimo input utente per processo ( \* ) \\         | 30 secondi | Per processo          |
| Ritardo input utente per sessione ( \* )- \\ ritardo input massimo        | 30 secondi | per ogni sessione          |
| \*RTT TCP corrente rete () RemoteFX \\ | 30 secondi | Per macchina virtuale  |
| RemoteFX rete ( \* ) \\ larghezza di banda UDP corrente     | 30 secondi | Per macchina virtuale  |

Se si stima che le dimensioni di ogni record siano di 200 byte, una macchina virtuale di esempio che esegue un carico di lavoro leggero sulla frequenza di campionamento predefinita invierà circa 90 megabyte di dati del contatore delle prestazioni al giorno per ogni macchina virtuale. Nel frattempo, una VM di esempio che esegue un carico di lavoro di alimentazione invierà circa 130 megabyte di dati del contatore delle prestazioni al giorno per ogni macchina virtuale. Tuttavia, le dimensioni dei record e l'utilizzo dell'ambiente possono variare, quindi i megabyte al giorno usati dalla distribuzione potrebbero essere diversi.

Per ulteriori informazioni sui contatori delle prestazioni di ritardo di input, vedere [contatori delle prestazioni di ritardo input utente](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

## <a name="estimating-windows-event-log-ingestion"></a>Stima dell'inserimento del registro eventi di Windows

I registri eventi di Windows sono origini dati raccolte da agenti Log Analytics in macchine virtuali Windows. È possibile raccogliere eventi da log standard come il sistema e l'applicazione, nonché i log personalizzati creati dalle applicazioni che è necessario monitorare.

Questi sono gli eventi di Windows predefiniti per monitoraggio di Azure per desktop virtuale di Windows:

- Applicazione
- Microsoft-Windows-TerminalServices-RemoteConnectionManager/admin
- Microsoft-Windows-TerminalServices-LocalSessionManager/operativo
- Sistema
- Microsoft-FSLogix-Apps/operativo
- Microsoft-FSLogix-app/amministratore

Gli eventi di Windows vengono inviati ogni volta che vengono soddisfatte le condizioni dell'evento nell'ambiente. I computer in Stati integri invieranno un numero di eventi inferiore a quello dei computer in Stati non integri. Poiché il conteggio degli eventi è imprevedibile, viene usato un intervallo compreso tra 1.000 e 10.000 per ogni macchina virtuale al giorno, in base ad esempi di ambienti integri per questa stima. Se, ad esempio, si stima che le dimensioni di ogni record di evento in questo esempio siano pari a 1.500 byte, si tratta di circa 2-15 megabyte di dati evento al giorno per l'ambiente specificato.

Per ulteriori informazioni sugli eventi di Windows, vedere [proprietà di record eventi di Windows](../azure-monitor/agents/data-sources-windows-events.md).

## <a name="estimating-diagnostics-ingestion"></a>Stima dell'inserimento di diagnostica

Il servizio di diagnostica crea log attività per le azioni utente e amministrative.

Questi sono i nomi dei log attività che il contatore di diagnostica tiene traccia:

- WVDCheckpoints
- WVDConnections
- WVDErrors
- WVDFeeds
- WVDManagement
- WVDAgentHealthStatus

Il servizio invia le informazioni di diagnostica ogni volta che l'ambiente soddisfa i termini richiesti per creare un record. Poiché il numero di record di diagnostica è imprevedibile, viene usato un intervallo compreso tra 500 e 1000 per ogni macchina virtuale al giorno, in base ad esempi di ambienti integri per questa stima.

Se, ad esempio, si stima che ogni dimensione del record di diagnostica in questo esempio sia 200 byte, il totale dei dati inseriti sarà inferiore a 1 MB per macchina virtuale al giorno.

Per ulteriori informazioni sulle categorie del log attività, vedere [diagnostica del desktop virtuale di Windows](diagnostics-log-analytics.md).

## <a name="estimating-total-costs"></a>Stima dei costi totali

Infine, calcoliamo il costo totale. In questo esempio, supponiamo di ottenere i risultati seguenti in base ai valori di esempio nelle sezioni precedenti:

| Origine dati        | Dimensioni stimate al giorno (in megabyte)   |
|-------------------------------------|------------------------------------------|
| Contatori delle prestazioni   | 90-130 |
| Eventi    | 2-15 |
| Diagnostica desktop virtuale di Windows | \< 1 |

In questo esempio, il totale dei dati inseriti per il monitoraggio di Azure per desktop virtuale Windows è compreso tra 92 e 145 megabyte per VM al giorno. In altre parole, ogni 31 giorni ogni macchina virtuale inserisce approssimativamente da 3 a 5 gigabyte di dati.

Usando il modello con pagamento in base al consumo predefinito per [log Analytics prezzi](https://azure.microsoft.com/pricing/details/monitor/), è possibile stimare la raccolta dei dati di monitoraggio di Azure e i costi di archiviazione al mese. A seconda dell'inserimento dei dati, è anche possibile prendere in considerazione il modello di prenotazione della capacità per Log Analytics i prezzi.

## <a name="manage-your-data-ingestion-to-reduce-costs"></a>Gestione dell'inserimento dei dati per ridurre i costi

In questa sezione viene illustrato come misurare e gestire l'inserimento dei dati per ridurre i costi.

Per informazioni sulla gestione dei diritti e delle autorizzazioni per la cartella di lavoro, vedere [controllo di accesso](../azure-monitor/visualize/workbooks-access-control.md).

>[!NOTE]
>La rimozione dei punti dati influirà sugli oggetti visivi corrispondenti in monitoraggio di Azure per desktop virtuale di Windows.

### <a name="log-analytics-settings"></a>Impostazioni Log Analytics

Ecco alcuni suggerimenti per ottimizzare le impostazioni di Log Analytics per gestire l'inserimento dei dati:

- Utilizzare un'area di lavoro Log Analytics designata per le risorse del desktop virtuale di Windows per assicurarsi che Log Analytics raccolga solo gli eventi e i contatori delle prestazioni per le macchine virtuali nella distribuzione di desktop virtuali Windows.
- Modificare le impostazioni di archiviazione Log Analytics per gestire i costi. È possibile ridurre il periodo di conservazione, valutare se un piano tariffario di archiviazione fisso sarebbe più conveniente o impostare limiti sulla quantità di dati che è possibile inserire per limitare l'impatto di una distribuzione non integra. Per altre informazioni, vedere [gestire l'utilizzo e i costi per i log di monitoraggio di Azure](../azure-monitor/platform/manage-cost-storage.md).

### <a name="remove-excess-data"></a>Rimuovere i dati in eccesso

La configurazione predefinita è l'unico set di dati consigliato per il monitoraggio di Azure per desktop virtuale di Windows. È sempre possibile aggiungere altri punti dati e visualizzarli nella diagnostica host: browser host oppure creare grafici personalizzati, ma i dati aggiunti aumenteranno il costo Log Analytics. Questi possono essere rimossi per risparmiare sui costi.

### <a name="measure-and-manage-your-performance-counter-data"></a>Misurare e gestire i dati dei contatori delle prestazioni

I costi di monitoraggio reali dipendono dalle dimensioni dell'ambiente, dall'utilizzo e dall'integrità. Per comprendere come misurare l'inserimento dei dati nell'area di lavoro Log Analytics, vedere [informazioni sul volume dei dati di log](../azure-monitor/logs/manage-cost-storage.md#understanding-ingested-data-volume)inseriti.

I contatori delle prestazioni usati dagli host della sessione probabilmente saranno la principale fonte di dati inseriti per il monitoraggio di Azure per desktop virtuale di Windows. Il modello di query personalizzato seguente per un'area di lavoro di Log Analytics può tenere traccia della frequenza e dei megabyte inseriti per ogni contatore delle prestazioni nell'ultimo giorno:

```azure
let WVDHosts = dynamic(['Host1.MyCompany.com', 'Host2.MyCompany.com']);
Perf
| where TimeGenerated > ago(1d)
| where Computer in (WVDHosts)
| extend PerfCounter = strcat(ObjectName, ":", CounterName)
| summarize Records = count(TimeGenerated), InstanceNames = dcount(InstanceName), Bytes=sum(_BilledSize) by PerfCounter
| extend Billed_MBytes = Bytes / (1024 * 1024), BytesPerRecord = Bytes / Records
| sort by Records desc
```

>[!NOTE]
>Assicurarsi di sostituire i valori segnaposto del modello con i valori usati dall'ambiente. in caso contrario, la query non funzionerà.

Questa query Mostra tutti i contatori delle prestazioni abilitati nell'ambiente e non solo quelli predefiniti per il monitoraggio di Azure per desktop virtuale di Windows. Queste informazioni possono essere utili per comprendere quali aree sono destinate a ridurre i costi, ad esempio per ridurre la frequenza di un contatore o rimuoverlo completamente.

È anche possibile ridurre i costi rimuovendo i contatori delle prestazioni. Per informazioni su come rimuovere i contatori delle prestazioni o modificare i contatori esistenti per ridurne la frequenza, vedere [configurazione dei contatori delle prestazioni](../azure-monitor/platform/data-sources-performance-counters.md#configuring-performance-counters).

### <a name="manage-windows-event-logs"></a>Gestire i registri eventi di Windows

È improbabile che gli eventi di Windows provochino un picco di inserimento dei dati quando tutti gli host sono integri. Un host non integro può aumentare il numero di eventi inviati al log, ma le informazioni possono essere fondamentali per risolvere i problemi dell'host. È consigliabile mantenerle. Per ulteriori informazioni su come gestire i registri eventi di Windows, vedere [configurazione dei registri eventi di Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).

### <a name="manage-diagnostics"></a>Gestire la diagnostica

Diagnostica desktop virtuale di Windows dovrebbe costituire meno dell'1% dei costi di archiviazione dei dati, pertanto non è consigliabile rimuoverli. Per gestire la diagnostica del desktop virtuale di Windows, [utilizzare log Analytics per la funzionalità di diagnostica](diagnostics-log-analytics.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su monitoraggio di Azure per desktop virtuale Windows, vedere questi articoli:

- [Usare monitoraggio di Azure per desktop virtuale Windows per monitorare la distribuzione](azure-monitor.md).
- Usare il [Glossario](azure-monitor-glossary.md) per ulteriori informazioni sui termini e i concetti.
- Se si verifica un problema, consultare la [Guida alla risoluzione dei problemi](troubleshoot-azure-monitor.md) per assistenza.
- Per altre informazioni sulla gestione dei costi di monitoraggio, vedere [monitoraggio dell'utilizzo e dei costi stimati in monitoraggio di Azure](../azure-monitor/usage-estimated-costs.md) .
