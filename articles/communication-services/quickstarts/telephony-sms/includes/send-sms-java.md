---
title: includere file
description: includere file
services: azure-communication-services
author: pvicencio
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/12/2021
ms.topic: include
ms.custom: include file
ms.author: pvicencio
ms.openlocfilehash: 30451f237f4a6d42fee018d5e6c5adb3bbf022b4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958177"
---
Per iniziare a usare i servizi di comunicazione di Azure, è possibile usare i servizi di comunicazione Java SMS SDK per inviare messaggi SMS.

Le procedure illustrate in questa guida di avvio rapido comportano l'addebito di qualche centesimo (USD) o meno nell'account Azure.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/) versione 8 o successiva.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Una stringa di connessione e una risorsa attiva di Servizi di comunicazione. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Un numero di telefono abilitato per gli SMS. [Ottenere un numero di telefono](../get-phone-number.md).

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

- In una finestra del terminale o di comando eseguire `mvn -v` per verificare se Maven è installato.
- Per visualizzare i numeri di telefono associati alla risorsa di Servizi di comunicazione, accedere al [portale di Azure](https://portal.azure.com/), individuare la risorsa di Servizi di comunicazione e aprire la scheda dei **numeri di telefono** nel riquadro di spostamento sinistro.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-java-application"></a>Creare una nuova applicazione Java

Aprire la finestra del terminale o di comando e passare alla directory in cui creare l'applicazione Java. Eseguire il comando seguente per generare il progetto Java dal modello maven-archetype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

L'obiettivo 'generate' creerà una directory con lo stesso nome di artifactId. In questa directory la directory **src/main/java** contiene il codice sorgente del progetto, la **directory src/test/java** contiene l'origine di test e il file **pom.xml** è il modello a oggetti del progetto (POM).

### <a name="install-the-package"></a>Installare il pacchetto

Aprire il file **pom.xml** nell'editor di testo. Aggiungere l'elemento di dipendenza seguente al gruppo di dipendenze.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Aggiungere la dipendenza `azure-core-http-netty` al file **pom.xml**.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core-http-netty</artifactId>
    <version>1.8.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core</artifactId>
    <version>1.13.0</version> <!-- {x-version-update;com.azure:azure-core;dependency} -->
</dependency>
```

Aprire **/src/main/java/com/communication/quickstart/App.java** in un editor di testo, aggiungere le direttive import e rimuovere l'istruzione `System.out.println("Hello world!");`:

```java
package com.communication.quickstart;

import com.azure.communication.sms.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.communication.sms.*;
import com.azure.core.http.HttpClient;
import com.azure.core.http.netty.NettyAsyncHttpClientBuilder;
import com.azure.core.util.Context;
import java.util.Arrays;

public class App
{
    public static void main( String[] args )
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità di Azure Communication Services SMS SDK per Java.

| Nome                                                             | Descrizione                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | Questa classe crea l'oggetto SmsClient. Specificare l'endpoint, le credenziali e un client HTTP. |
| SmsClient                    | Questa classe è necessaria per tutte le funzionalità SMS. Viene usata per inviare messaggi SMS.                |
| SmsSendOptions               | Questa classe fornisce opzioni per l'aggiunta di tag personalizzati e la configurazione della creazione di report di recapito. Se deliveryReportEnabled è impostato su true, verrà generato un evento al completamento del recapito.|                           |
| SmsSendResult                | Questa classe contiene il risultato del servizio SMS.                                          |

## <a name="authenticate-the-client"></a>Autenticare il client

Creare un'istanza di `SmsClient` con la stringa di connessione. (Credential è il `Key` dal portale di Azure. Informazioni su come [gestire la stringa di connessione della risorsa](../../create-communication-resource.md#store-your-connection-string).

Aggiungere al metodo `main` il codice seguente:

```java
// You can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<resource-name>.communication.azure.com/";
AzureKeyCredential azureKeyCredential = new AzureKeyCredential("<access-key-credential>");

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

SmsClient smsClient = new SmsClientBuilder()
                .endpoint(endpoint)
                .credential(azureKeyCredential)
                .httpClient(httpClient)
                .buildClient();
```

È possibile inizializzare il client con qualsiasi client HTTP personalizzato che implementi l'interfaccia `com.azure.core.http.HttpClient`. Il codice precedente illustra l'uso del [client HTTP Azure Core Netty](/java/api/overview/azure/core-http-netty-readme) fornito da `azure-core`.

È anche possibile specificare l'intera stringa di connessione usando la funzione connectionString() invece di fornire l'endpoint e la chiave di accesso. 
```java
// You can find your connection string from your resource in the Azure Portal
String connectionString = "https://<resource-name>.communication.azure.com/;<access-key>";

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

SmsClient smsClient = new SmsClientBuilder()
            .connectionString(connectionString)
            .httpClient(httpClient)
            .buildClient();
```

## <a name="send-a-11-sms-message"></a>Invia un messaggio SMS 1:1

Per inviare un messaggio SMS a un singolo destinatario, chiamare il `send` metodo da SmsClient con un singolo numero di telefono del destinatario. È anche possibile passare parametri facoltativi per specificare se il report di recapito deve essere abilitato e impostare tag personalizzati.

```java
SmsSendResult sendResult = smsClient.send(
                "<from-phone-number>",
                "<to-phone-number>",
                "Weekly Promotion");

System.out.println("Message Id: " + sendResult.getMessageId());
System.out.println("Recipient Number: " + sendResult.getTo());
System.out.println("Send Result Successful:" + sendResult.isSuccessful());
```

È necessario sostituire `<from-phone-number>` con un numero di telefono abilitato per SMS associato alla risorsa servizi di comunicazione e `<to-phone-number>` con un numero di telefono a cui si desidera inviare un messaggio.

> [!WARNING]
> Si noti che i numeri di telefono devono essere specificati nel formato standard internazionale E.164, (ad esempio: + 14255550123).

## <a name="send-a-1n-sms-message-with-options"></a>Invia un messaggio SMS 1: N con opzioni
Per inviare un messaggio SMS a un elenco di destinatari, chiamare il `send` metodo con un elenco di numeri di telefono del destinatario. È anche possibile passare parametri facoltativi per specificare se il report di recapito deve essere abilitato e impostare tag personalizzati.
```java
SmsSendOptions options = new SmsSendOptions();
options.setDeliveryReportEnabled(true);
options.setTag("Marketing");

Iterable<SmsSendResult> sendResults = smsClient.send(
    "<from-phone-number>",
    Arrays.asList("<to-phone-number1>", "<to-phone-number2>"),
    "Weekly Promotion",
    options /* Optional */,
    Context.NONE);

for (SmsSendResult result : sendResults) {
    System.out.println("Message Id: " + result.getMessageId());
    System.out.println("Recipient Number: " + result.getTo());
    System.out.println("Send Result Successful:" + result.isSuccessful());
}
```

È necessario sostituire `<from-phone-number>` con un numero di telefono abilitato per SMS associato alla risorsa servizi di comunicazione e `<to-phone-number-1>` `<to-phone-number-2>` con i numeri di telefono a cui si desidera inviare un messaggio.

> [!WARNING]
> Si noti che i numeri di telefono devono essere specificati nel formato standard internazionale E.164, (ad esempio: + 14255550123).

Il `setDeliveryReportEnabled` metodo viene utilizzato per configurare la creazione di report di recapito. È utile per gli scenari in cui si vogliono generare eventi quando vengono recapitati messaggi SMS. Per configurare i report di recapito per i messaggi SMS, vedere la guida di avvio rapido [Gestire gli eventi SMS](../handle-sms-events.md).

Il `setTag` metodo viene utilizzato per applicare un tag al report di recapito.

## <a name="run-the-code"></a>Eseguire il codice

Passare alla directory contenente il file **pom.xml** e compilare il progetto tramite il comando `mvn`.

```console

mvn compile

```

Compilare il pacchetto.

```console

mvn package

```

Eseguire il comando `mvn` seguente per eseguire l'app.

```console

mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false

```