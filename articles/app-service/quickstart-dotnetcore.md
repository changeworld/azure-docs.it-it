---
title: "Guida introduttiva: distribuire un'app Web ASP.NET"
description: Informazioni su come eseguire app Web nel servizio app Azure distribuendo la prima app ASP.NET.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 03/30/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-ide
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 7f538f5accb533b01c5ea685e424c70bfeb44f00
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058252"
---
<!-- NOTES:

I'm a .NET developer who wants to deploy my web app to App Service. I may develop apps with
Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET SDK/CLI. This article
should be able to guide .NET devs, whether they're app is .NET Core, .NET, or .NET Framework.

As a .NET developer, when choosing an IDE and .NET TFM - you map to various OS requirements.
For example, if you choose Visual Studio - you're developing the app on Windows, but you can still
target cross-platform with .NET Core 3.1 or .NET 5.0.

| .NET / IDE         | Visual Studio | Visual Studio for Mac | Visual Studio Code | Command line   |
|--------------------|---------------|-----------------------|--------------------|----------------|
| .NET Core 3.1      | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET 5.0           | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET Framework 4.8 | Windows       | N/A                   | Windows            | Windows        |

-->

# <a name="quickstart-deploy-an-aspnet-web-app"></a>Guida introduttiva: distribuire un'app Web ASP.NET

In questa Guida introduttiva si apprenderà come creare e distribuire la prima app Web ASP.NET nel [servizio app Azure](overview.md). Il servizio app supporta varie versioni delle app .NET e fornisce un servizio di hosting Web ad alta scalabilità e con funzionalità di correzione automatica. Le app Web ASP.NET sono multipiattaforma e possono essere ospitate in Linux o Windows. Al termine, si avrà un gruppo di risorse di Azure costituito da un piano di hosting del servizio app e un servizio app con un'applicazione Web distribuita.

> [!TIP]
> .NET Core 3,1 è la versione corrente del supporto a lungo termine (LTS) di .NET. Per ulteriori informazioni, vedere [criteri di supporto .NET](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

## <a name="prerequisites"></a>Prerequisiti

:::zone target="docs" pivot="development-environment-vs"

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/dotnet).
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2019</a> con il carico di lavoro **Sviluppo ASP.NET e Web**.

    Se è già stato installato Visual Studio 2019:

    - Installare gli aggiornamenti più recenti in Visual Studio selezionando **?**  > **Controlla aggiornamenti**.
    - Aggiungere il carico di lavoro selezionando **Strumenti** > **Ottieni strumenti e funzionalità**.

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/dotnet).
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio Code</a>.
- Estensione <a href="https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack" target="_blank">degli strumenti di Azure</a> .

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Installare la versione più recente di .NET Core 3,1 SDK. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Installare la versione più recente di .NET 5,0 SDK. </a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> Installare la .NET Framework 4,8 Developer Pack. </a>

> [!NOTE]
> Visual Studio Code è multipiattaforma, tuttavia, .NET Framework non lo è. Se si sviluppano app .NET Framework con Visual Studio Code, provare a usare un computer Windows per soddisfare le dipendenze di compilazione.

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/dotnet).
- <a href="/cli/azure/install-azure-cli" target="_blank">Interfaccia della riga di comando di Azure</a>.
- .NET SDK (include Runtime e CLI).

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Installare la versione più recente di .NET Core 3,1 SDK. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Installare la versione più recente di .NET 5,0 SDK. </a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Installare la versione più recente di .net 5,0 SDK. </a> e <a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> il .NET Framework 4,8 Developer Pack. </a>

> [!NOTE]
> Tuttavia, l'interfaccia della riga di comando di [.NET](/dotnet/core/tools) è multipiattaforma, ma non .NET Framework. Se si sviluppano app .NET Framework con l'interfaccia della riga di comando di .NET, è consigliabile usare un computer Windows per soddisfare le dipendenze di compilazione.

---

:::zone-end

## <a name="create-an-aspnet-web-app"></a>Creare un'app Web ASP.NET

:::zone target="docs" pivot="development-environment-vs"

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

1. Aprire Visual Studio e selezionare **Crea un nuovo progetto**.
1. In **creare un nuovo progetto**, trovare e scegliere **ASP.NET Web Core app**, quindi selezionare **Avanti**.
1. In **Configura il nuovo progetto**, denominare l'applicazione _MyFirstAzureWebApp_, quindi fare clic su **Avanti**.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Configurare ASP.NET Core app Web 3,1" border="true":::

