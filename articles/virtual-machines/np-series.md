---
title: Serie NP-macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie NP.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: aa67a858d0396badc25a625b23dc2f2fdf1bdff9
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551374"
---
# <a name="np-series"></a>Serie NP 
Le macchine virtuali serie NP sono basate su [Xilinx U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) FPGA per accelerare i carichi di lavoro, tra cui l'inferenza di Machine Learning, la transcodifica video e la ricerca di database & Analytics. Le macchine virtuali della serie NP sono basate anche su CPU Intel Xeon 8171M (Skylake) con tutta la velocità di clock di base Turbo 3,2 GHz.

[Archiviazione Premium](premium-storage-performance.md): supportata<br>
[Caching archiviazione Premium](premium-storage-performance.md): supportato<br>
[Live Migration](maintenance-and-updates.md): non supportato<br>
[Aggiornamenti con mantenimento della memoria](maintenance-and-updates.md): non supportato<br>
Supporto per la generazione di VM: generazione 1<br>
[Rete accelerata](../virtual-network/create-vm-accelerated-networking-cli.md): supportata<br>
[Dischi del sistema operativo temporaneo](ephemeral-os-disks.md): non supportati <br>
<br>

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | FPGA | Memoria FPGA: GiB | Numero massimo di dischi dati | NIC massimo/larghezza di banda di rete prevista (MBps) | 
|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


##  <a name="frequently-asked-questions"></a>Domande frequenti

**D:** Quale versione di Vitis è necessario usare? 

**R:** Xilinx consiglia [Vitis 2020,2](https://www.xilinx.com/products/design-tools/vitis/vitis-platform.html)


**D:** È necessario usare le macchine virtuali NP per sviluppare la soluzione? 

**R:** No, puoi sviluppare in locale e distribuire nel cloud. Assicurarsi di seguire la documentazione di attestazione per la distribuzione in macchine virtuali NP. 

**D:** Quale versione di XRT è necessario usare?

**R:** xrt_202020.2.8.832 

**D:** Qual è la piattaforma di distribuzione di destinazione?

**R:** Usare le piattaforme seguenti.
- Xilinx-U250-gen3x16-xDMa-Platform-2.1-3_all
- Xilinx-U250-gen3x16-xDMa-validate_2.1-3005608.1 

**D:** Quale piattaforma è necessario definire come destinazione per lo sviluppo?

**R:** Xilinx-U250-gen3x16-xDMa-2.1-202010-1-dev_1-2954688_all 

**D:** Quali sono i sistemi operativi supportati? 

**R:** Xilinx e Microsoft hanno convalidato Ubuntu 18,04 LTS e CentOS 7,8.

 Xilinx ha creato le immagini del Marketplace seguenti per semplificare la distribuzione di queste macchine virtuali. 

[Xilinx alveo-VM di distribuzione U250-Ubuntu 18.04](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_ubuntu1804_032321)

[Xilinx alveo-VM di distribuzione U250-CentOS 7,8](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_centos78_032321)

**D:** È possibile distribuire macchine virtuali Ubuntu/CentOS personalizzate e installare XRT/piattaforma di destinazione della distribuzione? 

**R:** Sì.

**D:** Se si distribuisce la macchina virtuale Ubuntu 18.04, quali sono i pacchetti e i passaggi necessari?

**R:** Usare il kernel 4.1 X per la [documentazione di Xilinx XRT](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf)
       
Installare i pacchetti seguenti.
- xrt_202020 xrt_202020.2.8.832_18.04-amd64-XRT. deb
       
- xrt_202020 xrt_202020.2.8.832_18.04-amd64-Azure. deb
       
- Xilinx-U250-gen3x16-xDMa-Platform-2.1-3_all_18.04. deb. tar. gz
       
- Xilinx-U250-gen3x16-xDMa-validate_2.1-3005608.1_all. deb  

**D:** In Ubuntu, dopo il riavvio della macchina virtuale, non è possibile trovare gli FPGA seguenti: 

**R:** Verificare che il kernel non sia stato aggiornato (uname-a). In tal caso, effettuare il downgrade al kernel 4.1 X. 

**D:** Se si distribuisce una macchina virtuale CentOS 7.8, quali sono i pacchetti e i passaggi necessari?

**R:** USA versione kernel: 3.10.0-1160.15.2.el7.x86_64

 Installare i pacchetti seguenti.
   
 - xrt_202020 xrt_202020.2.8.832_7.4.1708-x86_64-XRT. rpm 
      
 - xrt_202020 xrt_202020.2.8.832_7.4.1708-x86_64-Azure. rpm 
     
 - Xilinx-U250-gen3x16-xDMa-Platform-2.1 3. noarch. rpm. tar. gz 
      
 - Xilinx-U250-gen3x16-xDMa-Validate-2.1-3005608.1. noarch. rpm  

**D:** Quando si esegue xbutil Validate on CentOS, viene visualizzato il messaggio di avviso seguente: "avviso: la versione del kernel 3.10.0-1160.15.2.el7.x86_64 non è ufficialmente supportata. 4.18.0-193 è la versione supportata più recente ". 

**R:** Questo può essere tranquillamente ignorato. 

**D:** Quali sono le differenze tra le VM OnPrem e NP relative a XRT? 

**R:** In Azure la piattaforma XDMA 2,1 supporta solo le funzionalità di conservazione dei dati Host_Mem (SB) e DDR. 

Per abilitare Host_Mem (SB) (1Gb di RAM): sudo xbutil host_mem--Enable--size 1g 

Per disabilitare Host_Mem (SB): sudo xbutil host_mem--Disable 

**D:** È possibile eseguire I comandi xbmgmt? 

**R:** No, nelle macchine virtuali di Azure non è disponibile alcun supporto per la gestione direttamente dalla macchina virtuale di Azure. 

 **D:** È necessario caricare una PLP? 

**R:** No, il PLP viene caricato automaticamente, quindi non è necessario caricare tramite i comandi xbmgmt. 

 
**D:** Azure supporta PLPs diversi? 

**R:** Non in questo momento. Sono supportati solo i PLP forniti nei pacchetti della piattaforma di distribuzione. 

**D:** Come è possibile eseguire una query sulle informazioni PLP? 

**R:** È necessario eseguire la query xbutil ed esaminare la parte inferiore. 

## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
