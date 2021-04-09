---
title: Informazioni sui set di risorse
description: Questo articolo illustra i set di risorse e il modo in cui vengono creati da Azure.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: cbf070dce056795ad8e4a5f3e4d609e7d36d631e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200822"
---
# <a name="understanding-resource-sets"></a>Informazioni sui set di risorse

Questo articolo illustra come Azure asset usa i set di risorse per eseguire il mapping degli asset di dati alle risorse logiche.
## <a name="background-info"></a>Informazioni di base

I sistemi di elaborazione dati su larga scala in genere archiviano una singola tabella su un disco come più file. Questo concetto è rappresentato in Azure ambito usando i set di risorse. Un set di risorse è un singolo oggetto nel catalogo che rappresenta un numero elevato di asset nello spazio di archiviazione.

Si supponga, ad esempio, che il cluster Spark abbia reso permanente un frame di dati in un'origine dati di Azure Data Lake Storage (ADLS) Gen2. Sebbene in Spark la tabella abbia un aspetto simile a una singola risorsa logica, sul disco sono probabilmente presenti migliaia di file parquet, ciascuno dei quali rappresenta una partizione del contenuto del frame di dati totale. I dati e i dati del log Web hanno la stessa sfida. Si supponga di avere un sensore che restituisce i file di log più volte al secondo. Non sarà necessario molto tempo fino a quando non si dispone di centinaia di migliaia di file di log da quel singolo sensore.

Per risolvere il problema del mapping di un numero elevato di asset di dati a una singola risorsa logica, Azure ambito usa i set di risorse.

## <a name="how-azure-purview-detects-resource-sets"></a>Modalità di rilevamento di set di risorse in Azure.

Azure competenza supporta il rilevamento di set di risorse nell'archiviazione BLOB di Azure, ADLS Gen1 e ADLS Gen2.

Azure competenza rileva automaticamente i set di risorse durante l'analisi. Questa funzionalità esamina tutti i dati inseriti tramite l'analisi e li confronta con un set di modelli definiti.

Si supponga, ad esempio, di eseguire l'analisi di un'origine dati il cui URL è `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` . Azure ambito esamina i segmenti di percorso e determina se corrispondono a qualsiasi modello incorporato. Include modelli predefiniti per GUID, numeri, formati di data, codici di localizzazione (ad esempio, en-US) e così via. In questo caso, il modello di numeri corrisponde a *23*. Azure competenza presuppone che questo file faccia parte di un set di risorse denominato `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` .

In alternativa, per un URL come `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` , Azure competenza corrisponde sia al modello di localizzazione sia al modello numerico, producendo un set di risorse denominato `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` .

Con questa strategia, Azure competenza eseguirà il mapping delle risorse seguenti allo stesso set di risorse `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` :

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Tipi di file che Azure non rileva come set di risorse

La competenza non tenta intenzionalmente di classificare la maggior parte dei tipi di file di documento come Word, Excel o PDF come set di risorse. L'eccezione è il formato CSV poiché si tratta di un formato di file partizionato comune.

## <a name="how-azure-purview-scans-resource-sets"></a>Analisi degli insiemi di risorse in Azure.

Quando Azure competenza rileva le risorse che ritiene fanno parte di un set di risorse, passa da un'analisi completa a un'analisi di esempio. In un'analisi di esempio, apre solo un subset dei file che ritiene si trovino nel set di risorse. Per ogni file aperto, utilizza lo schema ed esegue i relativi classificatori. Azure è quindi in grado di trovare la risorsa più recente tra le risorse aperte e usa lo schema e le classificazioni di tale risorsa nella voce per l'intero set di risorse nel catalogo.

## <a name="what-azure-purview-stores-about-resource-sets"></a>Che cosa archivia Azure per informazioni sui set di risorse

Oltre a singoli schemi e classificazioni, Azure ambito archivia le informazioni seguenti sui set di risorse:

- Dati della risorsa di partizione più recente sottoposta a scansione approfondita.
- Aggregare le informazioni sulle risorse della partizione che costituiscono il set di risorse.
- Numero di partizioni che mostra il numero di risorse di partizione trovate.
- Conteggio degli schemi che mostra il numero di schemi univoci trovati nel set di esempio su cui è stata eseguita l'analisi approfondita. Questo valore può essere un numero compreso tra 1 e 5 o per valori maggiori di 5, 5 +.
- Elenco di tipi di partizione quando più di un tipo di partizione è incluso nel set di risorse. Ad esempio, un sensore Internet delle cose può restituire file sia XML che JSON, anche se entrambi sono parte logicamente dello stesso set di risorse.

## <a name="built-in-resource-set-patterns"></a>Modelli predefiniti di set di risorse

Azure competenza supporta i modelli di set di risorse seguenti. Questi modelli possono essere visualizzati come nome in una directory o come parte di un nome file.
### <a name="regex-based-patterns"></a>Modelli basati su espressioni regolari

| Nome modello | Nome visualizzato | Descrizione |
|--------------|--------------|-------------|
| Guid         | GUID       | Identificatore univoco globale come definito nella [specifica RFC 4122](https://tools.ietf.org/html/rfc4122) |
| Number       | N          | Una o più cifre |
| Formati di data/ora | Anno Mese Giorno N     | Sono supportati diversi formati di data/ora, ma tutti sono rappresentati con {Year} [Delimiter] {month} [Delimiter] {Day} o serie di {N} s. |
| 4ByteHex     | ESADECIMALE        | Numero ESADECIMALe a 4 cifre. |
| Localizzazione | LOC        | Un tag di lingua come definito in [BCP 47](https://tools.ietf.org/html/bcp47), i nomi-e _ sono supportati (ad esempio, EN_CA e en-CA) |

### <a name="complex-patterns"></a>Modelli complessi

| Nome modello | Nome visualizzato | Descrizione |
|--------------|--------------|-------------|
| SparkPath    | {SparkPartitions} | Identificatore del file di partizione Spark |
| Instradamento data (aaaa/mm/gg)  | {Year}/{Month}/{Day} | Schema anno/mese/giorno con più cartelle |


## <a name="how-resource-sets-are-displayed-in-the-azure-purview-catalog"></a>Come vengono visualizzati i set di risorse nel catalogo delle competenze di Azure

Quando Azure competenza corrisponde a un gruppo di asset in un set di risorse, tenta di estrarre le informazioni più utili da usare come nome visualizzato nel catalogo. Di seguito sono riportati alcuni esempi della convenzione di denominazione predefinita applicata: 

### <a name="example-1"></a>Esempio 1

Nome completo: `https://myblob.blob.core.windows.net/sample-data/name-of-spark-output/{SparkPartitions}`

Nome visualizzato: "nome dell'output Spark"

### <a name="example-2"></a>Esempio 2

Nome completo: `https://myblob.blob.core.windows.net/my-partitioned-data/{Year}-{Month}-{Day}/{N}-{N}-{N}-{N}/{GUID}`

Nome visualizzato: "dati partizionati"

### <a name="example-3"></a>Esempio 3

Nome completo: `https://myblob.blob.core.windows.net/sample-data/data{N}.csv`

Nome visualizzato: "dati"

## <a name="known-issues-with-resource-sets"></a>Problemi noti relativi ai set di risorse

Sebbene i set di risorse funzionino correttamente nella maggior parte dei casi, è possibile che si verifichino i problemi seguenti, in cui Azure competenza:

- Contrassegna erroneamente un asset come set di risorse
- Inserisce un asset nel set di risorse errato
- Contrassegna erroneamente un asset come non un set di risorse

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare Azure, vedere [Guida introduttiva: creare un account Azure per la competenza](create-catalog-portal.md).
