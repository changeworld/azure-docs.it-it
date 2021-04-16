---
title: Tipi di avviso e descrizioni
description: Per le descrizioni degli avvisi IoT, vedere Defender.
ms.date: 4/8/2021
ms.topic: how-to
ms.openlocfilehash: 483563b53a5849b0354986269568bc42b9124cc2
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477996"
---
# <a name="alert-types-and-descriptions"></a>Tipi di avviso e descrizioni

Questo articolo descrive tutti i tipi di avviso che possono essere generati dai motori Defender per IoT. Gli avvisi vengono visualizzati nella finestra Avvisi, che consente di gestire l'evento di avviso. 

## <a name="policy-engine-alerts"></a>Avvisi del motore dei criteri

Gli avvisi del motore dei criteri descrivono le deviazioni rilevate dal comportamento della baseline appresa.

| Titolo  | Descrizione | Gravità |
|--|--|--|
| Uso anomalo degli indirizzi MAC | È stato rilevato un nuovo dispositivo di origine nella rete, ma non è stato autorizzato. | Minorenne |
| Software di Beckhoff modificato | Il firmware è stato aggiornato in un dispositivo di origine. Può trattarsi di un'attività autorizzata, ad esempio una procedura di manutenzione pianificata. | Principale |
| Accesso al database non riuscito | È stato rilevato un tentativo di accesso non riuscito da un dispositivo di origine a un server di destinazione. Questo potrebbe essere il risultato di un errore umano, ma potrebbe anche indicare un tentativo dannoso di compromettere il server o i dati su di esso. | Principale |
| Modifica della versione del firmware ROC emerson | Il firmware è stato aggiornato in un dispositivo di origine. Può trattarsi di un'attività autorizzata, ad esempio una procedura di manutenzione pianificata. | Principale |
| Indirizzo esterno all'interno della rete comunicata con Internet | Un dispositivo di origine definito come parte della rete sta comunicando con gli indirizzi Internet. L'origine non è autorizzata a comunicare con gli indirizzi Internet. | Critico |
| Dispositivo sul campo individuato in modo imprevisto | È stato rilevato un nuovo dispositivo di origine nella rete, ma non è stato autorizzato. | Principale |
| Rilevata modifica del firmware | Il firmware è stato aggiornato in un dispositivo di origine. Può trattarsi di un'attività autorizzata, ad esempio una procedura di manutenzione pianificata. | Principale |
| Versione firmware modificata | Il firmware è stato aggiornato in un dispositivo di origine. Può trattarsi di un'attività autorizzata, ad esempio una procedura di manutenzione pianificata. | Principale |
| Operazione non autorizzata di I/A foxboro | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Accesso FTP non riuscito | È stato rilevato un tentativo di accesso non riuscito da un dispositivo di origine a un server di destinazione. Questo potrebbe essere il risultato di un errore umano, ma potrebbe anche indicare un tentativo dannoso di compromettere il server o i dati su di esso. | Principale |
| Il codice della funzione ha generato un'eccezione non autorizzata | Un dispositivo di origine (slave) ha restituito un'eccezione a un dispositivo di destinazione (master). | Principale |
| Impostazioni del tipo di messaggio GOOSE | Le impostazioni del messaggio (identificate dall'ID protocollo) sono state modificate in un dispositivo di origine. | Avviso |
| Versione del firmware honeywell modificata | Il firmware è stato aggiornato in un dispositivo di origine. Può trattarsi di un'attività autorizzata, ad esempio una procedura di manutenzione pianificata. | Principale |
| Comunicazione HTTP non valida | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Rilevato accesso a Internet | Un dispositivo di origine definito come parte della rete sta comunicando con gli indirizzi Internet. L'origine non è autorizzata a comunicare con gli indirizzi Internet. | Principale |
| Modifica della versione del firmware Mitsubishi | Il firmware è stato aggiornato in un dispositivo di origine. Può trattarsi di un'attività autorizzata, ad esempio una procedura di manutenzione pianificata. | Principale |
| Violazione dell'intervallo di indirizzi modbus | Un dispositivo master ha richiesto l'accesso a un nuovo indirizzo di memoria slave. | Principale |
| Modifica della versione del firmware modbus | Il firmware è stato aggiornato in un dispositivo di origine. Può trattarsi di un'attività autorizzata, ad esempio una procedura di manutenzione pianificata. | Principale |
| Rilevata nuova attività - Classe CIP | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Rilevata nuova attività - Servizio di classe CIP | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Rilevata nuova attività - Comando CIP PCCC | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Rilevata nuova attività - Simbolo CIP | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Rilevata nuova attività - Connessione I/O EtherNet/IP | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Rilevata nuova attività - Comando protocollo EtherNet/IP | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Rilevata nuova attività - Codice messaggio GSM | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Rilevata nuova attività - Codici di comando LonTalk | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Nuova individuazione porte | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Avviso |
| Rilevata nuova attività - Variabile di rete LonTalk | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Rilevata nuova attività - Richiesta di dati di ovazione | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Rilevata nuova attività - Comando lettura/scrittura (gruppo di indici AMS) | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Rilevata nuova attività - Comando lettura/scrittura (offset indice AMS) | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Rilevata nuova attività - Tipo di messaggio DeltaV non autorizzato | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Rilevata nuova attività - Operazione ROC DeltaV non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Rilevata nuova attività - Tipo di messaggio RPC non autorizzato | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Rilevata nuova attività - Chiamata di procedura RPC non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Rilevata nuova attività - Uso del comando del protocollo AMS | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Nuova attività rilevata - Uso del comando SICAM di Siemens | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Rilevata nuova attività - Uso del comando Suitelink Protocol | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Rilevata nuova attività - Uso delle sessioni del protocollo Suitelink | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Rilevata nuova attività con il comando VNetIP di Yokogawa | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Rilevato nuovo asset | Nella rete è stato rilevato un nuovo dispositivo di origine, ma non è stato autorizzato. | Principale |
| Nuova configurazione del dispositivo LLDP | Nella rete è stato rilevato un nuovo dispositivo di origine, ma non è stato autorizzato. | Principale |
| Nuova individuazione porte | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Avviso |
| Comando non autorizzato Omron FINS | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Firmware S7 Plus PLC modificato | Il firmware è stato aggiornato in un dispositivo di origine. Può trattarsi di un'attività autorizzata, ad esempio una procedura di manutenzione pianificata. | Principale |
| Valori campionati - Impostazioni del tipo di messaggio | Le impostazioni del messaggio (identificate dall'ID protocollo) sono state modificate in un dispositivo di origine. | Avviso |
| Sospetto di analisi dell'integrità non valida | È stata rilevata un'analisi in un dispositivo di origine DNP3 (outstation). Questa analisi non è stata autorizzata come traffico appreso nella rete. | Principale |
| Comando toshiba Computer Link Unauthorized | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Minorenne |
| Operazione non autorizzata del file totalflow ABB | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Operazione di registrazione del flusso totale ABB non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Accesso non autorizzato a Siemens S7 Data Block | Un dispositivo di origine ha tentato di accedere a una risorsa in un altro dispositivo. Un tentativo di accesso a questa risorsa tra questi due dispositivi non è stato autorizzato come traffico appreso nella rete. | Avviso |
| Accesso non autorizzato all'oggetto Siemens S7 Plus | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Accesso non autorizzato al tag Wonderware | Un dispositivo di origine ha tentato di accedere a una risorsa in un altro dispositivo. Un tentativo di accesso a questa risorsa tra questi due dispositivi non è stato autorizzato come traffico appreso nella rete. | Principale |
| Accesso non autorizzato agli oggetti BACNet | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Route BACNet non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Accesso al database non autorizzato | È stato rilevato un tentativo di accesso tra un client di origine e un server di destinazione. La comunicazione tra questi dispositivi non è stata autorizzata come traffico appreso nella rete. | Principale |
| Operazione di database non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Operazione ROC di Emerson non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Accesso non autorizzato ai file GE SRTP | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Comando del protocollo GE SRTP non autorizzato | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Operazione di memoria di sistema GE SRTP non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Attività HTTP non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Server HTTP non autorizzato | È stata rilevata un'applicazione non autorizzata in un dispositivo di origine. L'applicazione non è stata autorizzata come applicazione appresa nella rete. | Principale |
| Azione SOAP HTTP non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Agente utente HTTP non autorizzato | È stata rilevata un'applicazione non autorizzata in un dispositivo di origine. L'applicazione non è stata autorizzata come applicazione appresa nella rete. | Principale |
| Rilevata connettività Internet non autorizzata | Un dispositivo di origine definito come parte della rete comunica con gli indirizzi Internet. L'origine non è autorizzata a comunicare con gli indirizzi Internet. | Critico |
| Comando MITsubishi MELSEC non autorizzato | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Accesso al programma MMS non autorizzato | Un dispositivo di origine ha tentato di accedere a una risorsa in un altro dispositivo. Un tentativo di accesso a questa risorsa tra questi due dispositivi non è stato autorizzato come traffico appreso nella rete. | Principale |
| Servizio MMS non autorizzato | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Connessione multicast/broadcast non autorizzata | È stata rilevata una connessione multicast/broadcast tra un dispositivo di origine e altri dispositivi. La comunicazione multicast/broadcast non è autorizzata. | Critico |
| Query sul nome non autorizzato | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Attività OPC UA non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Richiesta/risposta OPC UA non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Operazione non autorizzata rilevata da una regola definita dall'utente | È stato rilevato traffico tra due dispositivi. Questa attività non è autorizzata in base a una regola di avviso personalizzata definita da un utente. | Principale |
| Lettura configurazione PLC non autorizzata | Il dispositivo di origine non è definito come un dispositivo di programmazione, ma ha eseguito un'operazione di lettura/scrittura in un controller di destinazione. Le modifiche di programmazione devono essere eseguite solo dai dispositivi di programmazione. È possibile che nel dispositivo sia stata installata un'applicazione di programmazione. | Avviso |
| Scrittura configurazione PLC non autorizzata | Il dispositivo di origine ha inviato un comando per leggere/scrivere il programma di un controller di destinazione. Questa attività non è stata vista in precedenza. | Principale |
| Caricamento del programma PLC non autorizzato | Il dispositivo di origine ha inviato un comando per leggere/scrivere il programma di un controller di destinazione. Questa attività non è stata vista in precedenza. | Principale |
| Programmazione PLC non autorizzata | Il dispositivo di origine non è definito come dispositivo di programmazione, ma ha eseguito un'operazione di lettura/scrittura in un controller di destinazione. Le modifiche di programmazione devono essere eseguite solo dai dispositivi di programmazione. È possibile che nel dispositivo sia stata installata un'applicazione di programmazione. | Critico |
| Tipo di frame Non autorizzato | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Comando S-Bus SAIA non autorizzato | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Funzione di controllo siemens S7 non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Esecuzione non autorizzata di Siemens S7 della funzione definita dall'utente | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Accesso non autorizzato a Siemens S7 Plus Block | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Operazione Siemens S7 Plus non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Accesso SMB non autorizzato | È stato rilevato un tentativo di accesso tra un client di origine e un server di destinazione. La comunicazione tra questi dispositivi non è stata autorizzata come traffico appreso nella rete. | Principale |
| Operazione SNMP non autorizzata | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Accesso SSH non autorizzato | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Processo Windows non autorizzato | È stata rilevata un'applicazione non autorizzata in un dispositivo di origine. L'applicazione non è stata autorizzata come applicazione appresa nella rete. | Principale |
| Servizio Windows non autorizzato | È stata rilevata un'applicazione non autorizzata in un dispositivo di origine. L'applicazione non è stata autorizzata come applicazione appresa nella rete. | Principale |
| Operazione non autorizzata rilevata da una regola definita dall'utente | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri viola una regola definita dall'utente | Principale |
| Estensione elettrica Modbus Schneider Nonpermitted | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Utilizzo non eseguito dei tipi asdu | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Utilizzo non eseguito del codice della funzione DNP3 | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |
| Utilizzo non eseguito dell'indicazione interna (IIN) | Un dispositivo di origine DNP3 (outstation) ha segnalato un'indicazione interna (IIN) non autorizzata come traffico appreso nella rete. | Principale |
| Utilizzo non eseguito del codice della funzione Modbus | Sono stati rilevati nuovi parametri di traffico. Questa combinazione di parametri non è stata autorizzata come traffico appreso nella rete. La combinazione seguente non è autorizzata. | Principale |

## <a name="anomaly-engine-alerts"></a>Avvisi del motore delle anomalie

Gli avvisi del motore di anomalie descrivono le anomalie rilevate nell'attività di rete.

| Titolo | Descrizione | Gravità |
|--|--|--|
| Modello di eccezione anomala in slave | È stato rilevato un numero eccessivo di errori in un dispositivo di origine. Questo può essere il risultato di un problema operativo. | Minorenne |
| Lunghezza intestazione HTTP anomala | Il dispositivo di origine ha inviato un messaggio anomalo. Ciò potrebbe indicare un tentativo di attacco al dispositivo di destinazione. | Critico |
| Numero anomalo di parametri nell'intestazione HTTP | Il dispositivo di origine ha inviato un messaggio anomalo. Ciò potrebbe indicare un tentativo di attacco al dispositivo di destinazione. | Critico |
| Comportamento periodico anomalo nel canale di comunicazione | È stata rilevata una modifica della frequenza di comunicazione tra i dispositivi di origine e di destinazione. | Minorenne |
| Terminazione anomala delle applicazioni | È stato rilevato un numero eccessivo di comandi di arresto in un dispositivo di origine. Ciò può essere il risultato di un problema operativo o di un tentativo di modificare il dispositivo. | Principale |
| Larghezza di banda del traffico anomala | È stata rilevata una larghezza di banda anomala in un canale. La larghezza di banda sembra essere notevolmente inferiore o superiore a quella rilevata in precedenza. Per informazioni dettagliate, usare il widget Larghezza di banda totale. | Avviso |
| Larghezza di banda del traffico anomala tra i dispositivi | È stata rilevata una larghezza di banda anomala in un canale. La larghezza di banda sembra essere significativamente inferiore/superiore a quella rilevata in precedenza. Per informazioni dettagliate, usare il widget Larghezza di banda totale. | Avviso |
| Rilevata analisi degli indirizzi | È stato rilevato un dispositivo di origine che esegue l'analisi dei dispositivi di rete. Questo dispositivo non è stato autorizzato come dispositivo di analisi di rete. | Critico |
| Rilevata analisi degli indirizzi ARP | È stato rilevato un dispositivo di origine che esegue l'analisi dei dispositivi di rete usando il protocollo ARP (Address Resolution Protocol). Questo indirizzo del dispositivo non è stato autorizzato come indirizzo di analisi ARP valido. | Critico |
| Rilevata analisi degli indirizzi ARP | È stato rilevato un dispositivo di origine che esegue l'analisi dei dispositivi di rete usando il protocollo ARP (Address Resolution Protocol). Questo indirizzo del dispositivo non è stato autorizzato come indirizzo di analisi ARP valido. | Critico |
| ARP Spoofing | È stata rilevata una quantità anomala di pacchetti nella rete. Ciò potrebbe indicare un attacco, ad esempio, uno spoofing ARP o un attacco di flooding ICMP. | Avviso |
| Tentativi di accesso eccessivi | Un dispositivo di origine ha eseguito un numero eccessivo di tentativi di accesso a un server di destinazione. Potrebbe trattarsi di un attacco di forza bruta. Il server potrebbe essere compromesso da un attore malintenzionato. | Critico |
| Numero eccessivo di sessioni | Un dispositivo di origine ha eseguito un numero eccessivo di tentativi di accesso a un server di destinazione. Potrebbe trattarsi di un attacco di forza bruta. Il server potrebbe essere compromesso da un attore malintenzionato. | Critico |
| Frequenza di riavvio eccessiva di un'outstation | È stato rilevato un numero eccessivo di comandi di riavvio in un dispositivo di origine. Ciò può essere il risultato di un problema operativo o di un tentativo di modificare il dispositivo. | Principale |
| Numero eccessivo di tentativi di accesso SMB | Un dispositivo di origine ha eseguito un numero eccessivo di tentativi di accesso a un server di destinazione. Potrebbe trattarsi di un attacco di forza bruta. Il server potrebbe essere compromesso da un attore malintenzionato. | Critico |
| ICMP Flooding | È stata rilevata una quantità anomala di pacchetti nella rete. Ciò potrebbe indicare un attacco, ad esempio, uno spoofing ARP o un attacco di flooding ICMP. | Avviso |
| Contenuto dell'intestazione HTTP non valido | Il dispositivo di origine ha avviato una richiesta non valida. | Critico |
| Canale di comunicazione inattivo | Un canale di comunicazione tra due dispositivi era inattivo durante un periodo in cui l'attività è in genere visibile. Ciò potrebbe indicare che il programma che genera questo traffico è stato modificato o che il programma potrebbe non essere disponibile. È consigliabile esaminare la configurazione del programma installato e verificare che sia configurato correttamente. | Avviso |
| Rilevata analisi degli indirizzi di lunga durata | È stato rilevato un dispositivo di origine che esegue l'analisi dei dispositivi di rete. Questo dispositivo non è stato autorizzato come dispositivo di analisi di rete. | Critico |
| Rilevato tentativo di indovinare la password | Un dispositivo di origine ha eseguito un numero eccessivo di tentativi di accesso a un server di destinazione. Potrebbe trattarsi di un attacco di forza bruta. Il server potrebbe essere compromesso da un attore malintenzionato. | Critico |
| Rilevata analisi PLC | È stato rilevato un dispositivo di origine che esegue l'analisi dei dispositivi di rete. Questo dispositivo non è stato autorizzato come dispositivo di analisi di rete. | Critico |
| Rilevata analisi delle porte | È stato rilevato un dispositivo di origine che esegue l'analisi dei dispositivi di rete. Questo dispositivo non è stato autorizzato come dispositivo di analisi di rete. | Critico |
| Lunghezza del messaggio imprevista | Il dispositivo di origine ha inviato un messaggio anomalo. Ciò potrebbe indicare un tentativo di attacco al dispositivo di destinazione. | Critico |
| Traffico imprevisto per la porta standard | Il traffico è stato rilevato in un dispositivo usando una porta riservata a un altro protocollo. | Principale |

## <a name="protocol-violation-engine-alerts"></a>Avvisi del motore di violazione del protocollo

Gli avvisi del motore di protocollo descrivono le deviazioni rilevate nella struttura dei pacchetti o i valori dei campi rispetto alle specifiche del protocollo.

| Titolo | Descrizione | Gravità |
|--|--|--|
| Numero eccessivo di pacchetti in formato non valido in una singola sessione | Numero anomalo di pacchetti in formato non valido inviati dal dispositivo di origine al dispositivo di destinazione. Ciò potrebbe indicare comunicazioni erre o un tentativo di modificare il dispositivo di destinazione. | Principale |
| Aggiornamento del firmware | Un dispositivo di origine ha inviato un comando per aggiornare il firmware in un dispositivo di destinazione. Verificare che gli aggiornamenti recenti della programmazione, della configurazione e del firmware eseciti al dispositivo di destinazione siano validi. | Avviso |
| Codice funzione non supportato da Outstation | Il dispositivo di destinazione ha ricevuto una richiesta non valida. | Principale |
| Messaggio BACNet non valido | Il dispositivo di origine ha avviato una richiesta non valida. | Principale |
| Tentativo di connessione non valido sulla porta 0 | Un dispositivo di origine ha tentato di connettersi al dispositivo di destinazione sul numero di porta zero (0). Per TCP, la porta 0 è riservata e non può essere usata. Per UDP, la porta è facoltativa e il valore 0 indica nessuna porta. In genere non esiste alcun servizio in un sistema in ascolto sulla porta 0. Questo evento può indicare un tentativo di attacco al dispositivo di destinazione o che un'applicazione è stata programmata in modo non corretto. | Minorenne |
| Operazione DNP3 non valida | Il dispositivo di origine ha avviato una richiesta non valida. | Principale |
| Operazione MODBUS non valida (eccezione generata dal master) | Il dispositivo di origine ha avviato una richiesta non valida. | Principale |
| Operazione MODBUS non valida (codice funzione zero) | Il dispositivo di origine ha avviato una richiesta non valida. | Principale |
| Versione del protocollo non valida | Il dispositivo di origine ha avviato una richiesta non valida. | Principale |
| Parametro non corretto inviato all'outstation | Il dispositivo di destinazione ha ricevuto una richiesta non valida. | Principale |
| Avvio di un codice di funzione obsoleto (inizializzare i dati) | Il dispositivo di origine ha avviato una richiesta non valida. | Minorenne |
| Avvio di un codice di funzione obsoleto (salva configurazione) | Il dispositivo di origine ha avviato una richiesta non valida. | Minorenne |
| Il master ha richiesto una conferma del livello applicazione | Il dispositivo di origine ha avviato una richiesta non valida. | Avviso |
| Eccezione modbus | Un dispositivo di origine (slave) ha restituito un'eccezione a un dispositivo di destinazione (master). | Principale |
| Il dispositivo slave ha ricevuto un tipo di ASDU non valido | Il dispositivo di destinazione ha ricevuto una richiesta non valida. | Principale |
| Il dispositivo slave ha ricevuto una causa di trasmissione del comando non valida | Il dispositivo di destinazione ha ricevuto una richiesta non valida. | Principale |
| Il dispositivo slave ha ricevuto un indirizzo comune non valido | Il dispositivo di destinazione ha ricevuto una richiesta non valida. | Principale |
| Il dispositivo slave ha ricevuto il parametro dell'indirizzo dati non valido | Il dispositivo di destinazione ha ricevuto una richiesta non valida. | Principale |
| Il dispositivo slave ha ricevuto un parametro di valore di dati non valido | Il dispositivo di destinazione ha ricevuto una richiesta non valida. | Principale |
| Il dispositivo slave ha ricevuto codice di funzione non valido | Il dispositivo di destinazione ha ricevuto una richiesta non valida. | Principale |
| Il dispositivo slave ha ricevuto l'indirizzo dell'oggetto informazioni non valido | Il dispositivo di destinazione ha ricevuto una richiesta non valida. | Principale |
| Oggetto sconosciuto inviato all'outstation | Il dispositivo di destinazione ha ricevuto una richiesta non valida. | Principale |
| Utilizzo di un codice di funzione riservata | Il dispositivo di origine ha avviato una richiesta non valida. | Principale |
| Utilizzo di una formattazione non corretta da parte dell'outstation | Il dispositivo di origine ha avviato una richiesta non valida. | Avviso |
| Utilizzo dei flag di stato riservati (IIN) | Un dispositivo di origine DNP3 (outstation) ha usato l'indicatore interno 2.6 riservato. È consigliabile controllare la configurazione del dispositivo. | Avviso |

## <a name="malware-engine-alerts"></a>Avvisi del motore di malware

Gli avvisi del motore di malware descrivono le attività di rete dannose rilevate.

| Titolo | Descrizione| Gravità |
|--|--|--|
| Tentativo di connessione a IP dannoso noto | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Principale |
| Messaggio SMB non valido (impianto backdoor DoublePulsar) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Richiesta di nome di dominio dannoso | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Principale |
| Rilevato file di test del malware - EICAR AV Success | È stato rilevato un file di test av EICAR nel traffico tra due dispositivi. Il file non è malware. Viene usato per verificare che il software antivirus sia installato correttamente. illustrare cosa accade quando viene trovato un virus e controllare le procedure e le reazioni interne quando viene trovato un virus. Il software antivirus dovrebbe rilevare EICAR come se fosse un virus reale. | Principale |
| Sospetto di malware Conficker | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Principale |
| Sospetto di attacco Denial of Service | Un dispositivo di origine ha tentato di avviare un numero eccessivo di nuove connessioni a un dispositivo di destinazione. Potrebbe trattarsi di un attacco Denial Of Service (DOS) contro il dispositivo di destinazione e potrebbe interrompere la funzionalità del dispositivo, influire sulle prestazioni e sulla disponibilità del servizio o causare errori irreversibili. | Critico |
| Sospetto di attività dannose | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Principale |
| Sospetto di attività dannosa (Black Black) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di attività dannose (DarkComet) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di attività dannosa (Duqu) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di attività dannosa (Ata) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di attività dannose (Havex) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di attività dannosa (Conflittogany) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di attività dannosa (LightsOut) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di attività dannosa (query nome) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Principale |
| Sospetto di attività dannosa (Poison Ivy) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di attività dannosa (Regin) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di attività dannose (Stuxnet) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di attività dannose (WannaCry) | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Principale |
| Sospetto di malware NotPetya : rilevati parametri SMB non validi | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di malware NotPetya - Rilevata transazione SMB non valida | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |
| Sospetto di esecuzione di codice remoto con PsExec | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Principale |
| Sospetto di gestione remota dei servizi Windows | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Principale |
| Rilevato file eseguibile sospetto nell'endpoint | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Principale |
| Rilevato traffico sospetto | È stata rilevata un'attività di rete sospetta. Questa attività può essere associata a un attacco che sfrutta un metodo usato da malware noto. | Critico |

## <a name="operational-engine-alerts"></a>Avvisi del motore operativo

Gli avvisi del motore operativo descrivono gli eventi imprevisti operativi rilevati o le entità che non funzionano correttamente.

| Titolo | Descrizione | Gravità |
|--|--|--|
| È stato inviato un comando PLC di arresto S7 | Il dispositivo di origine ha inviato un comando di arresto a un controller di destinazione. Il controller smette di operare fino a quando non viene inviato un comando di avvio. | Avviso |
| Operazione BACNet non riuscita | Un server ha restituito un codice di errore. Indica un errore del server o una richiesta non valida da parte di un client. | Principale |
| Stato del dispositivo MMS non valido | Un dispositivo VMD (Virtual Manufacturing Device) MMS ha inviato un messaggio di stato. Il messaggio indica che il server potrebbe non essere configurato correttamente, parzialmente operativo o non operativo. | Principale |
| Modifica della configurazione del dispositivo | È stata rilevata una modifica alla configurazione in un dispositivo di origine. | Minorenne |
| Overflow continuo del buffer degli eventi in fase di outstation | È stato rilevato un evento di overflow del buffer in un dispositivo di origine. L'evento può causare il danneggiamento dei dati, arresti anomali del programma o l'esecuzione di codice dannoso. | Principale |
| Reimpostazione del controller | Un dispositivo di origine ha inviato un comando di reimpostazione a un controller di destinazione. Il controller ha arrestato temporaneamente il funzionamento e viene avviato di nuovo automaticamente. | Avviso |
| Arresto del controller | Il dispositivo di origine ha inviato un comando di arresto a un controller di destinazione. Il controller smette di operare fino a quando non viene inviato un comando di avvio. | Avviso |
| Il dispositivo non è riuscito a ricevere un indirizzo IP dinamico | Il dispositivo di origine è configurato per ricevere un indirizzo IP dinamico da un server DHCP, ma non ha ricevuto un indirizzo. Indica un errore di configurazione nel dispositivo o un errore operativo nel server DHCP. È consigliabile informare l'amministratore di rete dell'evento imprevisto | Principale |
| Si sospetta che il dispositivo sia disconnesso (non risponde) | Un dispositivo di origine non ha risposto a un comando inviato. È possibile che sia stata disconnessa al momento dell'invio del comando. | Principale |
| Richiesta di servizio CIP EtherNet/IP non riuscita | Un server ha restituito un codice di errore. Indica un errore del server o una richiesta non valida da parte di un client. | Principale |
| Comando del protocollo di incapsulamento EtherNet/IP non riuscito | Un server ha restituito un codice di errore. Indica un errore del server o una richiesta non valida da parte di un client. | Principale |
| Overflow del buffer eventi in outstation | È stato rilevato un evento di overflow del buffer in un dispositivo di origine. L'evento può causare il danneggiamento dei dati, arresti anomali del programma o l'esecuzione di codice dannoso. | Principale |
| L'operazione di backup prevista non è stata eseguita | L'attività di backup/trasferimento di file prevista non si è verificata tra due dispositivi. Ciò può indicare errori nel processo di backup/trasferimento di file. | Principale |
| Errore del comando GE SRTP | Un server ha restituito un codice di errore. Indica un errore del server o una richiesta non valida da parte di un client. | Principale |
| Il comando GE SRTP Stop PLC è stato inviato | Il dispositivo di origine ha inviato un comando di arresto a un controller di destinazione. Il controller smette di operare fino all'invio di un comando di avvio. | Avviso |
| Il blocco di controllo GOOSE richiede un'ulteriore configurazione | Un dispositivo di origine ha inviato un messaggio GOOSE che indica che il dispositivo deve essere commissionato. Questo significa che il blocco di controllo GOOSE richiede un'ulteriore configurazione e i messaggi GOOSE sono parzialmente o completamente non operativi. | Principale |
| La configurazione del set di dati GOOSE è stata modificata | Un set di dati di messaggio (identificato dall'ID protocollo) è stato modificato in un dispositivo di origine. Questo significa che il dispositivo segnala un set di dati diverso per questo messaggio. | Avviso |
| Stato imprevisto del controller Honeywell | Un controller Honeywell ha inviato un messaggio di diagnostica imprevisto che indica una modifica dello stato. | Avviso |
| Errore del client HTTP | Il dispositivo di origine ha avviato una richiesta non valida. | Avviso |
| Indirizzo IP non valido | Il sistema ha rilevato il traffico tra un dispositivo di origine e un indirizzo IP che è un indirizzo non valido. Ciò potrebbe indicare una configurazione errata o un tentativo di generare traffico non valido. | Minorenne |
| Master-Slave errore di autenticazione | Il processo di autenticazione tra un dispositivo di origine (master) DNP3 e un dispositivo di destinazione (outstation) non è riuscito. | Minorenne |
| Richiesta di servizio MMS non riuscita | Un server ha restituito un codice di errore. Indica un errore del server o una richiesta non valida da parte di un client. | Principale |
| Nessun traffico rilevato nell'interfaccia del sensore | Un sensore ha smesso di rilevare il traffico di rete su un'interfaccia di rete. | Critico |
| OPC UA Server ha generato un evento che richiede l'attenzione dell'utente | Un OPC UA server ha inviato una notifica degli eventi a un client. Questo tipo di evento richiede l'attenzione dell'utente | Principale |
| OPC UA richiesta di servizio non riuscita | Un server ha restituito un codice di errore. Indica un errore del server o una richiesta non valida da parte di un client. | Principale |
| Outstation Restarted | È stato rilevato un riavvio a freddo in un dispositivo di origine. Ciò significa che il dispositivo è stato spento fisicamente e di nuovo acceso. | Avviso |
| Riavvii di outstation frequenti | È stato rilevato un numero eccessivo di riavvii a freddo in un dispositivo di origine. Ciò significa che il dispositivo è stato spento e ri spento fisicamente un numero eccessivo di volte. | Minorenne |
| Modifica della configurazione di Outstation | È stata rilevata una modifica alla configurazione in un dispositivo di origine. | Principale |
| Rilevata configurazione danneggiata dell'outstation | Questo dispositivo di origine DNP3 (outstation) ha segnalato una configurazione danneggiata. | Principale |
| Comando DCP Di Proinet non riuscito | Un server ha restituito un codice di errore. Indica un errore del server o una richiesta non valida da parte di un client. | Principale |
| Reimpostazione di Profinet Device Factory | Un dispositivo di origine ha inviato un comando di reimpostazione della factory a un dispositivo di destinazione Profinet. Il comando reset cancella le configurazioni dei dispositivi Profinet e ne arresta il funzionamento. | Avviso |
| Operazione RPC non riuscita | Un server ha restituito un codice di errore. Indica un errore del server o una richiesta non valida da parte di un client. | Principale |
| La configurazione del set di dati dei messaggi dei valori campionati è stata modificata | Un set di dati di messaggio (identificato dall'ID protocollo) è stato modificato in un dispositivo di origine. Questo significa che il dispositivo segnala un set di dati diverso per questo messaggio. | Avviso |
| Errore irreversibile del dispositivo slave | È stato rilevato un errore di condizione irreversibile in un dispositivo di origine. Questo tipo di errore indica in genere un errore hardware o un errore di esecuzione di un comando specifico. | Principale |
| Sospetto di problemi hardware in outstation | È stato rilevato un errore di condizione irreversibile in un dispositivo di origine. Questo tipo di errore indica in genere un errore hardware o un errore di esecuzione di un comando specifico. | Principale |
| Sospetto di un dispositivo MODBUS non risponde | Un dispositivo di origine non ha risposto a un comando inviato. Potrebbe essere stato disconnesso quando è stato inviato il comando. | Minorenne |
| Traffico rilevato nell'interfaccia del sensore | Un sensore ha ripreso a rilevare il traffico di rete su un'interfaccia di rete. | Avviso |

## <a name="next-steps"></a>Passaggi successivi

È possibile gestire [gli eventi di avviso](how-to-manage-the-alert-event.md).
Informazioni su come [inoltrare le informazioni sugli avvisi.](how-to-forward-alert-information-to-partners.md)
