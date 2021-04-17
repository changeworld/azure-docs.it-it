---
title: Monitoraggio di Azure per soluzioni SAP panoramica e architettura| Microsoft Docs
description: Questo articolo fornisce le risposte alle domande frequenti su Monitoraggio di Azure per le soluzioni SAP
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 45085c910974402a968075a66087a04fb30e8bd9
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576204"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>Monitoraggio di Azure per le soluzioni SAP (anteprima)

## <a name="overview"></a>Panoramica

Monitoraggio di Azure per soluzioni SAP è un prodotto di monitoraggio nativo di Azure per i clienti, che esegue i propri panorama SAP in Azure. Il prodotto funziona sia con SAP in Azure [macchine virtuali](./hana-get-started.md) che con SAP in Azure [istanze Large.](./hana-overview-architecture.md)
Con Monitoraggio di Azure per soluzioni SAP, i clienti possono raccogliere dati di telemetria dall'infrastruttura e dai database di Azure in un'unica posizione centrale e correlare visivamente i dati di telemetria per una risoluzione dei problemi più rapida.

Monitoraggio di Azure per soluzioni SAP viene offerto tramite Azure Marketplace. Offre un'esperienza di configurazione semplice e intuitiva e richiede solo pochi clic per distribuire la risorsa per Monitoraggio di Azure per soluzioni SAP (nota come **risorsa di monitoraggio SAP).**

I clienti possono monitorare diversi componenti di un panorama SAP, ad esempio macchine virtuali di Azure, cluster a disponibilità elevata, database SAP HANA, SAP NetWeaver e così via, aggiungendo il **provider** corrispondente per tale componente.

Infrastruttura supportata:

- Macchina virtuale di Azure
- Istanza large di Azure

Database supportati:
- Database SAP HANA
- Microsoft SQL Server

Monitoraggio di Azure per soluzioni SAP sfrutta la potenza delle funzionalità Monitoraggio di Azure [esistenti,](../../../azure-monitor/overview.md) ad esempio Log Analytics e Cartelle di lavoro, [per](../../../azure-monitor/visualize/workbooks-overview.md) offrire più funzionalità di monitoraggio. I clienti possono creare visualizzazioni personalizzate modificando le cartelle di [](../../../azure-monitor/logs/log-analytics-tutorial.md) lavoro predefinite [](../../../azure-monitor/alerts/tutorial-response.md) fornite da Monitoraggio di Azure per soluzioni SAP, scrivere query [](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) personalizzate e creare avvisi personalizzati usando l'area di lavoro Azure Log Analytics, sfruttare il periodo di conservazione flessibile e connettere i dati di monitoraggio con il sistema di creazione di ticket. [](../../../azure-monitor/visualize/workbooks-overview.md#getting-started)

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Quali dati vengono raccolti Monitoraggio di Azure per le soluzioni SAP?

La raccolta dei Monitoraggio di Azure per soluzioni SAP dipende dai provider configurati dai clienti. Durante l'anteprima pubblica vengono raccolti i dati seguenti.

