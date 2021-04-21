---
title: Funzioni nelle query Monitoraggio di Azure log
description: Questo articolo descrive come usare le funzioni per chiamare una query da un'altra query di log in Monitoraggio di Azure.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/19/2021
ms.openlocfilehash: fecede1d582232ebc75878a687a24818031f0d80
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752854"
---
# <a name="functions-in-azure-monitor-log-queries"></a>Funzioni nelle query Monitoraggio di Azure log
Una funzione è una query di log in Monitoraggio di Azure che può essere usata in altre query di log come se fosse un comando. Le funzioni consentono agli sviluppatori di fornire soluzioni a clienti diversi e di riutilizzare la logica di query nel proprio ambiente. Questo articolo fornisce informazioni dettagliate su come usare le funzioni e su come crearne di personalizzate.

## <a name="types-of-functions"></a>Tipi di funzioni
Esistono due tipi di funzioni in Monitoraggio di Azure:

- **Funzione della soluzione:** Funzioni predefinite incluse in Monitoraggio di Azure. Sono disponibili in tutte le aree di lavoro Log Analytics e non possono essere modificate.
- **Funzioni dell'area di lavoro:** Funzioni installate in una particolare area di lavoro Log Analytics e possono essere modificate e controllate dall'utente.

## <a name="viewing-functions"></a>Visualizzazione delle funzioni
È possibile visualizzare le funzioni della soluzione e le funzioni dell'area di lavoro nell'area di lavoro corrente dalla **scheda Funzioni** nel riquadro sinistro di un'area di lavoro Log Analytics. Usare il **pulsante Filtra** per filtrare le funzioni incluse nell'elenco e **Raggruppa per** per modificare il raggruppamento. Digitare una stringa nella casella **Di** ricerca per individuare una funzione specifica. Passare il puntatore del mouse su una funzione per visualizzare i relativi dettagli, inclusi una descrizione e i parametri.

:::image type="content" source="media/functions/view-functions.png" alt-text="Funzione View" lightbox="media/functions/view-functions.png":::

## <a name="use-a-function"></a>Usare una funzione
Usare una funzione in una query digitandone il nome con i valori per tutti i parametri esattamente come si digita in un comando. L'output della funzione può essere restituito come risultati o reindirizzato tramite pipe a un altro comando.

Aggiungere una funzione alla query corrente facendo doppio clic sul nome o passando il puntatore del mouse su di essa e selezionando **Usa nell'editor**. Le funzioni nell'area di lavoro verranno incluse anche in IntelliSense durante la digitazione in una query. 

Se una query richiede parametri, specificarli usando la sintassi : `function_name(param1,param2,...)` .

:::image type="content" source="media/functions/function-use.png" alt-text="Usare una funzione" lightbox="media/functions/function-use.png":::

## <a name="create-a-function"></a>Creare una funzione
Per creare una funzione dalla query corrente nell'editor, selezionare **Salva** e quindi **Salva come funzione**. 

:::image type="content" source="media/functions/function-save.png" alt-text="Creare una funzione" lightbox="media/functions/function-save.png":::

Creare una funzione con Log Analytics nel portale di Azure facendo clic su **Salva** e quindi specificando le informazioni richieste nella tabella seguente.

