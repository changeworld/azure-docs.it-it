---
title: Esecuzione dell'esportazione richiamando $export comando nell'API di Azure per FHIR
description: Questo articolo descrive come esportare i dati di FHIR usando $export
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/18/2021
ms.author: cavoeg
ms.openlocfilehash: a5b3daa499546f3a30b5a4d133d77786a1916b6a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559197"
---
# <a name="how-to-export-fhir-data"></a>Come esportare i dati di FHIR


La funzionalità di esportazione bulk consente di esportare i dati dal server FHIR in base alla [specifica FHIR](https://hl7.org/fhir/uv/bulkdata/export/index.html). 

Prima di usare $export, è necessario assicurarsi che l'API di Azure per FHIR sia configurata per usarla. Per la configurazione delle impostazioni di esportazione e la creazione dell'account di archiviazione di Azure, fare riferimento alla [pagina Configura dati di esportazione](configure-export-data.md).

## <a name="using-export-command"></a>Uso di $export comando

Dopo aver configurato l'API di Azure per FHIR per l'esportazione, è possibile usare il comando $export per esportare i dati dal servizio. I dati verranno archiviati nell'account di archiviazione specificato durante la configurazione dell'esportazione. Per informazioni su come richiamare $export comando in FHIR server, vedere la documentazione sulla [specifica HL7 FHIR $Export](https://hl7.org/Fhir/uv/bulkdata/export/index.html).


**Processi bloccati in uno stato non valido**

In alcune situazioni è possibile che un processo rimanga bloccato in uno stato non valido. Questa situazione può verificarsi soprattutto se le autorizzazioni dell'account di archiviazione non sono state impostate correttamente. Un modo per verificare se l'esportazione ha esito positivo consiste nel controllare l'account di archiviazione per verificare se sono presenti i file del contenitore corrispondente, ovvero ndjson. Se non sono presenti e non sono in esecuzione altri processi di esportazione, esiste la possibilità che il processo corrente sia bloccato in uno stato non valido. È necessario annullare il processo di esportazione inviando una richiesta di annullamento e provare a ripetere l'accodamento del processo. Il tempo di esecuzione predefinito per un'esportazione in stato non valido è 10 minuti prima che venga arrestato e spostato in un nuovo processo o ritentare l'esportazione. 

L'API di Azure per FHIR supporta $export ai livelli seguenti:
* [Sistema](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export): `GET https://<<FHIR service base URL>>/$export>>`
* [Paziente](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients): `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [Gruppo di pazienti *](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) : l'API di Azure per FHIR Esporta tutte le risorse correlate, ma non Esporta le caratteristiche del gruppo: `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`

Quando i dati vengono esportati, viene creato un file separato per ogni tipo di risorsa. Per assicurarsi che i file esportati non diventino troppo grandi. Viene creato un nuovo file dopo che le dimensioni di un singolo file esportato diventano maggiori di 64 MB. Il risultato è che è possibile ottenere più file per ogni tipo di risorsa, che verranno enumerati (ovvero patient-1. ndjson, patient-2. ndjson). 


> [!Note] 
> `Patient/$export` e `Group/[ID]/$export` possono esportare risorse duplicate se la risorsa si trova in un raggruppamento di più di una risorsa o si trova in più gruppi.

Inoltre, il controllo dello stato di esportazione tramite l'URL restituito dall'intestazione Location durante l'accodamento è supportato insieme all'annullamento del processo di esportazione effettivo.

### <a name="exporting-fhir-data-to-adls-gen2"></a>Esportazione dei dati di FHIR in ADLS Gen2

Attualmente sono supportati $export per gli account di archiviazione ADLS Gen2 abilitati, con la limitazione seguente:

- L'utente non può sfruttare gli [spazi dei nomi gerarchici](../../storage/blobs/data-lake-storage-namespace.md), ma non esiste un modo per indirizzare l'esportazione a una sottodirectory specifica all'interno del contenitore. Viene fornita solo la possibilità di fare riferimento a un contenitore specifico (in cui viene creata una nuova cartella per ogni esportazione).
- Una volta completata l'esportazione, non viene mai eseguita alcuna esportazione in tale cartella, perché le esportazioni successive nello stesso contenitore si troveranno all'interno di una cartella appena creata.


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

> [!Note]
> Solo gli account di archiviazione nella stessa sottoscrizione dell'API di Azure per FHIR possono essere registrati come destinazione per le operazioni di $export.

## <a name="secure-export-to-azure-storage"></a>Esportazione sicura in archiviazione di Azure

L'API di Azure per FHIR supporta un'operazione di esportazione sicura. Un'opzione per eseguire un'esportazione sicura consiste nell'consentire l'accesso all'account di archiviazione di Azure a specifici indirizzi IP associati all'API di Azure per FHIR. Le configurazioni sono diverse a seconda che l'account di archiviazione si trovi nella stessa posizione o in un percorso diverso rispetto a quello dell'API di Azure per FHIR.

### <a name="when-the-azure-storage-account-is-in-a-different-region"></a>Quando l'account di archiviazione di Azure si trova in un'area diversa

Selezionare **rete** dell'account di archiviazione di Azure dal portale. 

   :::image type="content" source="media/export-data/storage-networking.png" alt-text="Impostazioni di rete di archiviazione di Azure." lightbox="media/export-data/storage-networking.png":::
   
Selezionare **Reti selezionate**. Nella sezione firewall, specificare l'indirizzo IP nella casella **intervallo indirizzi** . Aggiungere gli intervalli IP per consentire l'accesso da Internet o dalle reti locali. È possibile trovare l'indirizzo IP nella tabella seguente per l'area di Azure in cui viene eseguito il provisioning del servizio API di Azure per FHIR.

|**Area di Azure**         |**Indirizzo IP pubblico** |
|:----------------------|:-------------------|
| Australia orientale       | 20.53.44.80       |
| Canada centrale       | 20.48.192.84      |
| Stati Uniti centrali           | 52.182.208.31     |
| Stati Uniti orientali              | 20.62.128.148     |
| Stati Uniti orientali 2            | 20.49.102.228     |
| Stati Uniti orientali 2 EUAP       | 20.39.26.254      |
| Germania settentrionale        | 51.116.51.33      |
| Germania centro-occidentale | 51.116.146.216    |
| Giappone orientale           | 20.191.160.26     |
| Corea centrale        | 20.41.69.51       |
| Stati Uniti centro-settentrionali     | 20.49.114.188     |
| Europa settentrionale         | 52.146.131.52     |
| Sudafrica settentrionale   | 102.133.220.197   |
| Stati Uniti centro-meridionali     | 13.73.254.220     |
| Asia sud-orientale       | 23.98.108.42      |
| Svizzera settentrionale    | 51.107.60.95      |
| Regno Unito meridionale             | 51.104.30.170     |
| Regno Unito occidentale              | 51.137.164.94     |
| Stati Uniti centro-occidentali      | 52.150.156.44     |
| Europa occidentale          | 20.61.98.66       |
| Stati Uniti occidentali 2            | 40.64.135.77      |

### <a name="when-the-azure-storage-account-is-in-the-same-region"></a>Quando l'account di archiviazione di Azure si trova nella stessa area

Il processo di configurazione è identico al precedente, ad eccezione del fatto che viene usato un intervallo di indirizzi IP specifico in formato CIDR, 100.64.0.0/10. Il motivo per cui è necessario specificare l'intervallo di indirizzi IP, che include 100.64.0.0-100.127.255.255, è dato dal fatto che l'indirizzo IP effettivo utilizzato dal servizio varia, ma sarà compreso nell'intervallo, per ogni richiesta $export.

> [!Note] 
> In alternativa, è possibile che venga usato un indirizzo IP privato compreso nell'intervallo 10.0.2.0/24. In tal caso, l'operazione di $export avrà esito negativo. È possibile ritentare la richiesta di $export, ma non vi è alcuna garanzia che venga usato il prossimo intervallo di un indirizzo IP compreso nell'intervallo di 100.64.0.0/10. Questo è il comportamento di rete noto per la progettazione. In alternativa, è possibile configurare l'account di archiviazione in un'area diversa.
    
## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come esportare le risorse di FHIR usando $export comando. Successivamente, per informazioni su come esportare dati deselezionati, vedere:
 
>[!div class="nextstepaction"]
>[Esporta dati deselezionati](de-identified-export.md)
