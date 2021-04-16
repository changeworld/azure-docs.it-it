---
title: Condizione di posizione nell'Azure Active Directory condizionale
description: Informazioni su come usare la condizione della posizione per controllare l'accesso alle app cloud in base al percorso di rete dell'utente.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 8b46ca16fc32a7b96c071a745f49bf5d5557f34b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530221"
---
# <a name="using-the-location-condition-in-a-conditional-access-policy"></a>Uso della condizione di posizione in un criterio di accesso condizionale 

Come illustrato nell'articolo [di](overview.md) panoramica i criteri di accesso condizionale sono al massimo un'istruzione if-then che combina i segnali, per prendere decisioni e applicare i criteri dell'organizzazione. Uno di questi segnali che possono essere incorporati nel processo decisionale è il percorso di rete.

![Segnale condizionale concettuale oltre a decisione per l'applicazione di criteri](./media/location-condition/conditional-access-signal-decision-enforcement.png)

Le organizzazioni possono usare questo percorso di rete per attività comuni come: 

- Necessità dell'autenticazione a più fattori per gli utenti che accedono a un servizio quando non sono connessi alla rete aziendale.
- Blocco dell'accesso per gli utenti che accedono a un servizio da specifici paesi o aree geografiche.

Il percorso di rete è determinato dall'indirizzo IP pubblico fornito da un client Azure Active Directory. Per impostazione predefinita, i criteri di accesso condizionale si applicano a tutti gli indirizzi IPv4 e IPv6. 

## <a name="named-locations"></a>Posizioni specifiche

I percorsi vengono designati nel portale di Azure in **Azure Active Directory**  >  **sicurezza**  >  **condizionale Percorsi**  >  **denominati**. Questi percorsi di rete denominati possono includere percorsi come gli intervalli di rete della sede centrale di un'organizzazione, gli intervalli di rete VPN o gli intervalli da bloccare. Le località denominate possono essere definite da intervalli di indirizzi IPv4/IPv6 o da paesi/aree geografiche. 

![Percorsi denominati nella portale di Azure](./media/location-condition/new-named-location.png)

### <a name="ip-address-ranges"></a>Intervalli di indirizzi IP

Per definire una posizione denominata in base agli intervalli di indirizzi IPv4/IPv6, è necessario specificare un **nome** e un intervallo IP. 

Le posizioni denominate definite dagli intervalli di indirizzi IPv4/IPv6 sono soggette alle limitazioni seguenti: 
- Configurare fino a 195 località denominate
- Configurare fino a 2000 intervalli IP per ogni località denominata
- Sono supportati entrambi gli intervalli IPv4 e IPv6
- Gli intervalli IP privati non possono essere configurati
- Il numero di indirizzi IP contenuti in un intervallo è limitato. Quando si definisce un intervallo IP, sono consentite solo maschere CIDR maggiori di /8. 

### <a name="trusted-locations"></a>Percorsi attendibili

Gli amministratori possono designare le località denominate definite dagli intervalli di indirizzi IP come posizioni denominate attendibili. 

![Percorsi attendibili nel portale di Azure](./media/location-condition/new-trusted-location.png)

Gli account di accesso da posizioni denominate attendibili migliorano l'accuratezza del calcolo dei rischi di Azure AD Identity Protection, abbassando il rischio di accesso degli utenti quando eseguono l'autenticazione da una posizione contrassegnata come attendibile. Inoltre, i percorsi denominati attendibili possono essere destinati ai criteri di accesso condizionale. Ad esempio, potrebbe essere necessario limitare la registrazione dell'autenticazione a più fattori solo a percorsi denominati attendibili. 

### <a name="countries-and-regions"></a>Paesi e aree geografiche

Alcune organizzazioni possono scegliere di limitare l'accesso a determinati paesi o aree geografiche usando l'accesso condizionale. Oltre a definire località denominate in base agli intervalli IP, gli amministratori possono definire località denominate in base al paese o alle aree geografiche. Quando un utente accede, Azure AD risolve l'indirizzo IPv4 dell'utente in un paese o un'area geografica e il mapping viene aggiornato periodicamente. Le organizzazioni possono usare località denominate definite dai paesi per bloccare il traffico dai paesi in cui non sono commerciali, ad esempio la Corea del Nord. 

> [!NOTE]
> Non è possibile eseguire il mapping degli account di accesso da indirizzi IPv6 a paesi o aree geografiche e sono considerate aree sconosciute. Solo gli indirizzi IPv4 possono essere mappati a paesi o aree geografiche.

![Creare una nuova località basata su paese o area geografica nel portale di Azure](./media/location-condition/new-named-location-country-region.png)

#### <a name="include-unknown-areas"></a>Includi aree sconosciute

Alcuni indirizzi IP non sono mappati a un paese o a un'area specifica, inclusi tutti gli indirizzi IPv6. Per acquisire questi percorsi IP, selezionare la casella **Includi aree sconosciute quando** si definisce una località. Questa opzione consente di scegliere se questi indirizzi IP devono essere inclusi nella posizione specifica. Usare questa impostazione quando i criteri che usano la posizione specifica devono essere applicati a posizioni sconosciute.

### <a name="configure-mfa-trusted-ips"></a>Configurare gli ip attendibili MFA

È inoltre possibile configurare gli intervalli di indirizzi IP che rappresentano la Intranet locale dell'organizzazione nelle [impostazioni del servizio di autenticazione a più fattori](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Questa funzionalità consente di configurare fino a 50 intervalli di indirizzi IP. Gli intervalli di indirizzi IP sono in formato CIDR. Per altre informazioni, vedere [Ip attendibili.](../authentication/howto-mfa-mfasettings.md#trusted-ips)  

Se sono stati configurati indirizzi IP attendibili, questi vengono visualizzati come IP attendibili **MFA** nell'elenco delle località per la condizione della posizione.

### <a name="skipping-multi-factor-authentication"></a>Ignorare l'autenticazione a più fattori

Nella pagina delle impostazioni del servizio di autenticazione a più fattori, è possibile identificare gli utenti della rete Intranet aziendale selezionando **Ignora l'autenticazione a più fattori per le richieste provenienti da utenti federati nella Intranet**. Questa impostazione indica che l'attestazione della rete aziendale interna, rilasciata da AD FS, deve essere attendibile e usata per identificare l'utente come appartenente alla rete aziendale. Per altre informazioni, vedere [Abilitare la funzionalità Ip attendibili tramite l'accesso condizionale.](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access)

