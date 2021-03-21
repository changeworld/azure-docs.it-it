---
title: Risolvere i problemi di Azure Load Balancer
description: Informazioni su come risolvere i problemi noti relativi ad Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: bfe2b21a86f2ce4b4630ba69cde87796fd367f4b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98029200"
---
# <a name="troubleshoot-azure-load-balancer-backend-traffic-responses"></a>Risolvere i problemi Azure Load Balancer risposte al traffico back-end

Questa pagina fornisce informazioni sulla risoluzione dei problemi per Azure Load Balancer domande.

## <a name="vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>le macchine virtuali dietro Load Balancer non rispondono al traffico sulla porta dati configurata

Se una macchina virtuale del pool back-end è elencata come integra e risponde ai probe di integrità, ma comunque non partecipa al bilanciamento del carico o non risponde al traffico dati, i motivi potrebbero essere i seguenti:
* La macchina virtuale del pool back-end di Load Balancer non è in ascolto nella porta dati
* Un gruppo di sicurezza di rete blocca la porta nella macchina virtuale del pool back-end di Load Balancer  
* Accesso a Load Balancer dalla stessa macchina virtuale e interfaccia di rete
* Accesso al front-end del servizio di bilanciamento del carico Internet dalla macchina virtuale del pool back-end di Load Balancer

## <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Causa 1: la macchina virtuale del pool back-end di Load Balancer non è in ascolto sulla porta dati

Se una macchina virtuale non risponde al traffico dati, è possibile che la porta di destinazione non sia aperta nella macchina virtuale partecipante oppure che la macchina virtuale non sia in ascolto nella porta. 

**Convalida e la risoluzione**

1. Accedere alla macchina virtuale back-end. 
2. Aprire un prompt dei comandi ed eseguire questo comando per verificare che un'applicazione sia in ascolto nella porta dati:  
            netstat -an 
3. Se lo stato della porta non è elencato come "LISTENING", configurare la porta di attesa corretta. 
4. Se la porta è contrassegnata come LISTENING, verificare se sono presenti problemi nell'applicazione di destinazione in ascolto su tale porta.

## <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Causa 2: Un gruppo di sicurezza di rete blocca la porta nella macchina virtuale del pool back-end di Load Balancer  

Se uno o più gruppi di sicurezza di rete configurati nella subnet o nella macchina virtuale bloccano l'indirizzo IP o la porta di origine, la macchina virtuale non potrà rispondere.

Con il bilanciamento del carico pubblico, per la comunicazione tra i client e le macchine virtuali back-end del bilanciamento del carico verrà usato l'indirizzo IP dei client Internet. Assicurarsi che l'indirizzo IP dei client sia consentito nel gruppo di sicurezza di rete della macchina virtuale back-end.

1. Elencare i gruppi di sicurezza di rete configurati nella macchina virtuale back-end. Per altre informazioni, vedere [Gestire i gruppi di sicurezza di rete](../virtual-network/manage-network-security-group.md).
1. Dall'elenco dei gruppi di sicurezza di rete verificare se:
    - il traffico in ingresso o in uscita nella porta dati ha interferenze. 
    - nella scheda di interfaccia di rete della macchina virtuale o nella subnet è presente una regola di tipo **Nega tutto** di un gruppo di sicurezza di rete avente una priorità superiore rispetto alla regola predefinita che consente il traffico e i probe di Load Balancer. I gruppi di sicurezza di rete devono consentire l'IP 168.63.129.16 di Load Balancer, ovvero la porta probe
1. Se alcune di queste regole bloccano il traffico, rimuoverle e riconfigurarle per consentire il traffico dati.  
1. Verificare se la macchina virtuale ha ora iniziato a rispondere ai probe di integrità.

## <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Causa 3: accesso a Load Balancer dalla stessa macchina virtuale e interfaccia di rete 

Se l'applicazione ospitata nella macchina virtuale back-end di un servizio di bilanciamento del carico sta provando ad accedere a un'altra applicazione ospitata nella stessa macchina virtuale back-end tramite la stessa interfaccia di rete, questo scenario non è supportato e avrà esito negativo. 

