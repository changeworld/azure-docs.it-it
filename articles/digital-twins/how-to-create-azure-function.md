---
title: Configurare una funzione in Azure per l'elaborazione dei dati
titleSuffix: Azure Digital Twins
description: Vedere come creare una funzione in Azure che può accedere ed essere attivata da dispositivi gemelli digitali.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4889744347b72603a0f6318f981bc2db4906b835
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102433540"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Connetti le app per le funzioni in Azure per l'elaborazione dei dati

L'aggiornamento dei dispositivi gemelli digitali basati sui dati viene gestito usando le [**Route di eventi**](concepts-route-events.md) tramite le risorse di calcolo, ad esempio una funzione eseguita usando [funzioni di Azure](../azure-functions/functions-overview.md). Le funzioni possono essere usate per aggiornare un dispositivo gemello digitale in risposta a:
* dati di telemetria del dispositivo provenienti dall'hub Internet
* modifica delle proprietà o altri dati provenienti da un altro dispositivo gemello digitale all'interno del grafico gemello

Questo articolo illustra la creazione di una funzione in Azure per l'uso con i dispositivi gemelli digitali di Azure. 

Ecco una panoramica dei passaggi che contiene:

1. Creare un progetto Funzioni di Azure in Visual Studio
2. Scrivere una funzione con un trigger di [griglia di eventi](../event-grid/overview.md)
3. Aggiungere il codice di autenticazione alla funzione (per poter accedere ai dispositivi gemelli digitali di Azure)
4. Pubblicare l'app per le funzioni in Azure
5. Configurare l'accesso di [sicurezza](concepts-security.md) per l'app per le funzioni

## <a name="prerequisite-set-up-azure-digital-twins-instance"></a>Prerequisito: configurare l'istanza di dispositivi gemelli digitali di Azure

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Creare un'app per le funzioni in Visual Studio

In Visual Studio 2019 selezionare _File > nuovo progetto >_ e cercare il modello _funzioni di Azure_ . Selezionare _Avanti_.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Screenshot di Visual Studio che mostra la finestra di dialogo nuovo progetto. Il modello di progetto funzioni di Azure è evidenziato.":::

Specificare un nome per l'app per le funzioni e selezionare _Crea_.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Screenshot di Visual Studio che mostra la finestra di dialogo per configurare un nuovo progetto, incluso il nome del progetto, il percorso di salvataggio, la scelta per creare una nuova soluzione e il nome della soluzione.":::

Selezionare il tipo di app per le funzioni del *trigger griglia di eventi* e selezionare _Crea_.

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="Screenshot di Visual Studio che mostra la finestra di dialogo per creare una nuova applicazione funzioni di Azure. L'opzione trigger griglia di eventi è evidenziata.":::

Una volta creata l'app per le funzioni, Visual Studio genererà un esempio di codice in un file **function1.cs** nella cartella del progetto. Questa funzione breve viene utilizzata per registrare gli eventi.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Screenshot di Visual Studio nella finestra del progetto per il nuovo progetto creato. Esiste codice per una funzione di esempio denominata Funzione1." lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>Scrivere una funzione con un trigger di griglia di eventi

È possibile scrivere una funzione aggiungendo SDK all'app per le funzioni. L'app per le funzioni interagisce con i dispositivi gemelli digitali di Azure con [Azure Digital Twins SDK per .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client). 

Per usare l'SDK, è necessario includere nel progetto i pacchetti seguenti. È possibile installare i pacchetti usando Gestione pacchetti NuGet di Visual Studio o aggiungere i pacchetti usando `dotnet` in uno strumento da riga di comando.

