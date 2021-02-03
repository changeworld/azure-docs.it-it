---
title: Come usare i valori denominati nei criteri di Gestione API di Azure
description: Informazioni su come usare i valori denominati nei criteri di Gestione API di Azure. I valori denominati possono contenere stringhe letterali, espressioni di criteri e segreti archiviati in Azure Key Vault.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 12/14/2020
ms.author: apimpm
ms.openlocfilehash: 344500d5635f591b34a45130c7dd6b63659ad84d
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491014"
---
# <a name="use-named-values-in-azure-api-management-policies"></a>Usare i valori denominati nei criteri di gestione API di Azure

I [criteri di gestione API](api-management-howto-policies.md) sono una potente funzionalità del sistema che consente al server di pubblicazione di modificare il comportamento dell'API tramite la configurazione. I criteri sono una raccolta di istruzioni che vengono eseguite in modo sequenziale sulla richiesta o la risposta di un'API. Le istruzioni dei criteri possono essere costruite usando valori di testo letterali, espressioni di criteri e valori denominati.

*I valori denominati* sono una raccolta globale di coppie nome/valore in ogni istanza di gestione API. Non esiste alcun limite imposto per il numero di elementi nella raccolta. I valori denominati possono essere usati per gestire i valori di stringa costanti e i segreti in tutti i criteri e le configurazioni dell'API. 

:::image type="content" source="media/api-management-howto-properties/named-values.png" alt-text="Valori denominati nella portale di Azure":::

## <a name="value-types"></a>Tipi valore

