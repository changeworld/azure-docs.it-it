---
title: Gestire le risorse con Microsoft Graph
titleSuffix: Azure AD B2C
description: Come gestire le risorse in un tenant di Azure AD B2C chiamando l'API Microsoft Graph e usando un'identità dell'applicazione per automatizzare il processo.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1dc5b8dc8930d75456f307324ef97bd60e78eca9
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660158"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Gestire Azure AD B2C con Microsoft Graph

Microsoft Graph consente di gestire le risorse nella directory Azure AD B2C. Per la gestione delle risorse Azure AD B2C, inclusi gli utenti, i provider di identità, i flussi utente, i criteri personalizzati e le chiavi dei criteri, sono supportate le seguenti operazioni di Microsoft Graph API. Ogni collegamento nelle sezioni seguenti è destinato alla pagina corrispondente all'interno del Microsoft Graph riferimento API per l'operazione. 

## <a name="perquisites"></a>Prerequisiti

Per usare MS API Graph e interagire con le risorse nel tenant di Azure AD B2C, è necessaria una registrazione dell'applicazione che conceda le autorizzazioni necessarie. Seguire i passaggi descritti nell'articolo [gestire Azure ad B2C con Microsoft Graph](microsoft-graph-get-started.md) per creare una registrazione dell'applicazione che può essere usata dall'applicazione di gestione. 

## <a name="user-management"></a>Gestione degli utenti

- [Elencare gli utenti](/graph/api/user-list)
- [Creazione di un utente consumer](/graph/api/user-post-users)
- [Ottenere un utente](/graph/api/user-get)
- [Aggiornare un utente](/graph/api/user-update)
- [Eliminare un utente](/graph/api/user-delete)

## <a name="user-phone-number-management"></a>Gestione dei numeri di telefono dell'utente

- [Aggiungere](/graph/api/authentication-post-phonemethods)
- [Ottieni](/graph/api/b2cauthenticationmethodspolicy-get)
- [Aggiornamento](/graph/api/b2cauthenticationmethodspolicy-update)
- [Elimina](/graph/api/phoneauthenticationmethod-delete)

Per ulteriori informazioni sulla gestione del numero di telefono dell'accesso dell'utente, vedere [metodi di autenticazione B2C](/graph/api/resources/b2cauthenticationmethodspolicy).

## <a name="identity-providers-user-flow"></a>Provider di identità (flusso utente)

Gestire i provider di identità disponibili per i flussi utente nel tenant del Azure AD B2C.

