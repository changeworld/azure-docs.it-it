---
title: Vantaggio Azure Hybrid per i set di scalabilità di macchine virtuali Linux
description: Informazioni su come Vantaggio Azure Hybrid possibile applicare al set di scalabilità di macchine virtuali per risparmiare denaro sulle macchine virtuali Linux in esecuzione in Azure.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machine-scale-sets
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/20/2021
ms.author: mathapli
ms.openlocfilehash: a714434c39a0c40c2e908f2d0c424f02851921a6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933679"
---
# <a name="azure-hybrid-benefit-for-linux-virtual-machine-scale-set-public-preview"></a>Vantaggio Azure Hybrid per il set di scalabilità di macchine virtuali Linux (anteprima pubblica)

**Vantaggio Azure Hybrid per il set di scalabilità di macchine virtuali Linux è ora disponibile in anteprima pubblica**. I vantaggi di vantaggio Azure Hybrid consentono di ridurre il costo di esecuzione dei set di [scalabilità di macchine virtuali](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)RHEL e SLES.

Con questo vantaggio si paga solo il costo dell'infrastruttura del set di scalabilità. Il vantaggio è disponibile per tutte le immagini RHEL e SLES Marketplace con pagamento in base al consumo (PAYG).


>[!NOTE]
> Questo articolo descrive i Vantaggio Azure Hybrid per Linux VMSS. Esiste un [articolo separato disponibile [qui vantaggio Azure Hybrid per le macchine virtuali Linux](https://docs.microsoft.com/azure/virtual-machines/linux/azure-hybrid-benefit-linux), che è già disponibile per i clienti di Azure a partire dal novembre 2020.

## <a name="benefit-description"></a>Descrizione del vantaggio
Azure Hybrid consente di usare le licenze di accesso cloud esistenti da Red Hat o SUSE e di convertire in modo flessibile le istanze del set di scalabilità di macchine virtuali per la fatturazione BYOS (Bring your own Subscription). 

Il set di scalabilità di macchine virtuali distribuito da immagini del Marketplace PAYG addebiterà sia l'infrastruttura che la tariffa software. Con vantaggio Azure Hybrid, le istanze di PAYG possono essere convertite in un modello di fatturazione BYOS senza ridistribuzione.

:::image type="content" source="./media/azure-hybrid-benefit-linux/azure-hybrid-benefit-linux-cost.png" alt-text="Visualizzazione dei costi Vantaggio Azure Hybrid sulle VM Linux.":::

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux"></a>Ambito di Vantaggio Azure Hybrid idoneità per Linux
Vantaggio Azure Hybrid è disponibile per tutte le immagini RHEL e SLES PAYG di Azure Marketplace. Il vantaggio non è ancora disponibile per immagini RHEL o SLES BYOS o immagini personalizzate di Azure Marketplace.

Le istanze dell'host dedicato di Azure e i vantaggi ibridi SQL non sono idonei per Vantaggio Azure Hybrid se si usa già il vantaggio con le VM Linux.

## <a name="get-started"></a>Introduzione

### <a name="red-hat-customers"></a>Clienti Red Hat

Vantaggio Azure Hybrid per RHEL è disponibile per i clienti di Red Hat che soddisfano entrambi questi criteri:

- Avere sottoscrizioni RHEL attive o inutilizzate idonee per l'uso in Azure
- Sono state abilitate una o più sottoscrizioni per l'uso in Azure con il programma [Red Hat cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)

> [!IMPORTANT]
> Verificare che sia stata abilitata la sottoscrizione corretta nel programma di [accesso al cloud](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) .

Per iniziare a usare il vantaggio per Red Hat:

1. Abilitare le sottoscrizioni RHEL idonee in Azure usando l' [interfaccia del cliente Red Hat cloud Access](https://access.redhat.com/management/cloud).

   Le sottoscrizioni di Azure fornite durante il processo di abilitazione di Red Hat cloud Access saranno quindi autorizzate a usare la funzionalità Vantaggio Azure Hybrid.
1. Applicare Vantaggio Azure Hybrid a tutti i set di scalabilità di macchine virtuali RHEL PAYG esistenti e nuovi. È possibile usare portale di Azure o l'interfaccia della riga di comando di Azure per abilitare il vantaggio.
1. Seguire i [passaggi successivi](https://access.redhat.com/articles/5419341) consigliati per la configurazione delle origini aggiornamenti per le macchine virtuali RHEL e per le linee guida sulla conformità delle sottoscrizioni RHEL.


### <a name="suse-customers"></a>Clienti SUSE

Per iniziare a usare il vantaggio per SUSE:

1. Eseguire la registrazione con il programma SUSE public cloud.
1. Applicare il vantaggio al set di scalabilità di macchine virtuali appena creato o esistente tramite l'portale di Azure o l'interfaccia della riga di comando di Azure.
1. Registrare le VM che ricevono il vantaggio con una fonte separata di aggiornamenti.


## <a name="enable-and-disable-the-benefit-on-azure-portal"></a>Abilitare e disabilitare il vantaggio nel portale di Azure 
L'esperienza del portale per l'abilitazione e la disabilitazione di vantaggio Azure Hybrid nel set di scalabilità di macchine virtuali **non è attualmente disponibile**.

## <a name="enable-and-disable-the-benefit-using-azure-cli"></a>Abilitare e disabilitare il vantaggio usando l'interfaccia della riga di comando di Azure

È possibile usare il `az vmss update` comando per aggiornare le macchine virtuali esistenti. Per le VM RHEL, eseguire il comando con un `--license-type` parametro di `RHEL_BYOS` . Per le macchine virtuali SLES, eseguire il comando con un `--license-type` parametro di `SLES_BYOS` .

### <a name="cli-example-to-enable-the-benefit"></a>Esempio di interfaccia della riga di comando per abilitare il vantaggio
```azurecli
# This will enable the benefit on a RHEL VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>Esempio di interfaccia della riga di comando per disabilitare il vantaggio
Per disabilitare il vantaggio, utilizzare il `--license-type` valore seguente `None` :

```azurecli
# This will disable the benefit on a VM
az vmss update -g myResourceGroup -n myVmName --license-type None
```

>[!NOTE]
> I set di scalabilità hanno un ["criterio di aggiornamento"](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) che determina il modo in cui le macchine virtuali vengono aggiornate con il modello di set di scalabilità più recente. Di conseguenza, se il VMSS dispone di criteri di aggiornamento automatici, il vantaggio vantaggio Azure Hybrid verrà applicato automaticamente durante l'aggiornamento delle istanze di macchina virtuale. Se VMSS dispone di criteri di aggiornamento in sequenza, in base agli aggiornamenti pianificati, verrà applicato vantaggio Azure Hybrid.
In caso di criteri di aggiornamento "manuali", sarà necessario eseguire l'aggiornamento manuale di ogni macchina virtuale esistente.  

### <a name="cli-example-to-upgrade-virtual-machine-scale-set-instances-in-case-of-manual-upgrade-policy"></a>Esempio di interfaccia della riga di comando per aggiornare le istanze del set di scalabilità di macchine virtuali in caso di criteri di aggiornamento manuale 
```azurecli
# This will bring VMSS instances up to date with latest VMSS model 
az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
```

## <a name="apply-the-azure-hybrid-benefit-at-virtual-machine-scale-set-create-time"></a>Applicare la Vantaggio Azure Hybrid al set di scalabilità di macchine virtuali crea ora 
Oltre ad applicare il Vantaggio Azure Hybrid al set di scalabilità di macchine virtuali con pagamento in base al consumo esistente, è possibile richiamarlo al momento della creazione del set di scalabilità di macchine virtuali. I vantaggi di questa operazione sono triplici:
- È possibile eseguire il provisioning di istanze del set di scalabilità di macchine virtuali PAYG e BYOS usando la stessa immagine e lo stesso processo.
- Consente modifiche future in modalità di gestione delle licenze, un elemento non disponibile con un'immagine solo BYOS.
- Per impostazione predefinita, le istanze del set di scalabilità di macchine virtuali verranno connesse a Red Hat Update Infrastructure (RHUI) per garantire che rimanga aggiornato e protetto. È possibile modificare il meccanismo aggiornato dopo la distribuzione in qualsiasi momento.

### <a name="cli-example-to-create-virtual-machine-scale-set-with-ahb-benefit"></a>Esempio di interfaccia della riga di comando per creare set di scalabilità di macchine virtuali con vantaggio Azure Hybrid
```azurecli
# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type RHEL_BYOS 

# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type SLES_BYOS
```

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni su come creare e aggiornare le macchine virtuali e aggiungere i tipi di licenza (RHEL_BYOS, SLES_BYOS) per Vantaggio Azure Hybrid usando l'interfaccia della riga di comando di Azure](/cli/azure/vmss)
