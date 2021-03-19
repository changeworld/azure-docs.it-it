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
ms.openlocfilehash: 0e0021ec3564ca079f9ab02fe5ed3f0cfa5a1560
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609659"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>Virtualizzazione annidata per Azure IoT Edge per Linux in Windows
Esistono due tipi di virtualizzazione annidata compatibili con Azure IoT Edge per Linux in Windows. Gli utenti possono scegliere di eseguire la distribuzione tramite una macchina virtuale locale o una macchina virtuale di Azure. Questo articolo fornisce agli utenti chiarezza sull'opzione migliore per lo scenario e fornisce informazioni dettagliate sui requisiti di configurazione.

> [!NOTE]
>
> Assicurarsi di abilitare un' [opzione netowrking](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options) per la virtualizzazione nidificata. In caso contrario, si verificano errori di installazione di EFLOW. 

## <a name="deployment-on-local-vm"></a>Distribuzione in una macchina virtuale locale
Questo è l'approccio di base per qualsiasi VM Windows che ospita Azure IoT Edge per Linux in Windows. Per questo caso, è necessario abilitare la virtualizzazione annidata prima di avviare la distribuzione. Per altre informazioni su come configurare questo scenario, vedere [eseguire Hyper-V in una macchina virtuale con la virtualizzazione nidificata](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) .

Se si usa Windows Server, assicurarsi di [installare il ruolo Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

## <a name="deployment-on-azure-vms"></a>Distribuzione in macchine virtuali di Azure
Se si sceglie di eseguire la distribuzione in macchine virtuali di Azure, si noti che non è disponibile alcun commute esterno per progettazione. Azure IoT Edge per Linux in Windows non è compatibile anche in una macchina virtuale di Azure che esegue lo SKU del server a meno che non venga eseguito uno script specifico che riporti un'opzione predefinita. Per ulteriori informazioni su come visualizzare un'opzione predefinita, vedere la [sezione Windows Server](#windows-server) riportata di seguito. 

> [!NOTE]
>
> Tutte le macchine virtuali di Azure che dovrebbero ospitare EFLOW devono essere una macchina virtuale che [supporta la virtualizzazione nidificata](../virtual-machines/acu.md)


## <a name="limited-use-of-external-switch"></a>Uso limitato del Commuter esterno
In uno scenario in cui la macchina virtuale non è in grado di ottenere un indirizzo IP tramite un commutatore esterno, la funzionalità di distribuzione usa automaticamente l'opzione predefinita interna per la distribuzione. Ciò significa che la gestione del Azure IoT Edge per la macchina virtuale Linux può essere eseguita solo sul dispositivo di destinazione stesso (ovvero la connessione al Azure IoT Edge per la VM Linux tramite l'estensione di SSH di PowerShell per WAC è possibile solo in localhost).

## <a name="windows-server"></a>Windows Server
Per gli utenti di Windows Server, si noti che Azure IoT Edge per Linux in Windows non supporta automaticamente l'opzione predefinita.

* Conseguenze per la macchina virtuale locale: assicurarsi che la macchina virtuale EFLOW possa ottenere un indirizzo IP tramite il Commuter esterno.

* Conseguenze per la VM di Azure: poiché non è presente alcun comportatore esterno nelle macchine virtuali di Azure, non è possibile distribuire EFLOW prima di configurare uno switch interno nel server.

Per impostazione predefinita, non sono disponibili opzioni predefinite per gli SKU del server (indipendentemente dal fatto che lo SKU del server venga eseguito o meno in una macchina virtuale di Azure). Quando si esegue la distribuzione in una macchina virtuale di Azure in cui non è possibile usare il Commuter esterno, è necessario configurare e configurare manualmente l'opzione predefinita prima di Azure IoT Edge per la distribuzione di Linux in Windows. La funzionalità di distribuzione riguarda questo problema, poiché richiede la configurazione IP per il Commuter interno, una configurazione NAT e l'installazione e la configurazione di un server DHCP. La funzionalità di distribuzione in versione di anteprima pubblica indica che non è possibile aggirare queste impostazioni senza compromettere le configurazioni di rete nelle distribuzioni produttive.

* Informazioni rilevanti su come configurare manualmente l'opzione predefinita sono disponibili qui: [come abilitare la virtualizzazione annidata in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)
* La documentazione su come configurare un server DHCP per questo scenario è disponibile qui: [distribuire DHCP con Windows PowerShell](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-deploy-wps)
