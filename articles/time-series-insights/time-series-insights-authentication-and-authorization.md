---
title: Autenticazione e autorizzazione API - Azure Time Series Insights | Microsoft Docs
description: Questo articolo descrive come configurare l'autenticazione e l'autorizzazione per un'applicazione personalizzata che chiama l'API Azure Time Series Insights.
ms.service: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/23/2021
ms.custom: seodec18, has-adal-ref, devx-track-azurecli
ms.openlocfilehash: 225e72bc00ce0a80ff655a76562b5c6b70b7fa79
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479492"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticazione e autorizzazione per l'API Azure Time Series Insights

A seconda delle esigenze aziendali, la soluzione potrebbe includere una o più applicazioni client usate per interagire con le API dell'ambiente di [Azure Time Series Insights.](/rest/api/time-series-insights/reference-data-access-overview) Azure Time Series Insights esegue l'autenticazione [Azure AD token di sicurezza basati su OAUTH 2.0.](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) Per autenticare i client, è necessario ottenere un bearer token con le autorizzazioni appropriate e passarlo insieme alle chiamate API. Questo documento descrive diversi metodi per ottenere le credenziali che è possibile usare per ottenere un bearer token ed eseguire l'autenticazione, tra cui l'uso dell'identità gestita e la registrazione Azure Active Directory'app.

## <a name="managed-identities"></a>Identità gestite

Le sezioni seguenti descrivono come usare un'identità gestita da Azure Active Directory (Azure AD) per accedere all'API Azure Time Series Insights. In Azure le identità gestite eliminano la necessità di gestire le credenziali fornendo un'identità per la risorsa di Azure in Azure AD che è possibile usare per ottenere i token di Azure Active Directory (Azure AD). Ecco alcuni vantaggi derivanti dall'uso delle identità gestite:

- Non è necessario gestire le credenziali. Le credenziali non sono neanche accessibili.
- È possibile usare le identità gestite per eseguire l'autenticazione con qualsiasi servizio di Azure che supporti l'autenticazione di Azure AD, incluso Azure Key Vault.
- Le identità gestite possono essere usate senza costi aggiuntivi.

Per altre informazioni sui due tipi di identità gestite, vedere [Che cosa sono le identità gestite per le risorse di Azure?](../active-directory/managed-identities-azure-resources/overview.md)

È possibile usare le identità gestite da:

- Macchine virtuali di Azure
- Servizi app di Azure
- Funzioni di Azure
- Istanze di Azure Container
- e altro ancora ...

