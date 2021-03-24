---
title: Proteggere il back-end SPA in gestione API di Azure con Active Directory B2C
description: Proteggere un'API con OAuth 2,0 usando Azure Active Directory B2C, gestione API di Azure e Easy auth da chiamare da una SPA JavaScript usando il flusso di autenticazione SPA abilitato per PKCE.
services: api-management, azure-ad-b2c, app-service
documentationcenter: ''
author: WillEastbury
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2021
ms.author: wieastbu
ms.custom: fasttrack-new, fasttrack-update, devx-track-js
ms.openlocfilehash: baa6a0a6995e206924d14de25b98700e450f3a0c
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954922"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Proteggere il back-end SPA con OAuth 2,0, Azure Active Directory B2C e gestione API di Azure

Questo scenario illustra come configurare l'istanza di gestione API di Azure per proteggere un'API.
Il flusso Azure AD B2C SPA (codice di autenticazione + PKCE) verrà usato per acquisire un token, insieme a gestione API per proteggere un back-end di funzioni di Azure usando EasyAuth.

## <a name="aims"></a>Mira

Vedremo come usare gestione API in uno scenario semplificato con funzioni di Azure e Azure AD B2C. Verrà creata un'app JavaScript (JS) che chiama un'API che consente di accedere agli utenti con Azure AD B2C. Si useranno quindi le funzionalità di convalida JWT, CORS e limite di velocità di gestione API per proteggere l'API back-end.

Per la difesa in profondità, viene usato EasyAuth per convalidare nuovamente il token all'interno dell'API back-end e assicurarsi che gestione API sia l'unico servizio che può chiamare il back-end di funzioni di Azure.

## <a name="what-will-you-learn"></a>Concetti legati all'esercitazione

> [!div class="checklist"]
> * Configurazione di un'app a pagina singola e di un'API back-end in Azure Active Directory B2C
> * Creazione di un'API back-end di funzioni di Azure
> * Importazione di un'API di funzioni di Azure in gestione API di Azure
> * Protezione dell'API in gestione API di Azure
> * Chiamata degli endpoint di autorizzazione Azure Active Directory B2C tramite le librerie della piattaforma di identità Microsoft (MSAL.js)
> * Archiviazione di un'applicazione a pagina singola HTML/Vanilla JS e relativa conservazione da un endpoint di archiviazione BLOB di Azure

## <a name="prerequisites"></a>Prerequisiti

Per eseguire i passaggi in questo articolo è necessario avere quanto segue:

* Un account di archiviazione di Azure (archiviazione v2) per utilizzo generico V2 per ospitare l'app a pagina singola di frontend JS.
* Un'istanza di gestione API di Azure (qualsiasi livello funzionerà, incluso "consumo", tuttavia alcune funzionalità applicabili allo scenario completo non sono disponibili in questo livello (rate-limit-by-Key e IP virtuale dedicato). queste restrizioni sono riportate di seguito nell'articolo laddove appropriato.
* App per le funzioni di Azure vuota (che esegue il runtime di .NET Core V 3.1 in un piano a consumo) per ospitare l'API chiamata
* Un tenant di Azure AD B2C, collegato a una sottoscrizione.

Sebbene in pratica si usino risorse nella stessa area dei carichi di lavoro di produzione, per questo articolo sulle procedure l'area di distribuzione non è importante.

## <a name="overview"></a>Panoramica

