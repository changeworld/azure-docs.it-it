---
title: Creare attività di automazione per gestire e monitorare le risorse di Azure
description: Configurare attività automatizzate che consentono di gestire le risorse di Azure e monitorare i costi creando flussi di lavoro che vengono eseguiti App per la logica di Azure.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 04/05/2021
ms.openlocfilehash: 0a98f9e4b108d2498fa19bc0b041f9d52272c7d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774918"
---
# <a name="manage-azure-resources-and-monitor-costs-by-creating-automation-tasks-preview"></a>Gestire le risorse di Azure e monitorare i costi creando attività di automazione (anteprima)

> [!IMPORTANT]
> Questa funzionalità è in anteprima pubblica, viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Per facilitare la gestione delle risorse di [Azure,](../azure-resource-manager/management/overview.md#terminology) è possibile creare attività di gestione automatizzate per una risorsa o un gruppo di risorse specifico usando modelli di attività di automazione, che variano in base al tipo di risorsa. Ad esempio, per un [account di archiviazione di Azure,](../storage/common/storage-account-overview.md)è possibile configurare un'attività di automazione che invia il costo mensile per tale account di archiviazione. Per una [macchina virtuale di Azure,](https://azure.microsoft.com/services/virtual-machines/)è possibile creare un'attività di automazione che attiva o disattiva la macchina virtuale in base a una pianificazione predefinita.

Dietro le quinte, un'attività di automazione è in realtà un flusso di lavoro che viene eseguito nel [servizio App per la logica di Azure](../logic-apps/logic-apps-overview.md) e viene fatturato usando gli stessi prezzi [e](https://azure.microsoft.com/pricing/details/logic-apps/) gli stessi modelli [di prezzi.](../logic-apps/logic-apps-pricing.md) Dopo aver creato l'attività, è possibile visualizzare e modificare il flusso di lavoro sottostante aprendo l'attività in Progettazione app per la logica. Al termine di almeno un'esecuzione di un'attività, è possibile esaminare lo stato, la cronologia, gli input e gli output per ogni esecuzione.

Ecco i modelli di attività attualmente disponibili in questa anteprima:

| Tipo di risorsa | Modelli di attività di Automazione |
|---------------|---------------------------|
| Gruppi di risorse di Azure | **Quando la risorsa viene eliminata** |
| In tutte le risorse di Azure | **Inviare il costo mensile per la risorsa** |
| Macchine virtuali di Azure | Inoltre: <p>- **Spegnere la macchina virtuale** <br>- **Avviare la macchina virtuale** |
| Account di archiviazione di Azure | Inoltre: <p>- **Eliminare i BLOB vecchi** |
| Azure Cosmos DB | Inoltre, <p>- **Inviare il risultato della query tramite posta elettronica** |
|||

Questo articolo illustra come completare le attività seguenti:

* [Creare un'attività di automazione](#create-automation-task) per una risorsa di Azure specifica.

* [Esaminare la cronologia di un'attività](#review-task-history), che include lo stato di esecuzione, gli input, gli output e altre informazioni cronologiche.

* [Modificare l'attività](#edit-task) in modo che sia possibile aggiornare l'attività o personalizzare il flusso di lavoro sottostante dell'attività in Progettazione app per la logica.

<a name="differences"></a>

## <a name="how-do-automation-tasks-differ-from-azure-automation"></a>Differenze tra le attività di automazione e Automazione di Azure?

Attualmente, è possibile creare un'attività di automazione solo a livello di risorsa, visualizzare la cronologia delle esecuzioni dell'attività e modificare il flusso di lavoro dell'app per la logica sottostante dell'attività, basato sul [servizio](../logic-apps/logic-apps-overview.md) App per la logica di Azure. Le attività di automazione sono più semplici e leggere [rispetto Automazione di Azure](../automation/automation-intro.md).

In confronto, Automazione di Azure è un servizio di automazione e configurazione basato sul cloud che supporta la gestione coerente negli ambienti Azure e non Azure. Il servizio comprende l'automazione dei processi per [l'orchestrazione](../automation/automation-intro.md#process-automation) dei processi tramite [runbook](../automation/automation-runbook-execution.md), la gestione della configurazione con rilevamento delle modifiche e [inventario,](../automation/change-tracking/overview.md)la gestione degli aggiornamenti, le funzionalità condivise e le funzionalità eterogenee. Automazione fornisce il controllo completo durante la distribuzione, l'attività e la rimozione delle autorizzazioni di carichi di lavoro e risorse.

## <a name="prerequisites"></a>Prerequisiti

* Un account e una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* La risorsa di Azure che si vuole gestire. Questo articolo usa un account di archiviazione di Azure come esempio.

* Un account di Office 365 se si vuole seguire l'esempio, che invia un messaggio di posta elettronica usando Office 365 Outlook.

<a name="create-automation-task"></a>

## <a name="create-an-automation-task"></a>Creare un'attività di automazione

1. Nella [portale di Azure](https://portal.azure.com)trovare la risorsa che si vuole gestire.

1. Nel menu delle risorse scorrere fino alla sezione **Automazione** e selezionare **Attività**

   ![Screenshot che mostra il menu portale di Azure e una risorsa dell'account di archiviazione in cui nella sezione "Automazione" è selezionata la voce di menu "Attività".](./media/create-automation-tasks-azure-resources/storage-account-menu-automation-section.png)

1. Nel riquadro **Attività** selezionare Aggiungi **per** poter selezionare un modello di attività.

   ![Screenshot che mostra il riquadro "Attività" dell'account di archiviazione in cui è selezionata la barra degli strumenti "Aggiungi"](./media/create-automation-tasks-azure-resources/add-automation-task.png)

1. Nel riquadro **Aggiungi attività** selezionare **il** modello per l'attività che si vuole creare in Selezionare un modello. Se la pagina successiva non viene visualizzata, selezionare **Avanti: Autenticazione**.

   Questo esempio continua selezionando il modello Send monthly cost for resource task **(Invia costo mensile per attività** risorsa).

   ![Screenshot che mostra le selezioni, "Invia costo mensile per la risorsa" e "Avanti: Autenticazione"](./media/create-automation-tasks-azure-resources/select-task-template.png)

1. In **Autenticazione** nella sezione **Connessioni** selezionare Crea per ogni connessione visualizzata nell'attività in modo da poter fornire le credenziali di autenticazione per tutte le connessioni.  I tipi di connessioni in ogni attività variano in base all'attività.

   In questo esempio viene illustrata solo una delle connessioni richieste da questa attività.

   ![Screenshot che mostra l'opzione "Crea" selezionata per la Azure Resource Manager connessione](./media/create-automation-tasks-azure-resources/create-authenticate-connections.png)

1. Quando richiesto, accedere con le credenziali dell'account Azure.

   ![Screenshot che mostra la selezione "Accedi"](./media/create-automation-tasks-azure-resources/create-connection-sign-in.png)

   Ogni connessione autenticata è simile all'esempio seguente:

   ![Screenshot che mostra la connessione creata correttamente](./media/create-automation-tasks-azure-resources/create-connection-success.png)

1. Dopo aver autenticato tutte le connessioni, selezionare **Avanti: Configurazione** se la pagina successiva non viene visualizzata.

1. In **Configurazione** specificare un nome per l'attività e qualsiasi altra informazione necessaria per l'attività. Al termine, selezionare **Crea**.

   > [!NOTE]
   > Non è possibile modificare il nome dell'attività dopo la creazione, quindi prendere in considerazione un nome che si applica ancora se si [modifica il flusso di lavoro sottostante.](#edit-task-workflow) Le modifiche apportate al flusso di lavoro sottostante si applicano solo all'attività creata, non al modello di attività.
   >
   > Ad esempio, se si denota l'attività , ma successivamente si modifica il flusso di lavoro sottostante per l'esecuzione settimanale, non è possibile modificare il nome `Send monthly cost` dell'attività in `Send weekly cost` .

   Le attività che inviano notifiche di posta elettronica richiedono un indirizzo di posta elettronica.

   ![Screenshot che mostra le informazioni necessarie per l'attività selezionata](./media/create-automation-tasks-azure-resources/provide-task-information.png)

   L'attività creata, attiva e in esecuzione automaticamente, viene ora visualizzata **nell'elenco attività di** Automazione.

   ![Screenshot che mostra l'elenco delle attività di automazione](./media/create-automation-tasks-azure-resources/automation-tasks-list.png)

   > [!TIP]
   > Se l'attività non viene visualizzata immediatamente, provare ad aggiornare l'elenco attività o attendere un po' prima di eseguire l'aggiornamento. Sulla barra degli strumenti selezionare **Aggiorna**.

   Dopo l'esecuzione dell'attività selezionata, viene visualizzato un messaggio di posta elettronica simile al seguente:

   ![Screenshot che mostra la notifica di posta elettronica inviata dall'attività](./media/create-automation-tasks-azure-resources/email-notification-received.png)

<a name="review-task-history"></a>

## <a name="review-task-history"></a>Esaminare la cronologia delle attività

Per visualizzare la cronologia delle esecuzioni di un'attività con i relativi stati, input, output e altre informazioni, seguire questa procedura:

1. Nella [portale di Azure](https://portal.azure.com)trovare la risorsa con la cronologia attività che si vuole esaminare.

1. Nel menu della risorsa, in Impostazioni, **selezionare** Attività **di Automazione.**

1. Nell'elenco delle attività trovare l'attività che si vuole esaminare. Nella colonna Esecuzioni **dell'attività** selezionare **Visualizza**.

   ![Screenshot che mostra un'attività e l'opzione "Visualizza" selezionata](./media/create-automation-tasks-azure-resources/view-runs-for-task.png)

   Il **riquadro Cronologia esecuzioni** mostra tutte le esecuzioni per l'attività insieme ai relativi stati, ore di inizio, identificatori e durate di esecuzione.

   ![Screenshot che mostra le esecuzioni di un'attività, i relativi stati e altre informazioni](./media/create-automation-tasks-azure-resources/view-runs-history.png)

   Ecco i possibili stati per un'esecuzione:

   | Stato | Descrizione |
   |--------|-------------|
   | **annullato** | L'attività è stata annullata durante l'esecuzione. |
   | **Operazione non riuscita** | L'attività ha almeno un'azione non riuscita, ma non sono presenti azioni successive per gestire l'errore. |
   | **Running** | L'attività è attualmente in esecuzione. |
   | **Completato** | Tutte le azioni hanno avuto esito positivo. Un'attività può comunque essere completata correttamente se un'azione non è riuscita, ma esiste un'azione successiva per gestire l'errore. |
   | **Attesa** | L'esecuzione non è stata ancora avviata ed è sospesa perché un'istanza precedente dell'attività è ancora in esecuzione. |
   |||

   Per altre informazioni, vedere Esaminare [la cronologia delle esecuzioni](../logic-apps/monitor-logic-apps.md#review-runs-history)

1. Per visualizzare gli stati e altre informazioni per ogni passaggio di un'esecuzione, selezionarla.

   Viene visualizzato **il riquadro Esecuzione dell'app** per la logica che mostra il flusso di lavoro sottostante eseguito.

   * Un flusso di lavoro inizia sempre con un [*trigger*](../connectors/apis-list.md#triggers). Per questa attività, il flusso di lavoro inizia con [ **il** trigger Ricorrenza](../connectors/connectors-native-recurrence.md).

   * Ogni passaggio mostra lo stato e la durata dell'esecuzione. L'esecuzione dei passaggi con durate di 0 secondi ha richiesto meno di 1 secondo.

   ![Screenshot che mostra ogni passaggio nell'esecuzione, nello stato e nella durata dell'esecuzione](./media/create-automation-tasks-azure-resources/runs-history-details.png)

1. Per esaminare gli input e gli output per ogni passaggio, selezionare il passaggio che si espande.

   Questo esempio illustra gli input per il trigger Ricorrenza, che non ha output perché il trigger specifica solo quando viene eseguito il flusso di lavoro e non fornisce output per le azioni successive da elaborare.

   ![Screenshot che mostra il trigger espanso e gli input](./media/create-automation-tasks-azure-resources/view-trigger-inputs.png)

   Al contrario, **l'azione Invia un messaggio** di posta elettronica include input da azioni precedenti nel flusso di lavoro e negli output.

   ![Screenshot che mostra un'azione espansa, input e output](./media/create-automation-tasks-azure-resources/view-action-inputs-outputs.png)

Per informazioni su come creare flussi di lavoro automatizzati personalizzati in modo da poter integrare app, dati, servizi e sistemi oltre al contesto delle attività di automazione per le risorse di Azure, vedere Avvio [rapido: Creare](../logic-apps/quickstart-create-first-logic-app-workflow.md)il primo flusso di lavoro di integrazione usando App per la logica di Azure - portale di Azure .

<a name="edit-task"></a>

## <a name="edit-the-task"></a>Modificare l'attività

Per modificare un'attività, sono disponibili le opzioni seguenti:

* [Modificare l'attività "inline" in](#edit-task-inline) modo da poter modificare le proprietà dell'attività, ad esempio le informazioni di connessione o di configurazione, ad esempio l'indirizzo di posta elettronica.

* [Modificare il flusso di lavoro sottostante dell'attività](#edit-task-workflow) in Progettazione app per la logica.

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>Modificare l'attività inline

1. Nella [portale di Azure](https://portal.azure.com)trovare la risorsa con l'attività che si vuole aggiornare.

1. Nel menu della risorsa, in **Automazione,** selezionare **Attività**.

1. Nell'elenco delle attività trovare l'attività che si vuole aggiornare. Aprire il menu con i puntini di sospensione (**...**) dell'attività e **selezionare Modifica in linea**.

   ![Screenshot che mostra il menu con i puntini di sospensione aperti e l'opzione selezionata, "Modifica inline"](./media/create-automation-tasks-azure-resources/view-task-inline.png)

   Per impostazione predefinita, viene **visualizzata** la scheda Autenticazione con le connessioni esistenti.

1. Per aggiungere nuove credenziali di autenticazione o selezionare credenziali di autenticazione esistenti diverse per una connessione,  aprire il menu con i puntini di sospensione (**...**) della connessione e selezionare Aggiungi nuova connessione o, se disponibile, credenziali di autenticazione diverse.

   ![Screenshot che mostra la scheda Autenticazione, le connessioni esistenti e il menu con i puntini di sospensione selezionati](./media/create-automation-tasks-azure-resources/edit-connections.png)

1. Per aggiornare altre proprietà dell'attività, selezionare **Avanti: Configurazione.**

   Per l'attività in questo esempio, l'unica proprietà disponibile per la modifica è l'indirizzo di posta elettronica.

   ![Screenshot che mostra la scheda Configurazione](./media/create-automation-tasks-azure-resources/edit-task-configuration.png)

1. Al termine, selezionare **Salva**.

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>Modificare il flusso di lavoro sottostante dell'attività

Quando si modifica il flusso di lavoro sottostante per un'attività di automazione, le modifiche interessano solo l'istanza dell'attività creata e non il modello che crea l'attività. Dopo aver apportato e salvato le modifiche, il nome specificato per l'attività originale potrebbe non descrivere più in modo accurato l'attività, quindi potrebbe essere necessario ricreare l'attività con un nome diverso.

> [!TIP]
> Come procedura consigliata, clonare il flusso di lavoro sottostante in modo da poter modificare la versione copiata. In questo modo, è possibile apportare e testare le modifiche nella copia mentre l'attività di automazione originale continua a funzionare ed essere eseguita senza rischiare interruzioni o interruzioni delle funzionalità esistenti. Dopo aver completato le modifiche e aver soddisfatto la corretta esecuzione della nuova versione, è possibile disabilitare o eliminare l'attività di automazione originale e usare la versione clonata per l'attività di automazione. I passaggi seguenti includono informazioni su come clonare il flusso di lavoro.

1. Nella [portale di Azure](https://portal.azure.com)trovare la risorsa con l'attività che si vuole aggiornare.

1. Nel menu della risorsa, in **Automazione,** selezionare **Attività.**

1. Nell'elenco delle attività trovare l'attività che si vuole aggiornare. Aprire il menu con i puntini di sospensione (**...**) dell'attività e **selezionare Apri in App per la logica.**

   ![Screenshot che mostra il menu con i puntini di sospensione aperti e l'opzione selezionata"Apri in App per la logica"](./media/create-automation-tasks-azure-resources/edit-task-logic-app-designer.png)

   Il flusso di lavoro sottostante dell'attività viene aperto  nel servizio App per la logica di Azure e mostra il riquadro Panoramica in cui è possibile visualizzare la stessa cronologia di esecuzioni disponibile per l'attività.

   ![Screenshot che mostra l'attività nella App per la logica di Azure con il riquadro Panoramica selezionato](./media/create-automation-tasks-azure-resources/task-logic-apps-view.png)

1. Per aprire il flusso di lavoro sottostante in Progettazione app per la logica, scegliere Progettazione app per la logica dal menu **dell'app per la logica.**

   ![Screenshot che mostra l'opzione di menu "Progettazione app per la logica" selezionata e l'area di progettazione con il flusso di lavoro sottostante](./media/create-automation-tasks-azure-resources/view-task-workflow-logic-app-designer.png)

   È ora possibile modificare le proprietà per il trigger e le azioni del flusso di lavoro, nonché modificare il trigger e le azioni che definiscono il flusso di lavoro stesso. Tuttavia, come procedura consigliata, seguire la procedura per clonare il flusso di lavoro in modo da poter apportare le modifiche in una copia mentre il flusso di lavoro originale continua a funzionare ed eseguire.

1. Per clonare il flusso di lavoro e modificare invece la versione copiata, seguire questa procedura:

   1. Scegliere Panoramica dal menu del flusso di lavoro dell'app per la **logica.**

   1. Sulla barra degli strumenti del riquadro di panoramica selezionare **Clona**.

   1. Nel riquadro creazione app per la logica, in **Nome** immettere un nuovo nome per il flusso di lavoro dell'app per la logica copiato.

      Ad eccezione **di Stato app per la logica,** le altre proprietà non sono disponibili per la modifica. 
      
   1. In **Stato app per la** logica selezionare **Disabilitato** in modo che il flusso di lavoro clonato non sia eseguito mentre si apportano le modifiche. È possibile abilitare il flusso di lavoro quando si è pronti per testare le modifiche.

   1. Al termine del provisioning del flusso di lavoro clonato, Azure trova e apri il flusso di lavoro in Progettazione app per la logica.

1. Per visualizzare le proprietà per il trigger o un'azione, espandere tale trigger o azione.

   Ad esempio, è possibile modificare il trigger Ricorrenza per l'esecuzione settimanale, anziché mensile.

   ![Screenshot che mostra il trigger Ricorrenza espanso con l'elenco Frequenza aperto per visualizzare le opzioni di frequenza disponibili](./media/create-automation-tasks-azure-resources/edit-recurrence-trigger.png)

   Per altre informazioni sul trigger Ricorrenza, vedere Creare, pianificare ed eseguire attività e flussi di lavoro ricorrenti con il [trigger Ricorrenza](../connectors/connectors-native-recurrence.md). Per altre informazioni su altri trigger e azioni che è possibile usare, vedere [Connettori per App per la logica di Azure](../connectors/apis-list.md).

1. Per salvare le modifiche, sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

   ![Screenshot che mostra la barra degli strumenti della finestra di progettazione e il comando "Salva" selezionato](./media/create-automation-tasks-azure-resources/save-updated-workflow.png)

1. Per testare ed eseguire il flusso di lavoro aggiornato, sulla barra degli strumenti della finestra di progettazione selezionare **Esegui.**

   Al termine dell'esecuzione, la finestra di progettazione mostra i dettagli di esecuzione del flusso di lavoro.

   ![Screenshot che mostra i dettagli di esecuzione del flusso di lavoro nella finestra di progettazione](./media/create-automation-tasks-azure-resources/view-run-details-designer.png)

1. Per disabilitare il flusso di lavoro in modo che l'attività non continui a essere eseguita, vedere Gestire le app per [la logica nel portale di Azure](../logic-apps/manage-logic-apps-with-azure-portal.md).

## <a name="provide-feedback"></a>Fornire il feedback

È possibile udirlo. Per segnalare bug, inviare commenti e suggerimenti o porre domande su questa funzionalità di anteprima, contattare [il team App per la logica di Azure.](mailto:logicappspm@microsoft.com)

## <a name="next-steps"></a>Passaggi successivi

* [Gestire le app per la logica nel portale di Azure](../logic-apps/manage-logic-apps-with-azure-portal.md)
