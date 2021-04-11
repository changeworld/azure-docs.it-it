---
title: 'Esercitazione: instradare gli eventi di modifica dello stato dei criteri a griglia di eventi'
description: In questa esercitazione viene configurata la griglia di eventi per l'ascolto degli eventi di modifica dello stato dei criteri e viene chiamato un webhook.
ms.date: 03/29/2021
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1fe87e4fd3349df7d8f5d57b2b2d95f95ed3fba8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735546"
---
# <a name="tutorial-route-policy-state-change-events-to-event-grid-with-azure-cli"></a>Esercitazione: instradare gli eventi di modifica dello stato dei criteri a griglia di eventi

Questo articolo illustra come configurare le sottoscrizioni di eventi criteri di Azure per inviare eventi di modifica dello stato dei criteri a un endpoint Web. Gli utenti di criteri di Azure possono sottoscrivere gli eventi generati quando si verificano modifiche allo stato dei criteri sulle risorse. Questi eventi possono attivare gli hook Web, le [funzioni di Azure](../../../azure-functions/index.yml), le code di archiviazione di [Azure](../../../storage/queues/index.yml)o qualsiasi altro gestore eventi supportato da griglia di [eventi di Azure](../../../event-grid/index.yml). In genere, si inviano eventi a un endpoint che elabora i dati dell'evento e intraprende azioni. Tuttavia, per semplificare questa esercitazione, si inviano gli eventi a un'app Web che raccoglie e Visualizza i messaggi.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

- Questa guida di avvio rapido richiede l'interfaccia della riga di comando di Azure 2.0.76 o versioni successive. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

- Anche se in precedenza è stato usato criteri di Azure o griglia di eventi, registrare di nuovo i rispettivi provider di risorse:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Provider register: Register the Azure Event Grid provider
  az provider register --namespace Microsoft.EventGrid
  ```

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Gli argomenti della griglia di eventi sono risorse di Azure e devono essere inseriti in un gruppo di risorse di Azure. Un gruppo di risorse è una raccolta logica in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group). 

L'esempio seguente crea un gruppo di risorse denominato `<resource_group_name>` nell'area _westus_. Sostituire `<resource_group_name>` con un nome univoco per il gruppo di risorse.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az group create --name <resource_group_name> --location westus
```

## <a name="create-an-event-grid-system-topic"></a>Argomento creare un sistema di griglia di eventi

Ora che è disponibile un gruppo di risorse, viene creato un [argomento di sistema](../../../event-grid/system-topics.md). Un argomento di sistema in griglia di eventi rappresenta uno o più eventi pubblicati da servizi di Azure, ad esempio criteri di Azure e hub eventi di Azure. Questo argomento di sistema usa il `Microsoft.PolicyInsights.PolicyStates` tipo di argomento per le modifiche dello stato dei criteri di Azure. Sostituire `<SubscriptionID>` nel parametro **scope** con l'ID della sottoscrizione e `<resource_group_name>` nel parametro **Resource-Group** con il gruppo di risorse creato in precedenza.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az eventgrid system-topic create --name PolicyStateChanges --location global --topic-type Microsoft.PolicyInsights.PolicyStates --source "/subscriptions/<SubscriptionID>" --resource-group "<resource_group_name>"
```

## <a name="create-a-message-endpoint"></a>Creare un endpoint del messaggio

Prima di sottoscrivere l'argomento, creare l'endpoint per il messaggio dell'evento. L'endpoint richiede in genere azioni basate sui dati degli eventi. Per semplificare questa guida introduttiva, si distribuisce un'[app Web preesistente](https://github.com/Azure-Samples/azure-event-grid-viewer) che visualizza i messaggi di evento. La soluzione distribuita include un piano di servizio app, un'app Web del servizio app e codice sorgente da GitHub.

Sostituire `<your-site-name>` con un nome univoco per l'app Web. Il nome dell'app Web deve essere univoco perché fa parte della voce DNS.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az deployment group create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=<your-site-name> hostingPlanName=viewerhost
```

Per il completamento della distribuzione possono essere necessari alcuni minuti. Dopo il completamento della distribuzione, visualizzare l'app Web per assicurarsi che sia in esecuzione. In un Web browser passare a: `https://<your-site-name>.azurewebsites.net`

Il sito dovrebbe essere visibile senza messaggi attualmente visualizzati.

## <a name="subscribe-to-the-system-topic"></a>Sottoscrivere l'argomento di sistema

Si sottoscrive un argomento per indicare a Griglia di eventi gli eventi di cui si vuole tenere traccia e dove inviare tali eventi. L'esempio seguente sottoscrive l'argomento di sistema creato e passa l'URL dall'app Web come endpoint per ricevere le notifiche degli eventi. Sostituire `<event_subscription_name>` con un nome per la sottoscrizione di eventi. Per `<resource_group_name>` e `<your-site-name>` usare i valori creati in precedenza.

