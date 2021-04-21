---
title: Che cos'è l'analisi delle immagini?
titleSuffix: Azure Cognitive Services
description: Il servizio di analisi delle immagini usa modelli di intelligenza artificiale con training preliminare per estrarre molte caratteristiche visive diverse dalle immagini.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/30/2021
ms.author: pafarley
keywords: visione artificiale, applicazioni di visione artificiale, servizio visione artificiale
ms.openlocfilehash: 0258eb7c57bc0734b5c0a67644cbaa4f62a34537
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766916"
---
# <a name="what-is-image-analysis"></a>Che cos'è l'analisi delle immagini?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Il Visione artificiale di analisi delle immagini può estrarre un'ampia gamma di funzionalità visive dalle immagini. Ad esempio, può determinare se un'immagine contiene contenuto per adulti, trovare marchi o oggetti specifici o trovare visi umani.

È possibile usare l'analisi delle immagini tramite un SDK della libreria client o chiamando direttamente [l'API REST.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v2-ga/operations/5d986960601faab4bf452005) Seguire la [guida introduttiva](quickstarts-sdk/image-analysis-client-library.md) per iniziare.

Questa documentazione contiene i tipi di articoli seguenti:
* Le [guide introduttive](./quickstarts-sdk/image-analysis-client-library.md) sono istruzioni dettagliate che consentono di effettuare chiamate al servizio e ottenere risultati in un breve periodo di tempo. 
* Le [guide alle attività contengono](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) istruzioni per l'uso del servizio in modi più specifici o personalizzati.
* Gli [articoli concettuali](concept-tagging-images.md) forniscono spiegazioni approfondite delle funzionalità e delle funzionalità del servizio.
* Le [esercitazioni](./tutorials/storage-lab-tutorial.md) sono guide più lunghe che illustrano come usare questo servizio come componente in soluzioni aziendali più ampie.

## <a name="image-analysis-features"></a>Funzionalità di analisi delle immagini

È possibile analizzare le immagini per ricevere informazioni dettagliate sulle caratteristiche e gli aspetti visivi. Tutte le funzionalità nell'elenco seguente vengono fornite dall'API [Analizza immagine.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) Per iniziare, seguire una [guida di avvio rapido](./quickstarts-sdk/image-analysis-client-library.md).


### <a name="tag-visual-features"></a>Assegnare tag agli elementi visivi

Identificare e assegnare tag agli elementi visivi di un'immagine in base a un set di migliaia di oggetti riconoscibili, esseri viventi, panorami e azioni. Quando i tag sono ambigui o non di conoscenza comune, la risposta dell'API fornisce suggerimenti per chiarire il contesto del tag. L'assegnazione di tag non è limitata al soggetto principale, ad esempio una persona in primo piano, ma include anche scenari (interni o esterni), arredamenti, strumenti, piante, animali, accessori, gadget e così via. [Assegnare tag agli elementi visivi](concept-tagging-images.md)

### <a name="detect-objects"></a>Rilevare oggetti

Il rilevamento di oggetti è simile all'assegnazione di tag, ma l'API restituisce le coordinate del rettangolo di selezione per ogni tag applicato. Se, ad esempio, un'immagine contiene un cane, gatto e una persona, l'operazione di rilevamento elencherà tali oggetti insieme alle relative coordinate nell'immagine. È possibile usare questa funzionalità per elaborare ulteriormente le relazioni tra gli oggetti in un'immagine. Questa funzionalità consente anche di sapere quando sono presenti più istanze dello stesso tag in un'immagine. [Rilevare oggetti](concept-object-detection.md)

### <a name="detect-brands"></a>Rilevare marchi

È possibile identificare i marchi commerciali in immagini o video da un database di migliaia di logo globali. È ad esempio possibile usare questa funzionalità per individuare i marchi più popolari sui social media o quelli più prevalenti nel posizionamento dei prodotti multimediali. [Rilevare marchi](concept-brand-detection.md)

### <a name="categorize-an-image"></a>Classificare un'immagine

È possibile identificare e classificare un'intera immagine usando una [tassonomia di categorie](Category-Taxonomy.md) con gerarchie ereditarie di tipo padre/figlio. Le categorie possono essere usate singolarmente o con i nuovi modelli di assegnazione di tag.<br/>L'inglese è attualmente l'unica lingua supportata per l'assegnazione di tag e la classificazione di immagini. [Classificare un'immagine](concept-categorizing-images.md)

### <a name="describe-an-image"></a>Descrivere un'immagine

È possibile generare una descrizione di un'intera immagine in un linguaggio leggibile dall'utente, con frasi complete. Gli algoritmi di Visione artificiale generano descrizioni diverse in base agli oggetti identificati nell'immagine. Tutte le descrizioni vengono valutate e per ognuna viene generato un punteggio di attendibilità. Viene quindi restituito un elenco dei punteggi di attendibilità in ordine decrescente. [Descrivere un'immagine](concept-describing-images.md)

