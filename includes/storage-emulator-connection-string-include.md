---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/28/2020
ms.author: tamram
ms.openlocfilehash: a9d7f4f77d91abc88ea348e71a3d9c471b26a273
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622078"
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

#### <a name="connect-to-the-emulator-account-using-the-shortcut"></a>Connettersi all'account dell'emulatore usando il collegamento

Il modo più semplice per connettersi all'emulatore dall'applicazione consiste nel configurare una stringa di connessione nel file di configurazione dell'applicazione che fa riferimento al collegamento `UseDevelopmentStorage=true` . Il collegamento è equivalente alla stringa di connessione completa per l'emulatore, che specifica il nome dell'account, la chiave dell'account e gli endpoint dell'emulatore per ognuno dei servizi di archiviazione di Azure:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
```

Il frammento di codice .NET seguente mostra come è possibile usare il collegamento da un metodo che accetta una stringa di connessione. Ad esempio, il costruttore [BlobContainerClient (String, String)](/dotnet/api/azure.storage.blobs.blobcontainerclient.-ctor#Azure_Storage_Blobs_BlobContainerClient__ctor_System_String_System_String_) accetta una stringa di connessione.

```csharp
BlobContainerClient blobContainerClient = new BlobContainerClient("UseDevelopmentStorage=true", "sample-container");
blobContainerClient.CreateIfNotExists();
```

Assicurarsi che l'emulatore sia in esecuzione prima di chiamare il codice nel frammento.