L'endpoint per l'app Web deve includere il suffisso `/api/updates/`.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Create the subscription
az eventgrid system-topic event-subscription create \
  --name <event_subscription_name> \
  --resource-group <resource_group_name> \
  --system-topic-name PolicyStateChanges \
  --endpoint https://<your-site-name>.azurewebsites.net/api/updates
```

Visualizzare nuovamente l'app Web e notare che all'app è stato inviato un evento di convalida della sottoscrizione. Selezionare l'icona a forma di occhio per espandere i dati dell'evento. Griglia di eventi invia l'evento di convalida in modo che l'endpoint possa verificare che voglia ricevere i dati dell'evento. L'app Web include il codice per convalidare la sottoscrizione.

:::image type="content" source="../media/route-state-change-events/view-subscription-event.png" alt-text="Screenshot dell'evento di convalida della sottoscrizione di griglia di eventi nell'app Web predefinita.":::

## <a name="create-a-policy-assignment"></a>Creare un'assegnazione di criteri

In questa Guida introduttiva si crea un'assegnazione di criteri e si assegna la definizione **Richiedi un tag per i gruppi di risorse** . Questa definizione di criteri identifica i gruppi di risorse in cui manca il tag configurato durante l'assegnazione dei criteri.

Eseguire il comando seguente per creare un'assegnazione di criteri con ambito per il gruppo di risorse creato per ospitare l'argomento di griglia di eventi:

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az policy assignment create --name 'requiredtags-events' --display-name 'Require tag on RG' --scope '<ResourceGroupScope>' --policy '<policy definition ID>' --params '{ "tagName": { "value": "EventTest" } }'
```

Il comando precedente usa le informazioni seguenti.

- **Name**: nome effettivo dell'assegnazione. Per questo esempio è stato usato _requiredTags-Events_ .
- **DisplayName**: nome visualizzato per l'assegnazione di criteri. In questo caso, si usa _il tag require in RG_.
- **Scope**: ambito che determina le risorse o il raggruppamento di risorse a cui viene applicata l'assegnazione di criteri. e può variare da una sottoscrizione a gruppi di risorse. Assicurarsi di sostituire &lt;scope&gt; con il nome del gruppo di risorse. Il formato per un ambito del gruppo di risorse è `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>` .
- **Policy**: ID della definizione di criteri in base alla quale si crea l'assegnazione. In questo caso, l'ID della definizione dei criteri _richiede un tag nei gruppi di risorse_. Per ottenere l'ID della definizione di criteri, eseguire questo comando: `az policy definition list --query "[?displayName=='Require a tag on resource groups']"`

Dopo aver creato l'assegnazione dei criteri, attendere che venga visualizzata una notifica degli eventi **Microsoft. PolicyInsights. PolicyStateCreated** nell'app Web. Il gruppo di risorse creato Mostra un `data.complianceState` valore di  non conforme da avviare.

:::image type="content" source="../media/route-state-change-events/view-policystatecreated-event.png" alt-text="Screenshot dell'evento stato dei criteri di sottoscrizione di griglia di eventi creato per il gruppo di risorse nell'app Web precompilata.":::

> [!NOTE]
> Se il gruppo di risorse eredita altre assegnazioni di criteri dalla gerarchia della sottoscrizione o del gruppo di gestione, vengono visualizzati anche gli eventi per ognuno di essi. Verificare che l'evento sia per l'assegnazione in questa esercitazione valutando la `data.policyDefinitionId` Proprietà.

## <a name="trigger-a-change-on-the-resource-group"></a>Attivare una modifica nel gruppo di risorse

Per rendere il gruppo di risorse conforme, è necessario un tag con il nome **EventTest** . Aggiungere il tag al gruppo di risorse con il comando seguente sostituendo `<SubscriptionID>` con l'ID sottoscrizione e `<ResourceGroup>` con il nome del gruppo di risorse:

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az tag create --resource-id '/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>' --tags EventTest=true
```

Dopo aver aggiunto il tag richiesto al gruppo di risorse, attendere che venga visualizzata una notifica degli eventi **Microsoft. PolicyInsights. PolicyStateChanged** nell'app Web. Espandere l'evento e il `data.complianceState` valore ora Visualizza _conforme_.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare a usare questa app Web e la sottoscrizione agli eventi di criteri di Azure, non eliminare le risorse create in questo articolo. Se non si intende continuare, usare il comando seguente per eliminare le risorse create con questo articolo.

Sostituire `<resource_group_name>` con il gruppo di risorse creato in precedenza.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare argomenti e sottoscrizioni di eventi per criteri di Azure, altre informazioni sugli eventi di modifica dello stato dei criteri e sulla griglia di eventi possono essere utili:

- [Reazione agli eventi di modifica dello stato dei criteri di Azure](../concepts/event-overview.md)
- [Dettagli dello schema di criteri di Azure per griglia di eventi](../../../event-grid/event-schema-policy.md)
- [Informazioni sulla griglia di eventi](../../../event-grid/overview.md)