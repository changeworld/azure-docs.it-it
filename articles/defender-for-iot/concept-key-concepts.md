---
title: Vantaggi principali
description: Informazioni su Defender di base per i concetti relativi alle cose.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/13/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: cb984ca1a74d8b3838b857f2f5679264d1445187
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99508609"
---
# <a name="basic-concepts"></a>Concetti fondamentali 

Questo articolo descrive i vantaggi principali di Azure Defender per l'it.

## <a name="rapid-non-invasive-deployment-and-passive-monitoring"></a>Distribuzione rapida non invasiva e monitoraggio passivo

Defender per i sensori di Internet delle cose si connette alle porte dell'intervallo di commutazione (mirror) e ai rubinetti di rete e inizia immediatamente a raccogliere il traffico di rete ICS tramite il monitoraggio passivo (senza agenti) L'ispezione Deep Packet (DPI) viene usata per dissezionare il traffico da apparecchiature di rete di controllo seriale e Ethernet. Defender for Internet ha un effetto zero sulle reti OT perché non è inserito nel percorso dei dati e non esegue l'analisi attiva dei dispositivi OT. 

Per fornire snapshot istantanei delle informazioni dettagliate sui dispositivi Windows, è possibile configurare Defender per il sensore Internet per integrare il monitoraggio passivo con un componente attivo facoltativo. Questo componente usa i comandi sicuri e approvati dal fornitore per eseguire query sui dispositivi Windows per i dettagli del dispositivo, come spesso o con una frequenza minore.

## <a name="embedded-knowledge-of-ics-protocols-devices-and-applications"></a>Conoscenza incorporata di protocolli, dispositivi e applicazioni ICS

Solo DPI non è sufficiente per identificare le anomalie del protocollo e identificare il dispositivo a un livello granulare. La protezione per il sensore Internet è indirizzata ad alcuni degli ambienti più grandi e complessi. Più di 1.300 di reti OT sono state analizzate fino alla data, in tutti i settori industriali.

## <a name="analytics-and-self-learning-engines"></a>Motore di analisi e self-learning

I motori identificano i problemi di sicurezza tramite il monitoraggio continuo e cinque motori di analisi che incorporano l'apprendimento automatico per eliminare la necessità di aggiornare le firme o definire regole. I motori usano analisi del comportamento specifiche di ICS e data science per analizzare continuamente il traffico di rete per rilevare eventuali anomalie. I cinque motori sono:

- **Rilevamento della violazione del protocollo**: identifica l'uso di strutture di pacchetti e valori di campo che violano le specifiche del protocollo ICS.

- **Rilevamento delle violazioni dei criteri**: identifica le violazioni dei criteri, ad esempio l'uso non autorizzato di codici di funzione, l'accesso a oggetti specifici o le modifiche alla configurazione del dispositivo.

- **Rilevamento di malware industriali**: identifica i comportamenti che indicano la presenza di malware noto, ad esempio Conficker, energia nera, Havex, WannaCry e NotPetya.

- **Rilevamento anomalie**: rileva comunicazioni e comportamenti insoliti da computer a computer (M2M). Grazie alla modellazione delle reti ICS come sequenze deterministiche di Stati e transizioni, il motore usa una tecnica brevettata chiamata Industrial finite state Modeling (IFSM). La soluzione richiede un periodo di apprendimento più breve rispetto ad approcci matematici o analitici generici, che sono stati originariamente sviluppati per esso anziché come OT. Rileva anche le anomalie più velocemente, con falsi positivi minimi.

- **Rilevamento di eventi imprevisti operativi**: identifica i problemi operativi, ad esempio la connettività intermittente, che possono indicare i primi segnali degli errori delle apparecchiature.

## <a name="network-traffic-analysis-for-risk-and-vulnerability-assessment"></a>Analisi del traffico di rete per la valutazione del rischio e della vulnerabilità

Unico nel settore, Defender for Internet usa algoritmi NTA (Network Traffic Analysis) proprietari per identificare passivamente tutte le vulnerabilità di rete e di endpoint, ad esempio:

- Connessioni di accesso remoto non autorizzate
- Dispositivi non autorizzati o non documentati
- Autenticazione vulnerabile
- Dispositivi vulnerabili (basati su CVEs senza patch)
- Bridge non autorizzati tra subnet
- Regole firewall vulnerabili

## <a name="data-mining-for-investigations-forensics-and-threat-hunting"></a>Data mining per indagini, analisi forensi e caccia alle minacce

La piattaforma fornisce un'interfaccia di data mining intuitiva per la ricerca granulare del traffico cronologico in tutte le dimensioni pertinenti. Gli esempi includono il periodo di tempo, l'indirizzo IP, l'indirizzo MAC e le porte. È anche possibile eseguire query specifiche del protocollo basate su codici di funzione, servizi di protocollo e moduli. Per un'ulteriore analisi di drill-down sono disponibili PCAPs per la massima fedeltà.

## <a name="sensor-cloud-management-mode"></a>Modalità di gestione cloud dei sensori

La modalità di gestione del cloud dei sensori determina dove vengono visualizzati il dispositivo, l'avviso e altre informazioni rilevate dal sensore.

Per i **sensori connessi al cloud**, le informazioni rilevate dal sensore vengono visualizzate nella console del sensore. Le informazioni sugli avvisi vengono fornite tramite un hub Internet e possono essere condivise con altri servizi di Azure, ad esempio Azure Sentinel.

Per i **sensori connessi localmente**, le informazioni rilevate dal sensore vengono visualizzate nella console del sensore. Le informazioni rilevate vengono inoltre condivise con la console di gestione locale, se è connessa al sensore.

