---
title: Modalità di debug del flusso di dati di mapping
description: Avviare una sessione di debug interattivo durante la compilazione di flussi di dati
ms.author: makromer
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.openlocfilehash: ac0e088c587132b32f2112d21bce936eac35dc72
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515870"
---
# <a name="mapping-data-flow-debug-mode"></a>Modalità di debug del flusso di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Panoramica

Azure Data Factory modalità di debug del flusso di dati di mapping consente di osservare in modo interattivo la trasformazione della forma dei dati durante la compilazione e il debug dei flussi di dati. La sessione di debug può essere usata sia nelle sessioni Flusso di dati di progettazione sia durante l'esecuzione del debug della pipeline dei flussi di dati. Per attivare la modalità di debug, usare il Flusso di dati **Debug** nella barra superiore dell'area di disegno del flusso di dati o dell'area di disegno della pipeline quando sono presenti attività flusso di dati.

![Dispositivo di scorrimento debug 1](media/data-flow/debugbutton.png "Dispositivo di scorrimento debug")

![Dispositivo di scorrimento debug 2](media/data-flow/debug-button-4.png "Dispositivo di scorrimento debug")

Quando si attiva il dispositivo di scorrimento, verrà richiesto di selezionare la configurazione del runtime di integrazione da usare. Se si sceglie AutoResolveIntegrationRuntime, verrà riattivato un cluster con otto core di calcolo generale con un tempo di vita predefinito di 60 minuti. Se si vuole consentire un team più inattivo prima del timeout della sessione, è possibile scegliere un'impostazione TTL più alta. Per altre informazioni sui runtime di integrazione del flusso di dati, vedere [Prestazioni del flusso di dati](concepts-data-flow-performance.md#ir).

![Debug della selezione di IR](media/data-flow/debug-new-1.png "Selezione del debug del motore di esecuzione del debug")

Quando la modalità di debug è attivata, il flusso di dati verrà compilato in modo interattivo con un cluster Spark attivo. La sessione verrà chiusa quando si disattiva il debug in Azure Data Factory. È bene tenere conto dei costi orari addebitati da Azure Databricks durante il periodo in cui è attivata la sessione di debug.

Nella maggior parte dei casi, è consigliabile compilare i flussi di dati in modalità di debug in modo da poter convalidare la logica di business e visualizzare le trasformazioni dei dati prima di pubblicare il lavoro in Azure Data Factory. Usare il pulsante "Debug" nel pannello della pipeline per testare il flusso di dati in una pipeline.

![Visualizzare le sessioni di debug del flusso di dati](media/iterative-development-debugging/view-dataflow-debug-sessions.png)

> [!NOTE]
> Ogni sessione di debug avviata da un utente dall'interfaccia utente del browser ADF è una nuova sessione con il proprio cluster Spark. È possibile usare la visualizzazione di monitoraggio per le sessioni di debug precedenti per visualizzare e gestire le sessioni di debug per ogni factory. Viene addebitato ogni ora di esecuzione di ogni sessione di debug, inclusa l'ora TTL.

## <a name="cluster-status&quot;></a>Stato del cluster

L'indicatore di stato del cluster nella parte superiore dell'area di progettazione diventa verde quando il cluster è pronto per il debug. Se il cluster è già pronto, l'indicatore verde viene visualizzato quasi immediatamente. Se il cluster non era già in esecuzione quando è stata attivata la modalità di debug, il cluster Spark eseguirà un avvio a freddo. L'indicatore verrà ruotato fino a quando l'ambiente non sarà pronto per il debug interattivo.

Al termine del debug, disattivare l'opzione Debug in modo che il cluster Spark possa terminare e non verrà più fatturata l'attività di debug.

## <a name=&quot;debug-settings&quot;></a>Impostazioni di debug

Dopo aver attivata la modalità di debug, è possibile modificare la modalità di anteprima dei dati in un flusso di dati. Le impostazioni di debug possono essere modificate facendo clic su &quot;Impostazioni di debug&quot; sulla barra Flusso di dati canvas. È possibile selezionare qui il limite di righe o l'origine file da usare per ogni trasformazione Origine. I limiti di riga in questa impostazione sono solo per la sessione di debug corrente. È anche possibile selezionare il servizio collegato di staging da usare per un'Azure Synapse Analytics origine. 

