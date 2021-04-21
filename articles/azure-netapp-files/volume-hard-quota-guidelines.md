---
title: Che cosa significa modificare la quota rigida del volume per Azure NetApp Files servizio | Microsoft Docs
description: Descrive la modifica all'uso della quota rigida del volume, come pianificare la modifica e come monitorare e gestire le capacità.
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
ms.date: 03/29/2021
ms.author: b-juche
ms.openlocfilehash: 5e7f71f91e5778b4f096bb760bfe5a0a89b5cbcb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764280"
---
# <a name="what-changing-to-volume-hard-quota-means-for-your-azure-netapp-files-service"></a>Che cosa significa modificare la quota rigida del volume per il Azure NetApp Files locale

Dall'inizio del servizio, Azure NetApp Files il provisioning del pool di capacità e il meccanismo di crescita automatica. Azure NetApp Files i volumi vengono sottoposto a thin provisioning in un pool di capacità sottoposto a provisioning del cliente di un livello e di una dimensione selezionati. Le dimensioni del volume (quote) vengono usate per fornire prestazioni e capacità e le quote possono essere modificate in qualsiasi momento. Questo comportamento significa che, attualmente, la quota del volume è una leve di prestazioni usata per controllare la larghezza di banda per il volume. Attualmente, i pool di capacità sottostanti aumentano automaticamente quando la capacità si riempie.   

> [!IMPORTANT] 
> Il Azure NetApp Files del provisioning del pool di volumi e capacità cambierà in un *meccanismo* manuale *e controllabile.* **A partire dal 30 aprile 2021 (aggiornato), le dimensioni del volume (quota) gestiranno le prestazioni della larghezza di banda, nonché la capacità con provisioning e i pool di capacità sottostanti non aumentano più automaticamente.** 

## <a name="reasons-for-the-change-to-volume-hard-quota"></a>Motivi della modifica alla quota disco rigido del volume

Molti clienti hanno indicato tre problemi principali con il *comportamento* iniziale:
* I client vm visualizzano la capacità con thin provisioning (100 TiB) di qualsiasi volume specificato quando usano strumenti di monitoraggio dello spazio del sistema operativo o della capacità, offrendo una visibilità della capacità non accurata sul lato client o dell'applicazione.
* I proprietari delle applicazioni non avrebbero alcun controllo dello spazio del pool di capacità di cui è stato effettuato il provisioning (e dei costi associati), a causa del comportamento di crescita automatica del pool di capacità. Questa situazione è ingombrante negli ambienti in cui i "processi di run-away" potrebbero rapidamente riempire e aumentare la capacità e i costi di cui è stato effettuato il provisioning.
* I clienti vogliono visualizzare e mantenere una correlazione diretta tra le dimensioni del volume (quota) e le prestazioni. Con il comportamento corrente di oversubscribing (implicito) di un volume (per capacità) e della crescita automatica del pool, i clienti non hanno una correlazione diretta, fino a quando la quota del volume non è stata impostata o reimpostata attivamente. 

Molti clienti hanno richiesto il controllo diretto sulla capacità di cui è stato effettuato il provisioning. Vogliono controllare e bilanciare la capacità di archiviazione e l'utilizzo. Vogliono anche controllare i costi insieme alla visibilità lato applicazione e lato client della capacità disponibile, usata e di cui è stato effettuato il provisioning e le prestazioni dei volumi dell'applicazione. 

## <a name="what-is-the-volume-hard-quota-change"></a>Qual è la modifica della quota rigida del volume?   

Con la modifica della quota rigida del volume, Azure NetApp Files non verrà più eseguito il thin provisioning dei volumi al massimo 100 TiB. Verrà eseguito il provisioning dei volumi con le dimensioni effettive configurate (quota). Inoltre, i pool di capacità sottostanti non aumentano più automaticamente al raggiungimento dell'utilizzo della capacità completa. Questa modifica riflette il comportamento, ad esempio i dischi gestiti di Azure, di cui viene anche eseguito il provisioning così come sono, senza aumento automatico della capacità.

