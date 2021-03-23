---
title: Configurare i componenti di Azure Industrial Internet
description: In questa esercitazione si apprenderà come modificare i valori predefiniti della configurazione.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 348276a71b2227063374da6455445118fcb00fcf
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787687"
---
# <a name="tutorial-configure-the-industrial-iot-components"></a>Esercitazione: configurare i componenti dell'it industriale

Lo script di distribuzione configura automaticamente tutti i componenti in modo che funzionino reciprocamente usando i valori predefiniti. Tuttavia, le impostazioni dei valori predefiniti possono essere modificate per soddisfare i requisiti.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Personalizzare la configurazione dei componenti


Di seguito sono riportate alcune delle impostazioni di personalizzazione più rilevanti per i componenti di:
* Hub IoT
    * Rete → accesso pubblico: configurare l'accesso a Internet, ad esempio filtri IP
    * Rete → connessioni a endpoint privati: creare un endpoint non accessibile tramite Internet e che può essere usato internamente da altri servizi di Azure o da dispositivi locali, ad esempio tramite una connessione VPN.
    * IoT Edge: gestire la configurazione dei dispositivi perimetrali connessi ai server OPC UA 
* Cosmos DB
    * Replicare i dati a livello globale: configurare la ridondanza dei dati
    * Firewall e reti virtuali: configurare l'accesso a Internet e VNET e filtri IP
    * Connessioni a endpoint privati: creare un endpoint non accessibile tramite Internet 
* Key Vault
    * Segreti: gestire le impostazioni della piattaforma
    * Criteri di accesso: consente di gestire le applicazioni e gli utenti che possono accedere ai dati nel Key Vault e quali operazioni (ad esempio, lettura, scrittura, elenco, eliminazione) sono consentite per l'esecuzione in rete, firewall, VNET e endpoint privati
* Azure Active Directory (AAD) → Registrazioni app
    * <APP_NAME>-Web → autenticazione: Gestisci URI di risposta, ovvero l'elenco di URI che è possibile utilizzare come pagine di destinazione dopo che l'autenticazione ha avuto esito positivo. Lo script di distribuzione potrebbe non essere in grado di configurarlo automaticamente in determinati scenari, ad esempio la mancanza di diritti di amministratore di AAD. Potrebbe essere necessario aggiungere o modificare gli URI quando si modifica il nome host dell'app Web, ad esempio il numero di porta usato da localhost per il debug
* Servizio app
    * Configurazione: gestire le variabili di ambiente che controllano i servizi o l'interfaccia utente
* Macchina virtuale
    * Rete: configurare le reti supportate e le regole del firewall
    * Console seriale: accesso SSH per ottenere informazioni dettagliate o per il debug, ottenere le credenziali dall'output dello script di distribuzione o reimpostare la password
* Hub tutto → IoT Edge
    * Gestire le identità dei dispositivi IoT Edge che possono accedere all'hub, configurare i moduli installati e la configurazione che usano, ad esempio i parametri di codifica per il server di pubblicazione OPC
* Hub tutto → IoT Edge → \<DEVICE> → imposta moduli → OpcPublisher (solo per l'operazione di pubblicazione OPC autonoma)


## <a name="configuration-options"></a>Opzioni di configurazione

|Opzione di configurazione (abbreviazione/nome completo)    |    Descrizione   |
|----------------------------------------------|------------------|
PF/publishfile |Nome del file per la configurazione dei nodi da pubblicare. Se si specifica questa opzione, il server di pubblicazione OPC viene inserito in modalità autonoma.
LF/logfile |Nome file del file di log da utilizzare.
ll/LogLevel |Livello di registrazione da usare (consentito: irreversibile, errore, avviso, informazioni, debug, verbose).
me/MessageEncoding |Codifica della messaggistica per i messaggi in uscita i cui valori sono consentiti: JSON, UADP
mm/messagingmode |Modalità di messaggistica per i messaggi in uscita consentiti: PubSub, esempi
FM/fullfeaturedmessage |Modalità completa per i messaggi (tutti i campi compilati in). Il valore predefinito è' true '. per la compatibilità legacy, usare ' false '
AA/AutoAccept |Il server di pubblicazione è considerato attendibile per tutti i server a cui è connesso
BS/BatchSize |Numero di messaggi di modifica dati OPC UA da memorizzare nella cache per l'invio in batch.
si/iothubsendinterval |Intervallo di invio in batch del trigger in secondi.
MS/iothubmessagesize |Dimensione massima del messaggio (D2C).
OM/maxoutgressmessages |Dimensione massima del buffer in uscita del messaggio (D2C).
diagnosticsinterval |Mostra le informazioni di diagnostica del server di pubblicazione nell'intervallo specificato in secondi (sono necessarie informazioni sul livello di registro). -1 disabilita il log di diagnostica remoto e l'output di diagnostica
lt/logflugtimespan |Intervallo di tempo in secondi durante il quale il file di log deve essere scaricato.
IH/iothubprotocol |Protocollo da usare per la comunicazione con l'hub. Valori consentiti: AmqpOverTcp, AmqpOverWebsocket, MqttOverTcp, MqttOverWebsocket, AMQP, MQTT, TCP, WebSocket, any
HB/HeartbeatInterval |Il server di pubblicazione usa questo valore come valore predefinito in secondi per l'impostazione dell'intervallo di heartbeat dei nodi senza un'impostazione dell'intervallo di heartbeat.
OT/OperationTimeout |Timeout dell'operazione del client OPC UA del server di pubblicazione in ms.
OL/opcmaxstringlen |La lunghezza massima di una stringa OPC può trasmettere/ricevere.
OI/opcsamplinginterval |Valore predefinito in millisecondi per richiedere ai server di campionare i valori
op/opcpublishinginterval |Valore predefinito in millisecondi per l'impostazione dell'intervallo di pubblicazione delle sottoscrizioni rispetto al server OPC UA.
CT/createsessiontimeout |Intervallo con cui l'editore invia messaggi keep-alive in pochi secondi ai server OPC sugli endpoint a cui è connesso.
KT/keepalivethresholt |Specificare il numero di pacchetti Keep-Alive che un server può perdere prima che la sessione venga disconnessa.
TM/trustmyself |Il certificato del server di pubblicazione viene inserito automaticamente nell'archivio attendibile.
at/appcertstoretype |Tipo di archivio certificati dell'applicazione (consentito: directory, X509Store).


## <a name="next-steps"></a>Passaggi successivi
Ora che si è appreso come modificare i valori predefiniti della configurazione, è possibile 

> [!div class="nextstepaction"]
> [Estrarre i dati di IIoT in ADX](tutorial-industrial-iot-azure-data-explorer.md)

> [!div class="nextstepaction"]
> [Visualizza e analizza i dati usando Time Series Insights](tutorial-visualize-data-time-series-insights.md)
