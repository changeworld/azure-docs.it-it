---
title: Che cos'è una ontologia?
titleSuffix: Azure Digital Twins
description: Informazioni sulle Ontologie del settore DTDL per la modellazione in un determinato dominio
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: b53cac6e732fb568ba799534d9ad9168fd65eab1
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561530"
---
# <a name="what-is-an-ontology"></a>Che cos'è una ontologia? 

Il vocabolario di una soluzione di dispositivi gemelli digitali di Azure viene definito usando i [modelli](concepts-models.md), che descrivono i tipi di entità presenti nell'ambiente.

In alcuni casi, quando la soluzione è legata a un particolare settore, può risultare più semplice e più efficace iniziare con un set di modelli per quel settore già esistente, anziché creare un modello personalizzato da zero. Questi set di modelli preesistenti sono detti **ontologie**. 

In generale, una ontologia è un set di modelli per un determinato dominio, ad esempio una struttura di compilazione, un sistema per le cose, una città intelligente, una griglia energetica, contenuto Web e così via. Le ontologie vengono spesso usate come schemi per i grafici delle informazioni, in quanto possono abilitare:
* Armonizzazione di componenti software, documentazione, librerie di query e così via.
* Investimento ridotto nella modellazione concettuale e nello sviluppo di sistemi
* Interoperabilità dei dati più semplice a livello semantico
* Il riutilizzo delle procedure consigliate, anziché iniziare da zero o "reinventare la ruota"

Questo articolo spiega perché e come usare le ontologie per i modelli di dispositivi gemelli digitali di Azure, nonché le ontologie e gli strumenti disponibili oggi.

## <a name="using-ontologies-for-azure-digital-twins"></a>Uso di ontologie per i dispositivi gemelli digitali di Azure

Le ontologie forniscono un ottimo punto di partenza per le soluzioni gemelle digitali. Includono un set di modelli e relazioni specifici del dominio tra le entità per la progettazione, la creazione e l'analisi di un grafo gemello digitale. Le ontologie consentono agli sviluppatori di soluzioni di iniziare una soluzione di dispositivi gemelli digitali da un punto di partenza collaudato e di concentrarsi sulla risoluzione dei problemi aziendali. Le ontologie fornite da Microsoft sono progettate anche per essere facilmente estendibili, in modo da poterle personalizzare per la soluzione. 

Inoltre, l'uso di queste ontologie nelle soluzioni può configurarle per un'integrazione più trasparente tra partner e fornitori diversi, perché le ontologie possono fornire un vocabolario comune tra le soluzioni.

Poiché i modelli nei dispositivi gemelli digitali di Azure sono rappresentati in [DTDL (Digital Gemini Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md), le ontologie da usare con i dispositivi gemelli digitali di Azure sono scritte anche in DTDL. 

## <a name="strategies-for-integrating-ontologies"></a>Strategie per l'integrazione di ontologie

Esistono tre possibili strategie per l'integrazione di ontologie standard del settore con DTDL. È possibile scegliere quello più adatto a seconda delle esigenze:

| Strategia | Descrizione | Risorse |
| --- | --- | --- |
| **Adozione** | Puoi avviare la tua soluzione con una ontologia DTDL open source basata su standard di settore ampiamente adottati. È possibile usare questi set di modelli predefiniti oppure estenderli con le proprie aggiunte per una soluzione personalizzata. | [*Concetti: &nbsp; adozione di &nbsp; &nbsp; ontologie standard del settore*](concepts-ontologies-adopt.md)<br><br>[*Concetti: &nbsp; estensione delle &nbsp; ontologie*](concepts-ontologies-extend.md) |
| **Converti** | Se i modelli esistenti sono già stati rappresentati in un altro formato standard, è possibile convertirli in DTDL per usarli con i dispositivi gemelli digitali di Azure. | [*Concetti: &nbsp; conversione di &nbsp; ontologie*](concepts-ontologies-convert.md)<br><br>[*Concetti: &nbsp; estensione delle &nbsp; ontologie*](concepts-ontologies-extend.md) |
| **Autore** | È sempre possibile sviluppare modelli DTDL personalizzati da zero, usando gli standard di settore applicabili come ispirazione. | [*Concetti: modelli DTDL*](concepts-models.md) |

### <a name="using-ontology-strategies-in-a-model-development-path"></a>Uso delle strategie di ontologia in un percorso di sviluppo del modello

Indipendentemente dalla strategia scelta per l'integrazione di un'ontologia nei dispositivi gemelli digitali di Azure, è possibile seguire il percorso completo riportato di seguito per la creazione e il caricamento di ontologia come modelli DTDL.

1. Per iniziare, vedere la pagina relativa [alla modellazione DTDL nei dispositivi gemelli digitali di Azure](concepts-models.md).
1. Procedere con la strategia di integrazione di ontologia scelta in precedenza: [**adottare**](concepts-ontologies-adopt.md), [**convertire**](concepts-ontologies-convert.md)o [**creare**](concepts-models.md) i modelli in base alla propria ontologia.
    1. Se necessario, [estendere](concepts-ontologies-extend.md) la propria ontologia per personalizzarla in base alle proprie esigenze.
1. [Convalidare](how-to-parse-models.md) i modelli per verificare che funzionino con documenti DTDL.
1. È possibile caricare i modelli finiti nei dispositivi gemelli digitali di Azure usando le [API](how-to-manage-model.md#upload-models) o un esempio come l' [Uploader del modello di dispositivi digitali gemelli di Azure](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader).

In seguito, dovrebbe essere possibile usare i modelli nell'istanza di Azure Digital gemelli. 

È possibile visualizzarli con esempi quali [Esplora risorse di Azure Digital gemelli (ADT)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) o il [Visualizzatore del modello ADT](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer)oppure passare a usarli per creare i dispositivi [gemelli digitali](concepts-twins-graph.md).

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sulle strategie di adozione, conversione e creazione di ontologie:
* [*Concetti: adozione di ontologie standard del settore*](concepts-ontologies-adopt.md)
* [*Concetti: conversione di ontologie*](concepts-ontologies-convert.md)
* [*Procedura: gestire i modelli DTDL*](how-to-manage-model.md)

In alternativa, informazioni su come vengono usati i modelli per creare i dispositivi gemelli digitali: [*concetti: i gemelli digitali e il grafo gemello*](concepts-twins-graph.md).