Ecco un'illustrazione dei componenti in uso e del flusso tra di essi al termine del processo.
![Componenti in uso e flusso](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Componenti in uso e flusso")

Ecco una rapida panoramica dei passaggi:

1. Creare il Azure AD B2C chiamare (front-end, gestione API) e le applicazioni API con gli ambiti e concedere l'accesso all'API
1. Creare i criteri di iscrizione e accesso per consentire agli utenti di accedere con Azure AD B2C 
1. Configurare Gestione API con le nuove chiavi e gli ID client di Azure AD B2C per abilitare l'autorizzazione utente OAuth2 nella console per sviluppatori
1. Compilare l'API della funzione
1. Configurare l'API della funzione per abilitare EasyAuth con le nuove chiavi e l'ID client di Azure AD B2C e bloccare il gestione API VIP
1. Compilare la definizione dell'API in gestione API
1. Configurare OAuth2 per la configurazione API di gestione API
1. Configurare il criterio **CORS** e aggiungere il criterio **Validate-JWT** per convalidare il token OAuth per ogni richiesta in ingresso
1. Compilare l'applicazione chiamante per utilizzare l'API
1. Caricare l'esempio di JS SPA
1. Configurare l'app client JS di esempio con le nuove chiavi e l'ID client di Azure AD B2C 
1. Testare l'applicazione client

   > [!TIP]
   > Verranno acquisite alcune informazioni e chiavi e, in questo documento, potrebbe essere utile disporre di un editor di testo aperto per archiviare temporaneamente gli elementi di configurazione seguenti.  
   >
   > ID CLIENT BACK-END B2C:  
   > CHIAVE PRIVATA DEL CLIENT BACK-END B2C:  
   > URI DELL'AMBITO DELL'API BACK-END B2C:  
   > ID CLIENT FRONT-END B2C:  
   > URI DELL'ENDPOINT DEL FLUSSO UTENTE B2C:  
   > ENDPOINT OPENID BEN NOTO B2C:   
   > NOME del criterio B2C: Frontendapp_signupandsignin URL della funzione:  
   > URL DI BASE DELL'API GESTIONE API: URL DELL'ENDPOINT PRIMARIO DI ARCHIVIAZIONE:  

## <a name="configure-the-backend-application"></a>Configurare l'applicazione back-end

Aprire il pannello Azure AD B2C nel portale e seguire questa procedura.

1. Selezionare la scheda registrazioni per l' **app**
1. Fare clic sul pulsante "nuova registrazione".
1. Scegliere ' Web ' dalla casella di selezione URI di reindirizzamento.
1. A questo punto, impostare il nome visualizzato, scegliere un elemento univoco e pertinente per il servizio in fase di creazione. In questo esempio verrà usato il nome "applicazione back-end".
1. Usare i segnaposto per gli URL di risposta, ad esempio ' https://jwt.ms ' (sito di decodifica dei token di proprietà di Microsoft). questi URL verranno aggiornati in un secondo momento.
1. Assicurarsi di aver selezionato l'opzione "account in qualsiasi provider di identità o directory organizzativa (per l'autenticazione degli utenti con flussi utente)"
1. Per questo esempio, deselezionare la casella "Concedi il consenso dell'amministratore", in quanto non saranno richieste offline_access autorizzazioni attuali.
1. Fare clic su "Registra".
1. Registrare l'ID client dell'applicazione back-end per usarlo in seguito (visualizzato in "ID applicazione (client)").
1. Selezionare la scheda *certificati e segreti* (in Gestisci), quindi fare clic su' nuovo segreto client ' per generare una chiave di autenticazione (accettare le impostazioni predefinite e fare clic su' Aggiungi ').
1. Quando si fa clic su' Aggiungi ', copiare la chiave (in ' valore ') in un punto sicuro per un uso successivo come ' segreto client back-end '. si noti che questa finestra di dialogo è l'unica possibilità di copiare la chiave.
1. Selezionare ora la scheda *esporre un'API* (in Gestisci).
1. Verrà richiesto di impostare l'URI AppID, selezionare e registrare il valore predefinito.
1. Creare e denominare l'ambito "Hello" per l'API della funzione. è possibile usare la frase "Hello" per tutte le opzioni immesse, registrare l'URI del valore di ambito completo popolato, quindi fare clic su "Aggiungi ambito".
1. Tornare alla radice del pannello Azure AD B2C selezionando la navigazione "Azure AD B2C" nella parte superiore sinistra del portale.

   > [!NOTE]
   > Gli ambiti di Azure AD B2C sono le autorizzazioni effettivamente presenti all'interno dell'API a cui altre applicazioni possono richiedere l'accesso tramite il pannello di accesso all'API dalle applicazioni, in modo da creare solo le autorizzazioni dell'applicazione per l'API chiamata.

## <a name="configure-the-frontend-application"></a>Configurare l'applicazione front-end

