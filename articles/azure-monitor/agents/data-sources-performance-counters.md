---
title: Raccolta di origini dati per le prestazioni di Windows e Linux con Log Analytics Agent in monitoraggio di Azure
description: I contatori delle prestazioni vengono raccolti da Monitoraggio di Azure per analizzare le prestazioni degli agenti Windows e Linux.  Questo articolo descrive come configurare la raccolta di contatori delle prestazioni per gli agenti Windows e Linux, i cui dettagli vengono archiviati nell'area di lavoro, e come analizzarli nel portale di Azure.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: f4bddc1666d1165d6a1e4c749fdbc96ede37747a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102036776"
---
# <a name="collect-windows-and-linux-performance-data-sources-with-log-analytics-agent"></a>Raccogli origini dati per le prestazioni di Windows e Linux con Log Analytics Agent
I contatori delle prestazioni in Windows e Linux forniscono informazioni dettagliate sulle prestazioni di componenti hardware, sistemi operativi e applicazioni.  Monitoraggio di Azure è in grado di raccogliere i contatori delle prestazioni dagli agenti Log Analytics a intervalli frequenti per l'analisi di quasi in tempo reale (NRT) oltre ad aggregare i dati sulle prestazioni per l'analisi a lungo termine e la creazione di report

> [!IMPORTANT]
> Questo articolo illustra la raccolta dei dati sulle prestazioni con l' [agente di log Analytics](./log-analytics-agent.md) , che è uno degli agenti usati da monitoraggio di Azure. Altri agenti raccolgono dati diversi e sono configurati in modo diverso. Per un elenco degli agenti disponibili e dei dati che è possibile raccogliere, vedere [Panoramica degli agenti di monitoraggio di Azure](../agents/agents-overview.md) .

