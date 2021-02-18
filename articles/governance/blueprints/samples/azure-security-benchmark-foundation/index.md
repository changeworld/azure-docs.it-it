---
title: Panoramica dell'esempio di progetto di Azure Security benchmark Foundation
description: Panoramica e architettura dell'esempio di progetto Azure Security benchmark Foundation.
ms.date: 02/17/2020
ms.topic: sample
ms.openlocfilehash: b3b58f2fb603e23e4b188e527fa4fc60f4041a29
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095267"
---
# <a name="overview-of-the-azure-security-benchmark-foundation-blueprint-sample"></a>Panoramica dell'esempio di progetto di Azure Security benchmark Foundation

L'esempio Azure Security benchmark Foundation Blueprint fornisce un set di modelli di infrastruttura di base che consentono di creare un ambiente Azure sicuro e conforme. Il progetto consente di distribuire un'architettura basata sul cloud che offre soluzioni a scenari con requisiti di certificazione o di conformità. Questo esempio di progetto di base è un'estensione del [progetto di esempio di benchmark di sicurezza di Azure](../azure-security-benchmark/index.md). Distribuisce e configura i limiti di rete, il monitoraggio e altre risorse in linea con i criteri e altri Guardrails definiti nel [benchmark di sicurezza di Azure](../../../../security/benchmarks/index.yml).

## <a name="architecture"></a>Architettura

L'ambiente di base creato da questo esempio di progetto si basa sulle entità di architettura di un [modello hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke).
Il progetto distribuisce una rete virtuale Hub che contiene risorse comuni e condivise, servizi e artefatti come Azure Bastion, gateway e firewall per la connettività, la gestione e le subnet di Jump box per ospitare un'infrastruttura di gestione, manutenzione, amministrazione e connettività aggiuntiva/facoltativa. Una o più reti virtuali spoke vengono distribuite per ospitare carichi di lavoro dell'applicazione, ad esempio servizi Web e di database. Le reti virtuali spoke sono connesse alla rete virtuale dell'hub usando il peering di rete virtuale di Azure per una connettività semplice e sicura. È possibile aggiungere spoke aggiuntivi riassegnando il progetto di esempio o creando manualmente una rete virtuale di Azure e eseguendone il peering con la rete virtuale dell'hub. Tutte le connessioni esterne alle reti virtuali spoke e alle subnet sono configurate per il routing tramite la rete virtuale dell'hub e le caselle di salto di firewall, gateway e gestione.

:::image type="content" source="../../media/azure-security-benchmark-foundation/architecture.png" alt-text="Diagramma dell'architettura di esempio del progetto di Azure Security benchmark Foundation" border="false":::

Questo progetto distribuisce diversi servizi di Azure per fornire una base sicura, monitorata e pronta per l'azienda. L'ambiente è costituito da:

- [Log di monitoraggio di Azure](../../../../azure-monitor/platform/data-platform-logs.md) e un account di archiviazione di Azure per assicurarsi che i log delle risorse, i log attività, le metriche e i flussi di traffico delle reti siano archiviati in una posizione centralizzata per semplificare l'esecuzione di query, analisi, archiviazione e avvisi.
- [Centro sicurezza di Azure](../../../../security-center/security-center-introduction.md) (versione standard) per fornire la protezione dalle minacce per le risorse di Azure.
- [Rete virtuale di Azure](../../../../virtual-network/virtual-networks-overview.md) nell'hub che supporta le subnet per la connettività a una rete locale, uno stack in ingresso e in uscita a/per la connettività Internet e subnet facoltative per la distribuzione di servizi amministrativi o di gestione aggiuntivi. Rete in ingresso virtuale il spoke contiene subnet per ospitare i carichi di lavoro dell'applicazione. È possibile creare subnet aggiuntive dopo la distribuzione, in base alle esigenze, per supportare scenari applicabili.
- [Firewall di Azure](../../../../firewall/overview.md) per instradare tutto il traffico Internet in uscita e consentire il traffico Internet in ingresso tramite jump box. Le regole del firewall predefinite bloccano tutto il traffico in ingresso e in uscita Internet e le regole devono essere configurate dopo la distribuzione, come applicabile.
- [Gruppi di sicurezza di rete](../../../../virtual-network/network-security-group-how-it-works.md) (gruppi) assegnati a tutte le subnet (ad eccezione delle subnet di proprietà del servizio come Azure Bastion, gateway e Azure firewall) configurati per bloccare tutto il traffico in ingresso e in uscita Internet.
- [Gruppi di sicurezza delle applicazioni](../../../../virtual-network/application-security-groups.md) per consentire il raggruppamento di macchine virtuali di Azure per applicare criteri di sicurezza di rete comuni.
- [Tabelle di route](../../../../virtual-network/manage-route-table.md) per il routing di tutto il traffico Internet in uscita dalle subnet attraverso il firewall. (Le regole firewall e NSG di Azure devono essere configurate dopo la distribuzione per aprire la connettività).
- [Azure Network Watcher](../../../../network-watcher/network-watcher-monitoring-overview.md) per monitorare, diagnosticare e visualizzare le metriche delle risorse nella rete virtuale di Azure.
- [Protezione DDoS di Azure standard](../../../../ddos-protection/ddos-protection-overview.md) per proteggere le risorse di Azure da attacchi DDoS.
- [Azure Bastion](../../../../bastion/bastion-overview.md) per offrire connettività trasparente e sicura a una macchina virtuale che non richiede un indirizzo IP pubblico, un agente o un software client speciale.
- [Gateway VPN di Azure](../../../../vpn-gateway/vpn-gateway-about-vpngateways.md) per abilitare il traffico crittografato tra una rete virtuale di Azure e una posizione locale tramite la rete Internet pubblica.

> [!NOTE] 
> Azure Security benchmark Foundation definisce un'architettura di base per i carichi di lavoro. Il diagramma dell'architettura precedente include diverse risorse teoriche per illustrare il potenziale uso delle subnet. È ancora necessario distribuire i carichi di lavoro in questa architettura di base.

## <a name="next-steps"></a>Passaggi successivi

È stata esaminata la panoramica e l'architettura dell'esempio di progetto Azure Security benchmark Foundation.

> [!div class="nextstepaction"]
> [Progetto di Azure Security benchmark Foundation-procedura di distribuzione](./deploy.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).
