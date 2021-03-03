---
title: 'Avvio rapido: Creare la prima funzione in Azure con Visual Studio'
description: Questa guida di avvio rapido illustra come creare e pubblicare una funzione di Azure attivata tramite HTTP usando Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 09/30/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: fb3ba4ad49cc78b6082d347521cd87be5409cd34
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748781"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Avvio rapido: Creare la prima funzione in Azure con Visual Studio

In questo articolo viene usato Visual Studio per creare una funzione basata sulla libreria di classi C# che risponde alle richieste HTTP. Dopo aver testato il codice localmente, è necessario distribuirlo nel <abbr title="Ambiente di elaborazione del runtime in cui tutti i dettagli del server sono trasparenti per gli sviluppatori di applicazioni, semplificando il processo di distribuzione e gestione del codice.">senza server</abbr> ambiente di <abbr title="Un servizio di Azure che fornisce un ambiente di elaborazione senza server a basso costo per le applicazioni.">Funzioni di Azure</abbr>.

Il completamento di questa Guida introduttiva comporta un costo ridotto di pochi centesimi USD o meno nella <abbr title="Profilo che mantiene le informazioni di fatturazione per l'utilizzo di Azure.">Account Azure</abbr>.

## <a name="1-prepare-your-environment"></a>1. Preparare l'ambiente

+ Creare un'Azure <abbr title="Profilo che mantiene le informazioni di fatturazione per l'utilizzo di Azure.">account</abbr> con un oggetto attivo <abbr title="Struttura organizzativa di base in cui si gestiscono le risorse in Azure, in genere associate a un singolo utente o a un reparto all'interno di un'organizzazione.">sottoscrizione</abbr>. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ Installare [Visual Studio 2019](https://azure.microsoft.com/downloads/) e selezionare il carico di lavoro **sviluppo di Azure** durante l'installazione. 

