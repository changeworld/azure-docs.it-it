---
title: Autorizzare l'accesso ai dati con un'identità gestita
titleSuffix: Azure Storage
description: Usare le identità gestite per le risorse di Azure per autorizzare l'accesso ai dati di BLOB e code da applicazioni in esecuzione in macchine virtuali di Azure, app per le funzioni e altre.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 80df7b85ec1ad9e273081f9a6a96b9a9d7ec8cd9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791194"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Autorizzare l'accesso ai dati di BLOB e code con identità gestite per le risorse di Azure

Archiviazione di BLOB e coda di Azure supporta l'autenticazione con [identità gestite di Azure Active Directory per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md). Le identità gestite per le risorse di Azure possono autorizzare l'accesso ai dati di BLOB e code usando credenziali Azure AD da applicazioni in esecuzione in macchine virtuali di Azure, app per le funzioni, set di scalabilità di macchine virtuali e altri servizi. Usando le identità gestite per le risorse di Azure insieme all'autenticazione Azure AD, è possibile evitare di archiviare le credenziali con le applicazioni eseguite nel cloud.  

Questo articolo illustra come autorizzare l'accesso ai dati del BLOB o della coda da una macchina virtuale di Azure usando le identità gestite per le risorse di Azure. Viene inoltre descritto come testare il codice nell'ambiente di sviluppo.

## <a name="enable-managed-identities-on-a-vm"></a>Abilitare le identità gestite su una macchina virtuale

Prima di poter usare le identità gestite per le risorse di Azure per autorizzare l'accesso a BLOB e code dalla macchina virtuale, è necessario abilitare le identità gestite per le risorse di Azure nella macchina virtuale. Per informazioni su come abilitare identità gestite per le risorse di Azure, vedere uno di questi articoli:

- [Azure portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfaccia della riga di comando di Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modello di Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager librerie client](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Per altre informazioni sulle identità gestite, vedere [Identità gestite per le risorse di Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="authenticate-with-the-azure-identity-library"></a>Eseguire l'autenticazione con la libreria di identità di Azure

La libreria client di Identità di Azure offre il supporto per l Azure AD di autenticazione basata su token di Azure per [Azure SDK.](https://github.com/Azure/azure-sdk) Le versioni più recenti delle librerie client di Archiviazione di Azure per .NET, Java, Python e JavaScript si integrano con la libreria di identità di Azure per fornire un mezzo semplice e sicuro per acquisire un token OAuth 2.0 per l'autorizzazione delle richieste Archiviazione di Azure.

Un vantaggio della libreria client di identità di Azure è che consente di usare lo stesso codice per autenticare se l'applicazione è in esecuzione nell'ambiente di sviluppo o in Azure. La libreria client delle identità di Azure per .NET autentica un'entità di sicurezza. Quando il codice è in esecuzione in Azure, l'entità di sicurezza è un'identità gestita per le risorse di Azure. Nell'ambiente di sviluppo l'identità gestita non esiste, quindi la libreria client autentica l'utente o un'entità servizio a scopo di test.

Dopo l'autenticazione, la libreria client di Identità di Azure ottiene le credenziali del token. Questa credenziale del token viene quindi incapsulata nell'oggetto client del servizio creato per eseguire operazioni su Archiviazione di Azure. La libreria gestisce questo problema senza problemi ottenendo le credenziali del token appropriate.

Per altre informazioni sulla libreria client di Identità di Azure per .NET, vedere [Libreria client di identità di Azure per .NET.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) Per la documentazione di riferimento per la libreria client di Identità di Azure, vedere [Spazio dei nomi Azure.Identity](/dotnet/api/azure.identity).

### <a name="assign-azure-roles-for-access-to-data"></a>Assegnare ruoli di Azure per l'accesso ai dati

Quando un'Azure AD di sicurezza tenta di accedere ai dati blob o di coda, tale entità di sicurezza deve disporre delle autorizzazioni per la risorsa. Se l'entità di sicurezza è un'identità gestita in Azure o un account utente di Azure AD che esegue codice nell'ambiente di sviluppo, all'entità di sicurezza deve essere assegnato un ruolo di Azure che concede l'accesso ai dati blob o della coda Archiviazione di Azure. Per informazioni sull'assegnazione delle autorizzazioni tramite il controllo degli accessi in base al ruolo di Azure, vedere la sezione assegnare i ruoli di **Azure** per i diritti di accesso in Autorizzare l'accesso a BLOB e code di [Azure usando Azure Active Directory](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights).

> [!NOTE]
> Quando si crea un account Archiviazione di Azure, non vengono assegnate automaticamente le autorizzazioni per accedere ai dati tramite Azure AD. È necessario assegnare in modo esplicito un ruolo di Azure per Archiviazione di Azure. È possibile assegnare questo ruolo a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda.
>
> Prima di assegnare a se stessi un ruolo per l'accesso ai dati, sarà possibile accedere ai dati nell'account di archiviazione tramite il portale di Azure perché il portale di Azure può anche usare la chiave dell'account per l'accesso ai dati. Per altre informazioni, vedere [Scegliere come autorizzare l'accesso](../blobs/authorize-data-operations-portal.md)ai dati BLOB nel portale di Azure .

### <a name="authenticate-the-user-in-the-development-environment"></a>Autenticare l'utente nell'ambiente di sviluppo

Quando il codice è in esecuzione nell'ambiente di sviluppo, l'autenticazione può essere gestita automaticamente o potrebbe essere necessario un accesso al browser, a seconda degli strumenti in uso. Ad esempio, Microsoft Visual Studio supporta l'accesso Single Sign-On (SSO), in modo che l'account Azure AD utente attivo sia usato automaticamente per l'autenticazione. Per altre informazioni sull'accesso Single Sign-On, vedere [Single Sign-On alle applicazioni.](../../active-directory/manage-apps/what-is-single-sign-on.md)

Altri strumenti di sviluppo possono richiedere l'accesso tramite un Web browser.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Autenticare un'entità servizio nell'ambiente di sviluppo

Se l'ambiente di sviluppo non supporta l'accesso Single Sign-On o l'accesso tramite un Web browser, è possibile usare un'entità servizio per eseguire l'autenticazione dall'ambiente di sviluppo.

#### <a name="create-the-service-principal"></a>Creare l'entità servizio

Per creare un'entità servizio con l'interfaccia della riga di comando di Azure e assegnare un ruolo di Azure, chiamare [il comando az ad sp create-for-rbac.](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) Fornire un Archiviazione di Azure di accesso ai dati da assegnare alla nuova entità servizio. Specificare anche l'ambito per l'assegnazione di ruolo. Per altre informazioni sui ruoli predefiniti forniti per Archiviazione di Azure, vedere [Ruoli predefiniti di Azure.](../../role-based-access-control/built-in-roles.md)

Se non si dispone di autorizzazioni sufficienti per assegnare un ruolo all'entità servizio, potrebbe essere necessario chiedere al proprietario o all'amministratore dell'account di eseguire l'assegnazione di ruolo.

L'esempio seguente usa l'interfaccia della riga  di comando di Azure per creare una nuova entità servizio e assegnarvi il ruolo lettore di dati del BLOB di archiviazione con ambito account

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Contributor" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Il `az ad sp create-for-rbac` comando restituisce un elenco di proprietà dell'entità servizio in formato JSON. Copiare questi valori in modo che sia possibile usarli per creare le variabili di ambiente necessarie nel passaggio successivo.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> La propagazione delle assegnazioni dei ruoli può richiedere alcuni minuti.

#### <a name="set-environment-variables"></a>Impostare le variabili di ambiente

La libreria client di Identità di Azure legge i valori da tre variabili di ambiente in fase di esecuzione per autenticare l'entità servizio. Nella tabella seguente viene descritto il valore da impostare per ogni variabile di ambiente.

|Variabile di ambiente|Valore
|-|-
|`AZURE_CLIENT_ID`|ID app per l'entità servizio
|`AZURE_TENANT_ID`|ID tenant dell'Azure AD servizio
|`AZURE_CLIENT_SECRET`|Password generata per l'entità servizio

> [!IMPORTANT]
> Dopo aver impostato le variabili di ambiente, chiudere e ria aprire la finestra della console. Se si usa un Visual Studio o un altro ambiente di sviluppo, potrebbe essere necessario riavviare l'ambiente di sviluppo per poter registrare le nuove variabili di ambiente.

Per altre informazioni, vedere [Creare un'identità per l'app di Azure nel portale.](../../active-directory/develop/howto-create-service-principal-portal.md)

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Esempio di codice .NET: creare un BLOB in blocchi

Aggiungere le direttive seguenti al codice per usare l'identità di `using` Azure e Archiviazione di Azure client.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

Per ottenere una credenziale del token che il codice può usare per autorizzare le richieste Archiviazione di Azure, creare un'istanza della [classe DefaultAzureCredential.](/dotnet/api/azure.identity.defaultazurecredential) L'esempio di codice seguente illustra come ottenere le credenziali del token autenticato e usarle per creare un oggetto client del servizio, quindi usare il client del servizio per caricare un nuovo BLOB:

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Per autorizzare le richieste sui dati blob o di coda Azure AD, è necessario usare HTTPS per tali richieste.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire i diritti di accesso ai dati di archiviazione con il controllo degli accessi in base al ruolo di Azure.](./storage-auth-aad-rbac-portal.md)
- [Usare Azure AD con le applicazioni di archiviazione](storage-auth-aad-app.md).
- [Eseguire i comandi di PowerShell con Azure AD credenziali per accedere ai dati BLOB](../blobs/authorize-data-operations-powershell.md)
- [Esercitazione: Accedere all'archiviazione dal servizio app usando le identità gestite](../../app-service/scenario-secure-app-access-storage.md)
