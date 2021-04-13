---
title: Replica dei dati in ingresso-database di Azure per MySQL
description: Informazioni sull'uso di Replica dei dati in ingresso per eseguire la sincronizzazione da un server esterno al servizio database di Azure per MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 83ad2d4f392afb6bb33c99a5449b9bc8ceeaa058
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312808"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Eseguire la replica dei dati in Database di Azure per MySQL

Replica dei dati in ingresso consente di sincronizzare i dati da un server MySQL esterno nel servizio Database di Azure per MySQL. Il server esterno può trovarsi in locale, in macchine virtuali, o essere un servizio di database ospitato da altri provider di servizi cloud. Replica dei dati in ingresso si basa sulla replica binaria (binlog) basata sul file di log o su GTID nativa in MySQL. Per altre informazioni su questo tipo di replica, vedere [MySQL binlog replication overview](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html) (Panoramica della replica basata su binlog di MySQL).

## <a name="when-to-use-data-in-replication"></a>Quando usare la replica dei dati in ingresso

Gli scenari principali da considerare per l'utilizzo di Replica dei dati in ingresso sono:

- **Sincronizzazione ibrida dei dati**: con la replica dei dati in ingresso è possibile mantenere i dati sincronizzati tra i server locali e Database di Azure per MySQL. Questa sincronizzazione è utile per la creazione di applicazioni ibride. Il metodo è particolarmente interessante quando si ha già un server di database locale, ma si vogliono spostare i dati in un'area più vicina agli utenti finali.
- **Sincronizzazione multi-cloud**: per soluzioni cloud complesse, usare la replica dei dati in ingresso per sincronizzare i dati tra Database di Azure per MySQL e diversi provider cloud, inclusi servizi di database e macchine virtuali ospitati nei cloud.

Per gli scenari di migrazione, usare il [servizio migrazione del database di Azure](https://azure.microsoft.com/services/database-migration/)(DMS).

## <a name="limitations-and-considerations"></a>Limitazioni e considerazioni

### <a name="data-not-replicated"></a>Dati non replicati

Il [*database di sistema MySQL*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) nel server di origine non viene replicato. Inoltre, le modifiche agli account e alle autorizzazioni nel server di origine non vengono replicate. Se si crea un account nel server di origine e questo account deve accedere al server di replica, creare manualmente lo stesso account nel server di replica. Per informazioni sulle tabelle contenute nel database di sistema, vedere la [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html).

### <a name="filtering"></a>Filtro

Per ignorare le tabelle di replica del server di origine (ospitate in locale, in macchine virtuali o in un servizio di database ospitato da altri provider di servizi cloud), il `replicate_wild_ignore_table` parametro è supportato. Facoltativamente, aggiornare questo parametro nel server di replica ospitato in Azure usando il [portale di Azure](howto-server-parameters.md) o l'interfaccia della riga di comando di [Azure](howto-configure-server-parameters-using-cli.md).

Per altre informazioni su questo parametro, vedere la [documentazione di MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table).

## <a name="supported-in-general-purpose-or-memory-optimized-tier-only"></a>Supportato solo nel livello per utilizzo generico o con ottimizzazione per la memoria

Replica dei dati in ingresso è supportato solo nei piani tariffari per utilizzo generico e con ottimizzazione per la memoria.

### <a name="requirements"></a>Requisiti

- La versione del server di origine deve essere almeno la versione 5,6 di MySQL.
- Le versioni del server di origine e di replica devono essere uguali. Ad esempio, in entrambi deve essere installato MySQL versione 5.6 o MySQL versione 5.7.
- Ogni tabella deve avere una chiave primaria.
- Il server di origine deve usare il motore InnoDB di MySQL.
- L'utente deve disporre delle autorizzazioni per configurare la registrazione binaria e creare nuovi utenti nel server di origine.
- Se nel server di origine è abilitato SSL, verificare che il certificato della CA SSL fornito per il dominio sia stato incluso nel `mysql.az_replication_change_master` `mysql.az_replication_change_master_with_gtid` stored procedure o. Fare riferimento agli [esempi](./howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication) seguenti e al `master_ssl_ca` parametro.
- Verificare che l'indirizzo IP del server di origine sia stato aggiunto alle regole del firewall del server di replica di database di Azure per MySQL. Aggiornare le regole firewall usando il [portale di Azure](./howto-manage-firewall-using-portal.md) o l'[interfaccia della riga di comando di Azure](./howto-manage-firewall-using-cli.md).
- Verificare che il computer che ospita il server di origine consenta il traffico in ingresso e in uscita sulla porta 3306.
- Verificare che il server di origine disponga di un **indirizzo IP pubblico**, che il DNS sia accessibile pubblicamente o che il server di origine disponga di un nome di dominio completo (FQDN).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [configurare la replica dei dati](howto-data-in-replication.md)
- Informazioni sulla [replica in Azure con repliche in lettura](concepts-read-replicas.md)
- Informazioni su come [eseguire la migrazione dei dati con tempi di inattività minimi con DMS](howto-migrate-online.md)