|Tipo  |Descrizione  |
|---------|---------|
|Pianura     |  Stringa letterale o espressione di criteri     |
|Segreto     |   Stringa letterale o espressione di criteri crittografata da gestione API      |
|[Key Vault](#key-vault-secrets)     |  Identificatore di un segreto archiviato in un insieme di credenziali delle chiavi di Azure.      |

I valori semplici o i segreti possono contenere [espressioni di criteri](./api-management-policy-expressions.md). Ad esempio, l'espressione `@(DateTime.Now.ToString())` restituisce una stringa contenente la data e l'ora correnti.

Per informazioni dettagliate sugli attributi del valore denominato, vedere le informazioni di [riferimento sull'API REST](/rest/api/apimanagement/2020-06-01-preview/namedvalue/createorupdate)di gestione API.

## <a name="key-vault-secrets"></a>Segreti di Key Vault

I valori dei segreti possono essere archiviati come stringhe crittografate in gestione API (segreti personalizzati) o facendo riferimento a segreti in [Azure Key Vault](../key-vault/general/overview.md). 

È consigliabile usare i segreti dell'insieme di credenziali delle chiavi perché consente di migliorare la sicurezza di gestione API:

* I segreti archiviati negli insiemi di credenziali delle chiavi possono essere riutilizzati tra i servizi
* I [criteri di accesso](../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) granulari possono essere applicati ai segreti
* I segreti aggiornati nell'insieme di credenziali delle chiavi vengono automaticamente ruotati in gestione API. Dopo l'aggiornamento nell'insieme di credenziali delle chiavi, un valore denominato in gestione API viene aggiornato entro 4 ore. È anche possibile aggiornare manualmente il segreto usando il portale di Azure o tramite l'API REST di gestione.

### <a name="prerequisites-for-key-vault-integration"></a>Prerequisiti per l'integrazione di Key Vault

1. Per la procedura di creazione di un insieme di credenziali delle chiavi, vedere [Guida introduttiva: creare un insieme di credenziali delle chiavi usando il portale di Azure](../key-vault/general/quick-create-portal.md)
1. Abilitare un' [identità gestita](api-management-howto-use-managed-service-identity.md) assegnata dal sistema o assegnata dall'utente nell'istanza di gestione API.
1. Assegnare un [criterio di accesso di Key Vault](../key-vault/general/assign-access-policy-portal.md) all'identità gestita con le autorizzazioni per ottenere ed elencare i segreti dall'insieme di credenziali. Per aggiungere i criteri:
    1. Nel portale passare all'insieme di credenziali delle chiavi.
    1. Selezionare **impostazioni > criteri di accesso > + Aggiungi criteri di accesso**.
    1. Selezionare **autorizzazioni segrete**, quindi selezionare **Get** ed **elenco**.
    1. In **Seleziona entità** selezionare il nome della risorsa dell'identità gestita. Se si usa un'identità assegnata dal sistema, l'entità è il nome dell'istanza di gestione API.
1. Creare o importare un segreto nell'insieme di credenziali delle chiavi. Vedere [Guida introduttiva: impostare e recuperare un segreto da Azure Key Vault usando il portale di Azure](../key-vault/secrets/quick-create-portal.md).

Per usare il segreto dell'insieme di credenziali delle chiavi, [aggiungere o modificare un valore denominato](#add-or-edit-a-named-value)e specificare un tipo di insieme di credenziali delle **chiavi**. Selezionare il segreto dall'insieme di credenziali delle chiavi.

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-or-edit-a-named-value"></a>Aggiungere o modificare un valore denominato

### <a name="add-a-key-vault-secret"></a>Aggiungere un segreto dell'insieme di credenziali delle chiavi

Vedere [prerequisiti per l'integrazione di Key Vault](#prerequisites-for-key-vault-integration).

> [!CAUTION]
> Quando si usa un segreto di Key Vault in gestione API, prestare attenzione a non eliminare il segreto, l'insieme di credenziali delle chiavi o l'identità gestita usata per accedere all'insieme di credenziali delle chiavi.

1. Nel [portale di Azure](https://portal.azure.com) accedere all'istanza di Gestione API.
1. In **API** selezionare **valori denominati**  >  **+ Aggiungi**.
1. Immettere un identificatore **nome** e immettere un **nome visualizzato** usato per fare riferimento alla proprietà nei criteri.
1. In **tipo valore** selezionare **Key Vault**.
1. Immettere l'identificatore di un segreto dell'insieme di credenziali delle chiavi (senza versione) oppure scegliere **Seleziona** per selezionare un segreto da un insieme di credenziali delle chiavi.
    > [!IMPORTANT]
    > Se si immette manualmente un identificatore del segreto dell'insieme di credenziali delle chiavi, assicurarsi che non disponga di informazioni sulla versione. In caso contrario, il segreto non verrà ruotato automaticamente in gestione API dopo un aggiornamento nell'insieme di credenziali delle chiavi.
1. In **identità client** selezionare un'identità gestita assegnata dal sistema o esistente assegnata dall'utente. Informazioni su come [aggiungere o modificare le identità gestite nel servizio gestione API](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > L'identità necessita delle autorizzazioni per ottenere ed elencare i segreti dall'insieme di credenziali delle chiavi. Se l'accesso all'insieme di credenziali delle chiavi non è già stato configurato, gestione API richiede l'utente in modo che sia in grado di configurare automaticamente l'identità con le autorizzazioni necessarie.
1. Aggiungere uno o più tag facoltativi per organizzare i valori denominati, quindi **salvare**.
1. Selezionare **Crea**.

    :::image type="content" source="media/api-management-howto-properties/add-property.png" alt-text="Aggiungi valore del segreto dell'insieme di credenziali delle chiavi":::

### <a name="add-a-plain-or-secret-value"></a>Aggiungere un valore normale o segreto

1. Nel [portale di Azure](https://portal.azure.com) accedere all'istanza di Gestione API.
1. In **API** selezionare **valori denominati**  >  **+ Aggiungi**.
1. Immettere un identificatore **nome** e immettere un **nome visualizzato** usato per fare riferimento alla proprietà nei criteri.
1. In **tipo valore** selezionare **Plain** o **Secret**.
1. In **valore** immettere una stringa o un'espressione di criteri.
1. Aggiungere uno o più tag facoltativi per organizzare i valori denominati, quindi **salvare**.
1. Selezionare **Crea**.

Una volta creato il valore denominato, è possibile modificarlo selezionando il nome. Se si modifica il nome visualizzato, tutti i criteri che fanno riferimento a quel valore denominato vengono aggiornati automaticamente in modo da utilizzare il nuovo nome visualizzato.

## <a name="use-a-named-value"></a>Usa un valore denominato

Negli esempi di questa sezione vengono usati i valori denominati illustrati nella tabella seguente.

| Nome               | valore                      | Segreto | 
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | `TrackingId`                 | Falso  | 
| ContosoHeaderValue | ••••••••••••••••••••••     | True   | 
| ExpressionProperty | `@(DateTime.Now.ToString())` | Falso  | 

Per usare un valore denominato in un criterio, inserire il nome visualizzato all'interno di una coppia doppia di parentesi graffe `{{ContosoHeader}}` , come illustrato nell'esempio seguente:

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

I valori denominati possono anche contenere espressioni di criteri. Nell'esempio seguente `ExpressionProperty` viene utilizzata l'espressione.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Quando questo criterio viene valutato, `{{ExpressionProperty}}` viene sostituito con il relativo valore `@(DateTime.Now.ToString())` . Poiché il valore è un'espressione di criteri, l'espressione viene valutata e il criterio passa alla fase di esecuzione.

È possibile testare questa impostazione nel portale di Azure o nel [portale per sviluppatori](api-management-howto-developer-portal.md) chiamando un'operazione con un criterio con valori denominati nell'ambito. Nell'esempio seguente viene chiamata un'operazione che contiene i due criteri di esempio precedenti `set-header` con valori denominati. Si noti che la risposta contiene due intestazioni personalizzate che sono state configurate mediante criteri con valori denominati.

:::image type="content" source="media/api-management-howto-properties/api-management-send-results.png" alt-text="Risposta API di test":::

Se si esamina la [traccia API](api-management-howto-api-inspector.md) in uscita per una chiamata che include i due criteri di esempio precedenti con i valori denominati, è possibile visualizzare i due `set-header` criteri con i valori denominati inseriti e la valutazione dell'espressione di criteri per il valore denominato che contiene l'espressione di criteri.

:::image type="content" source="media/api-management-howto-properties/api-management-api-inspector-trace.png" alt-text="Traccia di Controllo API":::

> [!CAUTION]
> Se un criterio fa riferimento a un segreto in Azure Key Vault, il valore dell'insieme di credenziali delle chiavi sarà visibile agli utenti che hanno accesso alle sottoscrizioni abilitate per la [traccia delle richieste API](api-management-howto-api-inspector.md).

Sebbene i valori denominati possano contenere espressioni di criteri, non possono contenere altri valori denominati. Se il testo contenente un riferimento a un valore denominato viene usato per un valore, ad esempio `Text: {{MyProperty}}`, tale riferimento non verrà risolto e sostituito.

## <a name="delete-a-named-value"></a>Elimina un valore denominato

Per eliminare un valore denominato, selezionare il nome e quindi scegliere **Elimina** dal menu di scelta rapida (**...**).

> [!IMPORTANT]
> Se i criteri di gestione API fanno riferimento al valore denominato, non è possibile eliminarlo fino a quando non si rimuove il valore denominato da tutti i criteri che lo usano.

## <a name="next-steps"></a>Passaggi successivi

-   Ulteriori informazioni sull'uso dei criteri
    -   [Criteri di Gestione API](api-management-howto-policies.md)
    -   [Riferimento ai criteri](./api-management-policies.md)
    -   [Espressioni di criteri](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png

