---
title: Connettere ServiceNow con IT Service Management Connector
description: Informazioni su come connettere ServiceNow con IT Service Management Connector (connettore) in monitoraggio di Azure per monitorare e gestire centralmente gli elementi di lavoro ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 3cc38fad6f26bf6f382b4a275638c450c8333a04
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955733"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>Connettere ServiceNow con IT Service Management Connector

Questo articolo illustra come configurare la connessione tra un'istanza di ServiceNow e IT Service Management Connector (connettore) in Log Analytics, in modo da poter gestire centralmente gli elementi di lavoro di gestione dei servizi IT (ITSM).

## <a name="prerequisites"></a>Prerequisiti
Assicurarsi di soddisfare i prerequisiti seguenti per la connessione.

### <a name="itsmc-installation"></a>Installazione di connettore

Per informazioni sull'installazione di connettore, vedere [aggiungere la soluzione IT Service Management Connector](./itsmc-definition.md#add-it-service-management-connector).

> [!NOTE]
> CONNETTORE supporta solo l'offerta di Software as a Service (SaaS) ufficiale di ServiceNow. Le distribuzioni private di ServiceNow non sono supportate.

### <a name="oauth-setup"></a>Configurazione di OAuth

Le versioni supportate di ServiceNow includono Orlando, New York, Madrid, Londra, Kingston, Jakarta, Istanbul, Helsinki e Ginevra.

Gli amministratori di ServiceNow devono generare un ID client e un segreto client per la relativa istanza di ServiceNow. Vedere le informazioni seguenti come richiesto:

- [Configurare OAuth per Orlando](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Set up OAuth for New York](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html) (Configurare OAuth per New York)
- [Set up OAuth for Madrid](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html) (Configurare OAuth per Madrid)
- [Set up OAuth for London](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html) (Configurare OAuth per Londra)
- [Set up OAuth for Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html) (Configurare OAuth per Kingston)
- [Set up OAuth for Jakarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html) (Configurare OAuth per Giacarta)
- [Set up OAuth for Istanbul](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html) (Configurare OAuth per Istanbul)
- [Set up OAuth for Helsinki](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html) (Configurare OAuth for Helsinki)
- [Set up OAuth for Geneva](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html) (Configurare OAuth per Ginevra)

Come parte della configurazione di OAuth, è consigliabile:

1. [Creare un endpoint per consentire ai client di accedere all'istanza di](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_CreateEndpointforExternalClients.html).

1. Aggiornare la durata del token di aggiornamento:

   1. Nel riquadro **ServiceNow** cercare **System OAuth**, quindi selezionare Registro di sistema **dell'applicazione**. 
   1. Selezionare il nome del OAuth definito e modificare la **durata del token di aggiornamento** a **7.776.000 secondi** (90 giorni). 
   1. Selezionare **Aggiorna**. 

1. Stabilire una procedura interna per assicurarsi che la connessione rimanga attiva. Un paio di giorni prima della scadenza prevista della durata del token di aggiornamento, eseguire le operazioni seguenti:

   1. [Completare un processo di sincronizzazione manuale per la configurazione del connettore ITSM](./itsmc-resync-servicenow.md).

   1. Revocare al token di aggiornamento precedente. Non è consigliabile mantenere le chiavi obsolete per motivi di sicurezza. 
   
      1. Nel riquadro **ServiceNow** cercare **System OAuth**, quindi selezionare **Manage Tokens**. 
      
      1. Selezionare il token precedente dall'elenco in base al nome OAuth e alla data di scadenza.

         ![Screenshot che mostra un elenco di token per OAuth.](media/itsmc-connections/snow-system-oauth.png)
      1. Selezionare **REVOKE Access**  >  **Revoke**.

## <a name="install-the-user-app-and-create-the-user-role"></a>Installare l'app utente e creare il ruolo utente

Usare la procedura seguente per installare l'app utente del servizio ora e creare il ruolo utente di integrazione. Queste credenziali vengono usate per creare la connessione ServiceNow in Azure.

> [!NOTE]
> CONNETTORE supporta solo l'app utente ufficiale per l'integrazione di Microsoft Log Analytics che viene scaricata dall'archivio ServiceNow. CONNETTORE non supporta l'inserimento di codice sul lato ServiceNow o su qualsiasi applicazione che non fa parte della soluzione ServiceNow ufficiale. 

1. Visitare l' [Archivio ServiceNow](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) e installare l' **app utente per ServiceNow e l'integrazione di Microsoft OMS** nell'istanza di ServiceNow.
   
   >[!NOTE]
   >Come parte della transizione continua da Microsoft Operations Management Suite (OMS) a monitoraggio di Azure, OMS è ora chiamato Log Analytics.     
2. Dopo l'installazione, passare alla barra di spostamento a sinistra dell'istanza di ServiceNow e quindi cercare e selezionare **Microsoft OMS Integrator**.  
3. Selezionare **elenco di controllo installazione**.

   Lo stato viene visualizzato come  **non completato** perché il ruolo utente non è ancora stato creato.

4. Nella casella di testo accanto a **Crea utente di integrazione** immettere il nome dell'utente che può connettersi a connettore in Azure.
5. Immettere la password per l'utente e quindi fare clic su **OK**.  

L'utente appena creato viene visualizzato con i ruoli predefiniti assegnati:

- personalize_choices
- import_transformer
- x_mioms_microsoft.user
- itil
- template_editor
- view_changer

Dopo aver creato l'utente, lo stato dell' **elenco di controllo dell'installazione** viene spostato su **completato** ed elenca i dettagli del ruolo utente creato per l'app.

> [!NOTE]
> CONNETTORE può inviare eventi imprevisti a ServiceNow senza che altri moduli siano installati nell'istanza di ServiceNow. Se si usa il modulo EventManagement nell'istanza di ServiceNow e si vuole creare eventi o avvisi in ServiceNow usando il connettore, aggiungere i ruoli seguenti all'utente di integrazione:
> 
> - evt_mgmt_integration
> - evt_mgmt_operator  

## <a name="create-a-connection"></a>Creare una connessione
Utilizzare la procedura seguente per creare una connessione ServiceNow.

> [!NOTE]
> Gli avvisi inviati da monitoraggio di Azure possono creare uno degli elementi seguenti in ServiceNow: eventi, eventi imprevisti o avvisi. 

1. In portale di Azure passare a **tutte le risorse** e cercare **ServiceDesk (NomeAreadilavoro)**.

2. In **origini dati dell'area di lavoro** selezionare **connessioni ITSM**.

   ![Screenshot che mostra la selezione di un'origine dati.](media/itsmc-connections/add-new-itsm-connection.png)

3. Nella parte superiore del riquadro di destra selezionare **Aggiungi**.

4. Fornire le informazioni descritte nella tabella seguente e quindi fare clic su **OK**.

   | **Campo** | **Descrizione** |
   | --- | --- |
   | **Connection Name** (Nome connessione)   | Immettere un nome per l'istanza di ServiceNow che si vuole connettere con connettore. Questo nome verrà usato più avanti in Log Analytics quando si configurano gli elementi di lavoro ITSM e si visualizzano analisi dettagliate. |
   | **Tipo di partner**   | Selezionare **ServiceNow**. |
   | **URL server**   | Immettere l'URL dell'istanza di ServiceNow che si desidera connettere a connettore. L'URL deve puntare a una versione SaaS supportata con il suffisso *. servicenow.com* , ad esempio https://XXXXX.service-now.com/) .|
   | **Nome utente**   | Immettere il nome utente di integrazione creato nell'app ServiceNow per supportare la connessione a connettore.|
   | **Password**   | Immettere la password associata al nome utente. **Nota**: il nome utente e la password vengono usati solo per la generazione di token di autenticazione. Non vengono archiviati in nessun punto del servizio connettore.  |
   | **ID client**   | Immettere l'ID client che si vuole usare per l'autenticazione OAuth2, che è stata generata in precedenza. Per ulteriori informazioni sulla generazione di un ID client e di un segreto, vedere la pagina relativa alla [configurazione di OAuth](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
   | **Segreto client**   | Immettere il segreto client generato per questo ID.   |
   | **Ambito sincronizzazione dati (in giorni)** | Immettere il numero di giorni precedenti da cui si desiderano i dati. Il limite è di 120 giorni. |
   | **Elementi di lavoro da sincronizzare**   | Selezionare gli elementi di lavoro ServiceNow che si vuole sincronizzare con Azure Log Analytics, tramite connettore. I valori selezionati vengono importati in Log Analytics. Le opzioni sono eventi imprevisti e richieste di modifica.|
   | **Crea nuovo elemento di configurazione nel prodotto ITSM** | Selezionare questa opzione se si vogliono creare gli elementi di configurazione nel prodotto ITSM. Quando questa opzione è selezionata, connettore crea elementi di configurazione, se non esistono, nel sistema ITSM supportato. È disabilitata per impostazione predefinita. |

![Screenshot delle caselle e delle opzioni per l'aggiunta di una connessione ServiceNow.](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

Quando la connessione e la sincronizzazione sono state completate correttamente:

- Gli elementi di lavoro selezionati dall'istanza di ServiceNow vengono importati in Log Analytics. È possibile visualizzare il riepilogo di questi elementi di lavoro nel riquadro **IT Service Management Connector** .

- È possibile creare eventi imprevisti da Log Analytics avvisi o record di log oppure dagli avvisi di Azure in questa istanza di ServiceNow.

> [!NOTE]
> ServiceNow ha un limite di frequenza per le richieste all'ora. Per configurare il limite, definire la **limitazione della frequenza delle API REST in ingresso** nell'istanza di ServiceNow.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica sul connettore ITSM](itsmc-overview.md)
* [Creare elementi di lavoro di Connettore di Gestione dei servizi IT da avvisi di Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Risoluzione dei problemi nel connettore ITSM](./itsmc-resync-servicenow.md)