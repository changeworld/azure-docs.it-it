---
title: Risolvere i problemi di Connettore di Gestione dei servizi IT
description: Risoluzione dei problemi in IT Service Management Connector
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: e43c5fb36c5395e12fd0b9c2c67b787a1137f5d0
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761991"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>Risolvere i problemi di Connettore di Gestione dei servizi IT

Questo articolo illustra i problemi comuni in ITSM Connector e come risolverli.

Gli avvisi di monitoraggio di Azure notificano in modo proattivo quando vengono rilevate condizioni importanti nei dati di monitoraggio. Consentono di identificare e risolvere i problemi prima che si palesino agli utenti. Per ulteriori informazioni sugli avvisi, vedere Panoramica degli avvisi in Microsoft Azure.
Il cliente può selezionare il modo in cui desidera ricevere una notifica per l'avviso indipendentemente dal fatto che si tratti di posta elettronica, SMS, webhook o anche di automatizzare una soluzione. Un'altra opzione da notificare consiste nell'usare ITSM.
ITSM offre la possibilità di inviare gli avvisi a un sistema di ticket esterno, ad esempio ServiceNow.

## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Visualizzare e analizzare i dati degli eventi imprevisti e delle richieste di modifica

A seconda della configurazione quando si configura una connessione, connettore è in grado di sincronizzare fino a 120 giorni di dati relativi a eventi imprevisti e richieste di modifica. Lo schema dei record di log per questi dati è disponibile nella [sezione informazioni aggiuntive](./itsmc-synced-data.md) di questo articolo.

È possibile visualizzare i dati degli eventi imprevisti e delle richieste di modifica usando il dashboard di connettore:

![Screenshot che mostra il dashboard di connettore.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Il dashboard fornisce inoltre informazioni sullo stato del connettore, che è possibile utilizzare come punto di partenza per analizzare i problemi relativi alle connessioni.

Per ottenere altre informazioni sull'analisi del dashboard, vedere [analisi degli errori usando il dashboard](./itsmc-dashboard.md).

### <a name="service-map"></a>Mappa del servizio

È inoltre possibile visualizzare gli eventi imprevisti sincronizzati con i computer interessati in Mapping dei servizi.

Elenco dei servizi individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux ed esegue la mappatura della comunicazione fra i servizi. Consente di visualizzare i server come si pensa a questi ultimi: come sistemi interconnessi che forniscono servizi critici. Mapping dei servizi Mostra le connessioni tra server, processi e porte in qualsiasi architettura connessa a TCP. A parte l'installazione di un agente, non è necessaria alcuna configurazione. Per ulteriori informazioni, vedere [utilizzo di mapping dei servizi](../insights/service-map.md).

Se si usa Mapping dei servizi, è possibile visualizzare gli elementi del Service Desk creati nelle soluzioni ITSM, come illustrato di seguito:

![Screenshot che mostra la schermata Log Analytics.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="common-symptoms---how-should-it-be-resolved"></a>Sintomi comuni: come risolverlo?

L'elenco seguente contiene i sintomi comuni e il modo in cui risolverlo:

* **Sintomo**: se una connessione non riesce a connettersi al sistema ITSM e si verifica un **errore durante il salvataggio** del messaggio di connessione.

    **Motivo**: la ragione può essere una delle opzioni seguenti:
    * Credenziali non corrette
     * Privilegi insufficienti
     * L'app Web deve essere distribuita correttamente

    **Risoluzione**:
    * Per ServiceNow, Cherwell e Provance Connections:
        * Assicurarsi di aver immesso correttamente il nome utente, la password, l'ID client e il segreto client per ognuna delle connessioni.  
        * Per ServiceNow: assicurarsi di disporre di privilegi sufficienti nel prodotto ITSM corrispondente per eseguire la connessione come [specificato](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role).
  * Per le connessioni Service Manager:  
      * Verificare che l'app Web sia stata distribuita correttamente e che la connessione ibrida sia stata creata. Per verificare che la connessione venga stabilita correttamente con il computer Service Manager locale, passare all'URL dell'app Web come descritto nella documentazione per la creazione della [connessione ibrida](./itsmc-connections-scsm.md#configure-the-hybrid-connection).  
* **Sintomo**: vengono creati elementi di lavoro duplicati

    **Motivo**: la ragione può essere una delle due opzioni seguenti:
    * Sono state definite più azioni ITSM per l'avviso.
    * Avviso risolto.

    **Soluzione**: possono essere disponibili due soluzioni:
    * Assicurarsi di disporre di un singolo gruppo di azioni ITSM per ogni avviso.
    * Il connettore ITSM non supporta l'aggiornamento dello stato degli elementi di lavoro corrispondenti quando viene risolto un avviso. Viene creato un nuovo elemento di lavoro risolto.
* **Sintomo**: gli elementi di lavoro non sono stati creati

    **Causa**: possono essere presenti due motivi per il sintomo:
    * Modifica del codice sul lato ServiceNow
    * Configurazione errata delle autorizzazioni
    * I limiti di velocità ServiceNow sono troppo alti/bassi
    * Il token di aggiornamento è scaduto
    * Il connettore ITSM è stato eliminato

    **Soluzione**: è possibile controllare il [Dashboard](itsmc-dashboard.md) ed esaminare gli errori nella sezione stato connettore. Esaminare gli [errori comuni](itsmc-dashboard-errors.md) e scoprire come risolvere l'errore.

* **Sintomo**: non è possibile creare l'azione ITSM per il gruppo di azione

    **Motivo**: il connettore ITSM appena creato ha ancora completato la sincronizzazione iniziale.

    **Soluzione**: è possibile esaminare gli [errori comuni dell'interfaccia utente](itsmc-dashboard-errors.md#ui-common-errors) e scoprire come risolvere l'errore.