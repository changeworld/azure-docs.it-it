---
title: Caricare e gestire i sensori nel portale Defender per le cose
description: Informazioni su come eseguire l'onboarding, visualizzare e gestire i sensori in Defender per il portale.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/27/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: a763d8b65049cd9f301379c2c038a1d799114653
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839805"
---
# <a name="onboard-and-manage-sensors-in-the-defender-for-iot-portal"></a>Caricare e gestire i sensori nel portale Defender per le cose

Questo articolo descrive come eseguire l'onboarding, visualizzare e gestire i sensori nel portale Defender for Internet.

## <a name="onboard-sensors"></a>Eseguire l'onboarding dei sensori

Il caricamento di un sensore viene registrato con Azure Defender per l'IT e il download di un file di attivazione del sensore.

### <a name="register-the-sensor"></a>Registrare il sensore

Per eseguire la registrazione:

1. Passare alla pagina **iniziale** nel portale Defender for Internet.
1. Selezionare **onboarding Sensor**.
1. Creare un nome del sensore. È consigliabile includere l'indirizzo IP del sensore installato come parte del nome o usare un nome facilmente identificabile. In questo modo sarà più semplice tenere traccia e la denominazione coerente tra il nome di registrazione nel portale di Azure Defender for Internet e l'indirizzo IP del sensore distribuito visualizzato nella console del sensore.
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

Nel portale Defender for Internet è possibile visualizzare le informazioni di base sui sensori caricati. 

1. Selezionare **siti e sensori**.
1. Nella pagina **siti e sensori** usare gli strumenti di filtro e ricerca per trovare le informazioni sui sensori necessarie.

Le informazioni disponibili includono:

- Quanti sensori sono stati caricati
- Il numero di sensori che sono connessi al cloud e gestiti localmente
- Hub associato a un sensore caricato
- Dettagli aggiunti a un sensore, ad esempio il nome assegnato al sensore durante l'onboarding, la zona associata al sensore o altre informazioni descrittive aggiunte con i tag

## <a name="manage-onboarded-sensors"></a>Gestire i sensori caricati

Per le attività di gestione correlate ai sensori è possibile usare il portale di Defender for Internet.

### <a name="export"></a>Esportazione

Per esportare le informazioni sul sensore caricato, selezionare l'icona **Esporta** nella parte superiore della pagina **siti e sensori** .

### <a name="edit"></a>Modifica

Usare gli strumenti di modifica di **siti e sensori** per aggiungere e modificare il nome del sito, la zona e i tag.

### <a name="delete"></a>Delete

Se si elimina un sensore connesso al cloud, le informazioni non verranno inviate all'hub Internet delle cose. Eliminare i sensori connessi localmente quando non vengono più elaborati.

Per eliminare un sensore:

1. Selezionare i puntini di sospensione (**...**) per il sensore che si desidera eliminare. 
1. Confermare l'eliminazione.

### <a name="reactivate"></a>Riattiva

Potrebbe essere necessario aggiornare la modalità in cui il sensore è gestito. Ad esempio:

- **Usare la modalità connessa al cloud invece della modalità gestita localmente**: per eseguire questa operazione, aggiornare il file di attivazione per il sensore connesso localmente con un file di attivazione per un sensore connesso al cloud. Dopo la riattivazione, i rilevamenti dei sensori vengono visualizzati sia nel sensore che nel Defender per il portale. Dopo che il file di riattivazione è stato caricato correttamente, le informazioni di avviso appena rilevate vengono inviate ad Azure.

- **Lavorare in modalità connessa localmente anziché in modalità connessa al cloud**: per eseguire questa operazione, aggiornare il file di attivazione per un sensore connesso al cloud con un file di attivazione per un sensore gestito localmente. Dopo la riattivazione, le informazioni di rilevamento del sensore vengono visualizzate solo nel sensore.

- **Associare il sensore a un nuovo hub** Internet: per eseguire questa operazione, ripetere la registrazione del sensore e caricare un nuovo file di attivazione.

Per riattivare un sensore:

1. Passare alla pagina **siti e sensori** nel portale Defender for Internet.

2. Selezionare il sensore per il quale si vuole caricare un nuovo file di attivazione.

3. Eliminare il sensore.

4. Caricare nuovamente il sensore dalla pagina **onboarding** nella nuova modalità o con un nuovo hub Internet.

5. Scaricare il file di attivazione dalla pagina **Scarica file di attivazione** .

6. Accedere al Defender per la console del sensore Internet.

7. Nella console del sensore selezionare **impostazioni di sistema** , quindi selezionare **riattivazione**.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Caricare il file di attivazione per riattivare il sensore.":::

8. Selezionare **carica** e selezionare il file salvato.

9. Selezionare **Attiva**. 

## <a name="see-also"></a>Vedere anche

[Attivare e configurare il sensore](how-to-activate-and-set-up-your-sensor.md)
