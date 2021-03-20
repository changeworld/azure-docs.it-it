---
title: Proteggere le credenziali di accesso con i servizi collegati in Apache Spark per Azure Synapse Analytics
description: Questo articolo illustra i concetti relativi all'integrazione sicura di Apache Spark per Azure Synapse Analytics con altri servizi usando servizi collegati e la libreria di token
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/19/2020
ms.author: martinle
ms.reviewer: nirav
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: a588b37b270917524453419619fdad6f88f92338
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101693578"
---
# <a name="secure-credentials-with-linked-services-using-the-tokenlibrary"></a>Proteggere le credenziali con i servizi collegati tramite TokenLibrary

L'accesso ai dati da origini esterne è uno schema comune. A meno che l'origine dati esterna non consenta l'accesso anonimo, è probabile che sia necessario proteggere la connessione con una credenziale, un segreto o una stringa di connessione.  

Per impostazione predefinita, Synapse usa il passthrough di Azure Active Directory (AAD) per l'autenticazione tra risorse.  Se è necessario connettersi a una risorsa usando altre credenziali, usare direttamente TokenLibrary.  TokenLibrary semplifica il processo di recupero di token di firma di accesso condiviso, token di AAD, stringhe di connessione e segreti archiviati in un servizio collegato o in Azure Key Vault.

Quando si recuperano i segreti da Azure Key Vault, è consigliabile creare un servizio collegato alla propria istanza di Azure Key Vault.  Assicurarsi che l'identità del servizio gestita dell'area di lavoro di Synapse abbia privilegi Secret Get per Azure Key Vault.  Synapse eseguirà l'autenticazione in Azure Key Vault usando l'identità del servizio gestita dell'area di lavoro di Synapse. Se ci si connette direttamente ad Azure Key Vault senza un servizio collegato, si eseguirà l'autenticazione con le credenziali utente di Azure Active Directory.

Per altre informazioni, vedere [Servizi collegati](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="usage"></a>Uso

### <a name="tokenlibraryhelp"></a>TokenLibrary.help()
Questa funzione visualizza la documentazione della Guida per TokenLibrary.

::: zone pivot = "programming-language-scala"

```scala
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
Console.WriteLine(TokenLibrary.help());
```

::: zone-end

## <a name="tokenlibrary-for-azure-data-lake-storage-gen2"></a>TokenLibrary per Azure Data Lake Storage Gen2

#### <a name="adls-gen2-primary-storage"></a>Archiviazione primaria di ADLS Gen2

Per l'accesso ai file dall'archiviazione primaria di Azure Data Lake Storage per impostazione predefinita viene usato il passthrough di Azure Active Directory per l'autenticazione e non è richiesto l'uso esplicito di TokenLibrary.

::: zone pivot = "programming-language-scala"

```scala
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")
display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')
display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-linked-services"></a>Archiviazione di ADLS Gen2 con servizi collegati

Synapse offre un'esperienza integrata di servizi collegati per la connessione ad Azure Data Lake Storage Gen2.  I servizi collegati possono essere configurati per eseguire l'autenticazione tramite **chiave dell'account**, **entità servizio**, **identità gestita** o **credenziali**.

Se il metodo di autenticazione del servizio collegato è impostato su **Chiave dell'account**, l'autenticazione verrà eseguita con la chiave dell'account di archiviazione specificata, verrà chiesta una chiave di firma di accesso condiviso che verrà automaticamente applicata alla richiesta di archiviazione tramite **LinkedServiceBasedSASProvider**.

::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

Se il metodo di autenticazione del servizio collegato è impostato su **Identità gestita** o su **Entità servizio**, verrà usato il token dell'identità gestita o dell'entità servizio con il provider **LinkedServiceBasedTokenProvider**.  


::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.oauth.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider") 
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

#### <a name="adls-gen2-storage-without-linked-services"></a>Archiviazione di ADLS Gen2 senza servizi collegati

Connettersi all'archiviazione di ADLS Gen2 direttamente usando una chiave di firma di accesso condiviso tramite **ConfBasedSASProvider** e specificare la chiave di firma di accesso condiviso nell'impostazione di configurazione **spark.storage.synapse.sas**.

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark

spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-azure-key-vault"></a>Archiviazione di ADLS Gen2 con Azure Key Vault

Connettersi all'archiviazione di ADLS Gen2 usando un token di firma di accesso condiviso archiviato in un segreto di Azure Key Vault.  

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

## <a name="tokenlibrary-for-other-linked-services"></a>TokenLibrary per altri servizi collegati

Per connettersi ad altri servizi, è possibile effettuare una chiamata diretta a TokenLibrary.

#### <a name="getconnectionstring"></a>getConnectionString()

 Per recuperare la stringa di connessione, usare la funzione **getConnectionString** e passare il **nome del servizio collegato**.

::: zone pivot = "programming-language-scala"

```scala
%%spark
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
%%csharp
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

::: zone-end

#### <a name="getconnectionstringasmap"></a>getConnectionStringAsMap()

getConnectionStringAsMap è una funzione helper disponibile in Scala e in Python per analizzare specifici valori di una coppia _chiave-valore_ nella stringa di connessione, ad esempio

_`DefaultEndpointsProtocol=https;AccountName=<ACCOUNT NAME>;AccountKey=<ACCOUNT KEY>`_

usare la funzione **getConnectionStringAsMap** e passare la chiave per restituire il valore.  Nell'esempio di stringa di archiviazione precedente, 

_**TokenLibrary.getConnectionStringAsMap("DefaultEndpointsProtocol")**_

restituisce

**_"https"_**

::: zone pivot = "programming-language-scala"

```scala
// Linked services can be used for storing and retrieving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```
::: zone-end

::: zone pivot = "programming-language-python"

```python
# Linked services can be used for storing and retrieving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

::: zone-end

#### <a name="getsecret"></a>getSecret()

Per recuperare un segreto da Azure Key Vault, è consigliabile creare un servizio collegato ad Azure Key Vault all'interno dell'area di lavoro di Synapse. All'identità del servizio gestita dell'area di lavoro di Synapse sarà necessario concedere l'autorizzazione **GET** Secrets per Azure Key Vault.  Il servizio collegato userà l'identità del servizio gestita per connettersi al servizio Azure Key Vault e recuperare il segreto.  Altrimenti, se ci si connette direttamente ad Azure Key Vault, verranno usate le credenziali utente di Azure Active Directory (AAD).  In questo caso, all'utente sarà necessario concedere le autorizzazioni Get Secret per Azure Key Vault.

`TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>" [, <LINKED SERVICE NAME>])`

Per recuperare un segreto da Azure Key Vault, usare la funzione **TokenLibrary.getSecret()** .

::: zone pivot = "programming-language-scala"

```scala
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
import sys
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

connection_string = token_library.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>");
Console.WriteLine(connectionString);
```

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

- [Scrivere nel pool SQL dedicato](./synapse-spark-sql-pool-import-export.md)
