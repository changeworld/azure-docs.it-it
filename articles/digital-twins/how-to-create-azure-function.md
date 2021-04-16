---
title: Configurare una funzione in Azure per elaborare i dati
titleSuffix: Azure Digital Twins
description: Informazioni su come creare una funzione in Azure che può accedere e essere attivata da gemelli digitali.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5dddaabf47a261f741b3b1cb8d3319d589c4e474
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480774"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Connettere le app per le funzioni in Azure per l'elaborazione dei dati

I gemelli digitali possono essere aggiornati in base ai dati usando route [di eventi](concepts-route-events.md) tramite risorse di calcolo. Ad esempio, una funzione che viene effettuata usando [Funzioni di Azure](../azure-functions/functions-overview.md) aggiornare un gemello digitale in risposta a:
* Dati di telemetria del dispositivo hub IoT di Azure.
* Modifica di una proprietà o di altri dati da un altro gemello digitale all'interno del grafo del gemello.

Questo articolo illustra come creare una funzione in Azure da usare con Gemelli digitali di Azure. Per creare una funzione, seguire questa procedura di base:

1. Creare un progetto Funzioni di Azure in Visual Studio.
2. Scrivere una funzione con un trigger [Griglia di eventi di Azure](../event-grid/overview.md) trigger.
3. Aggiungere il codice di autenticazione alla funzione per poter accedere Gemelli digitali di Azure.
4. Pubblicare l'app per le funzioni in Azure.
5. Configurare la sicurezza [per](concepts-security.md) l'app per le funzioni.

## <a name="prerequisite-set-up-azure-digital-twins"></a>Prerequisito: configurare Gemelli digitali di Azure

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Creare un'app per le funzioni in Visual Studio

In Visual Studio 2019 seleziona **File** > **Nuovo** > **Progetto**. Cercare il **modello Funzioni di Azure** personalizzato. Selezionare **Avanti**.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Screenshot della Visual Studio che mostra la finestra di dialogo del nuovo progetto. Il Funzioni di Azure di progetto è evidenziato.":::

Specificare un nome per l'app per le funzioni e quindi selezionare __Crea.__

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Screenshot della Visual Studio che mostra la finestra di dialogo per configurare un nuovo progetto. Le impostazioni includono il nome del progetto, il percorso di salvataggio, la scelta di creare una nuova soluzione e il nome della soluzione.":::

Selezionare il tipo di app per le funzioni **Trigger griglia di** eventi e quindi selezionare __Crea__.

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="Screenshot della Visual Studio che mostra la finestra di dialogo per creare una nuova Funzioni di Azure applicazione. L'opzione trigger Griglia di eventi è evidenziata.":::

Dopo aver creato l'app per le funzioni, Visual Studio un esempio di codice in un file *Function1.cs* nella cartella del progetto. Questa funzione breve viene usata per registrare gli eventi.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Screenshot della Visual Studio. Viene visualizzata la finestra del progetto per il nuovo progetto. Il codice per una funzione di esempio viene visualizzato in un file denominato Function1." lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-that-has-an-event-grid-trigger"></a>Scrivere una funzione con un trigger di Griglia di eventi

