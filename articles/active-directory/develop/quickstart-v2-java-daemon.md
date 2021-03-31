---
title: 'Guida introduttiva: chiamare Microsoft Graph da un daemon Java | Azure'
titleSuffix: Microsoft identity platform
description: Questa Guida introduttiva illustra come un'app Java può ottenere un token di accesso e chiamare un'API protetta dall'endpoint della piattaforma di identità Microsoft, usando l'identità dell'app
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 01/22/2021
ms.author: nacanuma
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: 196b80a704b8a270a4cbb7d3505d5f9be1e23479
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99820322"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-java-console-app-using-apps-identity"></a>Guida introduttiva: acquisire un token e chiamare Microsoft Graph API da un'app console Java usando l'identità dell'app

In questa Guida introduttiva viene scaricato ed eseguito un esempio di codice che illustra come un'applicazione Java può ottenere un token di accesso usando l'identità dell'app per chiamare l'API Microsoft Graph e visualizzare un [elenco di utenti](/graph/api/user-list) nella directory. L'esempio di codice dimostra come è possibile eseguire un processo o un servizio di Windows automatico con un'identità dell'applicazione invece che con un'identità dell'utente. 

> [!div renderon="docs"]
> ![Mostra come funziona l'app di esempio generata da questo avvio rapido](media/quickstart-v2-java-daemon/java-console-daemon.svg)

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questo esempio, sono necessari:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 o versione successiva
- [Maven](https://maven.apache.org/)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrare e scaricare l'app della guida introduttiva

> [!div renderon="docs" class="sxs-lookup"]
>
> Per avviare l'applicazione della guida introduttiva sono disponibili due opzioni: Rapida (Opzione 1 di seguito) e Manuale (Opzione 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opzione 1: Registrare e configurare automaticamente l'app e quindi scaricare l'esempio di codice
>
> 1. Passare all'esperienza di avvio rapido <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaDaemonQuickstartPage/sourceType/docs" target="_blank">Portale di Azure - Registrazioni app</a>.
> 1. Immettere un nome per l'applicazione e fare clic su **Registra**.
> 1. Seguire le istruzioni per scaricare e configurare automaticamente la nuova applicazione con un clic.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opzione 2: Registrare e configurare manualmente l'applicazione e il codice di esempio

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione
> Per registrare l'applicazione e aggiungere manualmente le informazioni di registrazione dell'app alla soluzione, seguire questa procedura:
>
> 1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure</a>.
> 1. Se si accede a più tenant, usare il filtro **Directory e sottoscrizione** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: nel menu in alto e selezionare il tenant in cui si vuole registrare un'applicazione.
> 1. Cercare e selezionare **Azure Active Directory**.
> 1. In **Gestisci** selezionare **Registrazioni app** > **Nuova registrazione**.
> 1. Immettere un **Nome** per l'applicazione, ad esempio `Daemon-console`. Tale nome, che potrebbe essere visualizzato dagli utenti dell'app, può essere modificato in un secondo momento.
> 1. Selezionare **Registra**.
> 1. In **Gestisci** selezionare  **certificati & segreti**.
> 1. In **Segreti client** selezionare **Nuovo segreto client**, immettere un nome e quindi selezionare **Aggiungi**. Registrare il valore del segreto in una posizione sicura per usarlo in un passaggio successivo.
> 1. In **Gestisci** selezionare **Autorizzazioni API** > **Aggiungi un'autorizzazione**. Selezionare **Microsoft Graph**.
> 1. Selezionare **Autorizzazioni applicazione**.
> 1. Nel nodo **Utente** selezionare **User.Read.All**, quindi selezionare **Aggiungi autorizzazioni**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-quickstart-app"></a>Scaricare e configurare l'app di avvio rapido
>
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Passaggio 1: configurare l'applicazione in portale di Azure
> Per fare in modo che l'esempio di codice per questo avvio rapido funzioni, è necessario creare un segreto client e aggiungere l'autorizzazione dell'applicazione **User.Read.All** dell'API Graph.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta queste modifiche per me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-netcore-daemon/green-check.png) L'applicazione è configurata con questi attributi.

#### <a name="step-2-download-the-java-project"></a>Passaggio 2: scaricare il progetto Java

> [!div renderon="docs"]
> [Scaricare il progetto daemon Java](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Scaricare il codice di esempio](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


> [!div renderon="docs"]
> #### <a name="step-3-configure-the-java-project"></a>Passaggio 3: configurare il progetto Java
>
> 1. Estrarre il file ZIP in una cartella locale vicina alla radice del disco, ad esempio *C:\Azure-Samples*.
> 1. Passare alla sottocartella **MSAL-Client-Credential-Secret**.
> 1. Modificare *src\main\resources\application.Properties* e sostituire i valori dei campi `AUTHORITY` , `CLIENT_ID` e `SECRET` con il frammento di codice seguente:
>
>    ```
>    AUTHORITY=https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/
>    CLIENT_ID=Enter_the_Application_Id_Here
>    SECRET=Enter_the_Client_Secret_Here
>    ```
>    Dove:
>    - `Enter_the_Application_Id_Here` è l'**ID applicazione (client)** per l'applicazione registrata.
>    - `Enter_the_Tenant_Id_Here` -sostituire questo valore con l' **ID tenant** o il **nome del tenant** (ad esempio, contoso.Microsoft.com).
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
> Se si è un amministratore tenant globale, passare alla pagina **autorizzazioni API** in **registrazioni app** nel portale di Azure e selezionare **concedi il consenso dell'amministratore per {nome tenant}** (dove {nome tenant} è il nome della directory).

> [!div renderon="portal" class="sxs-lookup"]
> Se si è un amministratore globale, passare alla pagina **autorizzazioni API** selezionare **concedi il consenso dell'amministratore per Enter_the_Tenant_Name_Here**.
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

È possibile testare l'esempio direttamente eseguendo il metodo Main di ClientCredentialGrant. Java dall'IDE.

Dalla shell o dalla riga di comando:

```
$ mvn clean compile assembly:single
```

Verrà generato un file MSAL-Client-Credential-Secret-1.0.0. jar nella directory/targets. Eseguire questa operazione usando l'eseguibile Java come riportato di seguito:

```
$ java -jar msal-client-credential-secret-1.0.0.jar
```

Dopo l'esecuzione, l'applicazione deve visualizzare l'elenco di utenti nel tenant configurato.


> [!IMPORTANT]
> Questa applicazione della guida introduttiva usa un segreto client per identificarsi come client riservato. Poiché il segreto client viene aggiunto come testo normale ai file di progetto, per motivi di sicurezza è consigliabile usare un certificato anziché un segreto client prima di considerare l'applicazione come applicazione di produzione. Per altre informazioni su come usare un certificato, vedere [queste istruzioni](https://github.com/Azure-Samples/ms-identity-java-daemon/tree/master/msal-client-credential-certificate) nello stesso repository GitHub per questo esempio, ma nella seconda cartella **MSAL-Client-Credential-certificate**.

## <a name="more-information"></a>Ulteriori informazioni

### <a name="msal-java"></a>MSAL Java

[MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java) è la libreria usata per l'accesso degli utenti e la richiesta di token usati per accedere a un'API protetta dalla piattaforma di identità Microsoft. Come descritto, questo avvio rapido richiede i token usando l'identità propria dell'applicazione invece delle autorizzazioni delegate. Il flusso di autenticazione usato in questo caso è noto come *[flusso delle credenziali client OAuth](v2-oauth2-client-creds-grant-flow.md)* . Per altre informazioni su come usare MSAL Java con le app daemon, vedere [questo articolo](scenario-daemon-overview.md).

Aggiungere MSAL4J all'applicazione usando Maven o Gradle per gestire le dipendenze apportando le modifiche seguenti al file pom.xml (Maven) o build.gradle (Gradle) dell'applicazione.

In pom.xml:

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

In build.gradle:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>Inizializzazione della libreria MSAL

Aggiungere un riferimento a MSAL per Java aggiungendo il codice seguente all'inizio del file in cui verrà usato MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

Inizializzare quindi la libreria MSAL usando il codice seguente:

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

> | Dove: |Descrizione |
> |---------|---------|
> | `CLIENT_SECRET` | È il segreto client creato per l'applicazione nel portale di Azure. |
> | `CLIENT_ID` | **ID applicazione (client)** dell'applicazione registrata nel portale di Azure. Questo valore è riportato nella pagina **Panoramica** dell'app nel portale di Azure. |
> | `AUTHORITY`    | Endpoint del servizio token di sicurezza per l'utente da autenticare. in genere `https://login.microsoftonline.com/{tenant}` per il cloud pubblico, dove {tenant} è il nome del tenant o l'ID tenant.|

### <a name="requesting-tokens"></a>Richiesta di token

Per richiedere un token con l'identità dell'app, usare il metodo `acquireToken`:

```Java
IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
```

> |Dove:| Descrizione |
> |---------|---------|
> | `SCOPE` | Contiene gli ambiti richiesti. Per i client riservati, questo deve usare il formato simile a `{Application ID URI}/.default` per indicare che gli ambiti richiesti sono quelli definiti in modo statico nell'oggetto app impostato nel portale di Azure (per Microsoft Graph, `{Application ID URI}` punta a `https://graph.microsoft.com` ). Per le API Web personalizzate, `{Application ID URI}` viene definito nella sezione **esporre un'API** in **registrazioni app** nel portale di Azure.|

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle applicazioni daemon, vedere la pagina di destinazione dello scenario.

> [!div class="nextstepaction"]
> [Applicazione daemon che chiama le API Web](scenario-daemon-overview.md)
