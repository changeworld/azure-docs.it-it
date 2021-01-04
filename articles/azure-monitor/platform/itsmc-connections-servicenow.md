---
title: Connettere ServiceNow con IT Service Management Connector
description: Questo articolo fornisce informazioni su come ServiceNow con IT Service Management Connector (connettore) in monitoraggio di Azure per monitorare e gestire centralmente gli elementi di lavoro ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 662c36e4f0082c376a6e250e9a0885f0cd225964
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97729660"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>Connettere ServiceNow con IT Service Management Connector

Questo articolo fornisce informazioni su come configurare la connessione tra l'istanza di ServiceNow e IT Service Management Connector (connettore) in Log Analytics per gestire centralmente gli elementi di lavoro.

Le sezioni seguenti forniscono informazioni dettagliate su come connettere il prodotto ServiceNow a Connettore di Gestione dei servizi IT in Azure.

## <a name="prerequisites"></a>Prerequisiti
Accertarsi di aver soddisfatto i prerequisiti seguenti:
- Connettore di Gestione dei servizi IT installato. Altre informazioni: [Aggiunta della soluzione Connettore di Gestione dei servizi IT](./itsmc-definition.md#add-it-service-management-connector).
- Versioni supportate di ServiceNow: Orlando, New York, Madrid, Londra, Kingston, Jakarta, Istanbul, Helsinki, Ginevra.
- Attualmente gli avvisi inviati da monitoraggio di Azure possono creare in ServiceNow uno dei seguenti elementi: eventi, eventi imprevisti o avvisi.
> [!NOTE]
> Attualmente Gestione dei servizi IT supporta solo l'offerta SaaS ufficiale di ServiceNow. Le distribuzioni private di ServiceNow non sono attualmente supportate. 

**ServiceNow Admins deve eseguire le operazioni seguenti nell'istanza di ServiceNow**:
- Generare l'ID client e il segreto client per il prodotto ServiceNow. Per informazioni su come generare un ID client e un segreto client, vedere gli articoli seguenti:

    - [Configurare OAuth per Orlando](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Set up OAuth for New York](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html) (Configurare OAuth per New York)
    - [Set up OAuth for Madrid](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html) (Configurare OAuth per Madrid)
    - [Set up OAuth for London](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html) (Configurare OAuth per Londra)
    - [Set up OAuth for Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html) (Configurare OAuth per Kingston)
    - [Set up OAuth for Jakarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html) (Configurare OAuth per Giacarta)
    - [Set up OAuth for Istanbul](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html) (Configurare OAuth per Istanbul)
    - [Set up OAuth for Helsinki](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html) (Configurare OAuth for Helsinki)
    - [Set up OAuth for Geneva](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html) (Configurare OAuth per Ginevra)
> [!NOTE]
> Con la definizione della configurazione di OAuth è consigliabile eseguire le operazioni seguenti:
>
> 1) **Aggiornare la durata del token di aggiornamento a 90 giorni (7.776.000 secondi):** Durante la [configurazione di OAuth](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_SettingUpOAuth.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696739125&sdata=Q7mF6Ej8MCupKaEJpabTM56EDZ1T8vFVyihhoM594aA%3D&reserved=0) nella fase 2: [Creare un endpoint per consentire ai client di accedere all'istanza](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_CreateEndpointforExternalClients.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696749123&sdata=hoAJHJAFgUeszYCX1Q%2FXr4N%2FAKiFcm5WV7mwR2UqeWA%3D&reserved=0) Dopo aver definito l'endpoint, nel pannello ServiceNow cercare System OAuth (OAuth di sistema), quindi selezionare Application Registry (Registro applicazione). Selezionare il nome di OAuth definito e aggiornare il campo della durata del token di aggiornamento a 7.776.000 (90 giorni in secondi).
> Alla fine fare clic su Update (Aggiorna).
> 2) **È consigliabile stabilire una procedura interna per assicurarsi che la connessione rimanga attiva:** in base alla durata del token di aggiornamento per l'aggiornamento del token. Assicurarsi di eseguire le operazioni seguenti prima della scadenza prevista del token di aggiornamento. È consigliabile un paio di giorni prima della scadenza del token di aggiornamento:
>
>     1. [Completare un processo di sincronizzazione manuale per la configurazione del Connettore di Gestione dei servizi IT](./itsmc-resync-servicenow.md)
>     2. Revocare il token di aggiornamento precedente, poiché per motivi di sicurezza, non è consigliabile conservare chiavi obsolete. Nel pannello ServiceNow cercare System OAuth (OAuth di sistema), quindi selezionare Manage Tokens (Gestisci token). Selezionare il token obsoleto dall'elenco in base al nome OAuth e alla data di scadenza.
> ![Definizione di OAuth del sistema SNOW](media/itsmc-connections/snow-system-oauth.png)
>     3. Fare clic su Revoke Access (Revoca accesso ), quindi su Revoke (Revoca).

