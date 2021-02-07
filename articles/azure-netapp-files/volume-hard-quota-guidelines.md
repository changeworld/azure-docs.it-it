---
title: Cosa comporta la modifica della quota hardware del volume per il servizio Azure NetApp Files | Microsoft Docs
description: Viene descritta la modifica apportata all'utilizzo della quota hardware del volume, come pianificare la modifica e come monitorare e gestire le capacità.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: b-juche
ms.openlocfilehash: 69a970b7b088795602dfb842b20ad6457d5f27c4
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808226"
---
# <a name="what-changing-to-volume-hard-quota-means-for-your-azure-netapp-files-service"></a>Cosa comporta la modifica della quota hardware del volume per il servizio Azure NetApp Files

Dall'inizio del servizio, Azure NetApp Files ha utilizzato un meccanismo di provisioning e crescita automatico del pool di capacità. Il thin provisioning dei volumi Azure NetApp Files viene eseguito in un pool di capacità con provisioning del cliente di un livello e delle dimensioni selezionati. Le dimensioni del volume (quote) vengono usate per offrire prestazioni e capacità e le quote possono essere regolate in tempo reale in qualsiasi momento. Questo comportamento significa che, attualmente, la quota del volume è una leva di prestazioni utilizzata per controllare la larghezza di banda del volume. Attualmente, i pool di capacità di sottoposizione aumentano automaticamente quando la capacità si riempie.   

> [!IMPORTANT] 
> Il comportamento Azure NetApp Files del provisioning del pool di volumi e capacità cambierà in un meccanismo *manuale* e *controllabile* . **A partire dal 15 marzo 2021, le dimensioni del volume (quota) gestiranno le prestazioni della larghezza di banda, oltre alla capacità di cui è stato effettuato il provisioning, e i pool di capacità sottostanti non aumenteranno automaticamente.** 

## <a name="reasons-for-the-change-to-volume-hard-quota"></a>Motivi per la modifica della quota hardware del volume

Molti clienti hanno indicato tre principali problemi con il comportamento *iniziale* :
* I client VM vedranno la capacità con thin provisioning (100 TiB) di un determinato volume quando si usa lo spazio del sistema operativo o gli strumenti di monitoraggio della capacità, garantendo una visibilità non accurata della capacità del lato applicazione o client.
* I proprietari dell'applicazione non hanno il controllo dello spazio del pool di capacità con provisioning (e del costo associato), a causa del comportamento di aumento automatico del pool di capacità. Questa situazione è complessa negli ambienti in cui i "processi di run-away" possono riempirsi rapidamente e aumentare la capacità e i costi di cui è stato effettuato il provisioning.
* I clienti vogliono visualizzare e mantenere una correlazione diretta tra le dimensioni del volume (quota) e le prestazioni. Con il comportamento attuale di (implicito) oversubscriptioning di un volume (Capacity-Wise) e aumento automatico del pool, i clienti non hanno una correlazione diretta, fino a quando la quota del volume non è stata impostata o reimpostata attivamente. 

Molti clienti hanno richiesto il controllo diretto sulla capacità di cui è stato effettuato il provisioning. Vogliono controllare e bilanciare l'utilizzo e la capacità di archiviazione. Desiderano inoltre controllare i costi insieme alla visibilità lato applicazione e lato client della capacità e delle prestazioni disponibili, utilizzate e sottoposte a provisioning dei volumi delle applicazioni. 

## <a name="what-is-the-volume-hard-quota-change"></a>Qual è la modifica della quota hardware del volume   

Con la modifica della quota hardware del volume, non sarà più possibile eseguire il thin provisioning dei volumi di Azure NetApp Files al massimo 100 TiB. Il provisioning dei volumi verrà eseguito in corrispondenza delle dimensioni effettive configurate (quota). Inoltre, i pool di capacità di sottoposizione non aumenteranno automaticamente al raggiungimento del consumo completo della capacità. Questa modifica rifletterà il comportamento di Azure Managed disks, anch ' esso sottoposta a provisioning così com'è, senza un aumento automatico della capacità.