**Risoluzione**: è possibile risolvere questo problema con uno dei metodi seguenti:
* Configurare macchine virtuali del pool back-end separate per ogni applicazione. 
* Configurare l'applicazione in macchine virtuali con due schede di interfaccia di rete in modo che ogni applicazione usi un'interfaccia di rete e un indirizzo IP propri. 

## <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Causa 4: accesso al front-end di Load Balancer interno dalla macchina virtuale del pool back-end di Load Balancer

Se è configurato un servizio di bilanciamento del carico interno in una rete virtuale e una delle macchine virtuali del back-end sta tentando di accedere al front-end del servizio di bilanciamento del carico interno, potrebbero verificarsi errori durante il mapping del flusso alla macchina virtuale di origine. Questo scenario non è supportato.

**Risoluzione** È possibile sbloccare questo scenario in diversi modi, incluso l'uso di un proxy. Valutare un gateway applicazione o altri proxy di terze parti, ad esempio nginx o haproxy. Per altre informazioni sul gateway applicazione, vedere [Panoramica del gateway applicazione](../application-gateway/overview.md)

**Dettagli** I servizi di bilanciamento del carico interni non convertono le connessioni originate in uscita nel front-end di un servizio di bilanciamento del carico interno perché entrambi risiedono in uno spazio indirizzi IP privati. I servizi Load Balancer pubblici consentono di stabilire [connessioni in uscita](load-balancer-outbound-connections.md) dagli indirizzi IP privati interni alla rete virtuale a indirizzi IP pubblici. Per i servizi Load Balancer interni, questo approccio evita un potenziale esaurimento delle porte SNAT all'interno di uno spazio di indirizzi IP interno univoco, in cui la conversione non è obbligatoria.

Un effetto collaterale consiste nella mancata corrispondenza dei due rami del flusso qualora un flusso in uscita da una macchina virtuale nel pool back-end tenti un flusso verso il front-end del servizio Load Balancer interno nel pool _e_ venga mappato a se stesso. A causa della mancata corrispondenza, il flusso ha esito negativo. Il flusso ha esito positivo se non viene mappato alla stessa macchina virtuale nel pool back-end che ha creato il flusso verso il front-end.

Quando il flusso viene mappato nuovamente a se stesso, il flusso in uscita sembra provenire dalla macchina virtuale verso il front-end e il flusso in ingresso corrispondente sembra provenire dalla macchina virtuale verso se stesso. Dal punto di vista del sistema operativo guest, le parti in ingresso e in uscita dello stesso flusso non corrispondono all'interno della macchina virtuale. Lo stack TCP non riconosce queste metà come appartenenti allo stesso flusso, sebbene lo siano. L'origine e la destinazione non corrispondono. Quando il flusso viene mappato a qualsiasi altra macchina virtuale nel pool di back-end, le metà del flusso corrispondono e la macchina virtuale può rispondere al flusso stesso.

Il sintomo di questo scenario è il verificarsi di timeout di connessione intermittenti quando il flusso torna nello stesso back-end che lo ha originato. Soluzioni alternative comuni includono l'inserimento di un livello proxy dietro il servizio Load Balancer interno e l'uso delle regole di stile DSR (Direct Server Return). Per altre informazioni, vedere [Più front-end per Azure Load Balancer](load-balancer-multivip-overview.md).

È possibile combinare un servizio Load Balancer interno con qualsiasi proxy di terze parti o usare un [gateway applicazione](../application-gateway/overview.md) per scenari proxy con HTTP/HTTPS. Sebbene sia possibile usare un servizio Load Balancer pubblico per attenuare questo problema, lo scenario risultante è soggetto a [esaurimento SNAT](load-balancer-outbound-connections.md). Evitare questo secondo approccio, a meno che non sia gestito accuratamente.

## <a name="next-steps"></a>Passaggi successivi

Se i passaggi precedenti non risolvono il problema, aprire un [ticket di supporto](https://azure.microsoft.com/support/options/).