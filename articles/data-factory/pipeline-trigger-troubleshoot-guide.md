---
title: Risolvere i problemi di orchestrazione e trigger della pipeline in Azure Data Factory
description: Utilizzare metodi diversi per risolvere i problemi relativi ai trigger di pipeline in Azure Data Factory.
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 0e67a316b012eda61607c84edfd8e10d6aa3318d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589169"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Risolvere i problemi di orchestrazione e trigger della pipeline in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Una esecuzione di pipeline in Azure Data Factory definisce un'istanza dell'esecuzione di una pipeline. Si dispone, ad esempio, di una pipeline che viene eseguita alle ore 8:00, 9:00 e 10:00. In questo caso, sono presenti tre esecuzioni separate della pipeline o delle esecuzioni della pipeline. ognuna con un ID di esecuzione pipeline univoco. Un ID di esecuzione è un GUID (Globally Unique Identifier) che definisce l'esecuzione della pipeline specifica.

Le istanze delle esecuzioni di pipeline vengono in genere create passando argomenti ai parametri definiti nelle pipeline. È possibile eseguire una pipeline manualmente o tramite un trigger. Per informazioni dettagliate, vedere [esecuzione e trigger della pipeline in Azure Data Factory](concepts-pipeline-execution-triggers.md) .

## <a name="common-issues-causes-and-solutions"></a>Problemi comuni, cause e soluzioni

### <a name="pipeline-with-azure-function-throws-error-with-private-end-point-connectivity"></a>La pipeline con la funzione di Azure genera un errore con connettività di endpoint privata
 
#### <a name="issue"></a>Problema
Per un certo contesto, sono presenti Data Factory e app per le funzioni di Azure in esecuzione in un endpoint privato. Si sta tentando di ottenere una pipeline che interagisce con il app per le funzioni di Azure per funzionare. Sono stati provati tre metodi diversi, ma uno restituisce un errore `Bad Request` , gli altri due metodi restituiscono `103 Error Forbidden` .

#### <a name="cause"></a>Causa 
Data Factory attualmente non supporta un connettore di endpoint privato per app per le funzioni di Azure. Questo dovrebbe essere il motivo per cui Azure app per le funzioni rifiuta le chiamate perché verrebbe configurato per consentire solo le connessioni da un collegamento privato.

#### <a name="resolution"></a>Soluzione
È possibile creare un endpoint privato di tipo **PrivateLinkService** e fornire il DNS dell'app per le funzioni e la connessione dovrebbe funzionare.

### <a name="pipeline-run-is-killed-but-the-monitor-still-shows-progress-status"></a>L'esecuzione della pipeline viene terminata, ma il monitoraggio Mostra ancora lo stato di avanzamento

#### <a name="issue"></a>Problema
Spesso quando si termina l'esecuzione di una pipeline, il monitoraggio della pipeline Mostra ancora lo stato di avanzamento. Ciò si verifica a causa del problema della cache nel browser e non si dispone dei filtri corretti per il monitoraggio.

#### <a name="resolution"></a>Soluzione
Aggiornare il browser e applicare i filtri corretti per il monitoraggio.
 
### <a name="copy-pipeline-failure--found-more-columns-than-expected-column-count-delimitedtextmorecolumnsthandefined"></a>Errore della pipeline di copia: sono state trovate più colonne del numero di colonne previsto (DelimitedTextMoreColumnsThanDefined)

#### <a name="issue"></a>Problema  
Se i file in una particolare cartella che si sta copiando contengono file con schemi diversi, ad esempio un numero variabile di colonne, delimitatori diversi, impostazioni carattere virgolette o alcuni problemi di dati, la pipeline Data Factory verrà eseguita in questo errore:

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

#### <a name="resolution"></a>Soluzione
Selezionare l'opzione "copia binaria" durante la creazione dell'attività Copia dati. In questo modo, per la copia bulk o la migrazione dei dati da un Data Lake a un altro, con l'opzione **binary** data factory non aprirà i file per la lettura dello schema, ma considererà tutti i file come binari e li copierà nell'altra posizione.

### <a name="pipeline-run-fails-when-capacity-limit-of-integration-runtime-is-reached"></a>L'esecuzione della pipeline non riesce quando viene raggiunto il limite di capacità del runtime di integrazione

#### <a name="issue"></a>Problema
Messaggio di errore:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

L'errore indica la limitazione di per Runtime di integrazione, che attualmente è 50. Per informazioni dettagliate, vedere [limiti](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#version-2) .

Se si esegue una grande quantità di dati con lo stesso runtime di integrazione nello stesso momento, è possibile che si verifichi questo tipo di errore.

#### <a name="resolution"></a>Soluzione 
- Separare queste pipeline per un tempo di attivazione diverso per l'esecuzione.
- Creare un nuovo runtime di integrazione e suddividere queste pipeline tra più runtime di integrazione.

### <a name="how-to-monitor-pipeline-failures-on-regular-interval"></a>Come monitorare gli errori della pipeline a intervalli regolari

#### <a name="issue"></a>Problema
Spesso è necessario monitorare Data Factory pipeline a intervalli, ad indicare 5 minuti. È possibile eseguire query e filtrare le esecuzioni della pipeline da un data factory usando l'endpoint. 

#### <a name="recommendation"></a>Recommendation
1. Configurare un'app per la logica di Azure per eseguire una query su tutte le pipeline non riuscite ogni 5 minuti.
2. Quindi, è possibile segnalare eventi imprevisti al sistema di ticket in base a ogni [QueryByFactory](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory).

#### <a name="reference"></a>Informazioni di riferimento
- [External-Invia notifiche da Data Factory](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/)

### <a name="how-to-handle-activity-level-errors-and-failures-in-pipelines"></a>Come gestire errori ed errori a livello di attività nelle pipeline

#### <a name="issue"></a>Problema
Azure Data Factory orchestrazione consente la logica condizionale e consente all'utente di eseguire percorsi diversi in base ai risultati di un'attività precedente. Consente quattro percorsi condizionali: "in caso di esito positivo (passaggio predefinito)", "in caso di esito negativo", "al termine" e "su Ignora". È consentito l'utilizzo di percorsi diversi.

Azure Data Factory definisce l'esito positivo e negativo dell'esecuzione della pipeline come indicato di seguito:

- Valutare il risultato per tutte le attività a livello foglia. Se un'attività foglia è stata ignorata, viene invece valutata l'attività padre.
- Il risultato della pipeline viene eseguito correttamente se e solo se tutte le foglie hanno esito positivo.

#### <a name="recommendation"></a>Recommendation
- Implementare i controlli a livello [di attività in seguito alla gestione degli errori e degli errori della pipeline](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
- Usare l'app per la logica di Azure per monitorare le pipeline a intervalli regolari in [base alle query di datafactory]( https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi, usare le risorse seguenti:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità di Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video di Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Pagina delle domande di Domande e risposte Microsoft](/answers/topics/azure-data-factory.html)
*  [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)