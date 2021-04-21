---
title: Informazioni sulla messaggistica da cloud a dispositivo dell'hub IoT di Azure | Microsoft Docs
description: Questa guida per gli sviluppatori illustra come usare la messaggistica da cloud a dispositivo con l'hub IoT. Include informazioni sul ciclo di vita dei messaggi e sulle opzioni di configurazione.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.custom: mqtt, devx-track-azurecli
ms.openlocfilehash: 7bb3ca2b31eaef5c0639f30e0f2a329a37dfe7e0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761782"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Inviare messaggi da cloud a dispositivo da un hub IoT

Per inviare notifiche unidiresche a un'app per dispositivi dal back-end della soluzione, inviare messaggi da cloud a dispositivo dall'hub IoT al dispositivo. Per una descrizione delle altre opzioni da cloud a dispositivo supportate da hub IoT di Azure, vedere Indicazioni sulle comunicazioni da cloud a [dispositivo.](iot-hub-devguide-c2d-guidance.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I messaggi da cloud a dispositivo vengono inviati tramite un endpoint con connessione al servizio, */messages/devicebound*. Un dispositivo riceve quindi i messaggi tramite un endpoint specifico del dispositivo, */devices/{deviceId}/messages/devicebound*.

Per impostare come destinazione ogni messaggio da cloud a dispositivo in un singolo dispositivo, l'hub IoT imposta la proprietà **su** */devices/{deviceId}/messages/devicebound*.

Ogni coda di dispositivi contiene al massimo 50 messaggi da cloud a dispositivo. Se si prova a inviare altri messaggi allo stesso dispositivo, viene restituito un errore.

## <a name="the-cloud-to-device-message-life-cycle"></a>Ciclo di vita dei messaggi da cloud a dispositivo

Per garantire il recapito dei messaggi almeno una volta, l'hub IoT rende persistenti i messaggi da cloud a dispositivo nelle code per dispositivo. Perché l'hub IoT rimova i messaggi dalla coda, i dispositivi devono confermare in modo esplicito il *completamento*. Questo approccio garantisce la resilienza rispetto a errori di connettività e del dispositivo.

Il grafico dello stato del ciclo di vita viene visualizzato nel diagramma seguente:

![Ciclo di vita dei messaggi da cloud a dispositivo](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Quando il servizio hub IoT invia un messaggio a un dispositivo, il servizio imposta lo stato del messaggio su *Accodato.* Quando un dispositivo vuole ricevere *un* messaggio, l'hub IoT *blocca* il messaggio impostando lo stato su *Invisibile.* Questo stato consente ad altri thread nel dispositivo di iniziare a ricevere altri messaggi. Quando un thread del dispositivo completa l'elaborazione di un messaggio, invia una notifica all'hub IoT *completando* il messaggio. L'hub IoT imposta quindi lo stato su *Completed*.

Un dispositivo può anche:

* *Rifiutare* il messaggio, in modo che l'hub IoT lo imposta sullo *stato Non recapitato.* I dispositivi che si connettono tramite il protocollo MQTT (Message Queuing Telemetry Transport) non possono rifiutare i messaggi da cloud a dispositivo.

* *Abbandonare* il messaggio, che fa sì che l'hub IoT lo metta di nuovo nella coda, con lo stato impostato su *Accodato.* I dispositivi che si connettono tramite il protocollo MQTT non possono abbandonare i messaggi da cloud a dispositivo.

Un thread potrebbe non riuscire a elaborare un messaggio senza notificare l'hub IoT. In questo caso, i  messaggi passano automaticamente dallo stato Invisibile allo stato *Accodato* dopo un *timeout* di visibilità (o *timeout* di blocco). Il valore di questo timeout è di un minuto e non può essere modificato.

La **proprietà max delivery count** nell'hub IoT determina il numero massimo di volte in cui un messaggio può eseguire la transizione tra gli stati *Accodati* *e Invisibili.* Dopo tale numero di transizioni, l'hub IoT imposta lo stato del messaggio su *Non recapitato.* Analogamente, l'hub IoT imposta lo stato di un messaggio su Messaggi non *recapitati* dopo la scadenza. Per altre informazioni, vedere [Time to live](#message-expiration-time-to-live).

L'articolo Come inviare messaggi da cloud a dispositivo con l'hub [IoT](iot-hub-csharp-csharp-c2d.md) illustra come inviare messaggi da cloud a dispositivo dal cloud e riceverli in un dispositivo.

Un dispositivo in genere completa un messaggio da cloud a dispositivo quando la perdita del messaggio non influisce sulla logica dell'applicazione. Un esempio può essere quando il dispositivo ha salvato in modo permanente il contenuto del messaggio in locale o ha eseguito correttamente un'operazione. Il messaggio potrebbe anche avere informazioni temporanee, la cui perdita non inciderebbe sulla funzionalità dell'applicazione. In alcuni casi, per le attività con esecuzione prolungata è possibile:

* Completare il messaggio da cloud a dispositivo dopo che il dispositivo ha reso persistente la descrizione dell'attività nell'archiviazione locale.

* Inviare al back-end della soluzione una notifica con uno o più messaggi da dispositivo a cloud in diverse fasi di avanzamento dell'attività.

## <a name="message-expiration-time-to-live"></a>Scadenza del messaggio (durata)

Ogni messaggio da cloud a dispositivo ha una scadenza. Questa ora viene impostata in uno dei modi seguenti:

* Proprietà **ExpiryTimeUtc** nel servizio
* L'hub IoT, usando il tempo *di* vita predefinito specificato come proprietà dell'hub IoT

Vedere [Opzioni di configurazione da cloud a dispositivo](#cloud-to-device-configuration-options).

Un modo comune per sfruttare la scadenza di un messaggio ed evitare l'invio di messaggi ai dispositivi disconnessi è impostare un tempo breve *per i valori* in tempo reale. Questo approccio ottiene lo stesso risultato della gestione dello stato di connessione del dispositivo, ma è più efficiente. Quando si richiedono riconoscimenti dei messaggi, l'hub IoT notifica quali dispositivi sono:

* sono abilitati a ricevere messaggi.
* sono offline, oppure l'operazione non è riuscita.

## <a name="message-feedback"></a>Commenti sui messaggi

Quando si invia un messaggio da cloud a dispositivo, il servizio può richiedere il recapito di commenti e suggerimenti per messaggio sullo stato finale del messaggio. A tale scopo, impostare la proprietà dell'applicazione **iothub-ack** nel messaggio da cloud a dispositivo inviato a uno dei quattro valori seguenti:

| Valore della proprietà Ack | Comportamento |
| ------------ | -------- |
| Nessuno     | L'hub IoT non genera un messaggio di feedback (comportamento predefinito). |
| positivo | Se il messaggio da cloud a dispositivo raggiunge lo *stato Completato,* l'hub IoT genera un messaggio di feedback. |
| negativo | Se il messaggio da cloud a dispositivo raggiunge lo stato Messaggi *non* recapitati, l'hub IoT genera un messaggio di feedback. |
| completi     | L'hub IoT genera un messaggio di feedback in entrambi i casi. |

Se il **valore di Ack** *è pieno* e non si riceve un messaggio di feedback, significa che il messaggio di feedback è scaduto. Il servizio non può sapere cosa è successo al messaggio originale. In pratica, un servizio deve garantire che sia possibile elaborare i commenti prima della scadenza. La scadenza massima è di due giorni, che lascia il tempo necessario per eseguire nuovamente il servizio in caso di errore.

Come illustrato in [Endpoint,](iot-hub-devguide-endpoints.md)l'hub IoT invia commenti e suggerimenti tramite un endpoint rivolto al servizio, */messages/servicebound/feedback,* come messaggi. La semantica di ricezione per i commenti è uguale a quella dei messaggi da cloud a dispositivo. Quando è possibile, i commenti sui messaggio vengono riuniti in batch in un unico messaggio con il formato seguente:

| Proprietà     | Descrizione |
| ------------ | ----------- |
| EnqueuedTime | Timestamp che indica quando il messaggio di feedback è stato ricevuto dall'hub |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

Il sistema invierà il feedback quando il batch raggiunge 64 messaggi o in 15 secondi dall'ultimo invio, a seconda di quale sia il primo. 

Il corpo è una matrice serializzata con JSON dei record, ognuno con le proprietà seguenti:

| Proprietà           | Descrizione |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Timestamp che indica quando si è verificato il risultato del messaggio, ad esempio quando l'hub ha ricevuto il messaggio di feedback o il messaggio originale è scaduto. |
| OriginalMessageId  | *MessageId del* messaggio da cloud a dispositivo a cui sono correlate queste informazioni di feedback |
| StatusCode         | Stringa obbligatoria, usata nei messaggi di feedback generati dall'hub IoT: <br/> *Success* <br/> *Scaduta* <br/> *DeliveryCountExceeded* <br/> *Rifiutato* <br/> *Eliminati* |
| Descrizione        | Valori stringa per *StatusCode* |
| DeviceId           | DeviceId *del* dispositivo di destinazione del messaggio da cloud a dispositivo a cui è correlato questo feedback |
| DeviceGenerationId | *DeviceGenerationId del* dispositivo di destinazione del messaggio da cloud a dispositivo a cui è correlato questo feedback |

Perché il messaggio da cloud a dispositivo correla il feedback con il messaggio originale, il servizio deve specificare *un MessageId*.

Il corpo di un messaggio di feedback viene visualizzato nel codice seguente:

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

**Commenti e suggerimenti in sospeso per i dispositivi eliminati**

Quando un dispositivo viene eliminato, vengono eliminati anche eventuali commenti e suggerimenti in sospeso. Il feedback del dispositivo viene inviato in batch. Se un dispositivo viene eliminato nella finestra ristretta (spesso inferiore a 1 secondo) tra il momento in cui il dispositivo conferma la ricezione del messaggio e quando viene preparato il batch di feedback successivo, il feedback non verrà generato.

È possibile risolvere questo comportamento attendendo un periodo di tempo per l'arrivo di commenti in sospeso prima di eliminare il dispositivo. Si presuppone che il feedback dei messaggi correlati andaa perso dopo l'eliminazione di un dispositivo.

## <a name="cloud-to-device-configuration-options"></a>Opzioni di configurazione da cloud a dispositivo

Ogni hub IoT espone le opzioni di configurazione seguenti per la messaggistica da cloud a dispositivo:

| Proprietà                  | Descrizione | Intervallo e valore predefinito |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Durata (TTL) predefinita per i messaggi da cloud a dispositivo | ISO_8601 intervallo massimo di 2 giorni (minimo 1 minuto); impostazione predefinita: 1 ora |
| maxDeliveryCount          | Numero massimo di rescinti per le code da cloud a dispositivo per dispositivo | Da 1 a 100; default: 10 |
| feedback.ttlAsIso8601     | Conservazione per i messaggi di feedback associati al servizio | ISO_8601 intervallo massimo di 2 giorni (minimo 1 minuto); impostazione predefinita: 1 ora |
| feedback.maxDeliveryCount | Numero massimo di recapito per la coda di commenti e suggerimenti | da 1 a 100; valore predefinito: 10 |
| feedback.lockDurationAsIso8601 | Numero massimo di recapito per la coda di commenti e suggerimenti | ISO_8601 intervallo da 5 a 300 secondi (minimo 5 secondi); valore predefinito: 60 secondi. |

È possibile impostare le opzioni di configurazione in uno dei modi seguenti:

* **portale di Azure**: in **Impostazioni** nell'hub IoT selezionare **Endpoint predefiniti** ed espandere **Messaggistica da cloud a dispositivo.** L'impostazione **delle proprietà feedback.maxDeliveryCount** e **feedback.lockDurationAsIso8601** non è attualmente supportata in portale di Azure.

    ![Impostare le opzioni di configurazione per la messaggistica da cloud a dispositivo nel portale](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Interfaccia della riga di comando** di Azure: usare il comando [az iot hub update:](/cli/azure/iot/hub#az_iot_hub_update)

    ```azurecli
    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.defaultTtlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.ttlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.lockDurationAsIso8601=PT0H1M0S
    ```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sugli SDK che è possibile usare per ricevere messaggi da cloud a dispositivo, vedere Azure IoT [SDK](iot-hub-devguide-sdks.md).

Per provare a ricevere i messaggi da cloud a dispositivo, vedere l'esercitazione [Invio da cloud a dispositivo](iot-hub-csharp-csharp-c2d.md).
