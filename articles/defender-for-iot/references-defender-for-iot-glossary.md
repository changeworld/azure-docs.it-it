---
title: Glossario di Defender per IoT
description: Questo glossario fornisce una breve descrizione di un difensore importante per i concetti e i termini della piattaforma Internet.
ms.date: 12/09/2020
ms.topic: article
ms.openlocfilehash: 191b94ca37118ed6413149dd6047f94c5ee568cd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786822"
---
# <a name="defender-for-iot-glossary"></a>Glossario di Defender per IoT

Questo glossario fornisce una breve descrizione dei termini e dei concetti importanti per Azure Defender per la piattaforma Internet delle cose. Selezionare i collegamenti **ulteriori informazioni** per passare a termini correlati nel glossario. Ciò consentirà di apprendere e usare più rapidamente gli strumenti per i prodotti.

> [!Note]
> Qualsiasi termine con un `(DB)` elenco denominato è un termine di generatore di dispositivi basato su agente. 

<a name="glossary-a"></a>

## <a name="a"></a>A

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **Gruppo di accesso** | Supportare i requisiti di accesso degli utenti per le organizzazioni di grandi dimensioni creando regole del gruppo di accesso.<br /><br />Le regole consentono di controllare l'accesso alla visualizzazione e alla configurazione alla console di gestione di protezione per gli utenti in locale per ruoli utente specifici in business unit, aree, siti e zone pertinenti.<br /><br />Ad esempio, è possibile consentire agli analisti della sicurezza di un gruppo di Active Directory di accedere ai dati autoeuropei dell'Europa occidentale, evitando però l'accesso ai dati in Africa. | **[Console di gestione locale](#o)** <br /><br />**[Business unit](#b)** |
| **Token di accesso** | Generare token di accesso per accedere al Defender per l'API REST. | **[API](#glossary-a)** |
| **Conferma evento di avviso** | Chiedere a Defender for Internet per l'utente di nascondere l'avviso una volta per l'evento rilevato. L'avviso verrà nuovamente attivato se l'evento viene nuovamente rilevato. | **[Avviso di](#glossary-a) <br /> <br /> [Informazioni sugli eventi](#l) <br /> <br /> di avviso [Disattiva evento di avviso](#m)** |
| **Avviso** | Messaggio che viene attivato da un Defender per il motore delle cose in merito a deviazioni dal comportamento di rete autorizzato, da anomalie di rete o da traffico e attività di rete sospette. | **[](#f) <br /> Regola <br /> di invio [](#e) <br /> Regola <br /> di esclusione [Notifiche di sistema](#s)** |
| **Commento avviso** | Commenti che gli analisti e gli amministratori della sicurezza effettuano nei messaggi di avviso. Ad esempio, un commento di avviso può fornire istruzioni sulle azioni di mitigazione da intraprendere oppure i nomi di singoli utenti da contattare per quanto riguarda l'evento.<br /><br />Gli utenti che stanno esaminando gli avvisi possono scegliere il commento o i commenti che meglio riflettono lo stato dell'evento o i passaggi eseguiti per esaminare l'avviso. | **[Avviso](#glossary-a)** |
| **Motore anomalie** | Un Defender per il motore delle cose che rileva un comportamento e una comunicazione da computer a computer (M2M) insoliti. È ad esempio possibile che il motore rilevi un numero eccessivo di tentativi di accesso SMB. Gli avvisi di anomalie vengono attivati quando vengono rilevati questi eventi. | **[Defender per i motori di Internet delle cose](#d)** |
| **API** | Consente ai sistemi esterni di accedere ai dati individuati da Defender per tutto il tempo ed eseguire azioni usando l'API REST esterna tramite le connessioni SSL. | **[Token di accesso](#glossary-a)** |
| **Report vettoriale di attacco** | Rappresentazione grafica in tempo reale delle catene di vulnerabilità di endpoint sfruttabili.<br /><br />I report consentono di valutare l'effetto delle attività di mitigazione nella sequenza di attacco da determinare. Ad esempio, è possibile valutare se un aggiornamento del sistema interrompe il percorso del pirata informatico compromettendo la catena di attacco o se rimane un percorso di attacco alternativo. Questo assegna la priorità alle attività di correzione e mitigazione. | **[Report di valutazione del rischio](#r)** |

## <a name="b"></a>B

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **Business unit** | Organizzazione logica dell'azienda in base a settori specifici.<br /><br />Ad esempio, una società globale che contiene vetrerie e fabbriche di plastica può essere gestita come due unità aziendali diverse. È possibile controllare l'accesso di Defender per gli utenti di un sacco a business unit specifiche. | **[](#o) <br /> Console <br /> di gestione locale [](#glossary-a) <br /> Gruppo <br /> di accesso [Sito di](#s) <br /> <br /> [Area](#z) di** |
| **Di base** | Traffico di rete, protocolli, comandi e dispositivi approvati. Defender for Internet rileva le deviazioni dalla linea di base di rete. Consente di visualizzare il traffico di base approvato generando report di data mining. | **[Data mining](#d) <br /> <br /> [Modalità di apprendimento](#l)** |

## <a name="c"></a>C

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **Comandi dell'interfaccia della riga di comando** | Opzioni dell'interfaccia della riga di comando (CLI) per Defender per gli utenti amministratore. I comandi dell'interfaccia della riga di comando sono disponibili per le funzionalità a cui non è possibile accedere dal Defender per le console. | - |


## <a name="d"></a>D

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **Data mining** | Generare report completi e granulari sui dispositivi di rete:<br /><br />- **Risposta agli eventi imprevisti SOC**: report in tempo reale per gestire la risposta immediata agli eventi imprevisti. Ad esempio, un report può elencare i dispositivi che potrebbero richiedere l'applicazione di patch.<br /><br />- **Forensics**: report basati sui dati cronologici per i report investigativi.<br /><br />- **Integrità della rete it**: report che consentono di migliorare la sicurezza complessiva della rete. Un report, ad esempio, può elencare i dispositivi con credenziali di autenticazione vulnerabili.<br /><br />- **visibilità**: report che coprono tutti gli elementi della query per visualizzare tutti i parametri di base della rete.<br /><br />Salvare i report di data mining per gli utenti di sola lettura da visualizzare. | **[Linea di base](#b) <br /> <br /> [Report](#r) di** |
| **Defender per i motori di Internet delle cose** | I motori di analisi self-learning in Defender per tutti gli strumenti eliminano la necessità di aggiornare le firme o definire regole. I motori usano analisi del comportamento specifiche di ICS e data science per analizzare continuamente il traffico di rete per anomalie, malware, problemi operativi, violazioni del protocollo e deviazioni dall'attività della rete di base.<br /><br />Quando un motore rileva una deviazione, viene generato un avviso. Gli avvisi possono essere visualizzati e gestiti dalla schermata **avvisi** o da un Siem. | **[Avviso](#glossary-a)** |
| **Defender per la piattaforma Internet** | La soluzione Defender for Internet è installata in Defender per i sensori Internet e la console di gestione locale. | **[Sensore di](#s) <br /> <br /> [Console di gestione locale](#o)** |
| **Mappa dei dispositivi** | Rappresentazione grafica dei dispositivi di rete che Defender for it rileva. Mostra le connessioni tra i dispositivi e le informazioni su ogni dispositivo. Usare la mappa per:<br /><br />-Recuperare e controllare le informazioni critiche sul dispositivo.<br /><br />-Analizza le sezioni di rete.<br /><br />-Esportare i dettagli e i riepiloghi del dispositivo. | **[Gruppo di livelli Purdue](#p)** |
| **Inventario dispositivi-sensore** | Nell'inventario del dispositivo viene visualizzata una vasta gamma di attributi del dispositivo rilevati da Defender per l'intero. Sono disponibili opzioni per:<br /><br />-Filtrare le informazioni visualizzate.<br /><br />-Esportare le informazioni in un file CSV.<br /><br />-Importa i dettagli del registro di sistema di Windows. | **[Group](#g)** <br /><br />**[Inventario dispositivi-console di gestione locale](#d)** |
| **Inventario dispositivi-console di gestione locale** | Le informazioni sul dispositivo dei sensori connessi possono essere visualizzate dalla console di gestione locale nell'inventario dei dispositivi. In questo modo gli utenti della console di gestione locale forniscono una panoramica completa di tutte le informazioni sulla rete. | **[Inventario dispositivi-sensore](#d) <br /> <br /> [Inventario dispositivi-Data Integrator](#d)** |
| **Inventario dispositivi-Data Integrator** | Le funzionalità di integrazione dei dati della console di gestione locale consentono di migliorare i dati nell'inventario dei dispositivi con le informazioni provenienti da altre risorse aziendali. Le risorse di esempio sono CMDBs, DNS, firewall e API Web. | **[Inventario dispositivi-console di gestione locale](#d)** |
| **Dispositivi gemelli**`(DB)` | I dispositivi gemelli sono documenti JSON che archiviano le informazioni sullo stato del dispositivo, inclusi metadati, configurazioni e condizioni. | [Modulo gemello](#m) <br /> <br />[Defender-molto-micro-Agent gemello](#s) |

## <a name="e"></a>E

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **Visualizzazione Enterprise** | Mappa globale che presenta le unità aziendali, i siti e le aree in cui sono installati i difensori dei sensori Internet. Visualizza le posizioni geografiche di avvisi dannosi, avvisi operativi e altro ancora. | **[Business Unit](#b) <br /> <br /> [Sito di](#s) <br /> <br /> [Area](#z) di** |
| **Sequenza temporale eventi** | Sequenza temporale delle attività rilevate nella rete, tra cui:<br /><br />-Avvisi attivati.<br /><br />-Eventi di rete (informational).<br /><br />-Operazioni utente come l'accesso, l'eliminazione degli utenti e la creazione dell'utente e le operazioni di gestione degli avvisi, ad esempio mute, Learn e conferma. Disponibile nelle console del sensore. | - |
| **Regola di esclusione** | Indica a Defender di ignorare i trigger di avviso in base al periodo di tempo, all'indirizzo del dispositivo e al nome dell'avviso o a un sensore specifico.<br /><br />Ad esempio, se si sa che tutti i dispositivi OT monitorati da un sensore specifico passeranno attraverso una procedura di manutenzione compresa tra 6:30 e 10:15 al mattino, è possibile impostare una regola di esclusione che indica che questo sensore non deve inviare avvisi nel periodo predefinito. | **[Avviso di](#glossary-a) <br /> <br /> [Disattiva evento di avviso](#m)** |

## <a name="f"></a>F

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **Regola di invio** | Le regole di invio indicano a Defender per l'invio di informazioni sugli avvisi ai fornitori o ai sistemi partner.<br /><br />Inviare, ad esempio, informazioni sugli avvisi a un server Splunk o a un server syslog. | **[Avviso](#glossary-a)** |

## <a name="g"></a>G

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **Gruppo** | Gruppi predefiniti o personalizzati di dispositivi che contengono attributi specifici, ad esempio dispositivi che hanno eseguito attività di programmazione o dispositivi che si trovano in una subnet specifica. Usare i gruppi per visualizzare i dispositivi e analizzare i dispositivi in Defender per l'it.<br /><br />I gruppi possono essere visualizzati e creati dalla mappa del dispositivo e dall'inventario dei dispositivi. | **[](#d) <br /> Mappa <br /> dei dispositivi [Inventario dei dispositivi](#d)** |

## <a name="h"></a>H

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **Ambiente di sviluppo open Horizon** | Proteggere i dispositivi e i dispositivi ICS che eseguono protocolli o protocolli proprietari e personalizzati che si scostano da qualsiasi standard. Usare l'SDK dell'ambiente di sviluppo open Horizon (ODE) per sviluppare plug-in di dissettori che decodificano il traffico di rete in base ai protocolli definiti. I difensori dei servizi Internet analizzano il traffico per offrire monitoraggio completo, avvisi e report.<br /><br />USA orizzonte per:<br /><br />- **Espandi** visibilità e controllo senza dover aggiornare Defender per le versioni della piattaforma Internet.<br /><br />- **Proteggere** le informazioni proprietarie sviluppando il sito come plug-in esterno.<br /><br />- **Localizzare** il testo per gli avvisi, gli eventi e i parametri del protocollo.<br /><br />Per informazioni dettagliate, contattare il rappresentante di successo del cliente. | **[](#p) <br /> Supporto <br /> del protocollo [Localizzazione](#l)** |
| **Avviso personalizzato Horizon** | Migliorare la gestione degli avvisi nell'azienda attivando gli avvisi personalizzati per qualsiasi protocollo (in base ai dissettori del traffico di Horizon Framework).<br /><br />Questi avvisi possono essere usati per comunicare le informazioni:<br /><br />-Informazioni sui rilevamenti del traffico basati su protocolli e protocolli sottostanti in un plug-in di Horizon proprietario.<br /><br />-Informazioni su una combinazione di campi di protocollo da tutti i livelli di protocollo. | **[Supporto del protocollo](#p)** |

## <a name="i"></a>I

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **Hub IoT** `(DB)` | Servizio gestito, ospitato nel cloud, che funge da Hub messaggi centrale per la comunicazione bidirezionale tra l'applicazione Internet e i dispositivi gestiti.  |   |
| **Integrazioni** | Espandere Defender per le funzionalità di Internet delle cose condividendo le informazioni sui dispositivi con i sistemi partner. Le organizzazioni possono colmare le soluzioni di sicurezza, NAC, gestione degli eventi imprevisti e gestione dei dispositivi in precedenza per accelerare le risposte a livello di sistema e attenuare più rapidamente i rischi. | **[Regola di invio](#f)** |
| **Subnet interna** | Configurazioni di subnet definite da Defender per l'it. In alcuni casi, ad esempio gli ambienti che usano intervalli pubblici come intervalli interni, è possibile istruire Defender for Internet per la risoluzione di tutte le subnet come subnet interne. Le subnet vengono visualizzate nella mappa e in diversi Defender per i report relativi alle cose. | **[Subnet](#s)** |

## <a name="l"></a>L

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **Informazioni sugli eventi di avviso** | Indica a Defender per le cose di autorizzare il traffico rilevato in un evento di avviso. | **[Avviso di](#glossary-a) <br /> <br /> [Conferma evento](#glossary-a) <br /> <br /> di avviso [Disattiva evento di avviso](#m)** |
| **Modalità di apprendimento** | Modalità usata quando Defender for Internet apprende l'attività di rete. Questa attività diventa la linea di base di rete. Il Defender per le cose rimane in modalità per un periodo predefinito dopo l'installazione. L'attività che devia dall'attività appresa dopo questo periodo attiverà il Defender per gli avvisi di Internet. | **[](#s) <br /> Apprendimento <br /> intelligente it [Linea di base](#b)** |
| **Localizzazione** | Localizzare il testo per gli avvisi, gli eventi e i parametri del protocollo per i plug-in di dissettori sviluppati da Horizon. | **[Ambiente di sviluppo open Horizon](#h)** |

## <a name="m"></a>M


| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **Agente micro**`(DB)` | Offre funzionalità di sicurezza approfondite per i dispositivi Internet, inclusi il rilevamento delle minacce e le condizioni di sicurezza. | |
| **Modulo gemello** `(DB)` | I moduli gemelli sono documenti JSON nei quali vengono archiviate informazioni sullo stato dei moduli, ad esempio metadati, configurazioni e condizioni. | [Dispositivo gemello](#d) <br /> <br />[Defender-molto-micro-Agent gemello](#s) |
| **Disattiva evento di avviso** | Indica a Defender per le cose di ignorare continuamente le attività con dispositivi identici e traffico analogo. | **[Avviso di](#glossary-a) <br /> <br /> [](#e) <br /> Regola <br /> di esclusione [Conferma evento](#glossary-a) <br /> <br /> di avviso [Informazioni sugli eventi di avviso](#l)** |

## <a name="n"></a>N

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **Notifications** | Informazioni sulle modifiche alla rete o sulle proprietà dei dispositivi non risolti. Sono disponibili opzioni per aggiornare le informazioni sul dispositivo e sulla rete con i nuovi dati rilevati. La risposta alle notifiche arricchisce l'inventario dei dispositivi, la mappa e vari report. Disponibile nelle console del sensore. | **[Avviso di](#glossary-a) <br /> <br /> [Notifiche di sistema](#s)** |

## <a name="o"></a>O

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **Console di gestione locale** | La console di gestione locale offre una visualizzazione centralizzata e la gestione dei dispositivi e delle minacce che i difensori per le distribuzioni di sensori Internet in uso rilevano nell'organizzazione. | **[Defender per la piattaforma Internet](#d) <br /> <br /> [Sensore](#s) di** |
| **Avviso operativo** | Avvisi che riguardano problemi di rete operativo, ad esempio un dispositivo che si sospetta che sia disconnesso dalla rete. | **[Avviso di](#glossary-a) <br /> <br /> [Avviso di sicurezza](#s)** |

## <a name="p"></a>P

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **Livello Purdue** | Mostra le interconnessioni e le interdipendenze dei componenti principali di un ICS tipico sulla mappa. |  |
| **Supporto del protocollo** | Oltre al supporto per i protocolli incorporati, è possibile proteggere i dispositivi Internet e i dispositivi ICS che eseguono protocolli proprietari e personalizzati, o protocolli che deviano da qualsiasi standard, usando Horizon Open Development Environment SDK. | **[Ambiente di sviluppo open Horizon](#h)** |

## <a name="r"></a>R

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **Area** | Divisione logica di un'organizzazione globale in aree geografiche. Esempi sono America del Nord, Europa occidentale ed Europa orientale.<br /><br />America del Nord possono avere Factory di diverse business unit. | **[](#glossary-a) <br /> Gruppo <br /> di accesso [Business Unit](#b) <br /> <br /> [](#o) <br /> Console <br /> di gestione locale [Sito di](#s) <br /> <br /> [Area](#z) di** |
| **Report** | I report riflettono le informazioni generate dai risultati della query di data mining. Sono inclusi i risultati predefiniti di data mining, disponibili nella visualizzazione **report** . Gli amministratori e gli analisti della sicurezza possono anche generare query di data mining personalizzate e salvarle come report. Questi report saranno disponibili anche per gli utenti di sola lettura. | **[Data mining](#d)** |
| **Report di valutazione del rischio** | La creazione di report di valutazione dei rischi consente di generare un punteggio di sicurezza per ogni dispositivo di rete, insieme a un punteggio di sicurezza di rete globale. Il Punteggio complessivo rappresenta la percentuale di sicurezza del 100%. Il report fornisce consigli per la mitigazione che consentono di migliorare il Punteggio di sicurezza corrente. | - |

## <a name="s"></a>S

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **Avviso di sicurezza** | Avvisi che riguardano problemi di sicurezza, ad esempio tentativi di accesso SMB eccessivi o rilevamenti di malware. | **[Avviso di](#glossary-a) <br /> <br /> [Avviso operativo](#o)** |
| **Defender-molto-micro-Agent gemello**`(DB)` | Defender-Internet-micro-Agent gemello include tutte le informazioni rilevanti per la sicurezza dei dispositivi, per ogni dispositivo specifico nella soluzione. | [Dispositivo gemello](#d) <br /> <br />[Modulo gemello](#m)  |
| **Sondaggio selettivo** | Defender per l'Internet delle cose esamina passivamente il traffico e il traffico e rileva le informazioni rilevanti sui dispositivi, i relativi attributi, il loro comportamento e altro ancora. In alcuni casi, alcune informazioni potrebbero non essere visibili nelle analisi della rete passiva.<br /><br />Quando si verifica questo problema, è possibile usare gli strumenti di probe sicuri e granulari in Defender, per individuare informazioni importanti sui dispositivi precedentemente non raggiungibili. | - |
| **Sensor** | Macchina virtuale o fisica in cui è installata la piattaforma Defender for Internet. | **[Console di gestione locale](#o)** |
| **Sito** | Posizione di una factory o di un'altra entità. Il sito deve contenere una zona o diverse zone in cui è installato un sensore. | **[Zona](#z)** |
| **Gestione del sito** | Opzione della console di gestione locale che consente di gestire i sensori aziendali. | - |
| **Apprendimento intelligente IT** | Una volta completato il periodo di apprendimento e la modalità di apprendimento è disabilitata, il Defender per le cose potrebbe rilevare un livello insolitamente elevato di modifiche di base che sono il risultato della normale attività IT, ad esempio le richieste DNS e HTTP. Questo traffico potrebbe attivare avvisi di violazione dei criteri non necessari e notifiche di sistema. Per ridurre questi avvisi e notifiche, è possibile abilitare l'apprendimento IT intelligente. | **[](#l) <br /> Modalità <br /> di apprendimento [Linea di base](#b)** |
| **Subnet** | Per abilitare lo stato attivo sui dispositivi OT, i dispositivi IT vengono aggregati automaticamente per subnet nella mappa del dispositivo. Ogni subnet viene presentata come una singola entità sulla mappa, inclusa una funzionalità di compressione o espansione interattiva per concentrarsi in una subnet IT e viceversa. | **[Mappa dei dispositivi](#d)** |
| **Notifiche di sistema** | Notifiche dalla console di gestione locale:<br /><br />-Stato della connessione del sensore.<br /><br />-Errori di backup remoto. | **[Notifiche di](#n) <br /> <br /> [Avviso](#glossary-a) di** |

## <a name="z"></a>Z

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **Zona** | Area all'interno di un sito in cui sono installati un sensore o sensori. | **[Sito di](#s) <br /> <br /> [Business Unit](#b) <br /> <br /> [Area geografica](#r)** |
