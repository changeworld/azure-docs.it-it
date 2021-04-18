---
title: Autenticazione basata su intestazione con PingAccess per Azure AD Application Proxy
description: Pubblicare le applicazioni con PingAccess e il proxy dell'applicazione per supportare l'autenticazione basata su intestazione.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 23008d785c27b901f3487d3eddff7cb8e7529f6e
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600081"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Autenticazione basata su intestazione per l'accesso Single Sign-On con il proxy di applicazione e PingAccess

Azure Active Directory (Azure AD) Application Proxy ha collaborato con PingAccess in modo che i clienti Azure AD possano accedere a più applicazioni. PingAccess offre un'altra opzione oltre [all'accesso Single Sign-On](application-proxy-configure-single-sign-on-with-headers.md)integrato basato su intestazione.

## <a name="whats-pingaccess-for-azure-ad"></a>Che cos'è PingAccess per Azure AD?

Con PingAccess per Azure AD, è possibile concedere agli utenti l'accesso e l'accesso Single Sign-On (SSO) alle applicazioni che usano intestazioni per l'autenticazione. Application Proxy tratta queste applicazioni come qualsiasi altra, usando Azure AD per autenticare l'accesso e quindi passando il traffico attraverso il servizio del connettore. PingAccess si trova davanti alle applicazioni e converte il token di accesso da Azure AD in un'intestazione. L'applicazione riceve quindi l'autenticazione nel formato che può leggere.

Gli utenti non noteranno nulla di diverso quando eseguono l'accesso per usare le applicazioni aziendali. Possono comunque lavorare da qualsiasi luogo e dispositivo. I Application Proxy indirizzano il traffico remoto a tutte le app indipendentemente dal tipo di autenticazione, in modo che i carichi siano bilanciati automaticamente.

## <a name="how-do-i-get-access"></a>Come si ottiene l'accesso?

Poiché questo scenario deriva da una relazione tra Azure Active Directory e PingAccess, sono necessarie licenze per entrambi i servizi. Le sottoscrizioni Premium di Azure Active Directory, tuttavia, includono una licenza PingAccess di base che copre fino a 20 applicazioni. Se è necessario pubblicare più di 20 applicazioni basate su intestazione, è possibile acquistare una licenza aggiuntiva da PingAccess.

