---
title: 'Esercitazione: usare PlayBook con regole di automazione in Sentinel di Azure'
description: Usare questa esercitazione per usare i PlayBook insieme alle regole di automazione in Sentinel di Azure per automatizzare la risposta agli eventi imprevisti e correggere le minacce per la sicurezza.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2021
ms.author: yelevin
ms.openlocfilehash: 365ba9df39b4b3bd7397e86e6a51b285bf049242
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104600633"
---
# <a name="tutorial-use-playbooks-with-automation-rules-in-azure-sentinel"></a>Esercitazione: usare PlayBook con regole di automazione in Sentinel di Azure

Questa esercitazione illustra come usare i PlayBook insieme alle regole di automazione per automatizzare la risposta agli eventi imprevisti e correggere le minacce alla sicurezza rilevate da Azure Sentinel. Al termine di questa esercitazione, sarà possibile:

> [!div class="checklist"]
>
> * Creare una regola di automazione
> * Creare un playbook
> * Aggiungere azioni a un PlayBook
> * Aggiungere un PlayBook a una regola di automazione o una regola di analisi per automatizzare la risposta alle minacce

## <a name="what-are-automation-rules-and-playbooks"></a>Che cosa sono le regole e i PlayBook di automazione?

Le regole di automazione consentono di valutare gli eventi imprevisti in Sentinel di Azure. È possibile usarli per assegnare automaticamente gli eventi imprevisti al personale appropriato, chiudere gli eventi imprevisti rumorosi o i falsi positivi noti, modificare la gravità e aggiungere i tag. Sono anche il meccanismo mediante il quale è possibile eseguire PlayBook in risposta a eventi imprevisti.

I PlayBook sono raccolte di procedure che possono essere eseguite da Azure Sentinel in risposta a un avviso o a un evento imprevisto. Un PlayBook consente di automatizzare e orchestrare la risposta e può essere impostata in modo da essere eseguita automaticamente quando si generano avvisi o eventi imprevisti specifici, che vengono collegati rispettivamente a una regola di analisi o a una regola di automazione. Può essere eseguito anche manualmente su richiesta.

I PlayBook in Sentinel di Azure si basano sui flussi di lavoro compilati in app per la [logica di Azure](../logic-apps/logic-apps-overview.md), il che significa che è possibile ottenere tutta la potenza, la personalizzazione e i modelli predefiniti delle app per la logica. Ogni PlayBook viene creato per la sottoscrizione specifica a cui appartiene, ma la visualizzazione **PlayBook** Mostra tutti i PlayBook disponibili in tutte le sottoscrizioni selezionate.

