---
title: Gestire le risorse con Microsoft Graph
titleSuffix: Azure AD B2C
description: Come gestire le risorse in un tenant Azure AD B2C chiamando l'API Microsoft Graph e usando un'identità dell'applicazione per automatizzare il processo.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 884cb0c30bc754366fda79a4b54b977517fbadd3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530539"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Gestire Azure AD B2C con Microsoft Graph

Microsoft Graph consente di gestire le risorse nella directory Azure AD B2C locale. Le operazioni Microsoft Graph API seguenti sono supportate per la gestione delle risorse Azure AD B2C, tra cui utenti, provider di identità, flussi utente, criteri personalizzati e chiavi dei criteri. Ogni collegamento nelle sezioni seguenti ha come destinazione la pagina corrispondente all'interno Microsoft Graph riferimento all'API per tale operazione. 

## <a name="prerequisites"></a>Prerequisiti

Per usare MS API Graph e interagire con le risorse nel tenant Azure AD B2C, è necessaria una registrazione dell'applicazione che concedi le autorizzazioni a tale scopo. Seguire i passaggi nell'articolo [Gestire Azure AD B2C con Microsoft Graph](microsoft-graph-get-started.md) per creare una registrazione dell'applicazione che l'applicazione di gestione può usare. 

## <a name="user-management"></a>Gestione degli utenti

- [Elencare gli utenti](/graph/api/user-list)
- [Creare un utente consumer](/graph/api/user-post-users)
- [Ottenere un utente](/graph/api/user-get)
- [Aggiornare un utente](/graph/api/user-update)
- [Eliminare un utente](/graph/api/user-delete)

## <a name="user-phone-number-management-beta"></a>Gestione dei numeri di telefono utente (beta)

