---
title: Macchine virtuali Vantaggio Azure Hybrid e Linux
description: Informazioni su come Vantaggio Azure Hybrid possibile risparmiare sui costi delle macchine virtuali Linux in esecuzione in Azure.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: 1bc108f76ac35b13474de18d473f5728dbad9d23
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560017"
---
# <a name="how-azure-hybrid-benefit-applies-for-linux-virtual-machines"></a>Come si applica Vantaggio Azure Hybrid per le macchine virtuali Linux

Vantaggio Azure Hybrid è un vantaggio della licenza che consente di ridurre in modo significativo i costi di esecuzione delle macchine virtuali Red Hat Enterprise Linux (RHEL) e SUSE Linux Enterprise Server (SLES) nel cloud. Con questo vantaggio, paghi solo per i costi di infrastruttura della VM, perché la tua sottoscrizione RHEL o SLES copre la tariffa software. Il vantaggio è applicabile a tutte le immagini RHEL e SLES Marketplace con pagamento in base al consumo (PAYG).

Vantaggio Azure Hybrid per le macchine virtuali Linux è ora disponibile pubblicamente.

## <a name="benefit-description"></a>Descrizione del vantaggio

Tramite Vantaggio Azure Hybrid, è possibile eseguire la migrazione dei server RHEL e SLES locali in Azure convertendo le macchine virtuali RHEL e SLES PAYG esistenti in Azure per la fatturazione BYOS (Bring your own Subscription). In genere, le macchine virtuali distribuite da immagini PAYG in Azure addebiteranno sia la tariffa dell'infrastruttura che la tariffa software. Con Vantaggio Azure Hybrid, le macchine virtuali PAYG possono essere convertite in un modello di fatturazione BYOS senza una ridistribuzione, in modo da evitare eventuali rischi di inattività.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Visualizzazione dei costi Vantaggio Azure Hybrid sulle VM Linux.":::

Dopo aver abilitato il vantaggio in una macchina virtuale RHEL o SLES, non verrà più addebitata la tariffa software aggiuntiva in genere in una macchina virtuale PAYG. Al contrario, la macchina virtuale inizierà a accumulare un addebito per BYOS, che include solo la tariffa hardware di calcolo e nessuna tariffa software.

È anche possibile scegliere di convertire in un modello di fatturazione PAYG una macchina virtuale in cui è stato abilitato il vantaggio.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Ambito dell'idoneità Vantaggio Azure Hybrid per le macchine virtuali Linux

Vantaggio Azure Hybrid è disponibile per tutte le immagini RHEL e SLES PAYG di Azure Marketplace. Il vantaggio non è ancora disponibile per immagini RHEL o SLES BYOS o immagini personalizzate di Azure Marketplace.

Le istanze riservate, le istanze host dedicate di Azure e i vantaggi ibridi SQL non sono idonee per Vantaggio Azure Hybrid se si usa già il vantaggio con le VM Linux.

## <a name="get-started"></a>Introduzione

### <a name="red-hat-customers"></a>Clienti Red Hat

Vantaggio Azure Hybrid per RHEL è disponibile per i clienti di Red Hat che soddisfano entrambi questi criteri:

- Avere sottoscrizioni RHEL attive o inutilizzate idonee per l'uso in Azure
- Sono state abilitate una o più sottoscrizioni per l'uso in Azure con il programma [Red Hat cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)

Per iniziare a usare il vantaggio per Red Hat:

1. Abilitare una o più sottoscrizioni RHEL idonee per l'uso in Azure usando l' [interfaccia del cliente Red Hat cloud Access](https://access.redhat.com/management/cloud).

   Le sottoscrizioni di Azure fornite durante il processo di abilitazione di Red Hat cloud Access saranno quindi autorizzate a usare la funzionalità Vantaggio Azure Hybrid.
1. Applicare Vantaggio Azure Hybrid alle macchine virtuali RHEL PAYG esistenti e a tutte le nuove macchine virtuali RHEL distribuite dalle immagini PAYG di Azure Marketplace.
1. Seguire i [passaggi successivi](https://access.redhat.com/articles/5419341) consigliati per la configurazione delle origini aggiornamenti per le macchine virtuali RHEL e per le linee guida sulla conformità delle sottoscrizioni RHEL.


### <a name="suse-customers"></a>Clienti SUSE

Per iniziare a usare il vantaggio per SUSE:

1. Eseguire la registrazione con il programma SUSE public cloud.
1. Applicare il vantaggio alle macchine virtuali esistenti tramite l'interfaccia della riga di comando di Azure.
1. Registrare le VM che ricevono il vantaggio con una fonte separata di aggiornamenti.


## <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Abilitare e disabilitare il vantaggio nell'interfaccia della riga di comando di Azure

È possibile usare il `az vm update` comando per aggiornare le macchine virtuali esistenti. Per le VM RHEL, eseguire il comando con un `--license-type` parametro di `RHEL_BYOS` . Per le macchine virtuali SLES, eseguire il comando con un `--license-type` parametro di `SLES_BYOS` .

### <a name="cli-example-to-enable-the-benefit"></a>Esempio di interfaccia della riga di comando per abilitare il vantaggio
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>Esempio di interfaccia della riga di comando per disabilitare il vantaggio
Per disabilitare il vantaggio, utilizzare il `--license-type` valore seguente `None` :

```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>Esempio di interfaccia della riga di comando per abilitare il vantaggio su un numero elevato di macchine virtuali
Per abilitare il vantaggio in un numero elevato di macchine virtuali, è possibile usare il `--ids` parametro nell'interfaccia della riga di comando di Azure:

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file that contains a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

Gli esempi seguenti illustrano due metodi per ottenere un elenco di ID di risorsa: uno a livello di gruppo di risorse e uno a livello di sottoscrizione.

```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="apply-the-azure-hybrid-benefit-at-vm-create-time"></a>Applicare la Vantaggio Azure Hybrid al momento della creazione della macchina virtuale
Oltre ad applicare il Vantaggio Azure Hybrid alle macchine virtuali con pagamento in base al consumo esistenti, è possibile richiamarlo al momento della creazione della macchina virtuale. I vantaggi di questa operazione sono triplici:
- È possibile eseguire il provisioning di macchine virtuali PAYG e BYOS usando la stessa immagine e lo stesso processo.
- Consente le modifiche future della modalità di gestione delle licenze, qualcosa non disponibile con un'immagine solo BYOS o se si porta una macchina virtuale personalizzata.
- Per impostazione predefinita, la macchina virtuale verrà connessa a Red Hat Update Infrastructure (RHUI) per assicurarsi che rimanga aggiornata e protetta. È possibile modificare il meccanismo aggiornato dopo la distribuzione in qualsiasi momento.

## <a name="check-the-azure-hybrid-benefit-status-of-a-vm"></a>Verificare lo stato Vantaggio Azure Hybrid di una macchina virtuale
È possibile visualizzare lo stato Vantaggio Azure Hybrid di una macchina virtuale usando l'interfaccia della riga di comando di Azure o il servizio metadati dell'istanza di Azure.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

`az vm get-instance-view`A questo scopo, è possibile usare il comando. Cercare un `licenseType` campo nella risposta. Se il `licenseType` campo esiste e il valore è `RHEL_BYOS` o `SLES_BYOS` , il vantaggio è abilitato per la macchina virtuale.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Servizio metadati dell'istanza di Azure

Dall'interno della macchina virtuale, è possibile eseguire una query sui metadati attestati nel servizio metadati dell'istanza di Azure per determinare il valore della macchina virtuale `licenseType` . `licenseType`Il valore o indicherà `RHEL_BYOS` `SLES_BYOS` che il vantaggio è abilitato per la macchina virtuale. [Altre informazioni sui metadati attestati](./instance-metadata-service.md#attested-data).

## <a name="compliance"></a>Conformità

### <a name="red-hat"></a>Red Hat

I clienti che usano Vantaggio Azure Hybrid per RHEL accettano le [condizioni legali](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) standard e l' [informativa sulla privacy](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) associate alle offerte RHEL di Azure Marketplace.

I clienti che usano Vantaggio Azure Hybrid per RHEL hanno tre opzioni per la fornitura di patch e aggiornamenti software a tali macchine virtuali:

- [Red Hat Update Infrastructure](../workloads/redhat/redhat-rhui.md) (opzione predefinita)
- Server Red Hat satellite
- Gestione delle sottoscrizioni di Red Hat

I clienti che scelgono l'opzione RHUI possono continuare a usare RHUI come origine di aggiornamento principale per le macchine virtuali RHEL Vantaggio Azure Hybrid senza aggiungere sottoscrizioni RHEL a tali macchine virtuali. I clienti che scelgono l'opzione RHUI sono responsabili per garantire la conformità della sottoscrizione RHEL.

I clienti che scelgono Red Hat satellite server o Red Hat Subscription Manager devono rimuovere la configurazione di RHUI e quindi alleghino una sottoscrizione RHEL abilitata per l'accesso al cloud alle macchine virtuali RHEL Vantaggio Azure Hybrid.  

Per altre informazioni sulla conformità delle sottoscrizioni di Red Hat, sugli aggiornamenti software e sulle origini per Vantaggio Azure Hybrid macchine virtuali RHEL, vedere l' [articolo Red Hat sull'uso di sottoscrizioni RHEL con vantaggio Azure Hybrid](https://access.redhat.com/articles/5419341).

### <a name="suse"></a>SUSE

Per usare Vantaggio Azure Hybrid per le macchine virtuali SLES, è necessario prima registrarsi con il [programma SUSE Public Cloud](https://www.suse.com/media/guide/suse_public_cloud_service_provider_program_overview.pdf). Dopo aver acquistato le sottoscrizioni SUSE, è necessario registrare le VM che usano tali sottoscrizioni per la propria origine degli aggiornamenti. Utilizzare SUSE Customer Center, Subscription Management Tool Server o SUSE Manager per questa registrazione.

## <a name="frequently-asked-questions"></a>Domande frequenti
*D: è possibile usare un tipo di licenza `RHEL_BYOS` con un'immagine SLES o viceversa?*

R: No, non è possibile. Se si tenta di immettere un tipo di licenza che corrisponde erroneamente alla distribuzione in esecuzione nella macchina virtuale, non aggiornerà i metadati di fatturazione. Tuttavia, se si immette accidentalmente il tipo di licenza errato, l'aggiornamento della macchina virtuale al tipo di licenza corretto consentirà comunque il vantaggio.

*D: sono stato registrato con Red Hat cloud Access ma non è ancora possibile abilitare il vantaggio nelle macchine virtuali RHEL. Cosa dovrei fare?*

R: la propagazione della registrazione della sottoscrizione di Red Hat cloud Access da Red Hat ad Azure potrebbe richiedere del tempo. Se l'errore viene ancora visualizzato dopo un giorno lavorativo, contattare il supporto tecnico Microsoft.

*D: è stata distribuita una macchina virtuale usando un'immagine di RHEL BYOS "Golden Image". È possibile convertire la fatturazione su queste immagini da BYOS a PAYG?*

R: No, non è possibile. Vantaggio Azure Hybrid supporta la conversione solo su immagini con pagamento in base al consumo.

*D: è stata caricata un'immagine RHEL personalizzata dall'ambiente locale (tramite Azure Migrate, Azure Site Recovery o altro) in Azure. È possibile convertire la fatturazione su queste immagini da BYOS a PAYG?*

R: No, non è possibile. La funzionalità Vantaggio Azure Hybrid è attualmente disponibile solo per le immagini RHEL e SLES in Azure Marketplace. 

*D: è stata caricata un'immagine RHEL personalizzata dall'ambiente locale (tramite Azure Migrate, Azure Site Recovery o altro) in Azure. È necessario eseguire qualsiasi operazione per trarre vantaggio da Vantaggio Azure Hybrid?*

R: No. Le immagini RHEL caricate sono già considerate BYOS e vengono addebitati solo i costi di infrastruttura di Azure. L'utente è responsabile dei costi di sottoscrizione RHEL, esattamente come per gli ambienti locali. 

*D: è possibile usare Vantaggio Azure Hybrid nelle VM distribuite da immagini RHEL e SLES SAP di Azure Marketplace?*

R: sì. È possibile usare il tipo di licenza di `RHEL_BYOS` per le VM RHEL e `SLES_BYOS` per le conversioni delle macchine virtuali distribuite dalle immagini RHEL e SLES SAP di Azure Marketplace.

*D: è possibile usare Vantaggio Azure Hybrid nei set di scalabilità di macchine virtuali per RHEL e SLES?*

R: No, non è possibile. I set di scalabilità di macchine virtuali non sono attualmente inclusi nell'ambito di Vantaggio Azure Hybrid per RHEL e SLES.

*D: è possibile usare Vantaggio Azure Hybrid in istanze riservate per RHEL e SLES?*

R: No, non è possibile. Le istanze riservate non sono attualmente nell'ambito di Vantaggio Azure Hybrid per RHEL e SLES.

*D: è possibile usare Vantaggio Azure Hybrid in una macchina virtuale distribuita per SQL Server su immagini RHEL?*

R: No, non è possibile. Non esiste alcun piano per supportarli.
 

## <a name="common-problems"></a>Problemi comuni
Questa sezione elenca i problemi comuni che potrebbero verificarsi e i passaggi per la mitigazione.

| Errore | Strategia di riduzione del rischio |
| ----- | ---------- |
| "Non è stato possibile completare l'azione perché i nostri record indicano che non è stato abilitato correttamente Red Hat cloud Access nella sottoscrizione di Azure..." | Per usare il vantaggio con le VM RHEL, è necessario prima [registrare le sottoscrizioni di Azure con Red Hat cloud Access](https://access.redhat.com/management/cloud).

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni su come creare e aggiornare le macchine virtuali e aggiungere i tipi di licenza (RHEL_BYOS, SLES_BYOS) per Vantaggio Azure Hybrid usando l'interfaccia della riga di comando di Azure](/cli/azure/vm?preserve-view=true&view=azure-cli-latest)
