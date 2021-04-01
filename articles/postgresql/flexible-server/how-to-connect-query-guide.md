---
title: Connessione ed esecuzione di query-PostgreSQL con server flessibili
description: Collegamenti alle guide introduttive che illustrano come connettersi al server flessibile del database di Azure per PostgreSQL ed eseguire query.
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 12/08/2020
ms.openlocfilehash: ee3b1f7db8bdafb1233b32579e032e8c864c37a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97364580"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--flexible-server"></a>Panoramica di connessione ed esecuzione di query per database di Azure per PostgreSQL-server flessibile

Il documento seguente include collegamenti ad esempi che illustrano come connettersi ed eseguire query con il server singolo database di Azure per PostgreSQL. Questa guida include anche le raccomandazioni e l'estensione TLS che è possibile usare per connettersi al server nelle lingue supportate di seguito.

>[!IMPORTANT]
> Il server flessibile database di Azure per PostgreSQL è in **Anteprima**.

## <a name="quickstarts"></a>Avvi rapidi

| Guida introduttiva | Descrizione |
|---|---|
|[PgAdmin](https://www.pgadmin.org/)|È possibile utilizzare pgAdmin per connettersi al server e semplificare la creazione, la manutenzione e l'utilizzo di oggetti di database.|
|[PSQL in Azure Cloud Shell](./quickstart-create-server-cli.md#connect-using-postgresql-command-line-client)|Questo articolo illustra come eseguire [**PSQL**](https://www.postgresql.org/docs/current/static/app-psql.html) in [Azure cloud Shell](../../cloud-shell/overview.md) per connettersi al server e quindi eseguire istruzioni per eseguire query e inserire, aggiornare ed eliminare dati nel database. È possibile eseguire **PSQL** se installato nell'ambiente di sviluppo|
|[Python](connect-python.md)|Questa Guida introduttiva illustra come usare Python per connettersi a un database e usare il lavoro con oggetti di database per eseguire query sui dati. |
|[Django con il servizio app](tutorial-django-app-service-postgres.md)|Questa esercitazione illustra come usare Ruby per creare un programma per connettersi a un database e usare il lavoro con gli oggetti di database per eseguire query sui dati.|

## <a name="tls-considerations-for-database-connectivity"></a>Considerazioni su TLS per la connettività del database

Transport Layer Security (TLS) viene usato da tutti i driver forniti o supportati da Microsoft per la connessione ai database in database di Azure per PostgreSQL. Non è necessaria alcuna configurazione speciale, ma si applica TLS 1,2 per i server appena creati. È consigliabile usare TLS 1,0 e 1,1, quindi aggiornare la versione di TLS per i server. Vedere [come configurare TLS](how-to-connect-tls-ssl.md)

## <a name="postgresql-extensions"></a>Estensioni di PostgreSQL

PostgreSQL offre la capacità di estendere le funzionalità del database usando le estensioni. Le estensioni creano un bundle di più oggetti SQL correlati in un singolo pacchetto che può essere caricato o rimosso dal database con un singolo comando. Dopo essere stato caricate nel database, le estensioni si comportano come le funzionalità predefinite.

- [Estensioni Postgres 12](./concepts-extensions.md#postgres-12-extensions)
- [Estensioni Postgres 11](./concepts-extensions.md#postgres-11-extensions)
- [dblink e postgres_fdw](./concepts-extensions.md#dblink-and-postgres_fdw)
- [pg_prewarm](./concepts-extensions.md#pg_prewarm)
- [pg_stat_statements](./concepts-extensions.md#pg_stat_statements)

Per altri dettagli, vedere [come usare le estensioni di PostgreSQL in server flessibile](concepts-extensions.md).

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire la migrazione dei dati tramite dump e Restore](../howto-migrate-using-dump-and-restore.md)
- [Eseguire la migrazione dei dati tramite importazione ed esportazione](../howto-migrate-using-export-and-import.md)
