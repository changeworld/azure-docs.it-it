---
title: Esaminare gli errori usando il dashboard di connettore
description: Informazioni su come usare il dashboard IT Service Management Connector per esaminare gli errori.
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 5e1acf422abf487edda3e871fa99d07212c59b3a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039530"
---
# <a name="investigate-errors-by-using-the-itsmc-dashboard"></a>Esaminare gli errori usando il dashboard di connettore

Questo articolo contiene informazioni sul dashboard di IT Service Management Connector (connettore). Il dashboard consente di esaminare lo stato del connettore.

## <a name="view-errors"></a>Visualizza errori

Per visualizzare gli errori nel Dashboard:

1. Selezionare **tutte le risorse** e quindi trovare **ServiceDesk (*nome dell'area di lavoro*)**.

   ![Screenshot che mostra le risorse nei servizi di Azure.](media/itsmc-definition/create-new-connection-from-resource.png)

2. In **origini dati dell'area di lavoro** nel riquadro sinistro selezionare **connessioni ITSM**:

   ![Screenshot che mostra la selezione delle connessioni ITSM nelle origini dati dell'area di lavoro.](media/itsmc-overview/add-new-itsm-connection.png)

3. In **Riepilogo**, nell'area **IT Service Management Connector** Selezionare **Visualizza riepilogo**:

   ![Screenshot che mostra il pulsante Visualizza riepilogo.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. Quando un grafico viene visualizzato nell'area **IT Service Management Connector** , selezionarlo:

   ![Screenshot che mostra la selezione di un grafico.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. Verrà visualizzato il dashboard. Usarlo per esaminare lo stato e gli errori nel connettore.
   
   ![Screenshot che mostra lo stato del connettore nel dashboard.](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="understand-dashboard-elements"></a>Informazioni sugli elementi del dashboard

Il dashboard contiene informazioni sugli avvisi inviati allo strumento ITSM tramite questo connettore. Il dashboard è suddiviso in quattro parti.

### <a name="created-work-items"></a>Elementi di lavoro creati 

Nell'area **elementi di lavoro creati** il grafico e la tabella sottostante contengono il numero di elementi di lavoro per tipo. Se si seleziona il grafico o la tabella, è possibile visualizzare altri dettagli sugli elementi di lavoro.

![Screenshot che mostra un elemento di lavoro creato.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)

### <a name="affected-computers"></a>Computer interessati 

Nell'area **computer interessati** la tabella elenca i computer e i relativi elementi di lavoro associati. Selezionando le righe nelle tabelle, è possibile ottenere ulteriori informazioni sui computer.

La tabella contiene un numero limitato di righe. Se si desidera visualizzare tutte le righe, selezionare **Visualizza tutto**.

![Screenshot che mostra i computer interessati.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)

### <a name="connector-status"></a>Stato connettore 

Nell'area **stato connettore** il grafico e la tabella sottostante contengono messaggi sullo stato del connettore. Selezionando il grafico o le righe della tabella, è possibile ottenere altre informazioni sui messaggi.

La tabella contiene un numero limitato di righe. Se si desidera visualizzare tutte le righe, selezionare **Visualizza tutto**.

Per ulteriori informazioni sui messaggi nella tabella, vedere [questo articolo](itsmc-dashboard-errors.md).

![Screenshot che mostra lo stato del connettore.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)

### <a name="alert-rules"></a>Regole di avviso 

Nell'area **regole di avviso** la tabella contiene informazioni sul numero di regole di avviso rilevate. Selezionando le righe nella tabella, è possibile ottenere altri dettagli sulle regole rilevate.
    
La tabella contiene un numero limitato di righe. Se si desidera visualizzare tutte le righe, selezionare **Visualizza tutto**.

![Screenshot che mostra le regole di avviso.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)
