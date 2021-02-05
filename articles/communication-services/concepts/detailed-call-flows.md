---
title: Topologie del flusso di chiamate nei servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Informazioni sulle topologie del flusso di chiamate nei servizi di comunicazione di Azure.
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 12/11/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 1333bd08f8a79969817bcb21aa4580d1994d09ce
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594746"
---
# <a name="call-flow-topologies"></a>Topologie del flusso di chiamate
Questo articolo descrive le topologie del flusso di chiamate dei servizi di comunicazione di Azure. Questo è un ottimo articolo da consultare se si è un cliente aziendale che integra i servizi di comunicazione all'interno di una rete gestita. Per un'introduzione ai flussi di chiamata dei servizi di comunicazione, vedere la [documentazione concettuale sui flussi di chiamata](./call-flows.md).

## <a name="background"></a>Sfondo

### <a name="network-concepts"></a>Concetti di rete

Prima di esaminare le topologie del flusso di chiamate, verranno definiti alcuni termini a cui viene fatto riferimento nel documento.

Una **rete cliente** contiene tutti i segmenti di rete gestiti. Questo potrebbe includere reti cablate e wireless nell'ufficio o tra uffici, Data Center e provider di servizi Internet.

Una rete clienti ha in genere diversi perimetri di rete con firewall e/o server proxy che applicano i criteri di sicurezza dell'organizzazione. Si consiglia di eseguire una [valutazione della rete completa](https://docs.microsoft.com/microsoftteams/3-envision-evaluate-my-environment) per garantire prestazioni e qualità ottimali della soluzione di comunicazione.

La **rete dei servizi di comunicazione** è il segmento di rete che supporta i servizi di comunicazione di Azure. Questa rete è gestita da Microsoft ed è distribuita in tutto il mondo con bordi vicini alla maggior parte delle reti dei clienti. Questa rete è responsabile dell'inoltro del trasporto, dell'elaborazione multimediale per le chiamate al gruppo e di altri componenti che supportano le comunicazioni multimediali in tempo reale avanzate.

### <a name="types-of-traffic"></a>Tipi di traffico

I servizi di comunicazione sono basati principalmente su due tipi di traffico: **supporto in tempo reale** e **segnalazione**.

Il **supporto in tempo reale** viene trasmesso usando il protocollo di trasporto in tempo reale (RTP). Questo protocollo supporta la trasmissione di dati audio, video e condivisione schermo. Questi dati sono sensibili ai problemi di latenza di rete. Sebbene sia possibile trasmettere supporti in tempo reale tramite TCP o HTTP, è consigliabile utilizzare UDP come protocollo a livello di trasporto per supportare esperienze dell'utente finale a prestazioni elevate. I payload multimediali trasmessi tramite RTP vengono protetti tramite SRTP.

Gli utenti della soluzione Servizi di comunicazione verranno connessi ai servizi dai dispositivi client. La comunicazione tra questi dispositivi e i server viene gestita con la **segnalazione**. Ad esempio: l'avvio della chiamata e la chat in tempo reale sono supportati segnalando tra i dispositivi e il servizio. La maggior parte del traffico di segnalazione USA REST HTTPS, sebbene in alcuni scenari, SIP possa essere usato come protocollo di traffico di segnalazione. Sebbene questo tipo di traffico sia meno sensibile alla latenza, la segnalazione a bassa latenza consente agli utenti della soluzione un'esperienza utente finale piacevole.

### <a name="interoperability-restrictions"></a>Restrizioni di interoperabilità

Il flusso multimediale attraverso servizi di comunicazione è limitato come segue:

**Relay multimediali di terze parti.** L'interoperabilità con gli inoltri multimediali di terze parti non è supportata. Se uno degli endpoint multimediali è servizi di comunicazione, il supporto può attraversare solo i Media Relay nativi Microsoft, inclusi quelli che supportano Microsoft teams e Skype for business.

