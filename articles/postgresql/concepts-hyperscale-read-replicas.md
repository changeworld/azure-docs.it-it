---
title: Leggere repliche-database di Azure per PostgreSQL-iperscalabilità (CITUS)
description: Questo articolo descrive la funzionalità di lettura della replica in database di Azure per PostgreSQL-iperscalabilità (CITUS).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: cf8c64bf3858f0d3b7d633b94bc7302fbe563f87
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023965"
---
# <a name="read-replicas-in-azure-database-for-postgresql---hyperscale-citus"></a>Leggere repliche nel database di Azure per PostgreSQL-iperscalabilità (CITUS)

> [!IMPORTANT]
> Le repliche Read in iperscala (CITUS) sono attualmente in anteprima. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
>
> È possibile visualizzare un elenco completo di altre nuove funzionalità in [Anteprima funzionalità per l'iperscalabilità (CITUS)](hyperscale-preview-features.md).

La funzionalità lettura replica consente di replicare i dati da un gruppo di server iperscalabile (CITUS) a un gruppo di server di sola lettura. Le repliche vengono aggiornate in **modo asincrono** con la tecnologia di replica fisica PostgreSQL. È possibile eseguire la replica dal server primario a un numero illimitato di repliche.

Le repliche sono nuovi gruppi di server gestiti in modo analogo ai gruppi di server CITUS (Regular iperscale). Per ogni replica in lettura, viene addebitato il costo delle risorse di calcolo e di archiviazione sottoposte a provisioning, espresse rispettivamente in vCore e GB/mese.

Informazioni su come [creare e gestire le repliche](howto-hyperscale-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Quando usare una replica in lettura

La funzionalità di replica in lettura aiuta a migliorare prestazioni e scalabilità dei carichi di lavoro con utilizzo elevato della lettura. I carichi di lavoro di lettura possono essere isolati con le repliche, mentre i carichi di lavoro di scrittura possono essere indirizzati al database primario.

Uno scenario comune consiste nel fare in modo che i carichi di lavoro BI e analitici usino le repliche in lettura come origine dati per la creazione di report.

Poiché le repliche sono di sola lettura, non riducono direttamente gli oneri di capacità di scrittura nel database primario.

### <a name="considerations"></a>Considerazioni

Questa funzionalità è destinata agli scenari in cui l'intervallo di replica è accettabile ed è destinato all'offload delle query. Non è destinato a scenari di replica sincrona in cui i dati di replica devono essere aggiornati. Si verifica un ritardo misurabile tra il database primario e la replica. Il ritardo può essere di minuti o anche di ore a seconda del carico di lavoro e della latenza tra il database primario e la replica.  I dati nella replica diventano infine coerenti con i dati nel database primario. Usare questa funzionalità per i carichi di lavoro in grado di sostenere questo ritardo. 

## <a name="create-a-replica"></a>Creare una replica

Quando si avvia il flusso di lavoro di creazione della replica, viene creato un gruppo di server CITUS (iperscalabilità vuota). Il nuovo gruppo viene compilato con i dati presenti nel gruppo di server primario. Il tempo di creazione dipende dalla quantità di dati nel database primario e dall'ora dell'ultimo backup completo settimanale. Il tempo può variare da pochi minuti a diverse ore.

La funzionalità di replica in lettura usa la replica fisica di PostgreSQL e non la replica logica. La modalità predefinita è la replica di streaming usando gli slot di replica.
Se necessario, viene usato il log shipping per mettersi in pari.

Informazioni su come [creare una replica di lettura nel portale di Azure](howto-hyperscale-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Connessione a una replica

Quando si crea una replica, non eredita le regole del firewall per il gruppo di server primario. Queste regole devono essere configurate in modo indipendente per la replica.

La replica eredita l'account amministratore ("CITUS") dal gruppo di server primario.
Tutti gli account utente vengono replicati nelle repliche di lettura. È possibile connettersi a una replica di lettura solo usando gli account utente disponibili nel server primario.

È possibile connettersi al nodo coordinatore della replica usando il nome host e un account utente valido, come si farebbe per un gruppo di server CITUS (Regular overscale).
Per un server denominato **My replica** con il nome utente amministratore **CITUS**, è possibile connettersi al nodo coordinatore della replica usando PSQL:

```bash
psql -h c.myreplica.postgres.database.azure.com -U citus@myreplica -d postgres
```

Quando richiesto, immettere la password per l'account dell'utente.

## <a name="considerations"></a>Considerazioni

Questa sezione riepiloga le considerazioni sulla funzionalità di replica in lettura.

### <a name="new-replicas"></a>Nuove repliche

Una replica di lettura viene creata come un nuovo gruppo di server di iperscala (CITUS). Un gruppo di server esistente non può essere creato in una replica. Non è possibile creare una replica di un'altra replica in lettura.

### <a name="replica-configuration"></a>Configurazione della replica

Una replica viene creata usando le stesse impostazioni di calcolo, archiviazione e nodo del ruolo di lavoro del database primario. Dopo la creazione di una replica, è possibile modificare diverse impostazioni, tra cui l'archiviazione e il periodo di conservazione dei backup. Le altre impostazioni non possono essere modificate nelle repliche, ad esempio le dimensioni di archiviazione e il numero di nodi del ruolo di lavoro.

Ricordarsi di tenere le repliche sufficientemente solide per tenere le modifiche che arrivano dal database primario. Ad esempio, assicurarsi di ridimensionare la potenza di calcolo nelle repliche se la si ridimensiona nel database primario.

Le regole del firewall e le impostazioni dei parametri non vengono ereditate dal server primario alla replica quando la replica viene creata o successivamente.

### <a name="regions"></a>Regioni

I gruppi di server con iperscalabilità (CITUS) supportano solo la replica della stessa area.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare e gestire le repliche in lettura nel portale di Azure](howto-hyperscale-read-replicas-portal.md).
