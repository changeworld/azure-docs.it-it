---
title: Difensore del glossario
description: Questo glossario fornisce una breve descrizione di un difensore importante per i concetti e i termini della piattaforma Internet.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/09/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 7896237b1f9e9e66659532422efb3449c41ede11
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/01/2021
ms.locfileid: "97845283"
---
# <a name="defender-for-iot-glossary"></a>Difensore del glossario

Questo glossario fornisce una breve descrizione dei termini e dei concetti importanti per Azure Defender per la piattaforma Internet delle cose. Selezionare i collegamenti **ulteriori informazioni** per passare a termini correlati nel glossario. Ciò consentirà di apprendere e usare più rapidamente gli strumenti per i prodotti.

## <a name="a"></a>Una

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **gruppo di accesso** | Supportare i requisiti di accesso degli utenti per le organizzazioni di grandi dimensioni creando regole del gruppo di accesso.<br /><br />Le regole consentono di controllare l'accesso alla visualizzazione e alla configurazione alla console di gestione di protezione per gli utenti in locale per ruoli utente specifici in business unit, aree, siti e zone pertinenti.<br /><br />Ad esempio, è possibile consentire agli analisti della sicurezza di un gruppo di Active Directory di accedere ai dati autoeuropei dell'Europa occidentale, evitando però l'accesso ai dati in Africa. | **<br />Business Unit console di gestione locale <br />** |
| **token di accesso** | Generare token di accesso per accedere al Defender per l'API REST. | **API** |
| **Conferma evento di avviso** | Chiedere a Defender for Internet per l'utente di nascondere l'avviso una volta per l'evento rilevato. L'avviso verrà nuovamente attivato se l'evento viene nuovamente rilevato. | **Evento di avviso per la segnalazione dell'evento di avviso <br /> <br /> <br /> <br />** |
| **avviso** | Messaggio che viene attivato da un Defender per il motore delle cose in merito a deviazioni dal comportamento di rete autorizzato, da anomalie di rete o da traffico e attività di rete sospette. | **<br /> <br /> <br /> notifiche di sistema della regola di esclusione della regola di invio <br />** |
| **Commento avviso** | Commenti che gli analisti e gli amministratori della sicurezza effettuano nei messaggi di avviso. Ad esempio, un commento di avviso può fornire istruzioni sulle azioni di mitigazione da intraprendere oppure i nomi di singoli utenti da contattare per quanto riguarda l'evento.<br /><br />Gli utenti che stanno esaminando gli avvisi possono scegliere il commento o i commenti che meglio riflettono lo stato dell'evento o i passaggi eseguiti per esaminare l'avviso. | **avviso** |
| **motore anomalie** | Un Defender per il motore delle cose che rileva un comportamento e una comunicazione da computer a computer (M2M) insoliti. È ad esempio possibile che il motore rilevi un numero eccessivo di tentativi di accesso SMB. Gli avvisi di anomalie vengono attivati quando vengono rilevati questi eventi. | **Defender per i motori di Internet delle cose** |
| **API** | Consente ai sistemi esterni di accedere ai dati individuati da Defender per tutto il tempo ed eseguire azioni usando l'API REST esterna tramite le connessioni SSL. | **token di accesso** |
| **report vettoriale di attacco** | Rappresentazione grafica in tempo reale delle catene di vulnerabilità di endpoint sfruttabili.<br /><br />I report consentono di valutare l'effetto delle attività di mitigazione nella sequenza di attacco da determinare. Ad esempio, è possibile valutare se un aggiornamento del sistema interrompe il percorso del pirata informatico compromettendo la catena di attacco o se rimane un percorso di attacco alternativo. Questo assegna la priorità alle attività di correzione e mitigazione. | **report di valutazione del rischio** |

## <a name="b"></a>B

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **Business Unit** | Organizzazione logica dell'azienda in base a settori specifici.<br /><br />Ad esempio, una società globale che contiene vetrerie e fabbriche di plastica può essere gestita come due unità aziendali diverse. È possibile controllare l'accesso di Defender per gli utenti di un sacco a business unit specifiche. | **<br /> <br /> <br /> <br /> area del sito <br /> <br /> del gruppo di accesso della console di gestione locale** |
| **base** | Traffico di rete, protocolli, comandi e dispositivi approvati. Defender for Internet rileva le deviazioni dalla linea di base di rete. Consente di visualizzare il traffico di base approvato generando report di data mining. | **<br /> <br /> modalità di apprendimento data mining** |

## <a name="c"></a>C

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **Console di gestione locale** | La console di gestione locale offre una visualizzazione centralizzata e la gestione dei dispositivi e delle minacce che Defender per le distribuzioni di sensori Internet rileva nell'organizzazione. | **Defender per il sensore della piattaforma Internet <br /> <br />** |
| **Comandi dell'interfaccia della riga di comando** | Opzioni dell'interfaccia della riga di comando (CLI) per Defender per gli utenti amministratore. I comandi dell'interfaccia della riga di comando sono disponibili per le funzionalità a cui non è possibile accedere dal Defender per le console. | - |


