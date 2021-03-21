---
title: Personalizzare l'interfaccia utente con i modelli HTML
titleSuffix: Azure AD B2C
description: Informazioni su come personalizzare l'interfaccia utente con i modelli HTML per le applicazioni che usano Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e694a5f6144cee65be074d05ce0015d31bfdf65e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104675826"
---
# <a name="customize-the-user-interface-with-html-templates-in-azure-active-directory-b2c"></a>Personalizzare l'interfaccia utente con i modelli HTML in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

La personalizzazione e la personalizzazione dell'interfaccia utente che Azure Active Directory B2C (Azure AD B2C) Visualizza ai clienti contribuisce a offrire un'esperienza utente uniforme nell'applicazione. Queste esperienze includono l'iscrizione, l'accesso, la modifica del profilo e la reimpostazione della password. Questo articolo presenta i metodi di personalizzazione dell'interfaccia utente. 

> [!TIP]
> Se si desidera modificare solo il logo del banner, l'immagine di sfondo e il colore di sfondo delle pagine del flusso utente, è possibile provare la funzionalità di [personalizzazione della società](customize-ui.md) .

## <a name="custom-html-and-css-overview"></a>Cenni preliminari su HTML e CSS personalizzati

Azure AD B2C esegue il codice nel browser del cliente usando la [condivisione di risorse tra le origini (CORS)](https://www.w3.org/TR/cors/). In fase di esecuzione, il contenuto viene caricato da un URL specificato nel flusso utente o in un criterio personalizzato. Ogni pagina nell'esperienza utente carica il proprio contenuto dall'URL specificato per la pagina. Dopo che il contenuto è stato caricato dall'URL, viene unito a un frammento HTML inserito da Azure AD B2C, quindi la pagina viene visualizzata per il cliente.

![Margine contenuto pagina personalizzata](./media/customize-ui-with-html/html-content-merging.png)

### <a name="custom-html-page-content"></a>Contenuto pagina HTML personalizzato

Creare una pagina HTML con una personalizzazione personalizzata per gestire il contenuto della pagina personalizzata. Questa pagina può essere una pagina statica `*.html` o una pagina dinamica, ad esempio .NET, Node.js o php.

Il contenuto della pagina personalizzata può contenere qualsiasi elemento HTML, incluso CSS e JavaScript, ma non può includere elementi non protetti come gli iframe. L'unico elemento obbligatorio è un elemento div con `id` impostato su `api` , ad esempio questo `<div id="api"></div>` nella pagina HTML.

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Product Brand Name</title>
</head>
<body>
    <div id="api"></div>
</body>
</html>
```

#### <a name="customize-the-default-azure-ad-b2c-pages"></a>Personalizzare le pagine di Azure AD B2C predefinite

Invece di creare il contenuto della pagina personalizzata da zero, è possibile personalizzare Azure AD contenuto della pagina predefinita B2C.

Nella tabella seguente sono elencati i contenuti di pagina predefiniti forniti da Azure AD B2C. Scaricare i file e usarli come punto di partenza per la creazione di pagine personalizzate.

| Pagina predefinita | Descrizione | ID definizione del contenuto<br/>(solo criteri personalizzati) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Pagina di errore**. Questa pagina viene visualizzata quando viene rilevata un'eccezione o un errore. | *api.error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Pagina autocertificata**. Usare questo file come contenuto di pagina personalizzata per una pagina di iscrizione dell'account di social networking, una pagina di iscrizione dell'account locale, una pagina di accesso dell'account locale, la reimpostazione della password e altro ancora. Il modulo può contenere diversi controlli di input, ad esempio una casella per l'immissione di testo e una per l'immissione della password, un pulsante di opzione, caselle a discesa a selezione singola e caselle di controllo con selezione multipla. | API *. localaccountsignin*, *API. localaccountsignin*, *API. localaccountpasswordreset*, *API. selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Pagina dell'autenticazione a più fattori**. In questa pagina gli utenti possono verificare il proprio numero di telefono (tramite SMS o chiamata vocale) durante la procedura di iscrizione o di accesso. | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Pagina di aggiornamento del profilo**. Questa pagina contiene un modulo a cui gli utenti possono accedere per aggiornare il profilo. Questa pagina è simile alla pagina di iscrizione dell'account di social networking, a eccezione dei campi di immissione della password. | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Pagina unificata per l'iscrizione o l'accesso**. Questa pagina gestisce il processo di iscrizione e quello di accesso degli utenti. Gli utenti possono usare provider di identità aziendali, provider di identità basati su social network, ad esempio Facebook o Google +, o account locali. | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>Hosting del contenuto della pagina

Quando si usano i propri file HTML e CSS per personalizzare l'interfaccia utente, ospitare il contenuto dell'interfaccia utente in qualsiasi endpoint HTTPS disponibile pubblicamente che supporti CORS. Ad esempio, [archiviazione BLOB di Azure](../storage/blobs/storage-blobs-introduction.md), [Servizi app Azure](../app-service/index.yml), server Web, CDNs, AWS S3 o sistemi di condivisione di file.

## <a name="guidelines-for-using-custom-page-content"></a>Linee guida per l'utilizzo del contenuto della pagina personalizzata

- Usare un URL assoluto quando si includono risorse esterne come file multimediali, CSS e JavaScript nel file HTML.
- Usando il [layout di pagina](page-layout.md) 1.2.0 e versioni successive, è possibile aggiungere l' `data-preload="true"` attributo nei tag HTML per controllare l'ordine di caricamento per CSS e JavaScript. Con `data-preload="true"` , la pagina viene costruita prima di essere visualizzata all'utente. Questo attributo consente di impedire che la pagina venga "sfarfallio" precaricando il file CSS, senza che venga visualizzato un codice HTML non in stile. Il frammento di codice HTML seguente mostra l'uso del `data-preload` tag.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- È consigliabile iniziare con il contenuto predefinito della pagina e compilarlo.
- È possibile [includere JavaScript](javascript-and-page-layout.md) nel contenuto personalizzato.
- Le versioni di browser supportate sono:
  - Internet Explorer 11, 10 e Microsoft Edge
  - Supporto limitato per Internet Explorer 9 e 8
  - Google Chrome 42.0 e versioni successive
  - Mozilla Firefox 38.0 e versioni successive
  - Safari per iOS e macOS, versione 12 e successive
- A causa delle restrizioni di sicurezza, Azure AD B2C non supporta `frame` `iframe` `form` gli elementi HTML, o.

## <a name="localize-content"></a>Localizzare il contenuto

Si può localizzare il contenuto HTML abilitando la [personalizzazione della lingua](language-customization.md) nel tenant di Azure AD B2C. L'abilitazione di questa funzionalità consente Azure AD B2C di inviare il parametro OpenID Connect `ui_locales` all'endpoint. Il server di contenuti può usare questo parametro per fornire pagine HTML personalizzate specifiche della lingua.

È possibile eseguire il pull del contenuto da posizioni diverse in base alle impostazioni locali in uso. Nell'endpoint abilitato per CORS, configurare una struttura di cartelle in cui ospitare il contenuto per specifiche lingue. Se si inserisce il valore del carattere jolly `{Culture:RFC5646}` verrà chiamata la lingua corretta.

Ad esempio, l'URI della pagina personalizzata potrebbe essere simile al seguente:

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

È possibile caricare la pagina in francese eseguendo il pull del contenuto da:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="custom-page-content-walkthrough"></a>Procedura dettagliata contenuto pagina personalizzata

Ecco una panoramica del processo:

1. Preparare un percorso per ospitare il contenuto della pagina personalizzata (un endpoint HTTPS accessibile pubblicamente e abilitato per CORS).
1. Scaricare e personalizzare un file di contenuto di pagina predefinito, ad esempio `unified.html` .
1. Pubblicare il contenuto della pagina personalizzata dell'endpoint HTTPS disponibile pubblicamente.
1. Impostare la condivisione di risorse tra le origini (CORS) per l'app Web.
1. Puntare i criteri all'URI del contenuto dei criteri personalizzato.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="1-create-your-html-content"></a>1. creare il contenuto HTML

Creare un contenuto di pagina personalizzato con il nome del marchio del prodotto nel titolo.

1. Copiare il frammento di codice HTML seguente. Si tratta di un HTML5 ben formato con un elemento vuoto denominato che *\<div id="api"\>\</div\>* si trova all'interno dei *\<body\>* tag. Questo elemento indica il punto in cui deve essere inserito il contenuto di Azure AD B2C.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. Incollare il frammento copiato in un editor di testo
1. Usare CSS per applicare uno stile agli elementi dell'interfaccia utente inseriti nella pagina da Azure AD B2C. L'esempio seguente illustra un semplice file CSS che include anche le impostazioni per gli elementi HTML inseriti all'iscrizione:

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

1.  Salvare il file come *customize-ui.html*.

> [!NOTE]
> Gli elementi del form HTML verranno rimossi a causa di restrizioni di sicurezza se si usa login.microsoftonline.com. Se si desidera utilizzare elementi del form HTML nel contenuto HTML personalizzato, [utilizzare b2clogin.com](b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. creare un account di archiviazione BLOB di Azure

In questo articolo verrà usato l'archivio BLOB di Azure per ospitare il contenuto. È possibile scegliere di ospitare il contenuto in un server Web, ma è necessario [abilitare la condivisione di risorse tra le origini (CORS) nel server Web](https://enable-cors.org/server.html).

Per ospitare il contenuto HTML nell'archivio BLOB, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel menu **Hub** selezionare **Nuovo** > **Archiviazione** > **Account di archiviazione**.
1. Selezionare una **sottoscrizione** per l'account di archiviazione.
1. Creare un **gruppo di risorse** o selezionarne uno esistente.
1. Immettere un **nome** unico per l'account di archiviazione.
1. Selezionare la **posizione geografica** dell'account di archiviazione.
1. Per **Modello di distribuzione** è possibile lasciare **Resource Manager**.
1. Per **Prestazioni** è possibile lasciare **Standard**.
1. Modificare **Tipo di Account** in **archiviazione BLOB**.
1. Per **Replica** è possibile lasciare **RA-GRS**.
1. Per **Livello di accesso** è possibile lasciare **Frequente**.
1. Selezionare **Verifica + crea** per creare l'account di archiviazione.
    Al termine della distribuzione, viene visualizzata automaticamente la pagina **account di archiviazione** .

#### <a name="21-create-a-container"></a>2,1 creare un contenitore

Per creare un contenitore pubblico nell'archivio BLOB, seguire questa procedura:

1. In **servizio BLOB** scegliere **BLOB** dal menu a sinistra.
1. Selezionare **+ contenitore**.
1. Per **nome** immettere *radice*. Il nome può essere un nome a scelta, ad esempio *Contoso*, ma in questo esempio viene usata la *radice* per semplicità.
1. Per **livello di accesso pubblico** selezionare **BLOB** e quindi **OK**.
1. Selezionare **radice** per aprire il nuovo contenitore.

#### <a name="22-upload-your-custom-page-content-files"></a>2,2 caricare i file di contenuto della pagina personalizzati

1. Selezionare **Carica**.
1. Selezionare l'icona della cartella accanto a **selezionare un file**.
1. Passare a e selezionare **customize-ui.html**, creato in precedenza nella sezione relativa alla personalizzazione dell'interfaccia utente della pagina.
1. Se si desidera caricare in una sottocartella, espandere **Avanzate** e immettere un nome di cartella in **carica in cartella**.
1. Selezionare **Carica**.
1. Selezionare il BLOB di **customize-ui.html** caricato.
1. A destra della casella di testo **URL** selezionare l'icona **copia negli Appunti** per copiare l'URL negli Appunti.
1. Nel Web browser passare all'URL copiato per verificare che il BLOB caricato sia accessibile. Se è inaccessibile, ad esempio se si verifica un `ResourceNotFound` errore, verificare che il tipo di accesso del contenitore sia impostato su **BLOB**.

### <a name="3-configure-cors"></a>3. configurare CORS

Configurare l'archiviazione BLOB per la condivisione di risorse tra le origini eseguendo i passaggi seguenti:

1. Nel menu selezionare **CORS**.
1. Per **Origini consentite** immettere `https://your-tenant-name.b2clogin.com`. Sostituire `your-tenant-name` con il nome del tenant di Azure AD B2C. Ad esempio: `https://fabrikam.b2clogin.com`. Usare tutte le lettere minuscole quando si immette il nome del tenant.
1. Per **Metodi consentiti** selezionare sia `GET` che `OPTIONS`.
1. Per **Intestazioni consentite** immettere un asterisco (*).
1. Per **Intestazioni esposte** immettere un asterisco (*).
1. Per **Età massima** immettere 200.
1. Selezionare **Salva**.

#### <a name="31-test-cors"></a>3,1 test CORS

Verificare che l'utente sia pronto attenendosi alla procedura seguente:

1. Ripetere il passaggio Configure CORS. Per le **origini consentite**, immettere `https://www.test-cors.org`
1. Passare a [www.test-CORS.org](https://www.test-cors.org/) 
1. Per la casella **URL remoto** incollare l'URL del file HTML. Ad esempio: `https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html`
1. Selezionare **Invia richiesta**.
    Il risultato deve essere `XHR status: 200` . 
    Se si riceve un messaggio d'errore, verificare che le impostazioni CORS siano corrette. Potrebbe anche essere necessario cancellare la cache del browser o aprire una sessione di esplorazione anonima premendo Ctrl+Maiusc+P.

::: zone pivot="b2c-user-flow"

### <a name="4-update-the-user-flow"></a>4. aggiornare il flusso utente

1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Flussi utente** e quindi il flusso utente *B2C_1_signupsignin1*.
1. Selezionare **Layout di pagina** e quindi in **Pagina unificata per l'iscrizione o l'accesso** fare clic su **Sì** per **Usa contenuto di pagina personalizzato**.
1. In **URI della pagina personalizzata** immettere l'URI del file *custom-ui.html* registrato in precedenza.
1. Nella parte superiore della pagina selezionare **Salva**.

### <a name="5-test-the-user-flow"></a>5. testare il flusso utente

1. Dal tenant di Azure AD B2C selezionare **Flussi utente** e quindi il flusso utente *B2C_1_signupsignin1*.
1. Nella parte superiore della pagina, fare clic su **Esegui il flusso utente**.
1. Fare clic sul pulsante **Esegui il flusso utente**.

Dovrebbe essere visualizzata una pagina simile all'esempio seguente, con gli elementi allineati al centro in base al file CSS creato:

![Web browser che visualizza la pagina di iscrizione o di accesso con elementi dell'interfaccia utente personalizzati](./media/customize-ui-with-html/run-now.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="4-modify-the-extensions-file"></a>4. modificare il file delle estensioni

Per configurare la personalizzazione dell'interfaccia utente, copiare **ContentDefinition** e i relativi elementi figlio dal file di base nel file delle estensioni.

1. Aprire il file di base dei criteri, ad esempio <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em>. Questo file di base è uno dei file di criteri inclusi nello Starter Pack del criterio personalizzato, che è necessario ottenere nel prerequisito, [Introduzione ai criteri personalizzati](./custom-policy-get-started.md).
1. Cercare e copiare l'intero contenuto dell'elemento **ContentDefinitions**.
1. Aprire il file di estensione. ad esempio *TrustFrameworkExtensions.xml*. Cercare l'elemento **BuildingBlocks**. Se l'elemento non esiste, aggiungerlo.
1. Incollare l'intero contenuto dell'elemento **ContentDefinitions** copiato come figlio dell'elemento **BuildingBlocks** .
1. Cercare l'elemento **ContentDefinition** che contiene `Id="api.signuporsignin"` nel codice XML copiato.
1. Cambiare il valore di **LoadUri** nell'URL del file HTML caricato nell'archivio. Ad esempio: `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Il criterio personalizzato dovrebbe essere simile al frammento di codice seguente:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Salvare il file delle estensioni.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. caricare e testare i criteri personalizzati aggiornati

#### <a name="51-upload-the-custom-policy"></a>5,1 caricare i criteri personalizzati

1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Cercare e selezionare **Azure Active Directory B2C**.
1. In **Criteri** selezionare **Identity Experience Framework**.
1. Selezionare **Carica criteri personalizzati**.
1. Caricare il file delle estensioni modificato in precedenza.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5,2 testare i criteri personalizzati tramite **Esegui adesso**

1. Selezionare il criterio caricato, quindi selezionare **Esegui adesso**.
1. Dovrebbe essere possibile iscriversi usando un indirizzo di posta elettronica.

## <a name="configure-dynamic-custom-page-content-uri"></a>Configurare l'URI del contenuto della pagina personalizzata dinamica

Utilizzando Azure AD B2C criteri personalizzati, è possibile inviare un parametro nel percorso URL o in una stringa di query. Passando il parametro all'endpoint HTML, è possibile modificare dinamicamente il contenuto della pagina. È ad esempio possibile modificare l'immagine di sfondo della pagina di accesso o di iscrizione ad Azure AD B2C in base a un parametro passato dall'applicazione Web o per dispositivi mobili. Il parametro può essere qualsiasi [resolver di attestazioni](claim-resolver-overview.md), ad esempio l'ID applicazione, l'ID lingua o il parametro della stringa di query personalizzata, ad esempio `campaignId` .

### <a name="sending-query-string-parameters"></a>Invio dei parametri della stringa di query

Per inviare parametri della stringa di query, nel [criterio di relying party](relyingparty.md)aggiungere un `ContentDefinitionParameters` elemento come illustrato di seguito.

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

Nella definizione del contenuto modificare il valore di `LoadUri` in `https://<app_name>.azurewebsites.net/home/unified` . Il criterio personalizzato `ContentDefinition` dovrebbe essere simile al frammento di codice seguente:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Quando Azure AD B2C carica la pagina, effettua una chiamata all'endpoint del server Web:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>URI contenuto pagina dinamica

È possibile estrarre il contenuto da posizioni diverse in base ai parametri utilizzati. Nell'endpoint abilitato per CORS configurare una struttura di cartelle per ospitare il contenuto. Ad esempio, è possibile organizzare il contenuto nella struttura seguente. *Cartella/cartella radice per lingua/file HTML*. Ad esempio, l'URI della pagina personalizzata potrebbe essere simile al seguente:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C invia il codice ISO di due lettere per la lingua, `fr` per il francese:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

::: zone-end

## <a name="sample-templates"></a>Modelli di esempio

È possibile trovare modelli di esempio per la personalizzazione dell'interfaccia utente qui:

```bash
git clone https://github.com/azure-ad-b2c/html-templates
```

Questo progetto contiene i modelli seguenti:
- [Blu oceano](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/AzureBlue)
- [Grigio ardesia](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/MSA)
- [Classico](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/classic)
- [Risorse del modello](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/src)

Per utilizzare l'esempio:

1. Clonare il repository nel computer locale. Scegliere una cartella del modello `/AzureBlue` , `/MSA` o `/classic` .
1. Caricare tutti i file nella cartella del modello e nella `/src` cartella nell'archivio BLOB, come descritto nelle sezioni precedenti.
1. Aprire quindi ogni `\*.html` file nella cartella del modello. Sostituire quindi tutte le istanze degli `https://login.microsoftonline.com` URL con l'URL caricato nel passaggio 2. Ad esempio:
    
    Da:
    ```html
    https://login.microsoftonline.com/templates/src/fonts/segoeui.WOFF
    ```

    Con:
    ```html
    https://your-storage-account.blob.core.windows.net/your-container/templates/src/fonts/segoeui.WOFF
    ```
    
1. Salvare i `\*.html` file e caricarli nell'archivio BLOB.
1. A questo punto, modificare il criterio, puntando al file HTML, come indicato in precedenza.
1. Se vengono visualizzati i tipi di carattere, le immagini o i file CSS mancanti, controllare i riferimenti nei criteri delle estensioni e nei file con estensione \* HTML.

## <a name="use-company-branding-assets-in-custom-html"></a>Usare asset di personalizzazione dell'azienda in codice HTML personalizzato

Per usare le risorse di [personalizzazione della società](customize-ui.md#configure-company-branding) in un HTML personalizzato, aggiungere i tag seguenti all'esterno del `<div id="api">` tag. L'origine dell'immagine viene sostituita con quella dell'immagine di sfondo e il logo del banner.

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come abilitare il [codice JavaScript sul lato client](javascript-and-page-layout.md).
