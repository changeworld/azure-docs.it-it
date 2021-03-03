---
title: Risolvere i problemi di orchestrazione e trigger della pipeline in Azure Data Factory
description: Utilizzare metodi diversi per risolvere i problemi relativi ai trigger di pipeline in Azure Data Factory.
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 2950c175acfdda33394c93649a3e2c41d1264dd2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705994"
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

**Motivo**: è stato raggiunto il limite di capacità del runtime di integrazione. È possibile che sia in esecuzione una grande quantità di flusso di dati usando lo stesso runtime di integrazione nello stesso momento. Per informazioni dettagliate, vedere [sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md#version-2) .

**Risoluzione**:
 
- Eseguire le pipeline in momenti di trigger diversi.
- Creare un nuovo runtime di integrazione e suddividere le pipeline tra più runtime di integrazione.

### <a name="you-have-activity-level-errors-and-failures-in-pipelines"></a>Sono presenti errori ed errori a livello di attività nelle pipeline

Azure Data Factory orchestrazione consente la logica condizionale e consente agli utenti di eseguire percorsi diversi in base al risultato di un'attività precedente. Consente quattro percorsi condizionali: **dopo l'esito positivo** (passaggio predefinito), in caso di **errore**, **al completamento** e in caso di **Skip**. 

Azure Data Factory valuta il risultato di tutte le attività a livello foglia. I risultati della pipeline sono riusciti solo se tutte le foglie hanno esito positivo. Se un'attività foglia è stata ignorata, viene invece valutata l'attività padre. 

**Risoluzione**

1. Implementare i controlli a livello di attività attenendosi [alla gestione degli errori e degli errori della pipeline](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
1. Usare app per la logica di Azure per monitorare le pipeline a intervalli regolari dopo le [query in base alla Factory](/rest/api/datafactory/pipelineruns/querybyfactory).

### <a name="how-to-monitor-pipeline-failures-in-regular-intervals"></a>Come monitorare gli errori della pipeline a intervalli regolari

Potrebbe essere necessario monitorare le pipeline di Data Factory non riuscite, ad intervalli di 5 minuti. È possibile eseguire una query e filtrare le esecuzioni della pipeline da un data factory usando l'endpoint. 

**Risoluzione** dei problemi È possibile configurare un'app per la logica di Azure per eseguire una query su tutte le pipeline non riuscite ogni 5 minuti, come descritto in [query by Factory](/rest/api/datafactory/pipelineruns/querybyfactory). Quindi, è possibile segnalare eventi imprevisti al sistema di ticket.

Per ulteriori informazioni, vedere [inviare notifiche da Data Factory, parte 2](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/).

### <a name="degree-of-parallelism--increase-does-not-result-in-higher-throughput"></a>L'aumento del grado di parallelismo non comporta una velocità effettiva più elevata

**Causa** 

Il grado di parallelismo in *foreach* è in realtà max degree of parallelism. Non è possibile garantire un numero specifico di esecuzioni che si verificano contemporaneamente, ma questo parametro garantisce che non si vada mai oltre il valore impostato. Questo è un limite, da sfruttare quando si controlla l'accesso simultaneo alle origini e ai sink.

Fact noti su *foreach*
 * Foreach dispone di una proprietà denominata batch Count (n) in cui il valore predefinito è 20 e il valore max è 50.
 * Il numero di batch, n, viene usato per costruire n code. In seguito verranno illustrati alcuni dettagli sul modo in cui vengono costruite queste code.
 * Ogni coda viene eseguita in sequenza, ma è possibile disporre di più code eseguite in parallelo.
 * Le code vengono create in precedenza. Ciò significa che non è previsto il ribilanciamento delle code durante il Runtime.
 * In qualsiasi momento, è possibile elaborare al massimo un elemento per ogni coda. Ciò significa che al massimo n elementi elaborati in un determinato momento.
 * Il tempo di elaborazione totale foreach equivale al tempo di elaborazione della coda più estesa. Ciò significa che l'attività ForEach dipende dal modo in cui vengono costruite le code.
 
**Risoluzione**

 * Non è consigliabile usare l'attività *sevariabile* all'interno *di per ogni* esecuzione in parallelo.
 * Tenendo in considerazione il modo in cui vengono costruite le code, il cliente può migliorare le prestazioni di foreach impostando più *foreach* , in cui ogni foreach avrà elementi con tempi di elaborazione simili. In questo modo, le esecuzioni lunghe verranno elaborate in parallelo piuttosto in sequenza.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi, usare le risorse seguenti:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità di Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video di Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Pagina delle domande di Domande e risposte Microsoft](/answers/topics/azure-data-factory.html)
*  [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)
