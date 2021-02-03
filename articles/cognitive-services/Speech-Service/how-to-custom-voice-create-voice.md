---
title: Creazione di un servizio di riconoscimento vocale personalizzato
titleSuffix: Azure Cognitive Services
description: Quando si è pronti per caricare i dati, passare al portale vocale personalizzato. Creare o selezionare un progetto Voice personalizzato. Il progetto deve condividere la lingua/le impostazioni locali corrette e le proprietà di genere come i dati che si intende usare per il training vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 0650a173b02e1b8f1f829953be1dd852024e6f65
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524516"
---
# <a name="create-a-custom-voice"></a>Creare una voce personalizzata

In [preparare i dati per la voce personalizzata](how-to-custom-voice-prepare-data.md)sono stati descritti i diversi tipi di dati che è possibile usare per eseguire il training di una voce personalizzata e i diversi requisiti di formato. Una volta preparati i dati, è possibile iniziare a caricarli nel [portale vocale personalizzato](https://aka.ms/custom-voice-portal)oppure tramite l'API di training vocale personalizzata. Qui vengono descritti i passaggi per eseguire il training di una voce personalizzata tramite il portale.

> [!NOTE]
> In questa pagina si presuppone che sia stato letto [Introduzione alla voce personalizzata](how-to-custom-voice.md) e che [i dati vengano preparati per la voce personalizzata](how-to-custom-voice-prepare-data.md)e che sia stato creato un progetto Voice personalizzato.

Controllare le lingue supportate per la funzionalità Voice personalizzata: [lingua per la personalizzazione](language-support.md#customization).

## <a name="upload-your-datasets"></a>Caricare i set di dati

Quando si è pronti per caricare i dati, passare al [portale vocale personalizzato](https://aka.ms/custom-voice-portal). Creare o selezionare un progetto Voice personalizzato. Il progetto deve condividere la lingua/le impostazioni locali corrette e le proprietà di genere come i dati che si intende usare per il training vocale. Selezionare, ad esempio, `en-GB` se le registrazioni audio disponibili vengono eseguite in inglese con un accento del Regno Unito.

Passare alla scheda **dati** e fare clic su **Carica dati**. Nella procedura guidata selezionare il tipo di dati corretto che corrisponde a quello preparato.

Ogni set di dati caricato deve soddisfare i requisiti per il tipo di dati scelto. È importante formattare correttamente i dati prima che vengano caricati. Ciò garantisce che i dati vengano elaborati accuratamente dal servizio Voice personalizzato. Passare a [preparare i dati per la voce personalizzata](how-to-custom-voice-prepare-data.md) e assicurarsi che i dati siano stati formattati correttamente.

> [!NOTE]
> Gli utenti della sottoscrizione gratuita (F0) possono caricare contemporaneamente due set di impostazioni. Gli utenti della sottoscrizione standard (S0) possono caricare contemporaneamente cinque set di impostazioni. Se si raggiunge il limite, attendere che l'importazione di almeno un set di dati venga completata. Quindi riprovare.

> [!NOTE]
> Il numero massimo di set di dati che possono essere importati per ogni sottoscrizione è 10 file zip per gli utenti della sottoscrizione gratuita (F0) e 500 per gli utenti con sottoscrizione standard (S0).

I set di impostazioni vengono convalidati automaticamente quando si preme il pulsante carica. La convalida dei dati include una serie di controlli sui file audio per verificare il formato, le dimensioni e la frequenza di campionamento del file. Correggere gli eventuali errori e inviarli nuovamente. Quando la richiesta di importazione dei dati viene avviata correttamente, dovrebbe essere visualizzata una voce nella tabella dati corrispondente al set di dati appena caricato.

Nella tabella seguente sono indicati gli stati di elaborazione per i set di dati importati:

| State | Significato |
| ----- | ------- |
| Elaborazione in corso | Il set di dati è stato ricevuto ed è in corso l'elaborazione. |
| Completato | Il set di dati è stato convalidato e può ora essere usato per compilare un modello vocale. |
| Non riuscito | Errore del set di dati durante l'elaborazione a causa di diversi motivi, ad esempio errori di file, problemi relativi ai dati o problemi di rete. |

Al termine della convalida, è possibile visualizzare il numero totale di espressioni corrispondenti per ognuno dei set di impostazioni nella colonna **enunciazioni** . Se il tipo di dati selezionato richiede una segmentazione audio lunga, questa colonna riflette solo le espressioni che sono state segmentate in base alle trascrizioni o tramite il servizio di trascrizione vocale. È possibile scaricare ulteriormente il set di dati convalidato per visualizzare i risultati dettagliati degli enunciati importati correttamente e le relative trascrizioni di mapping. Suggerimento: la segmentazione dell'audio lungo può richiedere più di un'ora per completare l'elaborazione dei dati.

Nella visualizzazione dei dettagli dei dati è possibile controllare ulteriormente i punteggi di pronuncia e il livello di disturbo per ognuno dei set di dati. Gli intervalli del punteggio di pronuncia sono compresi tra 0 e 100. Un punteggio della pronuncia inferiore a 70 generalmente indica un errore di riconoscimento vocale o una mancata corrispondenza dello script. Un forte accento può ridurre il punteggio della pronuncia e influire sulla voce digitale generata.

Un rapporto segnale/rumore superiore indica un livello di rumore inferiore nell'audio. Generalmente è possibile raggiungere un rapporto segnale/rumore superiore a 50 eseguendo la registrazione in studi professionali. L'audio con un rapporto segnale/rumore inferiore a 20 può determinare la presenza di rumore nella voce generata.

Valutare se ripetere la registrazione di qualsiasi espressione con punteggi di pronuncia o rapporti segnale/rumore particolarmente bassi. Se non è possibile ripetere la registrazione, tali espressioni possono essere escluse dal set di dati.

> [!NOTE]
> È necessario che se si usa una voce neurale personalizzata, è necessario registrare il talento vocale nella scheda **Voice Talent** . Quando si prepara lo script di registrazione, assicurarsi di includere la frase seguente per acquisire la conferma del Talent Voice per l'uso dei dati vocali per creare un modello vocale TTS e generare un riconoscimento vocale sintetico. "I [dichiarare il nome e il cognome] sono consapevoli che le registrazioni della mia voce verranno usate da [stato nome della società] per creare e usare una versione sintetica della mia voce".
Questa frase verrà usata per verificare se le registrazioni nei set di impostazioni di training vengono eseguite dalla stessa persona che rilascia il consenso. [Altre informazioni sulle modalità di elaborazione dei dati e sul modo in cui viene eseguita la verifica del talento vocale in questo articolo](https://aka.ms/CNV-data-privacy). 

## <a name="build-your-custom-voice-model"></a>Compilare il modello Voice personalizzato

Dopo che il set di dati è stato convalidato, è possibile usarlo per compilare il modello Voice personalizzato.

1.  Passare a sintesi vocale **> > vocale personalizzata [nome del progetto] > modello**.

2.  Fare clic su **Train Model**.

3.  Immettere quindi un **nome** e una **Descrizione** che consentano di identificare questo modello.

    Scegliere un nome con attenzione. Il nome immesso in questo campo è il nome usato per specificare la voce nella richiesta di sintesi vocale come parte dell'input SSML. Sono consentiti solo lettere, numeri e alcuni caratteri di punteggiatura, ad esempio-, \_ e (','). Usare nomi diversi per modelli vocali diversi.

    Un utilizzo comune del campo **Description** consiste nel registrare i nomi dei set di dati utilizzati per creare il modello.

4.  Nella pagina **selezione dati di training** scegliere uno o più set di dati che si desidera utilizzare per il training. Verificare il numero di espressioni prima di inviarle. È possibile iniziare con un numero qualsiasi di espressioni per i modelli di voce en-US e zh-CN usando il metodo di training "Adaptive". Per le altre impostazioni locali, è necessario selezionare più di 2.000 enunciati per poter eseguire il training di una voce usando un livello standard, inclusi i metodi di training "statistica parametrica" e "concatenativa" e più di 300 espressioni per eseguire il training di una voce neurale personalizzata. 

    > [!NOTE]
    > I nomi audio duplicati verranno rimossi dal training. Verificare che i set di dati selezionati non contengano gli stessi nomi audio in più file con estensione zip.

    > [!TIP]
    > Per i risultati di qualità è necessario usare i set di dati dello stesso altoparlante. Metodi di training diversi richiedono dimensioni dei dati di training differenti. Per eseguire il training di un modello con il metodo "statistico parametrico", sono necessari almeno 2.000 enunciati distinti. Per il metodo "concatenativo", le espressioni 6.000, mentre per "neurale", il requisito minimo per le dimensioni dei dati è 300.

5. Nel passaggio successivo selezionare il **metodo di training** . 

    > [!NOTE]
    > Se si vuole eseguire il training di una voce neurale, è necessario specificare un profilo di talento vocale con il file di consenso audio fornito dal talento vocale che riconosce di usare i propri dati vocali per eseguire il training di un modello vocale personalizzato. La voce neurale personalizzata è disponibile con accesso limitato. Assicurarsi di comprendere i [requisiti di intelligenza artificiale responsabili](https://aka.ms/gating-overview) e di [applicare l'accesso qui](https://aka.ms/customneural). 
    
    In questa pagina è anche possibile scegliere di caricare lo script per il test. Lo script di test deve essere un file txt, minore di 1 MB. Il formato di codifica supportato include ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE o UTF-16-BE. Ogni paragrafo dell'espressione determinerà un audio separato. Se si desidera combinare tutte le frasi in un unico audio, impostarle in un paragrafo. 

6. Fare clic su **Train** per iniziare a creare il modello vocale.

Nella tabella training viene visualizzata una nuova voce che corrisponde al modello appena creato. La tabella Visualizza anche lo stato: elaborazione, operazione riuscita, non riuscita.

Lo stato illustrato riflette il processo di conversione del set di dati in un modello vocale, come illustrato di seguito.

| State | Significato |
| ----- | ------- |
| Elaborazione in corso | È in corso la creazione del modello vocale. |
| Completato | Il modello vocale è stato creato e può essere distribuito. |
| Non riuscito | Non è stato possibile eseguire il training del modello vocale a causa di diversi motivi, ad esempio problemi relativi ai dati o problemi di rete. |

Il tempo di training varia a seconda del volume dei dati audio elaborati e del metodo di training selezionato. Può variare da 30 minuti a 40 ore. Una volta completato il training del modello, è possibile iniziare a testarlo. 

> [!NOTE]
> Gli utenti della sottoscrizione gratuita (F0) possono eseguire il training di un tipo di carattere vocale simultaneamente. Gli utenti della sottoscrizione standard (S0) possono eseguire il training simultaneo di tre voci. Se si raggiunge il limite, attendere che venga completato il training di almeno un carattere voce, quindi riprovare.

> [!NOTE]
> Il training di voci neurali personalizzate non è disponibile. Controllare i [prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) qui. 

> [!NOTE]
> Il numero massimo di modelli vocali di cui è possibile eseguire il training per ogni sottoscrizione è 10 modelli per gli utenti della sottoscrizione gratuita (F0) e 100 per gli utenti con sottoscrizione standard (S0).

Se si utilizza la funzionalità di training per la voce neurale, è possibile scegliere di eseguire il training di un modello ottimizzato per scenari di streaming in tempo reale o di un modello neurale HD ottimizzato per la [sintesi audio](long-audio-api.md)asincrona.  

## <a name="test-your-voice-model"></a>Testare il modello vocale

Ogni training genererà automaticamente i file audio di esempio 100 per facilitare il test del modello. Al termine della compilazione del modello vocale, è possibile testarlo prima di distribuirlo per l'utilizzo.

1.  Passare a sintesi vocale **> > vocale personalizzata [nome del progetto] > modello**.

2.  Fare clic sul nome del modello che si desidera testare.

3.  Nella pagina dei dettagli del modello è possibile trovare i file audio di esempio nella scheda **test** . 

La qualità della voce dipende da diversi fattori, tra cui la dimensione dei dati di training, la qualità della registrazione, l'accuratezza del file di trascrizione, il modo in cui la voce registrata nei dati di training corrisponde alla personalità della voce progettata per il caso d'uso previsto e altro ancora. [Vedere qui per altre informazioni sulle funzionalità e sui limiti della tecnologia e sulla procedura consigliata per migliorare la qualità del modello](https://aka.ms/CNV-limits). 

## <a name="create-and-use-a-custom-voice-endpoint"></a>Creare e usare un endpoint Voice personalizzato

Dopo aver creato e testato il modello vocale, distribuirlo in un endpoint personalizzato per la sintesi vocale. È quindi possibile usare questo endpoint al posto dell'endpoint normale quando si effettuano richieste di sintesi vocale tramite l'API REST. L'endpoint personalizzato può essere chiamato solo dalla sottoscrizione usata per distribuire il tipo di carattere.

Per creare un nuovo endpoint vocale personalizzato, passare a sintesi vocale **> endpoint voice > personalizzato**. Selezionare **Aggiungi endpoint** e immettere un **nome** e una **Descrizione** per l'endpoint personalizzato. Selezionare quindi il modello Voice personalizzato che si desidera associare a questo endpoint.

Dopo aver fatto clic sul pulsante **Aggiungi** nella tabella dell'endpoint, viene visualizzata una voce per il nuovo endpoint. La creazione di un'istanza per un nuovo endpoint potrebbe richiedere alcuni minuti. Quando lo stato della distribuzione ha **esito positivo**, l'endpoint è pronto per l'utilizzo.

È possibile **sospendere** e **riprendere** l'endpoint se non lo si usa continuamente. Quando un endpoint viene riattivato dopo la sospensione, l'URL dell'endpoint viene mantenuto invariato e non è necessario modificare il codice nelle app. 

È inoltre possibile aggiornare l'endpoint a un nuovo modello. Per modificare il modello, verificare che il nuovo modello sia denominato identico a quello che si desidera aggiornare. 

> [!NOTE]
> Per gli utenti della sottoscrizione gratuita (F0) è possibile distribuire un solo modello. Gli utenti della sottoscrizione standard (S0) possono creare fino a 50 endpoint, ognuno con la propria voce personalizzata.

> [!NOTE]
> Per utilizzare la voce personalizzata, è necessario specificare il nome del modello vocale, utilizzare l'URI personalizzato direttamente in una richiesta HTTP e utilizzare la stessa sottoscrizione per passare attraverso l'autenticazione del servizio TTS.

Dopo la distribuzione dell'endpoint, il nome dell'endpoint viene visualizzato come collegamento. Fare clic sul collegamento per visualizzare informazioni specifiche per l'endpoint, ad esempio la chiave dell'endpoint, l'URL dell'endpoint e il codice di esempio.

Il test online dell'endpoint è anche disponibile tramite il portale per la voce personalizzata. Per testare l'endpoint, scegliere **Controlla endpoint** dalla pagina dei **Dettagli dell'endpoint** . Viene visualizzata la pagina di test degli endpoint. Immettere il testo da pronunciare (in formato testo normale o [SSML](speech-synthesis-markup.md) nella casella di testo. Selezionare **Riproduci** per ascoltare il testo pronunciato con il carattere voce personalizzato. Questa funzionalità di test verrà addebitata in base all'utilizzo di sintesi vocale personalizzato.

Dal punto di vista funzionale, l'endpoint personalizzato è identico all'endpoint standard usato per le richieste di sintesi vocale. Per altre informazioni, vedere [API REST](rest-text-to-speech.md).

## <a name="next-steps"></a>Passaggi successivi

* [Guida: registrare gli esempi di voce](record-custom-voice-samples.md)
* [Riferimento da testo a Speech API](rest-text-to-speech.md)
* [API Long audio](long-audio-api.md)
