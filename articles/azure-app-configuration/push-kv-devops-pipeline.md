---
title: Inserire le impostazioni nella configurazione dell'app con Azure Pipelines
description: Informazioni su come usare Azure Pipelines per eseguire il push dei valori di chiave in un archivio di configurazione dell'app
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 02/23/2021
ms.author: alkemper
ms.openlocfilehash: e1a4fb52a5f9622758e9ed805bf9380f5f608870
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106068253"
---
# <a name="push-settings-to-app-configuration-with-azure-pipelines"></a>Inserire le impostazioni nella configurazione dell'app con Azure Pipelines

L'attività [push di configurazione app Azure](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) inserisce i valori chiave da un file di configurazione nell'archivio di configurazione dell'app. Questa attività Abilita la funzionalità del cerchio completo all'interno della pipeline, perché ora è possibile effettuare il pull delle impostazioni dall'archivio di configurazione dell'app, nonché le impostazioni push nell'archivio di configurazione dell'app.

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
- Risorsa di configurazione dell'app: crearne una gratuitamente nell' [portale di Azure](https://portal.azure.com).
- Progetto DevOps di Azure- [crearne uno](https://go.microsoft.com/fwlink/?LinkId=2014881) gratuitamente
- App Azure attività push di configurazione: scaricare gratuitamente dal [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push).

## <a name="create-a-service-connection"></a>Creare una connessione al servizio

Una [connessione al servizio](/azure/devops/pipelines/library/service-endpoints) consente di accedere alle risorse nella sottoscrizione di Azure dal progetto DevOps di Azure.

1. In Azure DevOps passare al progetto contenente la pipeline di destinazione e aprire le **impostazioni del progetto** in basso a sinistra.
1. In **pipeline** selezionare **connessioni al servizio** e selezionare **nuova connessione al servizio** in alto a destra.
1. Selezionare **Azure Resource Manager**.
![Screenshot mostra la selezione di Azure Resource Manager dall'elenco a discesa nuova connessione al servizio.](./media/new-service-connection.png)
1. Nella finestra di dialogo **metodo di autenticazione** selezionare **entità servizio (automatica)**.
    > [!NOTE]
    > L'autenticazione di **identità gestita** non è attualmente supportata per l'attività di configurazione dell'app.
1. Compilare la sottoscrizione e la risorsa. Assegnare un nome alla connessione al servizio.

Ora che è stata creata la connessione al servizio, trovare il nome dell'entità servizio assegnata. Nel passaggio successivo verrà aggiunta una nuova assegnazione di ruolo a questa entità servizio.

1. Passare a **Impostazioni progetto**  >  **connessioni al servizio**.
1. Selezionare la connessione al servizio creata nella sezione precedente.
1. Selezionare **Gestisci entità servizio**.
1. Prendere nota del **nome visualizzato** elencato.
![Screenshot che mostra il nome visualizzato dell'entità servizio.](./media/service-principal-display-name.png)

## <a name="add-role-assignment"></a>Aggiungi un'assegnazione di ruolo

Assegnare le assegnazioni di ruolo di configurazione dell'app appropriate alle credenziali usate all'interno dell'attività in modo che l'attività possa accedere all'archivio di configurazione dell'app.

1. Passare all'archivio di configurazione dell'app di destinazione. 
1. A sinistra selezionare controllo di **accesso (IAM)**.
1. Sul lato destro fare clic sul pulsante **Aggiungi assegnazioni di ruolo** .
![Screenshot che mostra il pulsante Aggiungi assegnazioni di ruolo.](./media/add-role-assignment-button.png)
1. In **ruolo** selezionare **proprietario dati di configurazione dell'app**. Questo ruolo consente all'attività di leggere e scrivere nell'archivio di configurazione dell'app. 
1. Selezionare l'entità servizio associata alla connessione al servizio creata nella sezione precedente.
![Screenshot mostra la finestra di dialogo Aggiungi assegnazione ruolo.](./media/add-role-assignment.png)

  
## <a name="use-in-builds"></a>Uso nelle compilazioni

Questa sezione illustra come usare l'attività push di configurazione app Azure in una pipeline di compilazione di Azure DevOps.

1. Passare alla pagina della pipeline di compilazione facendo **clic su pipeline pipeline**  >  . La documentazione per le pipeline di compilazione è disponibile [qui](/azure/devops/pipelines/create-first-pipeline?tabs=tfs-2018-2).
      - Se si sta creando una nuova pipeline di compilazione, nell'ultimo passaggio del processo, nella scheda **Verifica** , selezionare **Mostra Assistente** sul lato destro della pipeline.
      ![Screenshot mostra il pulsante Mostra Assistente per una nuova pipeline.](./media/new-pipeline-show-assistant.png)
      - Se si sta usando una pipeline di compilazione esistente, fare clic sul pulsante **modifica** in alto a destra.
      ![Screenshot mostra il pulsante modifica per una pipeline esistente.](./media/existing-pipeline-show-assistant.png)
1. Cercare l'attività **push di configurazione app Azure** .
![Screenshot mostra la finestra di dialogo Aggiungi attività con app Azure push di configurazione nella casella di ricerca.](./media/add-azure-app-configuration-push-task.png)
1. Configurare i parametri necessari per l'attività per eseguire il push dei valori di chiave dal file di configurazione nell'archivio di configurazione dell'app. Le spiegazioni dei parametri sono disponibili nella sezione **parametri** riportata di seguito e nelle descrizioni comandi accanto a ogni parametro.
![Screenshot mostra i parametri dell'attività push di configurazione dell'app.](./media/azure-app-configuration-push-parameters.png)
1. Salvare e accodare una compilazione. Nel log di compilazione verranno visualizzati tutti gli errori che si sono verificati durante l'esecuzione dell'attività.

## <a name="use-in-releases"></a>Uso nelle versioni

Questa sezione illustra come usare l'attività push di configurazione app Azure in una pipeline di rilascio di Azure DevOps.

1. Passare alla pagina della pipeline di rilascio selezionando **pipeline**  >  **versioni**. La documentazione per le pipeline di versione è disponibile [qui](/azure/devops/pipelines/release).
1. Scegliere una pipeline di rilascio esistente. Se non si dispone di un valore, selezionare **+ nuovo** per crearne uno nuovo.
1. Selezionare il pulsante **modifica** nell'angolo superiore destro per modificare la pipeline di rilascio.
1. Dall'elenco a discesa **attività** scegliere la **fase** a cui si desidera aggiungere l'attività. Altre informazioni sulle fasi sono disponibili [qui](/azure/devops/pipelines/release/environments).
![Screenshot mostra la fase selezionata nell'elenco a discesa attività.](./media/pipeline-stage-tasks.png)
1. Fare clic su **+** Avanti accanto al processo a cui si desidera aggiungere una nuova attività.
![Screenshot che mostra il pulsante più accanto al processo.](./media/add-task-to-job.png)
1. Nella finestra di dialogo **Aggiungi attività** Digitare **app Azure push configurazione** nella casella di ricerca e selezionarlo.
1. Configurare i parametri necessari all'interno dell'attività per eseguire il push dei valori di chiave dal file di configurazione all'archivio di configurazione dell'app. Le spiegazioni dei parametri sono disponibili nella sezione **parametri** riportata di seguito e nelle descrizioni comandi accanto a ogni parametro.
1. Salvare e accodare una versione. Nel log versione verranno visualizzati tutti gli errori rilevati durante l'esecuzione dell'attività.

## <a name="parameters"></a>Parametri

I parametri seguenti vengono usati dall'attività push di configurazione dell'app:

- **Sottoscrizione di Azure**: un elenco a discesa contenente le connessioni al servizio di Azure disponibili. Per aggiornare e aggiornare l'elenco delle connessioni dei servizi di Azure disponibili, fare clic sul pulsante **Aggiorna sottoscrizione di Azure** a destra della casella di testo.
- **Nome della configurazione dell'app**: un elenco a discesa che carica gli archivi di configurazione disponibili nella sottoscrizione selezionata. Per aggiornare e aggiornare l'elenco degli archivi di configurazione disponibili, fare clic sul pulsante **Aggiorna nome della configurazione dell'app** a destra della casella di testo.
- **Percorso del file di configurazione**: il percorso del file di configurazione. Il parametro del **percorso del file di configurazione** inizia dalla radice del repository di file. È possibile esplorare l'artefatto di compilazione per selezionare un file di configurazione. ( `...` pulsante a destra della casella di testo). I formati di file supportati sono: YAML, JSON, Properties. Di seguito è riportato un esempio di file di configurazione in formato JSON.
    ```json
    {
        "TestApp:Settings:BackgroundColor":"#FFF",
        "TestApp:Settings:FontColor":"#000",
        "TestApp:Settings:FontSize":"24",
        "TestApp:Settings:Message": "Message data"
    }
    ```
- **Separator**: il separatore usato per appiattire i file. JSON e. yml.
- **Depth**: la profondità con cui i file con estensione JSON e yml verranno resi bidimensionali.
- **Prefix**: stringa aggiunta all'inizio di ogni chiave inserita nell'archivio di configurazione dell'app.
- **Label**: stringa aggiunta a ogni valore chiave come etichetta all'interno dell'archivio di configurazione dell'app.
- **Tipo di contenuto**: stringa aggiunta a ogni valore chiave come tipo di contenuto all'interno dell'archivio di configurazione dell'app.
- **Tags**: un oggetto JSON nel formato `{"tag1":"val1", "tag2":"val2"}` , che definisce i tag aggiunti a ogni chiave-valore inserito nell'archivio di configurazione dell'app.
- **Elimina tutte le altre Key-Values nell'archivio con il prefisso e l'etichetta specificati: il** valore predefinito è **deselezionato**.
  - **Checked**: rimuove tutti i valori di chiave nell'archivio di configurazione dell'app che corrispondono sia al prefisso specificato che all'etichetta prima di eseguire il push di nuovi valori di chiave dal file di configurazione.
  - **Deselezionato**: inserisce tutti i valori chiave dal file di configurazione nell'archivio di configurazione dell'app e lascia intatto tutto il resto nell'archivio di configurazione dell'app.



## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verifica un errore imprevisto, è possibile abilitare i log di debug impostando la variabile della pipeline `system.debug` su `true` .

## <a name="faq"></a>Domande frequenti

**Come è possibile caricare più file di configurazione?**

Creare più istanze dell'attività push di configurazione app Azure all'interno della stessa pipeline per eseguire il push di più file di configurazione nell'archivio di configurazione dell'app.

**Perché viene visualizzato un errore 409 durante il tentativo di effettuare il push dei valori di chiave nell'archivio di configurazione?**

Si verificherà un messaggio di errore di conflitto 409 se l'attività tenta di rimuovere o sovrascrivere un valore chiave bloccato nell'archivio di configurazione dell'app.
