---
title: Panoramica della rete-server flessibile per database di Azure per MySQL
description: Informazioni sulle opzioni di connettività e rete nell'opzione di distribuzione flessibile del server per database di Azure per MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/23/2020
ms.openlocfilehash: ec835073a1fe447490f6965fe41478319a47f503
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105106837"
---
# <a name="connectivity-and-networking-concepts-for-azure-database-for-mysql---flexible-server-preview"></a>Connettività e concetti di rete per database di Azure per MySQL-server flessibile (anteprima)

Questo articolo descrive le opzioni di connettività pubblica e privata per il server. Vengono illustrati in dettaglio i concetti di rete per il server flessibile database di Azure per MySQL per creare un server in modo sicuro in Azure.

> [!IMPORTANT]
> Database di Azure per MySQL: il server flessibile è in versione di anteprima.

## <a name="choosing-a-networking-option"></a>Scelta di un'opzione di rete
Sono disponibili due opzioni di rete per il server flessibile del database di Azure per MySQL. Le opzioni sono **Accesso privato (integrazione rete virtuale)** e **Accesso pubblico (indirizzi IP consentiti)** . Al momento della creazione del server, è necessario selezionare un'opzione. 

> [!NOTE]
> L'opzione di rete non può essere modificata dopo la creazione del server. 

* **Accesso privato (integrazione rete virtuale)** : è possibile distribuire il server flessibile nella [rete virtuale di Azure](../../virtual-network/virtual-networks-overview.md). Le reti virtuali di Azure forniscono comunicazioni private e sicure. Le risorse di una rete virtuale possono comunicare tramite indirizzi IP privati.

   Scegliere l'opzione di integrazione rete virtuale per ottenere le funzionalità seguenti:
   * Connettersi dalle risorse di Azure nella stessa rete virtuale o in una [rete virtuale con peering](../../virtual-network/virtual-network-peering-overview.md) al server flessibile
   * Usare VPN o ExpressRoute per connettersi al server flessibile da risorse non di Azure
   * Nessun endpoint pubblico

* **Accesso pubblico (indirizzi IP consentiti)** : è possibile accedere al server flessibile tramite un endpoint pubblico. L'endpoint pubblico è un indirizzo DNS risolvibile pubblicamente. L'espressione "indirizzi IP consentiti" si riferisce a un intervallo di indirizzi IP a cui si sceglie di concedere l'autorizzazione per l'accesso al server. Queste autorizzazioni si definiscono **regole del firewall**. 

   Scegliere il metodo di accesso pubblico se si desiderano le seguenti funzionalità:
   * Connettersi dalle risorse di Azure che non supportano le reti virtuali
   * Connettersi da risorse esterne a un Azure non connesse tramite VPN o ExpressRoute 
   * Il server flessibile dispone di un endpoint pubblico

