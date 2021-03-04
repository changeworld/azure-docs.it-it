---
title: Connettere SCSM con IT Service Management Connector
description: Questo articolo fornisce informazioni su come SCSM con IT Service Management Connector (connettore) in monitoraggio di Azure per monitorare e gestire centralmente gli elementi di lavoro ITSM.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 3f80c42be217d062510c687075cf46b4e7539419
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045344"
---
# <a name="connect-system-center-service-manager-with-it-service-management-connector"></a>Connetti System Center Service Manager con IT Service Management Connector

Questo articolo fornisce informazioni su come configurare la connessione tra l'istanza di System Center Service Manager e IT Service Management Connector (connettore) in Log Analytics per gestire centralmente gli elementi di lavoro.

Le sezioni seguenti forniscono informazioni dettagliate su come connettere il prodotto System Center Service Manager a Connettore di Gestione dei servizi IT in Azure.

## <a name="prerequisites"></a>Prerequisiti

Accertarsi di aver soddisfatto i prerequisiti seguenti:

- Connettore di Gestione dei servizi IT installato. Altre informazioni: [Aggiunta della soluzione Connettore di Gestione dei servizi IT](./itsmc-definition.md).
- L'applicazione Web (app Web) Service Manager è stata distribuita e configurata. Per informazioni sull'app Web, vedere[qui](#create-and-deploy-service-manager-web-app-service).
- Connessione ibrida è stata creata e configurata. Altre informazioni: [Configurazione della connessione ibrida](#configure-the-hybrid-connection).
- Versioni supportate di Service Manager:  2012 R2 o 2016.
- Ruolo utente:  [Operatore avanzato](/previous-versions/system-center/service-manager-2010-sp1/ff461054(v=technet.10)).
- Attualmente gli avvisi inviati da monitoraggio di Azure possono creare in System Center Service Manager eventi imprevisti.

> [!NOTE]
> - Connettore di Gestione dei servizi IT può connettersi solo a istanze di ServiceNow basate su cloud. Le istanze locali di ServiceNow non sono attualmente supportate.
> - Per usare i [modelli](./itsmc-definition.md#define-a-template) personalizzati come parte delle azioni, è necessario eseguire il mapping del parametro "ProjectionType" nel modello SCSM a "IncidentManagement! System. WorkItem. Incident. ProjectionType "

## <a name="connection-procedure"></a>Procedura di connessione

Usare la procedura seguente per connettere l'istanza di System Center Service Manager a Connettore di Gestione dei servizi IT:

1. Nel portale di Azure passare a **Tutte le risorse** e cercare **ServiceDesk(NomeAreaDiLavoro)**

2. In **ORIGINI DATI DELL'AREA DI LAVORO** fare clic su **Connessioni di Gestione dei servizi IT**.

    ![Nuova connessione](media/itsmc-connections-scsm/add-new-itsm-connection.png)

3. Nella parte superiore del riquadro destro fare clic su **Aggiungi**.

4. Specificare le informazioni come illustrato nella tabella seguente e quindi fare clic su **OK** per creare la connessione.

> [!NOTE]
> Tutti questi parametri sono obbligatori.

| **Campo** | **Descrizione** |
| --- | --- |
| **Connection Name** (Nome connessione)   | Digitare il nome dell'istanza di System Center Service Manager da connettere a Connettore di Gestione dei servizi IT.  Questo nome viene usato in seguito durante la configurazione degli elementi di lavoro in questa istanza o quando si visualizzano analisi dei log dettagliate. |
| **Tipo di partner**   | Selezionare **System Center Service Manager**. |
| **URL del server**   | Digitare l'URL dell'app Web Service Manager. Per altre informazioni sull'app Web Service Manager, vedere [qui](#create-and-deploy-service-manager-web-app-service).
| **ID client**   | Digitare l'ID client generato tramite lo script automatico per l'autenticazione dell'app Web. Per altre informazioni sullo script automatico, vedere [qui](./itsmc-service-manager-script.md).|
| **Segreto client**   | Digitare il segreto client, generato per questo ID.   |
| **Sincronizza dati**   | Selezionare gli elementi di lavoro di Service Manager da sincronizzare tramite Connettore di Gestione dei servizi IT.  Questi elementi di lavoro vengono importati in Log Analytics. **Opzioni:**  Eventi imprevisti, Richieste di modifica.|
| **Ambito sincronizzazione dati** | Digitare il numero di giorni precedenti da cui si vogliono recuperare i dati. **Limite massimo**: 120 giorni. |
| **Create new configuration item in ITSM solution** (Crea nuovo elemento di configurazione nella soluzione ITSM) | Selezionare questa opzione se si vogliono creare gli elementi di configurazione nel prodotto ITSM. Se questa opzione è selezionata, Log Analytics crea le integrazioni continue interessate come elementi di configurazione (in caso di integrazioni continue inesistenti) nel sistema di Gestione dei servizi IT supportato. **Impostazione predefinita**: disabilitata. |

![Connessione di Service Manager](media/itsmc-connections-scsm/service-manager-connection.png)

**Dopo la corretta connessione e la sincronizzazione**:

- Gli elementi di lavoro selezionati in Service Manager vengono importati in Azure **Log Analytics**. È possibile visualizzare il riepilogo di questi elementi di lavoro nel riquadro IT Service Management Connector.

- È possibile creare eventi imprevisti dagli avvisi o dai record di log di Log Analytics oppure dagli avvisi di Azure in questa istanza di Service Manager.

Altre informazioni: [Creare elementi di lavoro di Connettore di Gestione dei servizi IT da avvisi di Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="create-and-deploy-service-manager-web-app-service"></a>Creare e distribuire il servizio app Web di Service Manager

Per connettere l'istanza locale di Service Manager con Connettore di Gestione dei servizi IT in Azure, Microsoft ha creato un'app Web di Service Manager in GitHub.

Per configurare l'app Web ITSM per l'istanza di Service Manager, seguire questa procedura:

- **Distribuire l'app Web**: distribuire l'app Web, configurare le proprietà ed eseguire l'autenticazione con Azure AD. È possibile distribuire l'app Web usando lo [script automatico](./itsmc-service-manager-script.md) fornito da Microsoft.
- **Configurare la connessione ibrida** - [Configurare questa connessione](#configure-the-hybrid-connection) manualmente.

### <a name="deploy-the-web-app"></a>Distribuire l'app Web
Usare lo [script](./itsmc-service-manager-script.md) automatico per distribuire l'app Web, configurare le proprietà ed eseguire l'autenticazione con Azure AD.

Eseguire lo script, fornendo i dettagli richiesti seguenti:

- Dettagli della sottoscrizione di Azure
- Nome del gruppo di risorse
- Location
- Dettagli del server di Service Manager (nome del server, dominio, nome utente e password)
- Prefisso del nome del sito per l'app Web
- Spazio dei nomi ServiceBus.

Lo script crea l'app Web usando il nome specificato insieme ad alcune stringhe aggiuntive per renderlo univoco. Genera **URL dell'app Web**, **ID del client** e **segreto client**.

Salvare questi valori perché serviranno al momento della creazione di una connessione a Connettore di Gestione dei servizi IT.

**Controllare l'installazione dell'app Web**

1. Passare al **portale di Azure** > **Risorse**.
2. Selezionare l'app Web, fare clic su **Impostazioni** > **Impostazioni applicazione**.
3. Confermare le informazioni sull'istanza di Service Manager specificate durante la distribuzione dell'app tramite lo script.

## <a name="configure-the-hybrid-connection"></a>Configurare la connessione ibrida

Usare la procedura seguente per configurare la connessione ibrida tra l'istanza di Service Manager e Connettore di Gestione dei servizi IT in Azure.

1. Trovare l'app Web di Service Manager in **Risorse di Azure**.
2. Fare clic su **Impostazioni** > **Rete**.
3. In **Connessioni ibride** fare clic su **Configurare gli endpoint della connessione ibrida**.

    ![Rete per la connessione ibrida](media/itsmc-connections-scsm/itsmc-hybrid-connection-networking-and-end-points.png)
4. Nel pannello **Connessioni ibride** fare clic su **Aggiungi connessione ibrida**.

    ![Aggiunta di una connessione ibrida](media/itsmc-connections-scsm/itsmc-new-hybrid-connection-add.png)

5. Nel pannello **Aggiungi connessione ibrida** fare clic su **Crea nuova connessione ibrida**.

    ![Nuova connessione ibrida](media/itsmc-connections-scsm/itsmc-create-new-hybrid-connection.png)

6. Digitare i valori seguenti:

   - **Nome endpoint**: specificare un nome per la nuova connessione ibrida.
   - **Host EndPoint**: nome di dominio completo del server di gestione di Service Manager.
   - **Porta EndPoint**: digitare 5724
   - **Spazio dei nomi del bus di servizio**: usare uno spazio dei nomi del bus di servizio esistente o crearne uno nuovo.
   - **Località**: selezionare la località.
   - **Name**: specificare un nome per il bus di servizio, se lo si sta creando.

     ![Valori della connessione ibrida](media/itsmc-connections-scsm/itsmc-new-hybrid-connection-values.png)
6. Fare clic su **OK** per chiudere il pannello **Crea connessione ibrida** e iniziare a creare la connessione ibrida.

    Al termine della creazione, la connessione ibrida viene visualizzata sotto il pannello.

7. Dopo la creazione della connessione ibrida, selezionare la connessione e fare clic su **Aggiungi connessione ibrida selezionata**.

    ![Nuova connessione ibrida](media/itsmc-connections-scsm/itsmc-new-hybrid-connection-added.png)

### <a name="configure-the-listener-setup"></a>Configurare le impostazioni del listener

Seguire questa procedura per configurare le impostazioni del listener per la connessione ibrida.

1. Nel pannello **Connessioni ibride** fare clic su **Scarica Gestione connessioni** ed eseguire l'installazione nella macchina virtuale in cui è in esecuzione l'istanza di System Center Service Manager.

    Al termine dell'installazione, l'opzione **Hybrid Connection Manager UI** (Interfaccia utente ibrida di Gestione connessioni) è disponibile nel menu **Start**.

2. Fare clic su **Hybrid Connection Manager UI** (Interfaccia utente ibrida di Gestione connessioni). Verranno richieste le credenziali di Azure.

3. Accedere con le credenziali di Azure e selezionare la sottoscrizione in cui è stata creata la connessione ibrida.

4. Fare clic su **Salva**.

La connessione ibrida è stata completata.

![Connessione ibrida completata](media/itsmc-connections-scsm/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> Al termine della creazione della connessione ibrida, verificare e testare la connessione passando all'app Web di Service Manager distribuita. Assicurarsi che la connessione funzioni prima di provare a connettersi a Connettore di Gestione dei servizi IT in Azure.

L'esempio seguente mostra i dettagli di una connessione riuscita:

![Test della connessione ibrida](media/itsmc-connections-scsm/itsmc-hybrid-connection-test.png)

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Connettore di Gestione dei servizi IT](itsmc-overview.md)
* [Creare elementi di lavoro di Connettore di Gestione dei servizi IT da avvisi di Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Risolvere i problemi di Connettore di Gestione dei servizi IT](./itsmc-resync-servicenow.md)