---
title: Configurare la rete
description: Informazioni sull'architettura della soluzione, la preparazione della rete, i prerequisiti e altre informazioni necessarie per garantire la corretta configurazione della rete per l'uso con Azure Defender per le appliance delle cose.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/03/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: a71ea75eb603b141c4b28cff5f2b4aa957583bcd
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621313"
---
# <a name="about-azure-defender-for-iot-network-setup"></a>Informazioni sulla configurazione della rete di Azure Defender per IoT

Azure Defender per Internet delle cose offre monitoraggio delle minacce per ICS continuo e individuazione dei dispositivi. La piattaforma include i componenti seguenti:

**Difensore dei sensori Internet:** I sensori raccolgono il traffico di rete ICS usando il monitoraggio passivo (senza agenti). Passiva e non intrusiva, i sensori hanno un impatto negativo sulle prestazioni delle reti e dei dispositivi OT e Internet. Il sensore si connette a una porta SPAN o a un TAP di rete e inizia immediatamente a monitorare la rete. I rilevamenti vengono visualizzati nella console del sensore. Qui è possibile visualizzarli, analizzarli e analizzarli in una mappa di rete, un inventario dei dispositivi e una vasta gamma di report. Gli esempi includono report di valutazione dei rischi, query data mining e vettori di attacco. 

**Defender per la console di gestione locale**: la console di gestione locale fornisce una visualizzazione consolidata di tutti i dispositivi di rete. Offre una visualizzazione in tempo reale degli indicatori di rischio e degli avvisi di rischio per tutte le funzionalità. Strettamente integrato con i tuoi flussi di lavoro e playbook di SOC, consente di semplificare la definizione delle priorità delle attività di mitigazione e la correlazione tra siti delle minacce. 

**Defender per il portale di Internet delle cose:** L'applicazione Defender for Internet è utile per acquistare appliance della soluzione, installare e aggiornare il software e aggiornare i pacchetti TI. 

Questo articolo fornisce informazioni sull'architettura della soluzione, la preparazione della rete, i prerequisiti e altro ancora per consentirti di configurare correttamente la tua rete per lavorare con Defender per gli elettrodomestici. I lettori che lavorano con le informazioni contenute in questo articolo devono essere esperti nel funzionamento e nella gestione delle reti OT e Internet. Ad esempio, i tecnici di automazione, i responsabili di stabilimento, i provider di servizi di infrastruttura di rete, i team Cybersecurity, CISO o cio.

