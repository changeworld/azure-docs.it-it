---
title: Informazioni su Azure IoT Edge linux in Windows | Microsoft Docs
description: Panoramica di è possibile eseguire moduli IoT Edge Linux in Windows 10 dispositivi
author: kgremban
manager: philmea
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 3c7fd6c842d465dd5af5257628044666f10f2ece
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538208"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows-preview"></a>Che cos'è Azure IoT Edge per Linux in Windows (anteprima)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge per Linux in Windows consente di eseguire carichi di lavoro Linux in contenitori insieme alle applicazioni Windows nelle distribuzioni Windows IoT. Le aziende che si basano su Windows IoT per alimentare i dispositivi perimetrali possono ora sfruttare i vantaggi delle soluzioni di analisi native del cloud create in Linux.

IoT Edge per Linux in Windows funziona eseguendo una macchina virtuale Linux in un dispositivo Windows. La macchina virtuale Linux viene preinstallato con IoT Edge runtime. Tutti IoT Edge i moduli distribuiti nel dispositivo vengono eseguiti all'interno della macchina virtuale. Nel frattempo, le applicazioni Windows in esecuzione nel dispositivo host Windows possono comunicare con i moduli in esecuzione nella macchina virtuale Linux.

[Inizia subito a](how-to-install-iot-edge-on-windows.md) usare l'anteprima.

>[!NOTE]
>Valuta la possibilità di [partecipare al sondaggio](https://aka.ms/AzEFLOW-Registration) sul prodotto per contribuire a migliorare Azure IoT Edge linux in Windows in base alle tue IoT Edge e agli obiettivi. È anche possibile usare questo sondaggio per iscriversi a annunci Azure IoT Edge per Linux in Windows.

## <a name="components"></a>Componenti

IoT Edge linux in Windows usa i componenti seguenti per consentire l'esecuzione reciproca dei carichi di lavoro Linux e Windows e la comunicazione senza problemi:

* Una macchina virtuale Linux che esegue **Azure IoT Edge:** una macchina virtuale Linux, basata sul sistema operativo [Più grande](https://github.com/microsoft/CBL-Mariner) di Microsoft, viene compilata con il runtime di IoT Edge e convalidata come ambiente supportato di livello 1 per i carichi di lavoro IoT Edge.

* **Windows Admin Center:** un'estensione IoT Edge per Windows Admin Center facilita l'installazione, la configurazione e la diagnostica IoT Edge nella macchina virtuale Linux. Windows Admin Center possibile distribuire IoT Edge per Linux in Windows nel dispositivo locale oppure connettersi ai dispositivi di destinazione e gestirli in remoto.

* **Microsoft Update:** l'integrazione con Microsoft Update mantiene aggiornati i componenti di Windows Runtime, la macchina virtuale Linux Disartie e IoT Edge componenti.

![Windows e la macchina virtuale Linux vengono eseguiti in parallelo, mentre il Windows Admin Center controlla entrambi i componenti](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

La comunicazione bidirezionale tra il processo di Windows e la macchina virtuale Linux significa che i processi di Windows possono fornire interfacce utente o proxy hardware per i carichi di lavoro eseguiti nei contenitori Linux.

## <a name="samples"></a>Esempi

IoT Edge per Linux in Windows enfatizza l'interoperabilità tra i componenti Linux e Windows.

Per esempi che illustrano la comunicazione tra applicazioni Windows e IoT Edge moduli, vedere [EFLOW & Windows 10 IoT Samples](https://aka.ms/AzEFLOW-Samples).

## <a name="public-preview"></a>Anteprima pubblica

IoT Edge per Linux in Windows è attualmente in [anteprima pubblica.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) I processi di installazione e gestione possono essere diversi rispetto alle funzionalità disponibili a livello generale.

## <a name="support"></a>Supporto tecnico

Usare i IoT Edge e i canali di feedback per ottenere assistenza IoT Edge per Linux in Windows.

**Segnalazione di bug:** i bug possono essere segnalati nella [pagina dei](https://github.com/azure/iotedge/issues) problemi IoT Edge progetto open source. I bug correlati Azure IoT Edge per Linux in Windows possono essere segnalati nella pagina dei problemi [di iotedge-eflow](https://aka.ms/AzEFLOW-Issues).

**Team di supporto tecnico Microsoft**: gli utenti con un [piano di supporto](https://azure.microsoft.com/support/plans/) possono rivolgersi al team di supporto tecnico Microsoft creando un ticket di supporto dal [portale di Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Richieste di funzionalità**: il prodotto Azure IoT Edge tiene traccia delle richieste di funzionalità tramite la [pagina UserVoice](https://feedback.azure.com/forums/907045-azure-iot-edge) del prodotto.

## <a name="next-steps"></a>Passaggi successivi

Vedere [IoT Edge per Linux Windows 10 IoT Enterprise](https://aka.ms/EFLOWPPC9) per altre informazioni e un esempio in azione.

Seguire la procedura descritta in Installare ed effettuare [il provisioning di Azure IoT Edge per Linux in](how-to-install-iot-edge-on-windows.md) un dispositivo Windows per configurare un dispositivo con IoT Edge per Linux in Windows.
