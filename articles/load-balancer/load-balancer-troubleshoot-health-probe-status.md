---
title: Risolvere i problemi relativi allo stato del probe integrità Azure Load Balancer
description: Informazioni su come risolvere i problemi noti con Azure Load Balancer stato del probe di integrità.
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
ms.date: 12/02/2020
ms.author: allensu
ms.openlocfilehash: 28823c997cd974d5061829df88680ed52075caa0
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98029210"
---
# <a name="troubleshoot-azure-load-balancer-health-probe-status"></a>Risolvere i problemi relativi allo stato del probe integrità Azure Load Balancer

Questa pagina fornisce informazioni sulla risoluzione dei problemi comuni Azure Load Balancer le domande di probe di integrità.

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Sintomo: Le macchine virtuali controllate da Load Balancer non rispondono ai probe di integrità
Per partecipare al set di bilanciamento del carico, i server back-end devono superare il controllo del probe. Per altre informazioni sui probe di integrità, vedere [Informazioni sui probe di bilanciamento del carico](load-balancer-custom-probe-overview.md). 

Le macchine virtuali del pool back-end di Load Balancer possono non rispondere ai probe per i motivi seguenti: 
- La macchina virtuale del pool back-end di Load Balancer non è integra 
- La macchina virtuale del pool back-end di Load Balancer non è in ascolto nella porta probe 
- Un firewall o un gruppo di sicurezza di rete blocca la porta nelle macchine virtuali del pool back-end di Load Balancer 
- Altri errori di configurazione in Load Balancer

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Causa 1: La macchina virtuale del pool back-end di Load Balancer non è integra

**Convalida e la risoluzione**

Per risolvere questo problema, accedere alle macchine virtuali partecipanti e verificare se la macchina virtuale è integra e può rispondere a richieste **PsPing** o **TCPing** inviate da un'altra macchina virtuale nel pool. Se la macchina virtuale non è integra o non riesce a rispondere al probe, è necessario risolvere il problema e ripristinare l'integrità della macchina virtuale prima che questa possa partecipare al bilanciamento del carico.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Causa 2: La macchina virtuale del pool back-end di Load Balancer non è in ascolto nella porta probe
Se la macchina virtuale è integra, ma non risponde al probe, è possibile che la porta probe non sia aperta nella macchina virtuale partecipante o che la macchina virtuale non sia in ascolto su tale porta.

**Convalida e la risoluzione**

1. Accedere alla macchina virtuale back-end.
2. Aprire un prompt dei comandi ed eseguire il comando seguente per verificare che sia presente un'applicazione in ascolto sulla porta Probe: netstat-an
3. Se lo stato della porta non è elencato come **LISTENING**, configurare la porta corretta. 
4. In alternativa, selezionare un'altra porta che sia elencata come **LISTENING** e aggiornare la configurazione di Load Balancer di conseguenza.

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Causa 3: Un firewall o un gruppo di sicurezza di rete blocca la porta nelle macchine virtuali del pool back-end di Load Balancer
Se il firewall nella macchina virtuale blocca la porta probe oppure uno o più gruppi di sicurezza di rete configurati nella subnet o nella macchina virtuale non consentono al probe di raggiungere la porta, la macchina virtuale non può rispondere al probe di integrità.

**Convalida e la risoluzione**

1. Se il firewall è abilitato, verificare se è configurato per consentire la porta probe. In caso contrario, configurare il firewall per consentire il traffico nella porta probe e riprovare.
2. Dall'elenco dei gruppi di sicurezza di rete, verificare se il traffico in ingresso o in uscita nella porta probe ha interferenze.
3. Verificare anche se nella scheda di interfaccia di rete della macchina virtuale o nella subnet è presente una regola di tipo **Nega tutto** di un gruppo di sicurezza di rete avente una priorità superiore rispetto alla regola predefinita che consente il traffico e i probe di Load Balancer. I gruppi di sicurezza di rete devono consentire l'IP 168.63.129.16 di Load Balancer.
4. Se alcune di queste regole bloccano il traffico probe, rimuoverle e riconfigurarle per consentire il traffico probe.  
5. Verificare se la macchina virtuale ha ora iniziato a rispondere ai probe di integrità.

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Causa 4: Altri errori di configurazione in Load Balancer
Se tutte le cause precedenti sembrano essere state verificate e risolte correttamente, ma la macchina virtuale back-end continua a non rispondere al probe di integrità, testare manualmente la connettività e raccogliere alcune tracce per ottenere informazioni sulla connettività stessa.

**Convalida e la risoluzione**

1. Usare **Psping** da una delle altre macchine virtuali all'interno della rete virtuale per testare la risposta della porta probe, ad esempio \psping.exe -t 10.0.0.4:3389, e registrare i risultati. 
2. Usare **TCPing** da una delle altre macchine virtuali all'interno della rete virtuale per testare la risposta della porta probe (esempio: \tcping.exe 10.0.0.4 3389) e registrare i risultati. 
3. Se non si ricevono risposte con questi test di ping:
    - Eseguire una traccia Netsh simultanea nella macchina virtuale del pool back-end e un'altra macchina virtuale di test dalla stessa rete virtuale. Eseguire ora un test PsPing per un certo periodo, raccogliere alcune tracce di rete e quindi interrompere il test. 
    - Analizzare l'acquisizione di rete e verificare se sono disponibili pacchetti in ingresso e in uscita correlati alla query di ping. 
        - Se non si osservano pacchetti in ingresso nella macchina virtuale del pool back-end, è possibile che un gruppo di sicurezza di rete o una route definita dall'utente configurata in modo errato blocchi il traffico. 
        - Se non si osservano pacchetti in uscita nella macchina virtuale del pool back-end, è necessario identificare eventuali problemi non correlati nella macchina virtuale, ad esempio la presenza di un'applicazione che blocca la porta probe. 
    - Verificare se i pacchetti probe vengono forzati verso un'altra destinazione, magari tramite impostazioni delle route definite dall'utente, prima di raggiungere il servizio di bilanciamento del carico. Ciò può impedire costantemente al traffico di raggiungere la macchina virtuale back-end. 
4. Modificare il tipo di probe, ad esempio da HTTP a TCP, e configurare la porta corrispondente negli ACL dei gruppi di sicurezza di rete e nel firewall per verificare se il problema è la configurazione della risposta probe. Per altre informazioni sulla configurazione del probe di integrità, vedere la [configurazione del probe di integrità di bilanciamento del carico con endpoint](/archive/blogs/mast/endpoint-load-balancing-heath-probe-configuration-details).

## <a name="next-steps"></a>Passaggi successivi

Se i passaggi precedenti non risolvono il problema, aprire un [ticket di supporto](https://azure.microsoft.com/support/options/).