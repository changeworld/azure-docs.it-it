---
title: 'Guida introduttiva: onboarding di Defender per le cose in una soluzione basata su agenti'
description: In questa Guida introduttiva si apprenderà come eseguire l'onboarding e abilitare il Defender per il servizio di sicurezza di Internet delle cose nell'hub Azure.
ms.topic: quickstart
ms.date: 1/20/2021
ms.openlocfilehash: b795773ae0eb667cf7f53f4209b6476ba937b17f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780957"
---
# <a name="quickstart-onboard-defender-for-iot-to-an-agent-based-solution"></a>Guida introduttiva: onboarding di Defender per le cose in una soluzione basata su agenti

Questo articolo illustra come abilitare il servizio Defender per il servizio Internet delle cose nell'hub Internet esistente. Se non si ha già un hub IoT, vedere [Creare un hub IoT usando il portale di Azure](../iot-hub/iot-hub-create-through-portal.md) per iniziare.

È possibile gestire la sicurezza di Internet delle cose con l'hub Internet in Defender per le cose. Il portale di gestione che si trova nell'hub cose consente di eseguire le operazioni seguenti: 

- Gestire la sicurezza dell'hub Internet.

- Gestione di base della sicurezza di un dispositivo, senza installare un agente in base ai dati di telemetria dell'hub Internet. 

- Gestione avanzata per la sicurezza di un dispositivo Internet delle cose in base al micro Agent.

> [!NOTE]
> Attualmente Defender per IoT supporta solo hub IoT del livello Standard.

## <a name="prerequisites"></a>Prerequisiti

nessuno

## <a name="onboard-defender-for-iot-to-an-iot-hub"></a>Onboarding di Defender per le cose in un hub Internet

Per tutti i nuovi hub Internet, Defender for Internet è impostato **su on** per impostazione predefinita. È possibile verificare che Defender for Internet è attivato o disattivato durante il **processo di creazione** dell'hub Internet.

Per verificare che l'interruttore sia impostato **su** attivato:

1. Passare al portale di Azure.

1. Selezionare **Hub** tutto dall'elenco dei servizi di Azure.

1. Selezionare **Crea**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="Selezionare il pulsante Crea sulla barra degli strumenti superiore." lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. Selezionare la scheda **Management (gestione** ) e verificare che **Defender for** l'interruttore Internet sia impostato **su on**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Verificare che l'interruttore Defender for Internet è impostato su on.":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>Onboarding di Defender per le cose in un hub Internet esistente

È possibile caricare Defender per le cose in un hub Internet esistente, in cui è quindi possibile monitorare i modelli di comunicazione da dispositivo a cloud e da cloud a dispositivo.

Per caricare Defender per le cose in un hub Internet esistente:

1. Passare all'hub Internet delle cose. 

1. Selezionare l'hub delle cose da caricare.

1. Selezionare una delle opzioni nella sezione **sicurezza** .

1. Fare clic su **Proteggi la soluzione** Internet   delle cose e completare il modulo di onboarding. 

    :::image type="content" source="media/quickstart-onboard-iot-hub/secure-your-iot-solution.png" alt-text="Selezionare il pulsante Proteggi la soluzione Internet delle cose per proteggere la soluzione.":::

Il pulsante **Proteggi la soluzione** Internet delle cose verrà visualizzato solo se l'hub Internet delle cose non è già stato caricato o se durante il caricamento è stato lasciato il Defender per l'attivazione/ **disattivazione.**

:::image type="content" source="media/quickstart-onboard-iot-hub/toggle-is-off.png" alt-text="Se l'interruttore è stato impostato su OFF durante l'onboarding.":::

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare la soluzione, passare all'articolo successivo...

> [!div class="nextstepaction"]
> [Creare un modulo gemello di Defender Internet Agent (anteprima)](quickstart-create-micro-agent-module-twin.md)
