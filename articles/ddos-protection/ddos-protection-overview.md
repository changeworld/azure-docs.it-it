---
title: Panoramica di Protezione DDoS di Azure Standard
description: Informazioni su Protezione DDoS di Azure Standard che, in combinazione con le procedure consigliate per la progettazione di applicazione, offre un meccanismo di difesa dagli attacchi DDoS.
services: virtual-network
documentationcenter: na
author: aletheatoh
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/9/2020
ms.author: yitoh
ms.openlocfilehash: c8b3b0227e293c42ac9db8f888bc863e073ff6f5
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103335"
---
# <a name="azure-ddos-protection-standard-overview"></a>Panoramica di Protezione DDoS di Azure Standard

Gli attacchi Distributed Denial of Service (DDoS) sono tra le principali preoccupazioni che riguardano la disponibilità e la sicurezza per quei clienti che spostano le loro applicazioni nel cloud. Un attacco DDoS tenta di esaurire le risorse di un'applicazione, che quindi non risulta più disponibile per gli utenti legittimi. Gli attacchi DDoS possono avere come obiettivo qualsiasi endpoint che è raggiungibile pubblicamente tramite Internet.

Ogni proprietà in Azure è protetta dalla protezione DDoS (Basic) dell'infrastruttura di Azure senza costi aggiuntivi. La scala e la capacità complete della rete globalmente distribuita di Azure forniscono una difesa contro gli attacchi comuni a livello di rete tramite il monitoraggio costante del traffico e la mitigazione in tempo reale. Protezione DDoS Basic non richiede alcuna modifica alla configurazione o all'applicazione da parte dell'utente. Protezione DDoS Basic consente di proteggere tutti i servizi di Azure, compresi i servizi PaaS come DNS di Azure.

Protezione DDoS di Azure standard, in combinazione con le procedure consigliate per la progettazione di applicazioni, fornisce funzionalità avanzate di mitigazione DDoS per la difesa da attacchi DDoS. Viene ottimizzata automaticamente per aiutare a proteggere le risorse di Azure specifiche in una rete virtuale. La protezione è semplice da abilitare in qualsiasi rete virtuale nuova o esistente e non richiede alcuna modifica di applicazioni o risorse. Presenta diversi vantaggi rispetto al servizio Basic, compresa la registrazione, gli avvisi e i dati di telemetria. 

![Confronto tra il servizio protezione DDoS di Azure](./media/ddos-protection-overview/ddos-comparison.png)

Protezione DDoS di Azure non archivia i dati dei clienti.

## <a name="features"></a>Funzionalità

- **Integrazione di piattaforma nativa:** integrato in modo nativo in Azure. Include la configurazione tramite il portale di Azure. Protezione DDoS Standard comprende le risorse e la loro configurazione.
- **Protezione chiavi in mano:** La configurazione semplificata consente di proteggere immediatamente tutte le risorse in una rete virtuale non appena è abilitato DDoS Protection standard. Non è necessaria alcuna definizione dell'utente o intervento. 
- **Monitoraggio del traffico always on:** I modelli di traffico delle applicazioni vengono monitorati 24 ore al giorno, 7 giorni alla settimana, cercando gli indicatori degli attacchi DDoS. Il servizio Protezione DDoS Standard mitiga istantaneamente e automaticamente gli attacchi non appena vengono rilevati.
- **Ottimizzazione adattiva:** La profilatura del traffico intelligente apprende il traffico dell'applicazione nel tempo e seleziona e aggiorna il profilo più adatto per il servizio. Il profilo viene modificato in base ai cambiamenti del traffico nel tempo.
- **Protezione a più livelli:** Quando viene distribuito con un web application firewall (WAF), protezione DDoS standard protegge entrambi a livello di rete (livello 3 e 4, offerto da protezione DDoS di Azure standard) e a livello di applicazione (livello 7, offerto da un WAF). Le offerte WAF includono lo [SKU WAF del gateway applicazione](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure e le offerte di Web Application Firewall di terze parti disponibili in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).
- **Scala di mitigazione completa:** è possibile mitigare più di 60 tipi diversi di attacchi con capacità globale, per una protezione dai più noti attacchi DDoS.
- **Analisi degli attacchi:** ottieni report dettagliati con incrementi di cinque minuti durante un attacco e un riepilogo completo al termine dell'attacco. Eseguire il flusso dei log del flusso di mitigazione in [Sentinel di Azure](../sentinel/connect-azure-ddos-protection.md) o un sistema Siem (Security Information and Event Management) offline per il monitoraggio quasi in tempo reale durante un attacco.
- **Metriche degli attacchi:** tramite Monitoraggio di Azure è possibile accedere al riepilogo delle metriche per ogni attacco.
- **Avviso di attacco:** Gli avvisi possono essere configurati in fase di avvio e arresto di un attacco e sulla durata dell'attacco, usando le metriche di attacco predefinite. Gli avvisi si integrano nel software operativo, come Microsoft Azure log di monitoraggio, Splunk, archiviazione di Azure, posta elettronica e il portale di Azure.
- **Risposta rapida DDoS**: Engage The DDoS Protection Rapid Response (DRR) team per informazioni sull'analisi degli attacchi e l'analisi. Per altre informazioni, vedere [risposta rapida DDoS](ddos-rapid-response.md).
- **Garanzia di costo:** Ricevere il credito per il trasferimento dei dati e il servizio di scalabilità orizzontale dell'applicazione per i costi delle risorse causati da attacchi DDoS documentati.

## <a name="pricing"></a>Prezzi

I piani di protezione DDoS hanno un costo mensile fisso di $2.944 al mese che copre fino a 100 indirizzi IP pubblici. La protezione per le risorse aggiuntive costerà altri $30 per ogni risorsa al mese.

In un tenant, un singolo piano di protezione DDoS può essere usato in più sottoscrizioni, quindi non è necessario creare più di un piano di protezione DDoS.

Per informazioni sui prezzi standard di protezione DDoS di Azure, vedere la pagina relativa ai [prezzi standard di protezione DDoS di Azure](https://azure.microsoft.com/pricing/details/ddos-protection/).

## <a name="reference-architectures"></a>Architetture di riferimento

Protezione DDoS standard è progettato per i [servizi distribuiti in una rete virtuale](../virtual-network/virtual-network-for-azure-services.md). Per altri servizi si applica il servizio Protezione DDoS Basic predefinito. Per altre informazioni sulle architetture supportate, vedere le [architetture di riferimento per la protezione DDoS](./ddos-protection-reference-architectures.md). 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un piano di protezione DDoS](manage-ddos-protection.md)