Per assistenza o supporto tecnico, contattare [supporto tecnico Microsoft](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="on-site-deployment-tasks"></a>Attività di distribuzione in sede

Le attività di distribuzione del sito includono:

- [Raccogli informazioni sul sito](#collect-site-information)

- [Preparare una workstation di configurazione](#prepare-a-configuration-workstation)

- [Pianificazione dell'installazione del rack](#planning-rack-installation)

### <a name="collect-site-information"></a>Raccogli informazioni sul sito

Registrare le informazioni sul sito, ad esempio:

- Informazioni sulla rete di gestione dei sensori.

- Architettura di rete del sito.

- Ambiente fisico.

- Integrazioni di sistema.

- Credenziali utente pianificate.

- Workstation di configurazione.

- Certificati SSL (facoltativo ma consigliato).

- Autenticazione SMTP (facoltativo). Per utilizzare il server SMTP con l'autenticazione di, preparare le credenziali necessarie per il server.

- Server DNS (facoltativo). Preparare l'IP e il nome host del server DNS.

Per un elenco dettagliato e una descrizione delle informazioni importanti sul sito, vedere [esempio di sito](#example-site-book).

#### <a name="successful-monitoring-guidelines"></a>Linee guida di monitoraggio completate

Per trovare la posizione ottimale per connettere l'appliance in ogni rete di produzione, è consigliabile seguire questa procedura:

:::image type="content" source="media/how-to-set-up-your-network/production-network-diagram.png" alt-text="Esempio di diagramma della configurazione della rete di produzione.":::

### <a name="prepare-a-configuration-workstation"></a>Preparare una workstation di configurazione

Preparare una workstation Windows, incluse le seguenti:

- Connettività all'interfaccia di gestione dei sensori.

- Un browser supportato

- Software terminal, ad esempio PuTTy.

Verificare che le regole del firewall necessarie siano aperte sulla workstation. Per informazioni dettagliate, vedere [requisiti di accesso alla rete](#network-access-requirements) .

#### <a name="supported-browsers"></a>Browser supportati

I browser seguenti sono supportati per i sensori e le applicazioni Web della console di gestione locale:

- Chrome 32 +

- Microsoft Edge 86 +

- Internet Explorer 10+

### <a name="network-access-requirements"></a>Requisiti di accesso alla rete

Verificare che i criteri di sicurezza dell'organizzazione consentano l'accesso agli elementi seguenti:

| Protocollo | Trasporto | Ingresso/Uscita | Porta | Usata | Scopo | Source (Sorgente) | Destination |
|--|--|--|--|--|--|--|--|
| HTTPS | TCP | IN/OUT | 443 | Console Web della console di gestione locale e del sensore | Accesso alla console Web | Client | Sensore e console di gestione locale |
| SSH | TCP | IN/OUT | 22 | CLI | Accesso all'interfaccia della riga di comando | Client | Sensore e console di gestione locale |
| SSL | TCP | IN/OUT | 443 | Sensore e console di gestione locale | Connessione tra la piattaforma CyberX e la piattaforma di gestione centrale | sensore | Console di gestione locale |
| NTP | UDP | IN | 123 | Sincronizzazione dell'ora | La console di gestione locale usa come NTP per il sensore | sensore | Console di gestione locale |
| NTP | UDP | IN/OUT | 123 | Sincronizzazione dell'ora | Sensore connesso al server NTP esterno, quando non è installata alcuna console di gestione locale | sensore | NTP |
| SMTP | TCP | OUT | 25 | E-mail | Connessione tra la piattaforma CyberX e la piattaforma di gestione e il server di posta elettronica | Sensore e console di gestione locale | Server di posta elettronica |
| syslog | UDP | OUT | 514 | LEEF | Log inviati dalla console di gestione locale al server syslog | Sensore e console di gestione locali | Server Syslog |
| DNS |  | IN/OUT | 53 | DNS | Porta server DNS | Sensore e console di gestione locali | Server DNS |
| LDAP | TCP | IN/OUT | 389 | Active Directory | La connessione tra la piattaforma CyberX e la piattaforma di gestione per la Active Directory | Sensore e console di gestione locali | Server LDAP |
| LDAPS | TCP | IN/OUT | 636 | Active Directory | La connessione tra la piattaforma CyberX e la piattaforma di gestione per la Active Directory | Sensore e console di gestione locali | Server LDAPs |
| SNMP | UDP | OUT | 161 | Monitoraggio | Agenti di raccolta SNMP remoti. | Sensore e console di gestione locali | Server SNMP |
| WMI | UDP | OUT | 135 | Monitoraggio | Monitoraggio degli endpoint di Windows | Sensore | Elemento di rete pertinente |
| Tunneling | TCP | IN | 9000 <br /><br />-sulla porta 443 <br /><br />Dall'utente finale alla console di gestione locale. <br /><br />-Porta 22 dal sensore alla console di gestione locale  | Monitoraggio | Tunneling | Sensore | Console di gestione locale |

### <a name="planning-rack-installation"></a>Pianificazione dell'installazione del rack

Per pianificare l'installazione del rack:

1. Preparare un monitor e una tastiera per le impostazioni di rete dell'appliance.

1. Allocare lo spazio del rack per l'appliance.

1. Disporre dell'alimentazione CA disponibile per l'appliance.
1. Preparare il cavo LAN per la connessione della gestione al Commuter di rete.
1. Preparare i cavi LAN per la connessione delle porte del commutatore (mirror) o dei rubinetti di rete al Defender per l'appliance. 
1. Configurare, connettere e convalidare le porte SPAN nelle opzioni con mirroring, come descritto nella sessione di revisione dell'architettura.
1. Connettere la porta di estensione configurata a un computer che esegue Wireshark e verificare che la porta sia configurata correttamente.
1. Aprire tutte le porte del firewall pertinenti.

## <a name="about-passive-network-monitoring"></a>Informazioni sul monitoraggio della rete passiva

Il dispositivo riceve il traffico da più origini, tramite switch di porte mirror (porte SPAN) o tramite rubinetti di rete. La porta di gestione è connessa alla rete aziendale, aziendale o di gestione dei sensori con connettività a una console di gestione locale o al portale Defender for Internet.

:::image type="content" source="media/how-to-set-up-your-network/switch-with-port-mirroring.png" alt-text="Diagramma di un Commuter gestito con mirroring delle porte.":::

### <a name="purdue-model"></a>Modello Purdue

Le sezioni seguenti descrivono i livelli di Purdue.

:::image type="content" source="media/how-to-set-up-your-network/purdue-model.png" alt-text="Diagramma del modello Purdue.":::

####  <a name="level-0-cell-and-area"></a>Livello 0: cella e area  

Il livello 0 è costituito da un'ampia gamma di sensori, attuatori e dispositivi necessari per il processo di produzione di base. Questi dispositivi eseguono le funzioni di base del sistema di automazione e controllo industriale, ad esempio:

- Guida di un motore.

- Misurazione di variabili.
- Impostazione di un output.
- Esecuzione di funzioni chiave, ad esempio disegno, saldatura e flessione.

#### <a name="level-1-process-control"></a>Livello 1: controllo del processo

Il livello 1 è costituito da controller incorporati che controllano e manipolano il processo di produzione la cui funzione principale consiste nel comunicare con i dispositivi di livello 0. Nella produzione discreta, questi dispositivi sono controller di logica programmabili (PLC) o unità di telemetria remota (RTU). Nella produzione dei processi il controller di base è denominato controller di dominio.

#### <a name="level-2-supervisory"></a>Livello 2: supervisione

Il livello 2 rappresenta i sistemi e le funzioni associati alla supervisione e al funzionamento del runtime di un'area di un impianto di produzione. In genere sono inclusi i seguenti: 

- Interfacce operatore o HMI

- Avvisi o sistemi di avvisi

- Sistema di gestione di batch e storici di processo

- Workstation della sala di controllo

Questi sistemi comunicano con i PLC e RTU di livello 1. In alcuni casi, comunicano o condividono dati con i sistemi e le applicazioni del sito o dell'azienda (livello 4 e 5). Questi sistemi sono basati principalmente su apparecchiature di elaborazione standard e sui sistemi operativi (UNIX o Microsoft Windows).

#### <a name="levels-3-and-35-site-level-and-industrial-perimeter-network"></a>Livelli 3 e 3,5: rete perimetrale a livello di sito e industriale

Il livello sito rappresenta il livello più elevato di sistemi di automazione e controllo industriale. I sistemi e le applicazioni esistenti a questo livello gestiscono le funzioni di controllo e automazione industriale a livello di sito. I livelli da 0 a 3 sono considerati fondamentali per le operazioni del sito. I sistemi e le funzioni esistenti a questo livello possono includere quanto segue:

- Creazione di report di produzione (ad esempio, tempi di ciclo, indice qualitativo, manutenzione predittiva)

- Impianto storico

- Pianificazione di produzione dettagliata

- Gestione delle operazioni a livello di sito

- Gestione di dispositivi e materiali

- Server di avvio patch

- File server

- Dominio industriale, Active Directory, Terminal Server

Questi sistemi comunicano con la zona di produzione e condividono i dati con i sistemi e le applicazioni Enterprise (livello 4 e 5).  

#### <a name="levels-4-and-5-business-and-enterprise-networks"></a>Livelli 4 e 5: reti aziendali ed aziendali

Il livello 4 e il livello 5 rappresentano il sito o la rete aziendale in cui si trovano le funzioni e i sistemi IT centralizzati. L'organizzazione IT gestisce direttamente i servizi, i sistemi e le applicazioni a questi livelli.

## <a name="planning-for-network-monitoring"></a>Pianificazione del monitoraggio della rete

Gli esempi seguenti presentano diversi tipi di topologie per le reti di controllo industriale, oltre a considerazioni per il monitoraggio e il posizionamento dei sensori ottimali.

### <a name="what-should-be-monitored"></a>Cosa è necessario monitorare?

Il traffico che passa attraverso i livelli 1 e 2 deve essere monitorato.

### <a name="what-should-the-defender-for-iot-appliance-connect-to"></a>A cosa serve il Defender per l'appliance Internet?

Il Defender per l'appliance per le cose deve connettersi ai commutatori gestiti che vedono le comunicazioni industriali tra i livelli 1 e 2 (in alcuni casi anche livello 3).

Il diagramma seguente è un'astrazione generale di una rete multi-tenant multitenant con un ampio ecosistema Cybersecurity in genere gestito da un SOC e MSSP.

In genere, i sensori NTA vengono distribuiti nei livelli da 0 a 3 del modello OSI.

:::image type="content" source="media/how-to-set-up-your-network/osi-model.png" alt-text="Diagramma del modello OSI.":::

#### <a name="example-ring-topology"></a>Esempio: topologia ad anello

La rete Ring è una topologia in cui ogni commutatore o nodo si connette esattamente a due altre opzioni, formando un singolo percorso continuo per il traffico.

:::image type="content" source="media/how-to-set-up-your-network/ring-topology.PNG" alt-text="Diagramma della topologia ad anello.":::

#### <a name="example-linear-bus-and-star-topology"></a>Esempio: topologia bus lineare e stella

In una rete a stella, ogni host è connesso a un hub centrale. Nella sua forma più semplice, un hub centrale funge da canale per la trasmissione dei messaggi. Nell'esempio seguente, i commutatori inferiori non vengono monitorati e il traffico che rimane locale a tali opzioni non verrà visualizzato. I dispositivi possono essere identificati in base ai messaggi ARP, ma le informazioni di connessione saranno mancanti.

:::image type="content" source="media/how-to-set-up-your-network/linear-bus-star-topology.PNG" alt-text="Diagramma del bus lineare e della topologia a stella.":::

#### <a name="multisensor-deployment"></a>Distribuzione multisensore

Ecco alcuni suggerimenti per la distribuzione di più sensori:

| **Number** | **Metri** | **Dipendenza** | **Numero di sensori** |
|--|--|--|--|
| Distanza massima tra i commutatori | 80 metri | Cavo Ethernet preparato | Più di 1 |
| Numero di reti OT | Più di 1 | Nessuna connettività fisica | Più di 1 |
| Numero di opzioni | Può usare la configurazione RSPAN | Fino a otto commutatori con intervallo locale vicino al sensore per distanza del cablaggio | Più di 1 |

#### <a name="traffic-mirroring"></a>Mirroring del traffico  

Per visualizzare solo le informazioni rilevanti per l'analisi del traffico, è necessario connettere il Defender per la piattaforma Internet a una porta di mirroring in un commutatore o un TAP che includa solo circuiti integrati e traffico SCADA industriali. 

:::image type="content" source="media/how-to-set-up-your-network/switch.jpg" alt-text="Usare questa opzione per la configurazione.":::

È possibile monitorare il traffico switch usando i metodi seguenti:

- [Porta di intervallo switch](#switch-span-port)

- [INTERVALLO remoto (RSPAN)](#remote-span-rspan)

- [TOCCO di aggregazione attivo e passivo](#active-and-passive-aggregation-tap)

SPAN e RSPAN sono la terminologia Cisco. Altre marche di commutatori hanno funzionalità simili, ma potrebbero usare una terminologia diversa.

#### <a name="switch-span-port"></a>Porta di intervallo switch

Switch port Analyzer esegue il mirroring del traffico locale dalle interfacce del compartitore all'interfaccia sullo stesso Commuter. Di seguito sono riportate alcune considerazioni:

- Verificare che l'opzione pertinente supporti la funzione di mirroring delle porte.  

- Per impostazione predefinita, l'opzione di mirroring è disabilitata.

- Si consiglia di configurare tutte le porte dello switch, anche se non vi sono dati connessi. In caso contrario, un dispositivo non autorizzato potrebbe essere connesso a una porta non monitorata e non verrà avvisato sul sensore.

- Nelle reti OT che usano la messaggistica broadcast o multicast, configurare il Commuter solo per le trasmissioni RX (Receive). In caso contrario, i messaggi multicast verranno ripetuti per il numero di porte attive e la larghezza di banda viene moltiplicata.

Gli esempi di configurazione seguenti sono solo per riferimento e sono basati su un switch Cisco 2960 (24 porte) che esegue IOS. Sono solo esempi tipici, quindi non vengono usati come istruzioni. Le porte mirror su altri sistemi operativi Cisco e altre marche di commutazioni sono configurate in modo diverso.

:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-terminal-v2.png" alt-text="Diagramma di un terminale della configurazione della porta SPAN.":::
:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-mode-v2.png" alt-text="Diagramma della modalità di configurazione della porta SPAN.":::

##### <a name="monitoring-multiple-vlans"></a>Monitoraggio di più VLAN

Defender for Internet consente di monitorare le VLAN configurate nella rete. Non è necessaria alcuna configurazione di Defender per il sistema Internet delle cose. L'utente deve verificare che il commutatore nella rete sia configurato per l'invio di tag VLAN a Defender per tutto.

Nell'esempio seguente vengono illustrati i comandi obbligatori che devono essere configurati nel commutatore Cisco per abilitare le VLAN di monitoraggio in Defender per l'it:

**Monitoraggio sessione**: questo comando è responsabile del processo di invio di VLAN alla porta span.

- monitorare l'interfaccia di origine della sessione 1 Gi1/2

- monitoraggio della sessione 1 filtro del tipo di pacchetto positivo RX

- monitoraggio della sessione 1 di interfaccia di destinazione fastEthernet1/1 incapsulamento dot1q

**Monitorare la porta trunk F.E. Gi1/1**: le VLAN sono configurate sulla porta trunk.

- interfaccia GigabitEthernet1/1

- switchport trunk incapsulamento dot1q

- Trunk modalità switchport

#### <a name="remote-span-rspan"></a>INTERVALLO remoto (RSPAN)

La sessione di estensione remota riflette il traffico da più porte di origine distribuite in una VLAN remota dedicata.  

:::image type="content" source="media/how-to-set-up-your-network/remote-span.png" alt-text="Diagramma dell'intervallo remoto.":::

I dati nella VLAN vengono quindi recapitati tramite porte trunked tra più opzioni a un commutatore specifico che contiene la porta di destinazione fisica. Questa porta si connette al Defender per la piattaforma Internet.  

##### <a name="more-about-rspan"></a>Altre informazioni su RSPAN

- RSPAN è una funzionalità avanzata che richiede una VLAN speciale per portare il traffico che si estende tra i monitoraggi e i commutatori. RSPAN non è supportato in tutte le opzioni. Verificare che l'opzione supporti la funzione RSPAN.

- Per impostazione predefinita, l'opzione di mirroring è disabilitata.

- La VLAN remota deve essere consentita sulla porta trunked tra i commutatori di origine e di destinazione.

- Tutti i commutatori che si connettono alla stessa sessione RSPAN devono provenire dallo stesso fornitore.

> [!NOTE]
> Assicurarsi che la porta trunk che condivide la VLAN remota tra le opzioni non sia definita come porta di origine della sessione di mirroring.
>
> La VLAN remota aumenta la larghezza di banda sulla porta trunked delle dimensioni della larghezza di banda della sessione con mirroring. Verificare che la porta trunk dello switch supporti questa.  

:::image type="content" source="media/how-to-set-up-your-network/remote-vlan.jpg" alt-text="Diagramma della VLAN remota.":::

#### <a name="rspan-configuration-examples"></a>Esempi di configurazione di RSPAN

RSPAN: basato su Cisco Catalyst 2960 (24 porte).

**Esempio di configurazione dell'opzione di origine:**

:::image type="content" source="media/how-to-set-up-your-network/rspan-configuration.png" alt-text="Screenshot della configurazione di RSPAN.":::

1. Immettere la modalità di configurazione globale.

1. Creare una VLAN dedicata.

1. Identificare la VLAN come RSPAN VLAN.

1. Tornare alla modalità "Configura terminale".

1. Configurare tutte le 24 porte come origini della sessione.

1. Configurare la VLAN RSPAN come destinazione della sessione.

1. Tornare alla modalità Privileged EXEC.

1. Verificare la configurazione per il mirroring delle porte.

**Esempio di configurazione del cambio di destinazione:**

1. Immettere la modalità di configurazione globale.

1. Configurare la VLAN RSPAN come origine della sessione.

1. Configurare la porta fisica 24 come destinazione della sessione.

1. Tornare alla modalità Privileged EXEC.

1. Verificare la configurazione per il mirroring delle porte.

1. Salvare la configurazione.

#### <a name="active-and-passive-aggregation-tap"></a>TOCCO di aggregazione attivo e passivo

Un tocco di aggregazione attivo o passivo viene installato in linea sul cavo di rete. Duplica sia RX che TX nel sensore di monitoraggio.

Il punto di accesso terminale (TAP) è un dispositivo hardware che consente il flusso del traffico di rete dalla porta A alla porta B e dalla porta B alla porta A, senza interruzioni. Crea una copia esatta di entrambi i lati del flusso di traffico, in modo continuo, senza compromettere l'integrità della rete. Alcuni rubinetti aggregano e ricevono il traffico usando le impostazioni di cambio, se lo si desidera. Se l'aggregazione non è supportata, ogni TAP usa due porte del sensore per monitorare il traffico di invio e ricezione.

I colpetti sono vantaggiosi per vari motivi. Sono basati su hardware e non possono essere compromessi. Passano tutto il traffico, anche i messaggi danneggiati, che vengono spesso eliminati. Non sono sensibili al processore, quindi la temporizzazione dei pacchetti è esattamente la posizione in cui i commutatori gestiscono la funzione mirror come attività con priorità bassa che può influire sull'intervallo dei pacchetti con mirroring. Per scopi legali, un tocco è il dispositivo migliore.

Per il monitoraggio delle porte è inoltre possibile utilizzare gli aggregatori TAP. Questi dispositivi sono basati sul processore e non sono intrinsecamente protetti come rubinetti hardware. Potrebbero non riflettere la tempistica esatta del pacchetto.

:::image type="content" source="media/how-to-set-up-your-network/active-passive-tap-v2.PNG" alt-text="Diagramma dei rubinetti attivi e passivi.":::

##### <a name="common-tap-models"></a>Modelli TAP comuni

Questi modelli sono stati testati per la compatibilità. Anche altri fornitori e modelli potrebbero essere compatibili.

| Immagine | Modello |
|--|--|
| :::image type="content" source="media/how-to-set-up-your-network/garland-p1gccas-v2.png" alt-text="Screenshot di Garland P1GCCAS."::: | P1GCCAS Garland |
| :::image type="content" source="media/how-to-set-up-your-network/ixia-tpa2-cu3-v2.png" alt-text="Screenshot di IXIA TPA2-CU3."::: | IXIA TPA2-CU3 |
| :::image type="content" source="media/how-to-set-up-your-network/us-robotics-usr-4503-v2.png" alt-text="Screenshot della robotica US USR 4503."::: | Robotica statunitense USR 4503 |

##### <a name="special-tap-configuration"></a>Configurazione del tocco speciale

| TOCCO ghirlanda | TOCCO robotici statunitensi |
| ----------- | --------------- |
| Assicurarsi che i jumpers siano impostati nel modo seguente:<br />:::image type="content" source="media/how-to-set-up-your-network/jumper-setup-v2.jpg" alt-text="Screenshot dell'opzione robotica US.":::| Verificare che la modalità di aggregazione sia attiva. |

## <a name="deployment-validation"></a>Convalida della distribuzione

### <a name="engineering-self-review"></a>Revisione autonoma del reparto tecnico  

La revisione del diagramma di rete OT e ICS rappresenta il modo più efficiente per definire la posizione migliore a cui connettersi, in cui è possibile ottenere il traffico più rilevante per il monitoraggio.

I tecnici del sito conoscono l'aspetto della rete. La presenza di una sessione di revisione con la rete locale e i team operativi genereranno le aspettative e definiranno il posto migliore per connettere il dispositivo.

Informazioni rilevanti:

- Elenco di dispositivi noti (foglio di calcolo)  

- Numero stimato di dispositivi  

- Fornitori e protocolli industriali

- Modello di opzioni, per verificare che sia disponibile l'opzione per il mirroring delle porte

- Informazioni sugli utenti che gestiscono le opzioni, ad esempio, e se sono risorse esterne

- Elenco di reti OT nel sito

#### <a name="common-questions"></a>Domande frequenti

- Quali sono gli obiettivi generali dell'implementazione? Un inventario completo e una mappa di rete accurata sono importanti?

- Ci sono più reti o ridondanti nell'ICS? Tutte le reti vengono monitorate?

- Esistono comunicazioni tra l'ICS e la rete aziendale (aziendale)? Queste comunicazioni vengono monitorate?

- Le VLAN sono configurate nella progettazione della rete?

- Come viene eseguita la manutenzione dei circuiti integrati con i dispositivi fissi o temporanei?

- Dove sono installati i firewall nelle reti monitorate?

- Sono presenti routing nelle reti monitorate?

- Quali protocolli OT sono attivi nelle reti monitorate?

- Se ci connettiamo a questo switch, verrà visualizzata la comunicazione tra gli HMI e i PLC?

- Qual è la distanza fisica tra i commutatori ICS e il firewall aziendale? 

- È possibile sostituire le opzioni non gestite con i commutatori gestiti o usare un'opzione di rete?

- Sono presenti comunicazioni seriali in rete? In caso affermativo, visualizzarlo nel diagramma di rete.

- Se l'appliance Defender for Internet è connessa a tale commutatore, lo spazio disponibile per i rack è fisico nel cabinet?

#### <a name="other-considerations"></a>Altre considerazioni

Lo scopo di Defender per l'appliance Internet è quello di monitorare il traffico dai livelli 1 e 2.

Per alcune architetture, il Defender per l'appliance per le cose eseguirà anche il monitoraggio di livello 3, se il traffico OT esiste su questo livello. Mentre si esamina l'architettura del sito e si decide se monitorare un Commuter, considerare le variabili seguenti:

- Qual è il costo/vantaggio rispetto all'importanza del monitoraggio di questo switch?

- Se un'opzione non è gestita, sarà possibile monitorare il traffico da un commute di livello superiore?

  Se l'architettura ICS è una topologia ad anello, è necessario monitorare solo uno switch in questo anello.

- Qual è la sicurezza o i rischi operativi in questa rete?

- È possibile monitorare la VLAN del Commuter? La VLAN è visibile in un'altra opzione che è possibile monitorare?

#### <a name="technical-validation"></a>Convalida tecnica

La ricezione di un esempio di traffico registrato (file PCAP) dalla porta del compartitore (o mirror) può essere utile per:

- Verificare se l'opzione è configurata correttamente.

- Verificare che il traffico che passa attraverso l'opzione sia pertinente per il monitoraggio (traffico OT).

- Identifica la larghezza di banda e il numero stimato di dispositivi in questa opzione.

È possibile registrare un file PCAP di esempio (pochi minuti) connettendo un computer portatile a una porta SPAN già configurata tramite l'applicazione Wireshark.

:::image type="content" source="media/how-to-set-up-your-network/laptop-connected-to-span.jpg" alt-text="Screenshot di un computer portatile connesso a una porta SPAN.":::
:::image type="content" source="media/how-to-set-up-your-network/sample-pcap-file.PNG" alt-text="Screenshot della registrazione di un file PCAP di esempio.":::

#### <a name="wireshark-validation"></a>Convalida Wireshark

- Verificare che i *pacchetti unicast* siano presenti nel traffico di registrazione. Unicast è da un indirizzo a un altro. Se la maggior parte del traffico è costituita da messaggi ARP, l'installazione del Commuter non è corretta.

- Passare alla gerarchia del protocollo **Statistics**  >  . Verificare che siano presenti protocolli OT industriali.

:::image type="content" source="media/how-to-set-up-your-network/wireshark-validation.png" alt-text="Screenshot della convalida di Wireshark.":::

## <a name="troubleshooting"></a>Risoluzione dei problemi

Usare queste sezioni per la risoluzione dei problemi:

- [Non è possibile connettersi tramite un'interfaccia Web](#cant-connect-by-using-a-web-interface)

- [L'appliance non risponde](#appliance-is-not-responding)

### <a name="cant-connect-by-using-a-web-interface"></a>Non è possibile connettersi tramite un'interfaccia Web

1. Verificare che il computer che si sta tentando di connettere si trovi nella stessa rete del dispositivo.

2. Verificare che la rete GUI sia connessa alla porta di gestione sul sensore.

3. Effettuare il ping dell'indirizzo IP dell'appliance. Se non è presente alcuna risposta al ping:

    1. Connettere un monitor e una tastiera al dispositivo.

    1. Usare l'utente e la password di **supporto** per accedere.

    1. Usare l' **elenco rete** di comandi per visualizzare l'indirizzo IP corrente.

    :::image type="content" source="media/how-to-set-up-your-network/list-of-network-commands.png" alt-text="Screenshot del comando Network List.":::

4. Se i parametri di rete non sono configurati correttamente, attenersi alla procedura seguente per modificarlo:

    1. Usare le **impostazioni di modifica della rete di** comando.

    1. Per modificare l'indirizzo IP della rete di gestione, selezionare **Y**.

    1. Per modificare il subnet mask, selezionare **Y**.

    1. Per modificare il DNS, selezionare **Y**.

    1. Per modificare l'indirizzo IP del gateway predefinito, selezionare **Y**.

    1. Per la modifica dell'interfaccia di input (solo per il sensore), selezionare **Y**.

    1. Per l'interfaccia Bridge selezionare **N**.

    1. Per applicare le impostazioni, selezionare **Y**.

5. Dopo il riavvio, connettersi con supporto utente e utilizzare il comando **Network List** per verificare che i parametri siano stati modificati.

6. Provare di nuovo a eseguire il ping e connettersi dall'interfaccia utente grafica.

### <a name="appliance-is-not-responding"></a>L'appliance non risponde

1. Connettersi con un monitor e una tastiera al dispositivo oppure usare PuTTy per connettersi in remoto all'interfaccia della riga di comando.

2. Usare le credenziali di supporto per accedere.

3. Usare il comando **integrità sistema** e verificare che tutti i processi siano in esecuzione.

    :::image type="content" source="media/how-to-set-up-your-network/system-sanity-command.png" alt-text="Screenshot del comando di integrità del sistema.":::

Per eventuali altri problemi, contattare [supporto tecnico Microsoft](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="example-site-book"></a>Esempio di libro del sito

Usare il libro del sito di esempio per recuperare e rivedere le informazioni importanti necessarie per la configurazione della rete.

### <a name="site-checklist"></a>Elenco di controllo del sito

Esaminare questo elenco prima della distribuzione del sito:

| **#** | **Attività o attività** | **Status** | **Commenti** |
|--|--|--|--|
| 1 | Fornire Global. | ☐ |  |
| 3 | Ordinare le appliance. | ☐ |  |
| 4 | Preparare un elenco di subnet nella rete. | ☐ |  |
| 5 | Fornire un elenco di VLAN delle reti di produzione. | ☐ |  |
| 6 | Consente di specificare un elenco di modelli di switch nella rete. | ☐ |  |
| 7 | Fornire un elenco di fornitori e protocolli delle apparecchiature industriali. | ☐ |  |
| 8 | Fornire i dettagli di rete per i sensori (indirizzo IP, subnet, D-GW, DNS). | ☐ |  |
| 9 | Creare le regole del firewall necessarie e l'elenco di accesso. | ☐ |  |
| 10 | Creare porte estese sulle opzioni per il monitoraggio delle porte o configurare i rubinetti di rete in modo appropriato. | ☐ |  |
| 11 | Preparare lo spazio rack per le appliance del sensore. | ☐ |  |
| 12 | Preparare una workstation per il personale. | ☐ |  |
| 13 | Fornire una tastiera, un monitor e un mouse per il Defender per i dispositivi rack. | ☐ |  |
| 14 | Rack e cablaggio delle appliance. | ☐ |  |
| 15 | Allocare le risorse del sito per supportare la distribuzione. | ☐ |  |
| 16 | Creare gruppi di Active Directory o utenti locali. | ☐ |  |
| 17 | Training di configurazione (self-learning). | ☐ |  |
| 18 | Go o no-go. | ☐ |  |
| 19 | Pianificare la data di distribuzione. | ☐ |  |


| **Data** | **Nota** | **Data distribuzione** | **Nota** |
|--|--|--|--|
| Defender per IoT |  | Nome sito * |  |
| Nome |  | Nome |  |
| Posizione |  | Posizione |  |

#### <a name="architecture-review"></a>Revisione dell'architettura

Una panoramica del diagramma della rete industriale consentirà di definire la posizione corretta per il Defender per le apparecchiature per le cose.

1.  Visualizzare un diagramma di rete globale dell'ambiente OT industriale. Ad esempio:

    :::image type="content" source="media/how-to-set-up-your-network/ot-global-network-diagram.png" alt-text="Diagramma dell'ambiente OT industriale per la rete globale.":::

    > [!NOTE]
    > Il Defender per l'appliance delle cose deve essere connesso a un commutatore di livello inferiore che rileva il traffico tra le porte del commutatore.  

2. Fornire il numero approssimativo di dispositivi di rete che verranno monitorati. Queste informazioni sono necessarie per il caricamento della sottoscrizione nel portale di Azure Defender for Internet. Durante il processo di onboarding, verrà richiesto di immettere il numero di dispositivi con incrementi di 1000.

3. Fornire un elenco di subnet per le reti di produzione e una descrizione (facoltativo). 

    |  **#**  | **Nome della subnet** | **Descrizione** |
    |--| --------------- | --------------- |
    | 1  | |
    | 2  | |
    | 3  | |
    | 4  | |

4. Fornire un elenco di VLAN delle reti di produzione.

    | **#** | **Nome VLAN** | **Descrizione** |
    |--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

5. Per verificare che le opzioni dispongano della funzionalità di mirroring delle porte, fornire i numeri di modello del commutatore a cui deve connettersi la piattaforma Defender for Internet:

    | **#** | **Switch** | **Modello** | **Supporto per il mirroring del traffico (SPAN, RSPAN o None)** |
    |--|--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

    Una terza parte gestisce i commutatori? Y o N 

    In caso affermativo, chi? __________________________________ 

    Quali sono i criteri? __________________________________ 

    Ad esempio:

    - Siemens

    - Automazione Rockwell-Ethernet o IP

    - Emerson-DeltaV, Ovation
    
6. Sono presenti dispositivi che comunicano tramite una connessione seriale nella rete? Sì o No 

    In caso affermativo, specificare quale protocollo di comunicazione seriale: ________________ 

    In caso affermativo, contrassegnare il diagramma di rete quali dispositivi comunicano con i protocolli seriali e dove sono: 
 
    <Add your network diagram with marked serial connection> 

7. Per QoS, l'impostazione predefinita del sensore è 1,5 Mbps. Specificare se si desidera modificarlo: ________________ 

   Business Unit (BU): ________________ 

### <a name="specifications-for-site-equipment"></a>Specifiche per le apparecchiature del sito

#### <a name="network"></a>Rete  

L'appliance del sensore è connessa alla porta dello switch SPAN tramite una scheda di rete. È connessa alla rete aziendale del cliente per la gestione tramite un'altra scheda di rete dedicata.

Specificare i dettagli dell'indirizzo per la scheda di interfaccia di rete del sensore che verrà connessa nella rete aziendale: 

|  Elemento               | Appliance 1 | Appliance 2 | Appliance 3 |
| --------------- | ------------- | ------------- | ------------- |
| Indirizzo IP del dispositivo    |               |               |               |
| Subnet          |               |               |               |
| Gateway predefinito |               |               |               |
| DNS             |               |               |               |
| Nome host       |               |               |               |

#### <a name="idraciloserver-management"></a>gestione iDRAC/iLO/server

|       Elemento          | Appliance 1 | Appliance 2 | Appliance 3 |
| --------------- | ------------- | ------------- | ------------- |
| Indirizzo IP del dispositivo     |               |               |               |
| Subnet          |               |               |               |
| Gateway predefinito |               |               |               |
| DNS             |               |               |               |

#### <a name="on-premises-management-console"></a>Console di gestione locale  

|       Elemento          | Attivo | Passiva (in caso di utilizzo di a disponibilità elevata) |
| --------------- | ------ | ----------------------- |
| Indirizzo IP             |        |                         |
| Subnet          |        |                         |
| Gateway predefinito |        |                         |
| DNS             |        |                         |

#### <a name="snmp"></a>SNMP  

|   Elemento              | Dettagli |
| --------------- | ------ |
| IP              |        |
| Indirizzo IP | |
| Username | |
| Password | |
| Tipo di autenticazione | MD5 o SHA |
| Crittografia | DES o AES |
| Chiave privata | |
| Stringa comunità SNMP v2 |

### <a name="on-premises-management-console-ssl-certificate"></a>Certificato SSL della console di gestione locale

Si prevede di usare un certificato SSL? Sì o No

In caso affermativo, quale servizio utilizzerà per generarlo? Quali attributi includere nel certificato (ad esempio, dominio o indirizzo IP)?

### <a name="smtp-authentication"></a>Autenticazione SMTP

Si prevede di usare SMTP per l'invio di avvisi a un server di posta elettronica? Sì o No

In caso affermativo, quale metodo di autenticazione si utilizzerà?  

### <a name="active-directory-or-local-users"></a>Utenti Active Directory o locali

Contattare un amministratore Active Directory per creare un gruppo di utenti del sito Active Directory o creare utenti locali. Assicurarsi che gli utenti siano pronti per il giorno della distribuzione. 

### <a name="iot-device-types-in-the-network"></a>Tipi di dispositivi in rete

| Tipo di dispositivo | Numero di dispositivi nella rete | Larghezza di banda media |
| --------------- | ------ | ----------------------- |
| Fotocamera | |
| Macchina a raggi X | |

## <a name="see-also"></a>Vedi anche

[Informazioni sull'installazione di Defender for Internet](how-to-install-software.md)