1. Selezionare la scheda registrazioni per l' **app**
1. Fare clic sul pulsante "nuova registrazione".
1. Scegliere ' applicazione a pagina singola (SPA)' dalla casella di selezione URI di reindirizzamento.
1. Impostare ora il nome visualizzato e l'URI AppID, scegliere un elemento univoco e pertinente per l'applicazione front-end che userà questa AAD B2C la registrazione dell'app. In questo esempio è possibile usare "applicazione front-end"
1. Come per la prima registrazione dell'app, lasciare la selezione dei tipi di account supportati per impostazione predefinita (autenticazione degli utenti con flussi utente)
1. Usare i segnaposto per gli URL di risposta, ad esempio ' https://jwt.ms ' (sito di decodifica dei token di proprietà di Microsoft). questi URL verranno aggiornati in un secondo momento.
1. Lascia selezionata la casella di consenso dell'amministratore della concessione
1. Fare clic su "Registra".
1. Registrare l'ID client dell'applicazione front-end per usarlo in seguito (visualizzato in "ID applicazione (client)").
1. Passare alla scheda *autorizzazioni API* .
1. Concedere l'accesso all'applicazione back-end facendo clic su' Aggiungi un'autorizzazione ', quindi su' API ', selezionare l'applicazione back-end, selezionare ' autorizzazioni ', selezionare l'ambito creato nella sezione precedente e fare clic su' Aggiungi autorizzazioni '
1. Fare clic su "Concedi il consenso dell'amministratore per {tenant} e fare clic su" Sì "nella finestra di dialogo popup. Questo popup consentirà all'applicazione front-end di usare l'autorizzazione "Hello" definita nell'applicazione back-end creata in precedenza.
1. Tutte le autorizzazioni dovrebbero ora essere visualizzate per l'app come un segno di avanzamento verde nella colonna stato

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Creare un flusso utente "iscrizione e accesso"

1. Tornare alla radice del pannello B2C selezionando il Azure AD B2C il percorso di navigazione.
1. Passare alla scheda ' Flussi utente ' (in criteri).
1. Fare clic su "nuovo flusso utente"
1. Scegliere il tipo di flusso utente "iscrizione e accesso" e selezionare ' consigliato ' e quindi ' Crea '
1. Assegnare un nome al criterio e registrarlo per un momento successivo. Per questo esempio, è possibile usare "Frontendapp_signupandsignin". si noti che questo sarà preceduto da "B2C_1_" per rendere "B2C_1_Frontendapp_signupandsignin"
1. In "Identity providers" e "local accounts" selezionare "email Sign up" (o "User ID Sign up" a seconda della configurazione del tenant B2C) e fare clic su OK. Questa configurazione è dovuta al fatto che si registreranno account B2C locali, che non si rinviano a un altro provider di identità (ad esempio un provider di identità di social networking) per usare un account di social media esistente di un utente.
1. Lasciare le impostazioni predefinite dell'autenticazione a più fattori e dell'accesso condizionale.
1. In ' attributi utente e attestazioni ' fare clic su' Mostra più.. .' scegliere quindi le opzioni di attestazione che si desidera che gli utenti immettano e restituiscono nel token. Controllare almeno ' Display Name ' è email address ' da raccogliere, con ' Display Name ' è email Addresss ' da restituire (prestare particolare attenzione al fatto che si sta raccogliendo EmailAddress, Singular e chiedendo di restituire gli indirizzi di posta elettronica, più) e fare clic su' OK ', quindi fare clic su' Crea '.
1. Fare clic sul flusso utente creato nell'elenco, quindi fare clic sul pulsante "Esegui flusso utente".
1. Questa azione consente di aprire il pannello Esegui flusso utente, selezionare l'applicazione front-end, copiare l'endpoint del flusso utente e salvarlo per un momento successivo.
1. Copiare e archiviare il collegamento nella parte superiore, registrandosi come endpoint di configurazione OpenID noto per un uso successivo.

   > [!NOTE]
   > I criteri B2C consentono di esporre gli endpoint di accesso Azure AD B2C per poter acquisire diversi componenti dati e gli utenti di accesso in modi diversi.
   > 
   > In questo caso è stato configurato un flusso di iscrizione o accesso (criterio). È stato inoltre esposto un endpoint di configurazione noto, in entrambi i casi il criterio creato è stato identificato nell'URL dal parametro della stringa di query "p =".  
   >
   > Al termine di questa operazione, si avrà a disposizione una piattaforma funzionale per l'identità dei clienti che consentirà agli utenti di accedere a più applicazioni.

## <a name="build-the-function-api"></a>Compilare l'API della funzione

1. Tornare al tenant di Azure AD standard nel portale di Azure per poter configurare di nuovo gli elementi nella sottoscrizione.
1. Passare al pannello app per le funzioni del portale di Azure, aprire l'app per le funzioni vuota, quindi fare clic su' funzioni ', quindi su' Aggiungi '.
1. Nel riquadro a comparsa visualizzato scegliere ' sviluppo nel portale ', in ' selezionare un modello ' quindi scegliere ' trigger HTTP ', in dettagli modello denominarlo ' Hello ' con il livello di autorizzazione ' Function ', quindi selezionare Aggiungi.
1. Passare al pannello codice + test e copiare e incollare il codice di esempio riportato di seguito *sul codice esistente* visualizzato.
1. Selezionare Salva.

   ```csharp
   
   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   
   public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
   {
      log.LogInformation("C# HTTP trigger function processed a request.");
      
      return (ActionResult)new OkObjectResult($"Hello World, time and date are {DateTime.Now.ToString()}");
   }
   
   ```

   > [!TIP]
   > Il codice della funzione script c# appena incollato viene semplicemente registrato una riga nei log delle funzioni e viene restituito il testo "Hello World" con alcuni dati dinamici (data e ora).

