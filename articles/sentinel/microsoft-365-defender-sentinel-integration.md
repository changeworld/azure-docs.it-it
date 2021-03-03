---
title: Integrazione di Microsoft 365 Defender con Sentinel di Azure | Microsoft Docs
description: Scopri in che modo Microsoft 365 Defender Integration con Azure Sentinel ti permette di usare Azure Sentinel come coda di eventi imprevisti universali, mantenendo al tempo stesso i punti di forza di M365D's per semplificare l'analisi degli eventi imprevisti della sicurezza M365 e come inserire i dati di caccia avanzati dei componenti Defender in Sentinel di Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: abace18db51a7a571ecc66d50253277fbd2296d3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745923"
---
# <a name="microsoft-365-defender-integration-with-azure-sentinel"></a>Integrazione di Microsoft 365 Defender con Azure Sentinel

> [!IMPORTANT]
> Il connettore Microsoft 365 Defender è attualmente in versione di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

> [!IMPORTANT]
>
> **Microsoft 365 Defender** era noto in precedenza come **Microsoft Threat Protection** o **MTP**.
>
> **Microsoft Defender for endpoint** era noto in precedenza come **Microsoft Defender Advanced Threat Protection** o **MDATP**.
>
> È possibile visualizzare i nomi precedenti ancora in uso per un certo periodo di tempo.

## <a name="incident-integration"></a>Integrazione con eventi imprevisti

L'integrazione degli eventi imprevisti [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) di Azure Sentinel consente di trasmettere tutti gli eventi imprevisti M365D in Sentinel di Azure e di mantenerli sincronizzati tra entrambi i portali. Gli eventi imprevisti di M365D (precedentemente noto come Microsoft Threat Protection o MTP) includono tutti gli avvisi, le entità e le informazioni rilevanti associate, fornendo un contesto sufficiente per eseguire la valutazione e l'analisi preliminare in Sentinel di Azure. Una volta in Sentinel, gli eventi imprevisti rimarranno sincronizzati in modo bidirezionale con M365D, consentendo di sfruttare i vantaggi di entrambi i portali nell'analisi degli eventi imprevisti.

Questa integrazione garantisce Microsoft 365 eventi imprevisti di sicurezza da gestire da Azure Sentinel, come parte della coda di eventi imprevisti primari nell'intera organizzazione, in modo da poter vedere e correlare gli eventi imprevisti M365 insieme a quelli di tutti gli altri sistemi cloud e locali. Allo stesso tempo, consente di sfruttare i vantaggi e le capacità esclusivi di M365D per analisi approfondite e un'esperienza specifica di M365 nell'ecosistema M365. M365 Defender arricchisce e raggruppa gli avvisi di più prodotti M365, riducendo le dimensioni della coda degli eventi imprevisti del SOC e riducendo il tempo necessario per la risoluzione. I servizi componenti che fanno parte dello stack di M365 Defender sono:

- **Microsoft Defender per endpoint** (MDE, noto in precedenza come MDATP)
- **Microsoft Defender per Identity** (MDI, noto in precedenza come AATP)
- **Microsoft Defender per Office 365** (MDO, in precedenza O365ATP)
- **Microsoft cloud app Security** (MCAS)

Oltre a raccogliere gli avvisi da questi componenti, M365 Defender genera avvisi propri. Consente di creare eventi imprevisti da tutti gli avvisi e di inviarli ad Azure Sentinel.

### <a name="common-use-cases-and-scenarios"></a>Casi d'uso comuni e scenari

- Connessione con un clic di eventi imprevisti di M365 Defender, inclusi tutti gli avvisi e le entità dei componenti di M365 Defender, in Sentinel di Azure.

- Sincronizzazione bidirezionale tra Sentinel ed eventi imprevisti M365D su stato, proprietario e motivo di chiusura.

- Sfruttare le funzionalità di raggruppamento e arricchimento degli avvisi di M365 Defender in Sentinel di Azure, riducendo così il tempo per la risoluzione.

- Collegamento approfondito nel contesto tra un evento imprevisto di Azure Sentinel e il relativo evento M365 Defender parallelo, per facilitare le indagini su entrambi i portali.

### <a name="connecting-to-microsoft-365-defender"></a>Connessione a Microsoft 365 Defender

Dopo aver abilitato Microsoft 365 Defender Data Connector per raccogliere gli [eventi imprevisti e gli avvisi](connect-microsoft-365-defender.md), gli eventi imprevisti M365D verranno visualizzati nella coda eventi imprevisti di Sentinel di Azure, con **Microsoft 365 Defender** nel campo **nome prodotto** , subito dopo essere stati generati in M365 Defender.
- Possono essere necessari fino a 10 minuti dal momento in cui viene generato un evento imprevisto in M365 Defender fino al momento in cui viene visualizzato in Sentinel di Azure.

- Gli eventi imprevisti verranno inseriti e sincronizzati senza costi aggiuntivi.

Una volta connessa l'integrazione di M365 Defender, tutti i connettori di avviso dei componenti (MDE, MDI, MDO, MCAS) verranno connessi automaticamente in background, se non sono già presenti. Se sono state acquistate licenze per i componenti dopo la connessione di M365 Defender, gli avvisi e gli eventi imprevisti del nuovo prodotto continueranno a essere visualizzati in Azure Sentinel senza alcuna configurazione o addebito aggiuntivo.

### <a name="m365-defender-incidents-and-microsoft-incident-creation-rules"></a>Richieste di M365 Defender e regole per la creazione di eventi imprevisti Microsoft

- Gli eventi imprevisti generati da M365 Defender, sulla base degli avvisi provenienti dai prodotti per la sicurezza M365, vengono creati usando la logica M365 personalizzata.

