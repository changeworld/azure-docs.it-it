---
title: Novità di Azure Defender per l'it
description: Questo articolo consente di conoscere le novità della versione più recente di Defender per l'it.
ms.topic: how-to
ms.date: 03/14/2021
ms.openlocfilehash: 970d46a76f125dcaf82491fdea877e6874e2384f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778696"
---
# <a name="whats-new-in-azure-defender-for-iot"></a>Quali sono le novità di Azure Defender per l'it?

Questo articolo elenca le nuove funzionalità e i miglioramenti apportati alle funzionalità di Defender per l'it.

Le funzionalità indicate sono disponibili in anteprima. Le [condizioni aggiuntive per l'anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) includono termini legali aggiuntivi che si applicano a funzionalità di Azure in versione beta, anteprima o diversamente non ancora disponibili a livello generale.
## <a name="march-2021"></a>Marzo 2021

### <a name="sensor---enhanced-custom-alert-rules-public-preview"></a>Regole di avviso personalizzate ottimizzate dal sensore (anteprima pubblica)

È ora possibile creare regole di avviso personalizzate in base al giorno, al gruppo di giorni e all'attività di rete del periodo di tempo rilevato.  L'utilizzo delle condizioni della regola giorno e ora è utile, ad esempio nei casi in cui la gravità dell'avviso è derivata dal momento in cui si verifica l'evento di avviso. Ad esempio, creare una regola personalizzata che attiva un avviso di gravità elevata quando viene rilevata un'attività di rete in un fine settimana o durante la sera.

Questa funzionalità è disponibile nel sensore con il rilascio della versione 10,2.

### <a name="on-premises-management-console---export-alerts-public-preview"></a>Console di gestione locale-Esporta avvisi (anteprima pubblica)

È ora possibile esportare le informazioni sugli avvisi in un file con estensione csv dalla console di gestione locale. È possibile esportare le informazioni di tutti gli avvisi rilevati o esportare le informazioni in base alla visualizzazione filtrata.

Questa funzionalità è disponibile nella console di gestione locale con il rilascio della versione 10,2.

### <a name="add-second-network-interface-to-on-premises-management-console-public-preview"></a>Aggiungere la seconda interfaccia di rete alla console di gestione locale (anteprima pubblica)

È ora possibile migliorare la sicurezza della distribuzione mediante l'aggiunta di una seconda interfaccia di rete alla console di gestione locale. Questa funzionalità consente alla gestione locale di avere sensori connessi in una rete sicura, consentendo allo stesso tempo agli utenti di accedere alla console di gestione locale tramite una seconda interfaccia di rete separata.

Questa funzionalità è disponibile nella console di gestione locale con il rilascio della versione 10,2.
### <a name="device-builder---new-micro-agent-public-preview"></a>Generatore di dispositivi-nuovo agente micro (anteprima pubblica)

È disponibile un nuovo modulo generatore di dispositivi. Il modulo, denominato micro-Agent, consente:

- **Integrazione con l'hub Azure Internet e Azure Defender per** le cose: è possibile creare una sicurezza più avanzata degli endpoint direttamente nei dispositivi Internet e integrarla con l'opzione di monitoraggio offerta dall'hub Azure e da Azure Defender per l'it.
- **Opzioni di distribuzione flessibili con supporto per i sistemi operativi standard per** gli Internet, possono essere distribuite come pacchetto binario o come codice sorgente modificabile, con supporto per sistemi operativi standard per gli Internet come Linux e Azure RTO.
- **Requisiti minimi delle risorse senza dipendenze del kernel del sistema operativo** : footprint ridotto, consumo di CPU ridotto e nessuna dipendenza del kernel del sistema operativo.
- **Gestione delle posture della sicurezza** : monitora in modo proattivo il comportamento di sicurezza dei tuoi dispositivi.
- **Rilevamento delle minacce continuo e in tempo reale/OT** -rilevamento di minacce quali botnet, tentativi di forza bruta, minatori crittografici e attività di rete sospette

La documentazione deprecata Defender-Internet-micro-Agent verrà spostata nella *soluzione basata su agenti per i generatori di dispositivi>cartella classica* .

Questo set di funzionalità è disponibile con la versione corrente del cloud di anteprima pubblica.

## <a name="january-2021"></a>Gennaio 2021

- [Sicurezza](#security)
- [Onboarding](#onboarding)
- [Usabilità](#usability)
- [Altri aggiornamenti](#other-updates)
### <a name="security"></a>Sicurezza

Sono stati apportati miglioramenti al ripristino di certificati e password per questa versione.

#### <a name="certificates"></a>Certificati
  
Questa versione consente di:

- Caricare i certificati SSL direttamente nei sensori e nelle console di gestione locali.
- Eseguire la convalida tra la console di gestione locale e i sensori connessi e tra una console di gestione e una console di gestione a disponibilità elevata. La convalida è basata sulle date di scadenza, sull'autenticità della CA radice e sugli elenchi di revoche di certificati.  Se la convalida ha esito negativo, la sessione non continuerà.

Per gli aggiornamenti:

- Non sono state apportate modifiche al certificato SSL o alla funzionalità di convalida durante l'aggiornamento.
- Dopo l'aggiornamento, gli utenti amministratori del sensore e della console di gestione locale possono sostituire i certificati SSL o attivare la convalida del certificato SSL dalla finestra Impostazioni sistema, certificato SSL.  

Per le installazioni aggiornate:

- Durante il primo accesso, gli utenti devono usare un certificato SSL (scelta consigliata) o un certificato autofirmato generato localmente (scelta non consigliata)
- La convalida del certificato è attivata per impostazione predefinita per le installazioni aggiornate.

#### <a name="password-recovery"></a>Ripristino della password
  
Il sensore e gli utenti amministratori della console di gestione locale possono ora recuperare le password dal portale di Azure Defender per le cose. Il ripristino della password in precedenza richiedeva l'intervento del team di supporto.

### <a name="onboarding"></a>Onboarding

#### <a name="on-premises-management-console---committed-devices"></a>Console di gestione locale-dispositivi con commit

Dopo l'accesso iniziale alla console di gestione locale, agli utenti viene ora richiesto di caricare un file di attivazione. Il file contiene il numero aggregato di dispositivi da monitorare nella rete aziendale. Questo numero viene definito numero di dispositivi di cui è stato eseguito il commit.
I dispositivi di cui è stato eseguito il commit vengono definiti durante il processo di onboarding nel portale di Azure Defender per le cose in cui viene generato il file di attivazione.
Per la prima volta, gli utenti e gli utenti che eseguono l'aggiornamento sono necessari per caricare il file di attivazione.
Dopo l'attivazione iniziale, il numero di dispositivi rilevati nella rete potrebbe superare il numero di dispositivi di cui è stato eseguito il commit. Questo evento potrebbe verificarsi, ad esempio, se si connettono più sensori alla console di gestione. In caso di discrepanza tra il numero di dispositivi rilevati e il numero di dispositivi di cui è stato eseguito il commit, viene visualizzato un avviso nella console di gestione. Se si verifica questo evento, è necessario caricare un nuovo file di attivazione.

#### <a name="pricing-page-options"></a>Opzioni della pagina dei prezzi

Pagina dei prezzi consente di caricare le nuove sottoscrizioni di Azure Defender per l'IT e definire i dispositivi di cui è stato eseguito il commit nella rete.  
Inoltre, la pagina dei prezzi consente ora di gestire le sottoscrizioni esistenti associate a un sensore e aggiornare l'impegno del dispositivo.

#### <a name="view-and-manage-onboarded-sensors"></a>Visualizzare e gestire i sensori caricati

Una nuova pagina del portale di Site and Sensors consente di:

- Aggiungere informazioni descrittive sul sensore. Ad esempio, una zona associata al sensore o tag di testo libero.
- Visualizzare e filtrare le informazioni sul sensore. Ad esempio, visualizzare i dettagli sui sensori che sono connessi al cloud o gestiti localmente oppure visualizzare informazioni sui sensori in una zona specifica.  

### <a name="usability"></a>Usabilità

#### <a name="azure-sentinel-new-connector-page"></a>Pagina nuovo connettore di Azure Sentinel

La pagina Azure Defender for Internet Data Connector in Azure Sentinel è stata riprogettata. Data Connector è ora basato su sottoscrizioni anziché su hub Internet. consentire ai clienti di gestire meglio la connessione di configurazione a Sentinel di Azure.

#### <a name="azure-portal-permission-updates"></a>Aggiornamenti delle autorizzazioni portale di Azure  

È stato aggiunto il supporto per la sicurezza e l'amministratore della sicurezza.

### <a name="other-updates"></a>Altri aggiornamenti

#### <a name="access-group---zone-permissions"></a>Gruppo di accesso-autorizzazioni zona
  
Le regole del gruppo di accesso della console di gestione locale non includeranno l'opzione per concedere l'accesso a una zona specifica. Non sono state apportate modifiche alla definizione di regole che utilizzano siti, aree e business unit.   Dopo l'aggiornamento, i gruppi di accesso che contenevano regole che consentono l'accesso a zone specifiche verranno modificati per consentire l'accesso al sito padre, incluse tutte le relative zone.

#### <a name="terminology-changes"></a>Modifiche della terminologia

Il termine asset è stato rinominato dispositivo nel sensore e nella console di gestione locale, nei report e in altre interfacce della soluzione.
Nel sensore e negli avvisi della console di gestione locale, il termine gestione di questo evento è stato denominato procedura di correzione.

## <a name="next-steps"></a>Passaggi successivi

[Introduzione a Defender for Internet](getting-started.md)
