---
title: Gestire i sensori nel portale di Defender per IoT
description: Informazioni su come eseguire l'onboard, visualizzare e gestire i sensori nel portale di Defender per IoT.
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: f407a65f60a1b969f17ebe00be39a888a09ec83d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752715"
---
# <a name="manage-sensors-in-the-defender-for-iot-portal"></a>Gestire i sensori nel portale di Defender per IoT

Questo articolo descrive come eseguire l'onboard, visualizzare e gestire i sensori nel portale [di Defender per IoT.](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)

## <a name="onboard-sensors"></a>Eseguire l'onboarding dei sensori

È possibile eseguire l'onboarding di un sensore registrandolo Azure Defender per IoT e scaricando un file di attivazione del sensore.

### <a name="register-the-sensor"></a>Registrare il sensore

Per eseguire la registrazione:

1. Passare alla **pagina iniziale** nel portale di Defender per [IoT.](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)
1. Selezionare **Onboard sensor (Onboard sensor).**
1. Creare un nome di sensore. È consigliabile includere l'indirizzo IP del sensore installato come parte del nome o usare un nome facilmente identificabile. Ciò garantisce un rilevamento più semplice e una denominazione coerente tra il nome di registrazione nel portale di Azure [Defender per IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) e l'INDIRIZZO IP del sensore distribuito visualizzato nella console del sensore.
1. Associare il sensore a una sottoscrizione di Azure.
1. Scegliere una modalità di connessione del sensore usando l'interruttore **Connesso al** cloud. Se l'interruttore è attivato, il sensore è connesso al cloud. Se l'interruttore è disattivato, il sensore viene gestito in locale.

   - **Sensori connessi al cloud:** le informazioni rilevate dal sensore vengono visualizzate nella console dei sensori. Le informazioni sugli avvisi vengono recapitate tramite un hub IoT e possono essere condivise con altri servizi di Azure, ad esempio Azure Sentinel. È anche possibile eseguire il push dei pacchetti di intelligence per le minacce dal portale Azure Defender per IoT ai sensori. Al contrario, quando il sensore non è connesso al cloud, è necessario scaricare i pacchetti di intelligence per le minacce e quindi caricarli nei sensori aziendali. Per consentire a Defender per IoT di eseguire il push dei pacchetti nei sensori, abilitare l'interruttore **Aggiornamenti automatici di Threat Intelligence.** Per altre informazioni, vedere Ricerca e pacchetti [di Intelligence per le minacce.](how-to-work-with-threat-intelligence-packages.md)
   Scegliere un hub IoT da utilizzare come gateway tra questo sensore e il Azure Defender per IoT remoto. Definire un nome e una zona del sito. È anche possibile aggiungere tag descrittivi. Il nome del sito, la zona e i tag sono voci descrittive nella pagina [Siti e sensori](#view-onboarded-sensors).

   - **Sensori gestiti in locale:** le informazioni rilevate dai sensori vengono visualizzate nella console dei sensori. Se si lavora in una rete air-gapped e si vuole una visualizzazione unificata di tutte le informazioni rilevate da più sensori gestiti in locale, usare la console di gestione locale.

   Per i sensori connessi al cloud, il nome definito durante l'onboarding è il nome visualizzato nella console dei sensori. Non è possibile modificare questo nome direttamente dalla console. Per i sensori gestiti in locale, il nome applicato durante l'onboarding verrà archiviato in Azure, ma può essere aggiornato nella console dei sensori.

### <a name="download-the-sensor-activation-file"></a>Scaricare il file di attivazione del sensore

Il file di attivazione del sensore contiene istruzioni sulla modalità di gestione del sensore. Scaricare un file di attivazione univoco per ogni sensore distribuito. Un utente che accede alla console del sensore per la prima volta carica il file di attivazione nel sensore.

Per scaricare un file di attivazione:

1. Nella pagina **Onboard Sensor (Sensore di onboard)** selezionare **download activation file (Scarica file di attivazione).**
1. Rendere il file accessibile all'utente che accede per la prima volta alla console del sensore.

## <a name="view-onboarded-sensors"></a>Visualizzare i sensori di cui è stato fatto l'onboarded

Nel portale [di Defender per IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)è possibile visualizzare informazioni operative importanti sui sensori di cui è stato onboarded.

1. Selezionare **Siti e sensori.** La pagina mostra quanti sensori sono stati onboarded, il numero di sensori connessi al cloud e gestiti localmente, nonché:

- il nome del sensore assegnato durante l'onboarding.
- tipo di connessione (connesso al cloud o gestito localmente)
- la zona associata al sensore.
- Versione del sensore installata
- Stato della connessione del sensore al cloud.
- L'ultima volta che è stato rilevato il sensore che si connette al cloud.

## <a name="manage-onboarded-sensors"></a>Gestire i sensori onboarded

Usare il [portale di Defender per IoT per](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) le attività di gestione correlate ai sensori.

I sensori onboarded possono essere visualizzati nella **pagina Siti e** sensori. È anche possibile modificare le informazioni del sensore da questa pagina.

### <a name="export-sensor-details"></a>Esportare i dettagli del sensore

Per esportare le informazioni del  sensore onboarded, selezionare l'icona Esporta nella parte superiore della **pagina Siti e** sensori.

### <a name="edit-sensor-zone-details"></a>Modificare i dettagli della zona del sensore

Usare le **opzioni di modifica Siti e sensori** per modificare il nome e l'area del sensore.

Per modificare:

1. Selezionare i **puntini di sospensione** (**...**) per il sensore da modificare.
1. Selezionare **Modifica**.
1. Aggiornare la zona del sensore o crearne una nuova.

### <a name="delete-a-sensor"></a>Eliminare un sensore

Se si elimina un sensore connesso al cloud, le informazioni non verranno inviate all'hub IoT. Eliminare i sensori connessi in locale quando non si lavora più con essi.

Per eliminare un sensore:

1. Selezionare i puntini di sospensione (**...**) per il sensore da eliminare.
1. Confermare l'eliminazione.

### <a name="reactivate-a-sensor"></a>Riattivare un sensore 

Potrebbe essere necessario riattivare il sensore perché si vuole:

- **Lavorare in modalità connessa** al cloud anziché in modalità gestita in locale: dopo la riattivazione, i rilevamenti dei sensori vengono visualizzati nel sensore e le informazioni sugli avvisi appena rilevate vengono recapitate tramite l'hub IoT. Queste informazioni possono essere condivise con altri servizi di Azure, ad esempio Azure Sentinel.

- **Lavorare in modalità gestita in locale anziché** in modalità connessa al cloud: dopo la riattivazione, le informazioni di rilevamento dei sensori vengono visualizzate solo nel sensore.

- **Associare il sensore a un nuovo hub IoT:** a tale scopo, registrare nuovamente il sensore con un nuovo hub e quindi scaricare un nuovo file di attivazione.

Per riattivare un sensore:

1. Passare alla **pagina Sites and Sensors** (Siti e sensori) nel [portale di Defender per IoT.](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)

2. Selezionare il sensore per cui si vuole caricare un nuovo file di attivazione.

3. Eliminare il sensore.

4. Onboardre di nuovo il sensore nella nuova modalità o con un nuovo hub IoT selezionando **Onboard a sensor (Onboard** di un sensore) Attività iniziali pagina.

5. Scaricare il file di attivazione.

1. Accedere alla console del sensore Defender per IoT.

7. Nella console del sensore selezionare **Impostazioni di sistema** e quindi **riattivazione.**

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Caricare il file di attivazione per riattivare il sensore.":::

8. Selezionare **Carica** e selezionare il file salvato nella pagina Onboard sensor (Carica sensore).

9. Selezionare **Attiva**.

## <a name="next-steps"></a>Passaggi successivi

[Attivare e configurare il sensore](how-to-activate-and-set-up-your-sensor.md)
