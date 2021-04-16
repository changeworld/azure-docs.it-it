---
title: Eliminare i connettori ITSM inutilizzati
description: Questo articolo illustra come eliminare i connettori ITSM e i gruppi di azioni associati.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 3dc84ea6def9b762527226dbeb3e2eaab78ec200
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387939"
---
# <a name="delete-unused-itsm-connectors"></a>Eliminare i connettori ITSM inutilizzati

Il processo di eliminazione dei connettori Gestione dei servizi IT (ITSM) inutilizzati è in due fasi. Si eliminano tutte le azioni associate a un connettore ITSM e quindi si elimina il connettore stesso. Le azioni vengono prima eliminate perché le azioni senza un connettore potrebbero causare errori nella sottoscrizione.

## <a name="delete-associated-actions"></a>Eliminare le azioni associate

1. Nel portale di Azure selezionare **Monitoraggio**.
  
    ![Screenshot della selezione di Monitoraggio.](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. Selezionare **Avvisi**.
   
    ![Screenshot della selezione avvisi.](media/itsmc-connector-deletion/itsmc-alert-selection.png)

3. Selezionare **Gestisci azioni**.
   
    ![Screenshot della selezione Gestisci azioni.](media/itsmc-connector-deletion/itsmc-actions-selection.png)

4. Selezionare un gruppo di azioni associato al connettore ITSM da eliminare. Questo articolo usa l'esempio di un connettore Cherwell.
   
    ![Screenshot delle azioni associate al connettore Cherwell.](media/itsmc-connector-deletion/itsmc-actions-screen.png)

5. Esaminare le informazioni e quindi selezionare **Elimina gruppo di azioni**.

    ![Screenshot delle informazioni sul gruppo di azioni e del pulsante per l'eliminazione del gruppo.](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="delete-the-connector"></a>Eliminare il connettore

1. Nella barra di ricerca cercare **servicedesk**. Selezionare quindi **ServiceDesk** nell'elenco delle risorse.

    ![Screenshot della ricerca e della selezione di ServiceDesk.](media/itsmc-connector-deletion/itsmc-connector-selection.png)

2. Selezionare **ItsM Connections (Connessioni ITSM)** e quindi il connettore Cherwell.

    ![Screenshot del connettore Cherwell I T S M.](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)

3. Selezionare **Elimina**.

    ![Screenshot del pulsante elimina per il connettore I T S M.](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>Passaggi successivi

* [Risoluzione dei problemi in un connettore ITSM](./itsmc-resync-servicenow.md)
