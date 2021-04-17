---
title: Informazioni sui set di risorse
description: Questo articolo illustra quali sono i set di risorse e come vengono creati da Azure Purview.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 330a6e54ee88781f71c4a861051aab94f8eef81f
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587902"
---
# <a name="understanding-resource-sets"></a>Informazioni sui set di risorse

Questo articolo illustra come Azure Purview usa i set di risorse per eseguire il mapping degli asset di dati alle risorse logiche.
## <a name="background-info"></a>Informazioni di base

I sistemi di elaborazione dati su larga scala archiviano in genere una singola tabella in un disco come più file. Questo concetto è rappresentato in Azure Purview usando i set di risorse. Un set di risorse è un singolo oggetto nel catalogo che rappresenta un numero elevato di asset nell'archiviazione.

Si supponga, ad esempio, che il cluster Spark abbia salvato in modo permanente un dataframe in un'origine dati Azure Data Lake Storage (ADLS) Gen2. Sebbene in Spark la tabella sia simile a una singola risorsa logica, è probabile che sul disco siano presenti migliaia di file Parquet, ognuno dei quali rappresenta una partizione del contenuto totale del dataframe. I dati IoT e i dati del log Web hanno la stessa sfida. Si supponga di avere un sensore che restituisce i file di log più volte al secondo. Non ci vuole molto tempo finché non si dispone di centinaia di migliaia di file di log da quel singolo sensore.

Per risolvere la sfida di eseguire il mapping di un numero elevato di asset di dati a una singola risorsa logica, Azure Purview usa i set di risorse.

## <a name="how-azure-purview-detects-resource-sets"></a>Come Azure Purview rileva i set di risorse

Azure Purview supporta il rilevamento dei set di risorse Archiviazione BLOB di Azure, ADLS Gen1 e ADLS Gen2.

Azure Purview rileva automaticamente i set di risorse durante l'analisi. Questa funzionalità esamina tutti i dati inseriti tramite analisi e lo confronta con un set di modelli definiti.

Si supponga, ad esempio, di analizzare un'origine dati il cui URL è `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` . Azure Purview esamina i segmenti di percorso e determina se corrispondono a modelli predefiniti. Include modelli predefiniti per GUID, numeri, formati di data, codici di localizzazione (ad esempio en-us) e così via. In questo caso, il modello di numero corrisponde *a 23*. Azure Purview presuppone che questo file sia parte di un set di risorse denominato `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` .

Oppure, per un URL come , Azure Purview corrisponde sia al modello di localizzazione che al modello di `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` numero, producendo un set di risorse denominato `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` .

Usando questa strategia, Azure Purview esegue il mapping delle risorse seguenti allo stesso set di risorse, `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` :

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Tipi di file che Azure Purview non rileverà come set di risorse

Purview non tenta intenzionalmente di classificare la maggior parte dei tipi di file di documento come Word, Excel o PDF come set di risorse. L'eccezione è il formato CSV perché si tratta di un formato di file partizionato comune.

## <a name="how-azure-purview-scans-resource-sets"></a>Come Azure Purview analizza i set di risorse

Quando Azure Purview rileva le risorse che si pensa siano parte di un set di risorse, passa da un'analisi completa a un'analisi di esempio. In un'analisi di esempio viene aperto solo un subset dei file che si pensa siano nel set di risorse. Per ogni file aperto, usa il relativo schema ed esegue i classificatori. Azure Purview trova quindi la risorsa più recente tra le risorse aperte e usa lo schema e le classificazioni della risorsa nella voce per l'intero set di risorse nel catalogo.

## <a name="what-azure-purview-stores-about-resource-sets"></a>Cosa archivia Azure Purview sui set di risorse

Oltre ai singoli schemi e classificazioni, Azure Purview archivia le informazioni seguenti sui set di risorse:

- Dati della risorsa di partizione più recente di cui è stata eseguita l'analisi approfondita.
- Aggregare informazioni sulle risorse di partizione che costituiscono il set di risorse.
- Conteggio delle partizioni che mostra il numero di risorse di partizione trovate.
- Conteggio degli schemi che mostra il numero di schemi univoci trovati nel set di esempi su cui ha fatto analisi approfondite. Questo valore è un numero compreso tra 1 e 5 o per i valori maggiori di 5, 5+.
- Elenco di tipi di partizione quando nel set di risorse è incluso più di un singolo tipo di partizione. Ad esempio, un sensore IoT potrebbe avere come output sia file XML che JSON, anche se entrambi fanno parte logicamente dello stesso set di risorse.

## <a name="built-in-resource-set-patterns"></a>Modelli di set di risorse predefiniti

Azure Purview supporta i modelli di set di risorse seguenti. Questi modelli possono essere visualizzati come nome in una directory o come parte di un nome di file.
### <a name="regex-based-patterns"></a>Modelli basati su regex

| Nome modello | Nome visualizzato | Descrizione |
|--------------|--------------|-------------|
| Guid         | {GUID}       | Identificatore univoco globale definito in [RFC 4122](https://tools.ietf.org/html/rfc4122) |
| Number       | {N}          | Una o più cifre |
| Formati di data/ora | {Year} {Mese} {Day} {N}     | Sono supportati diversi formati di data/ora, ma tutti sono rappresentati con {Year}[delimiter]{Month}[delimiter]{Day} o una serie di {N}s. |
| 4ByteHex     | {HEX}        | Numero HEX a 4 cifre. |
| Localizzazione | {LOC}        | Un tag di lingua come definito in [BCP 47,](https://tools.ietf.org/html/bcp47)sono supportati sia i nomi - che i nomi _ (ad esempio, en_ca e en-ca) |

### <a name="complex-patterns"></a>Modelli complessi

| Nome modello | Nome visualizzato | Descrizione |
|--------------|--------------|-------------|
| SparkPath    | {SparkPartitions} | Identificatore del file di partizione Spark |
| Date(yyyy/mm/dd)InPath  | {Year}/{Month}/{Day} | Modello anno/mese/giorno che si estende su più cartelle |


## <a name="how-resource-sets-are-displayed-in-the-azure-purview-catalog"></a>Come vengono visualizzati i set di risorse nel catalogo di Azure Purview

Quando Azure Purview corrisponde a un gruppo di asset in un set di risorse, tenta di estrarre le informazioni più utili da usare come nome visualizzato nel catalogo. Alcuni esempi della convenzione di denominazione predefinita applicata: 

### <a name="example-1"></a>Esempio 1

Nome completo: `https://myblob.blob.core.windows.net/sample-data/name-of-spark-output/{SparkPartitions}`

Nome visualizzato: "nome dell'output spark"

### <a name="example-2"></a>Esempio 2

Nome completo: `https://myblob.blob.core.windows.net/my-partitioned-data/{Year}-{Month}-{Day}/{N}-{N}-{N}-{N}/{GUID}`

Nome visualizzato: "my partitioned data"

### <a name="example-3"></a>Esempio 3

Nome completo: `https://myblob.blob.core.windows.net/sample-data/data{N}.csv`

Nome visualizzato: "data"

## <a name="customizing-resource-set-grouping-using-pattern-rules"></a>Personalizzazione del raggruppamento dei set di risorse usando le regole dei criteri

Quando si esegue l'analisi di un account di archiviazione, Azure Purview usa un set di modelli definiti per determinare se un gruppo di asset è un set di risorse. In alcuni casi, il raggruppamento dei set di risorse di Azure Purview potrebbe non riflettere in modo accurato l'ambiente dei dati. Questi problemi possono includere:

- Contrassegno errato di un asset come set di risorse
- Inserimento di un asset nel set di risorse errato
- Contrassegnare in modo errato un asset come non un set di risorse

Per personalizzare o sostituire il modo in cui Azure Purview rileva quali asset vengono raggruppati come set di risorse e come vengono visualizzati all'interno del catalogo, è possibile definire regole di modello nel Centro gestione. Per istruzioni dettagliate e sintassi, vedere Regole [del modello di set di risorse](how-to-resource-set-pattern-rules.md).
## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare Azure Purview, vedere [Avvio rapido: Creare un account Azure Purview.](create-catalog-portal.md)
