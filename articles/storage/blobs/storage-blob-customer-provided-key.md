---
title: Specificare una chiave fornita dal cliente per una richiesta all'archivio BLOB con .NET-archiviazione di Azure
description: Informazioni su come specificare una chiave fornita dal cliente per una richiesta di archiviazione BLOB con .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: c3096da8b3c83dbfe8cfdd6a5fa4d177241334de
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97693505"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Specificare una chiave fornita dal cliente per una richiesta all'archivio BLOB con .NET

I client che effettuano richieste nell'archivio BLOB di Azure hanno la possibilità di fornire una chiave di crittografia AES-256 per una singola richiesta. L'inclusione della chiave di crittografia nella richiesta offre un controllo granulare sulle impostazioni di crittografia per le operazioni di archiviazione BLOB. Le chiavi fornite dal cliente possono essere archiviate in Azure Key Vault o in un altro archivio chiavi.

Questo articolo illustra come specificare una chiave fornita dal cliente per una richiesta con .NET.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Per altre informazioni su come eseguire l'autenticazione con la libreria client di identità di Azure, vedere [libreria client di identità di Azure per .NET](/dotnet/api/overview/azure/identity-readme).

## <a name="use-a-customer-provided-key-to-write-to-a-blob"></a>Usare una chiave fornita dal cliente per scrivere in un BLOB

Nell'esempio seguente viene fornita una chiave AES-256 durante il caricamento di un BLOB con la libreria client V12 per l'archiviazione BLOB. Nell'esempio viene usato l'oggetto [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) per autorizzare la richiesta di scrittura con Azure ad, ma è anche possibile autorizzare la richiesta con credenziali della chiave condivisa.

```csharp
async static Task UploadBlobWithClientKey(Uri blobUri,
                                          Stream data,
                                          byte[] key,
                                          string keySha256)
{
    // Create a new customer-provided key.
    // Key must be AES-256.
    var cpk = new CustomerProvidedKey(key);

    // Check the key's encryption hash.
    if (cpk.EncryptionKeyHash != keySha256)
    {
        throw new InvalidOperationException("The encryption key is corrupted.");
    }

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = cpk
    };

    // Create the client object with options specified.
    BlobClient blobClient = new BlobClient(
        blobUri,
        new DefaultAzureCredential(),
        options);

    // If the container may not exist yet,
    // create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient =
        blobClient.GetParentBlobContainerClient();

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

- [Fornire una chiave di crittografia per una richiesta all'archiviazione BLOB](encryption-customer-provided-keys.md)
- [Crittografia del servizio di archiviazione di Azure per dati inattivi](../common/storage-service-encryption.md)
