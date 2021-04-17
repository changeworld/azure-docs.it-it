---
title: Aggiornare il gruppo di server - Hyperscale (Citus) - Database di Azure per PostgreSQL
description: Questo articolo descrive come aggiornare PostgreSQL e Citus in Database di Azure per PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 4/5/2021
ms.openlocfilehash: 161204bf02ac36c1f5a3969cf57c61e98560c9b5
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518899"
---
# <a name="upgrade-hyperscale-citus-server-group"></a>Aggiornare Hyperscale (Citus) gruppo di server

Queste istruzioni descrivono come eseguire l'aggiornamento a una nuova versione principale di PostgreSQL in tutti i nodi del gruppo di server.

## <a name="test-the-upgrade-first"></a>Testare prima l'aggiornamento

L'aggiornamento di PostgreSQL comporta più modifiche di quanto si possa immaginare, perché Hyperscale (Citus) anche le estensioni di [database](concepts-hyperscale-extensions.md), inclusa l'estensione Citus.
È consigliabile testare l'applicazione con la nuova versione PostgreSQL e Citus prima di aggiornare l'ambiente di produzione.

Un modo pratico per testare è creare una copia del gruppo di server usando il ripristino [temporato.](concepts-hyperscale-backup.md#restore) Aggiornare la copia e testare l'applicazione. Dopo aver verificato che tutto funzioni correttamente, aggiornare il gruppo di server originale.

## <a name="upgrade-a-server-group-in-the-azure-portal"></a>Aggiornare un gruppo di server nel portale di Azure

1. Nella sezione **Panoramica** di un gruppo Hyperscale (Citus) server selezionare il **pulsante** Aggiorna.
1. Verrà visualizzata una finestra di dialogo che mostra la versione corrente di PostgreSQL e Citus.
   Scegliere una nuova versione di PostgreSQL **nell'elenco Aggiorna a.**
1. Verificare che il valore nella **versione di Citus dopo l'aggiornamento** sia quello previsto.
   Questo valore cambia in base alla versione di PostgreSQL selezionata.
1. Selezionare il **pulsante** Aggiorna per continuare.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle [versioni di PostgreSQL supportate.](concepts-hyperscale-versions.md)
* Vedere [quali estensioni sono](concepts-hyperscale-extensions.md) in pacchetto con ogni versione di PostgreSQL in un gruppo Hyperscale (Citus) server.
