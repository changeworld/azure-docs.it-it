---
title: Integrare Esplora dati di Azure per la conservazione dei log a lungo termine | Microsoft Docs
description: Inviare Azure Sentinel log a Esplora dati di Azure conservazione a lungo termine per ridurre i costi di archiviazione dei dati.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/21/2021
ms.author: bagol
ms.openlocfilehash: c7d9ef58d4bb15afe59c6734ce887c2cc3c07c26
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836227"
---
# <a name="integrate-azure-data-explorer-for-long-term-log-retention"></a>Integrare Esplora dati di Azure per la conservazione dei log a lungo termine

<!--Info not included:>

Script - can't xref out to a private github repo from docs
-->

Per impostazione predefinita, i log inseriti Azure Sentinel vengono archiviati in log Monitoraggio di Azure Log Analytics. Questo articolo illustra come ridurre i costi di conservazione Azure Sentinel inviandoli a Esplora dati di Azure (ADX) per la conservazione a lungo termine.

L'archiviazione dei log in ADX riduce i costi mantenendo la possibilità di eseguire query sui dati ed è particolarmente utile man mano che i dati aumentano. Ad esempio, mentre i dati di sicurezza possono perdere valore nel tempo, potrebbe essere necessario conservare i log per i requisiti normativi o eseguire analisi periodiche sui dati meno recenti.

## <a name="about-azure-data-explorer"></a>Informazioni su Esplora dati di Azure

ADX è una piattaforma di analisi dei Big Data altamente ottimizzata per l'analisi dei log e dei dati. Poiché ADX usa Kusto Query Language (KQL) come linguaggio di query, è una valida alternativa per l'archiviazione Azure Sentinel dati. L'uso di ADX per l'archiviazione dei dati consente di eseguire query multipiattaforma e visualizzare i dati sia in ADX che Azure Sentinel.

