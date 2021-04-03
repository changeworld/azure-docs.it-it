---
title: Connettere l'applicazione Azure IoT Central a Dynamics 365 for Field Service | Microsoft Docs
description: Informazioni su come creare una soluzione end-to-end con Azure IoT Central e Dynamics 365 for Field Service
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1d098f56bbadfe115620580c8d93fb6dd021550d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586675"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Creare una soluzione end-to-end con Azure IoT Central e Dynamics 365 for Field Service 
Gli sviluppatori possono abilitare l'integrazione dell'applicazione Azure IoT Central con altri sistemi aziendali. 

Ad esempio, in una soluzione di Gestione rifiuti connessa è possibile ottimizzare l'invio dei camion di raccolta rifiuti. L'ottimizzazione può essere eseguita in base ai dati dei sensori di Internet delle cose provenienti da contenitori di rifiuti connessi. Nell' [applicazione IoT Central Connected Waste Management](./tutorial-connected-waste-management.md) è possibile configurare regole e azioni e impostarla per attivare la creazione di avvisi nel servizio campo Dynamics. Questo scenario viene eseguito tramite Power Automate, che verrà configurato direttamente in IoT Central per l'automazione dei flussi di lavoro tra le applicazioni e i servizi. Inoltre, in base alle attività dei servizi in Field Service, è possibile restituire le informazioni ad Azure IoT Central. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Come connettere l'applicazione Azure IoT Central a Dynamics 365 for Field Service 

I processi di integrazione seguenti possono essere implementati facilmente grazie a una semplice esperienza di configurazione:
* Azure IoT Central può inviare informazioni sulle anomalie dei dispositivi a Connected Field Service (come un avviso IoT) per la diagnosi.
* Connected Field Service può creare casi o ordini di lavoro attivati da anomalie dei dispositivi.
* Connected Field Service può pianificare le ispezioni dei tecnici per evitare tempi di inattività.
* Il dashboard del dispositivo Azure IoT Central può essere aggiornato con le informazioni di pianificazione e assistenza pertinenti.


## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sui [modelli di IoT Central per enti pubblici](./overview-iot-central-government.md)
* Altre informazioni su [IoT Central](../core/overview-iot-central.md)
* Altre informazioni su [Dynamics 365 for Field Service](/dynamics365/field-service/cfs-iot-overview)
