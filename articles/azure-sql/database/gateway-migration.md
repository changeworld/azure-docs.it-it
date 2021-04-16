---
title: Avviso di migrazione del traffico del gateway
description: L'articolo fornisce agli utenti informazioni sulla migrazione degli indirizzi IP database SQL di Azure gateway
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 07611a3620a2fd8efe0da075b03b55a5be3a5be9
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505378"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>database SQL di Azure migrazione del traffico ai gateway più nuovi
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Con il miglioramento dell'infrastruttura di Azure, Microsoft aggiorna periodicamente l'hardware per garantire la migliore esperienza possibile per i clienti. Nei prossimi mesi si prevede di aggiungere gateway creati su generazioni di hardware più recenti, di eseguire la migrazione del traffico e infine di rimuovere le autorizzazioni dei gateway creati su hardware meno recente in alcune aree.  

I clienti riceveranno una notifica tramite notifiche di integrità del servizio con anticipo su qualsiasi modifica ai gateway disponibili in ogni area. I clienti [possono usare il portale di Azure per configurare gli avvisi del log attività.](../../service-health/alerts-activity-log-service-notifications-portal.md)

Le informazioni più aggiornate verranno mantenute nella tabella database SQL di Azure [indirizzi IP del gateway.](connectivity-architecture.md#gateway-ip-addresses)

## <a name="status-updates"></a>Aggiornamenti dello stato

# <a name="in-progress"></a>[In corso](#tab/in-progress-ip)
## <a name="may-2021"></a>Maggio 2021
Sono in corso l'aggiunta di nuovi gateway SQL alle aree seguenti:
- Regno Unito meridionale: 51.140.144.36, 51.105.72.32  
- Stati Uniti centro-occidentali: 13.71.193.32, 13.71.193.33 

Questo gateway SQL inizierà ad accettare il traffico dei clienti il 17 maggio 2021.

## <a name="april-2021"></a>Aprile 2021
Sono in corso l'aggiunta di nuovi gateway SQL alle aree seguenti:
- Stati Uniti orientali 2: 40.70.144.193

Questo gateway SQL inizierà ad accettare il traffico dei clienti il 30 aprile 2021.

Sono in corso l'aggiunta di nuovi gateway SQL alle aree seguenti:
- Norvegia orientale: 51.120.96.33
- South Asia orientale: 13.67.16.193
- Sudafrica settentrionale: 102.133.152.32
- Corea del Sud: 52.231.151.96
- Centro-settentrionale: Stati Uniti 52.162.105.9
- Australia sud-orientale: 13.77.49.32 

Questi gateway SQL inizieranno ad accettare il traffico dei clienti il 5 aprile 2021.

## <a name="march-2021"></a>Marzo 2021
I gateway SQL seguenti in più aree sono in fase di disattivazione:
- Brasile meridionale: 104.41.11.5
- Asia orientale: 191.234.2.139
- Stati Uniti orientali: 191.238.6.43
- Giappone orientale: 191.237.240.43
- Giappone occidentale: 191.238.68.11
- Europa settentrionale: 191.235.193.75
- Stati Uniti centro-meridionali: 23.98.162.75
- Asia sud-orientale: 23.100.117.95
- Europa occidentale: 191.237.232.75
- Stati Uniti occidentali: 23.99.34.75

Non è previsto alcun impatto sui clienti perché questi gateway (in esecuzione su hardware meno recente) non indirizzano il traffico dei clienti. Gli indirizzi IP per questi gateway verranno disattivati il 15 marzo 2021.

# <a name="completed"></a>[Operazione completata](#tab/completed-ip)
Le migrazioni del gateway seguenti sono state completate: 

## <a name="february-2021"></a>Febbraio 2021
Sono in corso l'aggiunta di nuovi gateway SQL alle aree seguenti:

- Stati Uniti centrali: 13.89.169.20

Questi gateway SQL inizieranno ad accettare il traffico dei clienti il 28 febbraio 2021.

## <a name="january-2021"></a>Gennaio 2021
Sono in corso l'aggiunta di nuovi gateway SQL alle aree seguenti:

- Australia centrale: 20.36.104.6 , 20.36.104.7 
- Australia centrale 2: 20.36.112.6 
- Brasile meridionale: 191.234.144.16 ,191.234.152.3 
- Canada orientale: 40.69.105.9 ,40.69.105.10
- India centrale: 104.211.86.30 , 104.211.86.31 
- Asia orientale: 13.75.32.14 
- Francia centrale: 40.79.137.8, 40.79.145.12 
- Francia meridionale: 40.79.177.10 ,40.79.177.12
- Corea centrale: 52.231.17.22 ,52.231.17.23
- India occidentale: 104.211.144.4

Questi gateway SQL inizieranno ad accettare il traffico dei clienti il 31 gennaio 2021.



### <a name="october-2020"></a>Ottobre 2020

Sono in corso l'aggiunta di nuovi gateway SQL alle aree seguenti:

- Germania centrale occidentale: 51.116.240.0, 51.116.248.0

Questi gateway SQL inizieranno ad accettare il traffico dei clienti il 12 ottobre 2020. 

### <a name="september-2020"></a>Settembre 2020
Sono in corso l'aggiunta di nuovi gateway SQL alle aree seguenti. Questi gateway SQL inizieranno ad accettare il traffico dei clienti **il 15 settembre 2020:**

- Australia sud-orientale: 13.77.48.10
- Canada orientale: 40.86.226.166, 52.242.30.154
- Regno Unito meridionale: 51.140.184.11, 51.105.64.0

I gateway SQL esistenti inizieranno ad accettare il traffico nelle aree seguenti. Questi gateway SQL inizieranno ad accettare il traffico dei clienti **il 15 settembre 2020:**

- Australia sud-orientale: 191.239.192.109 e 13.73.109.251
- Stati Uniti centrali: 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96 e 104.208.21.1
- Asia orientale: 191.234.2.139, 52.175.33.150 e 13.75.32.4
- Stati Uniti orientali: 40.121.158.30, 40.79.153.12, 191.238.6.43 e 40.78.225.32
- Stati Uniti orientali 2: 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107 e 104.208.150.3
- Francia centrale: 40.79.137.0 e 40.79.129.1
- Giappone occidentale: 104.214.148.156, 40.74.100.192, 191.238.68.11 e 40.74.97.10
- Stati Uniti centro-settersi: 23.96.178.199, 23.98.55.75 e 52.162.104.33
- Asia sud-orientale: 104.43.15.0, 23.100.117.95 e 40.78.232.3
- Stati Uniti occidentali: 104.42.238.205, 23.99.34.75 e 13.86.216.196

Sono in corso l'aggiunta di nuovi gateway SQL alle aree seguenti. Questi gateway SQL inizieranno ad accettare il traffico dei clienti **il 10 settembre 2020:**

- Stati Uniti centro-occidentali: 13.78.248.43 
- Sudafrica settentrionale: 102.133.120.2  

Sono in corso l'aggiunta di nuovi gateway SQL alle aree seguenti. Questi gateway SQL inizieranno ad accettare il traffico dei clienti il **1° settembre 2020:**

- Europa settentrionale: 13.74.104.113 
- Stati Uniti occidentali 2: 40.78.248.10 
- Europa occidentale: 52.236.184.163 
- Stati Uniti centro-meridionali: 20.45.121.1, 20.49.88.1 

I gateway SQL esistenti inizieranno ad accettare il traffico nelle aree seguenti. Questi gateway SQL inizieranno ad accettare il traffico dei clienti il **1° settembre 2020:**
- Giappone orientale: 40.79.184.8, 40.79.192.5


### <a name="august-2020"></a>Agosto 2020

Sono in corso l'aggiunta di nuovi gateway SQL alle aree seguenti:

- Australia orientale: 13.70.112.9
- Canada centrale: 52.246.152.0, 20.38.144.1 
- Stati Uniti occidentali 2: 40.78.240.8

Questi gateway SQL inizieranno ad accettare il traffico dei clienti il 10 agosto 2020. 

### <a name="october-2019"></a>Ottobre 2019
- Brasile meridionale
- Stati Uniti occidentali
- Europa occidentale
- Stati Uniti orientali
- Stati Uniti centrali
- Asia sud-orientale
- Stati Uniti centro-meridionali
- Europa settentrionale
- Stati Uniti centro-settentrionali
- Giappone occidentale
- Giappone orientale
- Stati Uniti orientali 2
- Asia orientale

---

## <a name="impact-of-this-change"></a>Impatto di questa modifica

La migrazione del traffico può modificare l'indirizzo IP pubblico risolto da DNS per il database in database SQL di Azure.
È possibile che si sia stati influenzati da:

- Hard coded the IP address for any particular gateway in your on-premises firewall (Hard coded the IP address for any particular gateway in your on-premises firewall) (Hard coded the IP address for any particular gateway in your on-premises
- Avere subnet che usano Microsoft.SQL come endpoint di servizio, ma non possono comunicare con gli indirizzi IP del gateway
- Usare la configurazione [con ridondanza della zona per il livello utilizzo generico](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- Usare la [configurazione con ridondanza della zona per i & premium e business critical](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability)

Non si avrà alcun impatto se si dispone di:
 
- Reindirizzamento come criterio di connessione
- Connessioni al database SQL dall'interno di Azure e usando i tag di servizio
- Le connessioni effettuate con le versioni supportate di JDBC Driver SQL Server non avranno alcun impatto. Per le versioni JDBC supportate, vedere [Scaricare Microsoft JDBC Driver per SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Cosa fare se si è interessati

È consigliabile consentire il traffico in uscita verso gli indirizzi IP per tutti gli indirizzi IP del [gateway](connectivity-architecture.md#gateway-ip-addresses) nell'area sulla porta TCP 1433 e l'intervallo di porte 11000-11999. Questa raccomandazione è applicabile ai client che si connettono da locale e anche a quelli che si connettono tramite endpoint di servizio. Per altre informazioni sugli intervalli di porte, vedere [Criteri di connessione](connectivity-architecture.md#connection-policy).

Le connessioni effettuate da applicazioni che usano Microsoft JDBC Driver versione 4.0 potrebbero non riuscire a convalidare il certificato. Le versioni inferiori di Microsoft JDBC si basano sul nome comune (CN) nel campo Oggetto del certificato. La mitigazione è garantire che la proprietà hostNameInCertificate sia impostata su *.database.windows.net. Per altre informazioni su come impostare la proprietà hostNameInCertificate, vedere [Connessione con crittografia](/sql/connect/jdbc/connecting-with-ssl-encryption).

Se la mitigazione precedente non funziona, determinare una richiesta di supporto per il database SQL Istanza gestita l'URL seguente: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sull'architettura [Azure SQL connettività](connectivity-architecture.md)