| Impostazione | Descrizione |
|:---|:---|
| Nome funzione  | Nome della funzione. Non può includere uno spazio o caratteri speciali. Potrebbe anche non iniziare con un carattere di sottolineatura (_) perché questo carattere è riservato per le funzioni della soluzione. |
| Categoria legacy | Categoria definita dall'utente che consente di filtrare e raggruppare le funzioni.   |
| Salva come gruppo di computer | Salvare la query come gruppo [di computer](computer-groups.md).  |
| Parametri | Aggiungere un parametro per ogni variabile nella funzione che richiede un valore quando viene usata. Per informazioni [dettagliate, vedere](#function-parameters) Parametri della funzione. |

:::image type="content" source="media/functions/function-details.png" alt-text="Dettagli della funzione" lightbox="media/functions/function-details.png":::

## <a name="function-parameters"></a>Parametri di funzione 
È possibile aggiungere parametri a una funzione in modo da poter fornire valori per determinate variabili quando la si chiama. In questo modo è possibile usare la stessa funzione in query diverse, ognuna delle quali fornisce valori diversi per i parametri. I parametri sono definiti dalle proprietà seguenti.

| Impostazione | Descrizione |
|:---|:---|
| Type  | Tipo di dati per il valore. |
| Nome  | Nome del parametro. Si tratta del nome che deve essere usato nella query per sostituire con il valore del parametro.  |
| Valore predefinito | Valore da usare per il parametro se non viene specificato un valore. |

I parametri vengono ordinati quando vengono creati con tutti i parametri che non hanno un valore predefinito posizionato davanti a quelli che hanno un valore predefinito.

## <a name="working-with-function-code"></a>Uso del codice della funzione
È possibile visualizzare il codice di una funzione per ottenere informazioni dettagliate sul funzionamento o per modificare il codice per una funzione dell'area di lavoro. Selezionare **Carica il codice della funzione** per aggiungere il codice della funzione alla query corrente nell'editor. Se la si aggiunge a una query vuota o alla prima riga di una query esistente, il nome della funzione verrà aggiunto alla scheda. Se si tratta di una funzione dell'area di lavoro, questa opzione consente di modificare i dettagli della funzione.

:::image type="content" source="media/functions/function-code.png" alt-text="Caricare il codice della funzione" lightbox="media/functions/function-code.png":::

## <a name="edit-a-function"></a>Modificare una funzione
Modificare le proprietà o il codice di una funzione creando una nuova query, quindi passare il puntatore del mouse sul nome della funzione e selezionare **Carica codice funzione**. Apportare le modifiche desiderate al codice e selezionare **Salva** e quindi **Modifica dettagli funzione**. Apportare le modifiche desiderate alle proprietà e ai parametri della funzione prima di fare clic su **Salva**.

:::image type="content" source="media/functions/function-edit.png" alt-text="Funzione Edit" lightbox="media/functions/function-edit.png":::
## <a name="example"></a>Esempio
La funzione di esempio seguente restituisce tutti gli eventi nel log attività di Azure da una data specifica e che corrispondono a una determinata categoria. 

Iniziare con la query seguente usando valori hardcoded. In questo modo viene verificato che la query funzioni come previsto.

```Kusto
AzureActivity
| where CategoryValue == "Administrative"
| where TimeGenerated > todatetime("2021/04/05 5:40:01.032 PM")
```

:::image type="content" source="media/functions/example-query.png" alt-text="Funzione di esempio : query iniziale" lightbox="media/functions/example-query.png":::

Sostituire quindi i valori hardcoded con i nomi dei parametri e quindi salvare la funzione selezionando **Salva** e **quindi Salva come funzione**.

```Kusto
AzureActivity
| where CategoryValue == CategoryParam
| where TimeGenerated > DateParam
```

:::image type="content" source="media/functions/example-save-function.png" alt-text="Funzione di esempio - funzione save" lightbox="media/functions/example-save-function.png":::

 Specificare i valori seguenti per le proprietà della funzione.

| Proprietà | Valore |
|:---|:---|
| Nome della funzione | AzureActivityByCategory |
| Categoria legacy | Funzioni demo |

Definire i parametri seguenti prima di salvare la funzione.

| Type | Nome | Valore predefinito |
|:---|:---|:---|
| string   | CategoryParam | "Administrative" |
| Datetime | DateParam     | |

:::image type="content" source="media/functions/example-function-properties.png" alt-text="Funzione di esempio : proprietà della funzione" lightbox="media/functions/example-function-properties.png":::

Creare una nuova query e visualizzare la nuova funzione passando il puntatore del mouse su di essa. Si noti l'ordine dei parametri perché è l'ordine in cui devono essere specificati quando si usa la funzione .

:::image type="content" source="media/functions/example-view-details.png" alt-text="Funzione di esempio: visualizzare i dettagli" lightbox="media/functions/example-view-details.png":::

Selezionare **Usa nell'editor** per aggiungere la nuova funzione a una query e quindi aggiungere i valori per i parametri. Si noti che non è necessario specificare un valore per CategoryParam perché ha un valore predefinito.

:::image type="content" source="media/functions/example-use-function.png" alt-text="Funzione di esempio - usare la funzione" lightbox="media/functions/example-use-function.png":::



## <a name="next-steps"></a>Passaggi successivi
Vedere altre lezioni per la scrittura di query di log di Monitoraggio di Azure:

- [Operazioni con stringhe](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)

