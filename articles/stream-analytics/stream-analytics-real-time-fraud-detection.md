---
title: 'Esercitazione: Analizzare i dati di chiamate fraudolente con Analisi di flusso di Azure e visualizzare i risultati in un dashboard di Power BI'
description: Questa esercitazione offre una dimostrazione completa relativa all'uso di Analisi di flusso di Azure per analizzare le chiamate fraudolente in un flusso di telefonate.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: contperf-fy21q2
ms.date: 12/17/2020
ms.openlocfilehash: 8e7a484ff968454f3c5b31422b87123dcee03726
ms.sourcegitcommit: e0ec3c06206ebd79195d12009fd21349de4a995d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97683025"
---
# <a name="tutorial-analyze-fraudulent-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>Esercitazione: Analizzare i dati di chiamate fraudolente con Analisi di flusso e visualizzare i risultati in un dashboard di Power BI

Questa esercitazione descrive come analizzare i dati delle telefonate con Analisi di flusso di Azure. I dati delle telefonate, generati da un'applicazione client, includono chiamate fraudolente che verranno filtrate dal processo di Analisi di flusso. È possibile usare le tecniche di questa esercitazione per altri tipi di rilevamento di frodi, ad esempio quelle relative alle carte di credito o al furto di identità.

In questa esercitazione viene illustrato come:

> [!div class="checklist"]
> * Generare dati di telefonate di esempio e inviarli ad Hub eventi di Azure.
> * Creare un processo di Analisi di flusso.
> * Configurare input e output del processo.
> * Definire query per filtrare le chiamate fraudolente.
> * Testare e avviare il processo.
> * Visualizzare i risultati in Power BI.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di aver completato i passaggi seguenti:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/).
* Scaricare l'app generatore eventi di telefonata [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) dall'Area download Microsoft oppure ottenere il codice sorgente da [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator).
* Sarà necessario un account Power BI.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-an-azure-event-hub"></a>Creare un Hub di eventi di Azure

Per consentire ad Analisi di flusso di analizzare il flusso di dati delle chiamate fraudolente, è necessario che i dati vengano inviati ad Azure. In questa esercitazione si invieranno i dati ad Azure usando [Hub eventi di Azure](../event-hubs/event-hubs-about.md).

Per creare un nuovo hub eventi e inviare i dati delle chiamate, seguire la procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **Crea una risorsa** > **Internet delle cose** > **Hub eventi**.

   ![Creare un Hub eventi di Azure nel portale](media/stream-analytics-real-time-fraud-detection/find-event-hub-resource.png)
3. Compilare il riquadro **Crea spazio dei nomi** con i valori seguenti:

   |**Impostazione**  |**Valore consigliato** |**Descrizione**  |
   |---------|---------|---------|
   |Nome     | asaTutorialEventHub        |  Nome univoco che identifica lo spazio dei nomi dell'hub eventi.       |
   |Subscription     |   \<Your subscription\>      |   Selezionare una sottoscrizione di Azure in cui creare l'hub eventi.      |
   |Resource group     |   MyASADemoRG      |  Selezionare **Crea nuovo** e immettere il nome di un nuovo gruppo di risorse per l'account.       |
   |Percorso     |   Stati Uniti occidentali 2      |    Località in cui lo spazio dei nomi dell'hub eventi può essere distribuito.     |

