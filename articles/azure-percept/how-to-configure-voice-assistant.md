---
title: Configurare un'applicazione di Assistente vocale usando l'hub Azure
description: Configurare un'applicazione di Assistente vocale usando l'hub Azure
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: ec3e06b2d161785b5e6978cdf4cc6415fc0eb592
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663084"
---
# <a name="configure-voice-assistant-application-using-azure-iot-hub"></a>Configurare un'applicazione di Assistente vocale usando l'hub Azure

Questo articolo descrive come configurare l'applicazione di Assistente vocale usando l'hub Internet. Per un'esercitazione dettagliata che illustra il processo di creazione di un assistente vocale usando il modello demo, vedere [creare un assistente vocale senza codice con Azure Percept studio e Azure Percept audio](./tutorial-no-code-speech.md).

## <a name="update-your-voice-assistant-configuration"></a>Aggiornare la configurazione dell'Assistente vocale

1. Aprire il [portale di Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) e digitare **Hub** Internet nella barra di ricerca. Fare clic sull'icona per aprire la pagina dell'hub Internet delle cose.

1. Nella pagina hub tutto selezionare l'hub Internet in cui è stato effettuato il provisioning del dispositivo.

1. Selezionare **IOT Edge** in **gestione automatica dispositivi** nel menu di spostamento a sinistra per visualizzare tutti i dispositivi connessi all'hub Internet.

1. Selezionare il dispositivo in cui è stata distribuita l'applicazione di Assistente vocale.

1. Fare clic su **imposta moduli**.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/set-modules.png" alt-text="Immagine.":::

1. Verificare che sia presente la voce seguente nella sezione **container Registry Credentials** . Se necessario, aggiungere le credenziali.

    |Nome|Indirizzo|Username|Password|
    |----|-------|--------|--------|
    |azureedgedevices|azureedgedevices.azurecr.io|devkitprivatepreviewpull|

1. Nella sezione **moduli IOT Edge** selezionare **azureearspeechclientmodule**.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/modules.png" alt-text="Immagine.":::

1. Fare clic sulla scheda **Impostazioni modulo** . Verificare la seguente configurazione:

    |URI immagine|Criteri di riavvio|Stato desiderato|
    |---------|--------------|--------------|
    |azureedgedevices.azurecr.io/azureearspeechclientmodule:preload-devkit |always|in esecuzione|

    Se le impostazioni non corrispondono, modificarle e fare clic su **Aggiorna**.

1. Fare clic sulla scheda **variabili di ambiente** . Verificare che non siano state definite variabili di ambiente.

1. Fare clic sulla scheda **Crea opzioni del contenitore** . Verificare che le impostazioni di **Hostconfig** corrispondano a quelle mostrate di seguito. Se non corrispondono, aggiornare le impostazioni.

    ```
    {
        "HostConfig": {
            "Privileged": true,
            "Binds": [
                "/dev:/dev"
            ]
        }
    }
    ```

1. Fare clic sulla scheda **Impostazioni gemelli del modulo** . Aggiornare la sezione **speechConfigs** come segue:

    ```
    "speechConfigs": {
        "appId": "<Application id for custom command project>",
        "key": "<Speech Resource key for custom command project>",
        "region": "<Region for the speech service>",
        "keywordModelUrl": "https://aedspeechscenarios.blob.core.windows.net/keyword-tables/computer.table",
        "keyword": "computer"
    }
    ```

    > [!NOTE]
    > La parola chiave usata in precedenza è una parola chiave predefinita disponibile pubblicamente. Se si vuole usare il proprio, è possibile aggiungere una parola chiave personalizzata caricando un file di tabella creato nell'archivio BLOB. L'archiviazione BLOB deve essere configurata con accesso al contenitore anonimo o accesso BLOB anonimo.

## <a name="how-to-find-out-appid-key-and-region"></a>Come trovare appId, chiave e area

Per individuare l' **AppID**, la **chiave** e l' **area**, passare a [speech studio](https://speech.microsoft.com/):

1. Accedere e selezionare la risorsa vocale appropriata.
1. Nella home page **speech studio** fare clic su **comandi personalizzati** in **assistenti vocali**.
1. Selezionare il progetto di destinazione.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/project.png" alt-text="Immagine.":::

1. Fare clic su **Impostazioni** nel pannello di menu a sinistra.
1. **AppID** e **Key** saranno disponibili nella scheda Impostazioni **generali** .

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/general-settings.png" alt-text="Immagine.":::

1. Per trovare la propria **area**, aprire la scheda **risorse Luis** all'interno delle impostazioni. La selezione della **risorsa di creazione** conterrà informazioni sull'area.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/luis-resources.png" alt-text="Immagine.":::

1. Dopo aver immesso le informazioni di **speechConfigs** , fare clic su **Aggiorna**.

1. Fare clic sulla scheda **Route** nella parte superiore della pagina **set Modules** . Assicurarsi di disporre di una route con il valore seguente:

    ```
    FROM /messages/modules/azureearspeechclientmodule/outputs/* INTO $upstream
    ```

    Aggiungere la route se non esiste.

1. Fare clic su **Rivedi e crea**.

1. Fare clic su **Crea**.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver aggiornato la configurazione dell'Assistente vocale, tornare alla demo in Azure Percept Studio per interagire con l'applicazione.