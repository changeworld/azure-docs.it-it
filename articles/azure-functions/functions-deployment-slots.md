---
title: Funzioni di Azure slot di distribuzione
description: Informazioni su come creare e usare gli slot di distribuzione con Funzioni di Azure
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.openlocfilehash: 5f003b68283217f7877dc650ae4f07ddc5a31012
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789340"
---
# <a name="azure-functions-deployment-slots"></a>Funzioni di Azure slot di distribuzione

Funzioni di Azure slot di distribuzione consentono all'app per le funzioni di eseguire istanze diverse denominate "slot". Gli slot sono ambienti diversi esposti tramite un endpoint disponibile pubblicamente. Viene sempre eseguito il mapping di un'istanza dell'app nello slot di produzione ed è possibile scambiare le istanze assegnate a uno slot su richiesta. Le app per le funzioni in esecuzione nel piano di servizio app possono avere più slot, mentre nel piano a consumo è consentito un solo slot.

Di seguito viene illustrato il modo in cui le funzioni sono interessate dallo scambio di slot:

- Il reindirizzamento del traffico è facile. nessuna richiesta viene eliminata a causa di uno scambio.
- Se una funzione è in esecuzione durante uno scambio, l'esecuzione continua e i trigger successivi vengono instradati all'istanza dell'app scambiata.

## <a name="why-use-slots"></a>Perché usare gli slot?

L'uso degli slot di distribuzione offre numerosi vantaggi. Gli scenari seguenti descrivono gli usi comuni degli slot:

- **Ambienti diversi per scopi diversi:** l'uso di slot diversi offre la possibilità di differenziare le istanze dell'app prima di passare a uno slot di produzione o di staging.
- **Prewarming:** la distribuzione in uno slot anziché direttamente nell'ambiente di produzione consente all'app di riscaldamento prima di passare alla fase live. Inoltre, l'uso degli slot riduce la latenza per i carichi di lavoro attivati da HTTP. Le istanze vengono riscaldamento prima della distribuzione, riducendo così l'avvio a freddo per le funzioni appena distribuite.
- **Fallback semplici: dopo** uno scambio con l'ambiente di produzione, lo slot con un'app di cui è stata precedentemente creata una fase di produzione ha ora l'app di produzione precedente. Se le modifiche scambiate nello slot di produzione non sono come previsto, è possibile invertire immediatamente lo scambio per ottenere l'ultima istanza buona nota.

## <a name="swap-operations"></a>Operazioni di scambio

Durante uno scambio, uno slot viene considerato l'origine e l'altro la destinazione. Lo slot di origine ha l'istanza dell'applicazione applicata nello slot di destinazione. I passaggi seguenti assicurano che lo slot di destinazione non si verifichi un tempo di inattività durante uno scambio:

1. **Applicare le impostazioni:** Le impostazioni dello slot di destinazione vengono applicate a tutte le istanze dello slot di origine. Ad esempio, le impostazioni di produzione vengono applicate all'istanza di staging. Le impostazioni applicate includono le categorie seguenti:
    - [Impostazioni dell'app specifiche](#manage-settings) dello slot e stringhe di connessione (se applicabile)
    - [Impostazioni di distribuzione](../app-service/deploy-continuous-deployment.md) continua (se abilitata)
    - [Impostazioni di autenticazione del servizio](../app-service/overview-authentication-authorization.md) app (se abilitate)

1. **Attendere i riavvii e la disponibilità:** Lo scambio attende che ogni istanza nello slot di origine completi il riavvio e sia disponibile per le richieste. Se un'istanza non viene riavviata, l'operazione di scambio ripristina tutte le modifiche apportate nello slot di origine e arresta l'operazione.

1. **Routing di aggiornamento:** Se tutte le istanze nello slot di origine vengono riscaldate correttamente, i due slot completano lo scambio passando le regole di routing. Dopo questo passaggio, lo slot di destinazione (ad esempio, lo slot di produzione) ha l'app precedentemente riscaldata nello slot di origine.

1. **Ripetere l'operazione:** Ora che lo slot di origine ha l'app di pre-scambio in precedenza nello slot di destinazione, completare la stessa operazione applicando tutte le impostazioni e riavviando le istanze per lo slot di origine.

Tenere presente quanto segue:

- In qualsiasi momento dell'operazione di scambio, l'inizializzazione delle app scambiate avviene nello slot di origine. Lo slot di destinazione rimane online durante la preparazione dello slot di origine, indipendentemente dal fatto che lo scambio riesca o meno.

- Per scambiare uno slot di staging con lo slot di produzione, assicurarsi che lo slot di produzione *sia sempre* lo slot di destinazione. In questo modo, l'operazione di scambio non influisce sull'app di produzione.

- Le impostazioni correlate alle origini eventi e alle associazioni devono essere configurate come impostazioni [dello slot](#manage-settings) di distribuzione prima di avviare *uno scambio.* Contrassegnarli come "sticky" in anticipo garantisce che gli eventi e gli output siano indirizzati all'istanza appropriata.

## <a name="manage-settings"></a>Gestire le impostazioni

Alcune impostazioni di configurazione sono specifiche dello slot. Di seguito sono elencate in dettaglio le impostazioni che cambiano quando si scambiano gli slot e che rimangono invariate.

**Impostazioni specifiche dello slot:**

* Endpoint di pubblicazione
* Nomi di dominio personalizzati
* Certificati non pubblici e impostazioni TLS/SSL
* Impostazioni di scalabilità
* Utilità di pianificazione WebJobs
* Restrizioni IP
* Always On
* Impostazioni di diagnostica
* Condivisione di risorse tra le origini (CORS)

**Impostazioni non specifiche dello slot:**

* Impostazioni generali, ad esempio la versione del framework, i Web Socket a 32/64 bit
* Impostazioni app (possono essere configurate per adattarsi a uno slot)
* Stringhe di connessione (possono essere configurate per adattarsi a uno slot)
* Mapping dei gestori
* Certificati pubblici
* Contenuto WebJobs
* Connessioni ibride *
* Integrazione della rete virtuale *
* Endpoint di servizio *
* Rete per la distribuzione di contenuti di Azure *

Le funzionalità contrassegnate con un asterisco (*) sono pianificate per l'annullamento delwapped. 

> [!NOTE]
> Alcune impostazioni dell'app che si applicano alle impostazioni non scaricate non vengono scambiate. Ad esempio, poiché le impostazioni di diagnostica non vengono scambiate, anche le impostazioni dell'app correlate come e non vengono scambiate, anche se non vengono mostrate `WEBSITE_HTTPLOGGING_RETENTION_DAYS` `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` come impostazioni dello slot.
>

### <a name="create-a-deployment-setting"></a>Creare un'impostazione di distribuzione

È possibile contrassegnare le impostazioni come un'impostazione di distribuzione, che lo rende "sticky". Un'impostazione sticky non viene scambiata con l'istanza dell'app.

Se si crea un'impostazione di distribuzione in uno slot, assicurarsi di creare la stessa impostazione con un valore univoco in qualsiasi altro slot coinvolto in uno scambio. In questo modo, anche se il valore di un'impostazione non cambia, i nomi delle impostazioni rimangono coerenti tra gli slot. Questa coerenza dei nomi garantisce che il codice non provi ad accedere a un'impostazione definita in uno slot ma non in un altro.

Per creare un'impostazione di distribuzione, seguire questa procedura:

1. Passare a **Slot di distribuzione nell'app** per le funzioni e quindi selezionare il nome dello slot.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Trovare gli slot nella portale di Azure." border="true":::

1. Selezionare **Configurazione** e quindi selezionare il nome dell'impostazione da inserire nello slot corrente.

    :::image type="content" source="./media/functions-deployment-slots/functions-configure-deployment-slot.png" alt-text="Configurare l'impostazione dell'applicazione per uno slot nel portale di Azure." border="true":::

1. Selezionare **Impostazione slot di distribuzione** e quindi selezionare **OK.**

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-setting.png" alt-text="Configurare l'impostazione dello slot di distribuzione." border="true":::

1. Quando la sezione delle impostazioni scompare, selezionare **Salva** per mantenere le modifiche

    :::image type="content" source="./media/functions-deployment-slots/functions-save-deployment-slot-setting.png" alt-text="Salvare l'impostazione dello slot di distribuzione." border="true":::

## <a name="deployment"></a>Distribuzione

Gli slot sono vuoti quando si crea uno slot. È possibile usare una qualsiasi delle tecnologie [di distribuzione supportate](./functions-deployment-technologies.md) per distribuire l'applicazione in uno slot.

## <a name="scaling"></a>Ridimensionamento

Tutti gli slot vengono ridimensionati allo stesso numero di worker dello slot di produzione.

- Per i piani a consumo, lo slot viene ridimensionato con la scalabilità dell'app per le funzioni.
- Per i piani di servizio app, l'app viene ridimensionata a un numero fisso di dipendenti. Gli slot vengono eseguiti nello stesso numero di dipendenti del piano dell'app.

## <a name="add-a-slot"></a>Aggiungi uno slot

È possibile aggiungere uno slot tramite l'interfaccia della [riga di comando](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_create) o tramite il portale. La procedura seguente illustra come creare un nuovo slot nel portale:

1. Passare all'app per le funzioni.

1. Selezionare **Slot di distribuzione** e quindi + Aggiungi **slot**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add.png" alt-text="Aggiungere Funzioni di Azure slot di distribuzione." border="true":::

1. Digitare il nome dello slot e selezionare **Aggiungi**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add-name.png" alt-text="Assegnare un nome Funzioni di Azure slot di distribuzione." border="true":::

## <a name="swap-slots"></a>Slot di scambio

È possibile scambiare gli slot tramite l'interfaccia [della riga di](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_swap) comando o tramite il portale. La procedura seguente illustra come scambiare gli slot nel portale:

1. Passare all'app per le funzioni.
1. Selezionare **Slot di distribuzione** e quindi Scambia. 

    :::image type="content" source="./media/functions-deployment-slots/functions-swap-deployment-slot.png" alt-text="Screenshot che mostra la pagina &quot;Slot di distribuzione&quot; con l'azione &quot;Aggiungi slot&quot; selezionata." border="true":::

1. Verificare le impostazioni di configurazione per lo scambio e selezionare **Scambia**
    
    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png" alt-text="Scambiare lo slot di distribuzione." border="true":::

L'operazione può richiedere qualche istante durante l'esecuzione dell'operazione di scambio.

## <a name="roll-back-a-swap"></a>Eseguire il rollback di uno scambio

Se uno scambio comporta un errore o si vuole semplicemente "annullare" uno scambio, è possibile eseguire il rollback allo stato iniziale. Per tornare allo stato pre-scambiato, eseguire un altro scambio per invertire lo scambio.

## <a name="remove-a-slot"></a>Rimuovere uno slot

È possibile rimuovere uno slot tramite l'interfaccia [della riga di comando](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_delete) o tramite il portale. La procedura seguente illustra come rimuovere uno slot nel portale:

1. Passare a **Slot di distribuzione nell'app** per le funzioni e quindi selezionare il nome dello slot.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Trovare gli slot nella portale di Azure." border="true":::

1. Selezionare **Elimina**.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot.png" alt-text="Screenshot che mostra la pagina &quot;Panoramica&quot; con l'azione &quot;Elimina&quot; selezionata." border="true":::

1. Digitare il nome dello slot di distribuzione da eliminare e quindi selezionare **Elimina.**

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot-details.png" alt-text="Eliminare lo slot di distribuzione nel portale di Azure." border="true":::

1. Chiudere il riquadro di conferma dell'eliminazione.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-deleted.png" alt-text="Conferma dell'eliminazione dello slot di distribuzione." border="true":::

## <a name="automate-slot-management"></a>Automatizzare la gestione degli slot

Usando [l'interfaccia della riga di](/cli/azure/functionapp/deployment/slot)comando di Azure è possibile automatizzare le azioni seguenti per uno slot:

- [create](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_create)
- [delete](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_delete)
- [list](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_list)
- [Swap](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_swap)
- [scambio automatico](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_auto_swap)

## <a name="change-app-service-plan"></a>Modificare il piano di servizio app

Con un'app per le funzioni in esecuzione in un piano di servizio app, è possibile modificare il piano di servizio app sottostante per uno slot.

> [!NOTE]
> Non è possibile modificare il piano di servizio app di uno slot nel piano a consumo.

Usare la procedura seguente per modificare il piano di servizio app di uno slot:

1. Passare a **Slot di distribuzione nell'app** per le funzioni e quindi selezionare il nome dello slot.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Trovare gli slot nella portale di Azure." border="true":::

1. In **Piano di servizio app** selezionare Modifica piano di servizio **app**.

1. Selezionare il piano a cui si vuole eseguire l'aggiornamento o crearne uno nuovo.

    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-apply.png" alt-text="Modificare il piano di servizio app nel portale di Azure." border="true":::

1. Selezionare **OK**.

## <a name="limitations"></a>Limitazioni

Funzioni di Azure slot di distribuzione hanno le limitazioni seguenti:

- Il numero di slot disponibili per un'app dipende dal piano. Al piano a consumo è consentito un solo slot di distribuzione. Sono disponibili slot aggiuntivi per le app in esecuzione nel piano di servizio app.
- Lo scambio di uno slot reimposta le chiavi per le app con `AzureWebJobsSecretStorageType` un'impostazione dell'app uguale a `files` .
- Gli slot non sono disponibili per il piano a consumo per Linux distribuzione.

## <a name="support-levels"></a>Livelli di supporto

Sono disponibili due livelli di supporto per gli slot di distribuzione:

- **Disponibilità generale: completamente supportata** e approvata per l'uso in produzione.
- **Anteprima:** non ancora supportata, ma dovrebbe raggiungere lo stato disponibile a livello globale in futuro.

| Piano di sistema operativo/hosting           | Livello di supporto     |
| ------------------------- | -------------------- |
| Utilizzo di Windows       | Disponibilità generale |
| Windows Premium           | Disponibilità generale  |
| Windows dedicato         | Disponibilità generale |
| a consumo per Linux         | Anteprima          |
| Linux Premium             | Disponibilità generale  |
| Linux dedicato           | Disponibilità generale |

## <a name="next-steps"></a>Passaggi successivi

- [Tecnologie di distribuzione in Funzioni di Azure](./functions-deployment-technologies.md)