- Le regole per la creazione di eventi imprevisti Microsoft in Sentinel di Azure creano anche eventi imprevisti dagli stessi avvisi, usando (una diversa) logica di Azure Sentinel personalizzata.

- L'uso combinato di entrambi i meccanismi è completamente supportato e questa configurazione può essere usata per facilitare la transizione alla nuova logica di creazione degli eventi imprevisti di M365 Defender. In questo modo, tuttavia, vengono creati **eventi imprevisti duplicati** per gli stessi avvisi.

- Per evitare la creazione di eventi imprevisti duplicati per gli stessi avvisi, si consiglia ai clienti di disattivare tutte **le regole di creazione degli eventi imprevisti Microsoft** per i prodotti M365 (MDE, MDI e MDO. vedere MCAS di seguito) quando si connette M365 Defender. Questa operazione può essere eseguita contrassegnando la casella di controllo pertinente nella pagina del connettore. Tenere presente che, se si esegue questa operazione, tutti i filtri applicati dalle regole di creazione degli eventi imprevisti non verranno applicati all'integrazione di M365 Defender Incident.

- Per gli avvisi Microsoft Cloud App Security (MCAS), non tutti i tipi di avviso vengono attualmente caricati in M365 Defender. Per assicurarsi di ricevere comunque gli eventi imprevisti per tutti gli avvisi di MCAS, è necessario mantenere o creare regole per la **creazione di eventi imprevisti Microsoft** per i tipi di avviso *non* caricati in M365D.

### <a name="working-with-m365-defender-incidents-in-azure-sentinel-and-bi-directional-sync"></a>Collaborazione con gli eventi imprevisti di M365 Defender in Sentinel di Azure e sincronizzazione bidirezionale

Gli eventi imprevisti di M365 Defender verranno visualizzati nella coda degli eventi imprevisti di Sentinel di Azure con il nome del prodotto **Microsoft 365 Defender** e con dettagli e funzionalità simili per qualsiasi altro evento imprevisto di Sentinel. Ogni evento imprevisto contiene un collegamento all'evento imprevisto parallelo nel portale di M365 Defender.

Man mano che l'evento imprevisto si evolve in M365 Defender e vengono aggiunti altri avvisi o entità, l'evento imprevisto di Sentinel di Azure verrà aggiornato di conseguenza.

Le modifiche apportate allo stato, al motivo di chiusura o all'assegnazione di un evento imprevisto M365, in M365D o in Sentinel di Azure, vengono aggiornate in modo analogo nella coda degli eventi imprevisti dell'altro. La sincronizzazione verrà eseguita in entrambi i portali immediatamente dopo l'applicazione della modifica all'evento imprevisto, senza alcun ritardo. Potrebbe essere necessario un aggiornamento per visualizzare le modifiche più recenti.

In M365 Defender è possibile trasferire tutti gli avvisi di un evento imprevisto a un altro, causando il merge degli eventi imprevisti. Quando si verifica questo problema, gli eventi imprevisti di Azure Sentinel riflettono le modifiche. Un evento imprevisto conterrà tutti gli avvisi provenienti da eventi imprevisti originali e l'altro evento imprevisto verrà chiuso automaticamente e verrà aggiunto un tag "Redirected".

> [!NOTE]
> Gli eventi imprevisti in Sentinel di Azure possono contenere un massimo di 150 avvisi. Gli eventi imprevisti di M365D possono avere più di questo. Se un evento imprevisto M365D con più di 150 avvisi viene sincronizzato con Sentinel di Azure, l'evento imprevisto di Sentinel verrà visualizzato come con "150 +" avvisi e fornirà un collegamento all'evento imprevisto parallelo in M365D in cui verrà visualizzato il set completo di avvisi.

## <a name="advanced-hunting-event-collection"></a>Raccolta di eventi di caccia avanzati

Il connettore Microsoft 365 Defender consente anche di trasmettere gli eventi di **caccia avanzati** , ovvero un tipo di dati di eventi non elaborati, da Microsoft 365 Defender e i relativi servizi componenti in Azure Sentinel. Attualmente è possibile raccogliere gli eventi di [caccia avanzati](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview) di [Microsoft Defender for endpoint (MDATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) e trasmetterli direttamente nelle tabelle create in modo specifico nell'area di lavoro di Azure Sentinel. Queste tabelle sono basate sullo stesso schema usato nel portale di Microsoft 365 Defender, offrendo l'accesso completo al set completo di eventi di caccia avanzati e consentendo di eseguire le operazioni seguenti:

- È possibile copiare facilmente le query di ricerca avanzate di Microsoft Defender per endpoint in Azure Sentinel.

- Usare i registri eventi non elaborati per fornire informazioni aggiuntive per gli avvisi, la ricerca e l'analisi e per correlare questi eventi ad altri da origini dati aggiuntive in Sentinel di Azure.

- Archiviare i log con una maggiore conservazione, oltre a Microsoft Defender per endpoint o la conservazione predefinita di Microsoft 365 Defender di 30 giorni. È possibile eseguire questa operazione configurando la conservazione dell'area di lavoro o configurando la conservazione per tabella in Log Analytics.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come trarre vantaggio dall'uso di Microsoft 365 Defender insieme a Sentinel di Azure, usando il connettore Microsoft 365 Defender.

- Ottenere le istruzioni per [abilitare il connettore Microsoft 365 Defender](connect-microsoft-365-defender.md).
- Creare [avvisi personalizzati](tutorial-detect-threats-custom.md) ed [esaminare gli eventi imprevisti](tutorial-investigate-cases.md).
