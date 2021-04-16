---
title: Architettura della connettività del database SQL di Azure
description: Questo documento illustra l'architettura database SQL di Azure connettività per le connessioni di database dall'interno di Azure o dall'esterno di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit, sqldbrb=1
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 01/25/2021
ms.openlocfilehash: 3442e3003ef8a299beb88cd212602c8713915474
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499955"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-connectivity-architecture"></a>Architettura di connettività del database SQL di Azure e di Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Questo articolo illustra l'architettura dei vari componenti che indirizzano il traffico di rete a un server database SQL di Azure o Azure Synapse Analytics. Illustra anche i diversi criteri di connessione e il modo in cui influisce sui client che si connettono dall'interno di Azure e i client che si connettono dall'esterno di Azure.

> [!IMPORTANT]
> Questo articolo *non* si applica a **Istanza gestita di SQL di Azure**. Vedere Architettura [della connettività per un'istanza gestita.](../managed-instance/connectivity-architecture-overview.md)

## <a name="connectivity-architecture"></a>Architettura della connettività

Il diagramma seguente offre una panoramica generale dell'architettura di connettività.

![Diagramma che mostra una panoramica generale dell'architettura di connettività.](./media/connectivity-architecture/connectivity-overview.png)

I passaggi seguenti descrivono come viene stabilita una connessione per database SQL di Azure:

- I client si connettono al gateway, che ha un indirizzo IP pubblico ed è in ascolto sulla porta 1433.
- A seconda dei criteri di connessione effettivi, il gateway reindirizza o trasmette tramite proxy il traffico al cluster di database corretto.
- All'interno del cluster di database il traffico viene inoltrato al database appropriato.

## <a name="connection-policy"></a>Criteri di connessione

I server nel database SQL e Azure Synapse supportano le tre opzioni seguenti per l'impostazione dei criteri di connessione del server:

- **Reindirizzamento (scelta consigliata):** I client stabiliscono connessioni direttamente al nodo che ospita il database, con una latenza ridotta e una velocità effettiva migliorata. Per usare questa modalità per le connessioni, i client devono:
  - Consentire le comunicazioni in uscita dal client a Azure SQL indirizzi IP nell'area sulle porte nell'intervallo 11000 11999. Usare i tag di servizio per SQL per semplificare la gestione.  
  - Consentire le comunicazioni in uscita dal client database SQL di Azure indirizzi IP del gateway sulla porta 1433.

- **Proxy:** In questa modalità, tutte le connessioni vengono proxy tramite i gateway database SQL di Azure, con un aumento della latenza e una velocità effettiva ridotta. Per consentire alle connessioni di usare questa modalità, i client devono consentire le comunicazioni in uscita dal client database SQL di Azure indirizzi IP del gateway sulla porta 1433.

- **Impostazione predefinita:** Si tratta dei criteri di connessione in vigore in tutti i server dopo la creazione, a meno che non si modificano in modo esplicito i criteri di connessione in `Proxy` o `Redirect` . Il criterio predefinito è per tutte le connessioni client che hanno origine all'interno di Azure (ad esempio, da una macchina virtuale di Azure) e per tutte le connessioni client che hanno origine all'esterno (ad esempio, le connessioni dalla `Redirect` `Proxy` workstation locale).

Se si preferisce la minor latenza e la maggiore velocità effettiva possibili, quindi, si consiglia di scegliere i criteri di connessione `Redirect` anziché `Proxy`. Tuttavia, è necessario soddisfare i requisiti aggiuntivi per consentire il traffico di rete, come descritto in precedenza. Se il client è una macchina virtuale di Azure, è possibile eseguire questa operazione usando gruppi di sicurezza di rete (NSG) con [tag di servizio](../../virtual-network/network-security-groups-overview.md#service-tags). Se il client si connette da una workstation locale, potrebbe essere necessario collaborare con l'amministratore di rete per consentire il traffico di rete attraverso il firewall aziendale.

## <a name="connectivity-from-within-azure"></a>Connettività dall'interno di Azure

Se ci si connette dall'interno di Azure, il criterio di connessione predefinito per le connessioni è `Redirect`. Un criterio di indica che dopo che la sessione TCP è stata stabilita database SQL di Azure, la sessione client viene quindi reindirizzata al cluster di database giusto con una modifica all'INDIRIZZO IP virtuale di destinazione da quella del gateway database SQL di Azure a quella `Redirect` del cluster. Tutti i pacchetti successivi passano poi direttamente al cluster, ignorando il gateway del database SQL di Azure. Il diagramma seguente illustra il flusso del traffico.

![panoramica dell'architettura](./media/connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Connettività dall'esterno di Azure

Se ci si connette dall'esterno di Azure, le connessioni usano un criterio di connessione `Proxy` per impostazione predefinita. Il criterio `Proxy` significa che la sessione TCP viene stabilita tramite il gateway del database SQL di Azure e che tutti i pacchetti successivi passano attraverso il gateway. Il diagramma seguente illustra il flusso del traffico.

![Diagramma che illustra come viene stabilita la sessione TCP tramite il gateway database SQL di Azure e tutti i pacchetti successivi vengono inviati tramite il gateway.](./media/connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Aprire anche le porte TCP 1434 e 14000-14999 per abilitare la connessione con l'applicazione livello [dati](/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators#connecting-with-dac)

## <a name="gateway-ip-addresses"></a>Indirizzi IP del gateway

La tabella seguente elenca gli indirizzi IP dei gateway per area. Per connettersi al database SQL o Azure Synapse, è necessario consentire il traffico di rete da e verso **tutti** i gateway per l'area.

Informazioni dettagliate su come eseguire la migrazione del traffico ai nuovi gateway in aree specifiche sono disponibili nell'articolo seguente: Database SQL di Azure migrazione del traffico ai [gateway più nuovi](gateway-migration.md)

| Nome area          | Indirizzi IP del gateway |
| --- | --- |
| Australia centrale    | 20.36.105.0, 20.36.104.6, 20.36.104.7 |
| Australia centrale 2   | 20.36.113.0, 20.36.112.6 |
| Australia orientale       | 13.75.149.87, 40.79.161.1, 13.70.112.9 |
| Australia sud-orientale | 191.239.192.109, 13.73.109.251, 13.77.48.10, 13.77.49.32 |
| Brasile meridionale         | 191.233.200.14, 191.234.144.16, 191.234.152.3 |
| Canada centrale       | 40.85.224.249, 52.246.152.0, 20.38.144.1 |
| Canada orientale          | 40.86.226.166, 52.242.30.154, 40.69.105.9 , 40.69.105.10 |
| Stati Uniti centrali           | 13.67.215.62, 52.182.137.15, 104.208.16.96, 104.208.21.1, 13.89.169.20 |
| Cina orientale           | 139.219.130.35     |
| Cina orientale 2         | 40.73.82.1         |
| Cina settentrionale          | 139.219.15.17      |
| Cina settentrionale 2        | 40.73.50.0         |
| Asia orientale            | 52.175.33.150, 13.75.32.4, 13.75.32.14 |
| Stati Uniti orientali              | 40.121.158.30, 40.79.153.12, 40.78.225.32 |
| Stati Uniti orientali 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3, 40.70.144.193 |
| Francia centrale       | 40.79.137.0, 40.79.129.1, 40.79.137.8, 40.79.145.12 |
| Francia meridionale         | 40.79.177.0, 40.79.177.10 ,40.79.177.12 |
| Germania centrale      | 51.4.144.100       |
| Germania nord-orientale   | 51.5.144.179       |
| Germania centro-occidentale | 51.116.240.0, 51.116.248.0, 51.116.152.0 |
| India centrale        | 104.211.96.159, 104.211.86.30 , 104.211.86.31 |
| India meridionale          | 104.211.224.146    |
| India occidentale           | 104.211.160.80, 104.211.144.4 |
| Giappone orientale           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 40.79.192.5 |
| Giappone occidentale           | 104.214.148.156, 40.74.100.192, 40.74.97.10 |
| Corea centrale        | 52.231.32.42, 52.231.17.22 ,52.231.17.23 |
| Corea meridionale          | 52.231.200.86, 52.231.151.96 |
| Stati Uniti centro-settentrionali     | 23.96.178.199, 23.98.55.75, 52.162.104.33, 52.162.105.9 |
| Europa settentrionale         | 40.113.93.91, 52.138.224.1, 13.74.104.113 |
| Norvegia orientale          | 51.120.96.0, 51.120.96.33 |
| Norvegia occidentale          | 51.120.216.0       |
| Sudafrica settentrionale   | 102.133.152.0, 102.133.120.2, 102.133.152.32 |
| Sudafrica occidentale    | 102.133.24.0       |
| Stati Uniti centro-meridionali     | 13.66.62.124, 104.214.16.32, 20.45.121.1, 20.49.88.1   |
| Asia sud-orientale      | 104.43.15.0, 40.78.232.3, 13.67.16.193 |
| Svizzera settentrionale    | 51.107.56.0, 51.107.57.0 |
| Svizzera occidentale     | 51.107.152.0, 51.107.153.0 |
| Emirati Arabi Uniti centrali          | 20.37.72.64        |
| Emirati Arabi Uniti settentrionali            | 65.52.248.0        |
| Regno Unito meridionale             | 51.140.184.11, 51.105.64.0, 51.140.144.36, 51.105.72.32 |
| Regno Unito occidentale              | 51.141.8.11        |
| Stati Uniti centro-occidentali      | 13.78.145.25, 13.78.248.43, 13.71.193.32, 13.71.193.33 |
| Europa occidentale          | 40.68.37.158, 104.40.168.105, 52.236.184.163  |
| Stati Uniti occidentali              | 104.42.238.205, 13.86.216.196   |
| Stati Uniti occidentali 2            | 13.66.226.202, 40.78.240.8, 40.78.248.10  |
| Stati Uniti occidentali 2            | 13.66.226.202, 40.78.240.8, 40.78.248.10  |
|                      |                    |


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come modificare i criteri database SQL di Azure connessione per un server, vedere [conn-policy.](/cli/azure/sql/server/conn-policy)
- Per informazioni sul comportamento della connessione al database SQL di Azure per i client che usano ADO.NET 4.5 o versione successiva, vedere [Porte successive alla 1433 per ADO.NET 4.5](adonet-v12-develop-direct-route-ports.md).
- Per una panoramica generale sullo sviluppo di applicazioni, vedere [Panoramica dello sviluppo di applicazioni del database SQL](develop-overview.md).
