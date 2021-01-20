---
title: 'Avvio rapido: Chiamare Microsoft Graph da un daemon Python | Azure'
titleSuffix: Microsoft identity platform
description: Questa guida di avvio rapido illustra come un processo Python può ottenere un token di accesso e chiamare un'API protetta da un endpoint di Microsoft Identity Platform usando l'identità dell'app
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, devx-track-python, scenarios:getting-started, languages:Python
ms.openlocfilehash: 47aaf67c9ae2402e0445de60de439b77242bd87d
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178229"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-python-console-app-using-apps-identity"></a>Avvio rapido: Acquisire un token e chiamare l'API Microsoft Graph da un'app console Python usando l'identità dell'app

In questa guida di avvio rapido si scarica e si esegue un esempio di codice che illustra come un'applicazione Python può ottenere un token di accesso usando l'identità dell'app per chiamare l'API Microsoft Graph e visualizzare un [elenco di utenti](/graph/api/user-list) nella directory. L'esempio di codice dimostra come è possibile eseguire un processo o un servizio di Windows automatico con un'identità dell'applicazione invece che con un'identità dell'utente. 

> [!div renderon="docs"]
> ![Mostra come funziona l'app di esempio generata da questo avvio rapido](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questo esempio, sono necessari:

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) o [Python 3+](https://www.python.org/downloads/release/python-364/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrare e scaricare l'app della guida introduttiva

> [!div renderon="docs" class="sxs-lookup"]
>
> Per avviare l'applicazione della guida introduttiva sono disponibili due opzioni: Rapida (Opzione 1 di seguito) e Manuale (Opzione 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opzione 1: Registrare e configurare automaticamente l'app e quindi scaricare l'esempio di codice
>
> 1. Passare all'esperienza di avvio rapido <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonDaemonQuickstartPage/sourceType/docs" target="_blank">Portale di Azure - Registrazioni app<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
> 1. Immettere un nome per l'applicazione e fare clic su **Registra**.
> 1. Seguire le istruzioni per scaricare e configurare automaticamente la nuova applicazione con un clic.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opzione 2: Registrare e configurare manualmente l'applicazione e il codice di esempio

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione
> Per registrare l'applicazione e aggiungere manualmente le informazioni di registrazione dell'app alla soluzione, seguire questa procedura:
>
> 1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
> 1. Se si accede a più tenant, usare il filtro **Directory e sottoscrizione** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: nel menu in alto e selezionare il tenant in cui si vuole registrare un'applicazione.
> 1. Cercare e selezionare **Azure Active Directory**.
> 1. In **Gestisci** selezionare **Registrazioni app** > **Nuova registrazione**.
> 1. Immettere un **Nome** per l'applicazione, ad esempio `Daemon-console`. Tale nome, che potrebbe essere visualizzato dagli utenti dell'app, può essere modificato in un secondo momento.
> 1. Selezionare **Registra**.
> 1. In **Gestisci**, selezionare **Certificati e segreti**.
> 1. In **Segreti client** selezionare **Nuovo segreto client**, immettere un nome e quindi selezionare **Aggiungi**. Registrare il valore del segreto in una posizione sicura per usarlo in un passaggio successivo.
> 1. In **Gestisci** selezionare **Autorizzazioni API** > **Aggiungi un'autorizzazione**. Selezionare **Microsoft Graph**.
> 1. Selezionare **Autorizzazioni applicazione**.
> 1. Nel nodo **Utente** selezionare **User.Read.All**, quindi selezionare **Aggiungi autorizzazioni**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Scaricare e configurare l'app della guida introduttiva
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Passaggio 1: Configurare l'applicazione nel portale di Azure
> Per fare in modo che l'esempio di codice per questo avvio rapido funzioni, è necessario creare un segreto client e aggiungere l'autorizzazione dell'applicazione **User.Read.All** dell'API Graph.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta queste modifiche per me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-netcore-daemon/green-check.png) L'applicazione è configurata con questi attributi.

#### <a name="step-2-download-your-python-project"></a>Passaggio 2: Scaricare il progetto Python

