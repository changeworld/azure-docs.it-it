---
title: Flussi di chiamate in Servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Informazioni sui flussi di chiamate in Servizi di comunicazione di Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 8f7bfd63d858fb409286268c318c9f66474e3d53
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110917"
---
# <a name="call-flow-basics"></a>Nozioni di base sul flusso di chiamate

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

La sezione seguente offre una panoramica dei flussi di chiamate in Servizi di comunicazione di Azure. I flussi multimediali e di segnalazione dipendono dai tipi di chiamate effettuate dagli utenti. Esempi di tipi di chiamate sono le chiamate VoIP uno-a-uno, PSTN uno-a-uno e di gruppo che contengono una combinazione di partecipanti VoIP e connessi a PSTN. Esaminare i [tipi di chiamate](./voice-video-calling/about-call-types.md).

## <a name="about-signaling-and-media-protocols"></a>Informazioni sui protocolli multimediali e di segnalazione

Quando si stabilisce una chiamata peer-to-peer o di gruppo, vengono usati due protocolli: HTTP (REST) per la segnalazione e SRTP per i file multimediali.

La segnalazione tra gli SDK o tra SDK e i controller di segnalazione dei servizi di comunicazione viene gestita con il protocollo HTTP REST (TLS). Per il traffico multimediale in tempo reale (RTP), è preferibile il protocollo UDP (User Datagram Protocol). Se l'uso di UDP è impedito dal firewall, l'SDK userà il Transmission Control Protocol (TCP) per i supporti.

Di seguito vengono descritti i protocolli multimediali e di segnalazione in diversi scenari.

## <a name="call-flow-cases"></a>Casi di flussi di chiamate

### <a name="case-1-voip-where-a-direct-connection-between-two-devices-is-possible"></a>Caso 1: VoIP in cui è possibile una connessione diretta tra due dispositivi

Nelle chiamate video o VoIP uno-a-uno, il traffico preferisce il percorso più diretto. "Percorso diretto" significa che se due SDK possono raggiungere l'uno all'altro direttamente, stabiliranno una connessione diretta. Questo è in genere possibile quando due SDK si trovano nella stessa subnet (ad esempio, in una subnet 192.168.1.0/24) o due quando i dispositivi si trovano in subnet che possono vedersi reciprocamente (gli SDK nella subnet 10.10.0.0/16 e 192.168.1.0/24 possono raggiungere l'uno dall'altro).

:::image type="content" source="./media/call-flows/about-voice-case-1.png" alt-text="Diagramma che illustra una chiamata VoIP diretta tra gli utenti e Servizi di comunicazione.":::

### <a name="case-2-voip-where-a-direct-connection-between-devices-is-not-possible-but-where-connection-between-nat-devices-is-possible"></a>Caso 2: VoIP in cui non è possibile una connessione diretta tra i dispositivi, ma è possibile una connessione tra dispositivi NAT

Se due dispositivi si trovano in subnet che non possono raggiungere reciprocamente (ad esempio, Alice lavora da un bar e Bob lavora dal proprio ufficio), ma la connessione tra i dispositivi NAT è possibile, gli SDK lato client stabiliranno la connettività tramite dispositivi NAT.

Per Giorgia, sarà il NAT del bar e per Davide sarà il NAT dell'ufficio. Il dispositivo di Giorgia invierà l'indirizzo esterno del NAT e il dispositivo di Davide eseguirà la stessa operazione. Gli SDK apprendere gli indirizzi esterni da un servizio STUN (utilità di attraversamento della sessione per NAT) che i servizi di comunicazione di Azure forniscono gratuitamente. La logica che gestisce l'handshake tra Alice e Bob è incorporata all'interno degli SDK forniti da servizi di comunicazione di Azure. Non è necessaria alcuna configurazione aggiuntiva.

:::image type="content" source="./media/call-flows/about-voice-case-2.png" alt-text="Diagramma che illustra una chiamata VoIP che usa una connessione STUN.":::

### <a name="case-3-voip-where-neither-a-direct-nor-nat-connection-is-possible"></a>Caso 3: VoIP in cui non è possibile una connessione diretta né NAT