- Installare l'app utente per l'integrazione di Microsoft Log Analytics (app ServiceNow). [Altre informazioni](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 )
> [!NOTE]
> CONNETTORE supporta solo l'app utente ufficiale per l'integrazione di Microsoft Log Analytics che viene scaricata da ServiceNow Store. CONNETTORE non supportano l'inserimento di codice nel lato ServiceNow o l'applicazione che non fa parte della soluzione ServiceNow ufficiale. 
- Creare un ruolo utente integrazione per l'app utente installata. Per informazioni su come creare il ruolo utente di integrazione, vedere [qui](#create-integration-user-role-in-servicenow-app).

## <a name="connection-procedure"></a>**Procedura di connessione**
Seguire questa procedura per creare una connessione ServiceNow:


1. Nel portale di Azure passare a **Tutte le risorse** e cercare **ServiceDesk(NomeAreaDiLavoro)**

2.  In **ORIGINI DATI DELL'AREA DI LAVORO** fare clic su **Connessioni di Gestione dei servizi IT**.
    ![Nuova connessione](media/itsmc-connections/add-new-itsm-connection.png)

3. Nella parte superiore del riquadro destro fare clic su **Aggiungi**.

4. Specificare le informazioni come illustrato nella tabella seguente e quindi fare clic su **OK** per creare la connessione.


> [!NOTE]
> Tutti questi parametri sono obbligatori.

| **Campo** | **Descrizione** |
| --- | --- |
| **Connection Name** (Nome connessione)   | Digitare un nome per l'istanza di ServiceNow da connettere a Connettore di Gestione dei servizi IT.  Questo nome viene usato in seguito in Log Analytics quando si configurano elementi di lavoro in questa istanza di Gestione dei servizi IT o si visualizzano analisi dei log dettagliate. |
| **Tipo di partner**   | Selezionare **ServiceNow**. |
| **Nome utente**   | Digitare il nome utente di integrazione creato nell'app ServiceNow per supportare la connessione a Connettore di Gestione dei servizi IT. Altre informazioni: [Create ServiceNow app user role](#create-integration-user-role-in-servicenow-app) (Creare il ruolo utente dell'app ServiceNow).|
| **Password**   | Digitare la password associata a questo nome utente. **Nota**: il nome utente e la password vengono usati solo per generare i token di autenticazione e non vengono archiviati nel servizio Connettore di Gestione dei servizi IT.  |
| **URL del server**   | Digitare l'URL dell'istanza di ServiceNow da connettere a Connettore di Gestione dei servizi IT. L'URL deve puntare a una versione SaaS supportata con suffisso ".servicenow.com".|
| **ID client**   | Digitare l'ID client da usare per l'autenticazione OAuth2, generata in precedenza.  Per altre informazioni sulla generazione dell'ID client e del segreto:   [Installazione di OAuth](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Segreto client**   | Digitare il segreto client, generato per questo ID.   |
| **Ambito sincronizzazione dati**   | Selezionare gli elementi di lavoro di ServiceNow da sincronizzare con Azure Log Analytics tramite Connettore di Gestione dei servizi IT.  I valori selezionati vengono importati in Log Analytics.   **Opzioni:**  Eventi imprevisti e Richieste di modifica.|
| **Sincronizza dati** | Digitare il numero di giorni precedenti da cui si vogliono recuperare i dati. **Limite massimo**: 120 giorni. |
| **Create new configuration item in ITSM solution** (Crea nuovo elemento di configurazione nella soluzione ITSM) | Selezionare questa opzione se si vogliono creare gli elementi di configurazione nel prodotto ITSM. Se questa opzione è selezionata, Connettore di Gestione dei servizi IT crea le integrazioni continue interessate come elementi di configurazione (nel caso in cui non esistano) nel sistema di Gestione dei servizi IT supportato. **Impostazione predefinita**: disabilitata. |

