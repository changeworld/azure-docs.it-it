---
title: Risolvere i problemi comuni Azure Load Balancer
description: Informazioni su come risolvere i problemi comuni con Azure Load Balancer.
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
ms.openlocfilehash: cddaf1bde84d7e60eb59bd4c58c65fa889e06ae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028812"
---
# <a name="troubleshoot-azure-load-balancer"></a>Risolvere i problemi di Azure Load Balancer

Questa pagina fornisce informazioni sulla risoluzione dei problemi comuni di Azure Load Balancer Basic e Standard. Per altre informazioni su Load Balancer Standard, vedere la [panoramica di Load Balancer Standard](load-balancer-standard-diagnostics.md).

Quando la connettività di Load Balancer non è disponibile, i sintomi più comuni sono i seguenti:

- Le macchine virtuali dietro il Load Balancer non rispondono ai Probe di integrità 
- Le macchine virtuali dietro il Load Balancer non rispondono al traffico sulla porta configurata

Quando i client esterni alle macchine virtuali back-end usano il bilanciamento del carico, per la comunicazione verrà usato l'indirizzo IP dei client. Assicurarsi che l'indirizzo IP dei client venga aggiunto all'elenco degli indirizzi consentiti del gruppo di sicurezza di rete.

## <a name="no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>Nessuna connettività in uscita dai bilanciamenti del carico interno standard (ILB)

**Convalida e la risoluzione**

Le gli ILB standard sono **sicure per impostazione predefinita**. Gli ILB di base hanno consentito la connessione a Internet tramite un indirizzo IP pubblico *nascosto* . Questa operazione non è consigliata per i carichi di lavoro di produzione perché l'indirizzo IP non è né statico né bloccato tramite gruppi di cui si è proprietari. Se è stato recentemente spostato da un ILB di base a un ILB standard, è necessario creare un indirizzo IP pubblico in modo esplicito tramite la configurazione [solo in uscita](egress-only.md) , che blocca l'IP tramite gruppi. È anche possibile usare un [gateway NAT](../virtual-network/nat-overview.md) nella subnet.

## <a name="cant-change-backend-port-for-existing-lb-rule-of-a-load-balancer-that-has-virtual-machine-scale-set-deployed-in-the-backend-pool"></a>Non è possibile modificare la porta back-end per la regola LB esistente di un servizio di bilanciamento del carico con set di scalabilità di macchine virtuali distribuito nel pool back-end.

### <a name="cause-the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-virtual-machine-scale-set"></a>Motivo: non è possibile modificare la porta back-end per una regola di bilanciamento del carico usata da un probe di integrità per il servizio di bilanciamento del carico a cui fa riferimento il set di scalabilità di macchine virtuali

**Risoluzione** dei problemi Per modificare la porta, è possibile rimuovere il probe di integrità aggiornando il set di scalabilità di macchine virtuali, aggiornare la porta e quindi configurare di nuovo il probe di integrità.

## <a name="small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>Il traffico di piccole dimensioni passa ancora attraverso il servizio di bilanciamento del carico dopo aver rimosso le VM dal pool back-end del servizio di bilanciamento del carico.

### <a name="cause-vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>Motivo: le macchine virtuali rimosse dal pool back-end non devono più ricevere traffico. La presenza di traffico di rete ridotto potrebbe essere correlata all'archiviazione, a DNS e ad altre funzioni in Azure.

Per verificarlo, è possibile eseguire una traccia di rete. Il nome di dominio completo usato per gli account di archiviazione BLOB è elencato nelle proprietà di ogni account di archiviazione.  Da una macchina virtuale all'interno della sottoscrizione di Azure, è possibile eseguire nslookup per determinare l'indirizzo IP di Azure assegnato a tale account di archiviazione.

## <a name="additional-network-captures"></a>Altre acquisizioni di rete

Se si decide di aprire un caso di supporto, raccogliere le informazioni seguenti per una soluzione più rapida. Scegliere una singola macchina virtuale back-end per eseguire questi test:

- Usare il ping PS da una delle VM back-end all'interno della VNet per testare la risposta della porta Probe, ad esempio: PS ping 10.0.0.4:3389, e registrare i risultati. 
- Se non si ricevono risposte in questi test di ping, eseguire una traccia Netsh simultanea nella macchina virtuale back-end e nella macchina virtuale di test della rete virtuale mentre si esegue PsPing, quindi arrestare la traccia Netsh.

## <a name="load-balancer-in-failed-state"></a>Load Balancer in stato di errore

**Risoluzione**

- Una volta identificata la risorsa in stato di errore, passare a [Azure Resource Explorer](https://resources.azure.com/) e identificare la risorsa in questo stato.
- Aggiornare l'interruttore nell'angolo superiore destro in lettura/scrittura.
- Fare clic su modifica per la risorsa in stato di errore.
- Fare clic su PUT seguito da GET per assicurarsi che lo stato di provisioning sia stato aggiornato a succeeded.
- È quindi possibile procedere con altre azioni perché la risorsa non è in stato di errore.

## <a name="next-steps"></a>Passaggi successivi

Se i passaggi precedenti non risolvono il problema, aprire un [ticket di supporto](https://azure.microsoft.com/support/options/).