Per altre informazioni, vedere la documentazione di ADX [e](/azure/data-explorer/) il [blog](https://azure.microsoft.com/en-us/blog/tag/azure-data-explorer/).

### <a name="when-to-integrate-with-adx"></a>Quando eseguire l'integrazione con ADX

Azure Sentinel funzionalità SIEM e SOAR complete, distribuzione e configurazione rapide, nonché funzionalità di sicurezza avanzate e integrate per i team SOC. Tuttavia, il valore dell'archiviazione dei dati di sicurezza in Azure Sentinel può scendere dopo alcuni mesi, quando gli utenti soc non devono accedervi con la frequenza con cui accedono ai dati più nuovi.

Se è necessario accedere a tabelle specifiche solo occasionalmente, ad esempio per analisi o controlli periodici, è possibile considerare che la conservazione dei dati in Azure Sentinel non è più conveniente. A questo punto, è consigliabile archiviare i dati in ADX, con un costo inferiore, ma che consente comunque di esplorare usando le stesse query KQL eseguite in Azure Sentinel.

È possibile accedere ai dati in ADX direttamente da Azure Sentinel tramite la funzionalità [proxy ADX](//azure/azure-monitor/logs/azure-monitor-data-explorer-proxy)di Log Analytics . A tale scopo, usare query tra cluster nella ricerca log o nelle cartelle di lavoro. 

> [!IMPORTANT]
> Le funzionalità SIEM di base, incluse le regole analitiche, UEBA e il grafico di indagine, non supportano i dati archiviati in ADX.
>

> [!NOTE]
> L'integrazione con ADX consente anche di avere controllo e granularità nei dati. Per altre informazioni, vedere Considerazioni [sulla progettazione.](#design-considerations)
> 
## <a name="send-data-directly-to-azure-sentinel-and-adx-in-parallel"></a>Inviare dati direttamente a Azure Sentinel e ADX in parallelo

È possibile conservare tutti i dati con valore di sicurezza *in* Azure Sentinel da usare in rilevamenti, indagini sugli eventi imprevisti, ricerca di minacce, UEBA e così via. Mantenere questi dati in Azure Sentinel vantaggi principalmente per gli utenti del Security Operations Center (SOC), in cui in genere sono sufficienti 3-12 mesi di archiviazione.

È anche possibile configurare tutti i dati, indipendentemente dal valore di *sicurezza,* da inviare contemporaneamente ad ADX, in cui possono essere archiviati più a lungo. Anche se l'invio di dati a Azure Sentinel e ADX contemporaneamente comporta una certa duplicazione, il risparmio sui costi può essere significativo in quanto si riducono i costi di conservazione in Azure Sentinel.

> [!TIP]
> Questa opzione consente anche di correlare i dati distribuiti tra archivi dati, ad esempio per arricchire i dati di sicurezza archiviati in Azure Sentinel con dati operativi o a lungo termine archiviati in ADX. Per altre informazioni, vedere [Query tra risorse Esplora dati di Azure usando Monitoraggio di Azure](/azure/azure-monitor/logs/azure-monitor-data-explorer-proxy).
>

L'immagine seguente illustra come è possibile conservare tutti i dati in ADX, inviando al contempo solo i dati di sicurezza Azure Sentinel per l'uso giornaliero.

:::image type="content" source="media/store-logs-in-adx/store-data-in-sentinel-and-adx-in-parallel.png" alt-text="Archiviare i dati in ADX e Azure Sentinel in parallelo.":::

Per altre informazioni sull'implementazione di questa opzione di architettura, [vedere Esplora dati di Azure monitoraggio.](/azure/architecture/solution-ideas/articles/monitor-azure-data-explorer)

## <a name="export-data-from-log-analytics-into-adx"></a>Esportare dati da Log Analytics in ADX

Anziché inviare i dati direttamente ad ADX, è possibile scegliere di esportare i dati da Log Analytics in ADX tramite un hub eventi ADX o Azure Data Factory.

### <a name="data-export-architecture"></a>Architettura di esportazione dei dati

L'immagine seguente mostra un flusso di esempio di dati esportati tramite la pipeline Monitoraggio di Azure di inserimento dati. I dati vengono indirizzati a Log Analytics per impostazione predefinita, ma è anche possibile configurarlo per l'esportazione in un account Archiviazione di Azure o nell'hub eventi.

:::image type="content" source="media/store-logs-in-adx/export-data-from-azure-monitor.png" alt-text="Esportare dati da Monitoraggio di Azure- architettura.":::

Quando si configurano le regole di esportazione dati, selezionare i tipi di log da esportare. Dopo la configurazione, i nuovi dati in arrivo all'endpoint di inserimento di Log Analytics e destinati all'area di lavoro per le tabelle selezionate vengono esportati nell'account di archiviazione o nell'hub eventi.

Quando si configurano i dati per l'esportazione, tenere presente quanto segue:

|Considerazioni  | Dettagli |
|---------|---------|
|**Ambito dei dati esportati**     |  Dopo aver configurato l'esportazione per una tabella specifica, vengono esportati tutti i dati inviati a tale tabella, senza eccezioni. L'esportazione di un subset filtrato dei dati o la limitazione dell'esportazione a eventi specifici non è supportata.       |
|**Requisiti di posizione**     |   Sia l Monitoraggio di Azure/Azure Sentinel che la posizione di destinazione (un account Archiviazione di Azure o un hub eventi) devono trovarsi nella stessa area geografica.      |
|**Tabelle supportate**     | Non tutte le tabelle sono supportate per l'esportazione, ad esempio le tabelle di log personalizzate, che non sono supportate. <br><br>Per altre informazioni, vedere Esportazione dei [dati dell'area](/azure/azure-monitor/logs/logs-data-export) di lavoro Log Analytics Monitoraggio di Azure e [l'elenco delle tabelle supportate.](/azure/azure-monitor/logs/logs-data-export#supported-tables)         |
|     |         |

### <a name="data-export-methods-and-procedures"></a>Metodi e procedure per l'esportazione dei dati

Usare una delle procedure seguenti per esportare i dati da Azure Sentinel in ADX:

- **Tramite un hub eventi ADX.** Esportare i dati da Log Analytics in un hub eventi, in cui è possibile inserire i dati in ADX. Questo metodo archivia alcuni dati (i primi X mesi) sia in Azure Sentinel che in ADX.

- **Tramite Archiviazione di Azure e Azure Data Factory**. Esportare i dati da Log Analytics in Archiviazione BLOB di Azure, quindi Azure Data Factory viene usato per eseguire un processo di copia periodico per esportare ulteriormente i dati in ADX. Questo metodo consente di copiare dati da Azure Data Factory solo quando si avvicina il limite di conservazione in Azure Sentinel/Log Analytics, evitando la duplicazione.

### <a name="adx-event-hub"></a>[Hub eventi ADX](#tab/adx-event-hub)

Questa sezione descrive come esportare Azure Sentinel dati da Log Analytics in un hub eventi, in cui è possibile inserire i dati in ADX. Analogamente all'invio diretto di dati a Azure Sentinel e [ADX in parallelo,](#send-data-directly-to-azure-sentinel-and-adx-in-parallel)questo metodo include alcune duplicazioni dei dati quando i dati vengono trasmessi in ADX non appena arrivano in Log Analytics.

L'immagine seguente mostra un flusso di esempio di dati esportati in un hub eventi, da dove vengono inseriti in ADX.

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-event-hub.png" alt-text="Esportare i dati in ADX tramite un hub eventi ADX.":::

L'architettura illustrata nell'immagine precedente offre l'esperienza SIEM completa di Azure Sentinel, tra cui gestione degli eventi imprevisti, indagini visive, ricerca di minacce, visualizzazioni avanzate, UEBA e altro ancora, per i dati a cui è necessario accedere di frequente, ogni *X* mesi. Allo stesso tempo, questa architettura consente anche di eseguire query sui dati a lungo termine accedendo ai dati direttamente in ADX o tramite Azure Sentinel grazie alla funzionalità proxy ADX. Le query nell'archiviazione dei dati a lungo termine in ADX possono essere portate senza alcuna modifica da Azure Sentinel ad ADX.

> [!TIP]
> Per altre informazioni su questa procedura, vedere [Esercitazione: Inserire ed](/azure/data-explorer/ingest-data-no-code)eseguire query sui dati di monitoraggio in Esplora dati di Azure .
>

**Per esportare i dati in ADX tramite un hub eventi:**

1. **Configurare l'esportazione dei dati di Log Analytics in un hub eventi.** Per altre informazioni, vedere Esportazione dei [dati dell'area di](/azure/azure-monitor/platform/logs-data-export)lavoro Log Analytics in Monitoraggio di Azure .

1. **Creare un cluster ADX e un database**. Per altre informazioni, vedere:

    - [Creare un cluster Esplora dati di Azure database](/azure/data-explorer/create-cluster-database-portal)
    - [Selezionare lo SKU di calcolo corretto per il cluster Esplora dati di Azure dati](/azure/data-explorer/manage-cluster-choose-sku)

1. **Creare tabelle di destinazione**. I dati non elaborati vengono prima inseriti in una tabella intermedia, in cui i dati non elaborati vengono archiviati, manipolati ed espansi.

    Un criterio di aggiornamento, simile a una funzione applicata a tutti i nuovi dati, viene usato per inserire i dati espansi nella tabella finale, che ha lo stesso schema della tabella originale in Azure Sentinel.

    Impostare la conservazione nella tabella non elaborata su **0** giorni. I dati vengono archiviati solo nella tabella formattata correttamente ed eliminati nella tabella non elaborata non appena vengono trasformati.

    Per altre informazioni, vedere Inserire ed eseguire query sui dati [di monitoraggio in Esplora dati di Azure](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. <a name="mapping"></a>**Creare il mapping di tabella**. Eseguire il mapping delle tabelle JSON per definire il modo in cui i record atterrano nella tabella degli eventi non elaborati quando arrivano da un hub eventi. Per altre informazioni, vedere [Creare i criteri di aggiornamento per la metrica e i dati di log.](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)

1. **Creare un criterio di aggiornamento e collegarlo alla tabella dei record non elaborati**. In questo passaggio creare una funzione, denominata criteri di aggiornamento, e collegarla alla tabella di destinazione in modo che i dati vengono trasformati in fase di inserimento.

    > [!NOTE]
    > Questo passaggio è necessario solo quando si vogliono avere tabelle dati in ADX con lo stesso schema e lo stesso formato di Azure Sentinel.
    >

    Per altre informazioni, vedere [Connettere un hub eventi Esplora dati di Azure](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs).

1. **Creare una connessione dati tra l'hub eventi e la tabella dei dati non elaborati in ADX.** Configurare ADX con informazioni dettagliate su come esportare i dati nell'hub eventi.

    Usare le istruzioni nella documentazione [Esplora dati di Azure e](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs) specificare i dettagli seguenti:

    - **Destinazione**. Specificare la tabella specifica con i dati non elaborati.
    - **Formattare**. Specificare `.json` come formato di tabella.
    - **Mapping da applicare.** Specificare la tabella di mapping creata nel [passaggio 4](#mapping) precedente.


1. **Modificare la conservazione per la tabella di destinazione**. I [criteri Esplora dati di Azure di conservazione predefiniti](/azure/data-explorer/kusto/management/retentionpolicy) potrebbero essere molto più lunghi del necessario.

    Usare il comando seguente per aggiornare i criteri di conservazione a un anno:

    ```kusto
    .alter-merge table <tableName> policy retention softdelete = 365d recoverability = disabled
    ```
### <a name="azure-storage--azure-data-factory"></a>[Archiviazione di Azure/Azure Data Factory](#tab/azure-storage-azure-data-factory)

Questa sezione descrive come esportare Azure Sentinel dati da Log Analytics in Archiviazione di Azure, in cui Azure Data Factory eseguire un processo normale per esportare i dati in ADX.

L Archiviazione di Azure e Azure Data Factory consente di copiare dati da Archiviazione di Azure solo quando è vicino al limite di conservazione in Azure Sentinel/Log Analytics. Non è presente alcuna duplicazione  dei dati e ADX viene usato solo per accedere ai dati precedenti al limite di conservazione in Azure Sentinel.

> [!TIP]
> Anche se l'architettura per Archiviazione di Azure e Azure Data Factory per i dati legacy è più complessa, questo metodo può offrire un risparmio complessivo sui costi maggiore.
>
L'immagine seguente mostra un flusso di esempio di dati esportati in un Archiviazione di Azure, da cui Azure Data Factory esegue un processo normale per esportarlo ulteriormente in ADX.

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-azure-storage-azure-data-factory.png" alt-text="Esportare i dati in ADX tramite Archiviazione di Azure e Azure Data Factory.":::

**Per esportare i dati in ADX tramite Archiviazione di Azure e Azure Data Factory**:

1. **Configurare l'esportazione dei dati di Log Analytics in un hub eventi.** Per altre informazioni, vedere Esportazione dei [dati dell'area di](/azure/azure-monitor/logs/logs-data-export?tabs=portal#enable-data-export)lavoro Log Analytics in Monitoraggio di Azure .

1. **Creare un cluster ADX e un database**. Per altre informazioni, vedere:

    - [Creare un cluster Esplora dati di Azure database](/azure/data-explorer/create-cluster-database-portal)
    - [Selezionare lo SKU di calcolo corretto per il cluster Esplora dati di Azure dati](/azure/data-explorer/manage-cluster-choose-sku)

1. **Creare tabelle di destinazione**. I dati non elaborati vengono prima inseriti in una tabella intermedia, in cui i dati non elaborati vengono archiviati, manipolati ed espansi.

    Un criterio di aggiornamento, simile a una funzione applicata a tutti i nuovi dati, viene usato per inserire i dati espansi nella tabella finale, che ha lo stesso schema della tabella originale in Azure Sentinel.

    Impostare la conservazione nella tabella non elaborata su **0** giorni. I dati vengono archiviati solo nella tabella formattata correttamente ed eliminati nella tabella non elaborata non appena vengono trasformati.

    Per altre informazioni, vedere Inserire ed eseguire query sui dati [di monitoraggio in Esplora dati di Azure](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. <a name="mapping"></a>**Creare il mapping di tabella**. Eseguire il mapping delle tabelle JSON per definire il modo in cui i record atterrano nella tabella degli eventi non elaborati quando arrivano da un hub eventi. Per altre informazioni, vedere [Creare i criteri di aggiornamento per la metrica e i dati di log.](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)

1. **Creare un criterio di aggiornamento e collegarlo alla tabella dei record non elaborati**. In questo passaggio creare una funzione, denominata criteri di aggiornamento, e collegarla alla tabella di destinazione in modo che i dati vengono trasformati in fase di inserimento.

    > [!NOTE]
    > Questo passaggio è necessario solo quando si vogliono avere tabelle dati in ADX con lo stesso schema e lo stesso formato di Azure Sentinel.
    >

    Per altre informazioni, vedere [Connettere un hub eventi Esplora dati di Azure](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs).

1. **Creare una connessione dati tra l'hub eventi e la tabella dei dati non elaborati in ADX.** Configurare ADX con informazioni dettagliate su come esportare i dati nell'hub eventi.

    Usare le istruzioni nella documentazione [Esplora dati di Azure e](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs) specificare i dettagli seguenti:

    - **Destinazione**. Specificare la tabella specifica con i dati non elaborati.
    - **Formattare**. Specificare `.json` come formato di tabella.
    - **Mapping da applicare.** Specificare la tabella di mapping creata nel [passaggio 4](#mapping) precedente.

1. **Configurare la pipeline Azure Data Factory:**

    - Creare servizi collegati per Archiviazione di Azure e Esplora dati di Azure. Per altre informazioni, vedere:

        - [Copiare e trasformare i dati in archiviazione BLOB di Azure con Azure Data Factory](/azure/data-factory/connector-azure-blob-storage)
        - [Copiare dati in o da Esplora dati di Azure usando Azure Data Factory](/azure/data-factory/connector-azure-data-explorer).

    - Creare un set di dati da Archiviazione di Azure. Per altre informazioni, vedere [Set di dati in Azure Data Factory](/azure/data-factory/concepts-datasets-linked-services).

    - Creare una pipeline di dati con un'operazione di copia, in base **alle proprietà LastModifiedDate.**

        Per altre informazioni, vedere [Copiare file nuovi e modificati da **LastModifiedDate** con Azure Data Factory](/azure/data-factory/solution-template-copy-new-files-lastmodifieddate).

---

## <a name="design-considerations"></a>Considerazioni relative alla progettazione

Quando si archiviano Azure Sentinel dati in ADX, considerare gli elementi seguenti:

|Considerazioni  |Descrizione  |
|---------|---------|
|**Dimensioni del cluster e SKU**     | Pianificare con attenzione il numero di nodi e lo SKU della macchina virtuale nel cluster. Questi fattori determineranno la quantità di potenza di elaborazione e le dimensioni della cache ad accesso caldo (SSD e memoria). Maggiore è la dimensione della cache, maggiore sarà il numero di dati su cui sarà possibile eseguire query a prestazioni più elevate. <br><br>Si consiglia di visitare il calcolatore [del ridimensionamento di ADX,](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html)in cui è possibile usare configurazioni diverse e visualizzare il costo risultante. <br><br>ADX offre anche una funzionalità di scalabilità automatica che prende decisioni intelligenti per aggiungere/rimuovere nodi in base alle esigenze in base al carico del cluster. Per altre informazioni, vedere Gestire la [scalabilità orizzontale del cluster (aumento del](/azure/data-explorer/manage-cluster-horizontal-scaling)numero di Esplora dati di Azure per soddisfare la domanda in continua evoluzione.      |
|**Cache ad caldo/a freddo**     | ADX offre il controllo sulle tabelle di dati presenti nella cache ad accesso rapido e restituisce i risultati più velocemente. Se nel cluster ADX sono presenti grandi quantità di dati, è possibile suddividere le tabelle in base al mese, in modo da ottenere una maggiore granularità per i dati presenti nella cache ad accesso elevato. <br><br>Per altre informazioni, vedere [Cache policy (hot and cold cache)](/azure/data-explorer/kusto/management/cachepolicy)       |
|**Conservazione**     |   In ADX è possibile configurare il momento in cui i dati vengono rimossi da un database o da una singola tabella, che è anche una parte importante della limitazione dei costi di archiviazione. <br><br> Per altre informazioni, vedere [Criteri di conservazione.](/azure/data-explorer/kusto/management/retentionpolicy)       |
|**Sicurezza**     |  Diverse impostazioni DIX consentono di proteggere i dati, ad esempio la gestione delle identità, la crittografia e così via. In particolare per il controllo degli accessi in base al ruolo, è possibile configurare ADX per limitare l'accesso a database, tabelle o persino righe all'interno di una tabella. Per altre informazioni, vedere [Sicurezza a livello Esplora dati di Azure](/azure/data-explorer/security) e Sicurezza a livello di [riga.](/azure/data-explorer/kusto/management/rowlevelsecuritypolicy)|
|**Condivisione dei dati**     |   ADX consente di rendere disponibili parti di dati ad altre parti, ad esempio partner o fornitori, e persino di acquistare dati da altre parti. Per altre informazioni, vedere [Usare Condivisione dati di Azure per condividere dati con Esplora dati di Azure](/azure/data-explorer/data-share).      |
| **Altri componenti di costo** | Considerare gli altri componenti di costo per i metodi seguenti: <br><br>**Esportazione di dati tramite un hub eventi ADX:** <br>- Costi di esportazione dei dati di Log Analytics, addebitati per i BLOB esportati. <br>- Costi dell'hub eventi, addebitati in base all'unità di velocità effettiva.  <br><br>**Esportare dati tramite Archiviazione di Azure e Azure Data Factory**: <br>- Esportazione dei dati di Log Analytics, addebitata per i BLOB esportati. <br>- Archiviazione di Azure, addebitato dai BLOB archiviati. <br>- Azure Data Factory, addebitato per ogni copia delle attività eseguite.
|     |         |

## <a name="next-steps"></a>Passaggi successivi

Indipendentemente dalla posizione in cui vengono archiviati i dati, continuare a ricercare e analizzare usando Azure Sentinel.

Per altre informazioni, vedere:

- [Esercitazione: Analizzare gli eventi imprevisti con Azure Sentinel](tutorial-investigate-cases.md)
- [Cercare minacce con Azure Sentinel](hunting.md)
