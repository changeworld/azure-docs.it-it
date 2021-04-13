---
title: Procedure consigliate per l'uso dell'API MultiVariante del rilevatore di anomalie
titleSuffix: Azure Cognitive Services
description: Procedure consigliate per l'uso dell'API MultiVariante del rilevatore di anomalie per applicare il rilevamento delle anomalie ai dati delle serie temporali.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: rilevamento anomalie, Machine Learning, algoritmi
ms.openlocfilehash: 7de25b4a099c706c05b32b52492096923033f822
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315614"
---
# <a name="multivariate-time-series-anomaly-detector-best-practices"></a>Procedure consigliate per il rilevamento di anomalie in serie temporali multivariate

In questo articolo vengono fornite indicazioni sulle procedure consigliate da seguire quando si usano le API del rilevamento di anomalie multivariate.

## <a name="how-to-prepare-data-for-training"></a>Come preparare i dati per il training

Per usare le API multivariate del rilevatore di anomalie, è necessario eseguire il training del modello prima di usare il rilevamento. I dati utilizzati per il training sono un batch di serie temporali, ogni serie temporale deve essere in formato CSV con due colonne, timestamp e valore. Tutte le serie temporali devono essere compresse in un unico file zip e caricate nell'archiviazione BLOB di Azure. Per impostazione predefinita, il nome del file verrà utilizzato per rappresentare la variabile per la serie temporale. In alternativa, è possibile includere nel file zip un meta.jsaggiuntivo sul file se si desidera che il nome della variabile sia diverso dal nome del file con estensione zip. Una volta generato un [URL di firma di accesso condiviso (SAS) BLOB](../../../storage/common/storage-sas-overview.md), è possibile usarlo per il training.

## <a name="data-quality-and-quantity"></a>Qualità e quantità dei dati

L'API MultiVariante del rilevatore di anomalie utilizza reti neurali profonde all'avanguardia per apprendere i modelli normali dai dati cronologici e stimare se i valori futuri sono anomalie. La qualità e la quantità di dati di training sono importanti per eseguire il training di un modello ottimale. Poiché il modello apprende i normali modelli dai dati cronologici, i dati di training devono rappresentare lo stato normale generale del sistema. È difficile per il modello apprendere questi tipi di modelli se i dati di training sono pieni di anomalie. Inoltre, il modello include milioni di parametri ed è necessario un numero minimo di punti dati per apprendere un set ottimale di parametri. La regola generale è che è necessario fornire almeno 15.000 punti dati per variabile per eseguire correttamente il training del modello. Maggiore è il numero di dati, maggiore sarà il modello.

È comune che molte serie temporali abbiano valori mancanti, che potrebbero influire sulle prestazioni dei modelli sottoposti a training. Il rapporto mancante di ogni serie temporale deve essere controllato con un valore ragionevole. Una serie temporale con valori del 90% mancanti fornisce poche informazioni sui modelli normali del sistema. Ancora peggio, il modello può considerare i valori riempiti come modelli normali, che sono in genere segmenti dritti o valori costanti. Quando nuovi flussi di dati sono in, i dati potrebbero essere rilevati come anomalie.

Una soglia di valore max Missing consigliata è il 20%, ma in alcune circostanze potrebbe essere accettabile una soglia maggiore. Ad esempio, se si dispone di una serie temporale con granularità di un minuto e un'altra serie temporale con granularità oraria.  Ogni ora sono presenti 60 punti dati al minuto di dati e 1 punto dati per i dati orari, il che significa che il rapporto mancante per i dati orari è 98,33%. Tuttavia, è possibile riempire i dati orari con l'unico valore se la serie temporale oraria in genere non fluttua eccessivamente.

## <a name="parameters"></a>Parametri

### <a name="sliding-window"></a>Finestra temporale scorrevole

Il rilevamento di anomalie a più variabili accetta un segmento di punti dati di lunghezza `slidingWindow` come input e decide se il punto dati successivo è un'anomalia. Maggiore è la lunghezza del campione, più dati verranno presi in considerazione per una decisione. Quando si sceglie un valore appropriato per `slidingWindow` le proprietà dei dati di input e il compromesso tra tempo di formazione/inferenza e potenziale miglioramento delle prestazioni, è necessario tenere presente quanto segue: `slidingWindow` è costituito da un numero intero compreso tra 28 e 2880. È possibile decidere il numero di punti dati usati come input a seconda che i dati siano periodici e la frequenza di campionamento per i dati.

Quando i dati sono periodici, è possibile includere 1-3 cicli come input e quando i dati vengono campionati a frequenza elevata (granularità ridotta), ad esempio dati di livello minuto o di secondo livello, è possibile selezionare più dati come input. Un altro problema è costituito dal fatto che gli input più lunghi possono causare tempi di formazione/inferenza più lunghi e non vi è alcuna garanzia che altri punti di input comportino un miglioramento delle prestazioni. Mentre un numero troppo basso di punti dati, può rendere difficile la convergenza del modello a una soluzione ottimale. Ad esempio, è difficile rilevare le anomalie quando i dati di input hanno solo due punti.

### <a name="align-mode"></a>Modalità allineamento

