---
title: Backup e ripristino - Hyperscale (Citus) - Database di Azure per PostgreSQL
description: Protezione dei dati da danneggiamento o eliminazione accidentale
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/14/2021
ms.openlocfilehash: 7681e9c28bbbbcec06bcc1cf2bf469f1b4189d79
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520174"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Backup e ripristino in Database di Azure per PostgreSQL - Hyperscale (Citus)

Database di Azure per PostgreSQL: Hyperscale (Citus) automaticamente i backup di ogni nodo e li archivia nell'archiviazione con ridondanza locale. I backup possono essere usati per ripristinare il Hyperscale (Citus) di server a un'ora specificata.
Il backup e il ripristino sono una parte essenziale di qualsiasi strategia di continuità aziendale, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale.

## <a name="backups"></a>Backup

Almeno una volta al giorno, Database di Azure per PostgreSQL eseguirà backup snapshot dei file di dati e del log delle transazioni del database. I backup consentono di ripristinare un server in qualsiasi momento all'interno del periodo di conservazione. Il periodo di conservazione è attualmente di 35 giorni per tutti i gruppi di server. Tutti i backup vengono crittografati usando la crittografia AES a 256 bit.

Nelle aree di Azure che supportano le zone di disponibilità, gli snapshot di backup vengono archiviati in tre zone di disponibilità. Finché almeno una zona di disponibilità è online, il gruppo Hyperscale (Citus) server è ripristinabile.

I file di backup non possono essere esportati. Possono essere usati solo per le operazioni di ripristino in Database di Azure per PostgreSQL.

### <a name="backup-storage-cost"></a>Costo dell'archiviazione dei backup

Per i prezzi correnti dell'archiviazione di backup, vedere la pagina dei prezzi di Database di Azure per PostgreSQL - Hyperscale (Citus) [prezzi.](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)

## <a name="restore"></a>Restore

È possibile ripristinare un Hyperscale (Citus) di server a qualsiasi punto nel tempo negli ultimi 35 giorni.  Il ripristino temporizzato è utile in più scenari, Può accadere, ad esempio, quando un utente elimina accidentalmente i dati o rimuove una tabella o un database importante oppure se un'applicazione sovrascrive accidentalmente dati validi con dati non validi.

> [!IMPORTANT]
> Non Hyperscale (Citus) possibile ripristinare i gruppi di server eliminati. Se si elimina il gruppo di server, tutti i nodi che appartengono al gruppo di server vengono eliminati e non possono essere ripristinati. Per proteggere le risorse del gruppo di server, dopo la distribuzione, da eliminazioni accidentali o modifiche impreviste, gli amministratori possono sfruttare [i blocchi di gestione](../azure-resource-manager/management/lock-resources.md).

Il processo di ripristino crea un nuovo gruppo di server nella stessa area di Azure, nella stessa sottoscrizione e nello stesso gruppo di risorse dell'originale. Il gruppo di server ha la configurazione originale: lo stesso numero di nodi, numero di vCore, dimensioni di archiviazione, ruoli utente, versione di PostgreSQL e versione dell'estensione Citus.

Le impostazioni del firewall e i parametri del server PostgreSQL non vengono mantenuti dal gruppo di server originale, ma vengono reimpostati sui valori predefiniti. Il firewall impedirà tutte le connessioni. Sarà necessario modificare manualmente queste impostazioni dopo il ripristino. In generale, vedere l'elenco delle attività [post-ripristino suggerite.](howto-hyperscale-restore-portal.md#post-restore-tasks)

## <a name="next-steps"></a>Passaggi successivi

* Vedere i passaggi per [ripristinare un gruppo di server](howto-hyperscale-restore-portal.md) nella portale di Azure.
* Informazioni sulle zone [di disponibilità di Azure.](../availability-zones/az-overview.md)
