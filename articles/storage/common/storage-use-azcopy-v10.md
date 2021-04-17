---
title: Copiare o spostare dati Archiviazione di Azure usando AzCopy v10 | Microsoft Docs
description: AzCopy è un'utilità della riga di comando che è possibile usare per copiare dati in, da o tra account di archiviazione. Questo articolo consente di scaricare AzCopy, connettersi all'account di archiviazione e di trasferire i file.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: 34d3bd45d2c0bf0260a4f8524cff6f8ac03b746c
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501720"
---
# <a name="get-started-with-azcopy"></a>Introduzione ad AzCopy

AzCopy è un'utilità della riga di comando che è possibile usare per copiare i BLOB o i file da e verso un account di archiviazione. Questo articolo consente di scaricare AzCopy, connettersi all'account di archiviazione e di trasferire i file.

> [!NOTE]
> AzCopy **V10** è la versione attualmente supportata di AzCopy.
>
> Se è necessario usare una versione precedente di AzCopy, vedere la sezione Usare la versione precedente di [AzCopy](#previous-version) di questo articolo.

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>Scaricare AzCopy

Per prima cosa, scaricare il file eseguibile AzCopy V10 in qualsiasi directory del computer. AzCopy V10 è solo un file eseguibile, quindi non c'è niente da installare.

- [Windows a 64 bit](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows a 32 bit](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [macOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Questi file vengono compressi come file ZIP (Windows e Mac) o come file tar (Linux). Per scaricare e decomprimere il file tar in Linux, vedere la documentazione per la distribuzione di Linux.

> [!NOTE]
> Se si vogliono copiare dati da e verso il servizio di archiviazione tabelle di [Azure,](../tables/table-storage-overview.md) installare [AzCopy versione 7.3.](https://aka.ms/downloadazcopynet)

## <a name="run-azcopy"></a>Eseguire Azcopy

Per praticità, è consigliabile aggiungere il percorso della directory del file eseguibile di AzCopy al percorso di sistema per facilitarne l'uso. In questo modo è possibile `azcopy` digitare da qualsiasi directory del sistema.

Se si sceglie di non aggiungere la directory AzCopy al percorso, è necessario modificare le directory nel percorso del file eseguibile AzCopy e digitare o in Windows PowerShell prompt dei `azcopy` `.\azcopy` comandi.

In quanto proprietario dell'account Archiviazione di Azure, non vengono assegnate automaticamente le autorizzazioni per accedere ai dati. Prima di poter eseguire qualsiasi operazione significativa con AzCopy, è necessario decidere come fornire le credenziali di autorizzazione al servizio di archiviazione. 

<a id="choose-how-youll-provide-authorization-credentials"></a>

## <a name="authorize-azcopy"></a>Autorizzare AzCopy

È possibile fornire le credenziali di autorizzazione usando Azure Active Directory (AD) o un token di firma di accesso condiviso.

Usare questa tabella come guida:

| Tipo di archiviazione | Metodo di autorizzazione attualmente supportato |
|--|--|
|**Archiviazione BLOB** | Azure AD e firma di accesso condiviso |
|**Archiviazione BLOB (spazio dei nomi gerarchico)** | Azure AD e firma di accesso condiviso |
|**Archiviazione file** | Solo SAS |

#### <a name="option-1-use-azure-active-directory"></a>Opzione 1: usare Azure Active Directory

Questa opzione è disponibile solo per l'archiviazione BLOB. Usando Azure Active Directory, è possibile specificare le credenziali una sola volta invece di dover aggiungere un token di firma di accesso condiviso a ogni comando.  

> [!NOTE]
> Nella versione corrente, se si prevede di copiare BLOB tra account di archiviazione, sarà necessario aggiungere un token di firma di accesso condiviso a ogni URL di origine. È possibile omettere il token di firma di accesso condiviso solo dall'URL di destinazione. Per esempi, vedere [Copiare BLOB tra account di archiviazione.](#transfer-data)

Per autorizzare l'accesso usando Azure AD, vedere Autorizzare l'accesso ai BLOB con [AzCopy e Azure Active Directory (Azure AD)](storage-use-azcopy-authorize-azure-active-directory.md).

#### <a name="option-2-use-a-sas-token"></a>Opzione 2: Usare un token di firma di accesso condiviso

È possibile aggiungere un token di firma di accesso condiviso a ogni URL di origine o di destinazione che usa nei comandi AzCopy.

Questo comando di esempio copia in modo ricorsivo i dati da una directory locale a un contenitore BLOB. Un token di firma di accesso condiviso fittizio viene aggiunto alla fine dell'URL del contenitore.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Per altre informazioni sui token di firma di accesso condiviso e su come ottenerne uno, vedere [Uso delle](./storage-sas-overview.md)firme di accesso condiviso .

> [!NOTE]
> [L'impostazione Trasferimento sicuro](storage-require-secure-transfer.md) richiesto di un account di archiviazione determina se la connessione a un account di archiviazione è protetta con Transport Layer Security (TLS). Questa opzione è attivata per impostazione predefinita.   

<a id="transfer-data"></a>

## <a name="transfer-data"></a>Trasferire i dati

Dopo aver autorizzato l'identità o ottenuto un token di firma di accesso condiviso, è possibile iniziare a trasferire i dati.

Per trovare comandi di esempio, vedere uno di questi articoli.

| Servizio | Articolo |
|--------|-----------|
|Archiviazione BLOB di Azure|[Caricare file in Archiviazione BLOB di Azure](storage-use-azcopy-blobs-upload.md) |
|Archiviazione BLOB di Azure|[Scaricare BLOB da Archiviazione BLOB di Azure](storage-use-azcopy-blobs-download.md)|
|Archiviazione BLOB di Azure|[Copiare BLOB tra account di archiviazione di Azure](storage-use-azcopy-blobs-copy.md)|
|Archiviazione BLOB di Azure|[Sincronizzare con Archiviazione BLOB di Azure](storage-use-azcopy-blobs-synchronize.md)|
|File di Azure |[Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)|
|Amazon S3|[Copiare dati da Amazon S3 a Archiviazione di Azure](storage-use-azcopy-s3.md)|
|Google Cloud Storage|[Copiare dati da Google Cloud Storage Archiviazione di Azure (anteprima)](storage-use-azcopy-google-cloud.md)|
|Azure Stack archiviazione|[Trasferire dati con AzCopy e Azure Stack archiviazione](/azure-stack/user/azure-stack-storage-transfer#azcopy)|

## <a name="get-command-help"></a>Ottenere la guida per i comandi

Per visualizzare un elenco di comandi, digitare `azcopy -h` e quindi premere INVIO.

Per informazioni su un comando specifico, è sufficiente includere il nome del comando (ad esempio: `azcopy list -h` ).

> [!div class="mx-imgBorder"]
> ![Guida inline](media/storage-use-azcopy-v10/azcopy-inline-help.png)

### <a name="list-of-commands"></a>Elenco di comandi

Nella tabella seguente sono elencati tutti i comandi AzCopy v10. Ogni comando si collega a un articolo di riferimento. 

|Comando|Descrizione|
|---|---|
|[azcopy bench](storage-ref-azcopy-bench.md?toc=/azure/storage/blobs/toc.json)|Esegue un benchmark delle prestazioni caricando o scaricando i dati di test in o da un percorso specificato.|
|[azcopy copy](storage-ref-azcopy-copy.md?toc=/azure/storage/blobs/toc.json)|Copia i dati di origine in un percorso di destinazione|
|[azcopy doc](storage-ref-azcopy-doc.md?toc=/azure/storage/blobs/toc.json)|Genera la documentazione per lo strumento in formato Markdown.|
|[azcopy env](storage-ref-azcopy-env.md?toc=/azure/storage/blobs/toc.json)|Mostra le variabili di ambiente che possono configurare il comportamento di AzCopy.|
|[azcopy jobs](storage-ref-azcopy-jobs.md?toc=/azure/storage/blobs/toc.json)|Sottocomandi correlati alla gestione dei processi.|
|[azcopy jobs clean](storage-ref-azcopy-jobs-clean.md?toc=/azure/storage/blobs/toc.json)|Rimuovere tutti i file di log e di piano per tutti i processi.|
|[azcopy jobs list](storage-ref-azcopy-jobs-list.md?toc=/azure/storage/blobs/toc.json)|Visualizza informazioni su tutti i processi.|
|[azcopy jobs remove](storage-ref-azcopy-jobs-remove.md?toc=/azure/storage/blobs/toc.json)|Rimuovere tutti i file associati all'ID processo specificato.|
|[azcopy jobs resume](storage-ref-azcopy-jobs-resume.md?toc=/azure/storage/blobs/toc.json)|Riprende il processo esistente con l'ID processo specificato.|
|[azcopy jobs show](storage-ref-azcopy-jobs-show.md?toc=/azure/storage/blobs/toc.json)|Visualizza informazioni dettagliate per l'ID processo specificato.|
|[azcopy load](storage-ref-azcopy-load.md)|Sottocomandi correlati al trasferimento dei dati in formati specifici.|
|[azcopy load clfs](storage-ref-azcopy-load-avere-cloud-file-system.md?toc=/azure/storage/blobs/toc.json)|Trasferisce i dati locali in un contenitore e li archivia nel formato Microsoft Avere Cloud FileSystem (CLFS).|
|[azcopy list](storage-ref-azcopy-list.md?toc=/azure/storage/blobs/toc.json)|Elenca le entità in una determinata risorsa.|
|[azcopy login](storage-ref-azcopy-login.md?toc=/azure/storage/blobs/toc.json)|Accede a per Azure Active Directory accedere Archiviazione di Azure risorse.|
|[azcopy logout](storage-ref-azcopy-logout.md?toc=/azure/storage/blobs/toc.json)|Disconnette l'utente e termina l'accesso Archiviazione di Azure risorse.|
|[azcopy make](storage-ref-azcopy-make.md?toc=/azure/storage/blobs/toc.json)|Crea un contenitore o una condivisione file.|
|[azcopy remove](storage-ref-azcopy-remove.md?toc=/azure/storage/blobs/toc.json)|Eliminare BLOB o file da un account di archiviazione di Azure.|
|[azcopy sync](storage-ref-azcopy-sync.md?toc=/azure/storage/blobs/toc.json)|Replica il percorso di origine nel percorso di destinazione.|

## <a name="use-in-a-script"></a>Usare in uno script

#### <a name="obtain-a-static-download-link"></a>Ottenere un collegamento di download statico

Nel corso del tempo, il collegamento di [download](#download-and-install-azcopy) di AzCopy farà riferimento alle nuove versioni di AzCopy. Se lo script scarica AzCopy, lo script potrebbe smettere di funzionare se una versione più recente di AzCopy modifica le funzionalità da cui dipende lo script.

Per evitare questi problemi, ottenere un collegamento statico (immutato) alla versione corrente di AzCopy. In questo modo, lo script scarica la stessa versione esatta di AzCopy ogni volta che viene eseguito.

Per ottenere il collegamento, eseguire questo comando:

| Sistema operativo  | Comando |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> Per Linux, nel comando rimuove la cartella di primo livello che contiene il nome della versione ed estrae il file binario `--strip-components=1` `tar` direttamente nella cartella corrente. In questo modo lo script può essere aggiornato con una nuova versione `azcopy` di aggiornando solo l'URL. `wget`

L'URL viene visualizzato nell'output di questo comando. Lo script può quindi scaricare AzCopy usando tale URL.

| Sistema operativo  | Comando |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

#### <a name="escape-special-characters-in-sas-tokens"></a>Caratteri speciali di escape nei token di firma di accesso condiviso

Nei file batch con estensione , sarà necessario eseguire l'escape dei caratteri `.cmd` visualizzati nei token di firma di accesso `%` condiviso. A tale scopo, aggiungere un carattere aggiuntivo `%` accanto ai caratteri esistenti nella stringa del token di firma di accesso `%` condiviso.

#### <a name="run-scripts-by-using-jenkins"></a>Eseguire script con Jenkins

Se si prevede di usare [Jenkins](https://jenkins.io/) per eseguire gli script, assicurarsi di inserire il comando seguente all'inizio dello script.

```
/usr/bin/keyctl new_session
```

## <a name="use-in-azure-storage-explorer"></a>Usare in Azure Storage Explorer

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) usa AzCopy per eseguire tutte le operazioni di trasferimento dei dati. È possibile [usare Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) se si vogliono sfruttare i vantaggi in termini di prestazioni di AzCopy, ma si preferisce usare un'interfaccia utente grafica anziché la riga di comando per interagire con i file.

Storage Explorer la chiave dell'account per eseguire operazioni, quindi dopo aver eseguito l'accesso Storage Explorer, non sarà necessario fornire credenziali di autorizzazione aggiuntive.

<a id="previous-version"></a>

## <a name="configure-optimize-and-fix"></a>Configurare, ottimizzare e correggere

Vedere una delle risorse seguenti:

- [Impostazioni di configurazione di AzCopy](storage-ref-azcopy-configuration-settings.md)

- [Ottimizzare le prestazioni di AzCopy](storage-use-azcopy-optimize.md)

- [Risolvere i problemi di AzCopy V10 in Archiviazione di Azure usando i file di log](storage-use-azcopy-configure.md)

## <a name="use-a-previous-version"></a>Usare una versione precedente

Se è necessario usare la versione precedente di AzCopy, vedere uno dei collegamenti seguenti:

- [AzCopy in Windows (v8)](/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy in Linux (v7)](/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>Passaggi successivi

In caso di domande, problemi o commenti generali, inviarli [nella pagina GitHub.](https://github.com/Azure/azure-storage-azcopy)
