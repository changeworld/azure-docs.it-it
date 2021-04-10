---
title: Tipi di avviso e descrizioni
description: Esaminare Defender per le descrizioni degli avvisi.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/29/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 42a922dab2237925c2294245e112aab70b4245f0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727723"
---
# <a name="alert-types-and-descriptions"></a>Tipi di avviso e descrizioni

Questo articolo descrive tutti i tipi di avviso di OPF, che possono essere generati dal Defender per i motori di Internet delle cose. Gli avvisi vengono visualizzati nella finestra avvisi, che consente di gestire l'evento di avviso. 

## <a name="policy-engine-alerts"></a>Avvisi del motore criteri

Gli avvisi del motore di criteri descrivono le deviazioni dal comportamento della rete di base appreso.

| Titolo  | Descrizione | Gravità |
|--|--|--|
| Utilizzo anomalo degli indirizzi MAC | Un nuovo dispositivo di origine è stato rilevato sulla rete ma non è stato autorizzato. | Minorenne |
| Software Beckhoff modificato | Il firmware è stato aggiornato in un dispositivo di origine. Questa attività può essere autorizzata, ad esempio una procedura di manutenzione pianificata. | Principale |
| Accesso al database non riuscito | È stato rilevato un tentativo di accesso non riuscito da un dispositivo di origine a un server di destinazione. Questo potrebbe essere il risultato di un errore umano, ma potrebbe indicare anche un tentativo dannoso di compromettere il server o i dati. | Principale |
| Versione del firmware Emerson ROC modificata | Il firmware è stato aggiornato in un dispositivo di origine. Questa attività può essere autorizzata, ad esempio una procedura di manutenzione pianificata. | Principale |
| Indirizzo esterno all'interno della rete comunicato con Internet | Un dispositivo di origine definito come parte della rete sta comunicando con gli indirizzi Internet. L'origine non è autorizzata a comunicare con gli indirizzi Internet. | Critico |
| Individuazione del dispositivo campo in modo imprevisto | Un nuovo dispositivo di origine è stato rilevato sulla rete ma non è stato autorizzato. | Principale |
| Modifica del firmware rilevata | Il firmware è stato aggiornato in un dispositivo di origine. Questa attività può essere autorizzata, ad esempio una procedura di manutenzione pianificata. | Principale |
| Versione del firmware modificata | Il firmware è stato aggiornato in un dispositivo di origine. Questa attività può essere autorizzata, ad esempio una procedura di manutenzione pianificata. | Principale |
| Operazione di I/o non autorizzato Foxboro | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Accesso FTP non riuscito | È stato rilevato un tentativo di accesso non riuscito da un dispositivo di origine a un server di destinazione. Questo potrebbe essere il risultato di un errore umano, ma potrebbe indicare anche un tentativo dannoso di compromettere il server o i dati. | Principale |
| Il codice della funzione ha generato un'eccezione non autorizzata | Un dispositivo di origine (slave) ha restituito un'eccezione a un dispositivo di destinazione (Master). | Principale |
| Impostazioni del tipo di messaggio GOOSE | Le impostazioni del messaggio (identificato dall'ID del protocollo) sono state modificate in un dispositivo di origine. | Avviso |
| Versione del firmware Honeywell modificata | Il firmware è stato aggiornato in un dispositivo di origine. Questa attività può essere autorizzata, ad esempio una procedura di manutenzione pianificata. | Principale |
| Comunicazione HTTP non valida | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Accesso a Internet rilevato | Un dispositivo di origine definito come parte della rete sta comunicando con gli indirizzi Internet. L'origine non è autorizzata a comunicare con gli indirizzi Internet. | Principale |
| Versione del firmware Mitsubishi modificata | Il firmware è stato aggiornato in un dispositivo di origine. Questa attività può essere autorizzata, ad esempio una procedura di manutenzione pianificata. | Principale |
| Violazione intervallo indirizzi Modbus | Un dispositivo master ha richiesto l'accesso a un nuovo indirizzo di memoria slave. | Principale |
| Versione del firmware Modbus modificata | Il firmware è stato aggiornato in un dispositivo di origine. Questa attività può essere autorizzata, ad esempio una procedura di manutenzione pianificata. | Principale |
| Nuova attività rilevata-classe CIP | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Nuova attività rilevata-servizio della classe CIP | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Nuova attività rilevata-comando CIP PCCC | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Nuova attività rilevata-simbolo CIP | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Nuova attività rilevata-connessione I/O EtherNet/IP | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Nuova attività rilevata-comando protocollo EtherNet/IP | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Nuova attività rilevata-codice messaggio GSM | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Nuova attività rilevata-codici di comando LonTalk | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Nuova individuazione porta | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Avviso |
| Nuova attività rilevata-variabile di rete LonTalk | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Nuova attività rilevata-richiesta di dati Ovation | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Nuova attività rilevata-comando di lettura/scrittura (gruppo di indici AMS) | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Nuova attività rilevata-comando di lettura/scrittura (offset Indice AMS) | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Nuova attività rilevata-tipo di messaggio DeltaV non autorizzato | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Nuova attività rilevata: operazione ROC DeltaV non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Nuova attività rilevata-tipo di messaggio RPC non autorizzato | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Nuova attività rilevata-chiamata di procedura RPC non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Nuova attività rilevata-uso del comando protocollo AMS | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Nuova attività rilevata-uso del comando Siemens SICA | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Nuova attività rilevata-uso del comando protocollo SuiteLink | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Nuova attività rilevata-uso delle sessioni del protocollo SuiteLink | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Nuova attività rilevata-uso del comando Yokogawa VNetIP | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Nuovo asset rilevato | Un nuovo dispositivo di origine è stato rilevato sulla rete ma non è stato autorizzato. | Principale |
| Nuova configurazione del dispositivo LLDP | Un nuovo dispositivo di origine è stato rilevato sulla rete ma non è stato autorizzato. | Principale |
| Nuova individuazione porta | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Avviso |
| Comando per le PINNe OMRON non autorizzato | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Firmware S7 Plus PLC modificato | Il firmware è stato aggiornato in un dispositivo di origine. Questa attività può essere autorizzata, ad esempio una procedura di manutenzione pianificata. | Principale |
| Impostazioni del tipo di messaggio valori campionati | Le impostazioni del messaggio (identificato dall'ID del protocollo) sono state modificate in un dispositivo di origine. | Avviso |
| Sospetto di un'analisi di integrità non valida | È stata rilevata un'analisi in un dispositivo di origine DNP3 (Outstation). Questa analisi non è stata autorizzata come traffico acquisito sulla rete. | Principale |
| Comando di collegamento non autorizzato del computer Toshiba | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Minorenne |
| Operazione del file Totalflow di ABB non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Operazione di registrazione ABB Totalflow non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Accesso non autorizzato al blocco di dati Siemens S7 | Un dispositivo di origine ha tentato di accedere a una risorsa in un altro dispositivo. Un tentativo di accesso a questa risorsa tra questi due dispositivi non è stato autorizzato come traffico acquisito sulla rete. | Avviso |
| Accesso non autorizzato all'oggetto Siemens S7 Plus | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Accesso non autorizzato al tag Wonderware | Un dispositivo di origine ha tentato di accedere a una risorsa in un altro dispositivo. Un tentativo di accesso a questa risorsa tra questi due dispositivi non è stato autorizzato come traffico acquisito sulla rete. | Principale |
| Accesso a oggetti BACNet non autorizzato | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Route BACNet non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Accesso al database non autorizzato | È stato rilevato un tentativo di accesso tra un client di origine e un server di destinazione. La comunicazione tra questi dispositivi non è stata autorizzata come traffico acquisito sulla rete. | Principale |
| Operazione di database non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Operazione Emerson ROC non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Accesso al file GE SRTP non autorizzato | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Comando del protocollo GE SRTP non autorizzato | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Operazione di memoria di sistema GE SRTP non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Attività HTTP non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Server HTTP non autorizzato | È stata rilevata un'applicazione non autorizzata in un dispositivo di origine. L'applicazione non è stata autorizzata come applicazione appresa sulla rete. | Principale |
| Azione SOAP HTTP non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Agente utente HTTP non autorizzato | È stata rilevata un'applicazione non autorizzata in un dispositivo di origine. L'applicazione non è stata autorizzata come applicazione appresa sulla rete. | Principale |
| È stata rilevata una connettività Internet non autorizzata | Un dispositivo di origine definito come parte della rete sta comunicando con gli indirizzi Internet. L'origine non è autorizzata a comunicare con gli indirizzi Internet. | Critico |
| Comando Mitsubishi MELSEC non autorizzato | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Accesso al programma MMS non autorizzato | Un dispositivo di origine ha tentato di accedere a una risorsa in un altro dispositivo. Un tentativo di accesso a questa risorsa tra questi due dispositivi non è stato autorizzato come traffico acquisito sulla rete. | Principale |
| Servizio MMS non autorizzato | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Connessione multicast/broadcast non autorizzata | È stata rilevata una connessione multicast/broadcast tra un dispositivo di origine e altri dispositivi. La comunicazione multicast/broadcast non è autorizzata. | Critico |
| Query nome non autorizzato | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Attività OPC UA non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Richiesta/risposta OPC UA non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Operazione non autorizzata rilevata da una regola definita dall'utente | Il traffico è stato rilevato tra due dispositivi. Questa attività non è autorizzata in base a una regola di avviso personalizzata definita da un utente. | Principale |
| Lettura configurazione del PLC non autorizzata | Il dispositivo di origine non è definito come un dispositivo di programmazione, ma ha eseguito un'operazione di lettura/scrittura in un controller di destinazione. La programmazione delle modifiche deve essere eseguita solo dai dispositivi di programmazione. È possibile che sia stata installata un'applicazione di programmazione in questo dispositivo. | Avviso |
| Scrittura configurazione PLC non autorizzata | Il dispositivo di origine ha inviato un comando per leggere/scrivere il programma di un controller di destinazione. Questa attività non è stata visualizzata in precedenza. | Principale |
| Caricamento del programma PLC non autorizzato | Il dispositivo di origine ha inviato un comando per leggere/scrivere il programma di un controller di destinazione. Questa attività non è stata visualizzata in precedenza. | Principale |
| Programmazione di PLC non autorizzati | Il dispositivo di origine non è definito come un dispositivo di programmazione, ma ha eseguito un'operazione di lettura/scrittura in un controller di destinazione. La programmazione delle modifiche deve essere eseguita solo dai dispositivi di programmazione. È possibile che sia stata installata un'applicazione di programmazione in questo dispositivo. | Critico |
| Tipo di frame PROFINET non autorizzato | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Comando SAIA S-Bus non autorizzato | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Esecuzione della funzione di controllo per Siemens S7 non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Esecuzione di una funzione definita dall'utente non autorizzata Siemens S7 | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Accesso a blocchi Siemens S7 Plus non autorizzato | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Operazione Siemens S7 Plus non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Accesso SMB non autorizzato | È stato rilevato un tentativo di accesso tra un client di origine e un server di destinazione. La comunicazione tra questi dispositivi non è stata autorizzata come traffico acquisito sulla rete. | Principale |
| Operazione SNMP non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Accesso SSH non autorizzato | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Processo Windows non autorizzato | È stata rilevata un'applicazione non autorizzata in un dispositivo di origine. L'applicazione non è stata autorizzata come applicazione appresa sulla rete. | Principale |
| Servizio Windows non autorizzato | È stata rilevata un'applicazione non autorizzata in un dispositivo di origine. L'applicazione non è stata autorizzata come applicazione appresa sulla rete. | Principale |
| Operazione non autorizzata rilevata da una regola definita dall'utente | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri viola una regola definita dall'utente | Principale |
| Estensione Modbus Schneider non consentita | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Utilizzo non consentito dei tipi ASDU | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Utilizzo non consentito del codice della funzione DNP3 | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |
| Utilizzo non consentito dell'indicazione interna (nell') | Un dispositivo di origine DNP3 (Outstation) ha segnalato un'indicazione interna (nell') che non ha autorizzato il traffico acquisito sulla rete. | Principale |
| Utilizzo non consentito del codice della funzione Modbus | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico acquisito sulla rete. La seguente combinazione non è autorizzata. | Principale |