## <a name="d"></a>D

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **data mining** | Generare report completi e granulari sui dispositivi di rete:<br /><br />- **Risposta agli eventi imprevisti SOC**: report in tempo reale per gestire la risposta immediata agli eventi imprevisti. Ad esempio, un report può elencare i dispositivi che potrebbero richiedere l'applicazione di patch.<br /><br />- **Forensics**: report basati sui dati cronologici per i report investigativi.<br /><br />- **Integrità della rete it**: report che consentono di migliorare la sicurezza complessiva della rete. Un report, ad esempio, può elencare i dispositivi con credenziali di autenticazione vulnerabili.<br /><br />- **visibilità**: report che coprono tutti gli elementi della query per visualizzare tutti i parametri di base della rete.<br /><br />Salvare i report di data mining per gli utenti di sola lettura da visualizzare. | **report di base <br /> <br />** |
| **Defender per i motori di Internet delle cose** | I motori di analisi self-learning in Defender per tutti gli strumenti eliminano la necessità di aggiornare le firme o definire regole. I motori usano analisi del comportamento specifiche di ICS e data science per analizzare continuamente il traffico di rete per anomalie, malware, problemi operativi, violazioni del protocollo e deviazioni dall'attività della rete di base.<br /><br />Quando un motore rileva una deviazione, viene generato un avviso. Gli avvisi possono essere visualizzati e gestiti dalla schermata **avvisi** o da un Siem. | **avviso** |
| **Defender per la piattaforma Internet** | La soluzione Defender for Internet è installata in Defender per i sensori Internet e la console di gestione locale. | **<br /> <br /> console di gestione locale del sensore** |
| **Mappa dei dispositivi** | Rappresentazione grafica dei dispositivi di rete che Defender for it rileva. Mostra le connessioni tra i dispositivi e le informazioni su ogni dispositivo. Usare la mappa per:<br /><br />-Recuperare e controllare le informazioni critiche sul dispositivo.<br /><br />-Analizza le sezioni di rete.<br /><br />-Esportare i dettagli e i riepiloghi del dispositivo. | **Gruppo di livelli Purdue** |
| **inventario dispositivi-sensore** | Nell'inventario del dispositivo viene visualizzata una vasta gamma di attributi del dispositivo rilevati da Defender per l'intero. Sono disponibili opzioni per:<br /><br />-Filtrare le informazioni visualizzate.<br /><br />-Esportare le informazioni in un file CSV.<br /><br />-Importa i dettagli del registro di sistema di Windows. | **gruppo inventario dispositivi-console di gestione locale** |
| **inventario dispositivi-console di gestione locale** | Le informazioni sul dispositivo dei sensori connessi possono essere visualizzate dalla console di gestione locale nell'inventario dei dispositivi. In questo modo gli utenti della console di gestione locale forniscono una panoramica completa di tutte le informazioni sulla rete. | **inventario dispositivi- <br /> <br /> inventario dispositivi del sensore-Data Integrator** |
| **inventario dispositivi-Data Integrator** | Le funzionalità di integrazione dei dati della console di gestione locale consentono di migliorare i dati nell'inventario dei dispositivi con le informazioni provenienti da altre risorse aziendali. Le risorse di esempio sono CMDBs, DNS, firewall e API Web. | **inventario dispositivi-console di gestione locale** |

## <a name="e"></a>E

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **visualizzazione Enterprise** | Mappa globale che presenta le unità aziendali, i siti e le aree in cui sono installati i sensori Defender for Internet. Visualizza le posizioni geografiche di avvisi dannosi, avvisi operativi e altro ancora. | **<br /> <br /> <br /> <br /> area sito business unit** |
| **sequenza temporale eventi** | Sequenza temporale delle attività rilevate nella rete, tra cui:<br /><br />-Avvisi attivati.<br /><br />-Eventi di rete (informational).<br /><br />-Operazioni utente, ad esempio accesso, eliminazione di utenti e creazione di utenti, nonché operazioni di gestione degli avvisi, ad esempio mute, Learn e riconoscimento. Disponibile nelle console del sensore. | - |
| **regola di esclusione** | Indica a Defender di ignorare i trigger di avviso in base al periodo di tempo, all'indirizzo del dispositivo e al nome dell'avviso o a un sensore specifico.<br /><br />Ad esempio, se si sa che tutti i dispositivi OT monitorati da un sensore specifico passeranno attraverso una procedura di manutenzione compresa tra 6:30 e 10:15 al mattino, è possibile impostare una regola di esclusione che indica che questo sensore non deve inviare avvisi nel periodo predefinito. | **<br /> <br /> evento di avviso di silenziamento avviso** |

