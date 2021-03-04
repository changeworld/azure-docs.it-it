---
title: 'Versioni supportate: database di Azure per PostgreSQL-server flessibile'
description: Descrive le versioni principali e secondarie di PostgreSQL supportate nel database di Azure per PostgreSQL-server flessibile.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 474545b7c5eca64b558e5fe26e23e6800b0cab96
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037167"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>Versioni principali di PostgreSQL supportate nel database di Azure per PostgreSQL-server flessibile

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

Database di Azure per PostgreSQL: il server flessibile supporta attualmente le versioni principali seguenti:

## <a name="postgresql-version-12"></a>PostgreSQL versione 12

La versione secondaria corrente è 12,5. Per ulteriori informazioni sui miglioramenti e sulle correzioni in questa versione secondaria, vedere la [documentazione di PostgreSQL](https://www.postgresql.org/docs/12/static/release-12-4.html) .

## <a name="postgresql-version-11"></a>PostgreSQL versione 11

La versione secondaria corrente è 11,10. Per ulteriori informazioni sui miglioramenti e sulle correzioni in questa versione secondaria, vedere la [documentazione di PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-9.html) .

## <a name="postgresql-version-10-and-older"></a>PostgreSQL versione 10 e precedenti

Non sono supportati PostgreSQL versione 10 e versioni precedenti per database di Azure per PostgreSQL-server flessibile. Se sono necessarie versioni precedenti, usare l'opzione di distribuzione a [server singolo](../concepts-supported-versions.md) .

## <a name="managing-upgrades"></a>Gestione degli aggiornamenti

Il progetto PostgreSQL rilascia periodicamente versioni secondarie per correggere i bug segnalati. Database di Azure per PostgreSQL applica automaticamente patch ai server attraverso release minori durante le distribuzioni mensili del servizio.

L'automazione per l'aggiornamento della versione principale non è ancora supportata. Ad esempio, non è attualmente disponibile un aggiornamento automatico da PostgreSQL 11 a PostgreSQL 12.<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->