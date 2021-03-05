---
title: "Avvio rapido: Registrare un'app in Microsoft Identity Platform | Azure"
description: Questo argomento di avvio rapido illustra come registrare un'applicazione con Microsoft Identity Platform.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, contperf-fy21q1, contperf-fy21q2
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: c72ae2a2e6dbd2278bdd78f26c145386be22764e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175431"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Guida introduttiva: Registrare un'applicazione con Microsoft Identity Platform

Questo argomento di avvio rapido illustra come registrare un'app nel portale di Azure in modo che Microsoft Identity Platform possa fornire servizi di autenticazione e autorizzazione per l'applicazione e i relativi utenti.

La piattaforma Microsoft Identity esegue la gestione delle identità e degli accessi solo per le applicazioni registrate. Sia che si tratti di un'applicazione client, come un'app Web o per dispositivi mobili, oppure di un'API Web a supporto di un'app client, la registrazione consente di stabilire una relazione di trust tra l'applicazione e il provider di identità, ovvero Microsoft Identity Platform.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Completamento della Guida introduttiva [per la configurazione di un tenant](quickstart-create-new-tenant.md) .

## <a name="register-an-application"></a>Registrare un'applicazione

La registrazione dell'applicazione consente di stabilire una relazione di trust tra l'app e Microsoft Identity Platform. La relazione di trust è unidirezionale, ovvero l'app considera attendibile Microsoft Identity Platform e non viceversa.

Per creare la registrazione dell'app, seguire questa procedura:

1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure</a>.
1. Se si ha accesso a più tenant, nel menu in alto usare il filtro **directory + sottoscrizione** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: per selezionare il tenant in cui si vuole registrare un'applicazione.
1. Cercare e selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Registrazioni app** > **Nuova registrazione**.
1. Immettere un **nome** visualizzato per l'applicazione. Gli utenti dell'applicazione potrebbero visualizzare il nome visualizzato quando usano l'app, ad esempio durante l'accesso.
    È possibile modificare il nome visualizzato in qualsiasi momento e più registrazioni di app possono condividere lo stesso nome. L'ID applicazione (client) generato automaticamente dall'app, non il nome visualizzato, identifica in modo univoco l'app all'interno della piattaforma di identità.
1. Specificare gli utenti che possono usare l'applicazione, talvolta denominata *destinatari di accesso*.

    | Tipi di account supportati | Descrizione |
    |-------------------------|-------------|
    | **Account solo in questa directory organizzativa** | Selezionare questa opzione se si intende creare un'applicazione utilizzabile solo da utenti (o guest) nel tenant *personale*.<br><br>Spesso denominata applicazione *line-of-business* (LOB), questa app è un'applicazione a *tenant singolo* nella piattaforma di identità Microsoft. |
    | **Account in qualsiasi directory organizzativa** | Selezionare questa opzione se si desidera che gli utenti in *qualsiasi* tenant di Azure Active Directory (Azure ad) siano in grado di utilizzare l'applicazione. Questa opzione è appropriata se, ad esempio, si sta creando un'applicazione SaaS (Software-As-A-Service) che si intende fornire a più organizzazioni.<br><br>Questo tipo di app è noto come applicazione *multi-tenant* nella piattaforma di identità Microsoft. |
    | **Account in qualsiasi directory organizzativa e account Microsoft personali** | Selezionare questa opzione per includere il set più ampio possibile di clienti.<br><br>Selezionando questa opzione, si sta registrando un'applicazione *multi-tenant* che può supportare anche gli utenti con *account Microsoft* personali. |
    | **Account Microsoft personali** | Selezionare questa opzione se si sta creando un'applicazione solo per gli utenti che hanno account Microsoft personali. Gli account Microsoft personali includono gli account Skype, Xbox, Live e Hotmail. |

1. Non immettere alcun valore per l' **URI di reindirizzamento (facoltativo)**. Nella sezione successiva verrà configurato un URI di reindirizzamento.
1. Selezionare **Registra** per completare la registrazione iniziale dell'app.

    :::image type="content" source="media/quickstart-register-app/portal-02-app-reg-01.png" alt-text="Screenshot del portale di Azure in un Web browser, che mostra il riquadro registra un'applicazione.":::

