---
title: Aggiornare l'ambiente Azure Percept DK in modalità aerea
description: Scopri come ricevere gli aggiornamenti in modalità aerea per Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: b8f9e6f4bc091abbd1bb08ecbd649c1411e5ab20
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095392"
---
# <a name="update-your-azure-percept-dk-over-the-air"></a>Aggiornare l'ambiente Azure Percept DK in modalità aerea

Seguire questa guida per informazioni su come aggiornare la lavagna del vettore di Azure Percept DK in modalità wireless con l'aggiornamento dei dispositivi per l'hub Internet.

## <a name="import-your-update-file-and-manifest-file"></a>Importare il file di aggiornamento e il file manifesto.

> [!NOTE]
> Se l'aggiornamento è già stato importato, è possibile passare direttamente alla **creazione di un gruppo aggiornato del dispositivo**.

1. Passare all'hub Azure Internet che si sta usando per il dispositivo Percept di Azure. Nel pannello del menu a sinistra selezionare aggiornamenti del **dispositivo** in **gestione automatica dei dispositivi**.
 
1. Nella parte superiore della schermata vengono visualizzate diverse schede. Selezionare la scheda **aggiornamenti** .
 
1. Selezionare **+ Importa nuovo aggiornamento** sotto l'intestazione **pronto per la distribuzione** .
 
1. Fare clic sulle caselle sotto **selezionare Importa file manifesto** e **selezionare Aggiorna file** per selezionare il file manifesto appropriato (con estensione JSON) e un file di aggiornamento (SWU). [Qui](https://go.microsoft.com/fwlink/?linkid=2155625)è possibile trovare i file di aggiornamento per il dispositivo Azure Percept.
 
1. Selezionare l'icona della cartella o la casella di testo sotto **selezionare un contenitore di archiviazione**, quindi selezionare l'account di archiviazione appropriato.
 
1. Se è già stato creato un contenitore di archiviazione, è possibile usarlo di nuovo. In caso contrario, selezionare **+ contenitore** per creare un nuovo contenitore di archiviazione per gli aggiornamenti OTA. Selezionare il contenitore che si vuole usare e fare clic su **Seleziona**.
 
    >[!Note]
    >Se non si dispone di un contenitore, verrà richiesto di crearne uno.
 
1. Selezionare **Submit (Invia** ) per avviare il processo di importazione. Il processo di invio importerà circa 4 minuti.
 
    >[!Note]
    >Potrebbe essere richiesto di aggiungere una regola di richiesta Cross Origin (CORS) per accedere al contenitore di archiviazione selezionato. Selezionare **Aggiungi regola e riprova** per continuare.
 
    >[!Note]
    >A causa delle dimensioni dell'immagine, è possibile che venga visualizzata la pagina **INVIO...** Fino a 5 minuti prima di vedere il passaggio successivo.
    
1. Viene avviato il processo di importazione e si viene reindirizzati alla scheda **cronologia importazione** della pagina **aggiornamenti del dispositivo** . Fare clic su **Aggiorna** per monitorare lo stato di avanzamento durante il completamento del processo di importazione. A seconda delle dimensioni dell'aggiornamento, l'operazione potrebbe richiedere alcuni minuti o più (durante i periodi di picco, si prevede che il servizio di importazione debba richiedere fino a 1h).

1. Quando la colonna stato indica che l'importazione è riuscita, selezionare la scheda **pronto per la distribuzione** e fare clic su **Aggiorna**. L'aggiornamento importato verrà ora visualizzato nell'elenco.
 
## <a name="create-a-device-update-group"></a>Creare un gruppo di aggiornamento del dispositivo
L'aggiornamento del dispositivo per l'hub Internet delle cose consente di indirizzare un aggiornamento a gruppi specifici di Azure Percept DKs. Per creare un gruppo, è necessario aggiungere un tag al set di dispositivi di destinazione nell'hub Azure.

> [!NOTE]
> Se è già stato creato un gruppo, è possibile passare direttamente al passaggio successivo.

Requisiti dei tag di gruppo:
- È possibile aggiungere qualsiasi valore al tag, ad eccezione di **Uncategorized**, che è un valore riservato.
- Il valore del tag non può superare i 255 caratteri.
- Il valore del tag può contenere solo i caratteri speciali seguenti: ".", "-", "_", "~".
- I nomi di tag e gruppi fanno distinzione tra maiuscole e minuscole
- Un dispositivo può avere un solo tag. Eventuali tag successivi aggiunti al dispositivo eseguiranno l'override del tag precedente.
- Un dispositivo può appartenere a un solo gruppo.

1. Aggiungere un tag al dispositivo o ai dispositivi.
    1. Dal **IOT Edge** nel riquadro di spostamento a sinistra individuare Azure Percept dk e passare al **dispositivo gemello**.
    1. Aggiungere un nuovo **aggiornamento del dispositivo per il valore del tag dell'hub** Internet, come mostrato di seguito (modificare ```<CustomTagValue>``` il valore, ad esempio AzurePerceptGroup1). Altre informazioni sui tag del [documento JSON](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins#device-twins)del dispositivo gemello.

    ```
    "tags": {
    "ADUGroup": "<CustomTagValue>"
    },
    ```

 
1. Fare clic su **Salva** e risolvere eventuali problemi di formattazione.
 
1. Creare un gruppo selezionando un tag dell'hub Azure Internet esistente.
    1. Tornare alla pagina dell'hub Azure.
    1. Selezionare **aggiornamenti del dispositivo** in **gestione automatica dispositivi** nel pannello dei menu a sinistra.
    1. Selezionare la scheda **gruppi** . Questa pagina visualizzerà il numero di dispositivi non raggruppati connessi all'aggiornamento del dispositivo.
    1. Selezionare **+ Aggiungi** per creare un nuovo gruppo.
    1. Selezionare un tag dell'hub Internet delle cose nell'elenco e fare clic su **Submit (Invia**).
    1. Una volta creato il gruppo, verrà aggiornato l'elenco dei gruppi e del grafico di conformità degli aggiornamenti. Il grafico mostra il numero di dispositivi in diversi Stati di conformità: **sull'aggiornamento più recente**, i **nuovi aggiornamenti disponibili**, **gli aggiornamenti in corso** e **non ancora raggruppati**.
 

## <a name="deploy-an-update"></a>Distribuire un aggiornamento
1. Il gruppo appena creato verrà visualizzato con un nuovo aggiornamento elencato in **aggiornamenti disponibili** . potrebbe essere necessario eseguire l'aggiornamento una sola volta. Selezionare l'aggiornamento.
 
1. Verificare che il gruppo di dispositivi corretto sia selezionato come gruppo di dispositivi di destinazione. Selezionare una **Data di inizio** e un' **ora di inizio** per la distribuzione, quindi fare clic su **Crea distribuzione**. 

    >[!CAUTION]
    >L'impostazione dell'ora di inizio nel passato attiverà immediatamente la distribuzione.
 
1. Controllare il grafico di conformità. Si noterà che l'aggiornamento è in corso.
 
1. Al termine dell'aggiornamento, il grafico di conformità rifletterà il nuovo stato di aggiornamento.
 
1. Selezionare la scheda **distribuzioni** nella parte superiore della pagina **aggiornamenti del dispositivo** .
 
1. Selezionare la distribuzione per visualizzare i dettagli della distribuzione. Potrebbe essere necessario fare clic su **Aggiorna** fino a quando lo **stato** diventa **succeeded**.

## <a name="next-steps"></a>Passaggi successivi

Il kit dev è stato aggiornato correttamente. È possibile continuare lo sviluppo e le operazioni con la devkit.