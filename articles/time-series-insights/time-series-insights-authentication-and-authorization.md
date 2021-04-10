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
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 88fd575d40cc31f12f052158bda0aed9a5335555
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009267"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticazione e autorizzazione per l'API Azure Time Series Insights

A seconda delle esigenze aziendali, la soluzione potrebbe includere una o più applicazioni client usate per interagire con le [API](/rest/api/time-series-insights/reference-data-access-overview)dell'ambiente Azure Time Series Insights. Azure Time Series Insights esegue l'autenticazione usando [Azure ad token di sicurezza basati su OAUTH 2,0](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims). Per autenticare i client, è necessario ottenere una bearer token con le autorizzazioni corrette e passarla insieme alle chiamate API. Questo documento descrive diversi metodi per ottenere le credenziali che è possibile usare per ottenere una bearer token e l'autenticazione, tra cui l'uso dell'identità gestita e la registrazione di Azure Active Directory app.

## <a name="managed-identities"></a>Identità gestite

Le sezioni seguenti descrivono come usare un'identità gestita da Azure Active Directory (Azure AD) per accedere all'API Azure Time Series Insights. In Azure le identità gestite eliminano la necessità di gestire le credenziali fornendo un'identità per la risorsa di Azure in Azure AD che è possibile usare per ottenere i token di Azure Active Directory (Azure AD). Ecco alcuni vantaggi derivanti dall'uso delle identità gestite:

- Non è necessario gestire le credenziali. Le credenziali non sono neanche accessibili.
- È possibile usare le identità gestite per eseguire l'autenticazione con qualsiasi servizio di Azure che supporti l'autenticazione di Azure AD, incluso Azure Key Vault.
- Le identità gestite possono essere usate senza costi aggiuntivi.

Per altre informazioni sui due tipi di identità gestite, vedere [che cosa sono le identità gestite per le risorse di Azure?](../active-directory/managed-identities-azure-resources/overview.md)

È possibile usare le identità gestite dal:

- Macchine virtuali di Azure
- Servizi app di Azure
- Funzioni di Azure
- Istanze di contenitore di Azure
- e altro...

Per l'elenco completo, vedere [servizi di Azure che supportano identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) .

## <a name="azure-active-directory-app-registration"></a>Registrazione dell'app Azure Active Directory

È consigliabile usare le identità gestite laddove possibile, in modo che non sia necessario gestire le credenziali. Se l'applicazione client non è ospitata in un servizio di Azure che supporta identità gestite, è possibile registrare l'applicazione con un tenant di Azure AD. Quando si registra l'applicazione con Azure AD, si crea una configurazione di identità per l'applicazione che consente l'integrazione con Azure AD. Quando si registra un'app nella [portale di Azure](https://portal.azure.com/), è possibile scegliere se si tratta di un singolo tenant (accessibile solo nel tenant) o multi-tenant (accessibile in altri tenant) ed è possibile impostare facoltativamente un URI di reindirizzamento (a cui viene inviato il token di accesso).

Al termine della registrazione dell'app, si ha un'istanza univoca globale dell'app (oggetto applicazione) che risiede all'interno del tenant o della directory principale. È anche presente un ID univoco globale per l'app (l'ID app o client). Nel portale è quindi possibile aggiungere segreti o certificati e ambiti per eseguire il lavoro dell'app, personalizzare la personalizzazione dell'app nella finestra di dialogo di accesso e altro ancora.

Se si registra un'applicazione nel portale, vengono creati automaticamente un oggetto applicazione e un oggetto entità servizio nel tenant principale. Se si registra o si crea un'applicazione usando le API di Microsoft Graph, la creazione dell'oggetto entità servizio è un passaggio separato. Per richiedere token, è necessario un oggetto entità servizio.

Assicurarsi di esaminare l'elenco di controllo di [sicurezza](../active-directory/develop/identity-platform-integration-checklist.md#security) per l'applicazione. Come procedura consigliata, è consigliabile usare le [credenziali del certificato](../active-directory/develop/active-directory-certificate-credentials.md)e non le credenziali password (segreti client).

Per ulteriori informazioni, vedere [oggetti applicazione e oggetti entità servizio in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md) .

## <a name="step-1-create-your-managed-identity-or-app-registration"></a>Passaggio 1: creare l'identità gestita o la registrazione dell'app

Dopo aver identificato se si userà un'identità gestita o una registrazione dell'app, il passaggio successivo consiste nel provisioning di un utente.

### <a name="managed-identity"></a>Identità gestita

I passaggi da usare per creare un'identità gestita variano a seconda della posizione in cui si trova il codice e della possibilità di creare o meno un'identità assegnata a un sistema o assegnata dall'utente. Leggere [tipi di identità gestiti](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) per comprendere la differenza. Dopo aver selezionato il tipo di identità, individuare e seguire l'esercitazione corretta nella [documentazione relativa](../active-directory/managed-identities-azure-resources/index.yml)alle identità gestite da Azure ad. Sono disponibili istruzioni per la configurazione delle identità gestite per:

- [Macchine virtuali di Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-during-creation-of-a-vm)
- [Servizio app e funzioni di Azure](../app-service/overview-managed-identity.md)
- [Istanze di Azure Container](../container-instances/container-instances-managed-identity.md)
- e altro...

### <a name="application-registration"></a>Registrazione dell'applicazione

Seguire i passaggi elencati in [registrare un'applicazione](../active-directory/develop/quickstart-register-app.md#register-an-application).

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="step-2-grant-access"></a>Passaggio 2: concedere l'accesso

Quando l'ambiente di Azure Time Series Insights riceve una richiesta, prima viene convalidata la bearer token del chiamante. Se la convalida passa, il chiamante è stato autenticato e quindi viene eseguito un altro controllo per assicurarsi che il chiamante sia autorizzato a eseguire l'azione richiesta. Per autorizzare qualsiasi utente o entità servizio, è prima necessario concedere loro l'accesso all'ambiente assegnando loro il ruolo lettore o collaboratore.

- Per concedere l'accesso tramite l'interfaccia utente di [portale di Azure](https://portal.azure.com/) , seguire le istruzioni elencate nell'articolo [concedere l'accesso ai dati a un ambiente](concepts-access-policies.md) . Quando si seleziona l'utente, è possibile cercare l'identità gestita o la registrazione dell'app in base al nome o all'ID.

- Per concedere l'accesso usando l'interfaccia della riga di comando di Azure, eseguire il comando seguente. Esaminare la documentazione [qui](/cli/azure/ext/timeseriesinsights/tsi/access-policy) per l'elenco completo dei comandi disponibili per la gestione dell'accesso.

   ```azurecli-interactive
   az tsi access-policy create --name "ap1" --environment-name "env1" --description "some description" --principal-object-id "aGuid" --roles Reader Contributor --resource-group "rg1"
   ```

> [!Note]
> L'estensione timeseriesinsights per l'interfaccia della riga di comando di Azure richiede la versione 2.11.0 o successiva. L'estensione verrà installata automaticamente la prima volta che si esegue un comando AZ TSI Access-Policy. [Altre](/cli/azure/azure-cli-extensions-overview) informazioni sulle estensioni.

## <a name="step-3-requesting-tokens"></a>Passaggio 3: richiesta di token

Quando è stato effettuato il provisioning dell'identità gestita o della registrazione dell'app e ne è stato assegnato un ruolo, si è pronti per iniziare a usarla per richiedere token di porta OAuth 2,0. Il metodo usato per ottenere un token varia a seconda della posizione in cui è ospitato il codice e della lingua scelta. Quando si specifica la risorsa, nota anche come "audience" del token, è possibile identificare Azure Time Series Insights in base al relativo URL o GUID:

* `https://api.timeseries.azure.com/`
* `120d688d-1518-4cf7-bd38-182f158850b6`

> [!IMPORTANT]
> Se si usa l'URL come ID risorsa, il token deve essere emesso esattamente in `https://api.timeseries.azure.com/` . La barra finale è obbligatoria.

> * Se si usa il [post](https://www.getpostman.com/) , il **AuthUrl** sarà pertanto: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` è valido, ma `https://api.timeseries.azure.com` non lo è.

### <a name="managed-identities"></a>Identità gestite

Quando si accede da app Azure servizio o dalle funzioni, seguire le istruzioni fornite nella pagina [ottenere i token per le risorse di Azure](../app-service/overview-managed-identity.md).

Per le applicazioni e le funzioni .NET, il modo più semplice per usare un'identità gestita è tramite la [libreria client di identità di Azure](/dotnet/api/overview/azure/identity-readme) per .NET. Questa libreria client è molto diffusa grazie alla semplicità e ai vantaggi della sicurezza. Gli sviluppatori possono scrivere codice una volta e lasciare che la libreria client determini come eseguire l'autenticazione in base all'ambiente dell'applicazione, in una workstation di sviluppo che usa un account dello sviluppatore o distribuita in Azure usando un'identità del servizio gestito. Per indicazioni sulla migrazione dalla libreria AppAuthentication precedente, leggere [AppAuthentication in Azure. informazioni aggiuntive sulla migrazione di identità](/dotnet/api/overview/azure/app-auth-migration).

Richiedere un token per Azure Time Series Insights usando C# e la libreria client di identità di Azure per .NET:

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

* Gli sviluppatori possono usare [Microsoft Authentication Library](../active-directory/develop/msal-overview.md) (MSAL) per ottenere i token per le registrazioni dell'app.

MSAL può essere usato in molti scenari di applicazione, tra cui:

* [Applicazioni a pagina singola (JavaScript)](../active-directory/develop/scenario-spa-overview.md)
* [Applicazione Web per l'accesso di un utente e la chiamata di un'API Web per conto dell'utente](../active-directory/develop/scenario-web-app-call-api-overview.md)
* [API Web che chiama un'altra API Web downstream per conto dell'utente che ha eseguito l'accesso](../active-directory/develop/scenario-web-api-call-api-overview.md)
* [Applicazione desktop che chiama un'API Web per conto dell'utente che ha eseguito l'accesso](../active-directory/develop/scenario-desktop-overview.md)
* [Applicazione per dispositivi mobili che chiama un'API Web per conto dell'utente che ha eseguito l'accesso in modo interattivo](../active-directory/develop/scenario-mobile-overview.md).
* [Applicazione daemon di servizio/desktop che chiama un'API Web per proprio conto](../active-directory/develop/scenario-daemon-overview.md)

Per il codice C# di esempio che illustra come acquisire un token come registrazione dell'app ed eseguire query sui dati da un ambiente Gen2, visualizzare l'app di esempio in [GitHub](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/Program.cs)

> [!IMPORTANT]
> Se si usa [Azure Active Directory Authentication Library (adal),](../active-directory/azuread-dev/active-directory-authentication-libraries.md) vedere la pagina relativa [alla migrazione a MSAL](../active-directory/develop/msal-net-migration.md).

## <a name="common-headers-and-parameters"></a>Intestazioni e parametri comuni

Questa sezione descrive le intestazioni e i parametri della richiesta HTTP comuni usati per eseguire query sulle API Azure Time Series Insights Gen1 e Gen2. I requisiti specifici dell'API sono trattati più dettagliatamente nella [documentazione di riferimento dell'API REST di Azure Time Series Insights](/rest/api/time-series-insights/).

> [!TIP]
> Vedere [Informazioni di riferimento sull'API REST di Azure](/rest/api/azure/) per altre informazioni su come usare le API REST, effettuare richieste HTTP e gestire le risposte HTTP.

### <a name="http-headers"></a>Intestazioni HTTP

Di seguito sono descritte le intestazioni della richiesta obbligatorie.

| Intestazione della richiesta obbligatoria | Descrizione |
| --- | --- |
| Autorizzazione | Per eseguire l'autenticazione con Azure Time Series Insights, è necessario passare un token di porta OAuth 2,0 valido nell' [intestazione dell'autorizzazione](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate). |

> [!TIP]
> Leggere la visualizzazione di esempio di Hosted Azure Time Series Insights [client SDK](https://tsiclientsample.azurewebsites.net/) per informazioni su come eseguire l'autenticazione con le api di Azure Time Series Insights a livello di codice usando [JavaScript client SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) insieme a grafici e grafici.

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

| Versione | Valori della versione API |
| --- |  --- |
| Prima generazione | `api-version=2016-12-12`|
| Seconda generazione | `api-version=2020-07-31`|

I parametri della stringa di query dell'URL facoltativi includono l'impostazione di un timeout per i tempi di esecuzione della richiesta HTTP.

| Parametro di query facoltativo | Descrizione | Versione |
| --- |  --- | --- |
| `timeout=<timeout>` | Timeout lato server per l'esecuzione di una richiesta HTTP. Applicabile solo alle API [Get Environment Events](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) e [Get Environment Aggregates](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api). Il valore di timeout deve essere nel formato di durata ISO 8601, ad esempio `"PT20S"`, e deve essere compreso nell'intervallo `1-30 s`. Il valore predefinito è `30 s`. | Prima generazione |
| `storeType=<storeType>` | Per gli ambienti Gen2 con warm Store abilitato, la query può essere eseguita in `WarmStore` o `ColdStore` . Questo parametro nella query definisce l'archivio in cui quest'ultima deve essere eseguita. Se non è definito, la query verrà eseguita nell'archivio ad accesso sporadico. Per eseguire la query nell'archivio ad accesso frequente, occorre impostare **storeType** su `WarmStore`. Se non è definito, la query verrà eseguita nell'archivio ad accesso sporadico. | Seconda generazione |

## <a name="next-steps"></a>Passaggi successivi

* Per il codice di esempio che chiama l'API Azure Time Series Insights Gen1, vedere [eseguire query sui dati di Gen1 con C#](./time-series-insights-query-data-csharp.md).

* Per il codice di esempio che chiama gli esempi di codice dell'API Azure Time Series Insights Gen2, vedere [eseguire query sui dati di Gen2 con C#](./time-series-insights-update-query-data-csharp.md).

* Per le informazioni di riferimento sull'API, vedere la documentazione di [ riferimento sull'API di query](/rest/api/time-series-insights/reference-query-apis).