1. Selezionare **.NET Core 3,1 (supporto a lungo termine)**.
1. Verificare che **tipo di autenticazione** sia impostato su **nessuno**. Selezionare **Crea**.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-netcoreapp31.png" alt-text="Visual Studio: selezionare .NET Core 3,1 e nessuno per tipo di autenticazione." border="true":::

1. Nel menu di Visual Studio selezionare **Debug** > **Avvia senza eseguire debug** per eseguire l'app Web in locale.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio-.NET Core 3,1 esplorare localmente" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

1. Aprire Visual Studio e selezionare **Crea un nuovo progetto**.
1. In **creare un nuovo progetto**, trovare e scegliere **ASP.NET Web Core app**, quindi selezionare **Avanti**.
1. In **Configura il nuovo progetto**, denominare l'applicazione _MyFirstAzureWebApp_, quindi fare clic su **Avanti**.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Visual Studio: configurare l'app Web ASP.NET 5,0." border="true":::

1. Selezionare **.NET Core 5,0 (corrente)**.
1. Verificare che **tipo di autenticazione** sia impostato su **nessuno**. Selezionare **Crea**.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-net50.png" alt-text="Visual Studio-informazioni aggiuntive quando si seleziona .NET Core 5,0." border="true":::

1. Nel menu di Visual Studio selezionare **Debug** > **Avvia senza eseguire debug** per eseguire l'app Web in locale.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio-ASP.NET Core 5,0 in esecuzione in locale." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

1. Aprire Visual Studio e selezionare **Crea un nuovo progetto**.
1. In **Crea un nuovo progetto** trovare e scegliere **ASP.NET Web Application (.NET Framework)**, quindi fare clic su **Next (avanti**).
1. In **Configura il nuovo progetto**, denominare l'applicazione _MyFirstAzureWebApp_, quindi selezionare **Crea**.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-netframework48.png" alt-text="Visual Studio: configurare l'app Web ASP.NET Framework 4,8." border="true":::

1. Selezionare il modello **MVC** .
1. Assicurarsi che **l'autenticazione** sia impostata su **Nessuna autenticazione**. Selezionare **Crea**.

   :::image type="content" source="media/quickstart-dotnet/vs-mvc-no-auth-netframework48.png" alt-text="Visual Studio: selezionare il modello MVC." border="true":::

1. Nel menu di Visual Studio selezionare **Debug** > **Avvia senza eseguire debug** per eseguire l'app Web in locale.

   :::image type="content" source="media/quickstart-dotnet/vs-local-webapp-netframework48.png" alt-text="Visual Studio-ASP.NET Framework 4,8 in esecuzione localmente." lightbox="media/quickstart-dotnet/vs-local-webapp-netframework48.png" border="true":::

---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Creare una nuova cartella denominata _MyFirstAzureWebApp_ e aprirla in Visual Studio Code. Aprire la finestra del <a href="https://code.visualstudio.com/docs/editor/integrated-terminal" target="_blank">terminale</a> e creare una nuova app Web .NET usando il [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) comando.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -f netcoreapp3.1
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -f net5.0
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp --target-framework-override net48
```

> [!IMPORTANT]
> Il `--target-framework-override` flag è una sostituzione di testo in formato libero del moniker del Framework di destinazione (TFM) per il progetto e *non garantisce* che il modello di supporto esista o venga compilato. È possibile compilare ed eseguire .NET Framework app solo in Windows.

---

Dal **terminale** in Visual Studio Code eseguire localmente l'applicazione con il [`dotnet run`](/dotnet/core/tools/dotnet-run) comando.

```dotnetcli
dotnet run
```

Aprire un Web browser e passare all'app all'indirizzo `https://localhost:5001`.


### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Si noterà che il modello ASP.NET Core app Web 3,1 visualizzata nella pagina.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code eseguire .NET Core 3,1 nel browser localmente." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Si noterà che il modello ASP.NET Core app Web 5,0 visualizzata nella pagina.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Eseguire Visual Studio Code .NET 5,0 nel browser in locale." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Verrà visualizzato il modello ASP.NET Framework 4,8 app Web visualizzato nella pagina.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Eseguire Visual Studio Code .NET 4,8 nel browser in locale." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Aprire una finestra del terminale nel computer in una directory di lavoro. Creare una nuova app Web .NET usando il [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) comando e quindi modificare le directory nell'app appena creata.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f netcoreapp3.1 && cd MyFirstAzureWebApp
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f net5.0 && cd MyFirstAzureWebApp
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp --target-framework-override net48 && cd MyFirstAzureWebApp
```

> [!IMPORTANT]
> Il `--target-framework-override` flag è una sostituzione di testo in formato libero del moniker del Framework di destinazione (TFM) per il progetto e *non garantisce* che il modello di supporto esista o venga compilato. È possibile creare app .NET Framework solo in Windows.

---

Dalla stessa sessione terminal eseguire l'applicazione localmente usando il [`dotnet run`](/dotnet/core/tools/dotnet-run) comando.

```dotnetcli
dotnet run
```

Aprire un Web browser e passare all'app all'indirizzo `https://localhost:5001`.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Si noterà che il modello ASP.NET Core app Web 3,1 visualizzata nella pagina.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code ASP.NET Core 3,1 nel browser locale." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Si noterà che il modello ASP.NET Core app Web 5,0 visualizzata nella pagina.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code ASP.NET Core 5,0 nel browser locale." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Verrà visualizzato il modello ASP.NET Framework 4,8 app Web visualizzato nella pagina.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code-ASP.NET Framework 4,8 nel browser locale." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

## <a name="publish-your-web-app"></a>Pubblicare l'app Web

Per pubblicare l'app Web, è necessario prima creare e configurare un nuovo servizio app in cui pubblicarla.

Come parte della configurazione del servizio app, si creeranno:

