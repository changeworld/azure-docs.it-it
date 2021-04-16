---
title: Come creare regole del modello di set di risorse
description: Informazioni su come creare una regola del modello di set di risorse per sovrascrivere il modo in cui gli asset vengono raggruppati in set di risorse
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 04/15/2021
ms.openlocfilehash: b9d6ca88d5e9d49d3973193059197a1aa171e3e8
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568690"
---
# <a name="create-resource-set-pattern-rules"></a>Creare regole del modello di set di risorse

I sistemi di elaborazione dati su larga scala archiviano in genere una singola tabella in un disco come più file. Questo concetto è rappresentato in Azure Purview usando i set di risorse. Un set di risorse è un singolo oggetto nel catalogo dati che rappresenta un numero elevato di asset nell'archiviazione. Per altre informazioni, vedere [Informazioni sui set di risorse](concept-resource-sets.md).

Quando si esegue l'analisi di un account di archiviazione, Azure Purview usa un set di modelli definiti per determinare se un gruppo di asset è un set di risorse. In alcuni casi, il raggruppamento dei set di risorse di Azure Purview potrebbe non riflettere in modo accurato il proprio data estate. Le regole dei modelli di set di risorse consentono di personalizzare o sostituire il modo in cui Azure Purview rileva quali asset vengono raggruppati come set di risorse e come vengono visualizzati all'interno del catalogo.

Le regole del modello sono attualmente supportate nei tipi di origine seguenti:
- Azure Data Lake Storage Gen2
- Archiviazione BLOB di Azure
- File di Azure


## <a name="how-to-create-a-resource-set-pattern-rule"></a>Come creare una regola del modello di set di risorse

Seguire questa procedura per creare una nuova regola del modello di set di risorse:

1. Passare al centro di gestione. Selezionare **Regole modello** dal menu sotto l'intestazione Set di risorse. Selezionare **+ Nuovo** per creare un nuovo set di regole.

   :::image type="content" source="media/how-to-resource-set-pattern-rules/create-new-scoped-resource-set-rule.png" alt-text="Creare una nuova regola del modello di set di risorse" border="true":::

1. Immettere l'ambito della regola del modello del set di risorse. Selezionare il tipo di account di archiviazione e il nome dell'account di archiviazione in cui si vuole creare un set di regole. Ogni set di regole viene applicato in relazione a un ambito del percorso della cartella specificato nel **campo Percorso** cartella.

   :::image type="content" source="media/how-to-resource-set-pattern-rules/create-new-scoped-resource-set-scope.png" alt-text="Creare configurazioni delle regole del modello di set di risorse" border="true":::

1. Per immettere una regola per un ambito di configurazione, selezionare **+ Nuova regola.**

1. Immettere nei campi seguenti per creare una regola:

   1. **Nome regola:** Nome della regola di configurazione. Questo campo non ha alcun effetto sugli asset a cui si applica la regola.

   1. **Nome completo:** Percorso completo che usa una combinazione di testo, sostituzioni dinamiche e sostituzioni statiche per associare gli asset alla regola di configurazione. Questo percorso è relativo all'ambito della regola di configurazione. Per istruzioni [dettagliate](#syntax) su come specificare nomi completi, vedere la sezione relativa alla sintassi riportata di seguito.

   1. **Nome visualizzato:** Nome visualizzato dell'asset. Questo campo è facoltativo. Usare testo normale e sostitutori statici per personalizzare la modalità di visualizzazione di un asset nel catalogo. Per istruzioni più dettagliate, vedere la sezione relativa [alla sintassi](#syntax) riportata di seguito.

   1. **Non raggruppare come set di risorse:** Se abilitata, la risorsa corrispondente non verrà raggruppata in un set di risorse.

      :::image type="content" source="media/how-to-resource-set-pattern-rules/scoped-resource-set-rule-example.png" alt-text="Creare una nuova regola di configurazione." border="true":::

1. Salvare la regola facendo clic su **Aggiungi**.

## <a name="pattern-rule-syntax"></a><a name="syntax"></a> Sintassi delle regole dei criteri

Quando si creano regole del modello di set di risorse, usare la sintassi seguente per specificare a quali regole di asset si applicano.

### <a name="dynamic-replacers-single-brackets"></a>Sostituzioni dinamiche (parentesi singole)

