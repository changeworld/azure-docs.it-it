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
ms.openlocfilehash: c3515b559ef647b9a043a04282b4edfc6e7fa9be
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744399"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Dimensioni disponibili per i servizi cloud di Azure (supporto esteso)

Questo articolo descrive le dimensioni delle macchine virtuali disponibili per le istanze di servizi cloud (supporto esteso).   

| Famiglia SKU |  ACU/Core | 
|---|---|
| [A5-7](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#a-series)| 100 |
|[A8-A11](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#a-series---compute-intensive-instances) | 225* |
|[Av2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | 100 | 
|[D](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#d-series) | 160 | 
|[Dv2](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series) | 160 - 190* |
|[Dv3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series) | 160 - 190* |
|[EV3](https://docs.microsoft.com/azure/virtual-machines/ev3-esv3-series) | 160 - 190*
|[G](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#g-series) | 180-240 * |
|[H](https://docs.microsoft.com/azure/virtual-machines/h-series) | 290 - 300* | 

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

Per recuperare un elenco di dimensioni disponibili, vedere [SKU di risorse-elencare](https://docs.microsoft.com/rest/api/compute/resourceskus/list) e applicare i filtri seguenti:


`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


## <a name="next-steps"></a>Passaggi successivi 
- Esaminare i [prerequisiti di distribuzione](deploy-prerequisite.md) per i servizi cloud (supporto esteso).
- Esaminare le [domande frequenti](faq.md) per i servizi cloud (supporto esteso).
- Distribuire un servizio cloud (supporto esteso) usando il [portale di Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), il [modello](deploy-template.md) o [Visual Studio](deploy-visual-studio.md).
