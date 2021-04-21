---
title: Novità di Azure Defender per IoT
description: Questo articolo illustra le novità della versione più recente di Defender per IoT.
ms.topic: overview
ms.date: 04/19/2021
ms.openlocfilehash: da5358ccf0f69ca2ba8f5722b75889b6b7c92c07
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752607"
---
# <a name="whats-new-in-azure-defender-for-iot"></a>Novità di Azure Defender per IoT?

Questo articolo elenca le nuove funzionalità e i miglioramenti delle funzionalità per Defender per IoT.

Le funzionalità notate sono disponibili in ANTEPRIMA. Le [condizioni aggiuntive per l'anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) includono termini legali aggiuntivi che si applicano a funzionalità di Azure in versione beta, anteprima o diversamente non ancora disponibili a livello generale.

## <a name="april-2021"></a>Aprile 2021

### <a name="work-with-automatic-threat-intelligence-updates-public-preview"></a>Usare gli aggiornamenti automatici dell'intelligence per le minacce (anteprima pubblica)

È ora possibile eseguire automaticamente il push di nuovi pacchetti di intelligence per le minacce nei sensori connessi al cloud non appena vengono rilasciati da Microsoft Defender per IoT. Oltre a scaricare i pacchetti di intelligence per le minacce e quindi caricarli nei sensori.

L'uso degli aggiornamenti automatici consente di ridurre le attività operative e garantire una maggiore sicurezza. Abilitare l'aggiornamento automatico tramite l'onboarding del sensore connesso al cloud nel portale di Defender per IoT con l'interruttore Aggiornamenti automatici **dell'intelligence** per le minacce attivato.

Se si vuole adottare un approccio più conservativo per l'aggiornamento dei dati di intelligence per le minacce, è possibile eseguire manualmente il push dei pacchetti dal portale di Azure Defender per IoT ai sensori connessi al cloud solo quando si desidera.
In questo modo è possibile controllare quando viene installato un pacchetto, senza la necessità di scaricarlo e quindi caricarlo nei sensori. Eseguire manualmente il push degli aggiornamenti ai sensori dalla pagina Defender for IoT **Sites and Sensors (Siti e sensori di** Defender per IoT).

È anche possibile esaminare le informazioni seguenti sui pacchetti di intelligence per le minacce:

- Versione del pacchetto installata
- Modalità di aggiornamento dell'intelligence per le minacce 
- Stato di aggiornamento dell'intelligence per le minacce

### <a name="view-cloud-connected-sensor-information-public-preview"></a>Visualizzare le informazioni sui sensori connessi al cloud (anteprima pubblica)

Visualizzare informazioni operative importanti sui sensori connessi al cloud nella **pagina Sites and Sensors (Siti e** sensori).

- Versione del sensore installata
- Stato della connessione del sensore al cloud.
- L'ultima volta che è stato rilevato il sensore che si connette al cloud.

### <a name="alert-api-enhancements"></a>Miglioramenti dell'API di avviso

Sono disponibili nuovi campi per gli utenti che lavorano con le API di avviso.

**Console di gestione locale**

- Indirizzo di origine e di destinazione
- Procedura di correzione
- Nome del sensore definito dall'utente
- Nome della zona associata al sensore 
- Nome del sito associato al sensore

**Sensor**

- Indirizzo di origine e di destinazione
- Procedura di correzione

L'API versione 2 è necessaria quando si lavora con i nuovi campi.

### <a name="features-delivered-as-generally-available-ga"></a>Funzionalità disponibili a livello generale

Le funzionalità seguenti erano disponibili in precedenza per l'anteprima pubblica e sono ora disponibili a livello generale:The following features were previously available for Public Preview, and are now Generally Available (GA)features:

- Sensore: regole di avviso personalizzate migliorate
- Console di gestione locale - esportare gli avvisi
- Aggiungere la seconda interfaccia di rete alla console di gestione locale
- Generatore di dispositivi - nuovo micro agente

## <a name="march-2021"></a>Marzo 2021

### <a name="sensor---enhanced-custom-alert-rules-public-preview"></a>Sensore : regole di avviso personalizzate avanzate (anteprima pubblica)

È ora possibile creare regole di avviso personalizzate in base al giorno, al gruppo di giorni e all'attività di rete del periodo di tempo rilevato.  L'uso delle condizioni delle regole giorno e ora è utile, ad esempio nei casi in cui la gravità dell'avviso viene derivata dal momento in cui si verifica l'evento di avviso. Ad esempio, creare una regola personalizzata che attiva un avviso di gravità elevata quando viene rilevata un'attività di rete durante il fine settimana o la sera.

Questa funzionalità è disponibile nel sensore con la versione 10.2.

### <a name="on-premises-management-console---export-alerts-public-preview"></a>Console di gestione locale - Esportare avvisi (anteprima pubblica)

Le informazioni sugli avvisi possono ora essere esportate in un file CSV dalla console di gestione locale. È possibile esportare le informazioni di tutti gli avvisi rilevati o esportare le informazioni in base alla visualizzazione filtrata.

Questa funzionalità è disponibile nella console di gestione locale con la versione 10.2.

### <a name="add-second-network-interface-to-on-premises-management-console-public-preview"></a>Aggiungere una seconda interfaccia di rete alla console di gestione locale (anteprima pubblica)

È ora possibile migliorare la sicurezza della distribuzione aggiungendo una seconda interfaccia di rete alla console di gestione locale. Questa funzionalità consente alla gestione locale di avere sensori connessi in una rete sicura, consentendo allo stesso tempo agli utenti di accedere alla console di gestione locale tramite una seconda interfaccia di rete separata.

Questa funzionalità è disponibile nella console di gestione locale con la versione 10.2.

### <a name="add-second-network-interface-to-on-premises-management-console-public-preview"></a>Aggiungere una seconda interfaccia di rete alla console di gestione locale (anteprima pubblica)

È ora possibile migliorare la sicurezza della distribuzione aggiungendo una seconda interfaccia di rete alla console di gestione locale. Questa funzionalità consente alla gestione locale di avere sensori connessi in una rete sicura, consentendo allo stesso tempo agli utenti di accedere alla console di gestione locale tramite una seconda interfaccia di rete separata.

Questa funzionalità è disponibile nella console di gestione locale con la versione 10.2.
### <a name="device-builder---new-micro-agent-public-preview"></a>Generatore di dispositivi - nuovo micro-agente (anteprima pubblica)

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
- Eseguire la convalida tra la console di gestione locale e i sensori connessi e tra una console di gestione e una console di gestione a disponibilità elevata. La convalida si basa su date di scadenza, autenticità della CA radice ed elenchi di revoche di certificati.  Se la convalida non riesce, la sessione non continuerà.

Per gli aggiornamenti:

- Durante l'aggiornamento non sono presenti modifiche al certificato SSL o alla funzionalità di convalida.
- Dopo l'aggiornamento, gli utenti amministratori della console di gestione locale e del sensore possono sostituire i certificati SSL o attivare la convalida del certificato SSL dalla finestra Impostazioni di sistema, Certificato SSL.  

Per le installazioni nuove:

- Durante il primo accesso, agli utenti viene richiesto di usare un certificato SSL (scelta consigliata) o un certificato autofirmato generato localmente (scelta non consigliata)
- La convalida dei certificati è attivata per impostazione predefinita per le installazioni nuove.

#### <a name="password-recovery"></a>Ripristino della password
  
Gli utenti amministratori del sensore e della console di gestione locale possono ora ripristinare le password dal Azure Defender per IoT locale. In precedenza il team di supporto richiedeva l'intervento del team di supporto per il ripristino della password.

### <a name="onboarding"></a>Onboarding

#### <a name="on-premises-management-console---committed-devices"></a>Console di gestione locale - Dispositivi di cui è stato eseguito il commit

Dopo l'accesso iniziale alla console di gestione locale, gli utenti devono ora caricare un file di attivazione. Il file contiene il numero aggregato di dispositivi da monitorare nella rete aziendale. Questo numero è indicato come numero di dispositivi di cui è stato eseguito il commit.
I dispositivi di cui è stato eseguito il commit vengono definiti durante il processo di onboarding nel portale Azure Defender per IoT, in cui viene generato il file di attivazione.
Per caricare il file di attivazione, è necessario che gli utenti e gli utenti a cui si esegue l'aggiornamento per la prima volta.
Dopo l'attivazione iniziale, il numero di dispositivi rilevati nella rete potrebbe superare il numero di dispositivi di cui è stato eseguito il commit. Questo evento può verificarsi, ad esempio, se si connettono più sensori alla console di gestione. In caso di discrepanza tra il numero di dispositivi rilevati e il numero di dispositivi di cui è stato eseguito il commit, nella console di gestione viene visualizzato un avviso. Se si verifica questo evento, è necessario caricare un nuovo file di attivazione.

#### <a name="pricing-page-options"></a>Opzioni della pagina dei prezzi

La pagina dei prezzi consente di eseguire l'onboard delle nuove sottoscrizioni Azure Defender per IoT e definire i dispositivi di cui è stato eseguito il commit nella rete.  
Inoltre, la pagina Prezzi consente ora di gestire le sottoscrizioni esistenti associate a un sensore e aggiornare l'impegno del dispositivo.

#### <a name="view-and-manage-onboarded-sensors"></a>Visualizzare e gestire i sensori di cui è stato fatto l'onboarded

Una nuova pagina del portale Site and Sensors (Sito e sensori) consente di:

- Aggiungere informazioni descrittive sul sensore. Ad esempio, una zona associata al sensore o tag di testo libero.
- Visualizzare e filtrare le informazioni del sensore. Ad esempio, visualizzare i dettagli sui sensori connessi al cloud o gestiti localmente oppure visualizzare informazioni sui sensori in una zona specifica.  

### <a name="usability"></a>Usabilità

#### <a name="azure-sentinel-new-connector-page"></a>Azure Sentinel nuovo connettore

La Azure Defender per IoT del connettore dati in Azure Sentinel è stata riprogettata. Il connettore dati è ora basato sulle sottoscrizioni anziché sugli hub IoT. consentendo ai clienti di gestire meglio la connessione di configurazione Azure Sentinel.

#### <a name="azure-portal-permission-updates"></a>portale di Azure delle autorizzazioni  

È stato aggiunto il supporto di Security Reader e Security Administrator.

### <a name="other-updates"></a>Altri aggiornamenti

#### <a name="access-group---zone-permissions"></a>Gruppo di accesso - Autorizzazioni per l'area
  
Le regole del gruppo di accesso della console di gestione locale non includono l'opzione per concedere l'accesso a una zona specifica. La definizione delle regole che usano siti, aree e business unit non è stata apportata alcuna modifica.   Dopo l'aggiornamento, i gruppi di accesso che contenevano regole che consentono l'accesso a zone specifiche verranno modificati per consentire l'accesso al sito padre, incluse tutte le relative zone.

#### <a name="terminology-changes"></a>Modifiche della terminologia

Il termine asset è stato rinominato dispositivo nel sensore e nella console di gestione locale, nei report e in altre interfacce della soluzione.
Negli avvisi del sensore e della console di gestione locale, il termine Gestisci questo evento è stato denominato Passaggi di correzione.

## <a name="next-steps"></a>Passaggi successivi

[Introduzione a Defender per IoT](getting-started.md)
