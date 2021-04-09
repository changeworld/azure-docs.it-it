---
title: Distribuire un aggiornamento con l'aggiornamento del dispositivo per l'hub Azure Microsoft Docs
description: Distribuire un aggiornamento con l'aggiornamento del dispositivo per l'hub Azure.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 0a11c8e8946229941c1fe60f7f2ce84d9fadb2ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101679515"
---
# <a name="deploy-an-update-using-device-update-for-iot-hub"></a>Distribuire un aggiornamento con l'aggiornamento del dispositivo per l'hub Internet

Informazioni su come distribuire un aggiornamento a un dispositivo Internet delle cose usando l'aggiornamento dei dispositivi per l'hub Internet.

## <a name="prerequisites"></a>Prerequisiti

* [Accesso a un hub Internet delle cose con l'aggiornamento del dispositivo per l'hub](create-device-update-account.md)Internet. Si consiglia di usare un livello S1 (standard) o superiore per l'hub Internet. 
* [È stato importato almeno un aggiornamento per il dispositivo di cui è stato effettuato il provisioning.](import-update.md) 
* Viene eseguito il provisioning di un dispositivo o di un simulatore per l'aggiornamento del dispositivo all'interno dell'hub.
* [È stato assegnato un tag al dispositivo Internet delle cose che si sta tentando di aggiornare. Il dispositivo fa parte di almeno un gruppo di aggiornamento.](create-update-group.md)
* Browser supportati:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="deploy-an-update"></a>Distribuire un aggiornamento

1. Vai a [portale di Azure](https://portal.azure.com)

2. Passare al pannello di aggiornamento del dispositivo dell'hub Internet delle cose.

  :::image type="content" source="media/deploy-update/device-update-iot-hub.png" alt-text="Hub IoT" lightbox="media/deploy-update/device-update-iot-hub.png":::

3. Selezionare la scheda gruppi nella parte superiore della pagina. [Altre](device-update-groups.md) informazioni sui gruppi di dispositivi. 

  :::image type="content" source="media/deploy-update/updated-view.png" alt-text="Scheda Gruppi" lightbox="media/deploy-update/updated-view.png":::

4. Visualizzare l'elenco dei gruppi e del grafico di conformità degli aggiornamenti. Dovrebbe essere visualizzato un nuovo aggiornamento per il gruppo di dispositivi, con un collegamento all'aggiornamento con aggiornamenti in sospeso (potrebbe essere necessario eseguire l'aggiornamento una sola volta). [Altre informazioni sulla conformità degli aggiornamenti.](device-update-compliance.md) 

5. Selezionare l'aggiornamento disponibile.

6. Verificare che sia selezionato il gruppo corretto come gruppo di destinazione. Pianificare la distribuzione, quindi selezionare Distribuisci aggiornamento.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Seleziona aggiornamento" lightbox="media/deploy-update/select-update.png":::

7. Visualizzare il grafico di conformità. Si noterà che l'aggiornamento è in corso. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Aggiornamento in corso" lightbox="media/deploy-update/update-in-progress.png":::

8. Al termine dell'aggiornamento del dispositivo, si noterà che l'aggiornamento del grafico di conformità e dei dettagli della distribuzione è identico. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Aggiornamento completato" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Monitorare una distribuzione degli aggiornamenti

1. Selezionare la scheda distribuzioni nella parte superiore della pagina.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Scheda distribuzioni" lightbox="media/deploy-update/deployments-tab.png":::

2. Selezionare la distribuzione creata per visualizzare i dettagli della distribuzione.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Dettagli di distribuzione" lightbox="media/deploy-update/deployment-details.png":::

3. Selezionare Aggiorna per visualizzare i dettagli più recenti sullo stato. Continuare questo processo fino a quando lo stato diventa succeeded.


## <a name="retry-an-update-deployment"></a>Ritentare una distribuzione di aggiornamenti

Se la distribuzione non riesce per qualche motivo, è possibile riprovare la distribuzione per i dispositivi non riusciti. 

1. Passare alla scheda distribuzioni e selezionare la distribuzione che ha avuto esito negativo. 

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Dettagli di distribuzione" lightbox="media/deploy-update/deployment-details.png":::

2. Fare clic sullo stato del dispositivo "non riuscito" nel riquadro informazioni dettagliate sulla distribuzione.

3. Fare clic su "riprovare i dispositivi non riusciti" e confermare la notifica di conferma. 

## <a name="next-steps"></a>Passaggi successivi

[Risolvere i problemi comuni](troubleshoot-device-update.md)
