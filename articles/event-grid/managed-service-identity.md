---
title: Recapito di eventi, identità del servizio gestito e collegamento privato
description: Questo articolo descrive come abilitare l'identità del servizio gestito per un argomento di Griglia di eventi di Azure e per usarla per inviare eventi alle destinazioni supportate.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 76f10b4627dc9578b1e616a868eab03431b59b69
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625278"
---
# <a name="event-delivery-with-a-managed-identity"></a>Recapito di eventi con un'identità gestita
Questo articolo descrive come usare un' [identità del servizio gestito](../active-directory/managed-identities-azure-resources/overview.md) per un argomento di sistema di griglia di eventi di Azure, un argomento personalizzato o un dominio. Usare l'identità per inviare eventi a destinazioni supportate, ad esempio code e argomenti del bus di servizio, Hub eventi e account di archiviazione.



## <a name="prerequisites"></a>Prerequisiti
1. Assegnare un'identità assegnata dal sistema a un argomento di sistema, un argomento personalizzato o un dominio. 
    - Per argomenti e domini personalizzati, vedere [abilitare l'identità gestita per gli argomenti e i domini personalizzati](enable-identity-custom-topics-domains.md). 
    - Per gli argomenti di sistema, vedere [abilitare l'identità gestita per gli argomenti di sistema](enable-identity-system-topics.md)
1. Aggiungere l'identità a un ruolo appropriato, ad esempio il mittente dei dati del bus di servizio, nella destinazione, ad esempio una coda del bus di servizio. Per i passaggi dettagliati, vedere [aggiungere identità ai ruoli di Azure nelle destinazioni](add-identity-roles.md)

    > [!NOTE]
    > Attualmente non è possibile recapitare gli eventi usando [endpoint privati](../private-link/private-endpoint-overview.md). Per ulteriori informazioni, vedere la sezione [endpoint privati](#private-endpoints) alla fine di questo articolo. 

## <a name="create-event-subscriptions-that-use-an-identity"></a>Creare sottoscrizioni di eventi che usano un'identità
Quando si dispone di un argomento personalizzato di griglia di eventi o di un dominio o un argomento di sistema con un'identità gestita dal sistema e l'identità è stata aggiunta al ruolo appropriato nella destinazione, si è pronti per creare sottoscrizioni che utilizzano l'identità. 

### <a name="use-the-azure-portal"></a>Usare il portale di Azure
Quando si crea una sottoscrizione di eventi, viene visualizzata un'opzione per abilitare l'utilizzo di un'identità assegnata dal sistema per un endpoint nella sezione **Dettagli endpoint** . 

![Abilitare l'identità durante la creazione di una sottoscrizione di eventi per una coda del bus di servizio](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

È anche possibile abilitare l'uso di un'identità assegnata dal sistema da usare per i messaggi non recapitabili nella scheda **funzionalità aggiuntive** . 

![Abilitare l'identità assegnata dal sistema per l'inserimento nella coda di messaggi non recapitabili](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="use-the-azure-cli---service-bus-queue"></a>Usare l'interfaccia della riga di comando di Azure-coda Service Bus 
Questa sezione illustra come usare l'interfaccia della riga di comando di Azure per consentire l'uso di un'identità assegnata dal sistema per recapitare gli eventi a una coda del bus di servizio. L'identità deve essere un membro del ruolo **Mittente dei dati del bus di servizio di Azure**. Deve essere anche un membro del ruolo **Collaboratore ai dati del BLOB di archiviazione** nell'account di archiviazione usato per i messaggi non recapitabili. 

#### <a name="define-variables"></a>Definire le variabili
Per prima cosa, specificare i valori per le variabili seguenti da usare nel comando dell'interfaccia della riga di comando. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Creare una sottoscrizione di eventi usando un'identità gestita per il recapito 
Questo comando di esempio crea una sottoscrizione di eventi per un argomento personalizzato di griglia di eventi con un tipo di endpoint impostato sulla **coda del bus di servizio**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery-and-dead-lettering"></a>Creare una sottoscrizione di eventi usando un'identità gestita per il recapito e la ricezione di messaggi non recapitabili
Questo comando di esempio crea una sottoscrizione di eventi per un argomento personalizzato di griglia di eventi con un tipo di endpoint impostato sulla **coda del bus di servizio**. Specifica inoltre che l'identità gestita dal sistema deve essere utilizzata per i messaggi non recapitabili. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="use-the-azure-cli---event-hubs"></a>Usare l'interfaccia della riga di comando di Azure-Hub eventi 
Questa sezione illustra come usare l'interfaccia della riga di comando di Azure per consentire l'uso di un'identità assegnata dal sistema per recapitare gli eventi a un hub eventi. L'identità deve essere un membro del ruolo **Mittente dei dati di Hub eventi di Azure**. Deve essere anche un membro del ruolo **Collaboratore ai dati del BLOB di archiviazione** nell'account di archiviazione usato per i messaggi non recapitabili. 

#### <a name="define-variables"></a>Definire le variabili
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Creare una sottoscrizione di eventi usando un'identità gestita per il recapito 
Questo comando di esempio crea una sottoscrizione di eventi per un argomento personalizzato di griglia di eventi con un tipo di endpoint impostato su **Hub eventi**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Creare una sottoscrizione di eventi usando un'identità gestita per il recapito + DeadLetter 
Questo comando di esempio crea una sottoscrizione di eventi per un argomento personalizzato di griglia di eventi con un tipo di endpoint impostato su **Hub eventi**. Specifica inoltre che l'identità gestita dal sistema deve essere utilizzata per i messaggi non recapitabili. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="use-the-azure-cli---azure-storage-queue"></a>Usare l'interfaccia della riga di comando di Azure-coda archiviazione di Azure 
Questa sezione illustra come usare l'interfaccia della riga di comando di Azure per consentire l'uso di un'identità assegnata dal sistema per recapitare gli eventi a una coda di archiviazione di Azure. L'identità deve essere un membro del ruolo di **mittente del messaggio di dati della coda di archiviazione** nell'account di archiviazione. Deve essere anche un membro del ruolo **Collaboratore ai dati del BLOB di archiviazione** nell'account di archiviazione usato per i messaggi non recapitabili.

#### <a name="define-variables"></a>Definire le variabili  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Creare una sottoscrizione di eventi usando un'identità gestita per il recapito 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Creare una sottoscrizione di eventi usando un'identità gestita per il recapito + DeadLetter 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```

## <a name="private-endpoints"></a>Endpoint privati
Attualmente non è possibile recapitare gli eventi usando [endpoint privati](../private-link/private-endpoint-overview.md). In altre condizioni, non è previsto alcun supporto se si dispone di requisiti di isolamento di rete rigorosi in cui il traffico degli eventi recapitati non deve lasciare lo spazio IP privato. 

Tuttavia, se i requisiti richiedono un modo sicuro per inviare eventi usando un canale crittografato e un'identità nota del mittente (in questo caso, griglia di eventi) usando lo spazio IP pubblico, è possibile recapitare gli eventi a hub eventi, bus di servizio o servizio di archiviazione di Azure usando un argomento personalizzato di griglia di eventi di Azure o un dominio con identità gestita dal sistema configurato come illustrato in Quindi, è possibile usare un collegamento privato configurato in funzioni di Azure o il webhook distribuito nella rete virtuale per eseguire il pull degli eventi. Vedere l'esempio: [connettersi agli endpoint privati con funzioni di Azure](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).

Con questa configurazione, il traffico passa attraverso l'IP pubblico/Internet da griglia di eventi a hub eventi, bus di servizio o archiviazione di Azure, ma il canale può essere crittografato e viene usata un'identità gestita di griglia di eventi. Se si configurano le funzioni di Azure o il webhook distribuito nella rete virtuale per l'uso di hub eventi, bus di servizio o archiviazione di Azure tramite un collegamento privato, la sezione del traffico sarà chiaramente in Azure.


## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle identità gestite, vedere [che cosa sono le identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).
