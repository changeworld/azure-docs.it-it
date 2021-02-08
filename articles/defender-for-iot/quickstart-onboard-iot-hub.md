---
title: Onboarding in Defender per la soluzione basata su agenti it
description: Informazioni su come eseguire l'onboarding e abilitare il servizio di sicurezza Defender per IoT nell'hub IoT di Azure.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/20/2021
ms.author: shhazam
ms.openlocfilehash: 127e439a7740cb97cbe126071aaaa5245cd85782
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809134"
---
# <a name="onboard-to-defender-for-iot-agent-based-solution"></a>Onboarding in Defender per la soluzione basata su agenti it

Questo articolo illustra come abilitare il servizio Defender per il servizio Internet delle cose nell'hub Internet esistente. Se non si ha già un hub IoT, vedere [Creare un hub IoT usando il portale di Azure](../iot-hub/iot-hub-create-through-portal.md) per iniziare.

È possibile gestire la sicurezza di Internet delle cose con l'hub Internet in Defender per le cose. Il portale di gestione che si trova nell'hub cose consente di eseguire le operazioni seguenti: 

- Gestire la sicurezza dell'hub Internet.

- Gestione di base della sicurezza di un dispositivo, senza installare un agente in base ai dati di telemetria dell'hub Internet. 

- Gestione avanzata per la sicurezza di un dispositivo Internet delle cose in base al micro Agent.

> [!NOTE]
> Attualmente Defender per IoT supporta solo hub IoT del livello Standard.

## <a name="onboard-to-defender-for-iot-in-iot-hub"></a>Onboarding in Defender per le cose nell'hub Internet delle cose

Per tutti i nuovi hub Internet, Defender for Internet è impostato **su on** per impostazione predefinita. È possibile verificare che Defender for Internet è attivato o disattivato durante il **processo di creazione** dell'hub Internet.

Per verificare che l'interruttore sia impostato **su** attivato:

1. Passare al portale di Azure.

1. Selezionare **Hub** tutto dall'elenco dei servizi di Azure.

1. Selezionare **Crea**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="Selezionare il pulsante Crea sulla barra degli strumenti superiore." lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. Selezionare la scheda **Management (gestione** ) e verificare che **Defender for** l'interruttore Internet sia impostato **su on**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Verificare che l'interruttore Defender for Internet è impostato su on.":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>Onboarding di Defender per le cose in un hub Internet esistente

È possibile monitorare i modelli di comunicazione di gestione delle identità dei dispositivi, da dispositivo a cloud e da cloud a dispositivo, per avviare il servizio eseguire le operazioni seguenti: 

1. Passare all'hub Internet. 

1. Selezionare il menu **Panoramica sicurezza**   . 

1. Fare clic su Proteggi la soluzione Internet delle cose e completare il modulo di onboarding. 


## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare la soluzione, passare all'articolo successivo...

> [!div class="nextstepaction"]
> [Creare un modulo gemello di Defender Internet Agent (anteprima)](quickstart-create-micro-agent-module-twin.md)
