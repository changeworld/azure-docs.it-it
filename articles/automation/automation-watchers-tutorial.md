---
title: Rilevare i file aggiornati con un'attività watcher di Automazione di Azure
description: Questo articolo illustra come creare un'attività watcher nell'account di Automazione di Azure per controllare i nuovi file creati in una cartella.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: ca4c4013e0044811a5bac8786996761b8a5c45f1
ms.sourcegitcommit: e0ec3c06206ebd79195d12009fd21349de4a995d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97682741"
---
# <a name="track-updated-files-with-a-watcher-task"></a>Rilevare i file aggiornati con un'attività watcher

Automazione di Azure usa le attività watcher per rilevare gli eventi e le azioni di trigger con runbook PowerShell. Le attività Watcher sono composte da due parti: watcher e azione. Un runbook watcher viene eseguito a un intervallo definito nell'attività watcher e restituisce i dati a un'azione runbook.

> [!NOTE]
> Le attività watcher non sono supportate in Azure China (21Vianet).

> [!IMPORTANT]
> A partire da maggio 2020, l'uso di app per la logica di Azure è il modo consigliato e supportato per monitorare gli eventi, pianificare le attività ricorrenti e attivare le azioni. Vedere [Pianificare ed eseguire attività, processi e flussi di lavoro automatizzati ricorrenti con le app per la logica di Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Questo articolo illustra la creazione di un'attività Watcher per monitorare quando un nuovo file viene aggiunto a una directory. Si apprenderà come:

* Importare un runbook watcher
* Creare una variabile di automazione
* Creare un runbook azione
* Creare un'attività watcher
* Attivare un watcher
* Esaminare i risultati

## <a name="prerequisites"></a>Prerequisiti

Per completare questo articolo, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di automazione](./index.yml) per contenere i runbook watcher e azione e l'attività watcher.
* Un [ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md) dove viene eseguita l'attività watcher.
* Runbook PowerShell. I runbook del flusso di lavoro PowerShell non sono supportati dalle attività watcher.

## <a name="import-a-watcher-runbook"></a>Importare un runbook watcher

Questo articolo usa un Runbook Watcher denominato **Watch-NewFile** per cercare nuovi file in una directory. Il runbook watcher recupera la data e l'ora dell'ultima scrittura nota nei file di una cartella e cerca qualsiasi file che risulti più recente di quel limite.

È possibile scaricare Runbook dall'organizzazione di [GitHub di automazione di Azure](https://github.com/azureautomation).