Le parentesi singole vengono usate come **sostituzioni dinamiche** in regole di criteri. Specificare un replacer dinamico nel nome completo usando il formato `{<replacerName:<replacerType>}` . In caso di corrispondenza, i replacer dinamici vengono usati come condizione di raggruppamento che indica che gli asset devono essere rappresentati come set di risorse. Se gli asset vengono raggruppati in un set di risorse, il percorso completo del set di risorse conterrà `{replacerName}` dove è stato specificato il replacer.

Ad esempio, se due asset e `folder1/file-1.csv` corrispondono `folder2/file-2.csv` alla regola , il set di risorse sarà una singola entità `{folder:string}/file-{NUM:int}.csv` `{folder}/file-{NUM}.csv` .

#### <a name="special-case-dynamic-replacers-when-not-grouping-into-resource-set"></a>Caso speciale: sostitutori dinamici quando non vengono raggruppati in set di risorse

Se *l'opzione Non raggruppare come set di* risorse è abilitata per una regola del criterio, il nome del replacer è un campo facoltativo. `{:<replacerType>}` è una sintassi valida. Ad esempio, `file-{:int}.csv` corrisponderebbe correttamente per `file-1.csv` e e `file-2.csv` creerebbe due asset diversi invece di un set di risorse.

### <a name="static-replacers-double-brackets"></a>Sostituzioni statiche (doppie parentesi)

Le parentesi doppie vengono usate come **sostituzioni statiche** nel nome completo di una regola del criterio. Specificare un replacer statico nel nome completo usando il formato `{{<replacerName>:<replacerType>}}` . Se viene trovata una corrispondenza, ogni set di valori di sostituzione statici univoci creerà raggruppamenti di set di risorse diversi.

Ad esempio, se due asset e corrispondono alla regola , vengono creati due `folder1/file-1.csv` set di risorse e `folder2/file-2.csv` `{{folder:string}}/file-{NUM:int}.csv` `folder1/file-{NUM}.csv` `folder2/file-{NUM}.csv` .

I replacer statici possono essere usati per specificare il nome visualizzato di un asset corrispondente a una regola dei criteri. `{{<replacerName>}}`L'uso di nel nome visualizzato di una regola userà il valore corrispondente nel nome dell'asset.

### <a name="available-replacement-types"></a>Tipi di sostituzione disponibili

Di seguito sono riportati i tipi disponibili che possono essere usati nei replacer statici e dinamici:

