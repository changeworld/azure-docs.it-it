---
title: Regole in uscita Azure Load Balancer
description: Questo articolo illustra come configurare le regole in uscita per controllare l'uscita del traffico Internet con Azure Load Balancer.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 339bbd7edf48737113de360812165dc8148c5b93
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375866"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Regole in uscita Azure Load Balancer

Le regole in uscita consentono di definire in modo esplicito SNAT (Source Network Address Translation) per un servizio load balancer Standard pubblico. Questa configurazione consente di usare gli indirizzi IP pubblici del servizio di bilanciamento del carico per fornire connettività Internet in uscita per le istanze back-end.

Questa configurazione consente:

* Mascheramento ip
* Semplificazione degli elenchi di elementi consentiti.
* Riduce il numero di risorse IP pubblico per la distribuzione.

Con le regole in uscita, si ha il controllo dichiarativo completo sulla connettività Internet in uscita. Le regole in uscita consentono di ridimensionare e ottimizzare questa capacità in base alle esigenze specifiche. 

Le regole in uscita verranno seguite solo se la macchina virtuale back-end non ha un indirizzo IP pubblico (ILPIP) a livello di istanza.

![Regole in uscita di Load Balancer](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Con le regole in uscita, è possibile definire in modo esplicito il **comportamento SNAT in** uscita.

Le regole in uscita consentono di controllare:

* **Quali macchine virtuali vengono convertite in quali indirizzi IP pubblici.**
     * Due regole erano il pool back-end 1 usa l'indirizzo IP blu 1 e 2, il pool back-end 2 usa il prefisso IP giallo.
* **Modalità di allocazione delle porte SNAT in uscita.**
     * Se il pool back-end 2 è l'unico pool che effettua connessioni in uscita, assegnare tutte le porte SNAT al pool back-end 2 e nessuna al pool back-end 1.
* **Protocolli per cui fornire la conversione in uscita.**
     * Se il pool back-end 2 necessita di porte UDP per l'uscita e il pool back-end 1 necessita di TCP, assegnare le porte TCP a 1 e le porte UDP a 2.
* **Durata da usare per il timeout di inattività della connessione in uscita (4-120 minuti).**
     * Se sono presenti connessioni a esecuzione lunga con keepalives, riservare porte inattive per connessioni a esecuzione lunga per un massimo di 120 minuti. Si supponga che le connessioni non disponibili siano state abbandonate e che le porte siano rilasciate in 4 minuti per le nuove connessioni 
* **Indica se inviare una reimpostazione TCP in base al timeout di inattività.**
     * Quando si verifica il timeout delle connessioni inattive, si invia un RST TCP al client e al server in modo che sappiano che il flusso viene abbandonato?

## <a name="outbound-rule-definition"></a>Definizione della regola in uscita

Le regole in uscita seguono la stessa sintassi familiare del bilanciamento del carico e delle regole NAT in ingresso: **pool back-end** dei parametri  +    +  **front-end**. 

Una regola in uscita configura NAT in uscita per _tutte le macchine virtuali identificate dal pool back-end_ da convertire in _front-end_.  

I _parametri forniscono_ un controllo granulare aggiuntivo sull'algoritmo NAT in uscita.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Scalabilità di NAT in uscita con più indirizzi IP

Ogni indirizzo IP aggiuntivo fornito da un front-end fornisce altre 64.000 porte effimere che il servizio di bilanciamento del carico può usare come porte SNAT. 

Usare più indirizzi IP per pianificare scenari su larga scala. Usare le regole in uscita per ridurre [l'esaurimento SNAT.](troubleshoot-outbound-connection.md#snatexhaust) 

È anche possibile usare un [prefisso IP pubblico](./load-balancer-outbound-connections.md#outboundrules) direttamente con una regola in uscita. 

Un prefisso IP pubblico aumenta il ridimensionamento della distribuzione. Il prefisso può essere aggiunto all'elenco dei flussi consentiti provenienti dalle risorse di Azure. È possibile configurare una configurazione IP front-end all'interno del servizio di bilanciamento del carico per fare riferimento a un prefisso di indirizzo IP pubblico.  

Il servizio di bilanciamento del carico ha il controllo sul prefisso IP pubblico. La regola in uscita userà automaticamente tutti gli indirizzi IP pubblici contenuti nel prefisso IP pubblico per le connessioni in uscita. 

Ognuno degli indirizzi IP all'interno del prefisso IP pubblico fornisce altre 64.000 porte effimere per ogni indirizzo IP che il servizio di bilanciamento del carico può usare come porte SNAT.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Timeout di inattività del flusso in uscita e reimpostazione TCP

Le regole in uscita includono un parametro di configurazione per controllare il timeout di inattività per i flussi in uscita in modo da adattarlo alle esigenze dell'applicazione. Il timeout di inattività in uscita è per impostazione predefinita di 4 minuti. Per altre informazioni, vedere [Configurare i timeout di inattività](load-balancer-tcp-idle-timeout.md). 

Il comportamento predefinito del servizio di bilanciamento del carico è l'eliminazione automatica del flusso quando viene raggiunto il timeout di inattività in uscita. Il `enableTCPReset` parametro consente un comportamento e un controllo prevedibili dell'applicazione. Il parametro determina se inviare TCP Reset bidirezionale (TCP RST) al timeout del timeout di inattività in uscita. 

Per informazioni [dettagliate, inclusa la](./load-balancer-tcp-reset.md) disponibilità dell'area, vedere TCP Reset on idle timeout (Tcp Reset in base al timeout di inattività).

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>Protezione e controllo esplicito della connettività in uscita

Le regole di bilanciamento del carico forniscono la programmazione automatica di NAT in uscita. Alcuni scenari traggono vantaggio o richiedono la disabilitazione della programmazione automatica di NAT in uscita da parte della regola di bilanciamento del carico. La disabilitazione tramite la regola consente di controllare o perfezionare il comportamento.  

È possibile usare questo parametro in due modi:

1. Prevenzione dell'indirizzo IP in ingresso per SNAT in uscita. Disabilitare SNAT in uscita nella regola di bilanciamento del carico.
  
2. Ottimizzare i parametri **SNAT** in uscita di un indirizzo IP usato contemporaneamente per le connessioni in ingresso e in uscita. Il NAT in uscita automatico deve essere disabilitato per consentire a una regola in uscita di assumere il controllo. Per modificare l'allocazione della porta SNAT di un indirizzo usato anche per il traffico in ingresso, il `disableOutboundSnat` parametro deve essere impostato su true. 

L'operazione di configurazione di una regola in uscita avrà esito negativo se si tenta di ridefinire un indirizzo IP usato per l'ingresso.  Disabilitare prima il NAT in uscita della regola di bilanciamento del carico.

>[!IMPORTANT]
> La macchina virtuale non avrà connettività in uscita se si imposta questo parametro su true e non si dispone di una regola in uscita per definire la connettività in uscita.  Alcune operazioni della macchina virtuale o dell'applicazione possono dipendere dalla disponibilità della connettività in uscita. Assicurarsi di valutare le dipendenze dello scenario e l'impatto di questa modifica.

Talvolta non è desiderabile che una macchina virtuale crei un flusso in uscita. Potrebbe essere necessario gestire le destinazioni che ricevono i flussi in uscita o le destinazioni che iniziano i flussi in ingresso. Usare [i gruppi di sicurezza](../virtual-network/network-security-groups-overview.md) di rete per gestire le destinazioni raggiungete dalla macchina virtuale. Usare i gruppi di sicurezza di rete per gestire le destinazioni pubbliche che avviano i flussi in ingresso.

Quando si applica un gruppo di sicurezza di rete a una macchina virtuale con carico bilanciato, considerare i [tag del servizio](../virtual-network/network-security-groups-overview.md#service-tags) e le [regole di sicurezza predefinite](../virtual-network/network-security-groups-overview.md#default-security-rules). 

Assicurarsi che la macchina virtuale possa ricevere richieste di probe di integrità da Azure Load Balancer.

Se un gruppo di sicurezza di rete blocca le richieste del probe di integrità AZURE_LOADBALANCER tag predefinito, il probe di integrità della macchina virtuale ha esito negativo e la macchina virtuale è contrassegnata come non disponibile. Il servizio di bilanciamento del carico interrompe l'invio di nuovi flussi a tale macchina virtuale.

## <a name="scenarios-with-outbound-rules"></a>Scenari con regole in uscita
        

### <a name="outbound-rules-scenarios"></a>Scenari di regole in uscita


* Configurare le connessioni in uscita a un set specifico di indirizzi IP pubblici o prefisso.
* Modificare [l'allocazione delle porte SNAT.](load-balancer-outbound-connections.md)
* Abilitare solo in uscita.
* NAT in uscita solo per le macchine virtuali (nessuna in ingresso).
* NAT in uscita per il servizio di bilanciamento del carico standard interno.
* Abilitare entrambi i protocolli TCP & UDP per NAT in uscita con un servizio di bilanciamento del carico standard pubblico.


### <a name="scenario-1-configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>Scenario 1: Configurare le connessioni in uscita a un set specifico di indirizzi IP pubblici o prefisso


#### <a name="details"></a>Dettagli


Usare questo scenario per adattare le connessioni in uscita in modo che provenino da un set di indirizzi IP pubblici. Aggiungere indirizzi IP pubblici o prefissi a un elenco di indirizzi IP consentiti o non consentiti in base all'origine.


Questo indirizzo IP pubblico o prefisso può essere lo stesso usato da una regola di bilanciamento del carico. 


Per usare un indirizzo IP pubblico o un prefisso diverso da quello usato da una regola di bilanciamento del carico: 


1. Creare un prefisso IP pubblico o un indirizzo IP pubblico.
2. Creare un servizio di bilanciamento del carico standard pubblico 
3. Creare un front-end che faccia riferimento al prefisso IP pubblico o all'indirizzo IP pubblico che si vuole usare. 
4. Riutilizzare un pool back-end o creare un pool back-end e inserire le macchine virtuali in un pool back-end del servizio di bilanciamento del carico pubblico
5. Configurare una regola in uscita nel servizio di bilanciamento del carico pubblico per abilitare NAT in uscita per le macchine virtuali usando il front-end. Non è consigliabile usare una regola di bilanciamento del carico per le connessioni in uscita. Disabilitare SNAT in uscita nella regola di bilanciamento del carico.


### <a name="scenario-2-modify-snat-port-allocation"></a><a name="scenario2out"></a>Scenario 2: Modificare [l'allocazione delle porte SNAT](load-balancer-outbound-connections.md)


#### <a name="details"></a>Dettagli


È possibile usare le regole in uscita per ottimizzare [l'allocazione automatica delle porte SNAT in base alle dimensioni del pool back-end.](load-balancer-outbound-connections.md#preallocatedports) 


Se si verifica un esaurimento SNAT, aumentare il numero di porte [SNAT](load-balancer-outbound-connections.md) specificate dal valore predefinito 1024. 


Ogni indirizzo IP pubblico contribuisce fino a 64.000 porte effimere. Il numero di macchine virtuali nel pool back-end determina il numero di porte distribuite a ogni macchina virtuale. Una macchina virtuale nel pool back-end ha accesso a un massimo di 64.000 porte. Per due macchine virtuali, è possibile specificare un massimo di 32.000 porte [SNAT](load-balancer-outbound-connections.md) con una regola in uscita (2x 32.000 = 64.000). 


È possibile usare le regole in uscita per ottimizzare le porte SNAT specificate per impostazione predefinita. Si assegna un valore maggiore o minore rispetto all'allocazione predefinita delle porte [SNAT.](load-balancer-outbound-connections.md) Ogni indirizzo IP pubblico da un front-end di una regola in uscita contribuisce a un massimo di 64.000 porte effimere da usare come [porte SNAT.](load-balancer-outbound-connections.md) 


Load Balancer offre [porte SNAT](load-balancer-outbound-connections.md) in multipli di 8. Se si specifica un valore non divisibile per 8, l'operazione di configurazione viene rifiutata. Ogni regola di bilanciamento del carico e la regola NAT in ingresso utilizzano un intervallo di 8 porte. Se un bilanciamento del carico o una regola NAT in ingresso condivide lo stesso intervallo di 8 come un'altra, non verranno utilizzate porte aggiuntive.


Se si tenta di assegnare più [porte SNAT](load-balancer-outbound-connections.md) di quelle disponibili in base al numero di indirizzi IP pubblici, l'operazione di configurazione viene rifiutata. Ad esempio, se si assegnano 10.000 porte per macchina virtuale e sette macchine virtuali in un pool back-end condividono un singolo indirizzo IP pubblico, la configurazione viene rifiutata. Sette moltiplicati per 10.000 superano il limite di 64.000 porte. Aggiungere altri indirizzi IP pubblici al front-end della regola in uscita per abilitare lo scenario. 


Ripristinare [l'allocazione delle porte](load-balancer-outbound-connections.md#preallocatedports) predefinita specificando 0 per il numero di porte. Le prime 50 istanze di vm otterrà 1024 porte, 51-100 istanze di macchine virtuali otterrà fino a 512 istanze massime. Per altre informazioni sull'allocazione predefinita delle porte SNAT, vedere Tabella di allocazione delle porte [SNAT](./load-balancer-outbound-connections.md#preallocatedports).


### <a name="scenario-3-enable-outbound-only"></a><a name="scenario3out"></a>Scenario 3: Abilitare solo in uscita


#### <a name="details"></a>Dettagli


Usare un servizio di bilanciamento del carico standard pubblico per fornire NAT in uscita per un gruppo di macchine virtuali. In questo scenario, usare una regola in uscita da sola, senza alcuna regola aggiuntiva configurata.


> [!NOTE]
> **NAT di rete virtuale di Azure** può fornire connettività in uscita per le macchine virtuali senza la necessità di un servizio di bilanciamento del carico. Per [altre informazioni, vedere Informazioni NAT di rete virtuale di Azure?](../virtual-network/nat-overview.md) .

### <a name="scenario-4-outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>Scenario 4: NAT in uscita solo per le macchine virtuali (senza connessioni in ingresso)


> [!NOTE]
> **NAT di rete virtuale di Azure** può fornire connettività in uscita per le macchine virtuali senza la necessità di un servizio di bilanciamento del carico. Per [altre informazioni, vedere Informazioni NAT di rete virtuale di Azure?](../virtual-network/nat-overview.md) .

#### <a name="details"></a>Dettagli


Per questo scenario: Azure Load Balancer regole in uscita e NAT di rete virtuale sono disponibili opzioni per l'uscita da una rete virtuale.


1. Creare un indirizzo IP pubblico o un prefisso.
2. Creare un servizio di bilanciamento del carico standard pubblico. 
3. Creare un front-end associato all'indirizzo IP pubblico o al prefisso dedicato per l'uscita.
4. Creare un pool back-end per le macchine virtuali.
5. Inserire le macchine virtuali nel pool back-end.
6. Configurare una regola in uscita per abilitare NAT in uscita.



Usare un prefisso o un indirizzo IP pubblico per ridimensionare [le porte SNAT.](load-balancer-outbound-connections.md) Aggiungere l'origine delle connessioni in uscita a un elenco di connessioni consentite o non consentite.



### <a name="scenario-5-outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>Scenario 5: NAT in uscita per il servizio di bilanciamento del carico standard interno


> [!NOTE]
> **NAT di rete virtuale di Azure** può fornire connettività in uscita per le macchine virtuali che utilizzano un servizio di bilanciamento del carico standard interno. Per [altre informazioni, NAT di rete virtuale di Azure informazioni.](../virtual-network/nat-overview.md)

#### <a name="details"></a>Dettagli


La connettività in uscita non è disponibile per un servizio load balancer Standard interno fino a quando non viene dichiarata in modo esplicito tramite indirizzi IP pubblici o NAT di rete virtuale a livello di istanza o associando i membri del pool back-end a una configurazione del servizio di bilanciamento del carico solo in uscita. 


Per altre informazioni, vedere Configurazione [del servizio di bilanciamento del carico solo in uscita.](./egress-only.md)




### <a name="scenario-6-enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>Scenario 6: Abilitare entrambi i protocolli TCP & UDP per NAT in uscita con un servizio load balancer standard pubblico


#### <a name="details"></a>Dettagli


Quando si usa un servizio load balancer Standard pubblico, il NAT in uscita automatico fornito corrisponde al protocollo di trasporto della regola di bilanciamento del carico. 


1. Disabilitare [SNAT in uscita](load-balancer-outbound-connections.md) nella regola di bilanciamento del carico. 
2. Configurare una regola in uscita nello stesso servizio di bilanciamento del carico.
3. Riutilizzare il pool back-end già usato dalle macchine virtuali. 
4. Specificare "protocol": "All" come parte della regola in uscita. 


Quando si usano solo regole NAT in ingresso, non viene fornita alcuna conversione NAT in uscita. 


1. Posizionare le macchine virtuali in un pool back-end.
2. Definire una o più configurazioni IP front-end con indirizzi IP pubblici o prefisso IP pubblico 
3. Configurare una regola in uscita nello stesso servizio di bilanciamento del carico. 
4. Specificare "protocol": "All" come parte della regola in uscita


## <a name="limitations"></a>Limitazioni

- Il numero massimo di porte temporanee utilizzabili per ogni indirizzo IP front-end è 64.000.
- L'intervallo del timeout di inattività in uscita configurabile è compreso tra 4 a 120 minuti (da 240 a 7200 secondi).
- Load Balancer non supporta ICMP per NAT in uscita.
- Le regole in uscita possono essere applicate solo alla configurazione IP primaria di una scheda di interfaccia di rete.  Non è possibile creare una regola in uscita per l'indirizzo IP secondario di una macchina virtuale o di un'NVA. Sono supportate più schede di interfaccia di rete.
- Tutte le macchine virtuali all'interno **di un set di** disponibilità devono essere aggiunte al pool back-end per la connettività in uscita. 
- Tutte le macchine virtuali all'interno **di un set di scalabilità** di macchine virtuali devono essere aggiunte al pool back-end per la connettività in uscita.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Azure Load Balancer Standard](load-balancer-overview.md)
- Vedere le [domande frequenti su Azure Load Balancer](load-balancer-faqs.md)