Per altre informazioni, vedere [Edizioni di Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Pubblicare l'applicazione in Azure

Questo articolo consente agli utenti di pubblicare un'applicazione con questo scenario per la prima volta. Oltre a illustrare in dettaglio i passaggi di pubblicazione, viene descritto come iniziare a usare Application Proxy e PingAccess. Se entrambi i servizi sono già stati configurati ma si vuole un aggiornamento dei passaggi di pubblicazione, passare alla sezione Aggiungere l'applicazione Azure AD [con Application Proxy.](#add-your-application-to-azure-ad-with-application-proxy)

> [!NOTE]
> Poiché questo scenario è il risultato di una partnership fra Azure AD e PingAccess, alcune delle istruzioni sono presenti sul sito di Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Installare un connettore proxy di applicazione

Se è già stato abilitato Application Proxy e installato un connettore, è possibile ignorare questa sezione e passare a Aggiungere l'applicazione a Azure AD [con Application Proxy](#add-your-application-to-azure-ad-with-application-proxy).

Il Application Proxy è un servizio di Windows Server che indirizza il traffico dai dipendenti remoti alle applicazioni pubblicate. Per istruzioni più dettagliate sull'installazione, vedere [Esercitazione:](application-proxy-add-on-premises-application.md)Aggiungere un'applicazione locale per l'accesso remoto tramite Application Proxy in Azure Active Directory .

1. Accedere al portale di [Azure Active Directory come](https://aad.portal.azure.com/) amministratore dell'applicazione. Viene visualizzata **Azure Active Directory dell'interfaccia di amministrazione.**
1. Selezionare **Azure Active Directory**  >  **proxy applicazione Scaricare** il servizio  >  **connettore**. Verrà **visualizzata Application Proxy download del connettore.**

   ![Download del connettore proxy di applicazione](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Seguire le istruzioni di installazione.

Il download del connettore dovrebbe abilitare automaticamente Application Proxy per la directory, ma in caso contrario, è possibile selezionare **Abilita** Application Proxy .

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Aggiungere l'applicazione per Azure AD con Application Proxy

Nel portale di Azure è necessario eseguire due azioni. Per prima cosa, si pubblica l'applicazione con il proxy di applicazione, È quindi necessario raccogliere alcune informazioni sull'applicazione che è possibile usare durante la procedura PingAccess.

#### <a name="publish-your-application"></a>Pubblicare l'applicazione

È prima di tutto necessario pubblicare l'applicazione. Questa azione comporta:

- Aggiunta dell'applicazione locale Azure AD
- Assegnazione di un utente per testare l'applicazione e scegliere l'accesso SSO basato su intestazione
- Configurazione dell'URL di reindirizzamento dell'applicazione
- Concessione delle autorizzazioni per utenti e altre applicazioni per l'uso dell'applicazione locale

Per pubblicare un'applicazione locale:

1. Se non è stato fatto nell'ultima sezione, accedere al portale [Azure Active Directory come](https://aad.portal.azure.com/) amministratore dell'applicazione.
1. Selezionare **Applicazioni aziendali** Nuova  >  **applicazione** Aggiungere  >  **un'applicazione locale.** Verrà **visualizzata la pagina Aggiungi applicazione** locale.

   ![Aggiungere un'applicazione locale](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Compilare i campi obbligatori con le informazioni sulla nuova applicazione. Usare le indicazioni seguenti per le impostazioni.

   > [!NOTE]
   > Per una procedura dettagliata di questo passaggio, vedere [Aggiungere un'app locale](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)Azure AD .

   1. **URL interno:** in genere si specifica l'URL che consente di accedere alla pagina di accesso dell'app quando si è nella rete aziendale. Per questo scenario, il connettore deve considerare il proxy PingAccess come la prima pagina dell'applicazione. Usare il formato seguente: `https://<host name of your PingAccess server>:<port>`. La porta 3000 per impostazione predefinita, ma è possibile configurarla in PingAccess.

      > [!WARNING]
      > Per questo tipo di accesso Single Sign-On, l'URL interno deve usare `https` e non può usare `http` . Inoltre, quando si configura un'applicazione è necessario che nessuna delle due app abbia lo stesso URL interno, in quanto ciò consente al proxy dell'app di mantenere la distinzione tra le applicazioni.

   1. **Metodo di pre-autenticazione:** **scegliere Azure Active Directory**.
   1. **Translate URL in Headers (Converti URL nelle** intestazioni): **scegliere No.**

   > [!NOTE]
   > Se si tratta della prima applicazione, usare inizialmente la porta 3000 e aggiornare questa impostazione se viene modificata la configurazione di PingAccess. Per le applicazioni successive, la porta dovrà corrispondere al listener configurato in PingAccess. Altre informazioni sui [listener in PingAccess](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html).

1. Selezionare **Aggiungi**. Verrà visualizzata la pagina di panoramica per la nuova applicazione.

Assegnare ora un utente per il test dell'applicazione e scegliere l'accesso Single Sign-On basato su intestazione:

1. Nella barra laterale dell'applicazione selezionare **Utenti e gruppi** Aggiungi  >  **utenti** e  >  **gruppi \<Number> (selezionato).** Verrà visualizzato un elenco di utenti e gruppi tra cui scegliere.

   ![Mostra l'elenco di utenti e gruppi](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Selezionare un utente per il test dell'applicazione e selezionare **Seleziona**. Assicurarsi che questo account di test abbia accesso all'applicazione locale.
1. Selezionare **Assegna**.
1. Nella barra laterale dell'applicazione selezionare **Single Sign-On**  >  **Basato su intestazione.**

   > [!TIP]
   > Se è la prima volta che si usa l'accesso Single Sign-On basato su intestazione, è necessario installare PingAccess. Per assicurarsi che la sottoscrizione di Azure venga associata automaticamente all'installazione di PingAccess, usare il collegamento presente nella pagina dell'accesso Single Sign-On per scaricare PingAccess. È possibile aprire il sito di download ora o in un secondo momento.

   ![Mostra la schermata di accesso basata su intestazione e PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Selezionare **Salva**.

Assicurarsi quindi che l'URL di reindirizzamento sia impostato sull'URL esterno:

1. Nella barra **Azure Active Directory dell'interfaccia di** amministrazione selezionare **Azure Active Directory**  >  **Registrazioni app**. Viene visualizzato un elenco di applicazioni.
1. Selezionare l'applicazione.
1. Selezionare il collegamento accanto a **URI di reindirizzamento,** che mostra il numero di URI di reindirizzamento impostati per i client Web e pubblici. Verrà **\<application name> visualizzata la pagina -** Autenticazione.
1. Controllare se l'URL esterno assegnato in precedenza all'applicazione si trova **nell'elenco URI di reindirizzamento.** In caso contrario, aggiungere ora l'URL esterno usando un tipo di URI di reindirizzamento **Web** e selezionare **Salva**.

Oltre all'URL esterno, è necessario aggiungere un endpoint di autorizzazione Azure Active Directory nell'URL esterno all'elenco URI di reindirizzamento.

`https://*.msappproxy.net/pa/oidc/cb`
`https://*.msappproxy.net/`

Infine, configurare l'applicazione locale in modo che gli utenti hanno accesso in lettura e altre applicazioni hanno accesso in lettura/scrittura:

1. Nella barra **Registrazioni app** laterale per l'applicazione selezionare **Autorizzazioni API**  >  **Aggiungi un'autorizzazione** API  >  **Microsoft**  >  **Microsoft Graph**. Viene **visualizzata la** pagina Richiedi autorizzazioni API per **Microsoft Graph,** che contiene le API per Windows Azure Active Directory.

   ![Visualizza la pagina Richiedi autorizzazioni API](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Selezionare **Autorizzazioni delegate**  >    >  **User.Read**.
1. Selezionare **Autorizzazioni applicazione**  >  **Application**  >  **Application.ReadWrite.All**.
1. Selezionare **Aggiungi autorizzazioni**.
1. Nella pagina **Autorizzazioni API** selezionare Concedi il **consenso dell'amministratore per \<your directory name>**.

#### <a name="collect-information-for-the-pingaccess-steps"></a>Raccogliere informazioni per la procedura PingAccess

È necessario raccogliere queste tre informazioni (tutti i GUID) per configurare l'applicazione con PingAccess:

| Nome del Azure AD campo | Nome del campo PingAccess | Formato dati |
| --- | --- | --- |
| **ID applicazione (client)** | **ID client** | GUID |
| **ID directory (tenant)** | **Issuer** | GUID |
| `PingAccess key` | **Segreto client** | Stringa casuale |

Per raccogliere queste informazioni:

1. Nella barra **Azure Active Directory dell'interfaccia di** amministrazione selezionare **Azure Active Directory**  >  **Registrazioni app**. Viene visualizzato un elenco di applicazioni.
1. Selezionare l'applicazione. Verrà **Registrazioni app** pagina di configurazione per l'applicazione.

   ![Panoramica della registrazione per un'applicazione](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Accanto al **valore ID applicazione (client)** selezionare l'icona Copia negli **Appunti,** quindi copiarlo e salvarlo. Specificare questo valore in un secondo momento come ID client di PingAccess.
1. Successivamente, **nel valore ID directory (tenant)** selezionare anche Copia negli **Appunti,** quindi copiarlo e salvarlo. Specificare questo valore in un secondo momento come autorità di emittente di PingAccess.
1. Nella barra laterale del **Registrazioni app** per l'applicazione selezionare Certificati e **segreti** Nuovo  >  **segreto client.** Verrà **visualizzata la pagina Aggiungi un segreto** client.

   ![Mostra la pagina Aggiungi un segreto client](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. In **Descrizione** digitare `PingAccess key` .
1. In **Scadenza scegliere come** impostare la chiave PingAccess: In **1 anno,** **In 2 anni** o **Mai**.
1. Selezionare **Aggiungi**. La chiave PingAccess viene visualizzata nella tabella dei segreti client, con una stringa casuale che si riempie automaticamente nel **campo** VALUE.
1. Accanto al campo VALORE  della chiave PingAccess selezionare l'icona **Copia** negli Appunti, quindi copiarla e salvarla. Specificare questo valore in un secondo momento come segreto client di PingAccess.

**Aggiornare il `acceptMappedClaims` campo:**

1. Accedere al portale di [Azure Active Directory come](https://aad.portal.azure.com/) amministratore dell'applicazione.
1. Passare ad **Azure Active Directory** > **Registrazioni per l'app**. Viene visualizzato un elenco di applicazioni.
1. Selezionare l'applicazione.
1. Nella barra laterale della **pagina** Registrazioni app dell'applicazione selezionare **Manifesto.** Viene visualizzato il codice JSON del manifesto per la registrazione dell'applicazione.
1. Cercare il `acceptMappedClaims` campo e modificare il valore in `True` .
1. Selezionare **Salva**.

### <a name="use-of-optional-claims-optional"></a>Uso di attestazioni facoltative (facoltativo)

Le attestazioni facoltative consentono di aggiungere attestazioni standard ma non incluse per impostazione predefinita di ogni utente e tenant. È possibile configurare attestazioni facoltative per l'applicazione modificando il manifesto dell'applicazione. Per altre informazioni, vedere l'articolo [Informazioni sul manifesto Azure AD'applicazione](../develop/reference-app-manifest.md)

Esempio per includere l'indirizzo di posta elettronica nel access_token utilizzato da PingAccess:

```json
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>Uso dei criteri di mapping delle attestazioni (facoltativo)

[Criteri di mapping delle attestazioni (anteprima)](../develop/reference-claims-mapping-policy-type.md#claims-mapping-policy-properties) per gli attributi che non esistono in AzureAD. Il mapping delle attestazioni consente di eseguire la migrazione di app in ambiente pre-installazione nel cloud aggiungendo attestazioni personalizzate aggiuntive supportate dagli oggetti utente o ADFS

Per fare in modo che l'applicazione usi un'attestazione personalizzata e includa campi aggiuntivi, assicurarsi di aver creato anche un criterio di mapping delle attestazioni personalizzato e di [assegnarlo all'applicazione](../develop/active-directory-claims-mapping.md).

> [!NOTE]
> Per usare un'attestazione personalizzata, è anche necessario avere un criterio personalizzato definito e assegnato all'applicazione. Questo criterio deve includere tutti gli attributi personalizzati necessari.
>
> È possibile eseguire la definizione e l'assegnazione dei criteri tramite PowerShell o Microsoft Graph. Se si usano in PowerShell, potrebbe essere necessario prima usare e quindi `New-AzureADPolicy` assegnarlo all'applicazione con `Add-AzureADServicePrincipalPolicy` . Per altre informazioni, vedere Assegnazione [di criteri di mapping delle attestazioni](../develop/active-directory-claims-mapping.md).

Esempio:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Abilitare PingAccess per l'uso di attestazioni personalizzate

L'abilitazione di PingAccess per l'uso di attestazioni personalizzate è facoltativa, ma necessaria se si prevede che l'applicazione usi attestazioni aggiuntive.

Quando si configurerà PingAccess nel passaggio seguente, la sessione Web creata (Impostazioni->Access->Web  Sessions) deve  avere l'opzione Profilo richiesta deselezionata e l'opzione Aggiorna attributi utente impostata su **No**

## <a name="download-pingaccess-and-configure-your-application"></a>Scaricare PingAccess e configurare l'applicazione

Dopo aver completato la procedura di installazione di Azure Active Directory, è possibile ora passare alla configurazione di PingAccess.

I passaggi dettagliati per la parte PingAccess di questo scenario continuano nella documentazione relativa all'identità ping. Seguire le istruzioni in [Configurare PingAccess per Azure AD](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) per proteggere le applicazioni pubblicate tramite Microsoft Azure AD Application Proxy nel sito Web Ping Identity e scaricare la versione più recente di [PingAccess](https://www.pingidentity.com/en/lp/azure-download.html?).

Questi passaggi consentono di installare PingAccess e configurare un account PingAccess (se non ne è già presente uno). Quindi, per creare una connessione Azure AD OpenID Connect (OIDC), è necessario configurare un provider di token con il valore di ID directory **(tenant)** copiato dal portale Azure AD. Successivamente, per creare una sessione Web in PingAccess, usare l'ID e i **valori dell'applicazione (client).** `PingAccess key` Successivamente è possibile impostare i mapping delle identità e creare l'host virtuale, il sito e l'applicazione.

### <a name="test-your-application"></a>Testare l'applicazione

Dopo aver completato tutti questi passaggi, l'applicazione dovrebbe essere in esecuzione. Per testarlo, aprire un browser e passare all'URL esterno creato durante la pubblicazione dell'applicazione in Azure. Accedere con l'account di test assegnato all'applicazione.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare PingAccess per Azure AD proteggere le applicazioni pubblicate tramite Microsoft Azure AD Application Proxy](https://docs.pingidentity.com/bundle/pingaccess-60/page/jep1564006742933.html)
- [Accesso Single Sign-On alle applicazioni in Azure Active Directory](what-is-single-sign-on.md)
- [Risolvere i problemi e i messaggi di errore del proxy dell'applicazione](application-proxy-troubleshoot.md)