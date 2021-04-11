---
title: 'Griglia di eventi di Azure: impostare intestazioni personalizzate negli eventi recapitati'
description: Viene descritto come impostare le intestazioni personalizzate o le proprietà di recapito sugli eventi recapitati.
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: fb6f0de7919ed7cf9072c0fa35e8f9be5cb5e7db
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278287"
---
# <a name="custom-delivery-properties"></a>Proprietà di recapito personalizzate
Le sottoscrizioni di eventi consentono di impostare intestazioni HTTP incluse in eventi recapitati. Questa funzionalità consente di impostare intestazioni personalizzate richieste da una destinazione. È possibile impostare un massimo di 10 intestazioni quando si crea una sottoscrizione di eventi. Ogni valore di intestazione non deve essere maggiore di 4.096 byte (4K).

È possibile impostare intestazioni personalizzate per gli eventi che vengono recapitati alle destinazioni seguenti:

- Webhook
- Argomenti e code del bus di servizio di Azure
- Hub eventi di Azure
- Inoltrare connessioni ibride

Quando si crea una sottoscrizione di eventi nella portale di Azure, è possibile utilizzare la scheda **Proprietà recapito** per impostare intestazioni HTTP personalizzate. Questa pagina consente di impostare valori di intestazione fissi e dinamici.

## <a name="setting-static-header-values"></a>Impostazione dei valori di intestazione statici
Per impostare le intestazioni con un valore fisso, specificare il nome dell'intestazione e il relativo valore nei campi corrispondenti:

:::image type="content" source="./media/delivery-properties/static-header-property.png" alt-text="Proprietà recapito-statico":::

È possibile che si desideri controllare il **segreto?** quando si forniscono dati sensibili. I dati sensibili non verranno visualizzati nel portale di Azure. 

## <a name="setting-dynamic-header-values"></a>Impostazione dei valori di intestazione dinamici
È possibile impostare il valore di un'intestazione in base a una proprietà in un evento in ingresso. Usare la sintassi JsonPath per fare riferimento al valore della proprietà di un evento in ingresso da usare come valore per un'intestazione nelle richieste in uscita. Ad esempio, per impostare il valore di un'intestazione denominata **Channel** usando il valore del **sistema** di proprietà degli eventi in ingresso nei dati dell'evento, configurare la sottoscrizione di eventi nel modo seguente:

:::image type="content" source="./media/delivery-properties/dynamic-header-property.png" alt-text="Proprietà recapito-dinamico":::

## <a name="examples"></a>Esempio
In questa sezione vengono forniti alcuni esempi di utilizzo delle proprietà di recapito.

### <a name="setting-the-authorization-header-with-a-bearer-token-non-normative-example"></a>Impostazione dell'intestazione Authorization con un bearer token (esempio non normativo)

Impostare un valore su un'intestazione di autorizzazione per identificare la richiesta con il gestore del webhook. È possibile impostare un'intestazione di autorizzazione se non si [protegge il webhook con Azure Active Directory](secure-webhook-delivery.md).

| Nome intestazione   | Tipo di intestazione | Valore intestazione |
| :--           | :--         | :--            |
|`Authorization` | Static | `BEARER SlAV32hkKG...`|

Le richieste in uscita dovrebbero ora contenere l'intestazione impostata per la sottoscrizione di eventi:

```console
GET /home.html HTTP/1.1

Host: acme.com

User-Agent: <user-agent goes here>

Authorization: BEARER SlAV32hkKG...
```

> [!NOTE]
> La definizione delle intestazioni di autorizzazione è un'opzione sensata quando la destinazione è un webhook. Non deve essere usato per le [funzioni sottoscritte con ID di risorsa](/rest/api/eventgrid/eventsubscriptions/createorupdate#azurefunctioneventsubscriptiondestination), bus di servizio, Hub eventi e connessioni ibride perché tali destinazioni supportano i propri schemi di autenticazione se usati con griglia di eventi.

### <a name="service-bus-example"></a>Esempio di bus di servizio
Il bus di servizio di Azure supporta l'uso di un' [intestazione HTTP BrokerProperties](/rest/api/servicebus/message-headers-and-properties#message-headers) per definire le proprietà del messaggio quando si inviano messaggi singoli. Il valore dell' `BrokerProperties` intestazione deve essere specificato nel formato JSON. Se ad esempio è necessario impostare le proprietà dei messaggi quando si invia un singolo messaggio al bus di servizio, impostare l'intestazione nel modo seguente:

| Nome intestazione | Tipo di intestazione | Valore intestazione |
| :-- | :-- | :-- |
|`BrokerProperties` | Static     | `BrokerProperties:  { "MessageId": "{701332E1-B37B-4D29-AA0A-E367906C206E}", "TimeToLive" : 90}` |


### <a name="event-hubs-example"></a>Esempio di hub eventi

Se è necessario pubblicare eventi in una partizione specifica all'interno di un hub eventi, definire un' [intestazione HTTP BrokerProperties](/rest/api/eventhub/event-hubs-runtime-rest#common-headers) nella sottoscrizione degli eventi per specificare la chiave di partizione che identifica la partizione dell'hub eventi di destinazione.

| Nome intestazione | Tipo di intestazione | Valore intestazione                                  |
| :-- | :-- | :-- |
|`BrokerProperties` | Static | `BrokerProperties: {"PartitionKey": "0000000000-0000-0000-0000-000000000000000"}`  |


### <a name="configure-time-to-live-on-outgoing-events-to-azure-storage-queues"></a>Configurare la durata (TTL) degli eventi in uscita nelle code di archiviazione di Azure
Per la destinazione delle code di archiviazione di Azure, è possibile configurare solo la durata del messaggio in uscita dopo che è stato recapitato a una coda di archiviazione di Azure. Se non viene specificato alcun tempo, la durata predefinita del messaggio è 7 giorni. È anche possibile impostare l'evento in modo che non scada mai.

:::image type="content" source="./media/delivery-properties/delivery-properties-storage-queue.png" alt-text="Proprietà recapito-coda di archiviazione":::

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sul recapito degli eventi, vedere l'articolo seguente:

- [Recapito e nuovo tentativo](delivery-and-retry.md)
- [Recapito eventi webhook](webhook-event-delivery.md)
- [Filtro eventi](event-filtering.md)