Si consideri ad esempio un volume Azure NetApp Files configurato con dimensioni di 1 TiB (quota) in un pool di capacità a livello di servizio Ultra da 4 TiB. Un'applicazione scrive continuamente i dati nel volume.

Comportamento  iniziale:  
* Larghezza di banda prevista: 128 MiB/s
* Capacità totale utilizzabile (e visibile al client): 100 TiB   
    Non sarà possibile scrivere altri dati nel volume oltre queste dimensioni.
* Pool di capacità: aumenta automaticamente con incrementi di 1 TiB quando è pieno.
* Modifica della quota del volume: modifica solo le prestazioni (larghezza di banda) del volume. Non modifica la capacità visibile o utilizzabile del client.

Comportamento *modificato:*  
* Larghezza di banda prevista: 128 MiB/s
* Capacità totale utilizzabile (e visibile al client): 1 TiB Non sarà possibile scrivere altri dati nel volume oltre queste dimensioni.
* Pool di capacità: le dimensioni rimangono di 4 TiB e non aumentano automaticamente. 
* Modifica della quota del volume: modifica le prestazioni (larghezza di banda) e la capacità del client visibile o utilizzabile del volume.

È necessario monitorare in modo proattivo l'utilizzo Azure NetApp Files volumi e pool di capacità. È necessario modificare di proposito l'utilizzo del volume e del pool per un utilizzo quasi completo. Azure NetApp Files continuerà a consentire le operazioni di ridimensionamento in tempo reale del volume e del [pool di capacità](azure-netapp-files-resize-capacity-pools-or-volumes.md).

## <a name="how-to-operationalize-the-volume-hard-quota-change"></a>Come operazionare la modifica della quota rigida del volume

Questa sezione fornisce indicazioni su come operazionare la modifica alla quota rigida del volume per una transizione senza problemi. Fornisce anche informazioni dettagliate per la gestione dei volumi e dei pool di capacità attualmente di cui è stato effettuato il provisioning, il monitoraggio in corso e le opzioni di gestione degli avvisi e della capacità.

### <a name="currently-provisioned-volumes-and-capacity-pools"></a>Volumi e pool di capacità attualmente con provisioning

A causa della modifica della quota rigida del volume, è necessario modificare il modello operativo. I volumi di cui è stato effettuato il provisioning e i pool di capacità richiederanno una gestione continua della capacità.  Poiché il comportamento modificato si verifica immediatamente, il team di Azure NetApp Files consiglia una serie di misure correttive una sola volta per i volumi e i pool di capacità esistenti con provisioning precedente, come descritto in questa sezione.

#### <a name="one-time-corrective-or-preventative-measures-recommendations"></a>Raccomandazioni sulle misure correttive o preventive una sola volta  

La modifica della quota rigida del volume comporta modifiche nella capacità disponibile e con provisioning per i volumi e i pool di cui è stato eseguito il provisioning in precedenza. Di conseguenza, potrebbero verificarsi alcune difficoltà di allocazione della capacità. Per evitare situazioni di breve durata fuori spazio per i clienti, il team Azure NetApp Files consiglia le misure correttive/preventive seguenti: 

