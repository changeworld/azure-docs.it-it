---
title: Connettere Provance a IT Service Management Connector
description: Questo articolo fornisce informazioni su come dimostrare con IT Service Management Connector (connettore) in monitoraggio di Azure per monitorare e gestire centralmente gli elementi di lavoro ITSM.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 542b127823a73058f319e6a39c001bd563f042ae
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045378"
---
# <a name="connect-provance-with-it-service-management-connector"></a>Connettere Provance a IT Service Management Connector

Questo articolo fornisce informazioni su come configurare la connessione tra l'istanza di Provance e IT Service Management Connector (connettore) in Log Analytics per gestire centralmente gli elementi di lavoro.

> [!NOTE]
> A partire da 1-Oct-2020 l'integrazione di ITSM Provence con Azure Alert non verrà più abilitata per i nuovi clienti. Le nuove connessioni ITSM non saranno supportate.
> Saranno supportate le connessioni ITSM esistenti.

Le sezioni seguenti forniscono informazioni dettagliate su come connettere il prodotto Provance a Connettore di Gestione dei servizi IT in Azure.

## <a name="prerequisites"></a>Prerequisiti

Accertarsi di aver soddisfatto i prerequisiti seguenti:

- Connettore di Gestione dei servizi IT installato. Altre informazioni: [Aggiunta della soluzione Connettore di Gestione dei servizi IT](./itsmc-definition.md#add-it-service-management-connector).
- L'app Provance deve essere registrata in Azure AD e l'ID client deve essere stato reso disponibile. Per informazioni dettagliate, vedere [Come configurare l'autenticazione di Active Directory](../../app-service/configure-authentication-provider-aad.md).

- Ruolo utente:  Amministratore.

## <a name="connection-procedure"></a>Procedura di connessione

Seguire questa procedura per creare una connessione Provance:

1. Nel portale di Azure passare a **Tutte le risorse** e cercare **ServiceDesk(NomeAreaDiLavoro)**

2. In **ORIGINI DATI DELL'AREA DI LAVORO** fare clic su **Connessioni di Gestione dei servizi IT**.
    ![Nuova connessione](media/itsmc-overview/add-new-itsm-connection.png)

3. Nella parte superiore del riquadro destro fare clic su **Aggiungi**.

4. Specificare le informazioni come illustrato nella tabella seguente e quindi fare clic su **OK** per creare la connessione.

> [!NOTE]
> Tutti questi parametri sono obbligatori.

| **Campo** | **Descrizione** |
| --- | --- |
| **Connection Name** (Nome connessione)   | Digitare un nome per l'istanza di Provance da connettere a Connettore di Gestione dei servizi IT.  Questo nome viene usato in seguito quando si configurano gli elementi di lavoro in questa istanza di Gestione dei servizi IT o quando si visualizzano analisi dei log dettagliate. |
| **Tipo di partner**   | Selezionare **Provance**. |
| **Nome utente**   | Digitare il nome utente che può connettersi a Connettore di Gestione dei servizi IT.    |
| **Password**   | Digitare la password associata a questo nome utente. **Nota:** il nome utente e la password vengono usati solo per generare i token di autenticazione e non vengono archiviati nel servizio Connettore di Gestione dei servizi IT.|
| **URL del server**   | Digitare l'URL per l'istanza di Provance da connettere a Connettore di Gestione dei servizi IT. |
| **ID client**   | Digitare l'ID client per l'autenticazione di questa connessione, generato nell'istanza di Provance.  Per altre informazioni sull'ID client, vedere [Come configurare l'autenticazione di Active Directory](../../app-service/configure-authentication-provider-aad.md). |
| **Ambito sincronizzazione dati**   | Selezionare gli elementi di lavoro di Provance da sincronizzare con Azure Log Analytics tramite Connettore di Gestione dei servizi IT.  Questi elementi di lavoro vengono importati in Log Analytics.   **Opzioni:**   Eventi imprevisti, Richieste di modifica.|
| **Sincronizza dati** | Digitare il numero di giorni precedenti da cui si vogliono recuperare i dati. **Limite massimo**: 120 giorni. |
| **Create new configuration item in ITSM solution** (Crea nuovo elemento di configurazione nella soluzione ITSM) | Selezionare questa opzione se si vogliono creare gli elementi di configurazione nel prodotto ITSM. Se questa opzione è selezionata, Connettore di Gestione dei servizi IT crea le integrazioni continue interessate come elementi di configurazione (nel caso in cui non esistano) nel sistema di Gestione dei servizi IT supportato. **Impostazione predefinita**: disabilitata.|

![Screenshot che evidenzia il nome della connessione e gli elenchi dei tipi di partner.](media/itsmc-connections-provance/itsm-connections-provance-latest.png)

**Dopo la corretta connessione e la sincronizzazione**:

- Gli elementi di lavoro selezionati nell'istanza di Provance vengono importati in Azure **Log Analytics**. È possibile visualizzare il riepilogo di questi elementi di lavoro nel riquadro IT Service Management Connector.

- È possibile creare eventi imprevisti dagli avvisi o dai record di log di Log Analytics oppure dagli avvisi di Azure in questa istanza di Provance.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Connettore di Gestione dei servizi IT](itsmc-overview.md)
* [Creare elementi di lavoro di Connettore di Gestione dei servizi IT da avvisi di Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Risolvere i problemi di Connettore di Gestione dei servizi IT](./itsmc-resync-servicenow.md)