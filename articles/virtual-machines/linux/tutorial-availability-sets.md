---
title: Distribuire macchine virtuali in un set di disponibilità usando l'interfaccia della riga di comando di Azure
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per distribuire macchine virtuali a disponibilità elevata nei set di disponibilità
documentationcenter: ''
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 21f7d3e6f624c3a8e550a33293b46284bf5c5815
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816297"
---
# <a name="create-and-deploy-virtual-machines-in-an-availability-set-using-azure-cli"></a>Creare e distribuire macchine virtuali in un set di disponibilità usando l'interfaccia della riga di comando di Azure

In questa esercitazione si apprenderà come aumentare la disponibilità e l'affidabilità delle soluzioni delle proprie macchine virtuali in Azure tramite una funzionalità denominata set di disponibilità. I set di disponibilità assicurano che le macchine virtuali distribuite in Azure vengano distribuite tra più cluster hardware isolati. Questa operazione assicura che, se si verifica un errore hardware o software all'interno di Azure, solo un subset delle macchine virtuali viene interessato e che nel complesso la soluzione rimane disponibile e operativa.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un set di disponibilità
> * Creare una macchina virtuale in un set di disponibilità
> * Controllare le dimensioni delle macchine virtuali disponibili

Questa esercitazione usa l'interfaccia della riga di comando all'interno di [Azure Cloud Shell](../../cloud-shell/overview.md), che viene costantemente aggiornato alla versione più recente. Per aprire Cloud Shell, selezionare **Prova** nella parte superiore di qualsiasi blocco di codice.

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.30 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="create-an-availability-set"></a>Creare un set di disponibilità

È possibile creare un set di disponibilità usando il comando [az vm availability-set create](/cli/azure/vm/availability-set). In questo esempio il numero di domini di aggiornamento e di errore viene impostato su *2* per il set di disponibilità denominato *myAvailabilitySet* nel gruppo di risorse *myResourceGroupAvailability*.

Creare prima un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create), quindi creare il set di disponibilità:

```azurecli-interactive
az group create --name myResourceGroupAvailability --location eastus

az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

I set di disponibilità consentono di isolare le risorse in domini di errore e in domini di aggiornamento. Un **dominio di errore** rappresenta una raccolta isolata di server + rete + risorse di archiviazione. Nell'esempio precedente il set di disponibilità viene distribuito in almeno due domini di errore quando vengono distribuite le macchine virtuali. Il set di disponibilità viene distribuito anche in due **domini di aggiornamento**. Due domini di aggiornamento assicurano che, quando vengono eseguiti automaticamente gli aggiornamenti software, le risorse delle macchine virtuali siano isolate, impedendo che tutto il software in esecuzione nelle macchine virtuali venga aggiornato contemporaneamente.


## <a name="create-vms-inside-an-availability-set"></a>Creare macchine virtuali in un set di disponibilità

Per garantire la corretta distribuzione delle macchine virtuali in tutto l'hardware, le VM devono essere create all'interno del set di disponibilità. Non è possibile aggiungere una macchina virtuale esistente a un set di disponibilità dopo la sua creazione.

Quando si crea una macchina virtuale con [az vm create](/cli/azure/vm), usare il parametro `--availability-set` per specificare il nome del set di disponibilità.

```azurecli-interactive
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --vnet-name myVnet \
     --subnet mySubnet \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys
done
```

Sono ora disponibili due macchine virtuali all'interno del set di disponibilità. Poiché si trovano nello stesso set di disponibilità, Azure assicura che le macchine virtuali e tutte le relative risorse (inclusi i dischi dati) vengano distribuite in risorse hardware fisiche isolate. Questa distribuzione consente di garantire una disponibilità molto maggiore della soluzione complessiva delle macchine virtuali.

La distribuzione del set di disponibilità può essere esaminata nel portale accedendo a Gruppi di risorse > myResourceGroupAvailability > myAvailabilitySet. Le macchine virtuali vengono distribuite tra i due domini di errore e di aggiornamento, come illustrato nell'esempio seguente:

![Set di disponibilità nel portale](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Controllare le dimensioni delle macchine virtuali disponibili

È possibile aggiungere più macchine virtuali al set di disponibilità in un secondo momento, se le dimensioni delle macchine virtuali sono disponibili nell'hardware. Usare il comando [az vm availability-set list-sizes](/cli/azure/vm/availability-set#az_vm_availability_set_list_sizes) per elencare tutte le dimensioni disponibili nel cluster hardware per il set di disponibilità:

```azurecli-interactive
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare un set di disponibilità
> * Creare una macchina virtuale in un set di disponibilità
> * Controllare le dimensioni delle macchine virtuali disponibili

Passare all'esercitazione successiva per informazioni sui set di scalabilità di macchine virtuali.

> [!div class="nextstepaction"]
> [Creare un set di scalabilità di macchine virtuali](tutorial-create-vmss.md)

* Per altre informazioni sulle zone di disponibilità, vedere la [documentazione delle zone di disponibilità](../../availability-zones/az-overview.md).
* Altre informazioni sui set di disponibilità e sulle zone di disponibilità sono disponibili [qui](../availability.md).
* Per provare le zone di disponibilità, vedere [Creare una macchina virtuale Linux in una zona di disponibilità con l'interfaccia della riga di comando di Azure](./create-cli-availability-zone.md)