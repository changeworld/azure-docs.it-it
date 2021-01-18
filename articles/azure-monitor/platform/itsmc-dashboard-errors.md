---
title: Errori comuni
description: Questo documento contiene informazioni sugli errori comuni che esistono nel dashboard
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: 5e12ca3bf626ae212f44fe0378ccb6649738753c
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562856"
---
# <a name="errors-in-the-connector-status"></a>Errori nello stato del connettore

Nell'elenco stato connettore è possibile trovare errori che consentono di correggere il connettore ITSM.

## <a name="status-common-errors"></a>Errori comuni di stato

in questa sezione è possibile trovare l'errore comune che è possibile trovare nell'elenco di stato e come risolverlo:

*  **Errore**: "risposta imprevista da ServiceNow insieme al codice di stato di esito positivo. Risposta: {"import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "risultato": [{"transform_map": "evento imprevisto di OMS", "tabella": "evento imprevisto", "stato": "errore", "error_message": "{record di destinazione non trovato | Tabella non valida | Tabella di staging non valida "}"

    **Motivo**: tale errore viene restituito da ServiceNow quando:
    * Uno script personalizzato distribuito nell'istanza di ServiceNow fa sì che gli eventi imprevisti vengano ignorati.
    * Il codice "app di OMS Integrator" è stato modificato sul lato ServiceNow, ad esempio lo script OnBefore.

    **Soluzione**: disabilitare tutti gli script personalizzati o le modifiche del codice del percorso di importazione dati.

* **Errore**: "{" Error ": {" message ":" operazione non riuscita "," Detail ":" Impossibile aggiornare l'eccezione ACL a causa di vincoli di sicurezza "}"

    **Cause**: errori di configurazione delle autorizzazioni ServiceNow

    **Soluzione**: verificare che tutti i ruoli siano stati assegnati correttamente come [specificato](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role).

* **Errore**: "si è verificato un errore durante l'invio della richiesta".

    **Motivo**: "istanza di ServiceNow non disponibile"

    **Soluzione**: verificare che l'istanza in ServiceNow possa essere eliminata o non disponibile.

* **Errore**: "ServiceDeskHttpBadRequestException: StatusCode = 429"

    **Motivo**: i limiti di velocità ServiceNow sono troppo bassi.

    **Soluzione**: aumentare o annullare i limiti di velocità nell'istanza di ServiceNow, come illustrato [qui](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html).

* **Errore**: "AccessToken e RefreshToken non validi. L'utente deve eseguire nuovamente l'autenticazione ".

    **Motivo**: il token di aggiornamento è scaduto.

    **Soluzione**: sincronizzare il connettore ITSM per generare un nuovo token di aggiornamento, come illustrato [qui](./itsmc-resync-servicenow.md).

* **Errore**: "Impossibile creare o aggiornare l'elemento di lavoro per l'avviso {alertName}. ITSM Connector {connectionIdentifier} non esiste o è stato eliminato ".

    **Motivo**: il connettore ITSM è stato eliminato.

    **Soluzione**: il connettore ITSM è stato eliminato, ma sono ancora presenti azioni ITSM definite per usarlo. Per risolvere il problema sono disponibili due opzioni:
  * Trovare e disabilitare o eliminare tale azione
  * [Riconfigurare il gruppo di azioni per l'](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) uso di un connettore ITSM esistente.
  * [Creare un nuovo connettore ITSM](./itsmc-definition.md#create-an-itsm-connection) e [riconfigurare il gruppo di azioni per usarlo](itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="ui-common-errors"></a>Errori comuni dell'interfaccia utente

* **Errore**: "si è verificato un problema. Non è stato possibile ottenere i dettagli della connessione. "

    **Motivo**: il connettore ITSM appena creato ha ancora completato la sincronizzazione iniziale.

    **Risoluzione**: quando viene creato un nuovo connettore ITSM, il connettore ITSM avvia la sincronizzazione delle informazioni dal sistema ITSM, ad esempio i modelli di elemento di lavoro e gli elementi di lavoro. Sincronizzare il connettore ITSM per generare un nuovo token di aggiornamento, come illustrato [qui](./itsmc-resync-servicenow.md).
