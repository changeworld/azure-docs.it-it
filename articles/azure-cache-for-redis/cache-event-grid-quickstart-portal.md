---
title: "Guida introduttiva: Indirizzare cache di Azure per Redis eventi all'endpoint Web con il portale di Azure"
description: Usare Griglia di eventi di Azure per sottoscrivere cache di Azure per Redis eventi, inviare gli eventi a un webhook e gestire gli eventi in un'applicazione Web
author: curib
ms.author: cauribeg
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
ms.custom:
- mode-portal
ms.openlocfilehash: e021f386f255f1cef61e28cbd4fd6116fc2aa727
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529305"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-the-azure-portal"></a>Guida introduttiva: Indirizzare cache di Azure per Redis eventi all'endpoint Web con il portale di Azure

La griglia di eventi di Azure è un servizio di gestione degli eventi per il cloud. In questa guida introduttiva si userà il portale di Azure per creare un'istanza di cache di Azure per Redis, sottoscrivere eventi per tale istanza, attivare un evento e visualizzare i risultati. In genere, si inviano eventi a un endpoint che elabora i dati dell'evento e intraprende azioni. Tuttavia, per semplificare questa guida introduttiva, si invieranno eventi a un'app Web che raccoglierà e visualizza i messaggi. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Al termine, i dati dell'evento sono stati inviati all'app Web.

:::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Griglia di eventi di Azure ridimensionamento del visualizzatore in formato JSON.":::

## <a name="create-an-azure-cache-for-redis-cache-instance"></a>Creare un'istanza cache di Azure per Redis cache 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="create-a-message-endpoint"></a>Creare un endpoint del messaggio

