---
title: Risoluzione dei problemi relativi a timeout e errori di risposta del client con gestione API
description: Risolvere gli errori di connessione intermittenti e i problemi di latenza correlati in gestione API
author: vladvino
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: apimpm
ms.service: api-management
ms.openlocfilehash: 6cace4a02c8d45cacbbc34e9778b5c4a78ada27f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576518"
---
# <a name="troubleshooting-client-response-timeouts-and-errors-with-api-management"></a>Risoluzione dei problemi relativi a timeout e errori di risposta del client con gestione API

Questo articolo consente di risolvere gli errori di connessione intermittenti e i problemi di latenza correlati in [gestione API di Azure](./api-management-key-concepts.md). In particolare, in questo articolo vengono fornite informazioni e risoluzione dei problemi per l'esaurimento delle porte SNAT (Source Address Network Translation). Se è necessaria ulteriore assistenza, contattare gli esperti di Azure al [supporto della community di Azure](https://azure.microsoft.com/support/community/) o archiviare una richiesta di supporto con il [supporto tecnico di Azure](https://azure.microsoft.com/support/options/).

## <a name="symptoms"></a>Sintomi

Le applicazioni client che chiamano le API tramite il servizio gestione API (gestione API) possono presentare uno o più dei seguenti sintomi:

* Errori intermittenti HTTP 500
* Messaggi di errore di timeout

Questi sintomi si manifestano come istanze di `BackendConnectionFailure` nei [log delle risorse di monitoraggio di Azure](../azure-monitor/essentials/resource-logs.md).

## <a name="cause"></a>Causa

Questo modello di sintomi spesso si verifica a causa di limiti di porta Network Address Translation (SNAT) con il servizio gestione API.

Ogni volta che un client chiama una delle API di gestione API, il servizio gestione API di Azure apre una porta SNAT per accedere all'API back-end. Come descritto in [connessioni in uscita in Azure](../load-balancer/load-balancer-outbound-connections.md), azure USA Network Address Translation di origine (SNAT) e una Load Balancer (non esposta ai clienti) per comunicare con endpoint esterni ad Azure nello spazio degli indirizzi IP pubblici, nonché per gli endpoint interni ad Azure che non usano gli [endpoint del servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md). Questa situazione è applicabile solo alle API back-end esposte sugli indirizzi IP pubblici.

A ogni istanza del servizio gestione API viene inizialmente assegnato un numero pre-allocato di porte SNAT. Tale limite influiscono sull'apertura delle connessioni alla stessa combinazione host e porta. Le porte SNAT vengono utilizzate quando si hanno ripetute chiamate alla stessa combinazione di indirizzo e porta. Una volta rilasciata una porta SNAT, la porta sarà disponibile per il riutilizzo in base alle esigenze. Il servizio di bilanciamento del carico di rete di Azure recupera le porte SNAT dalle connessioni chiuse solo dopo quattro minuti di attesa.

Una rapida successione di richieste client alle API può esaurire la quota preallocata di porte SNAT se queste porte non sono chiuse e riciclate abbastanza velocemente, impedendo al servizio gestione API di elaborare le richieste client in modo tempestivo.

## <a name="mitigations-and-solutions"></a>Mitigazioni e soluzioni

Per risolvere il problema dell'esaurimento delle porte SNAT, è necessario innanzitutto diagnosticare e ottimizzare le prestazioni dei servizi back-end.

Le strategie generali per mitigare l'esaurimento delle porte SNAT sono illustrate in [risoluzione dei problemi relativi alle connessioni in uscita](../load-balancer/troubleshoot-outbound-connection.md) da *Azure Load Balancer* documentazione. Di queste strategie, le seguenti sono applicabili a gestione API.

### <a name="scale-your-apim-instance"></a>Ridimensionare l'istanza di gestione API

A ogni istanza di gestione API vengono allocate un numero di porte SNAT, basate su unità gestione API. È possibile allocare altre porte SNAT ridimensionando l'istanza di gestione API con unità aggiuntive. Per altre informazioni, vedere [ridimensionare il servizio gestione API](upgrade-and-scale.md#scale-your-api-management-service)

> [!NOTE]
> L'utilizzo delle porte SNAT non è attualmente disponibile come metrica per le unità di gestione API per la scalabilità automatica.

### <a name="use-multiple-ips-for-your-backend-urls"></a>Usare più indirizzi IP per gli URL back-end

