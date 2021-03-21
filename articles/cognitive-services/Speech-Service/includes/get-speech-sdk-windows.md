---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: fc4cc5063f72ff3f0db62cde79f7908add86166e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434583"
---
:::row:::
    :::column span="3":::
        Speech SDK supporta Windows 10 e Windows Server 2016 o versioni successive. Le versioni precedenti **non** sono ufficialmente supportate. È possibile usare parti dell'SDK di riconoscimento vocale con le versioni precedenti di Windows, anche se non sono consigliate.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>Requisiti di sistema

Per Speech SDK in Windows è necessario il <a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">Microsoft Visual C++ Redistributable per Visual Studio 2019 </a> nel sistema.

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">Installare per x86 </a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank">Installare per x64 </a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">Installare per ARMx64 </a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

Per l'input del microfono, è necessario installare le raccolte Media Foundation. Queste raccolte sono parte di Windows 10 e Windows Server 2016. È possibile utilizzare Speech SDK senza queste raccolte finché non viene utilizzato il microfono come dispositivo di input audio.

I file Speech SDK richiesti possono essere distribuiti nella stessa directory dell'applicazione. In questo modo l'applicazione può accedere direttamente alle raccolte. Assicurarsi di selezionare la versione corretta (x86/x64) corrispondente all'applicazione.

| Nome                                            | Funzione                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | SDK di base, necessario per la distribuzione nativa e gestita |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Obbligatorio per le distribuzioni gestite                      |

> [!NOTE]
> A partire dalla versione 1.3.0, il file `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (fornito nelle versioni precedenti) non è più necessario. La funzionalità è ora integrata in Core SDK.

> [!IMPORTANT]
> Per il progetto C# Windows Form App (.NET Framework), verificare che le librerie siano incluse nelle impostazioni di distribuzione del progetto. È possibile selezionare questa opzione in `Properties -> Publish Section` . Fai clic sul `Application Files` pulsante e trova le librerie corrispondenti dall'elenco di scorrimento verso il basso. Verificare che il valore sia impostato su `Included` . In Visual Studio il file verrà incluso quando il progetto viene pubblicato/distribuito.

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
