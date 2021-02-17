---
title: Funzionalità di Firewall di Azure
description: Informazioni sulle funzionalità del firewall di Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 9f89d84fc7033645b2b094e9f40a1d85b076623b
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100544834"
---
# <a name="azure-firewall-features"></a>Funzionalità di Firewall di Azure

Il [firewall di Azure](overview.md) è un servizio di sicurezza di rete gestito e basato sul cloud che protegge le risorse della rete virtuale di Azure.

![Panoramica del firewall](media/overview/firewall-threat.png)

Il firewall di Azure include le funzionalità seguenti:

- Disponibilità elevata predefinita
- Zone di disponibilità
- Scalabilità del cloud senza restrizioni
- Regole di filtro del nome di dominio completo dell'applicazione
- Regole di filtro per il traffico di rete
- Tag FQDN
- Tag di servizio
- Intelligence per le minacce
- Supporto SNAT in uscita
- Supporto DNAT in ingresso
- Più indirizzi IP pubblici
- Registrazione di Monitoraggio di Azure
- Tunneling forzato
- Categorie Web (anteprima)
- Certificazioni

## <a name="built-in-high-availability"></a>Disponibilità elevata predefinita

La disponibilità elevata è incorporata, quindi non sono necessari altri bilanciamenti del carico e non è necessario eseguire alcuna configurazione.

## <a name="availability-zones"></a>Zone di disponibilità

Firewall di Azure può essere configurato durante la distribuzione con più zone di disponibilità per una maggiore disponibilità. Con le zone di disponibilità, la disponibilità viene aumentata a un tempo di attività pari al 99,99%. Per altre informazioni, vedere il [Contratto di servizio](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/) per Firewall di Azure. Il contratto di servizio con un tempo di attività del 99,99% è disponibile quando si selezionano due o più zone di disponibilità.

È anche possibile associare Firewall di Azure a una zona specifica solo per motivi di prossimità, tramite il contratto di servizio standard pari al 99,95%.

La distribuzione di un firewall in una zona di disponibilità non comporta costi aggiuntivi. Tuttavia, sono presenti costi aggiuntivi per i trasferimenti di dati in ingresso e in uscita associati a zone di disponibilità. Per altre informazioni, vedere [Dettagli sui prezzi per la larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/).

Le zone di disponibilità di Firewall di Azure sono disponibili nelle aree che supportano le zone di disponibilità. Per altre informazioni, vedere [Aree che supportano le zone di disponibilità in Azure](../availability-zones/az-region.md)

> [!NOTE]
> Le zone di disponibilità possono essere configurate solo durante la distribuzione. Non è possibile configurare un firewall esistente per includere le zone di disponibilità.

