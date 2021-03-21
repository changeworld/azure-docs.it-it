---
title: 'Architettura di connettività: database di Azure per MariaDB'
description: Descrive l'architettura di connettività per il database di Azure per il server MariaDB.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mariadb
ms.topic: conceptual
ms.date: 2/11/2021
ms.openlocfilehash: 50aaae9e71ac9de366ee4db1981e633491094946
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199964"
---
# <a name="connectivity-architecture-in-azure-database-for-mariadb"></a>Architettura di connettività nel database di Azure per MariaDB
Questo articolo illustra l'architettura di connettività del database di Azure per MariaDB e il modo in cui il traffico viene indirizzato al database di Azure per l'istanza di MariaDB dai client all'interno e all'esterno di Azure.

## <a name="connectivity-architecture"></a>Architettura della connettività

La connessione al database di Azure per MariaDB viene stabilita tramite un gateway responsabile del routing delle connessioni in ingresso alla posizione fisica del server nei cluster. Il diagramma seguente illustra il flusso del traffico.

![Panoramica dell'architettura di connettività](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)


Quando il client si connette al database, la stringa di connessione al server viene risolta nell'indirizzo IP del gateway. Il gateway è in ascolto sull'indirizzo IP sulla porta 3306. All'interno del cluster di database, il traffico viene inviato al database di Azure appropriato per MariaDB. Pertanto, per connettersi al server, ad esempio dalle reti aziendali, è necessario aprire il **firewall sul lato client per consentire al traffico in uscita di raggiungere i nostri Gateway**. Di seguito è possibile trovare un elenco completo degli indirizzi IP usati dai gateway per area.

## <a name="azure-database-for-mariadb-gateway-ip-addresses"></a>Indirizzi IP del gateway di database di Azure per MariaDB

Il servizio gateway è ospitato su un gruppo di nodi di calcolo senza stato che si trova dietro un indirizzo IP, che verrà raggiunto prima dal client durante il tentativo di connessione a un database di Azure per il server MariaDB. 

Nell'ambito della manutenzione continuativa dei servizi, l'hardware di calcolo che ospita i gateway verrà aggiornato periodicamente per garantire l'esperienza più sicura ed efficiente. Quando l'hardware del gateway viene aggiornato, viene creato per primo un nuovo anello dei nodi di calcolo. Questo nuovo anello serve il traffico per tutti i server di database di Azure appena creati per MariaDB e avrà un indirizzo IP diverso rispetto agli anelli del gateway meno recenti nella stessa area per distinguere il traffico. Quando il nuovo anello è completamente funzionante, l'hardware del gateway precedente che funge da server esistenti è pianificato per la rimozione delle autorizzazioni. Prima di rimuovere un hardware del gateway, i clienti che eseguono i server e si connettono agli anelli del gateway meno recenti riceveranno una notifica tramite posta elettronica e nell'portale di Azure tre mesi prima della rimozione delle autorizzazioni. La rimozione delle autorizzazioni dei gateway può influisca sulla connettività ai server se 

* È possibile codificare gli indirizzi IP del gateway nella stringa di connessione dell'applicazione. Non è **consigliabile**. È necessario usare il nome di dominio completo (FQDN) del server nel formato <servername> . MariaDB.database.Azure.com, nella stringa di connessione per l'applicazione. 
* Non aggiornare gli indirizzi IP del gateway più recenti nel firewall sul lato client per consentire al traffico in uscita di raggiungere i nuovi anelli del gateway.

La tabella seguente elenca gli indirizzi IP del gateway del database di Azure per il gateway MariaDB per tutte le aree dati. Le informazioni più aggiornate degli indirizzi IP del gateway per ogni area sono gestite nella tabella seguente. Nella tabella riportata di seguito le colonne rappresentano le seguenti:

* **Indirizzi IP del gateway:** In questa colonna sono elencati gli indirizzi IP correnti dei gateway ospitati nell'ultima generazione di hardware. Se si esegue il provisioning di un nuovo server, è consigliabile aprire il firewall sul lato client per consentire il traffico in uscita per gli indirizzi IP elencati in questa colonna.
* **Indirizzi IP del gateway (rimozione delle autorizzazioni):** In questa colonna sono elencati gli indirizzi IP dei gateway ospitati in una generazione precedente di hardware di cui è in corso la rimozione delle autorizzazioni. Se si esegue il provisioning di un nuovo server, è possibile ignorare questi indirizzi IP. Se si dispone di un server esistente, continuare a mantenere la regola in uscita per il firewall per questi indirizzi IP, perché non è stata ancora rilasciata. Se si eliminano le regole del firewall per questi indirizzi IP, è possibile che vengano generati errori di connettività. Si prevede invece di aggiungere in modo proattivo i nuovi indirizzi IP elencati nella colonna indirizzi IP del gateway alla regola del firewall in uscita non appena si riceve la notifica per la rimozione delle autorizzazioni. In questo modo, quando viene eseguita la migrazione del server all'hardware del gateway più recente, non ci sono interruzioni della connettività al server.
* **Indirizzi IP del gateway (rimossi):** Queste colonne elencano gli indirizzi IP degli anelli del gateway, che vengono rimossi e non sono più in operazioni. È possibile rimuovere in modo sicuro questi indirizzi IP dalla regola del firewall in uscita. 


| **Nome area** | **Indirizzi IP del gateway** |**Indirizzi IP del gateway (rimozione delle autorizzazioni)** | **Indirizzi IP del gateway (rimossi)** |
|:----------------|:-------------------------|:-------------------------------------------|:------------------------------------------|
| Australia centrale| 20.36.105.0  | | |
| Central2 Australia     | 20.36.113.0  | | |
| Australia orientale | 13.75.149.87, 40.79.161.1     |  | |
| Australia sud-orientale |191.239.192.109, 13.73.109.251   |  | |
| Brasile meridionale |191.233.201.8, 191.233.200.16    |  | 104.41.11.5|
| Canada centrale |40.85.224.249  | | |
| Canada orientale | 40.86.226.166    | | |
| Stati Uniti centrali | 23.99.160.139, 52.182.136.37, 52.182.136.38 | 13.67.215.62 | |
| Cina orientale | 139.219.130.35    | | |
| Cina orientale 2 | 40.73.82.1  | | |
| Cina settentrionale | 139.219.15.17    | | |
| Cina settentrionale 2 | 40.73.50.0     | | |
| Asia orientale | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     | | |
| Stati Uniti orientali |40.71.8.203, 40.71.83.113 |40.121.158.30|191.238.6.43 |
| Stati Uniti orientali 2 | 40.70.144.38, 52.167.105.38  | 52.177.185.181 | |
| Francia centrale | 40.79.137.0, 40.79.129.1  | | |
| Francia meridionale | 40.79.177.0     | | |
| Germania centrale | 51.4.144.100     | | |
| Germania settentrionale | 51.116.56.0 | |
| Germania nord-orientale | 51.5.144.179  | | |
| Germania centro-occidentale | 51.116.152.0 | |
| India centrale | 104.211.96.159     | | |
| India meridionale | 104.211.224.146  | | |
| India occidentale | 104.211.160.80    | | |
| Giappone orientale | 40.79.192.23, 40.79.184.8 | 13.78.61.196 | |
| Giappone occidentale | 191.238.68.11, 40.74.96.6, 40.74.96.7     | 104.214.148.156 | |
| Corea centrale | 52.231.17.13   | 52.231.32.42 | |
| Corea meridionale | 52.231.145.3     | 52.231.200.86 | |
| Stati Uniti centro-settentrionali | 52.162.104.35, 52.162.104.36    | 23.96.178.199 | |
| Europa settentrionale | 52.138.224.6, 52.138.224.7  | 40.113.93.91 |191.235.193.75 |
| Sudafrica settentrionale  | 102.133.152.0    | | |
| Sudafrica occidentale | 102.133.24.0   | | |
| Stati Uniti centro-meridionali |104.214.16.39, 20.45.120.0  |13.66.62.124  |23.98.162.75 |
| Asia sud-orientale | 40.78.233.2, 23.98.80.12     | 104.43.15.0 | |
| Svizzera settentrionale | 51.107.56.0 ||
| Svizzera occidentale | 51.107.152.0| ||
| Emirati Arabi Uniti centrali | 20.37.72.64  | | |
| Emirati Arabi Uniti settentrionali | 65.52.248.0    | | |
| Regno Unito meridionale | 51.140.184.11   | | |
| Regno Unito occidentale | 51.141.8.11  | | |
| Stati Uniti centro-occidentali | 13.78.145.25     | | |
| Europa occidentale |13.69.105.208, 104.40.169.187 | 40.68.37.158 | 191.237.232.75 |
| Stati Uniti occidentali |13.86.216.212, 13.86.217.212 |104.42.238.205  | 23.99.34.75|
| Stati Uniti occidentali 2 | 13.66.226.202  | | |
||||

## <a name="connection-redirection"></a>Reindirizzamento della connessione

Il database di Azure per MariaDB supporta un criterio di connessione aggiuntivo, il **Reindirizzamento**, che consente di ridurre la latenza di rete tra le applicazioni client e i server MariaDB. Con questa funzionalità, dopo che la sessione TCP iniziale viene stabilita nel database di Azure per il server MariaDB, il server restituisce l'indirizzo back-end del nodo che ospita il server MariaDB al client. Successivamente, tutti i pacchetti successivi fluiscono direttamente sul server, ignorando il gateway. Poiché i pacchetti vengono inviati direttamente al server, la latenza e la velocità effettiva hanno un miglioramento delle prestazioni.

Questa funzionalità è supportata nei server di database di Azure per MariaDB con le versioni del motore 10,2 e 10,3.

Il supporto per il reindirizzamento è disponibile nell'estensione PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) , sviluppato da Microsoft ed è disponibile in [PECL](https://pecl.php.net/package/mysqlnd_azure). Per altre informazioni su come usare il reindirizzamento nelle applicazioni, vedere l'articolo sulla [configurazione del reindirizzamento](./howto-redirection.md) .

> [!IMPORTANT]
> Il supporto per il reindirizzamento nell'estensione [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) PHP è attualmente in anteprima.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="what-you-need-to-know-about-this-planned-maintenance"></a>Cosa è necessario sapere su questa manutenzione pianificata?
Si tratta di una modifica DNS che lo rende trasparente ai client. Mentre l'indirizzo IP per FQDN viene modificato nel server DNS, la cache DNS locale verrà aggiornata entro 5 minuti e verrà automaticamente eseguita dai sistemi operativi. Dopo l'aggiornamento locale del DNS, tutte le nuove connessioni si connetteranno al nuovo indirizzo IP, tutte le connessioni esistenti rimarranno connesse all'indirizzo IP precedente senza interruzioni fino a quando non vengono rimosse completamente le autorizzazioni degli indirizzi IP precedenti. Prima di rimuovere le autorizzazioni, l'indirizzo IP precedente imporrà approssimativamente da tre a quattro settimane; Pertanto, non dovrebbe avere alcun effetto sulle applicazioni client.

### <a name="what-are-we-decommissioning"></a>Quali sono le autorizzazioni per la rimozione delle autorizzazioni?
Verranno rimosse solo le autorizzazioni per i nodi del gateway. Quando gli utenti si connettono ai server, il primo arresto della connessione consiste nel nodo del gateway, prima che la connessione venga trasmessa al server. Gli anelli del gateway obsoleti (non gli anelli del tenant in cui il server è in esecuzione) fanno riferimento all' [architettura di connettività](#connectivity-architecture) per una maggiore chiarezza.

### <a name="how-can-you-validate-if-your-connections-are-going-to-old-gateway-nodes-or-new-gateway-nodes"></a>Come è possibile verificare se le connessioni passano ai nodi del gateway obsoleti o ai nuovi nodi del gateway?
Eseguire il ping del nome di dominio completo del server, ad esempio  ``ping xxx.mariadb.database.azure.com`` . Se l'indirizzo IP restituito è uno degli indirizzi IP elencati in indirizzi IP del gateway (rimozione delle autorizzazioni) nel documento precedente, significa che la connessione sta attraversando il gateway precedente. Contrariamente, se l'indirizzo IP restituito è uno degli indirizzi IP elencati in indirizzi IP del gateway, significa che la connessione sta attraversando il nuovo gateway.

È anche possibile eseguire il test tramite [PSPing](https://docs.microsoft.com/sysinternals/downloads/psping) o TCPPing del server di database dall'applicazione client con la porta 3306 e verificare che l'indirizzo IP restituito non sia uno degli indirizzi IP di rimozione delle autorizzazioni

### <a name="how-do-i-know-when-the-maintenance-is-over-and-will-i-get-another-notification-when-old-ip-addresses-are-decommissioned"></a>Ricerca per categorie sa quando la manutenzione è stata superata e si riceve un'altra notifica quando gli indirizzi IP precedenti vengono ritirati?
Si riceverà un messaggio di posta elettronica per informare l'utente quando avvierà il lavoro di manutenzione. La manutenzione può richiedere fino a un mese, a seconda del numero di server di cui è necessario eseguire la migrazione in aree di al. Preparare il client per la connessione al server di database usando il nome di dominio completo o usando il nuovo indirizzo IP della tabella precedente. 

### <a name="what-do-i-do-if-my-client-applications-are-still-connecting-to-old-gateway-server-"></a>Che cosa è necessario fare se le applicazioni client si connettono ancora al server gateway precedente?
Ciò indica che le applicazioni si connettono al server utilizzando un indirizzo IP statico anziché un nome di dominio completo. Esaminare le stringhe di connessione e l'impostazione del pool di connessioni, l'impostazione AKS o anche nel codice sorgente.

### <a name="is-there-any-impact-for-my-application-connections"></a>Sono previsti effetti per le connessioni alle applicazioni?
Questa manutenzione è solo una modifica DNS, quindi è trasparente per il client. Una volta che la cache DNS viene aggiornata nel client (eseguita automaticamente dal sistema operativo), tutta la nuova connessione si connetterà al nuovo indirizzo IP e tutta la connessione esistente continuerà a funzionare fino a quando l'indirizzo IP precedente non verrà completamente ritirato, che in genere è stato ripreso in un secondo momento. E la logica di ripetizione dei tentativi non è necessaria per questo caso, ma è opportuno vedere che l'applicazione dispone di una logica di ripetizione dei tentativi configurata. Usare il nome di dominio completo per connettersi al server di database o abilitare l'elenco dei nuovi indirizzi IP del gateway nella stringa di connessione dell'applicazione.
Questa operazione di manutenzione non eliminerà le connessioni esistenti. Consente solo alle nuove richieste di connessione di passare a un nuovo anello del gateway.

### <a name="can-i-request-for-a-specific-time-window-for-the-maintenance"></a>È possibile richiedere un intervallo di tempo specifico per la manutenzione? 
Poiché la migrazione deve essere trasparente e non ha alcun effetto sulla connettività del cliente, si prevede che non vi sia alcun problema per la maggior parte degli utenti. Esaminare l'applicazione in modo proattivo e assicurarsi di usare il nome di dominio completo per connettersi al server di database o abilitare l'elenco dei nuovi indirizzi IP del gateway nella stringa di connessione dell'applicazione.

### <a name="i-am-using-private-link-will-my-connections-get-affected"></a>Sto usando un collegamento privato, le connessioni vengono interessate?
No, si tratta di una rimozione delle autorizzazioni per l'hardware del gateway e non ci sono relazioni con i collegamenti privati o gli indirizzi IP privati, ma influiscono solo sugli indirizzi IP pubblici indicati in indirizzi IP di rimozione delle autorizzazioni.


## <a name="next-steps"></a>Passaggi successivi

* [Creare e gestire le regole del firewall di Database di Azure per MariaDB con il portale di Azure](./howto-manage-firewall-portal.md)
* [Creare e gestire regole del firewall di Database di Azure per MariaDB tramite l'interfaccia della riga di comando di Azure](./howto-manage-firewall-cli.md)