| Tipo | Struttura |
| ---- | --------- |
| string | Serie di 1 o più caratteri Unicode, inclusi i delimitatori come gli spazi. |
| INT | Serie di 1 o più caratteri ASCII da 0 a 9, con prefisso 0 (ad esempio 0001). |
| guid | Serie di rappresentazione di stringa 32 o 8-4-4-4-12 di un UUID come definitodefa in [RFC 4122.](https://tools.ietf.org/html/rfc4122) |
| data | Serie di 6 o 8 caratteri ASCII da 0 a 9 con separatori facoltativi: aaaammgg, aaaa-mm-gg, aammgg, aa-mm-gg, specificati in [RFC 3339.](https://tools.ietf.org/html/rfc3339) |
| time | Serie di 4 o 6 caratteri ASCII da 0 a 9 con separatori facoltativi: HHmm, HH:mm, HHmmss, HH:mm:ss specificati in [RFC 3339.](https://tools.ietf.org/html/rfc3339) |
|  timestamp | Serie di 12 o 14 caratteri ASCII da 0 a 9 con separatori facoltativi: aaaa-mm-ggTHH:mm, aaaammgghhmm, aaaa-mm-ggTHH:mm:ss, aaaammggHHmmss specificato in [RFC 3339](https://tools.ietf.org/html/rfc3339). |
| boolean | Può contenere 'true' o 'false', senza distinzione tra maiuscole e minuscole. |
| d'acquisto | Una serie di 0 o più 0-9 caratteri ASCII, può essere preceduta da 0 (ad esempio 0001) seguita facoltativamente da un punto '.' e da una serie di 1 o più 0-9 caratteri ASCII, può essere 0 suffisso (ad esempio .100) |
| hex | Serie di 1 o più caratteri ASCII del set 0-1 e A-F, il valore può essere preceduto da 0 |
| locale | Stringa che corrisponde alla sintassi specificata in [RFC 5646.](https://tools.ietf.org/html/rfc5646) |

## <a name="order-of-resource-set-pattern-rules-getting-applied"></a>Ordine di applicazione delle regole del modello del set di risorse

Di seguito è riportato l'ordine delle operazioni per l'applicazione delle regole del modello:

1. Gli ambiti più specifici avranno la priorità se un asset corrisponde a due regole. Ad esempio, le regole in un ambito `container/folder` verranno applicate prima delle regole nell'ambito `container` .

1. Ordine delle regole all'interno di un ambito specifico. Questa operazione può essere modificata nell'esperienza utente.

1. Se un asset non corrisponde a una regola specificata, viene applicata l'euristica del set di risorse predefinito.

## <a name="examples"></a>Esempi

### <a name="example-1"></a>Esempio 1

Estrazione di dati SAP in carichi completi e differenziali

#### <a name="inputs"></a>Input

File:

- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_02.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/delta/2020/01/15/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_02.txt`

#### <a name="pattern-rule"></a>Regola del modello

**Ambito:**`https://myazureblob.blob.core.windows.net/bar/`

**Nome visualizzato:** "Cliente esterno"

**Nome completo:**`customer/{extract:string}/{year:int}/{month:int}/{day:int}/saptable_customer_{date_from:date}_{date_to:time}_{sequence:int}.txt`

**Set di risorse:** true

#### <a name="output"></a>Output

Un asset del set di risorse

**Nome visualizzato:** Cliente esterno

**Nome completo:**`https://myazureblob.blob.core.windows.net/bar/customer/{extract}/{year}/{month}/{day}/saptable_customer_{date_from}_{date_to}_{sequence}.txt`

### <a name="example-2"></a>Esempio 2

Dati IoT in formato avro

#### <a name="inputs"></a>Input

File:

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rules"></a>Regole dei criteri

**Ambito:**`https://myazureblob.blob.core.windows.net/bar/`

Regola 1

**Nome visualizzato:** 'machine-89'

**Nome completo:**`raw/machinename-89/{date:date}/{time:time}-{id:int}.avro`

**Set di risorse:** true

Regola 2

**Nome visualizzato:** 'machine-90'

**Nome completo:**`raw/machinename-90/{date:date}/{time:time}-{id:int}.avro`

#### <a name="resource-set-true"></a>*Set di risorse: true*

#### <a name="outputs"></a>Output

2 set di risorse

Set di risorse 1

**Nome visualizzato:** machine-89

**Nome completo:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

Set di risorse 2

**Nome visualizzato:** machine-90

**Nome completo:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-3"></a>Esempio 3

Dati IoT in formato avro

#### <a name="inputs"></a>Input

File:

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.netbar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rule"></a>Regola dei criteri

**Ambito:**`https://myazureblob.blob.core.windows.net/bar/`

**Nome visualizzato:** 'Machine-{{machineid}}'

**Nome completo:**`raw/machinename-{{machineid:int}}/{date:date}/{time:time}-{id:int}.avro`

**Set di risorse:** true

#### <a name="outputs"></a>Output

Set di risorse 1

**Nome visualizzato:** machine-89

**Nome completo:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

Set di risorse 2

**Nome visualizzato:** machine-90

**Nome completo:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-4"></a>Esempio 4

Non raggruppare in set di risorse

#### <a name="inputs"></a>Input

File:

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rule"></a>Regola del modello

**Ambito:**`https://myazureblob.blob.core.windows.net/bar/`

**Nome visualizzato:**`Machine-{{machineid}}`

**Nome completo:**`raw/machinename-{{machineid:int}}/{{:date}}/{{:time}}-{{:int}}.avro`

**Set di risorse:** false

#### <a name="outputs"></a>Output

4 singoli asset

Asset 1

**Nome visualizzato:** machine-89

**Nome completo:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`

Asset 2

**Nome visualizzato:** machine-89

**Nome completo:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`

Asset 3

**Nome visualizzato:** machine-89

**Nome completo:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`

Asset 4

**Nome visualizzato:** machine-90

**Nome completo:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

## <a name="next-steps"></a>Passaggi successivi

Iniziare registrando [ed eseguendo l'analisi di un account di archiviazione di Azure Data Lake Gen2.](register-scan-adls-gen2.md)