---
title: Funzionalità in anteprima nel database di Azure per PostgreSQL-iperscalabilità (CITUS)
description: Elenco aggiornato delle funzionalità attualmente in anteprima
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 04/07/2021
ms.openlocfilehash: 775785e1b5130499d69b269e72f5c774e9e5f3f9
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024071"
---
# <a name="preview-features-for-postgresql---hyperscale-citus"></a>Funzionalità di anteprima per PostgreSQL-iperscalabilità (CITUS)

Database di Azure per PostgreSQL-iperscalabilità (CITUS) offre anteprime per le funzionalità non rilasciate. Le versioni di anteprima vengono fornite senza un contratto di servizio e non sono consigliate per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.  Per ulteriori informazioni, vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="features-currently-in-preview"></a>Funzionalità attualmente disponibili in anteprima

Ecco le funzionalità attualmente disponibili per l'anteprima:

* **[Archiviazione a colonne](concepts-hyperscale-columnar.md)**.
  Archivia le colonne delle tabelle selezionate (anziché le righe) in modo contiguo sul disco. Supporta la compressione su disco. Ideale per carichi di lavoro analitici e di data warehousing.
* **[PostgreSQL 12 e 13](concepts-hyperscale-versions.md)**.
  Utilizzare la versione più recente del database nel gruppo di server.
* **[Livello Basic](concepts-hyperscale-tiers.md)**. Eseguire un gruppo di server usando solo un nodo coordinatore e nessun nodo di lavoro. Un modo economico per eseguire test e sviluppo iniziali e gestire piccoli carichi di lavoro di produzione.
* **[Leggere le repliche](howto-hyperscale-read-replicas-portal.md)** (attualmente solo la stessa area). Tutte le modifiche apportate al gruppo di server primario vengono riflesse nella replica e le query sulla replica non provocano alcun carico aggiuntivo sull'originale.
  Le repliche sono uno strumento utile per migliorare le prestazioni per i carichi di lavoro di sola lettura.
* **[PgBouncer gestito](concepts-hyperscale-limits.md#managed-pgbouncer-preview)**.
  Una connessione pool che consente a molti client di connettersi contemporaneamente al gruppo di server, limitando al tempo stesso il numero di connessioni attive. Soddisfa le richieste di connessione mantenendo l'esecuzione senza problemi del nodo coordinatore.
* **[PgAudit](concepts-hyperscale-audit.md)**. Fornisce la registrazione dettagliata della sessione e del controllo degli oggetti tramite la funzionalità di registrazione standard di PostgreSQL. Produce i log di controllo necessari per superare determinati controlli di certificazione per enti pubblici, finanziari o ISO.

### <a name="available-regions-for-preview-features"></a>Aree disponibili per le funzionalità di anteprima

L'estensione pgAudit è disponibile in tutte le [aree supportate da iperscale (CITUS)](concepts-hyperscale-configuration-options.md#regions).
Le altre funzionalità di anteprima sono disponibili solo negli **Stati Uniti orientali** .

## <a name="does-my-server-group-have-access-to-preview-features"></a>Il gruppo di server ha accesso alle funzionalità in anteprima?

Per determinare se il gruppo di server con iperscalabilità (CITUS) dispone di funzionalità in anteprima abilitate, passare alla pagina **Panoramica** del gruppo di server nel portale di Azure.
Se viene visualizzato il livello di proprietà **: Basic (anteprima)** o **livello: standard (anteprima)** , il gruppo di server ha accesso alle funzionalità in anteprima.

### <a name="how-to-get-access"></a>Come ottenere l'accesso

Quando si crea un nuovo gruppo di server con iperscalabilità (CITUS), selezionare la casella **Abilita funzionalità di anteprima.**

## <a name="contact-us"></a>Contatti

Inviaci informazioni sulla tua esperienza usando le funzionalità di anteprima, tramite posta elettronica [Richiedi Azure DB per PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com).
Questo indirizzo di posta elettronica non è un canale di supporto tecnico. Per problemi tecnici, aprire una [richiesta di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
