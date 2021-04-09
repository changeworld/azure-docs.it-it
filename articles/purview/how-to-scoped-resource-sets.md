---
title: Come creare una configurazione di set di risorse con ambito
description: Informazioni su come creare una regola di configurazione di un set di risorse con ambito per sovrascrivere il modo in cui gli asset vengono raggruppati in set di risorse
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/17/2021
ms.openlocfilehash: 10e925a84dbe187ccdf5e444cb8b3dd4b7bb4676
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102608003"
---
# <a name="create-scoped-resource-set-configuration-rules"></a>Creare regole di configurazione di set di risorse con ambito

I sistemi di elaborazione dati su larga scala in genere archiviano una singola tabella su un disco come più file. Questo concetto è rappresentato in Azure ambito usando i set di risorse. Un set di risorse è un singolo oggetto nel Catalogo dati che rappresenta un numero elevato di asset nello spazio di archiviazione. Per altre informazioni, vedere [informazioni sui set di risorse](concept-resource-sets.md).

Quando si esegue l'analisi di un account di archiviazione, Azure per la competenza usa un set di modelli definiti per determinare se un gruppo di asset è un set di risorse. In alcuni casi, il raggruppamento di set di risorse di Azure è in grado di riflettere accuratamente il proprio patrimonio di dati. Le regole del set di risorse con ambito consentono di personalizzare o eseguire l'override del modo in cui Azure competenza rileva quali asset sono raggruppati come set di risorse e come vengono visualizzati nel catalogo.

## <a name="how-to-create-a-scoped-resource-set-configuration"></a>Come creare una configurazione di set di risorse con ambito

Attenersi alla procedura seguente per creare una nuova configurazione del set di risorse con ambito:

1. Passare al centro di gestione. Selezionare **set di risorse con ambito** dal menu. Selezionare **+ nuovo** per creare un nuovo set di regole di configurazione.

   :::image type="content" source="media/how-to-scoped-resource-sets/create-new-scoped-resource-set-rule.png" alt-text="Crea nuova regola set di risorse con ambito" border="true":::

1. Immettere l'ambito della configurazione del set di risorse con ambito. Selezionare il tipo di account di archiviazione e il nome dell'account di archiviazione in cui si vuole creare un set di regole. Ogni set di regole viene applicato in relazione a un ambito del percorso della cartella specificato nel campo **percorso cartella** .

   :::image type="content" source="media/how-to-scoped-resource-sets/create-new-scoped-resource-set-scope.png" alt-text="Creare configurazioni di set di risorse con ambito" border="true":::

1. Per immettere una regola per un ambito di configurazione, selezionare **+ nuova regola**.

1. Immettere nei campi seguenti per creare una regola:

   1. **Nome regola:** Nome della regola di configurazione. Questo campo non ha alcun effetto sugli asset a cui si applica la regola.

   1. **Nome completo:** Percorso completo che usa una combinazione di testo, sostituzioni dinamiche e sostituzioni statiche per la corrispondenza degli asset con la regola di configurazione. Questo percorso è relativo all'ambito della regola di configurazione. Per istruzioni dettagliate su come specificare nomi completi, vedere la sezione [sintassi](#syntax) riportata di seguito.

   1. **Nome visualizzato:** Nome visualizzato dell'asset. Questo campo è facoltativo. Usare il testo normale e le sostituzioni statiche per personalizzare la modalità di visualizzazione di un asset nel catalogo. Per istruzioni più dettagliate, vedere la sezione [sintassi](#syntax) riportata di seguito.

   1. Non **raggruppare come set di risorse:** Se abilitata, la risorsa corrispondente non verrà raggruppata in un set di risorse.

      :::image type="content" source="media/how-to-scoped-resource-sets/scoped-resource-set-rule-example.png" alt-text="Crea nuova regola di configurazione." border="true":::

1. Salvare la regola facendo clic su **Aggiungi**.

