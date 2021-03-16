---
title: Informazioni su Riconoscimento modulo
titleSuffix: Azure Cognitive Services
description: Il servizio Riconoscimento modulo di Azure consente di identificare ed estrarre coppie chiave/valore e dati di tabelle dai documenti del modulo, nonché di estrarre le informazioni principali da ricevute di vendita e biglietti da visita.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: elaborazione dati automatica, elaborazione di documenti, immissione automatica di dati, elaborazione di moduli
ms.openlocfilehash: fdd482a6b0d6ca53d99cd17076ccd9a3545f7879
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467339"
---
# <a name="what-is-form-recognizer"></a>Informazioni su Riconoscimento modulo

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Riconoscimento modulo di Azure è un servizio cognitivo che consente di creare software di elaborazione dati automatica tramite la tecnologia di Machine Learning. Il servizio identifica ed estrae testo, coppie chiave-valore, contrassegni di selezione, tabelle e strutture dai documenti, quindi restituisce dati strutturati che includono le relazioni nel file originale, rettangoli delimitatori, attendibilità e altro ancora. È possibile ottenere in modo rapido risultati accurati e su misura per il contenuto specifico, senza alcun intervento manuale impegnativo o competenze approfondite di data science. Usare Riconoscimento modulo per automatizzare l'immissione di dati nelle applicazioni e arricchire le funzionalità di ricerca di documenti.

Il sistema di riconoscimento dei moduli è costituito da modelli di elaborazione di documenti personalizzati, modelli predefiniti per fatture, ricevute, ID e biglietti da visita e il modello di layout. È possibile chiamare i modelli di Riconoscimento modulo usando un'API REST o SDK delle librerie client per ridurre la complessità e integrarli nel flusso di lavoro o nell'applicazione.

Riconoscimento modulo è costituito dai servizi seguenti:

* **[API Layout](#layout-api)** : estrarre testo, contrassegni di selezione e strutture di tabelle, oltre alle relative coordinate dei rettangoli delimitatori, dai documenti.
* **[Modelli personalizzati](#custom-models)** : estrarre testo, coppie chiave-valore, contrassegni di selezione e dati di tabelle dai moduli. Questi modelli vengono sottoposti a training con i dati dell'utente, quindi sono personalizzati per i suoi moduli.

* **[Modelli predefiniti](#prebuilt-models)** : Estrai i dati da tipi di documento univoci usando modelli predefiniti. Attualmente sono disponibili i modelli predefiniti seguenti

  * [Fatture](./concept-invoices.md)
  * [Scontrini](./concept-receipts.md)
  * [Biglietti da visita](./concept-business-cards.md)
  * [Schede di identificazione (ID)](./concept-identification-cards.md)

## <a name="try-it-out"></a>Provare questa operazione

Per provare il servizio Riconoscimento modulo, passare allo strumento dell'interfaccia utente di esempio online:
<!-- markdownlint-disable MD025 -->
<!-- markdownlint-disable MD024 -->

### <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)

> [!div class="nextstepaction"]
> [Provare Riconoscimento modulo](https://fott-preview.azurewebsites.net/)

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!div class="nextstepaction"]
> [Provare Riconoscimento modulo](https://fott.azurewebsites.net/)

---

Per provare il servizio Riconoscimento modulo, è necessario avere una sottoscrizione di Azure ([crearne una gratuita](https://azure.microsoft.com/free/cognitive-services)), oltre a un endpoint e a una chiave della [risorsa Riconoscimento modulo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer).

## <a name="layout-api"></a>API di layout

Riconoscimento modulo consente di estrarre testo, contrassegni di selezione e strutture di tabelle (i numeri di righe e colonne associate al testo) dai documenti tramite riconoscimento ottico dei caratteri (OCR) in alta definizione e con un modello di Deep Learning avanzato. Per altre informazioni, vedere la guida concettuale sul [layout](./concept-layout.md).

:::image type="content" source="./media/tables-example.jpg" alt-text="Esempi di tabelle" lightbox="./media/tables-example.jpg":::

## <a name="custom-models"></a>Modelli personalizzati

I modelli personalizzati di Riconoscimento modulo eseguono il training dei dati dell'utente e, per iniziare, sono sufficienti cinque moduli di input di esempio. Un modello di elaborazione documenti sottoposto a training può restituire dati strutturati che includono le relazioni nel documento del modulo originale. Dopo il training del modello, è possibile testarlo, ripeterne il training e infine usarlo per estrarre dati in modo affidabile da altri moduli in base alle proprie esigenze.

Quando si esegue il training di modelli personalizzati, sono disponibili le opzioni seguenti: training con dati etichettati e senza dati etichettati.

### <a name="train-without-labels"></a>Training senza etichette

Il riconoscimento del modulo usa l'apprendimento non supervisionato per comprendere il layout e le relazioni tra i campi e le voci nei form. Quando si inviano i moduli di input, l'algoritmo li raggruppa in base al tipo, individua quali chiavi e tabelle sono presenti e associa i valori alle chiavi e le voci alle tabelle. Per la formazione senza etichette non è necessaria l'etichettatura manuale dei dati o l'utilizzo intensivo di codice e manutenzione, quindi è consigliabile provare prima questo metodo.

Per suggerimenti su come raccogliere i documenti di training, vedere [Creare un set di dati di training](./build-training-data-set.md).

### <a name="train-with-labels"></a>Training con etichette

Quando si esegue il training con dati con etichetta, il modello usa l'apprendimento supervisionato per estrarre i valori di interesse, usando i moduli con etichetta forniti. I dati con etichetta generano modelli con prestazioni migliori e possono produrre modelli che funzionano con moduli o moduli complessi contenenti valori senza chiavi.

Il riconoscitore del modulo usa l' [API layout](#layout-api) per conoscere le dimensioni e le posizioni previste di elementi di testo stampati e scritti a mano ed estrarre le tabelle. USA quindi le etichette specificate dall'utente per apprendere le associazioni chiave/valore e le tabelle nei documenti. Per iniziare a eseguire il training di un nuovo modello, è consigliabile usare cinque moduli dello stesso tipo (stessa struttura) etichettati manualmente e aggiungere altri dati etichettati se necessario per migliorare l'accuratezza. Il riconoscimento del modulo consente il training di un modello per estrarre le coppie chiave-valore e le tabelle usando le funzionalità di apprendimento supervisionato. 

[Introduzione al training con le etichette](./quickstarts/label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prebuilt-models"></a>Modelli predefiniti

Riconoscimento modulo include anche modelli predefiniti per l'elaborazione dati automatica di tipi di moduli univoci.

### <a name="prebuilt-invoice-model"></a>Modello predefinito di fattura

Il modello di fattura predefinito estrae i dati dalle fatture in diversi formati e restituisce dati strutturati. Questo modello estrae informazioni chiave quali l'ID fattura, i dettagli del cliente, i dettagli del fornitore, la spedizione, la fatturazione, il totale, le imposte, il subtotale, le voci e altro ancora. Viene inoltre eseguito il training del modello di fattura predefinito per analizzare e restituire tutto il testo e le tabelle della fattura. Per altre informazioni, vedere la guida concettuale sulle [fatture](./concept-invoices.md).

:::image type="content" source="./media/overview-invoices.jpg" alt-text="Esempio di fattura" lightbox="./media/overview-invoices.jpg":::

### <a name="prebuilt-receipt-model"></a>Modello di ricevuta predefinito

Il modello di ricevuta predefinito viene usato per la lettura di ricevute di vendita in inglese di Australia, Canada, Gran Bretagna, India e Stati Uniti, il tipo usato da ristoranti, distributori di carburante, rivenditori e così via. Questo modello consente di estrarre le informazioni principali, come la data e l'ora della transazione, i dati del fornitore, gli importi delle imposte, gli articoli, i totali e altro ancora. Inoltre, il modello di ricezione predefinito viene sottoposto a training per analizzare e restituire tutto il testo in una ricevuta. Per altre informazioni, vedere [Concetti relativi alle ricevute](./concept-receipts.md).

:::image type="content" source="./media/overview-receipt.jpg" alt-text="ricevuta di esempio" lightbox="./media/overview-receipt.jpg":::

### <a name="prebuilt-identification-id-cards-model"></a>Modello di schede di identificazione (ID) predefinito

Il modello delle schede di identificazione (ID) consente di estrarre le informazioni chiave da passaporti globali e licenze per i driver statunitensi. Estrae i dati, ad esempio l'ID del documento, la data di nascita, la data di scadenza, il nome, il paese, l'area, la zona leggibile dal computer e altro ancora. Per altre informazioni, vedere la Guida concettuale sulle [schede di identificazione (ID)](./concept-identification-cards.md) .

:::image type="content" source="./media/overview-id.jpg" alt-text="scheda di identificazione di esempio" lightbox="./media/overview-id.jpg":::

### <a name="prebuilt-business-cards-model"></a>Modello di biglietto da visita predefinito

Il modello di biglietto da visita consente di estrarre informazioni quali il nome della persona, la posizione, la via e numero civico, l'indirizzo di posta elettronica, la società e i numeri di telefono dai biglietti da visita in inglese. Per altre informazioni, vedere [Concetti relativi ai biglietti da visita](./concept-business-cards.md).

:::image type="content" source="./media/overview-business-card.jpg" alt-text="biglietto da visita di esempio" lightbox="./media/overview-business-card.jpg":::

## <a name="get-started"></a>Introduzione

Usare lo strumento di riconoscimento moduli di esempio per provare il layout, i modelli precompilati e il training di un modello personalizzato per i documenti:  

### <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)

> [!div class="nextstepaction"]
> [Provare Riconoscimento modulo](https://fott-preview.azurewebsites.net/)

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!div class="nextstepaction"]
> [Provare Riconoscimento modulo](https://fott.azurewebsites.net/)

---
Seguire la [Guida introduttiva alla libreria client/API REST](./quickstarts/client-library.md) per iniziare l'estrazione dei dati dai documenti. È consigliabile usare il servizio gratuito mentre si acquisisce familiarità con questa tecnologia. Tenere presente che il numero di pagine gratuite è limitato a 500 al mese.

Per iniziare, è anche possibile usare gli esempi REST (GitHub) 

* Estrai testo, contrassegni di selezione e struttura della tabella da documenti
  * [Estrarre i dati del layout - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-layout.md)
* Eseguire il training di modelli personalizzati ed estrarre i dati dei moduli
  * [Training senza etichette - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md)
  * [Training con etichette - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)
* Estrarre dati dalle fatture
  * [Estrarre dati dalle fatture - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-invoices.md)
* Estrarre dati dalle ricevute di vendita
  * [Estrarre dati dalle ricevute - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-receipts.md)
* Estrarre dati dai biglietti da visita
  * [Estrarre dati dai biglietti da visita - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-business-cards.md)

### <a name="review-the-rest-apis"></a>Esaminare le API REST

Per eseguire il training dei modelli ed estrarre dati strutturati dai moduli, verranno usate le API seguenti.

|Nome |Descrizione |
|---|---|
| **Analyze Layout** | Analizzare un documento passato come flusso per estrarre il testo, i contrassegni di selezione, le tabelle e la struttura dal documento |
| **Eseguire il training di un modello personalizzato**| Eseguire il training di un nuovo modulo per analizzare i moduli usando cinque moduli dello stesso tipo. Impostare il parametro _useLabelFile_ su `true` per eseguire il training con dati etichettati manualmente. |
| **Analyze Form** |Analizzare un modulo passato come flusso per estrarre testo, coppie chiave-valore e tabelle dal modulo con un modello personalizzato.  |
| **Analyze Invoice** | Analizzare una fattura per estrarre informazioni chiave, tabelle e altro testo della fattura.|
| **Analyze Receipt** | Analizzare una ricevuta per estrarre informazioni chiave e altro testo.|
| **ID analisi** | Analizzare un documento della scheda ID per estrarre informazioni chiave e altro testo della carta di identificazione.|
| **Analyze Business Card** | Analizzare un biglietto da lavoro per estrarre le principali informazioni e il testo.|

### <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)

Per altre informazioni, esplorare la [documentazione di riferimento dell'API REST](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm). Se si ha familiarità con una versione precedente dell'API, vedere l'articolo [Novità](./whats-new.md) per informazioni sulle modifiche recenti.

### <a name="v20"></a>[v2.0](#tab/v2-0)

Per altre informazioni, esplorare la [documentazione di riferimento dell'API REST](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm). Se si ha familiarità con una versione precedente dell'API, vedere l'articolo [Novità](./whats-new.md) per informazioni sulle modifiche recenti.

---

## <a name="input-requirements"></a>Requisiti di input

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Distribuire in locale con i contenitori Docker

[Usare i contenitori di Riconoscimento modulo (anteprima)](form-recognizer-container-howto.md) per distribuire le funzionalità dell'API in locale. Questo contenitore Docker consente di rendere il servizio più vicino ai dati per la conformità, la sicurezza o altri motivi operativi.

## <a name="service-availability-and-redundancy"></a>Disponibilità e ridondanza del servizio

### <a name="is-form-recognizer-service-zone-resilient"></a>Il servizio Riconoscimento modulo offre resilienza della zona?

Sì. Il servizio Riconoscimento è con resilienza della zona per impostazione predefinita.

### <a name="how-do-i-configure-the-form-recognizer-service-to-be-zone-resilient"></a>Come si configura il servizio Riconoscimento modulo in modo da abilitare la resilienza della zona?

Non è necessaria alcuna configurazione del cliente per abilitare la resilienza della zona. La resilienza della zona per le risorse di Riconoscimento modulo è disponibile per impostazione predefinita e viene gestita dal servizio stesso.

## <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati

Come per tutti i servizi cognitivi, gli sviluppatori che usano il servizio di riconoscimento modulo devono conoscere la politica di Microsoft sui dati dei clienti. Per altre informazioni, vedere la [pagina sui Servizi cognitivi](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) nel Centro protezione di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul servizio di riconoscimento moduli, provare lo strumento online e la Guida introduttiva.

* [**Strumento riconoscimento moduli**](https://fott-preview.microsoft.com/)
* [**Guida introduttiva alla libreria client e all'API REST**](quickstarts/client-library.md)
