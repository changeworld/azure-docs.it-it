---
title: Che cos'è DNS privato di Azure?
description: Questo articolo offre una panoramica introduttiva del servizio di hosting DNS privato in Microsoft Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 04/09/2021
ms.author: rohink
ms.openlocfilehash: 560a88c973d71b3e2c6533e05e4f374f9a5bcd8f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311482"
---
# <a name="what-is-azure-private-dns"></a>Che cos'è DNS privato di Azure?

Il Domain Name System, o DNS, è responsabile della conversione (o risoluzione) del nome di un servizio in un indirizzo IP.  DNS di Azure è un servizio di hosting per i domini e fornisce la risoluzione dei nomi usando l'infrastruttura Microsoft Azure. Il servizio DNS di Azure non supporta solo i domini DNS con connessione Internet, ma supporta anche le zone DNS private.

Azure DNS privato fornisce un servizio DNS affidabile e sicuro per la rete virtuale. Azure DNS privato gestisce e risolve i nomi di dominio nella rete virtuale senza la necessità di configurare una soluzione DNS personalizzata. Usando le zone DNS private, è possibile usare il nome di dominio personalizzato anziché i nomi forniti da Azure durante la distribuzione. L'uso di un nome di dominio personalizzato consente di personalizzare l'architettura di rete virtuale per soddisfare al meglio le esigenze dell'organizzazione. Fornisce una risoluzione dei nomi per le macchine virtuali (VM) in una rete virtuale e le reti virtuali connesse. È anche possibile configurare i nomi di zone con una visualizzazione di tipo split-horizon, che consente a una zona DNS privata e pubblica di condividere il nome.

Per risolvere i record di una zona DNS privata dalla rete virtuale, è necessario collegare la rete virtuale alla zona. Le reti virtuali collegate hanno accesso completo e possono risolvere tutti i record DNS pubblicati nella zona privata. È anche possibile abilitare la registrazione in un collegamento di rete virtuale. Quando si Abilita la registrazione automatici in un collegamento di rete virtuale, i record DNS per le macchine virtuali nella rete virtuale vengono registrati nella zona privata. Quando viene abilitata la registrazione automatica, DNS di Azure aggiornerà il record della zona ogni volta che viene creata una macchina virtuale, ne modifica l'indirizzo IP o viene eliminata.

![Panoramica del servizio DNS](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Come procedura consigliata, non usare un dominio *.local* per la zona DNS privata. Non tutti i sistemi operativi supportano questa configurazione.

## <a name="benefits"></a>Vantaggi

DNS privato di Azure offre i vantaggi seguenti:

* **Elimina la necessità di soluzioni DNS personalizzate**. Molti clienti in precedenza creavano soluzioni personalizzate DNS per gestire le zone DNS nella rete virtuale. È ora possibile gestire le zone DNS usando l'infrastruttura nativa di Azure, eliminando così le attività di creazione e gestione di soluzioni DNS personalizzate.

* **Consente di usare tutti i tipi di record DNS comuni**. DNS di Azure supporta i record A, AAAA, CNAME, MX, PTR, SOA, SRV e TXT.

* **Gestione automatica dei record dei nomi host**. Oltre a ospitare i record DNS personalizzati, Azure mantiene automaticamente i record dei nomi host per le macchine virtuali nelle reti virtuali specificate. In questo scenario è possibile ottimizzare i nomi di dominio usati senza dover creare soluzioni DNS personalizzate o modificare le applicazioni.

* **Risoluzione di nomi host tra reti virtuali**. A differenza dei nomi host forniti da Azure, le zone DNS private possono essere condivise tra le reti virtuali. Questa funzionalità semplifica gli scenari tra più reti e l'individuazione dei servizi come il peering di rete virtuale.

* **Strumenti comuni ed esperienza utente**. Per ridurre la curva di apprendimento, questo servizio usa gli strumenti DNS di Azure già noti, ovvero portale di Azure, Azure PowerShell, interfaccia della riga di comando di Azure, modelli di Azure Resource Manager e API REST.

* **Supporto DNS split-horizon**. DNS di Azure consente di creare zone con lo stesso nome che si risolvono in risposte diverse dall'interno di una rete virtuale e dalla rete Internet pubblica. Uno scenario tipico di DNS split-horizon consiste nel fornire una versione dedicata di un servizio da usare all'interno della rete virtuale.

* **Disponibile in tutte le aree di Azure**. La funzionalità zone private di DNS di Azure è disponibile in tutte le aree di Azure nel cloud pubblico di Azure.

## <a name="capabilities"></a>Capabilities

DNS di Azure offre le funzionalità seguenti:

* **Registrazione automatica di macchine virtuali da una rete virtuale collegata a una zona privata con registrazione automatica abilitata**. Le macchine virtuali vengono registrate nella zona privata come record che puntano ai rispettivi indirizzi IP privati. Quando viene eliminata una macchina virtuale in un collegamento di rete virtuale con registrazione automatica abilitata, il servizio DNS di Azure rimuove automaticamente anche il record DNS corrispondente dalla zona privata collegata.

* **Inoltro della risoluzione DNS supportato sulle reti virtuali collegate alla zona privata**. Per la risoluzione DNS fra reti virtuali non è presente alcuna dipendenza esplicita che richiede il peering delle reti virtuali tra loro. Potrebbe tuttavia essere necessario eseguire il peering di reti virtuali per altri scenari, ad esempio il traffico HTTP.

* **Ricerca DNS inversa supportata nell'ambito della rete virtuale**. La ricerca DNS inversa per un indirizzo IP privato associato a una zona privata restituirà un nome di dominio completo che include il nome host/record e il nome della zona come suffisso.

## <a name="other-considerations"></a>Altre considerazioni

DNS di Azure presenta le limitazioni seguenti:

* Una determinata rete virtuale può essere collegata a una sola zona privata se è abilitata la registrazione automatica dei record DNS delle macchine virtuali. È tuttavia possibile collegare più reti virtuali a una singola zona DNS.
* Il DNS inverso funziona solo per lo spazio IP privato nella rete virtuale collegata
* Il DNS inverso per un indirizzo IP privato nella rete virtuale collegata restituirà `internal.cloudapp.net` come suffisso predefinito per la macchina virtuale. Per le reti virtuali collegate a una zona privata con registrazione automaticamente abilitata, il DNS inverso per un indirizzo IP privato restituisce due FQDN: uno con il suffisso predefinito `internal.cloudapp.net` e un altro con il suffisso della zona privata.
* L'invio condizionale non è attualmente supportato in modo nativo. Per abilitare la risoluzione tra Azure e le reti locali, vedere [Risoluzione dei nomi per le macchine virtuali e le istanze del ruolo](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
 
## <a name="pricing"></a>Prezzi

Per altre informazioni sui prezzi, vedere la pagina relativa ai [prezzi del DNS di Azure](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come creare una zona privata nel DNS di Azure tramite [Azure PowerShell](./private-dns-getstarted-powershell.md) o l'[interfaccia della riga di comando di Azure](./private-dns-getstarted-cli.md).

* Consultare alcuni [scenari comuni sulle zone private](./private-dns-scenarios.md) che possono essere realizzati con le zone private in DNS di Azure.

* Per domande e risposte comuni sulle zone private in DNS di Azure, vedere [DNS privato domande frequenti](./dns-faq-private.md).

* Per informazioni sui record e le zone DNS visitare la pagina [Panoramica delle zone e dei record DNS](dns-zones-records.md).

* Informazioni su alcune altre [funzionalità di rete](../networking/networking-overview.md) chiave di Azure.
