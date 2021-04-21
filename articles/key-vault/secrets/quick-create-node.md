---
title: Avvio rapido - Libreria client dei segreti di Azure Key Vault per JavaScript (versione 4)
description: Informazioni su come creare, recuperare ed eliminare segreti da Azure Key Vault usando la libreria client per JavaScript
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 90e505a4d505cc52920f1c7727afb321f4eda462
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814155"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-javascript-version-4"></a>Avvio rapido: Libreria client dei segreti di Azure Key Vault per JavaScript (versione 4)

Introduzione alla libreria client dei segreti di Azure Key Vault per JavaScript. [Azure Key Vault ](../general/overview.md) è un servizio cloud che offre un archivio sicuro per i segreti. È possibile archiviare in modo sicuro chiavi, password, certificati e altri segreti. È possibile creare e gestire istanze di Azure Key Vault tramite il portale di Azure. Questa guida di avvio rapido descrive come creare, recuperare ed eliminare segreti da Azure Key Vault usando la libreria client JavaScript

Risorse per la libreria client di Key Vault:

[Documentazione di riferimento sull'API](/javascript/api/overview/azure/key-vault-index) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [Pacchetto (npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

Per altre informazioni su Key Vault e sui segreti, vedere:
- [Panoramica di Key Vault](../general/overview.md)
- [Panoramica dei segreti](about-secrets.md).

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Versione aggiornata di [Node.js](https://nodejs.org) per il sistema operativo in uso.
- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)
- Un insieme di credenziali delle chiavi. È possibile crearne uno con il [portale di Azure](../general/quick-create-portal.md), l'[interfaccia della riga di comando di Azure](../general/quick-create-cli.md) o [Azure PowerShell](../general/quick-create-powershell.md).

Questa guida di avvio rapido presuppone che si usi l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

1. Eseguire il comando `login`.

    ```azurecli-interactive
    az login
    ```

    Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso di Azure.

    In caso contrario, aprire una pagina del browser all'indirizzo [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e immettere il codice di autorizzazione visualizzato nel terminale.

2. Accedere con le credenziali dell'account nel browser.

## <a name="create-new-nodejs-application"></a>Creare una nuova applicazione Node.js

Creare quindi un'applicazione Node.js che possa essere distribuita nel cloud. 

1. In una shell dei comandi creare una cartella denominata `key-vault-node-app`:

```terminal
mkdir key-vault-node-app
```

1. Passare alla directory *key-vault-node-app* appena creata ed eseguire il comando init per inizializzare il progetto Node:

```terminal
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Installare i pacchetti di Key Vault

Nella finestra della console installare la [libreria dei segreti](https://www.npmjs.com/package/@azure/keyvault-secrets) di Azure Key Vault per Node.js.

```terminal
npm install @azure/keyvault-secrets
```

Installare il pacchetto [azure.identity](https://www.npmjs.com/package/@azure/identity) per eseguire l'autenticazione in un insieme di credenziali delle chiavi

```terminal
npm install @azure/identity
```

## <a name="set-environment-variables"></a>Impostare le variabili di ambiente

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
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="grant-access-to-your-key-vault"></a>Concedere l'accesso all'insieme di credenziali delle chiavi

Creare un criterio di accesso per l'insieme di credenziali delle chiavi che concede le autorizzazioni per il segreto all'account utente

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
```

## <a name="code-examples"></a>Esempi di codice

Gli esempi di codice riportati di seguito illustrano come creare un client e come impostare, recuperare ed eliminare un segreto. 

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

1. Creare un nuovo file di testo e salvarlo con il nome 'index.js'

1. Aggiungere le chiamate necessarie per caricare i moduli di Azure e Node.js

1. Creare la struttura per il programma, inclusa la gestione delle eccezioni di base

```javascript
const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {
    
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
```

### <a name="add-directives"></a>Aggiungere le direttive

Aggiungere le direttive seguenti all'inizio del codice:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>Autenticare e creare un client

In questo argomento di avvio rapido viene usato l'utente connesso per eseguire l'autenticazione in Key Vault, che è il metodo preferito per lo sviluppo locale. Per le applicazioni distribuite in Azure, l'identità gestita deve essere assegnata al servizio app o alla macchina virtuale. Per altre informazioni, vedere [Informazioni sulle identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Nell'esempio seguente, il nome dell'insieme di credenziali delle chiavi viene esteso al relativo URI, nel formato "https://\<your-key-vault-name\>.vault.azure.net". Questo esempio usa la [classe 'DefaultAzureCredential()'](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) di [Azure Identity Library,](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)che consente di usare lo stesso codice in ambienti diversi con opzioni diverse per fornire l'identità. Per altre informazioni sull'autenticazione a Key Vault, vedere [La Guida per gli sviluppatori.](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)

Aggiungere il codice seguente alla funzione 'main()'

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Salvare un segreto

Ora che l'applicazione è stata autenticata, è possibile includere un segreto nell'insieme di credenziali delle chiavi usando il [metodo setSecret](/javascript/api/@azure/keyvault-secrets/secretclient#setSecret_string__string__SetSecretOptions_). A tale scopo è necessario un nome per il segreto e in questo esempio viene usato "mySecret".  

```javascript
await client.setSecret(secretName, secretValue);
```

### <a name="retrieve-a-secret"></a>Recuperare un segreto

È ora possibile recuperare il valore impostato in precedenza con il [metodo getSecret](/javascript/api/@azure/keyvault-secrets/secretclient#getSecret_string__GetSecretOptions_).

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

Il segreto è ora salvato come `retrievedSecret.value`.

### <a name="delete-a-secret"></a>consente di eliminare un segreto

Infine, eliminare e rimuovere definitivamente il segreto dall'insieme di credenziali delle chiavi con i metodi [beginDeleteSecret](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#beginDeleteSecret_string__BeginDeleteSecretOptions_) e [purgeDeletedSecret](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#purgeDeletedSecret_string__PurgeDeletedSecretOptions_).

```javascript
const deletePoller = await client.beginDeleteSecret(secretName);
await deletePoller.pollUntilDone();
await client.purgeDeletedSecret(secretName);
```

## <a name="sample-code"></a>Codice di esempio

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");

const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {

  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new SecretClient(KVUri, credential);

  const secretName = "mySecret";
  var secretValue = await askQuestion("Input the value of your secret > ");

  console.log("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");
  await client.setSecret(secretName, secretValue);

  console.log("Done.");

  console.log("Forgetting your secret.");
  secretValue = "";
  console.log("Your secret is '" + secretValue + "'.");

  console.log("Retrieving your secret from " + keyVaultName + ".");

  const retrievedSecret = await client.getSecret(secretName);

  console.log("Your secret is '" + retrievedSecret.value + "'.");

  console.log("Deleting your secret from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteSecret(secretName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your secret from {keyVaultName} ...");
  await client.purgeDeletedSecret(secretName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>Test e verifica

1. Eseguire i comandi seguenti per eseguire l'app.

    ```terminal
    npm install
    node index.js
    ```

1. Quando richiesto, immettere un valore di un segreto. Ad esempio, mySecretPassword.

    Compare una variante dell'output seguente:

    ```azurecli
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

In questo argomento di avvio rapido è stato creato un insieme di credenziali delle chiavi e quindi è stato archiviato e recuperato un segreto. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Leggere una [panoramica dei segreti di Azure Key Vault](about-secrets.md)
- Come [Proteggere l'accesso a un insieme di credenziali delle chiavi](../general/security-features.md)
- Vedere la [Guida per gli sviluppatori per Azure Key Vault](../general/developers-guide.md)
- Vedere [Panoramica della sicurezza di Azure Key Vault](../general/security-features.md)