> [!NOTE]
> Poiché i PlayBook usano app per la logica di Azure, potrebbero essere applicati addebiti aggiuntivi. Per altri dettagli, visitare la pagina dei prezzi di app per la [logica di Azure](https://azure.microsoft.com/pricing/details/logic-apps/) .

Se, ad esempio, si desidera arrestare l'esplorazione della rete da parte di utenti potenzialmente compromessi, è possibile creare una risposta automatizzata e con più facet agli eventi imprevisti generati da regole che rilevano utenti compromessi. Si inizia creando un PlayBook che esegue le azioni seguenti:

1. Quando il PlayBook viene chiamato da una regola di automazione che lo passa a un evento imprevisto, il PlayBook apre un ticket in [ServiceNow](/connectors/service-now/) o in qualsiasi altro sistema di ticketing it.

1. Invia un messaggio al canale delle operazioni di sicurezza in [Microsoft teams](/connectors/teams/) o [Slack](/connectors/slack/) per assicurarsi che gli analisti della sicurezza siano consapevoli dell'evento imprevisto.

1. Invia anche tutte le informazioni contenute nell'evento imprevisto in un messaggio di posta elettronica all'amministratore di rete e amministratore della sicurezza senior. Il messaggio di posta elettronica includerà i pulsanti di opzione **blocca** e **Ignora** utente.

1. Il PlayBook resta in attesa finché non viene ricevuta una risposta dagli amministratori, quindi continua con i passaggi successivi.

1. Se l'amministratore sceglie **blocco**, invia un comando a Azure ad per disabilitare l'utente e uno al firewall per bloccare l'indirizzo IP.

1. Se gli amministratori scelgono **Ignora**, il PlayBook chiude l'evento imprevisto in Sentinel di Azure e il ticket in ServiceNow.

Per attivare il PlayBook, si creerà quindi una regola di automazione che viene eseguita quando vengono generati questi eventi imprevisti. Questa regola effettuerà le seguenti operazioni:

1. La regola imposta lo stato dell'evento imprevisto su **attivo**.

1. Assegna l'evento imprevisto all'analista che è responsabile della gestione di questo tipo di evento imprevisto.

1. Viene aggiunto il tag "utente compromesso".

1. Infine, viene chiamato il PlayBook appena creato. [Per questo passaggio sono necessarie autorizzazioni speciali](automate-responses-with-playbooks.md#incident-creation-automated-response).

I PlayBook possono essere eseguiti automaticamente in risposta a eventi imprevisti, creando regole di automazione che chiamano i PlayBook come azioni, come nell'esempio precedente. Possono anche essere eseguiti automaticamente in risposta agli avvisi, indicando alla regola di analisi di eseguire automaticamente uno o più PlayBook quando viene generato l'avviso. 

È anche possibile scegliere di eseguire manualmente un PlayBook su richiesta, in risposta a un avviso selezionato.

Ottenere un'introduzione più completa e dettagliata sull'automazione della risposta alle minacce usando regole e [PlayBook](automate-responses-with-playbooks.md) di [automazione](automate-incident-handling-with-automation-rules.md) in Sentinel di Azure.

> [!IMPORTANT]
>
> - Le **regole di automazione** e l'uso del trigger per gli **eventi imprevisti** per i PlayBook sono attualmente in **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

## <a name="create-a-playbook"></a>Creare un playbook

Per creare un nuovo playbook in Sentinel di Azure, seguire questa procedura:

### <a name="prepare-the-playbook-and-logic-app"></a>Preparare il PlayBook e l'app per la logica

1. Dal menu di navigazione di **Azure Sentinel** selezionare **automazione**.

1. Nel menu in alto selezionare **Crea** e **Aggiungi nuovo playbook**.

    :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-playbook.png" alt-text="Aggiungere un nuovo playbook":::

    Verrà visualizzata una nuova scheda del browser che consente di passare alla procedura guidata **Crea un'app** per la logica.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-playbook.png" alt-text="Creare un'app per la logica":::

1. Immettere la **sottoscrizione** e il **gruppo di risorse** e assegnare un nome al PlayBook sotto il nome dell'app per la **logica**.

1. Per **Region (area**) selezionare l'area di Azure in cui archiviare le informazioni sull'app per la logica.

1. Se si vuole monitorare l'attività del PlayBook per scopi diagnostici, selezionare la casella di controllo **Abilita log Analytics** e immettere il nome dell' **area di lavoro log Analytics** .

1. Se si desidera applicare tag al PlayBook, fare clic su **Avanti: tag >** (non connesso ai tag applicati dalle regole di automazione. [Altre informazioni sui tag](../azure-resource-manager/management/tag-resources.md). In caso contrario, fare clic su **Verifica + crea**. Confermare i dettagli specificati, quindi fare clic su **Crea**.

1. Durante la creazione e la distribuzione del PlayBook (l'operazione richiederà alcuni minuti), verrà visualizzata una schermata denominata **Microsoft. EmptyWorkflow**. Quando viene visualizzato il messaggio "distribuzione completata", fare clic su **Vai alla risorsa.**

1. Si verrà portati alla [finestra di progettazione delle app](../logic-apps/logic-apps-overview.md)per la logica del nuovo playbook, in cui è possibile iniziare a progettare il flusso di lavoro. Verrà visualizzata una schermata con un breve video introduttivo e alcuni trigger e modelli di app per la logica usati di frequente. [Altre](../logic-apps/logic-apps-create-logic-apps-from-templates.md) informazioni sulla creazione di un PlayBook con le app per la logica.

1. Selezionare il modello **App per la logica vuota**.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-playbook-template.png" alt-text="Raccolta di modelli di progettazione di app per la logica":::

### <a name="choose-the-trigger"></a>Scegliere il trigger

Ogni PlayBook deve iniziare con un trigger. Il trigger definisce l'azione che avvierà il PlayBook e lo schema che si prevede di ricevere.

1. Nella barra di ricerca cercare Azure Sentinel. Selezionare **Sentinel di Azure** quando viene visualizzato nei risultati.

1. Nella scheda **trigger** risultante si vedranno i due trigger offerti da Azure Sentinel:
    - Quando viene attivata una risposta a un avviso di Azure Sentinel
    - When Azure Sentinel incident creation rule is triggered (Quando viene attivata la regola di creazione degli eventi imprevisti di Azure Sentinel)

   Scegliere il trigger corrispondente al tipo di PlayBook che si sta creando.

    :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-trigger.png" alt-text="Scegliere un trigger per il PlayBook":::

### <a name="add-actions"></a>Aggiunta di azioni

A questo punto è possibile definire cosa accade quando si chiama il PlayBook. È possibile aggiungere azioni, condizioni logiche, cicli o condizioni del cambio di maiuscole e minuscole selezionando **nuovo passaggio**. Questa selezione consente di aprire un nuovo frame nella finestra di progettazione, in cui è possibile scegliere un sistema o un'applicazione con cui interagire o una condizione da impostare. Immettere il nome del sistema o dell'applicazione nella barra di ricerca nella parte superiore del frame, quindi scegliere tra i risultati disponibili.

In ognuno di questi passaggi, facendo clic su qualsiasi campo viene visualizzato un pannello con due menu: **contenuto dinamico** ed **espressione**. Dal menu **contenuto dinamico** è possibile aggiungere riferimenti agli attributi dell'avviso o dell'evento imprevisto passato al PlayBook, inclusi i valori e gli attributi di tutte le entità in questione. Dal menu **espressione** è possibile scegliere da un'ampia libreria di funzioni per aggiungere logica aggiuntiva ai passaggi.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/logic-app.png" alt-text="Progettazione app per la logica":::

Questo screenshot mostra le azioni e le condizioni che si aggiungono per la creazione del PlayBook descritto nell'esempio all'inizio di questo documento. L'unica differenza è rappresentata dal fatto che, nel PlayBook illustrato di seguito, si sta usando il **trigger Alert** anziché il **trigger Incident**. Questo significa che questo PlayBook verrà chiamato direttamente da una regola di analisi, non da una regola di automazione. Di seguito vengono descritte entrambe le modalità di chiamata di un PlayBook.

## <a name="automate-threat-responses"></a>Automatizzare le risposte alle minacce

Sono stati creati il PlayBook e è stato definito il trigger, sono state impostate le condizioni e sono state prescritte le azioni che verranno eseguite e gli output che produrranno. A questo punto è necessario determinare i criteri in base ai quali verrà eseguito e configurare il meccanismo di automazione che lo eseguirà quando vengono soddisfatti tali criteri.

### <a name="respond-to-incidents"></a>Rispondere agli eventi imprevisti

Si usa un PlayBook per rispondere a un **evento imprevisto** creando una [regola di automazione](automate-incident-handling-with-automation-rules.md) che verrà eseguita quando viene generato l'evento imprevisto e a sua volta chiamerà il PlayBook.

Per creare una regola di automazione:

1. Dal pannello **automazione** nel menu di navigazione di Azure Sentinel selezionare **Crea** dal menu in alto e quindi **Aggiungi nuova regola**.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-rule.png" alt-text="Aggiungi una nuova regola":::

1. Verrà aperto il pannello **Crea nuova regola di automazione** . Immettere un nome per la regola.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-automation-rule.png" alt-text="Creare una regola di automazione":::

1. Se si vuole che la regola di automazione abbia effetto solo su determinate regole di analisi, specificare quali modifiche modificando la condizione di **nome della regola if Analytics** .

1. Aggiungere qualsiasi altra condizione da cui si desidera che questa attivazione della regola di automazione dipenda da. Fare clic su **Aggiungi condizione** e scegliere condizioni dall'elenco a discesa. L'elenco delle condizioni viene popolato dai campi Dettagli avviso e identificatore dell'entità.

1. Scegliere le azioni che devono essere eseguite da questa regola di automazione. Le azioni disponibili includono **assegna proprietario**, **Cambia stato**, **modifica gravità**, **Aggiungi tag** ed **Esegui PlayBook**. È possibile aggiungere tutte le azioni desiderate.

1. Se si aggiunge un'azione **Esegui PlayBook** , verrà richiesto di scegliere dall'elenco a discesa dei PlayBook disponibili. Solo i PlayBook che iniziano con il **trigger di evento imprevisto** possono essere eseguiti dalle regole di automazione, quindi solo essi verranno visualizzati nell'elenco.

    > [!IMPORTANT]
    > Per eseguire PlayBook da regole di automazione, è necessario concedere le autorizzazioni esplicite a Sentinel di Azure. Se nella casella di riepilogo a discesa viene visualizzato un PlayBook, significa che Sentinel non dispone dell'autorizzazione per il gruppo di risorse del PlayBook. Fare clic sul collegamento **Gestisci autorizzazioni PlayBook** per assegnare le autorizzazioni.
    > Nel pannello **Gestisci autorizzazioni** visualizzato, contrassegnare le caselle di controllo dei gruppi di risorse contenenti i PlayBook da eseguire e fare clic su **applica**.
    > :::image type="content" source="./media/tutorial-respond-threats-playbook/manage-permissions.png" alt-text="Gestione autorizzazioni":::
    > - È necessario avere le autorizzazioni di **proprietario** per qualsiasi gruppo di risorse a cui si vogliono concedere le autorizzazioni di Azure Sentinel ed è necessario avere il ruolo di **collaboratore dell'app** per la logica in qualsiasi gruppo di risorse che contiene i PlayBook che si vuole eseguire.
    > - In una distribuzione multi-tenant, se il PlayBook che si vuole eseguire si trova in un tenant diverso, è necessario concedere l'autorizzazione Sentinel di Azure per eseguire il PlayBook nel tenant del PlayBook.
    >    1. Dal menu di navigazione di Azure Sentinel nel tenant di Playbooks selezionare **Settings (impostazioni**).
    >    1. Nel pannello **Impostazioni** selezionare la scheda **Impostazioni** , quindi l'espansore **autorizzazioni PlayBook** .
    >    1. Fare clic sul pulsante **Configura autorizzazioni** per aprire il pannello **Gestisci autorizzazioni** indicato in precedenza e continuare come descritto.

1. Impostare una data di scadenza per la regola di automazione, se si vuole che ne abbia una.

1. Immettere un numero per determinare la posizione in cui verrà eseguita la regola nella sequenza di **regole di automazione** .

1. Fare clic su **Applica**. L'operazione è completata.

[Scopri altri modi](automate-incident-handling-with-automation-rules.md#creating-and-managing-automation-rules) per creare regole di automazione.

### <a name="respond-to-alerts"></a>Rispondere agli avvisi

Si usa un PlayBook per rispondere a un **avviso** creando una **regola di analisi** o modificando uno esistente, che viene eseguito quando viene generato l'avviso e selezionando il PlayBook come risposta automatica nella [creazione guidata regola di analisi](tutorial-detect-threats-custom.md).

1. Dal pannello **analisi** nel menu di navigazione di Azure Sentinel selezionare la regola di analisi per cui si vuole automatizzare la risposta e fare clic su **modifica** nel riquadro dettagli.

1. Nella pagina **Modifica regola esistente della creazione guidata regola di analisi** selezionare la scheda **risposta automatica** .

   :::image type="content" source="./media/tutorial-respond-threats-playbook/automated-response-tab.png" alt-text="Scheda risposta automatica":::

1. Scegliere il PlayBook dall'elenco a discesa. È possibile scegliere più di un PlayBook, ma saranno disponibili solo i PlayBook che usano il **trigger di avviso** .

1. Nella scheda **Verifica e crea** selezionare **Salva**.

## <a name="run-a-playbook-on-demand"></a>Eseguire un playbook su richiesta

È anche possibile eseguire un PlayBook su richiesta.

 > [!NOTE]
 > Solo i PlayBook che usano il **trigger di avviso** possono essere eseguiti su richiesta.

Per eseguire un playbook su richiesta:

1. Nella pagina **eventi imprevisti** selezionare un evento imprevisto e fare clic su **Visualizza dettagli completi**.

2. Nella scheda **Alerts** (Avvisi) fare clic sull'avviso sul quale eseguire il playbook, scorrere verso destra e fare clic su **View playbooks** (Visualizza i playbook). Selezionare quindi un playbook da **eseguire** dall'elenco di playbook disponibili nella sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare PlayBook e regole di automazione in Sentinel di Azure per rispondere alle minacce. 
- Informazioni su come [cercare in modo proattivo le minacce](hunting.md) usando Sentinel di Azure.
