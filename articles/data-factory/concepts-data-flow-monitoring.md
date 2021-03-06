---
title: Monitoraggio dei flussi di dati di mapping
description: Come monitorare visivamente i flussi di dati di mapping in Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/11/2021
ms.openlocfilehash: 82aba428627cba1a3df26fc67c5da0cde52d368c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309068"
---
# <a name="monitor-data-flows"></a>Monitorare i flussi di dati

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Al termine della compilazione e del debug del flusso di dati è necessario definire l'esecuzione del flusso di dati in base a una pianificazione nel contesto di una pipeline. È possibile pianificare la pipeline da Azure Data Factory tramite trigger. In alternativa, è possibile usare l'opzione Trigger Now (Attiva ora) nel generatore di pipeline di Azure Data Factory per attivare una singola esecuzione in modo da testare il flusso di dati nel contesto della pipeline.

Quando si esegue la pipeline, è possibile monitorarla insieme a tutte le attività che contiene, inclusa l'attività Flusso di dati. Fare clic sull'icona di monitoraggio nel pannello dell'interfaccia utente di Azure Data Factory. Verrà visualizzata una schermata simile alla seguente. Le icone evidenziate consentono di esaminare più in dettaglio le attività nella pipeline, tra cui l'attività Flusso di dati.

![Screenshot mostra le icone da selezionare per le pipeline.](media/data-flow/mon001.png "Monitoraggio dei flussi di dati")

A questo livello vengono visualizzate anche le statistiche, insieme alle tempistiche e allo stato di esecuzione. L'ID esecuzione a livello di attività è diverso da quello a livello di pipeline. L'ID esecuzione al livello precedente è per la pipeline. La selezione del simbolo a forma di occhiali consente la visualizzazione di ulteriori dettagli sull'esecuzione del flusso di dati.

![Screenshot mostra l'icona degli occhiali per visualizzare i dettagli dell'esecuzione del flusso di dati.](media/data-flow/monitoring-details.png "Monitoraggio dei flussi di dati")

Durante la visualizzazione grafica di monitoraggio dei nodi, è possibile osservare una versione semplificata di tipo solo visualizzazione del grafico del flusso di dati.

![Screenshot mostra la versione di sola visualizzazione del grafico.](media/data-flow/mon003.png "Monitoraggio dei flussi di dati")

Ecco un video informativo sul monitoraggio delle prestazioni dei flussi di dati dalla schermata di monitoraggio di ADF:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u4mH]

## <a name="view-data-flow-execution-plans"></a>Visualizzare i piani di esecuzione del flusso di dati

Quando il Flusso di dati viene eseguito in Spark, Azure Data Factory determina i percorsi del codice ottimali in base all'interezza del flusso di dati. Inoltre, i percorsi di esecuzione possono verificarsi in nodi con scalabilità orizzontale e partizioni di dati diversi. Di conseguenza, il grafico di monitoraggio rappresenta la progettazione del flusso, tenendo conto del percorso di esecuzione delle trasformazioni. La selezione di singoli nodi genera la visualizzazione di "raggruppamenti" che rappresentano il codice che è stato eseguito contemporaneamente nel cluster. Le tempistiche e i conteggi visualizzati rappresentano i gruppi invece dei singoli passaggi nella progettazione.

![Screenshot mostra la pagina per un flusso di dati.](media/data-flow/mon004.png "Monitoraggio dei flussi di dati")

* Quando si seleziona l'area aperta nella finestra di monitoraggio, le statistiche nel riquadro inferiore mostrano le tempistiche e i numeri di righe per ogni sink e le trasformazioni che hanno generato i dati sink per la derivazione della trasformazione.

* Quando si selezionano singole trasformazioni, si riceve ulteriore feedback nel pannello a destra che mostra le statistiche sulle partizioni, i numeri di colonne, l'asimmetria (quanto uniformemente sono distribuiti i dati tra le partizioni) e la curtosi (quanti picchi presentano i dati).

* Quando si seleziona il sink nella visualizzazione del nodo, viene visualizzata la derivazione della colonna. Esistono tre metodi diversi in cui vengono accumulate le colonne lungo il flusso di dati per raggiungere il sink. ovvero:

  * Calcolato: si usa la colonna per l'elaborazione condizionale o all'interno di un'espressione nel flusso di dati, ma questa non viene immessa nel sink
  * Derivato: la colonna è una nuova colonna generata nel flusso, ovvero non è presente nell'origine
  * Mappato: la colonna è derivata dall'origine e ne viene eseguito il mapping a un campo sink
  * Stato del flusso di dati: lo stato attuale dell'esecuzione
  * Tempo di avvio del cluster: periodo di tempo per l'acquisizione dell'ambiente di calcolo di Spark JIT per l'esecuzione del flusso di dati
  * Numero delle trasformazioni: il numero di passaggi di trasformazione eseguiti nel flusso
  
