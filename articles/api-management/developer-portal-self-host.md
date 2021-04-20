---
title: Ospitare autonomamente il portale per sviluppatori
titleSuffix: Azure API Management
description: Informazioni su come ospitare autonomamente il API Management per sviluppatori.
author: dlepow
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: e63a329a10699102802af6d544ab55aa19513f17
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741716"
---
# <a name="self-host-the-api-management-developer-portal"></a>Ospitare in modo self-API Management per sviluppatori

Questa esercitazione descrive come ospitare in modo self-host [il API Management per sviluppatori.](api-management-howto-developer-portal.md) Il self-hosting offre la flessibilità necessaria per estendere il portale per sviluppatori con logica e widget personalizzati che personalizzano dinamicamente le pagine in fase di esecuzione. È possibile ospitare in autonomia più portali per l API Management istanza di , con funzionalità diverse. Quando si ospita autonomamente un portale, si diventa il responsabile della manutenzione e si è responsabili degli aggiornamenti. 

La procedura seguente illustra come configurare l'ambiente di sviluppo locale, eseguire le modifiche nel portale per sviluppatori e pubblicarle e distribuirle in un account di archiviazione di Azure.

Se sono già stati caricati o modificati file multimediali nel portale gestito, vedere Passare da file gestiti a [self-hosted](#move-from-managed-to-self-hosted-developer-portal)più avanti in questo articolo.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Prerequisiti

Per configurare un ambiente di sviluppo locale, è necessario disporre di:

- Istanza API Management servizio. Se non se ne ha una, vedere [Avvio rapido - Creare un'istanza](get-started-create-service-instance.md)API Management Azure .
- Un account di archiviazione di Azure con [la funzionalità siti Web statici abilitata.](../storage/blobs/storage-blob-static-website.md) Vedere [Creare un account di archiviazione](../storage/common/storage-account-create.md).
- Git nel computer. Installarlo seguendo questa [esercitazione su Git.](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- Node.js (versione LTS o `v10.15.0` successiva) e npm nel computer. Vedere [Download e installazione di Node.js e npm.](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)
- Interfaccia della riga di comando di Azure. Seguire i [passaggi di installazione dell'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli-windows).

## <a name="step-1-set-up-local-environment"></a>Passaggio 1: Configurare l'ambiente locale

Per configurare l'ambiente locale, è necessario clonare il repository, passare alla versione più recente del portale per sviluppatori e installare i pacchetti npm.

1. Clonare [il repository api-management-developer-portal](https://github.com/Azure/api-management-developer-portal.git) da GitHub:

    ```console
    git clone https://github.com/Azure/api-management-developer-portal.git
    ```
1. Passare alla copia locale del repo:

    ```console
    cd api-management-developer-portal
    ```

1. Vedere la versione più recente del portale.

    Prima di eseguire il codice seguente, controllare il tag di versione corrente nella [sezione Versioni](https://github.com/Azure/api-management-developer-portal/releases) del repository e sostituire il valore con il tag di versione `<current-release-tag>` più recente.
    
    ```console
    git checkout <current-release-tag>
    ```

1. Installare tutti i pacchetti npm disponibili:

    ```console
    npm install
    ```

> [!TIP]
> Usare sempre la [versione più recente](https://github.com/Azure/api-management-developer-portal/releases) del portale e mantenere aggiornato il portale con fork. I tecnici del software usano `master` il ramo di questo repository per scopi di sviluppo giornalieri. Ha versioni instabili del software.

## <a name="step-2-configure-json-files-static-website-and-cors-settings"></a>Passaggio 2: Configurare i file JSON, il sito Web statico e le impostazioni CORS

Il portale per sviluppatori richiede API Management'API REST di Azure per gestire il contenuto.

### <a name="configdesignjson-file"></a>config.design.jssul file

Passare alla `src` cartella e aprire il `config.design.json` file.

```json
{
  "environment": "development",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature ...",
  "backendUrl": "https://<service-name>.developer.azure-api.net",
  "useHipCaptcha": false
}
```

Configurare il file:

1. Nel valore `managementApiUrl` sostituire con il nome `<service-name>` dell'istanza API Management locale. Se è stato configurato [un dominio personalizzato,](configure-custom-domain.md)usarlo invece (ad esempio, `https://management.contoso.com` ).

    ```json
    {
    ...
    "managementApiUrl": "https://contoso-api.management.azure-api.net"
    ...
    ``` 

1. [Creare manualmente un token di firma di accesso](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication#ManuallyCreateToken) condiviso per abilitare l'accesso diretto dell'API REST all'API Management locale.

1. Copiare il token generato e incollarlo come `managementApiAccessToken` valore.

1. Nel valore `backendUrl` sostituire con il nome `<service-name>` dell'istanza API Management locale. Se è stato configurato [un dominio personalizzato,](configure-custom-domain.md)usarlo invece (ad esempio, `https://portal.contoso.com` ).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

1. Se si desidera abilitare CAPTCHA nel portale per sviluppatori, vedere [Abilitare CAPTCHA.](#enable-captcha)

### <a name="configpublishjson-file"></a>config.publish.jssul file

Passare alla `src` cartella e aprire il `config.publish.json` file.

```json
{
  "environment": "publishing",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature...",
  "useHipCaptcha": false
}
```

Configurare il file:

1. Copiare e `managementApiUrl` incollare `managementApiAccessToken` i valori e dal file di configurazione precedente.

1. Se si desidera abilitare CAPTCHA nel portale per sviluppatori, vedere [Abilitare CAPTCHA.](#enable-captcha)

### <a name="configruntimejson-file"></a>config.runtime.jssul file

Passare alla `src` cartella e aprire il `config.runtime.json` file.

```json
{
  "environment": "runtime",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "backendUrl": "https://<service-name>.developer.azure-api.net"
}
```

Configurare il file:

1. Copiare e `managementApiUrl` incollare il valore del file di configurazione precedente.

1. Nel valore `backendUrl` sostituire con il nome `<service-name>` dell'API Management istanza. Se è stato configurato [un dominio personalizzato,](configure-custom-domain.md)usarlo invece (ad esempio, `https://portal.contoso.com`).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

### <a name="configure-the-static-website"></a>Configurare il sito Web statico

Configurare la **funzionalità Sito Web** statico nell'account di archiviazione specificando le route per le pagine di indice e di errore:

1. Passare all'account di archiviazione nella portale di Azure e selezionare **Sito Web** statico dal menu a sinistra.

1. Nella pagina **Sito Web statico** selezionare **Abilitato.**

1. Nel campo **Index document name (Nome documento** indice) immettereindex.htm *l*.

1. Nel campo **Percorso documento errore** immettere *404/index.html*.

1. Selezionare **Salva**.

### <a name="configure-the-cors-settings"></a>Configurare le impostazioni CORS

Configurare le impostazioni di Condivisione risorse tra le origini (CORS):

1. Passare all'account di archiviazione nel portale di Azure e selezionare **CORS** dal menu a sinistra.

1. Nella scheda **Servizio BLOB** configurare le regole seguenti:

    | Regola | Valore |
    | ---- | ----- |
    | Origini consentite | * |
    | Metodi consentiti | Selezionare tutti i verbi HTTP. |
    | Intestazioni consentite | * |
    | Intestazioni esposte | * |
    | Validità massima | 0 |

1. Selezionare **Salva**.

## <a name="step-3-run-the-portal"></a>Passaggio 3: Eseguire il portale

È ora possibile compilare ed eseguire un'istanza del portale locale in modalità di sviluppo. In modalità di sviluppo tutte le ottimizzazioni sono disattivate e le mappe di origine sono attivate.

Eseguire il comando seguente:

```console
npm start
```

Dopo un breve periodo di tempo, il browser predefinito viene aperto automaticamente con l'istanza del portale per sviluppatori locale. L'indirizzo predefinito `http://localhost:8080` è , ma la porta può cambiare se è già `8080` occupata. Qualsiasi modifica alla codebase del progetto attiverà una ricompilazione e apportare un aggiornamento alla finestra del browser.

## <a name="step-4-edit-through-the-visual-editor"></a>Passaggio 4: Modificare tramite l'editor visivo

Usare l'editor visivo per eseguire queste attività:

- Personalizzare il portale
- Creare contenuto
- Organizzare la struttura del sito Web
- Stilizzare l'aspetto

Vedere [Esercitazione: Accedere e personalizzare il portale per sviluppatori.](api-management-howto-developer-portal-customize.md) Illustra le nozioni di base dell'interfaccia utente amministrativa ed elenca le modifiche consigliate al contenuto predefinito. Salvare tutte le modifiche nell'ambiente locale e premere **CTRL+C** per chiuderla.

## <a name="step-5-publish-locally"></a>Passaggio 5: Pubblicare in locale

I dati del portale hanno origine sotto forma di oggetti con tipi forti. Il comando seguente li converte in file statici e inserisce l'output nella `./dist/website` directory:

```console
npm run publish
```

## <a name="step-6-upload-static-files-to-a-blob"></a>Passaggio 6: Caricare file statici in un BLOB

Usare l'interfaccia della riga di comando di Azure per caricare i file statici generati localmente in un BLOB e assicurarsi che i visitatori possano ottenerli:

1. Aprire il prompt dei comandi di Windows, PowerShell o un'altra shell dei comandi.

1. Eseguire il comando dell'interfaccia della riga di comando di Azure seguente:
   
    Sostituire `<account-connection-string>` con la stringa di connessione dell'account di archiviazione. È possibile ottenerlo dalla sezione **Chiavi di accesso** dell'account di archiviazione.

    ```azurecli
    az storage blob upload-batch --source dist/website \
        --destination '$web' \
        --connection-string <account-connection-string>
    ```


## <a name="step-7-go-to-your-website"></a>Passaggio 7: Passare al sito Web

Il sito Web è ora disponibile con il nome host specificato nelle proprietà Archiviazione di Azure (**Endpoint primario** nei siti **Web statici**).

## <a name="step-8-change-api-management-notification-templates"></a>Passaggio 8: Modificare i API Management di notifica

Sostituire l'URL del portale per sviluppatori API Management modelli di notifica in modo che puntino al portale self-hosted. Vedere [Come configurare le notifiche e i modelli di posta elettronica in Azure API Management](api-management-howto-configure-notifications.md).

In particolare, apportare le modifiche seguenti ai modelli predefiniti:

> [!NOTE]
> I valori nelle sezioni **aggiornate seguenti** presuppongono che il portale sia ospitato all'indirizzo **https: \/ /portal.contoso.com/**. 

### <a name="email-change-confirmation"></a>Conferma della modifica tramite posta elettronica

Aggiornare l'URL del portale per sviluppatori nel modello di notifica **di conferma della modifica tramite** posta elettronica:

**Contenuto originale**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**Aggiornato**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="new-developer-account-confirmation"></a>Conferma del nuovo account per sviluppatore

Aggiornare l'URL del portale per sviluppatori nel modello **di notifica di conferma nuovo account** per sviluppatore:

**Contenuto originale**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**Aggiornato**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="invite-user"></a>Invito utente

Aggiornare l'URL del portale per sviluppatori nel **modello di notifica** Invita utente:

**Contenuto originale**

```html
<a href="$ConfirmUrl">$ConfirmUrl</a>
```

**Aggiornato**

```html
<a href="https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery">https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery</a>
```

### <a name="new-subscription-activated"></a>Nuova sottoscrizione attivata

Aggiornare l'URL del portale per sviluppatori nel **modello di notifica attivato da Nuova** sottoscrizione:

**Contenuto originale**

```html
Thank you for subscribing to the <a href="http://$DevPortalUrl/products/$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**Aggiornato**

```html
Thank you for subscribing to the <a href="https://portal.contoso.com/product#product=$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**Contenuto originale**

```html
Visit the developer <a href="http://$DevPortalUrl/developer">profile area</a> to manage your subscription and subscription keys
```

**Aggiornato**

```html
Visit the developer <a href="https://portal.contoso.com/profile">profile area</a> to manage your subscription and subscription keys
```

**Contenuto originale**

```html
<a href="http://$DevPortalUrl/docs/services?product=$ProdId">Learn about the API</a>
```

**Aggiornato**

```html
<a href="https://portal.contoso.com/product#product=$ProdId">Learn about the API</a>
```

**Contenuto originale**

```html
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/applications">Feature your app in the app gallery</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">You can publish your application on our gallery for increased visibility to potential new users.</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/issues">Stay in touch</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
      If you have an issue, a question, a suggestion, a request, or if you just want to tell us something, go to the <a href="http://$DevPortalUrl/issues">Issues</a> page on the developer portal and create a new topic.
</p>
```

**Aggiornato**

```html
<!--Remove the entire block of HTML code above.-->
```

### <a name="password-change-confirmation"></a>Conferma della modifica della password

Aggiornare l'URL del portale per sviluppatori nel modello **di notifica di conferma della modifica** della password:

**Contenuto originale**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**Aggiornato**

```html
<a href="https://portal.contoso.com/confirm-password?$ConfirmQuery">https://portal.contoso.com/confirm-password?$ConfirmQuery</a>
```

### <a name="all-templates"></a>Tutti i modelli

Aggiornare l'URL del portale per sviluppatori in qualsiasi modello con un collegamento nel piè di pagina:

**Contenuto originale**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**Aggiornato**

```html
<a href="https://portal.contoso.com/">https://portal.contoso.com/</a>
```

## <a name="move-from-managed-to-self-hosted-developer-portal"></a>Passare dal portale per sviluppatori gestito a quello self-hosted

Nel corso del tempo, i requisiti aziendali possono cambiare. È possibile terminare in una situazione in cui la versione gestita del portale API Management per sviluppatori non soddisfa più le proprie esigenze. Ad esempio, un nuovo requisito può forzare la creazione di un widget personalizzato che si integra con un provider di dati di terze parti. A differenza della versione manged, la versione self-hosted del portale offre tutta la flessibilità e l'estendibilità.

### <a name="transition-process"></a>Processo di transizione

È possibile eseguire la transizione dalla versione gestita a una versione self-hosted all'interno della stessa API Management del servizio. Il processo mantiene le modifiche apportate nella versione gestita del portale. Assicurarsi di eseguire il backup del contenuto del portale in anticipo. È possibile trovare lo script di backup nella `scripts` cartella del repository GitHub API Management portale per [sviluppatori.](https://github.com/Azure/api-management-developer-portal)

Il processo di conversione è quasi identico alla configurazione di un portale self-hosted generico, come illustrato nei passaggi precedenti di questo articolo. Esiste un'eccezione nel passaggio di configurazione. L'account di archiviazione `config.design.json` nel file deve essere uguale all'account di archiviazione della versione gestita del portale. Per istruzioni su come recuperare l'URL di firma di accesso condiviso, [vedere Esercitazione:](../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-storage-sas.md#get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls) Usare un'identità assegnata dal sistema per una macchina virtuale Linux per accedere Archiviazione di Azure tramite credenziali di firma di accesso condiviso.

> [!TIP]
> È consigliabile usare un account di archiviazione separato nel `config.publish.json` file . Questo approccio offre un maggiore controllo e semplifica la gestione del servizio di hosting del portale.

## <a name="enable-captcha"></a>Abilitare CAPTCHA

Quando si configura il portale self-hosted, è possibile che CAPTCHA sia stato disabilitato tramite `useHipCaptcha` l'impostazione . La comunicazione con CAPTCHA avviene tramite un endpoint, che consente la condivisione di risorse tra le origini (CORS) solo per il nome host del portale per sviluppatori gestito. Se il portale per sviluppatori è self-hosted, usa un nome host diverso e CAPTCHA non consente la comunicazione.

### <a name="update-the-json-config-files"></a>Aggiornare i file di configurazione JSON

Per abilitare CAPTCHA nel portale self-hosted:

1. Assegnare un dominio personalizzato , ad esempio `api.contoso.com` , all'endpoint **del portale** per sviluppatori del API Management servizio.

    Questo dominio si applica alla versione gestita del portale e all'endpoint CAPTCHA. Per la procedura, vedere [Configurare un nome di dominio personalizzato per l'istanza API Management Azure.](configure-custom-domain.md)

1. Passare alla `src` cartella [nell'ambiente locale](#step-1-set-up-local-environment) per il portale self-hosted.

1. Aggiornare i file di `json` configurazione:

    | File | Nuovo valore | Nota |
    | ---- | --------- | ---- |
    | `config.design.json`| `"backendUrl": "https://<custom-domain>"` | Sostituire `<custom-domain>` con il dominio personalizzato configurato nel primo passaggio. |
    |  | `"useHipCaptcha": true` | Modificare il valore in `true` |
    | `config.publish.json`| `"backendUrl": "https://<custom-domain>"` | Sostituire `<custom-domain>` con il dominio personalizzato configurato nel primo passaggio. |
    |  | `"useHipCaptcha": true` | Modificare il valore in `true` |
    | `config.runtime.json` | `"backendUrl": "https://<custom-domain>"` | Sostituire `<custom-domain>` con il dominio personalizzato configurato nel primo passaggio. |

1. [Pubblicare](#step-5-publish-locally) il portale.

1. [Caricare](#step-6-upload-static-files-to-a-blob) e ospitare il portale appena pubblicato.

1. Esporre il portale self-hosted tramite un dominio personalizzato.

Il primo e il secondo livello del dominio del portale devono corrispondere al dominio configurato nel primo passaggio. Ad esempio: `portal.contoso.com`. I passaggi esatti dipendono dalla piattaforma di hosting scelta. Se è stato usato un account di archiviazione di Azure, è possibile fare riferimento a Eseguire il mapping di un dominio [personalizzato a](../storage/blobs/storage-custom-domain-name.md) un endpoint Archiviazione BLOB di Azure per istruzioni.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sugli [approcci alternativi all'hosting self-hosting](developer-portal-alternative-processes-self-host.md)