È possibile scrivere una funzione aggiungendo un SDK all'app per le funzioni. L'app per le funzioni interagisce con Gemelli digitali di Azure usando [Gemelli digitali di Azure SDK per .NET (C#).](/dotnet/api/overview/azure/digitaltwins/client) 

Per usare l'SDK, è necessario includere i pacchetti seguenti nel progetto. Installare i pacchetti usando la gestione Visual Studio pacchetti NuGet. In caso di aggiunta dei pacchetti tramite `dotnet` in uno strumento da riga di comando.

* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System.Net.Http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure.Core](https://www.nuget.org/packages/Azure.Core/)

Successivamente, in Visual Studio Esplora soluzioni aprire il file _Function1.cs_ che include il codice di esempio. Aggiungere le `using` istruzioni seguenti per i pacchetti.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Aggiungere il codice di autenticazione alla funzione

Dichiarare ora le variabili a livello di classe e aggiungere il codice di autenticazione che consentirà alla funzione di accedere Gemelli digitali di Azure. Aggiungere le variabili e il codice alla funzione nel file _Function1.cs._

* **Codice per leggere l'URL Gemelli digitali di Azure servizio come variabile di ambiente.** È consigliabile leggere l'URL del servizio da una variabile di ambiente invece di codificarlo a livello di codice nella funzione . Il valore di questa variabile di ambiente verrà impostato più [avanti in questo articolo.](#set-up-security-access-for-the-function-app) Per altre informazioni sulle variabili di ambiente, vedere [Gestire l'app per le funzioni.](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* **Variabile statica per contenere un'istanza di HttpClient.** HttpClient è relativamente costoso da creare, quindi è necessario evitare di crearlo per ogni chiamata di funzione.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* **Credenziali dell'identità gestita.**
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* **Variabile locale _DigitalTwinsClient._** Aggiungere la variabile all'interno della funzione per contenere l'Gemelli digitali di Azure del client. Non *rendere* statica questa variabile all'interno della classe.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* **Controllo Null per _adtInstanceUrl._** Aggiungere il controllo Null e quindi eseguire il wrapping della logica della funzione in un blocco try/catch per rilevare eventuali eccezioni.

Dopo queste modifiche, il codice della funzione sarà simile all'esempio seguente.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

Ora che l'applicazione è stata scritta, è possibile pubblicarla in Azure.

## <a name="publish-the-function-app-to-azure"></a>Pubblicare l'app per le funzioni in Azure

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

### <a name="verify-the-publication-of-your-function"></a>Verificare la pubblicazione della funzione

1. Accedere usando le proprie credenziali nel portale di Azure [.](https://portal.azure.com/)
2. Nella casella di ricerca nella parte superiore della finestra cercare il nome dell'app per le funzioni e quindi selezionarla.

    :::image type="content" source="media/how-to-create-azure-function/search-function-app.png" alt-text="Screenshot che mostra il portale di Azure. Nel campo di ricerca immettere il nome dell'app per le funzioni." lightbox="media/how-to-create-azure-function/search-function-app.png":::

3. Nella pagina **App per** le funzioni visualizzata scegliere Funzioni dal menu **a sinistra.** Se la funzione viene pubblicata correttamente, il relativo nome viene visualizzato nell'elenco.

    > [!Note] 
    > Potrebbe essere necessario attendere alcuni minuti o aggiornare la pagina un paio di volte prima che la funzione venga visualizzata nell'elenco delle funzioni pubblicate.

    :::image type="content" source="media/how-to-create-azure-function/view-published-functions.png" alt-text="Visualizzare le funzioni pubblicate nel portale di Azure." lightbox="media/how-to-create-azure-function/view-published-functions.png":::

Per accedere Gemelli digitali di Azure, l'app per le funzioni deve avere un'identità gestita dal sistema con autorizzazioni per accedere all'Gemelli digitali di Azure istanza. La configurazione verrà quindi impostata.

## <a name="set-up-security-access-for-the-function-app"></a>Configurare l'accesso di sicurezza per l'app per le funzioni

È possibile configurare l'accesso di sicurezza per l'app per le funzioni usando l'interfaccia della riga di comando di Azure o l'portale di Azure. Seguire i passaggi per l'opzione preferita.

# <a name="cli"></a>[CLI](#tab/cli)

Eseguire questi comandi in [un'installazione](https://shell.azure.com) Azure Cloud Shell'interfaccia [della riga di comando di Azure locale.](/cli/azure/install-azure-cli)
È possibile usare l'identità gestita dal sistema dell'app per le funzioni per **assegnargli** il ruolo proprietario Gemelli digitali di Azure dati per l'istanza Gemelli digitali di Azure funzione. Il ruolo concede all'app per le funzioni l'autorizzazione nell'istanza di per eseguire attività del piano dati. Rendere quindi l'URL dell'istanza accessibile alla funzione impostando una variabile di ambiente.

### <a name="assign-an-access-role"></a>Assegnare un ruolo di accesso

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Lo scheletro della funzione negli esempi precedenti richiede bearer token una funzione. Se il bearer token non viene passato, l'app per le funzioni non può eseguire l'autenticazione con Gemelli digitali di Azure. 

Per assicurarsi che il bearer token sia passato, configurare le [autorizzazioni per](../active-directory/managed-identities-azure-resources/overview.md) le identità gestite in modo che l'app per le funzioni possa accedere Gemelli digitali di Azure. Queste autorizzazioni vengono impostate una sola volta per ogni app per le funzioni.


1. Usare il comando seguente per visualizzare i dettagli dell'identità gestita dal sistema per la funzione. Prendere nota del `principalId` campo nell'output.

    ```azurecli-interactive 
    az functionapp identity show -g <your-resource-group> -n <your-App-Service-(function-app)-name> 
    ```

    >[!NOTE]
    > Se il risultato è vuoto anziché visualizzare i dettagli dell'identità, creare una nuova identità gestita dal sistema per la funzione usando questo comando:
    > 
    >```azurecli-interactive    
    >az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>  
    >```
    >
    > L'output visualizza i dettagli dell'identità, incluso `principalId` il valore necessario per il passaggio successivo. 

1. Usare il `principalId` valore nel comando seguente per assegnare l'identità dell'app per le funzioni al ruolo proprietario Gemelli digitali di Azure _dati_ per l'istanza Gemelli digitali di Azure funzione.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

### <a name="configure-application-settings"></a>Configurare le impostazioni dell'applicazione

Rendere l'URL dell'istanza accessibile alla funzione impostando una variabile di ambiente per la funzione. Per altre informazioni sulle variabili di ambiente, vedere [Gestire l'app per le funzioni.](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal) 

> [!TIP]
> L Gemelli digitali di Azure'URL dell'istanza viene creato  aggiungendo https:// all'inizio del nome host dell'istanza. Per visualizzare il nome host, insieme a tutte le proprietà dell'istanza, eseguire `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Completare i passaggi seguenti nella [portale di Azure](https://portal.azure.com/).

### <a name="assign-an-access-role"></a>Assegnare un ruolo di accesso

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Un'identità gestita assegnata dal sistema consente alle risorse di Azure di eseguire l'autenticazione ai servizi cloud (ad esempio, Azure Key Vault) senza archiviare le credenziali nel codice. Dopo aver abilitato l'identità gestita assegnata dal sistema, tutte le autorizzazioni necessarie possono essere concesse tramite il controllo degli accessi in base al ruolo di Azure. 

Il ciclo di vita di questo tipo di identità gestita è associato al ciclo di vita di questa risorsa. Inoltre, ogni risorsa può avere una sola identità gestita assegnata dal sistema.

1. [Nell'portale di Azure](https://portal.azure.com/)cercare l'app per le funzioni digitandone il nome nella casella di ricerca. Selezionare l'app dai risultati. 

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Screenshot del portale di Azure. Il nome dell'app per le funzioni si trova nella barra di ricerca del portale e il risultato della ricerca è evidenziato.":::

1. Nel menu a sinistra della pagina dell'app per le funzioni selezionare __Identità__ per usare un'identità gestita per la funzione. Nella pagina __Assegnata dal__ sistema verificare che Stato __sia__ impostato su **On**. In caso contrario, impostarla ora e quindi **salvare** la modifica.

    :::image type="content" source="media/how-to-create-azure-function/verify-system-managed-identity.png" alt-text="Screenshot del portale di Azure. Nella pagina Identità dell'app per le funzioni l'opzione Stato è impostata su Attivata." lightbox="media/how-to-create-azure-function/verify-system-managed-identity.png":::

1. Selezionare __Assegnazioni di ruolo di Azure.__

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-1.png" alt-text="Screenshot del portale di Azure. Nella pagina Identità della funzione di Azure, in Autorizzazioni, il pulsante Assegnazioni di ruolo di Azure è evidenziato." lightbox="media/how-to-create-azure-function/add-role-assignment-1.png":::

    Selezionare __+ Aggiungi assegnazione di ruolo (anteprima).__

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-2.png" alt-text="Screenshot del portale di Azure. Nella pagina Assegnazioni di ruolo di Azure il pulsante Aggiungi assegnazione di ruolo (anteprima) è evidenziato." lightbox="media/how-to-create-azure-function/add-role-assignment-2.png":::

1. Nella pagina __Aggiungi assegnazione di ruolo (anteprima)__ selezionare i valori seguenti:

    * **Ambito:** _gruppo di risorse_
    * **Sottoscrizione** Selezionare la sottoscrizione di Azure.
    * **Gruppo di risorse:** selezionare il gruppo di risorse.
    * **Ruolo**: _Gemelli digitali di Azure proprietario dei dati_

    Salvare i dettagli selezionando __Salva__.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-3.png" alt-text="Screenshot del portale di Azure, che mostra come aggiungere una nuova assegnazione di ruolo. La finestra di dialogo mostra i campi Ambito, Sottoscrizione, Gruppo di risorse e Ruolo.":::

### <a name="configure-application-settings"></a>Configurare le impostazioni dell'applicazione

Per rendere accessibile l'URL dell'Gemelli digitali di Azure alla funzione, è possibile impostare una variabile di ambiente. Le impostazioni dell'applicazione vengono esposte come variabili di ambiente per consentire l'accesso all Gemelli digitali di Azure istanza di . Per altre informazioni sulle variabili di ambiente, vedere [Gestire l'app per le funzioni.](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal) 

Per impostare una variabile di ambiente con l'URL dell'istanza, trovare prima il nome host dell'istanza: 

1. Cercare l'istanza nella [portale di Azure](https://portal.azure.com). 
1. Nel menu a sinistra selezionare __Panoramica.__ 
1. Copiare il __valore nome__ host.

    :::image type="content" source="media/how-to-create-azure-function/instance-host-name.png" alt-text="Screenshot del portale di Azure. Nella pagina Panoramica dell'istanza il valore del nome host è evidenziato.":::

È ora possibile creare un'impostazione dell'applicazione:

1. Nella barra di ricerca del portale cercare l'app per le funzioni e quindi selezionarla dai risultati.

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Screenshot del portale di Azure. Il nome dell'app per le funzioni viene cercato nella barra di ricerca del portale. Il risultato della ricerca è evidenziato.":::

1. A sinistra selezionare __Configurazione__. Nella scheda __Impostazioni applicazione selezionare__ quindi + Nuova impostazione __applicazione__.

    :::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Screenshot del portale di Azure. Nella scheda Configurazione dell'app per le funzioni è evidenziato il pulsante per creare un'impostazione Nuova applicazione.":::

1. Nella finestra visualizzata usare il valore del nome host copiato per creare un'impostazione dell'applicazione.
    * **Nome:** ADT_SERVICE_URL
    * **Valore:** https://{your-azure-digital-twins-host-name}
    
    Selezionare __OK per__ creare un'impostazione dell'applicazione.
    
    :::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Screenshot del portale di Azure. Nella pagina Aggiungi/Modifica impostazione applicazione vengono compilati i campi Nome e Valore. Il pulsante O K è evidenziato.":::

1. Dopo aver creato l'impostazione, questa verrà visualizzata nella __scheda Impostazioni__ applicazione. Verificare che **ADT_SERVICE_URL** visualizzato nell'elenco. Salvare quindi la nuova impostazione dell'applicazione selezionando __Salva__.

    :::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Screenshot del portale di Azure. Nella scheda delle impostazioni dell'applicazione è evidenziata la nuova impostazione A D SERVICE U R L. È anche evidenziato il pulsante Salva.":::

1. Qualsiasi modifica alle impostazioni dell'applicazione richiede un riavvio dell'applicazione, quindi selezionare __Continua__ per riavviare l'applicazione quando richiesto.

    :::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Screenshot del portale di Azure. Una nota indica che eventuali modifiche alle impostazioni dell'applicazione riavvieranno l'applicazione. Il pulsante Continua è evidenziato.":::

---

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si configura un'app per le funzioni in Azure per l'uso con Gemelli digitali di Azure. Successivamente, vedere come basarsi sulla funzione di base per [inserire i dati dell'hub IoT Gemelli digitali di Azure](how-to-ingest-iot-hub-data.md).
