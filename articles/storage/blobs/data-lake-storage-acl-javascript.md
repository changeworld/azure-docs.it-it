---
title: Utilizzare JavaScript per impostare gli ACL in Azure Data Lake Storage Gen2
description: Usare archiviazione di Azure Data Lake libreria client per JavaScript per gestire gli elenchi di controllo di accesso (ACL) negli account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico (HNS).
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: 4d3e13c6593c0e11df84131a9a07eb2868277d2f
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654236"
---
# <a name="use-javascript-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Utilizzare JavaScript per gestire gli ACL in Azure Data Lake Storage Gen2

Questo articolo illustra come usare JavaScript per ottenere, impostare e aggiornare gli elenchi di controllo di accesso di directory e file. 

[Pacchetto (gestione pacchetti di nodi)](https://www.npmjs.com/package/@azure/storage-file-datalake)  |  [Esempi](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)  |  di [Invia commenti e suggerimenti](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico. Per crearne uno, seguire [queste](create-data-lake-storage-account.md) istruzioni.

- Interfaccia della riga di comando di Azure versione `2.6.0` o successiva.

- Una delle autorizzazioni di sicurezza seguenti:

  - [Entità di sicurezza](../../role-based-access-control/overview.md#security-principal) con provisioning Azure Active Directory (ad) a cui è stato assegnato il ruolo di [proprietario dei dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) nell'ambito del contenitore di destinazione, del gruppo di risorse padre o della sottoscrizione.  

  - Utente proprietario del contenitore o della directory di destinazione a cui si intende applicare le impostazioni ACL. Per impostare gli ACL in modo ricorsivo, sono inclusi tutti gli elementi figlio nel contenitore o nella directory di destinazione.
  
  - Chiave dell'account di archiviazione..

## <a name="set-up-your-project"></a>Configurare il progetto

Installare Data Lake libreria client per JavaScript aprendo una finestra del terminale e digitando il comando seguente.

```javascript
npm install @azure/storage-file-datalake
```

Importare il `storage-file-datalake` pacchetto inserendo questa istruzione all'inizio del file di codice. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Effettuare la connessione all'account

Per usare i frammenti di codice in questo articolo, è necessario creare un'istanza di **DataLakeServiceClient** che rappresenta l'account di archiviazione. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Connettersi tramite Azure Active Directory (AD)

> [!NOTE]
> Se si usa Azure Active Directory (Azure AD) per autorizzare l'accesso, assicurarsi che all'entità di sicurezza sia stato assegnato il [ruolo proprietario dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Per altre informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere il  [modello di controllo di accesso in Azure Data Lake storage Gen2](./data-lake-storage-access-control-model.md).

È possibile usare la [libreria client Azure Identity per JS](https://www.npmjs.com/package/@azure/identity) per autenticare l'applicazione con Azure ad.

Ottenere un ID client, un segreto client e un ID tenant. Per eseguire questa operazione, vedere [acquisizione di un token da Azure ad per l'autorizzazione delle richieste da un'applicazione client](../common/storage-auth-aad-app.md). Come parte di questo processo, è necessario assegnare uno dei ruoli di controllo degli [accessi in base al ruolo di Azure (RBAC)](../../role-based-access-control/overview.md) per l'entità di sicurezza. 

|Ruolo|Funzionalità impostazione ACL|
|--|--|
|[Proprietario dei dati del BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Tutte le directory e i file nell'account.|
|[Collaboratore ai dati del BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Solo le directory e i file di proprietà dell'entità di sicurezza.|

Questo esempio crea un'istanza di **DataLakeServiceClient** usando un ID client, un segreto client e un ID tenant.  

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> Per altri esempi, vedere la documentazione della [libreria client Azure Identity per JS](https://www.npmjs.com/package/@azure/identity) .

### <a name="connect-by-using-an-account-key"></a>Connettersi tramite una chiave dell'account

Questo è il modo più semplice per connettersi a un account. 

Questo esempio crea un'istanza di **DataLakeServiceClient** usando una chiave dell'account.

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```

> [!NOTE]
> Questo metodo di autorizzazione funziona solo per applicazioni Node.js. Se si prevede di eseguire il codice in un browser, è possibile autorizzare usando Azure Active Directory (AD).

## <a name="get-and-set-a-directory-acl"></a>Ottenere e impostare un ACL di directory

Questo esempio ottiene e imposta l'ACL di una directory denominata `my-directory` . Questo esempio fornisce le autorizzazioni di lettura, scrittura ed esecuzione dell'utente proprietario, fornisce al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e concede a tutti gli altri utenti l'accesso in lettura.

> [!NOTE]
> Se l'applicazione autorizza l'accesso tramite Azure Active Directory (Azure AD), assicurarsi che l'entità di sicurezza usata dall'applicazione per autorizzare l'accesso sia stata assegnata al [ruolo proprietario dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Per altre informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere [Controllo di accesso in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

È anche possibile ottenere e impostare l'ACL della directory radice di un contenitore. Per ottenere la directory radice, passare una stringa vuota ( `/` ) nel metodo **DataLakeFileSystemClient. getDirectoryClient** .

### <a name="get-and-set-a-file-acl"></a>Ottenere e impostare un ACL di file

Questo esempio ottiene e imposta l'ACL di un file denominato `upload-file.txt` . Questo esempio fornisce le autorizzazioni di lettura, scrittura ed esecuzione dell'utente proprietario, fornisce al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e concede a tutti gli altri utenti l'accesso in lettura.

> [!NOTE]
> Se l'applicazione autorizza l'accesso tramite Azure Active Directory (Azure AD), assicurarsi che l'entità di sicurezza usata dall'applicazione per autorizzare l'accesso sia stata assegnata al [ruolo proprietario dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Per altre informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere [Controllo di accesso in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="see-also"></a>Vedi anche

- [Pacchetto (npm)](https://www.npmjs.com/package/@azure/storage-file-datalake)
- [Esempi](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
- [Invia commenti e suggerimenti](https://github.com/Azure/azure-sdk-for-java/issues)
- [Modello di controllo di accesso in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Elenchi di controllo di accesso (ACL) in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)