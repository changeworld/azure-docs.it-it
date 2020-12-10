---
title: Panoramica di protezione DDoS di Azure standard
description: Informazioni su Protezione DDoS di Azure Standard che, in combinazione con le procedure consigliate per la progettazione di applicazione, offre un meccanismo di difesa dagli attacchi DDoS.
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/9/2020
ms.author: yitoh
ms.openlocfilehash: c3931cafb6c755c6119bf8deb4ecc6b58ea7bc51
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938444"
---
# <a name="azure-ddos-protection-standard-overview"></a>Panoramica di Protezione DDoS di Azure Standard

Gli attacchi Distributed Denial of Service (DDoS) sono tra le principali preoccupazioni che riguardano la disponibilità e la sicurezza per quei clienti che spostano le loro applicazioni nel cloud. Un attacco DDoS tenta di esaurire le risorse di un'applicazione, che quindi non risulta più disponibile per gli utenti legittimi. Gli attacchi DDoS possono avere come obiettivo qualsiasi endpoint che è raggiungibile pubblicamente tramite Internet.

Ogni proprietà in Azure è protetta dalla protezione DDoS (Basic) dell'infrastruttura di Azure senza costi aggiuntivi. La scala e la capacità complete della rete globalmente distribuita di Azure forniscono una difesa contro gli attacchi comuni a livello di rete tramite il monitoraggio costante del traffico e la mitigazione in tempo reale. Protezione DDoS Basic non richiede alcuna modifica alla configurazione o all'applicazione da parte dell'utente. Protezione DDoS Basic consente di proteggere tutti i servizi di Azure, compresi i servizi PaaS come DNS di Azure.

Protezione DDoS di Azure standard, in combinazione con le procedure consigliate per la progettazione di applicazioni, fornisce funzionalità avanzate di mitigazione DDoS per la difesa da attacchi DDoS. Viene ottimizzata automaticamente per aiutare a proteggere le risorse di Azure specifiche in una rete virtuale. La protezione è semplice da abilitare in qualsiasi rete virtuale nuova o esistente e non richiede alcuna modifica di applicazioni o risorse. Presenta diversi vantaggi rispetto al servizio Basic, compresa la registrazione, gli avvisi e i dati di telemetria. 

![Confronto tra il servizio protezione DDoS di Azure](./media/ddos-protection-overview/ddos-comparison.png)

Protezione DDoS di Azure non archivia i dati dei clienti.

## <a name="features"></a>Funzionalità

- **Integrazione di piattaforma nativa:** integrato in modo nativo in Azure. Include la configurazione tramite il portale di Azure. Protezione DDoS Standard comprende le risorse e la loro configurazione.
- **Protezione chiavi in mano:** La configurazione semplificata consente di proteggere immediatamente tutte le risorse in una rete virtuale non appena è abilitato DDoS Protection standard. Non è necessaria alcuna definizione dell'utente o intervento. Il servizio Protezione DDoS Standard mitiga istantaneamente e automaticamente gli attacchi non appena vengono rilevati.
- **Monitoraggio del traffico always on:** I modelli di traffico delle applicazioni vengono monitorati 24 ore al giorno, 7 giorni alla settimana, cercando gli indicatori degli attacchi DDoS. La mitigazione viene applicata quando vengono superati i criteri di protezione.
- **Ottimizzazione adattiva:** La profilatura del traffico intelligente apprende il traffico dell'applicazione nel tempo e seleziona e aggiorna il profilo più adatto per il servizio. Il profilo viene modificato in base ai cambiamenti del traffico nel tempo.
- **Protezione a più livelli:** Fornisce la protezione DDoS dello stack completo, se usata con una web application firewall, per ottenere la protezione a livello di rete (livello 3 e 4, offerto da Azure DDoS Protection standard) e a livello di applicazione (livello 7, offerto da un WAF). Le offerte WAF includono lo [SKU WAF del gateway applicazione](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure e le offerte di Web Application Firewall di terze parti disponibili in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).
- **Scala di mitigazione completa:** è possibile mitigare più di 60 tipi diversi di attacchi con capacità globale, per una protezione dai più noti attacchi DDoS.
- **Analisi degli attacchi:** ottieni report dettagliati con incrementi di cinque minuti durante un attacco e un riepilogo completo al termine dell'attacco. Eseguire il flusso dei log del flusso di mitigazione in [Sentinel di Azure](../sentinel/connect-azure-ddos-protection.md) o un sistema Siem (Security Information and Event Management) offline per il monitoraggio quasi in tempo reale durante un attacco.
- **Metriche degli attacchi:** tramite Monitoraggio di Azure è possibile accedere al riepilogo delle metriche per ogni attacco.
- **Avviso di attacco:** Gli avvisi possono essere configurati in fase di avvio e arresto di un attacco e sulla durata dell'attacco, usando le metriche di attacco predefinite. Gli avvisi si integrano nel software operativo, come Microsoft Azure log di monitoraggio, Splunk, archiviazione di Azure, posta elettronica e il portale di Azure.
- **Risposta rapida DDoS**: Engage The DDoS Protection Rapid Response (DRR) team per informazioni sull'analisi degli attacchi e l'analisi. Per altre informazioni, vedere [risposta rapida DDoS](ddos-rapid-response.md).
- **Garanzia di costo:** Crediti di servizio di trasferimento dati e scalabilità orizzontale dell'applicazione per gli attacchi DDoS documentati.

## <a name="pricing"></a>Prezzi

Per informazioni sui prezzi standard di protezione DDoS di Azure, vedere la pagina relativa ai [prezzi standard di protezione DDoS di Azure](https://azure.microsoft.com/pricing/details/ddos-protection/).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un piano di protezione DDoS](manage-ddos-protection.md)