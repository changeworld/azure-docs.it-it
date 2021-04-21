---
title: Avvio rapido - Libreria client delle chiavi di Azure Key Vault per JavaScript (versione 4)
description: Informazioni su come creare, recuperare ed eliminare chiavi da Azure Key Vault usando la libreria client per JavaScript
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: c0066409732f4492186ea0bf604261e1ab59ca9f
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750303"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-javascript-version-4"></a>Avvio rapido: Libreria client delle chiavi di Azure Key Vault per JavaScript (versione 4)

Introduzione alla libreria client di chiavi di Azure Key Vault per JavaScript. [Azure Key Vault ](../general/overview.md) è un servizio cloud che offre un archivio sicuro per le chiavi crittografiche. È possibile archiviare in modo sicuro chiavi, password, certificati e altri segreti. È possibile creare e gestire istanze di Azure Key Vault tramite il portale di Azure. Questa guida di avvio rapido descrive come creare, recuperare ed eliminare chiavi da Azure Key Vault usando la libreria client di chiavi per JavaScript

Risorse per la libreria client di Key Vault:

[Documentazione di riferimento sull'API](/javascript/api/overview/azure/key-vault-index) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [Pacchetto (npm)](https://www.npmjs.com/package/@azure/keyvault-keys)

Per altre informazioni su Key Vault e sulle chiavi, vedere:
- [Panoramica di Key Vault](../general/overview.md)
- [Panoramica delle chiavi](about-keys.md).

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

```azurecli
mkdir key-vault-node-app
```

1. Passare alla directory *key-vault-node-app* appena creata ed eseguire il comando init per inizializzare il progetto Node:

```azurecli
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Installare i pacchetti di Key Vault

Nella finestra della console installare la [libreria delle chiavi](https://www.npmjs.com/package/@azure/keyvault-keys) di Azure Key Vault per Node.js.

```azurecli
npm install @azure/keyvault-keys
```

Installare il pacchetto [azure.identity](https://www.npmjs.com/package/@azure/identity) per eseguire l'autenticazione in un insieme di credenziali delle chiavi

```azurecli
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

Creare un criterio di accesso per l'insieme di credenziali delle chiavi che concede le autorizzazioni per le chiavi all'account utente

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --key-permissions delete get list create purge
```

## <a name="code-examples"></a>Esempi di codice

Gli esempi di codice riportati di seguito illustrano come creare un client e come impostare, recuperare ed eliminare una chiave. 

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
const { KeyClient } = require("@azure/keyvault-keys");
```

### <a name="authenticate-and-create-a-client"></a>Autenticare e creare un client

In questo argomento di avvio rapido viene usato l'utente connesso per eseguire l'autenticazione in Key Vault, che è il metodo preferito per lo sviluppo locale. Per le applicazioni distribuite in Azure, l'identità gestita deve essere assegnata al servizio app o alla macchina virtuale. Per altre informazioni, vedere [Informazioni sulle identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Nell'esempio seguente, il nome dell'insieme di credenziali delle chiavi viene esteso al relativo URI, nel formato "https://\<your-key-vault-name\>.vault.azure.net". Questo esempio usa la classe ['DefaultAzureCredential()'](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) della [libreria di identità di Azure](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme), che consente di usare lo stesso codice in ambienti diversi con opzioni diverse per specificare l'identità. Per altre informazioni sull'autenticazione nell'insieme di credenziali delle chiavi, vedere la [Guida per sviluppatori](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

Aggiungere il codice seguente alla funzione 'main()'

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new KeyClient(KVUri, credential);
```

### <a name="save-a-key"></a>Salvare una chiave

Ora che l'applicazione è stata autenticata, è possibile inserire una chiave nell'insieme di credenziali delle chiavi usando il [metodo createKey](/javascript/api/@azure/keyvault-keys/keyclient?#createKey_string__KeyType__CreateKeyOptions_). I parametri del metodo accettano un nome di chiave e il [tipo di chiave](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keytype)

```javascript
await client.createKey(keyName, keyType);
```

### <a name="retrieve-a-key"></a>Recuperare una chiave

È ora possibile recuperare il valore impostato in precedenza con il [metodo getKey](/javascript/api/@azure/keyvault-keys/keyclient?#getKey_string__GetKeyOptions_).

```javascript
const retrievedKey = await client.getKey(keyName);
 ```

### <a name="delete-a-key"></a>Eliminare una chiave

Infine, eliminare e rimuovere definitivamente la chiave dall'insieme di credenziali delle chiavi con i metodi [beginDeleteKey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#beginDeleteKey_string__BeginDeleteKeyOptions_) e [purgeDeletedKey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#purgeDeletedKey_string__PurgeDeletedKeyOptions_).

```javascript
const deletePoller = await client.beginDeleteKey(keyName);
await deletePoller.pollUntilDone();
await client.purgeDeletedKey(keyName);
```

## <a name="sample-code"></a>Codice di esempio

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { KeyClient } = require("@azure/keyvault-keys");

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
  const client = new KeyClient(KVUri, credential);

  const keyName = "myKey";
  
  console.log("Creating a key in " + keyVaultName + " called '" + keyName + "` ...");
  await client.createKey(keyName, "RSA");

  console.log("Done.");

  console.log("Retrieving your key from " + keyVaultName + ".");

  const retrievedKey = await client.getKey(keyName);

  console.log("Your key version is '" + retrievedKey.properties.version + "'.");

  console.log("Deleting your key from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteKey(keyName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your key from {keyVaultName} ...");
  await client.purgeDeletedKey(keyName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>Test e verifica

Eseguire i comandi seguenti per eseguire l'app.

```azurecli
npm install
npm index.js
```

Compare una variante dell'output seguente:

```azurecli
Creating a key in <your-unique-keyvault-name> called 'myKey' ... done.
Retrieving your key from mykeyvault.
Your key version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your key from <your-unique-keyvault-name> ... done.  
Purging your key from <your-unique-keyvault-name> ... done.   
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un insieme di credenziali delle chiavi e quindi è stata archiviata e recuperata una chiave. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Leggere una [panoramica delle chiavi di Azure Key Vault](about-keys.md)
- Come [Proteggere l'accesso a un insieme di credenziali delle chiavi](../general/security-overview.md)
- Vedere la [Guida per gli sviluppatori per Azure Key Vault](../general/developers-guide.md)
- Vedere [Panoramica della sicurezza di Azure Key Vault](../general/security-overview.md)
