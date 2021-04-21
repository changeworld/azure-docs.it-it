---
title: Come usare i valori denominati nei criteri di Gestione API di Azure
description: Informazioni su come usare i valori denominati nei criteri di Gestione API di Azure. I valori denominati possono contenere stringhe letterali, espressioni di criteri e segreti archiviati in Azure Key Vault.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: b0e076f3b248942870ba58a51c85c3df1f1277a4
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750609"
---
# <a name="use-named-values-in-azure-api-management-policies"></a>Usare valori denominati nei criteri di API Management Azure

[API Management criteri sono](api-management-howto-policies.md) una potente funzionalità del sistema che consente all'autore di modificare il comportamento dell'API tramite la configurazione. I criteri sono una raccolta di istruzioni che vengono eseguite in modo sequenziale sulla richiesta o la risposta di un'API. Le istruzioni dei criteri possono essere costruite usando valori di testo letterali, espressioni di criteri e valori denominati.

*I valori* denominati sono una raccolta globale di coppie nome/valore in ogni API Management istanza. Non esiste alcun limite imposto per il numero di elementi nella raccolta. I valori denominati possono essere usati per gestire segreti e valori stringa costanti in tutte le configurazioni e i criteri dell'API. 

:::image type="content" source="media/api-management-howto-properties/named-values.png" alt-text="Valori denominati nella portale di Azure":::

## <a name="value-types"></a>Tipi valore

