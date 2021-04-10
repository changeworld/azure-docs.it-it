---
title: Creazione di trigger di evento personalizzati in Azure Data Factory
description: Informazioni su come creare un trigger personalizzato in Azure Data Factory che esegue una pipeline in risposta a un evento personalizzato pubblicato in griglia di eventi.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 2d2f26b24e7fa10d9244de8f99d78c64a44b3d61
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785649"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-custom-event-preview"></a>Creare un trigger per l'esecuzione di una pipeline in risposta a un evento personalizzato (anteprima)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo descrive i trigger di evento personalizzati che è possibile creare nelle pipeline Data Factory.

Un'architettura guidata dagli eventi è un comune modello di integrazione dei dati che implica produzione, rilevamento, utilizzo e risposta agli eventi. Gli scenari di integrazione dei dati richiedono spesso Data Factory ai clienti di attivare pipeline in base a determinati eventi che si verificano. Data Factory integrazione nativa con [griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/) ora copre [gli eventi personalizzati](../event-grid/custom-topics.md): i clienti inviano eventi arbitrari a un argomento di griglia di eventi e data factory sottoscrive e ascolta l'argomento e attiva le pipeline di conseguenza.

> [!NOTE]
> L'integrazione descritta in questo articolo dipende dalla [Griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/). Verificare che la sottoscrizione sia registrata con il provider di risorse di Griglia di eventi. Per altre informazioni, vedere [Provider e tipi di risorse](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). È necessario essere in grado di eseguire l'azione *Microsoft. EventGrid/eventSubscriptions/**. Questa azione fa parte del ruolo predefinito collaboratore sottoscrizione evento di EventGrid.

Inoltre, combinando parametri della pipeline e trigger di evento personalizzati, i clienti possono analizzare e fare riferimento al payload di _dati_ personalizzati nelle esecuzioni di pipeline. il campo _dati_ nel payload dell'evento personalizzato è una struttura chiave-valore JSON in formato libero che offre ai clienti il controllo massimo sulle esecuzioni di pipeline guidate dagli eventi.

> [!IMPORTANT]
> Ogni tanto, una chiave a cui si fa riferimento nella parametrizzazione potrebbe mancare nel payload dell'evento personalizzato. L' _esecuzione del trigger_ avrà esito negativo e verrà visualizzato un errore che informa che non è possibile valutare l'espressione perché il _nome_ della proprietà non esiste. L'evento __non attiverà alcuna__ _esecuzione della pipeline_ .

## <a name="setup-event-grid-custom-topic"></a>Configurare l'argomento personalizzato di griglia di eventi

Per usare il trigger di evento personalizzato in Data Factory, è necessario _innanzitutto_ configurare un [argomento personalizzato in griglia di eventi](../event-grid/custom-topics.md). Il flusso di lavoro è diverso da quello dell'evento di archiviazione, in cui Data Factory configurerà l'argomento. Qui è necessario esplorare la griglia di eventi di Azure e creare l'argomento. Per altre informazioni su come creare l'argomento personalizzato, vedere [esercitazioni del portale](../event-grid/custom-topics.md#azure-portal-tutorials) di griglia di eventi di Azure e [esercitazioni sull'interfaccia](../event-grid/custom-topics.md#azure-cli-tutorials) della riga di comando

Le Data Factory prevedono che gli eventi seguano lo schema di eventi di [griglia di eventi](../event-grid/event-schema.md) Verificare che i payload dell'evento includano i campi seguenti.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

## <a name="data-factory-ui"></a>Interfaccia utente di Data Factory

Questa sezione illustra come creare un trigger di evento di archiviazione all'interno dell'interfaccia utente di Azure Data Factory.

1. Passare alla scheda **modifica** , mostrata con un simbolo a matita.

1. Selezionare **trigger** dal menu, quindi selezionare **nuovo/modifica**.

1. Nella pagina **Aggiungi trigger** selezionare **Scegli trigger**, quindi selezionare **+ nuovo**.

1. Selezionare il tipo di trigger **eventi personalizzati**

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-1-creation.png" alt-text="Screenshot della pagina Author (autore) per creare un nuovo trigger di evento personalizzato nell'interfaccia utente di Data Factory." lightbox="media/how-to-create-custom-event-trigger/custom-event-1-creation-expanded.png":::

1. Selezionare l'argomento personalizzato dall'elenco a discesa sottoscrizione di Azure o immettere manualmente l'ambito dell'argomento dell'evento.

   > [!NOTE]
   > Per creare un trigger di evento personalizzato o modificarne uno esistente, l'account Azure usato per accedere a Data Factory e pubblicare il trigger dell'evento di archiviazione deve avere l'autorizzazione appropriata per il controllo degli accessi in base al ruolo (RBAC di Azure) per l'argomento. Non è necessaria alcuna autorizzazione aggiuntiva: l'entità servizio per la Azure Data Factory _non necessita di_ autorizzazioni speciali per griglia di eventi. Per altre informazioni sul controllo di accesso, vedere la sezione [controllo degli accessi in base al ruolo](#role-based-access-control) .

1. Il **soggetto inizia con** e le **estremità dell'oggetto con** proprietà consentono di filtrare gli eventi per i quali si desidera attivare la pipeline. Entrambe le proprietà sono facoltative.

1. Usare **+ nuovo** per aggiungere i **tipi di evento** che si desidera filtrare. Dipendente del trigger di evento personalizzato relazione o per l'elenco: se un evento personalizzato ha una proprietà _eventType_ che corrisponde a qualsiasi oggetto elencato, attiverà un'esecuzione della pipeline. Il tipo di evento non fa distinzione tra maiuscole e minuscole. Nello screenshot seguente, ad esempio, il trigger corrisponde a tutti gli eventi _copycompleted_ o _copysucceeded_ con il soggetto inizia con _Factory_

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-2-properties.png" alt-text="Screenshot della pagina Modifica trigger per spiegare i tipi di evento e i filtri oggetto nell'interfaccia utente di Data Factory.":::

1. Il trigger di evento personalizzato può analizzare e inviare il payload di _dati_ personalizzato alla pipeline. Creare innanzitutto i parametri della pipeline e compilare i valori nella pagina **parametri** . Utilizzare Format **@triggerBody (). event. Data._Nome_** dell'oggetto per analizzare il payload dei dati e passare i valori ai parametri della pipeline. Per una spiegazione dettagliata, vedere [metadati del trigger di riferimento nelle pipeline](how-to-use-trigger-parameterization.md) e [variabili di sistema nel trigger di evento personalizzato](control-flow-system-variables.md#custom-event-trigger-scope) .

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-4-trigger-values.png" alt-text="Screenshot dell'impostazione dei parametri della pipeline.":::

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-3-parameters.png" alt-text="Screenshot della pagina dei parametri per fare riferimento al payload dei dati in un evento personalizzato.":::

1. Al termine, fare clic su **OK** .

## <a name="json-schema"></a>Schema JSON

Nella tabella seguente viene fornita una panoramica degli elementi dello schema correlati ai trigger di evento personalizzati:

| **Elemento JSON** | **Descrizione** | **Tipo** | **Valori consentiti** | **Obbligatorio** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **ambito** | ID della risorsa Azure Resource Manager dell'argomento di griglia di eventi. | string | ID Azure Resource Manager | Sì |
| **eventi** | Tipo di eventi che provocano l'attivazione del trigger. | Matrice di stringhe    |  | Sì, è previsto almeno un valore |
| **subjectBeginsWith** | Il campo oggetto deve iniziare con il modello fornito per il trigger da attivare. Ad esempio, in viene `factories` attivato solo il trigger per l'oggetto dell'evento che inizia con `factories` . | string   | | No |
| **subjectEndsWith** | Il campo oggetto deve terminare con il modello fornito per il trigger da attivare. | string   | | No |

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Azure Data Factory usa il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per garantire che l'accesso non autorizzato ad ascolto, sottoscrizione degli aggiornamenti da e trigger di pipeline collegati ad eventi personalizzati sia strettamente vietato.

* Per creare un nuovo trigger di evento personalizzato o aggiornarne uno esistente, l'account Azure che ha eseguito l'accesso al Data Factory deve avere l'accesso appropriato all'account di archiviazione pertinente. In caso contrario, l'operazione con ha esito negativo con _accesso negato_.
* Data Factory non richiede alcuna autorizzazione speciale per la griglia di eventi e _non_ è necessario assegnare l'autorizzazione RBAC di Azure speciale per data factory entità servizio per l'operazione.

In particolare, il cliente necessita dell'autorizzazione _Microsoft. EventGrid/EventSubscriptions/Write_ per _/Subscriptions/# # # #/resourceGroups//# # # #/Providers/Microsoft.EventGrid/topics/someTopics_

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni dettagliate sui trigger, vedere [Esecuzione e trigger di pipeline](concepts-pipeline-execution-triggers.md#trigger-execution).
* Informazioni su come fare riferimento ai metadati dei trigger nella pipeline, vedere [metadati del trigger di riferimento nelle esecuzioni di pipeline](how-to-use-trigger-parameterization.md)
