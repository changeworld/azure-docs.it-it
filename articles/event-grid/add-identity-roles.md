---
title: Aggiungere identità gestite a un ruolo nella destinazione di griglia di eventi di Azure
description: Questo articolo descrive come aggiungere identità gestite ai ruoli di Azure in destinazioni come il bus di servizio di Azure e hub eventi di Azure.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 1bcef878c982122d80980dd7194fae2de6fc8762
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630453"
---
# <a name="add-an-identity-to-azure-roles-on-azure-event-grid-destinations"></a>Aggiungere un'identità ai ruoli di Azure nelle destinazioni di griglia di eventi di Azure
Questa sezione descrive come aggiungere l'identità per un argomento di sistema, un argomento personalizzato o un dominio a un ruolo di Azure. 

## <a name="prerequisites"></a>Prerequisiti
Assegnare un'identità gestita assegnata dal sistema utilizzando le istruzioni fornite negli articoli seguenti:

- [Argomenti o domini personalizzati](enable-identity-custom-topics-domains.md)
- [Argomenti di sistema](enable-identity-system-topics.md)

## <a name="supported-destinations-and-azure-roles"></a>Destinazioni e ruoli di Azure supportati
Dopo aver abilitato l'identità per il dominio o l'argomento personalizzato di griglia di eventi, Azure crea automaticamente un'identità in Azure Active Directory. Aggiungere questa identità ai ruoli appropriati di Azure in modo che l'argomento o il dominio personalizzato possa inviare gli eventi alle destinazioni supportate. Ad esempio, aggiungere l'identità al ruolo di **mittente dei dati di hub eventi di Azure** per uno spazio dei nomi di hub eventi di Azure in modo che l'argomento personalizzato di griglia di eventi possa inviare eventi a hub eventi in tale spazio dei nomi. 

Griglia di eventi di Azure supporta attualmente gli argomenti personalizzati o i domini configurati con un'identità gestita assegnata dal sistema per l'invio di eventi alle destinazioni seguenti. Questa tabella fornisce anche i ruoli in cui deve trovarsi l'identità, in modo che l'argomento personalizzato possa trasmettere gli eventi.

| Destination | Ruolo di Azure | 
| ----------- | --------- | 
| Code e argomenti del bus di servizio | [Mittente dei dati del bus di servizio di Azure](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Hub eventi di Azure | [Mittente dei dati di Hub eventi di Azure](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Archiviazione BLOB di Azure | [Collaboratore ai dati del BLOB di archiviazione](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) |
| Archiviazione code di Azure |[Mittente dei messaggi sui dati della coda di archiviazione](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) | 


## <a name="use-the-azure-portal"></a>Usare il portale di Azure
È possibile utilizzare il portale di Azure per assegnare l'identità personalizzata o di dominio a un ruolo appropriato, in modo che l'argomento o il dominio personalizzato possano inviare eventi alla destinazione. 

L'esempio seguente aggiunge un'identità gestita per un argomento personalizzato di griglia di eventi denominato **msitesttopic** al ruolo di **mittente dei dati del bus di servizio di Azure** per uno spazio dei nomi del bus di servizio che contiene una risorsa della coda o dell'argomento. Quando si aggiunge al ruolo a livello di spazio dei nomi, l'argomento personalizzato di griglia di eventi può inviare gli eventi a tutte le entità all'interno dello spazio dei nomi. 

1. Passare allo **spazio dei nomi del bus di servizio** nel [portale di Azure](https://portal.azure.com). 
1. Selezionare **controllo di accesso** nel riquadro sinistro. 
1. Nella sezione **Aggiungi un'assegnazione di ruolo** selezionare **Aggiungi**. 
1. Nella pagina **Aggiungi un'assegnazione di ruolo** seguire questa procedura:
    1. Selezionare il ruolo. In questo caso: **Mittente dei dati del bus di servizio di Azure**. 
    1. Selezionare l' **identità** per il dominio o l'argomento personalizzato di griglia di eventi. 
    1. Selezionare **Save (Salva** ) per salvare la configurazione.

Per aggiungere un'identità agli altri ruoli specificati nella tabella, sarà necessario seguire una procedura simile. 

## <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure
L'esempio in questa sezione illustra come usare l'interfaccia della riga di comando di Azure per aggiungere un'identità a un ruolo di Azure. I comandi di esempio sono per gli argomenti personalizzati di griglia di eventi. ma sono simili a quelli per i domini di Griglia di eventi. 

### <a name="get-the-principal-id-for-the-custom-topics-system-identity"></a>Ottenere l'ID entità per l'identità di sistema dell'argomento personalizzato 
Per prima cosa, ottenere l'ID entità dell'identità gestita dal sistema dell'argomento personalizzato e assegnare l'identità ai ruoli appropriati.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Creare un'assegnazione di ruolo per Hub eventi in diversi ambiti 
L'esempio di interfaccia della riga di comando seguente illustra come aggiungere l'identità di un argomento personalizzato al ruolo di **mittente dei dati di hub eventi di Azure** a livello di spazio dei nomi o a livello di hub eventi. Se si crea l'assegnazione di ruolo a livello di spazio dei nomi, l'argomento personalizzato può inviare gli eventi a tutti gli hub eventi in tale spazio dei nomi. Se si crea un'assegnazione di ruolo a livello di hub eventi, l'argomento personalizzato può inviare gli eventi solo a tale hub eventi specifico. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>Creare un'assegnazione di ruolo per un argomento del bus di servizio in vari ambiti 
L'esempio di interfaccia della riga di comando seguente illustra come aggiungere l'identità di un argomento personalizzato di griglia di eventi al ruolo di **mittente dei dati del bus di servizio di Azure** a livello di spazio dei nomi o di argomento del bus di servizio. Se si crea l'assegnazione di ruolo a livello di spazio dei nomi, l'argomento di griglia di eventi può inviare eventi a tutte le entità (code o argomenti del bus di servizio) all'interno di tale spazio dei nomi. Se si crea un'assegnazione di ruolo a livello di coda o argomento del bus di servizio, l'argomento personalizzato di griglia di eventi può inviare eventi solo alla coda o all'argomento del bus di servizio specifico. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="next-steps"></a>Passaggi successivi
Ora che è stata assegnata un'identità assegnata dal sistema all'argomento del sistema, all'argomento personalizzato o al dominio e l'identità è stata aggiunta ai ruoli appropriati sulle destinazioni, vedere [eventi Devlier che usano l'identità](managed-service-identity.md) per la distribuzione di eventi a destinazioni usando l'identità.


