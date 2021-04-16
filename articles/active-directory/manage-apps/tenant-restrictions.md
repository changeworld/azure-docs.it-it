---
title: Uso delle restrizioni del tenant per gestire l'accesso alle app SaaS - Azure AD
description: Come usare le restrizioni del tenant per stabilire quali utenti possono accedere alle app in base al tenant Azure AD.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 4/6/2021
ms.author: iangithinji
ms.reviewer: hpsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 941bf61f3387abf19be58bdd4d8861ab123e244f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376580"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Uso delle restrizioni del tenant per gestire l'accesso alle applicazioni cloud SaaS

Le organizzazioni di grandi dimensioni che sottolineano la sicurezza vogliono passare a servizi cloud come Microsoft 365, ma devono sapere che gli utenti possono solo accedere alle risorse approvate. In genere, le aziende limitano gli indirizzi IP o i nomi di dominio quando vogliono gestire gli accessi. Questo approccio non è efficace in situazioni dove le app SaaS (software come servizio) vengono ospitate in un cloud pubblico ed eseguite su nomi di dominio condivisi come [outlook.office.com](https://outlook.office.com/) e [login.microsoftonline.com](https://login.microsoftonline.com/). Bloccare questi indirizzi impedirebbe totalmente agli utenti di accedere ad Outlook sul Web invece di limitare il loro accesso alle identità e alle risorse approvate.

La soluzione offerta da Azure Active Directory (Azure AD) per risolvere questo problema è una funzionalità denominata Restrizioni del tenant. Restrizioni del tenant consente alle organizzazioni di controllare l'accesso alle applicazioni cloud SaaS in base al tenant di Azure AD usato dalle applicazioni per il Single Sign-On. Ad esempio, è possibile consentire l'accesso alle applicazioni Microsoft 365 dell'organizzazione, impedendo l'accesso alle istanze di queste stesse applicazioni da parte di altre organizzazioni.  

Con Restrizioni del tenant le organizzazioni possono specificare l'elenco dei tenant ai quali i loro utenti possono accedere. Azure AD consente quindi l'accesso solo ai tenant autorizzati.

Questo articolo è in Microsoft 365 restrizioni del tenant, ma la funzionalità protegge tutte le app che inviano l'utente Azure AD per l'accesso Single Sign-On. Se si usano app SaaS con un tenant Azure AD diverso dal tenant usato dal Microsoft 365, assicurarsi che siano consentiti tutti i tenant necessari, ad esempio negli scenari di collaborazione B2B. Per ulteriori informazioni sulle app cloud SaaS, vedere il [Marketplace di Active Directory](https://azuremarketplace.microsoft.com/marketplace/apps).

Inoltre, la funzionalità delle restrizioni dei tenant supporta ora il blocco dell'uso di tutte le applicazioni consumer [Microsoft](#blocking-consumer-applications-public-preview) ,ad esempio OneDrive, Hotmail e Xbox.com.  Viene utilizzata un'intestazione separata per `login.live.com` l'endpoint ed è descritto in dettaglio alla fine del documento.

## <a name="how-it-works"></a>Funzionamento

La soluzione globale è composta dai seguenti elementi:

1. **Azure AD:** se l'intestazione è presente, Azure AD solo i token di `Restrict-Access-To-Tenants: <permitted tenant list>` sicurezza per i tenant consentiti.

2. **Infrastruttura di server proxy locale**: questa infrastruttura è un dispositivo proxy idoneo per l'ispezione Transport Layer Security (TLS). Per inserire l'intestazione contenente l'elenco dei tenant consentiti nel traffico destinato a Azure AD è necessario configurare il proxy.

3. **Software client**: per supportare Restrizioni del tenant, il software client deve richiedere i token direttamente da Azure AD in modo che l'infrastruttura proxy possa intercettare il traffico. Le applicazioni basate Microsoft 365 browser supportano attualmente le restrizioni dei tenant, così come i client di Office che usano l'autenticazione moderna (ad esempio OAuth 2.0).

4. **Tecniche di autenticazione moderne**: i servizi cloud devono usare tecniche di autenticazione moderne per Restrizioni del tenant e per bloccare l'accesso a tutti i tenant non consentiti. È necessario configurare i Microsoft 365 cloud per l'uso dei protocolli di autenticazione moderni per impostazione predefinita. Per le informazioni più recenti sul Microsoft 365 per l'autenticazione moderna, vedere Aggiornamento dell'autenticazione moderna di [Office 365.](https://www.microsoft.com/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)

Il diagramma seguente illustra il flusso di traffico di alto livello. Le restrizioni dei tenant richiedono l'ispezione TLS solo sul traffico verso Azure AD, non verso i Microsoft 365 cloud. Questa distinzione è importante perché il volume di traffico per l'autenticazione in Azure AD è in genere molto inferiore rispetto a quello verso applicazioni SaaS come Exchange Online e SharePoint Online.

![Flusso di traffico di Restrizioni del tenant, diagramma](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Configurazione di Restrizioni del tenant

Ci sono due passaggi iniziali per quanto riguarda Restrizioni del tenant. Per prima cosa bisogna controllare che i client possano connettersi agli indirizzi corretti. In seguito bisogna configurare l'infrastruttura proxy.

### <a name="urls-and-ip-addresses"></a>URL e indirizzi IP

Per usare Restrizioni del tenant i client devono essere in grado di connettersi agli URL di Azure AD seguenti per l'autenticazione: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.microsoft.com](https://login.microsoft.com/) e [login.windows.net](https://login.windows.net/). Inoltre, per accedere a Office 365, i client devono potersi connettere ai nomi di dominio completi (FQDN), agli URL e agli indirizzi IP definiti in [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Configurazione e requisiti del proxy

Per abilitare Restrizioni del tenant nell'infrastruttura proxy è necessaria la configurazione seguente. Questa guida è generica, pertanto è consigliabile consultare la documentazione del fornitore del proxy per i passaggi di implementazione specifici.

#### <a name="prerequisites"></a>Prerequisiti

- Il proxy deve poter eseguire l'intercettazione SSL, inserire intestazioni HTTP e filtrare le destinazioni tramite URL/FQDN.

- I client devono considerare attendibile la catena di certificati presentata dal proxy per le comunicazioni TLS. Ad esempio, se vengono usati certificati da un'[infrastruttura a chiave pubblica (PKI) interna](/windows/desktop/seccertenroll/public-key-infrastructure), deve essere considerato attendibile il certificato interno dell'autorità di certificazione interna.

- Azure AD Premium 1 licenze sono necessarie per l'uso delle restrizioni del tenant.

#### <a name="configuration"></a>Configurazione

Per ogni richiesta in uscita login.microsoftonline.com, login.microsoft.com e login.windows.net, inserire due intestazioni HTTP: *Restrict-Access-To-Tenants* e *Restrict-Access-Context*.

> [!NOTE]
> Non includere sottodomini in `*.login.microsoftonline.com` nella configurazione del proxy. Questa operazione includerà device.login.microsoftonline.com e interferirà con l'autenticazione del certificato client, usata negli scenari di registrazione del dispositivo e di accesso condizionale basato su dispositivo. Configurare il server proxy in modo da escludere device.login.microsoftonline.com dall'inserimento di intestazioni e interruzioni TLS.

Le intestazioni devono includere gli elementi seguenti:

- Per *Restrict-Access-To-Tenants,* usare il valore , ovvero un elenco delimitato da virgole di tenant a cui si vuole consentire agli \<permitted tenant list\> utenti di accedere. Qualsiasi dominio registrato con un tenant può essere usato per identificare il tenant in questo elenco, nonché l'ID directory stesso. Per un esempio di tutti e tre i modi per descrivere un tenant, la coppia nome/valore per consentire Contoso, Fabrikam e Microsoft è simile alla seguente: `Restrict-Access-To-Tenants: contoso.com,fabrikam.onmicrosoft.com,72f988bf-86f1-41af-91ab-2d7cd011db47`

- Per *Restrict-Access-Context*, usare un valore ID di directory singola, dichiarando quale tenant imposta Restrizioni del tenant. Ad esempio, per dichiarare Contoso come tenant che imposta i criteri di restrizione del tenant, la coppia nome/valore è simile alla seguente: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d` .  È **necessario** usare il proprio ID directory in questo punto per ottenere i log per queste autenticazioni.

> [!TIP]
> L'ID della directory si trova nel [Portale di Azure Active Directory](https://aad.portal.azure.com/). Accedere come amministratore, selezionare **Azure Active Directory**, quindi selezionare **Proprietà**. 
>
> Per verificare che un ID directory o un nome di dominio faccia riferimento allo stesso tenant, usare tale ID o dominio al posto di <tenant> in questo URL: `https://login.microsoftonline.com/<tenant>/v2.0/.well-known/openid-configuration` .  Se i risultati con il dominio e l'ID sono gli stessi, fanno riferimento allo stesso tenant. 

Per impedire agli utenti di inserire le proprie intestazioni HTTP con tenant non approvati, il proxy deve sostituire l'intestazione *Restrict-Access-To-Tenants* se questa è già presente nella richiesta in ingresso.

È necessario forzare l'uso del proxy nei client per tutte le richieste a login.microsoftonline.com, login.microsoft.com e login.windows.net. Ad esempio, se vengono usati file PAC per reindirizzare i client all'uso del proxy, gli utenti finali non devono poter modificare o disabilitare tali file.

## <a name="the-user-experience"></a>Esperienza utente

In questa sezione viene illustrata l'esperienza per utenti finali e amministratori.

### <a name="end-user-experience"></a>Esperienza utente finale

Un utente di esempio si trova nella rete Contoso ma tenta di accedere online all'istanza Fabrikam di un'applicazione SaaS condivisa come Outlook. Se Fabrikam è un tenant non consentito per l'istanza Contoso, viene visualizzato un messaggio di rifiuto dell'accesso che indica che si sta tentando di accedere a una risorsa che appartiene a un'organizzazione non approvata dal reparto IT.

![Messaggio di errore delle restrizioni del tenant, da aprile 2021](./media/tenant-restrictions/error-message.png)

### <a name="admin-experience"></a>Esperienza amministratore

La configurazione di Restrizioni del tenant viene eseguita nell'infrastruttura del proxy aziendale, ma gli amministratori possono accedere direttamente ai relativi report nel portale di Azure. Per visualizzare i report:

1. Accedere al [portale di Azure Active Directory](https://aad.portal.azure.com/). Viene visualizzato il dashboard **Interfaccia di amministrazione di Azure Active Directory**.

2. Nel riquadro sinistro selezionare **Azure Active Directory**. Viene visualizzata la pagina di panoramica di Azure Active Directory.

3. Nella pagina Panoramica selezionare **Restrizioni del tenant.**

L'amministratore per il tenant specificato come tenant Restricted-Access-Context può usare questo report per visualizzare gli accessi bloccati a causa dei criteri di Restrizioni del tenant, inclusi l'ID della directory di destinazione e le identità usate. Gli accessi sono inclusi se nelle impostazioni del tenant la restrizione di accesso è impostata sul tenant dell'utente o sul tenant della risorsa.

Il report può contenere informazioni limitate, ad esempio l'ID della directory di destinazione, quando un utente che si trova in un tenant diverso da Restricted-Access-Context accede. In questo caso, le informazioni identificabili dall'utente, ad esempio il nome e il nome dell'entità utente, vengono mascherate per proteggere i dati utente in altri tenant ("{PII Removed} @domain.com " o 000000000-0000-0000-0000-000000000000 al posto dei nomi utente e degli ID oggetto in base alle esigenze). 

Come per gli altri report nel portale di Azure, è possibile usare i filtri per specificare l'ambito del report. È possibile usare filtri per intervalli di tempo, utenti, applicazioni, client o stati specifici. Se si seleziona il pulsante **Colonne** è possibile scegliere di visualizzare i dati con qualsiasi combinazione dei campi seguenti:

- **Utente:** in questo campo è possibile rimuovere i dati personali, in cui verranno impostati su `00000000-0000-0000-0000-000000000000` . 
- **Applicazione**
- **Status**
- **Data**
- **Data (UTC)** - dove UTC è Coordinated Universal Time
- **Indirizzo IP**
- **Client**
- **Nome** utente: in questo campo possono essere rimossi i dati personali, in cui verranno impostati su `{PII Removed}@domain.com`
- **Posizione**
- **ID tenant di destinazione**

## <a name="microsoft-365-support"></a>Supporto tecnico di Microsoft 365

Microsoft 365 applicazioni devono soddisfare due criteri per supportare completamente le restrizioni dei tenant:

1. Il client usato supporta tecniche di autenticazione moderne.
2. L'autenticazione moderna è abilitata come protocollo di autenticazione predefinito per il servizio cloud.

Fare riferimento al [documento aggiornato sull'autenticazione moderna di Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) per le informazioni più recenti sui client Office che supportano attualmente l'autenticazione moderna. Questa pagina include anche collegamenti a istruzioni su come abilitare l'autenticazione moderna in tenant Exchange Online e Skype for Business Online specifici. SharePoint Online abilità già l'autenticazione moderna per impostazione predefinita.

Microsoft 365 applicazioni basate su browser (portale di Office, Yammer, siti di SharePoint, Outlook sul Web e altro ancora) supportano attualmente le restrizioni dei tenant. I client che richiedono molte risorse (Outlook, Skype for Business, Word, Excel, PowerPoint e così via) possono applicare restrizioni del tenant solo quando si usa l'autenticazione moderna.  

I client Outlook e Skype for Business che supportano l'autenticazione moderna possono comunque usare protocolli legacy con tenant dove l'autenticazione moderna non è abilitata, ignorando di fatto Restrizioni del tenant. Restrizioni del tenant potrebbe bloccare applicazioni che usano protocolli legacy se queste contattano login.microsoftonline.com, login.microsoft.com o login.windows.net durante l'autenticazione.

In Outlook per Windows, i clienti possono scegliere di implementare delle restrizioni per impedire agli utenti finali di aggiungere ai propri profili account di posta elettronica non approvati. Ad esempio, vedere l'impostazione di criteri di gruppo [Impedisci l'aggiunta di account di Exchange non predefiniti](https://gpsearch.azurewebsites.net/default.aspx?ref=1).

## <a name="testing"></a>Test

Se si desidera provare la funzionalità Restrizioni del tenant prima di implementarla in tutta l'organizzazione, sono disponibili due opzioni: un approccio basato su host con uno strumento come Fiddler o una pianificazione per fasi delle impostazioni del proxy.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler per un approccio basato su host

Fiddler è un proxy di debug Web gratuito, utilizzabile per acquisire e modificare il traffico HTTP/HTTPS, incluso l'inserimento di intestazioni HTTP. Per configurare il test di Restrizioni del tenant con Fiddler, eseguire la procedura seguente:

1. [Scaricare e installare Fiddler](https://www.telerik.com/fiddler).

2. Configurare Fiddler per decrittografare il traffico HTTPS, come indicato nella [relativa documentazione di aiuto](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Configurare Fiddler per inserire le intestazioni *Restrict-Access-To-Tenants* e *Restrict-Access-Context* con regole personalizzate:

   1. Nello strumento Fiddler Web Debugger, selezionare il menu **Rules** (Regole) e selezionare **Customize Rules…** (Personalizza regole…) per aprire il file CustomRules.

   2. Aggiungere le righe seguenti all'inizio della funzione `OnBeforeRequest`. Sostituire \<List of tenant identifiers\> con un dominio registrato con il tenant , ad esempio `contoso.onmicrosoft.com` . Sostituire \<directory ID\> con l'identificatore GUID Azure AD del tenant.  È **necessario** includere l'identificatore GUID corretto per visualizzare i log nel tenant. 

   ```JScript.NET
    // Allows access to the listed tenants.
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<List of tenant identifiers>";
          oSession.oRequest["Restrict-Access-Context"] = "<Your directory ID>";
      }

    // Blocks access to consumer apps
      if (
          oSession.HostnameIs("login.live.com")
      )
      {
          oSession.oRequest["sec-Restrict-Tenant-Access-Policy"] = "restrict-msa";
      }
   ```

   Se è necessario consentire più tenant, separare i vari nomi di tenant con le virgole. Ad esempio:

   `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Salvare e chiudere il file CustomRules.

Dopo aver configurato Fiddler, è possibile acquisire il traffico accedendo al menu **File** e selezionando **Capture Traffic** (Acquisisci traffico).

### <a name="staged-rollout-of-proxy-settings"></a>Implementazione per fasi delle impostazioni del proxy

A seconda delle funzionalità dell'infrastruttura di proxy, è possibile eseguire un'implementazione per fasi delle impostazioni agli utenti. Di seguito sono indicate due opzioni generali da tenere in considerazione:

1. Usare file PAC per indirizzare gli utenti di test a un'infrastruttura di proxy di test, mentre gli utenti normali continuano a utilizzare l'infrastruttura del proxy di produzione.
2. Alcuni server proxy possono supportare configurazioni diverse usando i gruppi.

Per informazioni dettagliate, consultare la documentazione del proprio server proxy.

## <a name="blocking-consumer-applications-public-preview"></a>Blocco delle applicazioni consumer (anteprima pubblica)

Le applicazioni di Microsoft che supportano account consumer e account aziendali, ad esempio [OneDrive](https://onedrive.live.com/) [o Microsoft Learn](/learn/), possono talvolta essere ospitate nello stesso URL.  Ciò significa che anche gli utenti che devono accedere a tale URL per motivi di lavoro possono accedervi per uso personale, cosa che potrebbe non essere consentita in base alle linee guida operative.

Alcune organizzazioni tentano di risolvere questo problema bloccando `login.live.com` per impedire l'autenticazione degli account personali.  Ciò presenta diversi svantaggi:

1. Il `login.live.com` blocco blocca l'uso di account personali in scenari guest B2B, che possono intromettersi nei visitatori e nella collaborazione.
1. [Autopilot richiede l'uso di `login.live.com` ](/mem/autopilot/networking-requirements) per la distribuzione. Gli scenari di Intune e Autopilot possono avere esito negativo `login.live.com` quando è bloccato.
1. I dati di telemetria dell'organizzazione e gli aggiornamenti di Windows basati login.live.com per gli ID dispositivo [smetteranno di funzionare.](/windows/deployment/update/windows-update-troubleshooting#feature-updates-are-not-being-offered-while-other-updates-are)

### <a name="configuration-for-consumer-apps"></a>Configurazione per le app consumer

Mentre l'intestazione funziona come elenco consentiti, il blocco account Microsoft (MSA) funziona come segnale di rifiuto, indicando alla piattaforma account Microsoft di non consentire agli utenti di accedere alle applicazioni `Restrict-Access-To-Tenants` consumer. Per inviare questo segnale, l'intestazione viene inserita al traffico in visita usando lo stesso proxy aziendale o lo stesso `sec-Restrict-Tenant-Access-Policy` `login.live.com` firewall precedente. [](#proxy-configuration-and-requirements) Il valore dell'intestazione deve essere `restrict-msa` . Quando l'intestazione è presente e un'app consumer sta tentando di accedere direttamente a un utente, tale accesso verrà bloccato.

Al momento, l'autenticazione alle applicazioni [](#admin-experience)consumer non viene visualizzata nei log di amministrazione, perché login.live.com è ospitata separatamente da Azure AD.

### <a name="what-the-header-does-and-does-not-block"></a>Cosa fa e non blocca l'intestazione

I `restrict-msa` criteri bloccano l'uso delle applicazioni consumer, ma consentono l'uso di diversi altri tipi di traffico e autenticazione:

1. Traffico senza utenti per i dispositivi.  Ciò include il traffico per Autopilot, i Windows Update e i dati di telemetria dell'organizzazione.
1. Autenticazione B2B degli account consumer. Gli utenti con account Microsoft invitati a collaborare con un tenant eseguono l'autenticazione [login.live.com](../external-identities/redemption-experience.md#invitation-redemption-flow) per accedere a un tenant delle risorse.
    1. Questo accesso viene controllato usando `Restrict-Access-To-Tenants` l'intestazione per consentire o negare l'accesso al tenant della risorsa.
1. Autenticazione "pass-through", usata da molte app di Azure e da Office.com, in cui le app usano Azure AD per consentire l'accesso degli utenti consumer in un contesto consumer.
    1. Questo accesso viene controllato anche tramite `Restrict-Access-To-Tenants` l'intestazione per consentire o negare l'accesso al tenant "pass-through" speciale ( `f8cdef31-a31e-4b4a-93e4-5f571e91255a` ).  Se questo tenant non viene visualizzato nell'elenco dei domini consentiti, gli account consumer verranno bloccati Azure AD `Restrict-Access-To-Tenants` l'accesso a queste app.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sull'[autenticazione moderna aggiornata di Office 365](https://www.microsoft.com/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- Vedere [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
