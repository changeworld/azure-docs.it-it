---
title: IT Service Management Connector in Log Analytics
description: Questo articolo fornisce una panoramica di IT Service Management Connector (connettore) e informazioni su come usarlo per monitorare e gestire gli elementi di lavoro ITSM in Log Analytics e risolvere rapidamente i problemi.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: a7e39bb8ed742007a13a222771b430372d50e889
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98071698"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>Connettere Azure agli strumenti ITSM usando IT Service Management Connector

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Questo articolo fornisce informazioni su come configurare IT Service Management Connector (connettore) in Log Analytics per gestire centralmente gli elementi di lavoro.

## <a name="add-it-service-management-connector"></a>Aggiungi IT Service Management Connector

Prima di poter creare una connessione, è necessario aggiungere connettore.

1. Nella portale di Azure selezionare **Crea una risorsa**:

   ![Screenshot che mostra la voce di menu Crea una risorsa.](media/itsmc-overview/azure-add-new-resource.png)

2. Cercare **IT Service Management Connector** in Azure Marketplace. Selezionare **Crea**:

   ![Screenshot che mostra il pulsante Crea in Azure Marketplace.](media/itsmc-overview/add-itsmc-solution.png)

3. Nella sezione **area di lavoro** , selezionare l'area di lavoro log Analytics di Azure in cui si vuole installare connettore.
   >[!NOTE]
   >
   > * CONNETTORE può essere installato solo nelle aree di lavoro Log Analytics nelle aree seguenti: Stati Uniti orientali, Stati Uniti occidentali 2, Stati Uniti centro-meridionali, Stati Uniti centro-occidentali, US Gov Arizona, US Gov Virginia, Canada centrale, Europa occidentale, Regno Unito meridionale, Asia sudorientale, Giappone orientale, India centrale e Australia sudorientale.

