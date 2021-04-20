---
title: Impostazione dei parametri per i flussi di dati di mapping
description: Informazioni su come parametrizzare un flusso di dati di mapping da data factory pipeline
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/19/2021
ms.openlocfilehash: 22c4fc0680d8666d8c2dfafb8829436e27cf1ebd
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725711"
---
# <a name="parameterizing-mapping-data-flows"></a>Impostazione dei parametri per i flussi di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

I flussi di dati di mapping Azure Data Factory e Azure Synapse Analytics supportano l'uso di parametri. Definire i parametri all'interno della definizione del flusso di dati e usarli in tutte le espressioni. I valori dei parametri vengono impostati dalla pipeline chiamante tramite l'attività Execute Flusso di dati. Sono disponibili tre opzioni per impostare i valori nelle espressioni di attività del flusso di dati:

* Usare il linguaggio delle espressioni del flusso di controllo della pipeline per impostare un valore dinamico
* Usare il linguaggio delle espressioni del flusso di dati per impostare un valore dinamico
* Usare uno dei due linguaggi di espressione per impostare un valore letterale statico

Usare questa funzionalità per rendere i flussi di dati per utilizzo generico, flessibili e riutilizzabili. È possibile parametrizzare le impostazioni e le espressioni del flusso di dati con questi parametri.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Creare parametri in un flusso di dati di mapping

Per aggiungere parametri al flusso di dati, fare clic sulla parte vuota dell'area di disegno del flusso di dati per visualizzare le proprietà generali. Nel riquadro delle impostazioni verrà visualizzata una scheda denominata **Parametro**. Selezionare **Nuovo** per generare un nuovo parametro. Per ogni parametro è necessario assegnare un nome, selezionare un tipo e facoltativamente impostare un valore predefinito.

![Creare Flusso di dati parametri](media/data-flow/create-params.png "Creare Flusso di dati parametri")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Usare parametri in un flusso di dati di mapping 

È possibile fare riferimento ai parametri in qualsiasi espressione del flusso di dati. I parametri iniziano con $ e non sono modificabili. L'elenco dei parametri disponibili è disponibile all'interno del Generatore di espressioni nella **scheda** Parametri .

![Screenshot che mostra i parametri disponibili nella scheda Parametri.](media/data-flow/parameter-expression.png "Espressione del parametro del flusso di dati")

È possibile aggiungere rapidamente altri parametri selezionando **Nuovo parametro** e specificando il nome e il tipo.

![Screenshot che mostra i parametri nella scheda Parametri con i nuovi parametri aggiunti.](media/data-flow/new-parameter-expression.png "Espressione del parametro del flusso di dati")

## <a name="assign-parameter-values-from-a-pipeline"></a>Assegnare i valori dei parametri da una pipeline

Dopo aver creato un flusso di dati con parametri, è possibile eseguirlo da una pipeline con l'attività esegui Flusso di dati dati. Dopo aver aggiunto l'attività al canvas della pipeline, verranno presentati i parametri del flusso di dati disponibili nella **scheda Parametri dell'attività.**

Quando si assegnano valori di parametro, è possibile usare il linguaggio delle espressioni [della pipeline](control-flow-expression-language-functions.md) o il [linguaggio delle](data-flow-expression-functions.md) espressioni del flusso di dati in base ai tipi spark. Ogni flusso di dati di mapping può avere qualsiasi combinazione di parametri di espressione del flusso di dati e della pipeline.