Si consideri, ad esempio, un volume di Azure NetApp Files configurato con una dimensione di 1-TiB (quota) in un pool di capacità del livello di servizio Ultra 4-TiB. Un'applicazione continua a scrivere dati nel volume.

Comportamento *iniziale* :  
* Larghezza di banda prevista: 128 MiB/s
* Capacità totale utilizzabile (e visibile al client): 100 TiB   
    Non sarà possibile scrivere più dati nel volume oltre questa dimensione.
* Pool di capacità: aumenta automaticamente con incrementi di 1 TiB quando è pieno.
* Modifica quota volume: modifica solo le prestazioni (larghezza di banda) del volume. Non modifica la capacità visibile o utilizzabile del client.

Comportamento *modificato* :  
* Larghezza di banda prevista: 128 MiB/s
* Capacità totale utilizzabile (e visibile al client): 1 TiB non sarà possibile scrivere più dati nel volume oltre questa dimensione.
* Pool di capacità: mantiene le dimensioni di 4 TiB e non aumenta automaticamente. 
* Modifica della quota del volume: modifica le prestazioni (larghezza di banda) e il client visibile o la capacità utilizzabile del volume.

È necessario monitorare in modo proattivo l'utilizzo di volumi Azure NetApp Files e pool di capacità. È necessario modificare in modo intenzionale l'utilizzo del volume e del pool per un consumo quasi esaurito. Azure NetApp Files continuerà a consentire [operazioni di ridimensionamento del pool di capacità e volumi in tempo reale](azure-netapp-files-resize-capacity-pools-or-volumes.md).

## <a name="how-to-operationalize-the-volume-hard-quota-change"></a>Come rendere operativo la modifica della quota hardware del volume

In questa sezione vengono fornite indicazioni su come rendere operativo la modifica alla quota hardware del volume per una transizione senza problemi. Fornisce anche informazioni dettagliate per la gestione dei volumi e dei pool di capacità attualmente sottoposti a provisioning, sul monitoraggio continuo e sulle opzioni di gestione degli avvisi e della capacità.

### <a name="currently-provisioned-volumes-and-capacity-pools"></a>Volumi e pool di capacità attualmente sottoposti a provisioning

A causa della modifica della quota hardware del volume, è necessario modificare il modello operativo. I volumi con provisioning e i pool di capacità richiedono la gestione delle capacità in corso.  Poiché il comportamento modificato si verificherà immediatamente, il team Azure NetApp Files consiglia una serie di misure correttive monouso per i volumi di capacità e i volumi precedentemente sottoposti a provisioning, come descritto in questa sezione.

#### <a name="one-time-corrective-or-preventative-measures-recommendations"></a>Raccomandazioni di misure correttive o preventive monouso  

La modifica della quota hardware del volume comporterà modifiche in provisioning e capacità disponibile per i volumi e i pool di cui è stato effettuato il provisioning in precedenza. Di conseguenza, è possibile che si verifichino problemi di allocazione della capacità. Per evitare situazioni di esaurimento dello spazio a breve termine per i clienti, il team di Azure NetApp Files consiglia le seguenti misure correttive/preventive una volta: 

