---
title: Novità di Azure Defender per l'it
description: Questo articolo consente di conoscere le novità della versione più recente di Defender per l'it.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/06/2021
ms.author: shhazam
ms.openlocfilehash: 72a6e50134647194679055a886b50b01f42e212d
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629980"
---
# <a name="whats-new"></a>Novità

Defender per l'it 10,0 fornisce miglioramenti delle funzionalità che migliorano la sicurezza, la gestione e l'usabilità.

## <a name="security"></a>Security

Sono stati apportati miglioramenti al ripristino di certificati e password per questa versione.

### <a name="certificates"></a>Certificati
  
Questa versione consente di:

- Caricare i certificati SSL direttamente nei sensori e nelle console di gestione locali.
- Eseguire la convalida tra la console di gestione locale e i sensori connessi e tra una console di gestione e una console di gestione a disponibilità elevata. La convalida è basata sulle date di scadenza, sull'autenticità della CA radice e sugli elenchi di revoche di certificati.  Se la convalida ha esito negativo, la sessione non continuerà.

Per gli aggiornamenti:

- Non sono state apportate modifiche al certificato SSL o alla funzionalità di convalida durante l'aggiornamento.
- Dopo l'aggiornamento, gli utenti amministratori del sensore e della console di gestione locale possono sostituire i certificati SSL o attivare la convalida del certificato SSL dalla finestra Impostazioni sistema, certificato SSL.  

Per le installazioni aggiornate:

- Durante il primo accesso, gli utenti devono usare un certificato SSL (scelta consigliata) o un certificato autofirmato generato localmente (scelta non consigliata)
- La convalida del certificato è attivata per impostazione predefinita per le installazioni aggiornate.

### <a name="password-recovery"></a>Ripristino della password
  
Il sensore e gli utenti amministratori della console di gestione locale possono ora recuperare le password dal portale di Azure Defender per le cose. Il ripristino della password in precedenza richiedeva l'intervento del team di supporto.

## <a name="onboarding"></a>Onboarding

### <a name="on-premises-management-console---committed-devices"></a>Console di gestione locale-dispositivi con commit

Dopo l'accesso iniziale alla console di gestione locale, agli utenti viene ora richiesto di caricare un file di attivazione. Il file contiene il numero aggregato di dispositivi da monitorare nella rete aziendale. Questo numero viene definito numero di dispositivi di cui è stato eseguito il commit.
I dispositivi di cui è stato eseguito il commit vengono definiti durante il processo di onboarding nel portale di Azure Defender per le cose in cui viene generato il file di attivazione.
Per la prima volta, gli utenti e gli utenti che eseguono l'aggiornamento sono necessari per caricare il file di attivazione.
Dopo l'attivazione iniziale, il numero di dispositivi rilevati nella rete potrebbe superare il numero di dispositivi di cui è stato eseguito il commit. Questo evento potrebbe verificarsi, ad esempio, se si connettono più sensori alla console di gestione. In caso di discrepanza tra il numero di dispositivi rilevati e il numero di dispositivi di cui è stato eseguito il commit, viene visualizzato un avviso nella console di gestione. Se si verifica questo evento, è necessario caricare un nuovo file di attivazione.

### <a name="pricing-page-options"></a>Opzioni della pagina dei prezzi

Pagina dei prezzi consente di caricare le nuove sottoscrizioni di Azure Defender per l'IT e definire i dispositivi di cui è stato eseguito il commit nella rete.  
Inoltre, la pagina dei prezzi consente ora di gestire le sottoscrizioni esistenti associate a un sensore e aggiornare l'impegno del dispositivo.

### <a name="view-and-manage-onboarded-sensors"></a>Visualizzare e gestire i sensori caricati

Una nuova pagina del portale di Site and Sensors consente di:

- Aggiungere informazioni descrittive sul sensore. Ad esempio, una zona associata al sensore o tag di testo libero.
- Visualizzare e filtrare le informazioni sul sensore. Ad esempio, visualizzare i dettagli sui sensori che sono connessi al cloud o gestiti localmente oppure visualizzare informazioni sui sensori in una zona specifica.  

## <a name="usability"></a>Usabilità

### <a name="azure-sentinel-new-connector-page"></a>Pagina nuovo connettore di Azure Sentinel

La pagina Azure Defender for Internet Data Connector in Azure Sentinel è stata riprogettata. Data Connector è ora basato su sottoscrizioni anziché su hub Internet. consentire ai clienti di gestire meglio la connessione di configurazione a Sentinel di Azure.

### <a name="azure-portal-permission-updates"></a>Aggiornamenti delle autorizzazioni portale di Azure  

È stato aggiunto il supporto per la sicurezza e l'amministratore della sicurezza.

## <a name="other-updates"></a>Altri aggiornamenti

### <a name="access-group---zone-permissions"></a>Gruppo di accesso-autorizzazioni zona
  
Le regole del gruppo di accesso della console di gestione locale non includeranno l'opzione per concedere l'accesso a una zona specifica. Non sono state apportate modifiche alla definizione di regole che utilizzano siti, aree e business unit.   Dopo l'aggiornamento, i gruppi di accesso che contenevano regole che consentono l'accesso a zone specifiche verranno modificati per consentire l'accesso al sito padre, incluse tutte le relative zone.

### <a name="terminology-changes"></a>Modifiche della terminologia

Il termine asset è stato rinominato dispositivo nel sensore e nella console di gestione locale, report e altre interfacce della soluzione.
Nel sensore e negli avvisi della console di gestione locale, il termine gestione di questo evento è stato denominato procedura di correzione.

## <a name="next-steps"></a>Passaggi successivi

[Introduzione a Defender for Internet](getting-started.md)