![Connessione di ServiceNow](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**Dopo la corretta connessione e la sincronizzazione**:

- Gli elementi di lavoro selezionati nell'istanza di ServiceNow vengono importati in Azure **Log Analytics**. È possibile visualizzare il riepilogo di questi elementi di lavoro nel riquadro IT Service Management Connector.

- È possibile creare eventi imprevisti dagli avvisi o dai record di log di Log Analytics oppure dagli avvisi di Azure in questa istanza di ServiceNow.

> [!NOTE]
> In ServiceNow è previsto un limite di velocità per le richieste all'ora. Per configurare il limite, usare questa impostazione definendo "limitazione della frequenza delle API REST in ingresso" nell'istanza di ServiceNow.

## <a name="create-integration-user-role-in-servicenow-app"></a>Creare un ruolo utente di integrazione nell'app ServiceNow

Seguire questa procedura:

1. Passare a [ServiceNow Store](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) e installare l'**app utente per ServiceNow e per l'integrazione con Microsoft OMS** nell'istanza di ServiceNow.
   
   >[!NOTE]
   >In base alla transizione in corso da Microsoft Operations Management Suite (OMS) a Monitoraggio di Azure, OMS è ora denominato Log Analytics.     
2. Dopo l'installazione, passare alla barra di spostamento sinistra dell'istanza di ServiceNow, eseguire una ricerca e selezionare Microsoft OMS Integrator.  
3. Fare clic su **Installation Checklist** (Elenco di controllo installazione).

   Lo stato visualizzato è **Not complete** (Non completato) se il ruolo utente deve essere ancora creato.

4. Nelle caselle di testo accanto a **Create integration user** (Crea utente di integrazione) immettere il nome dell'utente che può connettersi a Connettore di Gestione dei servizi IT in Azure.
5. Immettere la password per questo utente e fare clic su **OK**.  

> [!NOTE]
> Queste credenziali vengono usate per creare la connessione di ServiceNow in Azure.

L'utente appena creato viene visualizzato con i ruoli predefiniti assegnati.

**Ruoli predefiniti**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   itil
-   template_editor
-   view_changer

Al termine della creazione dell'utente, lo stato di **Check Installation Checklist** (Controllo elenco di controllo installazione) viene impostato su Completed (Completato) e vengono elencati i dettagli del ruolo utente creato per l'app.

> [!NOTE]
> Il connettore di Gestione dei servizi IT può inviare eventi imprevisti a ServiceNow senza che altri moduli siano installati nell'istanza di ServiceNow. Se si usa il modulo EventManagement nell'istanza di ServiceNow e si vuole creare gli eventi o avvisi in ServiceNow tramite il connettore, aggiungere i ruoli seguenti all'utente di integrazione:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="next-steps"></a>Passaggi successivi

* [Panoramica sul connettore ITSM](itsmc-overview.md)
* [Creare elementi di lavoro di Connettore di Gestione dei servizi IT da avvisi di Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Risolvere i problemi di Connettore di Gestione dei servizi IT](./itsmc-resync-servicenow.md)