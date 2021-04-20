---
title: Novità di Azure Defender per IoT
description: Questo articolo illustra le novità della versione più recente di Defender per IoT.
ms.topic: overview
ms.date: 4/6/2021
ms.openlocfilehash: df6a43dc68acd025b1c65877c65d1b7e947f210b
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739386"
---
# <a name="whats-new-in-azure-defender-for-iot"></a>Novità di Azure Defender per IoT?

Questo articolo elenca le nuove funzionalità e i miglioramenti delle funzionalità per Defender per IoT.

Le funzionalità notate sono disponibili in ANTEPRIMA. Le [condizioni aggiuntive per l'anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) includono termini legali aggiuntivi che si applicano a funzionalità di Azure in versione beta, anteprima o diversamente non ancora disponibili a livello generale.

## <a name="versioning-and-support-for-azure-defender-for-iot"></a>Controllo delle versioni e supporto per Azure Defender per IoT 

Di seguito sono elencati il supporto, i criteri di modifica che causano un'interruzione per Defender per IoT e le versioni di Azure Defender per IoT attualmente disponibili. 

### <a name="servicing-information-and-timelines"></a>Informazioni e tempistiche di manutenzione 

Microsoft prevede di rilasciare gli aggiornamenti per Azure Defender per IoT non meno di una volta al trimestre. Ogni versione disponibile a livello generale del sensore Azure Defender per IoT e della console di gestione locale è supportata fino a nove mesi dopo il rilascio. Le correzioni e le nuove funzionalità verranno applicate alla versione ga corrente attualmente supportata e non verranno applicate alle versioni ga precedenti.

### <a name="versions-and-support-dates"></a>Versioni e date di supporto

| Versione | Data di rilascio | Data di fine supporto |
|--|--|--|
| 10,0 | 01/2021 | 10/2021 |
## <a name="march-2021"></a>Marzo 2021

### <a name="sensor---enhanced-custom-alert-rules-public-preview"></a>Sensore - Regole di avviso personalizzate avanzate (anteprima pubblica)

È ora possibile creare regole di avviso personalizzate in base al giorno, al gruppo di giorni e al periodo di tempo in cui è stata rilevata l'attività di rete.  L'uso delle condizioni delle regole giorno e ora è utile, ad esempio nei casi in cui la gravità dell'avviso viene derivata dal momento in cui si verifica l'evento di avviso. Ad esempio, creare una regola personalizzata che attiva un avviso di gravità elevata quando viene rilevata un'attività di rete durante il fine settimana o la sera.

Questa funzionalità è disponibile nel sensore con la versione 10.2.

### <a name="on-premises-management-console---export-alerts-public-preview"></a>Console di gestione locale - Avvisi di esportazione (anteprima pubblica)

Le informazioni sugli avvisi possono ora essere esportate in un file CSV dalla console di gestione locale. È possibile esportare le informazioni di tutti gli avvisi rilevati o esportare le informazioni in base alla vista filtrata.

Questa funzionalità è disponibile nella console di gestione locale con la versione 10.2.

### <a name="add-second-network-interface-to-on-premises-management-console-public-preview"></a>Aggiungere la seconda interfaccia di rete alla console di gestione locale (anteprima pubblica)

È ora possibile migliorare la sicurezza della distribuzione aggiungendo una seconda interfaccia di rete alla console di gestione locale. Questa funzionalità consente alla gestione locale di avere sensori connessi in un'unica rete sicura, consentendo allo stesso tempo agli utenti di accedere alla console di gestione locale tramite una seconda interfaccia di rete separata.

Questa funzionalità è disponibile nella console di gestione locale con la versione 10.2.
### <a name="device-builder---new-micro-agent-public-preview"></a>Generatore di dispositivi - nuovo micro agente (anteprima pubblica)

È disponibile un nuovo modulo device builder. Il modulo, definito micro-agente, consente:

- Integrazione **con hub IoT di Azure e Azure Defender per IoT:** creare una sicurezza degli endpoint più avanzata direttamente nei dispositivi IoT integrando il dispositivo con l'opzione di monitoraggio fornita da hub IoT di Azure e Azure Defender per IoT.
- Opzioni di distribuzione flessibili con supporto per sistemi operativi **IoT standard:** possono essere distribuite come pacchetto binario o come codice sorgente modificabile, con supporto per sistemi operativi IoT standard come Linux e Azure RTOS.
- **Requisiti minimi delle risorse senza dipendenze del kernel** del sistema operativo: footprint ridotto, basso utilizzo della CPU e nessuna dipendenza del kernel del sistema operativo.
- **Gestione delle posture di** sicurezza: monitorare in modo proattivo la posizione di sicurezza dei dispositivi IoT.
- Rilevamento continuo delle minacce **IoT/OT** in tempo reale: rilevare minacce come botnet, tentativi di forza bruta, minatori crittografici e attività di rete sospette

La documentazione deprecata di Defender-IoT-micro-agent verrà spostata nella soluzione basata su agente per i generatori di *dispositivi>classica.*

Questo set di funzionalità è disponibile con la versione cloud di anteprima pubblica corrente.

## <a name="january-2021"></a>Gennaio 2021

- [Sicurezza](#security)
- [Onboarding](#onboarding)
- [Usabilità](#usability)
- [Altri aggiornamenti](#other-updates)
### <a name="security"></a>Security

Per questa versione sono stati apportati miglioramenti al ripristino di certificati e password.

#### <a name="certificates"></a>Certificati
  
Questa versione consente di:

- Caricare i certificati SSL direttamente nei sensori e nelle console di gestione locali.
- Eseguire la convalida tra la console di gestione locale e i sensori connessi e tra una console di gestione e una console di gestione a disponibilità elevata. La convalida si basa su date di scadenza, autenticità CA radice ed elenchi di revoche di certificati.  Se la convalida non riesce, la sessione non continuerà.

Per gli aggiornamenti:

- Non è stata apportata alcuna modifica al certificato SSL o alla funzionalità di convalida durante l'aggiornamento.
- Dopo l'aggiornamento, gli utenti amministratori del sensore e della console di gestione locale possono sostituire i certificati SSL o attivare la convalida del certificato SSL dalla finestra Impostazioni di sistema, Certificato SSL.  

Per le installazioni nuove:

- Durante il primo accesso, agli utenti viene richiesto di usare un certificato SSL (scelta consigliata) o un certificato autofirmato generato localmente (scelta non consigliata)
- La convalida del certificato è attivata per impostazione predefinita per le installazioni nuove.

#### <a name="password-recovery"></a>Ripristino della password
  
Gli utenti amministratori del sensore e della console di gestione locale possono ora ripristinare le password dal Azure Defender per IoT locale. In precedenza il team di supporto richiedeva l'intervento del team di supporto per il ripristino della password.

### <a name="onboarding"></a>Onboarding

#### <a name="on-premises-management-console---committed-devices"></a>Console di gestione locale - Dispositivi di cui è stato eseguito il commit

Dopo l'accesso iniziale alla console di gestione locale, gli utenti devono ora caricare un file di attivazione. Il file contiene il numero aggregato di dispositivi da monitorare nella rete aziendale. Questo numero è indicato come numero di dispositivi di cui è stato eseguito il commit.
I dispositivi di cui è stato eseguito il commit vengono definiti durante il processo di onboarding nel portale Azure Defender per IoT, in cui viene generato il file di attivazione.
Per caricare il file di attivazione, sono necessari utenti e utenti che ese aggiornano per la prima volta.
Dopo l'attivazione iniziale, il numero di dispositivi rilevati nella rete potrebbe superare il numero di dispositivi di cui è stato eseguito il commit. Questo evento potrebbe verificarsi, ad esempio, se si connettono più sensori alla console di gestione. In caso di discrepanza tra il numero di dispositivi rilevati e il numero di dispositivi di cui è stato eseguito il commit, nella console di gestione viene visualizzato un avviso. Se si verifica questo evento, è necessario caricare un nuovo file di attivazione.

#### <a name="pricing-page-options"></a>Opzioni della pagina dei prezzi

La pagina dei prezzi consente di eseguire l'onboard di nuove sottoscrizioni Azure Defender per IoT e definire i dispositivi di cui è stato eseguito il commit nella rete.  
Inoltre, la pagina Prezzi consente ora di gestire le sottoscrizioni esistenti associate a un sensore e aggiornare l'impegno del dispositivo.

#### <a name="view-and-manage-onboarded-sensors"></a>Visualizzare e gestire i sensori onboarded

Una nuova pagina del portale del sito e dei sensori consente di:

- Aggiungere informazioni descrittive sul sensore. Ad esempio, una zona associata al sensore o tag di testo libero.
- Visualizzare e filtrare le informazioni del sensore. Ad esempio, visualizzare i dettagli sui sensori connessi al cloud o gestiti localmente oppure visualizzare informazioni sui sensori in una zona specifica.  

### <a name="usability"></a>Usabilità

#### <a name="azure-sentinel-new-connector-page"></a>Azure Sentinel nuovo connettore

La Azure Defender per IoT del connettore dati in Azure Sentinel è stata riprogettata. Il connettore dati è ora basato sulle sottoscrizioni anziché sugli hub IoT. consentendo ai clienti di gestire meglio la connessione di configurazione Azure Sentinel.

#### <a name="azure-portal-permission-updates"></a>portale di Azure delle autorizzazioni  

È stato aggiunto il supporto di Security Reader e Security Administrator.

### <a name="other-updates"></a>Altri aggiornamenti

#### <a name="access-group---zone-permissions"></a>Gruppo di accesso - Autorizzazioni per l'area
  
Le regole del gruppo di accesso della console di gestione locale non includeranno l'opzione per concedere l'accesso a una zona specifica. La definizione delle regole che usano siti, aree e business unit non è stata apportata alcuna modifica.   Dopo l'aggiornamento, i gruppi di accesso che contenevano regole che consentono l'accesso a zone specifiche verranno modificati per consentire l'accesso al sito padre, incluse tutte le relative zone.

#### <a name="terminology-changes"></a>Modifiche della terminologia

Il termine asset è stato rinominato dispositivo nel sensore e nella console di gestione locale, nei report e in altre interfacce della soluzione.
Negli avvisi della console di gestione locale e del sensore il termine Gestisci questo evento è denominato Passaggi di correzione.

## <a name="next-steps"></a>Passaggi successivi

[Introduzione a Defender per IoT](getting-started.md)