Il parametro `alignMode` viene utilizzato per indicare come si desidera allineare più serie temporali negli indicatori temporali. Ciò è dovuto al fatto che molte serie temporali presentano valori mancanti ed è necessario allinearle sugli stessi timestamp prima di continuare l'elaborazione. Per questo parametro sono disponibili due opzioni, `inner join` e `outer join` . `inner join` significa che i risultati del rilevamento vengono segnalati in timestamp in cui **ogni serie temporale** ha un valore, mentre `outer join` significa che i risultati del rilevamento vengono segnalati in timestamp per **qualsiasi serie temporale** con un valore.  **Il `alignMode` influirà anche sulla sequenza di input del modello**, quindi scegliere un oggetto adatto `alignMode` per lo scenario in uso perché i risultati potrebbero essere significativamente diversi.

Qui viene illustrato un esempio per illustrare `alignModel` i diversi valori.

#### <a name="series1"></a>Series1

|timestamp | Valore|
----------| -----|
|`2020-11-01`| 1  
|`2020-11-02`| 2  
|`2020-11-04`| 4  
|`2020-11-05`| 5

#### <a name="series2"></a>Series2

timestamp | Valore  
--------- | -
`2020-11-01`| 1  
`2020-11-02`| 2  
`2020-11-03`| 3  
`2020-11-04`| 4

#### <a name="inner-join-two-series"></a>Inner join due serie
  
 timestamp | Series1 | Series2
----------| - | -
`2020-11-01`| 1 | 1
`2020-11-02`| 2 | 2
`2020-11-04`| 4 | 4

#### <a name="outer-join-two-series"></a>Outer join due serie

 timestamp | Series1 | Series2
--------- | - | -
`2020-11-01`| 1 | 1
`2020-11-02`| 2 | 2
`2020-11-03`| N/D | 3
`2020-11-04`| 4 | 4
`2020-11-05`| 5 | N/D

### <a name="fill-not-available-na"></a>Riempimento non disponibile (NA)

Quando le variabili sono allineate in base al timestamp da outer join, in alcune variabili potrebbe essere presente un `Not Available` `NA` valore (). È possibile specificare il metodo per compilare questo valore NA. Le opzioni per `fillNAMethod` sono `Linear` ,, `Previous` , `Subsequent`  `Zero` e `Fixed` .

| Opzione     | Metodo                                                                                           |
| ---------- | -------------------------------------------------------------------------------------------------|
| Lineari     | Inserire i valori NA per l'interpolazione lineare                                                           |
| Indietro   | Propaga l'ultimo valore valido per colmare le lacune. Esempio: `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 2, 3, 3, 4]` |
| Successive | Usare il valore valido successivo per colmare le lacune. Esempio: `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 3, 3, 4, 4]`       |
| Zero       | Inserire i valori NA con 0.                                                                           |
| Fisso      | Inserire i valori NA con un valore valido specificato da fornire in `paddingValue` .          |

## <a name="model-analysis"></a>Analisi del modello

### <a name="training-latency"></a>Latenza di training

Il training del rilevamento delle anomalie multivariata può richiedere molto tempo. In particolare quando si dispone di una grande quantità di timestamp utilizzati per il training. Pertanto, una parte del processo di training è consentita come asincrona. In genere, gli utenti inviano l'attività Train tramite l'API Train Model. Ottenere quindi lo stato del modello tramite `Get Multivariate Model API` . Qui viene illustrato come estrarre il tempo rimanente prima del completamento del training. Nella risposta dell'API del modello Get multivariate è presente un elemento denominato `diagnosticsInfo` . In questo elemento è presente un `modelState` elemento. Per calcolare il tempo rimanente, è necessario usare `epochIds` e `latenciesInSeconds` . Un Epoch rappresenta un ciclo completo dei dati di training. Ogni 10 epoche, le informazioni sullo stato vengono restituite. In totale, si eseguirà il training per 100 epochs, la latenza indica il tempo necessario per un'epoca. Con queste informazioni, è noto il tempo rimanente per il training del modello.

### <a name="model-performance"></a>Prestazioni del modello

Rilevamento di anomalie a più variabili, come modello senza supervisione. Il modo migliore per valutarlo consiste nel controllare manualmente i risultati delle anomalie. Nella risposta del modello Get multivariate vengono fornite alcune informazioni di base per analizzare le prestazioni del modello. Nell' `modelState` elemento restituito dall'API del modello Get multivariate è possibile utilizzare `trainLosses` e `validationLosses` per valutare se è stato eseguito il training del modello come previsto. Nella maggior parte dei casi, le due perdite si ridurranno gradualmente. Un'altra informazione che consente di analizzare le prestazioni del modello rispetto a è in `variableStates` . L'elenco di Stati delle variabili è classificato `filledNARatio` in ordine decrescente. Maggiore è il peggioramento delle prestazioni, in genere è necessario ridurlo il più `NA ratio` possibile. `NA` potrebbe essere causato da valori mancanti o da variabili non allineate da una prospettiva timestamp.

## <a name="next-steps"></a>Passaggi successivi

- [Guide introduttive](../quickstarts/client-libraries-multivariate.md).
- [Informazioni sugli algoritmi sottostanti che rilevano un sensore multivariato di Power Anomaly](https://arxiv.org/abs/2009.02040)