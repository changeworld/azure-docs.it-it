---
title: Soluzione Wire data in monitoraggio di Azure | Microsoft Docs
description: I dati in transito sono dati di prestazioni e di rete consolidati provenienti da computer con agenti Log Analytics. I dati di rete vengono combinati con i dati dei log per poter correlare i dati.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/26/2021
ms.openlocfilehash: 1a9ea544419ef5c688e78a25eeb0eb444b196ec9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732024"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor-retired"></a>Soluzione Wire Data 2.0 (anteprima) in monitoraggio di Azure (ritirata)

![Simbolo di Wire Data](media/wire-data/wire-data2-symbol.png)

>[!NOTE]
>La soluzione Wire data è stata sostituita con la soluzione [VM Insights](../vm/vminsights-overview.md) e [mapping dei servizi](../vm/service-map.md).  Entrambi usano l'agente Log Analytics e Dependency Agent per raccogliere i dati di connessione di rete in monitoraggio di Azure.
>
>Il supporto per la soluzione Wire data si concluderà il **31 marzo 2022**.  Fino alla data di ritiro, i clienti esistenti che usano la soluzione Wire Data 2.0 (anteprima) potrebbero continuare a usarlo.
>
>I clienti nuovi ed esistenti devono installare la soluzione [VM Insights](../vm/vminsights-enable-overview.md) o [mapping dei servizi](../vm/service-map.md).  Il set di dati della mappa che raccoglie è paragonabile al set di dati Wire Data 2.0 (anteprima).  VM Insights include il set di dati Mapping dei servizi insieme a ulteriori dati sulle prestazioni e funzionalità per l'analisi. Entrambe le offerte includono [connessioni con Sentinel di Azure](https://docs.microsoft.com/azure/sentinel/connect-data-sources#map-data-types-with-azure-sentinel-connection-options).
 

I dati in transito sono dati di prestazioni e di rete consolidati che vengono raccolti da computer connessi tramite Windows o Linux all'agente di Log Analytics e includono i dati monitorati da Operations Manager nell'ambiente in uso. I dati di rete vengono combinati con altri dati di log per consentire la correlazione dei dati.

Oltre all'agente di Log Analytics, la soluzione Wire Data usa le istanze di Microsoft Dependency Agent installate nei computer dell'infrastruttura IT. Le istanze di Dependency Agent monitorano i dati di rete inviati da e verso i computer per i livelli di rete 2-3 del [modello OSI](https://en.wikipedia.org/wiki/OSI_model), che includono le diverse porte e i vari protocolli usati. I dati vengono quindi inviati al monitoraggio di Azure tramite agenti.

## <a name="migrate-to-azure-monitor-vm-insights-or-service-map"></a>Eseguire la migrazione ad Azure monitor VM Insights o Mapping dei servizi

In molti casi, si noterà che i clienti hanno spesso sia Wire Data 2.0 (anteprima) che le  [informazioni sulle VM](../vm/vminsights-overview.md) o [mapping dei servizi soluzione](../vm/service-map.md) già abilitata nelle stesse macchine virtuali.  Ciò significa che l'offerta di sostituzione è abilitata nella macchina virtuale.  È possibile [rimuovere semplicemente la soluzione Wire data 2.0 (anteprima) dall'area di lavoro di log Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution).

