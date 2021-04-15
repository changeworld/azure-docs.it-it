---
title: SNAT (Source Network Address Translation) per le connessioni in uscita
titleSuffix: Azure Load Balancer
description: Informazioni su Azure Load Balancer per la connettività Internet in uscita (SNAT).
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 3b92ef3ce195a2eee9bce53e08d977593a9f1fc2
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477707"
---
# <a name="using-source-network-address-translation-snat-for-outbound-connections"></a>Uso di SNAT (Source Network Address Translation) per le connessioni in uscita

Gli indirizzi IP front-end di un servizio di bilanciamento del carico pubblico di Azure possono essere usati per fornire connettività in uscita a Internet per le istanze back-end. Questa configurazione usa **SNAT (Source Network Address Translation).** SNAT riscrive l'indirizzo IP del back-end nell'indirizzo IP pubblico del servizio di bilanciamento del carico. 

SNAT abilita il **mascheramento IP dell'istanza** back-end. Questa mascheramento impedisce alle origini esterne di avere un indirizzo diretto alle istanze back-end. Un indirizzo IP condiviso tra istanze back-end riduce il costo degli indirizzi IP pubblici statici. Un indirizzo IP noto supporta scenari come la semplificazione dell'elenco indirizzi IP consentiti con il traffico proveniente da indirizzi IP pubblici noti. 

>[!Note]
> Per le applicazioni che richiedono un numero elevato di connessioni in uscita o clienti aziendali che richiedono un singolo set di indirizzi IP da usare da una determinata rete [virtuale,](../virtual-network/nat-overview.md) NAT di rete virtuale è la soluzione consigliata. L'allocazione dinamica consente una configurazione semplice e l'uso più efficiente delle porte SNAT da ogni indirizzo IP. Consente a tutte le risorse nella rete virtuale di condividere un set di indirizzi IP senza la necessità di condividere un servizio di bilanciamento del carico.

>[!Important]
> Anche senza SNAT in uscita configurato, gli account di archiviazione di Azure nella stessa area saranno comunque accessibili e le risorse back-end avranno comunque accesso a servizi Microsoft come gli aggiornamenti di Windows.

>[!NOTE] 
>Questo articolo illustra Azure Resource Manager solo le distribuzioni. Vedere [Connessioni in uscita (versione classica)](/previous-versions/azure/load-balancer/load-balancer-outbound-connections-classic) per tutti gli scenari di distribuzione classica in Azure.

## <a name="sharing-frontend-ip-address-across-backend-resources"></a><a name ="snat"></a> Condivisione dell'indirizzo IP front-end tra risorse back-end

Se le risorse back-end di un servizio di bilanciamento del carico non hanno indirizzi IP pubblici (ILPIP) a livello di istanza, stabiliscono la connettività in uscita tramite l'INDIRIZZO IP front-end del servizio di bilanciamento del carico pubblico. Le porte vengono usate per generare identificatori univoci usati per gestire flussi distinti. Internet usa una tupla a cinque per fornire questa distinzione.

La tupla a cinque è costituita da:

* IP di destinazione
* Porta di destinazione
* IP di origine
* Porta e protocollo di origine per fornire questa distinzione.

Se una porta viene usata per le connessioni in ingresso, dispone di un **listener** per le richieste di connessione in ingresso su tale porta. Questa porta non può essere usata per le connessioni in uscita. Per stabilire una connessione  in uscita, viene usata una porta effimera per fornire alla destinazione una porta su cui comunicare e mantenere un flusso di traffico distinto. Quando queste porte effimeri vengono usate per SNAT, vengono chiamate **porte SNAT** 

Per definizione, ogni indirizzo IP ha 65.535 porte. Ogni porta può essere usata per le connessioni in ingresso o in uscita per TCP(Transmission Control Protocol) e UDP (User Datagram Protocol). 

Quando un indirizzo IP pubblico viene aggiunto come indirizzo IP front-end a un servizio di bilanciamento del carico, Azure offre 64.000 porte idonee per SNAT.

>[!NOTE]
> Ogni porta usata per un bilanciamento del carico o una regola NAT in ingresso usa un intervallo di otto porte da queste 64.000 porte, riducendo il numero di porte idonee per SNAT. Se una regola nat o di bilanciamento del carico è nello stesso intervallo di otto di un'altra, non verrà utilizzata alcuna porta aggiuntiva. 

