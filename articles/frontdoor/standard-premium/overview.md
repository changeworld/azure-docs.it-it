---
title: Azure front door standard/Premium | Microsoft Docs
description: Questo articolo fornisce una panoramica di Azure front door standard/Premium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: overview
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 5c3ae5c7b1c45d170548f6fa00481094117e1737
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937363"
---
# <a name="what-is-azure-front-door-standardpremium-preview"></a>Che cos'è Azure front door standard/Premium (anteprima)?

> [!IMPORTANT]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizzare i [documenti di Azure front door](../front-door-overview.md).

Azure front door standard/Premium è una rete CDN moderna, affidabile e sicura, che usa la rete Microsoft globale perimetrale e si integra con Intelligent Threat Protection. Combina le funzionalità di Azure front door, lo standard della rete per la distribuzione di contenuti (CDN) di Azure e il Web Application Firewall (WAF) di Azure in una singola piattaforma di rete CDN cloud sicura.

Con Azure front door standard/Premium è possibile trasformare le applicazioni consumer e aziendali globali in applicazioni moderne personalizzate e sicure e ad alte prestazioni con contenuti che raggiungono un pubblico globale presso la rete perimetrale vicino all'utente. Consente inoltre all'applicazione di aumentare il livello di scalabilità orizzontale senza riscaldamento, beneficiando del bilanciamento del carico HTTP globale con failover immediato.

   :::image type="content" source="../media/overview/front-door-overview.png" alt-text="Architettura standard/Premium di Azure front door" lightbox="../media/overview/front-door-overview-expanded.png":::

Azure front door standard/Premium funziona a livello 7 (livello HTTP/HTTPS) usando il protocollo anycast con Split TCP e la rete globale Microsoft per migliorare la connettività globale. In base al metodo di routing personalizzato che usa le regole impostate, è possibile assicurarsi che il front-end di Azure instraderà le richieste del client all'origine più veloce e più disponibile. Un'origine applicazione è un servizio con connessione Internet ospitato all'interno o all'esterno di Azure. Azure front door standard/Premium offre una gamma di metodi di routing del traffico e opzioni di monitoraggio dell'integrità delle origini per soddisfare diverse esigenze dell'applicazione e scenari di failover automatico. Simile a Gestione traffico, Frontdoor è resiliente agli errori, compreso gli errori di un'intera area di Azure.

Il front-end di Azure protegge anche l'app sui bordi con la protezione del firewall per le applicazioni Web, la protezione dei bot e la protezione integrata di livello 3/livello 4 distribuito. Protegge inoltre i back-end privati con il servizio di collegamento privato. Il front-end di Azure ti offre la sicurezza migliore in pratica di Microsoft su scala globale.  

>[!NOTE]
> Azure offre una suite di soluzioni di bilanciamento del carico completamente gestite per tutti gli scenari.
>
> * Se si sta cercando di eseguire il routing globale basato su DNS e **non** si hanno i requisiti per la terminazione del protocollo TLS (Transport Layer Security) ("offload SSL"), per la richiesta per HTTP/HTTPS o per l'elaborazione a livello di applicazione, vedere [Gestione traffico](../../traffic-manager/traffic-manager-overview.md).
> * Se si vuole bilanciare il carico tra i server in un'area a livello di applicazione, vedere [Gateway applicazione](../../application-gateway/overview.md).
> * Per eseguire il bilanciamento del carico a livello di rete, vedere [Load Balancer](../../load-balancer/load-balancer-overview.md).
>
> Gli scenari end-to-end potrebbero trarre vantaggio dalla combinazione di queste soluzioni in base alle esigenze.
> Per un confronto tra le opzioni di bilanciamento del carico di Azure, vedere [Panoramica delle opzioni di bilanciamento del carico in Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

> [!IMPORTANT]
> Azure front door standard/Premium è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="why-use-azure-front-door-standardpremium-preview"></a>Perché usare Azure front door standard/Premium (anteprima)?

Azure front door standard/Premium offre un'unica piattaforma unificata che si adatta all'accelerazione dinamica e statica grazie all'integrazione di sicurezza chiavi in mano integrata e a un modello di determinazione prezzi semplice e prevedibile. La porta anteriore consente inoltre di definire, gestire e monitorare il routing globale per l'app.

Funzionalità principali incluse in Azure front door standard/Premium (anteprima):

- Prestazioni delle applicazioni accelerate tramite il protocollo anycast **[Split basato su TCP](../front-door-routing-architecture.md#splittcp)** .

- Monitoraggio intelligente del **[Probe di integrità](concept-health-probes.md)** e bilanciamento del carico tra le **[origini](concept-origin.md)**.

- Definire un **[dominio personalizzato](how-to-add-custom-domain.md)** con la convalida del dominio flessibile.

- Sicurezza delle applicazioni con il **[Web Application Firewall integrato (WAF)](../../web-application-firewall/afds/afds-overview.md)**.

- Offload SSL e **[Gestione certificati](how-to-configure-https-custom-domain.md)** integrata.

- Proteggi le tue origini con **[collegamento privato](concept-private-link.md)**.  

- Routing e ottimizzazioni del traffico personalizzabili tramite **[set di regole](concept-rule-set.md)**.

- **[Report incorporati](how-to-reports.md)** con dashboard all-in-One per i modelli di sicurezza e di sportello anteriore.

- Monitoraggio e avvisi in **[tempo reale](how-to-monitor-metrics.md)** che si integrano con il monitoraggio di Azure.

- **[Registrazione](how-to-logs.md)** per ogni richiesta di sportello anteriore e probe di integrità non riusciti.

- Supporto nativo della connettività IPv6 end-to-end e del protocollo HTTP/2.

## <a name="pricing"></a>Prezzi

Azure front door standard/Premium offre due SKU, standard e Premium. Vedere [confronto tra livelli](tier-comparison.md). Per informazioni sui prezzi, vedere [Prezzi di Frontdoor](https://azure.microsoft.com/pricing/details/frontdoor/). 

## <a name="whats-new"></a>Novità

Sottoscrivere il feed RSS e visualizzare gli aggiornamenti più recenti delle funzionalità Frontdoor di Azure nella pagina [Aggiornamenti di Azure](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare una Frontdoor](create-front-door-portal.md).