Telemetria del cluster Pacemaker a disponibilità elevata:
- Stato del nodo, della risorsa e del dispositivo SBD
- Vincoli di posizione di Pacemaker
- Quorum votes and ring status (Stato anello e voti quorum)
- [Altro](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

SAP HANA telemetria:
- Utilizzo di CPU, memoria, disco e rete
- Replica di sistema HANA (HSR)
- Backup HANA
- Stato dell'host HANA
- Ruoli del server di indicizzazione e del server dei nomi

Telemetria di Microsoft SQL Server:
- CPU, memoria, utilizzo del disco
- Nome host, nome istanza SQL, ID sistema SAP
- Richieste batch, compilazioni e aspettativa di vita delle pagine nel tempo
- Prime 10 istruzioni SQL più costose nel tempo
- Prime 12 tabelle più grandi nel sistema SAP
- Problemi registrati nel log SQL Server errori
- Processi di blocco e statistiche di attesa SQL nel tempo

Telemetria del sistema operativo (Linux) 
- Utilizzo della CPU, numero di fork, processi in esecuzione e bloccati. 
- Utilizzo e distribuzione della memoria tra le risorse utilizzate, memorizzate nella cache e memorizzate nel buffer. 
- Utilizzo dello scambio, paging e frequenza di scambio. 
- Utilizzo dei file system, numero di byte letti e scritti per dispositivo a blocchi. 
- Latenza di lettura/scrittura per dispositivo a blocchi. 
- Conteggio I/O in corso, byte di lettura/scrittura della memoria persistente. 
- Pacchetti di rete in ingresso/in uscita, byte di rete in ingresso/in uscita 

Dati di telemetria di SAP NetWeaver:

- Disponibilità del sistema SAP e del server applicazioni, inclusa la disponibilità del processo di istanza di Dispatcher, ICM, Gateway, Server messaggi, Server di accodamento, Watchdog IGS
- Statistiche e tendenze di utilizzo del processo di lavoro
- Statistiche e tendenze dei blocchi di accodamento
- Statistiche e tendenze sull'utilizzo della coda

## <a name="data-sharing-with-microsoft"></a>Condivisione dei dati con Microsoft

Monitoraggio di Azure per soluzioni SAP raccoglie i metadati di sistema per offrire un supporto migliorato per i SAP in Azure clienti. Non vengono raccolte informazioni personali/EUII.
I clienti possono abilitare la condivisione dei dati con Microsoft al momento della creazione Monitoraggio di Azure per soluzioni SAP risorsa scegliendo *Condividi* dall'elenco a discesa.
È altamente consigliabile che i clienti abilitino la condivisione dei dati, in quanto forniscono ai team di supporto e progettazione Microsoft altre informazioni sull'ambiente dei clienti e forniscono un supporto migliorato ai clienti SAP in Azure cruciali.

## <a name="architecture-overview"></a>Panoramica dell'architettura

A livello di alto livello, il diagramma seguente illustra come Monitoraggio di Azure per soluzioni SAP dati di telemetria dal database SAP HANA dati. L'architettura è indipendente dal fatto che SAP HANA sia distribuito in macchine virtuali di Azure o istanze Large di Azure.

![Monitoraggio di Azure per l'architettura delle soluzioni SAP](https://user-images.githubusercontent.com/75772258/115046700-62ff3280-9ef5-11eb-8d0d-cfcda526aeeb.png)

I componenti chiave dell'architettura sono:
- portale di Azure: punto di partenza per i clienti. I clienti possono passare al marketplace all'interno portale di Azure e individuare Monitoraggio di Azure per soluzioni SAP
- Monitoraggio di Azure per soluzioni SAP risorsa: una posizione di destinazione in cui i clienti possono visualizzare i dati di telemetria di monitoraggio
- Gruppo di risorse gestite: distribuito automaticamente come parte della Monitoraggio di Azure per soluzioni SAP della risorsa gestita. Le risorse distribuite all'interno del gruppo di risorse gestite consentono la raccolta di dati di telemetria. Le risorse principali distribuite e il loro scopo sono:
   - Macchina virtuale di Azure: nota anche come macchina *virtuale dell'agente di raccolta.* Si tratta di una Standard_B2ms virtuale. Lo scopo principale di questa macchina virtuale è ospitare il *payload di monitoraggio*. Il payload di monitoraggio si riferisce alla logica di raccolta dei dati di telemetria dai sistemi di origine e trasferimento dei dati raccolti al framework di monitoraggio. Nel diagramma precedente il payload di monitoraggio contiene la logica per connettersi SAP HANA database tramite la porta SQL.
   - [Azure Key Vault:](../../../key-vault/general/basic-concepts.md)questa risorsa viene distribuita per contenere in modo sicuro SAP HANA credenziali del database e per archiviare le informazioni sui [provider](./azure-monitor-providers.md).
   - Area di lavoro Log Analytics: la destinazione in cui risiedono i dati di telemetria.
      - La visualizzazione si basa sui dati di telemetria in Log Analytics usando [Cartelle di lavoro di Azure.](../../../azure-monitor/visualize/workbooks-overview.md) I clienti possono personalizzare la visualizzazione. I clienti possono anche aggiungere le cartelle di lavoro o una visualizzazione specifica all'interno di Cartelle di lavoro al dashboard di Azure per la funzionalità di aggiornamento automatico con granularità minima di 30 minuti.
      - I clienti possono usare l'area di lavoro esistente all'interno della stessa sottoscrizione della risorsa di monitoraggio SAP scegliendo questa opzione al momento della distribuzione.
      - I clienti possono usare il linguaggio di query Kusto (KQL) per eseguire [query](../../../azure-monitor/logs/log-query-overview.md) sulle tabelle non elaborati all'interno dell'area di lavoro Log Analytics. Esaminare i *log personalizzati*.

> [!Note]
> I clienti sono responsabili dell'applicazione di patch e della gestione della macchina virtuale, distribuita nel gruppo di risorse gestite.

> [!Tip]
> I clienti possono scegliere di usare un'area di lavoro Log Analytics esistente per la raccolta di dati di telemetria, se viene distribuita nella stessa sottoscrizione di Azure della risorsa per Monitoraggio di Azure per soluzioni SAP.

### <a name="architecture-highlights"></a>Evidenziazioni dell'architettura

Di seguito sono riportati i principali elementi principali dell'architettura:
 - Istanze **multiple:** i clienti possono creare il monitoraggio per più istanze di un determinato tipo di componente (ad esempio, DATABASE HANA, cluster a disponibilità disponibilità, Microsoft SQL Server, SAP NetWeaver) in più SID SAP all'interno di una rete virtuale con una singola risorsa di Monitoraggio di Azure per soluzioni SAP.
 - **Multi-provider:** il diagramma dell'architettura precedente mostra il provider SAP HANA come esempio. Analogamente, i clienti possono configurare più provider per i componenti corrispondenti (ad esempio, HANA DB, cluster a livello di riga, Microsoft SQL Server, SAP NetWeaver) per raccogliere dati da tali componenti.
 - **Open source:** il codice sorgente Monitoraggio di Azure per soluzioni SAP è disponibile in [GitHub.](https://github.com/Azure/AzureMonitorForSAPSolutions) I clienti possono fare riferimento al codice del provider e ottenere altre informazioni sul prodotto, contribuire o condividere commenti e suggerimenti.
 - **Extensible Query Framework:** le query SQL per raccogliere dati di telemetria vengono scritte in [JSON.](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json) È possibile aggiungere facilmente altre query SQL per raccogliere più dati di telemetria. I clienti possono richiedere l'aggiunta di dati di telemetria specifici Monitoraggio di Azure per soluzioni SAP, lasciando commenti e suggerimenti tramite collegamento alla fine di questo documento o contattando il team dell'account.

## <a name="pricing"></a>Prezzi
Monitoraggio di Azure per soluzioni SAP è un prodotto gratuito (nessuna tariffa di licenza). I clienti sono responsabili del pagamento del costo per i componenti sottostanti nel gruppo di risorse gestite.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui provider e creare la prima risorsa Monitoraggio di Azure per soluzioni SAP locale.
 - Altre informazioni sui [provider](./azure-monitor-providers.md)
 - [Distribuire Monitoraggio di Azure per soluzioni SAP con Azure PowerShell](azure-monitor-sap-quickstart-powershell.md)
 - Sono state poste domande Monitoraggio di Azure per soluzioni SAP? Vedere la [sezione Domande](./azure-monitor-faq.md) frequenti
