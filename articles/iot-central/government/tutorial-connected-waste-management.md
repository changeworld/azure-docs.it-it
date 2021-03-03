---
title: "Esercitazione: Creare un'app di gestione rifiuti connessa con Azure IoT Central"
description: "Esercitazione: informazioni su come creare un'applicazione di gestione dello spreco connessa usando i modelli di applicazione di Azure IoT Central"
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: ff7cfb8c7aa8469111d4531da17c7cd184920f9b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727567"
---
# <a name="tutorial-create-a-connected-waste-management-app"></a>Esercitazione: Creare un'app di gestione rifiuti connessa

Questa esercitazione illustra come usare Azure IoT Central per creare un'applicazione di gestione rifiuti connessa. 

In particolare, si apprenderà come: 

> [!div class="checklist"]
> * Usare il modello *Gestione rifiuti connessa* di Azure IoT Central per creare l'app.
> * Esplorare e personalizzare il dashboard dell'operatore. 
> * Esplorare il modello di dispositivo cassonetto connesso.
> * Esplorare i dispositivi simulati.
> * Esplorare e configurare le regole.
> * Configurare i processi.
> * Modificare la personalizzazione dell'applicazione.

## <a name="prerequisites"></a>Prerequisiti

Una sottoscrizione di Azure (consigliata). In alternativa, è possibile usare una versione di valutazione gratuita valida per sette giorni. Se non si ha una sottoscrizione di Azure, è possibile crearne una nella [pagina di iscrizione ad Azure](https://aka.ms/createazuresubscription).

## <a name="create-your-app-in-azure-iot-central"></a>Creare l'app in Azure IoT Central

In questa sezione si userà il modello Gestione rifiuti connessa per creare l'app in Azure IoT Central. Ecco come:

1. Passare ad [Azure IoT Central](https://aka.ms/iotcentral).

    Se si ha una sottoscrizione di Azure, accedere con le credenziali usate per tale sottoscrizione, in caso contrario accedere con un account Microsoft:

    ![Screenshot dell'accesso a Microsoft.](./media/tutorial-connectedwastemanagement/sign-in.png)

1. Nel riquadro sinistro selezionare **Compila**. Selezionare quindi la scheda **Enti governativi**. Il riquadro Enti governativi mostra diversi modelli di applicazioni per enti pubblici.

    ![Screenshot della pagina Compila di Azure IoT Central.](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Selezionare il modello di applicazione **Gestione rifiuti connessa**. Questo modello include un modello di dispositivo per cassonetto connesso, un dispositivo simulato, un dashboard dell'operatore e regole di monitoraggio preconfigurate di esempio.    

1. Selezionare **Crea app** per aprire la finestra di dialogo **Nuova applicazione**. Immettere le informazioni per i campi seguenti:
    * **Nome applicazione**. Il nome predefinito dell'applicazione è **Gestione rifiuti connessa**, seguito da una stringa ID univoca generata da Azure IoT Central. Se si preferisce, è possibile scegliere un nome applicazione descrittivo. È anche possibile cambiare il nome dell'applicazione in un secondo momento.
    * **URL**. Se si preferisce, è possibile scegliere l'URL desiderato. L'URL può essere modificato in un secondo momento. 
    * **Piano tariffario**. Se si ha una sottoscrizione di Azure, immettere la directory, la sottoscrizione di Azure e l'area nei campi appropriati della finestra di dialogo **Informazioni di fatturazione**. Se non si ha una sottoscrizione, selezionare **Gratis** per abilitare la sottoscrizione di valutazione valida per sette giorni e immettere le informazioni di contatto richieste.  

    Per altre informazioni sulle sottoscrizioni e directory, vedere [Avvio rapido: Creare un'applicazione Azure IoT Central](../core/quick-deploy-iot-central.md).

1. Selezionare **Crea** nella parte inferiore della pagina. 

    ![Screenshot della finestra di dialogo Crea nuova applicazione di Azure IoT Central.](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Screenshot della finestra di dialogo Informazioni di fatturazione di Azure IoT Central.](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
L'applicazione appena creata è preconfigurata con gli elementi seguenti:
* Esempi di dashboard per operatore.
* Modelli predefiniti di dispositivo cassonetto connesso di esempio.
* Dispositivi cassonetto connesso simulati.
* Regole e processi.
* Personalizzazione di esempio. 

L'applicazione può essere modificata in qualsiasi momento. È quindi il momento di esplorare l'applicazione e personalizzarla.  

## <a name="explore-and-customize-the-operator-dashboard"></a>Esplorare e personalizzare il dashboard dell'operatore 

Esaminare il **dashboard di gestione rifiuti a livello mondiale**, che viene visualizzato dopo la creazione dell'app.

   ![Screenshot del dashboard di gestione Wide World Waste.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Tra le attività di compilazione, è possibile creare e personalizzare le visualizzazioni nel dashboard per gli operatori. Per prima cosa, è opportuno analizzare il dashboard. 

>[!NOTE]
>Tutti i dati visualizzati nel dashboard sono basati sui dati dei dispositivi simulati, che verranno descritti nella sezione successiva. 

Il dashboard è costituito da diversi riquadri:

* **Riquadro dell'immagine relativa all'utilità Wide World Waste**: il primo riquadro del dashboard presenta l'immagine di un'utilità di gestione rifiuti fittizia denominata "Wide World Waste". È possibile personalizzare il riquadro, inserendo un'immagine personalizzata, oppure rimuoverlo. 

* **Riquadro dell'immagine del cassonetto**: è possibile usare i riquadri di immagine e contenuto per creare una rappresentazione visiva del dispositivo monitorato, unitamente a una descrizione. 

* **Riquadro dell'indicatore KPI relativo al livello di colmo**: questo riquadro visualizza un valore restituito da un sensore *Fill level* (Livello di colmo) presente in un cassonetto. Il sensore Fill level (Livello di colmo) e altri sensori come *Odor meter* (Misuratore odori) o *Weight* (Peso) in un cassonetto possono essere monitorati da remoto. Un operatore può eseguire un'azione, ad esempio inviare un camion della raccolta di rifiuti. 

* **Mappa dell'area di monitoraggio dei rifiuti**: questo riquadro usa Mappe di Azure, che è possibile configurare direttamente in Azure IoT Central. Il riquadro della mappa visualizza la posizione del dispositivo. Passare il puntatore del mouse sulla mappa e provare i controlli sulla mappa, ad esempio zoom avanti, zoom indietro o espansione.

     ![Screenshot della mappa del dashboard del modello Gestione rifiuti connessa.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* **Grafico a barre relativo al livello di colmo, odori e peso**: è possibile visualizzare uno o più tipi di dati di telemetria del dispositivo in un grafico a barre. È anche possibile espandere il grafico a barre.  

  ![Screenshot del grafico a barre del dashboard del modello Gestione rifiuti connessa.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Field Service**: il dashboard include un collegamento per l'integrazione con Dynamics 365 for Field Service dall'applicazione di Azure IoT Central. È ad esempio possibile usare Dynamics 365 for Field Service per creare ticket per l'invio di servizi di raccolta rifiuti. 


### <a name="customize-the-dashboard"></a>Personalizzare il dashboard 

È possibile personalizzare il dashboard selezionando il menu **Modifica**. È quindi possibile aggiungere nuovi riquadri o configurare quelli esistenti. Ecco come appare il dashboard in modalità di modifica: 

![Screenshot del grafico a barre del dashboard del modello Gestione rifiuti connessa in modalità di modifica.](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

È anche possibile selezionare **+ Nuovo** per creare un nuovo dashboard e configurarlo da zero. È possibile creare più dashboard e spostarsi tra essi dal menu Dashboard. 

## <a name="explore-the-device-template"></a>Esplorare il modello di dispositivo

Un modello di dispositivo in Azure IoT Central definisce le funzionalità di un dispositivo, che possono includere dati di telemetria, proprietà o comandi. A livello di compilazione, è possibile definire modelli di dispositivo che rappresentano le funzionalità dei dispositivi connessi. 

L'applicazione Gestione rifiuti connessa include un modello di esempio per dispositivo cassonetto connesso.

Per visualizzare il modello di dispositivo:

1. In Azure IoT Central selezionare **Modelli di dispositivo** nel riquadro sinistro dell'app. 

    ![Screenshot dell'elenco di modelli di dispositivo nell'applicazione.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

1. Nell'elenco **Modelli di dispositivo** selezionare **Connected Waste Bin** (Cassonetto connesso).

1. Esaminare le funzionalità del modello di dispositivo. Come si può vedere, il modello definisce diversi sensori, tra cui **Fill level** (Livello di colmo), **Odor meter** (Misuratore odori), **Weight** (Peso) e **Location** (Posizione).

   ![Screenshot dei dettagli del modello di dispositivo Connected Waste Bin.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)


### <a name="customize-the-device-template"></a>Personalizzare il modello di dispositivo

Provare a personalizzare gli elementi seguenti:
1. Scegliere **Personalizza** dal menu del modello di dispositivo.
1. Individuare il tipo di dati di telemetria **Odor meter** (Contatore odori).
1. Aggiornare il campo **Nome visualizzato** di **Odor meter** (Contatore odori) in **Odor level** (Livello odori).
1. Provare ad aggiornare l'unità di misura oppure impostare **Valore minimo** e **Valore massimo**.
1. Selezionare **Salva**. 

### <a name="add-a-cloud-property"></a>Aggiungere una proprietà cloud 

Ecco come:
1. Scegliere **Proprietà cloud** dal menu del modello di dispositivo.
1. Selezionare **+ Aggiungi proprietà cloud**. In Azure IoT Central è possibile aggiungere una proprietà pertinente per un dispositivo, ma che non si prevede venga inviata da un dispositivo. Un esempio di proprietà cloud può essere una soglia di avviso specifica per l'area di installazione, informazioni sulle risorse, informazioni di manutenzione. 
1. Selezionare **Salva**. 
 
### <a name="views"></a>Viste 
Il modello di dispositivo cassonetto connesso include visualizzazioni predefinite. Esplorare le visualizzazioni e aggiornarle, se necessario. Le visualizzazioni consentono di definire il modo in cui gli operatori visualizzano i dati del dispositivo e immettono le proprietà cloud. 

  ![Screenshot delle visualizzazioni dei modelli di dispositivo del modello Gestione rifiuti connessa.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Pubblica 

Se sono state apportate modifiche, ricordarsi di pubblicare il modello di dispositivo. 

### <a name="create-a-new-device-template"></a>Creare un nuovo modello di dispositivo 

Per creare un nuovo modello di dispositivo, selezionare **+ Nuovo** e seguire la procedura. È possibile creare un modello di dispositivo personalizzato da zero oppure sceglierne uno dal catalogo dei dispositivi di Azure. 

## <a name="explore-simulated-devices"></a>Esplorare i dispositivi simulati

In Azure IoT Central è possibile creare dispositivi simulati per testare il proprio modello di dispositivo e l'applicazione. 

L'applicazione Gestione rifiuti connessa include due dispositivi simulati associati al modello di dispositivo per cassonetto connesso. 

### <a name="view-the-devices"></a>Visualizzare i dispositivi

1. Nel riquadro sinistro di Azure IoT Central selezionare **Dispositivo**. 

   ![Screenshot dei dispositivi del modello Gestione rifiuti connessa.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

1. Selezionare il dispositivo **Connected Waste Bin** (Cassonetto connesso).  

     ![Screenshot delle proprietà del dispositivo modello Gestione rifiuti connessa.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

1. Passare alla scheda **Proprietà cloud**. Aggiornare il valore di **Bin full alert threshold** (Soglia di avviso cassonetto pieno) da **95** a **100**. 

Esplorare le schede **Proprietà dispositivo** e **Device Dashboard** (Dashboard dispositivo). 

> [!NOTE]
> Tutte le schede sono state configurate dalle visualizzazioni del modello di dispositivo.

### <a name="add-new-devices"></a>Aggiungere nuovi dispositivi

Per aggiungere nuovi dispositivi, selezionare **+ Nuovo** nella scheda **Dispositivi**. 

## <a name="explore-and-configure-rules"></a>Esplorare e configurare le regole

In Azure IoT Central è possibile creare regole per monitorare automaticamente i dati di telemetria del dispositivo e attivare azioni quando vengono soddisfatte una o più condizioni. Le azioni possono includere l'invio di notifiche tramite posta elettronica o l'attivazione di un'azione in Power Automate o l'avvio di un'azione webhook per l'invio dei dati ad altri servizi.

L'applicazione Gestione rifiuti connessa ha quattro regole di esempio.

### <a name="view-rules"></a>Visualizzare le regole
1. Nel riquadro sinistro di Azure IoT Central selezionare **Regole**.

   ![Screenshot delle regole del modello Gestione rifiuti connessa.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

1. Selezionare **Bin full alert** (Avviso cassonetto pieno).

     ![Screenshot dell'avviso per cassonetto pieno.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 1. La regola **Bin full alert** (Avviso cassonetto pieno) verifica la condizione seguente: **Fill level is greater than or equal to Bin full alert threshold** (Il livello di colmo è maggiore o uguale alla soglia di avviso per cassonetto pieno).

    **Bin full alert threshold** (Soglia di avviso per cassonetto pieno) è una proprietà cloud definita nel modello di dispositivo Connected Waste Bin (Cassonetto connesso). 

Si creerà ora un'azione di posta elettronica.

### <a name="create-an-email-action"></a>Creare un'azione di posta elettronica

Nell'elenco **Azioni** della regola è possibile configurare un'azione di posta elettronica:
1. Selezionare **+ Posta elettronica**. 
1. In **Nome visualizzato** immettere **High pH alert** (Avviso per pH elevato).
1. In **A** immettere l'indirizzo di posta elettronica associato all'account Azure IoT Central. 
1. Se si vuole, immettere una nota da includere nel testo del messaggio di posta elettronica.
1. Selezionare **Fine** > **Salva**. 

A questo punto, quando la condizione configurata viene soddisfatta, si riceverà un messaggio di posta elettronica.

>[!NOTE]
>L'applicazione invia un messaggio ogni volta che viene soddisfatta una condizione. Selezionare Disabilita se non si vogliono più ricevere messaggi di posta elettronica dalla regola automatica. 
  
Per creare una nuova regola, nel riquadro sinistro di **Regole** selezionare **+ Nuovo**.

## <a name="configure-jobs"></a>Configurare i processi

In Azure IoT Central i processi consentono di attivare gli aggiornamenti delle proprietà del dispositivo o cloud su più dispositivi. È anche possibile usare i processi per attivare i comandi di dispositivo su più dispositivi. Azure IoT Central automatizza il flusso di lavoro. 

1. Nel riquadro sinistro di Azure IoT Central selezionare **Processi**. 
1. Selezionare **+ Nuovo** e configurare uno o più processi. 

## <a name="customize-your-application"></a>Personalizzare l'applicazione 

In qualità di autore, è possibile modificare diverse impostazioni per personalizzare l'esperienza utente nell'applicazione.

### <a name="change-the-application-theme"></a>Cambiare il tema dell'applicazione

Ecco come:
1. Passare ad **Amministrazione** > **Personalizzare l'applicazione**.
1. Selezionare **Cambia** per scegliere un'immagine da caricare come **logo dell'applicazione**.
1. Selezionare **Cambia** per scegliere un'immagine da caricare come **icona del browser** (un'immagine che verrà visualizzata nelle schede del browser).
1. È anche possibile sostituire i colori predefiniti del browser con codici colore esadecimali HTML. A questo scopo, usare i campi **Intestazione** e **Evidenziatore**.

   ![Screenshot della pagina Personalizzare l'applicazione del modello Gestione rifiuti connessa.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

1. È anche possibile cambiare le immagini dell'applicazione. Selezionare **Amministrazione** > **Impostazioni applicazione** > **Seleziona immagine** per scegliere un'immagine da caricare come immagine dell'applicazione.
1. È infine possibile cambiare anche il tema selezionando **Impostazioni** nella testata dell'applicazione.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminarla seguendo questa procedura:

1. Nel riquadro sinistro dell'app Azure IoT Central selezionare **Amministrazione**.
1. Selezionare **Impostazioni applicazione** > **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [concetti sulla gestione di rifiuti connessa](./concepts-connectedwastemanagement-architecture.md)