|Type  |Descrizione  |
|---------|---------|
|Pianura     |  Stringa letterale o espressione di criteri     |
|Segreto     |   Stringa letterale o espressione di criteri crittografata da API Management      |
|[Insieme di credenziali delle chiavi](#key-vault-secrets)     |  Identificatore di un segreto archiviato in un insieme di credenziali delle chiavi di Azure.      |

I valori semplici o i segreti possono contenere [espressioni di criteri](./api-management-policy-expressions.md). Ad esempio, l'espressione `@(DateTime.Now.ToString())` restituisce una stringa contenente la data e l'ora correnti.

Per informazioni dettagliate sugli attributi dei valori denominati, vedere le informazioni di API Management [sull'API REST.](/rest/api/apimanagement/2020-06-01-preview/namedvalue/createorupdate)

## <a name="key-vault-secrets"></a>Segreti dell'insieme di credenziali delle chiavi

I valori dei segreti possono essere archiviati come stringhe crittografate in API Management (segreti personalizzati) o facendo riferimento ai segreti in [Azure Key Vault](../key-vault/general/overview.md). 

È consigliabile usare i segreti dell'insieme di credenziali delle chiavi perché consente di migliorare API Management sicurezza:

* I segreti archiviati negli insiemi di credenziali delle chiavi possono essere riutilizzati tra i servizi
* È [possibile applicare criteri di](../key-vault/general/security-overview.md#privileged-access) accesso granulari ai segreti
* I segreti aggiornati nell'insieme di credenziali delle chiavi vengono ruotati automaticamente in API Management. Dopo l'aggiornamento nell'insieme di credenziali delle chiavi, un valore denominato in API Management viene aggiornato entro 4 ore. È anche possibile aggiornare manualmente il segreto usando il portale di Azure o tramite l'API REST di gestione.

### <a name="prerequisites-for-key-vault-integration"></a>Prerequisiti per l'integrazione dell'insieme di credenziali delle chiavi

1. Per la procedura di creazione di un insieme di credenziali delle chiavi, vedere [Avvio rapido: Creare un insieme](../key-vault/general/quick-create-portal.md)di credenziali delle chiavi usando l'portale di Azure .
1. Abilitare un'identità gestita assegnata dal sistema o [assegnata](api-management-howto-use-managed-service-identity.md) dall'utente nell'API Management predefinita.
1. Assegnare un [criterio di accesso dell'insieme di](../key-vault/general/assign-access-policy-portal.md) credenziali delle chiavi all'identità gestita con le autorizzazioni per ottenere ed elencare i segreti dall'insieme di credenziali. Per aggiungere i criteri:
    1. Nel portale passare all'insieme di credenziali delle chiavi.
    1. Selezionare **Impostazioni > criteri di accesso > +Aggiungi criteri di accesso**.
    1. Selezionare **Autorizzazioni segrete,** quindi Selezionare **Ottieni** ed **Elenca**.
    1. In **Seleziona entità** selezionare il nome della risorsa dell'identità gestita. Se si usa un'identità assegnata dal sistema, l'entità è il nome dell'istanza API Management sistema.
1. Creare o importare un segreto nell'insieme di credenziali delle chiavi. Vedere [Avvio rapido: Impostare e recuperare un segreto](../key-vault/secrets/quick-create-portal.md)da Azure Key Vault usando portale di Azure .

Per usare il segreto dell'insieme di credenziali delle chiavi, aggiungere o [modificare un valore denominato](#add-or-edit-a-named-value)e specificare un tipo di insieme di credenziali delle **chiavi.** Selezionare il segreto dall'insieme di credenziali delle chiavi.

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-or-edit-a-named-value"></a>Aggiungere o modificare un valore denominato

### <a name="add-a-key-vault-secret"></a>Aggiungere un segreto dell'insieme di credenziali delle chiavi

Vedere [Prerequisiti per l'integrazione dell'insieme di credenziali delle chiavi.](#prerequisites-for-key-vault-integration)

> [!CAUTION]
> Quando si usa un segreto dell'insieme di credenziali delle chiavi API Management, prestare attenzione a non eliminare il segreto, l'insieme di credenziali delle chiavi o l'identità gestita usata per accedere all'insieme di credenziali delle chiavi.

1. Nel [portale di Azure](https://portal.azure.com) accedere all'istanza di Gestione API.
1. In **API selezionare** **Valori denominati**  >  **+Aggiungi.**
1. Immettere un **Identificatore** nome e un **Nome** visualizzato usato per fare riferimento alla proprietà nei criteri.
1. In **Tipo di valore** selezionare Insieme di credenziali delle **chiavi.**
1. Immettere l'identificatore di un segreto dell'insieme di credenziali delle chiavi (senza versione) oppure scegliere **Seleziona** per selezionare un segreto da un insieme di credenziali delle chiavi.
    > [!IMPORTANT]
    > Se si immette manualmente un identificatore segreto dell'insieme di credenziali delle chiavi, assicurarsi che non abbia informazioni sulla versione. In caso contrario, il segreto non ruota automaticamente in API Management dopo un aggiornamento nell'insieme di credenziali delle chiavi.
1. In **Identità client** selezionare un'identità gestita assegnata dal sistema o un'identità gestita assegnata dall'utente esistente. Informazioni su come [aggiungere o modificare le identità gestite nel API Management servizio](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > L'identità deve avere le autorizzazioni per ottenere ed elencare i segreti dall'insieme di credenziali delle chiavi. Se non è già stato configurato l'accesso all'insieme di credenziali delle chiavi, API Management richiederà di configurare automaticamente l'identità con le autorizzazioni necessarie.
1. Aggiungere uno o più tag facoltativi per organizzare i valori denominati, quindi **salvare**.
1. Selezionare **Crea**.

    :::image type="content" source="media/api-management-howto-properties/add-property.png" alt-text="Aggiungere il valore del segreto dell'insieme di credenziali delle chiavi":::

### <a name="add-a-plain-or-secret-value"></a>Aggiungere un valore normale o segreto

### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nel [portale di Azure](https://portal.azure.com) accedere all'istanza di Gestione API.
1. In **API selezionare** **Valori denominati**  >  **+Aggiungi.**
1. Immettere un **Identificatore** nome e un **Nome** visualizzato usato per fare riferimento alla proprietà nei criteri.
1. In **Tipo valore** selezionare **Normale** o **Segreto.**
1. In **Valore** immettere una stringa o un'espressione di criteri.
1. Aggiungere uno o più tag facoltativi per organizzare i valori denominati, quindi **salvare**.
1. Selezionare **Crea**.

Dopo aver creato il valore denominato, è possibile modificarlo selezionando il nome. Se si modifica il nome visualizzato, tutti i criteri che fanno riferimento a tale valore denominato vengono aggiornati automaticamente per usare il nuovo nome visualizzato.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per iniziare a usare l'interfaccia della riga di comando di Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Per aggiungere un valore denominato, usare il [comando az apim nv create:](/cli/azure/apim/nv#az_apim_nv_create)

```azurecli
az apim nv create --resource-group apim-hello-word-resource-group \
    --display-name "named_value_01" --named-value-id named_value_01 \
    --secret true --service-name apim-hello-world --value test
```

Dopo aver creato un valore denominato, è possibile aggiornarlo usando il [comando az apim nv update.](/cli/azure/apim/nv#az_apim_nv_update) Per visualizzare tutti i valori denominati, eseguire il [comando az apim nv list:](/cli/azure/apim/nv#az_apim_nv_list)

```azurecli
az apim nv list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Per visualizzare i dettagli del valore denominato creato per questo esempio, eseguire il [comando az apim nv show:](/cli/azure/apim/nv#az_apim_nv_show)

```azurecli
az apim nv show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

Questo esempio è un valore segreto. Il comando precedente non restituisce il valore . Per visualizzare il valore, eseguire il [comando az apim nv show-secret:](/cli/azure/apim/nv#az_apim_nv_show_secret)

```azurecli
az apim nv show-secret --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

Per eliminare un valore denominato, usare il [comando az apim nv delete:](/cli/azure/apim/nv#az_apim_nv_delete)

```azurecli
az apim nv delete --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

---

## <a name="use-a-named-value"></a>Usare un valore denominato

Negli esempi di questa sezione vengono utilizzati i valori denominati illustrati nella tabella seguente.

| Nome               | valore                      | Segreto | 
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | `TrackingId`                 | Falso  | 
| ContosoHeaderValue | ••••••••••••••••••••••     | True   | 
| ExpressionProperty | `@(DateTime.Now.ToString())` | Falso  | 

Per usare un valore denominato in un criterio, inserire il nome visualizzato all'interno di una coppia doppia di parentesi graffe come `{{ContosoHeader}}` , come illustrato nell'esempio seguente:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

In questo esempio, la proprietà `ContosoHeader` viene usata come nome di un'intestazione in un criterio `set-header` e la proprietà `ContosoHeaderValue` viene usata come valore di tale intestazione. Quando questo criterio viene valutato durante una richiesta o una risposta al gateway di Gestione API, `{{ContosoHeader}}` e `{{ContosoHeaderValue}}` vengono sostituiti dai rispettivi valori.

I valori denominati possono essere usati come valori completi di attributo o di elemento, come illustrato nell'esempio precedente, ma possono anche essere inseriti all'interno di un'espressione di testo o combinati con una parte di un'espressione di testo, come illustrato nell'esempio seguente:  

```xml
<set-header name = "CustomHeader{{ContosoHeader}}" ...>
```

I valori denominati possono anche contenere espressioni di criteri. Nell'esempio seguente viene usata `ExpressionProperty` l'espressione .

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Quando questo criterio viene valutato, `{{ExpressionProperty}}` viene sostituito con il relativo valore, `@(DateTime.Now.ToString())` . Poiché il valore è un'espressione di criteri, l'espressione viene valutata e il criterio passa alla fase di esecuzione.

È possibile testare questa operazione nel [](api-management-howto-developer-portal.md) portale di Azure o nel portale per sviluppatori chiamando un'operazione con criteri con valori denominati nell'ambito. Nell'esempio seguente viene chiamata un'operazione che contiene i due criteri di esempio precedenti `set-header` con valori denominati. Si noti che la risposta contiene due intestazioni personalizzate configurate usando criteri con valori denominati.

:::image type="content" source="media/api-management-howto-properties/api-management-send-results.png" alt-text="Testare la risposta dell'API":::

Se si osserva la traccia [API](api-management-howto-api-inspector.md) in uscita per una chiamata che include i due criteri di esempio precedenti con valori denominati, è possibile visualizzare i due criteri con i valori denominati inseriti, nonché la valutazione dell'espressione dei criteri per il valore denominato che conteneva l'espressione dei `set-header` criteri.

:::image type="content" source="media/api-management-howto-properties/api-management-api-inspector-trace.png" alt-text="Traccia di Controllo API":::

> [!CAUTION]
> Se un criterio fa riferimento a un segreto in Azure Key Vault, il valore dell'insieme di credenziali delle chiavi sarà visibile agli utenti che hanno accesso alle sottoscrizioni abilitate per la traccia delle richieste [API.](api-management-howto-api-inspector.md)

Sebbene i valori denominati possano contenere espressioni di criteri, non possono contenere altri valori denominati. Se il testo contenente un riferimento a un valore denominato viene usato per un valore, ad esempio `Text: {{MyProperty}}`, tale riferimento non verrà risolto e sostituito.

## <a name="delete-a-named-value"></a>Eliminare un valore denominato

Per eliminare un valore denominato, selezionare il nome e quindi **scegliere Elimina** dal menu di scelta rapida (**...**).

> [!IMPORTANT]
> Se si fa riferimento al valore denominato da qualsiasi API Management, non è possibile eliminarlo fino a quando non si rimuove il valore denominato da tutti i criteri che lo usano.

## <a name="next-steps"></a>Passaggi successivi

-   Ulteriori informazioni sull'uso dei criteri
    -   [Criteri di Gestione API](api-management-howto-policies.md)
    -   [Riferimento ai criteri](./api-management-policies.md)
    -   [Espressioni di criteri](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png

