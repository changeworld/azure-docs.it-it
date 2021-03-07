---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 4a4705647b90d29f47e37b88531f3432c6a2f448
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434561"
---
Quando si sviluppa per macOS, sono disponibili tre SDK per la sintesi vocale.

- Objective-C Speech SDK è disponibile in modalità nativa come pacchetto CocoaPod
- .NET Speech SDK può essere usato con **Novell. Mac** mentre implementa .NET standard 2,0
- Python Speech SDK è disponibile come modulo PyPI

> [!TIP]
> Per informazioni dettagliate sull'uso dell'SDK di riconoscimento vocale Objective-C con Swift, vedere <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">importazione di Objective-c in Swift </a>.

### <a name="system-requirements"></a>Requisiti di sistema

- MacOS versione 10,13 o successiva

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        Il pacchetto CocoaPod macOS è disponibile per il download e l'uso con il <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">9.4.1 di Xcode (o versione successiva) </a> Integrated Development Environment (IDE). Prima di tutto, <a href="https://aka.ms/csspeech/macosbinary" target="_blank">scaricare il file binario CocoaPod </a>. Estrarre il pod nella stessa directory per l'uso previsto, creare un *Podfile* ed elencare `pod` come `target` .
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.15.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.Mac espone la versione completa di macOS SDK per gli sviluppatori .NET per creare applicazioni Mac native con C#. Per altre informazioni, vedere <a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Novell. Mac </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>Risorse aggiuntive

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">Guida introduttiva a macOS Speech SDK (codice sorgente Objective-C) </a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">Guida introduttiva a macOS Speech SDK-codice sorgente Swift </a>