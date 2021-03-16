---
title: Domande frequenti sul Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Risposte alle domande frequenti sul servizio di sintesi vocale.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: panosper
ms.openlocfilehash: bcb4408df08f3854b067c8b805b78433a3d5075c
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491249"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Domande frequenti sul Servizio di riconoscimento vocale

Se in questo documento non sono presenti risposte alle domande di proprio interesse, vedere le [altre opzioni di supporto](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext).

## <a name="general"></a>Generale

**D: Qual è la differenza tra un modello di base e un modello di riconoscimento vocale personalizzato?**

**R**: Un modello di base è stato sottoposto a training con dati di proprietà di Microsoft ed è già stato distribuito nel cloud. È possibile usare un modello personalizzato per adattare un modello a un determinato ambiente con una lingua o rumori di fondo particolari. Fabbriche, automobili o strade rumorose richiederebbero un modello acustico adattato. Argomenti come biologia, fisica, radiologia, nomi di prodotti e acronimi personalizzati richiederebbero un modello di lingua adattato. Se si esegue il training di un modello personalizzato, è consigliabile iniziare con il testo correlato per migliorare il riconoscimento di termini e frasi speciali.

**D: Da dove è necessario iniziare per usare un modello di base?**

**R**: Prima di tutto è necessario ottenere una [chiave di sottoscrizione](overview.md#try-the-speech-service-for-free). Per effettuare chiamate REST ai modelli di base predistribuiti, vedere le [API REST](./overview.md#reference-docs). Se si desidera usare WebSocket, [scaricare l'SDK](speech-sdk.md).

**: È sempre necessario compilare un modello conversione voce/testo personalizzato?**

**R**: No. Se l'applicazione usa un linguaggio quotidiano generico, non è necessario personalizzare un modello. Se l'applicazione viene usata in un ambiente in cui il rumore di fondo è scarso o addirittura assente, non è necessario personalizzare un modello.

È possibile distribuire modelli di base e personalizzati nel portale e quindi sottoporli a test di accuratezza. Questa funzionalità può essere usata per misurare l'accuratezza di un modello di base rispetto a un modello personalizzato.

**D: Come è possibile sapere quando l'elaborazione del set di dati o del modello è completa?**

**R**: Attualmente l'unica indicazione è lo stato del modello o del set di dati nella tabella. Quando l'elaborazione è completa, lo stato è **Succeded** (Operazione completata).

**D: È possibile creare più modelli?**

**R**: Non sono previsti limiti al numero di modelli che si può avere nella propria raccolta.

**D: ho realizzato un errore. Ricerca per categorie annullare l'importazione dei dati o la creazione del modello in corso?**

**R**: Al momento non è possibile eseguire il rollback di un processo di adattamento di un modello acustico o linguistico. I modelli e i dati importati possono essere eliminati quando sono in uno stato terminale.

**D: si ottengono diversi risultati per ogni frase con il formato di output dettagliato. Quale si deve usare?**

**R**: eseguire sempre il primo risultato, anche se un altro risultato ("N-Best") può avere un valore di confidenza maggiore. Il servizio riconoscimento vocale considera il primo risultato come il migliore. Può anche essere una stringa vuota se non è stata riconosciuta alcuna voce.

È probabile che gli altri risultati siano peggiori e che non si applichino le maiuscole e la punteggiatura complete. Questi risultati sono particolarmente utili in scenari speciali, ad esempio fornire agli utenti la possibilità di scegliere le correzioni da un elenco o di gestire i comandi riconosciuti in modo errato.

**D: perché esistono modelli di base diversi?**

**R**: è possibile scegliere da più di un modello di base nel servizio di riconoscimento vocale. Ogni nome di modello contiene la data in cui è stata aggiunta. Quando si avvia il training di un modello personalizzato, usare il modello più recente per ottenere la massima precisione. I modelli di base precedenti sono ancora disponibili per un certo periodo di tempo quando viene reso disponibile un nuovo modello. È possibile continuare a usare il modello con cui si è lavorato fino a quando non viene ritirato (vedere [ciclo di vita del modello ed endpoint](./how-to-custom-speech-model-and-endpoint-lifecycle.md)). È comunque consigliabile passare al modello di base più recente per una maggiore accuratezza.

**D: È possibile aggiornare un modello esistente (stacking di modelli)?**

**R**: Non è possibile aggiornare un modello esistente. Come soluzione, combinare il set di dati precedente con quello nuovo e riadattarlo.

I set di dati precedente e nuovo devono essere combinati in un unico file ZIP (per dati acustici) o in un file con estensione txt (per dati linguistici). Dopo aver terminato l'adattamento, per ottenere un nuovo endpoint è necessario ridistribuire il nuovo modello aggiornato

**D: quando è disponibile una nuova versione di un modello di base, la distribuzione viene aggiornata automaticamente?**

**R**: Le distribuzioni NON vengono aggiornate automaticamente.

Se è stato adattato e distribuito un modello, la distribuzione rimarrà invariata. È possibile rimuovere le autorizzazioni del modello distribuito, riadattarlo utilizzando la versione più recente del modello di base e ridistribuirlo per una maggiore accuratezza.

Sia i modelli di base che i modelli personalizzati verranno ritirati in seguito (vedere [ciclo di vita del modello ed endpoint](./how-to-custom-speech-model-and-endpoint-lifecycle.md)).

**D: È possibile scaricare il modello ed eseguirlo in locale?**

**R**: è possibile eseguire localmente un modello personalizzato in un [contenitore Docker](speech-container-howto.md?tabs=cstt).

**D: è possibile copiare o spostare i set di impostazioni, I modelli e le distribuzioni in un'altra area o sottoscrizione?**

**R**: è possibile usare l' [API REST](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) per copiare un modello personalizzato in un'altra area o sottoscrizione. Impossibile copiare i set di impostazioni o le distribuzioni. È possibile importare di nuovo un set di dati in un'altra sottoscrizione e creare endpoint utilizzando le copie del modello.

**D: Le richieste vengono registrate?**

**R**: per impostazione predefinita, le richieste non vengono registrate, né audio né trascrizioni. Se necessario, è possibile selezionare *il contenuto del log da questa opzione di endpoint* quando si [Crea un endpoint personalizzato](how-to-custom-speech-train-model.md#deploy-a-custom-model). È anche possibile abilitare la registrazione audio nell' [SDK di riconoscimento vocale](how-to-use-logging.md) in base alle singole richieste senza creare un endpoint personalizzato. In entrambi i casi, i risultati dell'audio e del riconoscimento delle richieste verranno archiviati in un archivio protetto. Per le sottoscrizioni che usano l'archiviazione di proprietà di Microsoft, saranno disponibili per 30 giorni.

È possibile esportare i file registrati nella pagina di distribuzione in speech studio se si usa un endpoint personalizzato con *contenuto di log da questo endpoint* abilitato. Se la registrazione audio è abilitata tramite l'SDK, chiamare l' [API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModelLogs) per accedere ai file.

**D: Le richieste sono limitate?**

**R**: vedere [quote e limiti per i servizi di riconoscimento vocale](speech-services-quotas-and-limits.md).

**D: come viene addebitato l'audio a doppio canale?**

**R**: se si invia ogni canale separatamente (ogni canale nel relativo file), verrà addebitato il costo per la durata di ogni file. Se si invia un singolo file con ogni canale multiplexato insieme, verrà addebitato il costo della durata del singolo file. Per informazioni dettagliate sui prezzi, vedere la [pagina dei prezzi dei servizi cognitivi di Azure](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> In caso di altri problemi di privacy che impediscono l'uso del Servizio di riconoscimento vocale personalizzato, contattare uno dei canali di supporto.

## <a name="increasing-concurrency"></a>Aumento della concorrenza
Vedere [quote e limiti per i servizi di riconoscimento vocale](speech-services-quotas-and-limits.md).


## <a name="importing-data"></a>Importazione di dati

**D: Qual è il limite di dimensione per un set di dati e perché esiste tale limite?**

**R**: il limite è dovuto alla restrizione sulla dimensione di un file per il caricamento http. Vedere [quote e limiti per i servizi vocali](speech-services-quotas-and-limits.md) per il limite effettivo. È possibile suddividere i dati in più set di dati e selezionarli tutti per eseguire il training del modello.

**D**: È possibile comprimere i file di testo per poter caricare file più grandi?

**R**: No. Attualmente sono ammessi solo file di testo non compressi.

**D: il rapporto dati indica che sono presenti espressioni non riuscite. Qual è il problema?**

**R**: Se non viene caricato il 100% delle espressioni di un file, non è un problema. Se la grande maggioranza, ad esempio più del 95%, delle espressioni in un set di dati acustico o linguistico viene importata correttamente, il set di dati può essere utilizzabile. È tuttavia consigliabile cercare di comprendere il motivo dell'esito negativo delle espressioni e risolvere i problemi. I problemi più comuni, ad esempio gli errori di formattazione, sono facili da risolvere.

## <a name="creating-an-acoustic-model"></a>Creazione di un modello acustico

**D: Quanti dati acustici sono necessari?**

**R**: È consigliabile iniziare con dati acustici di lunghezza compresa tra 30 minuti e un'ora.

**D: Quali dati è necessario raccogliere?**

**R**: Raccogliere dati più vicini possibile allo scenario e al caso d'uso dell'applicazione. La raccolta dei dati deve corrispondere all'applicazione e agli utenti di destinazione in termini di dispositivo o dispositivi, ambienti e tipi di parlanti. In genere, è consigliabile raccogliere dati dalla più ampia varietà di parlanti possibile.

**D: Come si deve raccogliere i dati acustici?**

**R**: È possibile creare un'applicazione di raccolta dati autonoma o usare un software di registrazione audio standard. È anche possibile creare una versione dell'applicazione in grado di registrare e quindi usare i dati audio.

**D: È necessario trascrivere personalmente i dati di adattamento?**

**R**: Sì. È possibile farlo personalmente o usando un servizio di trascrizione professionale. Alcuni utenti preferiscono usare sistemi di trascrizione professionali, mentre altri usano il crowdsourcing o eseguono le trascrizioni autonomamente.

**D: quanto tempo sarà necessario per eseguire il training di un modello personalizzato con dati audio?**

**R**: il training di un modello con dati audio può essere un processo lungo. A seconda della quantità di dati, la creazione di un modello personalizzato può richiedere diversi giorni. Se l'operazione non può essere completata entro una settimana, il servizio potrebbe interrompere l'operazione di training e segnalare il modello come non riuscito.

Usare una delle [aree](custom-speech-overview.md#set-up-your-azure-account) in cui è disponibile hardware dedicato per il training. Il servizio di riconoscimento vocale utilizzerà fino a 20 ore di audio per il training in queste aree. In altre aree, utilizzerà solo fino a 8 ore.

In generale, il servizio elabora circa 10 ore di dati audio al giorno in aree con hardware dedicato. Può elaborare circa 1 ora di dati audio al giorno in altre aree. È possibile copiare il modello completamente sottoposto a training in un'altra area usando l' [API REST](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription). Il training con solo testo è molto più veloce e in genere viene completato in pochi minuti.

Alcuni modelli di base non possono essere personalizzati con dati audio. Il servizio utilizzerà semplicemente il testo della trascrizione per il training e ignorerà i dati audio. Il training verrà completato molto più velocemente e i risultati saranno uguali a quelli di training con solo testo. Per un elenco dei modelli di base che supportano il training con dati audio, vedere Supporto per le [lingue](language-support.md#speech-to-text) .

## <a name="accuracy-testing"></a>Test di accuratezza

**D: Che cos'è la frequenza degli errori di parola (WER) e come viene calcolata?**

**R**: La frequenza degli errori di parola (WER) è la metrica di valutazione per il riconoscimento vocale. Viene calcolata come numero totale di errori, inclusi inserimenti, eliminazioni e sostituzioni, diviso per il numero totale di parole nella trascrizione di riferimento. Per altre informazioni, vedere [Evaluate riconoscimento vocale personalizzato Precision](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy).

**D: Come si determina se i risultati di un test di accuratezza sono positivi?**

**R**: I risultati mostrano un confronto tra il modello di base e quello personalizzato. Perché la personalizzazione sia proficua, è necessario che sia più efficiente del modello di base.

**D: Come si determina la frequenza degli errori di parola di un modello di base in modo da rendersi conto se c'è stato un miglioramento?**

**R**: I risultati dei test offline mostrano l'accuratezza di base del modello personalizzato e il miglioramento rispetto al modello di base.

## <a name="creating-a-language-model"></a>Creazione di un modello linguistico

**D**: Quanti dati di testo è necessario caricare?

**R**: Dipende da quanto il vocabolario e le frasi utilizzate nell'applicazione differiscono dai modelli linguistici iniziali. Per tutte le parole nuove è utile specificare il maggior numero possibile di esempi di utilizzo. Per le frasi frequenti usate nell'applicazione è utile anche includere frasi nei dati linguistici perché indicano al sistema di rimanere in ascolto anche di questi termini. È frequente che nel set di dati linguistici siano presenti almeno 100 espressioni, di solito ce ne sono almeno diverse centinaia o più. Se inoltre si prevede che alcuni tipi di query siano più comuni di altri, è possibile inserire nel set di dati varie copie delle query più comuni.

**D: È possibile caricare semplicemente un elenco di parole?**

**R**: Il caricamento di un elenco di parole consente di aggiungere le parole nel vocabolario ma non di insegnare al sistema come le parole stesse vengono generalmente usate. Se si specificano espressioni complete o parziali, ovvero frasi o espressioni di probabile uso da parte degli utenti, il modello linguistico può apprendere le nuove parole e come vengono usate. Il modello linguistico personalizzato è utile non solo per aggiungere nuove parole nel sistema ma anche per modificare la probabilità di parole note per l'applicazione. L'indicazione di espressioni complete favorisce l'apprendimento da parte del sistema.

## <a name="tenant-model-custom-speech-with-microsoft-365-data"></a>Modello tenant (Riconoscimento vocale personalizzato con dati Microsoft 365)

**D: quali informazioni sono incluse nel modello tenant e come viene creata?**

**R:** Un modello tenant viene compilato tramite messaggi di posta elettronica e documenti di [gruppo pubblici](https://support.microsoft.com/office/learn-about-microsoft-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2) che possono essere visualizzati da chiunque nell'organizzazione.

**D: quali esperienze vocali sono migliorate con il modello tenant?**

**R:** Quando il modello tenant è abilitato, creato e pubblicato, viene utilizzato per migliorare il riconoscimento per tutte le applicazioni aziendali compilate mediante il servizio di riconoscimento vocale. che passano anche un token di Azure AD utente che indica l'appartenenza all'azienda.

Le esperienze di sintesi vocale compilate in Microsoft 365, ad esempio la dettatura e la didascalia di PowerPoint, non vengono modificate quando si crea un modello tenant per le applicazioni di servizio vocale.

## <a name="next-steps"></a>Passaggi successivi

- [Risoluzione dei problemi](troubleshooting.md)
- [Note sulla versione](releasenotes.md)