---
title: Gestire gli utenti con l'API Microsoft Graph
titleSuffix: Azure AD B2C
description: Come gestire gli utenti in un tenant di Azure AD B2C chiamando l'API Microsoft Graph e usando un'identità dell'applicazione per automatizzare il processo.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/13/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff3cd858de86d21637f4a7a9ab9d9a83c7022f5a
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178875"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Gestire gli account utente Azure AD B2C con Microsoft Graph

Microsoft Graph consente di gestire gli account utente nella directory Azure AD B2C fornendo metodi di creazione, lettura, aggiornamento ed eliminazione nell'API Microsoft Graph. È possibile eseguire la migrazione di un archivio utente esistente a un tenant di Azure AD B2C ed eseguire altre operazioni di gestione degli account utente chiamando l'API Microsoft Graph.

Nelle sezioni seguenti vengono presentati gli aspetti chiave di Azure AD B2C la gestione degli utenti con l'API Microsoft Graph. Le operazioni, i tipi e le proprietà dell'API Microsoft Graph presentati di seguito sono un subset di che viene visualizzato nella documentazione di riferimento per l'API Microsoft Graph.

## <a name="register-a-management-application"></a>Registrare un'applicazione di gestione

Prima che qualsiasi applicazione di gestione utente o script scritto possa interagire con le risorse nel tenant di Azure AD B2C, è necessaria la registrazione di un'applicazione che conceda le autorizzazioni necessarie.

Per creare una registrazione dell'applicazione utilizzabile dall'applicazione di gestione, attenersi alla procedura descritta in questo articolo:

[Gestire Azure AD B2C con Microsoft Graph](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Operazioni di Microsoft Graph gestione utenti

Le seguenti operazioni di gestione degli utenti sono disponibili nell' [API Microsoft Graph](/graph/api/resources/user):

- [Ottenere un elenco di utenti](/graph/api/user-list)
- [Creare un utente](/graph/api/user-post-users)
- [Ottenere un utente](/graph/api/user-get)
- [Aggiornare un utente](/graph/api/user-update)
- [Eliminare un utente](/graph/api/user-delete)


## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Esempio di codice: come gestire gli account utente a livello di codice

Questo esempio di codice è un'applicazione console .NET Core che usa l' [SDK Microsoft Graph](/graph/sdks/sdks-overview) per interagire con Microsoft Graph API. Il codice illustra come chiamare l'API per gestire gli utenti a livello di codice in un tenant Azure AD B2C.
È possibile [scaricare l'archivio di esempio](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*. zip), [esplorare il repository](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) in GitHub o clonare il repository:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Dopo aver ottenuto l'esempio di codice, configurarlo per l'ambiente e quindi compilare il progetto:

1. Aprire il progetto in [Visual Studio](https://visualstudio.microsoft.com) o in [Visual Studio Code](https://code.visualstudio.com).
1. Aprire `src/appsettings.json`.
1. Nella `appSettings` sezione sostituire `your-b2c-tenant` con il nome del tenant e `Application (client) ID` e `Client secret` con i valori per la registrazione dell'applicazione di gestione (vedere la sezione [registrare un'applicazione di gestione](#register-a-management-application) di questo articolo).
1. Aprire una finestra della console all'interno del clone locale del repository, passare alla `src` Directory e quindi compilare il progetto:
    ```console
    cd src
    dotnet build
    ```
1. Eseguire l'applicazione con il comando `dotnet`:

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

L'applicazione visualizza un elenco di comandi che è possibile eseguire. Ad esempio, ottenere tutti gli utenti, ottenere un singolo utente, eliminare un utente, aggiornare la password di un utente ed eseguire l'importazione bulk.

### <a name="code-discussion"></a>Discussione sul codice

Il codice di esempio usa il [Microsoft Graph SDK](/graph/sdks/sdks-overview), progettato per semplificare la creazione di applicazioni di alta qualità, efficienti e resilienti che accedono a Microsoft Graph.

Qualsiasi richiesta all'API Microsoft Graph richiede un token di accesso per l'autenticazione. La soluzione USA il pacchetto NuGet [Microsoft. Graph. auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) che fornisce un wrapper basato sullo scenario di autenticazione di Microsoft Authentication Library (MSAL) per l'uso con il Microsoft Graph SDK.

Il `RunAsync` metodo nel file _Program.cs_ :

1. Legge le impostazioni dell'applicazione dal _appsettings.jssul_ file
1. Inizializza il provider di autenticazione utilizzando il flusso di [concessione delle credenziali client OAuth 2,0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) . Con il flusso di concessione delle credenziali client, l'app è in grado di ottenere un token di accesso per chiamare l'API Microsoft Graph.
1. Configura il client del servizio Microsoft Graph con il provider di autenticazione:

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

Il *GraphServiceClient* inizializzato viene quindi usato in _UserService.cs_ per eseguire le operazioni di gestione degli utenti. Ad esempio, ottenere un elenco degli account utente nel tenant:

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[Eseguire chiamate API usando gli sdk Microsoft Graph](/graph/sdks/create-requests) include informazioni su come leggere e scrivere informazioni da Microsoft Graph, usare `$select` per controllare le proprietà restituite, fornire parametri di query personalizzati e usare i `$filter` `$orderBy` parametri di query e.

## <a name="next-steps"></a>Passaggi successivi

Per un indice completo delle operazioni dell'API Microsoft Graph supportate per le risorse Azure AD B2C, vedere [Microsoft Graph operazioni disponibili per Azure ad B2C](microsoft-graph-operations.md).

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