## <a name="anomaly-engine-alerts"></a>Avvisi motore anomalie

| Titolo | Descrizione | Gravità |
|--|--|--|
| Modello di eccezione anomala in slave | È stato rilevato un numero eccessivo di errori in un dispositivo di origine. Questo potrebbe essere il risultato di un problema operativo. | Minorenne |
| Lunghezza intestazione HTTP anomala | Il dispositivo di origine ha inviato un messaggio anomalo. Questo può indicare un tentativo di attacco al dispositivo di destinazione. | Critico |
| Numero anomalo di parametri nell'intestazione HTTP | Il dispositivo di origine ha inviato un messaggio anomalo. Questo può indicare un tentativo di attacco al dispositivo di destinazione. | Critico |
| Comportamento periodico anomalo nel canale di comunicazione | È stata rilevata una modifica nella frequenza di comunicazione tra i dispositivi di origine e di destinazione. | Minorenne |
| Terminazione anomala delle applicazioni | È stato rilevato un numero eccessivo di comandi stop in un dispositivo di origine. Questo potrebbe essere il risultato di un problema operativo o un tentativo di modificare il dispositivo. | Principale |
| Larghezza di banda del traffico anormale | È stata rilevata una larghezza di banda anomala su un canale La larghezza di banda sembra essere significativamente inferiore o superiore a quella rilevata in precedenza. Per informazioni dettagliate, usare il widget larghezza di banda totale. | Avviso |
| Larghezza di banda del traffico anomala tra i dispositivi | È stata rilevata una larghezza di banda anomala su un canale La larghezza di banda sembra essere significativamente inferiore o superiore a quella rilevata in precedenza. Per informazioni dettagliate, usare il widget larghezza di banda totale. | Avviso |
| Analisi degli indirizzi rilevata | È stato rilevato un dispositivo di origine che analizza i dispositivi di rete. Questo dispositivo non è stato autorizzato come dispositivo di analisi della rete. | Critico |
| Analisi indirizzi ARP rilevata | È stato rilevato un dispositivo di origine che analizza i dispositivi di rete mediante ARP (Address Resolution Protocol). Questo indirizzo del dispositivo non è stato autorizzato come indirizzo di analisi ARP valido. | Critico |
| Analisi indirizzi ARP rilevata | È stato rilevato un dispositivo di origine che analizza i dispositivi di rete mediante ARP (Address Resolution Protocol). Questo indirizzo del dispositivo non è stato autorizzato come indirizzo di analisi ARP valido. | Critico |
| Spoofing ARP | È stata rilevata una quantità anomala di pacchetti nella rete. Questo potrebbe indicare un attacco, ad esempio, un attacco ARP spoofing o ICMP flooding. | Avviso |
| Tentativi di accesso eccessivi | È stato rilevato un dispositivo di origine che esegue un numero eccessivo di tentativi di accesso a un server di destinazione. Potrebbe trattarsi di un attacco di forza bruta. Il server può essere compromesso da un attore malintenzionato. | Critico |
| Numero eccessivo di sessioni | È stato rilevato un dispositivo di origine che esegue un numero eccessivo di tentativi di accesso a un server di destinazione. Potrebbe trattarsi di un attacco di forza bruta. Il server può essere compromesso da un attore malintenzionato. | Critico |
| Frequenza di riavvio eccessiva di un'Outstation | È stato rilevato un numero eccessivo di comandi di riavvio in un dispositivo di origine. Questo potrebbe essere il risultato di un problema operativo o un tentativo di modificare il dispositivo. | Principale |
| Tentativi di accesso SMB eccessivi | È stato rilevato un dispositivo di origine che esegue un numero eccessivo di tentativi di accesso a un server di destinazione. Potrebbe trattarsi di un attacco di forza bruta. Il server può essere compromesso da un attore malintenzionato. | Critico |
| Flood ICMP | È stata rilevata una quantità anomala di pacchetti nella rete. Questo potrebbe indicare un attacco, ad esempio, un attacco ARP spoofing o ICMP flooding. | Avviso |
| Contenuto intestazione HTTP non valido | La richiesta non è stata avviata dal dispositivo di origine. | Critico |
| Canale di comunicazione inattivo | Un canale di comunicazione tra due dispositivi era inattivo durante un periodo in cui l'attività è in genere visibile. Questo potrebbe indicare che il programma che ha generato il traffico è stato modificato o che il programma potrebbe non essere disponibile. Si consiglia di esaminare la configurazione del programma installato e verificare che sia configurato correttamente. | Avviso |
| È stata rilevata un'analisi degli indirizzi lunga durata | È stato rilevato un dispositivo di origine che analizza i dispositivi di rete. Questo dispositivo non è stato autorizzato come dispositivo di analisi della rete. | Critico |
| Rilevato tentativo di indovinare la password | È stato rilevato un dispositivo di origine che esegue un numero eccessivo di tentativi di accesso a un server di destinazione. Potrebbe trattarsi di un attacco di forza bruta. Il server può essere compromesso da un attore malintenzionato. | Critico |
| Analisi PLC rilevata | È stato rilevato un dispositivo di origine che analizza i dispositivi di rete. Questo dispositivo non è stato autorizzato come dispositivo di analisi della rete. | Critico |
| Analisi porta rilevata | È stato rilevato un dispositivo di origine che analizza i dispositivi di rete. Questo dispositivo non è stato autorizzato come dispositivo di analisi della rete. | Critico |
| Lunghezza messaggio imprevista | Il dispositivo di origine ha inviato un messaggio anomalo. Questo può indicare un tentativo di attacco al dispositivo di destinazione. | Critico |
| Traffico imprevisto per la porta standard | Il traffico è stato rilevato in un dispositivo usando una porta riservata per un altro protocollo. | Principale |