## <a name="f"></a>F

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **regola di invio** | Le regole di invio indicano a Defender per l'invio di informazioni sugli avvisi ai fornitori o ai sistemi partner.<br /><br />Inviare, ad esempio, informazioni sugli avvisi a un server Splunk o a un server syslog. | **avviso** |

## <a name="g"></a>G

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **utenti** | Gruppi predefiniti o personalizzati di dispositivi che contengono attributi specifici, ad esempio dispositivi che hanno eseguito attività di programmazione o dispositivi che si trovano in una subnet specifica. Usare i gruppi per visualizzare i dispositivi e analizzare i dispositivi in Defender per l'it.<br /><br />I gruppi possono essere visualizzati e creati dalla mappa del dispositivo e dall'inventario dei dispositivi. | **inventario dei <br /> dispositivi della mappa dei dispositivi <br />** |

## <a name="h"></a>H

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **Ambiente di sviluppo open Horizon** | Proteggere i dispositivi e i dispositivi ICS che eseguono protocolli o protocolli proprietari e personalizzati che si scostano da qualsiasi standard. Usare l'SDK dell'ambiente di sviluppo open Horizon (ODE) per sviluppare plug-in di dissettori che decodificano il traffico di rete in base ai protocolli definiti. Il Defender per i servizi di gestione delle cose analizza il traffico per offrire monitoraggio completo, avvisi e report.<br /><br />USA orizzonte per:<br /><br />- **Espandi** visibilità e controllo senza dover aggiornare Defender per le versioni della piattaforma Internet.<br /><br />- **Proteggere** le informazioni proprietarie sviluppando il sito come plug-in esterno.<br /><br />- **Localizzare** il testo per gli avvisi, gli eventi e i parametri del protocollo.<br /><br />Per informazioni dettagliate, contattare il rappresentante di successo del cliente. | **localizzazione del supporto del protocollo <br /> <br />** |
| **Avviso personalizzato Horizon** | Migliorare la gestione degli avvisi nell'azienda attivando gli avvisi personalizzati per qualsiasi protocollo (in base ai dissettori del traffico di Horizon Framework).<br /><br />Questi avvisi possono essere usati per comunicare le informazioni:<br /><br />-Informazioni sui rilevamenti del traffico basati su protocolli e protocolli sottostanti in un plug-in di Horizon proprietario.<br /><br />-Informazioni su una combinazione di campi di protocollo da tutti i livelli di protocollo. | **supporto del protocollo** |

## <a name="i"></a>I

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **integrazioni** | Espandere Defender per le funzionalità di Internet delle cose condividendo le informazioni sui dispositivi con i sistemi partner. Le organizzazioni possono colmare le soluzioni di sicurezza, NAC, gestione degli eventi imprevisti e gestione dei dispositivi in precedenza per accelerare le risposte a livello di sistema e attenuare più rapidamente i rischi. | **regola di invio** |
| **subnet interna** | Configurazioni di subnet definite da Defender per l'it. In alcuni casi, ad esempio gli ambienti che usano intervalli pubblici come intervalli interni, è possibile istruire Defender for Internet per la risoluzione di tutte le subnet come subnet interne. Le subnet vengono visualizzate nella mappa e in diversi Defender per i report relativi alle cose. | **subnet** |

## <a name="l"></a>L

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **Informazioni sugli eventi di avviso** | Indica a Defender per le cose di autorizzare il traffico rilevato in un evento di avviso. | **Evento di avviso del <br /> <br /> silenziamento dell'evento <br /> <br /> di avviso di avviso** |
| **modalità di apprendimento** | Modalità usata quando Defender for Internet apprende l'attività di rete. Questa attività diventa la linea di base di rete. Il Defender per le cose rimane in modalità per un periodo predefinito dopo l'installazione. L'attività che devia dall'attività appresa dopo questo periodo attiverà il Defender per gli avvisi di Internet. | **<br />Linea di base apprendimento IT intelligente <br />** |
| **localizzazione** | Localizzare il testo per gli avvisi, gli eventi e i parametri del protocollo per i plug-in di dissettori sviluppati da Horizon. | **Ambiente di sviluppo open Horizon** |

## <a name="m"></a>M

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **Disattiva evento di avviso** | Indica a Defender per le cose di ignorare continuamente le attività con dispositivi identici e traffico analogo. | **Evento di avviso di <br /> <br /> riconoscimento della regola di esclusione avvisi <br /> <br /> <br /> <br />** |