Prima di sottoscrivere gli eventi per l'istanza della cache, creare l'endpoint per il messaggio di evento. L'endpoint richiede in genere azioni basate sui dati degli eventi. Per semplificare questa guida introduttiva, si distribuirà [un'app Web predefinita](https://github.com/Azure-Samples/azure-event-grid-viewer) che visualizza i messaggi di evento. La soluzione distribuita include un piano di servizio app, un'app Web del servizio app e codice sorgente da GitHub.

1. Selezionare **Distribuisci in Azure** in GitHub README per distribuire la soluzione nella sottoscrizione. 

:::image type="content" source="media/cache-event-grid-portal/deploy-to-azure.png" alt-text="Pulsante Distribuisci in Azure.":::

2. Nella pagina **Distribuzione personalizzata** procedere come segue: 
    1. Per **Gruppo di risorse** selezionare il gruppo di risorse creato durante la creazione dell'istanza della cache. Eliminando il gruppo di risorse, sarà più facile eseguire la pulizia dopo aver completato l'esercitazione.  
    2. Per **Nome sito** immettere un nome per l'app Web.
    3. Per **Nome piano di hosting** immettere un nome per il piano del servizio app da usare per l'hosting dell'app Web.
    4. Selezionare la casella di controllo **Accetto le condizioni riportate sopra**. 
    5. Selezionare **Acquisto**. 
    
    | Impostazione      | Valore consigliato  | Descrizione |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Sottoscrizione** | Nell'elenco a discesa selezionare la sottoscrizione. | Sottoscrizione in cui creare questa app Web. | 
    | **Gruppo di risorse** | Nell'elenco a discesa selezionare un gruppo di risorse oppure scegliere **Crea nuovo** e immettere il nome di un nuovo gruppo di risorse. | L'inserimento di tutte le risorse di un'app in un unico gruppo di risorse ne semplifica la gestione o l'eliminazione. | 
    | **Nome del sito** | Immettere un nome per l'app Web. | Questo valore non può essere omesso. | 
    | **Nome del piano di hosting** | Immettere un nome per il piano di servizio app da usare per ospitare l'app Web. | Questo valore non può essere omesso. | 

1. Selezionare Avvisi (icona della campana) nel portale e quindi **Vai al gruppo di risorse**. 

    :::image type="content" source="media/cache-event-grid-portal/deployment-notification.png" alt-text="portale di Azure di distribuzione.":::

4. Nell'elenco di risorse della pagina **Gruppo di risorse** selezionare l'app Web creata. In questo elenco verranno visualizzati anche il piano di servizio app e l'istanza della cache. 

5. Nella pagina **Servizio app** per l'app Web selezionare l'URL per passare al sito Web. Il file deve essere nel formato `https://<your-site-name>.azurewebsites.net`.

6. Verificare che venga visualizzato il sito ma che al suo interno non sono stati ancora pubblicati eventi.

    :::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Sito vuoto del Visualizzatore griglia di eventi.":::

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-azure-cache-for-redis-instance"></a>Sottoscrivere l'istanza cache di Azure per Redis

In questo passaggio si sottoscriverà un argomento per indicare a Griglia di eventi quali eventi tenere traccia e dove inviare gli eventi.

1. Nel portale passare all'istanza della cache creata in precedenza. 
2. Nella pagina **cache di Azure per Redis** selezionare **Eventi** nel menu a sinistra. 
3. Selezionare **Web Hook.** Vengono inviati eventi all'app di visualizzazione tramite un webhook per l'endpoint. 

     :::image type="content" source="media/cache-event-grid-portal/event-grid-web-hook.png" alt-text="portale di Azure eventi.":::

4. Nella pagina **Crea sottoscrizione eventi** immettere quanto segue: 

    | Impostazione      | Valore consigliato  | Descrizione |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Specificare un nome per la sottoscrizione di eventi. | Il valore deve avere una lunghezza compresa tra 3 e 64 caratteri. Può contenere solo lettere, numeri e trattini. | 
    | **Tipi di evento** | Nell'elenco a discesa selezionare i tipi di evento di cui si vuole eseguire il push nella destinazione. Per questa guida introduttiva verrà ridimensionata l'istanza della cache. | L'applicazione di patch, il ridimensionamento, l'importazione e l'esportazione sono le opzioni disponibili. | 
    | **Tipo di endpoint** | Selezionare **Web Hook.** | Gestore eventi per ricevere gli eventi. | 
    | **Endpoint** | Fare **clic su Seleziona un endpoint** e immettere l'URL dell'app Web e aggiungere all'URL home page `api/updates` (ad esempio, ) e quindi selezionare Conferma `https://cache.azurewebsites.net/api/updates` **selezione**. | Si tratta dell'URL dell'app Web creata in precedenza. | 

5. A questo punto, nella pagina **Crea sottoscrizione di eventi** selezionare **Crea** per creare la sottoscrizione di eventi. 

6. Visualizzare nuovamente l'app Web e notare che all'app è stato inviato un evento di convalida della sottoscrizione. Selezionare l'icona a forma di occhio per espandere i dati dell'evento. Griglia di eventi invia l'evento di convalida in modo che l'endpoint possa verificare che voglia ricevere i dati dell'evento. L'app Web include il codice per convalidare la sottoscrizione.

    :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Griglia di eventi di Azure Visualizzatore.":::

## <a name="send-an-event-to-your-endpoint"></a>Inviare un evento all'endpoint

A questo punto, attivare un evento per vedere come la griglia di eventi distribuisce il messaggio nell'endpoint. Verrà ridimensionata l'istanza cache di Azure per Redis.

1. Nell'portale di Azure passare all'istanza cache di Azure per Redis e selezionare **Ridimensiona** nel menu a sinistra.

1. Selezionare il piano tariffario desiderato nella pagina **Scalabilità** e fare clic su **Seleziona.** 

    Il passaggio a un piano tariffario diverso è soggetto alle restrizioni seguenti:
    
    * Non è possibile passare da un piano tariffario superiore a uno inferiore.
      * Non è possibile passare da una cache **Premium** a una cache **Standard** o **Basic**.
      * Non è possibile passare da una cache **Standard** a una cache **Basic**.
    * È possibile passare da una cache **Basic** a una cache **Standard**, ma non è possibile modificare contemporaneamente la dimensione. Se occorre una dimensione diversa, è possibile eseguire successivamente un'operazione di ridimensionamento in base ai propri requisiti.
    * Non è possibile passare direttamente da una cache **Basic** a una cache **Premium**. È innanzitutto necessario passare da **Basic** a **Standard** con una prima operazione di ridimensionamento e quindi da **Standard** a **Premium** con una successiva operazione.
    * Non è possibile passare da una dimensione maggiore alla dimensione **C0 (250 MB)** .
 
    Mentre è in corso il ridimensionamento della cache in base al nuovo piano tariffario, nel pannello **Cache Redis di Azure** viene visualizzato lo stato **Ridimensionamento**. Al termine dell'operazione, lo stato passa da **Ridimensionamento** a **In esecuzione**.

1. È stato attivato l'evento e Griglia di eventi ha inviato il messaggio all'endpoint configurato al momento della sottoscrizione. Il messaggio è in formato JSON e contiene una matrice con uno o più eventi. Nell'esempio seguente il messaggio JSON contiene una matrice con un solo evento. Visualizzare l'app Web e notare che è stato ricevuto un evento **ScalingCompleted.** 

    :::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Griglia di eventi di Azure del visualizzatore di applicazioni in formato JSON.":::

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare a lavorare con questo evento, non pulire le risorse create in questa guida introduttiva. In caso contrario, eliminare le risorse create in questa guida introduttiva.

Selezionare il gruppo di risorse e quindi fare clic su **Elimina gruppo di risorse**.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare argomenti personalizzati e sottoscrizioni di eventi, è possibile approfondire le operazioni possibili con Griglia di eventi:

- [Reazione agli cache di Azure per Redis eventi](cache-event-grid.md)
- [Informazioni sulla griglia di eventi](../event-grid/overview.md)
