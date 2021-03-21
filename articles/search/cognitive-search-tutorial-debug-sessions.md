---
title: 'Esercitazione: usare le sessioni di debug per correggere skillsets'
titleSuffix: Azure Cognitive Search
description: Sessioni di debug (anteprima) è uno strumento portale di Azure usato per individuare, diagnosticare e risolvere i problemi in un livello di competenze.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/02/2021
ms.openlocfilehash: ed988baec46152d55cf63aec09fce7a298157212
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99509151"
---
# <a name="tutorial-debug-a-skillset-using-debug-sessions"></a>Esercitazione: eseguire il debug di un skillt mediante sessioni di debug

Skillsets coordinano una serie di azioni che analizzano o trasformano il contenuto, in cui l'output di una competenza diventa l'input di un altro. Quando gli input dipendono da output, gli errori nelle definizioni del gruppo di competenze e nelle associazioni di campo possono comportare operazioni e dati mancanti.

Le **sessioni di debug** nel portale di Azure forniscono una visualizzazione olistica di un skillt. Con questo strumento è possibile eseguire il drill-down fino a passaggi specifici per visualizzare facilmente il punto in cui un'azione potrebbe non riuscire.

In questo articolo si useranno le **sessioni di debug** per individuare e correggere 1) un input mancante e 2) mapping dei campi di output. L'esercitazione è all-inclusive. Fornisce i dati per l'indicizzazione (dati di valutazione clinica), una raccolta di post che crea oggetti e istruzioni per l'uso di **sessioni di debug** per individuare e correggere i problemi del gruppo di competenze.

> [!Important]
> Le sessioni di debug sono funzionalità di anteprima fornite senza un contratto di servizio e non sono consigliate per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario che siano soddisfatti i prerequisiti seguenti:

+ Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/).

+ Un'istanza del servizio Ricerca cognitiva di Azure. [Creare un servizio](search-create-service-portal.md) o [trovarne uno esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione corrente. È possibile usare un servizio gratuito per questo avvio rapido. 

+ Un account di archiviazione di Azure con [archiviazione BLOB](../storage/blobs/index.yml), usato per ospitare dati di esempio e per rendere permanente i dati temporanei creati durante una sessione di debug.

+ L' [app desktop poster](https://www.getpostman.com/) e una [raccolta di post](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) per creare oggetti usando le API REST.

+ [Dati di esempio (trial clinici)](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19).

> [!NOTE]
> Questo argomento di avvio rapido prevede anche l'uso di [Servizi cognitivi di Azure ](https://azure.microsoft.com/services/cognitive-services/) per l'intelligenza artificiale. Poiché il carico di lavoro è molto ridotto, Servizi cognitivi lavora dietro le quinte per offrire un'elaborazione gratuita per un massimo di 20 transazioni. Questo significa che è possibile completare questo esercizio senza dover creare una risorsa Servizi cognitivi aggiuntiva.

## <a name="set-up-your-data"></a>Configurare i dati

Questa sezione Crea il set di dati di esempio nell'archiviazione BLOB di Azure in modo che l'indicizzatore e il set di competenze dispongano di contenuti.

1. [Scaricare dati di esempio (clinico-Trials-PDF-19)](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19), costituito da 19 file.

