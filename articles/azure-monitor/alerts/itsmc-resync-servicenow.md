---
title: Come correggere manualmente i problemi di sincronizzazione ServiceNow
description: Reimpostare la connessione a ServiceNow in modo che gli avvisi nel Microsoft Azure possano chiamare nuovamente ServiceNow
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/17/2021
ms.openlocfilehash: 664f1522775d96b813b7cd99bdffdb26630497f0
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037439"
---
# <a name="how-to-manually-fix-sync-problems"></a>Come correggere manualmente i problemi di sincronizzazione

Monitoraggio di Azure può connettersi a provider di gestione dei servizi IT di terze parti (ITSM). ServiceNow è uno di questi provider.

Per motivi di sicurezza, potrebbe essere necessario aggiornare il token di autenticazione usato per la connessione con ServiceNow.
Usare il processo di sincronizzazione seguente per riattivare la connessione e aggiornare il token:

1. Cercare la soluzione nel banner di ricerca superiore, quindi selezionare le soluzioni pertinenti

    ![Screenshot che mostra il banner di ricerca superiore e la posizione in cui selezionare le soluzioni pertinenti.](media/itsmc-resync-servicenow/solution-search-8-bit.png)

1. Nella schermata della soluzione scegliere "Seleziona tutto" nel filtro della sottoscrizione e quindi filtrare in base a "ServiceDesk".

    ![Screenshot che mostra dove scegliere Seleziona tutto e dove filtrare in base a ServiceDesk.](media/itsmc-resync-servicenow/solutions-list-8-bit.png)

1. Selezionare la soluzione della connessione ITSM.
1. Selezionare connessione ITSM nel banner a sinistra.

    ![Screenshot che mostra dove selezionare le connessioni ITSM.](media/itsmc-resync-servicenow/itsm-connector-8-bit.png)

1. Selezionare ogni connettore dall'elenco. 
    1. Per configurarlo, fare clic sul nome del connettore
    1. Elimina tutti i connettori non più in uso

    1. Aggiornare i campi in base a [queste definizioni](./itsmc-connections.md) in base al tipo di partner

    1. Fare clic su Sincronizza

       ![Schermata che evidenzia il pulsante Sincronizza.](media/itsmc-resync-servicenow/resync-8-bit-2.png)

    1. Fare clic su Salva

        ![Nuova connessione](media/itsmc-resync-servicenow/save-8-bit.png)

f.    Esaminare le notifiche per verificare se il processo è stato avviato.
