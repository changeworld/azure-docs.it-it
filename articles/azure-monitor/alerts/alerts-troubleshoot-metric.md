---
title: Risoluzione dei problemi relativi agli avvisi delle metriche di Azure
description: Problemi comuni relativi Monitoraggio di Azure avvisi delle metriche e possibili soluzioni.
author: harelbr
ms.author: harelbr
ms.topic: troubleshooting
ms.date: 04/12/2021
ms.openlocfilehash: fc9af94b07add5728201baaa8fa6992728a60a8c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786010"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Risoluzione dei problemi negli avvisi relativi alle metriche di Monitoraggio di Azure 

Questo articolo illustra i problemi comuni relativi Monitoraggio di Azure [avvisi delle metriche](alerts-metric-overview.md) e come risolverli.

Monitoraggio di Azure avvisi notificano in modo proattivo quando vengono trovate condizioni importanti nei dati di monitoraggio. Consentono di identificare e risolvere i problemi prima che si palesino agli utenti. Per altre informazioni sugli avvisi, vedere [Panoramica degli avvisi in Microsoft Azure](./alerts-overview.md).

## <a name="metric-alert-should-have-fired-but-didnt"></a>L'avviso della metrica dovrebbe essere stato attivato ma non è stato attivato 

Se si ritiene che un avviso della metrica avrebbe dovuto essere attivato ma non è stato attivato e non è stato trovato nel portale di Azure, provare a eseguire la procedura seguente:

1. **Configurazione:** esaminare la configurazione della regola di avviso per le metriche per assicurarsi che sia configurata correttamente:
    - Verificare che i valori **di Tipo di aggregazione** **e Granularità aggregazione (periodo)** siano configurati come previsto. Il tipo **di** aggregazione determina la modalità di aggregazione dei valori delle metriche (altre informazioni [qui)](../essentials/metrics-aggregation-explained.md#aggregation-types)e Granularità aggregazione **(periodo)** controlla quanto indietro la valutazione aggrega i valori delle metriche ogni volta che viene eseguita la regola di avviso.
    -  Verificare che il **valore soglia o** **sensibilità** siano configurati come previsto.
    - Per una regola di avviso che usa soglie  dinamiche, controllare se sono configurate impostazioni avanzate, in quanto Numero di violazioni può filtrare gli avvisi e **Ignora** i dati prima può influire sul modo in cui vengono calcolate le soglie.

       > [!NOTE] 
       > Le soglie dinamiche richiedono almeno 3 giorni e 30 campioni di metriche prima di diventare attive.

2. **Attivato ma senza notifica:** esaminare l'elenco [degli avvisi](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) generati per verificare se è possibile individuare l'avviso attivato. Se è possibile visualizzare l'avviso nell'elenco, ma si verifica un problema con alcune azioni o notifiche, vedere altre informazioni [qui.](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)

3. **Già attivo:** verificare se è già presente un avviso attivato per la serie temporale della metrica per cui si prevede di ricevere un avviso. Gli avvisi delle metriche sono avvisi con stato, di conseguenza, una volta generato un avviso in una serie temporale della metrica specifica, gli avvisi successivi in tale serie temporale non verranno attivati fino a quando il problema non viene più rilevato. Questa scelta di progettazione riduce il rumore. L'avviso viene risolto automaticamente quando la condizione di avviso non viene soddisfatta per tre valutazioni consecutive.

4. Dimensioni **usate:** se sono stati selezionati alcuni valori di dimensione per una [metrica,](./alerts-metric-overview.md#using-dimensions)la regola di avviso monitora ogni singola serie temporale delle metriche (come definito dalla combinazione di valori di dimensione) per una violazione di soglia. Per monitorare anche la serie temporale della metrica aggregata (senza alcuna dimensione selezionata), configurare una regola di avviso aggiuntiva nella metrica senza selezionare le dimensioni.

5. **Aggregazione e granularità temporale:** se si visualizza la metrica usando [i](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)grafici delle metriche, assicurarsi che:
    * **L'aggregazione** selezionata nel grafico delle metriche corrisponde al tipo **di aggregazione** nella regola di avviso
    * La **granularità temporale selezionata** corrisponde alla granularità delle aggregazioni **(periodo)** nella regola di avviso e non è impostata su "Automatico".

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>Avviso di metrica generato quando non dovrebbe avere

Se si ritiene che l'avviso della metrica non avrebbe dovuto essere attivato ma è stato fatto, la procedura seguente potrebbe aiutare a risolvere il problema.

1. Esaminare [l'elenco degli avvisi](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) generati per individuare l'avviso attivato e fare clic per visualizzarne i dettagli. Esaminare le informazioni fornite in Perché l'avviso è stato **attivato?**  per visualizzare il grafico delle metriche, il valore **della** metrica e il valore Soglia nel momento in cui è stato attivato l'avviso.

    > [!NOTE] 
    > Se si usa un tipo di condizione Soglie dinamiche e si pensa che le soglie usate non siano corrette, inviare commenti e suggerimenti usando l'icona a forma di cipiglio. Questo feedback avrà un impatto sulla ricerca algoritmica di Machine Learning e aiuterà a migliorare i rilevamenti futuri.

2. Se sono stati selezionati più valori di dimensione per una  metrica, l'avviso verrà attivato quando una delle serie tempore della metrica (come definito dalla combinazione di valori di dimensione) viola la soglia. Per altre informazioni sull'uso delle dimensioni negli avvisi delle metriche, vedere [qui](./alerts-metric-overview.md#using-dimensions).

3. Esaminare la configurazione della regola di avviso per assicurarsi che sia configurata correttamente:
    - Verificare che **il tipo di aggregazione**, **granularità delle aggregazioni (periodo)** e **valore soglia** o riservatezza siano configurati come previsto 
    - Per una regola di avviso che usa soglie  dinamiche, controllare se sono configurate impostazioni avanzate, in quanto Numero di violazioni può filtrare gli avvisi e **Ignora** i dati prima può influire sul modo in cui vengono calcolate le soglie

   > [!NOTE]
   > Le soglie dinamiche richiedono almeno 3 giorni e 30 campioni di metriche prima di diventare attive.

4. Se si visualizza la metrica usando il grafico [Metriche](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics), assicurarsi che:
    - **L'aggregazione** selezionata nel grafico delle metriche corrisponde al tipo **di aggregazione** nella regola di avviso
    - La **granularità temporale selezionata** corrisponde al valore di **Granularità aggregazione (periodo)** nella regola di avviso e non è impostato su "Automatico".

5. Se l'avviso è stato attivato mentre sono già stati generati avvisi che monitorano gli stessi criteri (che non vengono risolti), controllare se la regola di avviso è stata configurata con la proprietà *autoMitigate* impostata su **false** (questa proprietà può essere configurata solo tramite REST/PowerShell/CLI, quindi controllare lo script usato per distribuire la regola di avviso). In tal caso, la regola di avviso non risolve automaticamente gli avvisi generati e non richiede la risoluzione di un avviso attivato prima di generare nuovamente l'avviso.


## <a name="cant-find-the-metric-to-alert-on---virtual-machines-guest-metrics"></a>Non è possibile trovare la metrica per cui creare un avviso: metriche guest delle macchine virtuali

Per avvisare sulle metriche del sistema operativo guest delle macchine virtuali (ad esempio memoria, spazio su disco), assicurarsi di aver installato l'agente necessario per raccogliere questi dati per Monitoraggio di Azure metriche:
- [Per macchine virtuali Windows](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
- [Per macchine virtuali Linux](../essentials/collect-custom-metrics-linux-telegraf.md)

Per altre informazioni sulla raccolta di dati dal sistema operativo guest di una macchina virtuale, vedere [qui](../vm/monitor-vm-azure.md#guest-operating-system).

> [!NOTE] 
> Se le metriche guest sono configurate per l'invio a un'area di lavoro  Log Analytics, le metriche vengono visualizzate nella risorsa dell'area di lavoro Log Analytics e iniziano a visualizzare i dati solo dopo aver creato una regola di avviso che li monitora. A tal proposito, seguire la procedura per [configurare un avviso delle metriche per i log](./alerts-metric-logs.md#configuring-metric-alert-for-logs).

> [!NOTE] 
> Il monitoraggio di una metrica guest per più macchine virtuali con una singola regola di avviso non è attualmente supportato dagli avvisi delle metriche. È possibile ottenere questo risultato con una regola [di avviso del log](./alerts-unified-log.md). A tale scopo, assicurarsi che le metriche guest siano raccolte in un'area di lavoro Log Analytics e creare una regola di avviso del log nell'area di lavoro.

## <a name="cant-find-the-metric-to-alert-on"></a>Non è possibile trovare la metrica per la notifica

Se si sta cercando di generare un avviso per una metrica specifica ma non è possibile visualizzarla durante la creazione di una regola di avviso, verificare quanto segue:
- Se non è possibile vedere le metriche per il tipo di risorsa, [verificare se tale tipo di risorsa è supportato per gli avvisi delle metriche](./alerts-metric-near-real-time.md).
- Se è possibile visualizzare alcune metriche per la risorsa, ma non si riesce a trovare una metrica specifica, [verificare se tale metrica è disponibile](../essentials/metrics-supported.md) e, in tal caso, vedere la descrizione della metrica per verificare se è disponibile solo in versioni o edizioni specifiche della risorsa.
- Se la metrica non è disponibile per la risorsa, potrebbe essere disponibile nei log della risorsa e potrebbe essere monitorata usando gli avvisi del log. Vedere qui per altre informazioni su come [raccogliere e analizzare i log delle risorse da una risorsa di Azure](../essentials/tutorial-resource-logs.md).

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>Non è possibile trovare la dimensione della metrica per cui creare un avviso

Se si sta cercando di avvisare su [valori di dimensione](./alerts-metric-overview.md#using-dimensions)specifici di una metrica , ma non si riesce a trovare questi valori, tenere presente quanto segue:

1. La visualizzazione dei valori delle dimensioni nell'elenco **Valori delle dimensioni** potrebbe richiedere alcuni minuti
2. I valori delle dimensioni visualizzati si basano sui dati delle metriche raccolti l'ultimo giorno
3. Se il valore della dimensione non è ancora stato emesso o non è visualizzato, è possibile usare l'opzione "Aggiungi valore personalizzato" per aggiungere un valore di dimensione personalizzato
4. Se si desidera avvisare tutti i valori possibili di una dimensione (inclusi i valori futuri), scegliere l'opzione "Seleziona tutti i valori correnti e futuri"
5. Le dimensioni delle metriche personalizzate Application Insights risorse sono disattivate per impostazione predefinita. Per attivare la raccolta di dimensioni per queste metriche personalizzate, vedere [qui](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>Regole di avviso delle metriche ancora definite in una risorsa eliminata 

Quando si elimina una risorsa di Azure, le regole di avviso delle metriche associate non vengono eliminate automaticamente. Per eliminare le regole di avviso associate a una risorsa eliminata:

1. Aprire il gruppo di risorse in cui è stata definita la risorsa eliminata
1. Nell'elenco che mostra le risorse selezionare la casella di controllo **Mostra tipi nascosti**
1. Filtrare l'elenco in base al tipo = = **microsoft.insights/metricalerts**
1. Selezionare le regole di avviso pertinenti e selezionare **Elimina**

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>Fare in modo che gli avvisi delle metriche si verifichino ogni volta che viene soddisfatta la condizione

Gli avvisi delle metriche sono con stato per impostazione predefinita e pertanto gli avvisi aggiuntivi non vengono generati se è già presente un avviso attivato in una determinata serie temporale. Se si vuole impostare una regola di avviso della metrica specifica senza stato e ricevere avvisi per ogni valutazione in cui viene soddisfatta la condizione di avviso, creare la regola di avviso a livello di codice , ad esempio [tramite Resource Manager](./alerts-metric-create-templates.md), [PowerShell](/powershell/module/az.monitor/), [REST](/rest/api/monitor/metricalerts/createorupdate), [CLI](/cli/azure/monitor/metrics/alert)e impostare la *proprietà autoMitigate* su 'False'.

> [!NOTE] 
> La creazione di una regola di avviso della metrica senza stato impedisce la risoluzione degli avvisi generati, quindi anche dopo che la condizione non è più soddisfatta, gli avvisi generati rimarranno in uno stato attivato fino al periodo di conservazione di 30 giorni.

## <a name="define-an-alert-rule-on-a-custom-metric-that-isnt-emitted-yet"></a>Definire una regola di avviso per una metrica personalizzata non ancora generata

Quando si crea una regola di avviso della metrica, il nome della metrica viene convalidato in base [all'API Definizioni](/rest/api/monitor/metricdefinitions/list) metrica per assicurarsi che esista. In alcuni casi, si desidera creare una regola di avviso in una metrica personalizzata anche prima che venga generata. Ad esempio, quando si crea (usando un modello Resource Manager) una risorsa Application Insights che genera una metrica personalizzata, insieme a una regola di avviso che monitora tale metrica.

Per evitare che la distribuzione abbia esito negativo quando si tenta di convalidare le definizioni della metrica personalizzata, è possibile usare il parametro *skipMetricValidation* nella sezione dei criteri della regola di avviso, che causerà l'omissione della convalida della metrica. Vedere l'esempio seguente per informazioni su come usare questo parametro in un Resource Manager modello. Per altre informazioni, vedere gli esempi [di modello Resource Manager per la creazione di regole di avviso per le metriche.](./alerts-metric-create-templates.md)

```json
"criteria": {
    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
        "allOf": [
            {
                "name" : "condition1",
                "metricName": "myCustomMetric",
                "metricNamespace": "myCustomMetricNamespace",
                "dimensions":[],
                "operator": "GreaterThan",
                "threshold" : 10,
                "timeAggregation": "Average",
                "skipMetricValidation": true
            }
        ]
    }
```

## <a name="export-the-azure-resource-manager-template-of-a-metric-alert-rule-via-the-azure-portal"></a>Esportare il Azure Resource Manager di una regola di avviso della metrica tramite il portale di Azure

L'esportazione Resource Manager modello di una regola di avviso per le metriche consente di comprendere la sintassi e le proprietà JSON e può essere usata per automatizzare le distribuzioni future.
1. Nella finestra portale di Azure aprire la regola di avviso per visualizzarne i dettagli.
2. Scegliere **Proprietà**.
3. In **Automazione** selezionare **Esporta modello.**

## <a name="metric-alert-rules-quota-too-small"></a>Quota delle regole di avviso della metrica troppo piccola

Il numero consentito di regole di avviso delle metriche per sottoscrizione è soggetto ai [limiti di quota](../service-limits.md).

Se è stato raggiunto il limite di quota, la procedura seguente può aiutare a risolvere il problema:
1. Provare a eliminare o disabilitare le regole di avviso delle metriche che non vengono più usate.

2. Passare a usare le regole di avviso della metrica che monitorano più risorse. Con questa funzionalità, una singola regola di avviso può monitorare più risorse usando una sola regola di avviso conteggiata rispetto alla quota. Per altre informazioni su questa funzionalità e sui tipi di risorse supportati, vedere [qui](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

3. Se è necessario aumentare il limite di quota, aprire una richiesta di supporto e specificare le informazioni seguenti:

    - ID sottoscrizione per cui è necessario aumentare il limite di quota
    - Tipo di risorsa per l'aumento della quota: **avvisi delle metriche** o avvisi delle metriche **(versione classica)**
    - Limite di quota richiesto

## <a name="check-total-number-of-metric-alert-rules"></a>Controllare il numero totale di regole di avviso delle metriche

Per controllare l'utilizzo corrente delle regole di avviso delle metriche, seguire questa procedura.

### <a name="from-the-azure-portal"></a>Dal portale di Azure

1. Aprire la schermata **Avvisi** e fare clic su **Gestisci regole di avviso**
2. Filtrare in base alla sottoscrizione pertinente usando il **controllo a discesa** Sottoscrizione
3. Assicurarsi di NON filtrare in base a un gruppo di risorse, un tipo di risorsa o una risorsa specifici
4. Nel controllo **a discesa Tipo di** segnale selezionare **Metriche**
5. Verificare che il **controllo a discesa** Stato sia impostato su **Abilitato**
6. Il numero totale di regole di avviso della metrica viene visualizzato sopra l'elenco delle regole di avviso

### <a name="from-api"></a>Nell'API

- PowerShell - [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2)
- REST API - [Elenco per sottoscrizione](/rest/api/monitor/metricalerts/listbysubscription)
- Interfaccia della riga di comando di Azure - [az monitor metrics alert list](/cli/azure/monitor/metrics/alert#az_monitor_metrics_alert_list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Gestione delle regole di avviso usando modelli Resource Manager, API REST, PowerShell o interfaccia della riga di comando di Azure

Se si verificano problemi durante la creazione, l'aggiornamento, il recupero o l'eliminazione di avvisi delle metriche usando modelli Resource Manager, API REST, PowerShell o l'interfaccia della riga di comando di Azure, la procedura seguente può aiutare a risolvere il problema.

### <a name="resource-manager-templates"></a>Modelli di Gestione risorse

- Rivedere l'elenco di [Errori di distribuzione di Azure comuni](../../azure-resource-manager/templates/common-deployment-errors.md) e provare a risolvere il problema
- Fare riferimento agli [avvisi delle metriche Azure Resource Manager esempi](./alerts-metric-create-templates.md) di modello per assicurarsi di passare correttamente tutti i parametri

### <a name="rest-api"></a>API REST

Esaminare la [guida all'API REST](/rest/api/monitor/metricalerts/) per verificare che tutti i parametri vengano passati correttamente

### <a name="powershell"></a>PowerShell

Assicurarsi di usare i cmdlet di PowerShell per gli avvisi delle metriche:

- I cmdlet di PowerShell per gli avvisi delle metriche sono disponibili nel [modulo Az.Monitor](/powershell/module/az.monitor/)
- Assicurarsi di usare i cmdlet che terminano con "V2" per gli avvisi delle metriche nuovi (non classici), ad esempio [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2).

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Assicurarsi di usare i comandi dell'interfaccia della riga di comando per gli avvisi delle metriche:

- I comandi dell'interfaccia della riga di comando per gli avvisi delle metriche iniziano con `az monitor metrics alert`. Rivedere le [informazioni di riferimento sull'interfaccia della riga di comando di Azure](/cli/azure/monitor/metrics/alert) per apprenderne la sintassi.
- È possibile visualizzare un [esempio che illustra come usare l'interfaccia della riga di comando per gli avvisi delle metriche](./alerts-metric.md#with-azure-cli)
- Per generare un avviso su una metrica personalizzata, assicurarsi di anteporre al nome della metrica lo spazio dei nomi pertinente: NAMESPACE.METRIC

### <a name="general"></a>Generale

- Se viene visualizzato un `Metric not found` errore:

   - Per una metrica della piattaforma: assicurarsi  di usare il nome della metrica dalla pagina Monitoraggio di Azure [metriche](../essentials/metrics-supported.md)supportate e non il nome visualizzato della **metrica**

   - Per una metrica personalizzata: assicurarsi che la metrica sia già stata generata (non è possibile creare una regola di avviso in una metrica personalizzata che non esiste ancora) e che venga fornito lo spazio dei nomi della metrica personalizzata (vedere un esempio di modello Resource Manager [qui)](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric)

- Se si creano avvisi [delle metriche nei log,](./alerts-metric-logs.md)assicurarsi che siano incluse le dipendenze appropriate. Vedere il [modello di esempio](./alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs).

- Se si crea una regola di avviso che contiene più criteri, tenere presente i vincoli seguenti:

   - È possibile selezionare un solo valore per dimensione all'interno di ogni criterio
   - Non è possibile usare "\*" come valore di dimensione
   - Quando le metriche configurate in criteri diversi supportano la stessa dimensione, un valore di dimensione configurato deve essere impostato in modo esplicito nello stesso modo per tutte queste metriche (vedere un esempio di modello Resource Manager qui [)](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria)


## <a name="no-permissions-to-create-metric-alert-rules"></a>Nessuna autorizzazione per la creazione di regole di avviso delle metriche

Per creare una regola di avviso per le metriche, è necessario disporre delle autorizzazioni seguenti:

- Autorizzazione di lettura per la risorsa di destinazione della regola di avviso
- Autorizzazione di scrittura per il gruppo di risorse in cui viene creata la regola di avviso (se si crea la regola di avviso dal portale di Azure, la regola di avviso viene creata per impostazione predefinita nello stesso gruppo di risorse in cui risiede la risorsa di destinazione)
- Autorizzazione lettura per qualsiasi gruppo di azioni associato alla regola di avviso (se applicabile)


## <a name="naming-restrictions-for-metric-alert-rules"></a>Restrizioni di denominazione per le regole di avviso delle metriche

Considerare le restrizioni seguenti per i nomi delle regole di avviso delle metriche:

- I nomi delle regole di avviso delle metriche non possono essere modificati (rinominati) dopo la creazione
- I nomi delle regole di avviso delle metriche devono essere univoci all'interno di un gruppo di risorse
- I nomi delle regole di avviso delle metriche non possono contenere i caratteri seguenti: * # & + : < > ? @ % { } \ / 
- I nomi delle regole di avviso delle metriche non possono terminare con uno spazio o un punto

> [!NOTE] 
> Se il nome della regola di avviso contiene caratteri non alfabetici o numerici (ad esempio spazi, segni di punteggiatura o simboli), questi caratteri possono essere codificati in URL quando vengono recuperati da determinati client.

## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>Restrizioni quando si usano le dimensioni in una regola di avviso per le metriche con più condizioni

Gli avvisi delle metriche supportano la creazione di avvisi per le metriche multidimensionali, nonché la definizione di più condizioni (fino a 5 condizioni per regola di avviso).

Quando si usano dimensioni in una regola di avviso che contiene più condizioni, considerare i vincoli seguenti:
- È possibile selezionare un solo valore per dimensione all'interno di ogni condizione.
- Non è possibile usare l'opzione "Seleziona tutti i valori correnti e futuri" (selezionare \* ).
- Quando le metriche configurate in condizioni diverse supportano la stessa dimensione, un valore di dimensione configurato deve essere impostato in modo esplicito nello stesso modo per tutte le metriche (nelle condizioni pertinenti).
Ad esempio:
    - Si consideri una regola di avviso della metrica definita in un account di archiviazione e che monitora due condizioni:
        * Totale **transazioni >** 5
        * Valore **medio successE2ELatency** > 250 ms
    - Si desidera aggiornare la prima condizione e monitorare solo le transazioni in cui la dimensione **ApiName** è uguale a *"GetBlob"*
    - Poiché entrambe le metriche **Transactions** e **SuccessE2ELatency** supportano una dimensione **ApiName,** è necessario aggiornare entrambe le condizioni e fare in modo che entrambe le metriche specificheranno la dimensione **ApiName** con un valore *"GetBlob".*

## <a name="setting-the-alert-rules-period-and-frequency"></a>Impostazione del periodo e della frequenza della regola di avviso

È consigliabile scegliere una *granularità aggregazione (periodo)* maggiore della frequenza di valutazione *,* per ridurre la probabilità di mancare la prima valutazione della serie temporale aggiunta nei casi seguenti:
-   Regola di avviso metrica che monitora più dimensioni: quando viene aggiunta una nuova combinazione di valori di dimensione
-   Regola di avviso metrica che monitora più risorse: quando viene aggiunta una nuova risorsa all'ambito
-   Regola di avviso metrica che monitora una metrica che non viene generata in modo continuo (metrica di tipo sparse): quando la metrica viene generata dopo un periodo di più di 24 ore in cui non è stata generata

## <a name="the-dynamic-thresholds-borders-dont-seem-to-fit-the-data"></a>I bordi soglie dinamiche non sembrano adattarsi ai dati

Se il comportamento di una metrica è stato modificato di recente, le modifiche non verranno necessariamente riflesse immediatamente nei bordi soglia dinamica (limiti superiore e inferiore), in quanto vengono calcolate in base ai dati della metrica degli ultimi 10 giorni. Quando si visualizzano i bordi di Soglia dinamica per una determinata metrica, assicurarsi di esaminare la tendenza della metrica nell'ultima settimana e non solo per le ore o i giorni recenti.

## <a name="why-is-weekly-seasonality-not-detected-by-dynamic-thresholds"></a>Perché la stagionalità settimanale non viene rilevata dalle soglie dinamiche?

Per identificare la stagionalità settimanale, il modello Soglie dinamiche richiede almeno tre settimane di dati cronologici. Quando sono disponibili dati cronologici sufficienti, verrà identificata qualsiasi stagionalità settimanale presente nei dati della metrica e il modello verrà modificato di conseguenza. 

## <a name="dynamic-thresholds-shows-a-negative-lower-bound-for-a-metric-even-though-the-metric-always-has-positive-values"></a>Soglie dinamiche mostra un limite inferiore negativo per una metrica anche se la metrica ha sempre valori positivi

Quando una metrica presenta una fluttuazione di grandi dimensioni, le soglie dinamiche compilano un modello più ampio intorno ai valori delle metriche, il che può comportare che il bordo inferiore sia inferiore a zero. In particolare, ciò può verificarsi nei casi seguenti:
1. La sensibilità è impostata su bassa 
2. I valori mediani sono vicini a zero
3. La metrica presenta un comportamento irregolare con varianza elevata (sono presenti picchi o picchi nei dati)

Quando il limite inferiore ha un valore negativo, ciò significa che è plausibile che la metrica raggiunga un valore zero in base al comportamento irregolare della metrica. È possibile scegliere una sensibilità più elevata o una granularità aggregazione maggiore  *(Periodo)* per rendere il modello meno sensibile oppure usare l'opzione Ignora dati prima per escludere una recente irritabilità dai dati cronologici usati per compilare il modello.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni generali sulla risoluzione dei problemi relativi ad avvisi e notifiche, vedere [Risoluzione dei problemi Monitoraggio di Azure avvisi](alerts-troubleshoot.md).
