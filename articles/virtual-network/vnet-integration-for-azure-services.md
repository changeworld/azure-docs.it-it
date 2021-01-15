---
title: Integrazione della rete virtuale dei servizi di Azure per l'isolamento rete
titlesuffix: Azure Virtual Network
description: Questo articolo descrive i diversi metodi di integrazione di un servizio di Azure in una rete virtuale che consente di accedere in modo sicuro al servizio di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: kumud
ms.openlocfilehash: a29454f09268406511730c63dcc3bc3063b909c6
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218655"
---
# <a name="integrate-azure-services-with-virtual-networks-for-network-isolation"></a>Integrare i servizi di Azure con reti virtuali per l'isolamento rete

L'integrazione della rete virtuale (VNet) per un servizio di Azure consente di bloccare l'accesso al servizio solo all'infrastruttura di rete virtuale. L'infrastruttura di VNet include anche reti virtuali con peering e reti locali.

L'integrazione con VNet offre ai servizi di Azure i vantaggi dell'isolamento rete e può essere eseguita da uno o più dei metodi seguenti:
- [Distribuzione di istanze dedicate del servizio in una rete virtuale](virtual-network-for-azure-services.md). I servizi sono accessibili privatamente all'interno della rete virtuale e da reti locali.
- Uso di un [endpoint privato](../private-link/private-endpoint-overview.md) che si connette privatamente e in modo sicuro a un servizio basato sul [collegamento privato di Azure](../private-link/private-link-overview.md). L'endpoint privato usa un indirizzo IP privato della VNet, portando il servizio nella rete virtuale.
- Accesso al servizio mediante endpoint pubblici mediante l'estensione di una rete virtuale al servizio tramite gli [endpoint di servizio](virtual-network-service-endpoints-overview.md). Gli endpoint di servizio consentono di proteggere le risorse del servizio nella rete virtuale.
- Uso dei [tag di servizio](service-tags-overview.md) per consentire o negare il traffico alle risorse di Azure da e verso gli endpoint IP pubblici.

## <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Distribuire servizi di Azure dedicati in reti virtuali

Quando si distribuiscono i servizi di Azure in una rete virtuale, è possibile comunicare con le risorse dei servizi privatamente, tramite indirizzi IP privati.

