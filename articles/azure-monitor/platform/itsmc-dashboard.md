---
title: Esaminare gli errori usando il dashboard
description: Questo documento contiene informazioni sugli errori nel dashboard di connettore.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: ebd59e637e498b8088fe9a302b1bb12efdf2c173
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089276"
---
# <a name="investigate-errors-by-using-the-itsmc-dashboard"></a>Esaminare gli errori usando il dashboard di connettore

Questo articolo contiene informazioni sul dashboard di IT Service Management Connector (connettore). Il dashboard consente di esaminare lo stato di connettore.

## <a name="view-the-dashboard"></a>Visualizzare il dashboard

Per aprire il dashboard, seguire questa procedura.

1. Selezionare **tutte le risorse** e quindi trovare **ServiceDesk (*nome dell'area di lavoro*)**.

   ![Screenshot che mostra le risorse nei servizi di Azure.](media/itsmc-definition/create-new-connection-from-resource.png)

1. Nel riquadro sinistro selezionare **origini dati dell'area di lavoro**, quindi selezionare **connessioni ITSM**.

   ![Screenshot che mostra la selezione delle connessioni ITSM nelle origini dati dell'area di lavoro.](media/itsmc-overview/add-new-itsm-connection.png)

1. Nella sezione **Riepilogo** selezionare **Visualizza riepilogo** per visualizzare un grafico di riepilogo.

    ![Screenshot che mostra l'opzione Visualizza riepilogo nella sezione Riepilogo.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

1. Selezionare il grafico nella sezione **Riepilogo** per aprire il dashboard.

    ![Screenshot che mostra la selezione del grafico di riepilogo.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

1. Esaminare il dashboard per lo stato e gli eventuali errori nel connettore.
    ![Screenshot che mostra il dashboard.](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="understand-dashboard-elements"></a>Informazioni sugli elementi del dashboard

Il dashboard contiene informazioni sugli avvisi inviati allo strumento ITSM tramite questo connettore.

Il dashboard è suddiviso in quattro sezioni:

- **Elementi di lavoro creati**: il grafico e la tabella mostrano il numero degli elementi di lavoro per tipo. Per ulteriori informazioni sugli elementi di lavoro, selezionare il grafico o la tabella.
      ![Screenshot che mostra la sezione elementi di lavoro creati.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
- **Computer interessati**: la tabella contiene informazioni dettagliate sugli elementi di configurazione creati dagli elementi di lavoro.
      Per ulteriori informazioni sugli elementi di configurazione, selezionare le righe nelle tabelle.
      La tabella contiene un numero limitato di righe. Per visualizzare l'intero elenco, selezionare **Visualizza tutto**.
      ![Screenshot che mostra la sezione computer interessati.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
- **Stato del connettore**: il grafico e la tabella mostrano informazioni sullo stato del connettore. Per ulteriori informazioni, selezionare il grafico o i messaggi nella tabella. La tabella mostra un numero limitato di righe. Per visualizzare l'intero elenco, selezionare **Visualizza tutto**.
      ![Screenshot che mostra la sezione stato connettore.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
- **Regole di avviso**: in questa sezione vengono fornite informazioni sul numero di regole di avviso rilevate. Per ulteriori informazioni sulle regole rilevate, selezionare le righe nelle tabelle. La tabella contiene un numero limitato di righe. Per visualizzare l'intero elenco, selezionare **Visualizza tutto**.
      ![Screenshot che mostra la sezione delle regole di avviso.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere [errori comuni sullo stato del connettore](itsmc-dashboard-errors.md).