Dopo aver selezionato questa opzione, l'inclusione del percorso denominato **MFA Trusted IPS** verrà applicata a tutti i criteri con questa opzione selezionata.

Per le applicazioni per dispositivi mobili e desktop, con durate di sessione di lunga durata, l'accesso condizionale viene rivalutato periodicamente. Il valore predefinito è una volta ogni ora. Quando l'attestazione della rete aziendale interna viene emessa solo al momento dell'autenticazione iniziale, Azure AD potrebbe non disporre di un elenco di intervalli IP attendibili. In questo caso, è più difficile determinare se l'utente sia ancora presente nella rete aziendale:

1. Verificare se l'indirizzo IP dell'utente è in uno degli intervalli di indirizzi IP attendibili.
1. Controllare se i primi tre ottetti dell'indirizzo IP dell'utente corrispondono ai primi tre ottetti dell'indirizzo IP dell'autenticazione iniziale. L'indirizzo IP viene confrontato con l'autenticazione iniziale quando l'attestazione all'interno della rete aziendale è stata originariamente rilasciata e il percorso utente è stato convalidato.

Se entrambi i passaggi danno esito negativo, l'utente non viene più considerato come su un indirizzo IP attendibile.

## <a name="location-condition-in-policy"></a>Condizione di posizione nei criteri

Quando si configura la condizione per la posizione, è possibile distinguere tra:

- Tutti i percorsi
- Tutte le località attendibili
- Le località selezionate

### <a name="any-location"></a>Tutti i percorsi

Per impostazione predefinita, la selezione di **Tutte le località** fa in modo che i criteri vengano applicati a tutti gli indirizzi IP, cioè qualsiasi indirizzo su Internet. Questa impostazione non è limitata agli indirizzi IP configurati come posizione specifica. Quando si seleziona **Tutte le località**, è comunque possibile escludere percorsi specifici dai criteri. Ad esempio, è possibile applicare dei criteri a tutte le posizioni tranne che ai percorsi attendibili per impostare l'ambito per tutte le posizioni ad eccezione della rete aziendale.