Numero di telefono che può essere usato da un utente per l'accesso tramite SMS o [chiamate vocali](identity-provider-local.md#phone-sign-in-preview)o [l'autenticazione a più fattori.](multi-factor-authentication.md) Per altre informazioni, vedere [l'API Azure AD metodi di autenticazione.](/graph/api/resources/phoneauthenticationmethod)

- [Aggiungere](/graph/api/authentication-post-phonemethods)
- [Elenco](/graph/api/authentication-list-phonemethods)
- [Ottieni](/graph/api/phoneauthenticationmethod-get)
- [Aggiornamento](/graph/api/phoneauthenticationmethod-update)
- [Elimina](/graph/api/phoneauthenticationmethod-delete)

Si noti che [l'operazione di](/graph/api/authentication-list-phonemethods) elenco restituisce solo i numeri di telefono abilitati. Il numero di telefono seguente deve essere abilitato per l'uso con le operazioni di elenco. 

![Abilitare l'accesso tramite telefono](./media/microsoft-graph-operations/enable-phone-sign-in.png)

## <a name="self-service-password-reset-email-address-beta"></a>Indirizzo di posta elettronica per la reimpostazione della password self-service (beta)

Indirizzo di posta elettronica che può essere usato da un [account di](identity-provider-local.md#username-sign-in) accesso con nome utente per reimpostare la password. Per altre informazioni, vedere API [Azure AD metodi di autenticazione.](/graph/api/resources/emailauthenticationmethod)

- [Aggiungere](/graph/api/emailauthenticationmethod-post)
- [Elenco](/graph/api/emailauthenticationmethod-list)
- [Ottieni](/graph/api/emailauthenticationmethod-get)
- [Aggiornamento](/graph/api/emailauthenticationmethod-update)
- [Elimina](/graph/api/emailauthenticationmethod-delete)

## <a name="identity-providers"></a>Provider di identità

Gestire i [provider di identità](add-identity-provider.md) disponibili per i flussi utente nel tenant Azure AD B2C aziendale.

- [Elencare i provider di identità registrati nel tenant Azure AD B2C](/graph/api/identityprovider-list)
- [Creare un provider di identità](/graph/api/identityprovider-post-identityproviders)
- [Ottenere un provider di identità](/graph/api/identityprovider-get)
- [Aggiornare il provider di identità](/graph/api/identityprovider-update)
- [Eliminare un provider di identità](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Flusso utente

Configurare criteri predefiniti per l'iscrizione, l'accesso, l'iscrizione e l'accesso combinati, la reimpostazione della password e l'aggiornamento del profilo.

- [Elencare i flussi utente](/graph/api/identitycontainer-list-b2cuserflows)
- [Creare un flusso utente](/graph/api/identitycontainer-post-b2cuserflows)
- [Ottenere un flusso utente](/graph/api/b2cidentityuserflow-get)
- [Eliminare un flusso utente](/graph/api/b2cidentityuserflow-delete)

## <a name="user-flow-authentication-methods-beta"></a>Metodi di autenticazione del flusso utente (beta)

Scegliere un meccanismo per consentire agli utenti di registrarsi tramite account locali. Gli account locali sono gli account in cui Azure AD esegue l'asserzione di identità. Per altre informazioni, vedere [tipo di risorsa b2cAuthenticationMethodsPolicy.](/graph/api/resources/b2cauthenticationmethodspolicy)

- [Ottieni](/graph/api/b2cauthenticationmethodspolicy-get)
- [Aggiornamento](/graph/api/b2cauthenticationmethodspolicy-update)

## <a name="custom-policies"></a>Criteri personalizzati

Le operazioni seguenti consentono di gestire i criteri Azure AD B2C Trust Framework, noti come [criteri personalizzati.](custom-policy-overview.md)

- [Elencare tutti i criteri del framework attendibilità configurati in un tenant](/graph/api/trustframework-list-trustframeworkpolicies)
- [Creare criteri del framework attendibilità](/graph/api/trustframework-post-trustframeworkpolicy)
- [Leggere le proprietà di un criterio del framework di attendibilità esistente](/graph/api/trustframeworkpolicy-get)
- [Aggiornare o creare criteri del framework di attendibilità.](/graph/api/trustframework-put-trustframeworkpolicy)
- [Eliminare un criterio del framework di attendibilità esistente](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Chiavi dei criteri

L Identity Experience Framework archivia i segreti a cui si fa riferimento in un criterio personalizzato per stabilire l'attendibilità tra i componenti. Questi segreti possono essere chiavi/valori simmetrici o asimmetrici. Nell'portale di Azure, queste entità vengono visualizzate come **Chiavi dei criteri**.

La risorsa di primo livello per le chiavi dei criteri nell'API Microsoft Graph è [trusted framework keyset](/graph/api/resources/trustframeworkkeyset). Ogni **keyset** contiene almeno una **chiave**. Per creare una chiave, creare prima un keyset vuoto e quindi generare una chiave nel keyset. È possibile creare un segreto manuale, caricare un certificato o una chiave PKCS12. La chiave può essere un segreto generato, una stringa (ad esempio il segreto dell'applicazione Facebook) o un certificato caricato. Se un keyset ha più chiavi, è attiva solo una delle chiavi.

### <a name="trust-framework-policy-keyset"></a>Keyset di criteri di Trust Framework

- [Elencare i set di chiavi del framework di attendibilità](/graph/api/trustframework-list-keysets)
- [Creare set di chiavi del framework di attendibilità](/graph/api/trustframework-post-keysets)
- [Ottenere un keyset](/graph/api/trustframeworkkeyset-get)
- [Aggiornare i set di chiavi di un framework di attendibilità](/graph/api/trustframeworkkeyset-update)
- [Eliminare i set di chiavi di un framework di attendibilità](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Chiave dei criteri di Trust Framework

- [Ottenere la chiave attualmente attiva nel keyset](/graph/api/trustframeworkkeyset-getactivekey)
- [Generare una chiave nel keyset](/graph/api/trustframeworkkeyset-generatekey)
- [Caricare un segreto basato su stringa](/graph/api/trustframeworkkeyset-uploadsecret)
- [Caricare un certificato X.509](/graph/api/trustframeworkkeyset-uploadcertificate)
- [Caricare un certificato in formato PKCS12](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Applicazioni

- [Elenco applicazioni](/graph/api/application-list)
- [Creare un'applicazione](/graph/api/resources/application)
- [Update application](/graph/api/application-update)
- [Creare servicePrincipal](/graph/api/resources/serviceprincipal)
- [Creare una concessione oauth2Permission](/graph/api/resources/oauth2permissiongrant)
- [Eliminare l'applicazione](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Proprietà dell'estensione dell'applicazione

- [Elencare le proprietà dell'estensione](/graph/api/application-list-extensionproperty)

Azure AD B2C fornisce una directory che può contenere 100 attributi personalizzati per utente. Per i flussi utente, queste proprietà di estensione [vengono gestite tramite l'portale di Azure](user-flow-custom-attributes.md). Per i criteri personalizzati, Azure AD B2C la proprietà automaticamente, la prima volta che il criterio scrive un valore nella proprietà di estensione.

## <a name="audit-logs"></a>Log di controllo

- [Elencare i log di controllo](/graph/api/directoryaudit-list)

Per altre informazioni sull'accesso Azure AD B2C log di controllo, vedere Accesso Azure AD B2C [log di controllo.](view-audit-logs.md)

## <a name="conditional-access"></a>Accesso condizionale

- [Elencare tutti i criteri di accesso condizionale](/graph/api/conditionalaccessroot-list-policies?tabs=http)
- [Leggere le proprietà e le relazioni di un criterio di accesso condizionale](/graph/api/conditionalaccesspolicy-get)
- [Creare nuovi criteri di accesso condizionale](/graph/api/resources/application)
- [Aggiornare un criterio di accesso condizionale](/graph/api/conditionalaccesspolicy-update)
- [Eliminare un criterio di accesso condizionale](/graph/api/conditionalaccesspolicy-delete)

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Esempio di codice: Come gestire gli account utente a livello di codice

Questo esempio di codice è un'applicazione console .NET Core che usa [Microsoft Graph SDK per](/graph/sdks/sdks-overview) interagire con Microsoft Graph API. Il codice illustra come chiamare l'API per gestire gli utenti in un tenant Azure AD B2C a livello di codice.
È possibile [scaricare l'archivio di esempio](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*.zip), esplorare il [repository](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) in GitHub o clonare il repository:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Dopo aver ottenuto l'esempio di codice, configurarlo per l'ambiente e quindi compilare il progetto:

1. Aprire il progetto in [Visual Studio](https://visualstudio.microsoft.com) o [Visual Studio Code](https://code.visualstudio.com).
1. Aprire `src/appsettings.json`.
1. Nella sezione sostituire con il nome del tenant e con i valori per la registrazione `appSettings` `your-b2c-tenant` dell'applicazione di `Application (client) ID` `Client secret` gestione. Per altre informazioni, vedere [Registrare un'Microsoft Graph app.](microsoft-graph-get-started.md)
1. Aprire una finestra della console all'interno del clone locale del repo, passare alla `src` directory e quindi compilare il progetto:

    ```console
    cd src
    dotnet build
    ```
    
1. Eseguire l'applicazione con il comando `dotnet`:

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

L'applicazione visualizza un elenco di comandi che è possibile eseguire. Ad esempio, ottenere tutti gli utenti, ottenere un singolo utente, eliminare un utente, aggiornare la password di un utente e eseguire l'importazione in blocco.

### <a name="code-discussion"></a>Discussione sul codice

Il codice di esempio usa [Microsoft Graph SDK](/graph/sdks/sdks-overview), progettato per semplificare la creazione di applicazioni di alta qualità, efficienti e resilienti che accedono Microsoft Graph.

Qualsiasi richiesta all'API Microsoft Graph richiede un token di accesso per l'autenticazione. La soluzione usa il pacchetto [NuGet Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) che fornisce un wrapper basato su scenari di autenticazione di Microsoft Authentication Library (MSAL) da usare con Microsoft Graph SDK.

Il `RunAsync` metodo nel file _Program.cs:_

1. Legge le impostazioni dell'applicazione _dalappsettings.jsfile_
1. Inizializza il provider di autenticazione usando il flusso di concessione delle credenziali [client OAuth 2.0.](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) Con il flusso di concessione delle credenziali client, l'app è in grado di ottenere un token di accesso per chiamare l'API Microsoft Graph client.
1. Configura il client Microsoft Graph client del servizio con il provider di autenticazione:

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

*GraphServiceClient inizializzato viene* quindi usato in _UserService.cs_ per eseguire le operazioni di gestione degli utenti. Ad esempio, ottenere un elenco degli account utente nel tenant:

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

Effettuare chiamate API usando gli [SDK di Microsoft Graph](/graph/sdks/create-requests) include informazioni su come leggere e scrivere informazioni da Microsoft Graph, usare per controllare le proprietà restituite, fornire parametri di query personalizzati e usare i parametri di query e `$select` `$filter` `$orderBy` .

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
