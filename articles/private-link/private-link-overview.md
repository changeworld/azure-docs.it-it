---
title: Che cos'è Collegamento privato di Azure?
description: Panoramica su funzionalità, architettura e implementazione di Collegamento privato di Azure. Informazioni sul funzionamento di Endpoint privati di Azure e del servizio Collegamento privato di Azure e su come usarli.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: overview
ms.date: 03/15/2021
ms.author: allensu
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 19a4c6697c161a78bfb1425009bd0b142dece876
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057236"
---
# <a name="what-is-azure-private-link"></a>Che cos'è Collegamento privato di Azure? 
Collegamento privato di Azure consente di accedere ai servizi PaaS di Azure, ad esempio Archiviazione di Azure e Database SQL, nonché ai servizi di proprietà di clienti/partner ospitati in Azure tramite un [endpoint privato](private-endpoint-overview.md) nella rete virtuale.

Il traffico tra la rete virtuale e il servizio attraversa la rete del backbone Microsoft. L'esposizione del servizio sulla rete Internet pubblica non è più necessaria. È possibile creare un [servizio Collegamento privato](private-link-service-overview.md) personale nella rete virtuale e distribuirlo ai clienti. Collegamento privato di Azure offre un'esperienza coerente di configurazione e utilizzo per i servizi PaaS di Azure, i servizi di proprietà dei clienti e quelli condivisi dei partner.

> [!IMPORTANT]
> Collegamento privato di Azure è ora disponibile a livello generale. L'endpoint privato e Collegamento privato (il servizio alla base di Load Balancer Standard) sono disponibili a livello generale. L'onboarding di soluzioni Azure PaaS diverse in Collegamento privato di Azure verrà eseguito in base a pianificazioni diverse. Vedere la pagina relativa alla [disponibilità del collegamento privato](availability.md) per uno stato accurato di Azure PaaS sul collegamento privato. Per informazioni sulle limitazioni note, vedere [Endpoint privato](private-endpoint-overview.md#limitations) e [Servizio Collegamento privato](private-link-service-overview.md#limitations). 

:::image type="content" source="./media/private-link-overview/private-link-center.png" alt-text="Centro collegamento privato di Azure nel portale di Azure" border="false":::

## <a name="key-benefits"></a>Vantaggi principali
Collegamento privato di Azure offre i vantaggi descritti di seguito.  
- **Accesso privato ai servizi nella piattaforma Azure**: connettere la rete virtuale ai servizi in Azure senza un indirizzo IP pubblico all'origine o alla destinazione. I provider possono offrire i servizi nella loro rete virtuale e i clienti possono accedervi nella loro rete virtuale locale. La piattaforma di Collegamento privato gestirà la connettività tra l'utente e i servizi tramite la rete backbone di Azure. 
 
- **Reti locali e con peering**: accedere ai servizi in esecuzione in Azure dall'ambiente locale tramite peering privato ExpressRoute, tunnel VPN e reti virtuali con peering usando endpoint privati. Non è necessario configurare il peering Microsoft ExpressRoute o attraversare Internet per raggiungere il servizio. Collegamento privato offre un modo sicuro per eseguire la migrazione dei carichi di lavoro ad Azure.
 
- **Protezione dall'esfiltrazione di dati**: un endpoint privato viene mappato a un'istanza di una risorsa PaaS invece che all'intero servizio. Gli utenti possono connettersi solo alla risorsa specifica. L'accesso a qualsiasi altra risorsa del servizio è bloccato. Questo meccanismo offre protezione dai rischi di perdita dei dati. 
 
- **Copertura globale**: è possibile connettersi privatamente a servizi in esecuzione in altre aree. La rete virtuale dell'utente può pertanto trovarsi nell'area A e connettersi ai servizi con Collegamento privato nell'area B.  
 
- **Estensione a servizi personalizzati**: la stessa esperienza e le stesse funzionalità possono essere sfruttate per offrire privatamente un servizio personalizzato agli utenti in Azure. Posizionando un servizio dietro Azure Load Balancer, è possibile abilitarlo per Collegamento privato. L'utente potrà quindi connettersi direttamente al servizio usando un endpoint privato nella propria rete virtuale. È possibile gestire queste richieste di connessione con un semplice flusso di chiamate di approvazione. Collegamento privato di Azure funziona anche per utenti e servizi appartenenti a tenant di Azure Active Directory diversi. 

> [!NOTE]
> I collegamenti privati di Azure, insieme alla rete virtuale di Azure, si estendono in [zone di disponibilità di Azure](../availability-zones/az-overview.md) e pertanto sono resilienti per la zona. Per garantire un'elevata disponibilità per la risorsa di Azure usando un endpoint privato, assicurarsi che la risorsa sia resiliente alla zona.

## <a name="availability"></a>Disponibilità 

Per informazioni sui servizi di Azure che supportano il collegamento privato, vedere [disponibilità del collegamento privato di Azure](availability.md).

Per le notifiche più aggiornate, vedere la [pagina degli aggiornamenti relativi al collegamento privato di Azure](https://azure.microsoft.com/updates/?product=private-link).

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

Collegamento privato di Azure prevede l'integrazione con Monitoraggio di Azure. Questa combinazione consente:

 - Archiviazione di log in un account di archiviazione.
 - Streaming di eventi nell'hub eventi.
 - Registrazione di Monitoraggio di Azure.

In Monitoraggio di Azure è possibile accedere alle informazioni seguenti. 
- **Endpoint privato**: 
    - dati elaborati dall'endpoint privato (IN/OUT)
 
- **Servizio Collegamento privato**:
    - Dati elaborati dal servizio Collegamento privato (IN/OUT)
    - Disponibilità di porte NAT  
 
## <a name="pricing"></a>Prezzi   
Per informazioni dettagliate sui prezzi, vedere [Prezzi di Collegamento privato di Azure](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Domande frequenti  
Per domande frequenti, vedere [Domande frequenti su Collegamento privato di Azure](private-link-faq.md).
 
## <a name="limits"></a>Limiti  
Per informazioni sui limiti, vedere [Limiti di Collegamento privato di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="service-level-agreement"></a>Contratto di servizio
Per informazioni, vedere [Contratto di servizio per Collegamento privato di Azure](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/).

## <a name="next-steps"></a>Passaggi successivi

- [Avvio rapido: Creare un endpoint privato con il portale di Azure](create-private-endpoint-portal.md)
- [Avvio rapido: Creare un servizio Collegamento privato usando il portale di Azure](create-private-link-service-portal.md)