![Screenshot che mostra l'opzione di aggiornamento.](media/data-flow/monitornew.png "Monitoraggio dei flussi di dati nuovo")

## <a name="total-sink-processing-time-vs-transformation-processing-time"></a>Tempo di elaborazione totale del sink e tempo di elaborazione della trasformazione

Ogni fase di trasformazione include il tempo totale necessario per completare la fase con ogni tempo di esecuzione della partizione totale. Quando si fa clic sul sink, viene visualizzato il termine "tempo di elaborazione sink". Questa volta include il tempo totale di trasformazione e *il tempo* di i/O impiegato per scrivere i dati nell'archivio di destinazione. La differenza tra il tempo di elaborazione del sink e il totale della trasformazione è il tempo di I/O per la scrittura dei dati.

È inoltre possibile visualizzare un intervallo dettagliato per ogni passaggio di trasformazione della partizione se si apre l'output JSON dall'attività flusso di dati nella visualizzazione di monitoraggio della pipeline di ADF. Il file JSON contiene un intervallo di millisecondi per ogni partizione, mentre la visualizzazione monitoraggio UX è un intervallo aggregato di partizioni aggiunte:

```
 {
     "stage": 4,
     "partitionTimes": [
          14353,
          14914,
          14246,
          14912,
          ...
         ]
}
```

### <a name="sink-processing-time"></a>Tempo di elaborazione sink

Quando si seleziona un'icona di trasformazione del sink nella mappa, il pannello scorrevole a destra indicherà un ulteriore punto dati denominato "tempo di elaborazione post" nella parte inferiore. Questa è la quantità di tempo impiegato per l'esecuzione del processo nel cluster Spark *dopo che* i dati sono stati caricati, trasformati e scritti. Questa volta può includere la chiusura dei pool di connessioni, l'arresto del driver, l'eliminazione di file, l'Unione di file e così via. Quando si eseguono azioni nel flusso, ad esempio "spostare file" e "output in un singolo file", è probabile che venga visualizzato un aumento del valore del tempo di elaborazione successivo.

* Durata della fase di scrittura: tempo di scrittura dei dati in un percorso di gestione temporanea per sinapsi SQL
* Tabella operazione SQL Duration: tempo impiegato per lo trasferimento dei dati dalle tabelle temporanee alla tabella di destinazione
* Durata pre-SQL & durata post SQL: tempo impiegato per l'esecuzione di comandi pre/post SQL
* Durata dei comandi pre & durata dei comandi post: tempo impiegato per l'esecuzione di eventuali operazioni pre/post per origine/sink basati su file. Ad esempio spostare o eliminare file dopo l'elaborazione.
* Durata merge: il tempo impiegato per l'Unione del file, i file di Unione vengono usati per i sink basati su file durante la scrittura in un singolo file o quando viene usato "nome file come dati di colonna". Se in questa metrica viene impiegato un tempo significativo, è consigliabile evitare di usare queste opzioni.
  
## <a name="error-rows"></a>Righe di errore

L'abilitazione della gestione delle righe con errori nel sink del flusso di dati verrà riflessa nell'output di monitoraggio. Quando si imposta il sink su "segnala esito positivo in caso di errore", l'output di monitoraggio visualizzerà il numero di righe con esito positivo e negativo quando si fa clic sul nodo monitoraggio sink.

![Screenshot che mostra le righe di errore.](media/data-flow/error-row-2.png "Monitoraggio righe errori riuscito")

Quando si seleziona "Segnala errore in caso di errore", lo stesso output verrà visualizzato solo nel testo di output di monitoraggio attività. Questo perché l'attività flusso di dati restituirà un errore per l'esecuzione e la visualizzazione di monitoraggio dettagliata non sarà disponibile.

![Screenshot mostra le righe di errore nell'attività.](media/data-flow/error-rows-4.png "Errore di monitoraggio righe errore")

## <a name="monitor-icons"></a>Icona di monitoraggio

Questa icona indica che i dati della trasformazione sono già stati memorizzati nella cache del cluster e di conseguenza le tempistiche e il percorso di esecuzione sono già stati presi in considerazione:

![Screenshot che mostra l'icona del disco.](media/data-flow/mon005.png "Monitoraggio dei flussi di dati")

Nella trasformazione vengono anche visualizzate alcune icone a forma di cerchio verde. Questa rappresentano un conteggio del numero di sink in cui vengono trasmessi i dati.
