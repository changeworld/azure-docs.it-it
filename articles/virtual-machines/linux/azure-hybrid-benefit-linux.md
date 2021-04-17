---
title: Vantaggio Azure Hybrid e linux
description: Informazioni su Vantaggio Azure Hybrid per risparmiare sui risparmi sulle macchine virtuali Linux in esecuzione in Azure.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machines
ms.subservice: azure-hybrid-benefit
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: 774f4be6a5aa0e0e772086c52938881c6637b261
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588191"
---
# <a name="how-azure-hybrid-benefit-applies-for-linux-virtual-machines"></a>Come Vantaggio Azure Hybrid per le macchine virtuali Linux

Vantaggio Azure Hybrid Red Hat Enterprise Linux è un vantaggio di licenza che consente di ridurre significativamente i costi di esecuzione delle macchine virtuali (RHEL) e SUSE Linux Enterprise Server (SLES) nel cloud. Con questo vantaggio, si paga solo per i costi di infrastruttura della macchina virtuale perché la sottoscrizione RHEL o SLES copre la tariffa software. Il vantaggio è disponibile per tutte le immagini RHEL e SLES Marketplace con pagamento in base al prezzo.

Vantaggio Azure Hybrid per le macchine virtuali Linux è ora disponibile pubblicamente.

## <a name="benefit-description"></a>Descrizione del vantaggio

Tramite Vantaggio Azure Hybrid è possibile eseguire la migrazione dei server RHEL e SLES locali in Azure convertendo le macchine virtuali RHEL e SLES con pagamento in base al prezzo in Azure alla fatturazione Bring Your Own Subscription (BYOS). In genere, le macchine virtuali distribuite da immagini con pagamento in base al pagamento in Azure addebiteranno sia una tariffa per l'infrastruttura che una tariffa software. Con Vantaggio Azure Hybrid, le macchine virtuali con pagamento in base al prezzo possono essere convertite in un modello di fatturazione BYOS senza ridistribuzione, in modo da evitare qualsiasi rischio di tempi di inattività.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Vantaggio Azure Hybrid visualizzazione dei costi nelle macchine virtuali Linux.":::

Dopo aver abilitato il vantaggio nella macchina virtuale RHEL o SLES, non verrà più addebitato il costo aggiuntivo del software in genere addebitato in una macchina virtuale con pagamento in base al pagamento. La macchina virtuale inizierà invece ad accumulare un addebito BYOS, che include solo la tariffa per l'hardware di calcolo e nessuna tariffa software.

È anche possibile scegliere di convertire una macchina virtuale in cui è stato abilitato il vantaggio in un modello di fatturazione con pagamento in base al prezzo.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Ambito di Vantaggio Azure Hybrid idoneità per le macchine virtuali Linux

Vantaggio Azure Hybrid è disponibile per tutte le immagini RHEL e SLES con pagamento in base al Azure Marketplace. Il vantaggio non è ancora disponibile per le immagini RHEL o SLES BYOS o per le immagini personalizzate Azure Marketplace.

host dedicato di Azure istanze e i vantaggi ibridi di SQL non sono idonei Vantaggio Azure Hybrid se si usa già il vantaggio con le macchine virtuali Linux.

## <a name="get-started"></a>Introduzione

### <a name="red-hat-customers"></a>Clienti Red Hat

Vantaggio Azure Hybrid per RHEL è disponibile per i clienti Red Hat che soddisfano entrambi questi criteri:

- Avere sottoscrizioni RHEL attive o non usate idonee per l'uso in Azure
- Sono state abilitate una o più sottoscrizioni per l'uso in Azure con il [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) programma

> [!IMPORTANT]
> Verificare che la sottoscrizione corretta sia stata abilitata nel [programma di accesso al](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) cloud.

Per iniziare a usare il vantaggio per Red Hat:

1. Abilitare una o più sottoscrizioni RHEL idonee per l'uso in Azure usando l'Red Hat Cloud Access [del cliente.](https://access.redhat.com/management/cloud)

   Le sottoscrizioni di Azure fornite durante il Red Hat Cloud Access di abilitazione saranno quindi autorizzate a usare la Vantaggio Azure Hybrid funzionalità.
1. Applicare Vantaggio Azure Hybrid a qualsiasi VM RHEL con pagamento in base al pagamento esistente e a qualsiasi nuova VM RHEL distribuita da Azure Marketplace con pagamento in base al prezzo. È possibile usare portale di Azure'interfaccia della riga di comando di Azure per abilitare il vantaggio.
1. Seguire i [passaggi successivi consigliati](https://access.redhat.com/articles/5419341) per configurare le origini di aggiornamento per le macchine virtuali RHEL e per le linee guida di conformità della sottoscrizione RHEL.


### <a name="suse-customers"></a>Clienti SUSE

Per iniziare a usare il vantaggio per SUSE:

1. Eseguire la registrazione con su SUSE Public Cloud Program.
1. Applicare il vantaggio alle macchine virtuali appena create o esistenti tramite l'interfaccia della portale di Azure o l'interfaccia della riga di comando di Azure.
1. Registrare le macchine virtuali che ricevono il vantaggio con un'origine di aggiornamenti separata.

## <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>Abilitare e disabilitare il vantaggio nel portale di Azure

È possibile abilitare il vantaggio nelle macchine virtuali esistenti visitando **l'opzione Configurazione** a sinistra e seguendo la procedura. È possibile abilitare il vantaggio nelle nuove macchine virtuali durante l'esperienza di creazione della macchina virtuale.

### <a name="azure-portal-example-to-enable-the-benefit-for-an-existing-vm"></a>portale di Azure esempio per abilitare il vantaggio per una macchina virtuale esistente:
1. Visitare [portale di Microsoft Azure](https://portal.azure.com/)
1. Passare alla pagina "Crea una macchina virtuale" nel portale.
 ![AHB durante la creazione della macchina virtuale](./media/azure-hybrid-benefit/create-vm-ahb.png)
1. Fare clic sulla casella di controllo per abilitare la conversione AHB e usare le licenze di accesso cloud.
 ![Casella di controllo AHB durante la creazione della macchina virtuale](./media/azure-hybrid-benefit/create-vm-ahb-checkbox.png)
1. Creare una macchina virtuale seguendo il set di istruzioni successivo
1. Selezionare il **pannello** Configurazione per visualizzare l'opzione abilitata. 
![Pannello di configurazione AHB dopo la creazione](./media/azure-hybrid-benefit/create-configuration-blade.png)

### <a name="azure-portal-example-to-enable-the-benefit-during-creation-of-vm"></a>portale di Azure esempio per abilitare il vantaggio durante la creazione della macchina virtuale:
1. Visitare [portale di Microsoft Azure](https://portal.azure.com/)
1. Aprire la pagina Macchina virtuale a cui si vuole applicare la conversione.
1. Passare **all'opzione** Configurazione a sinistra. Verrà visualizzata la sezione Licenze. Per abilitare la conversione AHB, selezionare il pulsante di opzione "Sì" e selezionare la casella di controllo Conferma.
![Pannello di configurazione AHB dopo la creazione](./media/azure-hybrid-benefit/create-configuration-blade.png)

>[!NOTE]
> Se è stato creato uno **snapshot** personalizzato o un'immagine condivisa **(SIG)** di un'immagine del Marketplace RHEL o SLES con pagamento in base al prezzo, è possibile usare solo l'interfaccia della riga di comando di Azure per abilitare Vantaggio Azure Hybrid. Si tratta di una limitazione nota e attualmente non esiste alcuna sequenza temporale per fornire questa funzionalità anche nel portale di Azure.

## <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Abilitare e disabilitare il vantaggio nell'interfaccia della riga di comando di Azure

È possibile usare il `az vm update` comando per aggiornare le macchine virtuali esistenti. Per le macchine virtuali RHEL, eseguire il comando con `--license-type` il parametro `RHEL_BYOS` . Per le macchine virtuali SLES, eseguire il comando con `--license-type` il parametro `SLES_BYOS` .

### <a name="cli-example-to-enable-the-benefit"></a>Esempio dell'interfaccia della riga di comando per abilitare il vantaggio
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>Esempio dell'interfaccia della riga di comando per disabilitare il vantaggio
Per disabilitare il vantaggio, usare `--license-type` il valore `None` :

```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>Esempio dell'interfaccia della riga di comando per abilitare il vantaggio in un numero elevato di macchine virtuali
Per abilitare il vantaggio in un numero elevato di macchine virtuali, è possibile usare il `--ids` parametro nell'interfaccia della riga di comando di Azure:

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file that contains a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

Gli esempi seguenti illustrano due metodi per ottenere un elenco di ID risorsa: uno a livello di gruppo di risorse e uno a livello di sottoscrizione.

```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="apply-the-azure-hybrid-benefit-at-vm-create-time"></a>Applicare il Vantaggio Azure Hybrid in fase di creazione della macchina virtuale
Oltre ad applicare il Vantaggio Azure Hybrid alle macchine virtuali con pagamento in base al go esistenti, è possibile richiamarlo al momento della creazione della macchina virtuale. I vantaggi di questa operazione sono tre:
- È possibile effettuare il provisioning di macchine virtuali CON PAGAMENTO IN BASE AL PAGAMENTO e BYOS usando la stessa immagine e la stessa procedura.
- Consente modifiche future della modalità di gestione delle licenze, qualcosa che non è disponibile con un'immagine solo BYOS o se si porta la propria macchina virtuale.
- La macchina virtuale verrà connessa a Red Hat Update Infrastructure (RHUI) per impostazione predefinita, per garantire che rimanga aggiornata e sicura. È possibile modificare il meccanismo aggiornato dopo la distribuzione in qualsiasi momento.

## <a name="check-the-azure-hybrid-benefit-status-of-a-vm"></a>Controllare lo Vantaggio Azure Hybrid stato di una macchina virtuale
È possibile visualizzare lo stato Vantaggio Azure Hybrid di una macchina virtuale usando l'interfaccia della riga di comando di Azure o il servizio metadati dell'istanza di Azure.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

A questo `az vm get-instance-view` scopo, è possibile usare il comando . Cercare un `licenseType` campo nella risposta. Se il campo esiste e il valore è o , il vantaggio è abilitato `licenseType` `RHEL_BYOS` per la macchina `SLES_BYOS` virtuale.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Servizio metadati dell'istanza di Azure

Dall'interno della macchina virtuale stessa, è possibile eseguire una query sui metadati attestati nel servizio metadati dell'istanza di Azure per determinare il valore della `licenseType` macchina virtuale. Il `licenseType` valore `RHEL_BYOS` o `SLES_BYOS` indicherà che il vantaggio è abilitato per la macchina virtuale. [Altre informazioni sui metadati attestati.](./instance-metadata-service.md#attested-data)

## <a name="compliance"></a>Conformità

### <a name="red-hat"></a>Red Hat

I clienti che usano Vantaggio Azure Hybrid per RHEL accettano le condizioni legali [standard](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) e l'informativa sulla [privacy](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) associata alle Azure Marketplace RHEL.

I clienti che usano Vantaggio Azure Hybrid per RHEL hanno tre opzioni per fornire aggiornamenti software e patch a tali macchine virtuali:

- [Red Hat Update Infrastructure](../workloads/redhat/redhat-rhui.md) (opzione predefinita)
- Red Hat Satellite Server
- Red Hat Subscription Manager

I clienti che scelgono l'opzione RHUI possono continuare a usare RHUI come origine di aggiornamento principale per le macchine virtuali RHEL Vantaggio Azure Hybrid senza collegare sottoscrizioni RHEL a tali macchine virtuali. I clienti che scelgono l'opzione RHUI sono responsabili di garantire la conformità della sottoscrizione RHEL.

I clienti che scelgono Red Hat Satellite Server o Red Hat Subscription Manager devono rimuovere la configurazione RHUI e quindi collegare una sottoscrizione RHEL abilitata per Cloud Access alle macchine virtuali RHEL Vantaggio Azure Hybrid.  

Per altre informazioni sulla conformità della sottoscrizione Red Hat, gli aggiornamenti software e le origini per le macchine virtuali RHEL di Vantaggio Azure Hybrid, vedere l'articolo di Red Hat sull'uso delle sottoscrizioni [RHEL con Vantaggio Azure Hybrid](https://access.redhat.com/articles/5419341).

### <a name="suse"></a>SUSE

Per usare Vantaggio Azure Hybrid per le macchine virtuali SLES e per informazioni sul passaggio da SLES PAYG a BYOS o sul passaggio da SLES BYOS a PAYG, vedere [SUSE Linux Enterprise e Vantaggio Azure Hybrid](https://www.suse.com/c/suse-linux-enterprise-and-azure-hybrid-benefit/). 

## <a name="azure-hybrid-benefit-on-reserved-instances-is-in-preview"></a>Vantaggio Azure Hybrid sulle istanze riservate è disponibile in anteprima

Le prenotazioni di Azure (istanze di macchina virtuale riservate di Azure) consentono di risparmiare denaro impegnando piani di uno o tre anni per più prodotti. Per altre informazioni sulle istanze [riservate, vedere](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations). Il Vantaggio Azure Hybrid è disponibile in anteprima per le istanze di macchina virtuale [riservate.](https://review.docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations#charges-covered-by-reservation) Ciò significa che se sono stati acquistati costi di calcolo a una tariffa scontata usando l'istanza di istanza, è possibile applicare il vantaggio AHB sui costi di licenza per RHEL e SUSE. I passaggi per applicare il vantaggio AHB per un'istanza dell'istanza di istanza di istanza ri rimangono esattamente gli stessi di una normale macchina virtuale.
![AHB per le richieste di archiviazione](./media/azure-hybrid-benefit/reserved-instances.png)

>[!NOTE]
>Se sono già state acquistate prenotazioni per il software RHEL o SUSE PAYG su Azure Marketplace, attendere il completamento del percorso di prenotazione prima di usare il Vantaggio Azure Hybrid.


## <a name="frequently-asked-questions"></a>Domande frequenti
*D: È possibile usare un tipo di licenza `RHEL_BYOS` di con un'immagine SLES o viceversa?*

A: No, non è possibile. Se si tenta di immettere un tipo di licenza che corrisponde in modo non corretto alla distribuzione in esecuzione nella macchina virtuale, non verranno aggiornati i metadati di fatturazione. Se tuttavia si immette accidentalmente il tipo di licenza errato, l'aggiornamento della macchina virtuale al tipo di licenza corretto abiliterà comunque il vantaggio.

*D: Sono stato registrato con Red Hat Cloud Access ma non è ancora possibile abilitare il vantaggio nelle macchine virtuali RHEL. Cosa dovrei fare?*

A: La propagazione della registrazione della sottoscrizione Red Hat Cloud Access da Red Hat ad Azure potrebbe richiedere del tempo. Se l'errore persiste dopo un giorno lavorativo, contattare il supporto tecnico Microsoft.

*D: È stata distribuita una macchina virtuale usando RHEL BYOS "golden image". È possibile convertire la fatturazione per queste immagini da BYOS a PAYG?*

A: No, non è possibile. Vantaggio Azure Hybrid supporta la conversione solo nelle immagini con pagamento in base al costo.

*D: L'immagine RHEL è stata caricata dall'ambiente locale (tramite Azure Migrate, Azure Site Recovery o altro) in Azure. È possibile convertire la fatturazione per queste immagini da BYOS a PAYG?*

A: No, non è possibile. La Vantaggio Azure Hybrid è attualmente disponibile solo per le immagini RHEL e SLES in Azure Marketplace. 

*D: L'immagine RHEL è stata caricata dall'ambiente locale (tramite Azure Migrate, Azure Site Recovery o altro) in Azure. È necessario eseguire qualsiasi operazione per trarre vantaggio dalla Vantaggio Azure Hybrid?*

A: No, non è così. Le immagini RHEL caricate sono già considerate BYOS e vengono addebitati solo i costi dell'infrastruttura di Azure. L'utente è responsabile dei costi della sottoscrizione RHEL, proprio come per gli ambienti locali. 

*D: È possibile usare Vantaggio Azure Hybrid macchine virtuali distribuite da Azure Marketplace RHEL e SLES SAP?*

R: sì. È possibile usare il tipo di licenza di per le macchine virtuali RHEL e per le conversioni di macchine virtuali distribuite da Azure Marketplace `RHEL_BYOS` `SLES_BYOS` immagini SAP RHEL e SLES.

*D: È possibile usare Vantaggio Azure Hybrid set di scalabilità di macchine virtuali per RHEL e SLES?*

A: Sì, i Vantaggio Azure Hybrid set di scalabilità di macchine virtuali per RHEL e SLES sono in anteprima. Per altre [informazioni su questo vantaggio e su come usarlo, vedere](/azure/virtual-machine-scale-sets/azure-hybrid-benefit-linux)qui . 

*D: È possibile usare Vantaggio Azure Hybrid istanze riservate per RHEL e SLES?*

A: Sì, Vantaggio Azure Hybrid'istanza riservata per RHEL e SLES è disponibile in anteprima. Per altre [informazioni su questo vantaggio e su come usarlo, vedere](#azure-hybrid-benefit-on-reserved-instances-is-in-preview)qui .

*D: È possibile usare Vantaggio Azure Hybrid in una macchina virtuale distribuita per SQL Server in immagini RHEL?*

A: No, non è possibile. Non è disponibile alcun piano per il supporto di queste macchine virtuali.

*D: È possibile usare Vantaggio Azure Hybrid nella sottoscrizione del data center virtuale RHEL?*

A: No, non è possibile. Il data center virtuale non è affatto supportato in Azure, incluso AHB.  
 

## <a name="common-problems"></a>Problemi comuni
Questa sezione elenca i problemi comuni che possono verificarsi e i passaggi per la mitigazione.

| Errore | Mitigazione |
| ----- | ---------- |
| "Non è stato possibile completare l'azione perché i record indicano che non è stata abilitata Red Hat Cloud Access nella sottoscrizione di Azure...." | Per usare il vantaggio con le macchine virtuali RHEL, è necessario prima [registrare le sottoscrizioni di Azure con Red Hat Cloud Access](https://access.redhat.com/management/cloud).

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni su come creare e aggiornare macchine virtuali e aggiungere tipi di licenza (RHEL_BYOS, SLES_BYOS) Vantaggio Azure Hybrid tramite l'interfaccia della riga di comando di Azure](/cli/azure/vm)
