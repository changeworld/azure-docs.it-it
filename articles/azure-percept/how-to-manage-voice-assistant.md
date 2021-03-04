---
title: Configurare un'applicazione di Assistente vocale in Azure Percept Studio
description: Configurare un'applicazione di Assistente vocale in Azure Percept Studio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: 5e41dea3b47c608b9e82ac57fa1cfe5247ea6cc2
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099013"
---
# <a name="managing-your-voice-assistant"></a>Gestione dell'Assistente vocale

Questo articolo descrive come configurare la parola chiave e i comandi dell'applicazione Voice Assistant in [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819). Per istruzioni su come configurare la parola chiave nell'hub degli indirizzi Internet anziché nel portale, vedere questo [articolo sulle procedure](./how-to-configure-voice-assistant.md).

Se non è stata ancora creata un'applicazione per l'Assistente vocale, vedere [creare un assistente vocale senza codice con Azure Percept studio e Azure Percept audio](./tutorial-no-code-speech.md).

## <a name="keyword-configuration"></a>Configurazione parole chiave

Una parola chiave è una parola o una frase breve usata per attivare un assistente vocale. Ad esempio, "Hey Cortana" è la parola chiave per Cortana Assistant. L'attivazione vocale consente agli utenti di iniziare a interagire con il prodotto senza alcuna pratica, semplicemente pronunciando la parola chiave. Poiché il prodotto è costantemente in ascolto per la parola chiave, tutto il suono viene elaborato localmente nel dispositivo fino a quando non si verifica un rilevamento per garantire che i dati degli utenti rimangano i più privati possibile.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>Configurazione nella finestra demo di Assistente vocale

1. Fare clic su **modifica** accanto a **parola chiave personalizzata** nella pagina demo.

    :::image type="content" source="./media/manage-voice-assistant/hospitality-demo.png" alt-text="Screenshot della finestra demo dell'ospitalità.":::

    Se la pagina demo non è aperta, passare alla pagina del dispositivo (vedere di seguito) e fare clic su **testare l'Assistente vocale** in **azioni** per accedere alla demo.

1. Selezionare una delle parole chiave disponibili e fare clic su **Salva** per applicare le modifiche.

1. I tre indicatori LED sul dispositivo audio Azure Percept cambieranno in blu chiaro (nessun lampeggio) al termine della configurazione e l'Assistente vocale è pronto per l'uso.

### <a name="configuration-within-the-device-page"></a>Configurazione nella pagina del dispositivo

1. Nella pagina Panoramica di [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)fare clic su **dispositivi** nel riquadro del menu a sinistra.

    :::image type="content" source="./media/manage-voice-assistant/portal-overview-devices.png" alt-text="Screenshot della pagina di Panoramica di Azure Percept studio con i dispositivi evidenziati.":::

1. Selezionare il dispositivo in cui è stata distribuita l'applicazione di Assistente vocale.

1. Aprire la scheda **sintesi vocale** .

    :::image type="content" source="./media/manage-voice-assistant/device-page.png" alt-text="Screenshot della pagina del dispositivo perimetrale con la scheda vocale evidenziata.":::

1. Fare clic su **modifica** accanto a **parola chiave**.

    :::image type="content" source="./media/manage-voice-assistant/change-keyword-device.png" alt-text="Screenshot delle azioni disponibili per la soluzione vocale.":::

1. Selezionare una delle parole chiave disponibili e fare clic su **Salva** per applicare le modifiche.

1. I tre indicatori LED sul dispositivo audio Azure Percept cambieranno in blu chiaro (nessun lampeggio) al termine della configurazione e l'Assistente vocale è pronto per l'uso.

## <a name="create-a-custom-keyword"></a>Creare una parola chiave personalizzata

Con [speech studio](https://speech.microsoft.com/)è possibile creare una parola chiave personalizzata per l'Assistente vocale. Per eseguire il training di un modello di parola chiave personalizzata di base sono necessari fino a 30 minuti.

Per istruzioni sulla creazione di una parola chiave personalizzata, seguire la [documentazione di speech studio](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) . Una volta configurata, la parola chiave New sarà disponibile nel progetto di Santa Cruz Portal per l'uso con l'applicazione Voice Assistant.

## <a name="commands-configuration"></a>Configurazione comandi

I comandi personalizzati semplificano la creazione di app avanzate per i comandi vocali ottimizzate per esperienze di interazione vocali-First. I comandi personalizzati sono ideali per gli scenari di completamento delle attività o di comando e controllo.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>Configurazione nella finestra demo di Assistente vocale

1. Fare clic su **Cambia** accanto a **comando personalizzato** nella pagina demo. Se la pagina demo non è aperta, passare alla pagina del dispositivo (vedere di seguito) e fare clic su **testare l'Assistente vocale** in **azioni** per accedere alla demo.

1. Selezionare uno dei comandi personalizzati disponibili e fare clic su **Salva** per applicare le modifiche.

### <a name="configuration-within-the-device-page"></a>Configurazione nella pagina del dispositivo

1. Nella pagina Panoramica di [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)fare clic su **dispositivi** nel riquadro del menu a sinistra.

1. Selezionare il dispositivo in cui è stata distribuita l'applicazione di Assistente vocale.

1. Aprire la scheda **sintesi vocale** .

1. Fare clic su **Cambia** accanto a **comando**.

1. Selezionare uno dei comandi personalizzati disponibili e fare clic su **Salva** per applicare le modifiche.

## <a name="create-custom-commands"></a>Creare comandi personalizzati

Con [speech studio](https://speech.microsoft.com/)è possibile creare comandi personalizzati per l'esecuzione dell'Assistente vocale.

Per istruzioni sulla creazione di comandi personalizzati, seguire la [documentazione di speech studio](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-custom-commands-application) . Una volta configurati, i nuovi comandi saranno disponibili in Azure Percept Studio per l'uso con l'applicazione dell'Assistente vocale.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un'applicazione di Assistente vocale, provare a sviluppare una soluzione per la [visione senza codice](./tutorial-nocode-vision.md) con Azure Percept dk.