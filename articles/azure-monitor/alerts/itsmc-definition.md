---
title: IT Service Management Connector in Log Analytics
description: Questo articolo fornisce una panoramica di IT Service Management Connector (connettore) e informazioni su come usarlo per monitorare e gestire gli elementi di lavoro ITSM in Log Analytics e risolvere rapidamente i problemi.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 98f53ec1b6506a6d47146377e837576254f445e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103601067"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-solution"></a>Connettere Azure agli strumenti ITSM usando la soluzione IT Service Management

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Questo articolo fornisce informazioni su come configurare IT Service Management Connector (connettore) in Log Analytics per gestire in modo centralizzato gli elementi di lavoro ITSM (IT Service Management).

## <a name="add-it-service-management-connector"></a>Aggiungi IT Service Management Connector

Prima di poter creare una connessione, è necessario installare connettore.

1. Nella portale di Azure selezionare **Crea una risorsa**:

   ![Screenshot che mostra la voce di menu per la creazione di una risorsa.](media/itsmc-overview/azure-add-new-resource.png)

2. Cercare **IT Service Management Connector** in Azure Marketplace. quindi selezionare **Crea**:

   ![Screenshot che mostra il pulsante Crea in Azure Marketplace.](media/itsmc-overview/add-itsmc-solution.png)

3. Nella sezione **area di lavoro la** selezionare l'area di lavoro log Analytics in cui si desidera installare connettore.
   > [!NOTE]
   > È possibile installare connettore nelle aree di lavoro Log Analytics solo nelle aree seguenti: Stati Uniti orientali, Stati Uniti occidentali 2, Stati Uniti centro-meridionali, Stati Uniti centro-occidentali, US Gov Arizona, US Gov Virginia, Canada centrale, Europa occidentale, Regno Unito meridionale, Asia sudorientale, Giappone orientale, India centrale e Australia sudorientale.

4. Nella sezione **area di lavoro log Analytics** selezionare il gruppo di risorse in cui si vuole creare la risorsa connettore:

   ![Screenshot che mostra la sezione area di lavoro Log Analytics.](media/itsmc-overview/itsmc-solution-workspace.png)
   
   > [!NOTE]
   > Nell'ambito della transizione continua da Microsoft Operations Management Suite (OMS) a monitoraggio di Azure, le aree di lavoro di OMS sono ora denominate *log Analytics aree di lavoro*.

5. Selezionare **OK**.

Quando viene distribuita la risorsa connettore, viene visualizzata una notifica nell'angolo superiore destro della finestra.

## <a name="create-an-itsm-connection"></a>Creare una connessione di Gestione dei servizi IT

Dopo aver installato connettore, è necessario preparare lo strumento ITSM per consentire la connessione da connettore. In base al prodotto ITSM a cui si sta effettuando la connessione, selezionare uno dei seguenti collegamenti per istruzioni:

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

Dopo aver preparato lo strumento ITSM, completare la procedura seguente per creare una connessione:

1. In **tutte le risorse** cercare **ServiceDesk (*nome dell'area di lavoro*)**:

   ![Screenshot che mostra le risorse recenti nel portale di Azure.](media/itsmc-definition/create-new-connection-from-resource.png)

1. In **origini dati dell'area di lavoro** nel riquadro sinistro selezionare **connessioni ITSM**:

   ![Screenshot che mostra la voce di menu connessioni ITSM.](media/itsmc-overview/add-new-itsm-connection.png)

1. Selezionare **Aggiungi connessione**.

1. Specificare le impostazioni di connessione in base al prodotto ITSM che si sta usando:

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   > Per impostazione predefinita, connettore aggiorna i dati di configurazione della connessione ogni 24 ore. Per aggiornare immediatamente i dati della connessione in modo da riflettere le modifiche o gli aggiornamenti di modelli apportati, selezionare il pulsante **Sincronizza** nel riquadro della connessione:
   >
   > ![Screenshot che mostra il pulsante Sincronizza nel riquadro della connessione.](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="create-itsm-work-items-from-azure-alerts"></a>Creare elementi di lavoro di Gestione dei servizi IT dagli avvisi di Azure

Dopo aver creato la connessione ITSM, è possibile usare ITMC per creare elementi di lavoro nello strumento ITSM in base agli avvisi di Azure. Per creare gli elementi di lavoro, usare l'azione ITSM nei gruppi di azioni.

I gruppi di azioni forniscono un metodo modulare e riutilizzabile per attivare le azioni per gli avvisi di Azure. È possibile usare i gruppi di azioni con avvisi metrica, avvisi del log attività e avvisi Log Analytics nel portale di Azure.

> [!NOTE]
> Dopo aver creato la connessione ITSM, è necessario attendere 30 minuti per il completamento del processo di sincronizzazione.

## <a name="define-a-template"></a>Definire un modello

Alcuni tipi di elemento di lavoro possono usare i modelli definiti nello strumento ITSM. Utilizzando i modelli, è possibile definire i campi che verranno popolati automaticamente in base ai valori fissi per un gruppo di azioni. È possibile definire il modello che si desidera utilizzare come parte della definizione di un gruppo di azioni. Per informazioni su come creare i modelli, vedere in ServiceNow docs [ https://docs.servicenow.com/bundle/paris-platform-administration/page/administer/form-administration/task/t_CreateATemplateUsingTheTmplForm.html ].

Per creare un gruppo di azione:

1. Nella portale di Azure selezionare  **avvisi**.
2. Nel menu nella parte superiore della schermata selezionare **Gestisci azioni**:

    ![Screenshot che mostra la voce di menu Gestisci azioni.](media/itsmc-overview/action-groups-selection-big.png)

   Viene visualizzata la finestra **Crea gruppo di azioni** .

3. Selezionare la **sottoscrizione** e il **gruppo di risorse** in cui si vuole creare il gruppo di azioni. Specificare i valori nel **nome del gruppo di azioni** e il **nome visualizzato** per il gruppo di azioni. Quindi selezionare **Avanti: notifiche**.

    ![Screenshot che mostra la finestra Crea gruppo di azioni.](media/itsmc-overview/action-groups-details.png)

4. Nella scheda **notifiche** selezionare **Avanti: azioni**.
5. Nella scheda **azioni** selezionare **ITSM** nell'elenco tipo di **azione** . Per **nome** specificare un nome per l'azione. Quindi selezionare il pulsante penna che rappresenta **i dettagli di modifica**.

    ![Screenshot che mostra le selezioni per la creazione di un gruppo di azioni.](media/itsmc-definition/action-group-pen.png)

6. Nell'elenco **sottoscrizione** selezionare la sottoscrizione che contiene l'area di lavoro log Analytics. Nell'elenco **connessione** selezionare il nome del connettore ITSM. Sarà seguito dal nome dell'area di lavoro. Un esempio è *MyITSMConnector (area di lavoro)*.

7. Selezionare un tipo di **elemento di lavoro** .

8. Se si desidera compilare i campi predefiniti con valori fissi, selezionare **Usa modello personalizzato**. In caso contrario, scegliere un [modello](#define-a-template) esistente nell'elenco dei **modelli** e immettere i valori fissi nei campi del modello.

9. Nell'ultima sezione dell'interfaccia per la creazione di un gruppo di azioni ITSM è possibile definire il numero di elementi di lavoro che verranno creati per ogni avviso.

   > [!NOTE]
   > Questa sezione è pertinente solo per gli avvisi di ricerca nei log. Per tutti gli altri tipi di avviso verrà creato un elemento di lavoro per ogni avviso.

   * Se nell'elenco a discesa **elemento di lavoro** è stato selezionato **evento imprevisto** o **avviso** , è possibile creare singoli elementi di lavoro per ogni elemento di configurazione.
    
     ![Screenshot che mostra l'area del ticket I T S con evento imprevisto selezionato come elemento di lavoro.](media/itsmc-overview/itsm-action-configuration.png)
    
     * Se si seleziona la casella **di controllo Crea elementi di lavoro singoli per ogni elemento di configurazione** , ogni elemento di configurazione in ogni avviso creerà un nuovo elemento di lavoro. Poiché si verificheranno diversi avvisi per gli stessi elementi di configurazione interessati, saranno presenti più elementi di lavoro per ogni elemento di configurazione.

       Ad esempio, un avviso con tre elementi di configurazione creerà tre elementi di lavoro. Un avviso che include un elemento di configurazione creerà un elemento di lavoro.
        
     * Se si deseleziona la casella di controllo **Crea elementi di lavoro singoli per ogni elemento di configurazione** , connettore creerà un singolo elemento di lavoro per ogni regola di avviso e vi aggiungerà tutti gli elementi di configurazione interessati. Verrà creato un nuovo elemento di lavoro se quello precedente è chiuso.

       >[!NOTE]
       > In questo caso, alcuni degli avvisi attivati non genereranno nuovi elementi di lavoro nello strumento ITSM.

       Ad esempio, un avviso con tre elementi di configurazione creerà un elemento di lavoro. Se un avviso per la stessa regola di avviso dell'esempio precedente include un elemento di configurazione, tale elemento di configurazione verrà allegato all'elenco degli elementi di configurazione interessati nell'elemento di lavoro creato. Un avviso per una regola di avviso diversa che include un elemento di configurazione creerà un elemento di lavoro.

   * Se è stato selezionato **evento** nell'elenco a discesa **elemento di lavoro** , è possibile scegliere di creare singoli elementi di lavoro per ogni voce di log o per ogni elemento di configurazione.
    
     ![Screenshot che mostra l'area del ticket I T S con l'evento selezionato come elemento di lavoro.](media/itsmc-overview/itsm-action-configuration-event.png)

     * Se si seleziona **Crea elementi di lavoro singoli per ogni voce di log (il campo elemento di configurazione non è pieno. Può generare un numero elevato di elementi di lavoro.)**, verrà creato un elemento di lavoro per ogni riga nei risultati della ricerca della query di avviso di ricerca log. La proprietà Description nel payload dell'elemento di lavoro conterrà la riga dei risultati della ricerca.
      
     * Se si seleziona **Crea elementi di lavoro singoli per ogni elemento di configurazione**, ogni elemento di configurazione in ogni avviso creerà un nuovo elemento di lavoro. Ogni elemento di configurazione può avere più di un elemento di lavoro nel sistema ITSM. Questa opzione è identica alla selezione della casella di controllo che viene visualizzata dopo aver selezionato **evento imprevisto** come tipo di elemento di lavoro.

10. Selezionare **OK**.

Quando si crea o si modifica una regola di avviso di Azure, usare un gruppo di azioni con un'azione ITSM. Quando l'avviso viene attivato, l'elemento di lavoro viene creato o aggiornato nello strumento ITSM.

> [!NOTE]
> Per informazioni sui prezzi dell'azione ITSM, vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/monitor/) per i gruppi di azioni.
>
> Il campo Descrizione breve nella definizione della regola di avviso è limitato a 40 caratteri quando lo si invia usando l'azione ITSM.

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi in connettore](./itsmc-resync-servicenow.md)
