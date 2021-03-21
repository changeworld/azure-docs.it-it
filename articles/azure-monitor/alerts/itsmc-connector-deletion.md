---
title: Eliminazione del connettore ITSM e dell'azione associata
description: Questo articolo fornisce una spiegazione su come eliminare il connettore ITSM e i gruppi di azioni associati.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: e73510b6c61c58f6f0b2b8067a240214ee35a46c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102036487"
---
# <a name="deletion-of-unused-itsm-connectors"></a>Eliminazione di connettori ITSM inutilizzati

Il processo di eliminazione del connettore non usato contiene 2 fasi:

1. Eliminazione delle azioni associate: tutte le azioni associate al connettore ITSM devono essere eliminate. Questa operazione deve essere eseguita in modo da non avere azioni senza connettore che potrebbero causare errori nella sottoscrizione.

2. Eliminazione del connettore ITSM non usato.

## <a name="deletion-of-the-associated-actions"></a>Eliminazione delle azioni associate

1. Per trovare il gruppo di azione, è necessario passare allo screenshot "monitoraggio"  ![ della selezione del monitoraggio.](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. Selezionare la schermata "avvisi"  ![ della selezione degli avvisi.](media/itsmc-connector-deletion/itsmc-alert-selection.png)
3. Selezionare "Gestisci azioni"  ![ screenshot della selezione Gestisci azioni.](media/itsmc-connector-deletion/itsmc-actions-selection.png)
4. Selezionare tutti i connettori ITSM connessi allo  ![ screenshot Cherwell dei connettori ITSM connessi a Cherwell.](media/itsmc-connector-deletion/itsmc-actions-screen.png)
5. Elimina lo  ![ screenshot del gruppo di azioni dell'eliminazione del gruppo di azioni.](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="deletion-of-the-unused-itsm-connector"></a>Eliminazione del connettore ITSM non usato

1. È consigliabile cercare e selezionare "ServiceDesk" nella barra di ricerca in alto  ![ screenshot di Search e selezionare "ServiceDesk" la.](media/itsmc-connector-deletion/itsmc-connector-selection.png)
2. Selezionare "connessioni ITSM" e selezionare lo screenshot del connettore Cherwell  ![ dei connettori CHERWELL ITSM.](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)
3. Selezionare "Elimina"  ![ screenshot dell'eliminazione del connettore ITSM.](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi di Connettore di Gestione dei servizi IT](./itsmc-resync-servicenow.md)
