---
title: Preparare la rete dell'organizzazione per i servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Informazioni sui requisiti di rete per la chiamata vocale e video di servizi di comunicazione di Azure
author: nmurav
manager: jken
services: azure-communication-services
ms.author: nmurav
ms.date: 3/23/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 753bb7a88f8032d6ed9aeac1b1adf4f34d7cec43
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105051351"
---
# <a name="ensure-high-quality-media-in-azure-communication-services"></a>Verifica i contenuti multimediali di alta qualità nei servizi di comunicazione di Azure

Questo documento fornisce una panoramica dei fattori e delle procedure consigliate da prendere in considerazione durante la creazione di esperienze di comunicazione multimediale di alta qualità con i servizi di comunicazione di Azure.

## <a name="factors-that-affect-media-quality-and-reliability"></a>Fattori che influiscono sulla qualità e l'affidabilità dei supporti

Esistono molti fattori diversi che contribuiscono alla qualità del supporto in tempo reale di servizi di comunicazione di Azure (audio, video e condivisione di applicazioni). Sono incluse la qualità della rete, la larghezza di banda, il firewall, l'host e le configurazioni dei dispositivi.


### <a name="network-quality"></a>Qualità della rete

La qualità dei supporti in tempo reale su IP è significativamente influenzata dalla qualità della connettività di rete sottostante, ma soprattutto dalla quantità di:
* **Latenza**. Questo è il tempo necessario per ottenere un pacchetto IP dal punto A al punto B sulla rete. Questo ritardo di propagazione della rete è determinato dalla distanza fisica tra i due punti e da eventuali overhead aggiuntivi causati dai dispositivi che passano attraverso il traffico. La latenza viene misurata come tempo unidirezionale o round trip (RTT).
* **Perdita di pacchetti**. Percentuale di pacchetti persi in un determinato intervallo di tempo. La perdita di pacchetti influiscono direttamente sulla qualità audio, da piccoli pacchetti persi singoli che non hanno quasi alcun impatto, fino a perdite di picchi di back-to-back che provocano il troncamento dell'audio completo.
* **Jitter dell'arrivo tra pacchetti o semplicemente jitter**. Si tratta della variazione media del ritardo tra i pacchetti successivi. I servizi di comunicazione di Azure possono adattarsi ad alcuni livelli di jitter tramite il buffering. Si tratta solo quando la jitter supera il buffering che un partecipante ne noterà gli effetti.

### <a name="network-bandwidth"></a>Larghezza di banda di rete

Assicurarsi che la rete sia configurata in modo da supportare la larghezza di banda richiesta dalle sessioni multimediali di servizi di comunicazione Azure simultanee e da altre applicazioni aziendali. Il test del percorso di rete end-to-end per i colli di bottiglia della larghezza di banda è fondamentale per la corretta distribuzione della soluzione Servizi di comunicazione multimediale.

Di seguito sono riportati i requisiti di larghezza di banda per le librerie client JavaScript:

|Larghezza di banda|Scenari|
|:--|:--|
|40 Kbps|Chiamata audio peer-to-peer|
|500 Kbps|Chiamata audio peer-to-peer e condivisione dello schermo|
|500 Kbps|Video di qualità peer-to-peer che chiama 360p a 30 fps|
|1,2 Mbps|Video di qualità HD peer-to-peer che chiama con risoluzione di HD 720p a 30 fps|
|500 Kbps|Gruppo di video che chiama 360p a 30 fps|
|1,2 Mbps|Video di gruppo HD che chiama con risoluzione di HD 720p a 30 fps| 

Di seguito sono riportati i requisiti di larghezza di banda per le librerie client native di Android e iOS:

|Larghezza di banda|Scenari|
|:--|:--|
|30 Kbps|Chiamata audio peer-to-peer |
|130 Kbps|Chiamata audio peer-to-peer e condivisione dello schermo|
|500 Kbps|Video di qualità peer-to-peer che chiama 360p a 30 fps|
|1,2 Mbps|Video di qualità HD peer-to-peer che chiama con risoluzione di HD 720p a 30 fps|
|1,5 Mbps|Video di qualità HD peer-to-peer che chiama con risoluzione HD 1080p a 30 fps |
|500kbps/1 Mbps|Chiamata del video di gruppo|
|1 Mbps/2Mbps|Chiamata video del gruppo HD (video 540P nella schermata 1080p)|

