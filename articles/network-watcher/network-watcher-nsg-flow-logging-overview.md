---
title: Introduzione alla registrazione dei flussi per i NSG
titleSuffix: Azure Network Watcher
description: Questo articolo illustra come usare la funzionalità dei log dei flussi dei gruppi di sicurezza di rete di Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: 206bcfaeb5cb13d3ecf1e5f6335518c42df21eb8
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535301"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introduzione alla registrazione dei flussi per i gruppi di sicurezza di rete

## <a name="introduction"></a>Introduzione

[I log dei flussi](../virtual-network/network-security-groups-overview.md#security-rules) del gruppo di sicurezza di rete (NSG) sono una funzionalità di Azure Network Watcher che consente di registrare informazioni sul flusso del traffico IP attraverso un gruppo di sicurezza di rete. I dati del flusso vengono inviati Archiviazione di Azure account da cui è possibile accedervi, nonché esportati in qualsiasi strumento di visualizzazione, SIEM o IDS di propria scelta.

![Panoramica dei log di flusso](./media/network-watcher-nsg-flow-logging-overview/homepage.jpg)

## <a name="why-use-flow-logs"></a>Perché usare i log dei flussi?

È fondamentale monitorare, gestire e conoscere la rete per fare in modo che la sicurezza, la conformità e le prestazioni non vengano compromesse. Per proteggere e ottimizzare l'ambiente, è indispensabile conoscerlo. Spesso è necessario conoscere lo stato corrente della rete, chi si connette a cosa, da dove si connettono, quali porte sono aperte a Internet, il comportamento previsto della rete, il comportamento irregolare della rete e i picchi improvvisi di traffico.

I log dei flussi sono l'origine di dati veri e propri per tutte le attività di rete nell'ambiente cloud. Indipendentemente dal fatto che si stia tentando di ottimizzare le risorse o che un'azienda di grandi dimensioni stia tentando di rilevare le intrusioni, i log dei flussi sono la soluzione migliore. È possibile usarlo per ottimizzare i flussi di rete, monitorare la velocità effettiva, verificare la conformità, rilevare le intrusioni e altro ancora.

## <a name="common-use-cases"></a>Casi d'uso comuni

**Monitoraggio di rete:** identificare il traffico sconosciuto o indesiderato. Monitorare i livelli di traffico e l'utilizzo della larghezza di banda. Filtrare i log dei flussi in base all'INDIRIZZO IP e alla porta per comprendere il comportamento dell'applicazione. Esportare i log dei flussi in strumenti di analisi e visualizzazione di propria scelta per configurare i dashboard di monitoraggio.

**Monitoraggio e ottimizzazione dell'utilizzo:** Identificare i principali talker nella rete. Combinare con i dati GeoIP per identificare il traffico tra aree. Comprendere la crescita del traffico per la previsione della capacità. Usare i dati per rimuovere regole di traffico eccessivamente restrittive.

**Conformità:** usare i dati del flusso per verificare l'isolamento della rete e la conformità alle regole di accesso aziendale

**Analisi della sicurezza & analisi della sicurezza:** analizzare i flussi di rete provenienti da ip e interfacce di rete compromessi. Esportare i log dei flussi in qualsiasi strumento SIEM o IDS di propria scelta.

## <a name="how-logging-works"></a>Funzionamento della registrazione

**Proprietà chiave**

- I log di flusso operano [al livello 4](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer) e registrano tutti i flussi IP in ingresso e in uscita da un gruppo di sicurezza di rete
- I log vengono raccolti a **intervalli di 1 minuto** tramite la piattaforma Azure e non influiscono in alcun modo sulle risorse dei clienti o sulle prestazioni di rete.
- I log vengono scritti in formato JSON e mostrano i flussi in uscita e in ingresso in base alle regole del gruppo di protezione di rete.
- Ogni record di log contiene l'interfaccia di rete (NIC) a cui si applica il flusso, informazioni a 5 tuple, la decisione del traffico & (solo versione 2) informazioni sulla velocità effettiva. Per _informazioni dettagliate,_ vedere Formato log di seguito.
- I log di flusso hanno una funzionalità di conservazione che consente di eliminare automaticamente i log fino a un anno dopo la loro creazione. 

> [!NOTE]
> La conservazione è disponibile solo se si usano account di archiviazione per utilizzo [generico v2 (GPv2).](../storage/common/storage-account-overview.md#types-of-storage-accounts) 

**Concetti di base**

- Le reti software-defined sono organizzate in base alle reti virtuali (VNET) e alle subnet. La sicurezza di queste reti virtuali e subnet può essere gestita tramite un gruppo di sicurezza di rete.
- Un gruppo di sicurezza di rete (NSG) contiene un elenco di regole di sicurezza che consentono o negano il traffico di rete nelle risorse a cui è connesso.  I NSG possono essere associati a subnet, singole macchine virtuali o singole interfacce di rete collegate alle macchine virtuali (Resource Manager). Per altre informazioni, vedere [Panoramica dei gruppi di sicurezza di rete](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Tutti i flussi di traffico nella rete vengono valutati usando le regole nel gruppo di sicurezza di rete applicabile.
- Il risultato di queste valutazioni è log del flusso del gruppo di sicurezza di rete. I log di flusso vengono raccolti tramite la piattaforma Azure e non richiedono alcuna modifica alle risorse del cliente.
- Nota: le regole sono di due tipi: terminando & terminazione, ognuna con comportamenti di registrazione diversi.
- - Le regole di negazione del gruppo di sicurezza di rete vengono terminate. Il gruppo di sicurezza di rete che nega il traffico lo logerà nei log di Flow e l'elaborazione in questo caso si arresterà dopo che un gruppo di sicurezza di rete negherà il traffico. 
- - Le regole consentite del gruppo di sicurezza di rete non terminano, ovvero anche se un gruppo di sicurezza di rete lo consente, l'elaborazione continuerà al gruppo di sicurezza di rete successivo. L'ultimo gruppo di sicurezza di rete che consente il traffico registra il traffico nei log di Flow.
- I log di flusso del gruppo di rete vengono scritti in account di archiviazione da cui è possibile accedervi.
- È possibile esportare, elaborare, analizzare e visualizzare i log di Flusso usando strumenti come TA, Splunk, Grafana, Stealthwatch e così via.

## <a name="log-format"></a>Formato del log

I log dei flussi includono le proprietà seguenti:

* **time**: ora in cui l'evento è stato registrato.
* **systemId** - ID di sistema del gruppo di sicurezza di rete.
* **category**: categoria dell'evento. La categoria è sempre **NetworkSecurityGroupFlowEvent**
* **resourceid:** ID risorsa del gruppo di criteri di rete
* **operationName**: sempre NetworkSecurityGroupFlowEvents.
* **properties**: raccolta di proprietà del flusso.
    * **Version**: numero di versione dello schema di eventi del log dei flussi.
    * **flows** : raccolta di flussi. Questa proprietà ha più voci per regole diverse.
        * **rule**: regola per cui vengono elencati i flussi.
            * **flows**: raccolta di flussi.
                * **mac**: indirizzo MAC della scheda di interfaccia di rete per la VM in cui è stato raccolto il flusso.
                * **flowTuples**: stringa che contiene più proprietà per la tupla del flusso nel formato con valori separati da virgole.
                    * **Timestamp:** questo valore è il timestamp di quando il flusso si è verificato in formato un tempo UNIX
                    * **Source IP**: IP di origine.
                    * **Destination IP**: IP di destinazione.
                    * **Source Port**: porta di origine.
                    * **Destination Port**: porta di destinazione.
                    * **Protocol**: protocollo del flusso. I valori validi sono **T** per TCP e **U** per UDP.
                    * **Traffic Flow**: direzione del flusso del traffico. I valori validi sono **I** per traffico in ingresso e **O** per il traffico in uscita.
                    * **Traffic Decision**: indica se il traffico è stato consentito o negato. I valori validi sono **A** per il traffico consentito e **D** per il traffico negato.
                    * **Flow State - Version 2 Only**: acquisisce lo stato del flusso. Gli stati possibili sono **B**: indica la creazione di un flusso. Non vengono fornite statistiche. **C**: indica un flusso in corso. Vengono fornite statistiche a intervalli di 5 minuti. **E**: indica un flusso terminato. Vengono fornite statistiche.
                    * **Packets - Source to destination - Version 2 Only** Numero totale di pacchetti TCP o UDP inviati dall'origine alla destinazione dall'ultimo aggiornamento.
                    * **Bytes sent - Source to destination - Version 2 Only** Numero totale di byte dei pacchetti TCP o UDP inviati dall'origine alla destinazione dall'ultimo aggiornamento. I byte dei pacchetti includono l'intestazione del pacchetto e il payload.
                    * **Packets - Destination to source - Version 2 Only** Numero totale di pacchetti TCP o UDP inviati dalla destinazione all'origine dall'ultimo aggiornamento.
                    * **Bytes sent - Destination to source - Version 2 Only** Numero totale di byte dei pacchetti TCP e UDP inviati dalla destinazione all'origine dall'ultimo aggiornamento. I byte dei pacchetti includono payload e intestazione del pacchetto.


**Log dei flussi del gruppo di protezione di rete versione 2 (rispetto alla versione 1)** 

La versione 2 dei log introduce il concetto di stato del flusso. È possibile configurare la versione del log di flusso ricevuta.

Lo stato del flusso _B_ viene registrato quando viene avviato un flusso. Gli stati del flusso _C_ ed _E_ contrassegnano rispettivamente la continuazione e la fine di un flusso. Gli stati _C_ ed _E_ contengono informazioni sulla larghezza di banda del traffico.

### <a name="sample-log-records"></a>Record di log di esempio

Di seguito è riportato un testo di esempio di log dei flussi. Come si può osservare, più record seguono l'elenco di proprietà descritto nella sezione precedente.

> [!NOTE]
> I valori nella *proprietà flowTuples* sono un elenco delimitato da virgole.
 
**Esempio di formato di log di flusso del gruppo di sicurezza di rete versione 1**
```json
{
    "records": [
        {
            "time": "2017-02-16T22:00:32.8950000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A",
                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A",
                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A",
                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2017-02-16T22:01:32.8960000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",
                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",
                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
    "records":
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        
        
```
**Esempio di formato di log di flusso del gruppo di sicurezza di rete versione 2**
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        }
        
```
**Illustrata la tupla di log**

![tupla dei log di flusso](./media/network-watcher-nsg-flow-logging-overview/tuple.png)

**Calcolo della larghezza di banda di esempio**

tuple dei flussi da una conversazione TCP tra 185.170.185.105:35370 e 10.2.0.4:23:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

Per gli stati _C_ ed _E_ del flusso, i conteggi di byte e pacchetti sono conteggi aggregati dal momento della registrazione della tupla del flusso precedente. Facendo riferimento alla conversazione di esempio precedente, il numero totale di pacchetti trasferiti è 1021+52+8005+47=9125. Il numero totale di byte trasferiti è 588096+29952+4610880+27072=5256000.


## <a name="enabling-nsg-flow-logs"></a>Abilitazione dei log dei flussi dei gruppi di sicurezza di rete

Usare il collegamento pertinente riportato di seguito per le guide sull'abilitazione dei log di flusso.

- [Azure portal](./network-watcher-nsg-flow-logging-portal.md)
- [PowerShell](./network-watcher-nsg-flow-logging-powershell.md)
- [CLI](./network-watcher-nsg-flow-logging-cli.md)
- [REST](./network-watcher-nsg-flow-logging-rest.md)
- [Azure Resource Manager](./network-watcher-nsg-flow-logging-azure-resource-manager.md)

## <a name="updating-parameters"></a>Aggiornamento dei parametri

**Azure portal**

Nel portale di Azure passare alla sezione Log flusso del gruppo di protezione di rete in Network Watcher. Fare quindi clic sul nome del gruppo di criteri di rete. Verrà visualizzato il riquadro delle impostazioni per il log di Flow. Modificare i parametri desiderati e fare clic **su Salva** per distribuire le modifiche.

**PS/CLI/REST/ARM**

Per aggiornare i parametri tramite gli strumenti da riga di comando, usare lo stesso comando usato per abilitare i log di flusso (dall'alto) ma con i parametri aggiornati che si desidera modificare.

## <a name="working-with-flow-logs"></a>Uso dei log di Flow

*Leggere ed esportare i log dei flussi*

- [Scaricare &amp; i log del flusso di visualizzazione dal portale](./network-watcher-nsg-flow-logging-portal.md#download-flow-log)
- [Leggere i log di Flow usando le funzioni di PowerShell](./network-watcher-read-nsg-flow-logs.md)
- [Esportare i log del flusso del gruppo di protezione di rete in Splunk](https://www.splunk.com/en_us/blog/tips-and-tricks/splunking-microsoft-azure-network-watcher-data.html)

Anche se i log dei flussi specificano come destinazione gruppi di sicurezza di rete, non vengono visualizzati come gli altri log. I log dei flussi vengono archiviati solo in un account di archiviazione e hanno un percorso di registrazione come quello dell'esempio seguente:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

*Visualizzare i log di flusso*

- [Analisi del traffico di Azure](./traffic-analytics.md) è un servizio nativo di Azure per elaborare i log dei flussi, estrarre informazioni dettagliate e visualizzare i log di flusso. 
- [[Esercitazione] Visualizzare i log del flusso NSG con Power BI](./network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [[Esercitazione] Visualizzare i log del flusso del gruppo di protezione di rete con lo stack elastico](./network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [[Esercitazione] Gestire e analizzare i log del flusso NSG usando Grafana](./network-watcher-nsg-grafana.md)
- [[Esercitazione] Gestire e analizzare i log del flusso NSG usando Graylog](./network-watcher-analyze-nsg-flow-logs-graylog.md)

*Disabilitare i log di flusso*

Quando il log di flusso è disabilitato, la registrazione del flusso per il gruppo di protezione di rete associato viene arrestata. Tuttavia, il log di flusso come risorsa continua a esistere con tutte le relative impostazioni e associazioni. Può essere abilitato in qualsiasi momento per avviare la registrazione del flusso nel gruppo di protezione di rete configurato. La procedura per disabilitare/abilitare un log di flusso è disponibile in [questa guida.](./network-watcher-nsg-flow-logging-powershell.md)  

*Eliminare i log di flusso*

Quando il log di flusso viene eliminato, non solo la registrazione del flusso per il gruppo di sicurezza di rete associato viene arrestata, ma anche la risorsa del log di flusso viene eliminata con le relative impostazioni e associazioni. Per iniziare di nuovo la registrazione del flusso, è necessario creare una nuova risorsa di log del flusso per tale gruppo di protezione di rete. Un log di flusso può essere eliminato usando [PowerShell, l'interfaccia](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkwatcherflowlog) [della riga di comando](https://docs.microsoft.com/cli/azure/network/watcher/flow-log#az_network_watcher_flow_log_delete) o [l'API REST.](https://docs.microsoft.com/rest/api/network-watcher/flowlogs/delete) Il supporto per l'eliminazione dei log di flusso portale di Azure è in pipeline.    

Inoltre, quando viene eliminato un gruppo di protezione di rete, per impostazione predefinita viene eliminata la risorsa log di flusso associata.

> [!NOTE]
> Per spostare un gruppo di sicurezza di rete in un gruppo di risorse o una sottoscrizione diversa, i log di flusso associati devono essere eliminati, ma la semplice disabilitazione dei log di flusso non funzionerà. Dopo la migrazione del gruppo di protezione di rete, i log di flusso devono essere ricreati per abilitare la registrazione del flusso su di esso.  

## <a name="nsg-flow-logging-considerations"></a>Considerazioni sulla registrazione dei flussi del gruppo di sicurezza di rete

**Considerazioni sull'account di archiviazione:** 

- Località: l'account di archiviazione usato deve essere nella stessa area del gruppo di criteri di rete.
- Livello di prestazioni: attualmente sono supportati solo gli account di archiviazione di livello standard.
- Rotazione delle chiavi con gestione automatica: se si modificano o ruotano le chiavi di accesso nell'account di archiviazione, i log dei flussi del gruppo di protezione di rete smetteranno di funzionare. Per risolvere questo problema, è necessario disabilitare e quindi riattivare i log dei flussi del gruppo di sicurezza di rete.

**Costi di registrazione del flusso:** la registrazione dei flussi del gruppo di disponibilità di rete viene fatturata in base al volume di log prodotti. Un volume di traffico elevato può avere come effetto un volume elevato dei log dei flussi, con i conseguenti costi associati. I prezzi dei log dei flussi dei gruppi di sicurezza di rete non includono i costi di archiviazione sottostanti. L'uso della funzionalità dei criteri di conservazione con registrazione del flusso del gruppo di sicurezza di rete comporta costi di archiviazione separati per lunghi periodi di tempo. Se si vogliono conservare i dati per sempre e non si vogliono applicare criteri di conservazione, impostare conservazione (giorni) su 0. Per altre informazioni, vedere Prezzi [Network Watcher e](https://azure.microsoft.com/pricing/details/network-watcher/) [prezzi Archiviazione di Azure per](https://azure.microsoft.com/pricing/details/storage/) altri dettagli.

**Problemi con le regole TCP in** ingresso definite dall'utente: i gruppi di sicurezza di rete [vengono](../virtual-network/network-security-groups-overview.md) implementati come firewall [con stato.](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true) Tuttavia, a causa delle attuali limitazioni della piattaforma, le regole definite dall'utente che influiscono sui flussi TCP in ingresso vengono implementate senza stato. A causa di questo problema, i flussi interessati dalle regole in ingresso definite dall'utente diventano non terminanti. Inoltre, i conteggi di byte e pacchetti non vengono registrati per questi flussi. Di conseguenza, il numero di byte e pacchetti segnalati nei log dei flussi del gruppo di Analisi del traffico (NSG Flow Logs) potrebbe essere diverso dai numeri effettivi. È previsto che un flag di consenso esplicito che corregge questi problemi sia disponibile entro il mese di marzo 2021 più recente. Nel frattempo, i clienti che si trovano ad affrontare problemi gravi a causa di questo comportamento possono richiedere il consenso esplicito tramite il supporto, generare una richiesta di supporto in Network Watcher > di flusso del gruppo di sicurezza di rete.  

Flussi in ingresso registrati dagli indirizzi IP Internet alle macchine virtuali senza indirizzi IP **pubblici:** le macchine virtuali a cui non è assegnato un indirizzo IP pubblico tramite un indirizzo IP pubblico associato alla scheda di interfaccia di rete come IP pubblico a livello di istanza o che fanno parte di un pool back-end del servizio di bilanciamento del carico di base, usano [SNAT](../load-balancer/load-balancer-outbound-connections.md) predefinito e hanno un indirizzo IP assegnato da Azure per facilitare la connettività in uscita. Di conseguenza, se il flusso è destinato a una porta nell'intervallo di porte assegnate per SNAT, potrebbero essere visualizzate voci di log dei flussi provenienti da indirizzi IP Internet. Anche se Azure non consente questi flussi alla macchina virtuale, il tentativo viene registrato e visualizzato nel log dei flussi del gruppo di sicurezza di rete di Network Watcher per impostazione predefinita. È consigliabile bloccare in modo esplicito il traffico Internet in ingresso indesiderato con il gruppo di sicurezza di rete.

**Problema con il gruppo di** sicurezza di rete della subnet V2 del gateway applicazione: la registrazione del flusso nel gruppo di sicurezza di rete della subnet V2 del gateway applicazione non è [attualmente](../application-gateway/application-gateway-faq.yml#are-nsg-flow-logs-supported-on-nsgs-associated-to-application-gateway-v2-subnet) supportata. Questo problema non influisce sul gateway applicazione V1.

**Servizi incompatibili:** a causa delle limitazioni correnti della piattaforma, un piccolo set di servizi di Azure non è supportato dai log dei flussi dei gruppi di sicurezza di rete. L'elenco corrente di servizi incompatibili è
- [Servizio Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/)
- [App per la logica](https://azure.microsoft.com/services/logic-apps/) 

## <a name="best-practices"></a>Procedure consigliate

**Abilita in subnet/VNET** critiche: i log dei flussi devono essere abilitati in tutte le VNET/subnet critiche nella sottoscrizione come procedura consigliata per la controllabilità e la sicurezza. 

**Abilitare la registrazione dei flussi dei gruppi** di sicurezza di rete in tutti i gruppi di sicurezza di rete collegati a una risorsa: la registrazione dei flussi in Azure è configurata nella risorsa del gruppo di sicurezza di rete. Un flusso sarà associato a una sola regola di gruppo di sicurezza di rete. Negli scenari in cui vengono usati più gruppi di sicurezza di rete, è consigliabile abilitare i log dei flussi in tutti i gruppi di sicurezza di rete applicati all'interfaccia di rete o subnet di una risorsa per assicurarsi che tutto il traffico venga registrato, Per altre informazioni, vedere Come [viene valutato il traffico](../virtual-network/network-security-group-how-it-works.md) nei gruppi di sicurezza di rete. 

Alcuni scenari comuni:
1. **Più schede di interfaccia di rete in** una macchina virtuale: nel caso in cui più schede di interfaccia di rete siano collegate a una macchina virtuale, la registrazione dei flussi deve essere abilitata in tutte
1. **Avere un gruppo di** sicurezza di rete sia a livello di scheda di interfaccia di rete che a livello di subnet: nel caso in cui il gruppo di sicurezza di rete sia configurato sia a livello di scheda di interfaccia di rete che a livello di subnet, la registrazione dei flussi deve essere abilitata in entrambi i NSG. 

**Provisioning dell'archiviazione:** il provisioning dello spazio di archiviazione deve essere eseguito in modo ottimizzato con il volume previsto del log di flusso.

**Denominazione:** il nome del gruppo di criteri di rete deve contenere fino a 80 caratteri e i nomi delle regole del gruppo di criteri di rete devono contenere fino a 65 caratteri. Se i nomi superano il limite di caratteri, potrebbe essere troncato durante la registrazione.

## <a name="troubleshooting-common-issues"></a>Risoluzione dei problemi comuni

**Impossibile abilitare i log dei flussi dei gruppi di sicurezza di rete**

- Il provider **di risorse Microsoft.Insights** non è registrato

Se è stato ricevuto un errore _AuthorizationFailed_ o _GatewayAuthenticationFailed_, potrebbe significare che il provider di risorse Microsoft Insights non è stato abilitato nella sottoscrizione. [Seguire le istruzioni](./network-watcher-nsg-flow-logging-portal.md#register-insights-provider) per abilitare il provider di Microsoft Insights.

**Sono stati abilitati i log dei flussi dei gruppi di sicurezza di rete ma non vengono visualizzati dati nell'account di archiviazione**

- **Tempo di installazione**

La visualizzazione dei log dei flussi dei gruppo di sicurezza di rete nell'account di archiviazione (se configurato correttamente) può richiedere fino a 5 minuti. Verrà visualizzato PT1H.json, a cui è possibile accedere [come descritto qui](./network-watcher-nsg-flow-logging-portal.md#download-flow-log).

- **Assenza di traffico nei gruppi di sicurezza di rete**

Talvolta i log non vengono visualizzati perché le macchine virtuali non sono attive oppure in un gateway applicazione o in altri dispositivi sono presenti filtri upstream che bloccano il traffico verso i gruppi di sicurezza di rete.

**Si desidera automatizzare i log dei flussi dei gruppi di sicurezza di rete**

Il supporto dell'automazione tramite modelli di Azure Resource Manager non è attualmente disponibile per i log dei flussi dei gruppi di sicurezza di rete. Per altre informazioni, leggere l'[annuncio della funzionalità](https://azure.microsoft.com/updates/arm-template-support-for-nsg-flow-logs/).

## <a name="faq"></a>Domande frequenti

**Che cosa fanno i log dei flussi del gruppo di protezione di rete?**

Le risorse di rete di Azure possono essere combinate e gestite tramite gruppi di [sicurezza di rete (NSG).](../virtual-network/network-security-groups-overview.md) I log dei flussi del gruppo di sicurezza di rete consentono di registrare informazioni sul flusso di 5 tuple su tutto il traffico attraverso i NSG. I log di flusso non elaborati vengono scritti in un account Archiviazione di Azure da cui possono essere ulteriormente elaborati, analizzati, sottoposti a query o esportati in base alle esigenze.

**L'uso dei log di flusso influisce sulla latenza o sulle prestazioni della rete?**

I dati dei log di flusso vengono raccolti all'esterno del percorso del traffico di rete e pertanto non influiscono sulla velocità effettiva o sulla latenza di rete. È possibile creare o eliminare i log di flusso senza alcun rischio di impatto sulle prestazioni di rete.

**Ricerca per categorie usare i log dei flussi del gruppo di sicurezza di rete con un account di archiviazione dietro un firewall?**

Per usare un account di archiviazione dietro un firewall, è necessario fornire un'eccezione per i servizi Microsoft attendibili per accedere all'account di archiviazione:

- Passare all'account di archiviazione digitando il nome dell'account di archiviazione nella ricerca globale nel portale o nella pagina [Account di archiviazione](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)
- Nella sezione  **IMPOSTAZIONI**  selezionare  **Firewall e reti virtuali**
- In **Consenti accesso da** selezionare Reti  **selezionate**. In  **Eccezioni selezionare quindi** la casella accanto a ***Consenti servizi Microsoft attendibili per accedere a questo account di archiviazione****
- Se l'opzione è già selezionata, non occorre modificare niente.
- Individuare il gruppo di protezione di rete di destinazione nella pagina di panoramica [Log](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) flusso gruppo di rete e abilitare Log flusso gruppo di rete con l'account di archiviazione precedente selezionato.

È possibile controllare i log di archiviazione dopo alcuni minuti. Dovrebbe essere visualizzato un TimeStamp aggiornato o un nuovo file JSON creato.

**Ricerca per categorie usare i log dei flussi del gruppo di protezione di rete con un account di archiviazione dietro un endpoint di servizio?**

I log dei flussi dei NSG sono compatibili con gli endpoint di servizio senza richiedere alcuna configurazione aggiuntiva. Vedere [l'esercitazione sull'abilitazione degli endpoint di servizio](../virtual-network/tutorial-restrict-network-access-to-resources.md#enable-a-service-endpoint) nella rete virtuale.

**Qual è la differenza tra i log di flusso versioni 1 & 2?**

Flow Logs version 2 introduce il concetto di _Flow State_ & le informazioni sui byte e i pacchetti trasmessi. [Altre informazioni](#log-format)

## <a name="pricing"></a>Prezzi

I log di flusso del gruppo di rete vengono addebitati per GB di log raccolti e hanno un livello gratuito di 5 GB al mese per sottoscrizione. Per i prezzi correnti nell'area, vedere la pagina [Network Watcher prezzi.](https://azure.microsoft.com/pricing/details/network-watcher/)

L'archiviazione dei log viene addebitata separatamente, [Archiviazione di Azure prezzi dei BLOB in](https://azure.microsoft.com/pricing/details/storage/blobs/) blocchi per i prezzi pertinenti.