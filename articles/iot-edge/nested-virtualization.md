---
title: Virtualizzazione annidata per Azure IoT Edge per Linux in Windows | Microsoft Docs
description: Informazioni su come esplorare la virtualizzazione annidata in Azure IoT Edge per Linux in Windows.
author: fcabrera
manager: kgremban
ms.author: fcabrera
ms.date: 2/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 4f01362fd9342c1f508f165b34e121a11e8d07e2
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107496589"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>Virtualizzazione annidata per Azure IoT Edge per Linux in Windows
Esistono due forme di virtualizzazione annidata compatibili con Azure IoT Edge per Linux in Windows. Gli utenti possono scegliere di eseguire la distribuzione tramite una macchina virtuale locale o una macchina virtuale di Azure. Questo articolo fornisce agli utenti una maggiore chiarezza sull'opzione più adatta allo scenario e fornisce informazioni dettagliate sui requisiti di configurazione.

> [!NOTE]
>
> Assicurarsi di abilitare [un'opzione di rete per](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options) la virtualizzazione annidata. In caso negativo, si verificano errori di installazione di EFLOW. 

## <a name="deployment-on-local-vm"></a>Distribuzione nella macchina virtuale locale
Questo è l'approccio di base per qualsiasi macchina virtuale Windows che ospita Azure IoT Edge per Linux in Windows. In questo caso, la virtualizzazione annidata deve essere abilitata prima di avviare la distribuzione. Per altre informazioni su come configurare questo scenario, vedere Eseguire [Hyper-V in](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) una macchina virtuale con virtualizzazione annidata.

Se si usa Windows Server, assicurarsi di [installare il ruolo Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

## <a name="deployment-on-azure-vms"></a>Distribuzione in macchine virtuali di Azure
Se si sceglie di eseguire la distribuzione in macchine virtuali di Azure, si noti che non è disponibile alcun commutatore esterno in base alla progettazione. Azure IoT Edge per Linux in Windows non è compatibile anche in una macchina virtuale di Azure che esegue lo SKU del server, a meno che non venga eseguito uno script specifico che attiva un'opzione predefinita. Per altre informazioni su come visualizzare un'opzione predefinita, vedere la sezione [Windows Server riportata di](#windows-server) seguito. 

> [!NOTE]
>
> Tutte le macchine virtuali di Azure che devono ospitare EFLOW devono essere una macchina virtuale che [supporta la virtualizzazione annidata](../virtual-machines/acu.md)


## <a name="limited-use-of-external-switch"></a>Uso limitato del commutatore esterno
In qualsiasi scenario in cui la macchina virtuale non può ottenere un indirizzo IP tramite un commutatore esterno, la funzionalità di distribuzione usa automaticamente il commutatore predefinito interno per la distribuzione. Ciò significa che la gestione della macchina virtuale Azure IoT Edge per Linux può essere eseguita solo nel dispositivo di destinazione stesso(ad esempio, la connessione alla macchina virtuale Azure IoT Edge per Linux tramite l'estensione WAC PowerShell SSH è possibile solo in localhost).

## <a name="windows-server"></a>Windows Server
Per gli utenti di Windows Server, si Azure IoT Edge per Linux in Windows non supporta automaticamente l'opzione predefinita.

* Conseguenze per la macchina virtuale locale: assicurarsi che la macchina virtuale EFLOW possa ottenere un indirizzo IP tramite il commutatore esterno.

* Conseguenze per le macchine virtuali di Azure: poiché non è presente alcun commutatore esterno nelle macchine virtuali di Azure, non è possibile distribuire EFLOW prima di configurare un commutatore interno nel server.

Non è disponibile alcuna opzione predefinita per gli SKU del server per impostazione predefinita, indipendentemente dal fatto che lo SKU server venga eseguito o meno in una macchina virtuale di Azure. Quando si esegue la distribuzione in una macchina virtuale di Azure in cui non è possibile usare il commutatore esterno, il commutatore predefinito deve essere configurato manualmente prima di Azure IoT Edge per la distribuzione linux in Windows. La funzionalità di distribuzione illustra questo perché richiede la configurazione IP per il commutatore interno, una configurazione NAT e l'installazione e la configurazione di un server DHCP. La funzionalità di distribuzione nell'anteprima pubblica indica che non si aggira con queste impostazioni per non compromettere le configurazioni di rete nelle distribuzioni produttive.

* Le informazioni pertinenti su come configurare manualmente il commutatore predefinito sono disponibili qui: Come abilitare [la virtualizzazione annidata in Macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)
* La documentazione su come configurare un server DHCP per questo scenario è disponibile qui: [Deploy DHCP using Windows PowerShell](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-deploy-wps)
