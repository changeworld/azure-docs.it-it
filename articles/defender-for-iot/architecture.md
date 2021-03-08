---
title: Architettura della soluzione senza agenti
description: Scopri di più su Azure Defender per l'architettura e il flusso di informazioni senza agenti.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/25/2021
ms.author: shhazam
ms.openlocfilehash: 1478baa889faf84a53d373863961abc92c1fa34a
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449187"
---
# <a name="azure-defender-for-iot-architecture"></a>Azure Defender per l'architettura dell'it

Questo articolo descrive l'architettura del sistema funzionale del Defender per la soluzione senza agenti. Azure Defender per Internet delle cose offre due set di funzionalità che soddisfano le esigenze dell'ambiente, la soluzione senza agenti per le organizzazioni e la soluzione basata su agenti per i generatori di dispositivi.

## <a name="agentless-solution-for-organizations"></a>Soluzione senza agenti per le organizzazioni
### <a name="defender-for-iot-components"></a>Defender per i componenti dell'it

Defender for Internet si connette sia al cloud di Azure che ai componenti locali. La soluzione è progettata per la scalabilità in ambienti di grandi dimensioni e distribuiti geograficamente con più posizioni remote. Questa soluzione Abilita un'architettura distribuita a più livelli per paese, area, business unit o zona. 

Azure Defender per l'it include i componenti seguenti: 

**Distribuzioni connesse al cloud**

- Azure Defender per macchina virtuale o appliance del sensore Internet
- portale di Azure per l'integrazione e la gestione del cloud in Sentinel di Azure
- Console di gestione locale per la gestione del sito locale
- Un agente di sicurezza incorporato (facoltativo)

**Distribuzioni gapped (offline)**

- Azure Defender per macchina virtuale o appliance del sensore Internet
- Management Console locale per la gestione del sito locale

:::image type="content" source="./media/architecture/defender-iot-security-architecture-v3.png" alt-text="L'architettura di Defender per l'it.":::

### <a name="azure-defender-for-iot-sensors"></a>Azure Defender per sensori Internet

Il Defender per i sensori Internet rileva e monitora continuamente i dispositivi di rete. I sensori raccolgono il traffico di rete ICS usando il monitoraggio passivo (senza agenti) sui dispositivi e i dispositivi. 
 
Progettato in modo specifico per le reti Internet e Internet, la tecnologia senza agente offre una visibilità approfondita dei rischi e dei rischi in pochi minuti di connessione alla rete. Non ha alcun effetto sulle prestazioni della rete e dei dispositivi di rete a causa dell'approccio NTA (Network Traffic Analysis) non invasivo. 
 
Applicando l'analisi comportamentale, le cose e il controllo dei pacchetti di livello 7, è possibile analizzare oltre le tradizionali soluzioni basate su firme per rilevare immediatamente le minacce avanzate (ad esempio malware senza file) in base a attività anomale o non autorizzate. 
  
Defender per i sensori di Internet delle cose si connette a una porta di intervallo o a un TAP di rete e inizia immediatamente a eseguire le operazioni DPI e il traffico di rete. 
 
La raccolta dati, l'elaborazione, l'analisi e gli avvisi avvengono direttamente sul sensore. Questo processo lo rende particolarmente adatto per posizioni con larghezza di banda ridotta o connettività a latenza elevata, perché solo i metadati vengono trasferiti alla console di gestione.

Il sensore include cinque motori di rilevamento di analisi. I motori attivano gli avvisi in base all'analisi del traffico in tempo reale e pre-registrato. Sono disponibili i motori seguenti: 

#### <a name="protocol-violation-detection-engine"></a>Motore di rilevamento delle violazioni del protocollo
Il motore di rilevamento delle violazioni del protocollo identifica l'uso di strutture di pacchetti e valori di campo che violano le specifiche del protocollo ICS, ad esempio, l'eccezione Modbus e l'avvio di avvisi di codice di una funzione obsoleti.

#### <a name="policy-violation-detection-engine"></a>Motore di rilevamento delle violazioni dei criteri
Usando Machine Learning, il motore di rilevamento delle violazioni dei criteri avvisa gli utenti di qualsiasi deviazione rispetto al comportamento della linea di base, ad esempio l'uso non autorizzato di codici di funzione specifici, l'accesso a oggetti specifici o la modifica della configurazione del dispositivo. Ad esempio: la versione del software DeltaV è cambiata e gli avvisi di programmazione del PLC non autorizzati. In particolare, il motore di violazione dei criteri modella le reti ICS come sequenze deterministiche di Stati e transizioni, usando una tecnica brevettata chiamata Industrial finite state Modeling (IFSM). Il motore di rilevamento delle violazioni dei criteri stabilisce una linea di base delle reti ICS, in modo che la piattaforma richieda un periodo di apprendimento più breve per la creazione di una linea di base della rete rispetto ad approcci matematici generici o analisi, che sono state originariamente sviluppate per l'IT anziché le reti OT.