## <a name="protocol-violation-engine-alerts"></a>Avvisi del motore di violazione del protocollo

| Titolo | Descrizione | Gravità |
|--|--|--|
| Pacchetti in formato non valido in una singola sessione | Numero anomalo di pacchetti in formato non valido inviati dal dispositivo di origine al dispositivo di destinazione. Questo potrebbe indicare comunicazioni errate o un tentativo di modificare il dispositivo di destinazione. | Principale |
| Aggiornamento del firmware | Un dispositivo di origine ha inviato un comando per aggiornare il firmware in un dispositivo di destinazione. Verificare che gli aggiornamenti recenti di programmazione, configurazione e firmware apportati al dispositivo di destinazione siano validi. | Avviso |
| Il codice della funzione non è supportato da Outstation | Il dispositivo di destinazione ha ricevuto una richiesta non valida. | Principale |
| Messaggio BACNet non valido | La richiesta non è stata avviata dal dispositivo di origine. | Principale |
| Tentativo di connessione non valido sulla porta 0 | Un dispositivo di origine ha tentato di connettersi al dispositivo di destinazione sul numero di porta zero (0). Per TCP, la porta 0 è riservata e non può essere utilizzata. Per UDP, la porta è facoltativa e il valore 0 indica nessuna porta. Non è in genere disponibile alcun servizio in un sistema in ascolto sulla porta 0. Questo evento può indicare un tentativo di attacco al dispositivo di destinazione o indicare che un'applicazione è stata programmata in modo non corretto. | Minorenne |
| Operazione DNP3 non valida | La richiesta non è stata avviata dal dispositivo di origine. | Principale |
| Operazione MODBUS non valida (eccezione generata dal master) | La richiesta non è stata avviata dal dispositivo di origine. | Principale |
| Operazione MODBUS non valida (codice della funzione zero) | La richiesta non è stata avviata dal dispositivo di origine. | Principale |
| Versione del protocollo non valida | La richiesta non è stata avviata dal dispositivo di origine. | Principale |
| Parametro non corretto inviato all'Outstation | Il dispositivo di destinazione ha ricevuto una richiesta non valida. | Principale |
| Avvio di un codice di funzione obsoleto (inizializzazione di dati) | La richiesta non è stata avviata dal dispositivo di origine. | Minorenne |
| Avvio di un codice di funzione obsoleto (Salva configurazione) | La richiesta non è stata avviata dal dispositivo di origine. | Minorenne |
| Il Master ha richiesto una conferma a livello di applicazione | La richiesta non è stata avviata dal dispositivo di origine. | Avviso |
| Eccezione Modbus | Un dispositivo di origine (slave) ha restituito un'eccezione a un dispositivo di destinazione (Master). | Principale |
| Tipo ASDU non valido ricevuto dal dispositivo slave | Il dispositivo di destinazione ha ricevuto una richiesta non valida. | Principale |
| Il dispositivo slave ha ricevuto la trasmissione di un comando non valido | Il dispositivo di destinazione ha ricevuto una richiesta non valida. | Principale |
| Il dispositivo slave ha ricevuto un indirizzo comune non valido | Il dispositivo di destinazione ha ricevuto una richiesta non valida. | Principale |
| Parametro di indirizzo dati non valido ricevuto dal dispositivo slave | Il dispositivo di destinazione ha ricevuto una richiesta non valida. | Principale |
| Parametro valore dati non valido ricevuto dal dispositivo slave | Il dispositivo di destinazione ha ricevuto una richiesta non valida. | Principale |
| Il dispositivo slave ha ricevuto il codice di funzione non valido | Il dispositivo di destinazione ha ricevuto una richiesta non valida. | Principale |
| Indirizzo oggetto informazioni non valido ricevuto dal dispositivo slave | Il dispositivo di destinazione ha ricevuto una richiesta non valida. | Principale |
| Oggetto sconosciuto inviato all'Outstation | Il dispositivo di destinazione ha ricevuto una richiesta non valida. | Principale |
| Utilizzo di un codice di funzione riservato | La richiesta non è stata avviata dal dispositivo di origine. | Principale |
| Utilizzo della formattazione non corretta da Outstation | La richiesta non è stata avviata dal dispositivo di origine. | Avviso |
| Utilizzo dei flag di stato riservati (nell') | Un dispositivo di origine DNP3 (Outstation) ha usato l'indicatore interno riservato 2,6. Si consiglia di controllare la configurazione del dispositivo. | Avviso |

## <a name="malware-engine-alerts"></a>Avvisi del motore malware

| Titolo | Descrizione| Gravità |
|--|--|--|
| Tentativo di connessione a un IP dannoso noto | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Principale |
| Messaggio SMB non valido (impianto backdoor DoublePulsar) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Richiesta di nome di dominio dannoso | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Principale |
| Rilevato file di test malware-EICAR AV riuscita | Un file di test di EICAR AV è stato rilevato nel traffico tra due dispositivi. Il file non è di malware. Viene usato per confermare che il software antivirus è installato correttamente; dimostrare cosa accade quando viene trovato un virus e controllare le procedure e le reazioni interne quando viene rilevato un virus. Il software antivirus dovrebbe rilevare EICAR come se fosse un vero e proprio virus. | Principale |
| Sospetto di malware Conficker | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Principale |
| Sospetto di attacco Denial of Service | Un dispositivo di origine ha tentato di avviare un numero eccessivo di nuove connessioni a un dispositivo di destinazione. Potrebbe trattarsi di un attacco Denial of Service (DOS) contro il dispositivo di destinazione e potrebbe interrompere la funzionalità del dispositivo, influenzare le prestazioni e la disponibilità del servizio o causare errori irreversibili. | Critico |
| Sospetto di attività dannose | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Principale |
| Sospetto di attività dannose (BlackEnergy) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di attività dannose (DarkComet) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di attività dannose (Duqu) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di attività dannose (Flame) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di attività dannose (Havex) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di attività dannose (Karagany) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di attività dannose (Lights) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di attività dannose (query nome) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Principale |
| Sospetto di attività dannose (Edera non elaborata) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di attività dannose (Regin) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di attività dannose (Stuxnet) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di attività dannose (WannaCry) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Principale |
| Sospetto di NotPetya malware-sono stati rilevati parametri SMB non validi | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di NotPetya malware: è stata rilevata una transazione SMB non valida | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di esecuzione di codice in modalità remota con PsExec | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Principale |
| Sospetto della gestione remota dei servizi Windows | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Principale |
| Rilevato file eseguibile sospetto sull'endpoint | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Principale |
| Rilevato traffico sospetto | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |

## <a name="operational-engine-alerts"></a>Avvisi del motore operativo

| Titolo | Descrizione | Gravità |
|--|--|--|
| È stato inviato un comando S7 stop PLC | Il dispositivo di origine ha inviato un comando di arresto a un controller di destinazione. Il controller smetterà di funzionare fino a quando non viene inviato un comando di avvio. | Avviso |
| Operazione BACNet non riuscita | Un server ha restituito un codice di errore. Indica un errore del server o una richiesta non valida da un client. | Principale |
| Stato del dispositivo MMS errato | Un dispositivo di produzione virtuale MMS (VMD) ha inviato un messaggio di stato. Il messaggio indica che è possibile che il server non sia configurato correttamente, parzialmente funzionante o non funzionante. | Principale |
| Modifica della configurazione del dispositivo | È stata rilevata una modifica della configurazione in un dispositivo di origine. | Minorenne |
| Overflow del buffer dell'evento continuo all'Outstation | Un evento di overflow del buffer è stato rilevato in un dispositivo di origine. L'evento può causare il danneggiamento dei dati, arresti anomali del programma o l'esecuzione di codice dannoso. | Principale |
| Reimpostazione del controller | Un dispositivo di origine ha inviato un comando reset a un controller di destinazione. Il controller ha interrotto il funzionamento temporaneo e riavviato automaticamente. | Avviso |
| Arresto del controller | Il dispositivo di origine ha inviato un comando di arresto a un controller di destinazione. Il controller smetterà di funzionare fino a quando non viene inviato un comando di avvio. | Avviso |
| Il dispositivo non è stato in grado di ricevere un indirizzo IP dinamico | Il dispositivo di origine è configurato per la ricezione di un indirizzo IP dinamico da un server DHCP, ma non ha ricevuto un indirizzo. Ciò indica un errore di configurazione nel dispositivo o un errore operativo nel server DHCP. Si consiglia di notificare all'amministratore di rete l'evento imprevisto | Principale |
| Si sospetta che il dispositivo sia disconnesso (non risponde) | Un dispositivo di origine non ha risposto a un comando inviato. Potrebbe essere stato disconnesso al momento dell'invio del comando. | Principale |
| Richiesta di servizio EtherNet/IP CIP non riuscita | Un server ha restituito un codice di errore. Indica un errore del server o una richiesta non valida da un client. | Principale |
| Comando protocollo EtherNet/IP incapsulamento non riuscito | Un server ha restituito un codice di errore. Indica un errore del server o una richiesta non valida da un client. | Principale |
| Overflow del buffer eventi in Outstation | Un evento di overflow del buffer è stato rilevato in un dispositivo di origine. L'evento può causare il danneggiamento dei dati, arresti anomali del programma o l'esecuzione di codice dannoso. | Principale |
| Non è stata eseguita l'operazione di backup prevista | L'attività di backup/trasferimento file prevista non è stata eseguita tra due dispositivi. Questo può indicare errori nel processo di backup/trasferimento di file. | Principale |
| Errore comando SRTP GE | Un server ha restituito un codice di errore. Indica un errore del server o una richiesta non valida da un client. | Principale |
| Il comando GE SRTP stop PLC è stato inviato | Il dispositivo di origine ha inviato un comando di arresto a un controller di destinazione. Il controller smetterà di funzionare fino a quando non viene inviato un comando di avvio. | Avviso |
| Il blocco di controllo GOOSE richiede una configurazione aggiuntiva | Un dispositivo di origine ha inviato un messaggio GOOSE che indica che il dispositivo richiede la richiesta di autorizzazioni. Questo significa che il blocco di controllo GOOSE richiede ulteriori configurazioni e i messaggi GOOSE sono parzialmente o completamente non operativi. | Principale |
| La configurazione del set di dati GOOSE è stata modificata | Un set di dati del messaggio (identificato dal protocollo ID) è stato modificato in un dispositivo di origine. Ciò significa che il dispositivo segnalerà un set di dati diverso per questo messaggio. | Avviso |
| Stato imprevisto del controller Honeywell | Un controller Honeywell ha inviato un messaggio di diagnostica imprevisto che indica una modifica dello stato. | Avviso |
| Errore client HTTP | La richiesta non è stata avviata dal dispositivo di origine. | Avviso |
| Indirizzo IP non valido | Il sistema ha rilevato traffico tra un dispositivo di origine e un indirizzo IP che non è un indirizzo valido. Questo può indicare una configurazione errata o un tentativo di generare traffico non valido. | Minorenne |
| Errore di autenticazione Master-Slave | Il processo di autenticazione tra un dispositivo di origine DNP3 (Master) e un dispositivo di destinazione (Outstation) non è riuscito. | Minorenne |
| Richiesta del servizio MMS non riuscita | Un server ha restituito un codice di errore. Indica un errore del server o una richiesta non valida da un client. | Principale |
| Nessun traffico rilevato sull'interfaccia del sensore | Un sensore ha interrotto il rilevamento del traffico di rete in un'interfaccia di rete. | Critico |
| Il server OPC UA ha generato un evento che richiede l'attenzione dell'utente | Un server OPC UA ha inviato una notifica degli eventi a un client. Questo tipo di evento richiede l'attenzione dell'utente | Principale |
| Richiesta di servizio OPC UA non riuscita | Un server ha restituito un codice di errore. Indica un errore del server o una richiesta non valida da un client. | Principale |
| Outstation riavviato | Un riavvio a freddo è stato rilevato in un dispositivo di origine. Ciò significa che il dispositivo è stato disattivato fisicamente e di nuovo. | Avviso |
| Riavvii di Outstation frequenti | Un numero eccessivo di riavvii a freddo è stato rilevato in un dispositivo di origine. Ciò significa che il dispositivo è stato disattivato fisicamente ed è stato nuovamente riavviato un numero eccessivo di volte. | Minorenne |
| La configurazione di Outstation è cambiata | È stata rilevata una modifica della configurazione in un dispositivo di origine. | Principale |
| È stata rilevata la configurazione danneggiata della stazione | Questo dispositivo di origine DNP3 (Outstation) ha segnalato una configurazione danneggiata. | Principale |
| Comando DCP PROFINET non riuscito | Un server ha restituito un codice di errore. Indica un errore del server o una richiesta non valida da un client. | Principale |
| Ripristino delle impostazioni predefinite del dispositivo PROFINET | Un dispositivo di origine ha inviato un comando di ripristino delle impostazioni predefinite a un dispositivo di destinazione PROFINET. Il comando reset cancella la configurazione del dispositivo PROFINET e ne interrompe l'operazione. | Avviso |
| Operazione RPC non riuscita | Un server ha restituito un codice di errore. Indica un errore del server o una richiesta non valida da un client. | Principale |
| La configurazione del set di dati del messaggio valori campionati è stata modificata | Un set di dati del messaggio (identificato dal protocollo ID) è stato modificato in un dispositivo di origine. Ciò significa che il dispositivo segnalerà un set di dati diverso per questo messaggio. | Avviso |
| Errore irreversibile del dispositivo slave | È stato rilevato un errore di condizione irreversibile in un dispositivo di origine. Questo tipo di errore indica in genere un errore hardware o un errore di esecuzione di un comando specifico. | Principale |
| Sospetto di problemi hardware nell'Outstation | È stato rilevato un errore di condizione irreversibile in un dispositivo di origine. Questo tipo di errore indica in genere un errore hardware o un errore di esecuzione di un comando specifico. | Principale |
| Sospetto di non rispondere al dispositivo MODBUS | Un dispositivo di origine non ha risposto a un comando inviato. Potrebbe essere stato disconnesso al momento dell'invio del comando. | Minorenne |
| Traffico rilevato sull'interfaccia del sensore | Un sensore riprende il rilevamento del traffico di rete in un'interfaccia di rete. | Avviso |

## <a name="next-steps"></a>Passaggi successivi

È possibile [gestire gli eventi di avviso](how-to-manage-the-alert-event.md).
Informazioni su come [inviare le informazioni sugli avvisi](how-to-forward-alert-information-to-partners.md).
