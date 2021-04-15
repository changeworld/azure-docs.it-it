---
title: Versioni supportate - Database di Azure per MySQL
description: Informazioni sulle versioni del server MySQL supportate nel servizio Database di Azure per MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 1be15c16a1897797326ea869c34c3590ffb07691
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363870"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Versioni supportate del Database di Azure per il server MySQL

Database di Azure per MySQL è stato sviluppato da [MySQL Community Edition](https://www.mysql.com/products/community/)usando il motore di archiviazione InnoDB. Il servizio supporta tutte le versioni principali correnti supportate dalla community, ad esempio MySQL 5.6, 5.7 e 8.0. MySQL usa lo schema di denominazione X.Y.Z in cui X è la versione principale, Y è la versione secondaria e Z è la versione di correzione dei bug. Per altre informazioni sullo schema, vedere la [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).



## <a name="connect-to-a-gateway-node-that-is-running-a-specific-mysql-version"></a>Connettersi a un nodo gateway che esegue una versione di MySQL specifica

Nell'opzione di distribuzione Server singolo viene usato un gateway per reindirizzare le connessioni alle istanze del server. Dopo che è stata stabilita la connessione, il client MySQL visualizza la versione di MySQL impostata nel gateway e non la versione effettiva in esecuzione nell'istanza del server MySQL. Per determinare la versione dell'istanza del server MySQL, usare il comando `SELECT VERSION();` dal prompt di MySQL. Per [altre informazioni sui](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture#connectivity-architecture) gateway nell'architettura del servizio Database di Azure per MySQL, vedere Architettura della connettività.

Poiché Database di Azure per MySQL supporta le versioni principali v5.6, v5.7 e v8.0, la porta predefinita 3306 per la connessione a Database di Azure per MySQL esegue il client MySQL versione 5.6 (denominatore meno comune) per supportare le connessioni ai server di tutte e 3 le versioni principali supportate. Tuttavia, se l'applicazione ha un requisito per connettersi a una versione principale specifica, ad esempio v5.7 o v8.0, è possibile farlo modificando la porta nella stringa di connessione del server.

Nel servizio Database di Azure per MySQL i nodi gateway sono in ascolto sulla porta 3308 per i client v5.7 e sulla porta 3309 per i client v8.0. In altre parole, se si vuole connettersi al client gateway v5.7, è necessario usare il nome completo del server e la porta 3308 per connettersi al server dall'applicazione client. Analogamente, se si vuole connettersi al client gateway v8.0, è possibile usare il nome completo del server e la porta 3309 per connettersi al server. Per maggiore chiarezza, vedere l'esempio seguente.

:::image type="content" source="./media/concepts-supported-versions/concepts-supported-versions-gateway.png" alt-text="Esempio di connessione tramite diverse versioni di gateway mysql":::


## <a name="azure-database-for-mysql-currently-supports-the-following-major-and-minor-versions-of-mysql"></a>Database di Azure per MySQL supporta attualmente le versioni principali e secondarie di MySQL seguenti:


| Versione | [Server singolo](overview.md) <br/> Versione secondaria corrente |[Server flessibile (anteprima)](/azure/mysql/flexible-server/overview) <br/> Versione secondaria corrente  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL versione 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) (ritirato) | Non supportato|
|MySQL versione 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL versione 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

Leggere i criteri di supporto delle versioni per le versioni ritirate nella [documentazione dei criteri di supporto delle versioni.](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)

## <a name="managing-updates-and-upgrades"></a>Gestire gli aggiornamenti
Il servizio gestisce automaticamente l'applicazione di patch per gli aggiornamenti delle versioni di correzione dei bug. Ad esempio, da 5.7.20 a 5.7.21.  

L'aggiornamento della versione principale è attualmente supportato dal servizio per gli aggiornamenti da MySQL v5.6 a v5.7. Per altri dettagli, vedere [come eseguire gli aggiornamenti delle versioni principali.](how-to-major-version-upgrade.md) Se si desidera eseguire l'aggiornamento dalla versione 5.7 alla versione 8.0, è consigliabile eseguire [il dump](./concepts-migrate-dump-restore.md) e il ripristino in un server creato con la nuova versione del motore.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate sui criteri di controllo delle versioni di Database di Azure per MySQL, vedere [questo documento.](concepts-version-policy.md)
- Per informazioni sulle quote e le limitazioni specifiche delle risorse in base al livello **di servizio,** vedere [Livelli di servizio](./concepts-pricing-tiers.md)