#### <a name="industrial-malware-detection-engine"></a>Motore di rilevamento malware industriale
Il motore di rilevamento di malware industriali identifica i comportamenti che indicano la presenza di malware noto, ad esempio Conficker, energia nera, Havex, WannaCry, NotPetya e Triton. 

#### <a name="anomaly-detection-engine"></a>Motore di rilevamento delle anomalie
Il motore di rilevamento delle anomalie rileva comunicazioni e comportamenti insoliti da computer a computer (M2M). Grazie alla modellazione delle reti ICS come sequenze deterministiche di Stati e transizioni, la piattaforma richiede un periodo di apprendimento più breve rispetto agli approcci matematici generici o alle analisi sviluppate in origine, anziché ad OT. Rileva anche le anomalie più velocemente, con falsi positivi minimi. Gli avvisi del motore di rilevamento anomalie includono un numero eccessivo di tentativi di accesso SMB e avvisi di analisi del PLC.

#### <a name="operational-incident-detection"></a>Rilevamento dell'evento imprevisto operativo
Il rilevamento dell'evento imprevisto operativo rileva i problemi operativi, ad esempio la connettività intermittente, che possono indicare i primi segnali degli errori delle apparecchiature. Ad esempio, il dispositivo viene considerato disconnesso (non risponde) e il comando Siemens S7 stop PLC è stato inviato avvisi.

### <a name="management-consoles"></a>Console di gestione
La gestione di Azure Defender per tutti gli ambienti ibridi viene eseguita tramite due portali di gestione: 
- Console del sensore
- Console di gestione locale
- Portale di Azure

### <a name="sensor-console"></a>Console del sensore
I rilevamenti dei sensori vengono visualizzati nella console del sensore, dove possono essere visualizzati, analizzati e analizzati in una mappa di rete, un inventario dei dispositivi e in un'ampia gamma di report, ad esempio report di valutazione dei rischi, data mining query e vettori di attacco. È anche possibile usare la console di per visualizzare e gestire le minacce rilevate dai motori dei sensori, inviare le informazioni ai sistemi partner, gestire gli utenti e altro ancora.

:::image type="content" source="./media/architecture/sensor-console-v2.png" alt-text="Defender per la console del sensore Internet":::

### <a name="on-premises-management-console"></a>Console di gestione locale
La console di gestione locale consente agli operatori di Security Operations Center (SOC) di gestire e analizzare gli avvisi aggregati da più sensori in un unico dashboard e fornisce una visualizzazione complessiva dell'integrità delle reti OT.

Questa architettura offre una visualizzazione unificata completa della rete a livello di SOC, la gestione degli avvisi ottimizzata e il controllo della sicurezza della rete operativa, assicurando che il processo decisionale e la gestione dei rischi rimangano impeccabili.

Oltre al controllo remoto multi-tenant, di monitoraggio, dell'analisi dei dati e del sensore centralizzato, la console di gestione fornisce strumenti di manutenzione del sistema aggiuntivi (come l'esclusione degli avvisi) e funzionalità di creazione di report completamente personalizzate per ogni appliance remota. Questa architettura supporta sia la gestione locale a livello di sito, a livello di zona che la gestione globale all'interno del SOC.

La console di gestione può essere distribuita per la configurazione a disponibilità elevata, che fornisce una console di backup che riceve periodicamente backup di tutti i file di configurazione necessari per il ripristino. In caso di errore della console primaria, le appliance di gestione del sito locale eseguiranno automaticamente il failover per eseguire la sincronizzazione con la console di backup per mantenere la disponibilità senza interruzioni.

Strettamente integrato con i flussi di lavoro SOC ed eseguire libri, consente di semplificare la definizione delle priorità delle attività di mitigazione e la correlazione tra siti delle minacce.

- Olistica: Riduci la complessità con una singola piattaforma unificata per la gestione dei dispositivi, la gestione di rischi e vulnerabilità e il monitoraggio delle minacce con risposta agli eventi imprevisti.

- Aggregazione e correlazione: Visualizza, aggrega e analizza i dati e gli avvisi raccolti da tutti i siti.

- Controllare tutti i sensori: configurare e monitorare tutti i sensori da un'unica posizione.

   :::image type="content" source="media/updates/alerts-and-site-management-v2.png" alt-text="Gestire tutti gli avvisi e le informazioni.":::

### <a name="azure-portal"></a>Portale di Azure

Il portale di Defender per Internet delle cose in Azure viene usato per semplificare le operazioni seguenti:

- Acquistare appliance della soluzione

- Installare e aggiornare il software

- Caricare i sensori in Azure

- Aggiornare i pacchetti di intelligence per le minacce

## <a name="see-also"></a>Vedi anche

[Domande frequenti su Defender](resources-frequently-asked-questions.md)

[Prerequisiti di sistema](quickstart-system-prerequisites.md)
