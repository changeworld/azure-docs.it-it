---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: da88b8554d6c3214da9a386613538c237a318f73
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546905"
---
## <a name="download-and-install"></a>Download e installazione

#### <a name="windows-install"></a>[Installazione di Windows](#tab/windowsinstall)

Per installare l’interfaccia della riga di comando di Voce su Windows, seguire questa procedura:

1. In Windows è necessaria la versione di [Microsoft Visual C++ Redistributable per Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) per la piattaforma in uso. La prima volta che si esegue questa installazione può essere necessario riavviare il sistema.
2. Scaricare l'[archivio zip](https://aka.ms/speech/spx-zips.zip) dell’interfaccia della riga di comando di Voce, quindi estrarlo.
3. Passare alla directory in cui è stato estratto `spx-zips`. Questa cartella contiene i file di programma per l'interfaccia della riga di comando di Voce in un'ampia varietà di piattaforme. 
4. Estrarre i file per la piattaforma in uso (`spx-net471` per .NET Framework 4.7 o `spx-netcore-win-x64` per .NET Core 3.0 in CPU x64). Tenere presente che `spx` verrà eseguito da questa directory.

### <a name="run-the-speech-cli"></a>Eseguire l'interfaccia della riga di comando per Voce

1. Aprire il prompt dei comandi o PowerShell, quindi passare alla directory in cui è stata estratta l'interfaccia della riga di comando di Voce.  
2. Digitare `spx` per visualizzare i comandi della Guida per l'interfaccia della riga di comando di Voce.

