---
title: Esercitazione per la configurazione di Azure Active Directory B2C con arcose Labs
titleSuffix: Azure AD B2C
description: Esercitazione per configurare Azure Active Directory B2C con arcose Labs per identificare gli utenti rischiosi e fraudolenti
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/18/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 46f117b13909c2d9624b88e9f5d9a62c4c646e51
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500293"
---
# <a name="tutorial-configure-arkose-labs-with-azure-active-directory-b2c"></a>Esercitazione: configurare arcose Labs con Azure Active Directory B2C

In questa esercitazione di esempio viene illustrato come integrare Azure Active Directory (AD) B2C Authentication con [arcose Labs](https://www.arkoselabs.com/). Arcose Labs consente alle organizzazioni di attacchi bot, attacchi di acquisizione di account e aperture fraudolente degli account.  

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).

- [Un tenant Azure ad B2C](tutorial-create-tenant.md) collegato alla sottoscrizione di Azure.

- Un account [arcose Labs](https://www.arkoselabs.com/book-a-demo/) .

## <a name="scenario-description"></a>Descrizione dello scenario

L'integrazione di arcose Labs include i componenti seguenti:

- **Arcose Labs** : un servizio di frode e abuso per la protezione da bot e altro abuso automatizzato.

- **Azure ad B2C l'iscrizione del flusso utente** : l'esperienza di iscrizione che verrà usata dal servizio arcose Labs. Utilizzerà i connettori HTML e JavaScript personalizzati e le API per l'integrazione con il servizio arcose Labs.

- **Funzioni di Azure** : endpoint API ospitato dall'utente che funziona con la funzionalità connettori API. Questa API è responsabile della convalida lato server del token di sessione arcose Labs.

Il diagramma seguente illustra il modo in cui arcose Labs si integra con Azure AD B2C.

![Immagine che mostra il diagramma dell'architettura di arcose Labs](media/partner-arkose-labs/arkose-labs-architecture-diagram.png)

| Passaggio  | Descrizione |
|---|---|
|1     | Un utente si iscrive e crea un account. Quando l'utente seleziona Invia, viene visualizzata la richiesta di imposizione di arcose Labs.         |
|2     |  Dopo che l'utente ha completato la richiesta, Azure AD B2C invia lo stato a arcose Labs per generare un token. |
|3     |  Arcose Labs genera un token e lo invia nuovamente al Azure AD B2C.   |
|4     |  Azure AD B2C chiama un'API Web intermedia per passare il modulo di iscrizione.      |
|5     |  L'API Web intermedia invia il modulo di iscrizione a arcose Lab per la verifica dei token.    |
|6     | Arcose Lab elabora e invia i risultati della verifica di nuovo all'API Web intermedia.|
|7     | L'API Web intermedia invia il risultato dell'esito positivo o negativo dalla richiesta a Azure AD B2C. |
|8     | Se la richiesta viene completata correttamente, viene inviato un modulo di iscrizione a Azure AD B2C e Azure AD B2C completa l'autenticazione.|

## <a name="onboard-with-arkose-labs"></a>Onboarding con arcose Labs

1. Contattare [arcose](https://www.arkoselabs.com/book-a-demo/) e creare un account.

2. Una volta creato l'account, passare a https://dashboard.arkoselabs.com/login  

3. All'interno del Dashboard passare a Impostazioni sito per trovare la chiave pubblica e la chiave privata. Queste informazioni saranno necessarie in un secondo momento per configurare Azure AD B2C. I valori delle chiavi pubbliche e private vengono definiti `ARKOSE_PUBLIC_KEY` e `ARKOSE_PRIVATE_KEY` nel [codice di esempio](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose).

## <a name="integrate-with-azure-ad-b2c"></a>Integrazione con Azure AD B2C

### <a name="part-1--create-a-arkosesessiontoken-custom-attribute"></a>Parte 1: creare un attributo personalizzato ArkoseSessionToken

Per creare un attributo personalizzato, attenersi alla procedura seguente:  

1. Passa a **portale di Azure**  >  **Azure ad B2C**

2. Selezionare **gli attributi utente**

3. Selezionare **Aggiungi**

4. Immettere **ArkoseSessionToken** come nome dell'attributo

5. Selezionare **Crea**

Altre informazioni sugli [attributi personalizzati](https://docs.microsoft.com/azure/active-directory-b2c/user-flow-custom-attributes?pivots=b2c-user-flow).

### <a name="part-2---create-a-user-flow"></a>Parte 2: creare un flusso utente

Il flusso utente può essere per l' **iscrizione** e l' **accesso** **o semplicemente per l'iscrizione.** Il flusso utente di arcose Labs verrà visualizzato solo durante l'iscrizione.

1. Vedere le [istruzioni](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) per creare un flusso utente. Se si usa un flusso utente esistente, deve essere il tipo di versione **consigliato (anteprima di prossima generazione)** .

2. In Impostazioni flusso utente passare a **attributi utente** e selezionare l'attestazione **ArkoseSessionToken** .

![Immagine che Mostra come selezionare gli attributi personalizzati](media/partner-arkose-labs/select-custom-attribute.png)

### <a name="part-3---configure-custom-html-javascript-and-page-layouts"></a>Parte 3: configurare HTML, JavaScript e layout di pagina personalizzati

Passare allo [script HTML](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html)fornito. Il file contiene un modello HTML con `<script>` tag JavaScript che eseguiranno tre operazioni:

1. Caricare lo script arcose Labs, che esegue il rendering del widget arcose Labs e convalida arcose Labs sul lato client.

2. Nascondere l' `extension_ArkoseSessionToken` elemento e l'etichetta di input, corrispondenti all' `ArkoseSessionToken` attributo personalizzato, dall'interfaccia utente visualizzata all'utente.

3. Quando un utente completa la richiesta di arcose Labs, arcose Labs verifica la risposta dell'utente e genera un token. Il callback `arkoseCallback` nel codice JavaScript personalizzato imposta il valore di sul `extension_ArkoseSessionToken` valore del token generato. Questo valore verrà inviato all'endpoint API, come descritto nella sezione successiva.

    Vedere [questo articolo](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/214176229/Standard+Setup) per informazioni sulla convalida lato client di arcose Labs.

Seguire i passaggi indicati per usare il codice HTML e JavaScript personalizzato per il flusso utente.

1. Modificare [selfAsserted.htm](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html) il file in modo che `<ARKOSE_PUBLIC_KEY>` corrisponda al valore generato per la convalida sul lato client e usato per caricare lo script arcose Labs per l'account.

2. Ospitare la pagina HTML in un endpoint Web abilitato per la condivisione di risorse tra le origini (CORS). [Creare un account di archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) e [configurare CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services).

  >[!NOTE]
  >Se si dispone di un codice HTML personalizzato, copiare e incollare gli `<script>` elementi nella pagina HTML.

3. Per configurare il layout di pagina, seguire questa procedura.

   a. Dal portale di Azure passare a **Azure ad B2C**

   b. Passare a **flussi utente** e selezionare il flusso utente

   c. Seleziona **layout di pagina**

   d. Selezionare **il layout della pagina di iscrizione dell'account locale**

   e. Imposta/Nascondi **USA contenuto pagina personalizzata** su **Sì**

   f. Incollare l'URI in cui si trova il codice HTML personalizzato **usando il contenuto della pagina personalizzata**

   g. Se si usano i provider di identità basati su social network, ripetere il **passaggio** e e **f** per il layout della pagina di iscrizione dell' **account di social** networking.

   ![immagine che mostra i layout di pagina](media/partner-arkose-labs/page-layouts.png)

4. Dal flusso utente passare a **Proprietà** e selezionare **Abilita JavaScript** applicazione del layout di pagina (anteprima). Per altre informazioni, vedere questo [articolo](https://docs.microsoft.com/azure/active-directory-b2c/javascript-and-page-layout?pivots=b2c-user-flow) .

### <a name="part-4---create-and-deploy-your-api"></a>Parte 4: creare e distribuire l'API

Installare l' [estensione funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) per Visual Studio Code.

>[!Note]
>I passaggi descritti in questa sezione presuppongono che si usi Visual Studio Code per distribuire la funzione di Azure. È anche possibile usare portale di Azure, un terminale o un prompt dei comandi o qualsiasi altro editor di codice per la distribuzione.

#### <a name="run-the-api-locally"></a>Eseguire l'API localmente

1. Passare all'estensione Azure in Visual Studio Code sulla barra di spostamento a sinistra. Selezionare la cartella **del progetto locale** che rappresenta la funzione locale di Azure.

2. Premere **F5** o usare il menu **debug**  >  **Avvia debug** per avviare il debugger e connettersi all'host di funzioni di Azure. Questo comando usa automaticamente la configurazione di debug singola creata dalla funzione di Azure.

3. L'estensione della funzione di Azure genera automaticamente alcuni file per lo sviluppo locale, installa le dipendenze e installa gli strumenti di base per le funzioni, se non sono già presenti. Questi strumenti sono utili per l'esperienza di debug.

4. L'output dello strumento Core funzione viene visualizzato nel pannello Visual Studio Code **terminale** . Una volta avviato l'host, **Alt + clic** sull'URL locale indicato nell'output per aprire il browser ed eseguire la funzione. In Azure Functions Explorer fare clic con il pulsante destro del mouse sulla funzione per visualizzare l'URL della funzione ospitata localmente.

Per ridistribuire l'istanza locale durante i test, ripetere i passaggi da 1 a 4.

#### <a name="add-environment-variables"></a>Aggiungi variabili di ambiente

Questo esempio protegge l'endpoint dell'API Web usando [l'autenticazione di base http](https://tools.ietf.org/html/rfc7617).

Nome utente e password vengono archiviati come variabili di ambiente e non come parte del repository. Per ulteriori informazioni, vedere [local.settings.jssu](https://docs.microsoft.com/azure/azure-functions/functions-run-local?tabs=macos%2Ccsharp%2Cbash#local-settings-file) file.

1. Creare una local.settings.jsnel file nella cartella radice

2. Copiare e incollare il codice seguente nel file:

```
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "BASIC_AUTH_USERNAME": "<USERNAME>",
    "BASIC_AUTH_PASSWORD": "<PASSWORD>",
    "ARKOSE_PRIVATE_KEY": "<ARKOSE_PRIVATE_KEY>",
    "B2C_EXTENSIONS_APP_ID": "<B2C_EXTENSIONS_APP_ID>"
  }
}
```
I valori **BASIC_AUTH_USERNAME** e **BASIC_AUTH_PASSWORD** saranno le credenziali usate per autenticare la chiamata all'API per la funzione di Azure. Scegliere i valori desiderati.

`<ARKOSE_PRIVATE_KEY>`È il segreto lato server generato nel servizio arcose Labs. Viene usato per chiamare l' [API di convalida lato server di arcose Labs](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/266214758/Server-Side+Instructions) per convalidare il valore dell'oggetto `ArkoseSessionToken` generato dal front-end.

`<B2C_EXTENSIONS_APP_ID>`È l'ID applicazione dell'app usato da Azure ad B2C per archiviare gli attributi personalizzati nella directory. È possibile trovare questo ID applicazione passando a Registrazioni app, cercando B2C-Extensions-app e copiando l'ID dell'applicazione (client) dal riquadro **Panoramica** . Rimuovere i `-` caratteri.

![Immagine che mostra la ricerca per ID app](media/partner-arkose-labs/search-app-id.png)

#### <a name="deploy-the-application-to-the-web"></a>Distribuire l'applicazione sul Web

1. Seguire i passaggi descritti in [questa](https://docs.microsoft.com/azure/javascript/tutorial-vscode-serverless-node-04) guida per distribuire la funzione di Azure nel cloud. Copiare l'URL Web dell'endpoint della funzione di Azure.

2. Una volta distribuita, selezionare l'opzione **Carica impostazioni** . Le variabili di ambiente vengono caricate nelle [impostazioni dell'applicazione](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code?tabs=csharp#application-settings-in-azure) del servizio app. Queste impostazioni dell'applicazione possono anche essere configurate o [gestite tramite il portale di Azure.](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings)

Vedere [questo articolo](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code?tabs=csharp#republish-project-files) per altre informazioni sullo sviluppo di Visual Studio Code per funzioni di Azure.

#### <a name="configure-and-enable-the-api-connector"></a>Configurare e abilitare il connettore API

[Creare un connettore API](https://docs.microsoft.com/azure/active-directory-b2c/add-api-connector) e abilitarlo per il flusso utente. La configurazione del connettore API dovrebbe essere simile alla seguente:

![Immagine che Mostra come configurare il connettore API](media/partner-arkose-labs/configure-api-connector.png)

- **URL dell'endpoint** : è l'URL della funzione copiato in precedenza mentre è stata distribuita la funzione di Azure.

- **Nome utente e password** : nome utente e password definiti in precedenza come variabili di ambiente.

Per abilitare il connettore API, nelle impostazioni del **connettore API** per il flusso utente selezionare il connettore API da richiamare in **prima di creare il passaggio utente** . Verrà richiamata l'API quando un utente seleziona **Crea** nel flusso di iscrizione. L'API eseguirà una convalida sul lato server del `ArkoseSessionToken` valore, che è stato impostato dal callback del widget arcose `arkoseCallback` .

![Immagine che mostra l'abilitazione del connettore API](media/partner-arkose-labs/enable-api-connector.png)

## <a name="test-the-user-flow"></a>Testare il flusso utente

1. Aprire il tenant di Azure AD B2C e in criteri selezionare **flussi utente**.

2. Selezionare il flusso utente creato in precedenza.

3. Selezionare **Esegui flusso utente** e selezionare le impostazioni:

   a. Applicazione: selezionare l'app registrata (l'esempio è JWT)

   b. URL di risposta: selezionare l'URL di Reindirizzamento

   c. Selezionare **Esegui il flusso utente**.

4. Esaminare il flusso di iscrizione e creare un account

5. Disconnetti

6. Esaminare il flusso di accesso  

7. Dopo aver selezionato **continua**, verrà visualizzato un puzzle di arcose Labs.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Codici di esempio](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) per Azure ad B2C flusso utente di iscrizione

- [Criteri personalizzati in AAD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Introduzione ai criteri personalizzati in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
