---
title: Caricare e gestire sensori e sottoscrizioni nel portale di Defender per le cose
description: Informazioni su come eseguire l'onboarding, visualizzare e gestire i sensori in Defender per il portale.
ms.date: 2/18/2021
ms.topic: how-to
ms.openlocfilehash: 5c529a5679e8b3d4879b003fa3d168e911f9b518
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781824"
---
# <a name="onboard-and-manage-sensors-and-subscriptions-in-the-defender-for-iot-portal"></a>Caricare e gestire sensori e sottoscrizioni nel portale di Defender per le cose

Questo articolo descrive come eseguire l'onboarding, visualizzare e gestire i sensori nel [portale Defender for](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)Internet.

## <a name="onboard-sensors"></a>Eseguire l'onboarding dei sensori

Il caricamento di un sensore viene registrato con Azure Defender per l'IT e il download di un file di attivazione del sensore.

### <a name="register-the-sensor"></a>Registrare il sensore

Per eseguire la registrazione:

1. Passare alla pagina **iniziale** nel [portale Defender for](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)Internet.
1. Selezionare **onboarding Sensor**.
1. Creare un nome del sensore. È consigliabile includere l'indirizzo IP del sensore installato come parte del nome o usare un nome facilmente identificabile. In questo modo sarà più semplice tenere traccia e la denominazione coerente tra il nome di registrazione nel portale di Azure [Defender for](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) Internet e l'indirizzo IP del sensore distribuito visualizzato nella console del sensore.
1. Associare il sensore a una sottoscrizione di Azure.
1. Scegliere una modalità di gestione dei sensori usando l'interruttore **connesso al cloud** . Se l'interruttore è attivato, il sensore è connesso al cloud. Se l'interruttore è disattivato, il sensore viene gestito localmente.

   - **Sensori connessi al cloud**: le informazioni rilevate dal sensore vengono visualizzate nella console del sensore. Le informazioni sugli avvisi vengono fornite tramite un hub Internet e possono essere condivise con altri servizi di Azure, ad esempio Azure Sentinel.

   - **Sensori gestiti localmente**: le informazioni rilevate dai sensori vengono visualizzate nella console del sensore. Se si lavora in una rete gapped e si vuole una visualizzazione unificata di tutte le informazioni rilevate da più sensori gestiti localmente, usare la console di gestione locale.

   Per i sensori connessi al cloud, il nome definito durante il caricamento è il nome che viene visualizzato nella console del sensore. Non è possibile modificare questo nome direttamente dalla console. Per i sensori gestiti localmente, il nome applicato durante il caricamento viene archiviato in Azure e può essere aggiornato nella console del sensore.

