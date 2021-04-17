---
title: Impostazione di configurazione AzCopy v10 (Archiviazione di Azure) | Microsoft Docs
description: Questo articolo fornisce informazioni di riferimento per le impostazioni di configurazione di AzCopy V10.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 4fcbcf145dc417d2a7f78913e70429c3929cd902
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509118"
---
# <a name="azcopy-v10-configuration-settings-azure-storage"></a>Impostazioni di configurazione di AzCopy v10 (Archiviazione di Azure)

AzCopy è un'utilità della riga di comando che è possibile usare per copiare i BLOB o i file da e verso un account di archiviazione. Questo articolo contiene un elenco di variabili di ambiente che è possibile usare per configurare AzCopy v10.

> [!NOTE]
> Se si sta cercando contenuto per iniziare a usare AzCopy, vedere [Introduzione ad AzCopy.](storage-use-azcopy-v10.md)

## <a name="azcopy-v10-environment-variables"></a>Variabili di ambiente AzCopy v10

La tabella seguente descrive ogni variabile di ambiente e fornisce collegamenti al contenuto che consentono di usare la variabile.

| Variabile di ambiente | Descrizione |
|--|--|
| AWS_ACCESS_KEY_ID | Amazon Web Services chiave di accesso. Fornisce una chiave da autorizzare con Amazon Web Services. [Copiare dati da Amazon S3 a Archiviazione di Azure usando AzCopy](storage-use-azcopy-s3.md) |
| AWS_SECRET_ACCESS_KEY | Amazon Web Services chiave di accesso privata Fornisce una chiave privata da autorizzare con Amazon Web Services. [Copiare dati da Amazon S3 a Archiviazione di Azure usando AzCopy](storage-use-azcopy-s3.md) |
| AZCOPY_ACTIVE_DIRECTORY_ENDPOINT | Endpoint Azure Active Directory da usare. Questa variabile viene usata solo per l'accesso automatico. Usare invece il flag della riga di comando quando si richiama il comando login. |
| AZCOPY_AUTO_LOGIN_TYPE | Impostare questa variabile su `DEVICE` , `MSI` o `SPN` . Questa variabile consente di autorizzare senza usare il `azcopy login` comando . Questo meccanismo è utile nei casi in cui il sistema operativo non dispone di un archivio segreto, ad esempio un *keyring* Linux. Vedere [Autorizzare senza un archivio segreto.](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_BUFFER_GB | Specificare la quantità massima di memoria di sistema che AzCopy deve usare durante il download e il caricamento di file. Esprimere questo valore in gigabyte (GB). Vedere [Ottimizzare l'uso della memoria](storage-use-azcopy-optimize.md#optimize-memory-use) |
| AZCOPY_CACHE_PROXY_LOOKUP | Per impostazione predefinita, AzCopy in Windows memorizza nella cache le ricerche del server proxy a livello di nome host (senza prendere in considerazione il percorso URL). Impostare su qualsiasi altro valore diverso da 'true' per disabilitare la cache. |
| AZCOPY_CONCURRENCY_VALUE | Specifica il numero di richieste simultanee che possono verificarsi. È possibile usare questa variabile per aumentare la velocità effettiva. Se il computer ha meno di 5 CPU, il valore di questa variabile viene impostato su `32` . Altrimenti, il valore predefinito è pari a 16 moltiplicato per il numero di CPU. Il valore predefinito massimo di questa variabile è , ma è possibile impostare manualmente questo valore su un valore `3000` superiore o inferiore. Vedere [Aumentare la concorrenza](storage-use-azcopy-optimize.md#increase-concurrency) |
| AZCOPY_CONCURRENT_FILES | Esegue l'override del numero (approssimativo) di file in corso in qualsiasi momento, controllando il numero di file per cui vengono avviati contemporaneamente i trasferimenti. |
| AZCOPY_CONCURRENT_SCAN | Controlla il grado massimo di parallelismo usato durante l'analisi. Influisce solo sugli enumeratori parallelizzati, che includono File di Azure/BLOB e file system locali. |
| AZCOPY_DEFAULT_SERVICE_API_VERSION | Esegue l'override della versione dell'API del servizio in modo che AzCopy possa supportare ambienti personalizzati, ad esempio Azure Stack. |
| AZCOPY_DISABLE_HIERARCHICAL_SCAN | Si applica solo quando Blob di Azure è l'origine. L'analisi simultanea è più veloce, ma usa l'API elenco gerarchico, che può comportare più operazioni di I/O/costo. Specificare 'true' per sacrificare le prestazioni, ma risparmiare sui costi. |
| AZCOPY_JOB_PLAN_LOCATION | Sostituzioni in cui vengono archiviati i file del piano di processo (usati per il rilevamento e la ripresa dello stato) per evitare di riempire un disco. |
| AZCOPY_LOG_LOCATION | Esegue l'override della posizione in cui sono archiviati i file di log, per evitare di riempire un disco. |
| AZCOPY_MSI_CLIENT_ID | ID client di un'identità gestita assegnata dall'utente. Utilizzare quando `AZCOPY_AUTO_LOGIN_TYPE` è impostato su `MSI` . Vedere [Autorizzare senza un archivio segreto](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_MSI_OBJECT_ID | ID oggetto dell'identità gestita assegnata dall'utente. Utilizzare quando `AZCOPY_AUTO_LOGIN_TYPE` è impostato su `MSI` . Vedere [Autorizzare senza un archivio segreto](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_MSI_RESOURCE_STRING | ID risorsa dell'identità gestita assegnata dall'utente. Vedere [Autorizzare senza un archivio segreto](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_PACE_PAGE_BLOBS | La velocità effettiva per i BLOB di pagine deve essere regolata automaticamente in base ai limiti del servizio? Il valore predefinito è true. Impostare su 'false' per disabilitare |
| AZCOPY_PARALLEL_STAT_FILES | Fa in modo che AzCopy ricerca le proprietà dei file nei 'thread' paralleli durante l'analisi dei file file system.  I thread vengono estratti dal pool definito da AZCOPY_CONCURRENT_SCAN.  L'impostazione di questo valore su true può migliorare le prestazioni di analisi in Linux.  Non necessario o consigliato in Windows. |
| AZCOPY_SHOW_PERF_STATES | Se impostato su qualsiasi valore, l'output su schermo includerà i conteggi dei blocchi per stato |
| AZCOPY_SPA_APPLICATION_ID | ID applicazione della registrazione dell'app dell'entità servizio. Usare quando `AZCOPY_AUTO_LOGIN_TYPE` è impostato su `SPN` . Vedere [Autorizzare senza un archivio segreto](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CERT_PASSWORD | Password di un certificato. Usare quando `AZCOPY_AUTO_LOGIN_TYPE` è impostato su `SPN` . Vedere [Autorizzare senza un archivio segreti](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CERT_PATH | Percorso relativo o completo di un file di certificato. Utilizzare quando `AZCOPY_AUTO_LOGIN_TYPE` è impostato su `SPN` . Vedere [Autorizzare senza un archivio segreto](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CLIENT_SECRET | Segreto del client. Utilizzare quando `AZCOPY_AUTO_LOGIN_TYPE` è impostato su `SPN` . Vedere [Autorizzare senza un archivio segreto](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_TENANT_ID | ID Azure Active Directory tenant da usare per l'accesso interattivo del dispositivo OAuth. Questa variabile viene usata solo per l'accesso automatico. Usare invece il flag della riga di comando quando si richiama il comando login. |
| AZCOPY_TUNE_TO_CPU | Impostare su false per impedire ad AzCopy di prendere in considerazione l'utilizzo della CPU durante la configurazione automatica del livello di concorrenza, ad esempio nel comando benchmark. |
| AZCOPY_USER_AGENT_PREFIX | Aggiungere un prefisso all'agente utente AzCopy predefinito, usato per scopi di telemetria. Viene inserito automaticamente uno spazio. |
| GOOGLE_APPLICATION_CREDENTIALS | Percorso assoluto del file di chiave dell'account del servizio Fornisce una chiave da autorizzare con Google Cloud Storage. [Copiare dati da Google Cloud Storage Archiviazione di Azure usando AzCopy (anteprima)](storage-ref-azcopy-configuration-settings.md) |
| HTTPS_PROXY | Configura le impostazioni proxy per AzCopy. Impostare questa variabile sull'indirizzo IP del proxy e sul numero di porta proxy. Ad esempio: `xx.xxx.xx.xxx:xx`. Se si esegue AzCopy in Windows, AzCopy rileva automaticamente le impostazioni proxy, quindi non è necessario usare questa impostazione in Windows. Se si sceglie di usare questa impostazione in Windows, verrà eseguito l'override del rilevamento automatico. Vedere [Configurare le impostazioni del proxy](#configure-proxy-settings) |


## <a name="configure-proxy-settings"></a>Configurare le impostazioni proxy

Per configurare le impostazioni proxy per AzCopy, impostare la variabile `HTTPS_PROXY` di ambiente . Se si esegue AzCopy in Windows, AzCopy rileva automaticamente le impostazioni proxy, quindi non è necessario usare questa impostazione in Windows. Se si sceglie di usare questa impostazione in Windows, verrà eseguito l'override del rilevamento automatico.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | In un prompt dei comandi usare: `set HTTPS_PROXY=<proxy IP>:<proxy port>`<br> In PowerShell usare: `$env:HTTPS_PROXY="<proxy IP>:<proxy port>"`|
| **Linux** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |
| **macOS** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |

Attualmente, AzCopy non supporta proxy che richiedono l'autenticazione con NTLM o Kerberos.

### <a name="bypassing-a-proxy"></a>Bypass di un proxy

Se si esegue AzCopy in Windows e si vuole indicare di non usare alcun _proxy_ (anziché rilevare automaticamente le impostazioni), usare questi comandi. Con queste impostazioni, AzCopy non cerca né tenta di usare alcun proxy.

| Sistema operativo | Ambiente | Comandi  |
|--------|-----------|----------|
| **Windows** | Prompt dei comandi (CMD) | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

In altri sistemi operativi, è sufficiente lasciare HTTPS_PROXY variabile non impostata se non si vuole usare alcun proxy.

## <a name="see-also"></a>Vedi anche

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Ottimizzare le prestazioni di AzCopy v10 con Archiviazione di Azure](storage-use-azcopy-optimize.md)
- [Risolvere i problemi di AzCopy V10 in Archiviazione di Azure usando i file di log](storage-use-azcopy-configure.md)
