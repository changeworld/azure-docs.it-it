---
title: includere file
description: File di inclusione
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 0b3ccc31c9159b5d7b1615add89e8fdc308bf8df
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763466"
---
### <a name="publish-the-device-template"></a>Pubblicare il modello di dispositivo

Per poter aggiungere un dispositivo all'applicazione, è prima necessario pubblicare il modello di dispositivo:

1. Nel modello di dispositivo **LVA Edge Gateway v2** selezionare **Pubblica**.

1. Nella pagina **Pubblica questo modello di dispositivo nell'applicazione** selezionare **Pubblica**.

**LVA Edge Gateway v2** è ora disponibile come tipo di dispositivo da usare nella pagina **Dispositivi** nell'applicazione.

## <a name="migrate-the-gateway-device"></a>Eseguire la migrazione del dispositivo gateway

Il dispositivo **gateway-001** esistente usa il modello di dispositivo **LVA Edge Gateway**. Per usare il nuovo manifesto di distribuzione, eseguire la migrazione del dispositivo al nuovo modello di dispositivo:

Per eseguire la migrazione del dispositivo **gateway-001**:

1. Passare alla pagina **Dispositivi** e selezionare il dispositivo **gateway-001** per evidenziarlo nell'elenco.

1. Selezionare **Esegui migrazione**. Se l'icona **Esegui migrazione** non è visibile, selezionare **...** per visualizzare altre opzioni.

    :::image type="content" source="media/iot-central-video-analytics-part4/migrate-device.png" alt-text="Eseguire la migrazione del dispositivo gateway a una nuova versione":::

1. Nell'elenco della finestra di dialogo **Esegui migrazione** selezionare **LVA Edge Gateway v2** e quindi **Esegui migrazione**.

Dopo alcuni secondi, la migrazione viene completata. A questo punto, il dispositivo usa il modello di dispositivo **LVA Edge Gateway v2** con il manifesto di distribuzione personalizzato.

Non sono più disponibili dispositivi che usano il modello di dispositivo **LVA Edge Gateway** originale. Eliminare questo modello di dispositivo:

1. Passare alla pagina **Modelli di dispositivo** e selezionare **LVA Edge Gateway**.

1. Selezionare **Elimina** per eliminare il modello di dispositivo.

### <a name="get-the-device-credentials"></a>Ottenere le credenziali del dispositivo

È necessario ottenere le credenziali che consentono la connessione del dispositivo all'applicazione IoT Central. Per ottenere le credenziali del dispositivo:

1. Nella pagina **Dispositivi** selezionare il dispositivo **gateway-001**.

1. Selezionare **Connetti**.

1. Nella pagina **Connessione del dispositivo** prendere nota nel valore di **Ambito ID**, **ID dispositivo** e **Chiave primaria** del dispositivo nel file *scratchpad.txt*. Questi valori verranno usati più avanti.

1. Assicurarsi che il metodo di connessione sia impostato su **Firma di accesso condiviso**.

1. Selezionare **Chiudi**.

## <a name="next-steps"></a>Passaggi successivi

A questo punto è stata creata un'applicazione IoT Central usando il modello di applicazione **Analisi video - rilevamento movimento e oggetti**, è stato creato un modello di dispositivo per il dispositivo gateway ed è stato aggiunto un dispositivo gateway all'applicazione.

Se si vuole provare l'applicazione Analisi video - rilevamento movimento e oggetti usando i moduli IoT Edge in una macchina virtuale cloud con flussi video simulati:

> [!div class="nextstepaction"]
> [Creare un'istanza di IoT Edge per l'analisi video (macchina virtuale Linux)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-vm.md)

Se si vuole provare l'applicazione Analisi video - rilevamento movimento e oggetti usando i moduli IoT Edge in un dispositivo reale con la telecamera **ONVIF**:

> [!div class="nextstepaction"]
> [Creare un'istanza di IoT Edge per l'analisi video (Intel NUC)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-nuc.md)
