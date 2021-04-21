---
title: Usare l'interfaccia della riga di comando di Azure per creare una firma di accesso condiviso di delega utente per un contenitore o un BLOB
titleSuffix: Azure Storage
description: Informazioni su come creare una firma di accesso condiviso di delega utente con credenziali Azure Active Directory usando l'interfaccia della riga di comando di Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9ec434b9b6da3b3b80a3afddbb432ddeece2b389
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788548"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli"></a>Creare una firma di accesso condiviso di delega utente per un contenitore o un BLOB con l'interfaccia della riga di comando di Azure

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Questo articolo illustra come usare le credenziali Azure Active Directory (Azure AD) per creare una firma di accesso condiviso di delega utente per un contenitore o un BLOB con l'interfaccia della riga di comando di Azure.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>Installare la versione più recente dell'interfaccia della riga di comando di Azure

Per usare l'interfaccia della riga di comando di Azure per proteggere una firma di accesso condiviso con credenziali Azure AD, assicurarsi prima di tutto di aver installato la versione più recente dell'interfaccia della riga di comando di Azure. Per altre informazioni sull'installazione dell'interfaccia della riga di comando di Azure, vedere [Installare l'interfaccia della riga di comando di Azure.](/cli/azure/install-azure-cli)

Per creare una firma di accesso condiviso di delega utente usando l'interfaccia della riga di comando di Azure, assicurarsi di aver installato la versione 2.0.78 o successiva. Per controllare la versione installata, usare il `az --version` comando .

## <a name="sign-in-with-azure-ad-credentials"></a>Accedere con Azure AD credenziali

Accedere all'interfaccia della riga di comando di Azure con Azure AD credenziali. Per altre informazioni, vedere [Accedere tramite l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli).

## <a name="assign-permissions-with-azure-rbac"></a>Assegnare autorizzazioni con il controllo degli accessi in base al ruolo di Azure

Per creare una firma di accesso condiviso di delega utente da Azure PowerShell, all'account Azure AD usato per accedere all'interfaccia della riga di comando di Azure deve essere assegnato un ruolo che includa l'azione **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** Questa autorizzazione consente all'account Azure AD di richiedere la *chiave di delega utente*. La chiave di delega utente viene usata per firmare la firma di accesso condiviso di delega utente. Il ruolo che fornisce l'azione **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** deve essere assegnato a livello dell'account di archiviazione, del gruppo di risorse o della sottoscrizione.

Se non si dispone di autorizzazioni sufficienti per assegnare ruoli di Azure a un'entità di sicurezza Azure AD, potrebbe essere necessario chiedere al proprietario o all'amministratore dell'account di assegnare le autorizzazioni necessarie.

L'esempio seguente assegna il ruolo **Collaboratore** ai dati dei BLOB di archiviazione, che include l'azione **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** L'ambito del ruolo è a livello dell'account di archiviazione.

È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Per altre informazioni sui ruoli predefiniti che includono l'azione **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey,** vedere Ruoli predefiniti [di Azure.](../../role-based-access-control/built-in-roles.md)

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Usare Azure AD credenziali per proteggere una firma di accesso condiviso

Quando si crea una firma di accesso condiviso di delega utente con l'interfaccia della riga di comando di Azure, la chiave di delega utente usata per firmare la firma di accesso condiviso viene creata automaticamente in modo implicito. L'ora di inizio e l'ora di scadenza specificate per la firma di accesso condiviso vengono usate anche come ora di inizio e di scadenza per la chiave di delega utente.

Poiché l'intervallo massimo per cui la chiave di delega utente è valida è di 7 giorni dalla data di inizio, è necessario specificare un'ora di scadenza per la firma di accesso condiviso entro 7 giorni dall'ora di inizio. La firma di accesso condiviso non è valida dopo la scadenza della chiave di delega utente, quindi una firma di accesso condiviso con una scadenza superiore a 7 giorni sarà comunque valida solo per 7 giorni.

Quando si crea una firma di accesso condiviso di delega utente, `--auth-mode login` e `--as-user parameters` sono obbligatori. Specificare *login* per il parametro in modo che le richieste effettuate Archiviazione di Azure siano autorizzate `--auth-mode` con le Azure AD credenziali. Specificare il parametro per indicare che la firma di accesso condiviso restituita deve essere una firma di accesso condiviso `--as-user` di delega utente.

### <a name="create-a-user-delegation-sas-for-a-container"></a>Creare una firma di accesso condiviso di delega utente per un contenitore

Per creare una firma di accesso condiviso di delega utente per un contenitore con l'interfaccia della riga di comando di Azure, chiamare il [comando az storage container generate-sas.](/cli/azure/storage/container#az_storage_container_generate_sas)

Le autorizzazioni supportate per una firma di accesso condiviso di delega utente in un contenitore includono Add, Create, Delete, List, Read e Write. Le autorizzazioni possono essere specificate in modo singly o combinato. Per altre informazioni su queste autorizzazioni, vedere Creare una firma di accesso condiviso [di delega utente.](/rest/api/storageservices/create-user-delegation-sas)

Nell'esempio seguente viene restituito un token di firma di accesso condiviso di delega utente per un contenitore. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

Il token di firma di accesso condiviso di delega utente restituito sarà simile al seguente:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Creare una firma di accesso condiviso di delega utente per un BLOB

Per creare una firma di accesso condiviso di delega utente per un BLOB con l'interfaccia della riga di comando di Azure, chiamare il [comando az storage blob generate-sas.](/cli/azure/storage/blob#az_storage_blob_generate_sas)

Le autorizzazioni supportate per una firma di accesso condiviso di delega utente in un BLOB includono Add, Create, Delete, Read e Write. Le autorizzazioni possono essere specificate in modo singly o combinato. Per altre informazioni su queste autorizzazioni, vedere Creare una firma di accesso condiviso [di delega utente.](/rest/api/storageservices/create-user-delegation-sas)

La sintassi seguente restituisce una firma di accesso condiviso di delega utente per un BLOB. Nell'esempio viene specificato il `--full-uri` parametro , che restituisce l'URI del BLOB con il token di firma di accesso condiviso aggiunto. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati:

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user \
    --full-uri
```

L'URI di firma di accesso condiviso della delega utente restituito sarà simile al seguente:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> Una firma di accesso condiviso di delega utente non supporta la definizione delle autorizzazioni con criteri di accesso archiviati.

## <a name="revoke-a-user-delegation-sas"></a>Revocare una firma di accesso condiviso di delega utente

Per revocare una firma di accesso condiviso di delega utente dall'interfaccia della riga di comando di Azure, chiamare il [comando az storage account revoke-delegation-keys.](/cli/azure/storage/account#az_storage_account_revoke_delegation_keys) Questo comando revoca tutte le chiavi di delega utente associate all'account di archiviazione specificato. Tutte le firme di accesso condiviso associate a tali chiavi vengono invalidate.

È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> Sia la chiave di delega utente che le assegnazioni di ruolo di Azure vengono memorizzate nella cache da Archiviazione di Azure, quindi potrebbe verificarsi un ritardo tra l'avvio del processo di revoca e l'invalidità di una firma di accesso condiviso di delega utente esistente.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una firma di accesso condiviso di delega utente (API REST)](/rest/api/storageservices/create-user-delegation-sas)
- [Operazione Get User Delegation Key](/rest/api/storageservices/get-user-delegation-key)
