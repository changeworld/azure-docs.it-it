---
title: Abilitare un'identità gestita per il routing degli eventi (anteprima)-interfaccia della riga di comando
titleSuffix: Azure Digital Twins
description: Vedere come abilitare un'identità assegnata dal sistema per i dispositivi gemelli digitali di Azure e usarla per l'invio di eventi tramite l'interfaccia della riga di comando di Azure.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c9ce87584373bd87a8f89ecb4ea692b44d3fab4d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202961"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-cli"></a>Abilitare un'identità gestita per il routing degli eventi di Azure Digital Twins (anteprima): interfaccia della riga di comando di Azure

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

Questo articolo descrive come abilitare un' [identità assegnata dal sistema per un'istanza di dispositivi gemelli digitali di Azure](concepts-security.md#managed-identity-for-accessing-other-resources-preview) (attualmente in anteprima) e come usare l'identità quando si inviano eventi a destinazioni supportate, ad esempio l' [Hub eventi](../event-hubs/event-hubs-about.md), le destinazioni del [bus di servizio](../service-bus-messaging/service-bus-messaging-overview.md)   e il contenitore di archiviazione di [Azure](../storage/blobs/storage-blobs-introduction.md).

Questo articolo illustra il processo usando l'interfaccia della riga di comando di [**Azure**](/cli/azure/what-is-azure-cli).

Ecco i passaggi descritti in questo articolo: 

1. Creare un'istanza di Azure Digital Twins con un'identità assegnata dal sistema o abilitare l'identità assegnata dal sistema in un'istanza di Azure Digital Twins esistente. 
1. Aggiungere un ruolo o ruoli appropriati all'identità. Ad esempio, assegnare il ruolo di **mittente dei dati dell'hub eventi di Azure** all'identità se l'endpoint è un hub eventi o il **ruolo di mittente dei dati del bus di servizio di Azure** se l'endpoint è il bus di servizio.
1. Creare un endpoint nei dispositivi gemelli digitali di Azure in grado di usare identità assegnate dal sistema per l'autenticazione.

## <a name="enable-system-managed-identities-for-an-instance"></a>Abilitare le identità gestite dal sistema per un'istanza 

Quando si Abilita un'identità assegnata dal sistema nell'istanza di Azure Digital Twins, Azure crea automaticamente un'identità in [Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md). Tale identità può quindi essere usata per eseguire l'autenticazione agli endpoint di Azure Digital gemelli per l'invio di eventi.

È possibile abilitare le identità gestite dal sistema per un'istanza di Azure Digital Twins **come parte dell'installazione iniziale dell'istanza** o **abilitarla in un secondo momento in un'istanza già esistente**.

Uno di questi metodi di creazione fornirà le stesse opzioni di configurazione e lo stesso risultato finale per l'istanza. In questa sezione viene descritto come eseguire entrambe le operazioni.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>Aggiungere un'identità gestita dal sistema durante la creazione dell'istanza

Questa sezione illustra come abilitare un'identità gestita dal sistema in un'istanza di Azure Digital Twins attualmente in fase di creazione. 

Questa operazione viene eseguita aggiungendo un `--assign-identity` parametro al `az dt create` comando utilizzato per creare l'istanza. Per altre informazioni su questo comando, vedere la [documentazione di riferimento](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create) o le [istruzioni generali per la configurazione di un'istanza di dispositivi gemelli digitali di Azure](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance).

Per creare un'istanza con un'identità gestita dal sistema, aggiungere il  `--assign-identity` parametro come segue:

```azurecli-interactive
az dt create -n {new_instance_name} -g {resource_group} --assign-identity
```

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>Aggiungere un'identità gestita dal sistema a un'istanza esistente

In questa sezione si aggiungerà un'identità gestita dal sistema a un'istanza di Azure Digital Twins già esistente.

Questa operazione viene eseguita anche con il `az dt create` comando e il `--assign-identity` parametro. Anziché fornire un nuovo nome di un'istanza da creare, è possibile specificare il nome di un'istanza già esistente per aggiornare il valore di per l' `--assign-identity` istanza.

Il comando per **abilitare** l'identità gestita è lo stesso del comando per creare un'istanza con un'identità gestita dal sistema. Tutte le modifiche sono il valore del parametro del nome dell'istanza:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity
```

Per **disabilitare** l'identità gestita in un'istanza in cui è attualmente abilitata, usare il comando simile seguente per impostare `--assign-identity` su `false` .

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity false
```

## <a name="assign-azure-roles-to-the-identity"></a>Assegnare i ruoli di Azure all'identità 

Una volta creata un'identità assegnata dal sistema per l'istanza di Azure Digital gemelli, sarà necessario assegnare i ruoli appropriati per l'autenticazione con tipi diversi di [endpoint](concepts-route-events.md) per l'invio di eventi alle destinazioni supportate. In questa sezione vengono descritte le opzioni del ruolo e le modalità di assegnazione all'identità assegnata dal sistema.

>[!NOTE]
> Si tratta di un passaggio importante: senza di esso, l'identità non sarà in grado di accedere agli endpoint ed è Impossibile recapitare gli eventi.

