---
title: Effettuare richieste con Postman
titleSuffix: Azure Digital Twins
description: Informazioni su come configurare e usare il post per testare le API dei dispositivi gemelli digitali di Azure.
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: d4a6e25578cd26b10b34f74a9f859d4957cc553b
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783813"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Come usare il post per inviare richieste alle API dei dispositivi gemelli digitali di Azure

Il [post](https://www.getpostman.com/) è uno strumento di test REST che fornisce funzionalità di richiesta http chiave in un'interfaccia utente grafica basata su desktop e plug-in. È possibile usarlo per creare richieste HTTP e inviarle alle [API REST di Digital gemelli di Azure](how-to-use-apis-sdks.md).

Questo articolo descrive come configurare il [client REST di post](https://www.getpostman.com/) per interagire con le API di Azure Digital gemelli, seguendo questa procedura:

1. Usare l'interfaccia della riga di comando di Azure per [**ottenere un Bearer token**](#get-bearer-token) che si userà per eseguire richieste API in un altro utente.
1. Configurare una [**raccolta di post**](#about-postman-collections) e configurare il client REST per l'uso del Bearer token per l'autenticazione. Quando si configura la raccolta, è possibile scegliere una delle opzioni seguenti:
    1. [**Importare**](#import-collection-of-azure-digital-twins-apis) una raccolta precompilata di richieste API per i dispositivi gemelli digitali di Azure.
    1. [**Creare**](#create-your-own-collection) una raccolta personalizzata da zero.
1. [**Aggiungere richieste**](#add-an-individual-request) alla raccolta configurata e inviarle alle API dei dispositivi gemelli digitali di Azure.

I dispositivi gemelli digitali di Azure hanno due set di API che è possibile usare: **piano dati** e **piano di controllo**. Per altre informazioni sulla differenza tra questi set di API, vedere [*How-to: Use the Azure Digital Twins API and SDKs*](how-to-use-apis-sdks.md). Questo articolo contiene informazioni per entrambi i set di API.

## <a name="prerequisites"></a>Prerequisiti

Per continuare a usare il post per accedere alle API dei dispositivi gemelli digitali di Azure, è necessario configurare un'istanza di dispositivi gemelli digitali di Azure e scaricare il post. Nella parte restante di questa sezione vengono illustrati questi passaggi.

### <a name="set-up-azure-digital-twins-instance"></a>Configurare l'istanza di Gemelli digitali di Azure

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Scarica il post

Scaricare quindi la versione desktop del client di post. Passare a [*www.getpostman.com/Apps*](https://www.getpostman.com/apps) e seguire le istruzioni per scaricare l'app.

## <a name="get-bearer-token"></a>Ottenere bearer token

Dopo aver configurato il post e l'istanza di Azure Digital gemelli, è necessario ottenere un bearer token che le richieste del post di pubblicazione possano usare per l'autorizzazione alle API dei dispositivi gemelli digitali di Azure.

È possibile ottenere questo token in diversi modi. Questo articolo usa l' [interfaccia](/cli/azure/install-azure-cli) della riga di comando di Azure per accedere all'account Azure e ottenere un token in questo modo.

Se è installata un'interfaccia della riga di comando di Azure in [locale](/cli/azure/install-azure-cli), è possibile avviare un prompt dei comandi nel computer per eseguire i comandi seguenti.
In caso contrario, è possibile aprire una finestra di [Azure cloud Shell](https://shell.azure.com) nel browser ed eseguire i comandi in tale finestra.

1. Prima di tutto, assicurarsi di essere connessi ad Azure con le credenziali appropriate eseguendo questo comando:

    ```azurecli-interactive
    az login
    ```

2. Usare quindi il comando [AZ account Get-Access-token](/cli/azure/account#az_account_get_access_token) per ottenere un Bearer token con accesso al servizio Azure Digital Twins. In questo comando viene passato l'ID di risorsa per l'endpoint di servizio di Azure Digital Twins, per ottenere un token di accesso in grado di accedere alle risorse di Azure Digital gemelli. 

    Il contesto richiesto per il token dipende da quale set di API si sta usando, quindi usare le schede seguenti per selezionare tra le API del [piano dati](how-to-use-apis-sdks.md#overview-data-plane-apis) e del [piano di controllo](how-to-use-apis-sdks.md#overview-control-plane-apis) .

    # <a name="data-plane"></a>[Piano dati](#tab/data-plane)
    
    Per ottenere un token da usare con le API del **piano dati** , usare il valore statico seguente per il contesto del token: `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Si tratta dell'ID risorsa per l'endpoint di servizio di Azure Digital gemelli.
    
    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```
    
    # <a name="control-plane"></a>[Piano di controllo](#tab/control-plane)
    
    Per ottenere un token da usare con le API del **piano di controllo** , usare il valore seguente per il contesto del token: `https://management.azure.com/` .
    
    ```azurecli-interactive
    az account get-access-token --resource https://management.azure.com/
    ```
    ---


3. Copiare il valore di `accessToken` nel risultato e salvarlo per usarlo nella sezione successiva. Si tratta del **valore del token** che verrà fornito all'utente per autorizzare le richieste.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Screenshot della console che mostra il risultato del comando AZ account Get-Access-token. Il campo accessToken e il relativo valore di esempio sono evidenziati.":::

>[!TIP]
>Questo token è valido per almeno cinque minuti e un massimo di 60 minuti. Se si esaurisce il tempo assegnato per il token corrente, è possibile ripetere i passaggi in questa sezione per ottenerne uno nuovo.

Successivamente, si imposterà il post per usare questo token per effettuare richieste API ai dispositivi gemelli digitali di Azure.

## <a name="about-postman-collections"></a>Informazioni sulle raccolte di postazione

Le richieste in Posting vengono salvate in **raccolte** (gruppi di richieste). Quando si crea una raccolta per raggruppare le richieste, è possibile applicare le impostazioni comuni a numerose richieste contemporaneamente. Questo può semplificare notevolmente l'autorizzazione se si prevede di creare più di una richiesta per le API di dispositivi gemelli digitali di Azure, perché è necessario solo configurare questi dettagli una volta per l'intera raccolta.

Quando si lavora con i dispositivi gemelli digitali di Azure, è possibile iniziare con l'importazione di una [raccolta predefinita di tutte le richieste di dispositivi gemelli digitali di Azure](#import-collection-of-azure-digital-twins-apis). Questa operazione può essere utile se si esplorano le API e si vuole configurare rapidamente un progetto con esempi di richiesta.

In alternativa, è anche possibile scegliere di iniziare da zero, [creando una raccolta vuota](#create-your-own-collection) e inserendola in singole richieste che chiamano solo le API necessarie. 

Le sezioni seguenti descrivono entrambi questi processi. Il resto dell'articolo si verifica nell'applicazione di posting locale, quindi aprire l'applicazione postazione nel computer.

## <a name="import-collection-of-azure-digital-twins-apis"></a>Importare la raccolta di API dei dispositivi gemelli digitali di Azure

Un modo rapido per iniziare a usare i dispositivi gemelli digitali di Azure in Posting consiste nell'importare una raccolta precompilata di richieste per le API dei dispositivi gemelli digitali di Azure.

### <a name="download-the-collection-file"></a>Scaricare il file della raccolta

Il primo passaggio nell'importazione del set di API consiste nel scaricare una raccolta. Scegliere la scheda seguente per la scelta del piano dati o del piano di controllo per visualizzare le opzioni predefinite della raccolta.

# <a name="data-plane"></a>[Piano dati](#tab/data-plane)

Attualmente sono disponibili due raccolte di piani dati per i dispositivi digitali gemelli di Azure da scegliere:
* [**Raccolta di post di Azure Digital gemelli**](https://github.com/microsoft/azure-digital-twins-postman-samples): questa raccolta fornisce una semplice esperienza introduttiva per i dispositivi gemelli digitali di Azure in un post. Nelle richieste sono inclusi dati di esempio, quindi è possibile eseguirli con modifiche minime necessarie. Scegliere questa raccolta se si desidera un insieme digeribile di richieste API chiave contenenti informazioni di esempio.
    - Per trovare la raccolta, passare al collegamento del repository e aprire il file denominato *postman_collection.json*.
* **[Azure Digital gemelli data plan spavalderia](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)**: questo repository contiene il file spavalderia completo per il set di API di Azure Digital Twins, che può essere scaricato e importato in postazione come raccolta. In questo esempio viene fornito un set completo di tutte le richieste API, ma con corpi dati vuoti anziché con dati di esempio. Scegliere questa raccolta se si vuole avere accesso a ogni chiamata API e compilare tutti i dati manualmente.
    - Per trovare la raccolta, passare al collegamento del repository e scegliere la cartella per la versione specifica più recente. A questo punto, aprire il file denominato *digitaltwins.json*.

# <a name="control-plane"></a>[Piano di controllo](#tab/control-plane)

La raccolta attualmente disponibile per il piano di controllo è la [**spavalderia del piano di controllo dei gemelli digitali di Azure**](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Questo repository contiene il file spavalderia completo per il set di API dei dispositivi digitali gemelli di Azure, che può essere scaricato e importato in postazione come raccolta. Verrà fornito un set completo di tutte le richieste API.

Per trovare la raccolta, passare al collegamento del repository e scegliere la cartella per la versione specifica più recente. A questo punto, aprire il file denominato *digitaltwins.json*.

---

Ecco come scaricare la raccolta scelta nel computer in modo che sia possibile importarla in post.
1. Usare i collegamenti precedenti per aprire il file della raccolta in GitHub nel browser.
1. Selezionare il pulsante non **elaborato** per aprire il testo non elaborato del file.
    :::image type="content" source="media/how-to-use-postman/swagger-raw.png" alt-text="Screenshot del piano dati digitaltwins.jsnel file in GitHub. È presente un'evidenziazione intorno al pulsante non elaborato." lightbox="media/how-to-use-postman/swagger-raw.png":::
1. Copiare il testo dalla finestra e incollarlo in un nuovo file nel computer.
1. Salvare il file con estensione *JSON* (il nome del file può essere quello desiderato, purché sia possibile ricordarlo per trovare il file in un secondo momento).

### <a name="import-the-collection"></a>Importare la raccolta

Importare quindi la raccolta in poster.

1. Dalla finestra principale, selezionare il pulsante **Importa** .
    :::image type="content" source="media/how-to-use-postman/postman-import-collection.png" alt-text="Screenshot di una nuova finestra di post-apertura. Il pulsante ' importa ' è evidenziato." lightbox="media/how-to-use-postman/postman-import-collection.png":::

1. Nella finestra di importazione che segue selezionare **Carica file** e passare al file della raccolta nel computer creato in precedenza. Selezionare Apri.
1. Selezionare il pulsante **Importa** per confermare.

    :::image type="content" source="media/how-to-use-postman/postman-import-collection-2.png" alt-text="Screenshot della finestra ' importa ' del post, che mostra il file da importare come raccolta e il pulsante Importa.":::

La raccolta appena importata ora può essere visualizzata dalla visualizzazione principale del post, nella scheda raccolte.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-imported.png" alt-text="Screenshot della finestra principale del post. La raccolta appena importata viene evidenziata nella scheda ' Collections ' (raccolte)." lightbox="media/how-to-use-postman/postman-post-collection-imported.png":::

Continuare quindi con la sezione successiva per aggiungere una bearer token alla raccolta per l'autorizzazione e connetterla all'istanza di Azure Digital gemelli.

### <a name="configure-authorization"></a>Configura autorizzazione

Successivamente, modificare la raccolta creata per configurare alcuni dettagli di accesso. Evidenziare la raccolta creata e selezionare l'icona **Visualizza altre azioni** per eseguire il pull di un menu. Selezionare **Modifica**.

:::image type="content" source="media/how-to-use-postman/postman-edit-collection.png" alt-text="Screenshot del post. L'icona ' Visualizza altre azioni ' per la raccolta importata è evidenziata è modifica ' è evidenziata nelle opzioni." lightbox="media/how-to-use-postman/postman-edit-collection.png":::

Per aggiungere un bearer token alla raccolta per l'autorizzazione, attenersi alla seguente procedura. Questo è il punto in cui verrà usato il **valore del token** raccolto nella sezione [Get Bearer token](#get-bearer-token) per usarlo per tutte le richieste API nella raccolta.

1. Nella finestra di dialogo di modifica della raccolta verificare che sia attiva la scheda **autorizzazione** . 

    :::image type="content" source="media/how-to-use-postman/postman-authorization-imported.png" alt-text="Screenshot della finestra di dialogo di modifica della raccolta importata in post, che mostra la scheda &quot;autorizzazione&quot;." lightbox="media/how-to-use-postman/postman-authorization-imported.png":::

1. Impostare il tipo su **OAuth 2,0**, incollare il token di accesso nella casella token di accesso e selezionare **Salva**.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-imported.png" alt-text="Screenshot della finestra di dialogo di modifica del postore per la raccolta importata, nella scheda ' Authorization '. Il tipo è &quot;OAuth 2,0&quot; e la casella token di accesso è evidenziata." lightbox="media/how-to-use-postman/postman-paste-token-imported.png":::

### <a name="additional-configuration"></a>Configurazione aggiuntiva

# <a name="data-plane"></a>[Piano dati](#tab/data-plane)

Se si sta creando una raccolta di [piani dati](how-to-use-apis-sdks.md#overview-data-plane-apis) , consentire alla raccolta di connettersi facilmente alle risorse di Azure Digital Twins impostando alcune **variabili** fornite con le raccolte. Quando più richieste in una raccolta richiedono lo stesso valore (ad esempio il nome host dell'istanza di Azure Digital Twins), è possibile archiviare il valore in una variabile che si applica a ogni richiesta nella raccolta. Entrambe le raccolte scaricabili per i dispositivi gemelli digitali di Azure dispongono di variabili già create che è possibile impostare a livello di raccolta.

1. Sempre nella finestra di dialogo di modifica della raccolta, passare alla scheda **variabili** .

1. Usare il **nome host** dell'istanza dalla sezione [*prerequisiti*](#prerequisites) per impostare il campo del valore corrente della variabile pertinente. Selezionare **Salva**.

    :::image type="content" source="media/how-to-use-postman/postman-variables-imported.png" alt-text="Screenshot della finestra di dialogo di modifica della raccolta importata in post, che mostra la scheda &quot;variabili&quot;. Il campo ' valore corrente ' è evidenziato." lightbox="media/how-to-use-postman/postman-variables-imported.png":::

1. Se la raccolta contiene variabili aggiuntive, compilare e salvare anche questi valori.

Al termine della procedura descritta in precedenza, è stata completata la configurazione della raccolta. Se lo si desidera, è possibile chiudere la scheda modifica per la raccolta.

# <a name="control-plane"></a>[Piano di controllo](#tab/control-plane)

Se si sta creando una raccolta di [piani di controllo](how-to-use-apis-sdks.md#overview-control-plane-apis) , sono state eseguite tutte le operazioni necessarie per configurare la raccolta. Se lo si desidera, è possibile chiudere la scheda modifica per la raccolta, quindi passare alla sezione successiva.

--- 

### <a name="explore-requests"></a>Esplora richieste

Esplorare quindi le richieste all'interno della raccolta di API dei dispositivi digitali gemelli di Azure. È possibile espandere la raccolta per visualizzare le richieste create in precedenza (ordinate per categoria di operazione). 

Richieste diverse richiedono informazioni diverse sull'istanza e i relativi dati. Per visualizzare tutte le informazioni necessarie per creare una particolare richiesta, cercare i dettagli della richiesta nella [documentazione di riferimento dell'API REST di Digital gemelli di Azure](/rest/api/azure-digitaltwins/).

È possibile modificare i dettagli di una richiesta nella raccolta dei post con i passaggi seguenti:

1. Selezionarlo dall'elenco per estrarne i dettagli modificabili. 

1. Immettere i valori per le variabili elencate nella scheda **params** in **variabili di percorso**.

    :::image type="content" source="media/how-to-use-postman/postman-request-details-imported.png" alt-text="Screenshot del post. La raccolta viene espansa per visualizzare una richiesta. La sezione ' variabili di percorso ' è evidenziata nei dettagli della richiesta." lightbox="media/how-to-use-postman/postman-request-details-imported.png":::

1. Fornire le **intestazioni** o i dettagli del **corpo** necessari nelle rispettive schede.

Una volta forniti tutti i dettagli necessari, è possibile eseguire la richiesta con il pulsante **Invia** .

È anche possibile aggiungere richieste personalizzate alla raccolta, usando il processo descritto nella sezione [*aggiungere una singola richiesta*](#add-an-individual-request) di seguito.

## <a name="create-your-own-collection"></a>Crea una raccolta personalizzata

Anziché importare la raccolta esistente di tutte le API dei dispositivi gemelli digitali di Azure, è anche possibile creare una raccolta personalizzata da zero. È quindi possibile popolarlo con singole richieste usando la [documentazione di riferimento dell'API REST di Digital gemelli di Azure](/rest/api/azure-digitaltwins/).

### <a name="create-a-postman-collection"></a>Creare una raccolta di Postman

1. Per creare una raccolta, selezionare il pulsante **nuovo** nella finestra principale del post.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Screenshot della finestra principale del post. Il pulsante ' nuovo ' è evidenziato." lightbox="media/how-to-use-postman/postman-new.png":::

    Scegliere un tipo di **raccolta**.

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-2.png" alt-text="Screenshot della finestra di dialogo &quot;Crea nuovo&quot; in post. L'opzione ' Collection ' è evidenziata.":::

1. Verrà aperta una scheda per riempire i dettagli della nuova raccolta. Selezionare l'icona di modifica accanto al nome predefinito della raccolta (**nuova raccolta**) per sostituirla con la propria scelta di nome. 

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-3.png" alt-text="Screenshot della finestra di dialogo di modifica della nuova raccolta in post. Viene evidenziata l'icona di modifica accanto al nome ' nuova raccolta '." lightbox="media/how-to-use-postman/postman-new-collection-3.png":::

Continuare quindi con la sezione successiva per aggiungere un bearer token alla raccolta per l'autorizzazione.

### <a name="configure-authorization"></a>Configura autorizzazione

Per aggiungere un bearer token alla raccolta per l'autorizzazione, attenersi alla seguente procedura. Questo è il punto in cui verrà usato il **valore del token** raccolto nella sezione [Get Bearer token](#get-bearer-token) per usarlo per tutte le richieste API nella raccolta.

1. Sempre nella finestra di dialogo di modifica per la nuova raccolta, passare alla scheda **autorizzazione** .

    :::image type="content" source="media/how-to-use-postman/postman-authorization-custom.png" alt-text="Screenshot della finestra di dialogo di modifica della nuova raccolta in post, che mostra la scheda &quot;autorizzazione&quot;." lightbox="media/how-to-use-postman/postman-authorization-custom.png":::

1. Impostare il tipo su **OAuth 2,0**, incollare il token di accesso nella casella token di accesso e selezionare **Salva**.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-custom.png" alt-text="Screenshot della finestra di dialogo di modifica dei post per la nuova raccolta, nella scheda &quot;autorizzazione&quot;. Il tipo è &quot;OAuth 2,0&quot; e la casella token di accesso è evidenziata." lightbox="media/how-to-use-postman/postman-paste-token-custom.png":::

Al termine della procedura descritta in precedenza, è stata completata la configurazione della raccolta. Se lo si desidera, è possibile chiudere la scheda modifica per la nuova raccolta.

La nuova raccolta può essere visualizzata dalla visualizzazione principale del post, nella scheda raccolte.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-custom.png" alt-text="Screenshot della finestra principale del post. La raccolta personalizzata appena creata viene evidenziata nella scheda ' Collections ' (raccolte)." lightbox="media/how-to-use-postman/postman-post-collection-custom.png":::

## <a name="add-an-individual-request"></a>Aggiungere una singola richiesta

Ora che la raccolta è configurata, è possibile aggiungere richieste personalizzate alle API dei dispositivi gemelli digitali di Azure.

1. Per creare una richiesta, usare di nuovo il pulsante **nuovo** .

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Screenshot della finestra principale del post. Il pulsante ' nuovo ' è evidenziato." lightbox="media/how-to-use-postman/postman-new.png":::

    Scegliere un tipo di **richiesta**.

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Screenshot della finestra di dialogo &quot;Crea nuovo&quot; in post. L'opzione ' request ' è evidenziata.":::

1. Questa azione apre la finestra Salva richiesta, in cui è possibile immettere un nome per la richiesta, assegnarle una descrizione facoltativa e scegliere la raccolta di cui fa parte. Inserire i dettagli e salvare la richiesta nella raccolta creata in precedenza.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="Screenshot della finestra ' Salva richiesta ' nel post che mostra i campi descritti. Il pulsante ' Salva in Azure Digital Twins Collection ' è evidenziato.":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

È ora possibile visualizzare la richiesta nella raccolta e selezionarla per estrarre i dettagli modificabili.

:::image type="content" source="media/how-to-use-postman/postman-request-details-custom.png" alt-text="Screenshot del post. La raccolta di dispositivi gemelli digitali di Azure viene espansa per visualizzare i dettagli della richiesta." lightbox="media/how-to-use-postman/postman-request-details-custom.png":::

### <a name="set-request-details"></a>Imposta dettagli richiesta

Per creare una richiesta post a una delle API dei dispositivi gemelli digitali di Azure, è necessario l'URL dell'API e informazioni sui dettagli necessari. Queste informazioni sono disponibili nella documentazione di [riferimento dell'API REST di Digital gemelli di Azure](/rest/api/azure-digitaltwins/).

Per procedere con una query di esempio, in questo articolo verrà usata l'API di query (e la relativa [documentazione di riferimento](/rest/api/digital-twins/dataplane/query/querytwins)) per eseguire una query per tutti i dispositivi gemelli digitali in un'istanza.

1. Ottenere l'URL della richiesta e il tipo dalla documentazione di riferimento. Per l'API di query, questo è *attualmente `https://digitaltwins-hostname/query?api-version=2020-10-31` post*.
1. In postazione impostare il tipo per la richiesta e immettere l'URL della richiesta, inserendo i segnaposto nell'URL in base alle esigenze. Questa è la posizione in cui verrà usato il **nome host** dell'istanza dalla sezione [*prerequisiti*](#prerequisites) .
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Screenshot dei dettagli della nuova richiesta in post. L'URL della query dalla documentazione di riferimento è stato compilato nella casella URL richiesta." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Verificare che i parametri visualizzati per la richiesta nella scheda **params** corrispondano a quelli descritti nella documentazione di riferimento. Per questa richiesta in postazione, il `api-version` parametro è stato compilato automaticamente quando l'URL della richiesta è stato immesso nel passaggio precedente. Per l'API di query, questo è l'unico parametro obbligatorio, quindi questo passaggio viene eseguito.
1. Nella scheda **autorizzazione** impostare il tipo su **eredita autenticazione da padre**. Ciò indica che questa richiesta utilizzerà l'autorizzazione configurata in precedenza per l'intera raccolta.
1. Verificare che le intestazioni visualizzate per la richiesta nella scheda **intestazioni** corrispondano a quelle descritte nella documentazione di riferimento. Per questa richiesta sono state compilate automaticamente diverse intestazioni. Per l'API di query, nessuna delle opzioni di intestazione è obbligatoria, quindi questo passaggio viene eseguito.
1. Verificare che il corpo visualizzato per la richiesta nella scheda **corpo** corrisponda alle esigenze descritte nella documentazione di riferimento. Per l'API di query, è necessario un corpo JSON per fornire il testo della query. Di seguito è riportato un esempio di corpo per questa richiesta che esegue query per tutti i dispositivi gemelli digitali nell'istanza:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="Screenshot dei dettagli della nuova richiesta nella scheda Body (corpo). Contiene un corpo JSON non elaborato con una query di ' SELECT * FROM DIGITALTWINS '." lightbox="media/how-to-use-postman/postman-request-body.png":::

   Per altre informazioni sulla creazione di query dei dispositivi gemelli digitali di Azure, vedere [*procedura: eseguire una query sul grafico gemello*](how-to-query-graph.md).

1. Controllare la documentazione di riferimento per tutti gli altri campi che potrebbero essere necessari per il tipo di richiesta. Per l'API di query, tutti i requisiti sono ora soddisfatti nella richiesta di invio, quindi questo passaggio viene eseguito.
1. Usare il pulsante **Send (Invia** ) per inviare la richiesta completata.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Screenshot del post che mostra i dettagli della nuova richiesta. Il pulsante Invia è evidenziato." lightbox="media/how-to-use-postman/postman-request-send.png":::

Dopo l'invio della richiesta, i dettagli della risposta verranno visualizzati nella finestra di posting sotto la richiesta. È possibile visualizzare il codice di stato della risposta e qualsiasi testo del corpo.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Screenshot della richiesta inviata in post. Sotto i dettagli della richiesta, viene visualizzata la risposta. Status è 200 OK e body Visualizza i risultati della query." lightbox="media/how-to-use-postman/postman-request-response.png":::

È anche possibile confrontare la risposta ai dati di risposta previsti specificati nella documentazione di riferimento per verificare il risultato o ottenere ulteriori informazioni sugli errori che si verificano.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle API dei dispositivi gemelli digitali, vedere [*procedura: usare le API e gli SDK di dispositivi digitali gemelli di Azure*](how-to-use-apis-sdks.md)o visualizzare la [documentazione di riferimento per le API REST](/rest/api/azure-digitaltwins/).