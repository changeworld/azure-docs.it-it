---
title: Monitoraggio remoto e notifiche di IoT con App per la logica di Azure | Microsoft Docs
description: Usare le app per la logica di Azure per il monitoraggio della temperatura IoT sull'hub IoT e inviare automaticamente notifiche tramite e-mail alla cassetta postale per eventuali anomalie rilevate.
author: robinsh
keywords: Monitoraggio IoT, notifiche IoT, monitoraggio della temperatura IoT
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: 74724357dea9cd6c8c89a11a9eeb3d1b2933b790
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564940"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Monitoraggio remoto e notifiche di IoT con App per la logica di Azure tramite la connessione all'hub IoT e alla cassetta postale

![Diagramma end-to-end](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[App per la logica di Azure](../logic-apps/index.yml) consente di orchestrare i flussi di lavoro in servizi locali e cloud, in una o più aziende e in vari protocolli. Un'app per la logica inizia con un trigger, seguito da una o più azioni che possono essere sequenziate usando controlli predefiniti, ad esempio condizioni e iteratori. Questa flessibilità rende App per la logica una soluzione IoT ideale per gli scenari di monitoraggio IoT. Ad esempio, l'arrivo di dati di telemetria da un dispositivo in un endpoint dell'hub IoT può avviare flussi di lavoro dell'app per la logica per eseguire il warehouse dei dati in un BLOB Archiviazione di Azure, inviare avvisi di posta elettronica per avvisare le anomalie dei dati, pianificare una visita del tecnico se un dispositivo segnala un errore e così via.

Questo articolo illustra come creare un'app per la logica che connette l'hub IoT e la cassetta postale per il monitoraggio della temperatura e le notifiche. Il codice client in esecuzione nel dispositivo imposta una proprietà dell'applicazione, , su ogni messaggio `temperatureAlert` di telemetria inviato all'hub IoT. Quando il codice client rileva una temperatura superiore a 30 C, imposta questa proprietà su ; in caso contrario, imposta `true` la proprietà su `false` .

I messaggi in arrivo nell'hub IoT sono simili ai seguenti, con i dati di telemetria contenuti nel corpo e la proprietà contenuta nelle proprietà dell'applicazione (le proprietà di sistema `temperatureAlert` non vengono visualizzate):

```json
{
  "body": {
    "messageId": 18,
    "deviceId": "Raspberry Pi Web Client",
    "temperature": 27.796111770668457,
    "humidity": 66.77637926438427
  },
  "applicationProperties": {
    "temperatureAlert": "false"
  }
}
```

Per altre informazioni sul formato dei messaggi dell'hub IoT, vedere [Creare e leggere messaggi dell'hub IoT.](iot-hub-devguide-messages-construct.md)

In questo argomento viene configurato il routing nell'hub IoT per inviare messaggi in cui la `temperatureAlert` proprietà è a un endpoint del bus di `true` servizio. Si configura quindi un'app per la logica che si attiva sui messaggi in arrivo all'endpoint del bus di servizio e invia una notifica tramite posta elettronica.

## <a name="prerequisites"></a>Prerequisiti

