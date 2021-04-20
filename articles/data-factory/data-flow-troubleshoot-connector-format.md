---
title: Risolvere i problemi relativi al connettore e al formato nei flussi di dati di mapping
description: Informazioni su come risolvere i problemi relativi al flusso di dati correlati al connettore e al formato in Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/20/2021
ms.openlocfilehash: 1991436e2cc890b5c6a339f79e42536ced2fbd36
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739477"
---
# <a name="troubleshoot-connector-and-format-issues-in-mapping-data-flows-in-azure-data-factory"></a>Risolvere i problemi relativi al connettore e al formato nel mapping dei flussi di dati in Azure Data Factory


Questo articolo illustra i metodi di risoluzione dei problemi relativi al connettore e al formato per il mapping dei flussi di dati in Azure Data Factory (ADF).


## <a name="cosmos-db--json"></a>Cosmos DB & JSON

### <a name="support-customized-schemas-in-the-source"></a>Supporto di schemi personalizzati nell'origine

#### <a name="symptoms"></a>Sintomi
Quando si vuole usare il flusso di dati ADF per spostare o trasferire dati da Cosmos DB/JSON in altri archivi dati, è possibile che alcune colonne dei dati di origine non siano disponibili. 

#### <a name="cause"></a>Causa 
Per i connettori senza schema (il numero di colonna, il nome della colonna e il tipo di dati della colonna di ogni riga possono essere diversi quando si confrontano con altri), per impostazione predefinita, ADF usa righe di esempio (ad esempio, primi 100 o 1000 dati di righe) per dedurre lo schema e il risultato dedotto verrà usato come schema per leggere i dati. Pertanto, se gli archivi dati hanno colonne aggiuntive che non vengono visualizzate nelle righe di esempio, i dati di queste colonne aggiuntive non vengono letti, spostati o trasferiti in archivi dati sink.

#### <a name="recommendation"></a>Raccomandazione
Per sovrascrivere il comportamento predefinito e visualizzare campi aggiuntivi, ADF offre opzioni per personalizzare lo schema di origine. È possibile specificare colonne aggiuntive o mancanti che potrebbero mancare nel risultato dello schema-infer nella proiezione dell'origine del flusso di dati per leggere i dati ed è possibile applicare una delle opzioni seguenti per impostare lo schema personalizzato. In genere, **è preferibile l'opzione 1.**

- **Opzione-1:** rispetto ai dati di origine originali che possono essere un file, una tabella o un contenitore di grandi dimensioni che contiene milioni di righe con schemi complessi, è possibile creare una tabella o un contenitore temporaneo con poche righe contenenti tutte le colonne da leggere e quindi passare all'operazione seguente: 

    1. Usare le impostazioni di debug dell'origine flusso **di** dati per fare in modo che **import projection** with sample files/tables (Importa proiezione con file/tabelle di esempio) per ottenere lo schema completo. È possibile seguire i passaggi nell'immagine seguente:<br/>

        ![Screenshot che mostra la prima parte della prima opzione per personalizzare lo schema di origine.](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-1.png)<br/>
         1. Selezionare **Impostazioni di debug nell'area** di disegno del flusso di dati.
         1. Nel riquadro popup selezionare **Tabella** di esempio nella scheda **cosmosSource** e immettere il nome della tabella nel **blocco** Tabella.
         1. Per salvare le impostazioni, fare clic su **Save** (Salva).
         1. Selezionare **Importa proiezione.**<br/>  
    
    1. Modificare le impostazioni **di debug per** usare il set di dati di origine per lo spostamento o la trasformazione dei dati rimanenti. È possibile passare alla procedura illustrata nell'immagine seguente:<br/>

        ![Screenshot che mostra la seconda parte della prima opzione per personalizzare lo schema di origine.](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-2.png) <br/>   
         1. Selezionare **Impostazioni di debug** nell'area di disegno del flusso di dati.
         1. Nel riquadro popup selezionare Source **dataset (Set di dati di** origine) nella scheda **cosmosSource.**
         1. Per salvare le impostazioni, fare clic su **Save** (Salva).<br/>
    
    Successivamente, il runtime del flusso di dati di AdF rispetta e usa lo schema personalizzato per leggere i dati dall'archivio dati originale. <br/>

- **Opzione 2:** se si ha familiarità con lo schema e il linguaggio DSL dei dati di origine, è possibile aggiornare manualmente lo script di origine del flusso di dati per aggiungere altre colonne o non aver letto i dati. Un esempio è illustrato nell'immagine seguente: 

    ![Screenshot che mostra la seconda opzione per personalizzare lo schema di origine.](./media/data-flow-troubleshoot-connector-format/customize-schema-option-2.png)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla risoluzione dei problemi, vedere le risorse seguenti:

*  [Risolvere i problemi relativi ai flussi di dati di mapping in Azure Data Factory](data-flow-troubleshoot-guide.md)
*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità di Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video di Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum Stack Overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)