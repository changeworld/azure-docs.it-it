---
title: Abilitare un'identità gestita per il routing degli eventi (anteprima)-portale
titleSuffix: Azure Digital Twins
description: Vedere come abilitare un'identità assegnata dal sistema per i dispositivi gemelli digitali di Azure e usarla per l'invio di eventi, usando il portale di Azure.
author: baanders
ms.author: baanders
ms.date: 1/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5c216661e45e5ed34a95e8a56002f3ad175b089a
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545913"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-portal"></a>Abilitare un'identità gestita per il routing degli eventi di Azure Digital gemelli (anteprima): portale di Azure

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

Questo articolo descrive come abilitare un' [identità assegnata dal sistema per un'istanza di dispositivi gemelli digitali di Azure](concepts-security.md#managed-identity-for-accessing-other-resources-preview) (attualmente in anteprima) e come usare l'identità quando si inviano eventi a destinazioni supportate, ad esempio l' [Hub eventi](../event-hubs/event-hubs-about.md), le destinazioni del [bus di servizio](../service-bus-messaging/service-bus-messaging-overview.md)   e il contenitore di archiviazione di [Azure](../storage/blobs/storage-blobs-introduction.md).

Questo articolo illustra il processo di utilizzo del [**portale di Azure**](https://portal.azure.com).

Ecco i passaggi descritti in questo articolo: 

1. Creare un'istanza di Azure Digital Twins con un'identità assegnata dal sistema o abilitare l'identità assegnata dal sistema in un'istanza di Azure Digital Twins esistente. 
1. Aggiungere un ruolo o ruoli appropriati all'identità. Ad esempio, assegnare il ruolo di **mittente dei dati dell'hub eventi di Azure** all'identità se l'endpoint è un hub eventi o il **ruolo di mittente dei dati del bus di servizio di Azure** se l'endpoint è il bus di servizio.
1. Creare un endpoint nei dispositivi gemelli digitali di Azure in grado di usare identità assegnate dal sistema per l'autenticazione.

## <a name="enable-system-managed-identities-for-an-instance"></a>Abilitare le identità gestite dal sistema per un'istanza 

Quando si Abilita un'identità assegnata dal sistema nell'istanza di Azure Digital Twins, Azure crea automaticamente un'identità in [Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md). Tale identità può quindi essere usata per eseguire l'autenticazione agli endpoint di Azure Digital gemelli per l'invio di eventi.

È possibile abilitare le identità gestite dal sistema per un'istanza di Azure Digital Twins **come parte dell'installazione iniziale dell'istanza** o **abilitarla in un secondo momento in un'istanza già esistente**.

Uno di questi metodi di creazione fornirà le stesse opzioni di configurazione e lo stesso risultato finale per l'istanza. In questa sezione viene descritto come eseguire entrambe le operazioni.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>Aggiungere un'identità gestita dal sistema durante la creazione dell'istanza

Questa sezione illustra come abilitare un'identità gestita dal sistema in un'istanza di Azure Digital Twins attualmente in fase di creazione. Questa sezione è incentrata sul passaggio di identità gestito del processo di creazione; per una procedura dettagliata completa per la creazione di una nuova istanza di dispositivi gemelli digitali di Azure, vedere [*procedura: configurare un'istanza e l'autenticazione*](how-to-set-up-instance-portal.md).

L'opzione identità gestita dal sistema si trova nella scheda **Avanzate** della configurazione dell'istanza.

In questa scheda selezionare l'opzione **on** per **identità gestita dal sistema** per attivare questa funzionalità.

:::image type="content" source="media/how-to-enable-managed-identities/create-instance-advanced.png" alt-text="Screenshot della portale di Azure che mostra la scheda Avanzate della finestra di dialogo Crea risorsa per i dispositivi gemelli digitali di Azure. C'è un'evidenziazione intorno al nome della scheda, l'opzione on per identità gestita dal sistema e i pulsanti di navigazione (Revisione + creazione, precedente, successiva: avanzata).":::

È quindi possibile usare i pulsanti di spostamento in basso per continuare con il resto dell'installazione dell'istanza.

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>Aggiungere un'identità gestita dal sistema a un'istanza esistente

In questa sezione si aggiungerà un'identità gestita dal sistema a un'istanza di Azure Digital Twins già esistente.

1. Per prima cosa, passare alla [portale di Azure](https://portal.azure.com) in un browser.

1. Cercare il nome dell'istanza nella barra di ricerca del portale e selezionarlo per visualizzarne i dettagli.

1. Selezionare **Identity (anteprima)** nel menu a sinistra.

1. In questa pagina selezionare l'opzione **on** per attivare questa funzionalità.

1. Premere il pulsante **Salva** e **Sì** per confermare.

    :::image type="content" source="media/how-to-enable-managed-identities/identity-digital-twins.png" alt-text="Screenshot della portale di Azure che mostra la pagina identità (anteprima) per un'istanza di dispositivi gemelli digitali di Azure. C'è un'evidenziazione intorno al nome della pagina nel menu di istanza di Azure Digital gemelli, l'opzione on per lo stato, il pulsante Salva e il pulsante Sì conferma.":::

Dopo aver salvato la modifica, verranno visualizzati più campi in questa pagina per le **autorizzazioni** e l' **ID oggetto** della nuova identità.

È possibile copiare l' **ID oggetto** da qui, se necessario, e usare il pulsante **autorizzazioni** per visualizzare i ruoli di Azure assegnati all'identità. Per configurare alcuni ruoli, passare alla sezione successiva.

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

>[!NOTE]
> Questa sezione deve essere completata da un utente di Azure con le autorizzazioni per gestire l'accesso degli utenti alle risorse di Azure (incluse le autorizzazioni di concessione e delega). I ruoli comuni che soddisfano questo requisito sono *proprietario*, *amministratore account* o la combinazione di *amministratore accesso utenti* e *collaboratore*. Per altre informazioni sui requisiti di autorizzazione per i ruoli di Azure Digital gemelli, vedere [*How-to: set up instance and Authentication*](how-to-set-up-instance-portal.md#prerequisites-permission-requirements).

Per assegnare un ruolo all'identità, iniziare aprendo il [portale di Azure](https://portal.azure.com).

1. Passare alla risorsa endpoint (hub eventi, argomento del bus di servizio o contenitore di archiviazione) cercando il nome nella barra di ricerca del portale. 
1. Selezionare **controllo di accesso (IAM)** nel menu a sinistra.
1. Selezionare il pulsante **+ Aggiungi** per aggiungere una nuova assegnazione di ruolo.

    :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-1.png" alt-text="Screenshot della portale di Azure che mostra la pagina controllo di accesso (IAM) per un hub eventi. Il pulsante + Aggiungi è evidenziato." lightbox="media/how-to-enable-managed-identities/add-role-assignment-1.png":::

1. Nella pagina **Aggiungi assegnazione ruolo** Digitare i valori seguenti:
    * **Ruolo**: selezionare il ruolo desiderato dal menu a discesa.
    * **Assegnare l'accesso a**: in **identità gestita assegnata dal sistema** selezionare **gemelli digitali**.
    * **Sottoscrizione**: selezionare la propria sottoscrizione. Vengono visualizzate tutte le identità gestite di Azure Digital gemelli nella sottoscrizione selezionata.
    * **Select**: selezionare l'identità gestita dell'istanza di Azure Digital Twins a cui viene assegnato il ruolo. Il nome dell'identità gestita corrisponde al nome dell'istanza, quindi scegliere il nome dell'istanza di Azure Digital gemelli. Quando si seleziona questa opzione, l'identità per l'istanza verrà visualizzata nella sezione **membri selezionati** nella parte inferiore del riquadro.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-2.png" alt-text="Compilazione dei campi elencati nella finestra di dialogo ' Aggiungi assegnazione ruolo '":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Al termine dell'immissione dei dettagli, selezionare **Salva**.

## <a name="create-an-endpoint-with-identity-based-authorization"></a>Creazione di un endpoint con autorizzazione basata sull'identità

Dopo aver configurato un'identità gestita dal sistema per l'istanza di Azure Digital Twins e averla assegnata ai ruoli appropriati, è possibile creare [endpoint](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) di Azure Digital gemelli in grado di usare l'identità per l'autenticazione. Questa opzione è disponibile solo per gli endpoint di tipo hub eventi e bus di servizio (non è supportata per griglia di eventi).

>[!NOTE]
> Non è possibile modificare un endpoint che è già stato creato con l'identità basata su chiave per passare all'autenticazione basata sull'identità. Quando l'endpoint viene creato per la prima volta, è necessario scegliere il tipo di autenticazione.

Seguire le [istruzioni per creare un endpoint di Azure Digital gemelli](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins).

Quando si arriva al passaggio del completamento dei dettagli richiesti per il tipo di endpoint, assicurarsi di selezionare **Identity-based** per il tipo di autenticazione.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="Screenshot della creazione di un endpoint di tipo hub eventi." lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Completare la configurazione dell'endpoint e selezionare **Salva**.

## <a name="considerations-for-disabling-system-managed-identities"></a>Considerazioni per la disabilitazione delle identità gestite dal sistema

Poiché un'identità viene gestita separatamente dagli endpoint che la usano, è importante considerare gli effetti che qualsiasi modifica apportata all'identità o ai relativi ruoli può avere sugli endpoint nell'istanza di Azure Digital gemelli. Se l'identità è disabilitata o un ruolo necessario per un endpoint viene rimosso da tale identità, l'endpoint può diventare inaccessibile e il flusso di eventi verrà danneggiato.

Per continuare a usare un endpoint configurato con un'identità gestita che ora è stata disabilitata, è necessario eliminare l'endpoint e [ricrearlo](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) con un tipo di autenticazione diverso. Potrebbe essere necessaria fino a un'ora prima che gli eventi riprendano il recapito all'endpoint dopo questa modifica.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle identità gestite sono disponibili in Azure AD: 
* [*Identità gestite per le risorse di Azure*](../active-directory/managed-identities-azure-resources/overview.md)