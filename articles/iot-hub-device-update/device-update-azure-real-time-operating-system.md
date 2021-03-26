---
title: Esercitazione sull'aggiornamento del dispositivo per l'hub Azure per Azure-in tempo reale-sistema operativo | Microsoft Docs
description: Introduzione all'aggiornamento dei dispositivi per l'hub Azure Internet con Azure-in tempo reale-sistema operativo
author: valls
ms.author: valls
ms.date: 3/18/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: aaf954642fb276e16dfe4581c2675f817a708f3b
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572619"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-azure-real-time-operating-system-rtos"></a>Esercitazione sull'aggiornamento del dispositivo per l'hub Azure. uso del sistema operativo in tempo reale di Azure (RTO)

Questa esercitazione illustra come creare l'aggiornamento del dispositivo per l'agente hub Internet in Azure RTO NetX Duo. Fornisce anche API semplici per gli sviluppatori per integrare la funzionalità di aggiornamento del dispositivo nell'applicazione. Scopri gli [esempi](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU) di lavagne di valutazione dei semiconduttori chiave che includono le guide introduttive per imparare a configurare, compilare e distribuire gli aggiornamenti in modalità wireless (OTA) per i dispositivi.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Introduzione
> * Contrassegnare il dispositivo
> * Creare un gruppo di dispositivi
> * Distribuire un aggiornamento dell'immagine
> * Monitorare la distribuzione degli aggiornamenti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
* Accesso a un hub. Si consiglia di usare un livello S1 (standard) o superiore.
* Un'istanza di aggiornamento del dispositivo e un account collegati all'hub Internet delle cose. Seguire la guida per [creare e collegare](http://create-device-update-account.md/) un account di aggiornamento del dispositivo, se non è stato fatto in precedenza.

## <a name="get-started"></a>Introduzione

Ogni progetto di esempio RTO di Azure specifico per la lavagna contiene codice e documentazione su come usare l'aggiornamento dei dispositivi per l'hub Internet. 
1. Scaricare i file di esempio specifici della lavagna da [Azure RTO e gli esempi di aggiornamento del dispositivo](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU).
2. Trovare la cartella docs dall'esempio scaricato.
3. Da docs seguire i passaggi per preparare le risorse di Azure, l'account e registrare i dispositivi Internet.
5. Seguire quindi la documentazione per creare una nuova immagine del firmware e importare il manifesto per la lavagna.
6. Pubblicare successivamente l'immagine del firmware e il manifesto nell'aggiornamento del dispositivo per l'hub Internet.
7. Infine, scaricare ed eseguire il progetto nel dispositivo.

Scopri di più su [Azure RTO](https://docs.microsoft.com/azure/rtos/).  

## <a name="tag-your-device"></a>Contrassegnare il dispositivo

1. Lasciare l'applicazione del dispositivo in esecuzione nel passaggio precedente.
2. Accedere a [portale di Azure](https://portal.azure.com) e passare all'hub Internet delle cose.
3. Dal riquadro di spostamento a sinistra di "dispositivi Internet, trovare il dispositivo Internet delle cose e passare al dispositivo gemello.
4. Nel dispositivo gemello eliminare qualsiasi valore del tag di aggiornamento del dispositivo esistente impostando il valore su null.
5. Aggiungere un nuovo valore del tag di aggiornamento del dispositivo come illustrato di seguito.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="create-update-group"></a>Crea gruppo di aggiornamento

1. Passare all'hub Internet delle cose precedentemente connesso all'istanza di aggiornamento del dispositivo.
2. Selezionare l'opzione aggiornamenti del dispositivo in gestione automatica dispositivi dalla barra di spostamento a sinistra.
3. Selezionare la scheda gruppi nella parte superiore della pagina. 
4. Selezionare il pulsante Aggiungi per creare un nuovo gruppo.
5. Selezionare il tag dell'hub Internet delle cose creato nel passaggio precedente dall'elenco. Selezionare Crea gruppo di aggiornamento.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Screenshot che mostra la selezione di tag." lightbox="media/create-update-group/select-tag.PNG":::

[Altre](create-update-group.md) informazioni sull'aggiunta di tag e la creazione di gruppi di aggiornamento

## <a name="deploy-new-firmware"></a>Distribuire un nuovo firmware

1. Una volta creato il gruppo, verrà visualizzato un nuovo aggiornamento per il gruppo di dispositivi, con un collegamento all'aggiornamento in aggiornamenti in sospeso. Potrebbe essere necessario eseguire l'aggiornamento una sola volta. 
2. Fare clic sull'aggiornamento disponibile.
3. Verificare che sia selezionato il gruppo corretto come gruppo di destinazione. Pianificare la distribuzione, quindi selezionare Distribuisci aggiornamento.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Seleziona aggiornamento" lightbox="media/deploy-update/select-update.png":::

4. Visualizzare il grafico di conformità. Si noterà che l'aggiornamento è in corso. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Aggiornamento in corso" lightbox="media/deploy-update/update-in-progress.png":::

5. Al termine dell'aggiornamento del dispositivo, si noterà che l'aggiornamento del grafico di conformità e dei dettagli della distribuzione è identico. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Aggiornamento completato" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Monitorare una distribuzione degli aggiornamenti

1. Selezionare la scheda distribuzioni nella parte superiore della pagina.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Scheda distribuzioni" lightbox="media/deploy-update/deployments-tab.png":::

2. Selezionare la distribuzione creata per visualizzare i dettagli della distribuzione.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Dettagli di distribuzione" lightbox="media/deploy-update/deployment-details.png":::

3. Selezionare Aggiorna per visualizzare i dettagli più recenti sullo stato. Continuare questo processo fino a quando lo stato diventa succeeded.

A questo punto è stato completato un aggiornamento dell'immagine end-to-end corretto usando l'aggiornamento del dispositivo per l'hub Internet in un dispositivo Raspberry Pi 3 B +. 

## <a name="cleanup-resources"></a>Risorse di pulizia

Quando non è più necessario pulire l'account di aggiornamento del dispositivo, l'istanza, l'hub e il dispositivo Internet. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure RTO e sul relativo funzionamento con Azure, vedere https://azure.com/rtos .
