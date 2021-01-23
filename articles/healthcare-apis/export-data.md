---
title: Esecuzione dell'esportazione richiamando $export comando nell'API di Azure per FHIR
description: Questo articolo descrive come esportare i dati di FHIR usando $export
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/21/2021
ms.author: cavoeg
ms.openlocfilehash: 48dbd0892c9ec02f203edba55d1104f1ab0118a8
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737609"
---
# <a name="how-to-export-fhir-data"></a>Come esportare i dati di FHIR


La funzionalità di esportazione bulk consente di esportare i dati dal server FHIR in base alla [specifica FHIR](https://hl7.org/fhir/uv/bulkdata/export/index.html). 

Prima di usare $export, è necessario assicurarsi che l'API di Azure per FHIR sia configurata per usarla. Per la configurazione delle impostazioni di esportazione e la creazione dell'account di archiviazione di Azure, fare riferimento alla [pagina Configura dati di esportazione](configure-export-data.md).

## <a name="using-export-command"></a>Uso di $export comando

Dopo aver configurato l'API di Azure per FHIR per l'esportazione, è possibile usare il comando $export per esportare i dati dal servizio. I dati verranno archiviati nell'account di archiviazione specificato durante la configurazione dell'esportazione. Per informazioni su come richiamare $export comando in FHIR server, vedere la documentazione sulla [specifica HL7 FHIR $Export](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

L'API di Azure per FHIR supporta $export ai livelli seguenti:
* [Sistema](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export): `GET https://<<FHIR service base URL>>/$export>>`
* [Paziente](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients): `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [Gruppo di pazienti *](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) : l'API di Azure per FHIR Esporta tutte le risorse correlate, ma non Esporta le caratteristiche del gruppo: `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`



> [!Note] 
> `Patient/$export` e `Group/[ID]/$export` possono esportare risorse duplicate se la risorsa si trova in un raggruppamento di più di una risorsa o si trova in più gruppi.

Inoltre, il controllo dello stato di esportazione tramite l'URL restituito dall'intestazione Location durante l'accodamento è supportato insieme all'annullamento del processo di esportazione effettivo.

## <a name="settings-and-parameters"></a>Impostazioni e parametri

### <a name="headers"></a>Intestazioni
È necessario impostare due parametri di intestazione obbligatori per $export processi. I valori sono definiti dalla [specifica $Export](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers)corrente.
* **Accept** -Application/Fhir + JSON
* **Preferisci** -Rispondi-asincrono

### <a name="query-parameters"></a>Parametri di query
L'API di Azure per FHIR supporta i parametri di query seguenti. Tutti questi parametri sono facoltativi:
|Query parameter (Parametro di query)        | Definito dalla specifica FHIR?    |  Descrizione|
|------------------------|---|------------|
| \_outputFormat | Sì | Attualmente supporta tre valori da allineare alla specifica FHIR: Application/FHIR + ndjson, Application/ndjson o solo ndjson. Tutti i processi di esportazione restituiranno `ndjson` e il valore passato non ha alcun effetto sul comportamento del codice. |
| \_since | Sì | Consente di esportare solo le risorse che sono state modificate dopo il periodo di tempo specificato |
| \_tipo | Sì | Consente di specificare i tipi di risorse che verranno inclusi. Ad esempio, \_ Type = patient restituisce solo le risorse del paziente|
| \_TypeFilter | Sì | Per richiedere filtri con granularità fine, è possibile usare \_ TypeFilter insieme al \_ parametro di tipo. Il valore del parametro _typeFilter è un elenco delimitato da virgole di query FHIR che limitano ulteriormente i risultati |
| \_contenitore | No |  Specifica il contenitore all'interno dell'account di archiviazione configurato in cui devono essere esportati i dati. Se viene specificato un contenitore, i dati verranno esportati in tale contenitore in una nuova cartella con il nome. Se il contenitore non è specificato, verrà esportato in un nuovo contenitore usando timestamp e ID processo. |


## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come esportare le risorse di FHIR usando $export comando. Si apprenderà quindi come esportare i dati deselezionati:
 
>[!div class="nextstepaction"]
>[Esporta dati deselezionati](de-identified-export.md)
