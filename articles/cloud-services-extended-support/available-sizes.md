---
title: Dimensioni disponibili per i servizi cloud di Azure (supporto esteso)
description: Dimensioni disponibili per le distribuzioni di servizi cloud di Azure (supporto esteso)
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 1c1faf14eb101da41679f9f0596e1e750a3c6a51
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166277"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Dimensioni disponibili per i servizi cloud di Azure (supporto esteso)

Questo articolo descrive le dimensioni delle macchine virtuali disponibili per le istanze di servizi cloud (supporto esteso).   

| Famiglia SKU |  ACU/Core | 
|---|---|
| [A5-7](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#a-series)| 100 |
|[A8-A11](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#a-series---compute-intensive-instances) | 225* |
|[Av2](../virtual-machines/av2-series.md) | 100 | 
|[D](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#d-series) | 160 | 
|[Dv2](../virtual-machines/dv2-dsv2-series.md) | 160 - 190* |
|[Dv3](../virtual-machines/dv3-dsv3-series.md) | 160 - 190* |
|[Dav4](../virtual-machines/dav4-dasv4-series.md) | 230-260 |
|[Eav4](../virtual-machines/eav4-easv4-series.md) | 230-260 |
|[EV3](../virtual-machines/ev3-esv3-series.md) | 160 - 190* |
|[G](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#g-series) | 180-240 * |
|[H](../virtual-machines/h-series.md) | 290 - 300* | 

>[!NOTE]
> Le unità ACU contrassegnate con * usano la tecnologia Intel® Turbo per aumentare la frequenza della CPU e offrire un miglioramento delle prestazioni. L'entità dell'aumento di prestazioni può variare in base alle dimensioni della macchina virtuale, al carico di lavoro e agli altri carichi di lavoro in esecuzione sullo stesso host.

## <a name="configure-sizes-for-cloud-services-extended-support"></a>Configurare le dimensioni per i servizi cloud (supporto esteso)

È possibile specificare le dimensioni della macchina virtuale di un'istanza del ruolo come parte del modello del servizio nel file di definizione del servizio. Le dimensioni del ruolo determinano il numero di core CPU, la capacità di memoria e le dimensioni file system locali.

Ad esempio, impostando le dimensioni dell'istanza del ruolo Web su `Standard_D2` : 

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2"> 
</WorkerRole> 
```

## <a name="change-the-size-of-an-existing-role"></a>Modificare le dimensioni di un ruolo esistente

Per modificare le dimensioni di un ruolo esistente, modificare le dimensioni della macchina virtuale nel file di definizione del servizio (csdef), ricreare il pacchetto del servizio cloud e ridistribuirlo. 

## <a name="get-a-list-of-available-sizes"></a>Ottenere un elenco delle dimensioni disponibili 

Per recuperare un elenco di dimensioni disponibili, vedere [SKU di risorse-elencare](/rest/api/compute/resourceskus/list) e applicare i filtri seguenti:


`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


## <a name="next-steps"></a>Passaggi successivi 
- Esaminare i [prerequisiti di distribuzione](deploy-prerequisite.md) per i servizi cloud (supporto esteso).
- Esaminare le [domande frequenti](faq.md) per i servizi cloud (supporto esteso).
- Distribuire un servizio cloud (supporto esteso) usando il [portale di Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), il [modello](deploy-template.md) o [Visual Studio](deploy-visual-studio.md).