Per [l'elenco completo, vedere Servizi](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) di Azure che supportano le identità gestite per le risorse di Azure.

## <a name="azure-active-directory-app-registration"></a>Registrazione dell'app Azure Active Directory

È consigliabile usare le identità gestite quando possibile in modo che non sia necessario gestire le credenziali. Se l'applicazione client non è ospitata in un servizio di Azure che supporta le identità gestite, è possibile registrare l'applicazione con un tenant Azure AD servizio. Quando si registra l'applicazione con Azure AD, si crea una configurazione di identità per l'applicazione che consente l'integrazione con Azure AD. Quando si registra un'app nel [portale di Azure](https://portal.azure.com/), si sceglie se si tratta di un tenant singolo (accessibile solo nel tenant) o multi-tenant (accessibile in altri tenant) e facoltativamente è possibile impostare un URI di reindirizzamento (a cui viene inviato il token di accesso).

Dopo aver completato la registrazione dell'app, si ha un'istanza univoca globale dell'app (l'oggetto applicazione) che si trova all'interno del tenant o della directory principale. Si ha anche un ID univoco globale per l'app (l'ID app o client). Nel portale è quindi possibile aggiungere segreti o certificati e ambiti per far funzionare l'app, personalizzare la personalizzazione dell'app nella finestra di dialogo di accesso e altro ancora.

Se si registra un'applicazione nel portale, nel tenant principale vengono creati automaticamente un oggetto applicazione e un oggetto entità servizio. Se si registra/crea un'applicazione usando le API Microsoft Graph, la creazione dell'oggetto entità servizio è un passaggio separato. Per richiedere i token è necessario un oggetto entità servizio.

Assicurarsi di esaminare l'elenco di [controllo di](../active-directory/develop/identity-platform-integration-checklist.md#security) sicurezza per l'applicazione. Come procedura consigliata, è consigliabile usare [le credenziali del certificato](../active-directory/develop/active-directory-certificate-credentials.md)e non le credenziali password (segreti client).

Per [altri dettagli,](../active-directory/develop/app-objects-and-service-principals.md) vedere Oggetti applicazione e oggetti entità Azure Active Directory servizio in Gestione applicazioni.

## <a name="step-1-create-your-managed-identity-or-app-registration"></a>Passaggio 1: Creare l'identità gestita o la registrazione dell'app

Dopo aver identificato se si sta usando un'identità gestita o la registrazione dell'app, il passaggio successivo consiste nel effettuarne il provisioning.

### <a name="managed-identity"></a>Identità gestita

I passaggi da eseguire per creare un'identità gestita variano a seconda della posizione del codice e del fatto che si crei o meno un'identità assegnata dal sistema o assegnata dall'utente. Per [comprendere la differenza, vedere](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) Tipi di identità gestita. Dopo aver selezionato il tipo di identità, individuare e seguire l'esercitazione corretta nella documentazione Azure AD identità gestite [dall'utente.](../active-directory/managed-identities-azure-resources/index.yml) Sono disponibili istruzioni su come configurare le identità gestite per:

- [Macchine virtuali di Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-during-creation-of-a-vm)
- [Servizio app e Funzioni di Azure](../app-service/overview-managed-identity.md)
- [Istanze di Azure Container](../container-instances/container-instances-managed-identity.md)
- e altro ancora...

### <a name="application-registration"></a>Registrazione dell'applicazione

Seguire i passaggi elencati in [Registrare un'applicazione.](../active-directory/develop/quickstart-register-app.md#register-an-application)

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="step-2-grant-access"></a>Passaggio 2: Concedere l'accesso

Quando l'ambiente Azure Time Series Insights riceve una richiesta, prima viene convalidato il bearer token del chiamante. Se la convalida viene superata, il chiamante è stato autenticato e viene eseguito un altro controllo per verificare che il chiamante sia autorizzato a eseguire l'azione richiesta. Per autorizzare qualsiasi utente o entità servizio, è innanzitutto necessario concedere loro l'accesso all'ambiente assegnando loro il ruolo Lettore o Collaboratore.

- Per concedere l'accesso tramite [l portale di Azure'interfaccia](https://portal.azure.com/) utente, seguire le istruzioni elencate nell'articolo Concedere l'accesso ai dati [a un ambiente.](concepts-access-policies.md) Quando si seleziona l'utente, è possibile cercare l'identità gestita o la registrazione dell'app in base al nome o all'ID.

- Per concedere l'accesso tramite l'interfaccia della riga di comando di Azure, eseguire il comando seguente. Vedere la documentazione [qui per](/cli/azure/ext/timeseriesinsights/tsi/access-policy) l'elenco completo dei comandi disponibili per gestire l'accesso.

   ```azurecli-interactive
   az tsi access-policy create --name "ap1" --environment-name "env1" --description "some description" --principal-object-id "aGuid" --roles Reader Contributor --resource-group "rg1"
   ```

> [!Note]
> L'estensione timeseriesinsights per l'interfaccia della riga di comando di Azure richiede la versione 2.11.0 o successiva. L'estensione verrà installata automaticamente la prima volta che si esegue un comando az tsi access-policy. [Altre informazioni](/cli/azure/azure-cli-extensions-overview) sulle estensioni.

## <a name="step-3-requesting-tokens"></a>Passaggio 3: Richiesta di token

Dopo aver effettuato il provisioning dell'identità gestita o della registrazione dell'app e aver assegnato un ruolo, è possibile iniziare a usarlo per richiedere i token di connessione OAuth 2.0. Il metodo utilizzato per ottenere un token varia a seconda della posizione in cui è ospitato il codice e del linguaggio scelto. Quando si specifica la risorsa (nota anche come "gruppo di destinatari" del token), è possibile identificare Azure Time Series Insights in base all'URL o al GUID:

* `https://api.timeseries.azure.com/`
* `120d688d-1518-4cf7-bd38-182f158850b6`

> [!IMPORTANT]
> Se si usa l'URL come ID risorsa, il token deve essere rilasciato esattamente a `https://api.timeseries.azure.com/` . La barra finale è obbligatoria.

> * Se si [usa Postman,](https://www.getpostman.com/) **AuthURL** sarà quindi: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` è valido, ma `https://api.timeseries.azure.com` non lo è.

### <a name="managed-identities"></a>Identità gestite

Quando si accede da Servizio app di Azure o Funzioni, seguire le indicazioni riportate in [Ottenere i token per le risorse di Azure](../app-service/overview-managed-identity.md).

Per le applicazioni e le funzioni .NET, il modo più semplice per usare un'identità gestita è tramite la libreria [client di Identità di Azure](/dotnet/api/overview/azure/identity-readme) per .NET. Questa libreria client è molto diffusa grazie alla semplicità e ai vantaggi per la sicurezza. Gli sviluppatori possono scrivere codice una sola volta e consentire alla libreria client di determinare come eseguire l'autenticazione in base all'ambiente dell'applicazione, indipendentemente dal fatto che in una workstation di sviluppo si utilizzi un account sviluppatore o distribuito in Azure usando un'identità del servizio gestita. Per indicazioni sulla migrazione dalla libreria AppAuthentication precedente, vedere [AppAuthentication in Azure.Identity Migration Guidance](/dotnet/api/overview/azure/app-auth-migration).

Richiedere un token per Azure Time Series Insights C# e la libreria client di Identità di Azure per .NET:

   ```csharp
   using Azure.Identity;
   // ...
   var credential = new DefaultAzureCredential();
   var token = credential.GetToken(
   new Azure.Core.TokenRequestContext(
       new[] { "https://api.timeseries.azure.com/" }));
   var accessToken = token.Token;
   ```

### <a name="app-registration"></a>Registrazione dell'app

* Gli sviluppatori possono usare [Microsoft Authentication Library](../active-directory/develop/msal-overview.md) (MSAL) per ottenere i token per le registrazioni di app.

MSAL può essere usato in molti scenari applicativi, tra cui, ma non solo:

* [Applicazioni a pagina singola (JavaScript)](../active-directory/develop/scenario-spa-overview.md)
* [Applicazione Web per l'accesso di un utente e la chiamata di un'API Web per conto dell'utente](../active-directory/develop/scenario-web-app-call-api-overview.md)
* [API Web che chiama un'altra API Web downstream per conto dell'utente che ha eseguito l'accesso](../active-directory/develop/scenario-web-api-call-api-overview.md)
* [Applicazione desktop che chiama un'API Web per conto dell'utente connesso](../active-directory/develop/scenario-desktop-overview.md)
* [Applicazione per dispositivi mobili che chiama un'API Web per conto dell'utente](../active-directory/develop/scenario-mobile-overview.md)che ha eseguito l'accesso in modo interattivo.
* [Applicazione daemon di servizio/desktop che chiama un'API Web per proprio conto](../active-directory/develop/scenario-daemon-overview.md)

Per il codice C# di esempio che illustra come acquisire un token come registrazione dell'app ed eseguire query sui dati da un ambiente Gen2, visualizzare l'app di esempio [in GitHub](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/Program.cs)

> [!IMPORTANT]
> Se si usa Azure Active Directory [Authentication Library (ADAL)](../active-directory/azuread-dev/active-directory-authentication-libraries.md) leggere le informazioni sulla [migrazione a MSAL.](../active-directory/develop/msal-net-migration.md)

## <a name="common-headers-and-parameters"></a>Intestazioni e parametri comuni

Questa sezione descrive le intestazioni e i parametri comuni delle richieste HTTP usati per eseguire query sulle API Azure Time Series Insights Gen1 e Gen2. I requisiti specifici dell'API sono trattati in modo più dettagliato nella documentazione [Azure Time Series Insights di riferimento dell'API REST.](/rest/api/time-series-insights/)

> [!TIP]
> Vedere [Informazioni di riferimento sull'API REST di Azure](/rest/api/azure/) per altre informazioni su come usare le API REST, effettuare richieste HTTP e gestire le risposte HTTP.

### <a name="http-headers"></a>Intestazioni HTTP

Di seguito sono descritte le intestazioni della richiesta obbligatorie.

| Intestazione della richiesta obbligatoria | Descrizione |
| --- | --- |
| Autorizzazione | Per eseguire l'Azure Time Series Insights, è necessario passare un token di connessione OAuth 2.0 valido [nell'intestazione authorization](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate). |

> [!TIP]
> Per informazioni su Azure Time Series Insights come eseguire l'autenticazione con le API di Azure Time Series Insights a livello di codice usando [JavaScript Client SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) insieme a grafici, leggere la visualizzazione di esempio dell'SDK [del client](https://tsiclientsample.azurewebsites.net/) Azure Time Series Insights ospitato.

Di seguito sono descritte le intestazioni della richiesta facoltative.

| Intestazione di richiesta facoltativa | Descrizione |
| --- | --- |
| Tipo di contenuto | È supportato solo `application/json`. |
| x-ms-client-request-id | ID della richiesta client. Il servizio registra questo valore. Consente al servizio di tenere traccia dell'operazione nei diversi servizi. |
| x-ms-client-session-id | ID della sessione client. Il servizio registra questo valore. Consente al servizio di tenere traccia di un gruppo di operazioni correlate nei diversi servizi. |
| x-ms-client-application-name | Nome dell'applicazione che ha generato questa richiesta. Il servizio registra questo valore. |

Di seguito sono descritte le intestazioni della risposta facoltative ma consigliate.

| Intestazione risposta | Descrizione |
| --- | --- |
| Tipo di contenuto | È supportato solo `application/json`. |
| x-ms-request-id | ID della richiesta generato dal server. Può essere usato per contattare Microsoft se dovesse essere necessario esaminare una richiesta. |
| x-ms-Property-not-found-Behavior | Intestazione della risposta facoltativa dell'API di disponibilità generale. I valori possibili sono `ThrowError` (impostazione predefinita) o `UseNull`. |

### <a name="http-parameters"></a>Parametri HTTP

> [!TIP]
> Per approfondire le informazioni sulle query obbligatorie e facoltative, vedere la [documentazione di riferimento](/rest/api/time-series-insights/).

I parametri della stringa di query dell'URL obbligatori dipendono dalla versione dell'API.

| Versione | Valori della versione dell'API |
| --- |  --- |
| Prima generazione | `api-version=2016-12-12`|
| Seconda generazione | `api-version=2020-07-31`|

I parametri della stringa di query dell'URL facoltativi includono l'impostazione di un timeout per i tempi di esecuzione della richiesta HTTP.

| Parametro di query facoltativo | Descrizione | Versione |
| --- |  --- | --- |
| `timeout=<timeout>` | Timeout lato server per l'esecuzione di una richiesta HTTP. Applicabile solo alle API [Get Environment Events](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) e [Get Environment Aggregates](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api). Il valore di timeout deve essere nel formato di durata ISO 8601, ad esempio `"PT20S"`, e deve essere compreso nell'intervallo `1-30 s`. Il valore predefinito è `30 s`. | Prima generazione |
| `storeType=<storeType>` | Per gli ambienti Gen2 con warm store abilitato, la query può essere eseguita in `WarmStore` o `ColdStore` . Questo parametro nella query definisce l'archivio in cui quest'ultima deve essere eseguita. Se non è definito, la query verrà eseguita nell'archivio ad accesso sporadico. Per eseguire la query nell'archivio ad accesso frequente, occorre impostare **storeType** su `WarmStore`. Se non è definito, la query verrà eseguita nell'archivio ad accesso sporadico. | Seconda generazione |

## <a name="next-steps"></a>Passaggi successivi

* Per il codice di esempio che chiama l'API Azure Time Series Insights Gen1, leggere Eseguire query sui [dati di Gen1 usando C#.](./time-series-insights-query-data-csharp.md)

* Per il codice di esempio che chiama gli esempi di Azure Time Series Insights'API gen2, vedere Eseguire query sui dati [di Gen2 usando C#.](./time-series-insights-update-query-data-csharp.md)

* Per le informazioni di riferimento sull'API, vedere la documentazione di [ riferimento sull'API di query](/rest/api/time-series-insights/reference-query-apis).
