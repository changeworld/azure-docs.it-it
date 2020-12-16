---
title: 'Azure ExpressRoute: routing asimmetrico'
description: Questo articolo illustra i problemi che si possono riscontrare con il routing asimmetrico in una rete che ha più collegamenti a una destinazione.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 0713c52c7f07db93d9ae9084062ef2c3b25a9074
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560509"
---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>Routing asimmetrico con più percorsi di rete
Questo articolo illustra il modo in cui il traffico di rete può prendere percorsi diversi quando sono disponibili più route tra l'origine e la destinazione di rete.

Esistono due concetti che è necessario conoscere per comprendere il routing asimmetrico. Il primo è l'effetto di più percorsi di rete. L'altro è il modo in cui i dispositivi, ad esempio un firewall mantengono lo stato. Questi tipi di dispositivi sono detti dispositivi con stato. Quando questi due fattori vengono combinati, possono creare uno scenario in cui il traffico di rete viene eliminato dal dispositivo con stato.  Il traffico viene eliminato perché non rileva che il traffico ha avuto origine da se stesso.

## <a name="multiple-network-paths"></a>Più percorsi di rete
Quando una rete aziendale dispone di un solo collegamento a Internet tramite un provider di servizi Internet, tutto il traffico da e verso Internet viaggia nello stesso percorso. Spesso le aziende acquistano più circuiti per creare percorsi ridondanti per migliorare i tempi di esecuzione della rete. Con questo tipo di configurazione è possibile che il traffico esca da un collegamento a Internet e venga restituito tramite un collegamento diverso. Questo scenario è comunemente noto come routing asimmetrico. Nel routing asimmetrico, il traffico di rete restituito accetta un percorso diverso rispetto al flusso originale in uscita.