> [!NOTE]
> PowerShell non controlla la directory locale durante la ricerca di un comando. In PowerShell cambiare directory passando alla posizione di `spx` e chiamare lo strumento immettendo `.\spx`.
> Se si aggiunge questa directory al percorso, PowerShell e il prompt dei comandi di Windows troveranno `spx` da qualsiasi directory senza includere il prefisso `.\`.

### <a name="font-limitations"></a>Limitazioni dei tipi di carattere

In Windows, l'interfaccia della riga di comando di Voce può visualizzare solo i tipi di carattere disponibili per il prompt dei comandi nel computer locale.
Il [terminale Windows](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) supporta tutti i tipi di carattere prodotti in modo interattivo dall'interfaccia della riga di comando di Voce.

Se l'output viene restituito in un file, è possibile che anche un editor di testo come Blocco note o un Web browser come Microsoft Edge visualizzi tutti i tipi di carattere.

#### <a name="linux-install"></a>[Installazione di Linux](#tab/linuxinstall)

Per installare l’interfaccia della riga di comando di Voce su Linux su una CPU x64, seguire questa procedura:

1. Installare [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0).
2. Scaricare l'[archivio zip](https://aka.ms/speech/spx-zips.zip) dell’interfaccia della riga di comando di Voce, quindi estrarlo.
3. Passare alla directory radice `spx-zips` estratta dal download ed estrarre `spx-netcore-30-linux-x64` in una nuova directory `~/spx`.
4. In un terminale digitare i comandi seguenti:
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

Digitare `spx` per visualizzare la guida per l'interfaccia della riga di comando di Voce.

#### <a name="docker-install-windows-linux-macos"></a>[Installazione in Docker (Windows, Linux, macOS)](#tab/dockerinstall)

Seguire questa procedura per installare l'interfaccia della riga di comando di Voce in un contenitore Docker:

1. <a href="https://www.docker.com/get-started" target="_blank">Installare Docker Desktop<span class="docon docon-navigate-external x-hidden-focus"></span></a> per la piattaforma se non è già stato fatto.
2. In un nuovo prompt dei comandi o terminale digitare questo comando: 
   ```shell   
   docker pull msftspeech/spx
   ```
3. Digitare il comando seguente. Dovrebbero essere visualizzate informazioni della Guida per l'interfaccia della riga di comando di Voce: 
   ```shell 
   docker run -it --rm msftspeech/spx help
   ```

### <a name="mount-a-directory-in-the-container"></a>Montare una directory nel contenitore

Lo strumento interfaccia della riga di comando di Voce salva le impostazioni di configurazione come file e carica tali file durante l'esecuzione di qualsiasi comando, ad eccezione dei comandi della Guida.
Quando si usa l'interfaccia della riga di comando di Voce in un contenitore Docker, è necessario montare una directory locale dal contenitore, in modo che il contenitore possa archiviare o trovare le impostazioni di configurazione e anche per consentire allo strumento di leggere o scrivere eventuali file richiesti dal comando, ad esempio file audio di parlato.

In Windows digitare questo comando per creare una directory locale che può essere usata dall'interfaccia della riga di comando di Voce entro il contenitore:

`mkdir c:\spx-data`

In alternativa, in Linux o macOS digitare questo comando in un terminale per creare una directory e visualizzarne il percorso assoluto:

```bash
mkdir ~/spx-data
cd ~/spx-data
pwd
```

Il percorso assoluto verrà usato quando si chiama l'interfaccia della riga di comando di Voce.

### <a name="run-speech-cli-in-the-container"></a>Eseguire l'interfaccia della riga di comando di Voce nel contenitore

Questa documentazione mostra il comando `spx` dell'interfaccia della riga di comando di Voce usato nelle installazioni non Docker.
Quando si chiama il comando `spx` in un contenitore Docker, è necessario montare una directory nel contenitore nel file system in cui l'interfaccia della riga di comando di Voce può archiviare e trovare valori di configurazione e leggere e scrivere file.

In Windows i comandi inizieranno come segue:

```shell
docker run -it -v c:\spx-data:/data --rm msftspeech/spx
```

In Linux o macOS i comandi inizieranno in modo analogo al seguente:
```shell   
sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx
```

> [!NOTE]
> Sostituire `/ABSOLUTE_PATH` con i percorsi assoluti mostrati dal comando `pwd` nella sezione precedente.

Per usare il comando `spx` installato in un contenitore, immettere sempre il comando completo mostrato sopra, seguito dai parametri della richiesta specifica.
Ad esempio, in Windows questo comando configura la chiave:

```shell
docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY
```

> [!WARNING]
> Non è possibile usare il microfono del computer quando si esegue l'interfaccia della riga di comando di Voce in un contenitore Docker. Tuttavia è possibile leggere e salvare i file audio nella directory montata locale. 

### <a name="optional-create-a-command-line-shortcut"></a>Facoltativo: Creare un collegamento alla riga di comando

Se l'interfaccia della riga di comando di Voce viene eseguita da un contenitore Docker in Linux o macOS, è possibile creare un collegamento. 

Seguire queste istruzioni:
1. Aprire `.bash_profile` nell'editor di testo preferito. Ad esempio:
   ```shell
   nano ~/.bash_profile
   ```
2. Aggiungere quindi questa funzione a `.bash_profile`. Assicurarsi di aggiornare questa funzione con il percorso corretto della directory montata:
   ```shell   
   spx(){
       sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx
   }
   ```
3. Definire l'origine del profilo:
   ```shell
   source ~/.bash_profile
   ```
4. Ora invece di eseguire `sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx`, è possibile digitare semplicemente `spx` seguito dagli argomenti. Ad esempio: 
   ```shell
   // Get some help
   spx help recognize

   // Recognize speech from an audio file 
   spx recognize --file /mounted/directory/file.wav
   ```

> [!WARNING]
> Se si cambia la directory montata a cui Docker fa riferimento, è necessario aggiornare la funzione in `.bash_profile`.

***

## <a name="create-subscription-config"></a>Creare la configurazione della sottoscrizione

Per iniziare a usare l'interfaccia della riga di comando di Voce, è necessario immettere la chiave della sottoscrizione e l'identificatore di area del servizio Voce. Per ottenere queste credenziali, seguire la procedura descritta in [Provare il servizio Voce gratuitamente](../overview.md#try-the-speech-service-for-free).
Dopo aver ottenuto l’identificatore dell’area e della chiave di sottoscrizione (ad esempio, `eastus`, `westus`), eseguire i comandi seguenti.

```shell
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

L'autenticazione della sottoscrizione è ora archiviata per richieste SPX future. Se è necessario rimuovere uno di questi valori archiviati, eseguire `spx config @region --clear` o `spx config @key --clear`.
