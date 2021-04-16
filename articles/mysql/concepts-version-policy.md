---
title: Criteri di supporto della versione - Database di Azure per MySQL - Server singolo e server flessibile (anteprima)
description: Descrive i criteri per le versioni principali e secondarie di MySQL in Database di Azure per MySQL
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.date: 11/03/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 49e59c43e9eaedf770b1a8e052dd73aa331d31ce
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389571"
---
# <a name="azure-database-for-mysql-version-support-policy"></a>Criteri di supporto della versione di Database di Azure per MySQL

Questa pagina descrive i criteri di controllo delle versioni di Database di Azure per MySQL ed è applicabile alle modalità di distribuzione Database di Azure per MySQL - Server singolo e Database di Azure per MySQL - Server flessibile (anteprima).

## <a name="supported--mysql-versions"></a>Versioni di MySQL supportate

Database di Azure per MySQL è stato sviluppato da [MySQL Community Edition](https://www.mysql.com/products/community/)usando il motore di archiviazione InnoDB. Il servizio supporta tutte le versioni principali correnti supportate dalla community, mySQL 5.6, 5.7 e 8.0. MySQL usa lo schema di denominazione X.Y.Z dove X è la versione principale, Y è la versione secondaria e Z è la versione per la correzione di bug. Per altre informazioni sullo schema, vedere la [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

Database di Azure per MySQL supporta attualmente le versioni principali e secondarie seguenti di MySQL:

| Versione | [Server singolo](overview.md) <br/> Versione secondaria corrente |[Server flessibile (anteprima)](/azure/mysql/flexible-server/overview) <br/> Versione secondaria corrente  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL versione 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html)(ritirato) | Non supportato|
|MySQL versione 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL versione 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

> [!NOTE]
> Nell'opzione di distribuzione Server singolo viene usato un gateway per reindirizzare le connessioni alle istanze del server. Dopo che è stata stabilita la connessione, il client MySQL visualizza la versione di MySQL impostata nel gateway e non la versione effettiva in esecuzione nell'istanza del server MySQL. Per determinare la versione dell'istanza del server MySQL, usare il comando `SELECT VERSION();` dal prompt di MySQL. Se l'applicazione deve connettersi a una versione principale specifica, ad esempio v5.7 o v8.0, è possibile farlo modificando la porta nella stringa di connessione del server, come illustrato nella documentazione [qui.](concepts-supported-versions.md#connect-to-a-gateway-node-that-is-running-a-specific-mysql-version)

> [!IMPORTANT]
> MySQL v5.6 è stato ritirato nel server singolo a febbraio 2021. A partire dal 1° settembre 2021, non sarà possibile creare nuovi server v5.6 in Database di Azure per MySQL - Opzione di distribuzione Server singolo. Sarà tuttavia possibile eseguire ripresi tempormente e creare repliche in lettura per i server esistenti.

Leggere i criteri di supporto delle versioni per le versioni ritirate nella [documentazione dei criteri di supporto delle versioni.](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)

## <a name="major-version-support"></a>Supporto delle versioni principali
Ogni versione principale di MySQL sarà supportata da Database di Azure per MySQL dalla data in cui Azure inizia a supportare la versione fino al ritiro della versione da parte della community di MySQL, come specificato nei criteri di controllo delle versioni [.](https://www.mysql.com/support/eol-notice.html)

## <a name="minor-version-support"></a>Supporto delle versioni secondarie
Database di Azure per MySQL esegue automaticamente gli aggiornamenti della versione secondaria alla versione mySQL preferita di Azure come parte della manutenzione periodica. 

## <a name="major-version-retirement-policy"></a>Criteri di ritiro della versione principale
La tabella seguente fornisce i dettagli sul ritiro per le versioni principali di MySQL. Le date seguono i [criteri di controllo delle versioni di MySQL](https://www.mysql.com/support/eol-notice.html).

| Versione | Novità | supporto tecnico di Azure data di inizio | Data di ritiro|
| ----- | ----- | ------ | ----- |
| [MySQL 5.6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [Funzionalità](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | 20 marzo 2018 | Febbraio 2021
| [MySQL 5.7](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/) | [Funzionalità](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | 20 marzo 2018 | Ottobre 2023
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [Funzionalità )](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html) | 11 dicembre 2019 | Aprile 2026


## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>Versioni del motore MySQL ritirate non supportate in Database di Azure per MySQL

Dopo la data di ritiro per ogni versione del database MySQL, se si continua a eseguire la versione ritirata, tenere presente le restrizioni seguenti:
- Poiché la community non rilascia altre correzioni di bug o correzioni di sicurezza, Database di Azure per MySQL non correggerà il motore di database ritirato per eventuali bug o problemi di sicurezza o in caso contrario adotta misure di sicurezza per quanto riguarda il motore di database ritirato. Azure continuerà tuttavia a eseguire attività periodiche di manutenzione e applicazione di patch per l'host, il sistema operativo, i contenitori e qualsiasi altro componente correlato al servizio.
- Se si verificano problemi di supporto relativi al database MySQL, potrebbe non essere possibile fornire supporto. In questi casi, sarà necessario aggiornare il database per consentire all'utente di fornire supporto.
- Non sarà possibile creare nuovi server di database per la versione ritirata. Sarà tuttavia possibile eseguire ripresi tempormente e creare repliche in lettura per i server esistenti.
- Le nuove funzionalità del servizio sviluppate da Database di Azure per MySQL possono essere disponibili solo per le versioni supportate del server di database.
- I contratti di servizio per il tempo di attività si applicano esclusivamente ai problemi relativi al servizio Database di Azure per MySQL e non a eventuali tempi di inattività causati da bug correlati al motore di database.  
- In caso di grave minaccia per il servizio causato dalla vulnerabilità del motore di database MySQL identificata nella versione del database ritirata, Azure potrebbe scegliere di arrestare prima il nodo di calcolo del server di database per proteggere il servizio. Verrà richiesto di aggiornare il server prima di portare online il server. Durante il processo di aggiornamento, i dati verranno sempre protetti usando i backup automatici eseguiti nel servizio, che possono essere usati per ripristinare la versione precedente, se lo si desidera. 



## <a name="next-steps"></a>Passaggi successivi
- Vedere Database di Azure per MySQL - Versioni supportate [per server singolo](./concepts-supported-versions.md)
- Vedere Versioni supportate di Database di Azure per MySQL - Server flessibile [(anteprima)](flexible-server/concepts-supported-versions.md)
- Vedere Dump e [ripristino di](./concepts-migrate-dump-restore.md) MySQL per eseguire gli aggiornamenti.