![Contatori delle prestazioni](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Configurazione dei contatori delle prestazioni
Configurare i contatori delle prestazioni dal [menu di configurazione agenti](../agents/agent-data-sources.md#configuring-data-sources) per l'area di lavoro log Analytics.

Quando si configurano per la prima volta i contatori delle prestazioni di Windows o Linux per una nuova area di lavoro, è possibile creare rapidamente alcuni contatori comuni.  Viene visualizzato l'elenco dei contatori con le caselle di controllo corrispondenti.  Assicurarsi di aver selezionato tutti i contatori da creare e quindi fare clic su **Aggiungi i contatori delle prestazioni selezionati**.

Per i contatori delle prestazioni di Windows è possibile scegliere un'istanza specifica per ogni contatore delle prestazioni. Per i contatori delle prestazioni di Linux, l'istanza di ogni contatore scelto viene applicata a tutti i contatori figlio del contatore padre. La tabella seguente illustra le istanze comuni disponibili ai contatori delle prestazioni di Linux e Windows.

| Nome istanza | Descrizione |
| --- | --- |
| \_Totale |Totale di tutte le istanze |
| \* |Tutte le istanze |
| (/&#124;/var) |Corrisponde alle istanze denominate / oppure /var |

### <a name="windows-performance-counters"></a>Contatori delle prestazioni di Windows

[![Configurare i contatori delle prestazioni di Windows](media/data-sources-performance-counters/configure-windows.png)](media/data-sources-performance-counters/configure-windows.png#lightbox)

Seguire questa procedura per aggiungere un nuovo contatore delle prestazioni Windows da raccogliere. Si noti che i contatori delle prestazioni di Windows V2 non sono supportati.

1. Fare clic su **Aggiungi contatore delle prestazioni**.
2. Digitare il nome del contatore nella casella di testo, usando il formato *oggetto(istanza)\contatore*.  Quando si inizia a digitare, viene visualizzato un elenco di contatori comuni corrispondenti.  È possibile selezionare un contatore nell'elenco o digitare quello desiderato.  Per restituire tutte le istanze per un contatore specifico, specificare *oggetto\contatore*.  

    Quando si raccolgono i contatori delle prestazioni di SQL Server da istanze denominate, tutti i contatori di tali istanze iniziano con *MSSQL$*, seguito dal nome dell'istanza.  Ad esempio, per raccogliere il contatore Percentuale riscontri cache log di tutti i database dall'oggetto delle prestazioni del database per l'istanza di SQL denominata INST2, specificare `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`.

4. Quando si aggiunge un contatore, per **Intervallo di campionamento** verrà usato il valore predefinito di 10 secondi.  Per ridurre i requisiti di spazio di archiviazione dei dati sulle prestazioni raccolti, è possibile impostare questa opzione su un valore più alto, fino a un massimo di 1800 secondi (30 minuti).
5. Al termine dell'aggiunta dei contatori, fare clic sul pulsante **applica** nella parte superiore della schermata per salvare la configurazione.

### <a name="linux-performance-counters"></a>Contatori delle prestazioni di Linux

[![Configurare i contatori delle prestazioni di Linux](media/data-sources-performance-counters/configure-linux.png)](media/data-sources-performance-counters/configure-linux.png#lightbox)

Seguire questa procedura per aggiungere un nuovo contatore delle prestazioni Linux da raccogliere.

1. Fare clic su **Aggiungi contatore delle prestazioni**.
1. Digitare il nome del contatore nella casella di testo, usando il formato *oggetto(istanza)\contatore*.  Quando si inizia a digitare, viene visualizzato un elenco di contatori comuni corrispondenti.  È possibile selezionare un contatore nell'elenco o digitare quello desiderato.  
1. Tutti i contatori per un oggetto usano lo stesso **intervallo di campionamento**.  Il valore predefinito è 10 secondi.  Per ridurre i requisiti di spazio di archiviazione dei dati sulle prestazioni raccolti, è possibile impostare questa opzione su un valore più alto, fino a un massimo di 1800 secondi (30 minuti).
1. Al termine dell'aggiunta dei contatori, fare clic sul pulsante **applica** nella parte superiore della schermata per salvare la configurazione.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Configurare i contatori delle prestazioni di Linux nel file di configurazione
Anziché usare il portale di Azure per configurare i contatori delle prestazioni di Linux, è possibile modificare i file di configurazione direttamente nell'agente di Linux.  Le metriche delle prestazioni da raccogliere sono controllate dalla configurazione in **/etc/opt/Microsoft/omsagent/Workspace \<workspace id\> /conf/omsagent.conf**.

Ogni oggetto o categoria delle metriche delle prestazioni da raccogliere deve essere definito nel file di configurazione come singolo elemento `<source>` . La sintassi segue il modello seguente.

```xml
<source>
    type oms_omi  
    object_name "Processor"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>
```


I parametri di questo elemento sono descritti nella tabella seguente.

| Parametri | Descrizione |
|:--|:--|
| object\_name | Nome dell'oggetto per la raccolta. |
| instance\_regex |  *Espressione regolare* che definisce le istanze da raccogliere. Il valore `.*` specifica tutte le istanze. Per raccogliere le metriche del processore solo per l'istanza \_Total, è possibile specificare `_Total`. Per raccogliere le metriche del processore solo per le istanze crond o sshd, è possibile specificare `(crond\|sshd)`. |
| counter\_name\_regex | *Espressione regolare* che definisce i contatori per l'oggetto da raccogliere. Per raccogliere tutti i contatori per l'oggetto, specificare `.*`. Per raccogliere solo i contatori dello spazio di swapping per l'oggetto Memory, ad esempio, è possibile specificare `.+Swap.+` |
| interval | Frequenza con cui vengono raccolti i contatori dell'oggetto. |


La tabella seguente elenca gli oggetti e i contatori che è possibile specificare nel file di configurazione.  Per alcune applicazioni sono disponibili contatori aggiuntivi come descritto in [Raccogliere i contatori delle prestazioni per applicazioni Linux in Monitoraggio di Azure](data-sources-linux-applications.md).

| Nome oggetto | Nome contatore |
|:--|:--|
| Disco logico | % Free Inodes |
| Disco logico | % Free Space |
| Disco logico | % Used Inodes |
| Disco logico | % Used Space |
| Disco logico | Byte letti da disco/sec |
| Disco logico | Letture disco/sec |
| Disco logico | Disk Transfers/sec |
| Disco logico | Byte scritti su disco/sec |
| Disco logico | Scritture disco/sec |
| Disco logico | Free Megabytes |
| Disco logico | Logical Disk Bytes/sec |
| Memoria | % Available Memory |
| Memoria | % Available Swap Space |
| Memoria | % Used Memory |
| Memoria | % Used Swap Space |
| Memoria | Available MBytes Memory |
| Memoria | Available MBytes Swap |
| Memoria | Page Reads/sec |
| Memoria | Page Writes/sec |
| Memoria | Pages/sec |
| Memoria | Used MBytes Swap Space |
| Memoria | Used Memory MBytes |
| Rete | Total Bytes Transmitted |
| Rete | Total Bytes Received |
| Rete | Total Bytes |
| Rete | Total Packets Transmitted |
| Rete | Total Packets Received |
| Rete | Total Rx Errors |
| Rete | Total Tx Errors |
| Rete | Total Collisions |
| Physical Disk | Avg. Disk sec/Read |
| Physical Disk | Avg. Disk sec/Transfer |
| Physical Disk | Avg. Disk sec/Write |
| Physical Disk | Physical Disk Bytes/sec |
| Processo | Pct Privileged Time |
| Processo | Pct User Time |
| Processo | Used Memory kBytes |
| Processo | Virtual Shared Memory |
| Processore | % DPC Time |
| Processore | % Idle Time |
| Processore | % Interrupt Time |
| Processore | % IO Wait Time |
| Processore | % Nice Time |
| Processore | % Privileged Time |
| Processore | % di tempo processore |
| Processore | % User Time |
| Sistema | Free Physical Memory |
| Sistema | Free Space in Paging Files |
| Sistema | Free Virtual Memory |
| Sistema | Processi |
| Sistema | Size Stored In Paging Files |
| Sistema | Uptime |
| Sistema | Utenti |


Di seguito è illustrata la configurazione predefinita per le metriche delle prestazioni.

```xml
<source>
    type oms_omi
    object_name "Physical Disk"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 5m
</source>

<source>
    type oms_omi
    object_name "Logical Disk"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 5m
</source>

<source>
    type oms_omi
    object_name "Processor"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>

<source>
    type oms_omi
    object_name "Memory"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>
```

## <a name="data-collection"></a>Raccolta dati
Monitoraggio di Azure raccoglierà tutti i contatori delle prestazioni specificati in base all'intervallo di campionamento definito per tutti gli agenti in cui è installato il contatore.  I dati non vengono aggregati e i dati non elaborati sono disponibili in tutte le visualizzazioni di query di log per la durata specificata dall'area di lavoro di log Analytics.

## <a name="performance-record-properties"></a>Proprietà dei record delle prestazioni
Il tipo dei record delle prestazioni è **Perf** e le proprietà sono elencate nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| Computer |Computer da cui è stato raccolto l'evento. |
| CounterName |Nome del contatore delle prestazioni. |
| CounterPath |Percorso completo del contatore nel \\ \\ \<Computer> \\ contatore dell'oggetto Form (istanza) \\ . |
| CounterValue |Valore numerico del contatore. |
| InstanceName |Nome dell'istanza dell'evento.  Vuoto se l'istanza non è presente. |
| ObjectName |Nome dell'oggetto prestazioni |
| SourceSystem |Tipo di agente da cui sono stati raccolti i dati. <br><br>OpsManager: agente Windows, con connessione diretta o SCOM <br>  Linux – Tutti gli agenti Linux  <br>  AzureStorage: Diagnostica di Azure |
| TimeGenerated |Data e ora di campionamento dei dati. |

## <a name="sizing-estimates"></a>Stime di dimensionamento
 Secondo una stima approssimativa per la raccolta di un determinato contatore a intervalli di 10 secondi, le dimensioni equivalgono a circa 1 MB al giorno per istanza.  Con la formula seguente è quindi possibile stimare i requisiti di spazio di archiviazione per un contatore specifico.

> 1 MB x (numero di contatori) x (numero di agenti) x (numero di istanze)

## <a name="log-queries-with-performance-records"></a>Query di log con record delle prestazioni
La tabella seguente mostra alcuni esempi di query di log che recuperano i record delle prestazioni.

| Query | Descrizione |
|:--- |:--- |
| Perf |Tutti i dati sulle prestazioni |
| Perf &#124; where Computer == "MyComputer" |Tutti i dati sulle prestazioni da un computer specifico |
| Perf &#124; where CounterName == "Current Disk Queue Length" |Tutti i dati sulle prestazioni da un contatore specifico |
| Perf &#124; where ObjectName = = "Processor" e CounterName = = "% Processor Time" e NomeIstanza = = "_Total" &#124; riepilogare AVGCPU = AVG (CounterValue) per computer |Utilizzo medio della CPU per tutti i computer |
| Prestazioni &#124; dove CounterName = = "% tempo processore" &#124; riepilogare AggregatedValue = Max (CounterValue) per computer |Utilizzo massimo della CPU per tutti i computer |
| Perf &#124; where ObjectName = = "disco logico" e CounterName = = "lunghezza corrente coda del disco" e computer = = "nomecomputer" &#124; riepiloga AggregatedValue = AVG (CounterValue) da NomeIstanza |Lunghezza media della coda del disco corrente per tutte le istanze di un computer specifico |
| Prestazioni &#124; dove CounterName = = "trasferimenti disco/sec" &#124; riepilogare AggregatedValue = percentile (CounterValue, 95) per computer |95° percentile di trasferimenti disco al secondo per tutti i computer |
| Perf &#124; where CounterName == "% Processor Time" and InstanceName == "_Total" &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), Computer |Utilizzo orario medio della CPU per tutti i computer |
| Perf &#124; where Computer == "MyComputer" and CounterName startswith_cs "%" and InstanceName == "_Total" &#124; summarize AggregatedValue = percentile(CounterValue, 70) by bin(TimeGenerated, 1h), CounterName | 70° percentile orario di ogni contatore percentuale % per un computer specifico |
| Perf &#124; where CounterName == "% Processor Time" and InstanceName == "_Total" and Computer == "MyComputer" &#124; summarize ["min(CounterValue)"] = min(CounterValue), ["avg(CounterValue)"] = avg(CounterValue), ["percentile75(CounterValue)"] = percentile(CounterValue, 75), ["max(CounterValue)"] = max(CounterValue) by bin(TimeGenerated, 1h), Computer |Utilizzo CPU orario medio, minimo, massimo e 75° percentile per un computer specifico |
| Perf &#124; where ObjectName == "MSSQL$INST2:Databases" and InstanceName == "master" | Tutti i dati sulle prestazioni nell'oggetto delle prestazioni del database per il database master dell'istanza di SQL Server denominata INST2.  




## <a name="next-steps"></a>Passaggi successivi
* [Raccogliere i contatori delle prestazioni da applicazioni Linux](data-sources-linux-applications.md) come il server HTTP Apache e MySQL.
* Altre informazioni sulle [query di log](../logs/log-query-overview.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni.  
* Esportare i dati raccolti in [Power BI](../visualize/powerbi.md) per altre opzioni di visualizzazione e analisi.