Se si dispone di macchine virtuali in cui sono abilitate solo Wire Data 2.0 (anteprima), è possibile caricare le VM in una soluzione di [VM Insights](../vm/vminsights-enable-overview.md) o [mapping dei servizi](../vm/service-map.md) , quindi [rimuovere la soluzione Wire data 2.0 (anteprima) dall'area di lavoro log Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution).

## <a name="migrate-your-queries-to-the-vmconnection-table-from-azure-monitor-vm-insights"></a>Eseguire la migrazione delle query alla tabella VMConnection da monitoraggio di Azure VM Insights

### <a name="agents-providing-data"></a>Agenti che forniscono dati

#### <a name="wire-data-20-query"></a>Query Wire Data 2.0

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by Computer
| limit 500000
```

#### <a name="vm-insights-and-service-map-query"></a>VM Insights e query Mapping dei servizi

```
VMConnection
| summarize AggregatedValue = sum(BytesReceived + BytesSent) by Computer
| limit 500000
```

### <a name="ip-addresses-of-the-agents-providing-data"></a>Indirizzi IP degli agenti che forniscono dati

#### <a name="wire-data-20-query"></a>Query Wire Data 2.0

```
WireData
| summarize AggregatedValue = count() by LocalIP
```

#### <a name="vm-insights-and-service-map-query"></a>VM Insights e query Mapping dei servizi

```
VMComputer
| distinct Computer, tostring(Ipv4Addresses)
```

### <a name="all-outbound-communications-by-remote-ip-address"></a>Tutte le comunicazioni in uscita per indirizzo IP remoto

#### <a name="wire-data-20-query"></a>Query Wire Data 2.0

```
WireData
| where Direction == "Outbound"
| summarize AggregatedValue = count() by RemoteIP
```

#### <a name="vm-insights-and-service-map-query"></a>VM Insights e query Mapping dei servizi

```
VMConnection
| where Direction == "outbound"
| summarize AggregatedValue = count() by RemoteIp
```

### <a name="bytes-received-by-protocol-name"></a>Byte ricevuti per nome protocollo

#### <a name="wire-data-20-query"></a>Query Wire Data 2.0

```
WireData 
| where Direction == "Inbound"
| summarize AggregatedValue = sum(ReceivedBytes) by ProtocolName
```

#### <a name="vm-insights-and-service-map-query"></a>VM Insights e query Mapping dei servizi

```
VMConnection
| where Direction == "inbound"
| summarize AggregatedValue = sum(BytesReceived) by Protocol
```

### <a name="amount-of-network-traffic-in-bytes-by-process"></a>Quantità di traffico di rete (in byte) per processo

#### <a name="wire-data-20-query"></a>Query Wire Data 2.0

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by ProcessName
```

#### <a name="vm-insights-and-service-map-query"></a>VM Insights e query Mapping dei servizi

```
VMConnection
| summarize sum(BytesReceived), sum(BytesSent) by ProcessName
```

### <a name="more-examples-queries"></a>Altri esempi di query

Vedere la [documentazione di ricerca log di VM Insights](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-log-search) e la [documentazione degli avvisi di VM Insights](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-alerts#sample-alert-queries) per altre query di esempio.

## <a name="uninstall-wire-data-20-solution"></a>Disinstalla Wire Data 2.0 soluzione

Per disinstallare Wire Data 2.0 è semplicemente necessario rimuovere la soluzione dalle aree di lavoro Log Analytics.  Si otterrà quindi quanto segue:

* il Wire Gestione dati Pack viene rimosso dalle macchine virtuali connesse all'area di lavoro 
* il tipo di dati wire data non viene più visualizzato nell'area di lavoro

Seguire [queste istruzioni](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution) per rimuovere la soluzione Wire data.

>[!NOTE]
>Se è presente la soluzione Mapping dei servizi o VM Insights nell'area di lavoro, la Management Pack non verrà rimossa, perché queste soluzioni usano anche questa Management Pack.

### <a name="wire-data-20-management-packs"></a>Management Pack Wire Data 2.0

Quando viene attivato Wire Data in un'area di lavoro Log Analytics, a tutti i server Windows nell'area di lavoro viene inviato un Management Pack di 300 KB. Se si usano agenti System Center Operations Manager in un [gruppo di gestione connesso](../agents/om-agents.md), il Management Pack di Dependency Monitor viene distribuito da System Center Operations Manager. Se gli agenti sono connessi direttamente, monitoraggio di Azure recapita il Management Pack.

Il Management Pack è denominato Microsoft.IntelligencePacks.ApplicationDependencyMonitor e viene inserito in %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\. L'origine dati usata dal Management Pack è %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;IDGeneratoAutomaticamente&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="uninstall-the-dependency-agent"></a>Disinstallare Dependency Agent

>[!NOTE]
>Se si prevede di sostituire i dati in transito con Mapping dei servizi o VM Insights, è consigliabile non rimuovere Dependency Agent.

Usare le sezioni seguenti per rimuovere Dependency Agent.  

### <a name="uninstall-the-dependency-agent-on-windows"></a>Disinstallare Dependency Agent in Windows

Dependency Agent per Windows può essere disinstallato da un amministratore tramite il Pannello di controllo.

Per disinstallare Dependency Agent, un amministratore può anche eseguire %Programfiles%\Microsoft Dependency Agent\Uninstall.exe.

### <a name="uninstall-the-dependency-agent-on-linux"></a>Disinstallare Dependency Agent in Linux

Per disinstallare completamente Dependency Agent da Linux, è necessario rimuovere l'agente stesso e il connettore, che viene installato automaticamente con l'agente. È possibile disinstallare entrambi con il singolo comando seguente:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="using-the-wire-data-20-solution"></a>Uso della soluzione Wire Data 2.0

Nella pagina **Panoramica** dell'area di lavoro Log Analytics nel portale di Azure, fare clic sul riquadro **Wire Data 2.0** per aprire il dashboard di Wire Data. Il dashboard include i pannelli nella tabella seguente. Ogni panello elenca fino a 10 elementi corrispondenti ai criteri del pannello per lo scope e l'intervallo di tempo specificati. È possibile eseguire una ricerca log per ottenere tutti i record facendo clic su **Vedi tutto** nella parte inferiore del pannello o facendo clic sull'intestazione del pannello.

| **Pannello** | **Descrizione** |
| --- | --- |
| Agenti che acquisiscono il traffico di rete | Mostra il numero degli agenti che acquisiscono il traffico di rete e un elenco dei primi 10 computer che acquisiscono il traffico. Fare clic sul numero per eseguire una ricerca nei log per <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code>. Fare clic su un computer nell'elenco per eseguire una ricerca nei log che restituisca il numero totale dei byte acquisiti. |
| Subnet locali | Mostra il numero delle subnet locali individuate dagli agenti.  Fare clic sul numero per eseguire una ricerca nei log per <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code> e ottenere un elenco di tutte le subnet con il numero dei byte inviati tramite ognuna. Fare clic su una subnet nell'elenco per eseguire una ricerca nei log che restituisca il numero totale dei byte inviati tramite la subnet. |
| Protocolli a livello dell'applicazione | Mostra il numero di protocolli a livello di applicazione in uso, in base a quanto individuato dagli agenti. Fare clic sul numero per eseguire una ricerca nei log per <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code>. Fare clic su un protocollo per eseguire una ricerca nei log che restituisca il numero totale dei byte inviati usando il protocollo. |

![Dashboard di Wire Data](./media/wire-data/wire-data-dash.png)

È possibile usare il pannello **Agenti che acquisiscono il traffico di rete** per determinare la quantità di larghezza di banda utilizzata dai computer. Questo pannello consente di trovare facilmente il computer _più comunicativo_ nel proprio ambiente. Tali computer potrebbero essere sovraccaricati, presentare un funzionamento anomalo o usare una quantità di risorse di rete superiore alla norma.

![Screenshot del pannello degli agenti di acquisizione del traffico di rete nel dashboard Wire Data 2.0 che mostra la larghezza di banda di rete utilizzata da ogni computer.](./media/wire-data/log-search-example01.png)

Analogamente, è possibile usare il pannello **Subnet locali** per determinare la quantità di traffico di rete sulle subnet. Gli utenti spesso definiscono le subnet per aree critiche per le applicazioni. Questo pannello offre un quadro di tali aree.

![Screenshot del pannello subnet locali nel dashboard Wire Data 2.0 che mostra la larghezza di banda di rete usata da ogni LocalSubnet.](./media/wire-data/log-search-example02.png)

Il pannello **Protocolli a livello dell'applicazione** è utile perché è opportuno sapere quali protocolli vengono usati. Se ad esempio si prevede che SSH non venga usato nel proprio ambiente di rete, visualizzando le informazioni disponibili nel pannello è possibile ottenere rapidamente conferma o smentita di tale previsione.

![Screenshot del pannello protocolli a livello di applicazione nel dashboard Wire Data 2.0 che mostra la larghezza di banda di rete usata da ogni protocollo.](./media/wire-data/log-search-example03.png)

È anche utile sapere se il traffico dei protocolli aumenta o diminuisce nel tempo. L'aumento della quantità di dati trasmessa da un'applicazione, ad esempio, può essere un aspetto di cui è consigliabile essere a conoscenza o che si potrebbe trovare degno di nota.

## <a name="input-data"></a>Dati di input

Wire Data raccoglie i metadati sul traffico di rete tramite gli agenti abilitati. Ogni agente invia dati ogni 15 secondi circa.

## <a name="output-data"></a>Dati di output

Per ogni tipo di dati di input vene creato un record con tipo _WireData_. I record WireData includono le proprietà elencate nella tabella seguente:

| Proprietà | Descrizione |
|---|---|
| Computer | Nome del computer in cui sono stati raccolti i dati |
| TimeGenerated | Ora del record |
| LocalIP | Indirizzo IP del computer locale |
| SessionState | Sessione connessa o disconnessa |
| ReceivedBytes | Quantità di byte ricevuta |
| ProtocolName | Nome del protocollo di rete usato |
| IPVersion | Versione IP |
| Direzione | In ingresso o in uscita |
| MaliciousIP | Indirizzo IP di un'origine dannosa nota |
| Gravità | Gravità del software dannoso sospetto |
| RemoteIPCountry | Paese/area geografica dell'indirizzo IP remoto |
| ManagementGroupName | Nome del gruppo di gestione di Operations Manager |
| SourceSystem | Origine in cui sono stati raccolti i dati |
| SessionStartTime | Data e ora di inizio della sessione |
| SessionEndTime | Data e ora di fine della sessione |
| LocalSubnet | Subnet in cui sono stati raccolti i dati |
| LocalPortNumber | Numero di porta locale |
| RemoteIP | Indirizzo IP remoto usato dal computer remoto |
| RemotePortNumber | Numero di porta usato dall'indirizzo IP remoto |
| SessionID | Valore univoco che identifica la sessione di comunicazione tra due indirizzi IP |
| SentBytes | Numero di byte inviati |
| TotalBytes | Numero totale dei byte inviati durante la sessione |
| ApplicationProtocol | Tipo di protocollo di rete usato   |
| ProcessID | ID processo Windows |
| ProcessName | Percorso e nome file del processo |
| RemoteIPLongitude | Valore di longitudine dell'indirizzo IP |
| RemoteIPLatitude | Valore di latitudine dell'indirizzo IP |

## <a name="next-steps"></a>Passaggi successivi

- Vedere [distribuire VM Insights](./vminsights-enable-overview.md) per i requisiti e i metodi che consentono il monitoraggio delle macchine virtuali.
- [Ricerche nei log](../logs/log-query-overview.md) per visualizzare i record di ricerca dettagliati su Wire Data.