Se uno o entrambi i dispositivi client sono protetti da un NAT simmetrico, è necessario un servizio cloud separato per inoltrare il supporto tra i due SDK. Questo servizio viene chiamato TURN (Traversal Using Relays around NAT) e viene fornito da Servizi di comunicazione. I servizi di comunicazione che chiamano SDK utilizzano automaticamente TURN Services in base alle condizioni di rete rilevate. L'uso del servizio TURN di Microsoft viene addebitato separatamente.

:::image type="content" source="./media/call-flows/about-voice-case-3.png" alt-text="Diagramma che illustra una chiamata VoIP che usa una connessione TURN.":::

### <a name="case-4-group-calls-with-pstn"></a>Caso 4: Chiamate di gruppo con PSTN

Sia la segnalazione che i file multimediali per le chiamate PSTN usano la risorsa di telefonia di Servizi di comunicazione di Azure. Questa risorsa è interconnessa con altri gestori telefonici.

Il traffico multimediale PSTN attraversa un componente denominato processore di contenuti multimediali.

:::image type="content" source="./media/call-flows/about-voice-pstn.png" alt-text="Diagramma che illustra una chiamata di gruppo PSTN con Servizi di comunicazione.":::

> [!NOTE]
> Per coloro che hanno familiarità con l'elaborazione di contenuti multimediali, il processore di contenuti multimediali è anche un agente utente back-to-back, come definito in [RFC 3261 SIP: Session Initiation Protocol](https://tools.ietf.org/html/rfc3261), ovvero può tradurre i codec quando si gestiscono le chiamate tra le reti Microsoft e i gestori di rete. Il controller di segnalazione di Servizi di comunicazione di Azure è l'implementazione Microsoft di un proxy SIP per la stessa RFC.

Per le chiamate di gruppo, i file multimediali e le segnalazioni vengono sempre propagati tramite il back-end di Servizi di comunicazione di Azure. L'audio e/o il video di tutti i partecipanti risulta combinato nel componente del processore di contenuti multimediali. Tutti i membri di una chiamata di gruppo inviano i flussi audio e/o video al processore di contenuti multimediali, che restituisce flussi multimediali misti.

Il protocollo RTP (Real-Time Protocol) predefinito per le chiamate di gruppo è UDP (User Datagram Protocol).

> [!NOTE]
> Il processore di contenuti multimediali può fungere da microcontroller (MCU) o unità di trasmissione selettiva (SFU)

:::image type="content" source="./media/call-flows/about-voice-group-calls.png" alt-text="Diagramma che illustra il flusso del processo multimediale UDP in Servizi di comunicazione.":::

Se l'SDK non è in grado di utilizzare UDP per supporti a causa di restrizioni del firewall, verrà effettuato un tentativo di utilizzare il Transmission Control Protocol (TCP). Si noti che il componente del processore di contenuti multimediali richiede UDP, quindi, in questo caso, il servizio TURN di Servizi di comunicazione verrà aggiunto alla chiamata di gruppo per convertire TCP in UDP. In questo caso, i costi di TURN verranno addebitati, a meno che le funzionalità di TURN non vengano disabilitate manualmente.

:::image type="content" source="./media/call-flows/about-voice-group-calls-2.png" alt-text="Diagramma che illustra il flusso del processo multimediale TCP in Servizi di comunicazione.":::

### <a name="case-5-communication-services-sdk-and-microsoft-teams-in-a-scheduled-teams-meeting"></a>Caso 5: Communication Services SDK e Microsoft teams in un team pianificato riunione

Segnalazione dei flussi attraverso il controller di segnalazione. Flussi multimediali tramite il processore di contenuti multimediali. Il controller di segnalazione e il processore di contenuti multimediali sono condivisi tra i servizi di comunicazione e i team Microsoft.

:::image type="content" source="./media/call-flows/teams-communication-services-meeting.png" alt-text="Diagramma che illustra l'SDK di servizi di comunicazione e il client teams in una riunione di Team pianificati.":::



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione alle chiamate](../quickstarts/voice-video-calling/getting-started-with-calling.md)

I documenti seguenti possono essere interessanti:

- Altre informazioni sui [tipi di chiamate](../concepts/voice-video-calling/about-call-types.md)
- Informazioni sull'[architettura client-server](./client-and-server-architecture.md)
- Informazioni sulle [topologie del flusso di chiamate](./detailed-call-flows.md)