## <a name="air-gapped-networks"></a>Reti gapped

Se si lavora in un ambiente gapped, la console di gestione locale in Defender for Internet offre una visualizzazione in tempo reale delle chiavi principali e degli indicatori di rischio e degli avvisi in tutte le funzionalità. Strettamente integrato con i flussi di lavoro SOC e manuali operativi, semplifica la definizione delle priorità delle attività di mitigazione e la correlazione tra siti delle minacce.  

Defender per le cose offre una visualizzazione consolidata di tutti i dispositivi. Fornisce inoltre informazioni critiche sui dispositivi, ad esempio il tipo (PLC, RTU, controller di dominio e altro ancora), il produttore, il modello e il livello di revisione del firmware, nonché le informazioni sugli avvisi.  

Defender per le cose consente la gestione efficace di più distribuzioni e una visualizzazione unificata completa della rete. Defender for Internet viene ottimizzato per la gestione degli avvisi e il controllo della sicurezza della rete operativa.

La console di gestione locale è una piattaforma di amministrazione basata sul Web che consente di monitorare e controllare le attività delle installazioni dei sensori globali. Oltre a gestire i dati ricevuti dai sensori distribuiti, la console di gestione locale integra facilmente i dati di diverse risorse aziendali: CMDBs, DNS, firewall, API Web e altro ancora.

:::image type="content" source="media/concept-air-gapped-networks/site-management-alert-screen.png" alt-text="Visualizzazione della console di gestione locale.":::

È consigliabile acquisire familiarità con i concetti, le funzionalità e le funzionalità disponibili per i sensori prima di usare la console di gestione locale.

## <a name="integrations"></a>Integrazioni

È possibile espandere le funzionalità di Defender per l'IT grazie alla condivisione di informazioni sul dispositivo e sugli avvisi con i sistemi partner. Le integrazioni aiutano le aziende a colmare soluzioni di sicurezza precedentemente silo per migliorare significativamente la visibilità dei dispositivi e l'Intelligence per le minacce. Le integrazioni consentono inoltre alle aziende di accelerare le risposte a livello di sistema e di ridurre i rischi più velocemente. 

Le integrazioni riducono la complessità ed eliminano i silo e i silo integrando questi ultimi nei flussi di lavoro SOC e nello stack di sicurezza esistenti. Ad esempio:

- SIEM, ad esempio IBM QRadar, Splunk, ArcSight, LogRhythm e RSA NetWitness

- Sistemi di orchestrazione e ticket di sicurezza, ad esempio ServiceNow e IBM Resilient

- Soluzioni di accesso remoto sicure, ad esempio CyberArk Privileged Session Manager (PSM) e BeyondTrust

- Sistemi di controllo di accesso alla rete (NAC) sicuri, ad esempio Aruba ClearPass e ForeScout contrastano

- Firewall, ad esempio Fortinet e check point

## <a name="complete-protocol-support"></a>Supporto completo del protocollo

Oltre al supporto per i protocolli incorporati, è possibile proteggere i dispositivi Internet e i dispositivi ICS che eseguono protocolli proprietari e personalizzati oppure protocolli che deviano da qualsiasi standard. Utilizzando l'SDK dell'ambiente di sviluppo open Horizon (ODE), gli sviluppatori possono creare plug-in di dissettore che decodificano il traffico di rete in base a protocolli definiti. Services analizza il traffico per offrire monitoraggio completo, avvisi e report. USA orizzonte per:

- Espandi visibilità e controllo senza la necessità di eseguire l'aggiornamento a nuove versioni.

- Proteggere le informazioni proprietarie sviluppando il sito come plug-in esterno.

- Localizzare il testo per gli avvisi, gli eventi e i parametri del protocollo.

Inoltre, è possibile usare gli avvisi del protocollo proprietario per comunicare le informazioni:

- Informazioni sui rilevamenti del traffico basati su protocolli e protocolli sottostanti in un plug-in di Horizon proprietario.

- Informazioni su una combinazione di campi di protocollo da tutti i livelli di protocollo. Ad esempio, in un ambiente che esegue MODBUS, potrebbe essere necessario generare un avviso quando il sensore rileva un comando Write in un registro di memoria in un indirizzo IP e una destinazione Ethernet specifici. In alternativa, potrebbe essere necessario generare un avviso quando viene eseguito un accesso a un indirizzo IP specifico.

Gli avvisi vengono attivati quando vengono soddisfatte le condizioni della regola di avviso Horizon.

Inoltre, l'utilizzo di avvisi personalizzati di Horizon consente di scrivere i propri titoli e messaggi di avviso. I campi e i valori del protocollo risolto possono essere incorporati nel testo del messaggio di avviso.

Tramite l'attivazione e la messaggistica di avvisi personalizzati basati sulle condizioni è possibile individuare specifiche attività di rete e aggiornare in modo efficiente la sicurezza, l'IT e i team operativi.


## <a name="high-availability"></a>Disponibilità elevata

Aumentare la resilienza del Defender per la distribuzione di Internet delle cose installando un appliance a disponibilità elevata nella console di gestione locale. Le distribuzioni a disponibilità elevata garantiscono che i sensori gestiti segnalino continuamente una console di gestione locale attiva.

Questa distribuzione viene implementata con una coppia di console di gestione locale che include un'appliance primaria e una secondaria.

## <a name="localization"></a>Localizzazione

Molte funzionalità della console supportano un'ampia gamma di linguaggi.

## <a name="next-step"></a>Passaggio successivo

[Introduzione a Defender for Internet](getting-started.md)
