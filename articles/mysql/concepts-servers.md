---
title: Concetti relativi ai server-database di Azure per MySQL
description: Questo argomento fornisce considerazioni e linee guida per l'uso del database di Azure per server MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: cb8394de49c2c5daeae156a9316466928eded148
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628476"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Concetti relativi ai server nel database di Azure per MySQL

Questo articolo presenta considerazioni e linee guida per l'utilizzo di Database di Azure per i server MySQL.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Che cos'è un database di Azure per il server MySQL?

Un database di Azure per il server MySQL funge da punto di gestione centrale per più database. È lo stesso costrutto di server MySQL con cui probabilmente si ha familiarità nell'ambiente locale. In particolare, il database di Azure per il servizio MySQL è gestito, assicura le prestazioni garantite ed espone accesso e funzionalità a livello di server.

Un database di Azure per il server MySQL:

- Viene creato all'interno di una sottoscrizione di Azure.
- È la risorsa madre per i database.
- Fornisce uno spazio dei nomi per i database.
- È un contenitore con semantica di lunga durata: l'eliminazione di un server comporta l'eliminazione dei database in esso contenuti.
- Colloca risorse in un'area.
- Fornisce un endpoint di connessione per l'accesso a server e database.
- Fornisce l'ambito per i criteri di gestione applicati ai database: account di accesso, firewall, utenti, ruoli, configurazioni e così via.
- È disponibile in più versioni. Per altre informazioni, vedere [Supported Azure Database for MySQL database versions](./concepts-supported-versions.md) (Database di Azure supportato per le diverse versioni del database MySQL).

In un database di Azure per il server MySQL è possibile creare uno o più database. È possibile scegliere di creare un singolo database per ogni server per usare tutte le risorse o per creare più database per condividere le risorse. I prezzi sono strutturati per server, in base alla configurazione di piano tariffario, vCore e archiviazione (GB). Per altre informazioni, vedere i [piani tariffari](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Come connettersi ed eseguire l'autenticazione a un database di Azure per il server MySQL?

I seguenti elementi contribuiscono a garantire un accesso sicuro al database.

| Concetto di sicurezza | Descrizione     |
| :-- | :-- |
| **Autenticazione e autorizzazione** | Il database di Azure per il server MySQL supporta l'autenticazione nativa a MySQL. È possibile connettersi ed eseguire l'autenticazione a un server con l'account amministratore del server. |
| **Protocollo** | Il servizio supporta un protocollo basato su messaggi usato da MySQL. |
| **TCP/IP** | Il protocollo è supportato su TCP/IP e sui socket di dominio Unix. |
| **Firewall** | Per proteggere i dati, le regole del firewall impediscono qualsiasi accesso al server del database finché non si specificano i computer autorizzati. Vedere [Azure Database for MySQL Server firewall rules](./concepts-firewall-rules.md) (Database di Azure per le regole firewall del server MySQL). |
| **SSL** | Il servizio supporta l'attivazione di connessioni SSL tra le applicazioni e il server del database.  Vedere [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Configurare la connettività SSL nell'applicazione per connettersi in modo sicuro al database di Azure per MySQL). |

## <a name="stopstart-an-azure-database-for-mysql"></a>Arrestare/avviare un database di Azure per MySQL

Database di Azure per MySQL offre la possibilità di **arrestare** il server quando non è in uso e di **avviare** il server quando si riprende l'attività. Questa operazione viene essenzialmente eseguita per ridurre i costi dei server di database e pagare solo per la risorsa quando è in uso. Questa operazione diventa ancora più importante per i carichi di lavoro di sviluppo e test e quando si usa solo il server per una parte del giorno. Quando si arresta il server, tutte le connessioni attive verranno eliminate. In seguito, quando si vuole riportare online il server, è possibile usare l' [portale di Azure](how-to-stop-start-server.md) o l' [interfaccia](how-to-stop-start-server.md)della riga di comando.

Quando il server si trova nello stato **interrotto** , il calcolo del server non viene fatturato. Tuttavia, lo spazio di archiviazione continua a essere fatturato in quanto l'archiviazione del server rimane per assicurarsi che i file di dati siano disponibili quando il server viene riavviato.

> [!IMPORTANT]
> Quando si **Arresta** il server, rimane in tale stato per i 7 giorni successivi in un'estensione. Se non viene **avviata** manualmente durante questo periodo di tempo, il server verrà avviato automaticamente alla fine dei 7 giorni. Se non si utilizza il server, è possibile scegliere di **arrestarlo** di nuovo.

Nel momento in cui il server viene arrestato, non è possibile eseguire alcuna operazione di gestione sul server. Per modificare le impostazioni di configurazione nel server, sarà necessario [avviare il server](how-to-stop-start-server.md).

### <a name="limitations-of-stopstart-operation"></a>Limitazioni dell'operazione di arresto/avvio
- Non supportato con le configurazioni della replica di lettura (origine e repliche).

## <a name="how-do-i-manage-a-server"></a>Gestione di un server

È possibile gestire la creazione, l'eliminazione, la configurazione dei parametri del server (My. cnf), il ridimensionamento, la rete, la sicurezza, la disponibilità elevata, il backup & il ripristino, il monitoraggio del database di Azure per i server MySQL usando l'interfaccia della riga di comando di portale di Azure o Azure. Inoltre, le stored procedure seguenti sono disponibili nel database di Azure per MySQL per eseguire determinate attività di amministrazione del database richieste come privilegi utente con privilegi avanzati non è supportata nel server.

|**Nome della stored procedure**|**Parametri di input**|**Parametri di output**|**Nota sull'utilizzo**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|N/D|Equivale a [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) Command. Termina la connessione associata al processlist_id fornito dopo la terminazione di qualsiasi istruzione che la connessione è in esecuzione.|
|*mysql.az_kill_query*|processlist_id|N/D|Equivale a [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) Command. Termina l'istruzione attualmente in esecuzione per la connessione. Lascia attiva la connessione.|
|*mysql.az_load_timezone*|N/D|N/D|Carica le [tabelle del fuso orario](howto-server-parameters.md#working-with-the-time-zone-parameter) per consentire l' `time_zone` impostazione del parametro sui valori denominati, ad esempio "US/Pacific".|

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica del servizio, vedere [Azure Database for MySQL Overview](./overview.md) (Database di Azure per una panoramica di MySQL)
- Per informazioni sulle quote di risorse e sulle limitazioni specifiche in base al piano **tariffario**, vedere [piani tariffari](./concepts-pricing-tiers.md)
- Per informazioni sulla connessione al servizio, vedere [Raccolte connessioni per il database di Azure per MySQL](./concepts-connection-libraries.md).