![Distribuire servizi di Azure dedicati in reti virtuali](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

La distribuzione di un servizio di Azure dedicato nella rete virtuale offre le funzionalità seguenti:
- Le risorse all'interno della rete virtuale possono comunicare tra loro privatamente, tramite indirizzi IP privati, ad esempio trasferendo direttamente i dati tra HDInsight e SQL Server in esecuzione in una macchina virtuale, nella rete virtuale.
- Le risorse locali possono accedere alle risorse in una rete virtuale usando indirizzi IP privati su una VPN da sito a sito (gateway VPN) o ExpressRoute.
- Le reti virtuali possono essere associate tramite peering per consentire alle risorse delle reti virtuali di comunicare tra loro, usando indirizzi IP privati.
- Le istanze del servizio in una rete virtuale sono in genere completamente gestite dal servizio di Azure. Questo include il monitoraggio dell'integrità delle risorse e la scalabilità con il carico.
- Le istanze dei servizi vengono distribuite in una subnet in una rete virtuale. L'accesso alla rete in ingresso e in uscita deve essere consentito tramite gruppi di sicurezza di rete per la subnet, in base alle indicazioni fornite dal servizio.
- Alcuni servizi impongono anche restrizioni per la subnet in cui vengono distribuiti, limitando l'applicazione dei criteri, le route o la combinazione di macchine virtuali e risorse del servizio all'interno della stessa subnet. Verificare con ogni servizio le restrizioni specifiche che potrebbero cambiare nel corso del tempo. Esempi di tali servizi sono Azure NetApp Files, HSM dedicato, istanze di Azure Container, servizio app.
- Facoltativamente, i servizi potrebbero richiedere una subnet delegata come identificatore esplicito del fatto che una subnet possa ospitare un servizio specifico. Grazie alla delega, i servizi ottendono autorizzazioni esplicite per creare le risorse specifiche del servizio nella subnet delegata.
- Vedere un esempio di risposta all'API REST in una rete virtuale con una subnet delegata. Un elenco completo dei servizi che usano il modello di subnet delegata può essere ottenuto tramite l'API Deleghe disponibili.

Per un elenco di servizi che possono essere distribuiti in una rete virtuale, vedere [distribuire servizi di Azure dedicati in reti virtuali](virtual-network-for-azure-services.md).

## <a name="private-link-and-private-endpoints"></a>Collegamento privato ed endpoint privati

È possibile usare endpoint privati per consentire l'ingresso di eventi direttamente dalla rete virtuale alla risorsa di Azure in modo sicuro tramite un collegamento privato senza passare attraverso la rete Internet pubblica. Un endpoint privato è un'interfaccia di rete speciale per un servizio di Azure nella rete virtuale. Quando si crea un endpoint privato per la risorsa di Azure, fornisce connettività sicura tra i client nella rete virtuale e la risorsa di Azure. All'endpoint privato viene assegnato un indirizzo IP dall'intervallo di indirizzi IP della rete virtuale. La connessione tra l'endpoint privato e il servizio di Azure usa un collegamento privato protetto.

Nell'esempio seguente viene illustrato l'accesso privato di un endpoint privato della risorsa griglia di eventi che garantisce una connettività sicura tra i client in una rete virtuale e una risorsa griglia di eventi.

![Accesso privato della risorsa database SQL tramite endpoint privato](./media/network-isolation/architecture-diagram.png)

Per altre informazioni sul collegamento privato e un elenco di servizi di Azure supportati, vedere [che cos'è un collegamento privato?](../private-link/private-link-overview.md)

## <a name="service-endpoints"></a>Endpoint di servizio
L'endpoint del servizio VNet offre connettività sicura e diretta ai servizi di Azure tramite una route ottimizzata sulla rete backbone di Azure. Gli endpoint consentono di associare le risorse critiche dei servizi di Azure solo alle proprie reti virtuali. Gli endpoint di servizio consentono agli indirizzi IP privati in VNet di raggiungere l'endpoint di un servizio di Azure senza la necessità di un indirizzo IP pubblico nella VNet.

![Associazione di servizi di Azure a reti virtuali](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

Per altre informazioni, vedere [endpoint del servizio rete virtuale](virtual-network-service-endpoints-overview.md)

## <a name="service-tags"></a>Tag di servizio

Un tag del servizio rappresenta un gruppo di prefissi di indirizzi IP di un determinato servizio di Azure. Usando i tag del servizio, è possibile definire i controlli di accesso alla rete nei [gruppi di sicurezza di rete](./network-security-groups-overview.md#security-rules) o nel firewall di [Azure](../firewall/service-tags.md). Specificando il nome del tag di servizio (ad esempio, AzureEventGrid) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente.

![Consentire o negare il traffico tramite tag di servizio](./media/network-isolation/service-tags.png)

È possibile usare i tag del servizio per ottenere l'isolamento rete e proteggere le risorse di Azure da Internet in generale durante l'accesso ai servizi di Azure con endpoint pubblici. Creare regole del gruppo di sicurezza di rete in ingresso/uscita per negare il traffico da/verso **Internet** e consentire il traffico da/verso **AzureCloud** o altri [tag del servizio](service-tags-overview.md#available-service-tags) disponibili dei servizi di Azure specifici.

Per altre informazioni sui tag di servizio e i servizi di Azure che li supportano, vedere [Cenni preliminari sui tag di servizio](service-tags-overview.md)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [integrare l'app con una rete di Azure](../app-service/web-sites-integrate-with-vnet.md).
- Informazioni su come [limitare l'accesso alle risorse usando i tag del servizio](tutorial-restrict-network-access-to-resources.md).
- Informazioni su come [connettersi privatamente a un account Azure Cosmos usando il collegamento privato di Azure](../private-link/tutorial-private-endpoint-cosmosdb-portal.md).