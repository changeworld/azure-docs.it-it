---
title: Avvio rapido di Azure - Creare un hub eventi tramite il portale di Azure
description: Questa guida di avvio rapido illustra come creare un hub eventi di Azure con il portale di Azure.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: bf83c53792c4934f43e0039d092bb3e36954bf7b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303118"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Avvio rapido: Creare un hub eventi con il portale di Azure
Hub eventi di Azure è una piattaforma di streaming per Big Data e un servizio di inserimento eventi che consente di ricevere ed elaborare milioni di eventi al secondo. Hub eventi consente di elaborare e archiviare eventi, dati o dati di telemetria generati dal software distribuito e dai dispositivi. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Per una panoramica dettagliata di Hub eventi, vedere [Panoramica di Hub eventi](event-hubs-about.md) e [Funzionalità di Hub eventi](event-hubs-features.md).

In questa guida introduttiva viene creato un hub eventi usando il [portale di Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva, assicurarsi di disporre di quanto segue:

- Sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è una raccolta logica per le risorse di Azure. Tutte le risorse vengono distribuite e gestite in un gruppo di risorse. Per creare un gruppo di risorse:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel riquadro di spostamento a sinistra selezionare **Gruppi di risorse**. Quindi selezionare **Aggiungi**.

   ![Gruppi di risorse: pulsante Aggiungi](./media/event-hubs-quickstart-portal/resource-groups1.png)

1. Per **Sottoscrizione** selezionare il nome della sottoscrizione di Azure in cui si vuole creare il gruppo di risorse.
1. Digitare un **nome univoco per il gruppo di risorse**. Il sistema verifica immediatamente se il nome è disponibile nella sottoscrizione di Azure attualmente selezionata.
1. Selezionare un'**area** per il gruppo di risorse.
1. Selezionare **Rivedi e crea**.

   ![Gruppo di risorse: Crea](./media/event-hubs-quickstart-portal/resource-groups2.png)
1. Nel riquadro **Rivedi e crea** selezionare **Crea**. 

## <a name="create-an-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi

Uno spazio dei nomi di Hub eventi fornisce un contenitore con ambito univoco in cui creare uno o più hub eventi. Per creare uno spazio dei nomi nel gruppo di risorse usando il portale, eseguire le azioni seguenti:

1. Nel portale di Azure selezionare **Crea una risorsa** nella parte superiore sinistra della schermata.
1. Selezionare **Tutti i servizi** nel menu a sinistra e scegliere la **stella (`*`)** accanto a **Hub eventi** nella categoria **Analytics**. Verificare che **Hub eventi** venga aggiunto a **PREFERITI** nel menu di spostamento a sinistra. 
    
   ![Cercare Hub eventi](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
1. Selezionare **Hub eventi** sotto **PREFERITI** nel menu di spostamento a sinistra e scegliere **Aggiungi** sulla barra degli strumenti.

   ![Pulsante Aggiungi](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
1. Nella pagina **Crea spazio dei nomi** seguire questa procedura:  
   1. Selezionare la **sottoscrizione** in cui creare lo spazio dei nomi.  
   1. Selezionare il **gruppo di risorse** creato nel passaggio precedente.   
   1. Immettere un **nome** per lo spazio dei nomi. Verrà effettuato immediatamente un controllo sulla disponibilità del nome.  
   1. Selezionare una **località** per lo spazio dei nomi.
   1. Scegliere il **piano tariffario** (Basic o Standard). Per informazioni su alcune differenze tra i livelli Basic e Standard, vedere [Prezzi di Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/), [Differenze tra i livelli](event-hubs-faq.yml#what-is-the-difference-between-event-hubs-basic-and-standard-tiers-) e [Quote e limiti](event-hubs-quotas.md). 
   1. Lasciare invariate le impostazioni di **Unità elaborate**. Le unità elaborate sono unità di capacità pre-acquistate. Per altre informazioni sulle unità elaborate, vedere [Scalabilità di Hub eventi](event-hubs-scalability.md#throughput-units).  
   1. Selezionare **Rivedi e crea** nella parte inferiore della pagina.
      
      ![Creare uno spazio dei nomi dell'hub eventi](./media/event-hubs-quickstart-portal/create-event-hub1.png)
   1. Nella pagina **Rivedi e crea** controllare le impostazioni e selezionare **Crea**. Attendere il completamento della distribuzione. 
      
      ![Pagina Rivedi e crea](./media/event-hubs-quickstart-portal/review-create.png)
      
   1. Nella pagina **Distribuzione** selezionare **Vai alla risorsa** per passare alla pagina relativa allo spazio dei nomi. 
      
      ![Distribuzione completata - Vai alla risorsa](./media/event-hubs-quickstart-portal/deployment-complete.png)  
   1. Verificare che la pagina the **Spazio dei nomi di Hub eventi** sia simile all'esempio seguente:   
      
      ![Home page dello spazio dei nomi](./media/event-hubs-quickstart-portal/namespace-home-page.png)       

      > [!NOTE]
      > Hub eventi di Azure fornisce un endpoint Kafka. che consente allo spazio dei nomi di Hub eventi di comprendere in modo nativo il protocollo del messaggio e le API [Apache Kafka](https://kafka.apache.org/intro). Con questa funzionalità è possibile comunicare con gli Hub eventi come si farebbe con gli argomenti Kafka senza modificare i client del protocollo o eseguire i propri cluster. Hub eventi supporta [Apache Kafka 1.0](https://kafka.apache.org/10/documentation.html) e versioni successive. Per altre informazioni, vedere [Usare Hub eventi di Azure da applicazioni Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).
    
## <a name="create-an-event-hub"></a>Creare un hub eventi

Per creare un hub eventi all'interno dello spazio dei nomi, eseguire le azioni seguenti:

1. Nella pagina dello spazio dei nomi degli hub eventi selezionare **Hub eventi** nel menu a sinistra.
1. Nella parte superiore della finestra selezionare **+ Hub eventi**.
   
    ![Aggiungi hub eventi: pulsante](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Digitare un nome per l'hub eventi e quindi selezionare **Crea**.
   
    ![Creare un hub eventi](./media/event-hubs-quickstart-portal/create-event-hub5.png)

    L'impostazione **Numero di partizioni** consente di parallelizzare il consumo tra molti consumer. Per altre informazioni, vedere [Partizioni](event-hubs-scalability.md#partitions).

    L'impostazione **Conservazione dei messaggi** specifica per quanto tempo il servizio Hub eventi mantiene i dati. Per altre informazioni, vedere [conservazione degli eventi](event-hubs-features.md#event-retention).
1. È possibile controllare lo stato della creazione dell'hub eventi negli avvisi. Dopo aver creato l'hub eventi, è possibile vederlo nell'elenco corrispondente.

    ![Hub eventi creato](./media/event-hubs-quickstart-portal/event-hub-created.png)
    
## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato creato un gruppo di risorse, uno spazio dei nomi Hub eventi e un hub eventi. Per le istruzioni dettagliate su come inviare o ricevere eventi da un hub eventi, vedere queste esercitazioni: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (solo invio)](event-hubs-c-getstarted-send.md)
- [Apache Storm (solo ricezione)](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png