- Un nuovo [gruppo di risorse](../azure-resource-manager/management/overview.md#terminology) che conterrà tutte le risorse di Azure per il servizio.
- Un nuovo [piano di hosting](overview-hosting-plans.md) che specifica la località, le dimensioni e le funzionalità della server farm Web che ospita l'app.

Seguire questa procedura per creare il servizio app e pubblicare l'app Web:

:::zone target="docs" pivot="development-environment-vs"

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **MyFirstAzureWebApp** e scegliere **pubblica**.
1. In **pubblica** selezionare **Azure** e quindi **Avanti**.

    :::image type="content" source="media/quickstart-dotnet/vs-publish-target-Azure.png" alt-text="Visual Studio: pubblicare l'app Web e Azure di destinazione." border="true":::

1. Le opzioni disponibili variano a seconda che sia già stato eseguito l'accesso ad Azure e che si abbia un account di Visual Studio collegato a un account di Azure. Selezionare **Aggiungi un account** o **Accedi** per accedere alla sottoscrizione di Azure. Se è già stato effettuato l'accesso, selezionare l'account da usare.

    :::image type="content" source="media/quickstart-dotnetcore/sign-in-Azure-vs2019.png" border="true" alt-text="Visual Studio: selezionare Accedi a finestra di dialogo di Azure.":::

1. Scegliere la **destinazione specifica**, ovvero **servizio app Azure (Linux)** o **servizio di app Azure (Windows)**.

    > [!IMPORTANT]
    > Quando la destinazione è ASP.NET Framework 4,8, si userà **app Azure Service (Windows)**.

1. A destra delle **istanze del servizio app** selezionare **+** .

    :::image type="content" source="media/quickstart-dotnetcore/publish-new-app-service.png" border="true" alt-text="Visual Studio-finestra di dialogo nuova app del servizio app.":::

1. Per **Sottoscrizione**, accettare la sottoscrizione elencata o sceglierne una nuova nell'elenco a discesa.
1. Per **Gruppo di risorse** selezionare **Nuovo**. In **Nome nuovo gruppo di risorse** immettere *myResourceGroup* e scegliere **OK**.
1. Per **Piano di hosting** selezionare **Nuovo**.
1. Nella finestra di dialogo **Piano di hosting: Crea nuovo** immettere i valori specificati nella tabella seguente:

    | Impostazione          | Valore consigliato          | Descrizione                                                           |
    |------------------|--------------------------|-----------------------------------------------------------------------|
    | **Piano di hosting** | *MyFirstAzureWebAppPlan* | Nome del piano di servizio app.                                         |
    | **Posizione**     | *Europa occidentale*            | Data center in cui è ospitata l'app Web.                           |
    | **Dimensione**         | *Free*                   | [Piano tariffario][app-service-pricing-tier] che determina le funzionalità di hosting. |

    :::image type="content" source="media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png" border="true" alt-text="Creare un nuovo piano di hosting":::

1. In **Nome** immettere un nome univoco per l'app che includa solo i caratteri validi, ossia `a-z`, `A-Z`, `0-9` e `-`. È possibile accettare il nome univoco generato automaticamente. L'URL dell'app Web è `http://<app-name>.azurewebsites.net`, dove `<app-name>` è il nome dell'app.
1. Selezionare **Crea** per creare le risorse di Azure.

    :::image type="content" source="media/quickstart-dotnetcore/web-app-name-vs2019.png" border="true" alt-text="Visual Studio-finestra di dialogo Crea risorse dell'app.":::

   Al termine della procedura guidata, le risorse di Azure vengono create automaticamente ed è possibile procedere alla pubblicazione.

1. Selezionare **Fine** per chiudere la procedura guidata.
1. Nella pagina **pubblica** selezionare **pubblica**. Visual Studio compila, crea il pacchetto e pubblica l'app in Azure, quindi la avvia nel browser predefinito.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Verrà visualizzata l'app Web ASP.NET Core 3,1 visualizzata nella pagina.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio: app Web di ASP.NET Core 3,1 in Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Verrà visualizzata l'app Web ASP.NET Core 5,0 visualizzata nella pagina.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio: app Web di ASP.NET Core 5,0 in Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Verrà visualizzata l'app Web ASP.NET Framework 4,8 visualizzata nella pagina.

    :::image type="content" source="media/quickstart-dotnet/vs-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio: app Web ASP.NET Framework 4,8 in Azure.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Per distribuire l'app Web usando l'estensione strumenti di Azure per Visual Studio:

1. In Visual Studio Code aprire il [**riquadro comandi**](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette), <kbd>CTRL</kbd> + <kbd>MAIUSC</kbd> + <kbd>P</kbd>.
1. Cercare e selezionare "servizio app Azure: Distribuisci in app Web".
1. Rispondere alle richieste come segue:

    - Selezionare *MyFirstAzureWebApp* come cartella da distribuire.
    - Quando richiesto, selezionare **Aggiungi configurazione** .
    - Se richiesto, accedere al proprio account Azure esistente.

    :::image type="content" source="media/quickstart-dotnet/vscode-sign-in-to-Azure.png" alt-text="Visual Studio Code-accedere ad Azure." border="true":::

    - Selezionare la **sottoscrizione**.
    - Selezionare **Crea nuova app Web... Avanzate**.
    - Per **immettere un nome univoco globale**, usare un nome univoco in tutte le aree di Azure. i *caratteri validi sono `a-z` , `0-9` e `-`*. Un criterio valido consiste nell'usare una combinazione del nome della società e di un identificatore dell'app.
    - Selezionare **Crea nuovo gruppo di risorse** e specificare un nome, ad esempio `myResourceGroup`.
    - Quando viene richiesto di **selezionare uno stack di runtime**:
      - Per *.net core 3,1*, selezionare **.NET Core 3,1 (LTS)**
      - Per *.net 5,0*, selezionare **.NET 5**
      - Per *.NET Framework 4,8*, selezionare **ASP.NET v 4.8**
    - Selezionare un sistema operativo (Windows o Linux).
        - Per *.NET Framework 4,8*, Windows verrà selezionato in modo implicito.
    - Selezionare **Crea un nuovo piano di servizio app**, specificare un nome e selezionare il piano [tariffario][app-service-pricing-tier] **gratuito F1** .
    - Per la risorsa di Application Insight, selezionare **Ignora per adesso**.
    - Selezionare una località nelle vicinanze.

1. Al termine della pubblicazione, selezionare **Sfoglia sito Web** nella notifica e selezionare **Apri** quando richiesto.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Verrà visualizzata l'app Web ASP.NET Core 3,1 visualizzata nella pagina.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="App Web Visual Studio Code ASP.NET Core 3,1 in Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Verrà visualizzata l'app Web ASP.NET Core 5,0 visualizzata nella pagina.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="App Web Visual Studio Code ASP.NET Core 5,0 in Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Verrà visualizzata l'app Web ASP.NET Framework 4,8 visualizzata nella pagina.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="App Web Visual Studio Code ASP.NET Framework 4,8 in Azure.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Distribuire il codice nella directory *MyFirstAzureWebApp* locale usando il [`az webapp up`](/cli/azure/webapp#az_webapp_up) comando:

```azurecli
az webapp up --sku F1 --name <app-name> --os-type <os>
```

- Se il `az` comando non è riconosciuto, assicurarsi di avere installato l'interfaccia della riga di comando di Azure come descritto in [prerequisiti](#prerequisites).
- Sostituire `<app-name>` con un nome univoco nell'ambito di Azure (*i caratteri validi sono `a-z`, `0-9` e `-`* ). Un criterio valido consiste nell'usare una combinazione del nome della società e di un identificatore dell'app.
- L' `--sku F1` argomento crea l'app Web nel piano  [tariffario][app-service-pricing-tier]gratuito. Omettere questo argomento per usare un livello Premium più rapido, che però comporta un costo orario.
- Sostituire `<os>` con `linux` o `windows` . È necessario usare `windows` quando la destinazione è *ASP.NET Framework 4,8*.
- Facoltativamente, è possibile includere l'argomento `--location <location-name>`, dove `<location-name>` è un'area di Azure disponibile. Per recuperare un elenco di aree consentite per l'account Azure, è possibile eseguire il comando [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations).

Il completamento del comando può richiedere alcuni minuti. Durante l'esecuzione, fornisce messaggi sulla creazione del gruppo di risorse, il piano di servizio app e l'app di hosting, la configurazione della registrazione, quindi l'esecuzione della distribuzione ZIP. Viene quindi restituito un messaggio con l'URL dell'app:

```azurecli
You can launch the app at http://<app-name>.azurewebsites.net
```

Aprire un Web browser e passare all'URL:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Verrà visualizzata l'app Web ASP.NET Core 3,1 visualizzata nella pagina.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="INTERFACCIA della riga di comando-ASP.NET Core app Web 3,1 in Azure.":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Verrà visualizzata l'app Web ASP.NET Core 5,0 visualizzata nella pagina.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="INTERFACCIA della riga di comando-ASP.NET Core app Web 5,0 in Azure.":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Verrà visualizzata l'app Web ASP.NET Framework 4,8 visualizzata nella pagina.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/Azure-webapp-net48.png" border="true" alt-text="INTERFACCIA della riga di comando-ASP.NET Framework 4,8 app Web in Azure.":::

---

:::zone-end

## <a name="update-the-app-and-redeploy"></a>Aggiornare e ridistribuire l'app

Seguire questa procedura per aggiornare e ridistribuire l'app Web:

:::zone target="docs" pivot="development-environment-vs"

1. In **Esplora soluzioni**, nel progetto, aprire *index. cshtml*.
1. Sostituire il primo `<div>` elemento con il codice seguente:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Salvare le modifiche.

1. Per eseguire la ridistribuzione in Azure, fare clic con il pulsante destro del mouse sul progetto **MyFirstAzureWebApp** in **Esplora soluzioni** e scegliere **pubblica**.
1. Nella pagina di riepilogo **Pubblica** selezionare **Pubblica**.

    Al termine del processo di pubblicazione, Visual Studio avvia un browser sull'URL dell'app Web.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Verrà visualizzata l'app Web ASP.NET Core 3,1 aggiornata visualizzata nella pagina.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio: aggiornamento dell'app Web ASP.NET Core 3,1 in Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Verrà visualizzata l'app Web ASP.NET Core 5,0 aggiornata visualizzata nella pagina.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio: aggiornamento dell'app Web ASP.NET Core 5,0 in Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Verrà visualizzata l'app Web ASP.NET Framework 4,8 aggiornata visualizzata nella pagina.

    :::image type="content" source="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio: app Web ASP.NET Framework 4,8 aggiornata in Azure.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

1. Aprire *Index.cshtml*.
1. Sostituire il primo `<div>` elemento con il codice seguente:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Salvare le modifiche.

1. Aprire la **barra laterale** Visual Studio Code, selezionare l'icona di **Azure** per espanderne le opzioni.
1. Nel nodo **servizio app** espandere la sottoscrizione e fare clic con il pulsante destro del mouse su **MyFirstAzureWebApp**.
1. Selezionare **Distribuisci in app Web...**.
1. Quando richiesto, selezionare **Distribuisci** .
1. Al termine della pubblicazione, selezionare **Sfoglia sito Web** nella notifica e selezionare **Apri** quando richiesto.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Verrà visualizzata l'app Web ASP.NET Core 3,1 aggiornata visualizzata nella pagina.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code aggiornamento ASP.NET Core app Web 3,1 in Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Verrà visualizzata l'app Web ASP.NET Core 5,0 aggiornata visualizzata nella pagina.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code aggiornamento ASP.NET Core app Web 5,0 in Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Verrà visualizzata l'app Web ASP.NET Framework 4,8 aggiornata visualizzata nella pagina.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code l'app Web ASP.NET Framework 4,8 aggiornata in Azure.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Nella directory locale aprire il file *index. cshtml* . Sostituire il primo `<div>` elemento:

```razor
<div class="jumbotron">
    <h1>.NET 💜 Azure</h1>
    <p class="lead">Example .NET app to Azure App Service.</p>
</div>
```

Salvare le modifiche, quindi ridistribuire l'app usando di nuovo il comando `az webapp up`:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

ASP.NET Core 3,1 è multipiattaforma, a seconda della distribuzione precedente, sostituire `<os>` con `linux` o `windows` .

```azurecli
az webapp up --os-type <os>
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

ASP.NET Core 5,0 è multipiattaforma, a seconda della distribuzione precedente, sostituire `<os>` con `linux` o `windows` .

```azurecli
az webapp up --os-type <os>
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

ASP.NET Framework 4,8 presenta dipendenze del Framework e deve essere ospitato in Windows.

```azurecli
az webapp up --os-type windows
```

> [!TIP]
> Se si è interessati a ospitare le app .NET in Linux, provare a eseguire la migrazione da [ASP.NET Framework a ASP.NET Core](/aspnet/core/migration/proper-to-2x).

---

Questo comando usa i valori memorizzati nella cache in locale nel file *.azure/config*, inclusi il nome dell'app, il gruppo di risorse e il piano di servizio app.

Al termine della distribuzione, tornare alla finestra del browser aperta nel passaggio **Passare all'app** e fare clic su Aggiorna.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Verrà visualizzata l'app Web ASP.NET Core 3,1 aggiornata visualizzata nella pagina.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="INTERFACCIA della riga di comando: aggiornata ASP.NET Core app Web 3,1 in Azure.":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Verrà visualizzata l'app Web ASP.NET Core 5,0 aggiornata visualizzata nella pagina.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="INTERFACCIA della riga di comando: aggiornata ASP.NET Core app Web 5,0 in Azure.":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Verrà visualizzata l'app Web ASP.NET Framework 4,8 aggiornata visualizzata nella pagina.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="INTERFACCIA della riga di comando-aggiornata app Web ASP.NET Framework 4,8 in Azure.":::

---

:::zone-end

## <a name="manage-the-azure-app"></a>Gestire l'app Azure

Per gestire l'app Web, passare al [portale di Azure](https://portal.azure.com), quindi cercare e selezionare **Servizi app**.

:::image type="content" source="media/quickstart-dotnetcore/app-services.png" alt-text="Portale di Azure: selezionare l'opzione servizi app.":::

Nella pagina **Servizi app** selezionare il nome dell'app Web.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Portale di Azure: pagina dei servizi app con un'app Web di esempio selezionata.":::

La pagina **Panoramica** per l'app Web contiene le opzioni per la gestione di base, ad esempio Sfoglia, Arresta, Avvia, Riavvia ed Elimina. Il menu a sinistra include ulteriori pagine per la configurazione dell'app.

:::image type="content" source="media/quickstart-dotnetcore/web-app-overview-page.png" alt-text="Portale di Azure: pagina di panoramica del servizio app.":::

<!--
## Clean up resources - H2 added from the next three includes
-->
:::zone target="docs" pivot="development-environment-vs"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

:::zone target="docs" pivot="development-environment-vscode"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->
[!INCLUDE [Clean-up CLI resources](../../includes/cli-samples-clean-up.md)]
:::zone-end

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stata creata e distribuita un'app Web ASP.NET per app Azure servizio.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Procedere con l'articolo successivo per informazioni su come creare un'app .NET Core e connetterla a un database SQL:

> [!div class="nextstepaction"]
> [Esercitazione: ASP.NET Core app con il database SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Configurare ASP.NET Core app 3,1](configure-language-dotnetcore.md)

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Procedere con l'articolo successivo per informazioni su come creare un'app .NET Core e connetterla a un database SQL:

> [!div class="nextstepaction"]
> [Esercitazione: ASP.NET Core app con il database SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Configurare ASP.NET Core app 5,0](configure-language-dotnetcore.md)

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Passare all'articolo successivo per informazioni su come creare un'app .NET Framework e connetterla a un database SQL:

> [!div class="nextstepaction"]
> [Esercitazione: app ASP.NET con database SQL](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [Configurare l'app ASP.NET Framework](configure-language-dotnet-framework.md)

---

[app-service-pricing-tier]: https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
