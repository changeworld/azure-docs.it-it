---
title: Gestione automatica di Azure domande frequenti su Gestione automatica di Azure per le macchine virtuali
description: Risposte alle domande frequenti sulle Gestione automatica di Azure per le macchine virtuali.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: f5a9ff7661fda372631d1bb912b1c137b37c7e07
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363360"
---
# <a name="frequently-asked-questions-for-azure-automanage-for-vms"></a>Domande frequenti sull'Gestione automatica di Azure per le macchine virtuali

Questo articolo fornisce le risposte ad alcune delle domande più comuni [sull'Gestione automatica di Azure per le macchine virtuali.](automanage-virtual-machines.md)

Se il problema riguardante Azure non viene risolto in questo articolo, visitare i forum di Azure su [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). È possibile pubblicare il problema in questi forum o in [@AzureSupport su Twitter](https://twitter.com/AzureSupport). È anche possibile inviare una richiesta di supporto tecnico di Azure. Per inviare una richiesta di supporto, [nella supporto tecnico di Azure selezionare](https://azure.microsoft.com/support/options/)Ottieni **supporto.**


## <a name="azure-automanage-for-virtual-machines"></a>Gestione automatica di Azure per le macchine virtuali

**Quali sono tutti i prerequisiti necessari per abilitare Gestione automatica di Azure?**

Di seguito sono riportati i prerequisiti per l'abilitazione Gestione automatica di Azure:
- Versioni [di Windows Server e](automanage-windows-server.md#supported-windows-server-versions) [distribuzioni Linux supportate](automanage-linux.md#supported-linux-distributions-and-versions)
- Le macchine virtuali devono essere in un'area supportata
- L'utente deve disporre delle autorizzazioni corrette
- Solo macchine virtuali non di set di scalabilità
- La gestione automatica non supporta le sottoscrizioni sandbox in questo momento

**Quale autorizzazione del controllo degli accessi in base al ruolo di Azure è necessaria per abilitare la gestione automatica?**

Se si abilita La gestione automatica in una macchina virtuale con un account di gestione automatica esistente, è necessario il ruolo Collaboratore per il gruppo di risorse in cui si trova la macchina virtuale.

Se si usa un nuovo account di gestione automatica durante l'abilitazione, è necessario avere il ruolo Proprietario o il ruolo Collaboratore e Amministratore accesso utenti per la sottoscrizione.


**Quali aree sono supportate?**

L'elenco completo delle aree supportate è [disponibile qui.](./automanage-virtual-machines.md#supported-regions)


**Quali funzionalità vengono Gestione automatica di Azure automatizzate?**

La gestione automatica registra, configura e monitora per tutto il ciclo di vita della macchina virtuale i servizi elencati [qui.](automanage-virtual-machines.md)

**Le Gestione automatica di Azure funzionano con Azure Arc abilitate per le macchine virtuali?**

La gestione automatica attualmente non supporta le macchine virtuali abilitate per Arc.

**È possibile personalizzare le configurazioni in Gestione automatica di Azure?**

I clienti possono personalizzare le impostazioni per servizi specifici, ad esempio Backup di Azure conservazione, tramite le preferenze di configurazione. Per l'elenco completo delle impostazioni che è possibile modificare, vedere la documentazione [qui.](automanage-virtual-machines.md#customizing-an-environment-using-preferences)


**Le Gestione automatica di Azure funzionano sia con le macchine virtuali Linux che con le macchine virtuali Windows?**

Sì, vedere le versioni [di Windows Server e](automanage-windows-server.md#supported-windows-server-versions) le [distribuzioni Linux supportate.](automanage-linux.md#supported-linux-distributions-and-versions)


**È possibile applicare la gestione automatica in modo selettivo solo a un set di macchine virtuali?**

La gestione automatica può essere abilitata con semplicità con clic e punti nelle macchine virtuali nuove ed esistenti selezionate. La funzionalità Dimanage automatico può anche essere disabilitata in qualsiasi momento.


**Le Gestione automatica di Azure supportano le macchine virtuali in un set di scalabilità di macchine virtuali?**

No, Gestione automatica di Azure attualmente non supporta le macchine virtuali in un set di scalabilità di macchine virtuali.


**Quanto costa Gestione automatica di Azure?**

Gestione automatica di Azure è disponibile senza costi aggiuntivi nell'anteprima pubblica. Le risorse di Azure collegate, ad esempio Backup di Azure, comportano costi.


**È possibile applicare La gestione automatica tramite Criteri di Azure?**

Sì, è presente un criterio predefinito che applica automaticamente La gestione automatica a tutte le macchine virtuali all'interno dell'ambito definito. Si specifica anche la configurazione dell'ambiente (DevTest o Production) insieme all'account Automanage. Per altre informazioni sull'abilitazione di Gestione automatica tramite Criteri di [Azure, vedere](virtual-machines-policy-enable.md).


**Che cos'è un account di Gestione automatica?**

L'account di gestione automatica è un'identità del servizio gestito che fornisce il contesto di sicurezza o l'identità con cui si verificano le operazioni automatiche.


**Quando si abilita La gestione automatica, influisce su altre macchine virtuali oltre alle VM selezionate?**

Se la macchina virtuale è collegata a un'area di lavoro Log Analytics esistente, l'area di lavoro verrà riutilizzata per applicare queste soluzioni: Rilevamento modifiche, Inventario e Gestione aggiornamenti. Tutte le macchine virtuali connesse a tale area di lavoro avranno queste soluzioni abilitate.


**È possibile modificare l'ambiente della macchina virtuale?**

A questo punto, è necessario disabilitare la gestione automatica per la macchina virtuale e quindi riattivarla con l'ambiente e le preferenze desiderate.


**Se la macchina virtuale è già configurata per un servizio, ad esempio Gestione aggiornamenti, verrà riconfigurata automaticamente?**
No, la funzionalità Disamanazione automatica non la riconfigurerà. Si inizierà a monitorare le risorse associate a tale servizio per la deriva.


**Perché la macchina virtuale ha uno stato Non riuscito nel portale di gestione automatica?**

Se lo stato è Non *riuscito,* è possibile risolvere i problemi della distribuzione in diversi modi:
* Passare a **Gruppi di risorse,** selezionare il gruppo di risorse, fare clic su **Distribuzioni** e visualizzare lo *stato* Non riuscito insieme ai dettagli dell'errore.
* Passare a **Sottoscrizioni,** selezionare il gruppo di risorse, fare clic su **Distribuzioni** e visualizzare lo *stato* Non riuscito insieme ai dettagli dell'errore.
* È anche possibile visitare il log attività di una macchina virtuale, che conterrà una voce per "Creare o aggiornare le assegnazioni del profilo di configurazione". Può anche contenere altri dettagli sulla distribuzione.

**Come è possibile ottenere supporto per la risoluzione dei problemi per La gestione automatica?**

È possibile stendare [un ticket del caso di supporto tecnico.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Per **l'opzione** Servizio cercare e selezionare *Gestione automatica* nella sezione *Monitoraggio e* gestione.


## <a name="next-steps"></a>Passaggi successivi

Provare ad abilitare la gestione automatica per le macchine virtuali nel portale di Azure.

> [!div class="nextstepaction"]
> [Abilitare la gestione automatica per le macchine virtuali nel portale di Azure](quick-create-virtual-machines-portal.md)