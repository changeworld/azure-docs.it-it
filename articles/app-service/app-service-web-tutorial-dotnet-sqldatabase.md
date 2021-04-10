---
title: 'Esercitazione: App ASP.NET con database SQL di Azure'
description: Informazioni su come distribuire un'app ASP.NET C# in Azure e nel database SQL di Azure
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.devlang: csharp
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: 533bd817b704db9976624b356a9950a9c48b8339
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606029"
---
# <a name="tutorial-deploy-an-aspnet-app-to-azure-with-azure-sql-database"></a>Esercitazione: Distribuire un'app ASP.NET in Azure con il database SQL di Azure

[Servizio app di Azure](overview.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione. Questa esercitazione illustra come distribuire un'app ASP.NET basata sui dati nel servizio app e connetterla al [database SQL di Azure](../azure-sql/database/sql-database-paas-overview.md). Al termine sarà disponibile un'app ASP.NET in esecuzione in Azure e connessa al database SQL.

![Applicazione ASP.NET pubblicata in Servizio app di Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Creare un database nel database SQL di Azure
> * Connettere un'app ASP.NET al database SQL
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Eseguire lo streaming dei log da Azure al terminale
> * Gestire l'app nel portale di Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

Installare <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> con il carico di lavoro **Sviluppo ASP.NET e Web**.

Se Visual Studio è già stato installato, aggiungere i carichi di lavoro in Visual Studio facendo clic su **Strumenti** > **Ottieni strumenti e funzionalità**.

## <a name="download-the-sample"></a>Scaricare l'esempio

1. [Scaricare il progetto di esempio](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip).

1. Estrarre (decomprimere) il file *dotnet-sqldb-tutorial-master.zip*.

Il progetto di esempio contiene un'app CRUD (create-read-update-delete) [ASP.NET MVC](https://www.asp.net/mvc) di base che usa [Code First di Entity Framework](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

### <a name="run-the-app"></a>Eseguire l'app

1. Aprire il file *dotnet-sqldb-tutorial-master/DotNetAppSqlDb.sln* in Visual Studio.

1. Digitare `Ctrl+F5` per eseguire l'app senza debug. L'app viene visualizzata nel browser predefinito.

1. Selezionare il collegamento **Crea nuovo** e creare due elementi *Attività*.

    ![Finestra di dialogo Nuovo progetto ASP.NET](media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

1. Testare i collegamenti **Modifica**, **Dettagli** ed **Elimina**.

L'app usa un contesto di database per connettersi al database. Nell'esempio il contesto di database usa una stringa di connessione denominata `MyDbConnection`. La stringa di connessione è impostata nel file *Web.config* e il file *Models\MyDatabaseContext.cs* include un riferimento alla stringa. Il nome della stringa di connessione viene usato più avanti nell'esercitazione per connettere l'app Azure a un database SQL di Azure.

## <a name="publish-aspnet-application-to-azure"></a>Pubblicare un'applicazione ASP.NET in Azure

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **DotNetAppSqlDb** e selezionare **Pubblica**.

    ![Pubblicare da Esplora soluzioni](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

1. Selezionare **Azure** come destinazione e fare clic su **Avanti**.

1. Verificare che **app Azure servizio (Windows)** sia selezionato e fare clic su **Avanti**.

#### <a name="sign-in-and-add-an-app"></a>Accedere e aggiungere un'app

1. Nella finestra di dialogo **pubblica** fare clic su **Aggiungi un account** dall'elenco a discesa Account Manager.

1. Accedere alla sottoscrizione di Azure. Se si è già connessi a un account Microsoft, verificare che l'account contenga la sottoscrizione di Azure. Se l'account Microsoft a cui si è connessi non include la sottoscrizione di Azure, fare clic su di esso per aggiungere l'account corretto.

1. Nel riquadro **istanze del servizio app** fare clic su **+** .

    ![Accedere ad Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

#### <a name="configure-the-web-app-name"></a>Configurare il nome dell'app Web

È possibile mantenere il nome dell'app Web generato o modificarlo in un altro nome univoco (i caratteri validi sono `a-z`, `0-9` e `-`). Il nome dell'app Web viene usato come parte dell'URL predefinito per l'app (`<app_name>.azurewebsites.net`, dove `<app_name>` è il nome dell'app Web). Il nome dell'app Web deve essere univoco in tutte le app di Azure.

> [!NOTE]
> Non selezionare **Crea** ancora.

![Finestra di dialogo Crea servizio app](media/app-service-web-tutorial-dotnet-sqldatabase/wan.png)

#### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [resource-group](../../includes/resource-group.md)]

1. Accanto a **Gruppo di risorse** fare clic su **Nuovo**.

   ![Accanto a Gruppo di risorse fare clic su Nuovo.](media/app-service-web-tutorial-dotnet-sqldatabase/new_rg2.png)

1. Assegnare al gruppo di risorse il nome **myResourceGroup**.

#### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Accanto a **piano di hosting** fare clic su **nuovo**.

1. Nella finestra di dialogo **Configura piano di servizio app** configurare il nuovo piano di servizio app con le impostazioni seguenti e fare clic su **OK**:

   | Impostazione  | Valore consigliato | Per ulteriori informazioni |
   | ----------------- | ------------ | ----|
   |**Piano di servizio app**| myAppServicePlan | [Piani del servizio app](../app-service/overview-hosting-plans.md) |
   |**Posizione**| Europa occidentale | [Aree di Azure](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |
   |**Dimensione**| Gratuito | [Piani tariffari](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)|

   ![Creare un piano di servizio app](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

1. Fare clic su **Crea** e attendere la creazione delle risorse di Azure.

1. La finestra di dialogo **Pubblica** mostra le risorse configurate. Fare clic su **Fine**.

   ![Risorse create](media/app-service-web-tutorial-dotnet-sqldatabase/app_svc_plan_done.png)


#### <a name="create-a-server-and-database"></a>Creare un server e un database

Prima di creare un database è necessario un [server SQL logico](../azure-sql/database/logical-servers.md). Un server SQL logico è un costrutto logico che contiene un gruppo di database gestiti come gruppo.

1. Nella finestra di dialogo **pubblica** scorrere verso il basso fino alla sezione **dipendenze del servizio** . Accanto a **SQL Server database** fare clic su **Configura**.

   ![Configurare la dipendenza del database SQL](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sqldb-dependency.png)

1. Selezionare **database SQL di Azure** e fare clic su **Avanti**.

1. Nella finestra di dialogo **Configura database SQL di Azure** fare clic su **+** .

1. Accanto a **server database** fare clic su **nuovo**.

   Viene generato un nome di server. Questo nome viene usato come parte dell'URL predefinito per il server `<server_name>.database.windows.net`. Deve essere univoco in tutti i server di Azure SQL. Sebbene sia possibile modificare il nome del server, per questa esercitazione mantenere il valore generato.

1. Aggiungere un nome utente e una password di amministratore. Per i requisiti di complessità delle password, vedere [Criteri password](/sql/relational-databases/security/password-policy).

   Prendere nota del nome utente e della password. Saranno necessari per gestire il server in un secondo momento.

   ![Creazione del server](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

   > [!IMPORTANT]
   > Anche se la password nelle stringhe di connessione è mascherata (sia in Visual Studio che nel servizio app), il fatto che venga mantenuta da qualche parte amplia la superficie di attacco dell'app. Il servizio app può usare [identità del servizio gestito](overview-managed-identity.md) per eliminare tale rischio rimuovendo del tutto l'esigenza di mantenere i segreti nel codice o nella configurazione dell'app. Per altre informazioni, vedere [Passaggi successivi](#next-steps).

1. Fare clic su **OK**.

1. Nella finestra di dialogo **database SQL di Azure** Mantieni il **nome del database** generato predefinito. Selezionare **Crea** e attendi la creazione delle risorse del database.

    ![Configurare il database](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

#### <a name="configure-database-connection"></a>Configurare la connessione al database

1. Al termine della creazione delle risorse del database da parte della procedura guidata, fare clic su **Avanti**.

1. Nel **nome della stringa di connessione al database** digitare _MyDbConnection_. Questo nome deve corrispondere alla stringa di connessione cui viene fatto riferimento in _Models\MyDatabaseContext.cs_.

1. In **nome utente connessione database** e **password connessione database** digitare il nome utente e la password dell'amministratore usati in [creare un server](#create-a-server-and-database).

1. Verificare che sia selezionata l'opzione **app Azure impostazioni** , quindi fare clic su **fine**.

    ![Configurare la stringa di connessione del database](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-connection.png)

1. Attendere il completamento della configurazione guidata, quindi fare clic su **Chiudi**.

#### <a name="deploy-your-aspnet-app"></a>Distribuire l'app ASP.NET

1. Nella scheda **pubblica** scorrere verso l'alto e fare clic su **pubblica**. Una volta distribuita l'app ASP.NET in Azure. Il browser predefinito viene avviato con l'URL dell'app distribuita.

1. Aggiungere alcune attività.

    ![Applicazione ASP.NET pubblicata nell'app Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

    Congratulazioni! L'applicazione ASP.NET basata sui dati è in esecuzione nel Servizio app di Azure.

## <a name="access-the-database-locally"></a>Accedere al database in locale

Visual Studio consente di esplorare e gestire facilmente il nuovo database in Azure in **Esplora oggetti di SQL Server**. Il nuovo database ha già aperto il firewall per l'app del servizio app creata, ma per accedervi dal computer locale, ad esempio da Visual Studio, è necessario aprire un firewall per l'indirizzo IP pubblico del computer locale. Se il provider di servizi Internet modifica l'indirizzo IP pubblico, è necessario riconfigurare il firewall per accedere nuovamente al database di Azure.

#### <a name="create-a-database-connection"></a>Creare una connessione al database

1. Dal menu **Visualizza** selezionare **Esplora oggetti di SQL Server**.

1. Nella parte superiore di **Esplora oggetti di SQL Server** fare clic sul pulsante **Aggiungi istanza di SQL Server**.

#### <a name="configure-the-database-connection"></a>Configurare la connessione al database

1. Nella finestra di dialogo **Connetti** espandere il nodo **Azure**. Vengono visualizzate tutte le istanze del database SQL di Azure.

1. Selezionare il database creato in precedenza. La connessione creata in precedenza viene inserita automaticamente nella parte inferiore.

1. Digitare la password di amministratore di database creata in precedenza e fare clic su **Connetti**.

    ![Configurare la connessione al database da Visual Studio](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

#### <a name="allow-client-connection-from-your-computer"></a>Consentire la connessione client dal computer

Viene visualizzata la finestra di dialogo **Crea nuova regola del firewall**. Per impostazione predefinita, un server consente solo le connessioni ai relativi database dai servizi di Azure, ad esempio dall'app Azure. Per connettersi al database dall'esterno di Azure, creare una regola del firewall a livello di server. La regola del firewall autorizza l'indirizzo IP pubblico del computer locale.

Nella finestra di dialogo è già specificato l'indirizzo IP pubblico del computer.

1. Assicurarsi che l'opzione **Aggiungi IP client** sia selezionata e fare clic su **OK**.

    ![Creare la regola del firewall](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

    Dopo che Visual Studio ha completato la creazione dell'impostazione del firewall per l'istanza del database SQL, la connessione viene visualizzata in **Esplora oggetti di SQL Server**.

    In questa posizione è possibile eseguire le operazioni di database più comuni, ad esempio eseguire query, creare visualizzazioni e stored procedure e così via.

1. Espandere la connessione > **Database** >  **&lt;database >**  > **Tabelle**. Fare clic con il pulsante destro del mouse sulla tabella `Todoes` e selezionare **Visualizza dati**.

    ![Esplorare gli oggetti del database SQL](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>Aggiornare l'app con Migrazioni Code First

Per aggiornare il database e l'app in Azure è possibile usare gli strumenti noti di Visual Studio. In questo passaggio si usa Migrazioni Code First di Entity Framework per apportare una modifica allo schema del database e pubblicarlo in Azure.

Per altre informazioni sull'uso di Migrazioni Code First di Entity Framework, vedere [Getting Started with Entity Framework 6 Code First using MVC 5](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) (Introduzione a Code First di Entity Framework 6 con MVC 5).

#### <a name="update-your-data-model"></a>Aggiornare il modello di dati

Aprire _Models\Todo.cs_ nell'editor di codice. Aggiungere la proprietà seguente alla classe `ToDo`:

```csharp
public bool Done { get; set; }
```
    
#### <a name="run-code-first-migrations-locally"></a>Eseguire Migrazioni Code First in locale

Eseguire alcuni comandi per eseguire gli aggiornamenti del database locale.

1. Nel menu **Strumenti** fare clic su **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.

1. Nella finestra Console di Gestione pacchetti abilitare le migrazioni Code First:

    ```powershell
    Enable-Migrations
    ```
    
1. Aggiungere una migrazione:

    ```powershell
    Add-Migration AddProperty
    ```
    
1. Aggiornare il database locale:

    ```powershell
    Update-Database
    ```
    
1. Digitare `Ctrl+F5` per eseguire l'app. Testare i collegamenti Modifica, Dettagli e Crea.

Se l'applicazione viene caricata senza errori, l'esecuzione di Migrazioni Code First è stata completata. La pagina tuttavia può apparire sempre uguale perché la logica dell'applicazione non usa ancora la nuova proprietà.

#### <a name="use-the-new-property"></a>Usare la nuova proprietà

Apportare alcune modifiche al codice per usare la proprietà `Done`. Per motivi di semplicità in questa esercitazione vengono modificate solo le visualizzazioni `Index` e `Create` per visualizzare la proprietà.

1. Aprire _Controllers\TodosController.cs_.

1. Trovare il metodo `Create()` alla riga 52 e aggiungere `Done` all'elenco delle proprietà nell'attributo `Bind`. Al termine, la firma del metodo `Create()` è simile al codice seguente:

    ```csharp
    public ActionResult Create([Bind(Include = "Description,CreatedDate,Done")] Todo todo)
    ```
    
1. Aprire _Views\Todos\Create.cshtml_.

1. Nel codice Razor viene visualizzato un elemento `<div class="form-group">` che usa `model.Description` e quindi un altro elemento `<div class="form-group">` che usa `model.CreatedDate`. Immediatamente dopo questi due elementi, aggiungere un altro elemento `<div class="form-group">` che usa `model.Done`:

    ```csharp
    <div class="form-group">
        @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
        <div class="col-md-10">
            <div class="checkbox">
                @Html.EditorFor(model => model.Done)
                @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
            </div>
        </div>
    </div>
    ```
    
1. Aprire _Views\Todos\Index.cshtml_.

1. cercare l'elemento `<th></th>` vuoto. Immediatamente sopra l'elemento, aggiungere il codice Razor seguente:

    ```csharp
    <th>
        @Html.DisplayNameFor(model => model.Done)
    </th>
    ```
    
1. Trovare l'elemento `<td>` che contiene i metodi helper `Html.ActionLink()`. _Sopra_ questo `<td>`, aggiungere un altro elemento `<td>` con il codice Razor seguente:

    ```csharp
    <td>
        @Html.DisplayFor(modelItem => item.Done)
    </td>
    ```
    
    Le modifiche verranno visualizzate nelle visualizzazioni `Index` e `Create`.

1. Digitare `Ctrl+F5` per eseguire l'app.

È ora possibile aggiungere un'attività e selezionare **Fine**. L'attività viene visualizzata come completata nella home page. Tenere presente che la visualizzazione `Edit` non mostra il campo `Done` poiché non è stata modificata la visualizzazione `Edit`.

#### <a name="enable-code-first-migrations-in-azure"></a>Abilitare Migrazioni Code First in Azure

Dopo aver completato la modifica al codice, inclusa la migrazione del database, è possibile eseguire la pubblicazione nell'app Azure e aggiornare anche il database SQL con Migrazioni Code First.

1. Fare di nuovo clic con il pulsante destro del mouse sul progetto e selezionare **Pubblica**.

1. Fare clic su **altre azioni**  >  **modifica** per aprire le impostazioni di pubblicazione.

    ![Aprire le impostazioni di pubblicazione](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

1. Nell'elenco a discesa **models\mydatabasecontext.cs** selezionare la connessione al database per il database SQL di Azure.

1. Selezionare **Esegui migrazione primo codice (inizia all'avvio dell'applicazione)** , quindi fare clic su **Salva**.

    ![Abilitare Migrazioni Code First nell'app Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

#### <a name="publish-your-changes"></a>Pubblicare le modifiche

Dopo aver abilitato Migrazioni Code First nell'app Azure pubblicare le modifiche al codice.

1. Nella pagina di pubblicazione fare clic su **Pubblica**.

1. Provare di nuovo ad aggiungere attività e selezionare **Fine**. Le attività verranno visualizzate nella home page come completate.

    ![App Azure dopo la migrazione Code First](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Tutte le attività esistenti rimangono visualizzate. Quando si pubblica nuovamente l'applicazione ASP.NET, i dati esistenti nel database SQL non vengono persi. Inoltre, solo Migrazioni Code First modifica lo schema dei dati lasciando intatti i dati esistenti.

## <a name="stream-application-logs"></a>Eseguire lo streaming dei log delle applicazioni

È possibile eseguire lo streaming dei messaggi di traccia direttamente dall'app Azure in Visual Studio.

Aprire _Controllers\TodosController.cs_.

Ogni azione inizia con un metodo `Trace.WriteLine()`. Questo codice viene aggiunto per illustrare come aggiungere messaggi di traccia all'app Azure.

#### <a name="enable-log-streaming"></a>Abilitare lo streaming dei log

1. Scegliere **Cloud Explorer** dal menu **Visualizza** .

1. In **Cloud Explorer** espandere la sottoscrizione di Azure con l'app ed espandere **servizio app**.

1. Fare clic con il pulsante destro del mouse sull'app Azure e scegliere **Visualizza log in streaming**.

    ![Abilitare lo streaming dei log](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

    Lo streaming dei log viene eseguito nella finestra **Output**.

    ![Streaming dei log nella finestra Output](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

    I messaggi di traccia tuttavia non sono ancora visibili. Ciò avviene perché la prima volta che si seleziona **Visualizza log in streaming** l'app Azure imposta il livello di traccia su `Error` che comporta soltanto la registrazione degli eventi di errore (con il metodo `Trace.TraceError()`).

#### <a name="change-trace-levels"></a>Modificare i livelli di traccia

1. Per modificare i livelli di traccia per l'output di altri messaggi di traccia, tornare a **Cloud Explorer**.

1. Fare di nuovo clic con il pulsante destro del mouse sull'app e scegliere **Apri nel portale**.

1. Nella pagina di gestione del portale per l'app scegliere **log del servizio app** dal menu a sinistra.

1. In **registrazione applicazioni (file System)** selezionare **dettagliato** in **livello**. Fare clic su **Salva**.

    ![Modificare il livello di traccia in Dettagli](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

    > [!TIP]
    > È possibile provare i diversi livelli di traccia per verificare i tipi di messaggi visualizzati per ogni livello. Ad esempio, il livello **Informazioni** include tutti i log creati da `Trace.TraceInformation()`, `Trace.TraceWarning()` e `Trace.TraceError()`, ma non i log creati da `Trace.WriteLine()`.

1. Nel browser passare di nuovo all'app in *http://&lt;nome dell'app>.azurewebsites.net* e quindi provare a fare clic nell'applicazione di elenco attività in Azure. Viene eseguito lo streaming dei messaggi di traccia nella finestra **Output** in Visual Studio.

    ```console
    Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
    Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
    Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
    Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
    ```
    
#### <a name="stop-log-streaming"></a>Arrestare lo streaming dei log

Per arrestare il servizio di streaming dei log, fare clic sul pulsante **Interrompi monitoraggio** nella finestra **Output**.

![Arrestare lo streaming dei log](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-app"></a>Gestire l'app Azure

Accedere al [portale di Azure](https://portal.azure.com) per visualizzare l'app Web. Cercare e selezionare **Servizi app**.

![Ricerca di Servizi app di Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-portal-navigate-app-services.png)

Selezionare il nome dell'app di Azure.

![Passaggio all'app di Azure nel portale](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

Viene visualizzata la pagina dell'app.

Per impostazione predefinita, il portale visualizza la pagina **Panoramica**. che offre una visualizzazione dello stato dell'app. In questa pagina è anche possibile eseguire attività di gestione di base come esplorare, arrestare, avviare, riavviare ed eliminare. Le schede sul lato sinistro della pagina mostrano le diverse pagine di configurazione che è possibile aprire.

![Pagina del servizio app nel portale di Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

[!INCLUDE [Clean up section](../../includes/clean-up-section-portal-web-app.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
>
> * Creare un database nel database SQL di Azure
> * Connettere un'app ASP.NET al database SQL
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Eseguire lo streaming dei log da Azure al terminale
> * Gestire l'app nel portale di Azure

Passare all'esercitazione successiva per apprendere come migliorare facilmente la sicurezza della connessione al database SQL di Azure.

> [!div class="nextstepaction"]
> [Accedere al database SQL in modo sicuro con identità gestite per risorse di Azure](app-service-web-tutorial-connect-msi.md)

Altre risorse:

> [!div class="nextstepaction"]
> [Configurare l'app ASP.NET](configure-language-dotnet-framework.md)

Si vuole ottimizzare e risparmiare sulla spesa per il cloud?

> [!div class="nextstepaction"]
> [Per iniziare, analizzare i costi con Gestione costi](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)