Al termine della registrazione, il portale di Azure Visualizza il riquadro **Panoramica** della registrazione dell'app. Viene visualizzato l' **ID dell'applicazione (client)**. Detto anche *ID client*, questo valore identifica in modo univoco l'applicazione nella piattaforma di identità Microsoft.

Il codice dell'applicazione o, in genere, una libreria di autenticazione usata nell'applicazione usa anche l'ID client. L'ID viene usato come parte della convalida dei token di sicurezza ricevuti dalla piattaforma di identità.

:::image type="content" source="media/quickstart-register-app/portal-03-app-reg-02.png" alt-text="Screenshot del portale di Azure in un Web browser, che mostra il riquadro Panoramica di una registrazione dell'app.":::

## <a name="add-a-redirect-uri"></a>Aggiungere un URI di reindirizzamento

Un *URI di reindirizzamento* è il percorso in cui la piattaforma di identità Microsoft reindirizza il client di un utente e invia i token di sicurezza dopo l'autenticazione.

In un'applicazione Web di produzione, ad esempio, l'URI di reindirizzamento è spesso un endpoint pubblico in cui viene eseguita l'app, ad esempio `https://contoso.com/auth-response`. Durante lo sviluppo capita di aggiungere l'endpoint in cui l'app viene eseguita in locale, ad esempio `https://127.0.0.1/auth-response` o `http://localhost/auth-response`.

Per aggiungere e modificare gli URI di reindirizzamento per le applicazioni registrate, configurarne le [impostazioni della piattaforma](#configure-platform-settings).

### <a name="configure-platform-settings"></a>Configurare le impostazioni della piattaforma

Le impostazioni per ogni tipo di applicazione, inclusi gli URI di reindirizzamento, vengono configurate in **Configurazioni della piattaforma** nel portale di Azure. Con alcune piattaforme, ad esempio le applicazioni **Web** e **a pagina singola**, è necessario specificare manualmente un URI di reindirizzamento. Per altre piattaforme, ad esempio per dispositivi mobili e desktop, è possibile selezionare gli URI di reindirizzamento generati quando si configurano le altre impostazioni.

Per configurare le impostazioni dell'applicazione in base alla piattaforma o al dispositivo di destinazione:

1. Nel portale di Azure, in **registrazioni app**, selezionare l'applicazione.
1. In **Gestisci** selezionare **Autenticazione**.
1. In **Configurazioni della piattaforma** selezionare **Aggiungi una piattaforma**.
1. In **Configura piattaforme** selezionare il riquadro per il tipo di applicazione (piattaforma) per configurarne le impostazioni.

    :::image type="content" source="media/quickstart-register-app/portal-04-app-reg-03-platform-config.png" alt-text="Screenshot del riquadro Configurazione piattaforma nel portale di Azure." border="false":::

    | Piattaforma | Impostazioni di configurazione |
    | -------- | ---------------------- |
    | **Web** | Immettere un **URI di reindirizzamento** per l'app. Questo URI è il percorso in cui la piattaforma Microsoft Identity reindirizza il client di un utente e invia i token di sicurezza dopo l'autenticazione.<br/><br/>Selezionare questa piattaforma per le applicazioni Web standard eseguite in un server. |
    | **Applicazione a pagina singola** | Immettere un **URI di reindirizzamento** per l'app. Questo URI è il percorso in cui la piattaforma Microsoft Identity reindirizza il client di un utente e invia i token di sicurezza dopo l'autenticazione.<br/><br/>Selezionare questa piattaforma se si sta compilando un'app Web sul lato client usando JavaScript o un Framework come un assembly Web angolare, Vue.js, React.js o blazer. |
    | **iOS/macOS** | Immettere l' **ID bundle** dell'app. Trovarlo nelle **impostazioni di compilazione** o in Xcode in *info. plist*.<br/><br/>Quando si specifica un **ID bundle**, viene generato automaticamente un URI di reindirizzamento. |
    | **Android** | Immettere il **nome del pacchetto** dell'app. Trovarlo nel file di *AndroidManifest.xml* . Generare e immettere anche l' **hash della firma**.<br/><br/>Quando si specificano queste impostazioni, viene generato automaticamente un URI di reindirizzamento. |
    | **Applicazioni per dispositivi mobili e desktop** | Selezionare uno degli **URI di reindirizzamento suggeriti**. Oppure specificare un **URI di reindirizzamento personalizzato**.<br/><br/>Per le applicazioni desktop, è consigliabile<br/>`https://login.microsoftonline.com/common/oauth2/nativeclient`<br/><br/>Selezionare questa piattaforma per le applicazioni per dispositivi mobili che non usano la versione più recente di Microsoft Authentication Library (MSAL) o non usano un broker. Selezionare questa piattaforma anche per le applicazioni desktop. |
1. Selezionare **Configura** per completare la configurazione della piattaforma.

### <a name="redirect-uri-restrictions"></a>Restrizioni relative agli URI di reindirizzamento

Esistono alcune restrizioni al formato degli URI di reindirizzamento aggiunti alla registrazione di un'app. Per informazioni dettagliate su queste restrizioni, vedere [restrizioni e limitazioni dell'URI di reindirizzamento (URL di risposta)](reply-url.md).

