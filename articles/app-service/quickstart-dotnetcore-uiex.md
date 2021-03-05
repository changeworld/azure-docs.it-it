---
title: "Guida introduttiva: Creare un'app ASP.NET Core in C#"
description: Informazioni su come eseguire app Web nel Servizio app di Azure distribuendo la prima app ASP.NET Core.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4e2e34d73bc3048c587d7c0505f4978dd638370a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180276"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Guida introduttiva: Creare un'app Web ASP.NET Core in Azure

::: zone pivot="platform-windows"  

In questa Guida introduttiva si apprenderà come creare e distribuire la prima app Web ASP.NET Core <abbr title="Un servizio basato su HTTP per l'hosting di applicazioni Web, API REST e applicazioni back-end per dispositivi mobili.">Servizio app di Azure</abbr>. Il servizio app supporta app .NET 5.0.

Al termine, sarà presente un'Azure <abbr title="Un contenitore logico per le risorse di Azure correlate che è possibile gestire come unità.">gruppo di risorse</abbr>, costituito da un <abbr title="Il piano che specifica la posizione, le dimensioni e le funzionalità del server farm Web che ospita l'app.">Piano di servizio app</abbr> e un <abbr title="La rappresentazione dell'app Web, che contiene il codice dell'app, i nomi host DNS, i certificati e le risorse correlate.">app del servizio app</abbr> con un'applicazione ASP.NET Core di esempio distribuita.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Preparare l'ambiente

