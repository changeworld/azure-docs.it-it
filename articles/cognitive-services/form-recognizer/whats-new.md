---
title: Novità di Riconoscimento modulo
titleSuffix: Azure Cognitive Services
description: Informazioni sulle modifiche più recenti all'API riconoscimento modulo.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2021
ms.author: lajanuar
ms.openlocfilehash: e952d481daf53b1806dc3cfbb658c8c0c21f6984
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516298"
---
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD036 -->
# <a name="whats-new-in-form-recognizer"></a>Novità di Riconoscimento modulo

Il riconoscimento modulo viene aggiornato regolarmente. Usare questo articolo per rimanere aggiornati sui miglioramenti delle funzionalità, le correzioni e gli aggiornamenti della documentazione.

## <a name="april-2021"></a>Aprile 2021
<!-- markdownlint-disable MD029 -->

### <a name="sdk-updates-api--version-21-preview3"></a>Aggiornamenti dell'SDK (API versione 2.1-preview.3)

### <a name="c-version-310-beta4"></a>**C# versione 3.1.0-beta.4**

* **Nuovi metodi per analizzare i dati dai documenti di identità:**

   **[StartRecognizeIdDocumentsFromUriAsync](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizeiddocumentsasync?view=azure-dotnet-preview&preserve-view=true)**

   **[StartRecognizeIdDocumentsAsync](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizeiddocumentsasync?view=azure-dotnet-preview&preserve-view=true)**

   Per un elenco dei valori dei campi, _vedere_ [Campi estratti nella](concept-identification-cards.md#fields-extracted) documentazione riconoscimento modulo.

* È stato esteso il set di lingue del documento che possono essere fornite al **[metodo StartRecognizeContent.](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecontent?view=azure-dotnet-preview&preserve-view=true)**

* **Nuova proprietà `Pages` supportata dalle classi seguenti:**

   **[RecognizeBusinessCardsOptions](/dotnet/api/azure.ai.formrecognizer.recognizebusinesscardsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeCustomFormsOptions](/dotnet/api/azure.ai.formrecognizer.recognizecustomformsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeInvoicesOptions](/dotnet/api/azure.ai.formrecognizer.recognizeinvoicesoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeReceiptsOptions](/dotnet/api/azure.ai.formrecognizer.recognizereceiptsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>

   La proprietà consente di selezionare singole pagine o un intervallo di pagine per documenti PDF e `Pages` TIFF a più pagine. Per le singole pagine, immettere il numero di pagina, ad esempio `3` . Per un intervallo di pagine,ad esempio la pagina 2 e le pagine 5-7, immettere i numeri di età p e gli intervalli separati da virgole: `2, 5-7` .    

* **Nuova proprietà `ReadingOrder` supportata per la classe seguente:**

   **[RecognizeContentOptions](/dotnet/api/azure.ai.formrecognizer.recognizecontentoptions?view=azure-dotnet-preview&preserve-view=true)**

  La proprietà è un parametro facoltativo che consente di specificare quale algoritmo dell'ordine di lettura, o , deve essere applicato per ordinare `ReadingOrder` `basic` `natural` l'estrazione di elementi di testo. Se non è specificato, il valore predefinito è `basic`.

#### <a name="breaking-changes"></a>Modifiche che causano un'interruzione

* Per impostazione predefinita, il client è la versione del servizio supportata più recente, attualmente **2.1-preview.3.**

* **[Il metodo StartRecognizeCustomForms](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecustomforms?view=azure-dotnet-preview&preserve-view=true#Azure_AI_FormRecognizer_FormRecognizerClient_StartRecognizeCustomForms_System_String_System_IO_Stream_Azure_AI_FormRecognizer_RecognizeCustomFormsOptions_System_Threading_CancellationToken_)** ora genera un'eccezione `RequestFailedException()` quando viene passato un file non valido.

### <a name="java-version-310-beta3"></a>**Java versione 3.1.0-beta.3**

* **Nuovi metodi per analizzare i dati dai documenti di identità:**

  **[beginRecognizeIdDocumentsFromUrl](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizeiddocumentsfromurl?view=azure-java-preview&preserve-view=true)**

  **[beginRecognizeIdDocuments](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizeiddocuments?view=azure-java-preview&preserve-view=true)**

   Per un elenco dei valori dei campi, _vedere_ [Campi estratti nella](concept-identification-cards.md#fields-extracted) documentazione riconoscimento modulo.

* **Supporto del file di immagine bitmap (con estensione bmp) per i moduli personalizzati e i metodi di training nell'enumerazione `FormContentType`**:

* `image/bmp`

* **Nuova proprietà `Pages` supportata dalle classi seguenti:**

   **[RecognizeBusinessCardsOptions](/java/api/com.azure.ai.formrecognizer.models.recognizebusinesscardsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeCustomFormOptions](/java/api/com.azure.ai.formrecognizer.models.recognizecustomformsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeInvoicesOptions](/java/api/com.azure.ai.formrecognizer.models.recognizeinvoicesoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeReceiptsOptions](/java/api/com.azure.ai.formrecognizer.models.recognizereceiptsoptions?view=azure-java-preview&preserve-view=true)**</br>

  La proprietà consente di selezionare singole pagine o un intervallo di pagine per documenti PDF e `Pages` TIFF a più pagine. Per le singole pagine, immettere il numero di pagina, ad esempio `3` . Per un intervallo di pagine,ad esempio la pagina 2 e le pagine 5-7, immettere i numeri di pagina e gli intervalli separati da virgole: `2, 5-7` .

* Supporto del file di immagine bitmap (con estensione bmp) per i moduli personalizzati e i metodi **di training nei campi [FormContentType](/java/api/com.azure.ai.formrecognizer.models.formcontenttype?view=azure-java-preview&preserve-view=true#fields)**:

  `image/bmp`

* **Nuovo argomento di parola `ReadingOrder` chiave supportato per i metodi seguenti:**

* **[beginRecognizeContent](https://docs.microsoft.com/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontent?view=azure-java-preview&preserve-view=true)**</br>
**[beginRecognizeContentFromUrl](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontentfromurl?view=azure-java-preview&preserve-view=true)**</br>

   L'argomento della parola chiave è un parametro facoltativo che consente di specificare quale algoritmo dell'ordine di lettura, o , deve essere applicato per ordinare `ReadingOrder` `basic` `natural` l'estrazione di elementi di testo. Se non è specificato, il valore predefinito è `basic`.

* Per impostazione predefinita, il client è la versione del servizio supportata più recente, che attualmente **è 2.1-preview.3.**

### <a name="javascript-version-310-beta3"></a>**JavaScript versione 3.1.0-beta.3**

* **Nuovi metodi per analizzare i dati dai documenti di identità:**

    **[beginRecognizeIdDocumentsFromUrl](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocumentsFromUrl_string__BeginRecognizeIdDocumentsOptions_)**

    **[beginRecognizeIdDocuments](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocuments_FormRecognizerRequestBody__BeginRecognizeIdDocumentsOptions_)**

    Per un elenco dei valori dei campi, _vedere_ [Campi estratti nella](concept-identification-cards.md#fields-extracted) documentazione riconoscimento modulo.

* **Nuovi valori di campo aggiunti all'interfaccia FieldValue:**

    `gender`: i valori possibili sono `M` `F` o `X` .</br>
   `country`: i valori possibili seguono la stringa di codice [iso alfa-3](https://www.iso.org/obp/ui/#search) di tre lettere del codice paese.

* **Nuova opzione `pages` supportata da tutti i metodi di riconoscimento modulo (moduli personalizzati e tutti i modelli predefiniti). L'argomento consente di selezionare singole pagine o un intervallo di pagine per documenti PDF e TIFF a più pagine. Per le singole pagine, immettere il numero di pagina, ad esempio `3` . Per un intervallo di pagine,ad esempio la pagina 2 e le pagine da 5 a 7, immettere i numeri di pagina e gli intervalli separati da virgole: `2, 5-7` .

* Aggiunta del supporto per **[un tipo ReadingOrder](/javascript/api/@azure/ai-form-recognizer/readingorder?view=azure-node-preview&preserve-view=true)** ai metodi di riconoscimento del contenuto. Questa opzione consente di controllare l'algoritmo utilizzato dal servizio per determinare la modalità di ordinamento delle righe di testo riconosciute. È possibile specificare quale algoritmo dell'ordine di lettura, o , deve essere `basic` `natural` applicato per ordinare l'estrazione degli elementi di testo. Se non è specificato, il valore predefinito è `basic`.

* Suddividere **[il tipo FormField](/javascript/api/@azure/ai-form-recognizer/formfield?view=azure-node-preview&preserve-view=true)** in diverse interfacce. Ciò non deve causare problemi di compatibilità dell'API tranne in determinati casi limite (valueType non definito).

* È stata eseguita la migrazione all'endpoint di servizio **riconoscimento modulo 2.1-preview.3** per tutte le chiamate API REST.

### <a name="python-version--310b4"></a>**Python versione 3.1.0b4**

* **Nuovi metodi per analizzare i dati dai documenti di identità:**

  **[begin_recognize_id_documents_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  **[begin_recognize_id_documents](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  Per un elenco dei valori di campo, _vedere_ [Campi estratti](concept-identification-cards.md#fields-extracted) nella documentazione riconoscimento modulo.

* **Nuovi valori di campo aggiunti [all'enumerazione FieldValueType](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.fieldvaluetype?view=azure-python-preview&preserve-view=true)**:

   gender: i valori possibili sono `M` `F` o `X` .

  country: i valori possibili seguono [i codici PAESE ISO alfa-3.](https://www.iso.org/obp/ui/#search)

* Supporto di file di immagine bitmap (con estensione bmp) per moduli personalizzati e metodi **di training [nell'enumerazione FormContentType](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formcontenttype?view=azure-python-preview&preserve-view=true)**:

    image/bmp

* **Nuovo argomento della `pages` parola chiave supportato dai metodi seguenti:**

    **[begin_recognize_receipts](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true&branch=main#begin-recognize-receipts-receipt----kwargs-)**

    **[begin_recognize_receipts_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-receipts-from-url-receipt-url----kwargs-)**

   **[begin_recognize_business_cards](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-business-card----kwargs-)**

   **[begin_recognize_business_cards_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-from-url-business-card-url----kwargs-)**

   **[begin_recognize_invoices](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-invoice----kwargs-)**

   **[begin_recognize_invoices_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-from-url-invoice-url----kwargs-)**

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

  **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   L'argomento della parola chiave consente di selezionare singole pagine o un intervallo di pagine per documenti PDF e `pages` TIFF a più pagine. Per le singole pagine, immettere il numero di pagina, ad esempio `3` . Per un intervallo di pagine,ad esempio la pagina 2 e le pagine da 5 a 7, immettere i numeri di pagina e gli intervalli separati da virgole: `2, 5-7` .

* **Nuovo argomento di parola `readingOrder` chiave supportato per i metodi seguenti:**

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

   **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   L'argomento della parola chiave è un parametro facoltativo che consente di specificare quale algoritmo dell'ordine di lettura, o , deve essere applicato per ordinare `readingOrder` `basic` `natural` l'estrazione di elementi di testo. Se non è specificato, il valore predefinito è `basic`.

## <a name="march-2021"></a>Marzo 2021

**riconoscimento modulo è ora disponibile l'anteprima pubblica 3 della versione 2.1.** È stata rilasciata la versione 2.1-preview.3, incluse le funzionalità seguenti:

* **Nuovo modello di ID predefinito** Il nuovo modello di ID predefinito consente ai clienti di prendere GLI ID e restituire dati strutturati per automatizzare l'elaborazione. Combina le potenti funzionalità di riconoscimento ottico dei caratteri (OCR) con modelli di riconoscimento id per estrarre informazioni chiave dai passaporti e dalle licenze di driver degli Stati Uniti, ad esempio nome, data di nascita, data di emissione, data di scadenza e altro ancora.

  [Altre informazioni sul modello ID predefinito](concept-identification-cards.md)

   :::image type="content" source="./media/id-canada-passport-example.png" alt-text="Esempio passport" lightbox="./media/id-canada-passport-example.png":::

* **Estrazione di voci per il modello di fattura predefinito:** il modello di fattura predefinito supporta ora l'estrazione di voci. estrae ora gli elementi completi e le relative parti: descrizione, importo, quantità, ID prodotto, data e altro ancora. Con una semplice chiamata API/SDK, è possibile estrarre dati utili dalle fatture, ad esempio testo, tabella, coppie chiave-valore e voci.

   [Altre informazioni sul modello di fattura predefinito](concept-invoices.md)

* Etichettatura e training di tabelle con **supervisione,** etichettatura con valori vuoti: oltre alle funzionalità di estrazione automatica delle tabelle di Deep [Learning](https://techcommunity.microsoft.com/t5/azure-ai/enhanced-table-extraction-from-documents-with-form-recognizer/ba-p/2058011)all'avanguardia di riconoscimento modulo, ora consente ai clienti di etichettare ed eseguire il training sulle tabelle. Questa nuova versione include la possibilità di etichettare ed eseguire il training su voci/tabelle (dinamiche e fisse) ed eseguire il training di un modello personalizzato per estrarre coppie chiave-valore ed elementi riga. Dopo il training di un modello, il modello estrarrà le voci come parte dell'output JSON nella sezione documentResults.

    :::image type="content" source="./media/table-labeling.png" alt-text="Etichettatura delle tabelle" lightbox="./media/table-labeling.png":::

    Oltre alle tabelle di etichettatura, è ora possibile etichettare valori e aree vuoti. Se alcuni documenti nel set di training non hanno valori per determinati campi, è possibile etichettarli in modo che il modello sappia di estrarre correttamente i valori dai documenti analizzati.

* **Supporto per 66 nuove lingue:** riconoscimento modulo'API Layout di Riconoscimento modulo e i modelli personalizzati supportano ora 73 lingue.

  [Altre informazioni sul riconoscimento modulo linguistico di riconoscimento modulo](language-support.md)

* Ordine di lettura **naturale,** classificazione della grafia e selezione della pagina: con questo aggiornamento è possibile scegliere di ottenere gli output delle righe di testo nell'ordine di lettura naturale invece dell'ordinamento predefinito da sinistra a destra e dall'alto verso il basso. Usare il nuovo parametro di query readingOrder e impostarlo sul valore "natural" per un output dell'ordine di lettura più semplice. Inoltre, per le lingue latini, riconoscimento modulo le righe di testo come stile scritto a mano o meno e assegnano un punteggio di attendibilità.

* **Miglioramenti predefiniti della qualità del modello di ricevuta** Questo aggiornamento include numerosi miglioramenti qualitativi per il modello di ricezione predefinito, in particolare per l'estrazione di voci.

## <a name="november-2020"></a>Novembre 2020

### <a name="new-features"></a>Nuove funzionalità

**riconoscimento modulo'anteprima pubblica 2.1 2 è ora disponibile.** È stata rilasciata la versione 2.1-preview.2, incluse le funzionalità seguenti:

- **Nuovo modello di fattura predefinito:** il nuovo modello di fattura predefinito consente ai clienti di prendere fatture in vari formati e restituire dati strutturati per automatizzare l'elaborazione della fattura. Combina le potenti funzionalità OCR (Optical Character Recognition) con i modelli di deep learning per la comprensione delle fatture per estrarre informazioni chiave dalle fatture in inglese. Estrae il testo chiave, le tabelle e le informazioni, ad esempio cliente, fornitore, ID fattura, data di scadenza della fattura, totale, importo dovuto, importo fiscale, spedizione a e fatturazione.

  > [Altre informazioni sul modello di fattura predefinito](concept-invoices.md)

  :::image type="content" source="./media/invoice-example.jpg" alt-text="esempio di fattura" lightbox="./media/invoice-example.jpg":::

- **Estrazione avanzata** di tabelle: riconoscimento modulo ora offre un'estrazione avanzata delle tabelle, che combina le potenti funzionalità di riconoscimento ottico dei caratteri (OCR) con un modello di estrazione di tabelle di Deep Learning. riconoscimento modulo è possibile estrarre dati da tabelle, incluse tabelle complesse con colonne unite, righe, bordi e altro ancora.

  :::image type="content" source="./media/tables-example.jpg" alt-text="Esempi di tabelle" lightbox="./media/tables-example.jpg":::


  > [Altre informazioni sull'estrazione del layout](concept-layout.md)

- **Aggiornamento della libreria client:** le versioni più recenti delle librerie [client](quickstarts/client-library.md) per .NET, Python, Java e JavaScript supportano l'API riconoscimento modulo 2.1.
- **Nuova lingua supportata: giapponese:** sono ora supportate le nuove lingue seguenti: `AnalyzeLayout` per e : giapponese ( `AnalyzeCustomForm` `ja` ). [Lingue supportate](language-support.md)
- Indicazione dello stile della riga di testo (solo scritte a **mano/altro)** - riconoscimento modulo ora restituisce un oggetto che classifica se ogni riga di testo è di tipo scritto a mano o meno, insieme a un punteggio di `appearance` attendibilità. Questa funzionalità è supportata solo per le lingue latini.
- **Miglioramenti della qualità: miglioramenti** dell'estrazione, inclusi i miglioramenti dell'estrazione a cifra singola.
- Nuova funzionalità di prova nell'esempio **di riconoscimento modulo** e nello strumento di etichettatura: possibilità di provare i modelli predefiniti di fattura, ricevuta e biglietto da visita e l'API layout usando lo strumento di etichettatura di esempio riconoscimento modulo. Vedere come verranno estratti i dati senza scrivere codice.

  > [Provare lo strumento riconoscimento modulo di esempio](https://fott-preview.azurewebsites.net/)

  ![Esempio di FOTT](./media/ui-preview.jpg)

- **Ciclo di feedback:** quando si analizzano i file tramite lo strumento di etichettatura dei campioni, è ora anche possibile aggiungerli al set di training e, se necessario, modificare le etichette ed eseguire il training per migliorare il modello.
- **Etichetta automaticamente i documenti:** etichetta automaticamente documenti aggiuntivi in base ai documenti etichettati in precedenza nel progetto.

## <a name="august-2020"></a>Agosto 2020

### <a name="new-features"></a>Nuove funzionalità

**riconoscimento modulo è ora disponibile l'anteprima pubblica v2.1.** È stata rilasciata la versione 2.1-preview.1, incluse le funzionalità seguenti:


- **Informazioni di riferimento sull'API REST** sono disponibili- Visualizzare le informazioni di riferimento [sulla versione 2.1-preview.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync)
- Nuove lingue supportate Oltre all'inglese, sono ora supportate le lingue seguenti: per e : inglese ( ), cinese  [](language-support.md) `Layout` `Train Custom Model` `en` (semplificato) ( ), olandese ( ), francese ( ), tedesco ( ), italiano ( ), portoghese ( ) e `zh-Hans` `nl` `fr` `de` `it` `pt` spagnolo ( `es` ).
- **Rilevamento della casella di controllo/del** segno di riconoscimento modulo supporta il rilevamento e l'estrazione dei segni di selezione, ad esempio caselle di controllo e pulsanti di opzione. I segni di selezione vengono estratti in ed è ora anche possibile etichettare ed eseguire il training in Train with Labels (Training con etichette) per estrarre coppie `Layout` `Train Custom Model`  -   chiave-valore per i segni di selezione.
- **Composizione modello:** consente di comporre e chiamare più modelli con un singolo ID modello. Quando si invia un documento da analizzare con un ID modello composto, viene innanzitutto eseguito un passaggio di classificazione per instradare il documento al modello personalizzato corretto. Model Compose è disponibile per `Train Custom Model`  -  _Il training con etichette_.
- **Nome modello:** aggiungere un nome descrittivo ai modelli personalizzati per semplificare la gestione e il rilevamento.
- **[Nuovo modello predefinito per i biglietti da visita per](concept-business-cards.md)** l'estrazione di campi comuni in biglietti da visita in lingua inglese.
- **[Nuove impostazioni locali per](concept-receipts.md)** le ricevute predefinite oltre a EN-US, il supporto è ora disponibile per EN-AU, EN-CA, EN-GB, EN-IN
- **Miglioramenti della qualità** per `Layout` , Eseguire il training senza `Train Custom Model`  -  _etichette_ _ed eseguire il training con le etichette_.

**La versione 2.0** include l'aggiornamento seguente:

- Le [librerie client](quickstarts/client-library.md) per NET, Python, Java e JavaScript hanno immesso Disponibilità generale.

**Nuovi esempi** sono disponibili in GitHub.

- Il [playbook Knowledge Extraction Recipes - Forms](https://github.com/microsoft/knowledge-extraction-recipes-forms) raccoglie le procedure consigliate dagli engagement reali dei clienti riconoscimento modulo e fornisce esempi di codice, elenchi di controllo e pipeline di esempio utilizzabili per lo sviluppo di questi progetti.
- Lo [strumento di etichettatura di esempio](https://github.com/microsoft/OCR-Form-Tools) è stato aggiornato per supportare la nuova funzionalità v2.1. Vedere questa [guida introduttiva](quickstarts/label-tool.md) per iniziare a usare lo strumento.
- [L'esempio di riconoscimento modulo](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md) chiosco multimediale intelligente illustra come integrare ed eseguire il training senza `Analyze Receipt` `Train Custom Model`  -  _etichette._

## <a name="july-2020"></a>Luglio 2020

### <a name="new-features"></a>Nuove funzionalità
<!-- markdownlint-disable MD004 -->
* **Informazioni di riferimento sulla versione 2.0** disponibili: vedere le informazioni di riferimento sulle [API v2.0](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) e gli SDK aggiornati [per .NET,](/dotnet/api/overview/azure/ai.formrecognizer-readme) [Python,](/python/api/overview/azure/) [Java](/java/api/overview/azure/ai-formrecognizer-readme)e [JavaScript.](/javascript/api/overview/azure/)
* **Miglioramenti delle tabelle e Estrazione:** include miglioramenti per l'accuratezza ed estrazioni di tabelle, in particolare la possibilità di apprendere le intestazioni e le strutture delle tabelle nel training personalizzato senza _etichette._

* **Supporto valuta** : rilevamento ed estrazione di simboli di valuta globali.
* **Azure Gov:** riconoscimento modulo è ora disponibile anche in Azure Gov.
* **Funzionalità di sicurezza avanzate:**
  * **Bring Your Own Key:** riconoscimento modulo crittografa automaticamente i dati quando vengono resi persistenti nel cloud per proteggerli e per soddisfare gli impegni di sicurezza e conformità dell'organizzazione. Per impostazione predefinita, la sottoscrizione usa chiavi di crittografia gestite da Microsoft. È ora possibile gestire la sottoscrizione anche con le proprie chiavi di crittografia. Le chiavi gestite dal cliente, note anche come [BYOK (Bring Your Own Key),](./encrypt-data-at-rest.md)offrono maggiore flessibilità per creare, ruotare, disabilitare e revocare i controlli di accesso. È anche possibile controllare le chiavi di crittografia usate per proteggere i dati.
  * **Endpoint privati: consente** a una rete virtuale di accedere in modo sicuro ai [dati tramite un collegamento privato.](../../private-link/private-link-overview.md)

## <a name="june-2020"></a>Giugno 2020

### <a name="new-features"></a>Nuove funzionalità

* **API CopyModel aggiunta agli SDK client:** è ora possibile usare gli SDK client per copiare modelli da una sottoscrizione a un'altra. Per [informazioni generali su questa funzionalità,](./disaster-recovery.md) vedere Backup e ripristino di modelli.
* **Azure Active Directory integrazione:** è ora possibile usare le credenziali Azure AD per autenticare gli riconoscimento modulo client negli SDK.
* **Modifiche specifiche dell'SDK:** questa modifica include sia le aggiunte di funzionalità secondarie che le modifiche di rilievo. Per altre informazioni, vedere i log delle modifiche dell'SDK.
  * [Log delle modifiche di C# SDK Preview 3](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Log delle modifiche di Python SDK Preview 3](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Log delle modifiche di Java SDK Preview 3](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Log delle modifiche di JavaScript SDK Preview 3](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>Aprile 2020

### <a name="new-features"></a>Nuove funzionalità

* **Supporto DELL'SDK riconoscimento modulo'anteprima pubblica dell'API v2.0:** questo mese è stato esteso il supporto del servizio per includere un SDK di anteprima per la versione riconoscimento modulo v2.0 (anteprima). Usare i collegamenti seguenti per iniziare a usare il linguaggio preferito:
  * [.NET SDK](/dotnet/api/overview/azure/ai.formrecognizer-readme)
  * [SDK per Java](/java/api/overview/azure/ai-formrecognizer-readme)
  * [Python SDK](/python/api/overview/azure/ai-formrecognizer-readme)
  * [JavaScript SDK](/javascript/api/overview/azure/ai-form-recognizer-readme)

  Il nuovo SDK supporta tutte le funzionalità dell'API REST v2.0 per riconoscimento modulo. Ad esempio, è possibile eseguire il training di un modello con o senza etichette ed estrarre testo, coppie chiave-valore e tabelle dai moduli, estrarre i dati dalle ricevute con il servizio di ricevute predefinito ed estrarre testo e tabelle con il servizio di layout dai documenti. È possibile condividere commenti e suggerimenti sugli SDK tramite il modulo [sdk Feedback](https://aka.ms/FR_SDK_v1_feedback).

* **Copiare un modello personalizzato** È ora possibile copiare modelli tra aree e sottoscrizioni usando la nuova funzionalità Copia modello personalizzato. Prima di richiamare l'API Copia modello personalizzato, è necessario ottenere l'autorizzazione per copiare nella risorsa di destinazione chiamando l'operazione di copia dell'autorizzazione sull'endpoint della risorsa di destinazione.

  * [Generare un'autorizzazione di copia](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization) REST API
  * [Copiare un modello personalizzato](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel) REST API

### <a name="security-improvements"></a>Miglioramenti della sicurezza

* Customer-Managed chiavi sono ora disponibili per FormRecognizer. Per altre informazioni, vedere [Crittografia dei dati in pausa per riconoscimento modulo](./encrypt-data-at-rest.md).
* Usare identità gestite per l'accesso alle risorse di Azure con Azure Active Directory. Per altre informazioni, vedere [Autorizzare l'accesso alle identità gestite.](../authentication.md#authorize-access-to-managed-identities)

## <a name="march-2020"></a>Marzo 2020

### <a name="new-features"></a>Nuove funzionalità

* **Tipi di valore per l'etichettatura** È ora possibile specificare i tipi di valori a cui si sta etichettando con lo riconoscimento modulo di etichettatura di esempio. Sono attualmente supportati i tipi di valore e le varianti seguenti:
  * `string`
    * predefinito, `no-whitespaces`, `alphanumeric`
  * `number`
    * predefinito, `currency`
  * `date`
    * predefinito, `dmy`, `mdy`, `ymd`
  * `time`
  * `integer`

  Per informazioni [su come usare questa funzionalità,](./quickstarts/label-tool.md#specify-tag-value-types) vedere La guida dello strumento di etichettatura di esempio.


* **Visualizzazione tabella** Lo strumento di etichettatura di esempio ora visualizza le tabelle riconosciute nel documento. Questa funzionalità consente di visualizzare le tabelle riconosciute ed estratte dal documento prima dell'etichettatura e dell'analisi. Questa funzionalità può essere attivata/disattivata usando l'opzione livelli.

  L'immagine seguente è un esempio di come le tabelle vengono riconosciute ed estratte:

  > [!div class="mx-imgBorder"]
  > ![Visualizzazione tabella con lo strumento di etichettatura di esempio](./media/whats-new/table-viz.png)

    Le tabelle estratte sono disponibili nell'output JSON in `"pageResults"` .

  > [!IMPORTANT]
  > L'etichettatura delle tabelle non è supportata. Se le tabelle non vengono riconosciute ed estraiate automaticamente, è possibile etichettarle solo come coppie chiave/valore. Quando si etichettano le tabelle come coppie chiave/valore, etichettare ogni cella come valore univoco.

### <a name="extraction-enhancements"></a>Miglioramenti dell'estrazione

Questa versione include miglioramenti dell'estrazione e dell'accuratezza, in particolare la possibilità di etichettare ed estrarre più coppie chiave/valore nella stessa riga di testo.

### <a name="sample-labeling-tool-is-now-open-source"></a>Lo strumento di etichettatura di esempio è ora open source

Lo riconoscimento modulo di etichettatura di esempio è ora disponibile come progetto open source. È possibile integrarlo all'interno delle soluzioni e apportare modifiche specifiche del cliente in base alle esigenze.

Per altre informazioni sullo strumento riconoscimento modulo di etichettatura di esempio, vedere la documentazione disponibile in [GitHub.](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)

### <a name="tls-12-enforcement"></a>Imposizione di TLS 1.2

TLS 1.2 viene ora applicato per tutte le richieste HTTP a questo servizio. Per altre informazioni, vedere [Sicurezza di Servizi cognitivi di Azure](../cognitive-services-security.md).

## <a name="january-2020"></a>Gennaio 2020

Questa versione introduce la versione riconoscimento modulo 2.0 (anteprima). Nelle sezioni seguenti sono disponibili altre informazioni su nuove funzionalità, miglioramenti e modifiche.

### <a name="new-features"></a>Nuove funzionalità

* **Modello personalizzato**
  * **Training con etichette** È ora possibile eseguire il training di un modello personalizzato con dati etichettati manualmente. Questo metodo produce modelli con prestazioni migliori e può produrre modelli che funzionano con forme complesse o moduli contenenti valori senza chiavi.
  * **API asincrona** È possibile usare chiamate API asincrone per eseguire il training con e analizzare file e set di dati di grandi dimensioni.
  * **Supporto dei file TIFF** È ora possibile eseguire il training con ed estrarre i dati dai documenti TIFF.
  * **Miglioramenti dell'accuratezza dell'estrazione**

* **Modello di ricevuta predefinito**
  * **Importi delle mancia** È ora possibile estrarre gli importi delle mancia e altri valori scritti a mano.
  * **Estrazione di voci** È possibile estrarre i valori delle voci dalle ricevute.
  * **Valori di attendibilità** È possibile visualizzare l'attendibilità del modello per ogni valore estratto.
  * **Miglioramenti dell'accuratezza dell'estrazione**

* **Estrazione del layout** È ora possibile usare l'API Layout per estrarre dati di testo e tabelle dai moduli.

### <a name="custom-model-api-changes"></a>Modifiche all'API del modello personalizzato

Tutte le API per il training e l'uso di modelli personalizzati sono state rinominate e alcuni metodi sincroni sono ora asincroni. Di seguito sono riportate le principali modifiche:

* Il processo di training di un modello è ora asincrono. Il training viene avviato tramite la chiamata API **/custom/models.** Questa chiamata restituisce un ID operazione, che è possibile passare in **custom/models/{modelID}** per restituire i risultati del training.
* L'estrazione di chiave/valore viene ora avviata dalla chiamata API **/custom/models/{modelID}/analyze.** Questa chiamata restituisce un ID operazione, che è possibile passare in **custom/models/{modelID}/analyzeResults/{resultID}** per restituire i risultati dell'estrazione.
* Gli ID operazione per l'operazione Train sono ora disponibili nell'intestazione **Location** delle risposte HTTP, non **nell'intestazione Operation-Location.**

### <a name="receipt-api-changes"></a>Modifiche all'API di ricezione

Le API per la lettura delle ricevute di vendita sono state rinominate.

* L'estrazione dei dati di ricezione viene ora avviata dalla chiamata API **/prebuilt/receipt/analyze.** Questa chiamata restituisce un ID operazione, che è possibile passare in **/prebuilt/receipt/analyzeResults/{resultID}** per restituire i risultati dell'estrazione.

### <a name="output-format-changes"></a>Modifiche al formato di output

Le risposte JSON per tutte le chiamate API hanno nuovi formati. Alcune chiavi e valori sono stati aggiunti, rimossi o rinominati. Vedere le guide introduttive per esempi dei formati JSON correnti.

## <a name="next-steps"></a>Passaggi successivi

Completare una [guida introduttiva](quickstarts/client-library.md) per iniziare a scrivere un'app per l'elaborazione di moduli con riconoscimento modulo nel linguaggio di sviluppo preferito.

## <a name="see-also"></a>Vedere anche

* [Informazioni su Riconoscimento modulo](./overview.md)