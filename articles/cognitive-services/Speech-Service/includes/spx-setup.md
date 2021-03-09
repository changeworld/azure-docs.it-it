---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: c939c1d061f4e01a00bc2b886056e42f0b059ad5
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102511046"
---
## <a name="download-and-install"></a>Download e installazione

#### <a name="windows-install"></a>[Installazione di Windows](#tab/windowsinstall)

Per installare l’interfaccia della riga di comando di Voce su Windows, seguire questa procedura:

1. In Windows è necessaria la versione di [Microsoft Visual C++ Redistributable per Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) per la piattaforma in uso. La prima volta che si esegue questa installazione può essere necessario riavviare il sistema.
1. Installare [.NET Core 3,1 SDK](/dotnet/core/install/windows).
2. Installare l'interfaccia della riga di comando vocale usando NuGet immettendo questo comando:

   ```console
   dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI --version 1.15.0
   ```
Digitare `spx` per visualizzare la guida per l'interfaccia della riga di comando di Voce.

> [!NOTE]
> In alternativa a NuGet, è possibile scaricare ed estrarre l' [archivio zip](https://aka.ms/speech/spx-zips.zip)dell'interfaccia della riga di comando vocale, trovare ed estrarre la piattaforma dalla `spx-zips` Directory e aggiungere il `spx` percorso alla variabile del **percorso** di sistema.


### <a name="font-limitations"></a>Limitazioni dei tipi di carattere

In Windows, l'interfaccia della riga di comando di Voce può visualizzare solo i tipi di carattere disponibili per il prompt dei comandi nel computer locale.
Il [terminale Windows](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) supporta tutti i tipi di carattere prodotti in modo interattivo dall'interfaccia della riga di comando di Voce.

Se l'output viene restituito in un file, è possibile che anche un editor di testo come Blocco note o un Web browser come Microsoft Edge visualizzi tutti i tipi di carattere.

#### <a name="linux-install"></a>[Installazione di Linux](#tab/linuxinstall)

Le distribuzioni di Linux seguenti sono supportate per le architetture x64 che usano l'interfaccia della riga di comando vocale:

* CentOS 7/8
* Debian 9/10 
* Red Hat Enterprise Linux (RHEL) 7/8
* Ubuntu 16.04/18.04/20.04

> [!NOTE]
> Le architetture aggiuntive sono supportate dall'SDK di riconoscimento vocale (non l'interfaccia della riga di comando vocale). Per ulteriori informazioni, vedere [informazioni sull'SDK di riconoscimento vocale](../speech-sdk.md).

Per installare l’interfaccia della riga di comando di Voce su Linux su una CPU x64, seguire questa procedura:

1. Installare [.NET Core 3,1 SDK](/dotnet/core/install/linux).
2. Installare l'interfaccia della riga di comando vocale usando NuGet immettendo questo comando:

    `dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI --version 1.15.0`

Digitare `spx` per visualizzare la guida per l'interfaccia della riga di comando di Voce.

> [!NOTE]
> In alternativa a NuGet, è possibile scaricare i file binari nell' [archivio zip](https://aka.ms/speech/spx-zips.zip), estrarre `spx-netcore-30-linux-x64.zip` in una nuova `~/spx` Directory, digitare `sudo chmod +r+x spx` sul file binario e aggiungere il `~/spx` percorso della variabile di sistema Path.


#### <a name="docker-install-windows-linux-macos"></a>[Installazione in Docker (Windows, Linux, macOS)](#tab/dockerinstall)

Seguire questa procedura per installare l'interfaccia della riga di comando di Voce in un contenitore Docker:

1. <a href="https://www.docker.com/get-started" target="_blank">Installare Docker Desktop</a> per la piattaforma se non è già stato fatto.
2. In un nuovo prompt dei comandi o terminale digitare questo comando: 
   ```console   
   docker pull msftspeech/spx
   ```
3. Digitare il comando seguente. Dovrebbero essere visualizzate informazioni della Guida per l'interfaccia della riga di comando di Voce: 
   ```console 
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

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx
```

In Linux o macOS i comandi saranno simili all'esempio seguente. Sostituire `ABSOLUTE_PATH` con il percorso assoluto della directory montata. Questo percorso è stato restituito dal comando `pwd` nella sezione precedente. 

Se si esegue questo comando prima di impostare la chiave e l'area, si riceverà un messaggio di errore indicante che è necessario eseguire queste impostazioni:
```console   
sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
```

Per usare il comando `spx` installato in un contenitore, immettere sempre il comando completo mostrato sopra, seguito dai parametri della richiesta specifica.
Ad esempio, in Windows questo comando configura la chiave:

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx config --set @key SUBSCRIPTION-KEY
```

Per un'interazione più estesa con lo strumento da riga di comando, è possibile avviare un contenitore con una shell bash interattiva aggiungendo un parametro entrypoint.
In Windows, immettere questo comando per avviare un contenitore che espone un'interfaccia della riga di comando interattiva in cui è possibile immettere più comandi `spx`:
```console
docker run -it --entrypoint=/bin/bash -v c:\spx-data:/data --rm msftspeech/spx
```

> [!WARNING]
> Non è possibile usare il microfono del computer quando si esegue l'interfaccia della riga di comando di Voce in un contenitore Docker. Tuttavia è possibile leggere e salvare i file audio nella directory montata locale. 

<!-- Need to troubleshoot issues with docker pull image

### Optional: Create a command line shortcut

If you're running the the Speech CLI from a Docker container on Linux or macOS you can create a shortcut. 

Follow these instructions to create a shortcut:
1. Open `.bash_profile` with your favorite text editor. For example:
   ```shell
   nano ~/.bash_profile
   ```
2. Next, add this function to your `.bash_profile`. Make sure you update this function with the correct path to your mounted directory:
   ```shell   
   spx(){
       sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
   }
   ```
3. Source your profile:
   ```shell
   source ~/.bash_profile
   ```
4. Now instead of running `sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx`, you can just type `spx` followed by arguments. For example: 
   ```shell
   // Get some help
   spx help recognize

   // Recognize speech from an audio file 
   spx recognize --file /mounted/directory/file.wav
   ```

> [!WARNING]
> If you change the mounted directory that Docker is referencing, you need to update the function in `.bash_profile`.
--->
***

## <a name="create-subscription-config"></a>Creare la configurazione della sottoscrizione

Per iniziare a usare l'interfaccia della riga di comando di Voce, è necessario immettere la chiave della sottoscrizione e l'identificatore di area del servizio Voce. Per ottenere queste credenziali, seguire la procedura descritta in [Provare il servizio Voce gratuitamente](../overview.md#try-the-speech-service-for-free).
Dopo aver ottenuto l’identificatore dell’area e della chiave di sottoscrizione (ad esempio, `eastus`, `westus`), eseguire i comandi seguenti.

```console
spx config --set @key SUBSCRIPTION-KEY
spx config --set @region REGION
```

L'autenticazione della sottoscrizione è ora archiviata per richieste SPX future. Se è necessario rimuovere uno di questi valori archiviati, eseguire `spx config @region --clear` o `spx config @key --clear`.
