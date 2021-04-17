---
title: Creare un account Purview con .NET SDK
description: Creare un account Azure Purview usando .NET SDK.
author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 4/2/2021
ms.author: nayenama
ms.openlocfilehash: b3dc7bf8ac7650a7219c15a09a31d4dcf84a40bf
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587800"
---
# <a name="quickstart-create-a-purview-account-using-net-sdk"></a>Guida introduttiva: Creare un account Purview con .NET SDK

Questa guida introduttiva descrive come usare .NET SDK per creare un account Azure Purview 

> [!IMPORTANT]
> Azure Purview è attualmente in anteprima. Le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) includono termini legali aggiuntivi che si applicano a funzionalità di Azure in versione beta, in anteprima o in altro modo non ancora disponibili a livello generale.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [tenant di Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* L'account deve avere l'autorizzazione per creare risorse nella sottoscrizione

* Se sono impostati **Criteri di Azure** che impediscono a tutte le applicazioni di creare un **account di archiviazione** e uno **spazio dei nomi EventHub**, è necessario creare un'eccezione dei criteri usando un tag, che può essere immesso durante il processo di creazione di un account Purview. Il motivo principale è che per ogni account Purview creato occorre creare un gruppo di risorse gestito e, all'interno di tale gruppo, un account di archiviazione e uno spazio dei nomi EventHub. Per altre informazioni, vedere [Creare il portale del catalogo](create-catalog-portal.md)

### <a name="visual-studio"></a>Visual Studio

Nella procedura guidata illustrata in questo articolo viene usato Visual Studio 2019. Le procedure per Visual Studio 2013, 2015 o 2017 sono leggermente diverse.

### <a name="azure-net-sdk"></a>Azure .NET SDK

Scaricare e installare [Azure .NET SDK](https://azure.microsoft.com/downloads/) nel computer.

## <a name="create-an-application-in-azure-active-directory"></a>Creare un'applicazione in Azure Active Directory

Dalle sezioni in *Procedura: Usare il portale per creare un'entità servizio e applicazione di Azure AD che possano accedere alle risorse*, seguire le istruzioni per eseguire queste attività:

1. In [Creare un'applicazione Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) creare un'applicazione che rappresenti l'applicazione .NET che verrà creata in questa esercitazione. Come URL di accesso è possibile specificare un URL fittizio, come illustrato nell'articolo (`https://contoso.org/exampleapp`).
2. In [Ottenere i valori per l'accesso](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) ottenere l'**ID dell'applicazione** e l'**ID del tenant**, quindi prendere nota di questi valori che verranno usati più avanti in questa esercitazione. 
3. In [Certificati e segreti](../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) ottenere la **chiave di autenticazione** e prendere nota di questo valore che verrà usato più avanti in questa esercitazione.
4. In [Assegnare l'applicazione a un ruolo](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) assegnare l'applicazione al ruolo **Collaboratore** a livello di sottoscrizione, in modo che l'applicazione possa creare le data factory nella sottoscrizione.

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

Creare quindi un'applicazione console C# .NET in Visual Studio:

1. Avviare **Visual Studio**.
2. Nella finestra di avvio selezionare **Crea un nuovo progetto** > **App console (.NET Framework)** . È necessario .NET versione 4.5.2 o successiva.
3. In **Nome progetto** immettere **PurviewQuickStart.**
4. Selezionare **Crea** per creare il progetto.

## <a name="install-nuget-packages"></a>Installare i pacchetti NuGet

1. Fare clic su **Strumenti** > **Gestione Pacchetti NuGet** > **Console di Gestione pacchetti**.
2. Nel riquadro **Console di Gestione pacchetti** eseguire questi comandi per installare i pacchetti. Per altre informazioni, vedere il [pacchetto NuGet Microsoft.Azure.Management.Purview](https://www.nuget.org/packages/Microsoft.Azure.Management.Purview/).

    ```powershell
    Install-Package Microsoft.Azure.Management.Purview
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-purview-client"></a>Creare un client Purview

1. Aprire **Program.cs** e includere le istruzioni seguenti per aggiungere riferimenti agli spazi dei nomi.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
      using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.Purview;
      using Microsoft.Azure.Management.Purview.Models;
      using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Aggiungere il codice seguente al metodo **Main** per impostare le variabili. Sostituire i segnaposto con i valori personalizzati. Per un elenco delle aree di Azure in cui Purview è attualmente disponibile, cercare in **Azure Purview** e selezionare le aree di interesse nella pagina seguente: Prodotti disponibili per [area](https://azure.microsoft.com/global-infrastructure/services/).

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string purviewAccountName = 
       "<specify the name of purview account to create. It must be globally unique.>";
   ```

3. Aggiungere il codice seguente al **metodo Main** che crea un'istanza della **classe PurviewManagementClient.** Questo oggetto viene utilizzato per creare un account Purview.

   ```csharp
   // Authenticate and create a purview management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
   "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new PurviewManagementClient(cred)
   {
      SubscriptionId = subscriptionId           
   };
   ```

## <a name="create-a-purview-account"></a>Creare un account Purview

Aggiungere il codice seguente al **metodo Main** che crea un **account Purview.** 

```csharp
    // Create a purview Account
    Console.WriteLine("Creating Purview Account " + purviewAccountName + "...");
    Account account = new Account()
    {
    Location = region,
    Identity = new Identity(type: "SystemAssigned"),
    Sku = new AccountSku(name: "Standard", capacity: 4)
    };            
    try
    {
      client.Accounts.CreateOrUpdate(resourceGroup, purviewAccountName, account);
      Console.WriteLine(client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState);                
    }
    catch (ErrorResponseModelException purviewException)
    {
      Console.WriteLine(purviewException.StackTrace);
    }
    Console.WriteLine(
      SafeJsonConvert.SerializeObject(account, client.SerializationSettings));
    while (client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState ==
           "PendingCreation")
    {
      System.Threading.Thread.Sleep(1000);
    }
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
```

## <a name="run-the-code"></a>Eseguire il codice

Compilare e avviare l'applicazione, quindi verificare l'esecuzione.

La console stampa lo stato di avanzamento della creazione dell'account Purview.

### <a name="sample-output"></a>Output di esempio

```json
Creating Purview Account testpurview...
Succeeded
{
  "sku": {
    "capacity": 4,
    "name": "Standard"
  },
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "southcentralus"
}

Press any key to exit...
```

## <a name="verify-the-output"></a>Verificare l'output

Passare alla pagina **Purview accounts** (Visualizza account) nel [portale di Azure](https://portal.azure.com) e verificare l'account creato usando il codice precedente. 

## <a name="delete-purview-account"></a>Eliminare l'account Purview

Per eliminare un account Purview a livello di codice, aggiungere le righe di codice seguenti al programma: 

```csharp
    Console.WriteLine("Deleting the Purview Account");
    client.Accounts.Delete(resourceGroup, purviewAccountName);
```

## <a name="check-if-purview-account-name-is-available"></a>Controllare se il nome dell'account Purview è disponibile

Per controllare la disponibilità di un account purview, usare il codice seguente: 

```csharp
    CheckNameAvailabilityRequest checkNameAvailabilityRequest = new CheckNameAvailabilityRequest()
    {
      Name = purviewAccountName,
      Type =  "Microsoft.Purview/accounts"
    };
    Console.WriteLine("Check Purview account name");
    Console.WriteLine(client.Accounts.CheckNameAvailability(checkNameAvailabilityRequest).NameAvailable);
```

Il codice precedente con stampa 'True' se il nome è disponibile e 'False' se il nome non è disponibile.


## <a name="next-steps"></a>Passaggi successivi

Il codice di questa esercitazione crea un account di visualizzazione, elimina un account di visualizzazione e verifica la disponibilità del nome dell'account di visualizzazione. È ora possibile scaricare .NET SDK e ottenere informazioni sulle altre azioni del provider di risorse che è possibile eseguire per un account Purview.

Passare all'articolo successivo per informazioni su come consentire agli utenti di accedere all'account Azure Purview. 

> [!div class="nextstepaction"]
> [Aggiungere utenti all'account Azure Purview](catalog-permissions.md)
