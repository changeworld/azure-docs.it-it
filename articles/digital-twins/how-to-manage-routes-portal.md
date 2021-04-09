---
title: Gestire endpoint e Route (portale)
titleSuffix: Azure Digital Twins
description: Vedere come configurare e gestire gli endpoint e le route di eventi per i dati di Azure Digital gemelli usando il portale di Azure.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 31f960b39e771e7bfbf67c6e52c5da8e1fc6e0ec
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932455"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-portal"></a>Gestire endpoint e route nei dispositivi gemelli digitali di Azure (portale)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Nei dispositivi gemelli digitali di Azure è possibile instradare le [notifiche degli eventi](how-to-interpret-event-data.md) ai servizi downstream o alle risorse di calcolo connesse. Questa operazione viene eseguita configurando prima di tutto gli **endpoint** che possono ricevere gli eventi. È quindi possibile creare [**Route di eventi**](concepts-route-events.md) che specificano gli eventi generati dai dispositivi gemelli digitali di Azure che vengono recapitati a quali endpoint.

Questo articolo illustra il processo di creazione di endpoint e route usando il [portale di Azure](https://portal.azure.com).

In alternativa, è anche possibile gestire endpoint e route con le [API route di eventi](/rest/api/digital-twins/dataplane/eventroutes), gli [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis)o l'interfaccia della riga di comando di [Azure Digital gemelli](how-to-use-cli.md). Per una versione di questo articolo che usa questi meccanismi anziché il portale, vedere [*How-to: Manage Endpoints and routes (API e CLI)*](how-to-manage-routes-apis-cli.md).

## <a name="prerequisites"></a>Prerequisiti

* È necessario un **account Azure** (è possibile impostarne uno gratuitamente [qui](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
* È necessaria un' **istanza di Azure Digital Twins** nella sottoscrizione di Azure. Se non si dispone già di un'istanza, è possibile crearne una usando la procedura descritta in [*procedura: configurare un'istanza e l'autenticazione*](how-to-set-up-instance-portal.md). Per usare più avanti in questo articolo, è possibile usare i valori seguenti del programma di installazione:
    - Nome istanza
    - Resource group

È possibile trovare questi dettagli nell' [portale di Azure](https://portal.azure.com) dopo aver configurato l'istanza di. Accedere al portale e cercare il nome dell'istanza nella barra di ricerca del portale.
 
:::image type="content" source="media/how-to-manage-routes-portal/search-field-portal.png" alt-text="Screenshot della barra di ricerca portale di Azure." lightbox="media/how-to-manage-routes-portal/search-field-portal.png":::

Selezionare l'istanza dai risultati per visualizzare questi dettagli nella panoramica dell'istanza:

:::image type="content" source="media/how-to-manage-routes-portal/instance-details.png" alt-text="Screenshot della pagina di panoramica per un'istanza di Azure Digital gemelli. Il nome e il gruppo di risorse sono evidenziati.":::

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Creare un endpoint per i dispositivi gemelli digitali di Azure

Questi sono i tipi di endpoint supportati che è possibile creare per l'istanza:
* [Griglia di eventi](../event-grid/overview.md) 
* [Hub eventi](../event-hubs/event-hubs-about.md)
* [Bus di servizio](../service-bus-messaging/service-bus-messaging-overview.md)

Per altre informazioni sui diversi tipi di endpoint, vedere [*scegliere tra i servizi di messaggistica di Azure*](../event-grid/compare-messaging-services.md).

In questa sezione viene illustrato come creare uno di questi endpoint nel [portale di Azure](https://portal.azure.com).

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>Creare l'endpoint 

Dopo aver creato le risorse dell'endpoint, è possibile usarle per un endpoint di Azure Digital gemelli. Per creare un nuovo endpoint, passare alla pagina dell'istanza nel [portale di Azure](https://portal.azure.com) (è possibile trovare l'istanza immettendone il nome nella barra di ricerca del portale).

1. Scegliere _endpoint_ dal menu istanza. Quindi, dalla pagina *endpoint* che segue, selezionare *+ Crea un endpoint*. Verrà visualizzata la pagina *Crea un endpoint* in cui inserire i campi nei passaggi seguenti.

    :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-grid.png" alt-text="Screenshot della creazione di un endpoint di tipo griglia di eventi." lightbox="media/how-to-manage-routes-portal/create-endpoint-event-grid.png":::

1. Immettere un **nome** per l'endpoint e scegliere il **tipo di endpoint**.

1. Completare gli altri dettagli richiesti per il tipo di endpoint, incluse la sottoscrizione e le risorse dell'endpoint descritte in [precedenza](#prerequisite-create-endpoint-resources).
    1. Solo per gli endpoint dell'hub eventi e del bus di servizio, è necessario selezionare un **tipo di autenticazione**. È possibile usare l'autenticazione basata su chiave con una regola di autorizzazione creata in precedenza oppure l'autenticazione basata su identità se si userà l'endpoint con un' [identità gestita](concepts-security.md#managed-identity-for-accessing-other-resources-preview) per l'istanza di Azure Digital gemelli. 

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="Screenshot della creazione di un endpoint di tipo hub eventi." lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

1. Per completare la creazione dell'endpoint, selezionare _Salva_.

>[!IMPORTANT]
> Per usare correttamente l'autenticazione basata su identità per l'endpoint, è necessario creare un'identità gestita per l'istanza attenendosi alla procedura illustrata in [*procedura: abilitare un'identità gestita per il routing degli eventi (anteprima)*](./how-to-enable-managed-identities-portal.md).

Dopo aver creato l'endpoint, è possibile verificare che l'endpoint sia stato creato correttamente selezionando l'icona di notifica nella barra portale di Azure superiore: 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-notifications.png" alt-text="Screenshot della notifica per verificare la creazione dell'endpoint. L'icona a forma di campana dalla barra superiore del portale è selezionata ed è presente una notifica che indica che l'endpoint ADT-eh-endpoint è stato creato correttamente.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Se la creazione dell'endpoint ha esito negativo, osservare il messaggio di errore e riprovare dopo alcuni minuti.

È anche possibile visualizzare l'endpoint che è stato creato nuovamente nella pagina *endpoint* per l'istanza di Azure Digital gemelli.

Ora l'argomento griglia di eventi, Hub eventi o bus di servizio è disponibile come endpoint all'interno di Azure Digital gemelli, sotto il nome scelto per l'endpoint. Questo nome viene in genere usato come destinazione di una **Route di eventi**, che verrà creata [più avanti in questo articolo](#create-an-event-route).

### <a name="create-an-endpoint-with-dead-lettering"></a>Creazione di un endpoint con messaggi non recapitabili

Quando un endpoint non è in grado di recapitare un evento entro un determinato periodo di tempo o dopo il tentativo di recapitare l'evento un certo numero di volte, può inviare l'evento non recapitato a un account di archiviazione. Questo processo è noto come **messaggio non recapitabile**.

Per creare un endpoint con i messaggi non recapitabili abilitati, è necessario usare i [comandi dell'interfaccia](how-to-use-cli.md) della riga di comando o le API del piano di [controllo](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) per creare l'endpoint, anziché il portale di Azure.

Per istruzioni su come eseguire questa operazione con questi strumenti, vedere le [*API e*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering) la versione dell'interfaccia della riga di comando di questo articolo.

## <a name="create-an-event-route"></a>Creare una route di eventi

Per inviare effettivamente i dati dai dispositivi gemelli digitali di Azure a un endpoint, è necessario definire una **route dell'evento**. Queste route consentono agli sviluppatori di collegare il flusso degli eventi, in tutto il sistema e ai servizi downstream. Per altre informazioni sulle route di eventi, vedere [*concetti relativi al routing di eventi gemelli digitali di Azure*](concepts-route-events.md).

**Prerequisito**: è necessario creare endpoint come descritto in precedenza in questo articolo prima di poter passare alla creazione di una route. È possibile procedere alla creazione di una route di eventi al termine della configurazione degli endpoint.

>[!NOTE]
>Se gli endpoint sono stati distribuiti di recente, verificare che la distribuzione sia terminata **prima** di provare a usarli per una nuova route di eventi. Se non si è in grado di configurare la route perché gli endpoint non sono pronti, attendere qualche minuto e riprovare.

### <a name="creation-steps-with-the-azure-portal"></a>Procedura di creazione con il portale di Azure

Una definizione di route dell'evento contiene gli elementi seguenti:
* Nome della route che si vuole usare
* Nome dell'endpoint che si desidera utilizzare
* Filtro che definisce gli eventi che vengono inviati all'endpoint
    - Per disabilitare la route in modo che non venga inviato alcun evento, usare un valore di filtro `false`
    - Per abilitare una route senza filtri specifici, usare un valore di filtro `true`
    - Per informazioni dettagliate su qualsiasi altro tipo di filtro, vedere la sezione relativa [*agli eventi di filtro*](#filter-events) riportata di seguito.

Una singola route può consentire la selezione di più notifiche e tipi di evento.

Per creare una route di eventi, passare alla pagina dei dettagli per l'istanza di Azure Digital Twins nel [portale di Azure](https://portal.azure.com) (è possibile trovare l'istanza inserendo il nome nella barra di ricerca del portale).

Scegliere _Route eventi_ dal menu istanza. Dalla pagina *Route eventi* seguente selezionare *+ Crea una route* per gli eventi. 

Nella pagina *Crea una route di evento* visualizzata, scegliere almeno:
* Nome della route nel campo del _nome_
* L' _endpoint_ che si vuole usare per creare la route 

Per abilitare la route, è necessario **aggiungere anche un filtro di route di un evento** almeno `true` . Se si lascia il valore predefinito `false` , verrà creata la route, ma non verrà inviato alcun evento. A tale scopo, attivare l'opzione _Editor avanzato_ per abilitarla e scrivere `true` nella casella *filtro* .

:::image type="content" source="media/how-to-manage-routes-portal/create-event-route-no-filter.png" alt-text="Screenshot della creazione della route dell'evento per l'istanza." lightbox="media/how-to-manage-routes-portal/create-event-route-no-filter.png":::

Al termine, fare clic sul pulsante _Salva_ per creare la route dell'evento.

## <a name="filter-events"></a>Filtrare gli eventi

Come descritto in precedenza, le route includono un campo di **filtro** . Se il valore del filtro nella route è `false` , nessun evento verrà inviato all'endpoint. 

Dopo aver abilitato il filtro minimo di `true` , gli endpoint riceveranno una serie di eventi da dispositivi gemelli digitali di Azure:
* Telemetria generata dai dispositivi [gemelli digitali](concepts-twins-graph.md) con l'API del servizio di dispositivi digitali gemelli di Azure
* Notifiche delle modifiche delle proprietà dei dispositivi gemelli, attivate per le modifiche delle proprietà per qualsiasi dispositivo gemello nell'istanza di Azure
* Eventi del ciclo di vita, generati quando vengono creati o eliminati gemelli o relazioni

È possibile limitare i tipi di eventi inviati definendo un filtro più specifico.

Per aggiungere un filtro eventi durante la creazione di una route dell'evento, usare la sezione _aggiungere un filtro di route_ per gli eventi della pagina *creare una route* di evento. 

È possibile scegliere tra le opzioni di filtro comuni di base oppure usare le opzioni di filtro avanzate per scrivere filtri personalizzati.

>[!NOTE]
> I filtri fanno **distinzione tra maiuscole** e minuscole e devono corrispondere al caso del payload. 
>
> Per i filtri di telemetria, ciò significa che la combinazione di maiuscole e minuscole deve corrispondere a quella nei dati di telemetria inviati dal dispositivo, non necessariamente all'involucro definito nel modello del gemello.

#### <a name="use-the-basic-filters"></a>Usare i filtri di base

Per usare i filtri di base, espandere l'opzione _tipi di evento_ e selezionare le caselle di controllo corrispondenti agli eventi che si desidera inviare all'endpoint. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-1.png" alt-text="Screenshot della creazione di una route di eventi con un filtro di base. Selezione delle caselle di controllo degli eventi.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

La casella di testo filtro verrà popolata automaticamente con il testo del filtro selezionato:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-2.png" alt-text="Screenshot della creazione di una route di eventi con un filtro di base. Visualizzazione del testo del filtro popolato automaticamente dopo aver selezionato gli eventi.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="use-the-advanced-filters"></a>Usare i filtri avanzati

In alternativa, è possibile usare l'opzione filtro avanzato per scrivere filtri personalizzati.

Per creare una route di eventi con opzioni di filtro avanzate, attivare o disattivare l'opzione per l' _Editor avanzato_ . È quindi possibile scrivere filtri eventi personalizzati nella casella *filtro* :

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-advanced.png" alt-text="Screenshot della creazione di una route di eventi con un filtro avanzato.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Ecco i filtri di route supportati. Il dettaglio nella colonna *schema testo filtro* è il testo che è possibile immettere nella casella filtro.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui diversi tipi di messaggi di evento che è possibile ricevere, vedere:
* [*Procedura: interpretare i dati degli eventi*](how-to-interpret-event-data.md)