1. Selezionare "integrazione" dal pannello a sinistra, quindi fare clic sul collegamento http (req) nella casella "trigger".
1. Dall'elenco a discesa ' metodi HTTP selezionati ' deselezionare il metodo HTTP POST, lasciando selezionato solo seleziona e quindi fare clic su Salva.
1. Tornare alla scheda codice + test, fare clic su' Ottieni URL funzione ', quindi copiare l'URL che viene visualizzato e salvarlo per un momento successivo.

   > [!NOTE]
   > Le associazioni appena create indicano alle funzioni di rispondere alle richieste HTTP GET anonime all'URL appena copiato ( `https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey` ). Ora abbiamo un'API HTTPS scalabile senza server, in grado di restituire un payload molto semplice.
   >
   > È ora possibile testare la chiamata a questa API da un Web browser usando la versione dell'URL precedente appena copiata e salvata. È anche possibile rimuovere i parametri della stringa di query "? Code = SecretKey" della parte dell'URL e testare di nuovo per dimostrare che funzioni di Azure restituirà un errore 401.

## <a name="configure-and-secure-the-function-api"></a>Configurare e proteggere l'API della funzione

1. È necessario configurare due aree aggiuntive nell'app per le funzioni (restrizioni di autorizzazione e di rete).
1. Prima di tutto, è necessario configurare l'autenticazione/autorizzazione, quindi tornare al pannello radice dell'app per le funzioni tramite il percorso di navigazione.
1. Selezionare quindi "autenticazione/autorizzazione" (in "Impostazioni").
1. Attivare la funzionalità di autenticazione del servizio app.
1. Impostare l'azione da eseguire quando la richiesta non è autenticata a discesa a "Accedi con Azure Active Directory".
1. In "provider di autenticazione" scegliere "Azure Active Directory".
1. Scegliere ' avanzate ' dall'opzione modalità di gestione.
1. Incollare l'ID client [Application] dell'applicazione back-end (da Azure AD B2C) nella casella ' ID client '
1. Incollare l'endpoint di configurazione Open-ID noto dal criterio di iscrizione e accesso nella casella URL autorità di certificazione. questa configurazione è stata registrata in precedenza.
1. Fare clic su' Mostra segreto ' e incollare il segreto client dell'applicazione back-end nella casella appropriata.
1. Fare clic su OK per tornare al pannello/schermata di selezione del provider di identità.
1. Lasciare l' [Archivio token](../app-service/overview-authentication-authorization.md#token-store) abilitato in impostazioni avanzate (impostazione predefinita).
1. Fare clic su "Salva" nella parte superiore sinistra del pannello.

   > [!IMPORTANT]
   > A questo punto, l'API della funzione viene distribuita e deve generare risposte 401 se il JWT corretto non viene specificato come intestazione di autorizzazione: Bearer e deve restituire i dati quando viene presentata una richiesta valida.  
   > È stata aggiunta una sicurezza di difesa in profondità aggiuntiva in EasyAuth configurando l'opzione ' login with Azure AD ' per gestire le richieste non autenticate. Tenere presente che questa operazione modificherà il comportamento della richiesta non autorizzata tra la app per le funzioni back-end e la SPA frontend, perché EasyAuth emetterà un reindirizzamento 302 ad AAD anziché una risposta 401 non autorizzata. questa operazione verrà corretta con gestione API in un secondo momento.  
   >
   > Non è ancora stata applicata alcuna sicurezza IP, se si dispone di una chiave valida e di un token OAuth2, chiunque può chiamarlo da qualsiasi posizione, idealmente si vuole forzare l'esecuzione di tutte le richieste tramite gestione API.  
   > 
   > Se si usa il livello di consumo gestione API, [non è disponibile un indirizzo IP virtuale di gestione API di Azure dedicato](./api-management-howto-ip-addresses.md#ip-addresses-of-consumption-tier-api-management-service) da consentire-list con le limitazioni di accesso alle funzioni. Nello SKU standard di gestione API di Azure e al [di sopra dell'indirizzo VIP è un singolo tenant e per la durata della risorsa](./api-management-howto-ip-addresses.md#changes-to-the-ip-addresses). Per il livello di utilizzo di gestione API di Azure, è possibile bloccare le chiamate API tramite la chiave della funzione segreta condivisa nella parte dell'URI copiata in precedenza. Inoltre, per il livello di consumo, i passaggi 12-17 seguenti non si applicano.

1. Chiudere il pannello "autenticazione/autorizzazione" 
1. Aprire il pannello *gestione API del portale*, quindi aprire l' *istanza*.
1. Registrare l'indirizzo VIP privato visualizzato nella scheda Panoramica.
1. Tornare al pannello *funzioni di Azure del portale* , quindi aprire di nuovo l' *istanza* .
1. Selezionare ' rete ', quindi selezionare ' configura restrizioni di accesso '
1. Fare clic su' Aggiungi regola ' e immettere l'indirizzo VIP copiato nel passaggio 3 precedente nel formato XX. XX. XX. XX/32.
1. Se si vuole continuare a interagire con il portale delle funzioni e per eseguire i passaggi facoltativi riportati di seguito, è necessario aggiungere anche il proprio indirizzo IP pubblico o l'intervallo CIDR.
1. Quando nell'elenco è presente una voce Consenti, Azure aggiunge una regola di negazione implicita per bloccare tutti gli altri indirizzi.

È necessario aggiungere i blocchi in formato CIDR degli indirizzi al pannello restrizioni IP. Quando è necessario aggiungere un singolo indirizzo, ad esempio l'indirizzo VIP di gestione API, è necessario aggiungerlo nel formato XX. XX. XX. XX/32.

   > [!NOTE]
   > A questo punto, l'API della funzione non deve essere chiamata da qualsiasi luogo oltre che tramite gestione API o l'indirizzo.

1. Aprire il pannello *gestione API*, quindi aprire l' *istanza*.
1. Selezionare il pannello API (in API).
1. Dal riquadro ' Aggiungi nuova API ' scegliere ' app per le funzioni ', quindi selezionare ' completa ' nella parte superiore del popup.
1. Fare clic su Sfoglia, scegliere l'app per le funzioni in cui è ospitata l'API e fare clic su Seleziona. Quindi, fare di nuovo clic su Seleziona.
1. Assegnare all'API un nome e una descrizione per l'uso interno di gestione API e aggiungerlo al prodotto "illimitato".
1. Copiare e registrare l'' URL di base dell'API ' e fare clic su' Crea '.
1. Fare clic sulla scheda ' impostazioni ', quindi in sottoscrizione-disattivare la casella di controllo ' sottoscrizione obbligatoria ' perché verrà usato il token OAuth JWT in questo caso per il limite di frequenza. Si noti che se si usa il livello di consumo, questo sarebbe comunque necessario in un ambiente di produzione. 

   > [!TIP]
   > Se si usa il livello di consumo di gestione API, il prodotto illimitato non sarà disponibile come predefinito. Passare invece a "Products" in "APIs" e fare clic su "Add".  
   > Digitare "illimitata" come nome del prodotto e Descrizione e selezionare l'API appena aggiunta dal callout "+" delle API nella parte inferiore sinistra della schermata. Selezionare la casella di controllo "pubblicato". Lasciare le altre impostazioni predefinite. Infine, fare clic sul pulsante "Crea". Il prodotto "illimitato" è stato creato e assegnato all'API. È possibile personalizzare il nuovo prodotto in un secondo momento.

## <a name="configure-and-capture-the-correct-storage-endpoint-settings"></a>Configurare e acquisire le impostazioni corrette dell'endpoint di archiviazione

1. Aprire il pannello account di archiviazione nell'portale di Azure 
1. Selezionare l'account creato e selezionare il pannello "sito Web statico" dalla sezione Impostazioni. se non viene visualizzata l'opzione "sito Web statico", verificare che sia stato creato un account V2.
1. Impostare la funzionalità di hosting Web statico su "Enabled" e impostare il nome del documento di indice su "index.html', quindi fare clic su" Salva ".
1. Annotare il contenuto dell'endpoint primario per un momento successivo, in quanto questa è la posizione in cui verrà ospitato il sito front-end.

   > [!TIP]
   > È possibile usare l'archiviazione BLOB di Azure e la riscrittura della rete CDN o il servizio app Azure per ospitare l'applicazione a singola pagina, ma la funzionalità di hosting di siti web statici di archiviazione BLOB offre un contenitore predefinito per gestire contenuto Web statico/HTML/JS/CSS da archiviazione di Azure e dedurrà una pagina predefinita per zero lavoro.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>Configurare i criteri **CORS** e **Validate-JWT**

> Le sezioni seguenti devono essere seguite indipendentemente dal livello di gestione API utilizzato. L'URL dell'account di archiviazione è dall'account di archiviazione che verrà reso disponibile dai prerequisiti all'inizio di questo articolo.
1. Passare al pannello Gestione API del portale e aprire l'istanza.
1. Selezionare API, quindi selezionare "tutte le API".
1. In "elaborazione in ingresso" fare clic sul pulsante di visualizzazione del codice "</>" per visualizzare l'editor dei criteri.
1. Modificare la sezione in ingresso e incollare il codice XML seguente in modo che legga come segue.
1. Sostituire i parametri seguenti nei criteri
1. {PrimaryStorageEndpoint} ("Endpoint di archiviazione primario" copiato nella sezione precedente), {ai criteri B2C-well-known-OpenID} (l'endpoint di configurazione OpenID noto, copiato in precedenza) e {backend-API-application-client-ID} (l'ID applicazione/client B2C per l' **API back-end**) con i valori corretti salvati in precedenza.
1. Se si usa il livello di consumo di gestione API, è necessario rimuovere entrambi i criteri rate-limit-by-Key perché questo criterio non è disponibile quando si usa il livello di consumo di gestione API di Azure.

   ```xml
   <inbound>
      <cors allow-credentials="true">
            <allowed-origins>
                <origin>{PrimaryStorageEndpoint}</origin>
            </allowed-origins>
            <allowed-methods preflight-result-max-age="120">
                <method>GET</method>
            </allowed-methods>
            <allowed-headers>
                <header>*</header>
            </allowed-headers>
            <expose-headers>
                <header>*</header>
            </expose-headers>
        </cors>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid." require-expiration-time="true" require-signed-tokens="true" clock-skew="300">
         <openid-config url="{b2cpolicy-well-known-openid}" />
         <required-claims>
            <claim name="aud">
               <value>{backend-api-application-client-id}</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <rate-limit-by-key calls="300" renewal-period="120" counter-key="@(context.Request.IpAddress)" />
      <rate-limit-by-key calls="15" renewal-period="60" counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
   </inbound>
   ```

   > [!NOTE]
   > Gestione API di Azure è ora in grado di rispondere alle richieste incrociate dalle app di JavaScript SPA e consente di eseguire la limitazione, la limitazione della frequenza e la pre-convalida del token di autenticazione JWT passato prima di inoltrare la richiesta all'API della funzione.
   > 
   > Sono ora disponibili Azure AD B2C, gestione API e funzioni di Azure che interagiscono per pubblicare, proteggere e utilizzare un'API.

   > [!TIP]
   > Se si usa il livello di utilizzo di gestione API, anziché la limitazione della frequenza da parte dell'oggetto JWT o dell'indirizzo IP in ingresso (limitare la frequenza delle chiamate in base ai criteri chiave non è attualmente supportata per il livello "consumo"), è possibile limitare la quota in base alla frequenza delle chiamate, vedere [qui](./api-management-access-restriction-policies.md#LimitCallRate).  
   > Poiché questo esempio è un'applicazione a pagina singola JavaScript, la chiave di gestione API viene usata solo per le chiamate di limitazione della frequenza e di fatturazione. L'autorizzazione e l'autenticazione effettive vengono gestite da Azure AD B2C e sono incapsulate in JWT, che viene convalidato due volte, una volta per gestione API e quindi dalla funzione di Azure back-end.

## <a name="upload-the-javascript-spa-sample-to-static-storage"></a>Caricare l'esempio di JavaScript SPA nell'archivio statico

1. Sempre nel pannello dell'account di archiviazione selezionare il pannello "contenitori" dalla sezione servizio BLOB e fare clic sul contenitore $web visualizzato nel riquadro di destra.
1. Salvare il codice riportato di seguito in un file in locale nel computer come index.html e quindi caricare il file index.html nel contenitore $web.

   ```html
    <!doctype html>
    <html lang="en">
    <head>
         <meta charset="utf-8">
         <meta http-equiv="X-UA-Compatible" content="IE=edge">
         <meta name="viewport" content="width=device-width, initial-scale=1">
         <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-BmbxuPwQa2lc/FVzBcNJ7UAyJxM6wuqIj61tLrc4wSX0szH/Ev+nYRRuWlolflfl" crossorigin="anonymous">
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.11.1/js/msal-browser.min.js"></script>
    </head>
    <body>
         <div class="container-fluid">
             <div class="row">
                 <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
                        <div class="container-fluid">
                            <a class="navbar-brand" href="#">Azure Active Directory B2C with Azure API Management</a>
                            <div class="navbar-nav">
                                <button class="btn btn-success" id="signinbtn"  onClick="login()">Sign In</a>
                            </div>
                        </div>
                    </nav>
                 </div>
             </div>
             <div class="row">
                 <div class="col-md-12">
                     <div class="card" >
                        <div id="cardheader" class="card-header">
                            <div class="card-text"id="message">Please sign in to continue</div>
                        </div>
                        <div class="card-body">
                            <button class="btn btn-warning" id="callapibtn" onClick="getAPIData()">Call API</a>
                            <div id="progress" class="spinner-border" role="status">
                                <span class="visually-hidden">Loading...</span>
                            </div>
                        </div>
                     </div>
                 </div>
             </div>
         </div>
         <script lang="javascript">
                // Just change the values in this config object ONLY.
                var config = {
                    msal: {
                        auth: {
                            clientId: "{CLIENTID}", // This is the client ID of your FRONTEND application that you registered with the SPA type in AAD B2C
                            authority:  "{YOURAUTHORITYB2C}", // Formatted as https://{b2ctenantname}.b2clogin.com/tfp/{b2ctenantguid or full tenant name including onmicrosoft.com}/{signuporinpolicyname}
                            redirectUri: "{StoragePrimaryEndpoint}", // The storage hosting address of the SPA, a web-enabled v2 storage account - recorded earlier as the Primary Endpoint.
                            knownAuthorities: ["{B2CTENANTDOMAIN}"] // {b2ctenantname}.b2clogin.com
                        },
                        cache: {
                            cacheLocation: "sessionStorage",
                            storeAuthStateInCookie: false 
                        }
                    },
                    api: {
                        scopes: ["{BACKENDAPISCOPE}"], // The scope that we request for the API from B2C, this should be the backend API scope, with the full URI.
                        backend: "{APIBASEURL}/hello" // The location that we will call for the backend api, this should be hosted in API Management, suffixed with the name of the API operation (in the sample this is '/hello').
                    }
                }
                document.getElementById("callapibtn").hidden = true;
                document.getElementById("progress").hidden = true;
                const myMSALObj = new msal.PublicClientApplication(config.msal);
                myMSALObj.handleRedirectPromise().then((tokenResponse) => {
                    if(tokenResponse !== null){
                        console.log(tokenResponse.account);
                        document.getElementById("message").innerHTML = "Welcome, " + tokenResponse.account.name;
                        document.getElementById("signinbtn").hidden = true;
                        document.getElementById("callapibtn").hidden = false;
                    }}).catch((error) => {console.log("Error Signing in:" + error);
                });
                function login() {
                    try {
                        myMSALObj.loginRedirect({scopes: config.api.scopes});
                    } catch (err) {console.log(err);}
                }
                function getAPIData() {
                    document.getElementById("progress").hidden = false; 
                    document.getElementById("message").innerHTML = "Calling backend ... "
                    document.getElementById("cardheader").classList.remove('bg-success','bg-warning','bg-danger');
                    myMSALObj.acquireTokenSilent({scopes: config.api.scopes, account: getAccount()}).then(tokenResponse => {
                        const headers = new Headers();
                        headers.append("Authorization", `Bearer ${tokenResponse.accessToken}`);
                        fetch(config.api.backend, {method: "GET", headers: headers})
                            .then(async (response)  => {
                                if (!response.ok)
                                {
                                    document.getElementById("message").innerHTML = "Error: " + response.status + " " + JSON.parse(await response.text()).message;
                                    document.getElementById("cardheader").classList.add('bg-warning');
                                }
                                else
                                {
                                    document.getElementById("cardheader").classList.add('bg-success');
                                    document.getElementById("message").innerHTML = await response.text();
                                }
                                }).catch(async (error) => {
                                    document.getElementById("cardheader").classList.add('bg-danger');
                                    document.getElementById("message").innerHTML = "Error: " + error;
                                });
                    }).catch(error => {console.log("Error Acquiring Token Silently: " + error);
                        return myMSALObj.acquireTokenRedirect({scopes: config.api.scopes, forceRefresh: false})
                    });
                    document.getElementById("progress").hidden = true;
             }
            function getAccount() {
                var accounts = myMSALObj.getAllAccounts();
                if (!accounts || accounts.length === 0) {
                    return null;
                } else {
                    return accounts[0];
                }
            }
        </script>
     </body>
    </html>
   ```

1. Passare all'endpoint primario del sito Web statico archiviato in precedenza nell'ultima sezione.

   > [!NOTE]
   > Congratulazioni, è stata appena distribuita un'app JavaScript a pagina singola nell'hosting di contenuto statico di archiviazione di Azure.  
   > Poiché l'app JS non è ancora stata configurata con i dettagli di Azure AD B2C, la pagina non funzionerà ancora se la si apre.

## <a name="configure-the-javascript-spa-for-azure-ad-b2c"></a>Configurare la SPA JavaScript per Azure AD B2C

1. A questo punto, è possibile configurare la SPA con l'indirizzo API di gestione API appropriato e il Azure AD B2C ID applicazione/client corretti.
1. Tornare al pannello archiviazione portale di Azure 
1. Selezionare ' contenitori ' (in ' impostazioni ') 
1. Selezionare il contenitore ' $web ' dall'elenco
1. Selezionare index.html BLOB dall'elenco 
1. Fare clic su' modifica ' 
1. Aggiornare i valori di autenticazione nella sezione MSAL config in modo che corrispondano all'applicazione *front-end* registrata in precedenza in B2C. Usare i commenti del codice per suggerimenti sul modo in cui i valori di configurazione devono essere osservati.
Il valore dell' *autorità* deve essere nel formato:-https:///{b2ctenantname}. b2clogin. com/TFP/{b2ctenantname}. onmicrosoft. com}/{signupandsigninpolicyname}, se sono stati usati i nomi di esempio e il tenant B2C è denominato ' Contoso ', si prevede che l'autorità sia ' https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com}/Frontendapp_signupandsignin '.
1. Impostare i valori dell'API in modo che corrispondano all'indirizzo back-end (l'URL di base dell'API registrato in precedenza e i valori "b2cScopes" sono stati registrati in precedenza per l' *applicazione back-end*).
1. Fare clic su Salva.

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Impostare gli URI di reindirizzamento per l'app front-end Azure AD B2C

1. Aprire il pannello Azure AD B2C e passare alla registrazione dell'applicazione per l'applicazione front-end JavaScript.
1. Fare clic su' reindirizza URI ' ed eliminare il segnaposto ' https://jwt.ms ' immesso in precedenza.
1. Aggiungere un nuovo URI per l'endpoint primario (archiviazione), meno la barra finale.

   > [!NOTE]
   > Questa configurazione comporterà un client dell'applicazione front-end che riceve un token di accesso con le attestazioni appropriate da Azure AD B2C.  
   > La SPA sarà in grado di aggiungerla come bearer token nell'intestazione HTTPS nella chiamata all'API back-end.  
   > 
   > Gestione API consente di pre-convalidare il token, il limite di frequenza delle chiamate all'endpoint sia dall'oggetto del JWT emesso da Azure ID (l'utente) che dall'indirizzo IP del chiamante (a seconda del livello di servizio di gestione API, vedere la nota precedente), prima di passare la richiesta all'API di ricezione della funzione di Azure, aggiungendo la chiave di sicurezza Functions.  
   > Il SPA eseguirà il rendering della risposta nel browser.
   >
   > *Complimenti, hai configurato Azure AD B2C, gestione API di Azure, funzioni di Azure, app Azure autorizzazione del servizio per lavorare in perfetta armonia!*

Ora è disponibile una semplice app con una semplice API protetta, che verrà testata.

## <a name="test-the-client-application"></a>Testare l'applicazione client

1. Aprire l'URL dell'app di esempio annotato dall'account di archiviazione creato in precedenza.
1. Fare clic su "Accedi" nell'angolo superiore destro. questo clic mostrerà il Azure AD B2C iscrizione o l'accesso al profilo.
1. L'app dovrebbe ricevere un benvenuto dal nome del profilo B2C.
1. A questo punto, fare clic su "chiama API" per aggiornare la pagina con i valori restituiti dall'API protetta.
1. Se si fa *ripetutamente* clic sul pulsante chiama API e si esegue il livello Developer o versione successiva di gestione API, è opportuno notare che la soluzione inizierà a limitare il numero di API e che questa funzionalità dovrebbe essere segnalata nell'app con un messaggio appropriato.

## <a name="and-were-done"></a>E abbiamo finito

I passaggi precedenti possono essere adattati e modificati per consentire molti usi diversi di Azure AD B2C con gestione API.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [Azure Active Directory e OAuth 2.0](../active-directory/develop/authentication-vs-authorization.md).
* Altre informazioni sui [video](https://azure.microsoft.com/documentation/videos/index/?services=api-management) relativi a Gestione API.
* Per altri metodi di protezione del servizio back-end, vedere [Autenticazione reciproca dei certificati](api-management-howto-mutual-certificates.md).
* [Creare un'istanza del servizio gestione API](get-started-create-service-instance.md).
* [Gestire la prima API](import-and-publish.md).