### <a name="all-trusted-locations"></a>Tutte le località attendibili

Questa opzione si applica a:

- Tutte le posizioni che sono state contrassegnate come attendibili
- Gli **indirizzi IP attendibili MFA** (se configurati)

### <a name="selected-locations"></a>Le località selezionate

Con questa opzione è possibile selezionare una o più posizioni specifiche. Per applicare criteri con questa impostazione, un utente deve connettersi da una delle posizioni selezionate. Quando si fa clic su **Seleziona**, si apre il controllo di selezione delle reti denominate che mostra l'elenco delle reti denominate. L'elenco indica anche se il percorso di rete è stato contrassegnato come attendibile. La posizione specifica denominata **Indirizzi IP attendibili MFA** viene usata per includere le impostazioni IP che possono essere configurate nella pagina di impostazione del servizio di autenticazione a più fattori.

## <a name="ipv6-traffic"></a>Traffico IPv6

Per impostazione predefinita, i criteri di accesso condizionale verranno applicati a tutto il traffico IPv6. È possibile escludere intervalli di indirizzi IPv6 specifici da un criterio di accesso condizionale se non si vuole applicare criteri per intervalli IPv6 specifici. Ad esempio, se si vuole non applicare un criterio per gli usi nella rete aziendale e la rete aziendale è ospitata in intervalli IPv6 pubblici.  

### <a name="when-will-my-tenant-have-ipv6-traffic"></a>Quando il tenant avrà traffico IPv6?

Azure Active Directory (Azure AD) non supporta attualmente connessioni di rete dirette che usano IPv6. Tuttavia, in alcuni casi il traffico di autenticazione viene proxy tramite un altro servizio. In questi casi, l'indirizzo IPv6 verrà usato durante la valutazione dei criteri.

La maggior parte del traffico IPv6 che viene proxy Azure AD proviene da Microsoft Exchange Online. Se disponibile, Exchange preferirà le connessioni IPv6. **Pertanto, se si dispone di criteri di accesso condizionale per Exchange, che sono stati configurati per intervalli IPv4 specifici, è necessario assicurarsi di aver aggiunto anche gli intervalli IPv6 delle organizzazioni.** L'esclusione degli intervalli IPv6 causerà un comportamento imprevisto per i due casi seguenti:

- Quando un client di posta elettronica viene usato per connettersi a Exchange Online con l'autenticazione legacy, Azure AD ricevere un indirizzo IPv6. La richiesta di autenticazione iniziale passa a Exchange e viene quindi inviata tramite proxy Azure AD.
- Quando outlook Accesso Web (OWA) viene usato nel browser, verifica periodicamente che tutti i criteri di accesso condizionale continuino a essere soddisfatti. Questo controllo viene usato per rilevare i casi in cui un utente potrebbe essere stato spostato da un indirizzo IP consentito a una nuova posizione, ad esempio il bar in fondo alla strada. In questo caso, se viene usato un indirizzo IPv6 e se l'indirizzo IPv6 non è in un intervallo configurato, la sessione dell'utente potrebbe essere interrotta e essere reindirizzata a Azure AD per eseguire di nuovo l'autenticazione. 

Questi sono i motivi più comuni per cui potrebbe essere necessario configurare intervalli IPv6 nelle posizioni denominate. Inoltre, se si usano reti virtuali di Azure, si avrà traffico proveniente da un indirizzo IPv6. Se il traffico di rete virtuale è bloccato da un criterio di accesso condizionale, controllare Azure AD log di accesso. Dopo aver identificato il traffico, è possibile ottenere l'indirizzo IPv6 in uso ed escluderlo dai criteri. 

> [!NOTE]
> Se si vuole specificare un intervallo CIDR IP per un singolo indirizzo, applicare la maschera di bit /128. Se si pronuncia l'indirizzo IPv6 2607:fb90:b27a:6f69:f8d5:dea0:fb39:74a e si vuole escludere il singolo indirizzo come intervallo, si userà 2607:fb90:b27a:6f69:f8d5:dea0:fb39:74a/128.

### <a name="identifying-ipv6-traffic-in-the-azure-ad-sign-in-activity-reports"></a>Identificazione del traffico IPv6 nei report Azure AD'attività di accesso

