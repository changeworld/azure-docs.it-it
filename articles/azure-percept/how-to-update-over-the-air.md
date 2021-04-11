---
title: Aggiornare Azure Percept DK in modalità wireless (OTA)
description: Informazioni su come ricevere aggiornamenti in modalità wireless (OTA) in Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to
ms.openlocfilehash: a3f586f853201534bbaa613e8538d55485ffe147
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063118"
---
# <a name="update-your-azure-percept-dk-over-the-air-ota"></a>Aggiornare Azure Percept DK in modalità wireless (OTA)

Seguire questa guida per informazioni su come aggiornare il sistema operativo e il firmware della lavagna del vettore di Azure Percept DK in modalità wireless (OTA) con l'aggiornamento dei dispositivi per l'hub Internet.

## <a name="prerequisites"></a>Prerequisiti

- Azure Percept DK (DevKit)
- [Sottoscrizione di Azure](https://azure.microsoft.com/free/)
- [Esperienza di installazione di Azure PERCEPT dk](./quickstart-percept-dk-set-up.md): il kit di sviluppo è stato connesso a una rete di Wi-Fi, è stato creato un hub Internet e il kit dev è stato connesso all'hub Internet.
- [La configurazione dell'aggiornamento del dispositivo per l'hub Internet è stata completata](./how-to-set-up-over-the-air-updates.md)

## <a name="import-your-update-file-and-manifest-file"></a>Importa il file di aggiornamento e il file manifesto

> [!NOTE]
> Se l'aggiornamento è già stato importato, è possibile passare direttamente alla **creazione di un gruppo di aggiornamento del dispositivo**.

1. [Scaricare il file manifesto (con estensione JSON) e il file di aggiornamento (con estensione SWU) appropriati per il dispositivo Percept di Azure](https://go.microsoft.com/fwlink/?linkid=2155625).

1. Passare all'hub Azure Internet che si sta usando per il dispositivo Percept di Azure. Nel pannello del menu a sinistra selezionare aggiornamenti del **dispositivo** in **gestione automatica dei dispositivi**.

1. Nella parte superiore della schermata vengono visualizzate diverse schede. Selezionare la scheda **aggiornamenti** .

1. Selezionare **+ Importa nuovo aggiornamento** sotto l'intestazione **pronto per la distribuzione** .

1. Fare clic sulle caselle sotto **selezionare Importa file manifesto** e **selezionare Aggiorna file** per selezionare il file manifesto (con estensione JSON) e aggiornare il file (con estensione SWU).

1. Selezionare l'icona della cartella o la casella di testo sotto **selezionare un contenitore di archiviazione** e selezionare l'account di archiviazione appropriato. Se è già stato creato un contenitore di archiviazione, è possibile usarlo di nuovo. In caso contrario, selezionare **+ contenitore** per creare un nuovo contenitore di archiviazione per gli aggiornamenti OTA. Selezionare il contenitore che si vuole usare e fare clic su **Seleziona**.

1. Selezionare **Submit (Invia** ) per avviare il processo di importazione. A causa delle dimensioni dell'immagine, il processo di invio potrebbe richiedere fino a 5 minuti.

    > [!NOTE]
    > Potrebbe essere richiesto di aggiungere una regola di richiesta Cross Origin (CORS) per accedere al contenitore di archiviazione selezionato. Selezionare **Aggiungi regola e riprova** per continuare.

1. Quando viene avviato il processo di importazione, si verrà reindirizzati alla scheda **cronologia importazione** della pagina **aggiornamenti del dispositivo** . Fare clic su **Aggiorna** per monitorare lo stato di avanzamento durante il completamento del processo di importazione. A seconda delle dimensioni dell'aggiornamento, l'operazione potrebbe richiedere alcuni minuti o più (durante i periodi di picco, il servizio di importazione potrebbe richiedere fino a un'ora).

1. Quando la colonna **stato** indica che l'importazione è riuscita, selezionare la scheda **pronto per la distribuzione** e fare clic su **Aggiorna**. L'aggiornamento importato verrà ora visualizzato nell'elenco.

## <a name="create-a-device-update-group"></a>Creare un gruppo di aggiornamento del dispositivo

L'aggiornamento del dispositivo per l'hub Internet delle cose consente di indirizzare un aggiornamento a gruppi specifici di Azure Percept DKs. Per creare un gruppo, è necessario aggiungere un tag al set di dispositivi di destinazione nell'hub Azure.

> [!NOTE]
> Se è già stato creato un gruppo, è possibile passare alla sezione successiva.

Requisiti dei tag di gruppo:

- È possibile aggiungere qualsiasi valore al tag, ad eccezione di "senza categoria", che è un valore riservato.
- Il valore del tag non può superare i 255 caratteri.
- Il valore del tag può contenere solo i caratteri speciali seguenti: ".", "-", "_", "~".
- I nomi di tag e gruppi fanno distinzione tra maiuscole e minuscole
- Un dispositivo può avere un solo tag. Eventuali tag successivi aggiunti al dispositivo eseguiranno l'override del tag precedente.
- Un dispositivo può appartenere a un solo gruppo.

1. Aggiungere un tag ai dispositivi:

    1. Dal **IOT Edge** nel riquadro di spostamento a sinistra individuare Azure Percept dk e passare al **dispositivo gemello**.

    1. Aggiungere un nuovo **aggiornamento del dispositivo per** il valore del tag dell'hub Internet, come mostrato di seguito ( ```<CustomTagValue>``` si riferisce al valore o al nome del tag, ad esempio AzurePerceptGroup1). Altre informazioni sui tag del [documento JSON](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)del dispositivo gemello.

        ```
        "tags": {
        "ADUGroup": "<CustomTagValue>"
        },
        ```

1. Fare clic su **Salva** e risolvere eventuali problemi di formattazione.

1. Creare un gruppo selezionando un tag dell'hub Azure Internet esistente:

    1. Tornare alla pagina dell'hub Azure.

    1. Selezionare **aggiornamenti del dispositivo** in **gestione automatica dispositivi** nel pannello dei menu a sinistra.

    1. Selezionare la scheda **gruppi** . Questa pagina visualizzerà il numero di dispositivi non raggruppati connessi all'aggiornamento del dispositivo.

    1. Selezionare **+ Aggiungi** per creare un nuovo gruppo.

    1. Selezionare un tag dell'hub Internet delle cose nell'elenco e fare clic su **Submit (Invia**).

    1. Una volta creato il gruppo, verrà aggiornato l'elenco dei gruppi e del grafico di conformità degli aggiornamenti. Il grafico mostra il numero di dispositivi in diversi Stati di conformità: **sull'aggiornamento più recente**, i **nuovi aggiornamenti disponibili**, **gli aggiornamenti in corso** e **non ancora raggruppati**.

## <a name="deploy-an-update"></a>Distribuire un aggiornamento

1. Il gruppo appena creato verrà visualizzato con un nuovo aggiornamento elencato in **aggiornamenti disponibili** . potrebbe essere necessario eseguire l'aggiornamento una sola volta. Selezionare l'aggiornamento.

1. Verificare che il gruppo di dispositivi corretto sia selezionato come gruppo di dispositivi di destinazione. Selezionare una **Data di inizio** e un' **ora di inizio** per la distribuzione, quindi fare clic su **Crea distribuzione**.

    > [!CAUTION]
    > L'impostazione dell'ora di inizio nel passato attiverà immediatamente la distribuzione.

1. Controllare il grafico di conformità. Si noterà che l'aggiornamento è in corso.

1. Al termine dell'aggiornamento, il grafico di conformità rifletterà il nuovo stato di aggiornamento.

1. Selezionare la scheda **distribuzioni** nella parte superiore della pagina **aggiornamenti del dispositivo** .

1. Selezionare la distribuzione per visualizzare i dettagli della distribuzione. Potrebbe essere necessario fare clic su **Aggiorna** fino a quando lo **stato** diventa **succeeded**.

## <a name="next-steps"></a>Passaggi successivi

Il kit dev è stato aggiornato correttamente. È possibile continuare lo sviluppo e il funzionamento con il kit di sviluppo.