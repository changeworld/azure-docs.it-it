---
title: Configurare l'autenticazione di Azure AD
description: Informazioni su come configurare l'autenticazione di Azure Active Directory come provider di identità per un'app del servizio app o di Funzioni di Azure.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 04/14/2020
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: b1254e7db0e62d08ea2a3d6d30f2abd379675c55
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078316"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>Configurare un'app del servizio app o di Funzioni di Azure per l'uso dell'account di accesso di Azure AD

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo articolo illustra come configurare l'autenticazione per app Azure servizio o funzioni di Azure in modo che l'app esegua l'accesso degli utenti con la [piattaforma di identità Microsoft](../active-directory/develop/v2-overview.md) (Azure ad) come provider di autenticazione.

La funzionalità di autenticazione del servizio app può creare automaticamente una registrazione dell'app con la piattaforma di identità Microsoft. È anche possibile usare una registrazione creata separatamente dall'utente o da un amministratore di directory.

- [Crea automaticamente una nuova registrazione dell'app](#express)
- [Usa una registrazione esistente creata separatamente](#advanced)

> [!NOTE]
> L'opzione per creare una nuova registrazione non è disponibile per i cloud governativi. Definire invece [una registrazione separatamente](#advanced).

## <a name="create-a-new-app-registration-automatically"></a><a name="express"> </a>Crea automaticamente una nuova registrazione dell'app

Questa opzione è progettata per semplificare l'abilitazione dell'autenticazione e richiede solo pochi clic.

1. Accedere al [portale di Azure] e passare all'app.
1. Selezionare **autenticazione** nel menu a sinistra. Fare clic su **Aggiungi provider di identità**.
1. Selezionare **Microsoft** nell'elenco a discesa provider di identità. Per impostazione predefinita, è selezionata l'opzione per creare una nuova registrazione. È possibile modificare il nome della registrazione o i tipi di account supportati.

    Un segreto client verrà creato e archiviato come [impostazione dell'applicazione](./configure-common.md#configure-app-settings) slot-Sticky denominata `MICROSOFT_PROVIDER_AUTHENTICATION_SECRET` . È possibile aggiornare tale impostazione in un secondo momento per usare i [riferimenti Key Vault](./app-service-key-vault-references.md) se si vuole gestire il segreto in Azure Key Vault.

1. Se questo è il primo provider di identità configurato per l'applicazione, verrà visualizzata anche una sezione **impostazioni di autenticazione del servizio app** . In caso contrario, è possibile passare al passaggio successivo.
    
    Queste opzioni determinano il modo in cui l'applicazione risponde alle richieste non autenticate e le selezioni predefinite reindirizza tutte le richieste di accesso con questo nuovo provider. È possibile modificare la personalizzazione di questo comportamento ora oppure modificare le impostazioni in un secondo momento dalla schermata principale di **autenticazione** scegliendo **modifica** accanto a **impostazioni di autenticazione**. Per altre informazioni su queste opzioni, vedere [flusso di autenticazione](overview-authentication-authorization.md#authentication-flow).

1. Opzionale Fare clic su **Avanti: autorizzazioni** e aggiungere gli ambiti necessari per l'applicazione. Questi verranno aggiunti alla registrazione dell'app, ma è possibile modificarli anche in un secondo momento.
1. Fare clic su **Aggiungi**.

A questo punto si è pronti per usare la piattaforma di identità Microsoft per l'autenticazione nell'app. Il provider verrà elencato nella schermata **Authentication** . Da qui è possibile modificare o eliminare questa configurazione del provider.

Per un esempio di configurazione di Azure AD account di accesso per un'app Web che accede ad archiviazione di Azure e Microsoft Graph, vedere [questa esercitazione](scenario-secure-app-authentication-app-service.md).

## <a name="use-an-existing-registration-created-separately"></a><a name="advanced"> </a>Usa una registrazione esistente creata separatamente

È anche possibile registrare manualmente l'applicazione per la piattaforma di identità Microsoft, personalizzando la registrazione e configurando l'autenticazione del servizio app con i dettagli di registrazione. Questa operazione è utile, ad esempio, se si vuole usare la registrazione di un'app da un tenant di Azure AD diverso da quello in cui si trova l'applicazione.

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>Creare una registrazione in Azure AD per l'app del servizio app

Prima di tutto si creerà la registrazione dell'app. A tale scopo, raccogliere le informazioni seguenti, che sarà necessario in un secondo momento quando si configura l'autenticazione nell'app del servizio app:

- ID client
- ID tenant
- Segreto client (facoltativo)
- URI dell'ID applicazione

Per registrare l'app, seguire questa procedura:

1. Accedere al [Azure portal], cercare e selezionare **Servizi app** e quindi selezionare la propria app. Prendere nota dell'**URL** dell'app. Verrà usato per configurare la registrazione dell'app di Azure Active Directory.
1. Dal menu del portale selezionare **Azure Active Directory**, quindi passare alla scheda **registrazioni app** e selezionare **nuova registrazione**.
1. Nella pagina **Registra un'applicazione** immettere un valore per **Nome** per la registrazione dell'app.
1. In **URI di reindirizzamento** selezionare **Web** e digitare `<app-url>/.auth/login/aad/callback`. Ad esempio: `https://contoso.azurewebsites.net/.auth/login/aad/callback`.
1. Selezionare **Registra**.
1. Una volta creata la registrazione dell'app, copiare i valori di **ID applicazione (client)** e **ID della directory (tenant)** , che serviranno più avanti.
1. Selezionare **Autenticazione**. In **Concessione implicita** abilitare **Token ID** per consentire gli accessi utente OpenID Connect dal servizio app.
1. (Facoltativo) Selezionare **Personalizzazione**. In **URL pagina iniziale** immettere l'URL dell'app del servizio app e selezionare **Salva**.
1. Selezionare **Esporre un'API** > **Imposta**. Per l'app a singolo tenant, incollare l'URL dell'app del servizio app e selezionare **Salva**, mentre per l'app multi-tenant, incollare l'URL basato su uno dei domini verificati del tenant e quindi selezionare **Salva**.

   > [!NOTE]
   > Questo valore corrisponde a **URI ID applicazione** della registrazione dell'app. Se l'app Web richiede l'accesso a un'API nel cloud, il valore di **URI ID applicazione** dell'app Web è necessario quando si configura la risorsa del servizio app cloud. È possibile usare questo valore se ad esempio si vuole che il servizio cloud conceda esplicitamente l'accesso all'app Web.

1. Selezionare **Aggiungi un ambito**.
   1. In **Nome ambito** immettere *user_impersonation*.
   1. Nelle caselle di testo immettere il nome dell'ambito del consenso e la descrizione da mostrare agli utenti nella pagina del consenso. Immettere ad esempio *Accesso alla mia app*.
   1. Selezionare **Aggiungi ambito**.
1. (Facoltativo) Per creare un segreto client, selezionare **Certificati e segreti** > **Nuovo segreto client** > **Aggiungi**. Copiare il valore del segreto client visualizzato nella pagina. Non verrà più visualizzato.
1. (Facoltativo) Per aggiungere più valori per **URL di risposta**, selezionare **Autenticazione**.

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>Abilitare Azure Active Directory nell'app del servizio app

1. Accedere al [portale di Azure] e passare all'app.
1. Selezionare **autenticazione** nel menu a sinistra. Fare clic su **Aggiungi provider di identità**.
1. Selezionare **Microsoft** nell'elenco a discesa provider di identità.
1. Per **tipo di registrazione dell'app**, è possibile scegliere di selezionare **una registrazione dell'app esistente in questa directory** che raccoglierà automaticamente le informazioni necessarie sull'app. Se la registrazione viene eseguita da un altro tenant oppure non si dispone dell'autorizzazione per visualizzare l'oggetto di registrazione, scegliere **specificare i dettagli di una registrazione dell'app esistente**. Per questa opzione, è necessario specificare i dettagli di configurazione seguenti:

    |Campo|Descrizione|
    |-|-|
    |ID applicazione (client)| Usare il valore di **ID applicazione (client)** della registrazione dell'app. |
    |Segreto client (facoltativo)| Usare il segreto client generato nella registrazione dell'app. Con un segreto client, viene usato il flusso ibrido e il servizio app restituisce i token di accesso e aggiornamento. Quando il segreto client non è impostato, viene usato il flusso implicito e viene restituito solo un token ID. Questi token vengono inviati dal provider e archiviati nell'archivio dei token EasyAuth.|
    |URL autorità di certificazione| Usare `<authentication-endpoint>/<tenant-id>/v2.0` e sostituire *\<authentication-endpoint>* con l' [endpoint di autenticazione per l'ambiente cloud](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) , ad esempio " https://login.microsoftonline.com " per Azure globale, sostituendo anche *\<tenant-id>* con l'ID di **Directory (tenant)** in cui è stata creata la registrazione dell'app. Questo valore viene usato per reindirizzare gli utenti al tenant di Azure AD corretto, oltre che per scaricare i metadati appropriati in modo da determinare, ad esempio, i valori corretti delle chiavi di firma del token e dell'attestazione dell'autorità emittente del token corretti. Per le applicazioni che usano Azure AD V1 e per le app di funzioni di Azure, omettere `/v2.0` nell'URL.|
    |Destinatari token consentiti| Se si tratta di un'app cloud o server e si vogliono consentire i token di autenticazione di un'app Web, aggiungere qui il valore di **URI dell'ID applicazione** dell'app Web. L'**ID client** configurato viene *sempre* considerato implicitamente come destinatario consentito.|

    Il segreto client verrà archiviato come [impostazione dell'applicazione](./configure-common.md#configure-app-settings) slot-Sticky denominata `MICROSOFT_PROVIDER_AUTHENTICATION_SECRET` . È possibile aggiornare tale impostazione in un secondo momento per usare i [riferimenti Key Vault](./app-service-key-vault-references.md) se si vuole gestire il segreto in Azure Key Vault.

1. Se questo è il primo provider di identità configurato per l'applicazione, verrà visualizzata anche una sezione **impostazioni di autenticazione del servizio app** . In caso contrario, è possibile passare al passaggio successivo.
    
    Queste opzioni determinano il modo in cui l'applicazione risponde alle richieste non autenticate e le selezioni predefinite reindirizza tutte le richieste di accesso con questo nuovo provider. È possibile modificare la personalizzazione di questo comportamento ora oppure modificare le impostazioni in un secondo momento dalla schermata principale di **autenticazione** scegliendo **modifica** accanto a **impostazioni di autenticazione**. Per altre informazioni su queste opzioni, vedere [flusso di autenticazione](overview-authentication-authorization.md#authentication-flow).

1. Fare clic su **Aggiungi**.

A questo punto si è pronti per usare la piattaforma di identità Microsoft per l'autenticazione nell'app. Il provider verrà elencato nella schermata **Authentication** . Da qui è possibile modificare o eliminare questa configurazione del provider.

## <a name="configure-client-apps-to-access-your-app-service"></a>Configurare le app client per accedere al servizio app

Nella sezione precedente è stato registrato il servizio app o la funzione di Azure per autenticare gli utenti. Questa sezione illustra come registrare le app native client o daemon in modo che possano richiedere l'accesso alle API esposte dal servizio app per conto di utenti o se stesse. Il completamento dei passaggi descritti in questa sezione non è necessario se si desidera autenticare gli utenti.

### <a name="native-client-application"></a>Applicazione client nativa

È possibile registrare i client nativi per richiedere l'accesso alle API dell'app del servizio app per conto di un utente connesso.

1. Nel [Azure portal] selezionare **Active Directory** > **Registrazioni app** > **Nuova registrazione**.
1. Nella pagina **Registra un'applicazione** immettere un valore per **Nome** per la registrazione dell'app.
1. In **URI di reindirizzamento** selezionare **Client pubblico (per dispositivi mobili e desktop)** e digitare l'URL `<app-url>/.auth/login/aad/callback`. Ad esempio: `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > Per un'applicazione di Microsoft Store, usare invece il [SID pacchetto](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library#package-sid) come URI.
1. Selezionare **Create** (Crea).
1. Dopo la creazione della registrazione dell'app, copiare il valore di **ID applicazione (client)** .
1. Selezionare **Autorizzazioni API** > **Aggiungi un'autorizzazione** > **Le mie API**.
1. Selezionare la registrazione creata in precedenza per l'app del servizio app. Se la registrazione dell'app non è visibile, verificare di aver aggiunto l'ambito **user_impersonation** in [Creare una registrazione di app in Azure AD per l'app del servizio app](#register).
1. Selezionare **user_impersonation** in **Autorizzazioni delegate**, quindi selezionare **Aggiungi autorizzazioni**.

A questo punto è stata configurata un'applicazione client nativa che può richiedere l'accesso all'app del servizio app per conto di un utente.

### <a name="daemon-client-application-service-to-service-calls"></a>Applicazione client daemon (chiamate da servizio a servizio)

L'applicazione può acquisire un token per chiamare un'API Web ospitata nel servizio app o nell'app per le funzioni per conto di se stessa (non per conto di un utente). Questo scenario è utile per le applicazioni daemon non interattive che eseguono attività senza un utente connesso. Usa la concessione di [credenziali client](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) OAuth 2.0 standard.

1. Nel [Azure portal] selezionare **Active Directory** > **Registrazioni app** > **Nuova registrazione**.
1. Nella pagina **Registra un'applicazione** immettere un valore in **Nome** per la registrazione dell'app daemon.
1. Per un'applicazione daemon, non è necessario un URI di reindirizzamento, quindi è possibile lasciare questo campo vuoto.
1. Selezionare **Create** (Crea).
1. Dopo la creazione della registrazione dell'app, copiare il valore di **ID applicazione (client)** .
1. Selezionare **Certificati e segreti** > **Nuovo segreto client** > **Aggiungi**. Copiare il valore del segreto client visualizzato nella pagina. Non verrà più visualizzato.

È ora possibile [richiedere un token di accesso usando l'ID client e il segreto client](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret) impostando il parametro `resource` sull'**URI ID applicazione** dell'app di destinazione. Il token di accesso generato può quindi essere presentato all'app di destinazione usando l'[intestazione dell'autorizzazione OAuth 2.0](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#use-the-access-token-to-access-the-secured-resource) standard e l'autenticazione/autorizzazione del servizio app convaliderà e userà il token come di consueto per indicare che il chiamante (in questo caso un'applicazione, non un utente) è autenticato.

Al momento, questo consente a _qualsiasi_ applicazione client nel tenant di Azure AD di richiedere un token di accesso ed eseguire l'autenticazione all'app di destinazione. Per imporre all'_autorizzazione_ di consentire solo determinate applicazioni client, è necessario eseguire alcune operazioni di configurazione aggiuntive.

1. [Definire un ruolo dell'app](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) nel manifesto della registrazione dell'app che rappresenta il servizio app o l'app per le funzioni che si vuole proteggere.
1. Nella registrazione app che rappresenta il client che deve essere autorizzato selezionare **Autorizzazioni API** > **Aggiungi un'autorizzazione** > **API personali**.
1. Selezionare la registrazione app creata in precedenza. Se la registrazione app non è visualizzata, verificare di aver [aggiunto un ruolo dell'app](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md).
1. In **Autorizzazioni applicazione** selezionare il ruolo dell'app creato in precedenza e quindi selezionare **Aggiungi autorizzazioni**.
1. Assicurarsi di fare clic su **Fornisci il consenso amministratore** per autorizzare l'applicazione client a richiedere l'autorizzazione.
1. Analogamente allo scenario precedente (prima dell'aggiunta di eventuali ruoli), è ora possibile [richiedere un token di accesso](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret) per la stessa `resource` di destinazione e il token di accesso includerà un'attestazione `roles` contenente i ruoli dell'app autorizzati per l'applicazione client.
1. All'interno del servizio app di destinazione o del codice dell'app per le funzioni, è ora possibile verificare che i ruoli previsti siano presenti nel token. Questa operazione non viene eseguita dall'autenticazione/autorizzazione del servizio app. Per altre informazioni, vedere [Accedere alle attestazioni utente](app-service-authentication-how-to.md#access-user-claims).

È stata configurata un'applicazione client daemon che può accedere all'app del servizio app usando la propria identità.

## <a name="best-practices"></a>Procedure consigliate

Indipendentemente dalla configurazione usata per configurare l'autenticazione, le procedure consigliate seguenti consentiranno di rendere più sicure le applicazioni e il tenant:

- Assegnare a ogni app del servizio app le autorizzazioni specifiche e il consenso.
- Configurare ogni app del servizio app con la relativa registrazione.
- Evitare la condivisione delle autorizzazioni tra ambienti usando registrazioni di app separate per slot di distribuzione distinti. Quando si esegue il test di nuovo codice, questa procedura consente di evitare problemi che influiscono sull'app di produzione.

## <a name="next-steps"></a><a name="related-content"> </a>Passaggi successivi

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
* [Esercitazione: autenticare e autorizzare gli utenti in un'app Web che accede ad archiviazione di Azure e Microsoft Graph](scenario-secure-app-authentication-app-service.md)
* [Esercitazione: Autenticare e autorizzare gli utenti end-to-end nel Servizio app di Azure](tutorial-auth-aad.md)
<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
