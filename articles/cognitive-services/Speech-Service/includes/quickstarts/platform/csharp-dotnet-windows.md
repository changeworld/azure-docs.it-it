---
title: 'Guida introduttiva: Configurazione della piattaforma per .NET Framework (Windows) con Speech SDK - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Usare questa guida per configurare la piattaforma per C# in .NET Framework per Windows con il servizio Speech SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-dotnet
ms.openlocfilehash: 1a9faf24c5a82b815b40afe15769480b69074dc9
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551645"
---
Questa guida spiega come installare [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per .NET Framework (Windows). Se si vuole solo il nome del pacchetto per iniziare autonomamente, eseguire `Install-Package Microsoft.CognitiveServices.Speech` nella console NuGet.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* In Windows è necessaria la versione di [Microsoft Visual C++ Redistributable per Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) per la piattaforma in uso. La prima volta che si esegue questa installazione può essere necessario riavviare il sistema.
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Creare un progetto di Visual Studio e installare Speech SDK

Sarà necessario procedere all'installazione del [pacchetto NuGet Speech SDK](https://aka.ms/csspeech/nuget) per potervi fare riferimento nel proprio codice. A tale scopo, è prima di tutto necessario creare un progetto **HelloWorld**. Se si dispone già di un progetto con il carico di lavoro **.NET desktop development** disponibile, è possibile usare quel progetto e passare a [Usare NuGet Package Manager per installare Speech SDK](#use-nuget-package-manager-to-install-the-speech-sdk).

### <a name="create-helloworld-project"></a>Creazione di un progetto HelloWorld

1. Aprire Visual Studio 2019.

1. Nella finestra iniziale selezionare **Crea un nuovo progetto**. 

1. Nella finestra **Crea un nuovo progetto** scegliere **App console (.NET Framework)** e quindi fare clic su **Avanti**.

1. Nella finestra **Configura il nuovo progetto** immettere *helloworld* in **Nome progetto** scegliere o creare il percorso della directory in **Percorso** e quindi selezionare **Crea**.

1. Dalla barra dei menu di Visual Studio scegliere **strumenti**  >  **Ottieni strumenti e funzionalità**, che consente di aprire programma di installazione di Visual Studio e visualizzare la finestra di dialogo **modifica** .

1. Assicurarsi che il carico di lavoro di **Sviluppo per desktop .NET** sia disponibile. Se il carico di lavoro non è stato installato, selezionare la casella di controllo accanto ad esso e quindi fare clic su **Modifica** per avviare l'installazione. Il download e l'installazione può richiedere alcuni minuti.

   Se la casella di controllo accanto a **Sviluppo per desktop .NET** è già selezionata, fare clic su **Chiudi** per chiudere la finestra di dialogo.

   ![Abilitare lo sviluppo per desktop .NET](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Chiudere il Programma di installazione di Visual Studio.

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>Usare NuGet Package Manager per installare Speech SDK

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **helloworld** e quindi scegliere **Gestisci pacchetti NuGet** per visualizzare Gestione pacchetti NuGet.

   ![Gestione pacchetti NuGet](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. Nell'angolo in alto a destra individuare la casella a discesa **Origine dei pacchetti** e assicurarsi che sia selezionata l'opzione **`nuget.org`** .

1. Nell'angolo in alto a sinistra fare clic su **Sfoglia**.

1. Nella casella di ricerca digitare *Microsoft.CognitiveServices.Speech* e premere **INVIO**.

1. Nei risultati della ricerca selezionare il pacchetto **Microsoft.CognitiveServices.Speech** e quindi selezionare **Installa** per installare la versione stabile più recente.

   ![Installare il pacchetto NuGet Microsoft.CognitiveServices.Speech](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Accettare tutti i contratti e le licenze per avviare l'installazione.

   Dopo aver installato il pacchetto, viene visualizzato un messaggio di conferma nella finestra della **Console di Gestione pacchetti**.

### <a name="choose-target-architecture"></a>Scegliere l'architettura di destinazione

Per compilare ed eseguire l'applicazione console, creare una configurazione di piattaforma corrispondente all'architettura del computer.

1. Dalla barra dei menu selezionare **Compila**  >  **Configuration Manager**. Verrà visualizzata finestra di dialogo **Gestione configurazione**.

   ![Finestra di dialogo Gestione configurazione](~/articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Nella casella a discesa **Piattaforma soluzione attiva** selezionare **Nuova**. Verrà visualizzata la finestra di dialogo **Nuova piattaforma soluzione**.

1. Nella casella a discesa **Digitare o selezionare la nuova piattaforma**:
   - Se è in esecuzione Windows a 64 bit, selezionare **x64**.
   - Se è in esecuzione Windows a 32 bit, selezionare **x86**.

1. Fare clic su **OK** e quindi su **Chiudi**.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [windows](../quickstart-list.md)]
