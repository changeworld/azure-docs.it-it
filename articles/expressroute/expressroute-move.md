---
title: 'ExpressRoute: spostamento dei circuiti dal modello classico al Azure Resource Manager'
description: Informazioni su cosa accade quando si sposta un circuito Azure ExpressRoute dal modello di distribuzione classica al modello Azure Resource Manager.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/15/2020
ms.author: duau
ms.openlocfilehash: dcba2e9de2b37e8c432f94781b3c4c369ad52395
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97807942"
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Spostamento dei circuiti ExpressRoute dal modello di distribuzione classica al modello di distribuzione Resource Manager
Questo articolo fornisce una panoramica di ciò che accade quando si sposta un circuito Azure ExpressRoute dal modello di distribuzione classica al Azure Resource Manager.

È possibile usare un singolo circuito ExpressRoute per connettere le reti virtuali distribuite nei modelli di distribuzione classica e Gestione risorse.

![Circuito ExpressRoute collegato a reti virtuali in entrambi i modelli di distribuzione](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>Circuiti ExpressRoute creati nel modello di distribuzione classica
I circuiti ExpressRoute creati nel modello di distribuzione classica devono prima eseguire la migrazione al modello di distribuzione Gestione risorse. Solo in questo modo è possibile abilitare la connettività ai modelli di distribuzione classica e Gestione risorse. La connettività non è stata persa o è stata interrotta quando è in corso lo spostamento di una connessione. Vengono conservati tutti i collegamenti di rete da circuito a virtuale nel modello di distribuzione classica all'interno della stessa sottoscrizione e tra sottoscrizioni.

Al termine dello spostamento, il circuito ExpressRoute si comporterà esattamente come un circuito ExpressRoute creato nel modello di distribuzione Gestione risorse. Ora è possibile creare le connessioni alle reti virtuali nel modello di distribuzione Resource Manager.

Dopo aver spostato il circuito ExpressRoute nel modello di distribuzione di Resource Manager, è possibile gestirlo solo in Gestione risorse modello di distribuzione. Le operazioni per la gestione dei peering, l'aggiornamento delle proprietà del circuito e l'eliminazione dei circuiti saranno disponibili solo tramite il modello di distribuzione Gestione risorse. Per ulteriori informazioni su come gestire l'accesso a entrambi i modelli di distribuzione, vedere la sezione seguente.

Non è necessario coinvolgere il provider di connettività per spostare il circuito nel modello di distribuzione Gestione risorse.

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>Circuiti ExpressRoute creati nel modello di distribuzione Resource Manager
È possibile fare in modo che i circuiti ExpressRoute creati nel modello di distribuzione Resource Manager siano accessibili da entrambi i modelli di distribuzione. Qualsiasi circuito ExpressRoute nella sottoscrizione può essere configurato per avere accesso da entrambi i modelli di distribuzione.

* Per impostazione predefinita, i circuiti ExpressRoute creati nel modello di distribuzione Gestione risorse non hanno accesso al modello di distribuzione classica.
* Per impostazione predefinita, i circuiti ExpressRoute che sono stati spostati dal modello di distribuzione classica al modello di distribuzione Gestione risorse sono accessibili da entrambi i modelli di distribuzione.
* Un circuito ExpressRoute ha sempre accesso al modello di distribuzione Gestione risorse, indipendentemente dal fatto che sia stato creato nel modello di distribuzione Gestione risorse o classico. È possibile creare connessioni alle reti virtuali seguendo le istruzioni su [come collegare le reti virtuali](expressroute-howto-linkvnet-arm.md).
* L'accesso al modello di distribuzione classica è controllato dal parametro **allowClassicOperations** nel circuito ExpressRoute.

> [!IMPORTANT]
> Vengono applicate tutte le quote documentate nella pagina relativa ai [limiti dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md) . Ad esempio, un circuito standard può avere al massimo 10 collegamenti/connessioni di rete virtuale sia nel modello di distribuzione classica che nel modello di distribuzione Resource Manager.
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>Controllo dell'accesso al modello di distribuzione classica
È possibile abilitare un circuito ExpressRoute per il collegamento alle reti virtuali in entrambi i modelli di distribuzione. A tale scopo, impostare il parametro **allowClassicOperations** nel circuito ExpressRoute.

Impostando **allowClassicOperations** su TRUE è possibile collegare le reti virtuali da entrambi i modelli di distribuzione al circuito ExpressRoute. 
* Per collegare le reti virtuali nel modello di distribuzione classica, vedere [come collegare le reti virtuali per il modello di distribuzione classica](expressroute-howto-linkvnet-classic.md).
* Per collegare le reti virtuali nel modello di distribuzione Gestione risorse, vedere [come collegare le reti virtuali nel modello di distribuzione di gestione risorse](expressroute-howto-linkvnet-arm.md).

Impostando **allowClassicOperations** su FALSE viene bloccato l'accesso al circuito dal modello di distribuzione classica. Tuttavia, tutte le reti virtuali collegate al modello di distribuzione classica vengono comunque mantenute. Il circuito ExpressRoute non è visibile nel modello di distribuzione classica.

## <a name="supported-operations-in-the-classic-deployment-model"></a>Operazioni supportate nel modello di distribuzione classica
Le operazioni classiche seguenti sono supportate in un circuito ExpressRoute quando **allowClassicOperations** è impostato su TRUE:

* Ottenere informazioni sul circuito ExpressRoute.
* Creare, aggiornare, ottenere o eliminare collegamenti alle reti virtuali classiche.
* Creare, aggiornare, ottenere o eliminare autorizzazioni dei collegamenti alle reti virtuali per la connettività tra sottoscrizioni.

Tuttavia, quando **allowClassicOperations** è impostato su true, non è possibile eseguire le operazioni classiche seguenti:

* Creare, aggiornare, ottenere o eliminare peering BGP (Border Gateway Protocol) per peering pubblici e privati di Azure e peering Microsoft.
* Eliminare circuiti ExpressRoute.

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>Comunicazione tra i modelli di distribuzione Resource Manager e classica
Il circuito ExpressRoute fa da bridge tra il modello di distribuzione classica e il modello di distribuzione Resource Manager. Il traffico tra reti virtuali per entrambi i modelli di distribuzione può attraversare il circuito ExpressRoute se entrambe le reti virtuali sono collegate allo stesso circuito.

La velocità effettiva aggregata è limitata dalla capacità di velocità effettiva del gateway di rete virtuale. In questi casi, il traffico non immette le reti del provider di connettività o le reti. Il flusso del traffico tra le reti virtuali è interamente contenuto nella rete Microsoft.

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Accesso alle risorse di peering Microsoft e peering pubblico di Azure
È possibile continuare ad accedere alle risorse normalmente accessibili con il peering pubblico di Azure e il peering Microsoft senza interruzioni.  

## <a name="whats-supported"></a>Attività supportate
Questa sezione descrive le attività supportate per i circuiti ExpressRoute:

* Per accedere alle reti virtuali distribuite nei modelli di distribuzione Resource Manager o classica, è possibile usare un solo circuito ExpressRoute.
* È possibile spostare un circuito ExpressRoute dal modello di distribuzione classica al modello di distribuzione Resource Manager. Dopo lo spostamento, il circuito ExpressRoute continuerà a funzionare come qualsiasi altro circuito ExpressRoute creato nel modello di distribuzione Gestione risorse.
* Solo il circuito ExpressRoute può essere spostato. I gateway VPN, le reti virtuali e i collegamenti del circuito non possono essere spostati con questa operazione.
* Dopo aver spostato un circuito ExpressRoute nel modello di distribuzione Resource Manager, il ciclo di vita del circuito ExpressRoute può essere gestito unicamente con il modello di distribuzione Resource Manager. Ciò significa che è possibile eseguire operazioni quali l'aggiunta, l'aggiornamento o l'eliminazione di peering, l'aggiornamento delle proprietà del circuito (ad esempio larghezza di banda, SKU e tipo di fatturazione) e l'eliminazione dei circuiti solo nel modello di distribuzione Gestione risorse.
* Il circuito ExpressRoute fa da bridge tra il modello di distribuzione classica e il modello di distribuzione Resource Manager. Il traffico tra macchine virtuali in reti virtuali classiche e macchine virtuali in Gestione risorse reti virtuali è in grado di comunicare tramite ExpressRoute se entrambe le reti virtuali sono collegate allo stesso circuito ExpressRoute.
* La connettività tra sottoscrizioni è supportata sia nel modello di distribuzione classica che nel modello di distribuzione Resource Manager.
* Dopo avere spostato un circuito ExpressRoute dal modello classico al modello di Azure Resource Manager, è possibile [eseguire la migrazione delle reti virtuali collegate al circuito ExpressRoute](expressroute-migration-classic-resource-manager.md).

## <a name="whats-not-supported"></a>Attività non supportate
Questa sezione descrive le attività non supportate per i circuiti ExpressRoute:

* Gestione del ciclo di vita di un circuito ExpressRoute dal modello di distribuzione classica.
* Supporto del controllo degli accessi in base al ruolo di Azure per il modello di distribuzione classica. Non è possibile eseguire controlli RBAC di Azure in un circuito nel modello di distribuzione classica. Qualsiasi amministratore o coamministratore della sottoscrizione può collegare o scollegare le reti virtuali dal circuito.

## <a name="configuration"></a>Configurazione
Vedere le istruzioni riportate nell'articolo [Spostamento dei circuiti ExpressRoute dal modello di distribuzione classica al modello di distribuzione Resource Manager](expressroute-howto-move-arm.md).

## <a name="next-steps"></a>Passaggi successivi
* [Eseguire la migrazione delle reti virtuali collegate al circuito ExpressRoute dal modello classico al modello di Resource Manager](expressroute-migration-classic-resource-manager.md)
* Per informazioni sul flusso di lavoro, vedere [Flussi di lavoro e stati di provisioning di un circuito ExpressRoute](expressroute-workflows.md).
* Per configurare la connessione ExpressRoute:
  
  * [Creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configurare il routing](expressroute-howto-routing-arm.md)
  * [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)

