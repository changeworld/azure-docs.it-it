---
title: Stati e fatturazione di macchine virtuali di Azure
description: Panoramica dei vari Stati che possono essere immessi da una macchina virtuale e quando viene addebitato un utente.
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: c206cba3b23a0bf41ce32481980aa466e869bcde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104596298"
---
# <a name="states-and-billing-of-azure-virtual-machines"></a>Stati e fatturazione di macchine virtuali di Azure

Per Macchine virtuali di Azure (VM) sono previsti vari stati che possono essere categorizzati come stati di *provisioning* e di *alimentazione*. Lo scopo di questo articolo è descrivere questi stati e sottolineare in particolare quando viene addebitato l'utilizzo delle istanze ai clienti. 

## <a name="get-states-using-instance-view"></a>Ottenere gli Stati tramite la visualizzazione dell'istanza

L'API per la visualizzazione dell'istanza fornisce informazioni sullo stato di esecuzione della macchina virtuale. Per altre informazioni, vedere la documentazione dell'API [Virtual Machines - Instance View](/rest/api/compute/virtualmachines/instanceview) (Macchine virtuali - Visualizzazione istanza).

Azure Resource Explorer offre una semplice interfaccia utente per visualizzare lo stato di esecuzione della macchina virtuale: [Resource Explorer](https://resources.azure.com/).

Gli stati di provisioning sono visibili nelle proprietà della macchina virtuale e nella visualizzazione dell'istanza. Gli stati di alimentazione sono disponibili nella visualizzazione dell'istanza di macchina virtuale.

Per recuperare lo stato di alimentazione di tutte le macchine virtuali nella sottoscrizione, usare l'[API Macchine virtuali - Elenca tutte](/rest/api/compute/virtualmachines/listall) con il parametro **statusOnly** impostato su *true*.

> [!NOTE]
> [Macchine virtuali: elencare tutte le API](/rest/api/compute/virtualmachines/listall) con il parametro **statusOnly** impostato su true consente di recuperare gli Stati di alimentazione di tutte le macchine virtuali in una sottoscrizione. Tuttavia, in alcune situazioni rare, lo stato di alimentazione potrebbe non essere disponibile a causa di problemi intermittenti durante il processo di recupero. In tali situazioni, è consigliabile riprovare a usare la stessa API o usare [integrità risorse di Azure](../service-health/resource-health-overview.md) o un [grafo di risorse di Azure](..//governance/resource-graph/overview.md) per controllare lo stato di alimentazione delle macchine virtuali.
 
## <a name="power-states-and-billing"></a>Stati di alimentazione e fatturazione

Lo stato di alimentazione rappresenta l'ultimo stato noto della macchina virtuale.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-power-states.png" alt-text="Image Mostra il diagramma degli Stati di alimentazione che una macchina virtuale può passare. ":::

La tabella seguente include una descrizione di ogni stato dell'istanza e indica se tale stato genera o meno un addebito per l'utilizzo dell'istanza.

| Stato di alimentazione | Descrizione | Fatturazione |  
|---|---|---|
| Avvio in corso| La macchina virtuale sta per accendersi. |Non fatturato * | 
| In esecuzione | La macchina virtuale è completamente attiva. Si tratta dello stato di lavoro standard. | Fatturato | 
| Stopping | Si tratta di uno stato di transizione tra l'esecuzione e l'arresto. | Fatturato| 
|Arrestato | La macchina virtuale è stata arrestata dall'interno del sistema operativo guest o usando le API spento. In questo stato, la macchina virtuale sta ancora rilasciando l'hardware sottostante. Questo stato viene anche definito *arrestato (allocato)*. | Fatturato | 
| Deallocazione | Si tratta dello stato di transizione tra l'esecuzione e la deallocazione. | Non fatturato * | 
| Deallocato | La macchina virtuale ha rilasciato il lease nell'hardware sottostante ed è completamente spento. Questo stato viene anche definito *arrestato (deallocato)*. | Non fatturato * | 

&#42; alcune risorse di Azure, ad esempio i [dischi](https://azure.microsoft.com/pricing/details/managed-disks) e la [rete](https://azure.microsoft.com/pricing/details/bandwidth/) continueranno a comportare addebiti.


## <a name="provisioning-states"></a>Stati di provisioning

Uno stato di provisioning è lo stato di un'operazione del piano di controllo avviata dall'utente sulla macchina virtuale. Questi stati sono separati dallo stato di alimentazione di una macchina virtuale.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-provisioning-states.png" alt-text="Image Mostra gli Stati di provisioning che una macchina virtuale può attraversare.":::

| Stato provisioning | Descrizione | Stato di alimentazione | Fatturazione | 
|---|---|---|---|
| Crea | Creazione della macchina virtuale. | Avvio in corso | Non fatturato * | 
| Aggiornamento | Aggiorna il modello per una macchina virtuale esistente. Alcune modifiche non del modello a una macchina virtuale, ad esempio avvio e riavvio, rientrano nello stato di aggiornamento. | In esecuzione | Fatturato | 
| Delete | Eliminazione della macchina virtuale. | Deallocazione | Non fatturato * |
| Deallocare | La macchina virtuale viene arrestata e rimossa completamente dall'host sottostante. La deallocazione di una macchina virtuale è considerata un aggiornamento e visualizza stati di provisioning simili all'aggiornamento. | Deallocazione | Non fatturato * | 

&#42; alcune risorse di Azure, ad esempio i [dischi](https://azure.microsoft.com/pricing/details/managed-disks) e la [rete](https://azure.microsoft.com/pricing/details/bandwidth/) continueranno a comportare addebiti.

## <a name="os-provisioning-states"></a>Stati del provisioning del sistema operativo
Gli Stati del provisioning del sistema operativo si applicano solo alle macchine virtuali create con un'immagine del sistema operativo. Per le immagini specializzate non vengono visualizzati questi Stati. 

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/os-provisioning-states.png" alt-text="Image Mostra gli Stati del provisioning del sistema operativo che una macchina virtuale può passare.":::

| Stato di provisioning del sistema operativo | Descrizione | Stato di alimentazione | Fatturazione | 
|---|---|---|---|
| OSProvisioningInProgress | La macchina virtuale è in esecuzione ed è in corso l'installazione del sistema operativo guest. | In esecuzione | Fatturato | 
| OSProvisioningComplete | Si tratta di uno stato di breve durata. La macchina virtuale esegue rapidamente la transizione da questo stato a **riuscito**. Se è ancora in corso l'installazione delle estensioni, lo stato continuerà a essere visualizzato fino al completamento. | In esecuzione | Fatturato | 
| Completato | Le azioni avviate dall'utente sono state completate. | In esecuzione | Fatturato | 
| Non riuscito | Rappresenta un'operazione non riuscita. Per ulteriori informazioni e per le possibili soluzioni, vedere il codice di errore. | In esecuzione  | Fatturato | 


## <a name="next-steps"></a>Passaggi successivi
- Esaminare la [documentazione di gestione costi e fatturazione di Azure](../cost-management-billing/index.yml)
- Usare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/) per pianificare le distribuzioni.
- Per altre informazioni sul monitoraggio della VM, vedere [monitorare le macchine virtuali in Azure](../azure-monitor/vm/monitor-vm-azure.md).