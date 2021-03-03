---
title: Installazione del driver GPU AMD serie N di Azure per Windows
description: Informazioni su come configurare i driver GPU AMD per macchine virtuali serie N che eseguono Windows Server o Windows in Azure
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 78971a92f1815236bd4b360e8df01b02b4c40626
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677224"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Installare i driver GPU AMD sulle macchine virtuali serie N eseguite in Windows

Per usufruire delle funzionalità GPU delle nuove macchine virtuali serie NVv4 di Azure che eseguono Windows, è necessario installare i driver GPU AMD. L'[estensione del driver GPU AMD](../extensions/hpccompute-amd-gpu-windows.md) installa driver GPU AMD in una macchina virtuale serie NVv4. Installare o gestire l'estensione usando il portale di Azure o strumenti come i modelli di Azure PowerShell Azure o Azure Resource Manager. Vedere la [documentazione dell'estensione dei driver GPU AMD](../extensions/hpccompute-amd-gpu-windows.md) per informazioni sui sistemi operativi supportati e sui passaggi di distribuzione.

Se si sceglie di installare manualmente i driver GPU AMD, questo articolo descrive i driver, i sistemi operativi supportati e i passaggi di installazione e verifica.

Nelle macchine virtuali NVv4 sono supportati solo i driver GPU pubblicati da Microsoft. NON installare driver GPU di altro tipo.

Per conoscere le specifiche base, le capacità di archiviazione e i dettagli relativi ai dischi, vedere [Dimensioni delle macchine virtuali Windows GPU](../sizes-gpu.md?toc=/azure/virtual-machines/windows/toc.json).



## <a name="supported-operating-systems-and-drivers"></a>Sistemi operativi e driver supportati

| OS | Driver |
| -------- |------------- |
| Windows 10 Enterprise multisessione-Build 1909 <br/><br/>Windows 10-Build 1909<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. Q4](https://download.microsoft.com/download/f/1/6/f16e6275-a718-40cd-a366-9382739ebd39/AMD-Azure-NVv4-Driver-20Q4.exee) (. exe) |

 > [!NOTE]
   >  Se si usa la build 1903/1909, potrebbe essere necessario aggiornare i criteri di gruppo seguenti per ottenere prestazioni ottimali. Queste modifiche non sono necessarie per altre compilazioni di Windows.
   >  
   >  [Configurazione computer-criteri di >->impostazioni di Windows->Modelli amministrativi->componenti di Windows->Servizi Desktop remoto->ambiente sessione remota host sessione Desktop remoto->], impostare i criteri [USA driver di visualizzazione grafica WDDM per connessioni Desktop remoto] su disabilitato.
   >  


## <a name="driver-installation"></a>Installazione del driver

1. Connettersi tramite Desktop remoto a ciascuna macchina virtuale serie NVv4.

2. Se è necessario disinstallare la versione precedente del driver, scaricare l'utilità di pulitura AMD [qui](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe) . non usare l'utilità fornita con la versione precedente del driver.

3. Scaricare e installare il driver più recente.

4. Riavviare la macchina virtuale.

## <a name="verify-driver-installation"></a>Verificare l'installazione del driver

È possibile verificare l'installazione del driver in Gestione dispositivi. L'esempio seguente illustra la corretta configurazione della scheda Radeon Instinct MI25 in una macchina virtuale NVv4 di Azure.
<br />

![Screenshot che illustra la corretta configurazione della scheda Radeon Instinct MI25 in una VM NVv4 di Azure.](./media/n-series-amd-driver-setup/device-manager.png)

È possibile usare dxdiag per verificare le proprietà di visualizzazione della GPU, inclusa la RAM video. L'esempio seguente illustra una partizione 1/2 della scheda Radeon Instinct MI25 in una macchina virtuale NVv4 di Azure.
<br />
![Screenshot che mostra una partizione 1/2 della scheda Radeon Instinct MI25 in una VM NVv4 di Azure.](./media/n-series-amd-driver-setup/dxdiag-output-new.png)

Se si esegue Windows 10 Build 1903 o versione successiva, dxdiag non visualizzerà alcuna informazione nella scheda 'Display'. Usare quindi l'opzione 'Salva tutte le informazioni' nella parte inferiore e il file di output visualizzerà le informazioni correlate alla GPU AMD MI25.

![Proprietà del driver GPU](./media/n-series-amd-driver-setup/dxdiag-details.png)
