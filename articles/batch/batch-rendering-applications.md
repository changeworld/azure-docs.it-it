---
title: Applicazioni per il rendering
description: Con Azure Batch è possibile usare qualsiasi applicazione per il rendering. Tuttavia, le immagini di macchine virtuali (VM) di Azure Marketplace sono disponibili con applicazioni comuni preinstallate.
ms.date: 02/12/2021
ms.topic: how-to
ms.openlocfilehash: e296ab09498b6bb7ee21e3d88c9c416c66368d69
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362234"
---
# <a name="pre-installed-applications-on-batch-rendering-vm-images"></a>Applicazioni preinstallate in batch rendering di immagini VM

Con Azure Batch è possibile usare qualsiasi applicazione per il rendering. Tuttavia, le immagini di macchine virtuali (VM) di Azure Marketplace sono disponibili con applicazioni comuni preinstallate.

Dove applicabile, sono disponibili licenze con pagamento in base al consumo per le applicazioni per il rendering preinstallate. Quando viene creato un pool di Batch, è possibile specificare le applicazioni richieste e sia il costo della VM sia quello delle applicazioni verrà addebitato al minuto. I prezzi delle applicazioni sono elencati nella [pagina dei prezzi di Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Alcune applicazioni supportano solo Windows, ma la maggior parte è supportata sia in Windows che in Linux.

## <a name="applications-on-latest-centos-7-rendering-image"></a>Applicazioni su un'immagine di rendering CentOS 7 più recente

L'elenco seguente si applica all'immagine di rendering CentOS, versione 1.1.7.

* Autodesk Maya aggiornamento I/O 2020 4,6
* Autodesk Arnold per Maya 2020 (Arnold versione 6.2.0.0) MtoA-4.2.0-2020
* Chaos Group V-Ray per Maya 2020 (versione 5.00.21)
* Blender (2.80)
* AZ 10

## <a name="applications-on-latest-windows-server-rendering-image"></a>Applicazioni su un'immagine di rendering di Windows Server più recente

L'elenco seguente si applica all'immagine di rendering di Windows Server, versione 1.5.0.

* Autodesk Maya aggiornamento I/O 2020 4,4
* Autodesk 3ds Max I/O 2021 aggiornamento 3
* Autodesk Arnold per Maya 2020 (Arnold versione 6.1.0.1) MtoA-4.1.1.1-2020
* Autodesk Arnold per 3ds Max 2021 (Arnold versione 6.1.0.1) MAXtoA-4.2.2.20-2021
* Chaos Group V-Ray per Maya 2020 (versione 5.00.21)
* Chaos Group V-Ray per 3ds Max 2021 (versione 5.00.05)
* Blender (2.79)
* Blender (2.80)
* AZ 10

> [!IMPORTANT]
> Per eseguire V-Ray con Maya all'esterno dei [modelli di estensione Azure Batch](https://github.com/Azure/batch-extension-templates), avviare `vrayses.exe` prima di eseguire il rendering. Per avviare vrays.exe all'esterno dei modelli, è possibile usare il comando seguente `%MAYA_2020%\vray\bin\vrayses.exe"`.
>
> Per un esempio, vedere l'attività di avvio del [modello Maya e V-Ray](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json) in GitHub.

## <a name="applications-on-previous-windows-server-rendering-images"></a>Applicazioni sulle immagini di rendering precedenti di Windows Server

L'elenco seguente si applica alle immagini di rendering Windows Server 2016, versione 1.3.8.

* Autodesk Maya I/O 2017 Update 5 (versione 17.4.5459)
* Autodesk Maya I/O 2018 Update 6 (versione 18.4.0.7622)
* Autodesk Maya I/O 2019
* Autodesk 3ds Max I/O 2018 Update 4 (versione 20.4.0.4254)
* Autodesk 3ds Max I/O 2019 Update 1 (versione 21.2.0.2219)
* Autodesk 3ds Max I/O 2020 Update 2
* Autodesk Arnold per Maya 2017 (Arnold versione 5.3.0.2) MtoA-3.2.0.2-2017
* Autodesk Arnold per Maya 2018 (Arnold versione 5.3.0.2) MtoA-3.2.0.2-2018
* Autodesk Arnold per Maya 2019 (Arnold versione 5.3.0.2) MtoA-3.2.0.2-2019
* Autodesk Arnold per 3ds Max 2018 (Arnold versione 5.3.0.2)(versione 1.2.926)
* Autodesk Arnold per 3ds Max 2019 (Arnold versione 5.3.0.2)(versione 1.2.926)
* Autodesk Arnold per 3ds Max 2020 (Arnold versione 5.3.0.2)(versione 1.2.926)
* Chaos Group V-Ray per Maya 2017 (versione 4.12.01)
* Chaos Group V-Ray per Maya 2018 (versione 4.12.01)
* Chaos Group V-Ray per Maya 2019 (versione 4.04.03)
* Chaos Group V-Ray per 3ds Max 2018 (versione 4.20.01)
* Chaos Group V-Ray per 3ds Max 2019 (versione 4.20.01)
* Chaos Group V-Ray per 3ds Max 2020 (versione 4.20.01)
* Blender (2.79)
* Blender (2.80)
* AZ 10

L'elenco seguente si applica alle immagini di rendering Windows Server 2016, versione 1.3.7.

* Autodesk Maya I/O 2017 Update 5 (versione 17.4.5459)
* Autodesk Maya I/O 2018 Update 4 (versione 18.4.0.7622)
* Autodesk 3ds Max I/O 2019 Update 1 (versione 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (versione 20.4.0.4254)
* Autodesk Arnold per Maya 2017 (Arnold versione 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold per Maya 2018 (Arnold versione 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold per 3ds Max 2018 (Arnold versione 5.0.2.4)(versione 1.2.926)
* Autodesk Arnold per 3ds Max 2019 (Arnold versione 5.0.2.4)(versione 1.2.926)
* Chaos Group V-Ray per Maya 2018 (versione 3.52.03)
* Chaos Group V-Ray per 3ds Max 2018 (versione 3.60.02)
* Chaos Group V-Ray per Maya 2019 (versione 3.52.03)
* Chaos Group V-Ray per 3ds Max 2019 (versione 4.10.01)
* Blender (2.79)

> [!NOTE]
> Chaos Group V-Ray per 3ds Max 2019 (versione 4.10.01) introduce modifiche che causano un'interruzione in V-Ray. Per usare la versione precedente (versione 3.60.02), usare i nodi di rendering Windows Server 2016, versione 1.3.2.

## <a name="applications-on-previous-centos-rendering-images"></a>Applicazioni su immagini di rendering CentOS precedenti

L'elenco seguente si applica alle immagini di rendering CentOS 7.6, versione 1.1.6.

* Autodesk Maya I/O 2017 Update 5 (cut 201708032230)
* Autodesk Maya I/O 2018 Update 2 (cut 201711281015)
* Autodesk Maya I/O 2019 Update 1
* Autodesk Arnold per Maya 2017 (Arnold versione 5.3.1.1) MtoA-3.2.1.1-2017
* Autodesk Arnold per Maya 2018 (Arnold versione 5.3.1.1) MtoA-3.2.1.1-2018
* Autodesk Arnold per Maya 2019 (Arnold versione 5.3.1.1) MtoA-3.2.1.1-2019
* Chaos Group V-Ray per Maya 2017 (versione 3.60.04)
* Chaos Group V-Ray per Maya 2018 (versione 3.60.04)
* Blender (2.68)
* Blender (2.8)

## <a name="next-steps"></a>Passaggi successivi

Per usare le immagini di VM per il rendering, è necessario specificare le immagini nella configurazione del pool al momento della creazione. Vedere le [funzionalità del pool di Batch per il rendering](./batch-rendering-functionality.md).
