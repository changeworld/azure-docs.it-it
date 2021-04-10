---
title: Limiti e limitazioni – iperscalabilità (CITUS)-database di Azure per PostgreSQL
description: Limiti correnti per i gruppi di server iperscalare (CITUS)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: cfd57c60c4bfd60976eb28822c696412cabda212
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023961"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-limits-and-limitations"></a>Limiti e limitazioni per database di Azure per PostgreSQL – iperscalabilità (CITUS)

Nella sezione seguente vengono descritti i limiti di capacità e funzionalità nel servizio di iperscalabilità (CITUS).

## <a name="maximum-connections"></a>Numero massimo di connessioni

Ogni connessione PostgreSQL (anche quelle inattive) USA almeno 10 MB di memoria, quindi è importante limitare le connessioni simultanee. Di seguito sono riportati i limiti che abbiamo scelto di rendere integri i nodi:

* Nodo coordinatore
   * Numero massimo di connessioni: 300
   * Numero massimo di connessioni utente: 297
* Nodo del ruolo di lavoro
   * Numero massimo di connessioni: 600
   * Numero massimo di connessioni utente: 597

> [!NOTE]
> In un gruppo di server con [funzionalità di anteprima](hyperscale-preview-features.md) abilitate, i limiti di connessione al coordinatore sono leggermente diversi:
>
> * Numero massimo connessioni del nodo coordinatore
>    * 300 per 0-3 vcore
>    * 500 per 4-15 vcore
>    * 1000 per 16 + vcore

I tentativi di connessione oltre questi limiti avranno esito negativo e si verificherà un errore. Il sistema riserva tre connessioni per il monitoraggio dei nodi ed è per questo motivo che per le query utente sono disponibili tre connessioni minori rispetto alle connessioni totali.

### <a name="connection-pooling"></a>Pool di connessioni

La creazione di nuove connessioni richiede tempo. Funziona per la maggior parte delle applicazioni, che richiedono numerose connessioni di breve durata. È consigliabile usare una connessione pool, sia per ridurre le transazioni inattive che per riutilizzare le connessioni esistenti. Per altre informazioni, visitare il [post di Blog](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

È possibile eseguire la propria connessione pool o usare PgBouncer gestito da Azure.

#### <a name="managed-pgbouncer-preview"></a>PgBouncer gestito (anteprima)

> [!IMPORTANT]
> La connessione PgBouncer gestita pool in iperscala (CITUS) è attualmente in anteprima. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
>
> È possibile visualizzare un elenco completo di altre nuove funzionalità in [Anteprima funzionalità per l'iperscalabilità (CITUS)](hyperscale-preview-features.md).

I pool di connessioni, ad esempio PgBouncer, consentono a più client di connettersi contemporaneamente al nodo coordinatore. Le applicazioni si connettono a pool e pool inoltra i comandi al database di destinazione.

Quando i client si connettono tramite PgBouncer, il numero di connessioni che possono essere eseguite attivamente nel database non cambia. Al contrario, PgBouncer Accoda le connessioni in eccesso e le esegue quando il database è pronto.

Iperscale (CITUS) offre ora un'istanza gestita di PgBouncer per i gruppi di server (in anteprima). Supporta fino a 2.000 connessioni client simultanee.
Per connettersi tramite PgBouncer, seguire questa procedura:

1. Passare alla pagina **stringhe di connessione** per il gruppo di server nel portale di Azure.
2. Abilitare la casella di controllo **PgBouncer stringhe di connessione**. (Le stringhe di connessione elencate vengono modificate).
3. Aggiornare le applicazioni client per la connessione alla nuova stringa.

## <a name="storage-scaling"></a>Ridimensionamento dell'archiviazione

È possibile aumentare le capacità di archiviazione sui nodi coordinatore e di lavoro (aumentate), ma non è possibile ridurle (diminuita).

## <a name="storage-size"></a>Dimensioni dello spazio di archiviazione

Fino a 2 TiB di archiviazione è supportato nei nodi del coordinatore e del ruolo di lavoro. Per le dimensioni del nodo e del cluster, vedere le [Opzioni di archiviazione](concepts-hyperscale-configuration-options.md#compute-and-storage) e il calcolo IOPS disponibili.

## <a name="database-creation"></a>Creazione del database

Il portale di Azure fornisce le credenziali per la connessione a un gruppo di server di database per iperscala (CITUS), ovvero il `citus` database. La creazione di un altro database non è attualmente consentita e il comando CREATE DATABASE avrà esito negativo con un errore.

## <a name="columnar-storage"></a>Archiviazione a colonne

Iperscale (CITUS) presenta attualmente queste limitazioni con le [tabelle a colonne](concepts-hyperscale-columnar.md):

* La compressione si trova su disco, non in memoria
* Solo accodamento (nessun supporto di aggiornamento/eliminazione)
* Nessun recupero di spazio (ad esempio, le transazioni di cui è stato eseguito il rollback potrebbero ancora utilizzare spazio su disco)
* Nessun supporto per gli indici, analisi degli indici o analisi degli indici bitmap
* Nessun tidscans
* Nessuna analisi di esempio
* Nessun supporto popup (valori di grandi dimensioni supportati inline)
* Nessun supporto per le istruzioni ON CONFLICT (eccetto eseguire azioni senza alcuna destinazione specificata).
* Nessun supporto per i blocchi di tupla (SELECT... PER CONDIVISIONE SELEZIONARE... PER L'AGGIORNAMENTO)
* Nessun supporto per il livello di isolamento serializzabile
* Supporto solo per le versioni del server PostgreSQL 12 +
* Nessun supporto per chiavi esterne, vincoli univoci o vincoli di esclusione
* Nessun supporto per la decodifica logica
* Nessun supporto per analisi parallele intra-nodo
* Nessun supporto per AFTER... PER ogni trigger di riga
* Nessuna tabella a colonne non registrata
* Nessuna tabella a colonne temporanea

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [creare un gruppo di server con iperscalabilità (CITUS) nel portale](quickstart-create-hyperscale-portal.md).
