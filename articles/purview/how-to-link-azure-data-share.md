---
title: Connettersi alla condivisione dati di Azure
description: Questo articolo descrive come connettere un account di condivisione dati di Azure con la competenza di Azure per la ricerca di asset e la traccia della derivazione dei dati.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: edea881d67d5a677339c6ff357c1ac45f5dfd420
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96555364"
---
# <a name="how-to-connect-azure-data-share-and-azure-purview"></a>Come connettere la condivisione dati di Azure e la competenza di Azure

Questo articolo illustra come connettere l'account di [condivisione dati di Azure](../data-share/overview.md) ad Azure e come gestire i set di dati condivisi (in uscita e in ingresso) nel proprio data. Diversi utenti di governance dei dati possono individuare e tenere traccia della derivazione dei dati tra confini come organizzazioni, reparti e anche data center.

## <a name="common-scenarios"></a>Scenari comuni

La derivazione della condivisione dati ha lo scopo di fornire informazioni dettagliate per l'analisi delle cause radice e l'analisi dell'effetto.

### <a name="scenario-1-360-view-of-datasets-shared-inout-for-a-partner-organization-or-internal-department"></a>Scenario 1:360 visualizzazione dei set di impostazioni condivisi in/out per un'organizzazione partner o un reparto interno

Gli agenti di dati possono visualizzare un elenco di tutti i set di dati condivisi in modo bidirezionale con le organizzazioni partner. Possono cercare e individuare i set di impostazioni in base al nome dell'organizzazione e visualizzare una vista completa di tutte le condivisioni in uscita e in ingresso.

### <a name="scenario-2-root-cause-analysis---upstream-dependency-on-datasets-coming-into-organization-consumer-view-of-incoming-shares"></a>Scenario 2: analisi della causa radice-dipendenza upstream dai set di impostazioni in arrivo nell'organizzazione (visualizzazione del consumer delle condivisioni in ingresso)

Un report contiene informazioni non corrette a causa di problemi di dati upstream da un account di condivisione dati esterno. Gli ingegneri dei dati possono comprendere gli errori upstream, essere informati sui motivi e contattare ulteriormente il proprietario della condivisione per risolvere i problemi che causano la discrepanza dei dati.

### <a name="scenario-3-impact-analysis-on-datasets-going-outside-organization-provider-view-of-outgoing-shares"></a>Scenario 3: analisi di effetto sui set di impostazioni in uscita dall'organizzazione (visualizzazione del provider delle condivisioni in uscita)

I produttori di dati desiderano conoscere gli utenti che saranno interessati a apportare una modifica al set di dati. Grazie alla derivazione, un producer di dati può facilmente comprendere l'effetto dei partner interni o esterni a valle che utilizzano i dati tramite la condivisione di dati di Azure.

## <a name="azure-data-share-and-purview-connected-experience"></a>Condivisione dei dati di Azure e esperienza connessa a livello di competenza

Per connettere la condivisione di dati di Azure e l'account Azure per le competenze, seguire questa procedura:

1. Creare un account di competenza. Tutte le informazioni sulla derivazione della condivisione dati verranno raccolte da un account di competenza. È possibile usare un account esistente o crearne uno nuovo.

1. Connettere la condivisione di dati di Azure all'account di competenza.

    1. Nel portale di competenza è possibile accedere al **centro di gestione** e connettere la condivisione di dati di Azure nella sezione **connessioni esterne** .
    1. Selezionare **+ nuovo** nella barra superiore, trovare la condivisione di dati di Azure nella barra laterale popup e aggiungere l'account di condivisione dati. Eseguire un processo snapshot dopo aver connesso la condivisione dati all'account di competenza, in modo che le risorse di condivisione dei dati e le informazioni sulla derivazione siano visibili in ambito.

       :::image type="content" source="media/how-to-link-azure-data-share/connect-to-data-share.png" alt-text="Centro di gestione per collegare la condivisione di dati di Azure":::

1. Eseguire lo snapshot nella condivisione dati di Azure.

    - Una volta stabilita la connessione di condivisione dati di Azure con Azure, è possibile eseguire uno snapshot per le condivisioni esistenti. 
    - Se non si dispone di alcuna condivisione esistente, passare al portale di condivisione dati di Azure per [condividere i dati](../data-share/share-your-data.md) [e sottoscrivere una condivisione di dati](../data-share/subscribe-to-data-share.md).
    - Una volta completato lo snapshot di condivisione, è possibile visualizzare le risorse di condivisione dei dati associate e la derivazione in ambito.

1. Individuare gli account di condivisione dati e condividere le informazioni nell'account di competenza.

    - Nel home page dell'account di competenza selezionare **Sfoglia per tipo di risorsa** e selezionare il riquadro **condivisione dati di Azure** . È possibile cercare un nome account, un nome condivisione, uno snapshot di condivisione o un'organizzazione partner. In caso contrario, applicare filtri nella pagina dei risultati della ricerca per il nome dell'account, il tipo di condivisione (condivisioni inviate e quelle ricevute)

       :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-search-result-page.png" alt-text="Condivisione dati di Azure nella pagina dei risultati della ricerca":::

    >[!Important]
    >Per la visualizzazione delle risorse di condivisione dei dati in ambito, è necessario eseguire un processo snapshot dopo la connessione della condivisione dati alla competenza.

1. Tenere traccia della derivazione dei set di dati condivisi con la condivisione dati di Azure.

    - Nella pagina dei risultati della ricerca di competenza **scegliere lo snapshot** di condivisione dati (ricevuto/inviato) e selezionare la scheda derivazione per visualizzare un grafico di derivazione con dipendenze upstream e downstream.

    :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-lineage.png" alt-text="Derivazione di set di dati condivisi con la condivisione di dati di Azure":::

## <a name="next-steps"></a>Passaggi successivi

- [Guida dell'utente di derivazione del catalogo](catalog-lineage-user-guide.md)
- [Collegamento a Azure Data Factory per la derivazione](how-to-link-azure-data-factory.md)
