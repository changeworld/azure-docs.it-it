---
title: Analisi degli errori tramite Dashboard
description: Questo documento contiene informazioni sull'analisi degli errori usando il dashboard
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 9a43318ed935dd1e71b6da75c9f078fbebbd29eb
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599029"
---
# <a name="error-investigation-using-the-dashboard"></a>Analisi degli errori tramite il dashboard

Questa pagina contiene informazioni sul dashboard del connettore ITSM. Questo dashboard consentirà di esaminare lo stato del connettore ITSM.

## <a name="how-to-view-the-dashboard"></a>Come visualizzare il dashboard

Per visualizzare gli errori nel dashboard, attenersi alla procedura seguente:

1. In **tutte le risorse** cercare **ServiceDesk (*nome dell'area di lavoro*)**:

   ![Screenshot che mostra le risorse recenti nel portale di Azure.](media/itsmc-definition/create-new-connection-from-resource.png)

2. In **origini dati dell'area di lavoro** nel riquadro sinistro selezionare **connessioni ITSM**:

   ![Screenshot che mostra la voce di menu connessioni ITSM.](media/itsmc-overview/add-new-itsm-connection.png)

3. In **Riepilogo** nella casella a sinistra **connettore gestione servizi IT** Selezionare **Visualizza riepilogo**:

    ![Screenshot che mostra il riepilogo della visualizzazione.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. In **Summary (riepilogo** ) nella casella a sinistra **Service Management Connector** fare clic sul grafico:

    ![Screenshot che mostra il clic sul grafico.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. Con questo dashboard sarà possibile esaminare lo stato e gli errori nel connettore.
    ![Screenshot che mostra lo stato del connettore.](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="dashboard-elements"></a>Elementi del dashboard

Il dashboard contiene informazioni sugli avvisi che sono stati inviati allo strumento ITSM usando questo connettore.
Il dashboard è suddiviso in quattro parti:

1. Elemento di lavoro creato: il grafico e la tabella riportata di seguito contengono il numero di elementi di lavoro per tipo. Se si fa clic sul grafico o sulla tabella, è possibile visualizzare altri dettagli sugli elementi di lavoro.
    ![Screenshot che mostra l'elemento di lavoro creato.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
2. Computer interessati: le tabelle contengono informazioni dettagliate sugli elementi di configurazione che hanno creato elementi di configurazione.
    Facendo clic sulle righe delle tabelle, è possibile ottenere ulteriori informazioni sugli elementi di configurazione.
    La tabella contiene un numero limitato di righe se si desidera visualizzare tutti gli elenchi su cui è possibile fare clic su "Visualizza tutto".
    ![Screenshot che mostra i computer interessati.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
3. Stato del connettore: il grafico e la tabella riportata di seguito contengono messaggi sullo stato del connettore. Facendo clic sul grafico sulle righe della tabella, è possibile ottenere altre informazioni sui messaggi dello stato del connettore.
    La tabella contiene un numero limitato di righe se si desidera visualizzare tutti gli elenchi su cui è possibile fare clic su "Visualizza tutto".

    È possibile visualizzare i dettagli relativi ai messaggi nella [tabella.](itsmc-dashboard-errors.md)

    ![Screenshot che mostra lo stato del connettore.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
4. Regole di avviso: le tabelle contengono le informazioni sul numero di regole di avviso rilevate.
    Facendo clic sulle righe delle tabelle, è possibile ottenere altre informazioni sulle regole rilevate.
    La tabella contiene un numero limitato di righe se si desidera visualizzare tutti gli elenchi su cui è possibile fare clic su "Visualizza tutto".
    ![Screenshot che mostra le regole di avviso.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)