È possibile individuare il traffico IPv6 nel tenant accedendo ai report [Azure AD'attività di accesso.](../reports-monitoring/concept-sign-ins.md) Dopo aver aperto il report attività, aggiungere la colonna "Indirizzo IP". Questa colonna consente di identificare il traffico IPv6.

È anche possibile trovare l'indirizzo IP del client facendo clic su una riga nel report e quindi selezionando la scheda "Località" nei dettagli dell'attività di accesso. 

## <a name="what-you-should-know"></a>Informazioni utili

### <a name="when-is-a-location-evaluated"></a>Quando viene valutata una posizione?

I criteri di accesso condizionale vengono valutati quando:

- Un utente accede inizialmente a un'app Web, un'applicazione per dispositivi mobili o un'applicazione desktop.
- Un'applicazione desktop o per dispositivo mobili che usa l'autenticazione moderna usa un token di aggiornamento per acquisire un nuovo token di accesso. Per impostazione predefinita, questo controllo è una volta all'ora.

Questo controllo significa che per le applicazioni desktop e per dispositivi mobili che usano l'autenticazione moderna, viene rilevata una modifica nel percorso entro un'ora dalla modifica del percorso di rete. Per le applicazioni desktop e per dispositivi mobili che non usano l'autenticazione moderna, questo criterio viene applicato a ogni richiesta di token. La frequenza della richiesta può variare in base all'applicazione. Analogamente, per le applicazioni Web il criterio viene applicato all'accesso iniziale e rimane valido per l'intera durata della sessione dell'applicazione Web interessata. A causa delle differenze a livello di durata delle sessioni delle applicazioni, anche il tempo tra le valutazioni dei criteri può variare. Il criterio viene applicato ogni volta che l'applicazione richiede un nuovo token di accesso.

Per impostazione predefinita, Azure AD rilascia un token su base oraria. Se si esce dalla rete aziendale, entro un'ora i criteri vengono applicati per le applicazioni che usano l'autenticazione moderna.

### <a name="user-ip-address"></a>Indirizzo IP utente

L'indirizzo IP usato nella valutazione dei criteri è l'indirizzo IP pubblico dell'utente. Per i dispositivi in una rete privata, questo indirizzo IP non è l'INDIRIZZO IP client del dispositivo dell'utente sulla Intranet, ma è l'indirizzo usato dalla rete per connettersi alla rete Internet pubblica.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Caricamento e download in blocco delle posizioni specifiche

Quando si creano o si aggiornano le posizioni specifiche, per gli aggiornamenti in blocco è possibile caricare o scaricare un file CSV con gli intervalli di indirizzi IP. Un caricamento sostituisce gli intervalli IP nell'elenco con gli intervalli del file. Ogni riga del file contiene un intervallo di indirizzi IP in formato CIDR.

### <a name="cloud-proxies-and-vpns"></a>Proxy cloud e VPN

Quando si usa un proxy ospitato nel cloud o una soluzione VPN, l'indirizzo IP usato da Azure AD durante la valutazione dei criteri è l'indirizzo IP del proxy. L'intestazione X-Forwarded-For (XFF) che contiene l'indirizzo IP pubblico dell'utente non viene usata perché non è certo che provenga da un'origine attendibile, pertanto potrebbe essere un metodo per falsificare un indirizzo IP.

Quando è presente un proxy cloud, è possibile usare un criterio usato per richiedere un dispositivo aggiunto Azure AD ibrido o l'attestazione corpnet interna da AD FS.

### <a name="api-support-and-powershell"></a>Supporto dell'API e PowerShell

È disponibile una versione di anteprima API Graph percorsi denominati. Per altre informazioni, vedere [l'API namedLocation](/graph/api/resources/namedlocation).

> [!NOTE]
> I percorsi denominati creati tramite PowerShell vengono visualizzati solo in Percorsi denominati (anteprima). Non è possibile visualizzare le posizioni denominate nella visualizzazione precedente.  

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come configurare i criteri di accesso condizionale, vedere l'articolo [Creazione di criteri di accesso condizionale](concept-conditional-access-policies.md).
- Per informazioni su un criterio di esempio che usa la condizione della posizione, Vedere l'articolo [Accesso condizionale: Bloccare l'accesso in base alla posizione](howto-conditional-access-policy-location.md)