![Installare Visual Studio con il carico di lavoro Sviluppo di Azure](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

<details>
<summary>Usare invece un progetto di funzioni di Azure</summary>
Se si desidera creare un <abbr title="Un contenitore logico per una o più funzioni singole che possono essere distribuite e gestite insieme. "> Progetto di funzioni di Azure</abbr> con Visual Studio 2017 è invece necessario installare gli [strumenti di funzioni di Azure più recenti](functions-develop-vs.md#check-your-tools-version).
</details>

## <a name="2-create-a-function-app-project"></a>2. creare un progetto di app per le funzioni

1. Nel menu di Visual Studio selezionare **File** > **Nuovo** > **Progetto**.

1. In **Crea un nuovo progetto** immettere *funzioni* nella casella di ricerca, scegliere il modello **Funzioni di Azure** e quindi selezionare **Avanti**.

1. In **Configura il nuovo progetto**, immettere **<abbr title=" il nome dell'app per le funzioni deve essere valido come spazio dei nomi C#, quindi non usare caratteri di sottolineatura, trattini o altri caratteri non alfanumerici. "> Nome </abbr>** del progetto e quindi selezionare **Crea**. 

1. Fornire le informazioni seguenti per le impostazioni di **creazione di una nuova applicazione funzioni di Azure** :

    + Selezionare **<abbr title=" questo valore per creare un progetto di funzione che usa il runtime della versione 3. x di funzioni di Azure, che supporta .NET Core 3. x. funzioni di Azure 1. x supporta il .NET Framework. Per altre informazioni, vedere [Panoramica delle versioni del runtime di funzioni di Azure](functions-versions.md). "> Funzioni di Azure V3 (.NET Core </abbr> )** dall'elenco a discesa Functions Runtime
    
    + Selezionare **<abbr title=" questo valore per creare una funzione attivata da una richiesta HTTP. "> Trigger </abbr> http** come modello di funzione.
    
    + Selezionare **<abbr = "perché una funzione di Azure richiede un account di archiviazione. ne viene assegnato o creato uno quando si pubblica il progetto in Azure. Un trigger HTTP non usa una stringa di connessione dell'account di archiviazione di Azure. tutti gli altri tipi di trigger richiedono una stringa di connessione dell'account di archiviazione di Azure valida ". >emulatore </abbr> di archiviazione** dall'elenco a discesa account di archiviazione.
        
    + Selezionare **Anonimo** dal <abbr title="Viene creata una funzione che può essere attivata da qualsiasi client senza dover fornire una chiave. Questa impostazione di autorizzazione consente di testare più facilmente la nuova funzione. Per altre informazioni sulle chiavi e l'autorizzazione, vedere [Chiavi di autorizzazione](functions-bindings-http-webhook-trigger.md#authorization-keys) in [Associazioni di webhook e HTTP](functions-bindings-http-webhook.md).">Livello di autorizzazione</abbr> elenco a discesa

    + Selezionare **Crea**
        
## <a name="3-rename-the-function"></a>3. rinominare la funzione

L'attributo del metodo `FunctionName` imposta il nome della funzione, che per impostazione predefinita viene generato come `Function1`. Poiché gli strumenti non consentono di eseguire l'override del nome predefinito della funzione quando si crea il progetto, è necessario creare un nome migliore per la classe, il file e i metadati della funzione.

1. In **Esplora file** fare clic con il pulsante destro del mouse sul file function1.cs e rinominarlo *HttpExample.cs*.

1. Nel codice rinominare la classe Function1 in 'HttpExample'.

1. Nel metodo `HttpTrigger` denominato `Run` rinominare l'attributo del metodo `FunctionName` in `HttpExample`.


## <a name="4-run-the-function-locally"></a>4. eseguire la funzione localmente

1. Per eseguire la funzione, premere <kbd>F5</kbd> in Visual Studio.

1. Copiare l'URL della funzione dall'output di runtime di Funzioni di Azure.

    ![Runtime locale di Azure](../../includes/media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

1. Incollare l'URL per la richiesta HTTP nella barra degli indirizzi del browser. Accodare la stringa di query **? Name =<YOUR_NAME>** a questo URL ed eseguire la richiesta. 

    ![Risposta localhost della funzione nel browser](../../includes/media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

1. Per arrestare il debug, premere <kbd>MAIUSC</kbd> + <kbd>F5</kbd> in Visual Studio.

<details>
<summary>Risoluzione dei problemi</summary>
 Potrebbe essere necessario abilitare un'eccezione del firewall in modo che gli strumenti possano gestire le richieste HTTP. I livelli di autorizzazione non vengono mai applicati quando si esegue una funzione localmente.
</details>

## <a name="5-publish-the-project-to-azure"></a>5. pubblicare il progetto in Azure

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**.

1. In **destinazione** selezionare **Azure**

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-1.png" alt-text="Selezionare la destinazione di Azure":::

1. In **Destinazione specifica** selezionare **App per le funzioni di Azure (Windows)**

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-2.png" alt-text="Selezionare App per le funzioni":::

1. In **istanza della funzione** selezionare **Crea una nuova funzione di Azure** e quindi usare i valori specificati nei seguenti elementi:

    + Per **nome** specificare un <abbr title="Usare un nome che identifichi in modo univoco la nuova app per le funzioni. Accettare questo nome o immetterne uno nuovo. I caratteri validi sono: `a-z` , `0-9` e `-` .">Nome globalmente univoco</abbr>
    
    + **Selezionare** una sottoscrizione dall'elenco a discesa.
    
    + **Selezionare** una esistente <abbr title="Un contenitore logico per le risorse di Azure correlate che è possibile gestire come unità.">gruppo di risorse</abbr> dall'elenco a discesa o scegliere **nuovo** per creare un nuovo gruppo di risorse.
    
    + **Select** <abbr title="Quando si pubblica il progetto in un'app per le funzioni eseguita in un [piano a consumo](consumption-plan.md), vengono addebitati solo i costi relativi alle esecuzioni dell'app per le funzioni. Altri piani di hosting comportano costi più elevati.">Consumo</abbr> nell'elenco a discesa tipo di riproduzione
    
    + **Selezionare** un  <abbr title="Riferimento geografico a un Data Center di Azure specifico in cui vengono allocate le risorse. Per un elenco delle aree disponibili, vedere [aree](https://azure.microsoft.com/regions/) .">posizione</abbr> dall'elenco a discesa.
    
    + **Selezionare** un <abbr = "un account di archiviazione di Azure è richiesto dal runtime di funzioni. Selezionare Nuovo per configurare un account di archiviazione per utilizzo generico. È anche possibile scegliere un account esistente che soddisfi i requisiti dell'account di archiviazione. " >archiviazione di Azure</abbr> account dall'elenco a discesa

    ![Finestra di dialogo Crea servizio app](../../includes/media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Selezionare **Crea** 

1. In **Istanza di Funzioni** assicurarsi che sia selezionata l'opzione **Esegui da file di pacchetto**. 

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="Completare la creazione del profilo":::

    <details>
    <summary>Che cosa fa questa impostazione?</summary>
    Quando si usa l' **esecuzione dal file di pacchetto**, l'app per le funzioni viene distribuita usando la [distribuzione zip](functions-deployment-technologies.md#zip-deploy) con la modalità [di esecuzione del pacchetto](run-functions-from-deployment-package.md) abilitata. Questo è il metodo di distribuzione consigliato per il progetto di funzioni, perché offre prestazioni più elevate.    
    </details>   

1. Selezionare **Fine**.

1. Nella pagina Pubblica selezionare **Pubblica**.

1. Nella pagina pubblica esaminare l'URL radice dell'app per le funzioni.

1. Nella scheda Pubblica scegliere **Gestisci in <abbr title=" Cloud Explorer consente di usare Visual Studio per visualizzare il contenuto del sito, avviare e arrestare l'app per le funzioni e passare direttamente alle risorse dell'app per le funzioni in Azure e nella portale di Azure. "> Cloud Explorer</>**.
    
    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Messaggio di esito positivo della pubblicazione":::
    

## <a name="6-test-your-function-in-azure"></a>6. testare la funzione in Azure

1. In Cloud Explorer è necessario selezionare la nuova app per le funzioni. In caso contrario, espandere la sottoscrizione > **Servizi app** e selezionare la nuova app per le funzioni.

1. Fare clic con il pulsante destro del mouse sull'app per le funzioni e scegliere **Apri nel browser**. Verrà visualizzata la radice dell'app per le funzioni nel Web browser predefinito e verrà visualizzata la pagina che indica che l'app per le funzioni è in esecuzione. 

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="App per le funzioni in esecuzione":::

1. Nella barra degli indirizzi del browser aggiungere la stringa **/API/HttpExample? name = functions** all'URL di base ed eseguire la richiesta.

    Il formato dell'URL che chiama la funzione attivata tramite HTTP è il seguente:

    `http://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`

2. Passare a questo URL per vedere la risposta nel browser alla risposta GET remota restituita dalla funzione, simile all'esempio seguente:

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png" alt-text="Risposta della funzione nel browser":::

## <a name="7-clean-up-resources"></a>7. Pulire le risorse

Eliminare l'app per le funzioni e le relative risorse per evitare di incorrere in costi aggiuntivi.

1. In Cloud Explorer espandere la sottoscrizione > **Servizi app**, fare clic con il pulsante destro del mouse sull'app per le funzioni e scegliere **Apri nel portale**. 

1. Nella pagina dell'app per le funzioni selezionare la scheda **Panoramica** e quindi il collegamento sotto **Gruppo di risorse**.

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Selezionare il gruppo di risorse da eliminare nella pagina dell'app per le funzioni.":::

1. Nella pagina **Gruppo di risorse** esaminare l'elenco delle risorse incluse e verificare che siano quelle da eliminare.
 
1. Selezionare **Elimina gruppo di risorse** e seguire le istruzioni.

    L'eliminazione potrebbe richiedere alcuni minuti. Al termine, viene visualizzata una notifica per pochi secondi. È anche possibile selezionare l'icona a forma di campana nella parte superiore della pagina per visualizzare la notifica.

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per informazioni su come aggiungere un <abbr title="Un metodo per associare una funzione a una coda di archiviazione, in modo che possa creare messaggi nella coda. Le associazioni sono connessioni dichiarative tra una funzione e altre risorse. Un'associazione di input fornisce dati alla funzione. un'associazione di output fornisce dati dalla funzione ad altre risorse."> Associazione di output della coda di Archiviazione di Azure</abbr> per la funzione:

> [!div class="nextstepaction"]
> [Aggiungere un binding della coda di archiviazione di Azure alla funzione](functions-add-output-binding-storage-queue-vs.md)
