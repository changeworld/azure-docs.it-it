---
title: Abilitare Azure AD B2C domini personalizzati
titleSuffix: Azure AD B2C
description: Informazioni su come abilitare domini personalizzati negli URL di reindirizzamento per Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 869bd7b02186873f490d324cec863c7f26ee8469
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555313"
---
# <a name="enable-custom-domains-for-azure-active-directory-b2c"></a>Abilitare domini personalizzati per Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Questo articolo descrive come abilitare domini personalizzati negli URL di reindirizzamento per Azure Active Directory B2C (Azure AD B2C). L'uso di un dominio personalizzato con l'applicazione offre un'esperienza utente più trasparente. Dal punto di vista dell'utente, rimangono nel dominio durante il processo di accesso anziché eseguire il reindirizzamento al Azure AD B2C dominio predefinito *<nome-tenant>. b2clogin.com*.

![Esperienza utente del dominio personalizzato](./media/custom-domain/custom-domain-user-experience.png)

## <a name="custom-domain-overview"></a>Panoramica del dominio personalizzato

È possibile abilitare i domini personalizzati per Azure AD B2C usando il front-end di [Azure](https://azure.microsoft.com/services/frontdoor/). Il front-end di Azure è un punto di ingresso globale e scalabile che usa la rete perimetrale globale Microsoft per creare applicazioni Web veloci, sicure e scalabili. È possibile eseguire il rendering di Azure AD B2C contenuto dietro la porta anteriore di Azure e quindi configurare un'opzione nella porta anteriore di Azure per distribuire il contenuto tramite un dominio personalizzato nell'URL dell'applicazione.

Il diagramma seguente illustra l'integrazione della porta anteriore di Azure:

1. Da un'applicazione, un utente fa clic sul pulsante di accesso che li porta alla pagina di accesso Azure AD B2C. Questa pagina specifica un nome di dominio personalizzato.
1. Il Web browser risolve il nome di dominio personalizzato nell'indirizzo IP della porta anteriore di Azure. Durante la risoluzione DNS, un record di nome canonico (CNAME) con un nome di dominio personalizzato punta all'host front-end predefinito (ad esempio, `contoso.azurefd.net` ). 
1. Il traffico indirizzato al dominio personalizzato (ad esempio, `login.contoso.com` ) viene indirizzato all'host front-end predefinito () specificato per la porta anteriore `contoso.azurefd.net` .
1. Lo sportello anteriore di Azure richiama Azure AD B2C contenuto usando il `<tenant-name>.b2clogin.com` dominio predefinito Azure ad B2C. La richiesta all'endpoint Azure AD B2C include un'intestazione HTTP personalizzata che contiene il nome di dominio personalizzato originale.
1. Azure AD B2C risponde alla richiesta visualizzando il contenuto pertinente e il dominio personalizzato originale.

![Diagramma di rete del dominio personalizzato](./media/custom-domain/custom-domain-network-flow.png)

> [!IMPORTANT]
> La connessione dal browser alla porta anteriore di Azure deve sempre usare IPv4 anziché IPv6.

Quando si utilizzano domini personalizzati, tenere presente quanto segue:

- È possibile configurare più domini personalizzati. Per il numero massimo di domini personalizzati supportati, vedere [limiti e restrizioni del servizio Azure ad](../active-directory/enterprise-users/directory-service-limits-restrictions.md) per Azure ad B2C e [sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits) per il front-end di Azure.
- Azure front door è un servizio di Azure separato, quindi verranno addebitati costi aggiuntivi. Per altre informazioni, vedere [prezzi di porte anteriori](https://azure.microsoft.com/pricing/details/frontdoor).
- Attualmente, la funzionalità [Web Application Firewall](../web-application-firewall/afds/afds-overview.md) di Azure front door non è supportata.
- Dopo aver configurato i domini personalizzati, gli utenti potranno comunque accedere al Azure AD B2C nome di dominio predefinito *<nome-tenant>. b2clogin.com* , a meno che non si usi un criterio personalizzato e si [blocchi l'accesso](#block-access-to-the-default-domain-name).
- Se si dispone di più applicazioni, eseguirne la migrazione nel dominio personalizzato perché il browser archivia la sessione di Azure AD B2C sotto il nome di dominio attualmente in uso.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="add-a-custom-domain-name-to-your-tenant"></a>Aggiungere un nome di dominio personalizzato al tenant

Seguire le istruzioni per [aggiungere e convalidare il dominio personalizzato in Azure ad](../active-directory/fundamentals/add-custom-domain.md). Dopo la verifica del dominio, eliminare il record TXT DNS creato.

> [!IMPORTANT]
> Per questi passaggi, assicurarsi di accedere al tenant di **Azure ad B2C** e selezionare il servizio **Azure Active Directory** .

Verificare ogni sottodominio che si prevede di utilizzare. La verifica solo del dominio di primo livello non è sufficiente. Ad esempio, per poter accedere con *login.contoso.com* e *account.contoso.com*, è necessario verificare sia i sottodomini che non solo il dominio di primo livello *contoso.com*.  

## <a name="create-a-new-azure-front-door-instance"></a>Creare una nuova istanza di Azure front door

Seguire i passaggi per la [creazione di una porta anteriore per l'applicazione](../frontdoor/quickstart-create-front-door.md#create-a-front-door-for-your-application) usando le impostazioni predefinite per l'host front-end e le regole di routing. 

> [!IMPORTANT]
> Per questi passaggi, dopo aver effettuato l'accesso al portale di Azure nel passaggio 1, selezionare **directory + sottoscrizione** e scegliere la directory che contiene la sottoscrizione di Azure che si vuole usare per la porta anteriore di Azure. *Non* deve corrispondere alla directory che contiene il tenant Azure ad B2C. 

Nel passaggio **aggiungere un back-end**, usare le impostazioni seguenti:

* Per **tipo host backend** selezionare **host personalizzato**.  
* Per **nome host backend** selezionare il nome host per l'endpoint Azure AD B2C, <nome-tenant>. b2clogin.com. Ad esempio, contoso.b2clogin.com. 
* Per **intestazione host backend** selezionare lo stesso valore selezionato per **nome host back-end**.

![Aggiungere un back-end](./media/custom-domain/add-a-backend.png)

Dopo aver aggiunto il **back-end** al **pool back-end**, disabilitare i **Probe di integrità**.

![Aggiungere un pool back-end](./media/custom-domain/add-a-backend-pool.png)

## <a name="set-up-your-custom-domain-on-azure-front-door"></a>Configurare il dominio personalizzato in Azure front door

Seguire i passaggi per [aggiungere un dominio personalizzato all'sportello anteriore](../frontdoor/front-door-custom-domain.md). Quando si crea il `CNAME` record per il dominio personalizzato, usare il nome di dominio personalizzato verificato in precedenza nel passaggio [aggiungere un nome di dominio personalizzato al Azure ad](#add-a-custom-domain-name-to-your-tenant) . 

Dopo aver verificato il nome di dominio personalizzato, selezionare **nome di dominio personalizzato HTTPS**. Quindi, sotto il **tipo di gestione dei certificati**, selezionare [gestione sportello anteriore](../frontdoor/front-door-custom-domain-https.md#option-1-default-use-a-certificate-managed-by-front-door)o [usare il proprio certificato](../frontdoor/front-door-custom-domain-https.md#option-2-use-your-own-certificate). 

Lo screenshot seguente illustra come aggiungere un dominio personalizzato e abilitare HTTPS usando un certificato di sportello anteriore di Azure.

![Configurare il dominio personalizzato di Azure front door](./media/custom-domain/azure-front-door-add-custom-domain.png) 

## <a name="configure-cors"></a>Configurare CORS

Se si [Personalizza l'interfaccia utente di Azure ad B2C](customize-ui-with-html.md) con un modello HTML, è necessario [configurare CORS](customize-ui-with-html.md?pivots=b2c-user-flow.md#3-configure-cors) con il dominio personalizzato.

Configurare l'archiviazione BLOB di Azure per la condivisione di risorse tra le origini con i passaggi seguenti:

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.
1. Nel menu selezionare **CORS**.
1. Per **Origini consentite** immettere `https://your-domain-name`. Sostituire `your-domain-name` con il nome di dominio. Ad esempio: `https://login.contoso.com`. Usare tutte le lettere minuscole quando si immette il nome del tenant.
1. Per **Metodi consentiti** selezionare sia `GET` che `OPTIONS`.
1. Per **Intestazioni consentite** immettere un asterisco (*).
1. Per **Intestazioni esposte** immettere un asterisco (*).
1. Per **Età massima** immettere 200.
1. Selezionare **Salva**.

## <a name="configure-your-identity-provider"></a>Configurare il provider di identità

Quando un utente sceglie di accedere con un provider di identità basato su social network, Azure AD B2C avvia una richiesta di autorizzazione e porta l'utente al provider di identità selezionato per completare il processo di accesso. La richiesta di autorizzazione specifica `redirect_uri` con il Azure ad B2C nome di dominio predefinito: 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>/oauth2/authresp
```

Se i criteri sono stati configurati per consentire l'accesso con un provider di identità esterno, aggiornare gli URI di reindirizzamento OAuth con il dominio personalizzato. La maggior parte dei provider di identità consente di registrare più URI di reindirizzamento. È consigliabile aggiungere gli URI di reindirizzamento anziché sostituirli in modo da poter testare i criteri personalizzati senza influire sulle applicazioni che usano il Azure AD B2C nome di dominio predefinito. 

Nell'URI di reindirizzamento seguente:

```http
https://<custom-domain-name>/<tenant-name>/oauth2/authresp
``` 

- Sostituire **<nome di dominio personalizzato>** con il nome di dominio personalizzato.
- Sostituire **<nome tenant>** con il nome del tenant o l'ID tenant.

L'esempio seguente mostra un URI di reindirizzamento OAuth valido:

```http
https://login.contoso.com/contoso.onmicrosoft.com/oauth2/authresp
```

Se si sceglie di usare l' [ID tenant](#optional-use-tenant-id), un URI di reindirizzamento OAuth valido avrà un aspetto simile al seguente:

```http
https://login.contoso.com/11111111-1111-1111-1111-111111111111/oauth2/authresp
```

I metadati dei [provider di identità SAML](saml-identity-provider-technical-profile.md) avranno un aspetto simile al seguente:

```http
https://<custom-domain-name>.b2clogin.com/<tenant-name>/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

## <a name="test-your-custom-domain"></a>Testare un dominio personalizzato

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. In **Criteri** selezionare **Flussi utente (criteri)** .
1. Selezionare un flusso utente e quindi fare clic su **Esegui flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **copia negli Appunti**.

    ![Copiare l'URI della richiesta di autorizzazione](./media/custom-domain/user-flow-run-now.png)

1. Nell'URL dell' **endpoint di esecuzione del flusso utente** sostituire il dominio Azure AD B2C (<nome-tenant>. b2clogin.com) con il dominio personalizzato.  
    Ad esempio, anziché:

    ```http
    https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login
    ```

    usare

    ```http
    https://login.contoso.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login    
    ```
1. Selezionare **Esegui il flusso utente**. I criteri di Azure AD B2C devono essere caricati.
1. Accedere con account sia locali che sociali.
1. Ripetere il test con il resto dei criteri.

## <a name="configure-your-application"></a>Configurare l'applicazione 

Dopo aver configurato e testato il dominio personalizzato, è possibile aggiornare le applicazioni per caricare l'URL che specifica il dominio personalizzato come nome host anziché il dominio Azure AD B2C. 

L'integrazione del dominio personalizzato si applica agli endpoint di autenticazione che usano criteri di Azure AD B2C (flussi utente o criteri personalizzati) per autenticare gli utenti. Questi endpoint possono avere un aspetto simile al seguente:

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/v2.0/.well-known/openid-configuration</code>

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://\<custom-domain\>/<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

Sostituire:
- **dominio personalizzato** con il dominio personalizzato
- **nome-tenant** con nome tenant o ID tenant
- **Policy-Name** con il nome del criterio. [Altre informazioni sui criteri di Azure ad B2C](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 


I metadati del [provider di servizi SAML](connect-with-saml-service-providers.md) possono avere un aspetto simile al seguente: 

```html
https://custom-domain-name/tenant-name/policy-name/Samlp/metadata
```

### <a name="optional-use-tenant-id"></a>Opzionale Usa ID tenant

È possibile sostituire il nome del tenant B2C nell'URL con il GUID dell'ID tenant in modo da rimuovere tutti i riferimenti a "B2C" nell'URL. Il GUID dell'ID tenant è reperibile nella pagina Panoramica di B2C in portale di Azure.
Ad esempio, modificare `https://account.contosobank.co.uk/contosobank.onmicrosoft.com/` in `https://account.contosobank.co.uk/<tenant ID GUID>/`.

Se si sceglie di usare l'ID tenant anziché il nome del tenant, assicurarsi di aggiornare di conseguenza gli **URI di reindirizzamento OAuth** del provider di identità. Per altre informazioni, vedere [configurare il provider di identità](#configure-your-identity-provider).

### <a name="token-issuance"></a>Rilascio token

L'attestazione del nome dell'emittente del token (ISS) cambia in base al dominio personalizzato usato. Ad esempio:

```http
https://<domain-name>/11111111-1111-1111-1111-111111111111/v2.0/
```

::: zone pivot="b2c-custom-policy"

## <a name="block-access-to-the-default-domain-name"></a>Blocca l'accesso al nome di dominio predefinito

Dopo aver aggiunto il dominio personalizzato e configurato l'applicazione, gli utenti potranno comunque accedere al dominio <nome tenant>. b2clogin.com. Per impedire l'accesso, è possibile configurare i criteri per controllare la richiesta di autorizzazione "nome host" rispetto a un elenco di domini consentito. Il nome host è il nome di dominio visualizzato nell'URL. Il nome host è disponibile tramite i `{Context:HostName}` [resolver di attestazione](claim-resolver-overview.md). È quindi possibile presentare un messaggio di errore personalizzato. 

1. Ottenere l'esempio di un criterio di accesso condizionale che controlla il nome host da [GitHub](https://github.com/azure-ad-b2c/samples/blob/master/policies/check-host-name).
1. In ogni file sostituire la stringa `yourtenant` con il nome del tenant del Azure ad B2C. Ad esempio, se il nome del tenant B2C è *contosob2c*, tutte le istanze di `yourtenant.onmicrosoft.com` diventano `contosob2c.onmicrosoft.com` .
1. Caricare i file dei criteri nell'ordine seguente: `B2C_1A_TrustFrameworkExtensions_HostName.xml` e quindi `B2C_1A_signup_signin_HostName.xml` .

::: zone-end

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="azure-ad-b2c-returns-a-page-not-found-error"></a>Azure AD B2C restituisce un errore di pagina non trovata

- **Sintomo** : dopo aver configurato un dominio personalizzato, quando si tenta di accedere con il dominio personalizzato, viene ricevuto un messaggio di errore HTTP 404.
- **Possibili cause** : questo problema potrebbe essere correlato alla configurazione DNS o alla configurazione back-end di Azure front door. 
- **Risoluzione**:  
    1. Verificare che il dominio personalizzato sia [registrato e verificato correttamente](#add-a-custom-domain-name-to-your-tenant) nel tenant del Azure ad B2C.
    1. Verificare che il [dominio personalizzato](../frontdoor/front-door-custom-domain.md) sia configurato correttamente. Il `CNAME` record per il dominio personalizzato deve puntare all'host front-end di Azure front door predefinito, ad esempio contoso.azurefd.NET.
    1. Assicurarsi che la [configurazione del pool back-end di Azure front door](#set-up-your-custom-domain-on-azure-front-door) punti al tenant in cui è stato configurato il nome di dominio personalizzato e in cui sono archiviati i criteri personalizzati o il flusso utente.

### <a name="identify-provider-returns-an-error"></a>Il provider di identificazione restituisce un errore

- **Sintomo** : dopo aver configurato un dominio personalizzato, è possibile accedere con gli account locali. Tuttavia, quando si esegue l'accesso con le credenziali di [provider di identità social o aziendali](add-identity-provider.md)esterni, i provider di identità visualizzano un messaggio di errore.
- **Possibili cause** : quando Azure ad B2C porta l'utente ad accedere con un provider di identità federato, specifica l'URI di reindirizzamento. L'URI di reindirizzamento è l'endpoint a cui il provider di identità restituisce il token. L'URI di reindirizzamento è lo stesso dominio utilizzato dall'applicazione con la richiesta di autorizzazione. Se l'URI di reindirizzamento non è ancora registrato nel provider di identità, potrebbe non considerare attendibile il nuovo URI di reindirizzamento, che genera un messaggio di errore. 
- **Risoluzione** : seguire la procedura descritta in [configurare il provider di identità](#configure-your-identity-provider) per aggiungere il nuovo URI di reindirizzamento. 


## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="can-i-use-azure-front-door-advanced-configuration-such-as-web-application-firewall-rules"></a>È possibile usare la configurazione avanzata di Azure front door, ad esempio *le regole del Web Application Firewall*? 
  
Sebbene le impostazioni di configurazione avanzate di Azure front door non siano ufficialmente supportate, è possibile usarle a proprio rischio. 

### <a name="when-i-use-run-now-to-try-to-run-my-policy-why-i-cant-see-the-custom-domain"></a>Quando si usa Esegui adesso per provare a eseguire il criterio, perché non è possibile visualizzare il dominio personalizzato?

Copiare l'URL, modificare manualmente il nome di dominio e quindi incollarlo di nuovo nel browser.

### <a name="which-ip-address-is-presented-to-azure-ad-b2c-the-users-ip-address-or-the-azure-front-door-ip-address"></a>Quale indirizzo IP viene presentato Azure AD B2C? L'indirizzo IP dell'utente o l'indirizzo IP della porta anteriore di Azure?

Lo sportello anteriore di Azure passa l'indirizzo IP originale dell'utente. Si tratta dell'indirizzo IP che verrà visualizzato nei report di controllo o nei criteri personalizzati.

### <a name="can-i-use-a-third-party-wab-application-firewall-waf-with-b2c"></a>È possibile usare un firewall applicazione WAB di terze parti (WAF) con B2C?

Attualmente, Azure AD B2C supporta un dominio personalizzato tramite l'uso del solo sportello anteriore di Azure. Non aggiungere un altro WAF davanti all'avantreno di Azure.


## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle [richieste di autorizzazione OAuth](protocols-overview.md).

