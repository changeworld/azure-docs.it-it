---
title: Come creare distribuzioni di aggiornamenti per Automazione di Azure Gestione aggiornamenti
description: Questo articolo descrive come pianificare le distribuzioni degli aggiornamenti ed esaminarne lo stato.
services: automation
ms.subservice: update-management
ms.date: 04/19/2021
ms.topic: conceptual
ms.openlocfilehash: e410e5de529bde122fe42d21b593a6fc483dcbc0
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726696"
---
# <a name="how-to-deploy-updates-and-review-results"></a>Come distribuire gli aggiornamenti ed esaminare i risultati

Questo articolo descrive come pianificare una distribuzione degli aggiornamenti ed esaminare il processo al termine della distribuzione. È possibile configurare una distribuzione degli aggiornamenti da una macchina virtuale di Azure selezionata, dal server abilitato per Arc selezionato o dall'account di Automazione in tutti i computer e i server configurati.

In ogni scenario, la distribuzione viene creata come destinazione del computer o del server selezionato oppure, nel caso della creazione di una distribuzione dall'account di Automazione, è possibile scegliere come destinazione uno o più computer. Quando si pianifica una distribuzione di aggiornamenti da una macchina virtuale di Azure o da un server abilitato per Arc, i passaggi sono gli stessi della distribuzione dall'account di Automazione, con le eccezioni seguenti:

* Il sistema operativo viene automaticamente pre-selezionato in base al sistema operativo del computer
* Il computer di destinazione da aggiornare è impostato automaticamente come destinazione
* Quando si configura la pianificazione, è possibile specificare **Aggiorna ora**, si verifica una sola volta o usa una pianificazione ricorrente.

> [!IMPORTANT]
> Creando una distribuzione di aggiornamenti, si accettano le condizioni di licenza software fornite dalla società che offre aggiornamenti per il sistema operativo.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>Pianificare la distribuzione degli aggiornamenti

La pianificazione di una [](../shared-resources/schedules.md) distribuzione degli aggiornamenti crea una risorsa di pianificazione collegata al runbook **Patch-MicrosoftOMSComputers** che gestisce la distribuzione degli aggiornamenti nel computer o nei computer di destinazione. È necessario pianificare una distribuzione che rispetti la pianificazione delle versioni e l'intervallo di servizio per installare gli aggiornamenti. È possibile scegliere i tipi di aggiornamento da includere nella distribuzione. Ad esempio, è possibile includere aggiornamenti critici o di sicurezza ed escludere gli aggiornamenti cumulativi.

>[!NOTE]
>Se si elimina la risorsa di tipo pianificazione dal portale di Azure o si usa PowerShell dopo aver creato la distribuzione, l'eliminazione interrompe la distribuzione degli aggiornamenti pianificati e viene visualizzato un errore quando si prova a riconfigurare la risorsa di pianificazione dal portale. È possibile eliminare la risorsa di tipo pianificazione solo eliminando la pianificazione di distribuzione corrispondente.  

Per pianificare una nuova distribuzione degli aggiornamenti, seguire questa procedura. A seconda della risorsa selezionata,ovvero account di Automazione, server abilitato per Arc, macchina virtuale di Azure, i passaggi seguenti si applicano a tutti con differenze secondarie durante la configurazione della pianificazione della distribuzione.

1. Nel portale, per pianificare una distribuzione per:

   * Uno o più computer, passare ad Account **di Automazione e** selezionare l'account di Automazione con Gestione aggiornamenti abilitato dall'elenco.
   * Per una macchina virtuale di Azure, **passare a Macchine virtuali** e selezionare la macchina virtuale dall'elenco.
   * Per un server abilitato per Arc, passare **a Server - Azure Arc** e selezionare il server dall'elenco.

2. A seconda della risorsa selezionata, per passare a Gestione aggiornamenti:

   * Se è stato selezionato l'account di Automazione, passare a **Gestione aggiornamenti** in Gestione **aggiornamenti** e quindi selezionare Pianifica **distribuzione aggiornamenti.**
   * Se è stata selezionata una macchina virtuale di Azure, passare a **Guest e aggiornamenti host** e quindi selezionare Vai a **Gestione aggiornamenti**.
   * Se è stato selezionato un server abilitato per Arc, **passare Gestione aggiornamenti** e quindi selezionare Pianifica **distribuzione aggiornamenti**.

