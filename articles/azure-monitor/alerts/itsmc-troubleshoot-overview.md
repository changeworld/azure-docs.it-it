---
title: Risolvere i problemi in connettore
description: Informazioni su come risolvere i problemi comuni in IT Service Management Connector.
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 7094bf07453c06831fecfa2056480bf498b26a72
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041621"
---
# <a name="troubleshoot-problems-in-it-service-management-connector"></a>Risolvere i problemi in IT Service Management Connector

Questo articolo illustra i problemi comuni in IT Service Management Connector (connettore) e come risolverli.

Monitoraggio di Azure notifica in modo proattivo gli avvisi quando rileva condizioni importanti nei dati di monitoraggio. Questi avvisi consentono di identificare e risolvere i problemi prima che gli utenti del sistema li notino.

È possibile selezionare il modo in cui si desidera ricevere gli avvisi. È possibile scegliere posta elettronica, SMS o webhook o persino automatizzare una soluzione. 

In alternativa, è possibile ricevere una notifica tramite connettore. CONNETTORE offre la possibilità di inviare avvisi a un sistema di ticket esterno, ad esempio ServiceNow.

## <a name="use-the-dashboard-to-analyze-incident-and-change-request-data"></a>Usare il dashboard per analizzare i dati degli eventi imprevisti e delle richieste di modifica

A seconda della configurazione quando si configura una connessione, connettore è in grado di sincronizzare fino a 120 giorni di dati relativi a eventi imprevisti e richieste di modifica. Per ottenere lo schema dei record di log per questi dati, vedere l'articolo relativo al [prodotto sincronizzato dall'ITSM](./itsmc-synced-data.md) .

È possibile visualizzare i dati degli eventi imprevisti e delle richieste di modifica usando il dashboard di connettore:

![Screenshot che mostra il dashboard di connettore.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Il dashboard fornisce anche informazioni sullo stato del connettore. È possibile utilizzare queste informazioni come punto di partenza per analizzare i problemi relativi alle connessioni. Per altre informazioni, vedere [analisi degli errori usando il dashboard](./itsmc-dashboard.md).

## <a name="use-service-map-to-visualize-incidents"></a>Usare Mapping dei servizi per visualizzare gli eventi imprevisti

È inoltre possibile visualizzare gli eventi imprevisti sincronizzati con i computer interessati in Mapping dei servizi.

Elenco dei servizi individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux ed esegue la mappatura della comunicazione fra i servizi. Consente di visualizzare i server come si pensa a questi ultimi: come sistemi interconnessi che forniscono servizi critici. 

Mapping dei servizi Mostra le connessioni tra server, processi e porte in qualsiasi architettura connessa a TCP. A parte l'installazione di un agente, non è necessaria alcuna configurazione. Per ulteriori informazioni, vedere [utilizzo di mapping dei servizi](../vm/service-map.md).

Se si usa Mapping dei servizi, è possibile visualizzare gli elementi del Service Desk creati nelle soluzioni ITSM (IT Service Management), come illustrato nell'esempio seguente:

![Screenshot che mostra la schermata Log Analytics.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="resolve-problems"></a>Risolvere i problemi

Le sezioni seguenti identificano i sintomi comuni, le possibili cause e le risoluzioni. 

### <a name="a-connection-to-the-itsm-system-fails-and-you-get-an-error-in-saving-connection-message"></a>Una connessione al sistema ITSM non riesce e viene ricevuto un messaggio di errore durante il salvataggio della connessione

**Motivo**: la ragione può essere una delle seguenti opzioni:

* Le credenziali non sono corrette.
* I privilegi sono insufficienti.
* Per le connessioni Service Manager: l'app Web è stata distribuita in modo errato.

**Risoluzione**:

* Per ServiceNow, Cherwell e Provance Connections:
  * Assicurarsi di aver immesso correttamente il nome utente, la password, l'ID client e il segreto client per ognuna delle connessioni.  
  * Per ServiceNow, assicurarsi di disporre di [privilegi sufficienti](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role) nel prodotto ITSM corrispondente.

* Per le connessioni Service Manager:  
  * Verificare che l'app Web sia stata distribuita correttamente e che la connessione ibrida sia stata creata. Per verificare che la connessione venga stabilita correttamente con il computer Service Manager locale, passare all'URL dell'app Web come descritto nella [documentazione per la creazione di una connessione ibrida](./itsmc-connections-scsm.md#configure-the-hybrid-connection).  

### <a name="duplicate-work-items-are-created"></a>Vengono creati elementi di lavoro duplicati

**Motivo**: la ragione può essere una delle due opzioni seguenti:

* Per l'avviso è stata definita più di un'azione ITSM.
* L'avviso è stato risolto.

**Soluzione**: possono essere disponibili due soluzioni:

* Assicurarsi di disporre di un singolo gruppo di azioni ITSM per ogni avviso.
* CONNETTORE non supporta gli aggiornamenti di stato degli elementi di lavoro corrispondenti quando viene risolto un avviso. Creare un nuovo elemento di lavoro risolto.

### <a name="work-items-are-not-created"></a>Gli elementi di lavoro non vengono creati

**Causa**: possono esserci diversi motivi per questo sintomo:

* Il codice è stato modificato sul lato ServiceNow.
* Le autorizzazioni non sono configurate correttamente.
* I limiti di velocità ServiceNow sono troppo alti o troppo bassi.
* Un token di aggiornamento è scaduto.
* CONNETTORE è stato eliminato.

**Soluzione**: controllare il [Dashboard](itsmc-dashboard.md) ed esaminare gli errori nella sezione per lo stato del connettore. Esaminare quindi gli [errori comuni e le relative soluzioni](itsmc-dashboard-errors.md).

### <a name="you-cant-create-an-itsm-action-for-an-action-group"></a>Non è possibile creare un'azione ITSM per un gruppo di azioni

**Motivo**: una nuova istanza di connettore creata ha ancora completato la sincronizzazione iniziale.

**Soluzione**: esaminare gli [errori comuni e le relative soluzioni](itsmc-dashboard-errors.md).

### <a name="sync-connection"></a>Connessione di sincronizzazione 

**Causa**: possono esserci diversi motivi per questo sintomo:

* I modelli non vengono visualizzati come parte della definizione dell'azione.
* Incedents/Events non vengono creati in ServiceNow.

**Soluzione**: [sincronizzare il connettore](itsmc-resync-servicenow.md).
