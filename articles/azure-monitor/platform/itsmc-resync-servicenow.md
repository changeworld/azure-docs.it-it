---
title: Come correggere manualmente i problemi di sincronizzazione ServiceNow
description: Reimpostare la connessione a ServiceNow in modo che gli avvisi nel Microsoft Azure possano chiamare nuovamente ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 2f0889732ac756e16465659ee2e5b52335d1148f
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539268"
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

## <a name="troubleshoot-itsm-connections"></a>Risolvere i problemi delle connessioni di Gestione dei servizi IT

- Se una connessione non riesce a connettersi al sistema ITSM e si verifica un **errore durante il salvataggio** del messaggio di connessione, seguire questa procedura:
   - Per ServiceNow, Cherwell e Provance Connections:  
     - Assicurarsi di aver immesso correttamente il nome utente, la password, l'ID client e il segreto client per ognuna delle connessioni.  
     - Assicurarsi di disporre di privilegi sufficienti nel prodotto ITSM corrispondente per effettuare la connessione.  
   - Per le connessioni Service Manager:  
     - Verificare che l'app Web sia stata distribuita correttamente e che la connessione ibrida sia stata creata. Per verificare che la connessione venga stabilita correttamente con il computer Service Manager locale, passare all'URL dell'app Web come descritto nella documentazione per la creazione della [connessione ibrida](./itsmc-connections-scsm.md#configure-the-hybrid-connection).  

- Se i dati provenienti da ServiceNow non vengono sincronizzati con Log Analytics, assicurarsi che l'istanza di ServiceNow non venga sospesa. Le istanze di ServiceNow dev a volte passano a dormire quando sono inattive per molto tempo. Se ciò non accade, segnalare il problema.
- Se Log Analytics gli avvisi vengono attivati, ma gli elementi di lavoro non vengono creati nel prodotto ITSM, se gli elementi di configurazione non vengono creati/collegati agli elementi di lavoro o per altre informazioni, vedere le risorse seguenti:
   -  CONNETTORE: la soluzione Mostra un riepilogo delle connessioni, degli elementi di lavoro, dei computer e molto altro. Selezionare il riquadro con l'etichetta **stato connettore** . In questo modo, viene eseguita la **Ricerca log** con la query pertinente. `LogType_S`Per ulteriori informazioni, esaminare i record del log con un di `ERROR` .
   - Pagina **Ricerca log** : consente di visualizzare gli errori e le informazioni correlate direttamente usando la query `*ServiceDeskLog_CL*` .

### <a name="troubleshoot-service-manager-web-app-deployment"></a>Risolvere i problemi di Service Manager distribuzione di app Web

-   In caso di problemi con la distribuzione dell'app Web, assicurarsi di disporre delle autorizzazioni per creare/distribuire le risorse nella sottoscrizione.
-   Se si ottiene un **riferimento a un oggetto non impostato sull'istanza di un errore di oggetto** quando si esegue lo [script](itsmc-service-manager-script.md), assicurarsi di aver immesso valori validi nella sezione **Configurazione utente** .
-   Se non è possibile creare lo spazio dei nomi di inoltro del bus di servizio, verificare che il provider di risorse richiesto sia registrato nella sottoscrizione. Se non è registrato, creare manualmente lo spazio dei nomi di inoltro del bus di servizio dal portale di Azure. È anche possibile crearla quando si [Crea la connessione ibrida](./itsmc-connections-scsm.md#configure-the-hybrid-connection) nel portale di Azure.

### <a name="how-to-manually-fix-sync-problems"></a>Come correggere manualmente i problemi di sincronizzazione

Monitoraggio di Azure può connettersi a provider di gestione dei servizi IT di terze parti (ITSM). ServiceNow è uno di questi provider.

Per motivi di sicurezza, potrebbe essere necessario aggiornare il token di autenticazione usato per la connessione con ServiceNow.
Usare il processo di sincronizzazione seguente per riattivare la connessione e aggiornare il token:


1. Cercare la soluzione nel banner di ricerca superiore, quindi selezionare le soluzioni pertinenti

    ![Screenshot che mostra il banner di ricerca superiore e la posizione in cui selezionare le soluzioni pertinenti.](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. Nella schermata della soluzione scegliere "Seleziona tutto" nel filtro della sottoscrizione e quindi filtrare in base a "ServiceDesk".

    ![Screenshot che mostra dove scegliere Seleziona tutto e dove filtrare in base a ServiceDesk.](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Selezionare la soluzione della connessione ITSM.
1. Selezionare connessione ITSM nel banner a sinistra.

    ![Screenshot che mostra dove selezionare le connessioni ITSM.](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Selezionare ogni connettore dall'elenco. 
    1. Per configurarlo, fare clic sul nome del connettore
    1. Elimina tutti i connettori non più in uso

    1. Aggiornare i campi in base a [queste definizioni](./itsmc-connections.md) in base al tipo di partner

    1. Fare clic su Sincronizza

       ![Schermata che evidenzia il pulsante Sincronizza.](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Fare clic su Salva

        ![Nuova connessione](media/itsmc-resync-servicenow/save-8bit.png)

f.    Esaminare le notifiche per verificare se il processo è stato avviato.