### <a name="firewalls-configuration"></a>Configurazione di firewall/i

Le connessioni ai servizi di comunicazione di Azure richiedono la connettività Internet a porte e indirizzi IP specifici per offrire esperienze multimediali di alta qualità. Senza l'accesso a queste porte e indirizzi IP, i servizi di comunicazione di Azure possono continuare a funzionare. Tuttavia, l'esperienza ottimale viene fornita quando le porte e gli intervalli IP consigliati sono aperti.

| Category | Intervalli IP o FQDN | Porte | 
| :-- | :-- | :-- |
| Traffico multimediale | [Intervallo di indirizzi IP del cloud pubblico di Azure](https://www.microsoft.com/download/confirmation.aspx?id=56519) | Da UDP 3478 a 3481, porte TCP 443 |
| Segnalazione, telemetria, registrazione| *. skype.com, *. microsoft.com, *. azure.net, *. azureedge.net, *. office.com, *. trouter.io | TCP 443, 80 |

### <a name="network-optimization"></a>Ottimizzazione della rete

Le attività seguenti sono facoltative e non sono necessarie per implementare i servizi di comunicazione di Azure. Usare queste linee guida per ottimizzare le prestazioni della rete e dei servizi di comunicazione di Azure o se si conoscono alcune limitazioni di rete.
Potrebbe essere necessario ottimizzare ulteriormente se:
* I servizi di comunicazione di Azure vengono eseguiti lentamente (probabilmente la larghezza di banda è insufficiente)
* Le chiamate continuano a essere eliminate (potrebbero essere dovute a firewall o a blocchi proxy)
* Le chiamate sono statiche e tagliate oppure le voci sembrano i robot (potrebbe essere un jitter o una perdita di pacchetti)

| Attività di ottimizzazione della rete | Dettagli |
| :-- | :-- |
| Pianificare la rete | In questa documentazione è possibile trovare i requisiti minimi per la rete per le chiamate. Vedere l' [esempio teams per la pianificazione della rete](https://docs.microsoft.com/microsoftteams/tutorial-network-planner-example) |
| Risoluzione dei nomi esterna | Assicurarsi che tutti i computer in cui sono in esecuzione le librerie client di servizi di comunicazione Azure possano risolvere le query DNS esterne per individuare i servizi forniti dai servizi di comunicazione di Azure e che i firewall non impediscano l'accesso. Assicurarsi che le librerie client siano in grado di risolvere gli indirizzi *. skype.com, *. microsoft.com, *. azure.net, *. azureedge.net, *. office.com, *. trouter.io  |
| Mantieni persistenza sessione | Verificare che il firewall non modifichi le porte o gli indirizzi NAT (Network Address Translation) mappati per UDP
Convalida dimensioni pool NAT | Convalidare le dimensioni del pool di Network Address Translation (NAT) necessarie per la connettività utente. Quando più utenti e dispositivi accedono ai servizi di comunicazione di Azure tramite [NAT (Network Address Translation) o Pat (Port Address](https://docs.microsoft.com/office365/enterprise/nat-support-with-office-365)Translation), assicurarsi che i dispositivi nascosti dietro ogni indirizzo IP instradabile pubblicamente non superino il numero supportato. Assicurarsi che ai pool NAT siano assegnati indirizzi IP pubblici adeguati per evitare l'esaurimento delle porte. L'esaurimento delle porte contribuirà a utenti e dispositivi interni che non sono in grado di connettersi ai servizi di comunicazione di Azure |
| Guida al rilevamento e alla prevenzione delle intrusioni | Se l'ambiente dispone di un sistema di rilevamento o di prevenzione delle [intrusioni](https://docs.microsoft.com/azure/network-watcher/network-watcher-intrusion-detection-open-source-tools) (IDS/IPS) distribuito per un ulteriore livello di sicurezza per le connessioni in uscita, consentire tutti gli URL dei servizi di comunicazione di Azure |
| Configurare la VPN split tunnel | Si consiglia di specificare un percorso alternativo per il traffico dei team che ignora la rete privata virtuale (VPN), comunemente nota come [VPN split tunneling](https://docs.microsoft.com/windows/security/identity-protection/vpn/vpn-routing). Lo split tunneling significa che il traffico per i servizi di comunicazione di Azure non passa attraverso la VPN ma passa direttamente ad Azure. Il bypass della VPN avrà un impatto positivo sulla qualità dei supporti e riduce il carico dai dispositivi VPN e dalla rete dell'organizzazione. Per implementare una VPN con split tunneling, collaborare con il fornitore della VPN. Altri motivi per cui è consigliabile ignorare la VPN: <ul><li> In genere, le VPN non sono progettate o configurate per supportare supporti in tempo reale.</li><li> Le VPN potrebbero anche non supportare UDP (richiesta per i servizi di comunicazione di Azure)</li><li>Le VPN introducono anche un ulteriore livello di crittografia sopra il traffico multimediale già crittografato.</li><li>La connettività ai servizi di comunicazione di Azure potrebbe non essere efficiente a causa del traffico con blocco dei capelli attraverso un dispositivo VPN.</li></ul>|
| Implementare QoS | [Usare la qualità del servizio (QoS)](https://docs.microsoft.com/microsoftteams/qos-in-teams) per configurare la definizione delle priorità dei pacchetti. Questo consente di migliorare la qualità delle chiamate e di monitorare e risolvere i problemi relativi alla qualità delle chiamate. QoS deve essere implementato in tutti i segmenti di una rete gestita. Anche quando è stato effettuato il provisioning di una rete in modo adeguato per la larghezza di banda, QoS offre una mitigazione dei rischi in caso di eventi di rete non previsti. Con QoS, il traffico vocale è classificato in ordine di priorità in modo che questi eventi imprevisti non influiscano negativamente sulla qualità. | 
| Ottimizza WiFi | Analogamente alla VPN, le reti Wi-Fi non sono necessariamente progettate o configurate per supportare supporti in tempo reale. La pianificazione o l'ottimizzazione di una rete Wi-Fi per supportare i servizi di comunicazione di Azure è una considerazione importante per una distribuzione di alta qualità. Tenere presente questi fattori: <ul><li>Implementare QoS o Wi-Fi (WMM) per garantire che il traffico multimediale venga classificato in modo appropriato nelle reti Wi-Fi.</li><li>Pianificare e ottimizzare le bande Wi-Fi e il posizionamento dei punti di accesso. L'intervallo di 2,4 GHz può offrire un'esperienza adeguata a seconda del posizionamento dei punti di accesso, ma i punti di accesso sono spesso interessati da altri dispositivi consumer che operano in tale intervallo. L'intervallo di 5 GHz è più adatto per i supporti in tempo reale a causa dell'intervallo denso, ma richiede più punti di accesso per ottenere una copertura sufficiente. Gli endpoint devono inoltre supportare tale intervallo ed essere configurati per sfruttare tali bande di conseguenza.</li><li>Se si usano reti Wi-Fi dual-band, prendere in considerazione la possibilità di implementare lo sterzo della banda. Lo sterzo a gruppi è una tecnica implementata dai fornitori di Wi-Fi per influenzare i client dual-band in modo da usare l'intervallo di 5 GHz.</li><li>Quando i punti di accesso dello stesso canale sono troppo vicini, possono causare una sovrapposizione dei segnali e una concorrenza involontaria, causando un'esperienza utente degradata. Assicurarsi che i punti di accesso che si trovano accanto all'altro si trovino su canali che non si sovrappongono.</li></ul> Ogni fornitore senza fili ha i propri consigli per la distribuzione della soluzione wireless. Per indicazioni specifiche, consultare il fornitore del sistema Wi-Fi.|



### <a name="operating-system-and-browsers-for-javascript-client-libraries"></a>Sistema operativo e browser (per le librerie client JavaScript)

Le librerie client Voice/video di servizi di comunicazione di Azure supportano alcuni sistemi operativi e browser.
Informazioni sui sistemi operativi e i browser supportati dalle librerie client chiamante nella [documentazione concettuale chiamante](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features).

## <a name="next-steps"></a>Passaggi successivi

I documenti seguenti possono essere interessanti:

- Altre informazioni sulle [librerie di chiamata](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features)
- Informazioni sull'[architettura client-server](https://docs.microsoft.com/azure/communication-services/concepts/client-and-server-architecture)
- Informazioni sulle [topologie del flusso di chiamate](https://docs.microsoft.com/azure/communication-services/concepts/call-flows)
