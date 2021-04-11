---
title: includere file
description: includere file
services: azure-communication-services
author: peiliu
manager: rejooyan
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: peiliu
ms.openlocfilehash: 9282601391195ca3a47f277f05e9aa1ea0b211da
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958395"
---
Per iniziare a usare i servizi di comunicazione di Azure, usare Communication Services C# SMS SDK per inviare messaggi SMS.

Le procedure illustrate in questa guida di avvio rapido comportano l'addebito di qualche centesimo (USD) o meno nell'account Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Package (NuGet)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- La versione più recente [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) per il sistema operativo in uso.
- Una stringa di connessione e una risorsa attiva di Servizi di comunicazione. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Un numero di telefono abilitato per gli SMS. [Ottenere un numero di telefono](../get-phone-number.md).

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

- In un terminale o una finestra di comando eseguire il `dotnet` comando per verificare che .NET SDK sia installato.
- Per visualizzare i numeri di telefono associati alla risorsa di Servizi di comunicazione, accedere al [portale di Azure](https://portal.azure.com/), individuare la risorsa di Servizi di comunicazione e aprire la scheda dei **numeri di telefono** nel riquadro di spostamento sinistro.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `SmsQuickstart`. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: **Program.cs**.

```console
dotnet new console -o SmsQuickstart
```

Impostare la directory sulla cartella dell'app appena creata e usare il comando `dotnet build` per compilare l'applicazione.

```console
cd SmsQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installare il pacchetto

Quando si è ancora nella directory dell'applicazione, installare il pacchetto SMS SDK per Azure Communication Services per .NET usando il `dotnet add package` comando.

```console
dotnet add package Azure.Communication.Sms --version 1.0.0
```

Aggiungere una direttiva `using` all'inizio di **Program.cs** per includere lo spazio dei nomi `Azure.Communication`.

```csharp

using System;
using System.Collections.Generic;

using Azure;
using Azure.Communication;
using Azure.Communication.Sms;

```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità di Azure Communication Services SMS SDK per C#.

| Nome                                       | Descrizione                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SmsClient     | Questa classe è necessaria per tutte le funzionalità SMS. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per inviare messaggi SMS.                           |
| SmsSendOptions | Questa classe fornisce opzioni per la configurazione dei report di recapito. Se enable_delivery_report è impostato su True, verrà generato un evento quando il recapito ha esito positivo |
| SmsSendResult               | Questa classe contiene il risultato del servizio SMS.                                          |

## <a name="authenticate-the-client"></a>Autenticare il client

 Aprire **Program.cs** in un editor di testo e sostituire il corpo del metodo `Main` con il codice per inizializzare un oggetto `SmsClient` con la stringa di connessione. Il codice seguente recupera la stringa di connessione per la risorsa da una variabile di ambiente denominata `COMMUNICATION_SERVICES_CONNECTION_STRING`. Informazioni su come [gestire la stringa di connessione della risorsa](../../create-communication-resource.md#store-your-connection-string).


```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");

SmsClient smsClient = new SmsClient(connectionString);
```

## <a name="send-a-11-sms-message"></a>Invia un messaggio SMS 1:1

Per inviare un messaggio SMS a un singolo destinatario, chiamare la `Send` `SendAsync` funzione o da SmsClient. Aggiungere questo codice alla fine del metodo `Main` in **Program.cs**:

```csharp
SmsSendResult sendResult = smsClient.Send(
    from: "<from-phone-number>",
    to: "<to-phone-number>",
    message: "Hello World via SMS"
);

Console.WriteLine($"Sms id: {sendResult.MessageId}");
```
È necessario sostituire `<from-phone-number>` con un numero di telefono abilitato per gli SMS associato alla risorsa di Servizi di comunicazione e `<to-phone-number>` con il numero di telefono a cui inviare un messaggio.

> [!WARNING]
> Si noti che i numeri di telefono devono essere specificati nel formato standard internazionale E.164, (ad esempio: + 14255550123).

## <a name="send-a-1n-sms-message-with-options"></a>Invia un messaggio SMS 1: N con opzioni
Per inviare un messaggio SMS a un elenco di destinatari, chiamare la `Send` `SendAsync` funzione o da SmsClient con un elenco di numeri di telefono del destinatario. È anche possibile passare parametri facoltativi per specificare se il report di recapito deve essere abilitato e impostare tag personalizzati.

```csharp
Response<IEnumerable<SmsSendResult>> response = smsClient.Send(
    from: "<from-phone-number>",
    to: new string[] { "<to-phone-number-1>", "<to-phone-number-2>" },
    message: "Weekly Promotion!",
    options: new SmsSendOptions(enableDeliveryReport: true) // OPTIONAL
    {
        Tag = "marketing", // custom tags
    });

IEnumerable<SmsSendResult> results = response.Value;
foreach (SmsSendResult result in results)
{
    Console.WriteLine($"Sms id: {result.MessageId}");
    Console.WriteLine($"Send Result Successful: {result.Successful}");
}
```

È necessario sostituire `<from-phone-number>` con un numero di telefono abilitato per SMS associato alla risorsa servizi di comunicazione `<to-phone-number-1>` e `<to-phone-number-2>` con i numeri di telefono a cui si desidera inviare un messaggio.

> [!WARNING]
> Si noti che i numeri di telefono devono essere specificati nel formato standard internazionale E.164, (ad esempio: + 14255550123).

Il parametro `enableDeliveryReport` è facoltativo ed è possibile usarlo per configurare i report di recapito. È utile per gli scenari in cui si vogliono generare eventi quando vengono recapitati messaggi SMS. Per configurare i report di recapito per i messaggi SMS, vedere la guida di avvio rapido [Gestire gli eventi SMS](../handle-sms-events.md).

`Tag` viene utilizzato per applicare un tag al report di recapito

## <a name="run-the-code"></a>Eseguire il codice

Eseguire l'applicazione dalla directory dell'applicazione con il comando `dotnet run`.

```console
dotnet run
```

## <a name="sample-code"></a>Codice di esempio

È possibile scaricare l'app di esempio da [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/SendSMS)