1. Scegliere un hub Internet che funga da gateway tra questo sensore e Azure Defender per tutto.
1. Se il sensore è connesso al cloud, associarlo a un hub Internet e quindi definire un nome e una zona per il sito. È anche possibile aggiungere tag descrittivi. Il nome del sito, la zona e i tag sono voci descrittive nella [pagina siti e sensori](#view-onboarded-sensors).

### <a name="download-the-sensor-activation-file"></a>Scaricare il file di attivazione del sensore

Il file di attivazione del sensore contiene istruzioni sulla modalità di gestione del sensore. È possibile scaricare un file di attivazione univoco per ogni sensore distribuito. Un utente che accede alla console del sensore per la prima volta carica il file di attivazione nel sensore.

Per scaricare un file di attivazione:

1. Nella pagina **sensore di onboarding** selezionare **Scarica file di attivazione**.
1. Rendere il file accessibile per la prima volta all'utente che accede alla console del sensore.

## <a name="view-onboarded-sensors"></a>Visualizza sensori caricati

Nel [portale Defender for](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)Internet è possibile visualizzare le informazioni di base sui sensori caricati.

1. Selezionare **siti e sensori**.
1. Usare gli strumenti di filtro e ricerca per trovare informazioni sul sensore e sull'Intelligence per le minacce necessarie.

- Quanti sensori sono stati caricati
- Il numero di sensori che sono connessi al cloud e gestiti localmente
- Hub associato a un sensore caricato
- Dettagli aggiunti a un sensore, ad esempio il nome assegnato al sensore durante l'onboarding, la zona associata al sensore o altre informazioni descrittive aggiunte con i tag

## <a name="manage-onboarded-sensors"></a>Gestire i sensori caricati

Usare [Defender for](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) Internet per le attività di gestione correlate ai sensori.

I sensori caricati possono essere visualizzati nella pagina **siti e sensori** . È anche possibile modificare le informazioni sui sensori da questa pagina.

### <a name="export-sensor-details"></a>Dettagli esportazione dei sensori

Per esportare le informazioni sul sensore caricato, selezionare l'icona **Esporta** nella parte superiore della pagina **siti e sensori** .

### <a name="edit-sensor-zone-details"></a>Modificare i dettagli della zona dei sensori

Usare le opzioni di modifica dei **siti e dei sensori** per modificare il nome e la zona del sensore.

Per modificare:

1. Selezionare i **puntini** di sospensione (**...**) per il sensore che si desidera modificare.
1. Selezionare **Modifica**.
1. Aggiornare la zona del sensore o creare una nuova zona.

### <a name="delete-a-sensor"></a>Eliminare un sensore

Se si elimina un sensore connesso al cloud, le informazioni non verranno inviate all'hub Internet delle cose. Eliminare i sensori connessi localmente quando non vengono più elaborati.

Per eliminare un sensore:

1. Selezionare i puntini di sospensione (**...**) per il sensore che si desidera eliminare.
1. Confermare l'eliminazione.

### <a name="reactivate-a-sensor"></a>Riattivare un sensore 

Potrebbe essere necessario riattivare il sensore perché si desidera:

- **Lavorare in modalità connessa al cloud invece che in modalità gestita localmente**: dopo la riattivazione, i rilevamenti dei sensori vengono visualizzati nel sensore e le informazioni di avviso appena rilevate vengono recapitate tramite l'hub. Queste informazioni possono essere condivise con altri servizi di Azure, ad esempio Azure Sentinel.

- **Lavorare in modalità gestita localmente anziché in modalità connessa al cloud**: dopo la riattivazione, le informazioni di rilevamento del sensore vengono visualizzate solo nel sensore.

- **Associare il sensore a un nuovo hub** Internet: per eseguire questa operazione, registrare nuovamente il sensore con un nuovo hub e quindi scaricare un nuovo file di attivazione.

Per riattivare un sensore:

1. Passare alla pagina **siti e sensori** nel [portale Defender for](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)Internet.

2. Selezionare il sensore per il quale si vuole caricare un nuovo file di attivazione.

3. Eliminare il sensore.

4. Eseguire di nuovo il caricamento del sensore in modalità nuova o con un nuovo hub Internet, selezionando carica **un sensore** dalla pagina Introduzione.

5. Scaricare il file di attivazione.

1. Accedere al Defender per la console del sensore Internet.

7. Nella console del sensore selezionare **impostazioni di sistema** , quindi selezionare **riattivazione**.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Caricare il file di attivazione per riattivare il sensore.":::

8. Selezionare **carica** e selezionare il file salvato dalla pagina del sensore di onboarding.

9. Selezionare **Attiva**.

## <a name="offboard-a-subscription"></a>Offboard una sottoscrizione

Le sottoscrizioni vengono gestite su base mensile. Quando offboard una sottoscrizione, ti verrà addebitata la sottoscrizione fino alla fine del mese. 

Disinstallare tutti i sensori associati alla sottoscrizione prima di Offboarding la sottoscrizione. Per altre informazioni su come eliminare un sensore, vedere [eliminare un sensore](#delete-a-sensor). 

Per offboard una sottoscrizione:

1. Passare alla pagina dei **prezzi** .
1. Selezionare la sottoscrizione e quindi selezionare l'icona **Elimina** :::image type="icon" source="media/how-to-manage-sensors-on-the-cloud/delete-icon.png" border="false"::: .
1. Nella finestra popup di conferma selezionare la casella di controllo per confermare di avere eliminato tutti i sensori associati alla sottoscrizione.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="Selezionare la casella di controllo e selezionare offboard per offboard il sensore.":::

1. Selezionare il pulsante **offboard** . 

L'ambiente locale non è interessato, ma è necessario disinstallare il sensore dall'ambiente locale oppure riassegnare il sensore a un'altra sottoscrizione, in modo da impedire che i dati correlati vengano propagati alla console di gestione locale. 

## <a name="see-also"></a>Vedi anche

[Attivare e configurare il sensore](how-to-activate-and-set-up-your-sensor.md)