![Rete con più percorsi](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Sebbene il routing asimmetrico venga in genere eseguito quando si passa a Internet. Si verifica anche quando viene introdotta una combinazione di più percorsi. Il primo esempio è quando si dispone di un percorso Internet e di un percorso privato che passa alla stessa destinazione. Il secondo esempio è quando si hanno più percorsi privati che passano anche alla stessa destinazione.

Ogni router lungo il percorso tra l'origine e la destinazione calcolerà il percorso migliore da eseguire per raggiungere la destinazione. Il router determina il migliore percorso possibile in base a due fattori principali:

* Il routing tra le reti esterne è basato sul protocollo di routing, ovvero il protocollo BGP (Border Gateway Protocol). Il protocollo BGP riceve annunci dai vicini e li sottopone ad alcune procedure per determinare il percorso migliore per la destinazione specificata, quindi archivia il percorso migliore nella tabella di routing.
* La lunghezza di una subnet mask associata a una route influenza i percorsi di routing. Se un router riceve più annunci per lo stesso indirizzo IP, il router selezionerà il percorso con la subnet mask più lunga perché viene considerata una route più specifica.

## <a name="stateful-devices"></a>Dispositivi con stato
I router esaminano l'intestazione di un pacchetto IP per il routing. Alcuni dispositivi analizzano i pacchetti in modo più approfondito. In genere, questi dispositivi esaminano le intestazioni Layer 4-Transmission Control Protocol (TCP) o User Datagram Protocol (UDP) o persino Layer 7 (livello applicazione). Si tratta di dispositivi di sicurezza o di dispositivi di ottimizzazione della larghezza di banda. 

Un esempio comune di dispositivo con stato è rappresentato dal firewall. Un firewall consente o rifiuta i pacchetti per passare attraverso le interfacce basate su diversi criteri. Questi criteri includono ma non sono limitati a protocollo, porta TCP/UDP e intestazioni URL. Questo livello di ispezione dei pacchetti può comportare un carico di elaborazione elevato sul dispositivo. 

Per migliorare le prestazioni, il firewall controlla il primo pacchetto di un flusso. Se consente al pacchetto di passare attraverso le relative interfacce, mantiene le informazioni sul flusso nella tabella di stato. Tutti i pacchetti successivi correlati a questo flusso vengono quindi consentiti in base alla determinazione iniziale. Un pacchetto che fa parte di un flusso esistente può arrivare al firewall di cui non ha origine. Poiché non dispone di informazioni di stato precedenti sul flusso iniziale, il firewall elimina il pacchetto.

## <a name="asymmetric-routing-with-expressroute"></a>Routing asimmetrico con ExpressRoute
Quando ci si connette a Microsoft tramite Azure ExpressRoute, la rete subisce le modifiche seguenti:

* Sono presenti più collegamenti a Microsoft. Un collegamento è la connessione Internet esistente e l'altra tramite la connessione ExpressRoute. Determinati traffici destinati a Microsoft potrebbero passare attraverso la connessione Internet, ma tornare alla connessione ExpressRoute. Lo stesso si può verificare anche quando il traffico passa attraverso ExpressRoute ma restituisce il percorso Internet.
* Sono stati ricevuti indirizzi IP più specifici dal circuito ExpressRoute. Quindi, quando il traffico dalla rete passa a Microsoft per i servizi offerti tramite ExpressRoute, i router preferiranno sempre la connessione ExpressRoute.

Per comprendere l'effetto del modo in cui queste due modifiche sono presenti in una rete, si considerino alcuni scenari. Ad esempio, si dispone di un circuito per Internet e si utilizzano tutti i servizi Microsoft tramite Internet. Il traffico proveniente dalla rete da e verso Microsoft attraversa lo stesso collegamento Internet e passa attraverso un firewall. Il firewall registra il flusso quando rileva il primo pacchetto. Tutti i pacchetti successivi di tale conversazione sono consentiti perché il flusso è presente nella tabella di stato.

![Routing asimmetrico con ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

Si apre quindi un circuito ExpressRoute per l'utilizzo dei servizi offerti da Microsoft attraverso ExpressRoute. Tutti gli altri servizi di Microsoft vengono utilizzati tramite Internet. Si distribuisce un firewall separato presso la rete perimetrale connessa alla connessione ExpressRoute. Microsoft annuncia prefissi più specifici alla rete tramite ExpressRoute per determinati servizi. Per tali prefissi l'infrastruttura di routing sceglie ExpressRoute come percorso preferito. 

Se non si annunciano gli indirizzi IP pubblici a Microsoft tramite ExpressRoute. Microsoft comunicherà con gli indirizzi IP pubblici tramite Internet. Il traffico inviato dalla rete a Microsoft usa la connessione ExpressRoute, ma il traffico di ritorno da Microsoft utilizzerà il percorso Internet. Quando il firewall sul perimetro rileva un pacchetto di risposta per un flusso di cui non è a conoscenza, eliminerà questi pacchetti.

Se si sceglie di annunciare lo stesso pool di Network Address Translation (NAT) per ExpressRoute e Internet. Si noteranno problemi simili con i client nella rete in indirizzi IP privati. Le richieste di servizi come Windows Update passeranno attraverso Internet perché gli indirizzi IP per questi servizi non vengono annunciati tramite ExpressRoute. Tuttavia, il traffico di ritorno viene restituito tramite ExpressRoute. Poiché Microsoft ha ricevuto un indirizzo IP con lo stesso subnet mask da Internet e ExpressRoute, il percorso preferito è sempre ExpressRoute. Se un firewall o un altro dispositivo con stato sul bordo della rete che si trova nella connessione ExpressRoute non ha informazioni precedenti su un flusso, eliminerà i pacchetti.

## <a name="asymmetric-routing-solutions"></a>Soluzioni per il routing asimmetrico
Sono disponibili due opzioni disponibili per risolvere il problema del routing asimmetrico. Il primo consiste nel routing e il secondo consiste nell'usare un NAT basato su origine (SNAT).

### <a name="routing"></a>Routing.
Assicurarsi che gli indirizzi IP pubblici siano annunciati ai collegamenti Wide Area Network (WAN) appropriati. Ad esempio, se si vuole usare Internet per il traffico di autenticazione e ExpressRoute per il traffico di posta elettronica. Non pubblicizzare gli indirizzi IP pubblici Active Directory Federation Services (AD FS) tramite ExpressRoute. Assicurarsi anche di non esporre il server AD FS locale agli indirizzi IP ricevuti dal router tramite ExpressRoute. Le route ricevute tramite ExpressRoute sono più specifiche. Questo fa di ExpressRoute il percorso preferito per il traffico di autenticazione diretto a Microsoft, Se non si presta attenzione al modo in cui il routing viene eseguito nei problemi di routing di rete asimmetrica possono verificarsi dei problemi.

Se si vuole usare ExpressRoute per l'autenticazione, assicurarsi di annunciare AD FS indirizzi IP pubblici tramite ExpressRoute senza NAT. Quando è configurato in questo modo, il traffico proveniente da Microsoft passa al server di AD FS locale passerà a ExpressRoute. Il traffico di ritorno dalla rete che passa a Microsoft userà ExpressRoute perché è la route preferita su Internet.

### <a name="source-based-nat"></a>Source NAT
Un altro modo per risolvere il problema di routing asimmetrico consiste nell'usare SNAT. Ad esempio, si sceglie di non annunciare l'indirizzo IP pubblico di un server di Simple Mail Transfer Protocol locale (SMTP) su ExpressRoute. Si prevede invece di usare Internet per questo tipo di comunicazione. Una richiesta proveniente da Microsoft che viene inviata al server SMTP locale attraversa Internet. Tramite SNAT la richiesta in ingresso viene inviata a un indirizzo IP interno. Il traffico di ritorno dal server SMTP passerà al firewall perimetrale (usato per NAT) invece che tramite ExpressRoute. Come risultato, il traffico di ritorno prenderà il percorso Internet.

![Configurazione della rete con Source NAT](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>Rilevamento del routing asimmetrico
Il modo migliore per assicurarsi che il traffico attraversi il percorso previsto è il comando traceroute. Se si prevede che il traffico dal server SMTP locale a Microsoft riceva il percorso Internet, il traceroute previsto viene dal server SMTP a Microsoft 365. Il risultato convalida che il traffico stia effettivamente lasciando la rete verso Internet e non verso ExpressRoute.

