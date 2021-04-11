---
title: Versioni supportate-iperscala (CITUS)-database di Azure per PostgreSQL
description: Versioni di PostgreSQL disponibili nel database di Azure per PostgreSQL-iperscalabilità (CITUS)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: d8a584b6ba752e8f9220defa575f519828ba07e6
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023960"
---
# <a name="supported-database-versions-in-azure-database-for-postgresql--hyperscale-citus"></a>Versioni di database supportate nel database di Azure per PostgreSQL: iperscalabilità (CITUS)

## <a name="postgresql-versions"></a>Versioni di PostgreSQL

> [!IMPORTANT]
> Le versioni di PostgreSQL personalizzabili in iperscalabilità (CITUS) sono attualmente in anteprima.  Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
>
> È possibile visualizzare un elenco completo di altre nuove funzionalità in [Anteprima funzionalità per l'iperscalabilità (CITUS)](hyperscale-preview-features.md).

La versione di PostgreSQL in esecuzione in un gruppo di server con iperscalabilità (CITUS) è personalizzabile durante la creazione. La scelta di un valore diverso dalla versione 11 è attualmente una funzionalità in anteprima.

Iperscale (CITUS) supporta attualmente le versioni principali seguenti:

### <a name="postgresql-version-13-preview"></a>PostgreSQL versione 13 (anteprima)

La versione secondaria corrente è 13,2. Per ulteriori informazioni sui miglioramenti e sulle correzioni in questa versione secondaria, vedere la [documentazione di PostgreSQL](https://www.postgresql.org/docs/13/static/release-13-2.html) .

### <a name="postgresql-version-12-preview"></a>PostgreSQL versione 12 (anteprima)

La versione secondaria corrente è 12,6. Per ulteriori informazioni sui miglioramenti e sulle correzioni in questa versione secondaria, vedere la [documentazione di PostgreSQL](https://www.postgresql.org/docs/12/static/release-12-6.html) .

### <a name="postgresql-version-11"></a>PostgreSQL versione 11

La versione secondaria corrente è 11,11. Per ulteriori informazioni sui miglioramenti e sulle correzioni in questa versione secondaria, vedere la [documentazione di PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-11.html) .

### <a name="postgresql-version-10-and-older"></a>PostgreSQL versione 10 e precedenti

Non sono supportati PostgreSQL versione 10 e versioni precedenti per database di Azure per PostgreSQL-iperscalabilità (CITUS).

## <a name="citus-and-other-extension-versions"></a>CITUS e altre versioni di estensione

A seconda della versione di PostgreSQL in esecuzione in un gruppo di server, verranno installate anche [versioni diverse delle estensioni Postgres](concepts-hyperscale-extensions.md) .  In particolare, Postgres 13 viene fornita con CITUS 10 e le versioni Postgres precedenti sono fornite con CITUS 9,5.

## <a name="next-steps"></a>Passaggi successivi

* Vedere quali [estensioni](concepts-hyperscale-extensions.md) sono installate in quali versioni.
* Informazioni su come [creare un gruppo di server con iperscalabilità (CITUS)](quickstart-create-hyperscale-portal.md).
