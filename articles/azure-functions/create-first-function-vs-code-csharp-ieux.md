---
title: Creare una funzione in C# con Visual Studio Code - Funzioni di Azure
description: Informazioni su come creare una funzione in C# e quindi pubblicare il progetto locale nell'hosting serverless in Funzioni di Azure usando l'estensione Funzioni di Azure in Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
ms.custom: devx-track-csharp
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2848ce6214d59ba2732dcfc148ccaf9936497f17
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121032"
---
# <a name="quickstart-create-a-c-function-in-azure-using-visual-studio-code"></a>Avvio rapido: Creare una funzione in C# in Azure con Visual Studio Code

> [!div class="op_single_selector" title1="Selezionare il linguaggio della funzione: "]
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [Python](create-first-function-vs-code-python.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Altro (Go/Rust)](create-first-function-vs-code-other.md)

In questo articolo viene usato Visual Studio Code per creare una funzione basata sulla libreria di classi C# che risponde alle richieste HTTP. Dopo aver testato il codice localmente, è necessario distribuirlo nel <abbr title="Ambiente di elaborazione del runtime in cui tutti i dettagli del server sono trasparenti per gli sviluppatori di applicazioni, semplificando il processo di distribuzione e gestione del codice.">senza server</abbr> ambiente di <abbr title="Servizio di Azure che offre un ambiente di elaborazione senza server a basso costo per le applicazioni.">Funzioni di Azure</abbr>.

Le procedure illustrate in questa guida di avvio rapido comportano l'addebito di qualche centesimo (USD) o meno nell'account Azure.

È inoltre disponibile una [versione di questo articolo basata sull'interfaccia della riga di comando](create-first-function-cli-csharp.md).
    
## <a name="1-configure-your-environment"></a>1. configurare l'ambiente

Prima di iniziare, verificare che siano soddisfatti i requisiti seguenti:

+ Azure <abbr title="Profilo che mantiene le informazioni di fatturazione per l'utilizzo di Azure.">account</abbr> con un oggetto attivo <abbr title="Struttura organizzativa di base in cui si gestiscono le risorse in Azure, in genere associate a un singolo utente o a un reparto all'interno di un'organizzazione.">sottoscrizione</abbr>. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) versione 3.x.