![Screenshot che mostra la scheda Parametri con Flusso di dati'espressione selezionata per il valore di myparam.](media/data-flow/parameter-assign.png "Impostazione di un Flusso di dati dati")

### <a name="pipeline-expression-parameters"></a>Parametri dell'espressione della pipeline

I parametri delle espressioni di pipeline consentono di fare riferimento a variabili di sistema, funzioni, parametri della pipeline e variabili simili ad altre attività della pipeline. Quando si fa clic **su Espressione pipeline,** viene aperta una barra di spostamento laterale che consente di immettere un'espressione usando il generatore di espressioni.

![Screenshot che mostra il riquadro generatore di espressioni.](media/data-flow/parameter-pipeline.png "Impostazione di un Flusso di dati parametro")

Quando viene fatto riferimento, i parametri della pipeline vengono valutati e il relativo valore viene usato nel linguaggio delle espressioni del flusso di dati. Il tipo di espressione della pipeline non deve corrispondere al tipo di parametro del flusso di dati. 

#### <a name="string-literals-vs-expressions"></a>Valori letterali stringa ed espressioni

Quando si assegna un parametro di espressione della pipeline di tipo stringa, per impostazione predefinita verranno aggiunte le virgolette e il valore verrà valutato come valore letterale. Per leggere il valore del parametro come espressione del flusso di dati, selezionare la casella dell'espressione accanto al parametro.

![Screenshot che mostra il riquadro Parametri del flusso di dati Espressione selezionata per un parametro.](media/data-flow/string-parameter.png "Impostazione di un Flusso di dati parametro")

Se il parametro del flusso `stringParam` di dati fa riferimento a un parametro della pipeline con valore `upper(column1)` . 

- Se expression è selezionato, `$stringParam` restituisce il valore di column1 tutto maiuscolo.
- Se expression non è selezionato (comportamento predefinito),  `$stringParam` restituisce `'upper(column1)'`

#### <a name="passing-in-timestamps"></a>Passaggio di timestamp

Nel linguaggio delle espressioni della pipeline, variabili di sistema come e funzioni come timestamp restituiti come stringhe nel formato `pipeline().TriggerTime` `utcNow()` 'yyyy-MM-dd \' T \' HH:mm:ss. SSSSSSZ'. Per convertirli in parametri del flusso di dati di tipo timestamp, usare l'interpolazione di stringhe per includere il timestamp desiderato in una `toTimestamp()` funzione. Ad esempio, per convertire il tempo del trigger della pipeline in un parametro del flusso di dati, è possibile usare `toTimestamp(left('@{pipeline().TriggerTime}', 23), 'yyyy-MM-dd\'T\'HH:mm:ss.SSS')` . 

![Screenshot che mostra la scheda Parametri in cui è possibile immettere un'ora di attivazione.](media/data-flow/parameter-timestamp.png "Impostazione di un Flusso di dati parametro")

> [!NOTE]
> I flussi di dati possono supportare solo fino a 3 millisecondi. La `left()` funzione viene usata per ridurre le cifre aggiuntive.

#### <a name="pipeline-parameter-example"></a>Esempio di parametro della pipeline

Si noti che si dispone di un parametro `intParam` integer che fa riferimento a un parametro della pipeline di tipo String, `@pipeline.parameters.pipelineParam` . 

![Screenshot che mostra la scheda Parametri con parametri denominati stringParam e intParam.](media/data-flow/parameter-pipeline-2.png "Impostazione di un Flusso di dati parametro")

`@pipeline.parameters.pipelineParam` viene assegnato un valore di in `abs(1)` fase di esecuzione.

![Screenshot che mostra la scheda Parametri con il valore di b s (1) selezionato.](media/data-flow/parameter-pipeline-4.png "Impostazione di un Flusso di dati parametro")

Quando `$intParam` viene fatto riferimento in un'espressione, ad esempio una colonna derivata, `abs(1)` restituirà `1` . 

![Screenshot che mostra il valore delle colonne.](media/data-flow/parameter-pipeline-3.png "Impostazione di un Flusso di dati parametro")

### <a name="data-flow-expression-parameters"></a>Parametri dell'espressione del flusso di dati

Selezionare **Espressione flusso di dati** per aprire il generatore di espressioni del flusso di dati. Sarà possibile fare riferimento a funzioni, altri parametri e a qualsiasi colonna dello schema definita in tutto il flusso di dati. Questa espressione verrà valutata così come è quando viene fatto riferimento.

> [!NOTE]
> Se si passa un'espressione non valida o si fa riferimento a una colonna dello schema che non esiste nella trasformazione, il parametro restituirà Null.


### <a name="passing-in-a-column-name-as-a-parameter"></a>Passaggio di un nome di colonna come parametro

Un modello comune è quello di passare un nome di colonna come valore di parametro. Se la colonna è definita nello schema del flusso di dati, è possibile fare riferimento direttamente alla colonna come espressione stringa. Se la colonna non è definita nello schema, usare la `byName()` funzione . Ricordarsi di eseguire il cast della colonna al tipo appropriato con una funzione di cast, ad esempio `toString()` .

Ad esempio, se si desidera eseguire il mapping di una colonna stringa in base a un parametro , è possibile aggiungere una trasformazione colonna `columnName` derivata uguale a `toString(byName($columnName))` .

![Passaggio di un nome di colonna come parametro](media/data-flow/parameterize-column-name.png "Passaggio di un nome di colonna come parametro")

## <a name="next-steps"></a>Passaggi successivi
* [Attività Esegui flusso di dati](control-flow-execute-data-flow-activity.md)
* [Espressioni del flusso di controllo](control-flow-expression-language-functions.md)
