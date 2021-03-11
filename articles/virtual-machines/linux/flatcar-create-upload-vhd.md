---
title: Creare e caricare un disco rigido virtuale del contenitore pianale per l'uso in Azure
description: Informazioni su come creare e caricare un disco rigido virtuale contenente un sistema operativo pianale container Linux.
author: marga-kinvolk
ms.author: danis
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/16/2020
ms.reviewer: cynthn
ms.openlocfilehash: 5d8be9493b7a312270301e3520f301f797fe2167
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565292"
---
# <a name="using-a-prebuilt-flatcar-image-for-azure"></a>Uso di un'immagine pianale predefinita per Azure

È possibile scaricare immagini del disco rigido virtuale di Azure predefinite di pianale container Linux per ogni canale supportato da pianale:

- [stabile](https://stable.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [beta](https://beta.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [Alfa](https://alpha.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [bordo](https://edge.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)

Questa immagine è già stata completamente configurata e ottimizzata per l'esecuzione in Azure. È sufficiente decomprimerlo.

## <a name="building-your-own-flatcar-based-virtual-machine-for-azure"></a>Creazione di una macchina virtuale basata su pianale per Azure

In alternativa, è possibile scegliere di creare un'immagine Linux del contenitore pianale personalizzata.

In qualsiasi computer basato su Linux, seguire le istruzioni riportate nella [Guida pianale container Linux Developer SDK](https://docs.flatcar-linux.org/os/sdk-modifying-flatcar/). Quando si esegue lo `image_to_vm.sh` script, assicurarsi di passare `--format=azure` a creare un disco rigido virtuale di Azure.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato il file con estensione VHD, è possibile usare il file risultante per creare nuove macchine virtuali in Azure. Se è la prima volta che si carica un file con estensione VHD in Azure, vedere [creare una VM Linux da un disco personalizzato](upload-vhd.md#option-1-upload-a-vhd).