1. Passare alla pagina dell'organizzazione GitHub di automazione di Azure per [Watch-NewFile.ps1](https://github.com/azureautomation/watcher-action-that-processes-events-triggerd-by-a-watcher-runbook).
2. Per scaricare Runbook da GitHub, selezionare il **codice** dal lato destro della pagina, quindi selezionare **Scarica zip** per scaricare l'intero codice in un file zip.
3. Estrarre il contenuto e [importare il Runbook](manage-runbooks.md#import-a-runbook-from-the-azure-portal).

È anche possibile importare questo runbook nell'account di automazione dal portale seguendo questa procedura.

1. Aprire l'account di automazione e fare clic sulla pagina Runbook.
2. Fare clic su **Esplora raccolta** e nell'elenco a discesa **origine** Selezionare **GitHub**.
3. Cercare **Runbook watcher**, selezionare **Watcher runbook that looks for new files in a directory** (Runbook watcher che cerca nuovi file in una directory) e fare clic su **Importa**.
4. Assegnare al runbook un nome e una descrizione e fare clic su **OK** per importarlo nell'account di automazione.
5. Selezionare **Modifica** e quindi fare clic su **Pubblica**. Al prompt, selezionare **Sì** per pubblicare il runbook.

## <a name="create-an-automation-variable"></a>Creare una variabile di automazione

Una [variabile di automazione](./shared-resources/variables.md) viene usata per archiviare i timestamp che il Runbook precedente legge e archivia da ogni file.

1. Selezionare **Variabili** in **Risorse condivise** e fare clic su **+ Aggiungi variabile**.
1. Immettere **Watch-NewFileTimestamp** per il nome.
1. Selezionare DateTime in Tipo.
1. Fare clic su **Crea** per creare la variabile di automazione.

## <a name="create-an-action-runbook"></a>Creare un runbook azione

Un runbook azione viene usato in un'attività watcher per agire sui dati passati da un runbook watcher. È necessario importare un Runbook di azione predefinito denominato **Process-NewFile** dall' [organizzazione GitHub di automazione di Azure](https://github.com/azureautomation).

Per creare un runbook azione:

1. Passare alla pagina dell'organizzazione GitHub di automazione di Azure per [Process-NewFile.ps1](https://github.com/azureautomation/watcher-action-that-processes-events-triggerd-by-a-watcher-runbook).
2. Per scaricare Runbook da GitHub, selezionare il **codice** dal lato destro della pagina, quindi selezionare **Scarica zip** per scaricare l'intero codice in un file zip.
3. Estrarre il contenuto e [importare il Runbook](manage-runbooks.md#import-a-runbook-from-the-azure-portal).

È anche possibile importare questo runbook nell'account di automazione dal portale di Azure:

1. Passare all'account di automazione e selezionare **Runbook** sotto **Automazione processi**.
1. Fare clic su **Esplora raccolta** e nell'elenco a discesa **origine** Selezionare **GitHub**.
1. Cercare **Azione watcher** e selezionare **Watcher action that processes events triggered by a watcher runbook** (Azione watcher che elabora eventi attivati da un runbook watcher), quindi fare clic su **Importa**.
1. Assegnare al runbook un nome e una descrizione e fare clic su **OK** per importarlo nell'account di automazione.
1. Selezionare **Modifica** e quindi fare clic su **Pubblica**. Al prompt, selezionare **Sì** per pubblicare il runbook.

## <a name="create-a-watcher-task"></a>Creare un'attività watcher

In questo passaggio si configura l'attività watcher che fa riferimento ai runbook watcher e azione definiti nelle sezioni precedenti.

1. Passare all'account di automazione e selezionare **Attività watcher** sotto **Automazione processi**.
1. Selezionare la pagina Attività watcher e fare clic su **+ Aggiungi un'attività watcher**.
1. Immettere **WatchMyFolder** nel campo Nome.

1. Selezionare **Configura watcher** e scegliere il runbook **Watch-NewFile**.

1. Per i parametri inserire i valori seguenti:

   * **FOLDERPATH**: una cartella nel ruolo di lavoro ibrido per runbook in cui verranno creati nuovi file, ad esempio **d:\examplefiles**.
   * **EXTENSION**: estensione per la configurazione. Lasciare vuoto per elaborare tutte le estensioni di file.
   * **RECURSE**: operazione ricorsiva. Lasciare questo valore come impostazione predefinita.
   * **RUN SETTINGS**: impostazioni per l'esecuzione del runbook. Selezionare il ruolo di lavoro ibrido.

1. Fare clic su **OK** e quindi su **Seleziona** per tornare alla pagina del watcher.
1. Selezionare **Configura azione** e scegliere il runbook **Process-NewFile**.
1. Per i parametri inserire i valori seguenti:

   * **EVENTDATA**: dati dell'evento. Lasciare vuoto. I dati vengono passati dal runbook watcher.
   * **Impostazioni esecuzione test**: impostazioni per l'esecuzione del runbook. Lasciare questo campo impostato su Azure in quanto questo runbook viene eseguito in Automazione di Azure.

1. Fare clic su **OK** e quindi su **Seleziona** per tornare alla pagina del watcher.
1. Fare clic su **OK** per creare l'attività watcher.

    ![Configurare l'azione watcher dall'interfaccia utente](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Attivare un watcher

È necessario eseguire un test come descritto di seguito per assicurarsi che l'attività watcher funzioni come previsto. 

1. Passare al ruolo di lavoro ibrido per runbook.
2. Aprire **PowerShell** e creare un file di test nella cartella.

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

L'esempio seguente illustra l'output previsto.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Esaminare i risultati

1. Passare all'account di automazione e selezionare **Attività watcher** sotto **Automazione processi**.
1. Selezionare l'attività watcher **WatchMyFolder**.
1. Fare clic su **Visualizza flussi watcher** in **Flussi** per vedere che il watcher ha trovato il nuovo file e ha avviato il runbook azione.
1. Per visualizzare i processi del runbook azione, fare clic su **Visualizza processi dell'azione watcher**. È possibile selezionare ogni processo per visualizzarne i dettagli.

   ![Processi dell'azione watcher dall'interfaccia utente](media/automation-watchers-tutorial/WatcherActionJobs.png)

L'esempio seguente illustra l'output previsto quando viene trovato il nuovo file:

```output
Message is Process new file...

Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla creazione di Runbook personalizzati, vedere [creare un Runbook di PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