* Completare [l'esercitazione sul simulatore online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) o una delle esercitazioni sul dispositivo. Ad esempio, è possibile passare a [Raspberry Pi con node.js](iot-hub-raspberry-pi-kit-node-get-started.md) o a una delle guide introduttive [Inviare](quickstart-send-telemetry-dotnet.md) dati di telemetria. Questi articoli riguardano i requisiti seguenti:

  * Una sottoscrizione di Azure attiva.
  * Un hub IoT di Azure nella sottoscrizione.
  * Un'applicazione client in esecuzione nel dispositivo che invia messaggi di telemetria all Azure IoT hub.

## <a name="create-service-bus-namespace-and-queue"></a>Creare lo spazio dei nomi e la coda del bus di servizio

Creare uno spazio dei nomi del bus di servizio e una coda Più avanti in questo argomento si creerà una regola di routing nell'hub IoT per indirizzare i messaggi che contengono un avviso relativo alla temperatura alla coda del bus di servizio, in cui verranno prelevati da un'app per la logica e attivati per inviare un messaggio di posta elettronica di notifica.

### <a name="create-a-service-bus-namespace"></a>Creare uno spazio dei nomi del bus di servizio

1. Nella pagina [portale di Azure](https://portal.azure.com/)selezionare **+ Crea una risorsa** Bus di  >  **servizio**  >  **di integrazione.**

1. Nel riquadro **Crea spazio dei** nomi specificare le informazioni seguenti:

   **Nome:** nome dello spazio dei nomi del bus di servizio. Lo spazio dei nomi deve essere univoco in Azure.

   **Piano tariffario:** **selezionare Basic** nell'elenco a discesa. Il livello Base è sufficiente per questa esercitazione.

   **Gruppo di risorse**: usare lo stesso gruppo di risorse usato dall'hub IoT.

   **Posizione**: usare la stessa posizione che usa l'hub IoT.

   ![Creare uno spazio dei nomi del bus di servizio nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Selezionare **Crea**. Attendere il completamento della distribuzione prima di procedere al passaggio successivo.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Aggiungere una coda del bus di servizio allo spazio dei nomi

1. Aprire lo spazio dei nomi del bus di servizio. Il modo più semplice per accedere allo  spazio dei nomi del bus di servizio è selezionare Gruppi di risorse nel riquadro delle risorse, selezionare il gruppo di risorse e quindi selezionare lo spazio dei nomi del bus di servizio dall'elenco delle risorse.

1. Nel riquadro **Spazio dei nomi del** bus di servizio selezionare + **Coda.**

1. Immettere un nome per la coda e quindi selezionare **Crea.** Al termine della creazione della coda, il **riquadro Crea** coda si chiude.

   ![Aggiungere una coda del bus di servizio nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. Nel riquadro Spazio **dei nomi del bus di** servizio selezionare Code in **Entità.**  Aprire la coda del bus di servizio dall'elenco e quindi selezionare **Criteri di accesso condiviso**+  >  **Aggiungi.**

1. Immettere un nome per il criterio, **selezionare Gestisci** e quindi selezionare **Crea.**

   ![Aggiungere un criterio della coda del bus di servizio nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Aggiungere un endpoint personalizzato e una regola di routing all'hub IoT

Aggiungere un endpoint personalizzato per la coda del bus di servizio all'hub IoT e creare una regola di routing dei messaggi per indirizzare i messaggi che contengono un avviso di temperatura a tale endpoint, dove verranno prelevati dall'app per la logica. La regola di routing usa una query di routing, , per inoltrare i messaggi in base al valore della proprietà dell'applicazione impostata dal `temperatureAlert = "true"` codice client in esecuzione nel `temperatureAlert` dispositivo. Per altre informazioni, vedere [Query di routing dei messaggi basata sulle proprietà dei messaggi](./iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Aggiungere un endpoint personalizzato

1. Aprire l'hub IoT. Il modo più semplice per accedere all'hub IoT è selezionare Gruppi di risorse nel riquadro delle risorse, selezionare il gruppo di risorse e quindi selezionare l'hub IoT dall'elenco di risorse. 

1. In **Messaggistica** selezionare **Routing messaggi**. Nel riquadro **Routing messaggi** selezionare la scheda **Endpoint personalizzati** e quindi selezionare **+ Aggiungi**. Nell'elenco a discesa selezionare Coda **del bus di servizio**.

   ![Screenshot che evidenzia l'opzione Coda del bus di servizio.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. Nel riquadro **Aggiungi un endpoint del bus** di servizio immettere le informazioni seguenti:

   **Nome endpoint:** nome dell'endpoint.

   **Spazio dei nomi del bus di** servizio: selezionare lo spazio dei nomi creato.

   **Coda del bus di** servizio: selezionare la coda creata.

   ![Aggiungere un endpoint all'hub IoT nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Selezionare **Crea**. Dopo aver creato l'endpoint, procedere con il passaggio successivo.

### <a name="add-a-routing-rule"></a>Aggiungere una regola di routing

1. Nel riquadro **Routing messaggi** selezionare la scheda **Route** e quindi selezionare **+ Aggiungi**.

1. Nel riquadro **Aggiungi una route** immettere le informazioni seguenti:

   **Nome**: il nome della regola di routing.

   **Endpoint**: selezionare l'endpoint creato.

   **Origine dati:** selezionare **Messaggi di telemetria del dispositivo**.

   **Query di routing:** immettere `temperatureAlert = "true"` .

   ![Aggiungere una regola di routing nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Selezionare **Salva**. È possibile chiudere il **riquadro Routing** messaggi.

## <a name="create-and-configure-a-logic-app"></a>Creare e configurare un'app per la logica

Nella sezione precedente si configura l'hub IoT per instradare i messaggi contenenti un avviso di temperatura alla coda del bus di servizio. A questo punto, si configura un'app per la logica per monitorare la coda del bus di servizio e inviare una notifica tramite posta elettronica ogni volta che viene aggiunto un messaggio alla coda.

### <a name="create-a-logic-app"></a>Creare un'app per la logica

1. Selezionare **Crea una risorsa App per la**  >  **logica** di  >  **integrazione.**

1. Immettere le informazioni seguenti:

   **Nome**: il nome dell'app per la logica.

   **Gruppo di risorse**: usare lo stesso gruppo di risorse usato dall'hub IoT.

   **Posizione**: usare la stessa posizione che usa l'hub IoT.

   ![Creare un'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Selezionare **Crea**.

### <a name="configure-the-logic-app-trigger"></a>Configurare il trigger dell'app per la logica

1. Aprire l'app per la logica. Il modo più semplice per accedere all'app per la logica è selezionare Gruppi di risorse nel riquadro delle risorse, selezionare il gruppo di risorse e quindi selezionare l'app per la logica dall'elenco delle risorse.  Quando si seleziona l'app per la logica, si apre Progettazione app per la logica.

1. In Progettazione app per la logica scorrere verso il basso fino a **Modelli** e selezionare **App per la logica vuota.**

   ![Nel portale di Azure iniziare con un'app per la logica vuota](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Selezionare la **scheda Tutti** e quindi selezionare Bus **di servizio.**

   ![Selezionare il bus di servizio per avviare la creazione dell'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. In **Trigger selezionare** **All'arrivo di uno o più messaggi in una coda (completamento automatico).**

   ![Selezionare il trigger per l'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Creare una connessione per il bus di servizio.
   1. Immettere un nome di connessione e selezionare lo spazio dei nomi del bus di servizio dall'elenco. Viene visualizzata la schermata successiva.

      ![Screenshot che evidenzia l'opzione All'arrivo di uno o più messaggi in una coda (completamento automatico).](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Selezionare i criteri del bus di servizio (RootManageSharedAccessKey). Selezionare quindi **Crea.**

      ![Creare una connessione del bus di servizio per l'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Nella schermata finale, per **Nome coda**, selezionare la coda creata dall'elenco a discesa. Immettere `175` per Numero massimo di **messaggi.**

      ![Specificare il numero massimo di messaggi per la connessione del bus di servizio nell'app per la logica](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Selezionare **Salva nel** menu nella parte superiore di Progettazione app per la logica per salvare le modifiche.

### <a name="configure-the-logic-app-action"></a>Configurare l'azione dell'app per la logica

1. Creare una connessione del servizio SMTP.

   1. Selezionare **Nuovo passaggio**. In **Scegliere un'azione** selezionare la **scheda** Tutti.

   1. Digitare `smtp` nella casella di ricerca, selezionare il servizio **SMTP** nel risultato della ricerca e quindi selezionare **Invia messaggio di posta elettronica**.

      ![Creare una connessione SMTP nell'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Immettere le informazioni SMTP per la cassetta postale e quindi selezionare **Crea**.

      ![Inserire le informazioni sulla connessione SMTP nell'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Ottenere le informazioni di SMTP per [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en) e [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).

      > [!NOTE]
      > Potrebbe essere necessario disabilitare TLS/SSL per stabilire la connessione. Se questo è il caso e si vuole ri-abilitare TLS dopo aver stabilito la connessione, vedere il passaggio facoltativo alla fine di questa sezione.

   1. **Nell'elenco a discesa Aggiungi nuovo** parametro nel passaggio Invia messaggio di posta elettronica selezionare **Da**, **A**, **Oggetto** e **Corpo**.  Toccare o fare clic in un punto qualsiasi dello schermo per chiudere la casella di selezione.

      ![Scegliere i campi di posta elettronica di connessione SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Immettere l'indirizzo di posta elettronica per **From** (Da) e **To** (A)e `High temperature detected` per **Oggetto** e **Corpo**. Se viene visualizzata la finestra di dialogo Aggiungi contenuto dinamico dalle app e **dai connettori usati in questo** flusso, selezionare **Nascondi** per chiuderlo. In questa esercitazione non si usa contenuto dinamico.

      ![Compilare i campi di posta elettronica di connessione SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Selezionare **Salva** per salvare la connessione SMTP.

1. (Facoltativo) Se è stato necessario disabilitare TLS per stabilire una connessione con il provider di posta elettronica e si vuole abilitarlo di nuovo, seguire questa procedura:

   1. Nel riquadro **App per la logica,** in **Strumenti di sviluppo,** selezionare **Connessioni API.**

   1. Nell'elenco delle connessioni API selezionare la connessione SMTP.

   1. Nel riquadro **Smtp API Connection (Connessione API smtp)** in **General (Generale)** selezionare **Edit API connection (Modifica connessione API).**

   1. Nel riquadro **Modifica connessione API** selezionare Abilita **SSL?**, immettere nuovamente la password per l'account di posta elettronica e selezionare **Salva**.

      ![Modificare la connessione all'API SMTP nell'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

L'app per la logica è ora pronta per elaborare gli avvisi relativi alla temperatura dalla coda del bus di servizio e inviare notifiche all'account di posta elettronica.

## <a name="test-the-logic-app"></a>Testare l'app per la logica

1. Avviare l'applicazione client nel dispositivo.

1. Se si usa un dispositivo fisico, portare con attenzione una fonte di calore vicino al sensore termico fino a quando la temperatura non supera i 30 gradi C. Se si usa il simulatore online, il codice client restituisce in modo casuale i messaggi di telemetria che superano i 30 C.

1. È consigliabile iniziare a ricevere notifiche tramite posta elettronica inviate dall'app per la logica.

   > [!NOTE]
   > Il provider di servizi di posta elettronica potrebbe dover verificare l'identità del mittente per verificare che sia l'invio del messaggio di posta elettronica viene eseguito dall'utente.

## <a name="next-steps"></a>Passaggi successivi

È stata creata correttamente un'app per la logica che connette l'hub IoT e la cassetta postale per il monitoraggio della temperatura e le notifiche.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]