* Dimensioni del volume sottoposte a **provisioning**:   
    Ridimensionare tutti i volumi di cui è stato effettuato il provisioning in modo che dispongano di un buffer appropriato in base alla frequenza di modifica e al tempo di [ingrandimento](azure-netapp-files-resource-limits.md#resource-limits)per il ridimensionamento, ad esempio il 20% in base alle considerazioni tipiche del carico di lavoro, con un massimo di 100 TIB (ovvero il limite La nuova dimensione del volume, inclusa la capacità del buffer, deve basarsi sui fattori seguenti:
    * Capacità del volume di cui è stato effettuato il **provisioning** , nel caso in cui la capacità utilizzata sia inferiore alla quota del volume con provisioning.
    * Capacità del volume **utilizzata** , nel caso in cui la capacità utilizzata sia superiore alla quota del volume di cui è stato effettuato il provisioning.  
    Non è previsto alcun addebito aggiuntivo per l'aumento della capacità a livello di volume se non è necessario aumentare il pool di capacità. Per effetto di questa modifica, è possibile che si osservi un *aumento* del limite di larghezza di banda per il volume (nel caso in cui venga usato il [tipo di pool di capacità QoS automatica](azure-netapp-files-understand-storage-hierarchy.md#qos_types) ).

* **Dimensioni del pool di capacità con provisioning**:   
    Una volta adattate le dimensioni del volume, se la somma delle dimensioni dei volumi aumenta rispetto alla dimensione del pool di capacità host, il pool di capacità dovrà essere aumentato a una dimensione maggiore o uguale alla somma dei volumi, con un massimo di 500 TiB (ovvero il limite delle [dimensioni del pool di capacità](azure-netapp-files-resource-limits.md#resource-limits)). La capacità aggiuntiva del pool di capacità sarà soggetta ai costi di ACR come di consueto.

È consigliabile collaborare con gli specialisti Azure NetApp Files per convalidare l'ambiente, se è necessaria assistenza per la configurazione del monitoraggio o degli avvisi, come descritto nelle sezioni seguenti.

### <a name="ongoing-capacity-management"></a>Gestione della capacità in corso  

Una volta eseguite le misure correttive monouso, è necessario raggruppare i processi in corso per monitorare e gestire la capacità. Le sezioni seguenti forniscono suggerimenti e alternative sulla gestione e sul monitoraggio della capacità.

### <a name="monitor-capacity-utilization"></a>Monitora l'utilizzo della capacità

È possibile monitorare l'utilizzo della capacità a vari livelli. 

#### <a name="vm-level-monitoring"></a>Monitoraggio a livello di macchina virtuale 

Il livello più elevato di monitoraggio (più vicino all'applicazione) è all'interno della macchina virtuale dell'applicazione. Si osserverà una modifica del comportamento nella creazione di report di capacità dall'interno del sistema operativo client della macchina virtuale.

Nei due scenari seguenti, si consideri un Azure NetApp Files volume configurato con una dimensione di 1-TiB (quota) in un pool di capacità a livello di servizio a 4 TiB e ultra. 

##### <a name="windows"></a>Windows

I client Windows possono controllare la capacità utilizzata e disponibile di un volume usando le proprietà dell'unità mappata alla rete. È possibile utilizzare l'   ->    ->  opzione **Proprietà** unità di esplorazione.  

Gli esempi seguenti illustrano la creazione di report sulla capacità del volume in Windows *prima* del comportamento modificato:

![Schermate che illustrano la capacità di archiviazione di esempio di un volume prima della modifica del comportamento.](../media/azure-netapp-files/hard-quota-windows-capacity-before.png)

È anche possibile usare il `dir` comando al prompt dei comandi, come illustrato di seguito:

![Screenshot che mostra l'uso di un comando per visualizzare la capacità di archiviazione per un volume prima della modifica del comportamento.](../media/azure-netapp-files/hard-quota-command-capacity-before.png)

Gli esempi seguenti illustrano la creazione di report sulla capacità del volume in Windows *dopo* il comportamento modificato:

![Schermate che mostrano la capacità di archiviazione di esempio di un volume dopo la modifica del comportamento.](../media/azure-netapp-files/hard-quota-windows-capacity-after.png)

Nell'esempio seguente viene illustrato l' `dir` output del comando:  

![Screenshot che mostra l'uso di un comando per visualizzare la capacità di archiviazione per un volume dopo la modifica del comportamento.](../media/azure-netapp-files/hard-quota-command-capacity-after.png)

##### <a name="linux"></a>Linux 

I client Linux possono controllare la capacità utilizzata e disponibile di un volume usando il [ `df` comando](https://linux.die.net/man/1/df). L' `-h` opzione visualizzerà le dimensioni, lo spazio usato e lo spazio disponibile nel formato leggibile, usando le dimensioni unità M, G e T.

Nell'esempio seguente viene illustrata la creazione di report sulla capacità del volume in Linux *prima* del comportamento modificato:  

![Screenshot che mostra l'uso di Linux per visualizzare la capacità di archiviazione per un volume prima della modifica del comportamento.](../media/azure-netapp-files/hard-quota-linux-capacity-before.png)

Nell'esempio seguente viene illustrata la creazione di report sulla capacità del volume in Linux *dopo* il comportamento modificato:  

![Screenshot che mostra l'uso di Linux per visualizzare la capacità di archiviazione per un volume dopo la modifica del comportamento.](../media/azure-netapp-files/hard-quota-linux-capacity-after.png)


### <a name="configure-alerts-using-anfcapacitymanager"></a>Configurare gli avvisi tramite ANFCapacityManager

È possibile usare lo strumento ANFCapacityManager per le app per la logica supportato dalla community per monitorare la capacità di Azure NetApp Files e ricevere avvisi personalizzati. Lo strumento ANFCapacityManager è disponibile nella [pagina ANFCapacityManager GitHub](https://github.com/ANFTechTeam/ANFCapacityManager).

ANFCapacityManager è un'app per la logica di Azure che gestisce le regole di avviso basate sulla capacità. Aumenta automaticamente le dimensioni dei volumi per impedire che i volumi di Azure NetApp Files esauriscono lo spazio. È facile da distribuire e fornisce le seguenti funzionalità di Gestione avvisi:

* Quando viene creato un volume o un pool di capacità Azure NetApp Files, ANFCapacityManager crea una regola di avviso per la metrica in base alla soglia utilizzata per la percentuale specificata.
* Quando un pool o un volume di capacità Azure NetApp Files viene ridimensionato, ANFCapacityManager modifica la regola di avviso della metrica in base alla soglia utilizzata per la capacità specificata. Se la regola di avviso non esiste, verrà creata.
* Quando un pool o un volume di capacità Azure NetApp Files viene eliminato, la regola di avviso della metrica corrispondente verrà eliminata.

È possibile configurare le impostazioni di avviso chiave seguenti:  

* **Soglia completa del pool di capacità** : questa impostazione determina la soglia utilizzata che attiva un avviso per i pool di capacità. Il valore 90 comporta l'attivazione di un avviso quando il pool di capacità raggiunge il 90% utilizzato.
* **Soglia completa volume%** : questa impostazione determina la soglia utilizzata che attiva un avviso per i volumi. Il valore 80 comporta l'attivazione di un avviso quando il volume raggiunge il 80% utilizzato.
* **Gruppo di azione esistente per le notifiche di capacità** . questa impostazione è il gruppo di azioni che verrà attivato per gli avvisi basati sulla capacità. Questa impostazione deve essere creata in precedenza dall'utente. Il gruppo di azioni può inviare messaggi di posta elettronica, SMS o altri formati.

Nella figura seguente viene illustrata la configurazione degli avvisi:  

![Illustrazione che mostra la configurazione degli avvisi usando ANFCapacityManager.](../media/azure-netapp-files/hard-quota-anfcapacitymanager-configuration.png)

Dopo l'installazione di ANFCapacityManager, è possibile prevedere il comportamento seguente: quando viene creato, modificato o eliminato un Azure NetApp Files pool di capacità o un volume, l'app per la logica creerà automaticamente, modificherà o eliminerà una regola di avviso metrica basata sulla capacità con il nome `ANF_Pool_poolname` o `ANF_Volume_poolname_volname` . 

### <a name="manage-capacity"></a>Gestire la capacità

Oltre al monitoraggio e agli avvisi, è necessario incorporare anche una procedura di gestione della capacità dell'applicazione per gestire Azure NetApp Files (aumento) del consumo di capacità. Quando si riempie un volume Azure NetApp Files o un pool di capacità, [è possibile fornire capacità aggiuntiva in tempo reale senza alcuna distorsione dell'applicazione](azure-netapp-files-resize-capacity-pools-or-volumes.md). Questa sezione descrive vari modi manuali e automatici per aumentare lo spazio disponibile nel pool di volumi e capacità in base alle esigenze.
 
#### <a name="manual"></a>Manuale 

È possibile usare il portale o l'interfaccia della riga di comando per aumentare manualmente il volume o le dimensioni del pool di capacità. 

##### <a name="portal"></a>Portale 

Se necessario, è possibile [modificare le dimensioni di un volume](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-volume) . L'utilizzo della capacità di un volume concorre al calcolo della capacità di cui è stato effettuato il provisioning del pool.

1. Nel pannello Gestisci account NetApp fare clic su **Volumi**.  
2. Fare clic con il pulsante destro del mouse sul nome del volume che si desidera ridimensionare oppure fare clic sull' `…` icona alla fine della riga del volume per visualizzare il menu di scelta rapida. 
3. Usare le opzioni del menu di scelta rapida per ridimensionare o eliminare il volume.   

   ![Screenshot che mostra le opzioni del menu di scelta rapida per un volume.](../media/azure-netapp-files/hard-quota-volume-options.png) 

   ![Screenshot che mostra la finestra di aggiornamento della quota del volume.](../media/azure-netapp-files/hard-quota-update-volume-quota.png) 

In alcuni casi, il pool di capacità host non dispone di capacità sufficiente per ridimensionare i volumi. Tuttavia, è possibile [modificare le dimensioni del pool di capacità](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-the-capacity-pool) in incrementi di 1 TIB o decrementi. Le dimensioni del pool di capacità non possono essere inferiori a 4 TiB. *Il ridimensionamento del pool di capacità modifica la capacità di Azure NetApp Files acquistata.*

1. Nel pannello Gestisci account NetApp fare clic sul pool di capacità che si intende ridimensionare.
2. Fare clic con il pulsante destro del mouse sul nome del pool di capacità oppure fare clic sull' `…` icona alla fine della riga del pool di capacità per visualizzare il menu di scelta rapida.
3. Usare le opzioni del menu di scelta rapida per ridimensionare o eliminare il pool di capacità.    

   ![Screenshot che mostra le opzioni del menu di scelta rapida per un pool di capacità.](../media/azure-netapp-files/hard-quota-pool-options.png) 

   ![Screenshot che mostra la finestra Ridimensiona pool.](../media/azure-netapp-files/hard-quota-update-resize-pool.png) 


##### <a name="cli-or-powershell"></a>Interfaccia della riga di comando o PowerShell

È possibile usare gli [strumenti dell'interfaccia](azure-netapp-files-sdk-cli.md#cli-tools)della riga di comando di Azure NetApp files, tra cui l'interfaccia della riga di comando di Azure e Azure PowerShell, per modificare manualmente le dimensioni del pool di capacità  I due comandi seguenti possono essere usati per gestire Azure NetApp Files risorse del volume e del pool:  

* [`az netappfiles pool`](https://docs.microsoft.com/cli/azure/netappfiles/pool?view=azure-cli-latest&preserve-view=true)
* [`az netappfiles volume`](https://docs.microsoft.com/cli/azure/netappfiles/volume?view=azure-cli-latest&preserve-view=true)

Per gestire le risorse di Azure NetApp Files usando l'interfaccia della riga di comando di Azure, è possibile aprire il portale di Azure e selezionare il collegamento **cloud Shell** di Azure nella parte superiore della barra dei menu: 

[![Screenshot che illustra come accedere a cloud Shell collegamento. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png#lightbox)

Questa azione apre il Azure Cloud Shell:

[![Screenshot che mostra cloud Shell finestra. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png#lightbox)

Gli esempi seguenti usano i comandi per [visualizzare](https://docs.microsoft.com/cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-show&preserve-view=true) e [aggiornare](https://docs.microsoft.com/cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-update&preserve-view=true) le dimensioni di un volume:
 
[![Screenshot che mostra l'uso di PowerShell per visualizzare le dimensioni del volume. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png#lightbox)

[![Screenshot che illustra l'uso di PowerShell per aggiornare le dimensioni del volume. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png#lightbox)

Gli esempi seguenti usano i comandi per [visualizzare](https://docs.microsoft.com/cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-show&preserve-view=true) e [aggiornare](https://docs.microsoft.com/cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-update&preserve-view=true) le dimensioni di un pool di capacità:

[![Screenshot che mostra l'uso di PowerShell per visualizzare le dimensioni del pool di capacità. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png#lightbox) 

[![Screenshot che illustra l'uso di PowerShell per aggiornare le dimensioni del pool di capacità. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png#lightbox)

#### <a name="automated"></a>Automatizzata  

È possibile creare un processo automatizzato per gestire il comportamento modificato.

##### <a name="rest-api"></a>API REST   

L'API REST per il servizio Azure NetApp Files definisce le operazioni HTTP sulle risorse, ad esempio l'account di NetApp, il pool di capacità, i volumi e gli snapshot. La specifica dell'API REST per Azure NetApp Files viene pubblicata tramite la [pagina Azure NetApp Files gestione risorse GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)]. È possibile trovare il [codice di esempio da usare con le API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable/2020-06-01/examples) in GitHub.

Vedere [sviluppare per Azure NetApp files con l'API REST](azure-netapp-files-develop-with-rest-api.md). 

##### <a name="rest-api-using-powershell"></a>API REST con PowerShell  

L'API REST per il servizio Azure NetApp Files definisce le operazioni HTTP sulle risorse, ad esempio l'account di NetApp, il pool di capacità, i volumi e gli snapshot. La [specifica dell'API REST per Azure NetApp files](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager) viene pubblicata tramite GitHub.

Vedere [sviluppare per Azure NetApp files con l'API REST usando PowerShell](develop-rest-api-powershell.md).

##### <a name="capacity-management-using-anfcapacitymanager"></a>Gestione della capacità con ANFCapacityManager

ANFCapacityManager è un'app per la logica di Azure che gestisce le regole di avviso basate sulla capacità. Aumenta automaticamente le dimensioni dei volumi per impedire che i volumi di Azure NetApp Files esauriscono lo spazio. Oltre a inviare gli avvisi, può consentire l'aumento automatico delle dimensioni del pool di volumi e capacità per impedire che i volumi di Azure NetApp Files esauriscono lo spazio disponibile: 

* Facoltativamente, quando un volume Azure NetApp Files raggiunge la percentuale di soglia utilizzata specificata, la quota del volume (dimensioni) verrà aumentata in base alla percentuale specificata tra 10-100.  
* Se l'aumento delle dimensioni del volume supera la capacità del pool di capacità contenitore, verranno aumentate anche le dimensioni del pool di capacità in base alle nuove dimensioni del volume.

È possibile configurare la seguente impostazione di gestione della capacità chiave:  

* **Aumento automatico percentuale aumento** -percentuale delle dimensioni del volume esistente per aumentare automaticamente il volume se raggiunge la soglia di **% piena** specificata. Un valore pari a 0 (zero) Disabilita la funzionalità di aumento automatico delle dimensioni. È consigliabile utilizzare un valore compreso tra 10 e 100.

    ![Screenshot che mostra l'impostazione della percentuale di incremento automatico del volume.](../media/azure-netapp-files/hard-quota-volume-anfcapacitymanager-auto-grow-percent.png) 

## <a name="faq"></a>Domande frequenti 

Questa sezione risponde ad alcune domande sulla modifica della quota hardware del volume. 

### <a name="does-snapshot-space-count-towards-the-usable-or-provisioned-capacity-of-a-volume"></a>Lo spazio dello snapshot viene conteggiato per la capacità utilizzabile o sottoposta a provisioning di un volume?

Sì, la capacità dello snapshot utilizzata viene conteggiata per lo spazio sottoposto a provisioning nel volume. Se il volume viene eseguito in modo completo, prendere in considerazione due opzioni di monitoraggio e aggiornamento:

* Ridimensionare il volume come descritto in questo articolo.
* Rimuovere gli snapshot meno recenti per liberare spazio nel volume di hosting.

### <a name="does-this-change-mean-the-volume-auto-grow-behavior-will-disappear-from-azure-netapp-files"></a>Questa modifica significa che il comportamento di aumento automatico del volume scomparirà da Azure NetApp Files?

Un malinteso comune è che i *volumi* di Azure NetApp files aumenteranno automaticamente al termine del riempimento. È stato effettuato il thin provisioning dei volumi con una dimensione di 100 TiB, indipendentemente dalla quota di set effettiva, mentre il *pool di capacità* di sottodisposizione aumenta automaticamente con incrementi di 1 tib. Questa modifica affronterà le dimensioni del *volume* (visibile e utilizzabile) per la quota impostata e i *pool di capacità* non aumenteranno automaticamente. Questa modifica comporta uno spazio sul lato client accurato e un report sulla capacità. Evita il consumo di capacità "Runaway".

### <a name="does-this-change-have-any-effect-on-volumes-replicated-with-cross-region-replication-preview"></a>Questa modifica ha effetto sui volumi replicati con la replica tra aree (anteprima)? 

La quota del volume rigido non viene applicata ai volumi di destinazione della replica.

### <a name="does-this-change-have-any-effect-on-metrics-currently-available-in-azure-monitor"></a>Questa modifica ha effetto sulle metriche attualmente disponibili in monitoraggio di Azure?

Le metriche del portale e le statistiche di monitoraggio di Azure rifletteranno in modo accurato il nuovo modello di allocazione e utilizzo.

### <a name="does-this-change-have-any-effect-on-the-resource-limits-for-azure-netapp-files"></a>Questa modifica ha effetto sui limiti delle risorse per Azure NetApp Files?

Non sono state apportate modifiche ai limiti delle risorse per Azure NetApp Files oltre le modifiche alla quota descritte in questo articolo.

### <a name="is-there-an-example-anfcapacitymanager-workflow"></a>Esiste un esempio di flusso di lavoro ANFCapacityManager?  

Sì. Vedere la [pagina del flusso di lavoro volume AutoGrow di esempio GitHub](https://github.com/ANFTechTeam/ANFCapacityManager/blob/master/ResizeWorkflow.md).

### <a name="is-anfcapacitymanager-microsoft-supported"></a>ANFCapacityManager è supportato da Microsoft?  

[L'app per la logica ANFCapacityManager viene fornita così com'è e non è supportata da NetApp o Microsoft](https://github.com/ANFTechTeam/ANFCapacityManager#disclaimer). Si consiglia di modificare in base all'ambiente o ai requisiti specifici. È consigliabile testare la funzionalità prima di distribuirla in ambienti aziendali critici o di produzione.

### <a name="how-can-i-report-a-bug-or-submit-a-feature-request-for-anfcapacitymanger"></a>Come è possibile segnalare un bug o inviare una richiesta di funzionalità per ANFCapacityManger?
È possibile inviare bug e richieste di funzionalità facendo clic su **nuovo problema** nella [pagina ANFCapacityManager GitHub](https://github.com/ANFTechTeam/ANFCapacityManager/issues).

## <a name="next-steps"></a>Passaggi successivi
* [Ridimensionare un pool di capacità o un volume](azure-netapp-files-resize-capacity-pools-or-volumes.md) 
* [Metriche per Azure NetApp Files](azure-netapp-files-metrics.md) 