Le caratteristiche seguenti si applicano se si sceglie di utilizzare l'opzione accesso privato o accesso pubblico:
* Le connessioni dagli indirizzi IP consentiti devono eseguire l'autenticazione al server MySQL con credenziali valide
* La [crittografia della connessione](#tls-and-ssl) è disponibile per il traffico di rete
* Il server dispone di un nome di dominio completo (FQDN). Per la proprietà hostname nelle stringhe di connessione, è consigliabile utilizzare il nome di dominio completo anziché un indirizzo IP.
* Entrambe le opzioni controllano l'accesso a livello di server, non a livello di database o di tabella. Usare le proprietà dei ruoli di MySQL per controllare l'accesso a database, tabelle e altri oggetti.


## <a name="private-access-vnet-integration"></a>Accesso privato (integrazione rete virtuale)
L'integrazione di accesso privato con rete virtuale (VNET) fornisce comunicazioni private e sicure per il server MySQL flessibile.

### <a name="virtual-network-concepts"></a>Concetti relativi alla rete virtuale
Di seguito sono riportati alcuni concetti con cui si ha familiarità con l'uso di reti virtuali con i server MySQL flessibili.

* **Rete virtuale** : una rete virtuale di Azure (VNet) contiene uno spazio di indirizzi IP privato configurato per l'uso. Per altre informazioni sulle reti virtuali di Azure, vedere [Panoramica di rete virtuale](../../virtual-network/virtual-networks-overview.md) di Azure.

    La rete virtuale deve trovarsi nella stessa area di Azure del server flessibile.

* **Subnet delegata** : una rete virtuale contiene subnet (sottoreti). Le subnet consentono di segmentare la rete virtuale in spazi di indirizzi più piccoli. Le risorse di Azure vengono distribuite in subnet specifiche all'interno di una rete virtuale. 

   Il server MySQL flessibile deve trovarsi in una subnet **delegata** solo per l'uso di MySQL flexible server. Con questa delega solo i server flessibili di Database di Azure per MySQL possono usare tale subnet. Gli altri tipi di risorsa di Azure non possono trovarsi nella subnet delegata. Si delega una subnet assegnando la relativa proprietà di delega come Microsoft. DBforMySQL/flexibleServers.

* **Gruppi di sicurezza di rete (NSG)** Le regole di sicurezza nei gruppi di sicurezza di rete consentono di filtrare il tipo di traffico di rete che può fluire in entrata e in uscita dalle subnet della rete virtuale e dalle interfacce di rete. Per ulteriori informazioni, vedere la [Panoramica del gruppo di sicurezza di rete](../../virtual-network/network-security-groups-overview.md) .

* **Peering di rete virtuale** Il peering di rete virtuale consente di connettere facilmente due o più reti virtuali in Azure. Le reti virtuali con peering vengono visualizzate come una a scopo di connettività. Il traffico tra le macchine virtuali nelle reti virtuali con peering usa l'infrastruttura backbone di Microsoft. Il traffico tra l'applicazione client e il server flessibile nelle reti virtuali con peering viene instradato solo attraverso la rete privata di Microsoft ed è isolato solo per tale rete.

Il server flessibile supporta il peering di rete virtuale nella stessa area di Azure. Il peering di reti virtuali tra aree **non è supportato**. Per ulteriori informazioni, esaminare i [concetti relativi al peering di rete virtuale](../../virtual-network/virtual-network-peering-overview.md) .

### <a name="connecting-from-peered-vnets-in-same-azure-region"></a>Connessione da reti virtuali con peering nella stessa area di Azure
Se l'applicazione client che tenta di connettersi a un server flessibile si trova nella rete virtuale con peering, potrebbe non essere in grado di connettersi usando il server nomeserver flessibile perché non è possibile risolvere il nome DNS per il server flessibile da VNet con peering. Sono disponibili due opzioni per risolvere il problema:
* Usa indirizzo IP privato (scelta consigliata per uno scenario di sviluppo/test): questa opzione può essere usata a scopo di sviluppo o test. È possibile usare nslookup per eseguire la ricerca inversa dell'indirizzo IP privato per il ServerName flessibile (nome di dominio completo) e usare l'indirizzo IP privato per la connessione dall'applicazione client. L'utilizzo dell'indirizzo IP privato per la connessione al server flessibile non è consigliato per l'uso in produzione, in quanto può essere modificato durante un evento pianificato o non pianificato.
* USA zona DNS privato (scelta consigliata per la produzione): questa opzione è adatta ai fini della produzione. Si esegue il provisioning di una [zona DNS privata](../../dns/private-dns-getstarted-portal.md) e la si collega alla rete virtuale client. Nella zona DNS privata è possibile aggiungere un [record a](../../dns/dns-zones-records.md#record-types) per il server flessibile usando il relativo indirizzo IP privato. È quindi possibile usare il record a per connettersi dall'applicazione client nella rete virtuale con peering a un server flessibile.

### <a name="connecting-from-on-premises-to-flexible-server-in-virtual-network-using-expressroute-or-vpn"></a>Connessione da un server locale a un server flessibile nella rete virtuale tramite ExpressRoute o VPN
Per i carichi di lavoro che richiedono l'accesso al server flessibile nella rete virtuale dalla rete locale, è necessario che [ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute/) o [VPN](/azure/architecture/reference-architectures/hybrid-networking/vpn/) e la rete virtuale siano [connesse al sito locale](/azure/architecture/reference-architectures/hybrid-networking/). Con questa configurazione, sarà necessario un server di un server di un server di gestione per risolvere il nomeserver flessibile se si desidera connettersi dall'applicazione client, ad esempio MySQL Workbench, in esecuzione in una rete virtuale locale. Questo server d'inoltre DNS è responsabile della risoluzione di tutte le query DNS tramite un server di un server d'invio al servizio DNS fornito da Azure [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

Per configurare correttamente, sono necessarie le risorse seguenti:

- Rete locale
- Server flessibile MySQL con provisioning con accesso privato (integrazione VNet)
- Rete virtuale [connessa a locale](/azure/architecture/reference-architectures/hybrid-networking/)
- Usare [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md) del server di distribuzione DNS distribuito in Azure

È quindi possibile usare il nomeserver flessibile (FQDN) per connettersi dall'applicazione client nella rete virtuale con peering o nella rete locale al server flessibile.

### <a name="unsupported-virtual-network-scenarios"></a>Scenari di rete virtuale non supportati
* Endpoint pubblico (o IP pubblico o DNS): un server flessibile distribuito in una rete virtuale non può avere un endpoint pubblico
* Dopo che il server flessibile è stato distribuito in una rete virtuale e in una subnet, non è possibile spostarlo in un'altra rete virtuale o subnet. Non è possibile spostare la rete virtuale in un altro gruppo di risorse o sottoscrizione.
* Non è possibile aumentare le dimensioni della subnet (spazi degli indirizzi) dopo che sono presenti risorse nella subnet
* Il peering di reti virtuali tra aree non è supportato

Informazioni su come abilitare l'accesso privato (VNET Integration) usando l'interfaccia della riga di comando di [portale di Azure](how-to-manage-virtual-network-portal.md) o [Azure](how-to-manage-virtual-network-cli.md).

> [!NOTE]
> Se si usa il server DNS personalizzato, è necessario usare un server di trasmissione DNS per risolvere il nome di dominio completo del server di database di Azure per MySQL-flexible. Per altre informazioni, vedere [risoluzione dei nomi che usa il proprio server DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) .

## <a name="public-access-allowed-ip-addresses"></a>Accesso pubblico (indirizzi IP consentiti)
Le caratteristiche del metodo di accesso pubblico includono:
* Solo gli indirizzi IP consentiti sono autorizzati ad accedere al server MySQL flessibile. Per impostazione predefinita, non sono consentiti indirizzi IP. È possibile aggiungere gli indirizzi IP durante la creazione del server o successivamente.
* Il server MySQL ha un nome DNS risolvibile pubblicamente
* Il server flessibile non si trova in una delle reti virtuali di Azure
* Il traffico di rete da e verso il server non passa attraverso una rete privata. Il traffico USA i percorsi Internet generali.

### <a name="firewall-rules"></a>Regole del firewall
La concessione dell'autorizzazione a un indirizzo IP è detta regola del firewall. Se un tentativo di connessione proviene da un indirizzo IP non consentito, il client di origine visualizzerà un errore.


### <a name="allowing-all-azure-ip-addresses"></a>Consentire tutti gli indirizzi IP di Azure
Se un indirizzo IP in uscita fisso non è disponibile per il servizio di Azure, è possibile prendere in considerazione l'abilitazione delle connessioni da tutti gli indirizzi IP dei Data Center di Azure.

> [!IMPORTANT]
> L'opzione **Consenti l'accesso pubblico da servizi e risorse di Azure in Azure** consente di configurare il firewall in modo da consentire tutte le connessioni da Azure, incluse le connessioni dalle sottoscrizioni di altri clienti. Quando si seleziona questa opzione, verificare che l'account di accesso e le autorizzazioni utente limitino l'accesso ai soli utenti autorizzati.

Informazioni su come abilitare e gestire l'accesso pubblico (indirizzi IP consentiti) usando il [portale di Azure](how-to-manage-firewall-portal.md) o l'interfaccia della riga di comando di [Azure](how-to-manage-firewall-cli.md).


### <a name="troubleshooting-public-access-issues"></a>Risoluzione dei problemi di accesso pubblico
Quando l'accesso al database Microsoft Azure per il servizio MySQL server non si comporta come previsto, tenere presente quanto segue:

* **Le modifiche all'elenco di consentiti non sono state ancora applicate:** per rendere effettive le modifiche apportate alla configurazione del firewall del database di Azure per server MySQL possono essere necessari fino a cinque minuti.

* **Autenticazione non riuscita:** Se un utente non dispone delle autorizzazioni per il database di Azure per il server MySQL o la password usata non è corretta, la connessione al database di Azure per il server MySQL viene negata. La creazione di un'impostazione del firewall fornisce solo ai client la possibilità di provare a connettersi al server. Ogni client deve comunque fornire le credenziali di sicurezza necessarie.

* **Indirizzo IP client dinamico:** Se si dispone di una connessione Internet con indirizzi IP dinamici e si verificano problemi durante l'accesso al firewall, è possibile provare una delle soluzioni seguenti:

   * Chiedere al provider di servizi Internet (ISP) l'intervallo di indirizzi IP assegnato ai computer client che accedono al database di Azure per il server MySQL, quindi aggiungere l'intervallo di indirizzi IP come regola del firewall.
   * Ottenere indirizzi IP statici per i computer client, quindi aggiungere gli indirizzi IP statici come una regola del firewall.
  
* La **regola del firewall non è disponibile per il formato IPv6:** Le regole del firewall devono essere in formato IPv4. Se si specificano le regole del firewall in formato IPv6, verrà visualizzato l'errore di convalida.


## <a name="hostname"></a>Nome host
Indipendentemente dall'opzione di rete scelta, è consigliabile utilizzare sempre un nome di dominio completo (FQDN) come nome host per la connessione al server flessibile. Non è garantito che l'indirizzo IP del server rimanga statico. L'utilizzo del nome di dominio completo consente di evitare di apportare modifiche alla stringa di connessione. 

Esempio
* Consigliabile `hostname = servername.mysql.database.azure.com`
* Laddove possibile, evitare di usare `hostname = 10.0.0.4` (un indirizzo privato) o `hostname = 40.2.45.67` (un IP pubblico)


## <a name="tls-and-ssl"></a>TLS e SSL
Il server flessibile database di Azure per MySQL supporta la connessione delle applicazioni client al servizio MySQL usando Transport Layer Security (TLS). TLS è un protocollo standard del settore che garantisce connessioni di rete crittografate tra il server di database e le applicazioni client. TLS è un protocollo aggiornato di Secure Sockets Layer (SSL).

Il server flessibile database di Azure per MySQL supporta solo connessioni crittografate con Transport Layer Security (TLS 1,2). Tutte le connessioni in ingresso con TLS 1.0 e TLS 1.1 verranno rifiutate. Non è possibile disabilitare o modificare la versione di TLS per la connessione al server flessibile di database di Azure per MySQL. Per ulteriori informazioni, vedere la pagina relativa alla modalità di [connessione tramite SSL/TLS](how-to-connect-tls-ssl.md) . 


## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come abilitare l'accesso privato (integrazione rete virtuale) usando il [portale di Azure](how-to-manage-virtual-network-portal.md) o l'[interfaccia della riga di comando di Azure](how-to-manage-virtual-network-cli.md)
* Informazioni su come abilitare l'accesso pubblico (indirizzi IP consentiti) usando il [portale di Azure](how-to-manage-firewall-portal.md) o l'[interfaccia della riga di comando di Azure](how-to-manage-firewall-cli.md)
* Informazioni su come [usare TLS](how-to-connect-tls-ssl.md)
