---
title: Introduzione all'automazione in Sentinel di Azure | Microsoft Docs
description: Questo articolo presenta le funzionalità di orchestrazione, automazione e risposta (SOAR) di sicurezza di Azure Sentinel e ne descrive i componenti SOAR, ovvero regole di automazione e PlayBook.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 54d7c997ce17c927a692e84f6094e3a08f707af7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609800"
---
# <a name="security-orchestration-automation-and-response-soar-in-azure-sentinel"></a>Orchestrazione di sicurezza, automazione e risposta (SOAR) in Sentinel di Azure

Questo articolo descrive le funzionalità di orchestrazione, automazione e risposta (SOAR) di sicurezza di Azure Sentinel e Mostra come l'uso di regole e playbook di automazione in risposta a minacce per la sicurezza aumenta l'efficacia del SOC e consente di risparmiare tempo e risorse.

## <a name="azure-sentinel-as-a-soar-solution"></a>Azure Sentinel come soluzione SOAR

### <a name="the-problem"></a>Problema

I team SIEM/SOC sono in genere inondati da avvisi e incidenti di sicurezza a intervalli regolari, a volumi così grandi da sovraccaricare il personale disponibile. Si tratta di un risultato troppo spesso nelle situazioni in cui molti avvisi vengono ignorati e molti eventi imprevisti non vengono analizzati, lasciando l'organizzazione vulnerabile ad attacchi che non sono stati osservati.

### <a name="the-solution"></a>Soluzione

Azure Sentinel, oltre a essere un sistema SIEM (Security Information and Event Management), è anche una piattaforma per l'orchestrazione di sicurezza, l'automazione e la risposta (SOAR). Uno degli scopi principali è quello di automatizzare le attività ricorrenti e prevedibili di arricchimento, risposta e correzione che sono responsabili del centro operativo e del personale di sicurezza (SOC/secops), liberando tempo e risorse per un'analisi più approfondita e la ricerca di minacce avanzate. L'automazione impiega alcune forme diverse in Sentinel di Azure, dalle regole di automazione che gestiscono centralmente l'automazione della gestione e della risposta agli eventi imprevisti, ai PlayBook che eseguono sequenze predeterminate di azioni per offrire un'automazione avanzata potente e flessibile alle attività di risposta alle minacce.

## <a name="automation-rules"></a>Regole di automazione

Le regole di automazione sono un nuovo concetto in Sentinel di Azure. Questa funzionalità consente agli utenti di gestire centralmente l'automazione della gestione degli eventi imprevisti. Oltre a consentire l'assegnazione di PlayBook a eventi imprevisti (non solo agli avvisi come prima), le regole di automazione consentono anche di automatizzare le risposte per più regole di analisi in una sola volta, contrassegnare, assegnare o chiudere automaticamente gli eventi imprevisti senza la necessità di PlayBook e controllare l'ordine delle azioni eseguite. Le regole di automazione semplificano l'uso dell'automazione in Sentinel di Azure e consentono di semplificare i flussi di lavoro complessi per i processi di orchestrazione degli eventi imprevisti.

Scopri di più con questa [spiegazione completa delle regole di automazione](automate-incident-handling-with-automation-rules.md).

> [!IMPORTANT]
>
> - La funzionalità **regole di automazione** è attualmente disponibile in **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

## <a name="playbooks"></a>Playbook

Un PlayBook è una raccolta di azioni di risposta e correzione e la logica che può essere eseguita da Azure Sentinel come routine. Un PlayBook può aiutare ad automatizzare e orchestrare la risposta alle minacce, può integrarsi con altri sistemi sia interni che esterni e può essere impostata in modo da essere eseguita automaticamente in risposta a avvisi o eventi imprevisti specifici, quando vengono attivati rispettivamente da una regola di analisi o da una regola di automazione. Può essere eseguito anche manualmente su richiesta, in risposta agli avvisi, dalla pagina eventi imprevisti.

I PlayBook in Sentinel di Azure si basano sui flussi di lavoro compilati in app per la [logica di Azure](../logic-apps/logic-apps-overview.md), un servizio cloud che consente di pianificare, automatizzare e orchestrare attività e flussi di lavoro in tutti i sistemi aziendali. Ciò significa che i PlayBook possono sfruttare tutte le potenzialità e la personalizzazione delle funzionalità di integrazione e orchestrazione delle app per la logica e strumenti di progettazione facili da usare, nonché la scalabilità, l'affidabilità e il livello di servizio di un servizio di Azure di livello 1.

Scopri di più con questa [spiegazione completa dei PlayBook](automate-responses-with-playbooks.md).

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come Azure Sentinel usa l'automazione per aiutare il SOC a funzionare in modo più efficace ed efficiente.

- Per informazioni sull'automazione della gestione degli eventi imprevisti, vedere [automatizzare la gestione degli eventi imprevisti in Sentinel di Azure](automate-incident-handling-with-automation-rules.md).
- Per altre informazioni sulle opzioni di automazione avanzate, vedere [automatizzare la risposta alle minacce con PlayBook in Sentinel di Azure](automate-responses-with-playbooks.md).
- Per informazioni sull'implementazione di regole e playbook di automazione, vedere [esercitazione: usare PlayBook per automatizzare le risposte alle minacce in Sentinel di Azure](tutorial-respond-threats-playbook.md).
