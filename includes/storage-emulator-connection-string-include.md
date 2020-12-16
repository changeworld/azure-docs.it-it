---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 37fba0101365e425110c2943264c8c0e8c511329
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97582697"
---
L'emulatore supporta un singolo account fisso e una chiave di autenticazione nota per l'autenticazione con chiave condivisa. Questo account e questa chiave sono le uniche credenziali chiave condivise consentite per l'uso con l'emulatore. Ad esempio:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> La chiave di autenticazione supportata dall'emulatore è destinata solo a testare la funzionalità del codice di autenticazione client. Non viene utilizzata per eventuali scopi di sicurezza. Non è possibile usare l'account di archiviazione di produzione e la chiave con l'emulatore. Non usare l'account di sviluppo con dati di produzione.
> 
> L'emulatore supporta solo la connessione tramite HTTP. HTTPS è tuttavia il protocollo consigliato per accedere alle risorse in un account di archiviazione di Azure di produzione.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Connettersi all'account dell'emulatore utilizzando un collegamento
Il modo più semplice per connettersi all'emulatore dall'applicazione consiste nel configurare una stringa di connessione nel file di configurazione dell'applicazione che fa riferimento al collegamento `UseDevelopmentStorage=true` . Di seguito è riportato un esempio di stringa di connessione all'emulatore in un file di *app.config* : 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

Equivale a specificare completamente il nome dell'account, la chiave dell'account e gli endpoint per ciascuno dei servizi dell'emulatore che si desidera utilizzare nella stringa di connessione. Ciò è necessario per fare in modo che la stringa di connessione faccia riferimento agli endpoint dell’emulatore, che variano rispetto a quelli per un account di archiviazione di produzione. Ad esempio, il valore della stringa di connessione risulterà simile al seguente:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```
