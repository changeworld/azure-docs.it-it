---
title: Errori di stato del connettore nel dashboard di connettore
description: Informazioni sugli errori comuni presenti nel dashboard di IT Service Management Connector.
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: 727e744c59d0a8d90cf320e1ee2e2a17e10ff847
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471534"
---
# <a name="connector-status-errors-in-the-itsmc-dashboard"></a>Errori di stato del connettore nel dashboard di connettore

Il dashboard IT Service Management Connector (connettore) presenta errori che consentono di risolvere i problemi nel connettore.

Le sezioni seguenti descrivono gli errori comuni che vengono visualizzati nella sezione stato connettore del dashboard e come è possibile risolverli.

## <a name="unexpected-response"></a>Risposta imprevista

**Errore**: "risposta imprevista da ServiceNow insieme al codice di stato di esito positivo. Risposta: {"import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "risultato": [{"transform_map": "evento imprevisto di OMS", "tabella": "evento imprevisto", "stato": "errore", "error_message": "{record di destinazione non trovato | Tabella non valida | Tabella di staging non valida "}"

**Motivo**: ServiceNow restituisce questo errore quando:

* Uno script personalizzato distribuito in un'istanza di ServiceNow fa sì che gli eventi imprevisti vengano ignorati.
* Il codice "app di OMS Integrator" è stato modificato sul lato ServiceNow, ad esempio tramite lo `onBefore` script.

**Soluzione**: disabilitare tutti gli script personalizzati o le modifiche del codice.

## <a name="exception-update-failure"></a>Errore di aggiornamento dell'eccezione

**Errore**: "{" Error ": {" message ":" operazione non riuscita "," Detail ":" Impossibile aggiornare l'eccezione ACL a causa di vincoli di sicurezza "}"

**Motivo**: le autorizzazioni di ServiceNow non sono configurate correttamente.

**Soluzione**: verificare che tutti i ruoli siano assegnati correttamente come [specificato](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role).

## <a name="problem-sending-a-request"></a>Problema durante l'invio di una richiesta

**Errore**: "si è verificato un errore durante l'invio della richiesta".

**Motivo**: un'istanza di ServiceNow non è disponibile.

**Soluzione**: verificare l'istanza in ServiceNow. Potrebbe essere eliminato o non disponibile.

## <a name="servicenow-rate-problem"></a>Problema di velocità ServiceNow

**Errore**: "ServiceDeskHttpBadRequestException: StatusCode = 429"

**Motivo**: i limiti di velocità ServiceNow sono troppo alti o troppo bassi.

**Soluzione**: aumentare o annullare i limiti di velocità nell'istanza di ServiceNow, come illustrato nella [documentazione di ServiceNow](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html).

## <a name="invalid-refresh-token"></a>Token di aggiornamento non valido

**Errore**: 
  * "AccessToken e RefreshToken non sono validi. L'utente deve eseguire nuovamente l'autenticazione ".
  * "Impossibile sincronizzare la configurazione dei modelli per eventi, avvisi, eventi imprevisti. Per ulteriori informazioni, vedere il messaggio di eccezione ".

**Motivo**: un token di aggiornamento è scaduto.

**Soluzione**: sincronizzare connettore per generare un nuovo token di aggiornamento, come illustrato in [come correggere manualmente i problemi di sincronizzazione](./itsmc-resync-servicenow.md).

## <a name="missing-connector"></a>Connettore mancante

**Errore**: "Impossibile creare o aggiornare l'elemento di lavoro per l'avviso {alertName}. ITSM Connector {connectionIdentifier} non esiste o è stato eliminato ".

**Motivo**: connettore è stato eliminato.

**Soluzione**: connettore è stato eliminato, ma sono ancora associati gruppi di azioni ITSM (it Service Management) definiti. Per risolvere il problema sono disponibili tre opzioni:

* Trovare e disabilitare o eliminare tali gruppi di azioni.
* [Riconfigurare i gruppi di azioni per l'](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) utilizzo di un'istanza di connettore esistente.
* [Creare una nuova istanza di connettore](./itsmc-definition.md#create-an-itsm-connection) e [riconfigurare i gruppi di azioni per usarla](itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="lack-of-connection-details"></a>Mancanza di dettagli della connessione

**Errore**: "si è verificato un problema. Non è stato possibile ottenere i dettagli della connessione. " Questo errore viene visualizzato quando si definisce un gruppo di azioni ITSM.

**Motivo**: tale errore viene visualizzato in una delle situazioni seguenti:

* Per completare la sincronizzazione iniziale, è ancora stata creata un'istanza di ITSM Connector appena creata.
* Il connettore non è stato definito correttamente.

**Risoluzione**: 

* Quando viene creata una nuova istanza di connettore, viene avviata la sincronizzazione delle informazioni dal sistema ITSM, ad esempio i modelli di elemento di lavoro e gli elementi di lavoro. [Sincronizzare connettore per generare un nuovo token di aggiornamento](./itsmc-resync-servicenow.md).
* [Esaminare i dettagli della connessione in connettore](./itsmc-connections-servicenow.md#create-a-connection) e verificare che connettore sia in grado di eseguire correttamente la [sincronizzazione](./itsmc-resync-servicenow.md).


## <a name="ip-restrictions"></a>Restrizioni IP
**Errore**: "Impossibile aggiungere la connessione ITSM denominata" xxx "a causa di una richiesta non valida. Errore: richiesta non valida. Parametri non validi specificati per la connessione. Eccezione http: codice di stato non consentito. "

**Motivo**: l'indirizzo IP dell'applicazione ITSM non consente le connessioni ITSM da partner ITSM Tools.

**Soluzione**: per elencare gli indirizzi IP di ITSM per consentire le connessioni ITSM da partner ITSM Tools, è consigliabile elencare l'intero intervallo di indirizzi IP pubblici dell'area di Azure in cui appartiene l'area di lavoro di LogAnalytics. [Dettagli qui](https://www.microsoft.com/download/details.aspx?id=56519) Per le aree EUS/UEO/EUS2/WUS2/Stati Uniti centro-meridionali, il cliente può elencare solo i tag di rete ActionGroup.