- **Ottenere un account Azure** con un account attivo <abbr title="Struttura organizzativa di base in cui si gestiscono le risorse in Azure, in genere associate a un singolo utente o a un reparto all'interno di un'organizzazione.">sottoscrizione</abbr>. [Creare un account gratuitamente](https://azure.microsoft.com/free/dotnet/).
- **Installare <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>** con il carico di lavoro **sviluppo di ASP.NET e Web** .

<details>
<summary>Si dispone già di Visual Studio 2019?</summary>
  Se Visual Studio 2019 è già installato:

  - **Installare gli aggiornamenti più recenti** in Visual Studio selezionando la **Guida**  >  **Verifica disponibilità aggiornamenti**. Gli aggiornamenti più recenti contengono .NET 5.0 SDK.
  - **Aggiungere il carico di lavoro** scegliendo **strumenti**  >  **Ottieni strumenti e funzionalità**.
</details>

<hr/> 

## <a name="2-create-an-aspnet-core-web-app"></a>2. creare un'app Web ASP.NET Core

1. Aprire Visual Studio e selezionare **Crea un nuovo progetto**.

1. In **Crea un nuovo progetto** selezionare **Applicazione Web ASP.NET Core** e verificare che **C#** sia elencato nei linguaggi disponibili per tale scelta, quindi selezionare **Avanti**.

1. In **Configura il nuovo progetto** assegnare al progetto di applicazione Web il nome *myFirstAzureWebApp* e selezionare **Crea**.

   ![Configurare il progetto di app Web](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. Per un'app .NET 5.0, selezionare **ASP.NET Core 5.0** nell'elenco a discesa. In caso contrario, usare il valore predefinito.

1. È possibile distribuire qualsiasi tipo di app Web ASP.NET Core in Azure, ma per questo argomento di avvio rapido scegliere il modello **App Web ASP.NET Core**. Verificare che l'opzione **Autenticazione** sia impostata su **Nessuna autenticazione** e che non sia selezionata nessun'altra opzione. Scegliere quindi **Create** (Crea).

   ![Creare una nuova app Web ASP.NET Core](./media/quickstart-dotnetcore/create-aspnet-core-web-app-5.png) 
   
1. Nel menu di Visual Studio selezionare **Debug** > **Avvia senza eseguire debug** per eseguire l'app Web in locale.

   ![App Web in esecuzione in locale](./media/quickstart-dotnetcore/web-app-running-locally.png)

<hr/> 

## <a name="3-publish-your-web-app"></a>3. pubblicare l'app Web

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **myFirstAzureWebApp** e scegliere **Pubblica**. 

1. In **Pubblica** selezionare **Azure** e fare clic su **Avanti**.

1. Le opzioni disponibili variano a seconda che sia già stato eseguito l'accesso ad Azure e che si abbia un account di Visual Studio collegato a un account di Azure. Selezionare **Aggiungi un account** o **Accedi** per accedere alla sottoscrizione di Azure. Se è già stato effettuato l'accesso, selezionare l'account da usare.

   ![Accedere ad Azure](./media/quickstart-dotnetcore/sign-in-azure-vs2019.png)

1. A destra di **Istanze di Servizio app** fare clic su **+** .

   ![Nuova app del servizio app](./media/quickstart-dotnetcore/publish-new-app-service.png)

1. Per **Sottoscrizione**, accettare la sottoscrizione elencata o sceglierne una nuova nell'elenco a discesa.

1. Per **Gruppo di risorse** selezionare **Nuovo**. In **Nome nuovo gruppo di risorse** immettere *myResourceGroup* e scegliere **OK**. 

1. Per **Piano di hosting** selezionare **Nuovo**. 

1. Nella finestra di dialogo **Piano di hosting: Crea nuovo** immettere i valori specificati nella tabella seguente:

   | Impostazione  | Valore consigliato |
   | -------- | --------------- |
   | **Piano di hosting**  | *myFirstAzureWebAppPlan* |
   | **Posizione**      | *Europa occidentale* |
   | **Dimensione**          | *Free* |
   
   ![Creare un nuovo piano di hosting](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. In **nome** immettere un nome univoco per l'app.

    <details>
        <summary>Quali caratteri è possibile utilizzare?</summary>
        I caratteri validi sono a-z, A-Z, 0-9 e-. È possibile accettare il nome univoco generato automaticamente. L'URL dell'app Web è http:// <code>&lt;app-name&gt;.azurewebsites.net</code> , dove <code>&lt;app-name&gt;</code> è il nome dell'app.
    </details>

1. Selezionare **Crea** per creare le risorse di Azure. 

   ![Creare le risorse per l'app](./media/quickstart-dotnetcore/web-app-name-vs2019.png)

1. Attendere il completamento della creazione delle risorse di Azure da parte della procedura guidata. Selezionare **Fine** per chiudere la procedura guidata.

1. Nella pagina **pubblica** fare clic su **pubblica** per distribuire il progetto. 

    <details>
        <summary>Funzionalità di Visual Studio</summary>
        Visual Studio compila, crea il pacchetto e pubblica l'app in Azure, quindi la avvia nel browser predefinito.
    </details>

   ![App Web ASP.NET pubblicata in esecuzione in Azure](./media/quickstart-dotnetcore/web-app-running-live.png)

<hr/> 

## <a name="4-update-the-app-and-redeploy"></a>4. aggiornare l'app e ridistribuirla

1. Nel progetto in **Esplora soluzioni** aprire **Pagine** > **Index.cshtml**.

1. Sostituire l'intero tag `<div>` con il codice seguente:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Per la ridistribuzione in Azure, fare clic con il pulsante destro del mouse sul progetto **myFirstAzureWebApp** in **Esplora soluzioni** e selezionare **Pubblica**.

1. Nella pagina di riepilogo **Pubblica** selezionare **Pubblica**.

   <!-- ![Publish update to web app](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png) -->

    Al termine del processo di pubblicazione, Visual Studio avvia un browser sull'URL dell'app Web.

    ![App Web ASP.NET aggiornata in esecuzione in Azure](./media/quickstart-dotnetcore/updated-web-app-running-live.png)

<hr/> 

## <a name="5-manage-the-azure-app"></a>5. gestire l'app Azure

1. Passare alla [portale di Azure](https://portal.azure.com)e cercare e selezionare **Servizi app**.

    ![Selezionare Servizi app](./media/quickstart-dotnetcore/app-services.png)
    
1. Nella pagina **Servizi app** selezionare il nome dell'app Web.

    :::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Screenshot della pagina Servizi app con un'app Web di esempio selezionata.":::

1. La pagina **Panoramica** per l'app Web contiene le opzioni per la gestione di base, ad esempio Sfoglia, Arresta, Avvia, Riavvia ed Elimina. Il menu a sinistra include ulteriori pagine per la configurazione dell'app.

    ![Servizio app nel portale di Azure](./media/quickstart-dotnetcore/web-app-overview-page.png)
    
<hr/> 

## <a name="6-clean-up-resources"></a>6. Pulire le risorse

1. Nel menu del portale di Azure o nella pagina **Home** selezionare **Gruppi di risorse**. Quindi, nella pagina **Gruppi di risorse** selezionare **myResourceGroup**.

1. Nella pagina **myResourceGroup** assicurarsi che le risorse elencate siano quelle da eliminare.

1. Selezionare **Elimina gruppo di risorse**, digitare **myResourceGroup** nella casella di testo per confermare e quindi selezionare **Elimina**.

<hr/> 

## <a name="next-steps"></a>Passaggi successivi

Procedere con l'articolo successivo per informazioni su come creare un'app .NET Core e connetterla a un database SQL:

- [ASP.NET Core con database SQL](tutorial-dotnetcore-sqldb-app.md)
- [Configurare l'app ASP.NET Core](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
Questa Guida introduttiva illustra come creare un'app [.NET Core](/aspnet/core/) in <abbr title="Il Servizio app in Linux offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione basato sul sistema operativo Linux.">Servizio app in Linux</abbr>. È necessario creare l'app usando l'[interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli) e GIT per distribuire il codice Node.js nell'app.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Preparare l'ambiente

- **Ottenere un account Azure** con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/dotnet/).
- **Installare** la versione più recente di <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">.NET Core 3,1 sdk</a> o <a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">.NET 5,0 SDK</a>.
- **<a href="/cli/azure/install-azure-cli" target="_blank">Installare l'interfaccia della riga di comando di Azure più recente</a>**.

[Problemi? Segnalarli](https://aka.ms/DotNetAppServiceLinuxQuickStart).

<hr/> 

## <a name="2-create-the-app-locally"></a>2. creare l'app localmente

1. Eseguire `mkdir hellodotnetcore` per creare la directory.

    ```bash
    mkdir hellodotnetcore
    ```

1. Eseguire `cd hellodotnetcore` per modificare la directory. 

    ```bash
    cd hellodotnetcore
    ```

1. Eseguire `dotnet new web` per creare una nuova app .NET Core.

    ```bash
    dotnet new web
    ```

<hr/> 

## <a name="3-run-the-app-locally"></a>3. eseguire l'app localmente

1. Eseguire `dotnet run` per verificarne l'aspetto quando lo si distribuisce in Azure.

    ```bash
    dotnet run
    ```
    
1. **Aprire un Web browser** e passare all'app all'indirizzo `http://localhost:5000` .

![Eseguire il test con il browser](media/quickstart-dotnetcore/dotnet-browse-local.png)

[Problemi? Segnalarli](https://aka.ms/DotNetAppServiceLinuxQuickStart).

<hr/> 

## <a name="4-sign-into-azure"></a>4. accedere ad Azure

Eseguire `az login` per accedere ad Azure.

```azurecli
az login
```

[Problemi? Segnalarli](https://aka.ms/DotNetAppServiceLinuxQuickStart).

<hr/> 

## <a name="5-deploy-the-app"></a>5. distribuire l'app

1. **Esegui** `az webapp up` nella cartella locale. **Sostituire** <nome app> con un nome univoco globale.

    ```azurecli
    az webapp up --sku F1 --name <app-name> --os-type linux
    ```
    
    <details>
    <summary>Risoluzione dei problemi</summary>
    <ul>
    <li>Se il <code>az</code> comando non è riconosciuto, assicurarsi di avere installato l'interfaccia della riga di comando di Azure come descritto in <a href="#1-prepare-your-environment">preparare l'ambiente</a>.</li>
    <li>Sostituire <code>&lt;app-name&gt;</code> con un nome univoco in tutte le aree di Azure <em> . i caratteri validi sono <code>a-z</code> , <code>0-9</code> e <code>-</code> </em> . Un criterio valido consiste nell'usare una combinazione del nome della società e di un identificatore dell'app.</li>
    <li>Con l'argomento <code>--sku F1</code> l'app Web viene creata nel piano tariffario Gratuito. Omettere questo argomento per usare un livello Premium più rapido, che però comporta un costo orario.</li>
    <li>Facoltativamente, è possibile includere l'argomento <code>--location &lt;location-name&gt;</code>, dove <code>&lt;location-name&gt;</code> è un'area di Azure disponibile. È possibile recuperare un elenco di aree consentite per l'account Azure eseguendo il <a href="/cli/azure/appservice#az-appservice-list-locations"> <code>az account list-locations</code> </a> comando.</li>
    </ul>
    </details>
    
1. Attendere il completamento del comando, Potrebbero essere necessari alcuni minuti e termina con "è possibile avviare l'app in http:// &lt; app-name &gt; . azurewebsites.NET".

    <details>
    <summary>Cosa sta <code>az webapp up</code> facendo?</summary>
    <p>Il comando <code>az webapp up</code> esegue le azioni seguenti:</p>
    <ul>
    <li>Crea un gruppo di risorse predefinito.</li>
    <li>Creare un piano di servizio app predefinito.</li>
    <li><a href="/cli/azure/webapp#az-webapp-create">Creare un'app del servizio app</a> con il nome specificato.</li>
    <li><a href="/azure/app-service/deploy-zip">Distribuire file ZIP</a> i dalla directory di lavoro corrente all'app.</li>
    <li>Durante l'esecuzione, fornisce messaggi sulla creazione di risorse, la registrazione e la distribuzione ZIP.</li>
    </ul>
    </details>
    
# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

![Output di esempio del comando az webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-3.1.png)

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

![Output di esempio del comando az webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-5.0.png)

---

[Problemi? Segnalarli](https://aka.ms/DotNetAppServiceLinuxQuickStart).

<hr/> 

## <a name="6-browse-to-the-app"></a>6. passare all'app

**Passare all'applicazione distribuita** usando il Web browser.

```bash
http://<app_name>.azurewebsites.net
```

![App di esempio in esecuzione in Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

[Problemi? Segnalarli](https://aka.ms/DotNetAppServiceLinuxQuickStart).

<hr/> 

## <a name="7-update-and-redeploy-the-code"></a>7. aggiornare e ridistribuire il codice

1. **Aprire il file _Startup.cs_** nella directory locale. 

1. **Apportare una piccola modifica** al testo nella chiamata al metodo `context.Response.WriteAsync` .

    ```csharp
    await context.Response.WriteAsync("Hello Azure!");
    ```
    
1. **Salvare le modifiche**.

1. **Esegui** `az webapp up` per ridistribuire:

```azurecli
az webapp up --os-type linux
```

<details>
<summary>Cosa sta <code>az webapp up</code> facendo questa volta?</summary>
La prima volta che è stato eseguito il comando, sono stati salvati il nome dell'app, il gruppo di risorse e il piano di servizio app nel file <i>. Azure/config</i> dalla radice del progetto. Quando si esegue nuovamente dalla radice del progetto, vengono usati i valori salvati in <i>. Azure/config</i>, viene rilevato che le risorse del servizio app esistono già ed esegue nuovamente la distribuzione zip.
</details>

1. Al termine della distribuzione, fare **clic su Aggiorna** nella finestra del browser aperta in precedenza.

![App di esempio aggiornata in esecuzione in Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

[Problemi? Segnalarli](https://aka.ms/DotNetAppServiceLinuxQuickStart).

<hr/> 

## <a name="8-manage-your-new-azure-app"></a>8. gestire la nuova app Azure

1. Accedere al <a href="https://portal.azure.com" target="_blank">portale di Azure</a>.

1. Nel menu a sinistra fare clic su **Servizi app** e quindi sul nome dell'app Azure.

    :::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list-up.png" alt-text="Screenshot della pagina Servizi app con un'app Azure di esempio selezionata.":::

1. Nella pagina panoramica è possibile eseguire attività di gestione di base, ad esempio browse, stop, Start, restart ed Delete. Il menu a sinistra fornisce varie pagine per la configurazione dell'app. 

![Pagina del servizio app nel portale di Azure](media/quickstart-dotnetcore/portal-app-overview-up.png)

<hr/> 

## <a name="9-clean-up-resources"></a>9. Pulire le risorse

**Esegui** `az group delete --name myResourceGroup` per eliminare il gruppo di risorse.

```azurecli-interactive
az group delete --name myResourceGroup
```

[Problemi? Segnalarli](https://aka.ms/DotNetAppServiceLinuxQuickStart).

<hr/> 

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: App ASP.NET Core con database SQL](tutorial-dotnetcore-sqldb-app.md)
- [Configurare l'app ASP.NET Core](configure-language-dotnetcore.md)

::: zone-end
