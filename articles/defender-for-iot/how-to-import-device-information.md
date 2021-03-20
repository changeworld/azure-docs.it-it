---
title: Importare informazioni sui dispositivi
description: Il Defender per i sensori Internet monitora e analizza il traffico con mirroring. In questi casi, potrebbe essere necessario importare i dati per arricchire le informazioni sui dispositivi già rilevati.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 333ffbf4107dfd005ba7e7fae6a079a618e0c645
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100522241"
---
# <a name="import-device-information-to-a-sensor"></a>Importare le informazioni sul dispositivo in un sensore

Un Defender di Azure per il sensore di Internet delle cose monitora e analizza il traffico con mirroring. In alcuni casi, a causa di criteri di configurazione di rete specifici dell'organizzazione, alcune informazioni potrebbero non essere trasmesse.

In questi casi, potrebbe essere necessario importare i dati per arricchire le informazioni sui dispositivi già rilevati. Sono disponibili due opzioni per l'importazione di informazioni nei sensori:

- **Importa dalla mappa**: aggiornare il nome del dispositivo, il tipo, il gruppo o il livello Purdue alla mappa.

- **Importa da impostazioni di importazione**: importa il sistema operativo del dispositivo, l'indirizzo IP, il livello di patch o lo stato di autorizzazione.

## <a name="import-from-the-map"></a>Importa dalla mappa

Questa sezione descrive come importare i nomi dei dispositivi, i tipi, i gruppi o i livelli Purdue nella mappa del dispositivo. Questa operazione viene eseguita dalla mappa.

Ecco i requisiti di importazione:

- **Nomi**: possono contenere fino a 30 caratteri.

- **Tipo** o **livello Purdue**: usare le opzioni visualizzate nella finestra di dialogo **proprietà del dispositivo** . Fare clic con il pulsante destro del mouse sul dispositivo e scegliere **Visualizza proprietà**.

- **Gruppo di dispositivi**: creare un nuovo gruppo di un massimo di 30 caratteri. 

> [!NOTE]
> Per evitare conflitti, non importare i dati esportati da un sensore a un altro.

Per importare:

1. Nel menu laterale selezionare **dispositivi**.

2. Nell'angolo in alto a destra della finestra **dispositivi** selezionare :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="Screenshot della finestra del dispositivo.":::

3. Selezionare **Esporta dispositivi**. Nel file esportato viene visualizzata una vasta gamma di informazioni. Queste informazioni includono i protocolli usati dal dispositivo e lo stato di autorizzazione del dispositivo.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="Informazioni contenute nel file esportato.":::

4. Nel file CSV modificare solo il nome, il tipo, il gruppo e il livello Purdue del dispositivo. Salvare quindi il file. 

   Usare gli standard di capitalizzazione mostrati nel file esportato. Per il livello Purdue, ad esempio, usare tutte le maiuscole e minuscole.

5. Dal menu a discesa **Importa/Esporta** della finestra del **dispositivo** Selezionare **Importa dispositivi**.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Importare i dispositivi tramite la finestra del dispositivo.":::

6. Selezionare **Importa dispositivi** e selezionare il file CSV che si vuole importare. I messaggi di stato dell'importazione vengono visualizzati sullo schermo finché la finestra di dialogo **Importa dispositivi** non viene chiusa.

## <a name="import-from-import-settings"></a>Importa da impostazioni di importazione

Questa sezione descrive come importare l'indirizzo IP del dispositivo, il sistema operativo, il livello di patch o lo stato di autorizzazione nella mappa del dispositivo. A tale scopo, utilizzare la finestra di dialogo **Importa impostazioni** .

Per importare l'indirizzo IP, il sistema operativo e il livello di patch:

1. Scaricare il file [devices_info_2.2.8 e up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) da [Help Center](https://cyberx-labs.zendesk.com/hc/en-us) e immettere le informazioni nel modo seguente:

   - **Indirizzo IP**: immettere l'indirizzo IP del dispositivo.

   - **Sistema operativo**: selezionare dall'elenco a discesa.

   - **Ultimo aggiornamento**: usare il formato aaaa-mm-gg.

     :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="Schermata delle opzioni.":::

2. Nel menu laterale selezionare **Importa impostazioni**.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Importare le impostazioni.":::

3. Per caricare la configurazione richiesta, nella sezione **informazioni sul dispositivo** selezionare **Aggiungi** e caricare il file CSV preparato.

Per importare lo stato di autorizzazione:

1. Scaricare e salvare il file di [authorized_devices.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) da Defender per il centro informazioni. Verificare che il file sia stato salvato come CSV.

2. Immettere le informazioni seguenti:

   - **Indirizzo IP**: indirizzo IP del dispositivo.

   - **Nome**: il nome del dispositivo autorizzato. Assicurarsi che i nomi siano accurati. I nomi assegnati ai dispositivi nell'elenco importato sovrascrivono i nomi visualizzati nella mappa del dispositivo.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="File di Excel con l'elenco dei dispositivi importati.":::

3. Nel menu laterale selezionare **Importa impostazioni**.

4. Nella sezione **dispositivi autorizzati** selezionare **Aggiungi** e caricare il file CSV salvato.

Quando le informazioni vengono importate, si ricevono avvisi relativi a dispositivi non autorizzati per tutti i dispositivi che non vengono visualizzati nell'elenco.

## <a name="import-device-information-to-the-sensor"></a>Importare le informazioni sul dispositivo nel sensore

Il sensore monitora e analizza il traffico con mirroring. In alcuni casi, a causa di criteri di configurazione di rete specifici dell'organizzazione, alcune informazioni potrebbero non essere trasmesse.

In questi casi, potrebbe essere necessario importare i dati per arricchire le informazioni sui dispositivi già rilevati. Sono disponibili due opzioni per l'importazione di informazioni nei sensori:

- **Importa dalla mappa**: aggiornare il nome del dispositivo, il tipo, il gruppo o il livello Purdue alla mappa.

- **Importa da impostazioni di importazione**: importa il sistema operativo del dispositivo, l'indirizzo IP, il livello di patch o lo stato di autorizzazione.

### <a name="import-from-the-map"></a>Importa dalla mappa

Questa sezione descrive come importare i nomi dei dispositivi, i tipi, i gruppi o i livelli Purdue nella mappa del dispositivo. Questa operazione viene eseguita dalla mappa.

Ecco i requisiti di importazione:

- **Nomi**: possono contenere fino a 30 caratteri.

- **Tipo** o **livello Purdue**: usare le opzioni visualizzate nella finestra di dialogo **proprietà del dispositivo** . Fare clic con il pulsante destro del mouse sul dispositivo e scegliere **Visualizza proprietà**.

- **Gruppo di dispositivi**: creare un nuovo gruppo di un massimo di 30 caratteri. 

> [!NOTE]
> Per evitare conflitti, non importare i dati esportati da un sensore a un altro.

Per importare:

1. Nel menu laterale selezionare **dispositivi**.

2. Nell'angolo in alto a destra della finestra **dispositivi** selezionare :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="Finestra da cui scegliere il dispositivo.":::

3. Selezionare **Esporta dispositivi**. Nel file esportato viene visualizzata una vasta gamma di informazioni. Gli esempi includono i protocolli usati dal dispositivo e lo stato di autorizzazione del dispositivo.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="Informazioni contenute nel file esportato.":::

4. Nel file CSV modificare solo il nome, il tipo, il gruppo e il livello Purdue del dispositivo. Salvare quindi il file. 

   Usare gli standard di capitalizzazione mostrati nel file esportato. Per il livello Purdue, ad esempio, usare tutte le maiuscole e minuscole.

5. Dal menu a discesa **Importa/Esporta** della finestra del **dispositivo** Selezionare **Importa dispositivi**.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Importare i dispositivi.":::

6. Selezionare **Importa dispositivi** e selezionare il file CSV che si vuole importare. I messaggi di stato dell'importazione vengono visualizzati sullo schermo finché la finestra di dialogo **Importa dispositivi** non viene chiusa.

### <a name="import-from-import-settings"></a>Importa da impostazioni di importazione 

Questa sezione descrive come importare l'indirizzo IP del dispositivo, il sistema operativo, il livello di patch o lo stato di autorizzazione nella mappa del dispositivo. A tale scopo, utilizzare la finestra di dialogo **Importa impostazioni** .

Per importare l'indirizzo IP, il sistema operativo e il livello di patch:

1. Scaricare il file [devices_info_2.2.8 e up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) da [Help Center](https://cyberx-labs.zendesk.com/hc/en-us) e immettere le informazioni nel modo seguente:

   - **Indirizzo IP**: indirizzo IP del dispositivo.

   - **Sistema operativo**: selezionare dall'elenco a discesa.

   - **Data ultimo aggiornamento**: usare il formato aaaa-mm-gg.

    :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="Contenuto sullo schermo.":::

2. Nel menu laterale selezionare **Importa impostazioni**.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Compilare la schermata impostazioni di importazione.":::

3. Per caricare la configurazione richiesta, nella sezione **informazioni sul dispositivo** selezionare **Aggiungi** e caricare il file CSV preparato.

Per importare lo stato di autorizzazione:

1. Scaricare e salvare il file di [examples.csvauthorized_devices ](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) dal Defender per il centro informazioni. Verificare che il file sia stato salvato come CSV.

2. Immettere le informazioni seguenti:

   - **Indirizzo IP**: indirizzo IP del dispositivo.

   - **Nome**: il nome del dispositivo autorizzato. Verificare che i nomi siano accurati. I nomi assegnati ai dispositivi nell'elenco importato sovrascrivono i nomi visualizzati nella mappa del dispositivo.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="Elenco di importazione nella mappa del dispositivo.":::

3. Nel menu laterale selezionare **Importa impostazioni**.

4. Nella sezione **dispositivi autorizzati** selezionare **Aggiungi** e caricare il file CSV salvato.

Quando le informazioni vengono importate, si ricevono avvisi relativi a dispositivi non autorizzati per tutti i dispositivi che non vengono visualizzati nell'elenco.

## <a name="see-also"></a>Vedi anche

[Controllare quale traffico viene monitorato](how-to-control-what-traffic-is-monitored.md)

[Esaminare i rilevamenti dei sensori in un inventario dei dispositivi](how-to-investigate-sensor-detections-in-a-device-inventory.md)