Un controller di bordo della sessione di terze parti (SBC) sul confine con PSTN deve terminare il flusso RTP/RTCP, protetto mediante SRTP e non inoltrarlo all'hop successivo. Se il flusso viene inoltrato all'hop successivo, potrebbe non essere compreso.

**Server proxy SIP di terze parti.** Una finestra di dialogo di segnalazione dei servizi di comunicazione con un SBC e/o un gateway di terze parti può attraversare i proxy SIP nativi Microsoft (proprio come i team). L'interoperabilità con proxy SIP di terze parti non è supportata.

**B2BUA di terze parti (o SBC).** Il flusso multimediale dei servizi di comunicazione da e verso la rete PSTN viene terminato da un SBC di terze parti. L'interoperabilità con un SBC di terze parti all'interno della rete di servizi di comunicazione (in cui un SBC di terze parti media due endpoint di servizi di comunicazione) non è supportato.

### <a name="unsupported-technologies"></a>Tecnologie non supportate

**Rete VPN.** Servizi di comunicazione non supporta la trasmissione di contenuti multimediali tramite VPN. Se gli utenti usano client VPN, il client dovrebbe suddividere e instradare il traffico multimediale su una connessione non VPN, come specificato in [Abilitazione di Lync media per ignorare un tunnel VPN.](https://techcommunity.microsoft.com/t5/skype-for-business-blog/enabling-lync-media-to-bypass-a-vpn-tunnel/ba-p/620210)

*Si noti. Sebbene il titolo indichi Lync, è applicabile ai servizi e ai team di comunicazione di Azure.*

**Formatori di pacchetti.** I dispositivi di cattura dei pacchetti, l'ispezione dei pacchetti o i pacchetti di data shaping non sono supportati e possono peggiorare significativamente la qualità.

### <a name="call-flow-principles"></a>Principi del flusso di chiamate

Sono disponibili quattro principi generali che sostengono i flussi di chiamata dei servizi di comunicazione:

* **Il primo partecipante di una chiamata al gruppo di servizi di comunicazione determina l'area in cui è ospitata la chiamata**. Esistono eccezioni a questa regola in alcune topologie, descritte di seguito.
* **L'endpoint multimediale utilizzato per supportare una chiamata di servizi di comunicazione è selezionato in base alle esigenze di elaborazione dei supporti** e non è influenzato dal numero di partecipanti a una chiamata. Ad esempio, una chiamata Point-to-Point può usare un endpoint multimediale nel cloud per elaborare i supporti per la trascrizione o la registrazione, mentre una chiamata con due partecipanti non può usare alcun endpoint multimediale. Le chiamate ai gruppi utilizzeranno un endpoint multimediale per la combinazione e il routing. Questo endpoint è selezionato in base all'area in cui è ospitata la chiamata. Il traffico multimediale inviato da un client all'endpoint multimediale può essere indirizzato direttamente o può usare un inoltro di trasporto in Azure se le restrizioni del firewall di rete del cliente lo richiedono.
* **Il traffico multimediale per le chiamate peer-to-peer prende la route più diretta disponibile**, supponendo che la chiamata non necessiti di un endpoint multimediale nel cloud. La route preferita è diretta al peer remoto (client). Se una route diretta non è disponibile, uno o più Relay del trasporto effettueranno il inoltro del traffico. Il traffico multimediale non deve avere server trasversali che fungono da formatori di pacchetti, server VPN o altre funzioni che potrebbero ritardare l'elaborazione e peggiorare l'esperienza dell'utente finale.
* **Il traffico di segnalazione passa sempre a qualsiasi server più vicino all'utente.**

Per ulteriori informazioni sui dettagli sul percorso del supporto scelto, vedere la [documentazione concettuale sui flussi di chiamata](./call-flows.md).


## <a name="call-flows-in-various-topologies"></a>Flussi di chiamate in varie topologie

### <a name="communication-services-internet"></a>Servizi di comunicazione (Internet)

Questa topologia viene usata dai clienti che usano servizi di comunicazione dal cloud senza alcuna distribuzione locale, ad esempio l'interfaccia SIP. In questa topologia, il traffico da e verso i servizi di comunicazione passa attraverso Internet.

:::image type="content" source="./media/call-flows/detailed-flow-general.png" alt-text="Topologia di servizi di comunicazione di Azure.":::

*Figura 1-topologia di servizi di comunicazione*

La direzione delle frecce sul diagramma precedente riflette la direzione di avvio della comunicazione che influiscono sulla connettività nei perimetri aziendali. Nel caso di UDP per i supporti, i primi pacchetti possono fluire nella direzione inversa, ma questi pacchetti possono essere bloccati fino a quando non vengono propagati i pacchetti nell'altra direzione.

Descrizioni dei flussi:
* Flow 2 *: rappresenta un flusso avviato da un utente nella rete del cliente su Internet come parte dell'esperienza dei servizi di comunicazione dell'utente. Esempi di questi flussi includono la trasmissione di supporti DNS e peer-to-peer.
* Flow 2: rappresenta un flusso avviato da un utente remoto di servizi di comunicazione mobile con VPN per la rete del cliente.
* Flow 3: rappresenta un flusso avviato da un utente di servizi di comunicazione mobile remoto per gli endpoint di servizi di comunicazione.
* Flow 4: rappresenta un flusso avviato da un utente nella rete del cliente per i servizi di comunicazione.
* Flow 5: rappresenta un flusso multimediale peer-to-peer tra un utente di servizi di comunicazione e un altro all'interno della rete del cliente.
* Flow 6: rappresenta un flusso multimediale peer-to-peer tra un utente di servizi di comunicazione mobile remoto e un altro utente di servizi di comunicazione mobile remoto tramite Internet.

### <a name="use-case-one-to-one"></a>Caso d'uso: uno-a-uno

Le chiamate uno-a-uno usano un modello comune in cui il chiamante otterrà un set di candidati costituito da indirizzi IP/porte, tra cui local, Relay e riflessiv (indirizzo IP pubblico del client come osservato dall'inoltro) candidati. Il chiamante invia i candidati alla parte chiamata; l'entità chiamata ottiene anche un set di candidati simile e li invia al chiamante. I messaggi di controllo della connettività STUN vengono usati per trovare i percorsi del supporto chiamante/chiamato e il percorso di lavoro migliore è selezionato. I supporti, ovvero i pacchetti RTP/RTCP protetti tramite SRTP, vengono quindi inviati utilizzando la coppia candidata selezionata. L'inoltro del trasporto viene distribuito come parte dei servizi di comunicazione di Azure.

Se l'indirizzo IP locale/la porta candidati o i candidati riflessivi hanno connettività, il percorso diretto tra i client (o usando un NAT) verrà selezionato per il supporto. Se i client si trovano entrambi nella rete del cliente, è necessario selezionare il percorso diretto. Questa operazione richiede la connettività UDP diretta all'interno della rete del cliente. Se i client sono utenti del cloud Nomad, a seconda del NAT/firewall, i supporti possono usare la connettività diretta.

Se un client è interno nella rete del cliente e un client è esterno (ad esempio, un utente del Cloud Mobile), è improbabile che la connettività diretta tra i candidati locali o riflessivi sia funzionante. In questo caso, un'opzione consiste nell'usare uno dei candidati di inoltro del trasporto da entrambi i client (ad esempio, il client interno ha ottenuto un candidato di inoltro dall'inoltro di trasporto in Azure; il client esterno deve essere in grado di inviare pacchetti STUN/RTP/RTCP all'inoltro di trasporto). Un'altra opzione è il client interno che invia al candidato di inoltro ottenuto dal client Cloud Mobile. Sebbene sia consigliabile usare la connettività UDP per i supporti, è possibile usare il protocollo TCP.

**Passaggi generali:**
1.  Il servizio di comunicazione utente A risolve il nome di dominio dell'URL (DNS) usando Flow 2.
2.  L'utente A alloca una porta di inoltro multimediale nell'inoltro del trasporto dei team usando Flow 4.
3.  L'utente A di servizi di comunicazione A invia un "invito" con i candidati ICE che usano Flow 4 ai servizi di comunicazione.
4.  I servizi di comunicazione notificano all'utente B l'uso di Flow 4.
5.  L'utente B alloca una porta di inoltro multimediale nell'inoltro del trasporto dei team usando Flow 4.
6.  L'utente B invia "answer" con i candidati ICE usando Flow 4, che viene inoltrato nuovamente all'utente A usando Flow 4.
7.  L'utente A e l'utente B richiamano i test di connettività ICE e il percorso multimediale migliore disponibile è selezionato (vedere diagrammi di seguito per diversi casi d'uso).
8.  Entrambi gli utenti inviano dati di telemetria ai servizi di comunicazione usando Flow 4.

### <a name="customer-network-intranet"></a>Rete clienti (Intranet)

:::image type="content" source="./media/call-flows/one-to-one-internal.png" alt-text="Flusso del traffico all'interno della rete del cliente.":::

*Figura 2-all'interno della rete clienti*

Nel passaggio 7 è selezionato il flusso multimediale peer-to-peer 5.

Questa trasmissione multimediale è bidirezionale. La direzione di Flow 5 indica che un lato avvia la comunicazione dal punto di vista della connettività. In questo caso, non importa quale direzione viene usata perché entrambi gli endpoint si trovano all'interno della rete del cliente.

### <a name="customer-network-to-external-user-media-relayed-by-teams-transport-relay"></a>Da rete cliente a utente esterno (supporto inoltrato dall'inoltro del trasporto dei team)

:::image type="content" source="./media/call-flows/one-to-one-via-relay.png" alt-text="Un flusso di chiamate da uno a uno tramite un inoltro.":::

*Figura 3-uso da rete cliente a utente esterno (supporto inoltrato dall'inoltro di trasporto di Azure)*

Nel passaggio 7, Flow 4 (dalla rete del cliente ai servizi di comunicazione) e Flow 3 (da un utente remoto di servizi di comunicazione mobile ad Azure Communication Services) sono selezionati.

Questi flussi vengono inoltrati dall'inoltro del trasporto dei team in Azure.

Questa trasmissione multimediale è bidirezionale. La direzione indica quale lato avvia la comunicazione dal punto di vista della connettività. In questo caso, questi flussi vengono usati per la segnalazione e i supporti, usando diversi protocolli di trasporto e indirizzi.

### <a name="customer-network-to-external-user-direct-media"></a>Da rete cliente a utente esterno (supporto diretto)

:::image type="content" source="./media/call-flows/one-to-one-with-extenal.png" alt-text="Un flusso di chiamate uno a uno con un utente esterno.":::

*Figura 4-cliente da rete a utente esterno (supporto diretto)*

Nel passaggio 7 è selezionato il flusso 2 (da rete cliente al peer del client su Internet).

Il supporto diretto con un utente mobile remoto (non inoltrato tramite Azure) è facoltativo. In altre parole, è possibile bloccare questo percorso per applicare un percorso multimediale tramite un inoltro di trasporto in Azure.

Questa trasmissione multimediale è bidirezionale. La direzione di flusso 2 a utente remoto mobile indica che un lato avvia la comunicazione dal punto di vista della connettività.

### <a name="vpn-user-to-internal-user-media-relayed-by-teams-transport-relay"></a>Utente VPN per l'utente interno (supporto inoltrato dall'inoltro del trasporto dei team)

:::image type="content" source="./media/call-flows/vpn-to-internal-via-relay.png" alt-text="Un flusso di chiamate uno a uno con un utente VPN tramite inoltro.":::

*Figura 5-utente VPN per l'utente interno (supporto inoltrato dal relè di Azure*

La segnalazione tra la VPN e la rete del cliente usa Flow 2 *. Segnalazione tra la rete del cliente e Azure usa Flow 4. Tuttavia, il supporto ignora la VPN e viene instradato usando i flussi 3 e 4 tramite l'inoltro multimediale di Azure.

### <a name="vpn-user-to-internal-user-direct-media"></a>Da utente VPN a utente interno (supporto diretto)

:::image type="content" source="./media/call-flows/vpn-to-internal-direct-media.png" alt-text="Un flusso di chiamate uno-a-uno con una VPN con supporto diretto":::

*Figura 6-utente VPN per l'utente interno (supporto diretto)*

La segnalazione tra la VPN e la rete del cliente usa il flusso 2. La segnalazione tra la rete del cliente e Azure usa Flow 4. Tuttavia, il supporto ignora la VPN e viene instradato usando il flusso 2 dalla rete del cliente a Internet.

Questa trasmissione multimediale è bidirezionale. La direzione di Flow 2 all'utente mobile remoto indica che un lato avvia la comunicazione da una prospettiva di connettività.

### <a name="vpn-user-to-external-user-direct-media"></a>Utente VPN per utente esterno (supporto diretto)

:::image type="content" source="./media/call-flows/vpn-user-to-external-user.png" alt-text="Un flusso di chiamate uno-a-uno con una VPN con supporto diretto":::

*Figura 7-utente VPN a utente esterno (supporto diretto)*

La segnalazione tra l'utente VPN e la rete del cliente usa Flow 2 * e Flow 4 in Azure. Tuttavia, media Ignora la VPN e viene instradata usando Flow 6.

Questa trasmissione multimediale è bidirezionale. La direzione di Flow 6 per l'utente mobile remoto indica che un lato avvia la comunicazione dal punto di vista della connettività.

### <a name="use-case-communication-services-client-to-pstn-through-communication-services-trunk"></a>Caso d'uso: Servizi di comunicazione da client a PSTN tramite servizi di comunicazione trunk

Servizi di comunicazione consente l'inserimento e la ricezione di chiamate dalla rete PSTN (Public Switched Telephone Network). Se il trunk PSTN è connesso usando i numeri di telefono forniti da servizi di comunicazione, non sono previsti requisiti di connettività speciali per questo caso d'uso. Se si vuole connettere il trunk PSTN locale ai servizi di comunicazione di Azure, è possibile usare l'interfaccia SIP (disponibile in CY2021).

:::image type="content" source="./media/call-flows/acs-to-pstn.png" alt-text="Una chiamata a una chiamata con un partecipante PSTN":::

*Figura 8: comunicazione tra l'utente e il servizio PSTN tramite Azure trunk*

In questo caso, la segnalazione e i supporti dalla rete del cliente ad Azure usano Flow 4.

### <a name="use-case-communication-services-group-calls"></a>Caso d'uso: chiamate al gruppo di servizi di comunicazione

Il servizio audio/video/condivisione schermo (VBSS) fa parte dei servizi di comunicazione di Azure. Dispone di un indirizzo IP pubblico che deve essere raggiungibile dalla rete del cliente e deve essere raggiungibile da un client di cloud nomadi. Ogni client/endpoint deve essere in grado di connettersi al servizio.

I client interni otterranno candidati locali, riflessivi e di inoltro in modo analogo a quanto descritto per le chiamate uno-a-uno. I client invieranno questi candidati al servizio in un invito. Il servizio non utilizza un inoltro poiché dispone di un indirizzo IP raggiungibile pubblicamente, pertanto risponde con l'indirizzo IP locale candidato. Il client e il servizio controlleranno la connettività nello stesso modo descritto per le chiamate uno-a-uno.

:::image type="content" source="./media/call-flows/acs-group-calls.png" alt-text="Chiamata al gruppo OACS":::

*Figura 9: chiamate di gruppi di servizi di comunicazione*

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione alle chiamate](../quickstarts/voice-video-calling/getting-started-with-calling.md)

I documenti seguenti possono essere interessanti:

- Altre informazioni sui [tipi di chiamate](../concepts/voice-video-calling/about-call-types.md)
- Informazioni sull'[architettura client-server](./client-and-server-architecture.md)

