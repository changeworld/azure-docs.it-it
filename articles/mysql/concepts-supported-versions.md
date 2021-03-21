---
title: 'Versioni supportate: database di Azure per MySQL'
description: Informazioni sulle versioni di MySQL Server supportate nel database di Azure per il servizio MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 8b85307f01a11366a2147c947f26658f548932e8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467715"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Versioni supportate del Database di Azure per il server MySQL

Database di Azure per MySQL è stato sviluppato da [MySQL Community Edition](https://www.mysql.com/products/community/), usando il motore di archiviazione InnoDB. Il servizio supporta tutte le versioni principali correnti supportate dalla community, ovvero MySQL 5,6, 5,7 e 8,0. MySQL usa lo schema di denominazione X. Y. Z dove X è la versione principale, Y è la versione secondaria e Z è il rilascio della correzione di bug. Per altre informazioni sullo schema, vedere la [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> Nell'opzione di distribuzione a server singolo viene usato un gateway per reindirizzare le connessioni alle istanze del server. Dopo che è stata stabilita la connessione, il client MySQL visualizza la versione di MySQL impostata nel gateway e non la versione effettiva in esecuzione nell'istanza del server MySQL. Per determinare la versione dell'istanza del server MySQL, usare il comando `SELECT VERSION();` dal prompt di MySQL.

Database di Azure per MySQL supporta attualmente le versioni principali e secondarie seguenti di MySQL:


| Versione | Server unico <br/> Versione secondaria corrente |Server flessibile (anteprima) <br/> Versione secondaria corrente  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL versione 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) (ritirata) | Non supportato|
|MySQL versione 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL versione 8,0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

Leggere i criteri di supporto della versione per le versioni ritirate nella [documentazione dei criteri di supporto](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql) delle versioni.

## <a name="managing-updates-and-upgrades"></a>Gestire gli aggiornamenti
Il servizio gestisce automaticamente l'applicazione di patch per gli aggiornamenti delle versioni di correzione dei bug. Ad esempio, da 5.7.20 a 5.7.21.  

L'aggiornamento della versione principale è attualmente supportato dal servizio per gli aggiornamenti da MySQL v 5.6 a v 5.7. Per ulteriori informazioni, vedere [come eseguire gli aggiornamenti della versione principale](how-to-major-version-upgrade.md). Se si vuole eseguire l'aggiornamento da 5,7 a 8,0, è consigliabile eseguire il [dump e il ripristino](./concepts-migrate-dump-restore.md) in un server creato con la nuova versione del motore.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate sui criteri di controllo delle versioni di database di Azure per MySQL, vedere [questo documento](concepts-version-policy.md).
- Per informazioni sulle quote di risorse e sulle limitazioni specifiche in base al **livello di servizio**, vedere livelli di [servizio](./concepts-pricing-tiers.md)