- [Elencare i provider di identità registrati nel tenant di Azure AD B2C](/graph/api/identityprovider-list)
- [Creazione di un provider di identità](/graph/api/identityprovider-post-identityproviders)
- [Ottenere un provider di identità](/graph/api/identityprovider-get)
- [Aggiorna provider di identità](/graph/api/identityprovider-update)
- [Eliminare un provider di identità](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Flusso utente

Configurare criteri predefiniti per l'iscrizione, l'accesso, l'iscrizione e l'accesso combinati, la reimpostazione della password e l'aggiornamento del profilo.

- [Elencare i flussi utente](/graph/api/identitycontainer-list-b2cuserflows)
- [Creare un flusso utente](/graph/api/identitycontainer-post-b2cuserflows)
- [Ottenere un flusso utente](/graph/api/b2cidentityuserflow-get)
- [Eliminare un flusso utente](/graph/api/b2cidentityuserflow-delete)

## <a name="custom-policies"></a>Criteri personalizzati

Le operazioni seguenti consentono di gestire i criteri di Azure AD B2C Trust Framework, noti come [criteri personalizzati](custom-policy-overview.md).

- [Elencare tutti i criteri di Framework attendibilità configurati in un tenant](/graph/api/trustframework-list-trustframeworkpolicies)
- [Creare criteri di Framework attendibilità](/graph/api/trustframework-post-trustframeworkpolicy)
- [Leggere le proprietà di un criterio di Framework attendibilità esistente](/graph/api/trustframeworkpolicy-get)
- [Aggiornare o creare criteri di Framework attendibilità.](/graph/api/trustframework-put-trustframeworkpolicy)
- [Eliminare un criterio di Framework attendibilità esistente](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Chiavi dei criteri

Il Framework dell'esperienza di identità Archivia i segreti a cui si fa riferimento in un criterio personalizzato per stabilire una relazione di trust tra i componenti. Questi segreti possono essere chiavi/valori simmetriche o asimmetriche. Nel portale di Azure queste entità vengono visualizzate come chiavi dei **criteri**.

La risorsa di primo livello per le chiavi dei criteri nell'API Microsoft Graph è il [keyset del Framework attendibile](/graph/api/resources/trustframeworkkeyset). Ogni **Keyset** contiene almeno una **chiave**. Per creare una chiave, creare prima un keyset vuoto e quindi generare una chiave nel keyset. È possibile creare un segreto manuale, caricare un certificato o una chiave PKCS12. La chiave può essere un segreto generato, una stringa (ad esempio il segreto dell'applicazione Facebook) o un certificato caricato. Se un keyset dispone di più chiavi, solo una delle chiavi è attiva.

### <a name="trust-framework-policy-keyset"></a>Keyset criteri di Framework attendibilità

- [Elencare i set di impostazioni del Framework di attendibilità](/graph/api/trustframework-list-keysets)
- [Creare set di impostazioni di un Framework attendibilità](/graph/api/trustframework-post-keysets)
- [Ottenere un keyset](/graph/api/trustframeworkkeyset-get)
- [Aggiornare i set di impostazioni di un Framework attendibilità](/graph/api/trustframeworkkeyset-update)
- [Eliminare i set di impostazioni di un Framework attendibilità](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Chiave dei criteri di Framework attendibilità

- [Ottenere la chiave attualmente attiva nel keyset](/graph/api/trustframeworkkeyset-getactivekey)
- [Generare una chiave in keyset](/graph/api/trustframeworkkeyset-generatekey)
- [Carica un segreto basato su stringa](/graph/api/trustframeworkkeyset-uploadsecret)
- [Caricare un certificato X. 509](/graph/api/trustframeworkkeyset-uploadcertificate)
- [Carica un certificato in formato PKCS12](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Applicazioni

- [Elenco applicazioni](/graph/api/application-list)
- [Creare un'applicazione](/graph/api/resources/application)
- [Update application](/graph/api/application-update)
- [Crea servicePrincipal](/graph/api/resources/serviceprincipal)
- [Crea concessione oauth2Permission](/graph/api/resources/oauth2permissiongrant)
- [Elimina applicazione](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Proprietà dell'estensione dell'applicazione

- [Elencare le proprietà dell'estensione](/graph/api/application-list-extensionproperty)

Azure AD B2C fornisce una directory che può contenere 100 attributi personalizzati per utente. Per i flussi utente, queste proprietà di estensione vengono [gestite tramite il portale di Azure](user-flow-custom-attributes.md). Per i criteri personalizzati, Azure AD B2C crea automaticamente la proprietà, la prima volta che il criterio scrive un valore nella proprietà dell'estensione.

## <a name="audit-logs"></a>Log di controllo

- [Elencare i log di controllo](/graph/api/directoryaudit-list)

Per ulteriori informazioni sull'accesso ai log di controllo di Azure AD B2C, vedere [accesso ai log di controllo di Azure ad B2C](view-audit-logs.md).

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Esempio di codice: come gestire gli account utente a livello di codice

Questo esempio di codice è un'applicazione console .NET Core che usa l' [SDK Microsoft Graph](/graph/sdks/sdks-overview) per interagire con Microsoft Graph API. Il codice illustra come chiamare l'API per gestire gli utenti a livello di codice in un tenant Azure AD B2C.
È possibile [scaricare l'archivio di esempio](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*. zip), [esplorare il repository](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) in GitHub o clonare il repository:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Dopo aver ottenuto l'esempio di codice, configurarlo per l'ambiente e quindi compilare il progetto:

1. Aprire il progetto in [Visual Studio](https://visualstudio.microsoft.com) o in [Visual Studio Code](https://code.visualstudio.com).
1. Aprire `src/appsettings.json`.
1. Nella `appSettings` sezione sostituire `your-b2c-tenant` con il nome del tenant e `Application (client) ID` e `Client secret` con i valori per la registrazione dell'applicazione di gestione. Per ulteriori informazioni, vedere la pagina relativa alla [registrazione di un'applicazione Microsoft Graph](microsoft-graph-get-started.md).
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

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
