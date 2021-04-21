---
title: 'Avvio rapido: Libreria client di segreti di Azure Key Vault per .NET (versione 4)'
description: Informazioni su come creare, recuperare ed eliminare segreti da Azure Key Vault usando la libreria client .NET (versione 4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 6338b76e2f14e9f842b3395113badadcd37c8d32
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814173"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-net-sdk-v4"></a>Avvio rapido: Libreria client dei segreti di Azure Key Vault per .NET (SDK v4)

Introduzione alla libreria client dei segreti di Azure Key Vault per .NET. [Azure Key Vault ](../general/overview.md) è un servizio cloud che offre un archivio sicuro per i segreti. È possibile archiviare in modo sicuro chiavi, password, certificati e altri segreti. È possibile creare e gestire istanze di Azure Key Vault tramite il portale di Azure. Questa guida di avvio rapido descrive come creare, recuperare ed eliminare segreti da Azure Key Vault usando la libreria client .NET

Risorse per la libreria client di Key Vault:

[Documentazione di riferimento dell'API](/dotnet/api/azure.security.keyvault.secrets) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

Per altre informazioni su Key Vault e sui segreti, vedere:
- [Panoramica di Key Vault](../general/overview.md)
- [Panoramica dei segreti](about-secrets.md).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3.1 SDK o versioni successive](https://dotnet.microsoft.com/download/dotnet-core)
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)
* Un insieme di credenziali delle chiavi. È possibile crearne uno con il [portale di Azure](../general/quick-create-portal.md), l'[interfaccia della riga di comando di Azure](../general/quick-create-cli.md) o [Azure PowerShell](../general/quick-create-powershell.md).

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

### <a name="grant-access-to-your-key-vault"></a>Concedere l'accesso all'insieme di credenziali delle chiavi

Creare un criterio di accesso per l'insieme di credenziali delle chiavi che concede le autorizzazioni per il segreto all'account utente

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
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

Nella shell dei comandi installare la libreria client dei segreti di Azure Key Vault per .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
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
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS o Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Modello a oggetti

La libreria client dei segreti di Azure Key Vault per .NET consente di gestire i segreti. La sezione [Esempi di codice](#code-examples) illustra come creare un client e come impostare, recuperare ed eliminare un segreto.

## <a name="code-examples"></a>Esempi di codice

### <a name="add-directives"></a>Aggiungere le direttive

Aggiungere le direttive seguenti all'inizio di *Program.cs*:

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Autenticare e creare un client

In questo argomento di avvio rapido viene usato l'utente connesso per eseguire l'autenticazione in Key Vault, che è il metodo preferito per lo sviluppo locale. Per le applicazioni distribuite in Azure, l'identità gestita deve essere assegnata al servizio app o alla macchina virtuale. Per altre informazioni, vedere [Informazioni sulle identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Nell'esempio seguente, il nome dell'insieme di credenziali delle chiavi viene esteso al relativo URI, nel formato "https://\<your-key-vault-name\>.vault.azure.net". Questo esempio usa la classe ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential) della [libreria di identità di Azure](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme), che consente di usare lo stesso codice in ambienti diversi con opzioni diverse per specificare l'identità. Per altre informazioni sull'autenticazione nell'insieme di credenziali delle chiavi, vedere la [Guida per sviluppatori](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Salvare un segreto

Una volta autenticata l'applicazione console, aggiungere un segreto all'insieme di credenziali delle chiavi. Per questa attività, usare il metodo [SetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.setsecretasync). Il primo parametro del metodo accetta un nome per il segreto, ovvero "My Secret" in questo esempio.

```csharp
await client.SetSecretAsync(secretName, secretValue);
```

> [!NOTE]
> Se il nome del segreto esiste, il codice precedente ne crea una nuova versione.


### <a name="retrieve-a-secret"></a>Recuperare un segreto

È ora possibile recuperare il valore impostato in precedenza con il metodo [GetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.getsecretasync).

```csharp
var secret = await client.GetSecretAsync(secretName);
```

Il segreto è ora salvato come `secret.Value`.

### <a name="delete-a-secret"></a>consente di eliminare un segreto

Eliminare infine il segreto dall'insieme di credenziali delle chiavi con i metodi [StartDeleteSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.startdeletesecretasync) e [PurgeDeletedSecretAsync](/dotnet/api/azure.security.keyvault.keys.keyclient).

```csharp
var operation = await client.StartDeleteSecretAsync("mySecret");
// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

await client.PurgeDeletedKeyAsync("mySecret");
```

## <a name="sample-code"></a>Codice di esempio

Modificare l'applicazione console .NET Core per interagire con l'insieme di credenziali delle chiavi completando la procedura seguente:

1. Sostituire il codice in *Program.cs* con il codice seguente:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Secrets;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string secretName = "mySecret";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write("Input the value of your secret > ");
                var secretValue = Console.ReadLine();
    
                Console.Write($"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...");
                await client.SetSecretAsync(secretName, secretValue);
                Console.WriteLine(" done.");
    
                Console.WriteLine("Forgetting your secret.");
                secretValue = string.Empty;
                Console.WriteLine($"Your secret is '{secretValue}'.");
    
                Console.WriteLine($"Retrieving your secret from {keyVaultName}.");
                var secret = await client.GetSecretAsync(secretName);
                Console.WriteLine($"Your secret is '{secret.Value.Value}'.");
    
                Console.Write($"Deleting your secret from {keyVaultName} ...");
                DeleteSecretOperation operation = await client.StartDeleteSecretAsync(secretName);
                // You only need to wait for completion if you want to purge or recover the secret.
                await operation.WaitForCompletionAsync();
                Console.WriteLine(" done.");
                
                Console.Write($"Purging your secret from {keyVaultName} ...");
                await client.PurgeDeletedSecretAsync(secretName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Test e verifica

1. Eseguire il comando seguente per eseguire l'app.

    ```dotnetcli
    dotnet run
    ```

1. Quando richiesto, immettere un valore di un segreto. Ad esempio, mySecretPassword.

Compare una variante dell'output seguente:

```console
Input the value of your secret > mySecretPassword
Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
Forgetting your secret.
Your secret is ''.
Retrieving your secret from <your-unique-keyvault-name>.
Your secret is 'mySecretPassword'.
Deleting your secret from <your-unique-keyvault-name> ... done.    
Purging your secret from <your-unique-keyvault-name> ... done.
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Key Vault e su come integrarlo nelle app, vedere gli articoli seguenti:

- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Seguire un'[esercitazione sull'accesso a Key Vault da un'applicazione del servizio app](../general/tutorial-net-create-vault-azure-web-app.md)
- Seguire un'[esercitazione sull'accesso a Key Vault da una macchina virtuale](../general/tutorial-net-virtual-machine.md)
- Vedere la [Guida per gli sviluppatori per Azure Key Vault](../general/developers-guide.md)
- Vedere [Panoramica della sicurezza di Azure Key Vault](../general/security-features.md)
