---
title: 'Avvio rapido: Libreria client di chiavi di Azure Key Vault per .NET (versione 4)'
description: Informazioni su come creare, recuperare ed eliminare chiavi da Azure Key Vault usando la libreria client .NET (versione 4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 9a1404969b67a0c4919902a407427227ce397d9a
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825948"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-net-sdk-v4"></a>Avvio rapido: Libreria client di chiavi di Azure Key Vault per .NET (SDK v4)

Introduzione alla libreria client di chiavi di Azure Key Vault per .NET. [Azure Key Vault ](../general/overview.md) è un servizio cloud che offre un archivio sicuro per le chiavi crittografiche. È possibile archiviare in modo sicuro chiavi crittografiche, password, certificati e altri segreti. È possibile creare e gestire istanze di Azure Key Vault tramite il portale di Azure. Questa guida di avvio rapido descrive come creare, recuperare ed eliminare chiavi da Azure Key Vault usando la libreria client di chiavi per .NET

Risorse per la libreria client di chiavi di Key Vault:

[Documentazione di riferimento dell'API](/dotnet/api/azure.security.keyvault.keys) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.keys/)

Per altre informazioni su Key Vault e sulle chiavi, vedere:
- [Panoramica di Key Vault](../general/overview.md)
- [Panoramica delle chiavi](about-keys.md).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3.1 SDK o versioni successive](https://dotnet.microsoft.com/download/dotnet-core)
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)
* Un'istanza di Key Vault: è possibile crearne una con il [portale di Azure](../general/quick-create-portal.md), l'[interfaccia della riga di comando di Azure](../general/quick-create-cli.md) o [Azure PowerShell](../general/quick-create-powershell.md).

In questo argomento di avvio rapido si usano `dotnet` e l'interfaccia della riga di comando di Azure

## <a name="setup"></a>Configurazione

Questo argomento di avvio rapido usa la libreria di identità di Azure con l'interfaccia della riga di comando di Azure per autenticare l'utente nei servizi di Azure. Gli sviluppatori possono anche usare Visual Studio o Visual Studio Code per autenticare le chiamate. Per altre informazioni, vedere [Autenticare il client con la libreria client Azure Identity](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true).

### <a name="sign-in-to-azure"></a>Accedere ad Azure

1. Eseguire il comando `login`.

    ```azurecli-interactive
    az login
    ```

    Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso di Azure.

    In caso contrario, aprire una pagina del browser all'indirizzo [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e immettere il codice di autorizzazione visualizzato nel terminale.

2. Accedere con le credenziali dell'account nel browser.

#### <a name="grant-access-to-your-key-vault"></a>Concedere l'accesso all'insieme di credenziali delle chiavi

Creare un criterio di accesso per l'insieme di credenziali delle chiavi che concede le autorizzazioni per le chiavi all'account utente

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
```

### <a name="create-new-net-console-app"></a>Creare una nuova app console .NET

1. In una shell dei comandi eseguire questo comando per creare un progetto denominato `key-vault-console-app`:

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. Passare alla directory *key-vault-console-app* appena creata ed eseguire questo comando per compilare il progetto:

    ```dotnetcli
    dotnet build
    ```

    L'output di compilazione non deve contenere alcun avviso o errore.
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>Installare i pacchetti

Nella shell dei comandi installare la libreria client di chiavi di Azure Key Vault per .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Keys
```

Per questo argomento di avvio rapido è anche necessario installare la libreria client di Azure SDK per Azure Identity:

```dotnetcli
dotnet add package Azure.Identity
```

#### <a name="set-environment-variables"></a>Impostare le variabili di ambiente

Questa applicazione usa il nome dell'insieme di credenziali delle chiavi come variabile di ambiente denominata `KEY_VAULT_NAME`.

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS o Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Modello a oggetti

La libreria client di chiavi di Azure Key Vault per .NET consente di gestire le chiavi. La sezione [Esempi di codice](#code-examples) illustra come creare un client e come impostare, recuperare ed eliminare una chiave.

## <a name="code-examples"></a>Esempi di codice

### <a name="add-directives"></a>Aggiungere le direttive

Aggiungere le direttive seguenti all'inizio di *Program.cs*:

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Keys;
```

### <a name="authenticate-and-create-a-client"></a>Autenticare e creare un client

In questo argomento di avvio rapido viene usato l'utente connesso per eseguire l'autenticazione in Key Vault, che è il metodo preferito per lo sviluppo locale. Per le applicazioni distribuite in Azure, l'identità gestita deve essere assegnata al servizio app o alla macchina virtuale. Per altre informazioni, vedere [Informazioni sulle identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Nell'esempio seguente, il nome dell'insieme di credenziali delle chiavi viene esteso al relativo URI, nel formato "https://\<your-key-vault-name\>.vault.azure.net". Questo esempio usa la classe ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential) della [libreria di identità di Azure](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme), che consente di usare lo stesso codice in ambienti diversi con opzioni diverse per specificare l'identità. Per altre informazioni sull'autenticazione nell'insieme di credenziali delle chiavi, vedere la [Guida per sviluppatori](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

```csharp
var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.net";

var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-key"></a>Salvare una chiave

Per questa attività, usare il metodo [CreateKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.createkeyasync). I parametri del metodo accettano un nome di chiave e il [tipo di chiave](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.keys.keytype).

```csharp
var key = await client.CreateKeyAsync("myKey", KeyType.Rsa);
```

> [!NOTE]
> Se il nome della chiave esiste, il codice precedente ne crea una nuova versione.

### <a name="retrieve-a-key"></a>Recuperare una chiave

È ora possibile recuperare la chiave creata in precedenza con il metodo [GetKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.getkeyasync).

```csharp
var key = await client.GetKeyAsync("myKey");
```

### <a name="delete-a-key"></a>Eliminare una chiave

Infine, eliminare e rimuovere definitivamente la chiave dall'insieme di credenziali delle chiavi con i metodi [StartDeleteKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.startdeletekeyasync) e [PurgeDeletedKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.purgedeletedkeyasync).

```csharp
var operation = await client.StartDeleteKeyAsync("myKey");

// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

var key = operation.Value;
await client.PurgeDeletedKeyAsync("myKey");
```

## <a name="sample-code"></a>Codice di esempio

Modificare l'applicazione console .NET Core per interagire con l'insieme di credenziali delle chiavi completando la procedura seguente:

- Sostituire il codice in *Program.cs* con il codice seguente:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Keys;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string keyName = "myKey";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write($"Creating a key in {keyVaultName} called '{keyName}' ...");
                var createdKey = await client.CreateKeyAsync(keyName, KeyType.Rsa);
                Console.WriteLine("done.");
    
                Console.WriteLine($"Retrieving your key from {keyVaultName}.");
                var key = await client.GetKeyAsync(keyName);
                Console.WriteLine($"Your key version is '{key.Value.Properties.Version}'.");
    
                Console.Write($"Deleting your key from {keyVaultName} ...");
                var deleteOperation = await client.StartDeleteKeyAsync(keyName);
                // You only need to wait for completion if you want to purge or recover the key.
                await deleteOperation.WaitForCompletionAsync();
                Console.WriteLine("done.");

                Console.Write($"Purging your key from {keyVaultName} ...");
                await client.PurgeDeletedKeyAsync(keyName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Test e verifica

1.  Eseguire il comando seguente per creare il progetto

    ```dotnetcli
    dotnet build
    ```

1. Eseguire il comando seguente per eseguire l'app.

    ```dotnetcli
    dotnet run
    ```

1. Quando richiesto, immettere un valore di un segreto. Ad esempio, mySecretPassword.

    Compare una variante dell'output seguente:

    ```console
    Creating a key in mykeyvault called 'myKey' ... done.
    Retrieving your key from mykeyvault.
    Your key version is '8532359bced24e4bb2525f2d2050738a'.
    Deleting your key from jl-kv ... done
    Purging your key from <your-unique-keyvault-name> ... done.   
    ```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un insieme di credenziali delle chiavi e quindi è stata archiviata e recuperata una chiave. 

Per altre informazioni su Key Vault e su come integrarlo nelle app, vedere gli articoli seguenti:

- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Vedere una [panoramica delle chiavi](about-keys.md)
- Seguire un'[esercitazione sull'accesso a Key Vault da un'applicazione del servizio app](../general/tutorial-net-create-vault-azure-web-app.md)
- Seguire un'[esercitazione sull'accesso a Key Vault da una macchina virtuale](../general/tutorial-net-virtual-machine.md)
- Vedere la [Guida per gli sviluppatori per Azure Key Vault](../general/developers-guide.md)
- Esaminare le [procedure consigliate per Azure Key Vault](../general/best-practices.md)