## <a name="add-credentials"></a>Aggiungere le credenziali

Le credenziali vengono usate dalle [applicazioni client riservate](msal-client-applications.md) che accedono a un'API Web. Esempi di client riservati sono [app Web](scenario-web-app-call-api-overview.md), altre [API Web](scenario-protected-web-api-overview.md)o applicazioni di tipo [servizio e daemon](scenario-daemon-overview.md). Le credenziali consentono all'applicazione di eseguire l'autenticazione in modo autonomo, senza richiedere alcuna interazione utente in fase di esecuzione. 

È possibile aggiungere sia certificati che segreti client (una stringa) come credenziali della registrazione dell'app client riservata.

:::image type="content" source="media/quickstart-register-app/portal-05-app-reg-04-credentials.png" alt-text="Screenshot del portale di Azure, che mostra il riquadro certificati e segreti in una registrazione dell'app.":::

### <a name="add-a-certificate"></a>Aggiungere un certificato

Un certificato è talvolta denominato *chiave pubblica*, ma è il tipo di credenziale consigliato. Garantisce maggiore sicurezza rispetto a un segreto client. Per ulteriori informazioni sull'utilizzo di un certificato come metodo di autenticazione nell'applicazione, vedere [credenziali del certificato di autenticazione dell'applicazione della piattaforma Microsoft Identity](active-directory-certificate-credentials.md).

1. Nel portale di Azure, in **registrazioni app**, selezionare l'applicazione.
1. Selezionare **Certificati e segreti** > **Carica certificato**.
1. Selezionare il file che si desidera caricare. Deve essere uno dei seguenti tipi di file: *. cer*, *. pem*, *. CRT*.
1. Selezionare **Aggiungi**.

### <a name="add-a-client-secret"></a>Aggiungere un segreto client

Il segreto client è anche noto come *password dell'applicazione*. Si tratta di un valore di stringa che l'app può usare al posto di un certificato per l'identità. Il segreto client è il più semplice dei due tipi di credenziali da usare. Viene spesso usato durante lo sviluppo, ma è considerato meno sicuro di un certificato. Usare i certificati nelle applicazioni in esecuzione nell'ambiente di produzione. 

Per ulteriori informazioni sulle raccomandazioni relative alla sicurezza delle applicazioni, vedere procedure consigliate e consigli per la [piattaforma di identità Microsoft](identity-platform-integration-checklist.md#security).

1. Nel portale di Azure, in **registrazioni app**, selezionare l'applicazione.
1. Selezionare **Certificati e segreti** >  **Nuovo segreto client**.
1. Aggiungere una descrizione per il segreto client.
1. Selezionare una durata.
1. Selezionare **Aggiungi**.
1. *Registrare il valore del segreto* da usare nel codice dell'applicazione client. Il valore del segreto *non viene mai visualizzato di nuovo* dopo aver lasciato questa pagina.


## <a name="next-steps"></a>Passaggi successivi

Le applicazioni client devono in genere accedere alle risorse in un'API Web. È possibile proteggere l'applicazione client tramite la piattaforma di identità Microsoft. È anche possibile usare la piattaforma per autorizzare l'accesso con ambito basato sulle autorizzazioni all'API Web.

Passare alla Guida introduttiva successiva della serie per creare un'altra registrazione dell'app per l'API Web ed esporre gli ambiti.

> [!div class="nextstepaction"]
> [Configurare un'applicazione per esporre un'API Web](quickstart-configure-app-expose-web-apis.md)