## <a name="scoped-resource-set-syntax"></a><a name="syntax"></a> Sintassi del set di risorse con ambito

Quando si creano regole di set di risorse con ambito, usare la sintassi seguente per specificare le regole di asset da applicare a.

### <a name="dynamic-replacers-single-brackets"></a>Sostituzioni dinamiche (parentesi quadre)

Le singole parentesi quadre vengono usate come **sostituzioni dinamiche** in una regola di set di risorse con ambito. Specificare un sostituto dinamico nel nome completo utilizzando Format `{<replacerName:<replacerType>}` . Se corrisponde, i sostituitori dinamici vengono usati come condizione di raggruppamento che indica che gli asset devono essere rappresentati come set di risorse. Se gli asset sono raggruppati in un set di risorse, il percorso qualificato del set di risorse conterrà `{replacerName}` la posizione in cui è stato specificato il sostituto.

Se ad esempio due asset `folder1/file-1.csv` e `folder2/file-2.csv` corrispondono alla regola `{folder:string}/file-{NUM:int}.csv` , il set di risorse sarà una singola entità `{folder}/file-{NUM}.csv` .

#### <a name="special-case-dynamic-replacers-when-not-grouping-into-resource-set"></a>Caso speciale: sostituzioni dinamiche quando non vengono raggruppate in set di risorse

Se *non raggruppare come set di risorse* è abilitato per una regola di set di risorse con ambito, il nome sostitutivo è un campo facoltativo. `{:<replacerType>}` sintassi valida. Ad esempio, `file-{:int}.csv` corrisponderà correttamente a `file-1.csv` e `file-2.csv` e creerà due asset diversi anziché un set di risorse.

### <a name="static-replacers-double-brackets"></a>Sostituzioni statiche (doppie parentesi quadre)

Le doppie parentesi quadre vengono usate come **sostituzioni statiche** nel nome completo di una regola di set di risorse con ambito. Specificare un sostituto statico nel nome completo utilizzando Format `{{<replacerName>:<replacerType>}}` . Se corrisponde, ogni set di valori di sostituzione statici univoci creerà raggruppamenti di set di risorse diversi.

Se ad esempio due asset `folder1/file-1.csv` e `folder2/file-2.csv` corrispondono alla regola `{{folder:string}}/file-{NUM:int}.csv` , verranno creati due set di risorse `folder1/file-{NUM}.csv` e `folder2/file-{NUM}.csv` .

È possibile usare le sostituzioni statiche per specificare il nome visualizzato di un asset corrispondente a una regola di set di risorse con ambito. Se `{{<replacerName>}}` si usa nel nome visualizzato di una regola, il valore corrispondente viene usato nel nome dell'asset.

### <a name="available-replacement-types"></a>Tipi di sostituzione disponibili

Di seguito sono riportati i tipi disponibili che possono essere usati nei sostituti statici e dinamici:

| Tipo | Struttura |
| ---- | --------- |
| string | Una serie di uno o più caratteri Unicode, inclusi i delimitatori come spazi. |
| INT | Una serie di uno o più caratteri ASCII 0-9, può essere 0 con prefisso (ad esempio, 0001). |
| guid | Una serie di una rappresentazione di stringa 32 o 8-4-4-4-12 di un UUID come defineddefa in [RFC 4122](https://tools.ietf.org/html/rfc4122). |
| Data | Una serie di 6 o 8 0-9 caratteri ASCII con separatori facoltativi: AAAAMMGG, aaaa-mm-gg, AAMMGG, yy-mm-gg, specificato in [RFC 3339](https://tools.ietf.org/html/rfc3339). |
| time | Una serie di 4 o 6 0-9 caratteri ASCII con separatori facoltativi: HHmm, HH: mm, HHmmss, HH: mm: SS specificati in [RFC 3339](https://tools.ietf.org/html/rfc3339). |
|  timestamp | Una serie di 12 o 14 0-9 caratteri ASCII con separatori facoltativi: aaaa-mm-ggThh: mm, yyyyMMddhhmm, aaaa-mm-ggThh: mm: SS, ad aaaammgghhmmss specificato in [RFC 3339](https://tools.ietf.org/html/rfc3339). |
| boolean | Può contenere ' true ' o ' false ', senza distinzione tra maiuscole e minuscole. |
| d'acquisto | Una serie di 0 o più caratteri ASCII 0-9, può essere 0 con prefisso (ad esempio, 0001) e, facoltativamente, un punto ' .' e una serie di uno o più caratteri ASCII 0-9, può essere di 0 postfixed (ad esempio, 100) |
| hex | Una serie di uno o più caratteri ASCII dal set 0-1 e A-F, il valore può essere con prefisso 0 |
| locale | Stringa che corrisponde alla sintassi specificata in [RFC 5646](https://tools.ietf.org/html/rfc5646). |

## <a name="order-of-scoped-resource-set-rules-getting-applied"></a>Ordine di applicazione delle regole del set di risorse con ambito

Di seguito è riportato l'ordine delle operazioni per applicare le regole del set di risorse con ambito:

1. Gli ambiti più specifici hanno la priorità se un asset corrisponde a due regole. Ad esempio, le regole in un ambito `container/folder` verranno applicate prima delle regole nell'ambito `container` .

1. Ordine delle regole all'interno di un ambito specifico. Questa operazione può essere modificata in UX.

1. Se un asset non corrisponde a una regola specificata, viene applicata l'euristica predefinita del set di risorse.

## <a name="examples"></a>Esempi

### <a name="example-1"></a>Esempio 1

Estrazione dei dati SAP in carichi completi e Delta

#### <a name="inputs"></a>Input

File:

- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_02.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/delta/2020/01/15/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_02.txt`

#### <a name="scoped-resource-set-rule"></a>Regola set di risorse con ambito

**Ambito:**`https://myazureblob.blob.core.windows.net/bar/`

**Nome visualizzato:** ' Cliente esterno '

**Nome completo:**`customer/{extract:string}/{year:int}/{month:int}/{day:int}/saptable_customer_{date_from:date}_{date_to:time}_{sequence:int}.txt`

**Set di risorse:** true

#### <a name="output"></a>Output

Un asset del set di risorse

**Nome visualizzato:** Cliente esterno

**Nome completo:**`https://myazureblob.blob.core.windows.net/bar/customer/{extract}/{year}/{month}/{day}/saptable_customer_{date_from}_{date_to}_{sequence}.txt`

### <a name="example-2"></a>Esempio 2

Dati relativi alle cose nel formato avro

#### <a name="inputs"></a>Input

File:

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="scoped-resource-set-rules"></a>Regole set di risorse con ambito

**Ambito:**`https://myazureblob.blob.core.windows.net/bar/`

Regola 1

**Nome visualizzato:** ' Machine-89'

**Nome completo:**`raw/machinename-89/{date:date}/{time:time}-{id:int}.avro`

**Set di risorse:** true

Regola 2

**Nome visualizzato:** ' Machine-90'

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

Dati relativi alle cose nel formato avro

#### <a name="inputs"></a>Input

File:

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.netbar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="scoped-resource-set-rule"></a>Regola set di risorse con ambito

**Ambito:**`https://myazureblob.blob.core.windows.net/bar/`

**Nome visualizzato:** ' Computer-{{MachineID}}'

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

#### <a name="scoped-resource-set-rule"></a>Regola set di risorse con ambito

**Ambito:**`https://myazureblob.blob.core.windows.net/bar/`

**Nome visualizzato:**`Machine-{{machineid}}`

**Nome completo:**`raw/machinename-{{machineid:int}}/{{:date}}/{{:time}}-{{:int}}.avro`

**Set di risorse:** false

#### <a name="outputs"></a>Output

4 Asset singoli

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

Per iniziare, [registrare e analizzare un Azure Data Lake account di archiviazione Gen2](register-scan-adls-gen2.md).