---
title: Backup e ripristino - Hyperscale (Citus) - Database di Azure per PostgreSQL
description: Protezione dei dati da danneggiamenti o eliminazioni accidentali
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 8dfc82ce79f33553be5220b52a1e415d99c26518
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483912"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Backup e ripristino in Database di Azure per PostgreSQL - Hyperscale (Citus)

Database di Azure per PostgreSQL: Hyperscale (Citus) automaticamente i backup di ogni nodo e li archivia nell'archiviazione con ridondanza locale. I backup possono essere usati per ripristinare il cluster Hyperscale (Citus) a un'ora specificata. Il backup e il ripristino sono una parte essenziale di qualsiasi strategia di continuità aziendale, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale.

## <a name="backups"></a>Backup

Almeno una volta al giorno, Database di Azure per PostgreSQL consente di eseguire backup snapshot dei file di dati e del log delle transazioni del database. I backup consentono di ripristinare un server in qualsiasi momento entro il periodo di conservazione. Il periodo di conservazione è attualmente di 35 giorni per tutti i cluster. Tutti i backup vengono crittografati usando la crittografia AES a 256 bit.

Nelle aree di Azure che supportano le zone di disponibilità, gli snapshot di backup vengono archiviati in tre zone di disponibilità. Finché almeno una zona di disponibilità è online, il cluster Hyperscale (Citus) è ripristinabile.

I file di backup non possono essere esportati. Possono essere usati solo per le operazioni di ripristino in Database di Azure per PostgreSQL.

### <a name="backup-storage-cost"></a>Costo dell'archiviazione dei backup

Per i prezzi correnti dell'archiviazione di backup, vedere la pagina dei prezzi di Database di Azure per PostgreSQL - Hyperscale (Citus) [prezzi.](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)

## <a name="restore"></a>Restore

In Database di Azure per PostgreSQL il ripristino di un cluster Hyperscale (Citus) crea un nuovo cluster dai backup dei nodi originali. 

> [!IMPORTANT]
>È possibile ripristinare il cluster Hyperscale (Citus) solo all'interno della stessa sottoscrizione e gruppo di risorse e con un nome di cluster diverso.


> [!IMPORTANT]
> I Hyperscale (Citus) cluster non possono essere ripristinati. Se si elimina il cluster, tutti i nodi che appartengono al cluster vengono eliminati e non possono essere ripristinati. Per proteggere le risorse del cluster, dopo la distribuzione, da eliminazioni accidentali o modifiche impreviste, gli amministratori possono sfruttare [i blocchi di gestione](../azure-resource-manager/management/lock-resources.md).

### <a name="point-in-time-restore-pitr"></a>Ripristino temporato (PITR)

È possibile ripristinare un cluster in qualsiasi momento negli ultimi 35 giorni.
Il ripristino temporizzato è utile in più scenari, Può accadere, ad esempio, quando un utente elimina accidentalmente i dati o rimuove una tabella o un database importante oppure se un'applicazione sovrascrive accidentalmente dati validi con dati non validi.

Il processo di ripristino crea un nuovo cluster nella stessa area di Azure, nella stessa sottoscrizione e nello stesso gruppo di risorse dell'originale. Il cluster ha la configurazione originale: lo stesso numero di nodi, numero di vCore, dimensioni di archiviazione, ruoli utente, versione di PostgreSQL e versione dell'estensione Citus.

Le impostazioni del firewall e i parametri del server PostgreSQL non vengono mantenuti dal gruppo di server originale, ma vengono reimpostati sui valori predefiniti. Il firewall impedirà tutte le connessioni. Sarà necessario modificare manualmente queste impostazioni dopo il ripristino. In generale, vedere l'elenco delle attività [post-ripristino suggerite.](howto-hyperscale-restore-portal.md#post-restore-tasks)

## <a name="next-steps"></a>Passaggi successivi

* Vedere i passaggi per [ripristinare un gruppo di server](howto-hyperscale-restore-portal.md) nella portale di Azure.
* Informazioni sulle zone [di disponibilità di Azure.](../availability-zones/az-overview.md)
