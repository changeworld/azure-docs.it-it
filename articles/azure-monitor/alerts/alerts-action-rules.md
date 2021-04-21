---
title: Regole di azione per Monitoraggio di Azure avvisi
description: Informazioni sulle regole di Monitoraggio di Azure e su come configurarle e gestirle.
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 4f54ee7d21d52386bd18921aec33cabe02046852
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772560"
---
# <a name="action-rules-preview"></a>Regole di azione (anteprima)

Le regole di azione consentono di aggiungere o eliminare i gruppi di azioni per gli avvisi generati. Una singola regola può coprire ambiti diversi delle risorse di destinazione, ad esempio qualsiasi avviso su una risorsa specifica (ad esempio una macchina virtuale specifica) o qualsiasi avviso generato su qualsiasi risorsa in una sottoscrizione. Facoltativamente, è possibile aggiungere vari filtri per controllare quali avvisi sono coperti da una regola e definire una pianificazione, ad esempio perché sia attiva solo al di fuori dell'orario lavorativo o durante una finestra di manutenzione pianificata.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rBZ2]

## <a name="why-and-when-should-you-use-action-rules"></a>Perché e quando è consigliabile usare le regole di azione?

### <a name="suppression-of-alerts"></a>Eliminazione degli avvisi

Esistono molti scenari in cui è utile eliminare le notifiche generate da avvisi. Questi scenari vanno dall'eliminazione durante una finestra di manutenzione pianificata all'eliminazione durante l'orario non lavorativo. Ad esempio, il team responsabile di  **ContosoVM** vuole eliminare le notifiche di avviso per il prossimo fine settimana, perché **ContosoVM** è in fase di manutenzione pianificata.

Anche se il team può disabilitare manualmente ogni regola di avviso configurata in **ContosoVM** (e abilitarla nuovamente dopo la manutenzione), non si tratta di un processo semplice. Le regole di azione consentono di definire l'eliminazione degli avvisi su larga scala con la possibilità di configurare in modo flessibile il periodo di eliminazione. Nell'esempio precedente il team può definire una regola di azione in **ContosoVM** che elimina tutte le notifiche di avviso per il fine settimana.

### <a name="actions-at-scale"></a>Azioni su larga scala

Anche se le regole di avviso consentono di definire il gruppo di azioni che viene attivato quando viene generato l'avviso, i clienti hanno spesso un gruppo di azioni comune nell'ambito delle operazioni. Ad esempio, un team responsabile del gruppo di risorse **ContosoRG** definirà probabilmente lo stesso gruppo di azioni per tutte le regole di avviso definite all'interno **di ContosoRG.**

Le regole di azione consentono di semplificare questo processo. Definendo azioni su larga scala, è possibile attivare un gruppo di azioni per qualsiasi avviso generato nell'ambito configurato. Nell'esempio precedente, il team può definire una regola di azione in **ContosoRG** che attiverà lo stesso gruppo di azioni per tutti gli avvisi generati al suo interno.

> [!NOTE]
> Le regole di azione non si applicano integrità dei servizi di Azure avvisi.

## <a name="configuring-an-action-rule"></a>Configurazione di una regola di azione

### <a name="portal"></a>[Portale](#tab/portal)

È possibile accedere alla funzionalità selezionando **Gestisci azioni** dalla **pagina** di destinazione Avvisi in Monitoraggio di Azure. Selezionare quindi **Regole azione (anteprima)**. È possibile accedere alle regole selezionando Regole di azione **(anteprima)** dal dashboard della pagina di destinazione per gli avvisi.

![Regole di azione dalla pagina Monitoraggio di Azure destinazione](media/alerts-action-rules/action-rules-landing-page.png)

Selezionare **+ Nuova regola azione**.

![Screenshot che mostra la pagina Gestisci azioni con il pulsante Nuova regola di azione evidenziato.](media/alerts-action-rules/action-rules-new-rule.png)

In alternativa, è possibile creare una regola di azione durante la configurazione di una regola di avviso.

![Screenshot che mostra la pagina Crea regola con il pulsante Crea regola di azione evidenziato.](media/alerts-action-rules/action-rules-alert-rule.png)

Verrà ora visualizzata la pagina flusso per la creazione di regole di azione. Configurare gli elementi seguenti:

![Nuovo flusso di creazione della regola di azione](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Ambito

Scegliere innanzitutto l'ambito (sottoscrizione di Azure, gruppo di risorse o risorsa di destinazione). È anche possibile selezionare più volte una combinazione di ambiti all'interno di una singola sottoscrizione.

![Ambito della regola di azione](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Criteri di filtro

Facoltativamente, è possibile definire filtri in modo che la regola si applii a un subset specifico degli avvisi o a eventi specifici per ogni avviso , ad esempio solo "Attivato" o solo "Risolto".

I filtri disponibili sono:

* **Gravità**  
Questa regola verrà applicata solo agli avvisi con i livelli di gravità selezionati.  
Ad esempio, **severity = "Sev1"** indica che la regola verrà applicata solo agli avvisi con gravità Sev1.
* **Servizio di monitoraggio**  
Questa regola verrà applicata solo agli avvisi provenienti dai servizi di monitoraggio selezionati.  
Ad esempio, il servizio di monitoraggio **= "Backup di Azure"** significa che la regola verrà applicata solo agli avvisi di backup (provenienti da Backup di Azure).
* **Tipo di risorsa**  
Questa regola verrà applicata solo agli avvisi per i tipi di risorse selezionati.  
Ad esempio, **il tipo di risorsa = "Macchine virtuali"** significa che la regola verrà applicata solo agli avvisi nelle macchine virtuali.
* **ID regola di avviso**  
Questa regola verrà applicata solo agli avvisi provenienti da una regola di avviso specifica. Il valore deve essere l'ID Resource Manager della regola di avviso.  
Ad esempio, l'ID regola di avviso **= "/subscriptions/SubId1/resourceGroups/RG1/providers/microsoft.insights/metricalerts/API-Latency"** significa che questa regola verrà applicata solo agli avvisi provenienti dalla regola di avviso della metrica "LATENZA API".  
_NOTA: è possibile ottenere l'ID regola di avviso appropriato elencando le regole di avviso dall'interfaccia della riga di comando oppure aprendo una regola di avviso specifica nel portale, facendo clic su "Proprietà" e copiando il valore "ID risorsa"._
* **Condizione di monitoraggio**  
Questa regola verrà applicata solo agli eventi di avviso con la condizione di monitoraggio specificata, ad esempio **"Attivato"** **o "Risolto".**
* **Descrizione**  
Questa regola verrà applicata solo agli avvisi che contengono una stringa specifica nel campo della descrizione dell'avviso. Questo campo contiene la descrizione della regola di avviso.  
Ad esempio, **description contiene "prod"** significa che la regola corrisponderà solo agli avvisi che contengono la stringa "prod" nella relativa descrizione.
* **Contesto dell'avviso (payload)**  
Questa regola verrà applicata solo agli avvisi che contengono uno o più valori specifici nei campi del contesto di avviso.  
Ad esempio, il contesto **dell'avviso (payload) contiene "Computer-01"** significa che la regola verrà applicata solo agli avvisi il cui payload contiene la stringa "Computer-01".

> [!NOTE]
> Ogni filtro può includere fino a cinque valori.  
> Ad esempio, un filtro sul servizio di monitoraggio può includere fino a cinque nomi di servizio di monitoraggio.




Se si impostano più filtri in una regola, vengono applicati tutti. Ad esempio, se si imposta il tipo di risorsa **= "Macchine virtuali"** e la gravità **= "Sev0",** la regola verrà applicata solo per gli avvisi Sev0 nelle macchine virtuali.

![Filtri delle regole di azione](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Eliminazione o configurazione del gruppo di azioni

Configurare quindi la regola di azione per l'eliminazione degli avvisi o il supporto del gruppo di azioni. Non è possibile scegliere entrambi. La configurazione agisce su tutte le istanze di avviso che corrispondono all'ambito e ai filtri definiti in precedenza.

#### <a name="suppression"></a>Eliminazione degli avvisi

Se si seleziona **l'eliminazione,** configurare la durata per l'eliminazione di azioni e notifiche. Scegliere una delle seguenti opzioni:
* **Da ora (Sempre):** elimina tutte le notifiche all'infinito.
* **A un'ora pianificata:** elimina le notifiche entro una durata delimitata.
* **Con ricorrenza**: elimina le notifiche in base a una pianificazione ricorrente giornaliera, settimanale o mensile.

![Eliminazione delle regole di azione](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Gruppo di azioni

Se si seleziona **Gruppo di azioni** nell'interruttore, aggiungere un gruppo di azioni esistente o crearne uno nuovo.

> [!NOTE]
> È possibile associare un solo gruppo di azioni a una regola di azione.

![Aggiungere o creare una nuova regola di azione selezionando Gruppo di azioni](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Dettagli della regola di azione

Configurare infine i dettagli seguenti per la regola di azione:
* Nome
* Gruppo di risorse in cui è stato salvato
* Descrizione

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

È possibile creare regole di azione con l'interfaccia della riga di comando di Azure usando [il comando az monitor action-rule create.](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create)  Il `az monitor action-rule` riferimento è solo uno dei numerosi riferimenti all'interfaccia della riga di comando di Azure per [Monitoraggio di Azure](/cli/azure/azure-cli-reference-for-monitor).

### <a name="prepare-your-environment"></a>Preparare l'ambiente

1. [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)

   Se si preferisce, è anche possibile usare Azure Cloud Shell per completare la procedura descritta in questo articolo.  Azure Cloud Shell è un ambiente di shell interattivo utilizzabile tramite il browser.  Per avviare Cloud Shell, usare uno di questi metodi:

   - Aprire Cloud Shell passando a [https://shell.azure.com](https://shell.azure.com)

   - Selezionare il pulsante **Cloud Shell** sulla barra dei menu nell'angolo in alto a destra del [portale di Azure](https://portal.azure.com)

1. Accedere.

   Se si usa un'installazione locale dell'interfaccia della riga di comando, accedere con il [comando az login.](/cli/azure/reference-index#az_login)  Seguire le istruzioni visualizzate nel terminale per completare il processo di autenticazione.

    ```azurecli
    az login
    ```

1. Installare l'estensione `alertsmanagement`

   Il `az monitor action-rule` comando è un'estensione sperimentale dell'interfaccia della riga di comando di Azure di base. Per altre informazioni sui riferimenti alle estensioni, vedere Usare [l'estensione con l'interfaccia della riga di comando di Azure.](/cli/azure/azure-cli-extensions-overview?)

   ```azurecli
   az extension add --name alertsmanagement
   ```

   È previsto l'avviso seguente.

   ```output
   The installed extension `alertsmanagement` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="create-action-rules-with-the-azure-cli"></a>Creare regole di azione con l'interfaccia della riga di comando di Azure

Per informazioni sui parametri obbligatori e facoltativi, vedere il contenuto di riferimento dell'interfaccia della riga di comando di Azure per [az monitor action-rule create.](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create)

Creare una regola di azione per eliminare le notifiche in un gruppo di risorse.

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --scope-type ResourceGroup \
                              --scope /subscriptions/0b1f6471-1bf0-4dda-aec3-cb9272f09590/resourceGroups/MyResourceGroupName \
                              --suppression-recurrence-type Always \
                              --alert-context Contains Computer-01 \
                               --monitor-service Equals "Log Analytics"
```

Creare una regola di azione per eliminare le notifiche per tutti gli avvisi Sev4 in tutte le macchine virtuali all'interno della sottoscrizione ogni fine settimana.

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --severity Equals Sev4 \
                              --target-resource-type Equals Microsoft.Compute/VirtualMachines \
                              --suppression-recurrence-type Weekly \
                              --suppression-recurrence 0 6 \
                              --suppression-start-date 12/09/2018 \
                              --suppression-end-date 12/18/2018 \
                              --suppression-start-time 06:00:00 \
                              --suppression-end-time 14:00:00

```

* * *

## <a name="example-scenarios"></a>Scenari di esempio

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scenario 1: Eliminazione degli avvisi in base alla gravità

Contoso vuole eliminare le notifiche per tutti gli avvisi Sev4 in tutte le macchine virtuali all'interno della sottoscrizione **ContosoSub** ogni fine settimana.

**Soluzione:** Creare una regola di azione con:
* Scope = **ContosoSub**
* Filtri
    * Gravità = **Sev4**
    * Tipo di risorsa = **Macchine virtuali**
* Eliminazione con ricorrenza impostata su settimanale e controllo **di** sabato **e** domenica

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scenario 2: Eliminazione degli avvisi in base al contesto di avviso (payload)

Contoso vuole eliminare le notifiche per tutti gli avvisi di log generati per **Computer-01** in **ContosoSub** per un periodo illimitato durante la manutenzione.

**Soluzione:** Creare una regola di azione con:
* Scope = **ContosoSub**
* Filtri
    * Servizio di monitoraggio = **Log Analytics**
    * Il contesto di avviso (payload) **contiene Computer-01**
* Eliminazione impostata su **Da ora (sempre)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scenario 3: Gruppo di azioni definito in un gruppo di risorse

Contoso ha definito un [avviso di metrica a livello di sottoscrizione.](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) Ma vuole definire le azioni che vengono attivate in modo specifico per gli avvisi generati dal gruppo di risorse **ContosoRG.**

**Soluzione:** Creare una regola di azione con:
* Ambito = **ContosoRG**
* Nessun filtro
* Gruppo di azioni impostato su **ContosoActionGroup**

> [!NOTE]
> *I gruppi di azioni definiti all'interno delle regole di azione e delle regole di avviso operano in modo indipendente, senza deduplicazione.* Nello scenario descritto in precedenza, se viene definito un gruppo di azioni per la regola di avviso, viene attivato insieme al gruppo di azioni definito nella regola di azione.

## <a name="managing-your-action-rules"></a>Gestione delle regole di azione

### <a name="portal"></a>[Portale](#tab/portal)

È possibile visualizzare e gestire le regole di azione dalla visualizzazione elenco:

![Visualizzazione elenco regole di azione](media/alerts-action-rules/action-rules-list-view.png)

Da qui è possibile abilitare, disabilitare o eliminare le regole di azione su larga scala selezionando la casella di controllo accanto a esse. Quando si seleziona una regola di azione, viene visualizzata la pagina di configurazione. La pagina consente di aggiornare la definizione della regola di azione e abilitarla o disabilitarla.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

È possibile visualizzare e gestire le regole di azione usando il [comando az monitor action-rule](/cli/azure/ext/alertsmanagement/monitor) dall'interfaccia della riga di comando di Azure.

Prima di gestire le regole di azione con l'interfaccia della riga di comando di Azure, preparare l'ambiente usando le istruzioni fornite in [Configurazione di una regola di azione.](#configuring-an-action-rule)

```azurecli
# List all action rules for a subscription
az monitor action-rule list

# Get details of an action rule
az monitor action-rule show --resource-group MyResourceGroupName --name MyActionRuleName

# Update an action rule.
az monitor action-rule update --resource-group MyResourceGroupName --name MyActionRuleName --status Disabled

# Delete an action rule.
az monitor action-rule delete --resource-group MyResourceGroupName --name MyActionRuleName
```

* * *

## <a name="best-practices"></a>Procedure consigliate

Gli avvisi del log creati con l'opzione [numero](./alerts-unified-log.md) di risultati generano una singola istanza di avviso usando l'intero risultato della ricerca (che potrebbe estendersi su più computer). In questo scenario, se una regola di azione usa il filtro Contesto avviso **(payload),** agisce sull'istanza dell'avviso purché sia presente una corrispondenza. Nello scenario 2, descritto in precedenza, se i risultati della ricerca per l'avviso di log generato contengono sia **Computer-01** che **Computer-02**, l'intera notifica viene eliminata. Non viene generata alcuna notifica **per Computer-02.**

![Il diagramma mostra le regole di azione e gli avvisi dei log con una singola istanza di avviso evidenziata.](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Per usare al meglio gli avvisi del log con le regole di azione, creare avvisi di log con [l'opzione di misurazione della metrica.](./alerts-unified-log.md) Questa opzione consente di generare istanze di avviso separate, in base al campo del gruppo definito. Nello scenario 2 vengono quindi generate istanze di avviso separate per **Computer-01** **e Computer-02**. A causa della regola di azione descritta nello scenario, viene eliminata solo la notifica **per Computer-01.** La notifica **per Computer-02** continua a essere visualizzata come di consueto.

![Regole di azione e avvisi del log (numero di risultati)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Domande frequenti

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Durante la configurazione di una regola di azione, si desidera visualizzare tutte le possibili regole di azione sovrapposte, in modo da evitare notifiche duplicate. È possibile farlo?

Dopo aver definito un ambito durante la configurazione di una regola di azione, è possibile visualizzare un elenco di regole di azione che si sovrappongono nello stesso ambito (se presente). Questa sovrapposizione può essere una delle opzioni seguenti:

* Corrispondenza esatta: ad esempio, la regola di azione che si sta definendo e la regola di azione sovrapposta si trova nella stessa sottoscrizione.
* Un subset: ad esempio, la regola di azione che si sta definendo si trova in una sottoscrizione e la regola di azione sovrapposta si trova in un gruppo di risorse all'interno della sottoscrizione.
* Un superset: ad esempio, la regola di azione che si sta definendo si trova in un gruppo di risorse e la regola di azione sovrapposta si trova nella sottoscrizione che contiene il gruppo di risorse.
* Intersezione: ad esempio, la regola di azione che si sta definendo si trova in **VM1** e **VM2** e la regola di azione sovrapposta si trova in **VM2** e **VM3.**

![Screenshot che mostra la pagina Nuova regola di azione con le regole di azione sovrapposte visualizzate nella finestra Regole di azione definite nella stessa finestra dell'ambito.](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Durante la configurazione di una regola di avviso, è possibile sapere se sono già state definite regole di azione che potrebbero agire sulla regola di avviso che si sta definendo?

Dopo aver definito la risorsa di destinazione per la regola di avviso, è possibile visualizzare  l'elenco delle regole di azione che agiscono sullo stesso ambito (se presente) selezionando Visualizza azioni configurate nella sezione **Azioni.** Questo elenco viene popolato in base ai seguenti scenari per l'ambito:

* Corrispondenza esatta: ad esempio, la regola di avviso che si sta definendo e la regola di azione si trova nella stessa sottoscrizione.
* Un subset: ad esempio, la regola di avviso che si sta definendo si trova in una sottoscrizione e la regola di azione si trova in un gruppo di risorse all'interno della sottoscrizione.
* Un superset: ad esempio, la regola di avviso che si sta definendo si trova in un gruppo di risorse e la regola di azione si trova nella sottoscrizione che contiene il gruppo di risorse.
* Un'intersezione: ad esempio, la regola di avviso che si sta definendo si trova in **VM1** e **VM2** e la regola di azione si trova in **VM2** e **VM3.**

![Regole di azione sovrapposte](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>È possibile visualizzare gli avvisi che sono stati eliminati da una regola di azione?

Nella pagina [di elenco degli avvisi](./alerts-managing-alert-instances.md)è possibile scegliere una colonna aggiuntiva denominata Stato **eliminazione**. Se la notifica per un'istanza di avviso è stata eliminata, lo stato verrà visualizzato nell'elenco.

![Istanze di avviso soppresse](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Se è presente una regola di azione con un gruppo di azioni e un'altra con eliminazione attiva nello stesso ambito, cosa accade?

L'eliminazione ha sempre la precedenza sullo stesso ambito.

### <a name="what-happens-if-i-have-a-resource-that-is-covered-by-two-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Cosa accade se si ha una risorsa coperta da due regole di azione? Si ottengono una o due notifiche? Ad esempio, **VM2** nello scenario seguente:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with action group AG1`

Per ogni avviso in VM1 e VM3, il gruppo di azioni AG1 viene attivato una sola volta. Per ogni avviso in **VM2,** il gruppo di azioni AG1 viene attivato due volte, perché le regole di azione non deduplicano le azioni.

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Cosa accade se una risorsa è monitorata in due regole di azione separate e una richiede un'azione e un'altra per l'eliminazione? Ad esempio, **VM2** nello scenario seguente:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with suppression`

Per ogni avviso in VM1, il gruppo di azioni AG1 viene attivato una sola volta. Le azioni e le notifiche per ogni avviso in VM2 e VM3 verranno soppresse.

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Cosa accade se sono definite una regola di avviso e una regola di azione per la stessa risorsa che chiama gruppi di azioni diversi? Ad esempio, **VM1** nello scenario seguente:

   `alert rule rule1 on VM1 with action group AG2`

   `action rule AR1 defined for VM1 with action group AG1`

Per ogni avviso in VM1, il gruppo di azioni AG1 viene attivato una sola volta. Ogni volta che viene attivata la regola di avviso "rule1", verrà attivato anche AG2. I gruppi di azioni definiti all'interno delle regole di azione e delle regole di avviso funzionano in modo indipendente, senza deduplicazione.

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sugli avvisi in Azure](./alerts-overview.md)
