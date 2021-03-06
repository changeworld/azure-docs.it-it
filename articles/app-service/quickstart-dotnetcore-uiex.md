---
title: "Guida introduttiva: Creare un'app ASP.NET Core in C#"
description: Informazioni su come eseguire app Web nel Servizio app di Azure distribuendo la prima app ASP.NET Core.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ff86bedf47395b50dc25e552b8b3ed4176e23b65
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769104"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Guida introduttiva: Creare un'app Web ASP.NET Core in Azure

::: zone pivot="platform-windows"  

In questa guida introduttiva si apprenderà come creare e distribuire la prima app Web ASP.NET Core in <abbr title="Servizio basato su HTTP per l'hosting di applicazioni Web, API REST e applicazioni back-end per dispositivi mobili.">Servizio app di Azure</abbr>. Il servizio app supporta app .NET 5.0.

Al termine, si dirà di avere un'istanza di Azure <abbr title="Contenitore logico per le risorse di Azure correlate che è possibile gestire come unità.">gruppo di risorse</abbr>, costituito da un oggetto <abbr title="Piano che specifica la posizione, le dimensioni e le funzionalità del server farm che ospita l'app.">Piano di servizio app</abbr> e un <abbr title="Rappresentazione dell'app Web, che contiene il codice dell'app, i nomi host DNS, i certificati e le risorse correlate.">app del servizio app</abbr> con un esempio distribuito ASP.NET Core.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Preparare l'ambiente