4. Usare le opzioni predefinite per le restanti impostazioni e selezionare **Rivedi e crea**. Selezionare quindi **Crea** per avviare la distribuzione.

   ![Creare lo spazio dei nomi dell'hub eventi nel portale di Azure](media/stream-analytics-real-time-fraud-detection/create-event-hub-namespace.png)

5. Quando la distribuzione dello spazio dei nomi è stata completata, passare a **Tutte le risorse** e individuare *asaTutorialEventHub* nell'elenco delle risorse di Azure. Selezionare *asaTutorialEventHub* per aprirlo.

6. Selezionare quindi **+ Hub eventi** e immettere un **Nome** per l'hub eventi. Impostare **Conteggio partizioni** su 2.  Usare le opzioni predefinite per le impostazioni rimanenti e selezionare **Crea**. Attendere il completamento della distribuzione.

   ![Configurazione di Hub eventi nel portale di Azure](media/stream-analytics-real-time-fraud-detection/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Concedere l'accesso all'hub eventi e ottenere una stringa di connessione

Affinché un'applicazione possa inviare dati ad Hub eventi di Azure, è necessario che l'hub eventi abbia criteri che consentono l'accesso. I criteri di accesso generano una stringa di connessione che include informazioni di autorizzazione.

1. Passare all'hub eventi *MyEventHub* creato nel passaggio precedente. Selezionare **Criteri di accesso condivisi** in **Impostazioni** e quindi selezionare **+ Aggiungi**.

2. Assegnare al criterio il nome **MyPolicy** e assicurarsi che **Gestisci** sia selezionato. Selezionare quindi **Crea**.

   ![Creare criteri di accesso condiviso dell'hub eventi](media/stream-analytics-real-time-fraud-detection/create-event-hub-access-policy.png)

3. Dopo aver creato i criteri, fare clic sul relativo nome per aprirli. Trovare il valore di **Stringa di connessione - Chiave primaria**. Selezionare il pulsante **copia** accanto alla stringa di connessione.

   ![Salvare la stringa di connessione dei criteri di accesso condiviso](media/stream-analytics-real-time-fraud-detection/save-connection-string.png)

4. Incollare la stringa di connessione in un editor di testo. La stringa sarà necessaria nella sezione seguente.

   La stringa di connessione è simile a quanto segue:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   Si noti che la stringa di connessione contiene più coppie chiave-valore separate da punti e virgola: **Endpoint**, **SharedAccessKeyName**, **SharedAccessKey**, e **EntityPath**.

## <a name="start-the-event-generator-application"></a>Avviare l'applicazione generatore eventi

Prima di avviare l'app TelcoGenerator, configurarla per inviare i dati all'istanza di Hub eventi di Azure creata in precedenza.

1. Estrarre il contenuto del file [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip).
2. Aprire il file `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` in un editor di testo a scelta. Sono presenti più file `.config`, quindi assicurarsi di aprire quello corretto.

3. Aggiornare l'elemento `<appSettings>` nel file di configurazione con i dettagli seguenti:

   * Impostare il valore della chiave *EventHubName* sul valore di EntityPath nella stringa di connessione.
   * Impostare il valore della chiave *Microsoft.ServiceBus.ConnectionString* sulla stringa di connessione senza il valore di EntityPath. Assicurarsi di rimuovere il punto e virgola che precede il valore di EntityPath.

4. Salvare il file.

5. Aprire quindi una finestra di comando e passare alla cartella in cui è stata decompressa l'app TelcoGenerator. Immettere quindi il comando seguente:

   ```cmd
   .\telcodatagen.exe 1000 0.2 2
   ```

   Questo comando accetta i parametri seguenti:
   * Numero di record di dati delle chiamate all'ora.
   * Percentuale di probabilità di frode, ovvero frequenza con cui l'app deve simulare una chiamata fraudolenta. Il valore 0,2 significa che circa il 20% dei record delle chiamate apparirà come fraudolento.
   * Durata in ore, ovvero numero di ore per cui l'app deve essere eseguita. È anche possibile arrestare l'app in qualsiasi momento terminando il processo (**CTRL+C**) dalla riga di comando.

   Dopo alcuni secondi, l'app inizierà a visualizzare i record delle chiamate mentre ne esegue l'invio all'hub eventi. I dati delle telefonate contengono i campi seguenti:

   |**Record**  |**Definizione**  |
   |---------|---------|
   |CallrecTime    |  Timestamp dell'ora di inizio della chiamata.       |
   |SwitchNum     |  Commutatore telefonico usato per la connessione della chiamata. Per questo esempio i commutatori sono stringhe che rappresentano il paese/area di origine (Stati Uniti, Cina, Regno Unito, Germania o Australia).       |
   |CallingNum     |  Numero di telefono del chiamante.       |
   |CallingIMSI     |  Codice IMSI (International Mobile Subscriber Identity). Identificatore univoco del chiamante.       |
   |CalledNum     |   Numero di telefono del destinatario della chiamata.      |
   |CalledIMSI     |  Codice IMSI (International Mobile Subscriber Identity). Identificatore univoco del destinatario della chiamata.       |

## <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.

Dopo aver creato un flusso di eventi di chiamata, è possibile creare un processo di Analisi di flusso che legge i dati dall'hub eventi.

1. Per creare un processo di Analisi di flusso, passare al [portale di Azure](https://portal.azure.com/).

2. Selezionare **Crea una risorsa** e cercare **Processo di Analisi di flusso**. Selezionare il riquadro **Processo di Analisi di flusso** e fare clic su **Crea**.

3. Compilare il modulo **Nuovo processo di Analisi di flusso** con i valori seguenti:

   |**Impostazione**  |**Valore consigliato**  |**Descrizione**  |
   |---------|---------|---------|
   |Nome processo     |  ASATutorial       |   Nome univoco che identifica lo spazio dei nomi dell'hub eventi.      |
   |Sottoscrizione    |  \<Your subscription\>   |   Selezionare una sottoscrizione di Azure in cui creare il processo.       |
   |Resource group   |   MyASADemoRG      |   Selezionare **Usa esistente** e immettere il nome di un nuovo gruppo di risorse per l'account.      |
   |Percorso   |    Stati Uniti occidentali 2     |      Località in cui il processo può essere distribuito. È consigliabile posizionare il processo e l'hub eventi nella stessa area per ottenere prestazioni ottimali ed evitare di pagare il trasferimento dei dati tra aree.      |
   |Ambiente di hosting    | Cloud        |     Per la distribuzione dei processi di Analisi di flusso è possibile scegliere tra Cloud o Edge. L'opzione Cloud consente di eseguire la distribuzione nel cloud di Azure, mentre l'opzione Edge consente di eseguire la distribuzione in un dispositivo IoT Edge.    |
   |Unità di streaming     |    1       |      Le unità di streaming rappresentano le risorse di calcolo necessarie per eseguire un processo. Il valore predefinito di questa impostazione è 1. Per informazioni sul ridimensionamento delle unità di streaming, vedere [Informazioni sulle unità di flusso e su come modificarle](stream-analytics-streaming-unit-consumption.md).      |

4. Usare le opzioni predefinite per le restanti impostazioni, selezionare **Crea** e attendere il completamento della distribuzione.

   ![Creare un processo di Analisi di flusso di Azure](media/stream-analytics-real-time-fraud-detection/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>Configurare l'input del processo

Il passaggio successivo consiste nel definire un'origine di input da cui il processo può leggere i dati usando l'hub eventi creato nella sezione precedente.

1. Nel portale di Azure aprire la pagina **Tutte le risorse** e individuare il processo di Analisi di flusso *ASATutorial*.

2. Nella sezione **Topologia processo** del processo di Analisi di flusso selezionare **Input**.

3. Selezionare **+ Aggiungi input del flusso** e **Hub eventi**. Compilare il modulo di input con i valori seguenti:

   |**Impostazione**  |**Valore consigliato**  |**Descrizione**  |
   |---------|---------|---------|
   |Alias di input     |  CallStream       |  Specificare un nome descrittivo per identificare l'input. L'alias di input può contenere solo caratteri alfanumerici, trattini e caratteri di sottolineatura e deve avere una lunghezza compresa tra 3 e 63 caratteri.       |
   |Sottoscrizione    |   \<Your subscription\>      |   Selezionare la sottoscrizione di Azure in cui è stato creato l'hub eventi. L'hub eventi può trovarsi nella stessa sottoscrizione del processo di Analisi di flusso o in una sottoscrizione diversa.       |
   |Spazio dei nomi dell'hub eventi    |  asaTutorialEventHub       |  Selezionare lo spazio dei nomi dell'hub eventi creato nella sezione precedente. Nell'elenco a discesa sono presenti tutti gli spazi dei nomi degli hub eventi disponibili nella sottoscrizione corrente.       |
   |Nome dell'hub eventi    |   MyEventHub      |  Selezionare l'hub eventi creato nella sezione precedente. Nell'elenco a discesa sono presenti tutti gli hub eventi disponibili nella sottoscrizione corrente.       |
   |Nome criteri hub eventi   |  MyPolicy       |  Selezionare i criteri di accesso condiviso dell'hub eventi creati nella sezione precedente. Nell'elenco a discesa sono presenti tutti i criteri degli hub eventi disponibili nella sottoscrizione corrente.       |

4. Usare le opzioni predefinite per le restanti impostazioni e selezionare **Salva**.

   ![Configurare l'input Analisi di flusso di Azure](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>Configurare l'output del processo

L'ultimo passaggio consiste nel definire un sink di output in cui il processo può scrivere i dati trasformati. In questa esercitazione ottenere i dati di output e visualizzarli con Power BI.

1. Nel portale di Azure aprire il riquadro **Tutte le risorse** e selezionare il processo di Analisi di flusso *ASATutorial*.

2. Nella sezione **Topologia processo** del processo di Analisi di flusso selezionare l'opzione **Output**.

3. Selezionare **+ Aggiungi** > **Power BI**. Selezionare quindi **Autorizza** e seguire le istruzioni per l'autenticazione di Power BI.

:::image type="content" source="media/stream-analytics-real-time-fraud-detection/authorize-power-bi.png" alt-text="pulsante Autorizza per Power BI":::

4. Compilare il modulo di output con i dettagli seguenti e selezionare **Salva**:

   |**Impostazione**  |**Valore consigliato**  |
   |---------|---------|
   |Alias di output  |  MyPBIoutput  |
   |Area di lavoro del gruppo| Area di lavoro personale |
   |Nome del set di dati  |   ASAdataset  |
   |Nome tabella |  ASATable  |
   | Modalità di autenticazione | Token utente |

   ![Configurare l'output Analisi di flusso di Azure](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-output.png)

   Questa esercitazione usa la modalità di autenticazione *Token utente*. Per usare l'identità gestita, vedere [Usare l'identità gestita per autenticare il processo di Analisi di flusso di Azure per Power BI](powerbi-output-managed-identity.md).

## <a name="create-queries-to-transform-real-time-data"></a>Creare query per trasformare i dati in tempo reale

A questo punto il processo di Analisi di flusso è configurato per la lettura di un flusso di dati in ingresso. Il passaggio successivo consiste nel creare una query che analizzi i dati in tempo reale. Le query usano un linguaggio simile a SQL che include alcune estensioni specifiche di Analisi di flusso.

In questa sezione dell'esercitazione si creeranno e testeranno varie query per apprendere alcuni modi in cui trasformare un flusso di input per l'analisi. 

Le query qui create visualizzeranno semplicemente i dati trasformati. In una sezione successiva i dati trasformati verranno scritti in Power BI.

Per altre informazioni sul linguaggio, vedere le [Informazioni di riferimento sul linguaggio di query di analisi dei flussi di Azure](/stream-analytics-query/stream-analytics-query-language-reference).

### <a name="test-using-a-pass-through-query"></a>Test mediante una query pass-through

Per archiviare ogni evento, è possibile usare una query pass-through per leggere tutti i campi nel payload dell'evento.

1. Passare al processo di Analisi di flusso nel portale di Azure e selezionare **Query** in *Topologia processo*. 

2. Nella finestra di query immettere la query seguente:

   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Come con SQL, le parole chiave non effettuano distinzione tra maiuscole e minuscole e gli spazi non sono rilevanti.

    In questa query `CallStream` è l'alias specificato quando è stato creato l'input. In presenza di un alias diverso, usare questo nome.

3. Selezionare **Test query**.

    Il processo di Analisi di flusso esegue la query sui dati di esempio dall'input e visualizza l'output nella parte inferiore della finestra, a indicare che l'hub eventi e il processo di Analisi di flusso sono configurati correttamente.

    :::image type="content" source="media/stream-analytics-real-time-fraud-detection/sample-output-passthrough.png" alt-text="Output di esempio della query di test":::

    Il numero esatto di record visualizzati dipenderà dal numero di record acquisiti nell'esempio.

### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Ridurre il numero di campi usando una proiezione di colonna

In molti casi, l'analisi non necessita di tutte le colonne dal flusso di input. È possibile usare una query per proiettare un set di campi restituiti ridotto rispetto alla query pass-through.

Eseguire la query seguente e osservare l'output.

```SQL
SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNumCalledNum 
FROM 
    CallStream
```

### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Numero di chiamate in ingresso per area: finestra a cascata con aggregazione

Si supponga di voler contare il numero di chiamate in ingresso per ogni area. Nei dati di streaming, se si vogliono eseguire funzioni di aggregazione come il conteggio, è necessario segmentare il flusso in unità temporali, perché il flusso di dati stesso è effettivamente infinito. A tale scopo, usare una [funzione finestra](stream-analytics-window-functions.md) di Analisi di flusso. Sarà quindi possibile usare i dati all'interno di tale finestra come unità.

Per questa trasformazione si intende creare una sequenza di finestre temporali che non si sovrappongano. Ogni finestra includerà un set distinto di dati che è possibile raggruppare e aggregare. A questo tipo di finestra si fa riferimento con la locuzione *finestra a cascata*. All'interno della finestra a cascata è possibile ottenere un conteggio delle chiamate in ingresso raggruppate per `SwitchNum`, che rappresenta il paese/la regione in cui la chiamata ha avuto origine.

1. Incollare la query seguente nell'editor di query:

    ```SQL
    SELECT 
        System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
    FROM
        CallStream TIMESTAMP BY CallRecTime 
    GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
    ```

    Questa query usa la parola chiave `Timestamp By` nella clausola `FROM` per specificare il campo del timestamp nel flusso di input da usare per definire la finestra a cascata. In questo caso la finestra divide i dati in segmenti per il campo `CallRecTime` in ogni record. Se non è specificato alcun campo, l'operazione di windowing usa l'ora in cui ogni evento arriva all'hub. Vedere la sezione relativa a tempo di arrivo e tempo di applicazione in [Informazioni di riferimento sul linguaggio di query per l'analisi di flusso](/stream-analytics-query/stream-analytics-query-language-reference). 

    La proiezione include `System.Timestamp`, che restituisce un timestamp per la fine di ogni finestra. 

    Per specificare che si intende usare una finestra a cascata, usare la funzione [TUMBLINGWINDOW](/stream-analytics-query/tumbling-window-azure-stream-analytics) nella clausola `GROUP BY`. Specificare nella funzione un'unità di tempo (da un microsecondo a un giorno) e una dimensione della finestra (numero di unità). In questo esempio la finestra a cascata è costituita da intervalli di 5 secondi, per ottenere un conteggio in base al paese/regione per chiamate ogni 5 secondi.

2. Selezionare **Test query**. Si noti nei risultati che i timestamp in **WindowEnd** sono in incrementi di 5 secondi.

### <a name="detect-sim-fraud-using-a-self-join"></a>Rilevare una frode SIM mediante self-join

Per questo esempio, considerare come uso fraudolento le chiamate che provengono dallo stesso utente ma in posizioni diverse, a 5 secondi l'una dall'altra. Ad esempio, lo stesso utente non può eseguire legittimamente una chiamata dagli Stati Uniti e dall'Australia nello stesso momento.

Per verificare questi casi, è possibile usare un self-join dei dati di streaming per creare un join del flusso a se stesso in base al valore `CallRecTime`. Sarà quindi possibile cercare i record delle chiamate in cui il valore `CallingIMSI` (numero di origine) è lo stesso, ma il valore `SwitchNum` (paese/regione di origine) non coincide.

Quando si usa un join con i dati di streaming, il join deve garantire alcuni limiti per la distanza di separazione delle righe corrispondenti nel tempo. Come indicato in precedenza, i dati di streaming sono effettivamente infiniti. I limiti di tempo per la relazione sono specificati all'interno della clausola `ON` del join usando la funzione `DATEDIFF`. In questo caso il join è basato su un intervallo di 5 secondi di dati di chiamata.

1. Incollare la query seguente nell'editor di query:

    ```SQL
    SELECT  System.Timestamp as Time, 
        CS1.CallingIMSI, 
        CS1.CallingNum as CallingNum1, 
        CS2.CallingNum as CallingNum2, 
        CS1.SwitchNum as Switch1, 
        CS2.SwitchNum as Switch2 
    FROM CallStream CS1 TIMESTAMP BY CallRecTime 
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime 
        ON CS1.CallingIMSI = CS2.CallingIMSI 
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5 
    WHERE CS1.SwitchNum != CS2.SwitchNum
    ```

    Questa query è simile a un join SQL, ad eccezione della funzione `DATEDIFF` nel join. Si tratta di una versione di `DATEDIFF` specifica di Analisi di flusso che deve apparire nella clausola `ON...BETWEEN`. I parametri sono un'unità di tempo (secondi in questo esempio) e gli alias delle due origini per il join. Si tratta di una differenza rispetto alla funzione `DATEDIFF` SQL standard.

    La clausola `WHERE` include la condizione che contrassegna la chiamata fraudolenta: i commutatori di origine non sono uguali.

2. Selezionare **Test query**. Esaminare l'output e quindi selezionare **Salva query**.

## <a name="start-the-job-and-visualize-output"></a>Avviare il processo e visualizzare l'output

1. Per avviare il processo, passare al riquadro **Panoramica** del processo e fare clic su **Avvia**.

2. Selezionare **Ora** come orario di avvio per l'output del processo e selezionare **Avvia**. È possibile visualizzare lo stato del processo nella barra di notifica.

3. Dopo il completamento del processo, passare a [Power BI](https://powerbi.com/) e accedere con l'account aziendale o dell'istituto di istruzione. Se la query del processo di Analisi di flusso restituisce risultati, il set di dati *ASAdataset* creato è presente nella scheda **Set di dati**.

4. Nell'area di lavoro di Power BI selezionare **+ Crea** per creare un nuovo dashboard denominato *Fraudulent Calls*.

5. Nella parte superiore della finestra selezionare **Modifica** e **Aggiungi riquadro**. Selezionare quindi **Dati in streaming personalizzati** e **Avanti**. Scegliere **ASAdataset** in **Set di dati personali**. Selezionare **Scheda** nell'elenco a discesa **Tipo di visualizzazione** e aggiungere **fraudulent calls** a **Campi**. Selezionare **Avanti** per immettere un nome per il riquadro e quindi selezionare **Applica** per creare il riquadro.

   ![Creare i riquadri della dashboard di Power BI](media/stream-analytics-real-time-fraud-detection/create-power-bi-dashboard-tiles.png)

6. Ripetere il passaggio 5 con le opzioni seguenti:
   * Per Tipo di visualizzazione selezionare Grafico a linee.
   * Aggiungere un asse e selezionare **windowend**.
   * Aggiungere un valore e selezionare **fraudulentcalls**.
   * Per **Intervallo di tempo da visualizzare**  selezionare gli ultimi 10 minuti.

7. Dopo l'aggiunta di entrambi i riquadri, il dashboard è simile a quello dell'esempio seguente. Si noti che, se l'applicazione mittente dell'hub eventi e l'applicazione di Analisi di flusso sono in esecuzione, il dashboard di Power BI viene aggiornato periodicamente all'arrivo di nuovi dati.

   ![Visualizzare i risultati nella dashboard di Power BI](media/stream-analytics-real-time-fraud-detection/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>Incorporamento del dashboard di Power BI in un'applicazione Web

Per questa parte dell'esercitazione si userà un'applicazione Web [ASP.NET](https://asp.net/) di esempio creata dal team di Power BI per incorporare il dashboard. Per altre informazioni sull'incorporamento di dashboard, vedere l'articolo [Incorporamento con Power BI](/power-bi/developer/embedding).

Per configurare l'applicazione, passare al repository GitHub [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) e seguire le istruzioni nella sezione **User Owns Data** (Utente proprietario dei dati). Usare gli URL di reindirizzamento e della home page nella sottosezione **integrate-web-app**. Dal momento che si sta usando l'esempio relativo al dashboard, usare il codice di esempio **integrate-web-app** disponibile nel [repository GitHub](https://github.com/microsoft/PowerBI-Developer-Samples/tree/master/.NET%20Framework/Embed%20for%20your%20organization/).
Una volta che l'applicazione è in esecuzione nel browser, seguire questa procedura per incorporare il dashboard creato in precedenza nella pagina Web:

1. Selezionare **Accedi a Power BI**, per concedere all'applicazione l'accesso ai dashboard nell'account Power BI.

2. Fare clic sul pulsante **Get Dashboards** (Ottieni dashboard), per visualizzare i dashboard dell'account in una tabella. Individuare il nome del dashboard creato in precedenza, ovvero **powerbi-embedded-dashboard** e copiare il valore di **EmbedUrl** corrispondente.

3. Infine, incollare **EmbedUrl** nel campo di testo corrispondente e selezionare **Embed Dashboard** (Incorpora dashboard). È ora possibile visualizzare lo stesso dashboard incorporato in un'applicazione Web.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un processo di Analisi di flusso di esempio, sono stati analizzati i dati in ingresso e i risultati sono stati visualizzati in un dashboard di Power BI. Per altre informazioni sui processi di Analisi di flusso, continuare con l'esercitazione successiva:

> [!div class="nextstepaction"]
> [Eseguire Funzioni di Azure in processi di Analisi di flusso](stream-analytics-with-azure-functions.md)