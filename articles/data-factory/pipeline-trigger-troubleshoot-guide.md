---
title: Risolvere i problemi di orchestrazione e trigger della pipeline in Azure Data Factory
description: Utilizzare metodi diversi per risolvere i problemi relativi ai trigger di pipeline in Azure Data Factory.
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 0ceee3c65e8c4df5d843bb441fb6426a0f4eb696
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98220253"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Risolvere i problemi di orchestrazione e trigger della pipeline in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Una esecuzione di pipeline in Azure Data Factory definisce un'istanza dell'esecuzione di una pipeline. Si immagini, ad esempio, di avere una pipeline che viene eseguita alle ore 8:00, 9:00 e 10:00. In questo caso, sono presenti tre esecuzioni di pipeline separate. ognuna con un ID di esecuzione pipeline univoco. Un ID di esecuzione è un identificatore univoco globale (GUID) che definisce l'esecuzione della pipeline specifica.

Le istanze delle esecuzioni di pipeline vengono in genere create passando argomenti ai parametri definiti nelle pipeline. È possibile eseguire una pipeline manualmente o tramite un trigger. Per informazioni dettagliate, vedere [esecuzione e trigger della pipeline in Azure Data Factory](concepts-pipeline-execution-triggers.md) .

## <a name="common-issues-causes-and-solutions"></a>Problemi comuni, cause e soluzioni

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>Una pipeline dell'app funzioni di Azure genera un errore con la connettività degli endpoint privati
 
Sono presenti Data Factory e un'app per le funzioni di Azure in esecuzione in un endpoint privato. Si sta provando a eseguire una pipeline che interagisce con l'app per le funzioni. Sono stati tentati tre metodi diversi, ma uno restituisce l'errore "richiesta non valida" e gli altri due metodi restituiscono "errore 103 non consentito".

**Motivo**: data factory attualmente non supporta un connettore di endpoint privato per le app per le funzioni. Funzioni di Azure rifiuta le chiamate perché è configurato per consentire solo le connessioni da un collegamento privato.

**Soluzione**: creare un endpoint **PrivateLinkService** e fornire il DNS dell'app per le funzioni.

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>Un'esecuzione della pipeline viene annullata, ma il monitoraggio Mostra ancora lo stato di avanzamento

Quando si annulla l'esecuzione di una pipeline, il monitoraggio della pipeline Visualizza spesso lo stato di avanzamento. Ciò si verifica a causa di un problema relativo alla cache del browser. È anche possibile che non si disponga dei filtri di monitoraggio corretti.

**Soluzione**: aggiornare il browser e applicare i filtri di monitoraggio corretti.
 
### <a name="you-see-a-delimitedtextmorecolumnsthandefined-error-when-copying-a-pipeline"></a>Viene visualizzato un errore "DelimitedTextMoreColumnsThanDefined" durante la copia di una pipeline
 
Se una cartella che si sta copiando contiene file con schemi diversi, ad esempio un numero variabile di colonne, delimitatori diversi, impostazioni carattere virgolette o alcuni problemi di dati, la pipeline Data Factory potrebbe generare questo errore:

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

**Soluzione**: selezionare l'opzione **copia binaria** durante la creazione dell'attività di copia. In questo modo, per le copie bulk o la migrazione dei dati da un data Lake a un altro, Data Factory non aprirà i file per leggere lo schema. Al contrario, Data Factory considererà ogni file come binario e lo copierà nell'altra posizione.

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime"></a>L'esecuzione di una pipeline ha esito negativo quando si raggiunge il limite di capacità del runtime di integrazione

Messaggio di errore:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

**Motivo**: è stato raggiunto il limite di capacità del runtime di integrazione. È possibile che sia in esecuzione una grande quantità di flusso di dati usando lo stesso runtime di integrazione nello stesso momento. Per informazioni dettagliate, vedere [sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#version-2) .

**Risoluzione**:
 
- Eseguire le pipeline in momenti di trigger diversi.
- Creare un nuovo runtime di integrazione e suddividere le pipeline tra più runtime di integrazione.

### <a name="you-have-activity-level-errors-and-failures-in-pipelines"></a>Sono presenti errori ed errori a livello di attività nelle pipeline

Azure Data Factory orchestrazione consente la logica condizionale e consente agli utenti di eseguire percorsi diversi in base al risultato di un'attività precedente. Consente quattro percorsi condizionali: **dopo l'esito positivo** (passaggio predefinito), in caso di **errore**, **al completamento** e in caso di **Skip**. 

Azure Data Factory valuta il risultato di tutte le attività a livello foglia. I risultati della pipeline sono riusciti solo se tutte le foglie hanno esito positivo. Se un'attività foglia è stata ignorata, viene invece valutata l'attività padre. 

**Risoluzione**

1. Implementare i controlli a livello di attività attenendosi [alla gestione degli errori e degli errori della pipeline](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
1. Usare app per la logica di Azure per monitorare le pipeline a intervalli regolari dopo le [query in base alla Factory](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory).

## <a name="monitor-pipeline-failures-in-regular-intervals"></a>Monitorare gli errori della pipeline a intervalli regolari

Potrebbe essere necessario monitorare le pipeline di Data Factory non riuscite, ad intervalli di 5 minuti. È possibile eseguire una query e filtrare le esecuzioni della pipeline da un data factory usando l'endpoint. 

Configurare un'app per la logica di Azure per eseguire una query su tutte le pipeline non riuscite ogni 5 minuti, come descritto in [query by Factory](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory). Quindi, è possibile segnalare eventi imprevisti al nostro sistema di ticket.

Per ulteriori informazioni, vedere [inviare notifiche da Data Factory, parte 2](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi, usare le risorse seguenti:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità di Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video di Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Pagina delle domande di Domande e risposte Microsoft](/answers/topics/azure-data-factory.html)
*  [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)
