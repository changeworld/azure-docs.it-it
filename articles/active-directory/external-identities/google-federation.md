---
title: Aggiungere Google come provider di identità per B2B - Azure AD
description: Federazione con Google per consentire agli utenti guest di accedere alle app Azure AD con i propri account Gmail.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a397c17821d16594ccfb48175a8a141cb9f390b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687822"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Aggiungere Google come provider di identità per utenti guest B2B

Impostando la Federazione con Google, è possibile consentire agli utenti invitati di accedere alle app e alle risorse condivise con i propri account Gmail, senza dover creare account Microsoft.

Dopo aver aggiunto Google come una delle opzioni di accesso dell'applicazione, nella pagina di **accesso** un utente può semplicemente immettere il messaggio di posta elettronica usato per accedere a Google oppure può selezionare le **Opzioni di accesso** e scegliere **Accedi con Google**. In entrambi i casi, verranno reindirizzati alla pagina di accesso di Google per l'autenticazione.

![Opzioni di accesso per gli utenti di Google](media/google-federation/sign-in-with-google-overview.png)

> [!NOTE]
> Federazione Google è appositamente progettato per gli utenti di Gmail. Per attuare la Federazione con i domini G Suite, usare [Federazione diretta](direct-federation.md).

> [!IMPORTANT]
> **A partire dal 4 gennaio 2021**, il [supporto dell'accesso WebView verrà deprecato](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html) da Google. Se si usa la federazione Google o l'iscrizione self-service con Gmail, è consigliabile [testare la compatibilità delle applicazioni line-of-business native](google-federation.md#deprecation-of-webview-sign-in-support).

## <a name="what-is-the-experience-for-the-google-user"></a>Qual è l'esperienza per l'utente di Google?

Quando un utente di Google riscatta l'invito, la loro esperienza varia a seconda che l'utente sia già connesso a Google:

- Agli utenti guest che non hanno eseguito l'accesso a Google verrà richiesto di farlo.
- Agli utenti Guest a cui è già stato effettuato l'accesso a Google verrà richiesto di scegliere l'account che si desidera utilizzare. L'utente deve scegliere l'account usato nell'invito.

Gli utenti guest che visualizzano un errore di "intestazione troppo lungo" possono cancellare i cookie o aprire una finestra privata o in incognito e provare ad accedere di nuovo.

![Screenshot che mostra la pagina di accesso di Google.](media/google-federation/google-sign-in.png)

## <a name="sign-in-endpoints"></a>Endpoint di accesso

Gli utenti Guest Google possono ora accedere alle app multi-tenant o Microsoft di terze parti usando un [endpoint comune](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint) (in altre parole, un URL dell'app generale che non include il contesto del tenant). Durante il processo di accesso, l'utente Guest sceglie le **Opzioni di accesso** e quindi seleziona **Accedi a un'organizzazione**. L'utente digita quindi il nome dell'organizzazione e continua ad accedere usando le credenziali di Google.

Gli utenti guest di Google possono usare anche gli endpoint dell'applicazione che includono le informazioni del tenant, ad esempio:

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

È anche possibile assegnare agli utenti guest di Google un collegamento diretto a un'applicazione o a una risorsa includendo, ad esempio, le informazioni sul tenant `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>` .

## <a name="deprecation-of-webview-sign-in-support"></a>Deprecazione del supporto per l'accesso WebView

A partire dal 4 gennaio 2021, Google sta [deprecando il supporto per l'accesso a WebView incorporato](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Se si usa la Federazione di Google o l' [iscrizione self-service con Gmail](identity-providers.md), è necessario testare le applicazioni native line-of-business per la compatibilità. Se le app includono contenuto WebView che richiede l'autenticazione, gli utenti di Google Gmail non saranno in grado di eseguire l'autenticazione. Di seguito sono riportati gli scenari noti che influiranno sugli utenti di Gmail:

- App di Windows che usano WebView incorporato o WebAccountManager (WAM) nelle versioni precedenti di Windows.
- Altre app native sviluppate che usano un Framework del browser incorporato per l'autenticazione.

Questa modifica non influisce sui seguenti effetti:

- App di Windows che usano WebView incorporato o WebAccountManager (WAM) nelle versioni più recenti di Windows
- App Microsoft iOS
- Identità di g Suite, ad esempio quando si usa la [Federazione diretta](direct-federation.md) basata su SAML con G Suite

Stiamo continuando a testare diverse piattaforme e scenari e aggiorniamo questo articolo in modo appropriato.
### <a name="to-test-your-apps-for-compatibility"></a>Per testare la compatibilità delle app

1. Segui le [istruzioni di Google](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html) per determinare se le tue app sono interessate.
2. Usando Fiddler o un altro strumento di test, inserire un'intestazione durante l'accesso e usare un'identità esterna di Google per testare l'accesso:

   1. Aggiungere Google-Accounts-Check-OAuth-login: true alle intestazioni di richiesta HTTP quando le richieste vengono inviate a accounts.google.com.
   1. Provare ad accedere all'app immettendo un indirizzo Gmail nella pagina di accesso di accounts.google.com.
   1. Se l'accesso non riesce e viene visualizzato un errore simile al seguente: "il browser o l'app potrebbe non essere sicura", le identità esterne di Google verranno bloccate dall'accesso.

3. Per risolvere il problema, eseguire una delle operazioni seguenti:

   - Se l'app di Windows usa WebView incorporato o WebAccountManager (WAM) in una versione precedente di Windows, eseguire l'aggiornamento alla versione più recente di Windows.
   - Modificare le app per usare il browser di sistema per l'accesso. Per informazioni dettagliate, vedere [interfaccia utente Web di Visual Studio System incorporato](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui) nella documentazione di MSAL.NET.  


## <a name="step-1-configure-a-google-developer-project"></a>Passaggio 1: Configurare un progetto di Google Developers
Per prima cosa, creare un nuovo progetto nella console di sviluppatori di Google per ottenere un ID client e un segreto client che è possibile aggiungere in un secondo momento al Azure Active Directory (Azure AD). 
1. Passare alle API di Google all'indirizzo https://console.developers.google.com e accedere con l'account Google. È consigliabile usare l'account condiviso di un team Google.
2. Se richiesto, accettare le condizioni del servizio.
3. Creare un nuovo progetto: nell'angolo superiore sinistro della pagina selezionare l'elenco progetto, quindi nella pagina **Selezione progetto** selezionare **nuovo progetto**.
4. Nella pagina **nuovo progetto** assegnare un nome al progetto, ad esempio **Azure ad B2B**, quindi selezionare **Crea**: 
   
   ![Screenshot che mostra una nuova pagina del progetto.](media/google-federation/google-new-project.png)

4. Nella pagina **api & Services** selezionare **Visualizza** nel nuovo progetto.

5. Selezionare **Vai a API Panoramica** nella scheda API. Selezionare la **schermata di consenso OAuth**.

6. Selezionare **External** (Esterno), quindi **Create** (Crea). 

7. Nella **schermata di consenso OAuth** immettere il nome di un' **applicazione**:

   ![Screenshot che mostra la schermata di consenso di Google OAuth.](media/google-federation/google-oauth-consent-screen.png)

8. Scorrere fino alla sezione **domini autorizzati** e immettere **microsoftonline.com**:

   ![Screenshot che mostra la sezione domini autorizzati.](media/google-federation/google-oauth-authorized-domains.PNG)

9. Selezionare **Salva**.

10. Selezionare **Credentials** (Credenziali). Scegliere **ID client OAuth** dal menu **Crea credenziali** :

    ![Screenshot che mostra il menu di creazione delle credenziali di Google APIs.](media/google-federation/google-api-credentials.png)

11. In **Tipo di applicazione** selezionare **Applicazione Web**. Assegnare all'applicazione un nome appropriato, ad esempio **Azure ad B2B**. In **URI di reindirizzamento autorizzati** immettere gli URI seguenti:
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(dove `<tenant ID>` è l'ID tenant)
   
    > [!NOTE]
    > Per trovare l'ID tenant, passare alla [portale di Azure](https://portal.azure.com). In **Azure Active Directory** selezionare **Proprietà** e copiare l' **ID tenant**.

    ![Screenshot che mostra la sezione relativa agli URI di reindirizzamento autorizzati.](media/google-federation/google-create-oauth-client-id.png)

12. Selezionare **Crea**. Copiare l'ID client e il segreto client. Verranno usati quando si aggiunge il provider di identità nel portale di Azure.

    ![Screenshot che mostra l'ID client OAuth e il segreto client.](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Passaggio 2: Configurare la federazione con Google in Azure AD 
È ora possibile impostare l'ID client e il segreto client di Google. A tale scopo, è possibile usare il portale di Azure o PowerShell. Assicurarsi di testare la configurazione di Google Federation invitandolo. Usare un indirizzo Gmail e provare a riscattare l'invito con l'account Google invitato. 

**Per configurare Google Federation nella portale di Azure** 
1. Accedere al [portale di Azure](https://portal.azure.com). Nel riquadro sinistro selezionare **Azure Active Directory**. 
2. Selezionare **Identità esterne**.
3. Selezionare **tutti i provider di identità** e quindi fare clic sul pulsante **Google** .
4. Immettere l'ID client e il segreto client ottenuti in precedenza. Selezionare **Salva**: 

   ![Screenshot che mostra la pagina Aggiungi provider di identità Google.](media/google-federation/google-identity-provider.png)

**Per configurare la federazione con Google tramite PowerShell**
1. Installare la versione più recente del modulo di Azure AD PowerShell per Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Eseguire questo comando: `Connect-AzureAD`
3. Al prompt di accesso, accedere con l'account di amministratore globale gestito.  
4. Eseguire il comando seguente: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > Usare l'ID client e il segreto client dall'app creata in "passaggio 1: configurare un progetto per sviluppatori Google". Per ulteriori informazioni, vedere [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview). 
 
## <a name="how-do-i-remove-google-federation"></a>Come si rimuove la federazione con Google?
È possibile rimuovere la configurazione della federazione con Google. In tal caso, gli utenti guest di Google che hanno già riscattato l'invito non saranno in grado di eseguire l'accesso. È tuttavia possibile concedere loro nuovamente l'accesso alle risorse eliminandoli dalla directory e invitandoli di nuovo. 
 
**Per eliminare Google Federation nel portale di Azure AD**
1. Accedere al [portale di Azure](https://portal.azure.com). Nel riquadro sinistro selezionare **Azure Active Directory**. 
2. Selezionare **Identità esterne**.
3. Selezionare **Tutti i provider di identità**.
4. Nella riga di **Google** , selezionare il pulsante con i puntini di sospensione (**...**) e quindi selezionare **Elimina**. 
   
   ![Screenshot che mostra il pulsante Elimina per il provider di identità basato su social network.](media/google-federation/google-social-identity-providers.png)

1. Selezionare **Sì** per confermare l'eliminazione. 

**Per eliminare Google Federation usando PowerShell** 
1. Installare la versione più recente del modulo di Azure AD PowerShell per Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Eseguire `Connect-AzureAD`.  
4. Nella richiesta di accesso accedere con l'account amministratore globale gestito.  
5. Immettere il comando seguente:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Per altre informazioni, vedere l'articolo relativo al cmdlet [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview).
