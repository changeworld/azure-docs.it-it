---
title: Che cos'è Azure IoT Edge per Linux in Windows | Microsoft Docs
description: Panoramica di è possibile eseguire i moduli IoT Edge Linux nei dispositivi Windows 10
author: kgremban
manager: philmea
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: ebe0ac1151f3a1f43072f2832e2f433182ccc82d
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634180"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows-preview"></a>Informazioni su Azure IoT Edge per Linux in Windows (anteprima)

Azure IoT Edge per Linux in Windows consente di eseguire carichi di lavoro Linux in contenitori insieme alle applicazioni Windows nelle distribuzioni di Windows. Le aziende che fanno affidamento su Windows Internet per potenziare i dispositivi perimetrali possono ora sfruttare i vantaggi delle soluzioni di analisi native del cloud create in Linux.

IoT Edge per Linux in Windows funziona eseguendo una macchina virtuale Linux in un dispositivo Windows. La macchina virtuale Linux viene preinstallata con il IoT Edge Runtime. Tutti i moduli IoT Edge distribuiti nel dispositivo vengono eseguiti all'interno della macchina virtuale. Nel frattempo, le applicazioni Windows in esecuzione nel dispositivo host Windows possono comunicare con i moduli in esecuzione nella macchina virtuale Linux.

>[!NOTE]
>Prendere in considerazione il [sondaggio del prodotto](https://aka.ms/AzEFLOW-Registration) per contribuire a migliorare Azure IOT Edge per Linux in Windows in base ai IOT Edge sfondo e agli obiettivi. È anche possibile usare questo sondaggio per iscriversi a future Azure IoT Edge per Linux negli annunci di Windows.

## <a name="components"></a>Componenti

IoT Edge per Linux in Windows usa i componenti seguenti per consentire l'esecuzione di carichi di lavoro di Linux e Windows tra loro e per comunicare facilmente:

* **Una macchina virtuale Linux che esegue Azure IOT Edge**: una macchina virtuale Linux, basata sul sistema operativo del [primo gruppo di](https://github.com/microsoft/CBL-Mariner) Microsoft, viene compilata con il runtime di IOT Edge e convalidata come ambiente supportato di livello 1 per i carichi di lavoro IOT Edge.

* Interfaccia di **amministrazione di Windows**: un'estensione IOT Edge per l'interfaccia di amministrazione di Windows facilita l'installazione, la configurazione e la diagnostica dei IOT Edge nella macchina virtuale Linux. L'interfaccia di amministrazione di Windows è in grado di distribuire IoT Edge per Linux in Windows sul dispositivo locale oppure può connettersi ai dispositivi di destinazione e gestirli in modalità remota.

* **Microsoft Update**: l'integrazione con Microsoft Update mantiene i componenti Windows Runtime, la macchina virtuale Mariner Linux e IOT Edge aggiornati.

![Windows e la VM Linux vengono eseguiti in parallelo, mentre l'interfaccia di amministrazione di Windows controlla entrambi i componenti](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

La comunicazione bidirezionale tra il processo di Windows e la macchina virtuale Linux significa che i processi Windows possono fornire interfacce utente o proxy hardware per i carichi di lavoro eseguiti nei contenitori Linux.

## <a name="samples"></a>Esempi

IoT Edge per Linux in Windows enfatizza l'interoperabilità tra i componenti di Linux e Windows.

Per esempi che illustrano la comunicazione tra le applicazioni Windows e i moduli IoT Edge, vedere [esempi di Windows 10](https://github.com/microsoft/Windows-IoT-Samples).

## <a name="public-preview"></a>Anteprima pubblica

IoT Edge per Linux in Windows è attualmente disponibile in [anteprima pubblica](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). I processi di installazione e gestione possono essere diversi rispetto alle funzionalità disponibili a livello generale.

Attualmente, IoT Edge per Linux in Windows utilizza la versione di anteprima di Windows insider dell'interfaccia di amministrazione di Windows. Per ulteriori informazioni sul programma Windows Insider e per eseguire la registrazione, vedere [che cos'è il programma Windows Insider?](https://insider.windows.com/about-windows-insider-program).

## <a name="support"></a>Supporto

Usare i canali di supporto e feedback IoT Edge per ottenere assistenza con IoT Edge per Linux in Windows.

**Segnalazione di bug** : i bug possono essere segnalati nella [pagina problemi](https://github.com/azure/iotedge/issues) del IOT Edge progetto open source. 

**Team di supporto tecnico Microsoft**: gli utenti con un [piano di supporto](https://azure.microsoft.com/support/plans/) possono rivolgersi al team di supporto tecnico Microsoft creando un ticket di supporto dal [portale di Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Richieste di funzionalità**: il prodotto Azure IoT Edge tiene traccia delle richieste di funzionalità tramite la [pagina UserVoice](https://feedback.azure.com/forums/907045-azure-iot-edge) del prodotto.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni ed esempi in azione, vedere [IOT Edge per Linux in Windows 10](https://aka.ms/EFLOWPPC9) Internet.

Seguire la procedura descritta in [installare ed eseguire il provisioning di Azure IOT Edge per Linux in un dispositivo Windows](how-to-install-iot-edge-on-windows.md) per configurare un dispositivo con IOT Edge per Linux in Windows.