Ogni connessione dall'istanza di gestione API allo stesso indirizzo IP di destinazione e alla porta di destinazione del servizio back-end userà una porta SNAT per mantenere un flusso di traffico distinto. Senza porte SNAT diverse per il traffico di ritorno dal servizio in background, gestione API non avrebbe alcun modo per separare una risposta da un'altra.

Poiché le porte SNAT possono essere riutilizzate se l'indirizzo IP di destinazione o la porta di destinazione sono diversi, un altro modo per evitare l'esaurimento delle porte SNAT consiste nell'usare più indirizzi IP per gli URL del servizio back-end.

Per altre informazioni, vedere [Azure Load Balancer proxy in uscita](../load-balancer/load-balancer-outbound-connections.md).

### <a name="place-your-apim-and-backend-service-in-the-same-vnet"></a>Inserire il gestione API e il servizio back-end nella stessa VNet

Se l'API back-end è ospitata in un servizio di Azure che supporta gli *endpoint di servizio* , ad esempio il servizio app, è possibile evitare i problemi di esaurimento delle porte SNAT inserendo l'istanza di gestione API e il servizio back-end nella stessa rete virtuale ed esponendolo tramite [endpoint di servizio](../virtual-network/virtual-network-service-endpoints-overview.md) o [endpoint privati](../private-link/private-endpoint-overview.md). Quando si usa un VNet comune e si posizionano gli endpoint di servizio nella subnet di integrazione, il traffico in uscita dall'istanza di gestione API a tali servizi Ignora Internet, evitando così restrizioni per le porte SNAT. Analogamente, se si usa un endpoint VNet e privato, non si avranno problemi di porta SNAT in uscita per tale destinazione.

Per informazioni dettagliate, vedere [come usare gestione API di Azure con le reti virtuali](api-management-using-with-vnet.md) e [integrare il servizio app con una rete virtuale di Azure](../app-service/web-sites-integrate-with-vnet.md).

### <a name="place-your-apim-in-a-virtual-network-and-route-outbound-calls-to-azure-firewall"></a>Inserire i gestione API in una rete virtuale e indirizzare le chiamate in uscita al firewall di Azure

Analogamente all'inserimento dei servizi gestione API e back-end in una rete virtuale, è possibile usare il firewall di Azure in una VNet con il servizio gestione API, quindi instradare le chiamate gestione API in uscita al firewall di Azure. Tra gestione API e il firewall di Azure (che si trova nella stessa VNet), non sono necessarie porte SNAT. Per le connessioni SNAT ai servizi back-end, il firewall di Azure dispone di 64.000 porte disponibili, una quantità molto più elevata rispetto a quella allocata alle istanze di gestione API.

Per ulteriori informazioni, vedere la documentazione del [firewall di Azure](../firewall/overview.md) .

### <a name="consider-response-caching-and-other-backend-performance-tuning"></a>Considerare la memorizzazione nella cache delle risposte e altre ottimizzazioni delle prestazioni back-end

Un'altra possibile mitigazione da considerare è il miglioramento dei tempi di elaborazione per le API back-end. Un modo per eseguire questa operazione consiste nel configurare alcune API con la memorizzazione delle risposte nella cache per ridurre la latenza tra le applicazioni client che chiamano l'API e il carico del back-end gestione API.

Per altre informazioni, vedere [aggiungere la memorizzazione nella cache per migliorare le prestazioni in gestione API di Azure](api-management-howto-cache.md).

### <a name="consider-implementing-access-restriction-policies"></a>Considerare l'implementazione di criteri di restrizione di accesso

Se è opportuno per lo scenario aziendale, è possibile implementare i criteri di restrizione di accesso per il prodotto gestione API. Ad esempio, i `rate-limit-by-key` criteri possono essere usati per impedire picchi di utilizzo delle API in base alle singole chiavi limitando la frequenza delle chiamate per un periodo di tempo specificato.

Per altre informazioni, vedere [criteri di restrizione di accesso per gestione API](api-management-access-restriction-policies.md) .

## <a name="see-also"></a>Vedi anche

* [Azure Load Balancer: risoluzione degli errori delle connessioni in uscita](../load-balancer/troubleshoot-outbound-connection.md)
* [Servizio app Azure: risoluzione dei problemi relativi agli errori di connessione in uscita intermittenti](../app-service/troubleshoot-intermittent-outbound-connection-errors.md)