4. Nella sezione **area di lavoro log Analytics** selezionare il gruppo di risorse in cui si vuole creare la risorsa connettore:

   ![Screenshot che mostra la sezione area di lavoro Log Analytics.](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >Nell'ambito della transizione continua da Microsoft Operations Management Suite (OMS) a monitoraggio di Azure, le aree di lavoro di OMS sono ora denominate *log Analytics aree di lavoro*.

5. Selezionare **OK**.

Quando viene distribuita la risorsa connettore, viene visualizzata una notifica nell'angolo superiore destro della finestra.

## <a name="create-an-itsm-connection"></a>Creare una connessione di Gestione dei servizi IT

Dopo aver installato connettore, è possibile creare una connessione.

Per creare una connessione, è necessario preparare lo strumento ITSM per consentire la connessione da connettore.  

In base al prodotto ITSM a cui si sta effettuando la connessione, selezionare uno dei collegamenti seguenti per le istruzioni:

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

Dopo aver preparato gli strumenti ITSM, completare la procedura seguente per creare una connessione:

1. In **tutte le risorse** cercare **ServiceDesk (*nome dell'area di lavoro*)**:

   ![Screenshot che mostra le risorse recenti nel portale di Azure.](media/itsmc-definition/create-new-connection-from-resource.png)

1. In **origini dati dell'area di lavoro** nel riquadro sinistro selezionare **connessioni ITSM**:

   ![Screenshot che mostra la voce di menu connessioni ITSM.](media/itsmc-overview/add-new-itsm-connection.png)
1. Selezionare **Aggiungi connessione**.

1. Specificare le impostazioni di connessione descritte in base ai prodotti/servizi ITSM:

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   >
   > Per impostazione predefinita, connettore aggiorna i dati di configurazione della connessione ogni 24 ore. Per aggiornare immediatamente i dati della connessione in modo da riflettere le modifiche o gli aggiornamenti di modelli apportati, selezionare il pulsante **Sincronizza** nel pannello della connessione:
   >
   > ![Screenshot che mostra il pulsante Sincronizza nel pannello connessione.](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="use-itsmc"></a>Usare connettore

   È possibile usare connettore per creare avvisi dagli avvisi di monitoraggio di Azure nello strumento ITSM.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Creare elementi di lavoro di Gestione dei servizi IT dagli avvisi di Azure

Dopo aver creato la connessione ITSM, è possibile creare elementi di lavoro nello strumento ITSM in base agli avvisi di Azure. Per creare gli elementi di lavoro, usare l'azione ITSM nei gruppi di azioni.

I gruppi di azioni forniscono un metodo modulare e riutilizzabile per attivare le azioni per gli avvisi di Azure. È possibile usare i gruppi di azioni con avvisi metrica, avvisi del log attività e avvisi di Azure Log Analytics nel portale di Azure.

> [!NOTE]
> Dopo aver creato la connessione ITSM, è necessario attendere 30 minuti per il completamento del processo di sincronizzazione.

### <a name="template-definitions"></a>Definizioni di modello

   Sono disponibili tipi di elemento di lavoro che possono usare i modelli definiti dallo strumento ITSM.
Utilizzando i modelli, è possibile definire i campi che verranno popolati automaticamente in base ai valori fissi definiti come parte del gruppo di azioni. I modelli vengono definiti nello strumento ITSM.
È possibile definire il modello che si desidera utilizzare come parte della definizione del gruppo di azioni.

Utilizzare la seguente procedura per creare i gruppi di azioni:

1. Nella portale di Azure selezionare  **avvisi**.
2. Nel menu nella parte superiore della schermata selezionare **Gestisci azioni**:

    ![Screenshot che mostra la voce di menu Gestisci azioni.](media/itsmc-overview/action-groups-selection-big.png)

   Viene visualizzata la finestra **Crea gruppo di azioni** .

3. Selezionare la **sottoscrizione** e il **gruppo di risorse** in cui si vuole creare il gruppo di azioni. Specificare il **nome del gruppo di azioni** e il **nome visualizzato** per il gruppo di azioni. Selezionare **Avanti: notifiche**.

    ![Screenshot che mostra la finestra Crea gruppo di azioni.](media/itsmc-overview/action-groups-details.png)

4. Nell'elenco delle notifiche selezionare **Avanti: azioni**.
5. Nell'elenco azioni selezionare **ITSM** nell'elenco tipo di **azione** . Consente di specificare un **nome** per l'azione. Selezionare il pulsante penna che rappresenta **i dettagli di modifica**.

    ![Screenshot che mostra la definizione del gruppo di azioni.](media/itsmc-definition/action-group-pen.png)

6. Nell'elenco **sottoscrizione** selezionare la sottoscrizione in cui si trova l'area di lavoro log Analytics. Nell'elenco **connessione** selezionare il nome del connettore ITSM. Sarà seguito dal nome dell'area di lavoro. Ad esempio, MyITSMConnector (area di lavoro).

7. Selezionare un tipo di **elemento di lavoro** .

8. Se si desidera compilare i campi predefiniti con valori fissi, selezionare **Usa modello personalizzato**. In caso contrario, scegliere un [modello](#template-definitions) esistente nell'elenco dei **modelli** e immettere i valori fissi nei campi del modello.

9. Nell'ultima sezione della definizione del gruppo ITSM azione è possibile definire il numero di elementi di lavoro che verranno creati per ogni avviso.

    >[!NOTE]
    >
    > * Questa sezione è pertinente solo per gli avvisi di ricerca nei log.
    > * Per tutti gli altri tipi di avviso, viene creato un elemento di lavoro per ogni avviso.

    * In un caso, è possibile selezionare nell'elenco a discesa "elemento di lavoro" "evento imprevisto" o "avviso": ![ screenshot che mostra la finestra evento imprevisto ITSM.](media/itsmc-overview/itsm-action-configuration.png)
        * Se si seleziona la casella di controllo **"Crea elementi di lavoro singoli per ogni elemento di configurazione"** , ogni elemento di configurazione in ogni avviso creerà un nuovo elemento di lavoro. In seguito a diversi avvisi per gli stessi elementi di configurazione interessati, saranno presenti più elementi di lavoro per ogni elemento di configurazione.

             Esempio:
             1) Avviso 1 con 3 elementi di configurazione: A, B, C-creerà 3 elementi di lavoro.
             2) Avviso 2 con 1 elemento di configurazione: A-creerà 1 elemento di lavoro.

        * Se si deseleziona la casella di controllo **"Crea elementi di lavoro singoli per ogni elemento di configurazione"** , il connettore ITSM creerà un singolo elemento di lavoro per ogni regola di avviso e vi aggiungerà tutti gli elementi di configurazione interessati. Verrà creato un nuovo elemento di lavoro se quello precedente è chiuso.

        >[!NOTE]
        > In questo caso parte dell'avviso attivato non genererà nuovi elementi di lavoro nello strumento ITSM.

        Esempio:
         1) Avviso 1 con 3 elementi di configurazione: A, B, C-creerà 1 elemento di lavoro.
         2) Avviso 2 per la stessa regola di avviso del passaggio a con 1 elemento di configurazione: D-D verrà allegato all'elenco degli elementi di configurazione interessati nell'elemento di lavoro creato nel passaggio a.
         3) Avviso 3 per una regola di avviso diversa con 1 elemento di configurazione: E-creerà 1 elemento di lavoro.

    * Se si seleziona nell'elenco a discesa "elemento di lavoro" "evento": ![ screenshot che mostra la finestra dell'evento ITSM.](media/itsmc-overview/itsm-action-configuration-event.png)

        * Se si seleziona **"Crea elementi di lavoro singoli per ogni voce di log (il campo elemento di configurazione non è pieno. Può generare un numero elevato di elementi di lavoro.) "** nella selezione dei pulsanti di opzione viene creato un elemento di lavoro per ogni riga nei risultati della ricerca della query di avviso di ricerca log. Nel payload dell'elemento di lavoro la proprietà Description avrà la riga dei risultati della ricerca.
        * Se si seleziona **"Crea elementi di lavoro singoli per ogni elemento di configurazione"** nella selezione dei pulsanti di opzione, ogni elemento di configurazione in ogni avviso creerà un nuovo elemento di lavoro. Nel sistema ITSM possono essere presenti più elementi di lavoro per ogni elemento di configurazione. Corrisponde alla casella di controllo verifica nella sezione evento imprevisto/avviso.

10. Selezionare **OK**.

Quando si crea o si modifica una regola di avviso di Azure, usare un gruppo di azioni con un'azione ITSM. Quando l'avviso viene attivato, l'elemento di lavoro viene creato o aggiornato nello strumento ITSM.

> [!NOTE]
>
>- Per informazioni sui prezzi dell'azione ITSM, vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/monitor/) per i gruppi di azioni.
>
>
>- Il campo Descrizione breve nella definizione della regola di avviso è limitato a 40 caratteri quando lo si invia usando l'azione ITSM.

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi di Connettore di Gestione dei servizi IT](./itsmc-resync-servicenow.md)