3. In **Nuova distribuzione di aggiornamenti** immettere un **nome** univoco per la distribuzione nel campo Nome.

4. Selezionare il sistema operativo di destinazione per la distribuzione degli aggiornamenti.

    > [!NOTE]
    > Questa opzione non è disponibile se è stata selezionata una macchina virtuale di Azure o un server abilitato per Arc. Il sistema operativo viene identificato automaticamente.

5. **Nell'area Gruppi** da aggiornare definire una query che combina sottoscrizione, gruppi di risorse, posizioni e tag per creare un gruppo dinamico di macchine virtuali di Azure da includere nella distribuzione. Per altre informazioni, vedere [Usare i gruppi dinamici con Gestione aggiornamenti](configure-groups.md).

    > [!NOTE]
    > Questa opzione non è disponibile se è stata selezionata una macchina virtuale di Azure o un server abilitato per Arc. Il computer viene automaticamente destinato alla distribuzione pianificata.

6. Nell'area **Computer da aggiornare** selezionare una ricerca salvata o un gruppo importato oppure scegliere **Computer** dall'elenco a discesa e selezionare computer singoli. Con questa opzione è possibile visualizzare l'idoneità dell'agente di Log Analytics per ogni computer. Per altre informazioni sui diversi metodi di creazione di gruppi di computer nei log di Monitoraggio di Azure, vedere [Gruppi di computer nei log di Monitoraggio di Azure](../../azure-monitor/logs/computer-groups.md). È possibile includere fino a un massimo di 1000 computer in una distribuzione di aggiornamenti pianificata.

    > [!NOTE]
    > Questa opzione non è disponibile se è stata selezionata una macchina virtuale di Azure o un server abilitato per Arc. Il computer viene automaticamente destinato alla distribuzione pianificata.