Per altre informazioni sulle zone di disponibilità, vedere [Aree e zone di disponibilità in Azure](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>Scalabilità del cloud senza restrizioni

È possibile aumentare le prestazioni di Firewall di Azure a seconda delle esigenze in modo da soddisfare i mutevoli flussi del traffico di rete senza dover stabilire un budget per i periodi di traffico più intenso.

## <a name="application-fqdn-filtering-rules"></a>Regole di filtro del nome di dominio completo dell'applicazione

È possibile limitare il traffico HTTP/S in uscita o il traffico SQL di Azure a un elenco specificato di nomi di dominio completi (FQDN), inclusi i caratteri jolly. Questa funzionalità non richiede la terminazione TLS.

## <a name="network-traffic-filtering-rules"></a>Regole di filtro per il traffico di rete

È possibile creare in modo centralizzato regole di filtro di rete per *consentire* o *negare* il traffico in base all'indirizzo IP di origine e di destinazione, alla porta e al protocollo. Firewall di Azure è un servizio con stato completo, quindi in grado di distinguere i pacchetti validi per diversi tipi di connessioni. Le regole vengono applicate e registrate in più sottoscrizioni e reti virtuali.

## <a name="fqdn-tags"></a>Tag FQDN

I [tag FQDN](fqdn-tags.md) rendono più semplice consentire il traffico di rete noto del servizio di Azure attraverso il firewall. Ad esempio, si supponga di voler consentire il traffico di rete di Windows Update attraverso il firewall. Creare una regola di applicazione e includere il tag di Windows Update. A questo punto il traffico di rete da Windows Update può attraversare il firewall.

## <a name="service-tags"></a>Tag di servizio

Un [tag di servizio](service-tags.md) rappresenta un gruppo di prefissi di indirizzo IP che consente di ridurre al minimo la complessità nella creazione di regole di sicurezza. Non è possibile creare tag di servizio personalizzati, né specificare gli indirizzi IP da includere in un tag. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che aggiorna automaticamente il tag in caso di modifica degli indirizzi.

## <a name="threat-intelligence"></a>Intelligence per le minacce

I filtri basati sull'[intelligence per le minacce](threat-intel.md) possono essere abilitati per il firewall per la creazione di avvisi e il rifiuto del traffico da o verso indirizzi IP e domini dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft.

## <a name="outbound-snat-support"></a>Supporto SNAT in uscita

Tutti gli indirizzi IP del traffico di rete virtuale in uscita vengono convertiti nell'indirizzo IP pubblico di Firewall di Azure (Source Network Address Translation). È possibile identificare e consentire il traffico proveniente dalla rete virtuale a destinazioni Internet remote. Firewall di Azure non esegue la conversione degli indirizzi IP di origine (SNAT, Source Network Address Translation) quando l'indirizzo IP di destinazione è un intervallo di indirizzi IP privati in base allo standard [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

Se l'organizzazione usa un intervallo di indirizzi IP pubblici per le reti private, Firewall di Azure invierà il traffico tramite SNAT a uno degli indirizzi IP privati firewall in AzureFirewallSubnet. È possibile configurare Firewall di Azure in modo da **non** usare SNAT per l'intervallo di indirizzi IP pubblici. Per altre informazioni, vedere [Intervalli di indirizzi IP privati SNAT di Firewall di Azure](snat-private-range.md).

## <a name="inbound-dnat-support"></a>Supporto DNAT in ingresso

Il traffico di rete Internet in ingresso all'indirizzo IP pubblico del firewall viene convertito (Destination Network Address Translation) e filtrato per gli indirizzi IP nelle reti virtuali.

## <a name="multiple-public-ip-addresses"></a>Più indirizzi IP pubblici

È possibile associare al firewall [più indirizzi IP pubblici](deploy-multi-public-ip-powershell.md) (fino a 250).

Questo supporta gli scenari seguenti:

- **DNAT** - È possibile convertire più istanze di porta standard per i server back-end. Se ad esempio si dispone di due indirizzi IP pubblici, è possibile convertire la porta TCP 3389 (RDP) per entrambi gli indirizzi IP.
- **SNAT** -sono disponibili altre porte per le connessioni SNAT in uscita, riducendo il rischio di esaurimento delle porte SNAT. Al momento, Firewall di Azure seleziona in modo casuale l'indirizzo IP pubblico di origine da usare per una connessione. Se sono presenti filtri downstream nella rete, è necessario consentire tutti gli indirizzi IP pubblici associati al firewall. Per semplificare questa configurazione, provare a usare un [prefisso di indirizzo IP pubblico](../virtual-network/public-ip-address-prefix.md).

## <a name="azure-monitor-logging"></a>Registrazione di Monitoraggio di Azure

Tutti gli eventi vengono integrati con Monitoraggio di Azure, per consentire di archiviare i log in un account di archiviazione e di trasmettere i flussi di eventi all'hub eventi o inviarli ai log di Monitoraggio di Azure. Per gli esempi di log di monitoraggio di Azure, vedere [log di monitoraggio di Azure per il firewall di Azure](./firewall-workbook.md).

Per altre informazioni, vedere [Esercitazione: Monitorare i log e le metriche di Firewall di Azure](./firewall-diagnostics.md). 

La cartella di lavoro del firewall di Azure offre un'area di disegno flessibile per l'analisi dei dati del firewall È possibile usarlo per creare report visivi avanzati all'interno del portale di Azure. Per altre informazioni, vedere [monitorare i log con la cartella di lavoro del firewall di Azure](firewall-workbook.md).

## <a name="forced-tunneling"></a>Tunneling forzato

È possibile configurare Firewall di Azure per instradare tutto il traffico associato a Internet a un hop successivo designato anziché passare direttamente a Internet. Ad esempio, è possibile che si disponga di un firewall perimetrale locale o di un'altra appliance virtuale di rete per elaborare il traffico di rete prima che venga passato a Internet. Per altre informazioni, vedere [Tunneling forzato di Firewall di Azure](forced-tunneling.md).

## <a name="web-categories-preview"></a>Categorie Web (anteprima)

Le categorie Web consentono agli amministratori di consentire o negare l'accesso degli utenti alle categorie di siti Web, ad esempio siti Web di gioco, siti Web di social media e altri. Le categorie Web sono incluse in Azure firewall standard, ma sono più ottimizzate in Azure Firewall Premium Preview. Rispetto alla funzionalità delle categorie Web dello SKU standard che corrisponde alla categoria basata su un nome di dominio completo, lo SKU Premium corrisponde alla categoria in base all'intero URL per il traffico HTTP e HTTPS. Per altre informazioni su Azure Firewall Premium Preview, vedere [funzionalità di anteprima di Azure Firewall Premium](premium-features.md).

Ad esempio, se il firewall di Azure intercetta una richiesta HTTPS per `www.google.com/news` , è prevista la categorizzazione seguente: 

- Standard firewall: verrà esaminata solo la parte FQDN, quindi `www.google.com` verrà categorizzata come *motore di ricerca*. 

- Firewall Premium: verrà esaminato l'URL completo, quindi `www.google.com/news` verranno categorizzate come *notizie*.

Le categorie sono organizzate in base alla gravità sotto **responsabilità**, **larghezza di banda elevata**, **uso aziendale**, **perdita di produttività**, **navigazione generale** e senza **categoria**.

### <a name="category-exceptions"></a>Eccezioni di categoria

È possibile creare eccezioni alle regole di categoria Web. Creare una raccolta di regole Consenti o nega separata con una priorità più alta all'interno del gruppo di raccolta regole. Ad esempio, è possibile configurare una raccolta di regole che consente la `www.linkedin.com` priorità 100, con una raccolta di regole che nega la **rete sociale** con priorità 200. Verrà creata l'eccezione per la categoria Web di **social networking** predefinita.



## <a name="certifications"></a>Certificazioni

Firewall di Azure è conforme a PCI (Payment Card Industry), SOC (Service Organization Controls), ISO (International Organization for Standardization) e ICSA Labs. Per altre informazioni, vedere [Certificazioni di conformità di Firewall di Azure](compliance-certifications.md).

## <a name="next-steps"></a>Passaggi successivi

- [Logica di elaborazione delle regole del Firewall di Azure](rule-processing.md)