- **Ottenere un account Azure** con un account attivo <abbr title="Struttura organizzativa di base in cui si gestiscono le risorse in Azure, in genere associate a un singolo o reparto all'interno di un'organizzazione.">sottoscrizione</abbr>. [Creare un account gratuitamente](https://azure.microsoft.com/free/dotnet/).
- **Installare <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 con</a>** il carico di lavoro ASP.NET sviluppo Web **e** web.

<details>
<summary>Hai già Visual Studio 2019?</summary>
Se Visual Studio 2019 è già installato:

<ul>
<li><strong>Installare gli aggiornamenti più recenti</strong> in Visual Studio selezionando <strong>Verifica</strong> della guida per &gt; <strong>gli aggiornamenti</strong>. Gli aggiornamenti più recenti contengono .NET 5.0 SDK.</li>
<li><strong>Aggiungere il carico di</strong> lavoro selezionando <strong>Strumenti</strong> Ottieni strumenti &gt; <strong>e funzionalità</strong>.</li>
</ul>
</details>

<hr/> 

## <a name="2-create-an-aspnet-core-web-app"></a>2. Creare un'app Web ASP.NET Core

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

## <a name="3-publish-your-web-app"></a>3. Pubblicare l'app Web

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

1. In **Nome** immettere un nome univoco per l'app.

    <details>
        <summary>Quali caratteri è possibile usare?</summary>
        I caratteri validi sono a-z, A-Z, 0-9 e -. È possibile accettare il nome univoco generato automaticamente. L'URL dell'app Web http:// <code>&lt;app-name&gt;.azurewebsites.net</code> , dove è il nome <code>&lt;app-name&gt;</code> dell'app.
    </details>

1. Selezionare **Crea** per creare le risorse di Azure. 

   ![Creare le risorse per l'app](./media/quickstart-dotnetcore/web-app-name-vs2019.png)

1. Attendere il completamento della creazione delle risorse di Azure da parte della procedura guidata. Selezionare **Fine** per chiudere la procedura guidata.

1. Nella pagina **Pubblica** fare clic su **Pubblica** per distribuire il progetto. 

    <details>
        <summary>Che cosa Visual Studio fare?</summary>
        Visual Studio compila, crea il pacchetto e pubblica l'app in Azure, quindi la avvia nel browser predefinito.
    </details>

   ![App Web ASP.NET pubblicata in esecuzione in Azure](./media/quickstart-dotnetcore/web-app-running-live.png)

<hr/> 

## <a name="4-update-the-app-and-redeploy"></a>4. Aggiornare l'app e ridistribuire

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

## <a name="5-manage-the-azure-app"></a>5. Gestire l'app Azure

1. Passare al [portale di Azure](https://portal.azure.com)e cercare e selezionare **Servizi app.**

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
Questa guida introduttiva illustra come creare un'app [.NET Core](/aspnet/core/) in <abbr title="Il Servizio app in Linux offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione basato sul sistema operativo Linux.">Servizio app in Linux</abbr>. È necessario creare l'app usando l'[interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli) e GIT per distribuire il codice Node.js nell'app.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Preparare l'ambiente

- **Ottenere un account Azure** con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/dotnet/).
- **Installare** la versione <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">più recente di .NET Core 3.1 SDK</a> o <a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">.NET 5.0 SDK.</a>
- **<a href="/cli/azure/install-azure-cli" target="_blank">Installare la versione più recente dell'interfaccia della riga di comando di Azure.</a>**

[Problemi? Segnalarli](https://aka.ms/DotNetAppServiceLinuxQuickStart).

<hr/> 

## <a name="2-create-the-app-locally"></a>2. Creare l'app in locale

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

## <a name="3-run-the-app-locally"></a>3. Eseguire l'app in locale

1. Eseguire `dotnet run` per verificarlo quando viene distribuito in Azure.

    ```bash
    dotnet run
    ```
    
1. **Aprire un Web browser** e passare all'app all'indirizzo `http://localhost:5000` .

![Eseguire il test con il browser](media/quickstart-dotnetcore/dotnet-browse-local.png)

[Problemi? Segnalarli](https://aka.ms/DotNetAppServiceLinuxQuickStart).

<hr/> 

## <a name="4-sign-into-azure"></a>4. Accedere ad Azure

Eseguire `az login` per accedere ad Azure.

```azurecli
az login
```

[Problemi? Segnalarli](https://aka.ms/DotNetAppServiceLinuxQuickStart).

<hr/> 

## <a name="5-deploy-the-app"></a>5. Distribuire l'app

1. **Eseguire** `az webapp up` nella cartella locale. **Sostituire** <app-name> con un nome univoco globale.

    ```azurecli
    az webapp up --sku F1 --name <app-name> --os-type linux
    ```
    
    <details>
    <summary>Risoluzione dei problemi</summary>
    <ul>
    <li>Se il comando non viene riconosciuto, assicurarsi di avere installato l'interfaccia della riga di comando di <code>az</code> Azure come descritto in Preparare <a href="#1-prepare-your-environment">l'ambiente</a>.</li>
    <li>Sostituire con un nome univoco in tutto <code>&lt;app-name&gt;</code> Azure ( i caratteri validi sono , e <em> <code>a-z</code> <code>0-9</code> <code>-</code> </em> ). Un criterio valido consiste nell'usare una combinazione del nome della società e di un identificatore dell'app.</li>
    <li>Con l'argomento <code>--sku F1</code> l'app Web viene creata nel piano tariffario Gratuito. Omettere questo argomento per usare un livello Premium più rapido, che però comporta un costo orario.</li>
    <li>Facoltativamente, è possibile includere l'argomento <code>--location &lt;location-name&gt;</code>, dove <code>&lt;location-name&gt;</code> è un'area di Azure disponibile. È possibile recuperare un elenco di aree consentite per l'account Azure eseguendo il <a href="/cli/azure/appservice#az_appservice_list_locations"> <code>az account list-locations</code> </a> comando .</li>
    </ul>
    </details>
    
1. Attendere il completamento del comando, L'operazione potrebbe richiedere alcuni minuti e termina con "È possibile avviare l'app all'indirizzo http:// &lt; app-name &gt; .azurewebsites.net".

    <details>
    <summary>Che cosa <code>az webapp up</code> sta facendo?</summary>
    <p>Il comando <code>az webapp up</code> esegue le azioni seguenti:</p>
    <ul>
    <li>Crea un gruppo di risorse predefinito.</li>
    <li>Creare un piano di servizio app predefinito.</li>
    <li><a href="/cli/azure/webapp#az_webapp_create">Creare un'app del servizio app</a> con il nome specificato.</li>
    <li><a href="/azure/app-service/deploy-zip">Distribuire file ZIP</a> i dalla directory di lavoro corrente all'app.</li>
    <li>Durante l'esecuzione, fornisce messaggi relativi alla creazione, alla registrazione e alla distribuzione ZIP delle risorse.</li>
    </ul>
    </details>
    
# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

![Output di esempio del comando az webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-3.1.png)

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

![Output di esempio del comando az webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-5.0.png)

---

[Problemi? Segnalarli](https://aka.ms/DotNetAppServiceLinuxQuickStart).

<hr/> 

## <a name="6-browse-to-the-app"></a>6. Passare all'app

**Passare all'applicazione distribuita** usando il Web browser.

```bash
http://<app_name>.azurewebsites.net
```

![App di esempio in esecuzione in Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

[Problemi? Segnalarli](https://aka.ms/DotNetAppServiceLinuxQuickStart).

<hr/> 

## <a name="7-update-and-redeploy-the-code&quot;></a>7. Aggiornare e ridistribuire il codice

1. **Aprire il file _Startup.cs_** nella directory locale. 

1. **Apportare una piccola modifica** al testo nella chiamata al metodo `context.Response.WriteAsync` .

    ```csharp
    await context.Response.WriteAsync(&quot;Hello Azure!");
    ```
    
1. **Salvare le modifiche**.

1. **Eseguire** `az webapp up` per ridistribuire:

    ```azurecli
    az webapp up --os-type linux
    ```
    
    <details>
    <summary>Che cosa sta <code>az webapp up</code> facendo questa volta?</summary>
    La prima volta che è stato eseguito il comando, il nome dell'app, il gruppo di risorse e il piano di servizio app sono stati salvati nel file con estensione <i>azure/config</i> dalla radice del progetto. Quando viene eseguito di nuovo dalla radice del progetto, usa i valori salvati in <i>.azure/config,</i>rileva che le risorse del servizio app esistono già ed esegue nuovamente la distribuzione ZIP.
    </details>
    
1. Al termine della distribuzione, **fare clic su Aggiorna** nella finestra del browser aperta in precedenza.

    ![App di esempio aggiornata in esecuzione in Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)
    
[Problemi? Segnalarli](https://aka.ms/DotNetAppServiceLinuxQuickStart).

<hr/> 

## <a name="8-manage-your-new-azure-app"></a>8. Gestire la nuova app di Azure

1. Accedere al <a href="https://portal.azure.com" target="_blank">portale di Azure</a>.

1. Nel menu a sinistra fare clic su **Servizi app** e quindi sul nome dell'app Azure.

    :::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list-up.png" alt-text="Screenshot della pagina Servizi app con un'app Azure di esempio selezionata.":::

1. Nella pagina Panoramica è possibile eseguire attività di gestione di base, ad esempio sfogliare, arrestare, avviare, riavviare ed eliminare. Il menu a sinistra fornisce varie pagine per la configurazione dell'app. 

    ![Pagina del servizio app nel portale di Azure](media/quickstart-dotnetcore/portal-app-overview-up.png)
    
<hr/> 

## <a name="9-clean-up-resources"></a>9. Pulire le risorse

**Eseguire** `az group delete --name myResourceGroup` per eliminare il gruppo di risorse.

```azurecli-interactive
az group delete --name myResourceGroup
```

[Problemi? Segnalarli](https://aka.ms/DotNetAppServiceLinuxQuickStart).

<hr/> 

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: App ASP.NET Core con database SQL](tutorial-dotnetcore-sqldb-app.md)
- [Configurare l'app ASP.NET Core](configure-language-dotnetcore.md)

::: zone-end
