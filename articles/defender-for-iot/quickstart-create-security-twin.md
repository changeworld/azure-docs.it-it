---
title: Creare un modulo di sicurezza gemello
description: In questo argomento di avvio rapido sono disponibili informazioni su come creare un modulo gemello di Defender per IoT per l'uso con Azure Defender per IoT.
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
ms.date: 1/21/2021
ms.author: shhazam
ms.openlocfilehash: 68d137caeed91e9dea2dbf3883929d85c25382aa
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523261"
---
# <a name="create-an-azureiotsecurity-module-twin"></a>Creare un modulo gemello azureiotsecurity

Questo argomento di avvio rapido spiega come creare singoli moduli gemelli _azureiotsecurity_ per i nuovi dispositivi oppure creare moduli gemelli in batch per tutti i dispositivi in un hub IoT.

## <a name="understanding-azureiotsecurity-module-twins"></a>Informazioni sui moduli gemelli azureiotsecurity

Per le soluzioni IoT integrate in Azure, i dispositivi gemelli svolgono un ruolo fondamentale sia nella gestione dei dispositivi che nell'automazione dei processi.

Defender for Internet offre un'integrazione completa con la piattaforma di gestione dei dispositivi per Internet delle cose esistente, consentendo di gestire lo stato di sicurezza del dispositivo e di usare le funzionalità di controllo dei dispositivi esistenti.
L'integrazione di Defender per IoT si ottiene usando il meccanismo del dispositivo gemello dell'hub IoT.

Per informazioni sul concetto generale dei moduli gemelli nell'hub IoT di Azure, vedere [Moduli gemelli nell'hub IoT](../iot-hub/iot-hub-devguide-module-twins.md).

Defender per IoT usa il meccanismo dei moduli gemelli, mantenendo un modulo gemello di sicurezza denominato _azureiotsecurity_ per ogni dispositivo.

Il modulo gemello di sicurezza contiene tutte le informazioni pertinenti alla sicurezza del dispositivo per ognuno dei dispositivi.

Per sfruttare al meglio le funzionalità di Defender per le funzionalità di Internet delle cose, è necessario creare, configurare e usare questo modulo di sicurezza gemelli per ogni dispositivo nel servizio.

## <a name="create-azureiotsecurity-module-twin"></a>Creare un modulo gemello azureiotsecurity

I moduli gemelli _azureiotsecurity_ possono essere creati in due modi:

1. [Script batch del modulo](https://aka.ms/iot-security-github-create-module): crea automaticamente il modulo gemello per i nuovi dispositivi o per quelli che ne sono privi usando la configurazione predefinita.
1. Modifica manuale di ogni singolo modulo gemello con configurazioni specifiche per ogni dispositivo.

>[!NOTE]
> L'uso del metodo batch non sovrascrive i moduli gemelli azureiotsecurity esistenti, ma crea SOLO nuovi moduli gemelli per i dispositivi che ne sono privi.

Per informazioni su come modificare o cambiare la configurazione di un modulo gemello esistente, vedere l'articolo sulla [configurazione degli agenti](how-to-agent-configuration.md).

Per creare manualmente un nuovo modulo _azureiotsecurity_ gemello per un dispositivo:

1. Nell'hub IoT trovare e selezionare il dispositivo per cui si vuole creare un modulo gemello di sicurezza.

1. Selezionare nel dispositivo e quindi su **Aggiungi identità modulo**.

1. Nel campo **Nome identità del modulo** immettere **azureiotsecurity**.

1. Selezionare **Salva**.

## <a name="verify-creation-of-a-module-twin"></a>Verificare la creazione di un modulo gemello

Per verificare se è presente un modulo gemello di sicurezza per un dispositivo specifico:

1. Nell'hub IoT di Azure, selezionare **Dispositivi IoT** dal menu **Strumenti di esplorazione**.

1. Immettere l'ID del dispositivo oppure selezionare un'opzione nel **campo query Device** e selezionare **query Devices**.

    :::image type="content" source="./media/quickstart/verify-security-module-twin.png" alt-text="Query su dispositivi":::

1. Selezionare il dispositivo o fare doppio clic per aprire la pagina dei dettagli del dispositivo.

1. Selezionare il menu **Identità del modulo** e verificare l'esistenza del modulo **azureiotsecurity** nell'elenco delle identità dei moduli associate al dispositivo.

    :::image type="content" source="./media/quickstart/verify-security-module-twin-3.png" alt-text="Moduli associati a un dispositivo":::

Per altre informazioni sulla personalizzazione delle proprietà dei moduli gemelli di Defender per IoT, vedere [Configurazione dell'agente](how-to-agent-configuration.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come esaminare le raccomandazioni sulla sicurezza, passare all'articolo successivo:

> [!div class="nextstepaction"]
> [Esaminare le raccomandazioni sulla sicurezza](quickstart-investigate-security-recommendations.md)
