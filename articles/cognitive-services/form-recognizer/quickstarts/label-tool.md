---
title: 'Avvio rapido: Etichettare moduli, eseguire il training di un modello e analizzare i moduli usando lo strumento di etichettatura campioni - Riconoscimento modulo'
titleSuffix: Azure Cognitive Services
description: In questo argomento di avvio rapido si userà lo strumento di etichettatura campioni di Riconoscimento modulo per etichettare manualmente documenti modulo. Con i documenti etichettati, verrà quindi eseguito il training di un modello di elaborazione di documenti personalizzato, che verrà usato per estrarre coppie chiave-valore.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: elaborazione di documenti
ms.openlocfilehash: f83e13155fa851a06c8b7d36b87c3038257c0b3a
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864381"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
# <a name="train-a-custom-model-using-the-sample-labeling-tool"></a>Eseguire il training di un modello personalizzato usando lo strumento di assegnazione di etichette di esempio

In questo argomento di avvio rapido si userà l'API REST Riconoscimento modulo con lo strumento di etichettatura campioni per eseguire il training di un modello di elaborazione di documenti personalizzato con dati etichettati manualmente. Per altre informazioni sull'apprendimento supervisionato con Riconoscimento modulo, vedere la sezione [Eseguire il training con le etichette](../overview.md#train-with-labels) della panoramica.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prerequisites"></a>Prerequisiti

Per completare questo argomento di avvio rapido è necessario disporre di quanto segue:

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="creare una risorsa di Riconoscimento modulo"  target="_blank">creare una risorsa di Riconoscimento modulo </a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
  * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Riconoscimento modulo. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
  * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.
* Un set di almeno sei moduli dello stesso tipo. Questi dati verranno usati per eseguire il training del modello e testare un modulo. È possibile usare un [set di dati di esempio](https://go.microsoft.com/fwlink/?linkid=2090451) (scaricare ed estrarre il file *sample_data.zip*) per questa guida di avvio rapido. Caricare i file di training nella radice di un contenitore di archiviazione BLOB in un account di archiviazione di Azure con livello di prestazioni Standard.

## <a name="create-a-form-recognizer-resource"></a>Creare una risorsa di riconoscimento modulo

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="try-it-out"></a>Provare questa operazione

Per provare lo strumento di etichettatura campioni di Riconoscimento modulo online, visitare il sito Web [FOTT](https://fott-preview.azurewebsites.net/).

### <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)

> [!div class="nextstepaction"]
> [Provare i modelli predefiniti](https://fott-preview.azurewebsites.net/)

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!div class="nextstepaction"]
> [Provare i modelli predefiniti](https://fott.azurewebsites.net/)

---

Per provare il servizio Riconoscimento modulo, è necessario avere una sottoscrizione di Azure ([crearne una gratuita](https://azure.microsoft.com/free/cognitive-services)), oltre a un endpoint e a una chiave della [risorsa Riconoscimento modulo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer).

## <a name="set-up-the-sample-labeling-tool"></a>Configurare lo strumento di etichettatura campioni

Per eseguire lo strumento di etichettatura campioni, verrà usato il motore Docker. Per configurare il contenitore Docker, seguire questa procedura. Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).

> [!TIP]
> È inoltre disponibile lo strumento OCR Form Labeling Tool come progetto open source in GitHub. Si tratta di un'applicazione Web TypeScript creata con React + Redux. Per altre informazioni o per contribuire, vedere il repository dello [strumento OCR Form Labeling Tool](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md#run-as-web-application). Per provare lo strumento online, visitare il [sito Web di FOTT](https://fott.azurewebsites.net/).

1. Installare prima di tutto Docker in un computer host. Questa guida illustra come usare un computer locale come host. Se si vuole usare un servizio di hosting Docker in Azure, vedere la guida pratica [Distribuire lo strumento di etichettatura di esempio](../deploy-label-tool.md).

   Il computer host deve soddisfare i requisiti hardware seguenti:

    | Contenitore | Minima | Consigliato|
    |:--|:--|:--|
    |Strumento di etichettatura campioni|2 core, 4 GB di memoria|4 core, 8 GB di memoria|

   Installare Docker nel computer seguendo le istruzioni appropriate per il sistema operativo in uso:

   * [Windows](https://docs.docker.com/docker-for-windows/)
   * [macOS](https://docs.docker.com/docker-for-mac/)
   * [Linux](https://docs.docker.com/install/)

1. Ottenere il contenitore dello strumento di etichettatura campioni con il comando `docker pull`.

### <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)

```console
 docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```console
docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
```

---
</br>
  3. Ora è possibile eseguire il contenitore con `docker run`.

### <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)

```console
 docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview eula=accept
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```console
docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
```

---

   Questo comando rende disponibile lo strumento di etichettatura campioni tramite un Web browser. Passare a `http://localhost:3000`.

> [!NOTE]
> È anche possibile etichettare i documenti ed eseguire il training dei modelli usando l'API REST Riconoscimento modulo. Per eseguire il training e l'analisi con l'API REST, vedere [Eseguire il training con le etichette usando l'API REST e Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md).

## <a name="set-up-input-data"></a>Configurare i dati di input

Prima di tutto, assicurarsi che tutti i documenti di training abbiano lo stesso formato. Se si usano moduli in più formati, organizzarli in sottocartelle basate sul formato comune. Quando si esegue il training, è necessario indirizzare l'API a una sottocartella.

### <a name="configure-cross-domain-resource-sharing-cors"></a>Configurare la condivisione di risorse tra domini (CORS)

Abilitare CORS nell'account di archiviazione. Selezionare l'account di archiviazione nell'portale di Azure, quindi scegliere la scheda **CORS** nel riquadro sinistro. Nella riga inferiore inserire i valori seguenti. Selezionare **Save (Salva** ) nella parte superiore.

* Origini consentite = *
* Metodi consentiti = \[seleziona tutto\]
* Intestazioni consentite = *
* Intestazioni esposte = *
* Età massima = 200

> [!div class="mx-imgBorder"]
> ![Configurazione di CORS nel portale di Azure](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>Connettere lo strumento di etichettatura campioni

 Lo strumento di assegnazione di etichette di esempio si connette a un'origine (moduli caricati originali) e a una destinazione (etichette e dati di output creati).

Le connessioni possono essere configurate e condivise tra progetti. Si basano su un modello di provider estendibile, quindi è possibile aggiungere facilmente nuovi provider di origine/destinazione.

Per creare una nuova connessione, selezionare l'icona **nuova** connessione (plug) nella barra di spostamento a sinistra.

Compilare i campi con i valori seguenti:

* **Nome visualizzato**: il nome visualizzato della connessione.
* **Descrizione**: la descrizione del progetto.
* **URL di firma di accesso condiviso**: l'URL di firma di accesso condiviso del contenitore di archiviazione BLOB di Azure. [!INCLUDE [get SAS URL](../includes/sas-instructions.md)]

   :::image type="content" source="../media/quickstarts/get-sas-url.png" alt-text="Recupero dell'URL di firma di accesso condiviso":::

:::image type="content" source="../media/label-tool/connections.png" alt-text="Impostazioni di connessione dello strumento di etichettatura campioni.":::

## <a name="create-a-new-project"></a>Creare un nuovo progetto

Nello strumento di etichettatura campioni vengono archiviate le configurazioni e le impostazioni dei progetti. Creare un nuovo progetto e compilare i campi con i valori seguenti:

* **Nome visualizzato**: il nome visualizzato del progetto
* **Token di sicurezza**: alcuni progetti possono includere valori sensibili, ad esempio chiavi di API o altri segreti condivisi. Ogni progetto genererà un token di sicurezza che è possibile usare per crittografare/decrittografare le impostazioni di progetti sensibili. È possibile trovare i token di sicurezza nelle impostazioni dell'applicazione selezionando l'icona a forma di ingranaggio nella parte inferiore della barra di spostamento a sinistra.
* **Connessione protetta**: la connessione ad archiviazione BLOB di Azure creata nel passaggio precedente che si vuole usare per questo progetto.
* **Percorso cartella** (facoltativo): se i moduli di origine si trovano in una cartella del contenitore BLOB, specificare qui il relativo nome
* **URI del servizio Riconoscimento modulo**: l'URL dell'endpoint di Riconoscimento modulo.
* **Chiave API**: la chiave di sottoscrizione di Riconoscimento modulo.
* **Descrizione** (facoltativo): la descrizione del progetto

:::image type="content" source="../media/label-tool/new-project.png" alt-text="Pagina di nuovo progetto nello strumento di etichettatura campioni.":::

## <a name="label-your-forms"></a>Etichettare i moduli

Quando si crea o si apre un progetto, viene visualizzata la finestra principale dell'editor di tag. L'editor di tag è costituito da tre parti:

* Un riquadro di anteprima ridimensionabile che contiene un elenco scorrevole di moduli provenienti dalla connessione di origine.
* Il riquadro principale dell'editor che consente di applicare tag.
* Il riquadro dell'editor di tag che consente di modificare, bloccare, riordinare ed eliminare tag.

### <a name="identify-text-and-tables"></a>Identificare testo e tabelle 

Selezionare **Esegui OCR su tutti i file** nel riquadro sinistro per ottenere le informazioni sul layout del testo e della tabella per ogni documento. Lo strumento di etichettatura traccerà rettangoli di selezione intorno a ogni elemento di testo.

Lo strumento per l'assegnazione di etichette indicherà anche quali tabelle sono state estratte automaticamente. Selezionare l'icona tabella/griglia nella parte sinistra del documento per visualizzare la tabella estratta. In questa guida di avvio rapido il contenuto della tabella, poiché è stato estratto automaticamente, non verrà etichettato, ma ci si baserà sull'estrazione automatica.

:::image type="content" source="../media/label-tool/table-extraction.png" alt-text="Visualizzazione della tabella nello strumento di etichettatura campioni.":::

Nella versione 2.1, se il documento di training non contiene un valore compilato, è possibile creare una casella in cui il valore deve essere. Usare l' **area di attrazione** nell'angolo superiore sinistro della finestra per rendere l'area taggable.

### <a name="apply-labels-to-text"></a>Applicare le etichette al testo

Successivamente, verranno creati tag (etichette) e applicati agli elementi di testo che si desidera vengano analizzati dal modello.

### <a name="v20"></a>[v2.0](#tab/v2-1)  

1. Usare prima di tutto il riquadro dell'editor di tag per creare i tag da identificare.
   1. Selezionare **+** per creare un nuovo tag.
   1. Immettere il nome del tag.
   1. Premere INVIO per salvare il tag.
1. Nell'editor principale selezionare le parole dagli elementi di testo evidenziati o da un'area disegnata.
1. Selezionare il tag che si desidera applicare oppure premere il tasto di scelta rapida corrispondente. I tasti numerici vengono assegnati come tasti di scelta rapida per i primi 10 tag. È possibile riordinare i tag usando le icone delle frecce su e giù nel riquadro dell'editor di tag.
    > [!Tip]
    > Quando si etichettano i form, tenere presenti i suggerimenti seguenti:
    >
    > * È possibile applicare un unico tag a ogni elemento di testo selezionato.
    > * Ogni tag può essere applicato una sola volta per pagina. Se un valore viene visualizzato più volte nella stessa pagina, creare tag diversi per ogni istanza. Ad esempio: "invoice# 1", "invoice# 2" e così via.
    > * I tag non possono estendersi in più pagine.
    > * Etichettare i valori così come appaiono nel modulo. Non provare a dividere un valore in due parti con due tag diversi. Ad esempio, un campo di indirizzo deve essere etichettato con un singolo tag anche se si estende su più righe.
    > * Non includere le chiavi nei campi etichettati, ma solo i valori.
    > * I dati delle tabelle dovrebbero essere rilevati automaticamente e saranno disponibili nel file JSON finale di output. Tuttavia, se il modello non riesce a rilevare tutti i dati di una tabella, è anche possibile etichettare manualmente questi campi. Assegnare un'etichetta diversa a ogni cella della tabella. Se i moduli includono tabelle con un numero variabile di righe, assicurarsi di etichettare almeno un modulo con la tabella più grande possibile.
    > * Usare i pulsanti a destra del segno **+** per cercare, rinominare, riordinare ed eliminare i tag.
    > * Per rimuovere un tag applicato senza eliminarlo, selezionare il rettangolo con tag nella visualizzazione del documento e premere CANC.
    >

### <a name="v20"></a>[v2.0](#tab/v2-0)

1. Usare prima di tutto il riquadro dell'editor di tag per creare i tag da identificare.
   1. Selezionare **+** per creare un nuovo tag.
   1. Immettere il nome del tag.
   1. Premere INVIO per salvare il tag.
1. Nell'editor principale selezionare parole dagli elementi di testo evidenziati.
1. Selezionare il tag che si desidera applicare oppure premere il tasto di scelta rapida corrispondente. I tasti numerici vengono assegnati come tasti di scelta rapida per i primi 10 tag. È possibile riordinare i tag usando le icone delle frecce su e giù nel riquadro dell'editor di tag.
    > [!Tip]
    > Quando si etichettano i form, tenere presenti i suggerimenti seguenti:
    >
    > * È possibile applicare un unico tag a ogni elemento di testo selezionato.
    > * Ogni tag può essere applicato una sola volta per pagina. Se un valore viene visualizzato più volte nella stessa pagina, creare tag diversi per ogni istanza. Ad esempio: "invoice# 1", "invoice# 2" e così via.
    > * I tag non possono estendersi in più pagine.
    > * Etichettare i valori così come appaiono nel modulo. Non provare a dividere un valore in due parti con due tag diversi. Ad esempio, un campo di indirizzo deve essere etichettato con un singolo tag anche se si estende su più righe.
    > * Non includere le chiavi nei campi etichettati, ma solo i valori.
    > * I dati delle tabelle dovrebbero essere rilevati automaticamente e saranno disponibili nel file JSON finale di output. Tuttavia, se il modello non riesce a rilevare tutti i dati di una tabella, è anche possibile etichettare manualmente questi campi. Assegnare un'etichetta diversa a ogni cella della tabella. Se i moduli includono tabelle con un numero variabile di righe, assicurarsi di etichettare almeno un modulo con la tabella più grande possibile.
    > * Usare i pulsanti a destra del segno **+** per cercare, rinominare, riordinare ed eliminare i tag.
    > * Per rimuovere un tag applicato senza eliminarlo, selezionare il rettangolo con tag nella visualizzazione del documento e premere CANC.
>

---
---

:::image type="content" source="../media/label-tool/main-editor-2-1.png" alt-text="Finestra dell'editor principale dello strumento di etichettatura campioni.":::

Seguire i passaggi precedenti per etichettare almeno cinque moduli.

### <a name="specify-tag-value-types"></a>Specificare i tipi di valore di tag

È possibile impostare il tipo di dati previsto per ogni tag. Aprire il menu di scelta rapida a destra di un tag e selezionare un tipo dal menu. Questa funzionalità consente all'algoritmo di rilevamento di creare presupposti che consentiranno di migliorare l'accuratezza del rilevamento del testo. Garantisce inoltre che i valori rilevati vengano restituiti in un formato standardizzato nell'output JSON finale. Le informazioni sul tipo di valore vengono salvate nel file **fields.json** nello stesso percorso dei file di etichette.

> [!div class="mx-imgBorder"]
> ![Selezione del tipo di valore con lo strumento di etichettatura di esempio](../media/whats-new/value-type.png)

Sono attualmente supportati i tipi di valore e le varianti seguenti:

* `string`
  * predefinito, `no-whitespaces`, `alphanumeric`

* `number`
  * predefinito, `currency`

* `date`
  * predefinito, `dmy`, `mdy`, `ymd`

* `time`
* `integer`
* `selectionMark` – _Novità in v2.1-preview.1_

> [!NOTE]
> Vedere queste regole per la formattazione della data:
>
> Per il corretto funzionamento della formattazione della data, è necessario specificare un formato (`dmy`, `mdy`, `ymd`).
>
> Come delimitatori di data è possibile usare i caratteri seguenti: `, - / . \`. Non è possibile usare uno spazio come delimitatore. Ad esempio:
>
> * 01.01.2020
> * 01-01-2020
> * 01/01/2020
>
> Il giorno e il mese possono essere scritti con una o due cifre e l'anno con due o quattro cifre:
>
> * 1-1-2020
> * 1-01-20
>
> Se una stringa di data è costituita da otto cifre, il delimitatore è facoltativo:
>
> * 01012020
> * 01 01 2020
>
> Il mese può anche essere scritto con il nome completo o abbreviato. Se viene usato il nome, i caratteri delimitatori sono facoltativi. Tuttavia, questo formato può essere riconosciuto con meno accuratezza rispetto ad altri.
>
> * 01/gen/2020
> * 01gen2020
> * 01 gen 2020

### <a name="label-tables-v21-only"></a>Etichetta tabelle (solo versione 2.1)

In alcuni casi, i dati possono essere più preferiti a essere etichettati come tabella anziché come coppie chiave-valore. In questo caso, è possibile creare un tag di tabella facendo clic su "Aggiungi un nuovo tag di tabella", specificare se la tabella avrà un numero fisso di righe o un numero variabile di righe a seconda del documento e di definire lo schema.

:::image type="content" source="../media/label-tool/table-tag.png" alt-text="Configurazione di un tag di tabella.":::

Dopo aver definito il tag Table, contrassegnare i valori della cella.

:::image type="content" source="../media/table-labeling.png" alt-text="Assegnazione di etichette a una tabella.":::

## <a name="train-a-custom-model"></a>Eseguire il training di un modello personalizzato

Per aprire la pagina di training, scegliere l'icona del treno nel riquadro sinistro. Quindi selezionare il pulsante di **Train** per iniziare a eseguire il training del modello. Al termine del processo di training, verranno visualizzate le informazioni seguenti:

* **ID modello**: l'ID del modello creato e sottoposto a training. Ogni chiamata al training crea un nuovo modello con un proprio ID. Copiare questa stringa in un posto sicuro, perché sarà necessaria per eseguire le chiamate di previsione tramite l'[API REST](./client-library.md?pivots=programming-language-rest-api) o la [libreria client](./client-library.md).
* **Average Accuracy** (Accuratezza media): l'accuratezza media del modello. Per migliorare l'accuratezza del modello, è possibile assegnare etichette a moduli aggiuntivi e rieseguire il training per creare un nuovo modello. Per iniziare, è consigliabile etichettare cinque moduli ed aggiungerne altri se necessario.
* L'elenco dei tag e l'accuratezza stimata per ognuno.


:::image type="content" source="../media/label-tool/train-screen.png" alt-text="Visualizzazione del training.":::

Al termine del training, esaminare il valore di **Average Accuracy** (Accuratezza media). Se è basso, è necessario aggiungere altri documenti di input e ripetere i passaggi precedenti. I documenti già etichettati rimarranno nell'indice del progetto.

> [!TIP]
> È anche possibile eseguire il processo di training con una chiamata API REST. Per informazioni su come eseguire questa operazione, vedere [Eseguire il training con le etichette usando Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md).

## <a name="compose-trained-models"></a>Comporre modelli con training

### <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)

Con lo strumento di composizione modelli è possibile comporre fino a 100 modelli in un solo ID modello. Quando si chiama analizza con l'oggetto composto `modelID` , il riconoscimento del modulo classifica innanzitutto il form inviato, sceglie il modello di corrispondenza migliore e quindi restituisce i risultati per quel modello. Questa operazione è utile quando i moduli in ingresso possono appartenere a uno dei diversi modelli.

Per comporre modelli nello strumento di assegnazione di etichette di esempio, selezionare l'icona di composizione del modello (freccia di Unione) a sinistra. A sinistra selezionare i modelli da comporre insieme. I modelli contrassegnati con l'icona della freccia sono già composti.
Scegliere il **pulsante Componi**. Nella finestra popup assegnare un nome al nuovo modello composto e selezionare **compose**. Al termine dell'operazione, il modello appena composto verrà visualizzato nell'elenco.

:::image type="content" source="../media/label-tool/model-compose.png" alt-text="Visualizzazione dell'esperienza utente di composizione modelli.":::

### <a name="v20"></a>[v2.0](#tab/v2-0)

Questa funzionalità è attualmente disponibile in v2.1. preview.

---

## <a name="analyze-a-form"></a>Analizzare un modulo

Selezionare l'icona stima (lampadina) a sinistra per testare il modello. Caricare un documento modulo che non è stato usato nel processo di training. Scegliere quindi il pulsante **stima** a destra per ottenere le stime chiave/valore per il modulo. Lo strumento applicherà i tag nei riquadri di selezione e segnalerà l'attendibilità di ognuno.

> [!TIP]
> È anche possibile eseguire l'API di analisi con una chiamata REST. Per informazioni su come eseguire questa operazione, vedere [Eseguire il training con le etichette usando Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md).

## <a name="improve-results"></a>Migliorare i risultati

A seconda dell'accuratezza indicata, è possibile scegliere di eseguire ulteriori processi di training per migliorare il modello. Dopo aver eseguito una previsione, esaminare i valori di attendibilità per ognuno dei tag applicati. Se il valore di training di accuratezza medio era elevato, ma i punteggi di confidenza sono bassi (o se i risultati non sono accurati), è necessario aggiungere il file di stima al set di training, assegnargli un'etichetta e eseguire di nuovo il training.

L'accuratezza media segnalata, i punteggi di confidenza e l'accuratezza effettiva possono risultare incoerenti quando i documenti analizzati sono diversi da quelli utilizzati per il training. Tenere presente che alcuni documenti hanno un aspetto simile quando vengono visualizzati dalle persone ma sembrano diversi per il modello di intelligenza artificiale. Ad esempio, è possibile eseguire il training con un tipo di modulo con due varianti, in cui il set di training è costituito per il 20% dalla variante A e per l'80% dalla variante B. Durante la previsione, è probabile che i punteggi di attendibilità per i documenti della variante A siano inferiori.

## <a name="save-a-project-and-resume-later"></a>Salvare un progetto e riprenderlo in seguito

Per riprendere il progetto in un secondo momento o in un altro browser, è necessario salvare il relativo token di sicurezza e reimmetterlo in seguito.

### <a name="get-project-credentials"></a>Ottenere le credenziali del progetto

Passare alla pagina delle impostazioni del progetto (icona del dispositivo di scorrimento) e prendere nota del nome del token di sicurezza. Passare quindi alle impostazioni dell'applicazione (icona dell'ingranaggio), che visualizzano tutti i token di sicurezza nell'istanza del browser corrente. Trovare il token di sicurezza del progetto e copiarne il nome e il valore della chiave in un posto sicuro.

### <a name="restore-project-credentials"></a>Ripristinare le credenziali del progetto

Quando si vuole riprendere il progetto, è prima di tutto necessario creare una connessione allo stesso contenitore di archiviazione BLOB. A tale scopo, ripetere i passaggi precedenti. Quindi, passare alla pagina di impostazioni dell'applicazione (icona dell'ingranaggio) e verificare se il token di sicurezza del progetto è presente. Se non lo è, aggiungere un nuovo token di sicurezza e sovrascrivere il nome e la chiave del token del passaggio precedente. Selezionare **Save (Salva** ) per mantenere le impostazioni.

### <a name="resume-a-project"></a>Riprendere un progetto

Infine, passare alla pagina principale (icona della casa) e selezionare **Apri progetto cloud**. Selezionare quindi la connessione all'archiviazione BLOB e il file con estensione **fott** del progetto. L'applicazione caricherà tutte le impostazioni del progetto perché contiene il token di sicurezza.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come usare lo strumento di etichettatura campioni di Riconoscimento modulo per eseguire il training di un modello con dati etichettati manualmente. Per creare un'utilità personalizzata per etichettare i dati di training, usare le API REST relative ai dati di training etichettati.

> [!div class="nextstepaction"]
> [Eseguire il training con le etichette usando Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)

* [Informazioni su Riconoscimento modulo](../overview.md)
* [Avvio rapido di Riconoscimento modulo](client-library.md)
