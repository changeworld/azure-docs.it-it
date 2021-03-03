---
title: Usare identità gestite in servizi di comunicazione (.NET)
titleSuffix: An Azure Communication Services quickstart
description: Le identità gestite consentono di autorizzare l'accesso ai servizi di comunicazione di Azure da applicazioni in esecuzione in macchine virtuali di Azure, app per le funzioni e altre risorse.
services: azure-communication-services
author: stefang931
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 12/04/2020
ms.author: gistefan
ms.reviewer: mikben
ms.openlocfilehash: 7e8d9b56077819fc404d6c2bdc39f9f697224136
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692182"
---
# <a name="use-managed-identities-net"></a>Usare identità gestite (.NET)

Introduzione ai servizi di comunicazione di Azure usando identità gestite in .NET. Le librerie client SMS e di amministrazione di Communication Services supportano l'autenticazione Azure Active Directory (Azure AD) con [identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Questa Guida introduttiva illustra come autorizzare l'accesso alle librerie client di amministrazione e SMS da un ambiente Azure che supporta le identità gestite. Viene inoltre descritto come testare il codice in un ambiente di sviluppo.

## <a name="prerequisites"></a>Prerequisiti

 - Un account Azure con una sottoscrizione attiva. [Crea gratuitamente un account](https://azure.microsoft.com/free)
 - Una stringa di connessione e una risorsa attiva di Servizi di comunicazione. [Creare una risorsa di Servizi di comunicazione](./create-communication-resource.md?pivots=platform-azp&tabs=windows).

## <a name="setting-up"></a>Configurazione

### <a name="enable-managed-identities-on-a-virtual-machine-or-app-service"></a>Abilitare le identità gestite in una macchina virtuale o in un servizio app

Le identità gestite devono essere abilitate nelle risorse di Azure che si sta autorizzando. Per informazioni su come abilitare identità gestite per le risorse di Azure, vedere uno di questi articoli:

- [Azure portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfaccia della riga di comando di Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modello di Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Librerie client di Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [Servizi app](../../app-service/overview-managed-identity.md)

#### <a name="assign-azure-roles-with-the-azure-portal"></a>Assegnare i ruoli di Azure con il portale di Azure

1. Passare al portale di Azure.
1. Passare alla risorsa del servizio di comunicazione di Azure.
1. Passare a controllo di accesso (IAM) menu-> + Aggiungi-> Aggiungi assegnazione di ruolo.
1. Selezionare il ruolo "collaboratore" (questo è l'unico ruolo supportato).
1. Selezionare "identità gestita assegnata dall'utente" o "identità gestita assegnata dal sistema", quindi selezionare l'identità desiderata. Salvare la selezione.

![Ruolo identità gestita](media/managed-identity-assign-role.png)

#### <a name="assign-azure-roles-with-powershell"></a>Assegnare i ruoli di Azure con PowerShell

Per assegnare ruoli e autorizzazioni tramite PowerShell, vedere [aggiungere o rimuovere assegnazioni di ruolo di Azure usando Azure PowerShell](../../../articles/role-based-access-control/role-assignments-powershell.md)

## <a name="add-managed-identity-to-your-communication-services-solution"></a>Aggiungere identità gestite alla soluzione Servizi di comunicazione

### <a name="install-the-client-library-packages"></a>Installare i pacchetti della libreria client

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Configuration
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>Usare i pacchetti della libreria client

Aggiungere le `using` direttive seguenti al codice per usare le librerie di identità di Azure e client di archiviazione di Azure.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Configuration;
using Azure.Communication.Sms;
```

Gli esempi seguenti usano [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). Questa credenziale è adatta per ambienti di produzione e di sviluppo.

### <a name="create-an-identity-and-issue-a-token"></a>Creare un'identità ed emettere un token

Nell'esempio di codice seguente viene illustrato come creare un oggetto client del servizio con token di Azure Active Directory, quindi utilizzare il client per emettere un token per un nuovo utente:

```csharp
     public async Task<Response<CommunicationUserToken>> CreateIdentityAndIssueTokenAsync(Uri resourceEdnpoint) 
     {
          TokenCredential credential = new DefaultAzureCredential();
     
          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();
     
          var tokenResponse = await client.IssueTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-azure-active-directory-tokens"></a>Invia un SMS con Azure Active Directory token

Nell'esempio di codice seguente viene illustrato come creare un oggetto client del servizio con token di Azure Active Directory, quindi utilizzare il client per inviare un messaggio SMS:

```csharp

     public async Task SendSmsAsync(Uri resourceEndpoint, PhoneNumber from, PhoneNumber to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
     
          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SendSmsOptions { EnableDeliveryReport = true } // optional
          );
     }
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sull'autenticazione](../concepts/authentication.md)

Può essere utile vedere anche gli articoli seguenti:

- [Altre informazioni sul controllo degli accessi in base al ruolo di Azure](../../../articles/role-based-access-control/index.yml)
- [Altre informazioni su Azure Identity Library per .NET](/dotnet/api/overview/azure/identity-readme)
- [Creazione dei token di accesso utente](../quickstarts/access-tokens.md)
- [Inviare SMS](../quickstarts/telephony-sms/send.md)
- [Altre informazioni sugli SMS](../concepts/telephony-sms/concepts.md)