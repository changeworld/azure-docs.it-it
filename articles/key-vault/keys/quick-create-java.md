---
title: 'Avvio rapido: Libreria client di chiavi di Azure Key Vault per Java'
description: Guida di avvio rapido per la libreria client di chiavi di Azure Key Vault per Java.
author: msmbaldwin
ms.custom: devx-track-java
ms.author: mbaldwin
ms.date: 01/05/2021
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.openlocfilehash: 124e56fad35be0f3ac5b08ee9dd66454b9d077c5
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374693"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-java"></a>Avvio rapido: Libreria client di chiavi di Azure Key Vault per Java
Introduzione alla libreria client di chiavi di Azure Key Vault per Java. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.

Risorse aggiuntive:

* [Codice sorgente](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)
* [Documentazione di riferimento delle API](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Documentazione del prodotto](index.yml)
* [Esempi](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys/src/samples/java/com/azure/security/keyvault/keys)

## <a name="prerequisites"></a>Prerequisiti
- Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/) versione 8 o successiva
- [Apache Maven](https://maven.apache.org)
- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)

Questo argomento di avvio rapido presuppone l'esecuzione dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e di [Apache Maven](https://maven.apache.org) in una finestra del terminale Linux.

## <a name="setting-up"></a>Configurazione
Questa guida di avvio rapido usa la libreria di identità di Azure con l'interfaccia della riga di comando di Azure per autenticare l'utente nei servizi di Azure. Gli sviluppatori possono anche usare Visual Studio o Visual Studio Code per autenticare le chiamate. Per altre informazioni, vedere [Autenticare il client con la libreria client Azure Identity](/java/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Accedere ad Azure
1. Eseguire il comando `login`.

    ```azurecli-interactive
    az login
    ```

   Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso di Azure.

   In caso contrario, aprire una pagina del browser all'indirizzo [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e immettere il codice di autorizzazione visualizzato nel terminale.

2. Accedere con le credenziali dell'account nel browser.

### <a name="create-a-new-java-console-app"></a>Creare una nuova app console Java
In una finestra della console usare il comando `mvn` per creare una nuova app console Java denominata `akv-keys-java`.

```console
mvn archetype:generate -DgroupId=com.keyvault.keys.quickstart
                       -DartifactId=akv-keys-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

L'output della generazione del progetto sarà simile al seguente:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-keys-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Spostarsi nella cartella `akv-keys-java/` appena creata.

```console
cd akv-keys-java
```

### <a name="install-the-package"></a>Installare il pacchetto
Aprire il file *pom.xml* nell'editor di testo. Aggiungere gli elementi di dipendenza seguenti al gruppo di dipendenze.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-keys</artifactId>
      <version>4.2.3</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.2.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>Creare un gruppo di risorse e un insieme di credenziali delle chiavi
[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Concedere l'accesso all'insieme di credenziali delle chiavi
Creare un criterio di accesso per l'insieme di credenziali delle chiavi che concede le autorizzazioni per le chiavi all'account utente.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
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
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Modello a oggetti
La libreria client di chiavi di Azure Key Vault per Java consente di gestire le chiavi. La sezione [Esempi di codice](#code-examples) illustra come creare un client e come creare, recuperare ed eliminare una chiave.

L'intera app console è disponibile [di seguito](#sample-code).

## <a name="code-examples"></a>Esempi di codice
### <a name="add-directives"></a>Aggiungere le direttive
Aggiungere le direttive seguenti all'inizio del codice:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;
```

### <a name="authenticate-and-create-a-client"></a>Autenticare e creare un client
In questa guida di avvio rapido viene usato l'utente connesso per eseguire l'autenticazione in Key Vault, che è il metodo preferito per lo sviluppo locale. Per le applicazioni distribuite in Azure, occorre assegnare un'identità gestita a un servizio app o a una macchina virtuale. Per altre informazioni, vedere [Panoramica delle identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Nell'esempio riportato di seguito, il nome dell'insieme di credenziali delle chiavi viene esteso al relativo URI, nel formato "https://\<your-key-vault-name\>.vault.azure.net". Questo esempio usa la classe ['DefaultAzureCredential()'](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential), che consente di usare lo stesso codice in ambienti diversi con opzioni diverse per specificare l'identità. Per altre informazioni, vedere [Autenticazione DefaultAzureCredential](https://docs.microsoft.com/java/api/overview/azure/identity-readme).

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

KeyClient keyClient = new KeyClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="create-a-key"></a>Creare una chiave
Ora che l'applicazione è autenticata, è possibile creare una chiave nell'insieme di credenziali delle chiavi usando il metodo `keyClient.createKey`. A questo scopo è necessario avere un nome per la chiave e un tipo di chiave. In questo esempio è stato assegnato il valore "myKey" alla variabile `keyName` e si usa RSA come `KeyType`.

```java
keyClient.createKey(keyName, KeyType.RSA);
```

È possibile verificare che la chiave sia stata impostata con il comando [az keyvault key show](/cli/azure/keyvault/key?#az-keyvault-key-show):

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
```

### <a name="retrieve-a-key"></a>Recuperare una chiave
È ora possibile recuperare la chiave creata in precedenza con il metodo `keyClient.getKey`.

```java
KeyVaultKey retrievedKey = keyClient.getKey(keyName);
 ```

È ora possibile accedere ai dettagli della chiave recuperata con operazioni come `retrievedKey.getProperties`, `retrievedKey.getKeyOperations` e così via.

### <a name="delete-a-key"></a>Eliminare una chiave
Infine, eliminare la chiave dall'insieme di credenziali delle chiavi con il metodo `keyClient.beginDeleteKey`.

L'eliminazione della chiave è un'operazione a esecuzione prolungata, per cui è possibile eseguire il polling dello stato di avanzamento o attenderne il completamento.

```java
SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
deletionPoller.waitForCompletion();
```

Per verificare se la chiave è stata eliminata, usare il comando [az keyvault key show](/cli/azure/keyvault/key?#az-keyvault-key-show):

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
```

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non servono più, è possibile usare l'interfaccia della riga di comando di Azure o Azure PowerShell per rimuovere l'insieme di credenziali delle chiavi e il gruppo di risorse corrispondente.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Codice di esempio
```java
package com.keyvault.keys.quickstart;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and key vault URI = %s \n", keyVaultName, keyVaultUri);

        KeyClient keyClient = new KeyClientBuilder()
                .vaultUrl(keyVaultUri)
                .credential(new DefaultAzureCredentialBuilder().build())
                .buildClient();

        String keyName = "myKey";

        System.out.print("Creating a key in " + keyVaultName + " called '" + keyName + " ... ");

        keyClient.createKey(keyName, KeyType.RSA);

        System.out.print("done.");
        System.out.println("Retrieving key from " + keyVaultName + ".");

        KeyVaultKey retrievedKey = keyClient.getKey(keyName);

        System.out.println("Your key's ID is '" + retrievedKey.getId() + "'.");
        System.out.println("Deleting your key from " + keyVaultName + " ... ");

        SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
        deletionPoller.waitForCompletion();

        System.out.print("done.");
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
In questa guida di avvio rapido è stato creato un insieme di credenziali delle chiavi e quindi è stata creata, recuperata ed eliminata una chiave. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Vedere la [panoramica della sicurezza di Key Vault](../general/security-overview.md)
- Vedere la [Guida per gli sviluppatori per Azure Key Vault](../general/developers-guide.md)
- Come [Proteggere l'accesso a un insieme di credenziali delle chiavi](../general/secure-your-key-vault.md)