> [!div renderon="docs"]
> [Scaricare il progetto daemon Python](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Scaricare il codice di esempio](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


> [!div renderon="docs"]
> #### <a name="step-3-configure-your-python-project"></a>Passaggio 3: Configurare il progetto Python
>
> 1. Estrarre il file ZIP in una cartella locale vicina alla radice del disco, ad esempio **C:\Azure-Samples**.
> 1. Passare alla sottocartella **1-Call-MsGraph-WithSecret"** .
> 1. Modificare **parameters.json** e sostituire i valori dei campi `authority`, `client_id` e `secret` con il frammento di codice seguente:
>
>    ```json
>    "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
>    "client_id": "Enter_the_Application_Id_Here",
>    "secret": "Enter_the_Client_Secret_Here"
>    ```
>    Dove:
>    - `Enter_the_Application_Id_Here` è l'**ID applicazione (client)** per l'applicazione registrata.
>    - `Enter_the_Tenant_Id_Here`: sostituire questo valore con l'**ID tenant** o il **nome del tenant** (ad esempio, contoso.microsoft.com)
>    - `Enter_the_Client_Secret_Here`: sostituire questo valore con il segreto client creato nel passaggio 1.
>
> > [!TIP]
> > Per trovare i valori di **ID applicazione (client)** e **ID della directory (tenant)** , passare alla pagina **Panoramica** dell'app nel portale di Azure. Per generare una nuova chiave, passare alla pagina **Certificati e segreti**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Passaggio 3: Consenso dell'amministratore

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Passaggio 4: Consenso dell'amministratore

Se si prova a eseguire l'applicazione a questo punto, si riceverà l'errore *HTTP 403 - Accesso negato*: `Insufficient privileges to complete the operation`. Questo errore si verifica perché le *autorizzazioni solo per app* richiedono il consenso amministratore. È quindi necessario che un amministratore globale della directory conceda il consenso all'applicazione. Selezionare una delle opzioni seguenti in base al ruolo:

##### <a name="global-tenant-administrator"></a>Amministratore del tenant globale

> [!div renderon="docs"]
> Gli amministratori del tenant globali devono passare alla pagina **Autorizzazioni API** nella registrazione dell'applicazione del portale di Azure (anteprima) e selezionare **Concedi consenso amministratore per {nome del tenant}** (dove {nome del tenant} è il nome della directory).

> [!div renderon="portal" class="sxs-lookup"]
> Gli amministratori globali devono passare alla pagina **Autorizzazioni API** e selezionare **Concedi consenso amministratore per Immettere_il_nome_tenant_qui**
> > [!div id="apipermissionspage"]
> > [Passare alla pagina Autorizzazioni API]()

##### <a name="standard-user"></a>Utente standard

Gli utenti standard del tenant devono chiedere a un amministratore globale di concedere il consenso amministratore per l'applicazione. A tale scopo, assegnare l'URL seguente all'amministratore:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Dove:
>> * `Enter_the_Tenant_Id_Here`: sostituire questo valore con l'**ID tenant** o il **nome del tenant** (ad esempio, contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` è l'**ID applicazione (client)** per l'applicazione registrata.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Passaggio 4: Eseguire l'applicazione

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Passaggio 5: Eseguire l'applicazione

Le dipendenze di questo esempio devono essere installate una sola volta.

```console
pip install -r requirements.txt
```

Eseguire quindi l'applicazione tramite il prompt dei comandi o la console:

```console
python confidential_client_secret_sample.py parameters.json
```

Nell'output della console dovrebbe essere visualizzato un frammento JSON che rappresenta un elenco di utenti nella directory di Azure AD.

> [!IMPORTANT]
> Questa applicazione della guida introduttiva usa un segreto client per identificarsi come client riservato. Poiché il segreto client viene aggiunto come testo normale ai file di progetto, per motivi di sicurezza è consigliabile usare un certificato anziché un segreto client prima di considerare l'applicazione come applicazione di produzione. Per altre informazioni su come usare un certificato, vedere [queste istruzioni](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/README.md) nello stesso repository GitHub di questo esempio, ma nella seconda cartella **2-Call-MsGraph-WithCertificate**.

## <a name="more-information"></a>Ulteriori informazioni

### <a name="msal-python"></a>MSAL Python

[MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) è la libreria usata per concedere l'accesso agli utenti e richiedere i token usati per accedere a un'API protetta da Microsoft Identity Platform. Come descritto, questo avvio rapido richiede i token usando l'identità propria dell'applicazione invece delle autorizzazioni delegate. Il flusso di autenticazione usato in questo caso è noto come *[flusso delle credenziali client OAuth](v2-oauth2-client-creds-grant-flow.md)* . Per altre informazioni su come usare MSAL Python con le app daemon, vedere [questo articolo](scenario-daemon-overview.md).

 È possibile MSAL Python eseguendo il comando pip seguente.

```powershell
pip install msal
```

### <a name="msal-initialization"></a>Inizializzazione della libreria MSAL

È possibile aggiungere il riferimento per la libreria MSAL aggiungendo il codice seguente:

```Python
import msal
```

Inizializzare quindi la libreria MSAL usando il codice seguente:

```Python
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"])
```

> | Dove: |Descrizione |
> |---------|---------|
> | `config["secret"]` | È il segreto client creato per l'applicazione nel portale di Azure. |
> | `config["client_id"]` | **ID applicazione (client)** dell'applicazione registrata nel portale di Azure. Questo valore è riportato nella pagina **Panoramica** dell'app nel portale di Azure. |
> | `config["authority"]`    | Endpoint del servizio token di sicurezza per l'utente da autenticare. in genere `https://login.microsoftonline.com/{tenant}` per il cloud pubblico, dove {tenant} è il nome del tenant o l'ID tenant.|

Per altre informazioni, vedere la [documentazione di riferimento`ConfidentialClientApplication`](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)

### <a name="requesting-tokens"></a>Richiesta di token

Per richiedere un token con l'identità dell'app, usare il metodo `AcquireTokenForClient`:

```Python
result = None
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

> |Dove:| Descrizione |
> |---------|---------|
> | `config["scope"]` | Contiene gli ambiti richiesti. Per i client riservati, dovrebbe essere usato un formato simile a `{Application ID URI}/.default` per indicare che gli ambiti che vengono richiesti sono quelli definiti in modo statico nell'oggetto app impostato nel portale di Azure. Per Microsoft Graph, `{Application ID URI}` punta a `https://graph.microsoft.com`. Per le API Web personalizzate, `{Application ID URI}` è definito nella sezione **Esporre un'API** della registrazione dell'applicazione (anteprima) del portale di Azure. |

Per altre informazioni, vedere la [documentazione di riferimento`AcquireTokenForClient`](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle applicazioni daemon, vedere la pagina di destinazione dello scenario

> [!div class="nextstepaction"]
> [Applicazione daemon che chiama le API Web](scenario-daemon-overview.md)