* **Dimensioni dei volumi di cui è stato effettuato il provisioning:**   
    Ridimensionare ogni volume di cui è stato effettuato il provisioning in modo che abbia un buffer appropriato in base alla frequenza di modifica e alla frequenza di avviso o al tempo di turnaround di ridimensionamento (ad esempio, il 20% in base a considerazioni tipiche sui carichi di lavoro), con un massimo di 100 TiB (ovvero il limite delle dimensioni del [volume).](azure-netapp-files-resource-limits.md#resource-limits) Le nuove dimensioni del volume, inclusa la capacità del buffer, devono essere basate sui fattori seguenti:
    * **Capacità del** volume di cui è stato effettuato il provisioning, nel caso in cui la capacità usata sia inferiore alla quota del volume di cui è stato effettuato il provisioning.
    * **Capacità** del volume usata, nel caso in cui la capacità usata sia superiore alla quota del volume di cui è stato effettuato il provisioning.  
    Non è necessario alcun addebito aggiuntivo per l'aumento della capacità a livello di volume se non è necessario aumentare il pool di capacità sottostante. Come effetto di questa modifica, è  possibile osservare un aumento del limite di larghezza di banda per il volume (nel caso in cui venga usato il tipo di pool di capacità [QoS](azure-netapp-files-understand-storage-hierarchy.md#qos_types) automatico).

* **Dimensioni del pool di capacità di cui è stato effettuato il provisioning:**   
    Dopo le modifiche alle dimensioni dei volumi, se la somma delle dimensioni dei volumi diventa maggiore della dimensione del pool di capacità di hosting, il pool di capacità dovrà essere aumentato a una dimensione uguale o maggiore della somma dei volumi, con un massimo di 500 TiB (ovvero il limite di dimensioni del [pool](azure-netapp-files-resource-limits.md#resource-limits)di capacità). La capacità aggiuntiva del pool di capacità sarà soggetta normalmente all'addebito di ACR.

È consigliabile collaborare con gli Azure NetApp Files per convalidare l'ambiente, se è necessario assistenza per la configurazione del monitoraggio o degli avvisi, come descritto nelle sezioni seguenti.

### <a name="ongoing-capacity-management"></a>Gestione continua della capacità  

Dopo aver eseguito le misure correttive una sola volta, è consigliabile riunire i processi in corso per monitorare e gestire la capacità. Le sezioni seguenti forniscono suggerimenti e alternative sul monitoraggio e la gestione della capacità.

### <a name="monitor-capacity-utilization"></a>Monitorare l'utilizzo della capacità

È possibile monitorare l'utilizzo della capacità a vari livelli. 

#### <a name="vm-level-monitoring"></a>Monitoraggio a livello di macchina virtuale 

Il livello di monitoraggio più elevato (più vicino all'applicazione) è dall'interno della macchina virtuale dell'applicazione. Si osserverà una modifica del comportamento nella creazione di report sulla capacità dall'interno del sistema operativo client della macchina virtuale.

Nei due scenari seguenti si consideri un volume Azure NetApp Files configurato con dimensioni di 1 TiB (quota) in un pool di capacità a livello di servizio Ultra a 4 TiB. 

##### <a name="windows"></a>Windows

I client Windows possono controllare la capacità usata e disponibile di un volume usando le proprietà delle unità mappate alla rete. È possibile usare **l'opzione Proprietà**  ->  **unità**  ->  **esplora** risorse.  

Gli esempi seguenti illustrano la creazione di report sulla capacità del volume in Windows *prima* del comportamento modificato:

![Screenshot che mostrano la capacità di archiviazione di esempio di un volume prima della modifica del comportamento.](../media/azure-netapp-files/hard-quota-windows-capacity-before.png)

È anche possibile usare il `dir` comando al prompt dei comandi, come illustrato di seguito:

![Screenshot che mostra l'uso di un comando per visualizzare la capacità di archiviazione per un volume prima della modifica del comportamento.](../media/azure-netapp-files/hard-quota-command-capacity-before.png)

Gli esempi seguenti illustrano la creazione di report sulla capacità dei volumi in Windows *dopo* il comportamento modificato:

![Screenshot che mostrano la capacità di archiviazione di esempio di un volume dopo la modifica del comportamento.](../media/azure-netapp-files/hard-quota-windows-capacity-after.png)

L'esempio seguente mostra `dir` l'output del comando:  

![Screenshot che mostra l'uso di un comando per visualizzare la capacità di archiviazione per un volume dopo la modifica del comportamento.](../media/azure-netapp-files/hard-quota-command-capacity-after.png)

##### <a name="linux"></a>Linux 

I client Linux possono controllare la capacità usata e disponibile di un volume usando il [ `df` comando](https://linux.die.net/man/1/df). L'opzione mostra le dimensioni, lo spazio usato e lo spazio disponibile in formato leggibile dall'utente, usando le dimensioni delle unità `-h` M, G e T.

L'esempio seguente illustra la creazione di report sulla capacità dei volumi in Linux *prima* del comportamento modificato:  

![Screenshot che mostra l'uso di Linux per visualizzare la capacità di archiviazione per un volume prima della modifica del comportamento.](../media/azure-netapp-files/hard-quota-linux-capacity-before.png)

L'esempio seguente illustra la creazione di report sulla capacità dei volumi in Linux *dopo* la modifica del comportamento:  

![Screenshot che mostra l'uso di Linux per visualizzare la capacità di archiviazione per un volume dopo la modifica del comportamento.](../media/azure-netapp-files/hard-quota-linux-capacity-after.png)


### <a name="configure-alerts-using-anfcapacitymanager"></a>Configurare gli avvisi usando ANFCapacityManager

È possibile usare lo strumento ANFCapacityManager di App per la logica supportate dalla community per monitorare Azure NetApp Files capacità e ricevere avvisi personalizzati. Lo strumento ANFCapacityManager è disponibile nella pagina [ANFCapacityManager di GitHub.](https://github.com/ANFTechTeam/ANFCapacityManager)

ANFCapacityManager è un'app per la logica di Azure che gestisce le regole di avviso basate sulla capacità. Aumenta automaticamente le dimensioni dei volumi per impedire Azure NetApp Files di spazio insufficiente. È facile da distribuire e offre le funzionalità Gestione avvisi seguenti:

* Quando viene creato Azure NetApp Files un volume o un pool di capacità, ANFCapacityManager crea una regola di avviso della metrica in base alla soglia specificata per percentuale utilizzata.
* Quando un pool Azure NetApp Files o un volume viene ridimensionato, ANFCapacityManager modifica la regola di avviso della metrica in base alla soglia specificata per la capacità utilizzata. Se la regola di avviso non esiste, verrà creata.
* Quando viene eliminato Azure NetApp Files pool di capacità o un volume, la regola di avviso della metrica corrispondente verrà eliminata.

È possibile configurare le impostazioni di avviso chiave seguenti:  

* **Soglia completa % pool di** capacità: questa impostazione determina la soglia utilizzata che attiva un avviso per i pool di capacità. Un valore pari a 90 causerebbe l'attivazione di un avviso quando il pool di capacità raggiunge il 90% di utilizzo.
* **% volume Soglia completa:** questa impostazione determina la soglia utilizzata che attiva un avviso per i volumi. Un valore pari a 80 causerebbe l'attivazione di un avviso quando il volume raggiunge l'80% di utilizzo.
* **Gruppo di azioni esistente per le notifiche sulla** capacità: questa impostazione è il gruppo di azioni che verrà attivato per gli avvisi basati sulla capacità. Questa impostazione deve essere creata in precedenza dall'utente. Il gruppo di azioni può inviare messaggi di posta elettronica, SMS o altri formati.

La figura seguente mostra la configurazione dell'avviso:  

![Illustrazione che mostra la configurazione degli avvisi tramite ANFCapacityManager.](../media/azure-netapp-files/hard-quota-anfcapacitymanager-configuration.png)

Dopo l'installazione di ANFCapacityManager, è possibile prevedere il comportamento seguente: quando viene creato, modificato o eliminato un volume o un pool di capacità di Azure NetApp Files, l'app per la logica crea, modifica o elimina automaticamente una regola di avviso metrica basata sulla capacità con il nome `ANF_Pool_poolname` o `ANF_Volume_poolname_volname` . 

### <a name="manage-capacity"></a>Gestire la capacità

Oltre al monitoraggio e all'avviso, è necessario incorporare anche una procedura di gestione della capacità dell'applicazione per gestire Azure NetApp Files (aumento) del consumo di capacità. Quando un Azure NetApp Files volume o un pool di capacità si riempie, è possibile ottenere capacità aggiuntiva in tempo reale senza [interruzioni dell'applicazione.](azure-netapp-files-resize-capacity-pools-or-volumes.md) Questa sezione descrive vari modi manuali e automatizzati per aumentare lo spazio di cui è stato effettuato il provisioning del pool di volumi e capacità in base alle esigenze.
 
#### <a name="manual"></a>Manuale 

È possibile usare il portale o l'interfaccia della riga di comando per aumentare manualmente le dimensioni del volume o del pool di capacità. 

##### <a name="portal"></a>Portale 

È possibile [modificare le dimensioni di un volume in](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-volume) base alle esigenze. L'utilizzo della capacità di un volume concorre al calcolo della capacità di cui è stato effettuato il provisioning del pool.

1. Nel pannello Gestisci account NetApp fare clic su **Volumi**.  
2. Fare clic con il pulsante destro del mouse sul nome del volume da ridimensionare oppure fare clic sull'icona alla fine della riga del volume per `…` visualizzare il menu di scelta rapida. 
3. Usare le opzioni del menu di scelta rapida per ridimensionare o eliminare il volume.   

   ![Screenshot che mostra le opzioni del menu di scelta rapida per un volume.](../media/azure-netapp-files/hard-quota-volume-options.png) 

   ![Screenshot che mostra la finestra Aggiorna quota volume.](../media/azure-netapp-files/hard-quota-update-volume-quota.png) 

In alcuni casi, il pool di capacità di hosting non ha capacità sufficiente per ridimensionare i volumi. Tuttavia, è possibile [modificare le dimensioni del pool di capacità](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-the-capacity-pool) in incrementi o decrementi di 1 TiB. Le dimensioni del pool di capacità non possono essere inferiori a 4 TiB. *Il ridimensionamento del pool di capacità modifica la capacità di Azure NetApp Files acquistata.*

1. Nel pannello Gestisci account NetApp fare clic sul pool di capacità che si intende ridimensionare.
2. Fare clic con il pulsante destro del mouse sul nome del pool di capacità oppure fare clic sull'icona alla fine della riga del pool di capacità `…` per visualizzare il menu di scelta rapida.
3. Usare le opzioni del menu di scelta rapida per ridimensionare o eliminare il pool di capacità.    

   ![Screenshot che mostra le opzioni del menu di scelta rapida per un pool di capacità.](../media/azure-netapp-files/hard-quota-pool-options.png) 

   ![Screenshot che mostra la finestra Ridimensiona pool.](../media/azure-netapp-files/hard-quota-update-resize-pool.png) 


##### <a name="cli-or-powershell"></a>Interfaccia della riga di comando o PowerShell

È possibile usare gli strumenti [dell Azure NetApp Files dell'interfaccia](azure-netapp-files-sdk-cli.md#cli-tools)della riga di comando di Azure, inclusi l'interfaccia della riga di comando di Azure Azure PowerShell, per modificare manualmente le dimensioni del volume o del pool di capacità.  I due comandi seguenti possono essere usati per gestire le risorse Azure NetApp Files volume e pool:  

* [`az netappfiles pool`](/cli/azure/netappfiles/pool)
* [`az netappfiles volume`](/cli/azure/netappfiles/volume)

Per gestire le Azure NetApp Files con l'interfaccia della riga di comando di Azure, è possibile aprire il portale di Azure e selezionare il collegamento Azure **Cloud Shell** nella parte superiore della barra dei menu: 

[![Screenshot che mostra come accedere Cloud Shell collegamento. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png#lightbox)

Questa azione aprirà il Azure Cloud Shell:

[![Screenshot che mostra Cloud Shell finestra. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png#lightbox)

Gli esempi seguenti usano i comandi per [visualizzare](/cli/azure/netappfiles/volume#az_netappfiles_volume_show) e [aggiornare](/cli/azure/netappfiles/volume#az_netappfiles_volume_update) le dimensioni di un volume:
 
[![Screenshot che mostra l'uso di PowerShell per visualizzare le dimensioni del volume. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png#lightbox)

[![Screenshot che mostra l'uso di PowerShell per aggiornare le dimensioni del volume. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png#lightbox)

Gli esempi seguenti usano i comandi per [visualizzare](/cli/azure/netappfiles/pool#az_netappfiles_pool_show) e [aggiornare le](/cli/azure/netappfiles/pool#az_netappfiles_pool_update) dimensioni di un pool di capacità:

[![Screenshot che mostra l'uso di PowerShell per visualizzare le dimensioni del pool di capacità. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png#lightbox) 

[![Screenshot che mostra l'uso di PowerShell per aggiornare le dimensioni del pool di capacità. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png#lightbox)

#### <a name="automated"></a>Automatizzata  

È possibile compilare un processo automatizzato per gestire il comportamento modificato.

##### <a name="rest-api"></a>API REST   

L'API REST per il servizio Azure NetApp Files definisce le operazioni HTTP sulle risorse, ad esempio l'account di NetApp, il pool di capacità, i volumi e gli snapshot. La specifica dell'API REST per Azure NetApp Files viene pubblicata [tramite la Azure NetApp Files Resource Manager GitHub .](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager) È possibile trovare [codice di esempio da usare con le API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable/2020-06-01/examples) in GitHub.

Vedere [Sviluppare per la Azure NetApp Files con l'API REST.](azure-netapp-files-develop-with-rest-api.md) 

##### <a name="rest-api-using-powershell"></a>API REST con PowerShell  

L'API REST per il servizio Azure NetApp Files definisce le operazioni HTTP sulle risorse, ad esempio l'account di NetApp, il pool di capacità, i volumi e gli snapshot. La [specifica dell'API REST per Azure NetApp Files](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager) viene pubblicata tramite GitHub.

Vedere [Sviluppare per Azure NetApp Files con l'API REST tramite PowerShell.](develop-rest-api-powershell.md)

##### <a name="capacity-management-using-anfcapacitymanager"></a>Gestione della capacità con ANFCapacityManager

ANFCapacityManager è un'app per la logica di Azure che gestisce le regole di avviso basate sulla capacità. Aumenta automaticamente le dimensioni dei volumi per impedire Azure NetApp Files di spazio insufficiente. Oltre a inviare avvisi, può consentire l'aumento automatico delle dimensioni del volume e del pool di capacità per evitare che i volumi Azure NetApp Files esersino a causa dello spazio insufficiente: 

* Facoltativamente, quando un volume Azure NetApp Files raggiunge la soglia specificata per percentuale utilizzata, la quota del volume (dimensioni) verrà aumentata della percentuale specificata tra 10 e 100.  
* Se l'aumento delle dimensioni del volume supera la capacità del pool di capacità contenitore, verranno aumentate anche le dimensioni del pool di capacità per contenere le nuove dimensioni del volume.

È possibile configurare l'impostazione di gestione della capacità chiave seguente:  

* **Aumento percentuale aumento automatico:** percentuale delle dimensioni del volume esistenti per aumentare automaticamente un volume se raggiunge la **soglia % completa specificata.** Il valore 0 (zero) disabilita la funzionalità AutoGrow. È consigliabile un valore compreso tra 10 e 100.

    ![Screenshot che mostra la finestra Imposta percentuale crescita automatica volume.](../media/azure-netapp-files/hard-quota-volume-anfcapacitymanager-auto-grow-percent.png) 

## <a name="faq"></a>Domande frequenti 

Questa sezione risponde ad alcune domande sulla modifica della quota rigida del volume. 

### <a name="does-snapshot-space-count-towards-the-usable-or-provisioned-capacity-of-a-volume"></a>Lo spazio dello snapshot viene conteggiato per la capacità utilizzabile o di cui è stato effettuato il provisioning di un volume?

Sì, la capacità dello snapshot utilizzata viene conteggiata per lo spazio di cui è stato effettuato il provisioning nel volume. Nel caso in cui il volume sia pieno, prendere in considerazione due opzioni di correzione:

* Ridimensionare il volume come descritto in questo articolo.
* Rimuovere gli snapshot meno recenti per liberare spazio nel volume di hosting.

### <a name="does-this-change-mean-the-volume-auto-grow-behavior-will-disappear-from-azure-netapp-files"></a>Questa modifica significa che il comportamento di crescita automatica del volume scompare dal Azure NetApp Files?

Un errore comune è che i volumi Azure NetApp Files *aumentano* automaticamente al momento del riempimento. È stato effettuato il thin provisioning dei volumi con dimensioni di 100 TiB, indipendentemente dalla quota effettiva del set, mentre il *pool* di capacità sottostante aumenta automaticamente con incrementi di 1 TiB. Questa modifica indirizza le dimensioni del *volume* (visibili e utilizzabili) alla quota impostata e i *pool* di capacità non aumentano più automaticamente. Questa modifica comporta la creazione di report accurati e accurati sul lato client. Evita il consumo di capacità "runaway".

### <a name="does-this-change-have-any-effect-on-volumes-replicated-with-cross-region-replication-preview"></a>Questa modifica ha effetto sui volumi replicati con la replica tra aree (anteprima)? 

La quota del volume rigido non viene applicata ai volumi di destinazione della replica.

### <a name="does-this-change-have-any-effect-on-metrics-currently-available-in-azure-monitor"></a>Questa modifica influisce sulle metriche attualmente disponibili in Monitoraggio di Azure?

Le metriche del portale Monitoraggio di Azure statistiche rifletteranno in modo accurato il nuovo modello di allocazione e utilizzo.

### <a name="does-this-change-have-any-effect-on-the-resource-limits-for-azure-netapp-files"></a>Questa modifica ha effetto sui limiti delle risorse per Azure NetApp Files?

Non sono presenti modifiche ai limiti delle risorse per Azure NetApp Files oltre le modifiche della quota descritte in questo articolo.

### <a name="is-there-an-example-anfcapacitymanager-workflow"></a>Esiste un flusso di lavoro ANFCapacityManager di esempio?  

Sì. Vedere la pagina [gitHub di esempio volume AutoGrow Workflow](https://github.com/ANFTechTeam/ANFCapacityManager/blob/master/ResizeWorkflow.md).

### <a name="is-anfcapacitymanager-microsoft-supported"></a>ANFCapacityManager è supportato da Microsoft?  

[L'app per la logica ANFCapacityManager viene fornita così come è e non è supportata da NetApp o Microsoft.](https://github.com/ANFTechTeam/ANFCapacityManager#disclaimer) Si consiglia di modificare in base all'ambiente o ai requisiti specifici. È consigliabile testare la funzionalità prima di distribuirla in qualsiasi ambiente business critical o di produzione.

### <a name="how-can-i-report-a-bug-or-submit-a-feature-request-for-anfcapacitymanger"></a>Come è possibile segnalare un bug o inviare una richiesta di funzionalità per ANFCapacityManger?
È possibile inviare bug e richieste di funzionalità facendo **clic su Nuovo problema** nella pagina [ANFCapacityManager di GitHub.](https://github.com/ANFTechTeam/ANFCapacityManager/issues)

## <a name="next-steps"></a>Passaggi successivi
* [Ridimensionare un pool di capacità o un volume](azure-netapp-files-resize-capacity-pools-or-volumes.md) 
* [Metriche per Azure NetApp Files](azure-netapp-files-metrics.md)
