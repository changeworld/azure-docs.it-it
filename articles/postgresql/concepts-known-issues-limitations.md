---
title: Problemi noti e limitazioni-database di Azure per PostgreSQL-server singolo e server flessibile (anteprima)
description: Elenca i problemi noti che i clienti devono conoscere.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 3c26368e6281ad74d617891ba15c980117b25a6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100106431"
---
# <a name="azure-database-for-postgresql---known-issues-and-limitations"></a>Database di Azure per PostgreSQL-problemi noti e limitazioni

Questa pagina fornisce un elenco dei problemi noti di database di Azure per PostgreSQL che potrebbero influito sull'applicazione. Elenca anche eventuali attenuazioni e consigli per la risoluzione del problema.

## <a name="intelligent-performance---query-store"></a>Prestazioni intelligenti-Query Store

Applicabile a database di Azure per PostgreSQL: singolo server.

| Applicabile | Causa | Soluzione|
| ----- | ------ | ---- | 
| PostgreSQL 9,6, 10, 11 | L'attivazione del parametro Server `pg_qs.replace_parameter_placeholders` può causare un arresto del server in alcuni rari scenari. | Tramite il portale di Azure, sezione parametri server, attivare `pg_qs.replace_parameter_placeholders` e salvare il valore del parametro `OFF` .   | 

## <a name="server-parameters"></a>Parametri del server

Applicabile a database di Azure per PostgreSQL: server singolo e server flessibile

| Applicabile | Causa | Soluzione| 
| ----- | ------ | ---- | 
| PostgreSQL 9,6, 10, 11 | Il passaggio del parametro Server `max_locks_per_transaction` a un valore superiore rispetto a quello [consigliato](https://www.postgresql.org/docs/11/kernel-resources.html) potrebbe causare il mancato completamento del server dopo un riavvio. | Lasciare il valore predefinito (32 o 64) o modificare un valore ragionevole per la [documentazione](https://www.postgresql.org/docs/11/kernel-resources.html)di PostgreSQL. <br> <br> Dal lato del servizio, si sta lavorando per limitare il valore elevato in base allo SKU.  | 

## <a name="next-steps"></a>Passaggi successivi
- Vedere Query Store [procedure consigliate](./concepts-query-store-best-practices.md)
