---
title: Aggiornamento del gruppo di server-iperscala (CITUS)-database di Azure per PostgreSQL
description: Questo articolo descrive come è possibile aggiornare PostgreSQL e CITUS in database di Azure per PostgreSQL-iperscalabilità (CITUS).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 4/5/2021
ms.openlocfilehash: 3758e2d458e1a6bd052ac746ac361de033d508e9
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024026"
---
# <a name="upgrade-hyperscale-citus-server-group"></a>Aggiornamento gruppo di server iperscalare (CITUS)

Queste istruzioni descrivono come eseguire l'aggiornamento a una nuova versione principale di PostgreSQL in tutti i nodi del gruppo di server.

## <a name="test-the-upgrade-first"></a>Testare prima l'aggiornamento

L'aggiornamento di PostgreSQL causa un numero maggiore di modifiche rispetto a quanto si può immaginare, perché l'aggiornamento iperscalabile (CITUS) aggiornerà anche le [estensioni del database](concepts-hyperscale-extensions.md), inclusa l'estensione CITUS.
È consigliabile testare l'applicazione con la nuova versione di PostgreSQL e CITUS prima di aggiornare l'ambiente di produzione.

Un modo pratico per eseguire il test consiste nell'eseguire una copia del gruppo di server usando il [ripristino temporizzato](concepts-hyperscale-backup.md#point-in-time-restore-pitr). Aggiornare la copia e testare l'applicazione. Dopo aver verificato che tutto funzioni correttamente, aggiornare il gruppo di server originale.

## <a name="upgrade-a-server-group-in-the-azure-portal"></a>Aggiornare un gruppo di server nel portale di Azure

1. Nella sezione **Panoramica** di un gruppo di server con iperscalabilità (CITUS) selezionare il pulsante **Aggiorna** .
1. Viene visualizzata una finestra di dialogo che mostra la versione corrente di PostgreSQL e CITUS.
   Scegliere una nuova versione di PostgreSQL nell'elenco **Aggiorna a** .
1. Verificare il valore in **CITUS Version dopo l'aggiornamento** è quello previsto.
   Questo valore viene modificato in base alla versione di PostgreSQL selezionata.
1. Per continuare, selezionare il pulsante **Aggiorna** .

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle [versioni supportate di PostgreSQL](concepts-hyperscale-versions.md).
* Vedere [quali estensioni](concepts-hyperscale-extensions.md) vengono assemblate con ogni versione di PostgreSQL in un gruppo di server con iperscalabilità (CITUS).
