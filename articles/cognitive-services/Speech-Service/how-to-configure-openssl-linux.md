---
title: Come configurare OpenSSL per Linux
titleSuffix: Azure Cognitive Services
description: Informazioni su come configurare OpenSSL per Linux.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a6225fec30a87ca0bbe57e414733bc21489f87ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577445"
---
# <a name="configure-openssl-for-linux"></a>Configurare OpenSSL per Linux

Quando si usa una versione di SDK vocale prima di 1.9.0, [openssl](https://www.openssl.org) viene configurato dinamicamente per la versione del sistema host. Nelle versioni successive di Speech SDK, OpenSSL (versione [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) è collegato in modo statico alla libreria principale dell'SDK di riconoscimento vocale.

Per garantire la connettività, verificare che nel sistema siano stati installati i certificati OpenSSL. Eseguire un comando:
```bash
openssl version -d
```

L'output nei sistemi basati su Ubuntu/Debian dovrebbe essere:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Controllare se è presente una `certs` sottodirectory in OPENSSLDIR. Nell'esempio precedente, sarebbe `/usr/lib/ssl/certs` .

* Se è presente `/usr/lib/ssl/certs` e contiene molti singoli file di certificato (con `.crt` `.pem` estensione o), non è necessario eseguire ulteriori azioni.

* Se il valore di OPENSSLDIR è diverso da `/usr/lib/ssl` e/o se è presente un singolo file di bundle del certificato anziché più file singoli, è necessario impostare una variabile di ambiente SSL appropriata per indicare la posizione in cui è possibile trovare i certificati.

## <a name="examples"></a>Esempio

- OPENSSLDIR è `/opt/ssl` . È presente una `certs` sottodirectory con `.crt` molti `.pem` file o.
Impostare la variabile `SSL_CERT_DIR` di ambiente in modo che punti a `/opt/ssl/certs` prima di eseguire un programma che usa l'SDK di riconoscimento vocale. Ad esempio:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR è `/etc/pki/tls` (come nei sistemi basati su RHEL/CentOS). È presente `certs` una sottodirectory con un file di bundle di certificati, ad esempio `ca-bundle.crt` .
Impostare la variabile `SSL_CERT_FILE` di ambiente in modo che punti a tale file prima di eseguire un programma che usa l'SDK di riconoscimento vocale. Ad esempio:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="certificate-revocation-checks"></a>Controlli di revoca dei certificati
Quando ci si connette al servizio riconoscimento vocale, l'SDK vocale verificherà che il certificato TLS utilizzato dal servizio di riconoscimento vocale non sia stato revocato. Per eseguire questa verifica, l'SDK del riconoscimento vocale dovrà accedere ai punti di distribuzione CRL per le autorità di certificazione usate da Azure. In [questo documento](https://docs.microsoft.com/azure/security/fundamentals/tls-certificate-changes)è disponibile un elenco di possibili percorsi di download CRL. Se un certificato è stato revocato o non è possibile scaricare il CRL, l'SDK vocale interrompe la connessione e genera l'evento annullato.

Nel caso in cui la rete in cui viene usato l'SDK di riconoscimento vocale venga configurata in modo che non consenta l'accesso ai percorsi di download CRL, il controllo CRL può essere disabilitato o impostato in modo da non riuscire se non è possibile recuperare il CRL. Questa configurazione viene eseguita tramite l'oggetto di configurazione utilizzato per creare un oggetto Recognizer.

Per continuare con la connessione quando non è possibile recuperare un CRL, impostare la proprietà OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE.

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE"];
```

::: zone-end
Se impostato su "true", verrà effettuato un tentativo di recuperare l'elenco di revoche di certificati e, se il recupero ha esito positivo, verrà verificata la revoca del certificato. se il recupero ha esito negativo, la connessione potrà continuare.

Per disabilitare completamente i controlli di revoca dei certificati, impostare la proprietà OPENSSL_DISABLE_CRL_CHECK su "true".
::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_DISABLE_CRL_CHECK", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_DISABLE_CRL_CHECK"];
```

::: zone-end


> [!NOTE]
> È anche importante notare che alcune distribuzioni di Linux non hanno una variabile di ambiente TMP o TMPDIR definita. In questo modo l'SDK per la sintesi vocale Scarica l'elenco di revoche di certificati (CRL) ogni volta, anziché memorizzare il CRL su disco per riutilizzarlo fino alla scadenza. Per migliorare le prestazioni iniziali della connessione, è possibile [creare una variabile di ambiente denominata TMPDIR e impostarla sul percorso della directory temporanea scelta.](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su Speech SDK](speech-sdk.md)