7. Usare l'area **Classificazioni aggiornamenti** per specificare [classificazioni degli aggiornamenti](view-update-assessments.md#work-with-update-classifications) per i prodotti. Per ogni prodotto, deselezionare tutte le classificazioni degli aggiornamenti supportate, tranne quelle da includere nella distribuzione degli aggiornamenti.

   :::image type="content" source="./media/deploy-updates/update-classifications-example.png" alt-text="Esempio che mostra la selezione di classificazioni di aggiornamento specifiche.":::

    Se la distribuzione deve applicare solo un set selezionato di aggiornamenti, è necessario deselezionare tutte le classificazioni degli aggiornamenti preselezioni quando si configura l'opzione Includi/Escludi **aggiornamenti** come descritto nel passaggio successivo. In questo modo si garantisce  che nei computer di destinazione siano installati solo gli aggiornamenti specificati per l'inclusione in questa distribuzione.

   >[!NOTE]
   > La distribuzione degli aggiornamenti in base alla relativa classificazione non funziona nelle versioni RTM di CentOS. Per distribuire correttamente gli aggiornamenti per CentOS, selezionare tutte le classificazioni per assicurarsi che gli aggiornamenti vengano applicati. Attualmente non è supportato alcun metodo per abilitare la disponibilità dei dati di classificazione nativi in CentOS. Per altre informazioni sulle classificazioni degli aggiornamenti, vedere quanto [segue.](overview.md#update-classifications)

8. Usare **l'area Includi/escludi** aggiornamenti per aggiungere o escludere gli aggiornamenti selezionati dalla distribuzione. Nella pagina **Includi/Escludi** immettere i numeri ID degli articoli della Knowledge Base da includere o escludere per gli aggiornamenti di Windows. Per le distribuzioni Linux supportate, specificare il nome del pacchetto.

   :::image type="content" source="./media/deploy-updates/include-specific-updates-example.png" alt-text="Esempio che illustra come includere aggiornamenti specifici.":::

   > [!IMPORTANT]
   > Tenere presente che le esclusioni eseguono l'override delle inclusioni. Se, ad esempio, si definisce la regola di esclusione `*`, Gestione aggiornamenti esclude tutte le patch o i pacchetti dall'installazione. Le patch escluse vengono ancora visualizzate come mancanti dai computer. Per i computer Linux, se si include un pacchetto contenente un pacchetto dipendente che è stato escluso, Gestione aggiornamenti non installa il pacchetto principale.

   > [!NOTE]
   > Non è possibile indicare aggiornamenti sostituiti perché vengano inclusi nella distribuzione degli aggiornamenti.

   Ecco alcuni scenari di esempio che consentono di comprendere come usare l'inclusione/esclusione e la classificazione degli aggiornamenti contemporaneamente nelle distribuzioni di aggiornamenti:

   * Se si vuole installare solo un elenco specifico di  aggiornamenti, non selezionare alcuna classificazione di aggiornamento e fornire un elenco di aggiornamenti da applicare usando l'opzione **Includi.**

   * Se si desidera installare solo gli aggiornamenti critici e di sicurezza, insieme a uno o più aggiornamenti facoltativi dei driver, è necessario selezionare **Sicurezza** e critico **in** **Classificazioni degli aggiornamenti**. Quindi, per **l'opzione** Includi, specificare gli aggiornamenti del driver.

   * Se si desidera installare solo gli aggiornamenti critici e di sicurezza, ma ignorare uno  o più aggiornamenti per Python per evitare l'interruzione dell'applicazione legacy, è necessario selezionare Sicurezza e critico **in** **Classificazioni degli aggiornamenti**. Quindi, per **l'opzione** Escludi aggiungere i pacchetti Python da ignorare.

9. Selezionare **Impostazioni pianificazione**. L'ora di inizio predefinita è 30 minuti dopo il momento corrente. È possibile impostare l'ora di inizio su qualsiasi orario a partire da 10 minuti dal momento corrente.

    > [!NOTE]
    > Questa opzione è diversa se è stato selezionato un server abilitato per Arc. È possibile selezionare **Aggiorna ora** o un'ora di inizio di 20 minuti in futuro.

10. Usare **ricorrenza per specificare se** la distribuzione viene eseguita una sola volta o usa una pianificazione ricorrente, quindi selezionare **OK**.

11. **Nell'area Pre-script e post-script** selezionare gli script da eseguire prima e dopo la distribuzione. Per altre informazioni, vedere [Gestire pre-script e post-script](pre-post-scripts.md).

12. Usare il campo **Finestra di manutenzione (minuti)** per specificare la quantità di tempo consentita per l'installazione di aggiornamenti. Quando si specifica una finestra di manutenzione, tenere presenti i dettagli seguenti:

    * Le finestre di manutenzione controllano la quantità di aggiornamenti che vengono installati.
    * Gestione aggiornamenti non interrompe l'installazione di nuovi aggiornamenti se si avvicina la fine di una finestra di manutenzione.
    * Gestione aggiornamenti non termina gli aggiornamenti in corso se viene superata la finestra di manutenzione. Gli aggiornamenti rimanenti da installare non vengono tentati. Se ciò avviene in modo coerente, è necessario rivalutare la durata della finestra di manutenzione.
    * Se la finestra di manutenzione viene superata in Windows, il motivo è in genere dovuto all'aggiornamento di un Service Pack la cui installazione impiega molto tempo.

    > [!NOTE]
    > Per evitare che gli aggiornamenti vengano applicati al di fuori di una finestra di manutenzione in Ubuntu, riconfigurare il pacchetto `Unattended-Upgrade` in modo da disabilitare gli aggiornamenti automatici. Per informazioni su come configurare il pacchetto, vedere l'[argomento sugli aggiornamenti automatici nella guida a Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

13. Usare il campo **Opzioni di riavvio** per specificare la modalità di gestione dei riavvii durante la distribuzione. Sono disponibili le opzioni seguenti: 
    * Riavvia se necessario (impostazione predefinita)
    * Riavvia sempre
    * Non riavviare mai
    * Solo riavvio - Gli aggiornamenti non verranno installati

    > [!NOTE]
    > Le chiavi del Registro di sistema elencate in [Chiavi del Registro di sistema usate per gestire il riavvio](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) possono causare un evento di riavvio se le **Opzioni di riavvio** sono impostate su **Non riavviare mai**.

14. Al termine della configurazione della pianificazione della distribuzione, selezionare **Crea.**

    ![Riquadro di impostazioni della pianificazione di aggiornamenti](./media/deploy-updates/manageupdates-schedule-win.png)

    > [!NOTE]
    > Al termine della configurazione della pianificazione della distribuzione per un server abilitato per Arc selezionato, selezionare **Rivedi e crea.**

15. Verrà nuovamente visualizzato il dashboard dello stato. Selezionare **Pianificazioni distribuzione** per visualizzare la pianificazione della distribuzione creata. Vengono elencate al massimo 500 pianificazioni. Se sono disponibili più di 500 pianificazioni e si vuole esaminare l'elenco completo, vedere il metodo dell'API REST [Software Update Configurations - List](/rest/api/automation/softwareupdateconfigurations/list) (Configurazioni di aggiornamento software - Elenca API REST). Specificare l'API versione 2019-06-01 o successiva.

## <a name="schedule-an-update-deployment-programmatically"></a>Pianificare una distribuzione di aggiornamenti a livello di codice

Per informazioni su come creare una distribuzione di aggiornamenti con l'API REST, vedere [Configurazione degli aggiornamenti software - Creazione](/rest/api/automation/softwareupdateconfigurations/create).

È possibile usare un runbook di esempio per creare una distribuzione di aggiornamenti settimanale. Per altre informazioni su questo runbook, vedere [Create a weekly update deployment for one or more VMs in a resource group](https://github.com/azureautomation/create-a-weekly-update-deployment-for-one-or-more-vms-in-a-resource-group) (Creare una distribuzione di aggiornamenti settimanale per una o più macchine virtuali in un gruppo di risorse).

## <a name="check-deployment-status"></a>Controllare lo stato della distribuzione

Dopo l'avvio della distribuzione pianificata, è possibile visualizzarne lo stato nella **scheda** Cronologia in **Gestione aggiornamenti**. Se la distribuzione è attualmente in esecuzione, lo stato sarà **In corso**. Al termine della distribuzione, se questa ha esito positivo, lo stato passa a **Completata**. Se si verificano errori con uno o più aggiornamenti nella distribuzione, lo stato è **Non riuscito**.

## <a name="view-results-of-a-completed-update-deployment"></a>Visualizzare i risultati di una distribuzione di aggiornamenti completata

Al termine della distribuzione, è possibile selezionarla per visualizzarne i risultati.

[![Aggiornare il dashboard dello stato della distribuzione per una distribuzione specifica](./media/deploy-updates/manageupdates-view-results.png)](./media/deploy-updates/manageupdates-view-results-expanded.png#lightbox)

In **Risultati aggiornamento** è disponibile un riepilogo che indica il numero totale di aggiornamenti e i risultati della distribuzione nelle macchine virtuali di destinazione. La tabella a destra offre una suddivisione dettagliata degli aggiornamenti e dei risultati dell'installazione per ognuno.

I valori disponibili sono:

* **Tentativo non eseguito**: l'aggiornamento non è stato installato perché il tempo disponibile non era sufficiente, in base alla durata della finestra di manutenzione specificata.
* **Non selezionato**: l'aggiornamento non è stato selezionato per la distribuzione.
* **Completato**: l'aggiornamento è stato completato.
* **Non riuscito**: l'aggiornamento non è riuscito.

Selezionare **Tutti i log** per visualizzare tutte le voci di log create dalla distribuzione.

Selezionare **Output** per visualizzare il flusso del processo del runbook responsabile della gestione della distribuzione di aggiornamenti nelle macchine virtuali di destinazione.

Per visualizzare informazioni dettagliate sugli errori della distribuzione, selezionare **Errori**.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come creare avvisi per ricevere notifiche sui risultati della distribuzione degli aggiornamenti, vedere [Creare avvisi per Gestione aggiornamenti](configure-alerts.md).
* Per risolvere gli errori generali di Gestione aggiornamenti, vedere [Risolvere i problemi relativi a Gestione aggiornamenti](../troubleshoot/update-management.md).
