---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: trbye
ms.openlocfilehash: cb2aac1b068c92752a7924ada7b0a2eadbc91eef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612371"
---
Questa guida spiega come installare [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per Python. Se si vuole solo il nome del pacchetto per iniziare autonomamente, eseguire `pip install azure-cognitiveservices-speech`.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

- Il pacchetto Speech SDK per Python è disponibile per i sistemi operativi seguenti:
  - Windows: x64 e x86
  - Mac: macOS X versione 10.12 o successiva
  - Linux: vedere l'elenco di [distribuzioni Linux e architetture di destinazione supportate](~/articles/cognitive-services/speech-service/speech-sdk.md).

## <a name="prerequisites"></a>Prerequisiti

- In Windows è necessaria la versione di [Microsoft Visual C++ Redistributable per Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) per la piattaforma in uso. La prima volta che si esegue questa installazione può essere necessario riavviare il sistema.

- Le piattaforme Linux supportate richiedono l'installazione di alcune librerie (`libssl` per il supporto di Secure Sockets Layer e `libasound2` per il supporto audio). Per i comandi necessari per installare le versioni corrette di queste librerie, identificare la propria distribuzione di seguito.

  - In Ubuntu/Debian eseguire i comandi seguenti per installare i pacchetti necessari:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

    Se libssl1.0.0 non è disponibile, installare libssl1.0.x (dove x è maggiore di 0) o libssl1.1.

  - In RHEL/CentOS eseguire i comandi seguenti per installare i pacchetti necessari:

    ```sh
    sudo yum update
    sudo yum install alsa-lib openssl python3
    ```

> [!NOTE]
> - In RHEL/CentOS 7 seguire le istruzioni riportate in [Come configurare RHEL/CentOS 7 per Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - In RHEL/CentOS 8 seguire le istruzioni riportate in [Come configurare OpenSSL per Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- In Windows è necessaria la versione di [Microsoft Visual C++ Redistributable per Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) per la piattaforma in uso. La prima volta che si esegue questa installazione può essere necessario riavviare Windows prima di continuare con questa guida.
- Infine, sarà necessario [Python 3,6 a 3,9](https://www.python.org/downloads/). Per controllare l'installazione, aprire un prompt dei comandi e digitare il comando `python --version`, quindi controllare il risultato. Se è installato correttamente, si otterrà una risposta "Python 3.6.1" o simile.

## <a name="install-the-speech-sdk-from-pypi"></a>Installare Speech SDK da PyPI

Se si usano un ambiente o strumenti di compilazione personalizzati, eseguire il comando seguente per installare Speech SDK da [PyPI](https://pypi.org/). Gli utenti di Visual Studio Code possono procedere con la sottosezione successiva per l'installazione guidata.

```sh
pip install azure-cognitiveservices-speech
```

Se si usa macOS, può essere necessario eseguire il comando seguente per il corretto funzionamento del comando `pip` precedente:

```sh
python3 -m pip install --upgrade pip
```

Dopo aver usato correttamente `pip` per installare `azure-cognitiveservices-speech`, è possibile usare Speech SDK importando lo spazio dei nomi nei progetti Python.

```py
import azure.cognitiveservices.speech as speechsdk
```

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>Installare Speech SDK usando Visual Studio Code

1. Scaricare e installare la versione supportata più recente di [Python](https://www.python.org/downloads/) per la piattaforma, da 3,6 a 3,9.
   - Gli utenti di Windows dovranno assicurarsi di selezionare l'opzione per aggiungere Python a PATH durante il processo di installazione.
1. Scaricare e installare [Visual Studio Code](https://code.visualstudio.com/Download).
1. Aprire Visual Studio Code e installare l'estensione Python. Selezionare **File** > **Preferenze** > **Estensioni** dal menu. Cercare **Python** e fare clic su **Installa**.

   ![Installare l'estensione Python](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. Sempre in Visual Studio Code, installare il pacchetto Speech SDK per Python dalla riga di comando integrata:
   1. Aprire un terminale scegliendo **Terminale** > **Nuovo terminale** dai menu a discesa.
   1. Nel terminale visualizzato immettere il comando `python -m pip install azure-cognitiveservices-speech`

Se non si ha familiarità con Visual Studio Code, vedere la [Documentazione di Visual Studio Code](https://code.visualstudio.com/docs). Per altre informazioni su Visual Studio Code e Python, vedere l'[esercitazione su Visual Studio Code e Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="support-and-updates"></a>Supporto e aggiornamenti

Gli aggiornamenti del pacchetto Speech SDK per Python sono distribuiti tramite PyPI e annunciati nelle [Note sulla versione](~/articles/cognitive-services/speech-service/releasenotes.md).
Se è disponibile una nuova versione, è possibile eseguire l'aggiornamento ad essa con il comando `pip install --upgrade azure-cognitiveservices-speech`.
Controllare la versione attualmente installata esaminando la variabile `azure.cognitiveservices.speech.__version__`.

Se si verifica un problema o manca una funzionalità, vedere le [opzioni di supporto e assistenza](../../../../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext).

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [windows](../quickstart-list.md)]