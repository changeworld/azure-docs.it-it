---
title: Usare .NET per creare una firma di accesso condiviso di delega utente per un contenitore o un BLOB
titleSuffix: Azure Storage
description: Informazioni su come creare una firma di accesso condiviso di delega utente con Azure Active Directory credenziali usando la libreria client .NET per archiviazione di Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/13/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 512b949fceda850e968a6f97b3788ae3a602f56d
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98199258"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net"></a>Creare una firma di accesso condiviso di delega utente per un contenitore o un BLOB con .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Questo articolo illustra come usare le credenziali Azure Active Directory (Azure AD) per creare una firma di accesso condiviso di delega utente per un contenitore o un BLOB con la libreria client di archiviazione di Azure per .NET versione 12.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-azure-roles-for-access-to-data"></a>Assegnare i ruoli di Azure per l'accesso ai dati

Quando un Azure AD entità di sicurezza tenta di accedere ai dati BLOB, l'entità di sicurezza deve avere le autorizzazioni per la risorsa. Se l'entità di sicurezza è un'identità gestita in Azure o un account utente Azure AD che esegue il codice nell'ambiente di sviluppo, all'entità di sicurezza deve essere assegnato un ruolo di Azure che concede l'accesso ai dati BLOB in archiviazione di Azure. Per informazioni sull'assegnazione delle autorizzazioni tramite il controllo degli accessi in base al ruolo di Azure, vedere la sezione **assegnare i ruoli di Azure per i diritti di accesso** in [autorizzare l'accesso a BLOB e code di Azure con Azure Active Directory](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Per altre informazioni su come eseguire l'autenticazione con la libreria client di identità di Azure da archiviazione di Azure, vedere la sezione **eseguire l'autenticazione con la libreria di identità di Azure** in [autorizzare l'accesso a BLOB e code con Azure Active Directory e identità gestite per le risorse di Azure](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).

## <a name="get-an-authenticated-token-credential"></a>Ottenere le credenziali di un token autenticato

Per ottenere una credenziale token che il codice può usare per autorizzare le richieste ad archiviazione di Azure, creare un'istanza della classe [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) .

Il frammento di codice seguente mostra come ottenere le credenziali del token autenticato e usarle per creare un client del servizio per l'archiviazione BLOB:

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Ottenere la chiave di delega utente

Ogni firma di accesso condiviso è firmata con una chiave. Per creare una firma di accesso condiviso di delega utente, è necessario prima richiedere una chiave di delega utente, che viene quindi usata per firmare la firma di accesso condiviso. La chiave di delega utente è analoga alla chiave dell'account usata per firmare una firma di accesso condiviso del servizio o una firma di accesso condiviso dell'account, con la differenza che si basa sulle credenziali Azure AD. Quando un client richiede una chiave di delega utente usando un token OAuth 2,0, archiviazione di Azure restituisce la chiave di delega utente per conto dell'utente.

Una volta creata la chiave di delega utente, è possibile usare tale chiave per creare un numero qualsiasi di firme di accesso condiviso per la delega utente, per tutta la durata della chiave. La chiave di delega utente è indipendente dal token OAuth 2,0 usato per acquisirla, pertanto non è necessario rinnovare il token finché la chiave è ancora valida. È possibile specificare che la chiave sia valida per un periodo di un massimo di 7 giorni.

Per richiedere la chiave di delega utente, usare uno dei metodi seguenti:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

Il seguente frammento di codice ottiene la chiave di delega dell'utente e ne scrive le proprietà:

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="get-a-user-delegation-sas-for-a-blob"></a>Ottenere una firma di accesso condiviso di delega utente per un BLOB

L'esempio di codice seguente illustra il codice completo per l'autenticazione dell'entità di sicurezza e la creazione della firma di accesso condiviso della delega utente per un BLOB:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasBlob":::

Nell'esempio seguente viene verificata la firma di accesso condiviso della delega utente creata nell'esempio precedente da un'applicazione client simulata. Se la firma di accesso condiviso è valida, l'applicazione client è in grado di leggere il contenuto del BLOB. Se la firma di accesso condiviso non è valida, ad esempio se è scaduta, archiviazione di Azure restituisce il codice di errore 403 (accesso negato).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ReadBlobWithSasAsync":::

## <a name="get-a-user-delegation-sas-for-a-container"></a>Ottenere una firma di accesso condiviso di delega utente per un contenitore

Nell'esempio di codice seguente viene illustrato come generare una firma di accesso condiviso di delega utente per un contenitore:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasContainer":::

Nell'esempio seguente viene verificata la firma di accesso condiviso della delega utente creata nell'esempio precedente da un'applicazione client simulata. Se la firma di accesso condiviso è valida, l'applicazione client è in grado di leggere il contenuto del BLOB. Se la firma di accesso condiviso non è valida, ad esempio se è scaduta, archiviazione di Azure restituisce il codice di errore 403 (accesso negato).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ListBlobsWithSasAsync":::

## <a name="get-a-user-delegation-sas-for-a-directory"></a>Ottenere una firma di accesso condiviso di delega utente per una directory

Nell'esempio di codice seguente viene illustrato come generare una firma di accesso condiviso di delega utente per una directory quando è abilitato uno spazio dei nomi gerarchico:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasDirectory":::

Nell'esempio seguente viene verificata la firma di accesso condiviso della delega utente creata nell'esempio precedente da un'applicazione client simulata. Se la firma di accesso condiviso è valida, l'applicazione client è in grado di elencare i percorsi dei file per questa directory. Se la firma di accesso condiviso non è valida, ad esempio se è scaduta, archiviazione di Azure restituisce il codice di errore 403 (accesso negato).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ListFilePathsWithDirectorySasAsync":::

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Vedi anche

- [Concedere accesso limitato alle risorse di archiviazione di Azure tramite firme di accesso condiviso (SAS)](../common/storage-sas-overview.md)
- [Operazione di ottenimento della chiave di delega utente](/rest/api/storageservices/get-user-delegation-key)
- [Creare una firma di accesso condiviso per la delega utente (API REST)](/rest/api/storageservices/create-user-delegation-sas)