Tramite [regole in uscita](./outbound-rules.md) e regole di bilanciamento del carico, queste porte SNAT possono essere distribuite alle istanze back-end per consentire loro di condividere gli INDIRIZZI IP pubblici del servizio di bilanciamento del carico per le connessioni in uscita.

Quando [viene configurato lo scenario 2](#scenario2) seguente, l'host per ogni istanza back-end riceverà pacchetti SNAT che fanno parte di una connessione in uscita. 

Quando si esegue SNAT su una connessione in uscita da un'istanza back-end, l'host riscrive l'indirizzo IP di origine in uno degli indirizzi IP front-end. 

Per mantenere flussi univoci, l'host riscrive la porta di origine di ogni pacchetto in uscita in una porta SNAT nell'istanza back-end.

## <a name="outbound-connection-behavior-for-different-scenarios"></a>Comportamento della connessione in uscita per scenari diversi
  * Macchina virtuale con IP pubblico.
  * Macchina virtuale senza IP pubblico.
  * Macchina virtuale senza IP pubblico e senza bilanciamento del carico standard.
        
 ### <a name="scenario-1-virtual-machine-with-public-ip-either-with-or-without-a-load-balancer"></a><a name="scenario1"></a> Scenario 1: macchina virtuale con INDIRIZZO IP pubblico con o senza un servizio di bilanciamento del carico.

 | Associazioni | Metodo | Protocolli IP |
 | ---------- | ------ | ------------ |
 | Servizio di bilanciamento del carico pubblico o autonomo | [SNAT (Source Network Address Translation)](#snat) </br> non viene usato. | TCP (Transmission Control Protocol) </br> UDP (User Datagram Protocol) </br> ICMP (Internet Control Message Protocol) </br> ESP (Encapsulating Security Payload) |

 #### <a name="description"></a>Descrizione

 Tutto il traffico verrà restituito al client richiedente dall'indirizzo IP pubblico della macchina virtuale (IP a livello di istanza).
 
 Azure usa l'indirizzo IP pubblico assegnato alla configurazione IP della scheda di interfaccia di rete dell'istanza per tutti i flussi in uscita. L'istanza ha tutte le porte temporanee disponibili. Non è importante se la macchina virtuale è con carico bilanciato o meno. Questo scenario ha la precedenza rispetto agli altri. 

 Un indirizzo IP pubblico assegnato a una macchina virtuale è una relazione 1:1, anziché 1:molti, e viene implementato come NAT 1:1 senza stato.

 ### <a name="scenario-2-virtual-machine-without-public-ip-and-behind-standard-public-load-balancer"></a><a name="scenario2"></a>Scenario 2: Macchina virtuale senza IP pubblico e dietro l'indirizzo Load Balancer

 | Associazioni | Metodo | Protocolli IP |
 | ------------ | ------ | ------------ |
 | Load Balancer pubblico Standard | Uso di indirizzi IP front-end di bilanciamento del carico [per SNAT.](#snat)| TCP </br> UDP |

 #### <a name="description"></a>Descrizione

 La risorsa di bilanciamento del carico è configurata con una regola in uscita o una regola di bilanciamento del carico che abilita SNAT. Questa regola viene usata per creare un collegamento tra il front-end ip pubblico e il pool back-end. 

 Se non si completa questa configurazione della regola, il comportamento è come descritto nello scenario 3. 

 Una regola con un listener non è necessaria perché il probe di integrità riesca.

 Quando una macchina virtuale crea un flusso in uscita, Azure converte l'indirizzo IP di origine nell'indirizzo IP pubblico del front-end del servizio di bilanciamento del carico pubblico. Questa traduzione viene eseguita tramite [SNAT.](#snat) 

 Le porte effimere dell'indirizzo IP pubblico front-end del servizio di bilanciamento del carico vengono usate per distinguere i singoli flussi originati dalla macchina virtuale. SNAT usa dinamicamente le [porte temporanee preallocate](#preallocatedports) quando vengono creati i flussi in uscita. 

 In questo contesto le porte temporanee usate per SNAT sono dette porte SNAT. È consigliabile configurare in modo esplicito [una regola](./outbound-rules.md) in uscita. Se si usa SNAT predefinito tramite una regola di bilanciamento del carico, le porte SNAT vengono prealloce come descritto nella tabella di allocazione delle [porte SNAT predefinite](#snatporttable).

> [!NOTE]
> **NAT di rete virtuale di Azure** può fornire connettività in uscita per le macchine virtuali senza la necessità di un servizio di bilanciamento del carico. Per [altre informazioni, vedere Informazioni NAT di rete virtuale di Azure?](../virtual-network/nat-overview.md) .

 ### <a name="scenario-3-virtual-machine-without-public-ip-and-behind-standard-internal-load-balancer"></a><a name="scenario3"></a>Scenario 3: Macchina virtuale senza INDIRIZZO IP pubblico e dietro l'Load Balancer

 | Associazioni | Metodo | Protocolli IP |
 | ------------ | ------ | ------------ |
 | Servizio di bilanciamento del carico interno standard | Nessuna connettività Internet.| nessuno |

 #### <a name="description"></a>Descrizione
 
Quando si usa un servizio di bilanciamento del carico interno Standard, non viene utilizzato un indirizzo IP ffemero per SNAT. Questa funzionalità supporta la sicurezza per impostazione predefinita. Questa funzionalità garantisce che tutti gli indirizzi IP usati dalle risorse siano configurabili e possano essere riservati. 

Per ottenere la connettività in uscita a Internet quando si usa un servizio di bilanciamento del carico interno Standard, configurare un indirizzo IP pubblico a livello di istanza in modo che segua il comportamento [nello scenario 1.](#scenario1) 

Un'altra opzione è aggiungere le istanze back-end a un servizio di bilanciamento del carico pubblico Standard con una regola in uscita configurata. Le istanze back-end vengono aggiunte a un servizio di bilanciamento del carico interno per il bilanciamento del carico interno. Questa distribuzione segue il comportamento nello [scenario 2](#scenario2). 

> [!NOTE]
> **NAT di rete virtuale di Azure** può fornire connettività in uscita per le macchine virtuali senza la necessità di un servizio di bilanciamento del carico. Per [altre informazioni, vedere Informazioni NAT di rete virtuale di Azure?](../virtual-network/nat-overview.md) .

 ### <a name="scenario-4-virtual-machine-without-public-ip-and-behind-basic-load-balancer"></a><a name="scenario4"></a>Scenario 4: Macchina virtuale senza IP pubblico e dietro l'Load Balancer

 | Associazioni | Metodo | Protocolli IP |
 | ------------ | ------ | ------------ |
 |nessuno </br> Servizio di bilanciamento del carico di base | [SNAT con](#snat) indirizzo IP dinamico a livello di istanza| TCP </br> UDP | 

 #### <a name="description"></a>Descrizione

 Quando la macchina virtuale crea un flusso in uscita, Azure converte l'indirizzo IP di origine in un indirizzo IP di origine pubblico specificato dinamicamente. Questo indirizzo IP **pubblico non è configurabile** e non può essere riservato. Questo indirizzo non viene conteggiato rispetto al limite di risorse IP pubblico della sottoscrizione. 

L'indirizzo IP pubblico verrà rilasciato e verrà richiesto un nuovo indirizzo IP pubblico se si ridistribuisce: 

 * Macchina virtuale
 * Set di disponibilità
 * Set di scalabilità di macchine virtuali 

 Non usare questo scenario per l'aggiunta di indirizzi IP a un elenco elementi consentiti. Usare lo scenario 1 o 2 in cui si dichiara in modo esplicito il comportamento in uscita. [Le porte SNAT](#snat) sono preallocate come descritto nella tabella di allocazione delle [porte SNAT predefinite](#snatporttable).

## <a name="exhausting-ports"></a><a name="scenarios"></a> Esaurimento delle porte

Ogni connessione allo stesso indirizzo IP di destinazione e alla stessa porta di destinazione userà una porta SNAT. Questa connessione mantiene un flusso di **traffico distinto dall'istanza** back-end o **dal client** a un **server**. Questo processo fornisce al server una porta distinta su cui indirizza il traffico. Senza questo processo, il computer client non è a conoscenza del flusso di cui fa parte un pacchetto.

Si supponga di avere più browser in https://www.microsoft.com , ovvero:

* IP di destinazione = 23.53.254.142
* Porta di destinazione = 443
* Protocollo = TCP

Senza porte di destinazione diverse per il traffico di ritorno (la porta SNAT usata per stabilire la connessione), il client non avrà modo di separare un risultato della query da un altro.

Le connessioni in uscita possono essere burst. È possibile allocare porte insufficienti per un'istanza back-end. Senza il **riutilizzo della** connessione abilitato, il rischio di **esaurimento** delle porte SNAT aumenta.

Le nuove connessioni in uscita a un indirizzo IP di destinazione avranno esito negativo quando si verifica l'esaurimento delle porte. Le connessioni avranno esito positivo quando una porta diventa disponibile. Questo esaurimento si verifica quando le 64.000 porte da un indirizzo IP vengono distribuite in modo sottile in molte istanze back-end. Per indicazioni sulla mitigazione dell'esaurimento delle porte SNAT, vedere la guida [alla risoluzione dei problemi](./troubleshoot-outbound-connection.md).  

Per le connessioni TCP, il servizio di bilanciamento del carico userà una singola porta SNAT per ogni indirizzo IP e porta di destinazione. Questo multiuso consente più connessioni allo stesso INDIRIZZO IP di destinazione con la stessa porta SNAT. Questo multiuso è limitato se la connessione non è a porte di destinazione diverse.

Per le connessioni UDP, il servizio di bilanciamento del carico usa un algoritmo NAT con **cono** con restrizioni, che usa una porta SNAT per ogni IP di destinazione indipendentemente dalla porta di destinazione. 

Una porta viene riutilizzata per un numero illimitato di connessioni. La porta viene riutilizzata solo se l'indirizzo IP o la porta di destinazione è diversa.

## <a name="default-port-allocation"></a><a name="preallocatedports"></a> Allocazione di porte predefinita

A ogni INDIRIZZO IP pubblico assegnato come IP front-end del servizio di bilanciamento del carico vengono assegnate 64.000 porte SNAT per i membri del pool back-end. Le porte non possono essere condivise con i membri del pool back-end. Un intervallo di porte SNAT può essere usato solo da una singola istanza back-end per garantire che i pacchetti restituiti vengano instradati correttamente. 

È consigliabile usare una regola in uscita esplicita per configurare l'allocazione delle porte SNAT. Questa regola massimizza il numero di porte SNAT disponibili per ogni istanza back-end per le connessioni in uscita. 

Se si usa l'allocazione automatica di SNAT in uscita tramite una regola di bilanciamento del carico, la tabella di allocazione definirà l'allocazione delle porte.

La tabella <a name="snatporttable"></a> seguente illustra le preallocazioni delle porte SNAT per i livelli di dimensioni del pool back-end:

| Dimensioni del pool (istanze VM) | Porte SNAT preallocate per configurazione IP |
| --- | --- |
| 1-50 | 1\.024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1.000 | 32 | 

>[!NOTE]
> Se si dispone di un pool back-end con dimensioni massime pari a 10, ogni istanza può avere 64.000/10 = 6.400 porte se si definisce una regola in uscita esplicita. In base alla tabella precedente, ognuna avrà solo 1.024 se si sceglie l'allocazione automatica.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Regole in uscita e NAT di rete virtuale

Azure Load Balancer regole in uscita e NAT di rete virtuale sono opzioni disponibili per l'uscita da una rete virtuale.

Per altre informazioni sulle regole in uscita, vedere [Regole in uscita.](outbound-rules.md)

Per altre informazioni sui NAT di rete virtuale di Azure, vedere [Informazioni NAT di rete virtuale di Azure](../virtual-network/nat-overview.md).

## <a name="constraints"></a>Vincoli

*   Quando una connessione è inattiva senza nuovi pacchetti inviati, le porte verranno rilasciate dopo 4-120 minuti.
  * Questa soglia può essere configurata tramite regole in uscita.
*   Ogni indirizzo IP fornisce 64.000 porte che possono essere usate per SNAT.
*   Ogni porta può essere usata per le connessioni TCP e UDP a un indirizzo IP di destinazione
  * È necessaria una porta SNAT UDP indipendentemente dal fatto che la porta di destinazione sia univoca o meno. Per ogni connessione UDP a un indirizzo IP di destinazione, viene usata una porta SNAT UDP.
  * Una porta SNAT TCP può essere usata per più connessioni allo stesso IP di destinazione, a condizione che le porte di destinazione siano diverse.
*   L'esaurimento SNAT si verifica quando un'istanza back-end esaurisce le porte SNAT specificate. Un servizio di bilanciamento del carico può comunque avere porte SNAT inutilizzate. Se le porte SNAT usate di un'istanza back-end superano le porte SNAT specificate, non sarà possibile stabilire nuove connessioni in uscita.

## <a name="next-steps"></a>Passaggi successivi

*   [Risolvere gli errori di connessione in uscita a causa dell'esaurimento SNAT](./troubleshoot-outbound-connection.md)
*   [Esaminare le metriche SNAT](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) e acquisire familiarità con il modo corretto per filtrarle, dividerle e visualizzarle.