![Impostazioni di debug](media/data-flow/debug-settings.png &quot;Impostazioni di debug")

Se sono presenti parametri nel Flusso di dati o in uno dei set di dati a cui si fa riferimento, è possibile specificare i valori da usare durante il debug selezionando la **scheda** Parametri.

Usare le impostazioni di campionamento qui per puntare a file di esempio o tabelle di dati di esempio in modo che non sia necessario modificare i set di dati di origine. Usando qui un file o una tabella di esempio, è possibile mantenere le stesse impostazioni di logica e proprietà nel flusso di dati durante il test su un subset di dati.

![Parametri delle impostazioni di debug](media/data-flow/debug-settings2.png "Parametri delle impostazioni di debug")

L'IR predefinito usato per la modalità di debug nei flussi di dati di AdF è un piccolo nodo di lavoro singolo a 4 core con un nodo di driver singolo a 4 core. Questa operazione funziona correttamente con esempi di dati più piccoli durante il test della logica del flusso di dati. Se si espandono i limiti di riga nelle impostazioni di debug durante l'anteprima dei dati o si imposta un numero più elevato di righe campionate nell'origine durante il debug della pipeline, è consigliabile impostare un ambiente di calcolo più grande in un nuovo Azure Integration Runtime. È quindi possibile riavviare la sessione di debug usando l'ambiente di calcolo più grande.

## <a name="data-preview"></a>Anteprima dati

Quando il debug è attivato, la scheda Anteprima dati sarà attivata nel pannello inferiore. Senza la modalità di debug attivata, Flusso di dati verranno visualizzati solo i metadati correnti all'interno e all'interno di ogni trasformazione nella scheda Ispeziona. L'anteprima dei dati eseguirà una query solo sul numero di righe impostate come limite nelle impostazioni di debug. Fare **clic su** Aggiorna per recuperare l'anteprima dei dati.

![Anteprima dati](media/data-flow/datapreview.png "Anteprima dati")

> [!NOTE]
> Le origini file limitano solo le righe visualizzate, non le righe lette. Per i set di dati di dimensioni molto grandi, è consigliabile prendere una piccola parte del file e usarlo per i test. È possibile selezionare un file temporaneo in Impostazioni di debug per ogni origine che è un tipo di set di dati di file.

Quando è attiva la modalità di debug nel flusso di dati, i dati non vengono scritti nella trasformazione sink. Una sessione di debug è destinata a fungere da test harness per le trasformazioni. I sink non sono necessari durante il debug e vengono ignorati nel flusso di dati. Se si vuole testare la scrittura dei dati nel sink, eseguire il Flusso di dati da una pipeline Azure Data Factory e usare l'esecuzione di debug da una pipeline.

Anteprima dati è uno snapshot dei dati trasformati usando i limiti di riga e il campionamento dei dati dai frame di dati nella memoria Spark. Pertanto, i driver sink non vengono utilizzati o testati in questo scenario.

### <a name="testing-join-conditions"></a>Test delle condizioni di join

Quando si eserciteranno le trasformazioni Join, Exists o Lookup di unit test, assicurarsi di usare un piccolo set di dati noti per il test. È possibile usare l'opzione Impostazioni di debug precedente per impostare un file temporaneo da usare per i test. Questa operazione è necessaria perché quando si limitano o si esegue il campionamento di righe da un set di dati di grandi dimensioni, non è possibile stimare quali righe e quali chiavi verranno lette nel flusso per il test. Il risultato è non deterministico, ovvero le condizioni di join potrebbero non riuscire.

### <a name="quick-actions"></a>Azioni rapide

Dopo aver visualizzato l'anteprima dei dati, è possibile generare una trasformazione rapida per eseguire il typecast, rimuovere o eseguire una modifica in una colonna. Fare clic sull'intestazione di colonna e quindi selezionare una delle opzioni dalla barra degli strumenti di anteprima dei dati.

![Screenshot che mostra la barra degli strumenti di anteprima dei dati con opzioni: Typecast, Modify, Statistics e Remove.](media/data-flow/quick-actions1.png "Azioni rapide")

Dopo aver selezionato una modifica, l'anteprima dei dati verrà aggiornata immediatamente. Fare **clic su** Conferma nell'angolo in alto a destra per generare una nuova trasformazione.

![Screenshot che mostra il pulsante Conferma.](media/data-flow/quick-actions2.png "Azioni rapide")

**Typecast** e **Modify** genereranno una trasformazione Colonna derivata e **Rimuovi** genererà una trasformazione Seleziona.

![Screenshot che mostra le impostazioni della colonna derivata.](media/data-flow/quick-actions3.png "Azioni rapide")

> [!NOTE]
> Se si modifica il Flusso di dati, è necessario recuperare nuovamente l'anteprima dei dati prima di aggiungere una trasformazione rapida.

### <a name="data-profiling"></a>Profilatura dei dati

Se si seleziona una colonna  nella scheda dell'anteprima dei dati e si fa clic su Statistiche nella barra degli strumenti dell'anteprima dei dati, verrà visualizzato un grafico all'estrema destra della griglia dei dati con statistiche dettagliate su ogni campo. Azure Data Factory determina quale tipo di grafico visualizzare in base al campionamento dei dati. Per impostazione predefinita, i campi con cardinalità elevata vengono visualizzati nei grafici NULL/NOT NULL, mentre i dati categorici e numerici con cardinalità bassa visualizzano grafici a barre che mostrano la frequenza dei valori dei dati. Verranno visualizzati anche la lunghezza massima/minima dei campi stringa, i valori min/max nei campi numerici, lo sviluppo standard, i percentili, i conteggi e la media.

![Statistiche della colonna](media/data-flow/stats.png "Statistiche della colonna")

## <a name="next-steps"></a>Passaggi successivi

* Al termine della compilazione e del debug del flusso di dati, [eseguirlo da una pipeline.](control-flow-execute-data-flow-activity.md)
* Quando si testa la pipeline con un flusso di dati, usare l'opzione Di esecuzione [dell'esecuzione del debug della pipeline.](iterative-development-debugging.md)
