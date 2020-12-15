---
title: Architettura di connettività-database di Azure per PostgreSQL-server singolo
description: Descrive l'architettura di connettività del database di Azure per PostgreSQL-server singolo.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 680908fdb2b7badcc1bbf713805b638213590877
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508085"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Architettura di connettività nel database di Azure per PostgreSQL
Questo articolo illustra l'architettura di connettività del database di Azure per PostgreSQL e il modo in cui il traffico viene indirizzato all'istanza del database di Azure per PostgreSQL dai client all'interno e all'esterno di Azure.

## <a name="connectivity-architecture"></a>Architettura della connettività
La connessione al database di Azure per PostgreSQL viene stabilita tramite un gateway responsabile del routing delle connessioni in ingresso alla posizione fisica del server nei cluster. Il diagramma seguente illustra il flusso del traffico.

:::image type="content" source="./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png" alt-text="Panoramica dell'architettura di connettività":::


Quando il client si connette al database, la stringa di connessione al server viene risolta nell'indirizzo IP del gateway. Il gateway è in ascolto sull'indirizzo IP sulla porta 5432. All'interno del cluster di database, il traffico viene inviato al database di Azure per PostgreSQL appropriato. Pertanto, per connettersi al server, ad esempio dalle reti aziendali, è necessario aprire il **firewall sul lato client per consentire al traffico in uscita di raggiungere i nostri Gateway**. Di seguito è possibile trovare un elenco completo degli indirizzi IP usati dai gateway per area.

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>Indirizzi IP del gateway di database di Azure per PostgreSQL

Il servizio gateway è ospitato in un gruppo di nodi di calcolo senza stato che si trova dietro un indirizzo IP, che verrà raggiunto prima dal client durante il tentativo di connessione a un database di Azure per il server PostgreSQL. 

Nell'ambito della manutenzione continuativa dei servizi, l'hardware di calcolo che ospita i gateway verrà aggiornato periodicamente per garantire l'esperienza più sicura ed efficiente. Quando l'hardware del gateway viene aggiornato, viene creato per primo un nuovo anello dei nodi di calcolo. Questo nuovo anello serve il traffico per tutti i server di database di Azure per PostgreSQL appena creati e avrà un indirizzo IP diverso rispetto agli anelli del gateway meno recenti nella stessa area per distinguere il traffico. Quando il nuovo anello è completamente funzionante, l'hardware del gateway precedente che funge da server esistenti è pianificato per la rimozione delle autorizzazioni. Prima di rimuovere un hardware del gateway, i clienti che eseguono i server e si connettono agli anelli del gateway meno recenti riceveranno una notifica tramite posta elettronica e nell'portale di Azure tre mesi prima della rimozione delle autorizzazioni. La rimozione delle autorizzazioni dei gateway può influisca sulla connettività ai server se 

* È possibile codificare gli indirizzi IP del gateway nella stringa di connessione dell'applicazione. Non è **consigliabile**. 
* Non aggiornare gli indirizzi IP del gateway più recenti nel firewall sul lato client per consentire al traffico in uscita di raggiungere i nuovi anelli del gateway.

La tabella seguente elenca gli indirizzi IP del gateway del database di Azure per il gateway PostgreSQL per tutte le aree dati. Le informazioni più aggiornate degli indirizzi IP del gateway per ogni area sono gestite nella tabella seguente. Nella tabella riportata di seguito le colonne rappresentano le seguenti:

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
| Stati Uniti centrali | 23.99.160.139, 13.67.215.62, 52.182.136.37, 52.182.136.38     | | |
| Cina orientale | 139.219.130.35    | | |
| Cina orientale 2 | 40.73.82.1  | | |
| Cina settentrionale | 139.219.15.17    | | |
| Cina settentrionale 2 | 40.73.50.0     | | |
| Asia orientale | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     | | |
| Stati Uniti orientali |40.71.8.203, 40.71.83.113 |40.121.158.30|191.238.6.43 |
| Stati Uniti orientali 2 |40.79.84.180, 191.239.224.107, 52.177.185.181, 40.70.144.38, 52.167.105.38  | | |
| Francia centrale | 40.79.137.0, 40.79.129.1  | | |
| Francia meridionale | 40.79.177.0     | | |
| Germania centrale | 51.4.144.100     | | |
| Germania nord-orientale | 51.5.144.179  | | |
| India centrale | 104.211.96.159     | | |
| India meridionale | 104.211.224.146  | | |
| India occidentale | 104.211.160.80    | | |
| Giappone orientale | 13.78.61.196, 191.237.240.43, 40.79.192.23 | | |
| Giappone occidentale | 104.214.148.156, 191.238.68.11, 40.74.96.6, 40.74.96.7    | | |
| Corea centrale | 52.231.32.42   | | |
| Corea meridionale | 52.231.200.86    | | |
| Stati Uniti centro-settentrionali | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36    | | |
| Europa settentrionale | 52.138.224.6, 52.138.224.7  |40.113.93.91 |191.235.193.75 |
| Sudafrica settentrionale  | 102.133.152.0    | | |
| Sudafrica occidentale | 102.133.24.0   | | |
| Stati Uniti centro-meridionali |104.214.16.39, 20.45.120.0  |13.66.62.124  |23.98.162.75 |
| Asia sud-orientale | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     | | |
| Emirati Arabi Uniti centrali | 20.37.72.64  | | |
| Emirati Arabi Uniti settentrionali | 65.52.248.0    | | |
| Regno Unito meridionale | 51.140.184.11   | | |
| Regno Unito occidentale | 51.141.8.11  | | |
| Stati Uniti centro-occidentali | 13.78.145.25     | | |
| Europa occidentale |13.69.105.208,104.40.169.187 |40.68.37.158 | 191.237.232.75|
| Stati Uniti occidentali |13.86.216.212, 13.86.217.212 |104.42.238.205  | 23.99.34.75|
| West US 2 | 13.66.226.202  | | |
||||

## <a name="next-steps"></a>Passaggi successivi

* [Create and manage Azure Database for PostgreSQL firewall rules using the Azure portal](./howto-manage-firewall-using-portal.md) (Creare e gestire regole firewall per il Database di Azure per PostgreSQL usando il portale di Azure)
* [Creare e gestire regole del firewall di Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure](./howto-manage-firewall-using-cli.md)