## <a name="n"></a>N

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **notifiche** | Informazioni sulle modifiche alla rete o sulle proprietà dei dispositivi non risolti. Sono disponibili opzioni per aggiornare le informazioni sul dispositivo e sulla rete con i nuovi dati rilevati. La risposta alle notifiche arricchisce l'inventario dei dispositivi, la mappa e vari report. Disponibile nelle console del sensore. | **<br /> <br /> notifiche del sistema di avvisi** |

## <a name="o"></a>O

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **avviso operativo** | Avvisi che riguardano problemi di rete operativo, ad esempio un dispositivo che si sospetta che sia disconnesso dalla rete. | **avviso di <br /> <br /> sicurezza avviso** |

## <a name="p"></a>P

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **Livello Purdue** | Mostra le interconnessioni e le interdipendenze dei componenti principali di un ICS tipico sulla mappa. |  |
| **supporto del protocollo** | Oltre al supporto per i protocolli incorporati, è possibile proteggere i dispositivi Internet e i dispositivi ICS che eseguono protocolli proprietari e personalizzati, o protocolli che deviano da qualsiasi standard, usando Horizon Open Development Environment SDK. | **Ambiente di sviluppo open Horizon** |

## <a name="r"></a>R

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **region** | Divisione logica di un'organizzazione globale in aree geografiche. Esempi sono America del Nord, Europa occidentale ed Europa orientale.<br /><br />America del Nord possono avere Factory di diverse business unit. | **<br /> <br /> <br /> <br /> <br /> <br /> area del sito <br /> <br /> della console di gestione locale del gruppo di accesso** |
| **report** | I report riflettono le informazioni generate dai risultati della query di data mining. Sono inclusi i risultati predefiniti di data mining, disponibili nella visualizzazione **report** . Gli amministratori e gli analisti della sicurezza possono anche generare query di data mining personalizzate e salvarle come report. Questi report saranno disponibili anche per gli utenti di sola lettura. | **data mining** |
| **report di valutazione del rischio** | La creazione di report di valutazione dei rischi consente di generare un punteggio di sicurezza per ogni dispositivo di rete, insieme a un punteggio di sicurezza di rete globale. Il Punteggio complessivo rappresenta la percentuale di sicurezza del 100%. Il report fornisce consigli per la mitigazione che consentono di migliorare il Punteggio di sicurezza corrente. | - |

## <a name="s"></a>S

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **avviso di sicurezza** | Avvisi che riguardano problemi di sicurezza, ad esempio tentativi di accesso SMB eccessivi o rilevamenti di malware. | **avviso <br /> <br /> operativo avviso** |
| **sondaggio selettivo** | Defender per l'Internet delle cose esamina passivamente il traffico e il traffico e rileva le informazioni rilevanti sui dispositivi, i relativi attributi, il loro comportamento e altro ancora. In alcuni casi, alcune informazioni potrebbero non essere visibili nelle analisi della rete passiva.<br /><br />Quando si verifica questo problema, è possibile usare gli strumenti di probe sicuri e granulari in Defender, per individuare informazioni importanti sui dispositivi precedentemente non raggiungibili. | - |
| **sensore** | Macchina virtuale o fisica in cui è installata la piattaforma Defender for Internet. | **Console di gestione locale** |
| **sito** | Posizione di una factory o di un'altra entità. Il sito deve contenere una zona o diverse zone in cui è installato un sensore. | **zona** |
| **Gestione del sito** | Opzione della console di gestione locale che consente di gestire i sensori aziendali. | - |
| **Apprendimento intelligente IT** | Una volta completato il periodo di apprendimento e la modalità di apprendimento è disabilitata, il Defender per le cose potrebbe rilevare un livello insolitamente elevato di modifiche di base che sono il risultato della normale attività IT, ad esempio le richieste DNS e HTTP. Questo traffico potrebbe attivare avvisi di violazione dei criteri non necessari e notifiche di sistema. Per ridurre questi avvisi e notifiche, è possibile abilitare l'apprendimento IT intelligente. | **linea di <br /> base modalità di apprendimento <br />** |
| **subnet** | Per abilitare lo stato attivo sui dispositivi OT, i dispositivi IT vengono aggregati automaticamente per subnet nella mappa del dispositivo. Ogni subnet viene presentata come una singola entità sulla mappa, inclusa una funzionalità di compressione o espansione interattiva per concentrarsi in una subnet IT e viceversa. | **Mappa dei dispositivi** |
| **notifiche di sistema** | Notifiche dalla console di gestione locale:<br /><br />-Stato della connessione del sensore.<br /><br />-Errori di backup remoto. | **<br /> <br /> avviso notifiche** |

## <a name="z"></a>Z

| Termine | Descrizione | Altre informazioni |
|--|--|--|
| **zona** | Area all'interno di un sito in cui sono installati un sensore o sensori. | **<br /> <br /> area dell'unità aziendale <br /> <br /> del sito** |
