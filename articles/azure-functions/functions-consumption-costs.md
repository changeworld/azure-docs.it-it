---
title: Stima dei costi del piano a consumo in Funzioni di Azure
description: Informazioni su come stimare meglio i costi che possono verificarsi quando si esegue l'app per le funzioni in un piano a consumo in Azure.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 648be6325cce5bad36795b113c8bbccb3e21d37b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774004"
---
# <a name="estimating-consumption-plan-costs"></a>Stima dei costi del piano a consumo

Esistono attualmente tre tipi di piani di hosting per un'app eseguita in Funzioni di Azure, ognuno dei quali ha un proprio modello di determinazione prezzi: 

| Pianificare | Description |
| ---- | ----------- |
| [**Consumo**](consumption-plan.md) | Viene addebitato solo il tempo di esecuzione dell'app per le funzioni. Questo piano include una [pagina dei prezzi delle][concessioni] gratuite per ogni sottoscrizione.|
| [**Premium**](functions-premium-plan.md) | Offre le stesse funzionalità e gli stessi meccanismi di ridimensionamento del piano a consumo, ma con prestazioni migliorate e accesso alla rete virtuale. Il costo è basato sul piano tariffario scelto. Per altre informazioni, vedere [Funzioni di Azure Piano Premium.](functions-premium-plan.md) |
| [**Dedicato (servizio app)**](dedicated-plan.md) <br/>(livello Basic o superiore) | Quando è necessario eseguire in macchine virtuali dedicate o in isolamento, usare immagini personalizzate o si vuole usare la capacità del piano di servizio app in eccesso. Usa la [fatturazione normale del piano di servizio app.](https://azure.microsoft.com/pricing/details/app-service/) Il costo è basato sul piano tariffario scelto.|

È stato scelto il piano che meglio supporta i requisiti di prestazioni e costi delle funzioni. Per altre informazioni, vedere [Ridimensionamento e hosting di Funzioni di Azure](functions-scale.md).

Questo articolo riguarda solo il piano a consumo, poiché questo piano comporta costi variabili. Questo articolo sostituisce l'articolo Domande frequenti sulla fatturazione [dei costi del piano a](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ) consumo.

Durable Functions può essere eseguito anche in un piano a consumo. Per altre informazioni sulle considerazioni sui costi quando si usa Durable Functions, vedere Durable Functions [fatturazione.](./durable/durable-functions-billing.md)

## <a name="consumption-plan-costs"></a>Costi del piano a consumo

Il costo *di esecuzione* di una singola funzione viene misurato in *GB-secondi.* Il costo di esecuzione viene calcolato combinando l'utilizzo della memoria con il tempo di esecuzione. Una funzione che viene eseguita più a lungo costa di più, così come una funzione che utilizza più memoria. 

Si consideri un caso in cui la quantità di memoria usata dalla funzione rimane costante. In questo caso, il calcolo del costo è una semplice moltiplicazione. Ad esempio, si supponga che la funzione ha utilizzato 0,5 GB per 3 secondi. Il costo di esecuzione è `0.5GB * 3s = 1.5 GB-seconds` quindi . 

Poiché l'utilizzo della memoria cambia nel tempo, il calcolo è essenzialmente l'integrale dell'utilizzo della memoria nel tempo.  Il sistema esegue questo calcolo tramite il campionamento dell'utilizzo della memoria del processo (insieme ai processi figlio) a intervalli regolari. Come indicato nella pagina [dei prezzi,]l'utilizzo della memoria viene arrotondato al bucket di 128 MB più vicino. Quando il processo usa 160 MB, vengono addebitati 256 MB. Il calcolo prende in considerazione la concorrenza, ovvero più esecuzioni di funzioni simultanee nello stesso processo.

> [!NOTE]
> Anche se l'utilizzo della CPU non viene considerato direttamente nel costo di esecuzione, può influire sul costo quando influisce sul tempo di esecuzione della funzione.

Per una funzione attivata tramite HTTP, quando si verifica un errore prima dell'inizio dell'esecuzione del codice della funzione non viene addebitato alcun costo per un'esecuzione. Ciò significa che 401 risposte dalla piattaforma a causa della convalida della chiave API o della funzionalità di autenticazione/autorizzazione del servizio app non vengono conteggiati in base al costo di esecuzione. Analogamente, le risposte del codice di stato 5xx non vengono conteggiate quando si verificano nella piattaforma prima di una funzione che elabora la richiesta. Una risposta 5xx generata dalla piattaforma dopo l'avvio dell'esecuzione del codice della funzione viene comunque conteggiata come esecuzione, anche se l'errore non viene generato dal codice della funzione.

## <a name="other-related-costs"></a>Altri costi correlati

Quando si stima il costo complessivo dell'esecuzione delle funzioni in qualsiasi piano, tenere presente che il runtime di Funzioni usa diversi altri servizi di Azure, ognuno fatturato separatamente. Quando si calcolano i prezzi per le app per le funzioni, tutti i trigger e le associazioni integrati con altri servizi di Azure richiedono la creazione e il pagamento di tali servizi aggiuntivi. 

Per le funzioni in esecuzione in un piano a consumo, il costo totale è il costo di esecuzione delle funzioni, oltre al costo della larghezza di banda e dei servizi aggiuntivi. 

Quando si stimano i costi complessivi dell'app per le funzioni e dei servizi correlati, usare il calcolatore [prezzi di Azure.](https://azure.microsoft.com/pricing/calculator/?service=functions) 

| Costo correlato | Descrizione |
| ------------ | ----------- |
| **Account di archiviazione** | Per ogni app per le funzioni è necessario avere un account per utilizzo generico [Archiviazione di Azure,](../storage/common/storage-introduction.md#types-of-storage-accounts)che viene [fatturato separatamente.](https://azure.microsoft.com/pricing/details/storage/) Questo account viene usato internamente dal runtime di Funzioni, ma è anche possibile usarlo per trigger e associazioni di archiviazione. Se non si ha un account di archiviazione, ne viene creato uno automaticamente quando viene creata l'app per le funzioni. Per altre informazioni, vedere [Requisiti dell'account di archiviazione](storage-considerations.md#storage-account-requirements).|
| **Application Insights** | Funzioni si basa [Application Insights](../azure-monitor/app/app-insights-overview.md) per offrire un'esperienza di monitoraggio ad alte prestazioni per le app per le funzioni. Anche se non è obbligatorio, è [consigliabile abilitare Application Insights integrazione .](configure-monitoring.md#enable-application-insights-integration) Ogni mese viene inclusa una concessione gratuita di dati di telemetria. Per altre informazioni, vedere [la pagina Monitoraggio di Azure prezzi.](https://azure.microsoft.com/pricing/details/monitor/) |
| **Larghezza di banda di rete** | Non si paga per il trasferimento dei dati tra i servizi di Azure nella stessa area. Tuttavia, è possibile sostenere costi per i trasferimenti di dati in uscita in un'altra area o all'esterno di Azure. Per altre informazioni, vedere Dettagli [sui prezzi della larghezza di banda.](https://azure.microsoft.com/pricing/details/bandwidth/) |

## <a name="behaviors-affecting-execution-time"></a>Comportamenti che influiscono sul tempo di esecuzione

I comportamenti seguenti delle funzioni possono influire sul tempo di esecuzione:

+ **Trigger e associazioni:** il tempo impiegato per leggere l'input e scrivere l'output nelle associazioni di funzione viene conteggiato come tempo di esecuzione. [](functions-triggers-bindings.md) Ad esempio, quando la funzione usa un'associazione di output per scrivere un messaggio in una coda di archiviazione di Azure, il tempo di esecuzione include il tempo impiegato per scrivere il messaggio nella coda, incluso nel calcolo del costo della funzione. 

+ **Esecuzione asincrona:** il tempo di attesa della funzione per i risultati di una richiesta asincrona ( `await` in C#) viene conteggiato come tempo di esecuzione. Il calcolo di GB al secondo si basa sull'ora di inizio e di fine della funzione e sull'utilizzo della memoria in tale periodo. Ciò che accade in quel periodo di tempo in termini di attività della CPU non viene fattoriato nel calcolo. È possibile ridurre i costi durante le operazioni asincrone [usando](durable/durable-functions-overview.md)Durable Functions . Non viene addebitato il tempo impiegato in awaits nelle funzioni dell'agente di orchestrazione.

## <a name="viewing-cost-related-data"></a>Visualizzazione dei dati correlati ai costi

Nella [fattura](../cost-management-billing/understand/download-azure-invoice.md)è possibile visualizzare i dati relativi ai costi di Esecuzioni **totali -** Funzioni e Tempo di esecuzione **-** Funzioni , insieme ai costi effettivi fatturati. Tuttavia, questi dati della fattura sono un'aggregazione mensile per un periodo di fatturazione precedente. 

### <a name="function-app-level-metrics"></a>Metriche a livello di app per le funzioni

Per comprendere meglio l'impatto sui costi delle funzioni, è possibile usare Monitoraggio di Azure per visualizzare le metriche correlate ai costi attualmente generate dalle app per le funzioni. È possibile usare esplora [Monitoraggio di Azure metriche](../azure-monitor/essentials/metrics-getting-started.md) nel portale di Azure [o] API REST per ottenere questi dati.

#### <a name="monitor-metrics-explorer"></a>Monitorare Esplora metriche

Usare [Monitoraggio di Azure esplora metriche per](../azure-monitor/essentials/metrics-getting-started.md) visualizzare i dati correlati ai costi per le app per le funzioni del piano a consumo in formato grafico. 

1. Nella parte superiore della [portale di Azure] in Servizi di **ricerca, risorse**  e documentazione cercare e selezionare `monitor` **Monitoraggio** in **Servizi**.

1. A sinistra selezionare Metriche **Selezionare**  >  **una risorsa** e quindi usare le impostazioni sotto l'immagine per scegliere l'app per le funzioni.

    ![Selezionare la risorsa dell'app per le funzioni](media/functions-consumption-costing/select-a-resource.png)

      
    |Impostazione  |Valore consigliato  |Descrizione  |
    |---------|---------|---------|
    | Subscription    |  Sottoscrizione in uso  | Sottoscrizione con l'app per le funzioni.  |
    | Resource group     | Gruppo di risorse in uso  | Gruppo di risorse che contiene l'app per le funzioni.   |
    | Tipo di risorsa     |  Servizi app | Le app per le funzioni vengono visualizzate come istanze di Servizi app in Monitoraggio. |
    | Risorsa     |  App per le funzioni  | App per le funzioni da monitorare.        |

1. Selezionare **Applica** per scegliere l'app per le funzioni come risorsa da monitorare.

1. In **Metrica scegliere** **Conteggio esecuzioni funzioni e** Somma **per** **Aggregazione**. In questo modo viene aggiunta al grafico la somma dei conteggi delle esecuzioni durante il periodo scelto.

    ![Definire una metrica dell'app per le funzioni da aggiungere al grafico](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. Selezionare **Aggiungi metrica e** ripetere i passaggi da 2 a 4 per aggiungere le unità di esecuzione della **funzione** al grafico. 

Il grafico risultante contiene i totali per entrambe le metriche di esecuzione nell'intervallo di tempo scelto, che in questo caso è di due ore.

![Grafico dei conteggi di esecuzione delle funzioni e delle unità di esecuzione](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

Poiché il numero di unità di esecuzione è molto maggiore del numero di esecuzioni, il grafico mostra solo le unità di esecuzione.

Questo grafico mostra un totale di 1,11 miliardi utilizzati in un periodo di due `Function Execution Units` ore, misurato in MB-millisecondi. Per eseguire la conversione in GB-secondi, dividere per 1024000. In questo esempio l'app per le funzioni ha utilizzato `1110000000 / 1024000 = 1083.98` GB-secondi. È possibile prendere questo valore e moltiplicarlo per [][]il prezzo corrente del tempo di esecuzione nella pagina dei prezzi di Funzioni, che offre il costo di queste due ore, presupponendo che siano già state usate concessioni gratuite di tempo di esecuzione. 

#### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

[L'interfaccia della riga](/cli/azure/) di comando di Azure include comandi per il recupero delle metriche. È possibile usare l'interfaccia della riga di comando da un ambiente di comando locale o direttamente dal portale [usando Azure Cloud Shell](../cloud-shell/overview.md). Ad esempio, il comando [az monitor metrics list seguente](/cli/azure/monitor/metrics#az_monitor_metrics_list) restituisce i dati orari nello stesso periodo di tempo usato in precedenza.

Assicurarsi di sostituire con `<AZURE_SUBSCRIPTON_ID>` l'ID sottoscrizione di Azure che esegue il comando .

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

Questo comando restituisce un payload JSON simile all'esempio seguente:

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
Questa particolare risposta mostra che da a l'app ha utilizzato `2019-09-11T21:46` `2019-09-11T23:18` 11100000000 MB in millisecondi (1083,98 GB-secondi).

### <a name="function-level-metrics"></a>Metriche a livello di funzione

Le unità di esecuzione delle funzioni sono una combinazione del tempo di esecuzione e dell'utilizzo della memoria, il che lo rende una metrica difficile per comprendere l'utilizzo della memoria. I dati di memoria non sono una metrica attualmente disponibile tramite Monitoraggio di Azure. Tuttavia, se si vuole ottimizzare l'utilizzo della memoria dell'app, è possibile usare i dati del contatore delle prestazioni raccolti da Application Insights.  

Se non è già stato fatto, abilitare [Application Insights nell'app per le funzioni.](configure-monitoring.md#enable-application-insights-integration) Con questa integrazione abilitata, è possibile [eseguire query su questi dati di telemetria nel portale](analyze-telemetry-data.md#query-telemetry-data). 

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sulle app per le funzioni di monitoraggio](functions-monitoring.md)

[pagina dei prezzi]:https://azure.microsoft.com/pricing/details/functions/
[Azure portal]: https://portal.azure.com