* [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure. Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System.Net.Http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure. Core](https://www.nuget.org/packages/Azure.Core/)

Successivamente, nel Esplora soluzioni di Visual Studio aprire il file _function1.cs_ in cui è presente il codice di esempio e aggiungere le `using` istruzioni seguenti per questi pacchetti alla funzione. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Aggiungere il codice di autenticazione alla funzione

Verranno ora dichiarate le variabili a livello di classe e verrà aggiunto il codice di autenticazione che consente alla funzione di accedere ai dispositivi gemelli digitali di Azure. Si aggiungerà quanto segue alla funzione nel file _function1.cs_ .

* Codice per leggere l'URL del servizio di dispositivi digitali gemelli di Azure come **variabile di ambiente**. È consigliabile leggere l'URL del servizio da una variabile di ambiente, anziché impostarlo come hardcoded nella funzione. Il valore di questa variabile di ambiente verrà impostato [più avanti in questo articolo](#set-up-security-access-for-the-function-app). Per altre informazioni sulle variabili di ambiente, vedere [*gestire l'app per le funzioni*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal).

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* Variabile statica per la quale deve essere contenuta un'istanza di HttpClient. HttpClient è relativamente costoso da creare ed è consigliabile evitare di eseguire questa operazione per ogni chiamata di funzione.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* È possibile usare le credenziali di identità gestite in funzioni di Azure.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* Aggiungere una variabile locale _DigitalTwinsClient_ all'interno della funzione per ospitare l'istanza del client dei dispositivi digitali gemelli di Azure. Non *rendere questa* variabile statica all'interno della classe.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* Aggiungere un controllo null per _adtInstanceUrl_ ed eseguire il wrapping della logica di funzione in un blocco try/catch per rilevare eventuali eccezioni.

Una volta apportate queste modifiche, il codice della funzione sarà simile al seguente:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

Ora che l'applicazione è stata scritta, è possibile pubblicarla in Azure seguendo la procedura descritta nella sezione successiva.

## <a name="publish-the-function-app-to-azure"></a>Pubblicare l'app per le funzioni in Azure

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

## <a name="set-up-security-access-for-the-function-app"></a>Configurare l'accesso di sicurezza per l'app per le funzioni

È possibile configurare l'accesso di sicurezza per l'app per le funzioni usando l'interfaccia della riga di comando di Azure o la portale di Azure. Attenersi alla procedura seguente per l'opzione preferita.

# <a name="cli"></a>[CLI](#tab/cli)

È possibile eseguire questi comandi in [Azure cloud Shell](https://shell.azure.com) o in un' [installazione locale dell'interfaccia](/cli/azure/install-azure-cli)della riga di comando di Azure.

### <a name="assign-access-role"></a>Assegnare un ruolo di accesso

Per poter eseguire l'autenticazione con i dispositivi gemelli digitali di Azure, la struttura della funzione degli esempi precedenti richiede che venga passata una bearer token. Per assicurarsi che questo bearer token venga passato, è necessario configurare le autorizzazioni di [identità del servizio gestita (MSI)](../active-directory/managed-identities-azure-resources/overview.md) per l'app per le funzioni per accedere ai dispositivi gemelli digitali di Azure. Questa operazione deve essere eseguita solo una volta per ogni app per le funzioni.

È possibile usare l'identità gestita dal sistema dell'app per le funzioni per assegnargli il ruolo di _**proprietario dei dati di Azure Digital gemelli**_ per l'istanza di Digital gemelli di Azure. In questo modo si concede all'app per le funzioni l'autorizzazione per l'esecuzione di attività del piano dati. Quindi, rendere accessibile l'URL dell'istanza di Azure Digital Twins alla funzione impostando una variabile di ambiente.

1. Usare il comando seguente per visualizzare i dettagli dell'identità gestita dal sistema per la funzione. Prendere nota del campo _principalId_ nell'output.

    ```azurecli-interactive 
    az functionapp identity show -g <your-resource-group> -n <your-App-Service-(function-app)-name> 
    ```

    >[!NOTE]
    > Se il risultato è vuoto anziché visualizzare i dettagli di un'identità, creare una nuova identità gestita dal sistema per la funzione utilizzando questo comando:
    > 
    >```azurecli-interactive    
    >az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>  
    >```
    >
    > L'output visualizzerà quindi i dettagli dell'identità, incluso il valore _PrincipalId_ richiesto per il passaggio successivo. 

1. Usare il valore _principalId_ nel comando seguente per assegnare l'identità dell'app per le funzioni al ruolo _Azure Digital Twins Owner_ (Proprietario di Gemelli digitali di Azure) per l'istanza di Gemelli digitali di Azure.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

### <a name="configure-application-settings"></a>Configurare le impostazioni dell'applicazione

Infine, rendere accessibile l'URL dell'istanza di Azure Digital Twins alla funzione impostando una variabile di **ambiente** . Per altre informazioni sulle variabili di ambiente, vedere [*gestire l'app per le funzioni*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). 

> [!TIP]
> L'URL dell'istanza di Azure Digital gemelli viene creato aggiungendo *https://* all'inizio del *nome host* dell'istanza di Azure Digital gemelli. Per visualizzare il nome host, insieme a tutte le proprietà dell'istanza, è possibile eseguire `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Completare i passaggi seguenti nell' [portale di Azure](https://portal.azure.com/).

### <a name="assign-access-role"></a>Assegnare un ruolo di accesso

Un'identità gestita assegnata dal sistema consente alle risorse di Azure di eseguire l'autenticazione nei servizi cloud, ad esempio Azure Key Vault, senza archiviare le credenziali nel codice. Una volta abilitate, tutte le autorizzazioni necessarie possono essere concesse tramite il controllo degli accessi in base al ruolo di Azure. Il ciclo di vita di questo tipo di identità gestita è associato al ciclo di vita di questa risorsa. Ogni risorsa, inoltre, può avere solo un'identità gestita assegnata dal sistema.

1. Nella [portale di Azure](https://portal.azure.com/)cercare l'app per le funzioni digitando il relativo nome nella barra di ricerca. Selezionare l'app dai risultati. 

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Screenshot della portale di Azure: il nome dell'app per le funzioni viene cercato nella barra di ricerca del portale e il risultato della ricerca è evidenziato.":::

1. Nella pagina app per le funzioni selezionare _Identity_ nella barra di spostamento a sinistra per usare un'identità gestita per la funzione. Nella pagina _assegnazione sistema_ verificare che lo _stato_ sia impostato **su on (in** caso contrario, impostarlo ora e *salvare* la modifica).

    :::image type="content" source="media/how-to-create-azure-function/verify-system-managed-identity.png" alt-text="Screenshot del portale di Azure: nella pagina identità dell'app per le funzioni l'opzione stato è impostata su on." lightbox="media/how-to-create-azure-function/verify-system-managed-identity.png":::

1. Selezionare il pulsante _assegnazioni di ruolo di Azure_ , che consente di aprire la pagina *assegnazioni di ruolo di Azure* .

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-1.png" alt-text="Screenshot del portale di Azure: un'evidenziazione del pulsante assegnazioni di ruolo di Azure in autorizzazioni nella pagina identità della funzione di Azure." lightbox="media/how-to-create-azure-function/add-role-assignment-1.png":::

    Selezionare _+ Aggiungi assegnazione ruolo (anteprima)_.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-2.png" alt-text="Screenshot del portale di Azure: un Highlight About + Add Role Assignment (Preview) nella pagina assegnazioni di ruolo di Azure." lightbox="media/how-to-create-azure-function/add-role-assignment-2.png":::

1. Nella pagina _Aggiungi assegnazione ruolo (anteprima)_ visualizzata selezionare i valori seguenti:

    * **Ambito**: gruppo di risorse
    * **Sottoscrizione**: selezionare la sottoscrizione di Azure
    * **Gruppo di risorse**: selezionare il gruppo di risorse dall'elenco a discesa
    * **Ruolo**: selezionare il proprietario di dati di dispositivi _gemelli digitali di Azure_ dall'elenco a discesa

    Salvare quindi i dettagli facendo clic sul pulsante _Salva_ .

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-3.png" alt-text="Screenshot della finestra di dialogo portale di Azure: per aggiungere una nuova assegnazione di ruolo (anteprima). Sono disponibili campi per ambito, sottoscrizione, gruppo di risorse e ruolo.":::

### <a name="configure-application-settings"></a>Configurare le impostazioni dell'applicazione

Per rendere accessibile l'URL dell'istanza di Azure Digital gemelli alla funzione, è possibile impostare una **variabile di ambiente** . Per altre informazioni sulle variabili di ambiente, vedere [*gestire l'app per le funzioni*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). Le impostazioni dell'applicazione sono esposte come variabili di ambiente per accedere all'istanza di Azure Digital gemelli. 

Per impostare una variabile di ambiente con l'URL dell'istanza, ottenere prima l'URL individuando il nome host dell'istanza di Azure Digital gemelli. Cercare l'istanza nella barra di ricerca [portale di Azure](https://portal.azure.com) . Selezionare quindi _Panoramica_ sulla barra di spostamento a sinistra per visualizzare il _nome host_. Copiare questo valore.

:::image type="content" source="media/how-to-create-azure-function/instance-host-name.png" alt-text="Screenshot del portale di Azure: dalla pagina Panoramica per l'istanza di Azure Digital Twins, viene evidenziato il valore del nome host.":::

È ora possibile creare un'impostazione dell'applicazione con i passaggi seguenti:

1. Cercare l'app per le funzioni nella barra di ricerca del portale e selezionarla dai risultati.

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Screenshot della portale di Azure: il nome dell'app per le funzioni viene cercato nella barra di ricerca del portale e il risultato della ricerca è evidenziato.":::

1. Selezionare _configurazione_ nella barra di spostamento a sinistra. Nella scheda _Impostazioni applicazione_ selezionare _+ nuova impostazione applicazione_.

    :::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Screenshot del portale di Azure: nella pagina configurazione per l'app per le funzioni, viene evidenziato il pulsante per creare una nuova impostazione dell'applicazione.":::

1. Nella finestra visualizzata, usare il valore del nome host copiato in precedenza per creare un'impostazione dell'applicazione.
    * **Nome**: ADT_SERVICE_URL
    * **Valore**: https://{Your-Azure-Digital-gemells-host-name}
    
    Selezionare _OK_ per creare un'impostazione dell'applicazione.
    
    :::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Screenshot della portale di Azure: il pulsante OK viene evidenziato dopo aver compilato i campi nome e valore nella pagina Aggiungi/modifica impostazione applicazione.":::

1. Dopo aver creato l'impostazione, dovrebbe essere visualizzato nuovamente nella scheda _Impostazioni applicazione_ . Verificare che *ADT_SERVICE_URL* venga visualizzato nell'elenco, quindi salvare la nuova impostazione dell'applicazione selezionando il pulsante _Salva_ .

    :::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Screenshot della portale di Azure: la pagina delle impostazioni dell'applicazione, con la nuova impostazione di ADT_SERVICE_URL evidenziata. Viene evidenziato anche il pulsante Salva.":::

1. Per rendere effettive le modifiche apportate alle impostazioni dell'applicazione è necessario riavviare l'applicazione, quindi selezionare _continua_ per riavviare l'applicazione quando richiesto.

    :::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Screenshot del portale di Azure: si noterà che tutte le modifiche apportate alle impostazioni dell'applicazione con il riavvio dell'applicazione. Il pulsante continua è evidenziato.":::

---

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati seguiti i passaggi per configurare un'app per le funzioni in Azure per l'uso con i dispositivi gemelli digitali di Azure.

Vedere quindi come compilare la funzione di base per inserire i dati dell'hub Internet in dispositivi gemelli digitali di Azure:
* [*Procedura: inserire dati di telemetria dall'hub Internet*](how-to-ingest-iot-hub-data.md)