### <a name="supported-destinations-and-azure-roles"></a>Destinazioni e ruoli di Azure supportati 

Di seguito sono riportati i ruoli minimi che un'identità deve accedere a un endpoint, a seconda del tipo di destinazione. Funzioneranno anche i ruoli con autorizzazioni di livello superiore, come i ruoli del proprietario dei dati.

| Destination | Ruolo di Azure |
| --- | --- |
| Hub eventi di Azure | Mittente dei dati di Hub eventi di Azure |
| Bus di servizio di Azure | Mittente dei dati del bus di servizio di Azure |
| Contenitore di archiviazione di Azure | Collaboratore ai dati del BLOB di archiviazione |

Per altre informazioni sugli endpoint, le route e i tipi di destinazioni supportati per il routing nei dispositivi gemelli digitali di Azure, vedere [*concetti: route degli eventi*](concepts-route-events.md).

### <a name="assign-the-role"></a>Assegnare il ruolo

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

È possibile aggiungere il `--scopes` parametro al `az dt create` comando per assegnare l'identità a uno o più ambiti con un ruolo specificato. Questa operazione può essere utilizzata quando si crea prima l'istanza o in un secondo momento passando il nome di un'istanza già esistente.

Di seguito è riportato un esempio in cui viene creata un'istanza con un'identità gestita dal sistema e viene assegnata tale identità a un ruolo personalizzato chiamato `MyCustomRole` in un hub eventi.

```azurecli-interactive
az dt create -n {instance_name} -g {resource_group} --assign-identity --scopes "/subscriptions/<subscription ID>/resourceGroups/<resource_group>/providers/Microsoft.EventHub/namespaces/<Event_Hubs_namespace>/eventhubs/<event_hub_name>" --role MyCustomRole
```

Per ulteriori esempi di assegnazioni di ruolo con questo comando, vedere la [documentazione di riferimento **AZ DT create** Reference](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create).

In alternativa, è possibile usare anche il gruppo di comandi [**AZ Role Assignment**](/cli/azure/role/assignment) per creare e gestire i ruoli. Questa operazione può essere utilizzata per supportare scenari aggiuntivi in cui non si desidera raggruppare l'assegnazione di ruolo con il comando crea.

## <a name="create-an-endpoint-with-identity-based-authentication"></a>Creare un endpoint con l'autenticazione basata su identità

Dopo aver configurato un'identità gestita dal sistema per l'istanza di Azure Digital Twins e averla assegnata ai ruoli appropriati, è possibile creare [endpoint](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) di Azure Digital gemelli in grado di usare l'identità per l'autenticazione. Questa opzione è disponibile solo per gli endpoint di tipo hub eventi e bus di servizio (non è supportata per griglia di eventi).

>[!NOTE]
> Non è possibile modificare un endpoint che è già stato creato con l'identità basata su chiave per passare all'autenticazione basata sull'identità. Quando l'endpoint viene creato per la prima volta, è necessario scegliere il tipo di autenticazione.

Questa operazione viene eseguita aggiungendo un `--auth-type` parametro al `az dt endpoint create` comando usato per creare l'endpoint. Per ulteriori informazioni su questo comando, vedere la relativa [documentazione di riferimento](/cli/azure/ext/azure-iot/dt/endpoint/create) o le [istruzioni generali per la configurazione di un endpoint di dispositivi gemelli digitali di Azure](how-to-manage-routes-apis-cli.md#create-the-endpoint).

Per creare un endpoint che usa l'autenticazione basata su identità, specificare il `IdentityBased` tipo di autenticazione con il  `--auth-type` parametro. Nell'esempio seguente viene illustrato questo per un endpoint di hub eventi.

```azurecli-interactive
az dt endpoint create eventhub --endpoint-name {endpoint_name} --eventhub-resource-group {eventhub_resource_group} --eventhub-namespace {eventhub_namespace} --eventhub {eventhub_name} --auth-type IdentityBased -n {instance_name}
```

## <a name="considerations-for-disabling-system-managed-identities"></a>Considerazioni per la disabilitazione delle identità gestite dal sistema

Poiché un'identità viene gestita separatamente dagli endpoint che la usano, è importante considerare gli effetti che qualsiasi modifica apportata all'identità o ai relativi ruoli può avere sugli endpoint nell'istanza di Azure Digital gemelli. Se l'identità è disabilitata o un ruolo necessario per un endpoint viene rimosso da tale identità, l'endpoint può diventare inaccessibile e il flusso di eventi verrà danneggiato.

Per continuare a usare un endpoint configurato con un'identità gestita che ora è stata disabilitata, è necessario eliminare l'endpoint e [ricrearlo](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins) con un tipo di autenticazione diverso. Potrebbe essere necessaria fino a un'ora prima che gli eventi riprendano il recapito all'endpoint dopo questa modifica.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle identità gestite sono disponibili in Azure AD: 
* [*Identità gestite per le risorse di Azure*](../active-directory/managed-identities-azure-resources/overview.md)