### <a name="detect-faces"></a>Rilevare visi

È possibile rilevare visi in un'immagine e fornire informazioni su ogni viso rilevato. Visione artificiale restituisce le coordinate, un rettangolo, il sesso e l'età per ogni viso rilevato.<br/>Visione artificiale offre un subset delle funzionalità del servizio [Viso](../face/index.yml). È possibile usare il servizio Viso per analisi più dettagliate, come il riconoscimento facciale e il rilevamento della postura. [Rilevare visi](concept-detecting-faces.md)

### <a name="detect-image-types"></a>Rilevare i tipi di immagine

È possibile rilevare le caratteristiche di un'immagine, ad esempio per determinare se un'immagine è un disegno a linee o per ottenere la probabilità che un'immagine sia ClipArt. [Rilevare i tipi di immagine](concept-detecting-image-types.md)

### <a name="detect-domain-specific-content"></a>Rilevare contenuti specifici di dominio

È possibile usare modelli di dominio per rilevare e identificare contenuti specifici del dominio in un'immagine, ad esempio celebrità e luoghi di interesse. Se ad esempio un'immagine contiene persone, Visione artificiale può usare un modello di dominio per le celebrità per determinare se le persone rilevate nell'immagine corrispondono a celebrità note. [Rilevare contenuti specifici del dominio](concept-detecting-domain-content.md)

### <a name="detect-the-color-scheme"></a>Rilevare la combinazione di colori

È possibile analizzare l'utilizzo dei colori in un'immagine. Visione artificiale può determinare se un'immagine è in bianco e nero o a colori e nel caso delle immagini a colore può identificare il colore dominante e i colori in primo piano. [Rilevare la combinazione di colori](concept-detecting-color-schemes.md)

### <a name="generate-a-thumbnail"></a>Generare un'anteprima

È possibile analizzare i contenuti di un'immagine per generare un'anteprima appropriata per tale immagine. Visione artificiale genera prima di tutto un'anteprima di qualità elevata e quindi analizza gli oggetti inclusi nell'immagine per determinare l'*area di interesse*. L'immagine viene quindi ritagliata per soddisfare i requisiti dell'area di interesse. L'anteprima generata può essere visualizzata con proporzioni diverse da quelle dell'immagine originale, in base alle esigenze specifiche. [Generare un'anteprima](concept-generating-thumbnails.md)

### <a name="get-the-area-of-interest"></a>Ottenere l'area di interesse

Analizzare il contenuto di un'immagine per restituire le coordinate dell'*area di interesse*. Invece di ritagliare l'immagine e generare un'anteprima, Visione artificiale restituisce le coordinate del rettangolo di selezione dell'area, in modo che l'applicazione chiamante possa modificare l'immagine originale in base alle esigenze. [Ottenere l'area di interesse](concept-generating-thumbnails.md#area-of-interest)

## <a name="moderate-content-in-images"></a>Moderare i contenuti nelle immagini

È possibile usare Visione artificiale per [rilevare i contenuti per adulti](concept-detecting-adult-content.md) in un'immagine e restituire punteggi di attendibilità per le diverse classificazioni. La soglia per contrassegnare i contenuti può essere impostata tramite un indicatore di scorrimento in base alle preferenze dell'utente.

## <a name="image-requirements"></a>Requisiti dell'immagine

L'analisi delle immagini funziona su immagini che soddisfano i requisiti seguenti:

- L'immagine deve essere presentata in formato JPEG, PNG, GIF o BMP
- Le dimensioni del file dell'immagine devono essere minori di 4 megabyte (MB)
- Le dimensioni dell'immagine devono essere superiori a 50 x 50 pixel

## <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati

Come con tutti i Servizi cognitivi, gli sviluppatori che usano il servizio Visione artificiale devono conoscere i criteri di Microsoft sui dati dei clienti. Per altre informazioni, vedere la [pagina sui Servizi cognitivi](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) nel Centro protezione di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare l'analisi delle immagini, seguire la guida introduttiva nel linguaggio di sviluppo preferito:

- [Avvio rapido: API REST o librerie client di Visione artificiale](./quickstarts-sdk/image-analysis-client-library.md)