1. [Creare un account di archiviazione di Azure](../storage/common/storage-account-create.md?tabs=azure-portal) o [trovare un account esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   + Per evitare addebiti dovuti alla larghezza di banda, scegliere la stessa area di Ricerca cognitiva di Azure.

   + Scegliere il tipo di account Archiviazione V2 (utilizzo generico V2).

1. Passare alle pagine dei servizi di archiviazione di Azure nel portale e creare un contenitore BLOB. È consigliabile specificare il livello di accesso "privato". Assegnare al contenitore il nome `clinicaltrialdataset`.

1. Nel contenitore fare clic su **Carica** per caricare i file di esempio scaricati e decompressi nel primo passaggio.

1. Nel portale, ottenere e salvare la stringa di connessione per archiviazione di Azure. Sarà necessario per le chiamate all'API REST che indicizzano i dati. È possibile ottenere la stringa di connessione da **Impostazioni**  >  **chiavi di accesso** nel portale.

## <a name="get-a-key-and-url"></a>Ottenere una chiave e un URL

Le chiamate REST richiedono l'URL del servizio e una chiave di accesso per ogni richiesta. Con entrambi gli elementi viene creato un servizio di ricerca, quindi se il servizio Ricerca cognitiva di Azure è stato aggiunto alla sottoscrizione, seguire questi passaggi per ottenere le informazioni necessarie:

1. [Accedere al portale di Azure](https://portal.azure.com/) e ottenere l'URL nella pagina **Panoramica** del servizio di ricerca. Un endpoint di esempio potrebbe essere simile a `https://mydemo.search.windows.net`.

1. In **Impostazioni** > **Chiavi** ottenere una chiave amministratore per diritti completi sul servizio. Sono disponibili due chiavi amministratore interscambiabili, fornite per continuità aziendale nel caso in cui sia necessario eseguire il rollover di una di esse. È possibile usare la chiave primaria o secondaria nelle richieste per l'aggiunta, la modifica e l'eliminazione di oggetti.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Ottenere un endpoint HTTP e una chiave di accesso" border="false":::

Per ogni richiesta inviata al servizio è necessario specificare una chiave API. La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

## <a name="create-data-source-skillset-index-and-indexer"></a>Creare l'origine dati, il set di competenze, l'indice e l'indicizzatore

In questa sezione, per creare l'origine dati ricerca cognitiva, il gruppo di competenze, l'indice e l'indicizzatore, viene usato il post e una raccolta fornita. Se non si ha familiarità con il post, vedere [questa Guida introduttiva](search-get-started-rest.md).

Per completare questa attività, è necessario che sia stata creata la [raccolta dei post](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) per questa esercitazione. 

1. Avviare il post e importare la raccolta. In **Files** > **New** (File > Nuovo) selezionare la raccolta da importare.
1. Dopo aver importato la raccolta, espandere l'elenco delle azioni (...).
1. Fare clic su **Modifica**.
1. In valore corrente immettere il nome del `searchService` (ad esempio, se l'endpoint è `https://mydemo.search.windows.net` , il nome del servizio è " `mydemo` ").
1. Immettere `apiKey` con la chiave primaria o secondaria del servizio di ricerca.
1. Immettere `storageConnectionString` dalla pagina chiavi dell'account di archiviazione di Azure.
1. Immettere `containerName` per il contenitore creato nell'account di archiviazione e quindi fare clic su **Aggiorna**.

   :::image type="content" source="media/cognitive-search-debug/postman-enter-variables.png" alt-text="Modificare le variabili in Postman":::

La raccolta contiene quattro chiamate REST diverse che vengono usate per creare gli oggetti usati in questa esercitazione.

La prima chiamata crea l'origine dati. `clinical-trials-ds`. La seconda chiamata crea il set di competenze, `clinical-trials-ss`. La terza chiamata crea l'indice, `clinical-trials`. La quarta e ultima chiamata crea l'indicizzatore, `clinical-trials-idxr`.

+ Aprire ogni richiesta a turno, quindi fare clic su **Invia** per inviare ogni richiesta al servizio di ricerca. 

Una volta completate tutte le chiamate della raccolta, chiudere Postman e tornare al portale di Azure.

## <a name="check-results-in-the-portal"></a>Controllare i risultati nel portale

Il codice di esempio crea intenzionalmente un indice bacato come conseguenza di problemi che si sono verificati durante l'esecuzione di competenze. Il problema mancano i dati. 

1. Nella pagina di panoramica del servizio di ricerca di portale di Azure selezionare la scheda **indici** . 
1. Selezionare l' `clinical-trials` indice.
1. Immettere questa stringa di query: `$select=metadata_storage_path, organizations, locations&$count=true` per restituire i campi per documenti specifici (identificati dal `metadata_storage_path` campo univoco).
1. Fare clic su **Cerca** per eseguire la query, restituendo tutti i 19 documenti, mostrando i valori vuoti per "organizzazioni" e "località".

Questi campi devono essere popolati tramite le competenze per il [riconoscimento delle entità](cognitive-search-skill-entity-recognition.md)di Skills, usati per trovare le organizzazioni e i percorsi in qualsiasi punto all'interno del contenuto del BLOB. Nell'esercizio successivo verrà utilizzata la sessione di debug per determinare il problema che si è verificato.

Un altro modo per esaminare gli errori e gli avvisi consiste nel portale di Azure.

1. Aprire la scheda **indicizzatori** e selezionare `clinical-trials-idxr` .
1. Si noti che mentre il processo dell'indicizzatore ha avuto esito positivo in generale, sono stati 57 avvisi.
1. Fare clic su **operazione riuscita** per visualizzare gli avvisi (se si sono verificati per la maggior parte degli errori, il collegamento dettaglio potrebbe **non** riuscire). Verrà visualizzato un lungo elenco di tutti gli avvisi emessi dall'indicizzatore.

   :::image type="content" source="media/cognitive-search-debug/portal-indexer-errors-warnings.png" alt-text="Visualizza avvisi":::

## <a name="start-your-debug-session"></a>Avviare la sessione di debug

:::image type="content" source="media/cognitive-search-debug/new-debug-session-screen-required.png" alt-text="Avviare una nuova sessione di debug":::

1. Nella pagina di panoramica della ricerca fare clic sulla scheda **sessioni di debug** .
1. Selezionare **+ nuova sessione di debug**.
1. Assegnare un nome alla sessione. 
1. Connettere la sessione al proprio account di archiviazione. 
1. In modello di indicizzatore specificare il nome dell'indicizzatore. L'indicizzatore contiene riferimenti all'origine dati, al set di competenze e all'indice.
1. Per il documento di debug accettare l'impostazione predefinita, che corrisponde al primo documento della raccolta. 
1. Selezionare **Salva** per salvare la sessione. Salvando la sessione viene avviata la pipeline di arricchimento tramite intelligenza artificiale in base a quanto definito dal set di competenze.

> [!Important]
> Una sessione di debug funziona solo con un singolo documento. È possibile scegliere il documento di cui eseguire il debug o usare solo il primo.

<!-- > :::image type="content" source="media/cognitive-search-debug/debug-execution-complete1.png" alt-text="New debug session started"::: -->

Al termine dell'inizializzazione della sessione di debug, per impostazione predefinita la sessione viene impostata sulla scheda **arricchimenti ai** , evidenziando il **grafico delle competenze**. Il grafico skill fornisce una gerarchia visiva delle competenze e il relativo ordine di esecuzione in sequenza e in parallelo.

## <a name="find-issues-with-the-skillset"></a>Individuare i problemi con il insieme di competenze

Eventuali problemi segnalati dall'indicizzatore sono disponibili nella scheda **errori/avvisi** adiacenti. 

Si noti che la scheda **errori/avvisi** fornirà un elenco molto più piccolo rispetto a quello visualizzato in precedenza, poiché in questo elenco vengono illustrati solo i dettagli degli errori relativi a un singolo documento. Come con l'elenco visualizzato dall'indicizzatore, è possibile fare clic su un messaggio di avviso per visualizzare i dettagli corrispondenti.

Selezionare **errori/avvisi** per esaminare le notifiche. Verranno visualizzati tre:

   + "Impossibile eseguire il mapping del campo di output ' locations ' nell'indice di ricerca. Controllare la proprietà' outputFieldMappings ' dell'indicizzatore.
Manca il valore '/Document/merged_content/locations ' ".

   + "Impossibile eseguire il mapping del campo di output ' Organizations ' all'indice di ricerca. Controllare la proprietà' outputFieldMappings ' dell'indicizzatore.
Manca il valore '/Document/merged_content/Organizations ' ".

   + "Skill eseguita ma potrebbe avere risultati imprevisti perché uno o più input delle competenze non sono validi.
Input skill facoltativo mancante. Nome:' codiceLingua ', origine:'/document/languageCode '. Problemi di analisi del linguaggio delle espressioni: valore mancante '/document/languageCode '.

   Molte competenze hanno un parametro ' codiceLingua '. Esaminando l'operazione, è possibile notare che l'input del codice della lingua non è presente in `Enrichment.NerSkillV2.#1` , che è la stessa capacità di riconoscimento delle entità che si verificano problemi con l'output "Locations" e "Organizations". 

Poiché tutte e tre le notifiche sono relative a questa competenza, il passaggio successivo consiste nel eseguire il debug di questa competenza. Se possibile, iniziare risolvendo i problemi di input prima di procedere con i problemi di outputFieldMapping.

 :::image type="content" source="media/cognitive-search-debug/debug-session-errors-warnings.png" alt-text="Nuova sessione di debug avviata":::

<!-- + The Skill Graph provides a visual hierarchy of the skillset and its order of execution from top to bottom. Skills that are side by side in the graph are executed in parallel. Color coding of skills in the graph indicate the types of skills that are being executed in the skillset. In the example, the green skills are text and the red skill is vision. Clicking on an individual skill in the graph will display the details of that instance of the skill in the right pane of the session window. The skill settings, a JSON editor, execution details, and errors/warnings are all available for review and editing.

+ The Enriched Data Structure details the nodes in the enrichment tree generated by the skills from the source document's contents. -->

## <a name="fix-missing-skill-input-value"></a>Correggere un valore di input della competenza mancante

Nella scheda **errori/avvisi** si verifica un errore per un'operazione denominata `Enrichment.NerSkillV2.#1` . I dettagli dell'errore spiegano che c'è un problema con il valore di input della competenza "/document/languageCode". 

1. Tornare alla scheda **arricchimenti ai** .
1. Fare clic su **Skill Graph** (Grafo delle competenze).
1. Fare clic sull'abilità con etichetta **#1** per visualizzarne i dettagli nel riquadro di destra.
1. Individuare l'input per "languageCode".
1. Selezionare il simbolo **</>** all'inizio della riga e aprire l'analizzatore di espressioni.
1. Fare clic sul pulsante **Valuta** per verificare che l'espressione restituisca un errore. Verrà confermato che la proprietà "languageCode" non è un input valido.

   :::image type="content" source="media/cognitive-search-debug/expression-evaluator-language.png" alt-text="Analizzatore di espressioni":::

È possibile cercare questo errore nella sessione in due modi. Il primo consiste nell'esaminare la provenienza dell'input: quale competenza nella gerarchia dovrebbe produrre questo risultato? La scheda Esecuzioni nel riquadro dei dettagli della competenza dovrebbe visualizzare l'origine dell'input. L'assenza di origine indica un errore di mapping del campo.

1. Fare clic sulla scheda **Esecuzioni**.
1. In INPUTS trovare "languageCode". Per questo input non è elencata alcuna origine. 
1. Nel riquadro sinistro passare alla scheda Enriched Data Structure (Struttura dei dati arricchiti). Non è presente un percorso mappato corrispondente a "languageCode".

   :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-language.png" alt-text="Struttura dei dati arricchiti":::

È presente un percorso mappato per "language". C'è quindi un errore di ortografia nelle impostazioni della competenza. Per correggere questa espressione nell'#1 skill con l'espressione '/Document/Language ' deve essere aggiornata.

1. Aprire l'analizzatore di espressioni **</>** per il percorso "language".
1. Copiare l'espressione. Chiudere la finestra.
1. Passare alle impostazioni delle competenze per la competenza #1 e aprire l'analizzatore di espressioni **</>** per l'input "languageCode".
1. Incollare il nuovo valore "/document/language" nella casella Espressione e fare clic su **Valuta**.
1. Dovrebbe essere visualizzato l'input corretto "en". Fare clic su Applica per aggiornare l'espressione.
1. Fare clic su **Salva** nel riquadro dei dettagli della competenza a destra.
1. Fare clic su **Esegui** nel menu della finestra della sessione. Verrà avviata un'altra esecuzione del set di competenze usando il documento. 

Al termine dell'esecuzione della sessione di debug, fare clic sulla scheda Errori/Avvisi, dove si vedrà che l'errore "Enrichment.NerSkillV2.#1" è scomparso. Ci sono però ancora due avvisi che indicano che il servizio non è stato in grado di eseguire il mapping dei campi di output organizations e locations all'indice di ricerca. Mancano i valori "/document/merged_content/organizations" e "/document/merged_content/locations".

## <a name="fix-missing-skill-output-values"></a>Correggere i valori di output della competenza mancanti

:::image type="content" source="media/cognitive-search-debug/warnings-missing-value-locations-organizations.png" alt-text="Errori e avvisi":::

Mancano dei valori di output da una competenza. Per identificare la competenza con l'errore, passare alla scheda Enriched Data Structure (Struttura dei dati arricchiti), trovare il nome del valore e osservare la relativa origine. I valori organizations e locations mancanti sono output della competenza #1. Aprendo l'analizzatore di espressioni </> per ogni percorso, le espressioni verranno visualizzate rispettivamente come "/document/content/organizations" e "/document/content/locations".

:::image type="content" source="media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png" alt-text="Entità organizations dell'analizzatore di espressioni":::

L'output di queste entità è vuoto e non dovrebbe esserlo. Quali sono gli input che producono questo risultato?

1. Passare a **Skill Graph** (Grafo delle competenze) e selezionare la competenza #1.
1. Selezionare la scheda **Esecuzioni** nel riquadro dei dettagli della competenza a destra.
1. Aprire l'analizzatore di espressioni **</>** per la proprietà INPUTS "text".

   :::image type="content" source="media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png" alt-text="Input per la competenza text":::

Il risultato visualizzato per questo input non sembra un input di testo, ma piuttosto un'immagine racchiusa tra nuove righe. La mancanza di testo indica che non ci sono entità identificabili. In base alla gerarchia delle competenze, il contenuto viene prima elaborato dalla competenza #6 (OCR) e quindi viene passato alla competenza #5 (Merge). 

1. Selezionare la competenza #5 (Merge) in **Skill Graph** (Grafo delle competenze).
1. Selezionare la scheda **Esecuzioni** nel riquadro dei dettagli della competenza a destra e aprire l'analizzatore di espressioni **</>** per la proprietà OUTPUTS "mergedText".

   :::image type="content" source="media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png" alt-text="Output della competenza Merge":::

Qui il testo è associato all'immagine. Osservando l'espressione "/document/merged_content" si può notare l'errore nei percorsi "organizations" e "locations"per la competenza #1. Invece di usare "/document/content" dovrebbe usare "/document/merged_content" per la proprietà INPUTS "text".

1. Copiare l'espressione dell'output "mergedText" e chiudere la finestra dell'analizzatore di espressioni.
1. Selezionare la competenza #1 in **Skill Graph** (Grafo delle competenze).
1. Selezionare la scheda **Skill Settings** (Impostazioni competenza) nel riquadro dei dettagli della competenza a destra.
1. Aprire l'analizzatore di espressioni **</>** per la proprietà INPUTS "text".
1. Incollare la nuova espressione nella casella. Fare clic su **Valuta**.
1. Dovrebbe essere visualizzato l'input corretto con il testo aggiunto. Fare clic su **Applica** per aggiornare le impostazioni della competenza.
1. Fare clic su **Salva** nel riquadro dei dettagli della competenza a destra.
1. Fare clic su **Esegui** nel menu della finestra della sessione. Verrà avviata un'altra esecuzione del set di competenze usando il documento.

Al termine dell'esecuzione dell'indicizzatore, gli errori sono ancora presenti. Tornare alla competenza #1 per individuare la causa. L'input della competenza è stato corretto da "content" a "merged_content". Quali sono gli output per queste entità nella competenza?

1. Selezionare la scheda **AI Enrichments** (Arricchimenti tramite intelligenza artificiale).
1. Selezionare **Skill Graph** (Grafo delle competenze) e fare clic sulla competenza #1.
1. In **Skill Settings** (Impostazioni competenza) individuare "outputs".
1. Aprire l'analizzatore di espressioni **</>** per l'entità "organizations".

   :::image type="content" source="media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png" alt-text="Output dell'entità organizations":::

La valutazione del risultato dell'espressione restituisce il risultato corretto. La competenza lavora per identificare il valore corretto dell'entità "organizations". Tuttavia il mapping dell'output nel percorso dell'entità continua a generare un errore. Confrontando il percorso di output nella competenza con il percorso di output nell'errore, si nota che la competenza padre delle entità outputs, organizations e locations è nel nodo /document/content, mentre il mapping dei campi di output prevede che i risultati siano assegnati a una competenza padre nel nodo /document/merged_content. Nel passaggio precedente l'input è stato modificato da "/document/content" a "/document/merged_content". Per assicurare che l'output venga generato con il contesto corretto, occorre modificare il contesto nelle impostazioni della competenza.

1. Selezionare la scheda **AI Enrichments** (Arricchimenti tramite intelligenza artificiale).
1. Selezionare **Skill Graph** (Grafo delle competenze) e fare clic sulla competenza #1.
1. In **Skill Settings** (Impostazioni competenza) individuare "context".
1. Fare doppio clic sull'impostazione relativa a "context" e modificarla in "/document/merged_content".
1. Fare clic su **Salva** nel riquadro dei dettagli della competenza a destra.
1. Fare clic su **Esegui** nel menu della finestra della sessione. Verrà avviata un'altra esecuzione del set di competenze usando il documento.

   :::image type="content" source="media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png" alt-text="Correzione del contesto nell'impostazione della competenza":::

Tutti gli errori sono stati risolti.

## <a name="commit-changes-to-the-skillset"></a>Eseguire il commit delle modifiche apportate al set di competenze

Quando è stata avviata la sessione di debug, il servizio di ricerca ha creato una copia del set di competenze. Questa operazione è stata eseguita per proteggere le competenze originali nel servizio di ricerca. Ora che è stato terminato il debug del proprio sistema di competenze, è possibile eseguire il commit delle correzioni (sovrascrivere le competenze originali). 

In alternativa, se non si è pronti a eseguire il commit delle modifiche, è possibile salvare la sessione di debug e riaprirla in un secondo momento.

1. Fare clic su **Commit changes** (Esegui il commit delle modifiche) nel menu principale di Sessioni di debug.
1. Fare clic su **OK** per confermare che si vuole aggiornare il set di competenze.
1. Chiudere la sessione di debug e selezionare la scheda **Indicizzatori**.
1. Aprire "clinical-trials-idxr".
1. Fare clic su **Reimposta**.
1. Fare clic su **Esegui**. Fare clic su **OK** per confermare.

Al termine dell'esecuzione dell'indicizzatore, dovrebbe essere presente un segno di spunta verde e la parola Success accanto al timestamp per l'esecuzione più recente nella scheda **cronologia di esecuzione** . Per assicurarsi che le modifiche siano state applicate:

1. Nella pagina Panoramica ricerca selezionare la scheda **Indice** .
1. Aprire l'indice "cliniche-Trials" e nella scheda Esplora ricerche immettere questa stringa di query: `$select=metadata_storage_path, organizations, locations&$count=true` per restituire i campi per documenti specifici (identificati dal `metadata_storage_path` campo univoco).
1. Fare clic su **Cerca**.

I risultati dovrebbero indicare che le organizzazioni e le località sono ora popolate con i valori previsti.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno verificare se le risorse create sono ancora necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento **Tutte le risorse** o **Gruppi di risorse** nel riquadro di spostamento a sinistra.

Se si usa un servizio gratuito, tenere presente che il numero di indicizzatori e origini dati è limitato a tre. Per non superare il limite, è possibile eliminare i singoli elementi nel portale. 

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha toccato diversi aspetti della definizione e dell'elaborazione delle competenze. Per ulteriori informazioni sui concetti e sui flussi di lavoro, fare riferimento agli articoli seguenti:

+ [Come eseguire il mapping di campi di output delle competenze ai campi in un indice di ricerca](cognitive-search-output-field-mapping.md)

+ [Skillsets in Azure ricerca cognitiva](cognitive-search-working-with-skillsets.md)

+ [Come configurare la memorizzazione nella cache per l'arricchimento incrementale](cognitive-search-incremental-indexing-conceptual.md)