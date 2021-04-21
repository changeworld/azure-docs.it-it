---
title: Limiti - QnA Maker
description: QnA Maker prevede metalimiti per alcune parti della knowledge base e del servizio. Per poter eseguire test e pubblicare, è importate rispettare i limiti previsti per la knowledge base.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: ad498575b029f918538909a9b5b2d52c71c1389c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816369"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Limiti della knowledge base di QnA Maker

QnA Maker limiti indicati di seguito sono [](../../search/search-limits-quotas-capacity.md) una combinazione dei limiti Ricerca cognitiva di Azure del piano tariffario e dei QnA Maker [del piano tariffario.](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/) È necessario conoscere entrambi i set di limiti per comprendere il numero di Knowledge Base che è possibile creare per ogni risorsa e le dimensioni che ogni knowledge base può aumentare.

## <a name="knowledge-bases"></a>Knowledge Base

Il numero massimo di Knowledge Base è basato sui limiti Ricerca cognitiva di Azure [livello .](../../search/search-limits-quotas-capacity.md)

|**Ricerca cognitiva di Azure livello** | **Gratuito** | **Base** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Numero massimo di knowledge base pubblicate consentite|2|14|49|199|199|2999|

 Ad esempio, se il livello include 15 indici consentiti, è possibile pubblicare 14 articoli della knowledge base (1 indice per ogni articolo della knowledge base pubblicato). Il quindicesimo indice, `testkb`, viene usato per tutti gli articoli della knowledge base per la creazione e il testing.

## <a name="extraction-limits"></a>Limiti di estrazione

### <a name="file-naming-constraints"></a>Vincoli di denominazione dei file

I nomi dei file non possono includere i caratteri seguenti:

|Non usare caratteri|
|--|
|Virgoletta singola `'`|
|Virgolette doppie `"`|

### <a name="maximum-file-size"></a>Dimensione massima dei file

|Formato|Dimensioni massime del file (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Numero massimo di file

Il numero massimo di file che è possibile estrarre e le dimensioni massime dei file si basano sui limiti QnA Maker **[del piano tariffario](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)**.

> [!NOTE]
> QnA Maker gestito (anteprima) è un servizio gratuito senza limiti al numero di origini che è possibile aggiungere. La velocità effettiva è attualmente massima di 10 transazioni al secondo sia per le API di gestione che per le API di stima.

### <a name="maximum-number-of-deep-links-from-url"></a>Numero massimo di collegamenti diretti dall'URL

Il numero massimo di collegamenti diretti che possono essere sottoposti a ricerca per indicizzazione per l'estrazione di domande e risposte da una pagina URL è **20**.

## <a name="metadata-limits"></a>Limiti di metadati

I metadati vengono presentati come coppia chiave:valore basata su testo, ad esempio `product:windows 10` . Viene archiviato e confrontato in lettere minuscole.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Per Ricerca cognitiva di Azure piano tariffario

Il numero massimo di campi di metadati per knowledge base è basato sui limiti Ricerca cognitiva di Azure **[livello.](../../search/search-limits-quotas-capacity.md)**

|**Ricerca cognitiva di Azure livello** | **Gratuito** | **Base** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Numero massimo di campi di metadati per ogni servizio QnA Maker (attraverso tutte le knowledge base)|1\.000|100*|1\.000|1\.000|1\.000|1\.000|

### <a name="by-name-and-value"></a>Per nome e valore

La lunghezza e i caratteri accettabili per il nome e il valore dei metadati sono elencati nella tabella seguente.

|Elemento|Caratteri consentiti|Corrispondenza del modello regex|Numero massimo di caratteri|
|--|--|--|--|
|Nome (chiave)|Permette<br>alfanumerico (lettere e cifre)<br>`_` (carattere di sottolineatura)<br> Non deve contenere spazi.|`^[a-zA-Z0-9_]+$`|100|
|Valore|Consente tutto tranne<br>`:` (due punti)<br>`|` (barra verticale)<br>È consentito un solo valore.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Limiti di contenuto della Knowledge Base
Limiti complessivi sul contenuto nella knowledge base:
* Lunghezza del testo della risposta: 25.000 caratteri
* Lunghezza del testo della domanda: 1.000 caratteri
* Lunghezza del testo della chiave dei metadati: 100 caratteri
* Lunghezza del testo del valore dei metadati: 500 caratteri
* Caratteri supportati per il nome dei metadati: alfabeti, cifre e `_`
* Caratteri supportati per il valore dei metadati: tutti tranne `:` e `|`
* Lunghezza del nome file: 200
* Formati di file supportati: ".tsv", ".pdf", ".txt", ".docx", ".xlsx".
* Numero massimo di domande alternative: 300
* Numero massimo di coppie domanda-risposta: dipende dal **[livello Ricerca cognitiva di Azure scelto.](../../search/search-limits-quotas-capacity.md#document-limits)** Una coppia di domande e risposte esegue il mapping a un documento Ricerca cognitiva di Azure indice.
* Pagina URL/HTML: 1 milione di caratteri

## <a name="create-knowledge-base-call-limits"></a>Creare i limiti di chiamata per la Knowledge base:
Questi rappresentano i limiti per ogni azione di creazione di knowledge base; ovvero, fare clic su *Crea KB* o richiamare l'API di Crea Knowledge Base.
* Numero massimo consigliato di domande alternative per risposta: 300
* Numero massimo di URL: 10
* Numero massimo di file: 10
* Numero massimo di domande e risposte consentite per chiamata: 1000

## <a name="update-knowledge-base-call-limits"></a>Aggiornare i limiti di chiamata per la Knowledge base
Questi rappresentano i limiti per ogni azione di creazione di knowledge base; ovvero, fare clic su *Crea KB* o richiamare l'API di Crea Knowledge Base.
* Lunghezza di ogni nome di origine: 300
* Numero massimo consigliato di domande alternative aggiunte o eliminate: 300
* Numero massimo di campi di metadati aggiunti o eliminati: 10
* Numero massimo di URL che è possibile aggiornare: 5
* Numero massimo di domande e risposte consentite per chiamata: 1000

## <a name="add-unstructured-file-limits"></a>Aggiungere limiti per i file non strutturati

> [!NOTE]
> * Se è necessario usare file di dimensioni superiori al limite consentito, è possibile suddividere il file in file più piccoli prima di inviarli all'API. 

Questi rappresentano i limiti quando i file non strutturati vengono usati per creare *kb* o chiamare l'API CreateKnowledgeBase:
* Lunghezza del file: verranno estratti i primi 32000 caratteri
* Massimo 3 risposte per file.

## <a name="prebuilt-question-answering-limits"></a>Limiti predefiniti per la risposta alle domande

> [!NOTE]
> * Se è necessario usare documenti di dimensioni superiori al limite consentito, è possibile suddividere il testo in blocchi di testo più piccoli prima di inviarli all'API. 
> * Un documento è costituito da una singola stringa di caratteri di testo.  

Questi rappresentano i limiti quando si usa l'API precompilato per *generare una risposta* o chiamare l'API GenerateAnswer:
* Numero di documenti: 5
* Dimensioni massime di un singolo documento: 5.120 caratteri
* Massimo 3 risposte per documento.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su quando e come modificare i [piani tariffari dei servizi.](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku)
