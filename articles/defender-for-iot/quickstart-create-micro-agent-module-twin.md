---
title: Creare un modulo gemello di Defender Internet Agent (anteprima)
titleSuffix: Azure Defender for IoT
description: Informazioni su come creare singoli moduli DefenderIotMicroAgent gemelli per i nuovi dispositivi.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: f14f253960b628f2bf8052e92dbec3c897a205db
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820841"
---
# <a name="create-a-defender-iot-micro-agent-module-twin-preview"></a>Creare un modulo gemello di Defender Internet Agent (anteprima)

È possibile creare singoli moduli **DefenderIotMicroAgent** gemelli per i nuovi dispositivi. È anche possibile creare in batch i moduli gemelli per tutti i dispositivi in un hub Internet. 

## <a name="device-twins"></a>Dispositivi gemelli 

Per le soluzioni IoT integrate in Azure, i dispositivi gemelli svolgono un ruolo fondamentale sia nella gestione dei dispositivi che nell'automazione dei processi. 

Defender for Internet è in grado di integrarsi completamente con la piattaforma di gestione dei dispositivi. L'integrazione completa consente di gestire lo stato di sicurezza del dispositivo e consente di usare tutte le funzionalità di controllo dei dispositivi esistenti. Per ottenere l'integrazione, è possibile usare il meccanismo gemello dell'hub Internet. 

Altre informazioni sul concetto di [dispositivi gemelli](../iot-hub/iot-hub-devguide-device-twins.md)   nell'hub Azure. 

## <a name="security-module-twins"></a>Moduli di sicurezza gemelli 

Defender for Internet usa un modulo di protezione gemello per ogni dispositivo. Il gemello del modulo di sicurezza include tutte le informazioni rilevanti per la sicurezza dei dispositivi, per ogni dispositivo specifico nella soluzione. Le proprietà di sicurezza del dispositivo vengono configurate tramite un modulo di protezione dedicato gemello per una comunicazione più sicura, per abilitare gli aggiornamenti e per la manutenzione che richiede un minor numero 

## <a name="understanding-defenderiotmicroagent-module-twins"></a>Informazioni sui moduli gemelli DefenderIotMicroAgent 

I dispositivi gemelli svolgono un ruolo chiave sia per la gestione dei dispositivi che per l'automazione dei processi, per le soluzioni Internet delle cose integrate in Azure.

Defender for Internet è in grado di integrare completamente la piattaforma di gestione dei dispositivi Internet esistente, consentendo di gestire lo stato di sicurezza del dispositivo e di usare le funzionalità di controllo dei dispositivi esistenti. È possibile integrare il Defender per Internet delle cose usando il meccanismo gemello dell'hub Internet.  

Per altre informazioni sul concetto generale di moduli gemelli nell'hub Azure, vedere i [gemelli dei moduli dell'hub](../iot-hub/iot-hub-devguide-module-twins.md)Internet.

Defender for Internet usa il meccanismo del modulo gemello e mantiene un modulo di sicurezza gemello denominato `DefenderIotMicroAgent` per ogni dispositivo. 

Per sfruttare appieno tutti i vantaggi di tutte le funzionalità di Defender per le cose, è necessario creare, configurare e usare i moduli di sicurezza gemelli per ogni dispositivo nel servizio. 

## <a name="create-defenderiotmicroagent-module-twin"></a>Creare il modulo DefenderIotMicroAgent gemello 

I dispositivi gemelli del modulo **DefenderIotMicroAgent** possono essere creati modificando manualmente ogni modulo gemello per includere configurazioni specifiche per ogni dispositivo. 

Per creare manualmente un nuovo modulo **DefenderIotMicroAgent** gemello per un dispositivo: 

1. Nell'hub Internet delle cose individuare e selezionare il dispositivo in cui creare un modulo di sicurezza gemello. 

1. Selezionare **Aggiungi identità modulo**. 

1. Nel campo **nome identità modulo**   immettere  `DefenderIotMicroAgent` . 

1. Selezionare  **Salva**. 

## <a name="verify-the-creation-of-a-module-twin"></a>Verificare la creazione di un modulo gemello 

Per verificare se è presente un modulo gemello di sicurezza per un dispositivo specifico: 

1. Nell'hub Azure Internet Explorer, selezionare **dispositivi** Internet   dal menu **Esplora**   . 

1. Immettere l'ID del dispositivo oppure selezionare un'opzione nel campo **query Device** e selezionare **query Devices**.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/iot-devices.png" alt-text="Selezionare i dispositivi di query per ottenere un elenco dei dispositivi.":::

1. Selezionare il dispositivo e aprire la pagina dei **Dettagli del dispositivo** . 

1. Selezionare il menu **identità moduli**   e verificare l'esistenza del modulo **DefenderIotMicroAgent** nell'elenco delle identità del modulo associate al dispositivo.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/device-details-module.png" alt-text="Selezionare identità modulo dalla scheda.":::

## <a name="next-steps"></a>Passaggi successivi 

Passare all'articolo successivo per informazioni su come [analizzare le raccomandazioni sulla sicurezza](quickstart-investigate-security-recommendations.md).
