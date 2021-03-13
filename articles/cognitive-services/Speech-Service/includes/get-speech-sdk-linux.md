---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 2466b9b9b812ae9fb590a6678d840661c74f47bd
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2021
ms.locfileid: "103439235"
---
:::row:::
    :::column span="3":::
        Speech SDK supporta solo **Ubuntu 16.04/18.04/20.04**, **Debian 9/10**, **Red Hat Enterprise Linux (RHEL) 7/8** e **CentOS 7/8** sulle seguenti architetture di destinazione se usate con Linux:
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

- x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) e ARM64 (Debian/Ubuntu) per lo sviluppo in C++
- x64, ARM32 (Debian/Ubuntu) e ARM64 (Debian/Ubuntu) per Java
- x64, ARM32 (Debian/Ubuntu) e ARM64 (Debian/Ubuntu) per .NET Core
- x64 per Python

> [!IMPORTANT]
> Per C# in Linux ARM64, è necessario .NET Core 3. x (pacchetto dotnet-SDK-3. x).

> [!NOTE]
> Per usare l'SDK di riconoscimento vocale in Alpine Linux, creare un ambiente di chroot Debian come documentato in the Alpine Linux wiki su [https://wiki.alpinelinux.org/wiki/Running_glibc_programs](running glibc programs) e quindi seguire le istruzioni Debian qui.

### <a name="system-requirements"></a>Requisiti di sistema

Per un'applicazione nativa, l'SDK di riconoscimento vocale si basa su `libMicrosoft.CognitiveServices.Speech.core.so` . Verificare che l'architettura di destinazione (x86, x64) corrisponda all'applicazione. A seconda della versione di Linux, potrebbero essere necessarie dipendenze aggiuntive.

- Le raccolte condivise della Libreria GNU C (inclusa la libreria di programmazione thread POSIX, `libpthreads`)
- Libreria OpenSSL ( `libssl.so.1.0.0` o `libssl.so.1.0.2` )
- La libreria condivisa per le applicazioni ALSA (`libasound.so.2`)

# <a name="ubuntu-160418042004"></a>[Ubuntu 16.04/18.04/20.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

> [!NOTE]
> Se libssl 1.0. x non è disponibile, installare invece libssl 1.1.

# <a name="debian-910"></a>[Debian 9/10](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

> [!NOTE]
> Se libssl 1.0. x non è disponibile, installare invece libssl 1.1.

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 e CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> - In RHEL/CentOS 7 seguire le istruzioni riportate in [Come configurare RHEL/CentOS 7 per Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - In RHEL/CentOS 8 seguire le istruzioni riportate in [Come configurare OpenSSL per Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