+ [Visual Studio Code](https://code.visualstudio.com/) in una delle [piattaforme supportate](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [Estensione C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) per Visual Studio Code.  

+ [Estensione Funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) per Visual Studio Code.

## <a name="2-create-your-local-project"></a><a name="create-an-azure-functions-project"></a>2. creare il progetto locale

In questa sezione si usa Visual Studio Code per creare un locale <abbr title="Un contenitore logico per una o più funzioni singole che possono essere distribuite e gestite insieme.">Progetto di funzioni di Azure</abbr> in C#. Più avanti in questo articolo verrà pubblicato il codice della funzione in Azure.

1. Scegliere l'icona di Azure nella <abbr title="Il gruppo verticale di icone sul lato sinistro della finestra di Visual Studio Code.">Barra attività</abbr>, quindi nell'area **Azure: Functions** selezionare l'icona **Crea nuovo progetto...** .

    ![Scegliere Crea nuovo progetto](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Scegliere una posizione della directory per l'area di lavoro del progetto e quindi scegliere **Seleziona**.

    > [!NOTE]
    > Questa procedura è stata progettata per il completamento all'esterno di un'area di lavoro. In questo caso, non selezionare una cartella di progetto inclusa in un'area di lavoro.

1. Quando richiesto, immettere le informazioni seguenti:

    + **Selezionare un linguaggio per il progetto di funzione**: Scegliere `C#`.

    + **Selezionare un modello per la prima funzione del progetto**: Scegliere `HTTP trigger`.

    + **Specificare un nome di funzione**: Digitare `HttpExample`.

    + **Specificare uno spazio dei nomi**: Digitare `My.Functions`.

    + **Livello di autorizzazione**: Scegliere `Anonymous`, che consente a chiunque di chiamare l'endpoint della funzione. Per informazioni sui livelli di autorizzazione, vedere [Chiavi di autorizzazione](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Selezionare come si vuole aprire il progetto**: Scegliere `Add to workspace`.

1. Usando queste informazioni, Visual Studio Code genera un progetto di funzioni di Azure con un HTTP <abbr title="Tipo di evento che richiama il codice della funzione, ad esempio una richiesta HTTP, un messaggio della coda o un orario specifico.">trigger</abbr>. È possibile visualizzare i file di progetto locali in Explorer. Per altre informazioni sui file che vengono creati, vedere [File di progetto generati](functions-develop-vs-code.md#generated-project-files).

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Dopo aver verificato la corretta esecuzione della funzione nel computer locale, è possibile usare Visual Studio Code per pubblicare il progetto direttamente in Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="5-publish-the-project-to-azure"></a>5. pubblicare il progetto in Azure

In questa sezione verrà creata un'app per le funzioni con le risorse correlate nella sottoscrizione di Azure e quindi verrà distribuito il codice. 

> [!IMPORTANT]
> La pubblicazione in un'app per le funzioni esistente sovrascrive il contenuto di tale app in Azure. 

1. Selezionare l'icona di Azure nella barra attività, quindi nell'area **Azure: Funzioni** scegliere il pulsante **Deploy to function app...** (Distribuisci nell'app per le funzioni...).

    

1. Quando richiesto, immettere le informazioni seguenti:

    + **Selezionare la cartella**: scegliere una cartella dall'area di lavoro o selezionarne una che contenga l'app per le funzioni. Questa opzione non verrà visualizzata se è già stata aperta un'app per le funzioni valida.

    + **Selezionare la sottoscrizione**: scegliere la sottoscrizione da usare. Questa opzione non è visibile se è disponibile una sola sottoscrizione.

    + **Selezionare l'app per le funzioni in Azure**: Scegliere `+ Create new Function App`. Non scegliere l'opzione `Advanced`, che non è illustrata in questo articolo.

    + **Immettere un nome univoco a livello globale per l'app per le funzioni**: digitare un nome valido in un percorso URL. Il nome digitato viene convalidato per assicurarsi che sia <abbr title="Il nome deve essere univoco in tutti i progetti di funzioni usati da tutti i clienti di Azure a livello globale. In genere, si usa una combinazione del nome personale o della società, il nome dell'applicazione e un identificatore numerico, come in Contoso-bizapp-Func-20">univoco in funzioni di Azure</abbr>. 

    + **Selezionare una località per le nuove risorse**:  per prestazioni ottimali, scegliere un'[area](https://azure.microsoft.com/regions/) vicina.

    L'estensione Mostra lo stato delle singole risorse man mano che vengono create in Azure nell'area di notifica.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Notifica della creazione di risorse di Azure":::

1. Al termine, nella sottoscrizione vengono create le risorse di Azure seguenti con i nomi basati sul nome dell'app per le funzioni:

    + Un **gruppo di risorse**, che è un contenitore logico per le risorse correlate.
    + Un **account di archiviazione di Azure** standard, che mantiene lo stato e altre informazioni sui progetti.
    + **Piano a consumo**, che definisce l'host sottostante per l'app per le funzioni senza server. 
    + App per le **funzioni**, che fornisce l'ambiente per l'esecuzione del codice della funzione. Un'app per le funzioni consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione e la condivisione delle risorse all'interno dello stesso piano di hosting.
    + **Istanza di Application Insights** connessa all'app per le funzioni, che tiene traccia dell'utilizzo della funzione senza server.

    Dopo la creazione dell'app per le funzioni e dopo l'applicazione del pacchetto di distribuzione viene visualizzata una notifica. 

    > [!TIP]
    > Per impostazione predefinita, le risorse di Azure richieste dall'app per le funzioni vengono create in base al nome dell'app per le funzioni fornito. Per impostazione predefinita, vengono creati anche nello stesso nuovo gruppo di risorse con l'app per le funzioni. Se si desidera personalizzare i nomi di queste risorse o riutilizzare le risorse esistenti, è necessario [pubblicare il progetto con le opzioni di creazione avanzate](functions-develop-vs-code.md#enable-publishing-with-advanced-create-options).


1. Selezionare **Visualizza output** nelle notifiche per visualizzare i risultati della creazione e della distribuzione, incluse le risorse di Azure create. Se non si riesce a visualizzare la notifica, selezionare l'icona della campana nell'angolo in basso a destra per visualizzarla di nuovo.

    ![Notifica di creazione completata](./media/functions-create-first-function-vs-code/function-create-notifications.png)

## <a name="6-run-the-function-in-azure"></a>6. eseguire la funzione in Azure

1. Tornare all'area **Azure: Functions** sulla barra laterale, espandere la sottoscrizione, la nuova app per le funzioni e le **funzioni**. Fare clic con il pulsante destro del mouse su (Windows) o fare clic con il <kbd>pulsante destro del</kbd> mouse (MacOS) sulla `HttpExample` funzione e scegliere **Esegui ora funzione...**.

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Esegui la funzione ora in Azure da Visual Studio Code":::

1. In **immettere il corpo della richiesta** viene visualizzato il valore del corpo del messaggio di richiesta `{ "name": "Azure" }` .

    Premere INVIO per inviare il messaggio di richiesta alla funzione.  

1. Quando la funzione viene eseguita in Azure e restituisce una risposta, viene generata una notifica in Visual Studio Code.

## <a name="5-clean-up-resources"></a>5. pulire le risorse

Quando si continua con il [passaggio successivo](#next-steps) e si aggiunge un <abbr title="Un metodo per associare una funzione a una coda di archiviazione, in modo che possa creare messaggi nella coda."> Associazione di output della coda di Archiviazione di Azure</abbr> per la funzione, è necessario che tutte le risorse siano configurate per la compilazione in base a quanto già fatto.

In caso contrario, seguire questa procedura per eliminare l'app per le funzioni e le risorse correlate per evitare l'addebito di costi aggiuntivi.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

Per altre informazioni sui costi di Funzioni di Azure, vedere [Stima dei costi del piano a consumo](functions-consumption-costs.md).

## <a name="next-steps"></a>Passaggi successivi

È stato usato Visual Studio Code per creare un'app per le funzioni con una semplice funzione attivata tramite HTTP. Nell'articolo successivo si espande la funzione aggiungendo un output <abbr title="Connessione dichiarativa tra una funzione e altre risorse. Un'associazione di input fornisce dati alla funzione. un'associazione di output fornisce dati dalla funzione ad altre risorse.">binding</abbr>. Questo binding scrive la stringa della richiesta HTTP in un messaggio in una coda dell'archiviazione code di Azure. 

> [!div class="nextstepaction"]
> [Connettersi a una coda di Archiviazione di Azure](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
