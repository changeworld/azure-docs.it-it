---
title: Dashboard principale o pagina di panoramica del Centro sicurezza di Azure
description: Informazioni sulle funzionalità della pagina di panoramica del Centro sicurezza
author: memildin
ms.author: memildin
ms.date: 03/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 768d69b626a870910d6734e1a1ddc29871f96afb
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099778"
---
# <a name="azure-security-centers-overview-page"></a>Pagina di panoramica del Centro sicurezza di Azure

Quando si apre il Centro sicurezza di Azure, la prima pagina che viene visualizzata è quella di panoramica. 

Questo dashboard interattivo offre una visione unificata del comportamento di sicurezza dei carichi di lavoro cloud ibridi. Mostra inoltre avvisi di sicurezza, informazioni sulla copertura e altri dettagli.

È possibile selezionare qualsiasi elemento nella pagina per ottenere informazioni più dettagliate.

:::image type="content" source="media/overview-page/overview.png" alt-text="Pagina di panoramica di Centro sicurezza":::

## <a name="features-of-the-overview-page"></a>Funzionalità della pagina di panoramica

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="Barra superiore della pagina di panoramica del Centro sicurezza":::

La **barra dei menu superiore** offre:
- **Sottoscrizioni**: selezionando questo pulsante è possibile visualizzare e filtrare l'elenco delle sottoscrizioni. Il Centro sicurezza modificherà la visualizzazione in modo da riflettere il comportamento di sicurezza delle sottoscrizioni selezionate.
- **Novità**: apre le [note sulla versione](release-notes.md) per mantenersi aggiornati sulle nuove funzionalità, le correzioni di bug e le funzionalità deprecate.
- **Numeri generali** per gli account cloud connessi, per mostrare il contesto delle informazioni nei riquadri principali descritti di seguito. Nonché il numero di risorse valutate, le raccomandazioni attive e gli avvisi di sicurezza. Selezionare il numero di risorse valutato per accedere all' [inventario asset](asset-inventory.md). Leggere altre informazioni sulla connessione degli [account AWS](quickstart-onboard-aws.md) e dei [progetti GCP](quickstart-onboard-gcp.md).


Al centro della pagina sono presenti **quattro riquadri centrali**, ognuno dei quali conduce a un dashboard dedicato contenente altri dettagli:
- **Punteggio di sicurezza**: il Centro sicurezza valuta continuamente le risorse, le sottoscrizioni e l'intera organizzazione per rilevare problemi di sicurezza. Aggrega quindi tutti i risultati in un singolo punteggio, in modo da poter indicare, a colpo d'occhio, lo stato di sicurezza attuale: maggiore è il punteggio, minore è il livello di rischio identificato. [Altre informazioni](secure-score-security-controls.md)
- **Conformità alle normative** : il Centro sicurezza fornisce informazioni approfondite sul comportamento di conformità basato sulle valutazioni continue dell'ambiente Azure. Il Centro sicurezza analizza i fattori di rischio nell'ambiente cloud ibrido in base alle procedure di sicurezza consigliate. Tali valutazioni sono associate ai controlli di conformità di un set di standard supportato. [Altre informazioni](security-center-compliance-dashboard.md)
- **Azure Defender** : si tratta della piattaforma di protezione del carico di lavoro cloud (CWPP) integrata nel centro sicurezza per la protezione avanzata e intelligente dei carichi di lavoro ibridi e Azure. Il riquadro mostra la copertura delle risorse connesse (per le sottoscrizioni attualmente selezionate) e gli avvisi recenti, contraddistinti da colori diversi in base al livello di gravità. [Altre informazioni](azure-defender.md)
- **Gestione firewall** : questo riquadro Mostra lo stato degli hub e delle reti da [gestione firewall di Azure](../firewall-manager/overview.md). 


Il riquadro **Informazioni dettagliate** offre elementi personalizzati per l'ambiente, tra cui:
- Risorse che hanno subito il maggior numero di attacchi
- Controlli di sicurezza con le maggiori probabilità di aumentare il punteggio di sicurezza
- Raccomandazioni attive per le risorse maggiormente interessate
- Post di blog recenti degli esperti del Centro sicurezza di Azure

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni introduttive sulla pagina di panoramica del Centro sicurezza. Per informazioni correlate, vedere:

- [Esplorare e gestire le risorse con gli strumenti di inventario e gestione delle risorse](asset-inventory.md)
- [Punteggio di sicurezza nel Centro sicurezza di Azure](secure-score-security-controls.md)