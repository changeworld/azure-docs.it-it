---
title: Serie NP - Macchine virtuali di Azure
description: Specifiche per le macchine virtuali serie NP.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: 61488b88b00206cb78beed4fe773bf9377848701
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861050"
---
# <a name="np-series"></a>Serie NP 
Le macchine virtuali serie NP sono basate su FPGA [Xilinx U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) per accelerare i carichi di lavoro, tra cui l'inferenza di Machine Learning, la transcodizzazione video e la ricerca di database & analytics. Le macchine virtuali serie NP sono anche basate su CPU Intel Xeon 8171M (Skylake) con tutta la velocità di clock turbo core di 3,2 GHz.

[Archiviazione Premium:](premium-storage-performance.md)supportato<br>
[Archiviazione Premium memorizzazione nella cache:](premium-storage-performance.md)supportata<br>
[Live Migration:](maintenance-and-updates.md)Non supportato<br>
[Aggiornamenti con mantenimento della memoria:](maintenance-and-updates.md)non supportati<br>
Supporto per la generazione di macchine virtuali: generazione 1<br>
[Rete accelerata:](../virtual-network/create-vm-accelerated-networking-cli.md)supportata<br>
[Dischi del sistema operativo phemeral:](ephemeral-os-disks.md)non supportati <br>
<br>

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | FPGA | Memoria FPGA: GiB | Numero massimo di dischi dati | Schede di interfaccia di rete max/larghezza di banda di rete prevista (MBps) | 
|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


##  <a name="frequently-asked-questions"></a>Domande frequenti

**D:** Quale versione di Vitis è consigliabile usare? 

**A:** Xilinx consiglia [Vitis 2020.2](https://www.xilinx.com/products/design-tools/vitis/vitis-platform.html)


**D:** È necessario usare macchine virtuali NP per sviluppare la soluzione? 

**A:** No, è possibile sviluppare in locale e distribuire nel cloud. Assicurarsi di seguire la documentazione dell'attestazione per la distribuzione nelle macchine virtuali NP. 

**D:** Quale versione di XRT è consigliabile usare?

**A:** xrt_202020.2.8.832 

**D:** Qual è la piattaforma di distribuzione di destinazione?

**A:** Usare le piattaforme seguenti.
- xilinx-u250-gen3x16-xdma-platform-2.1-3_all
- xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1 

**D:** Quale piattaforma è necessario specificare come destinazione per lo sviluppo?

**A:** xilinx-u250-gen3x16-xdma-2.1-202010-1-dev_1-2954688_all 

**D:** Quali sono i sistemi operativi supportati? 

**A:** Xilinx e Microsoft hanno convalidato Ubuntu 18.04 LTS e CentOS 7.8.

 Xilinx ha creato le immagini del marketplace seguenti per semplificare la distribuzione di queste macchine virtuali. 

[Macchina virtuale di distribuzione Xilinx Alveo U250 - Ubuntu18.04](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_ubuntu1804_032321)

[Macchina virtuale di distribuzione Xilinx Alveo U250 - CentOS7.8](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_centos78_032321)

**D:** È possibile distribuire le proprie macchine virtuali Ubuntu/CentOS e installare XRT/Deployment Target Platform? 

**R:** Sì.

**D:** Se si distribuisce una macchina virtuale Ubuntu18.04, quali sono i pacchetti e i passaggi necessari?

**A:** Usare la documentazione di Kernel 4.1X per [Xilinx XRT](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf)
       
Installare i pacchetti seguenti.
- xrt_202020.2.8.832_18.04-amd64-xrt.deb
       
- xrt_202020.2.8.832_18.04-amd64-azure.deb
       
- xilinx-u250-gen3x16-xdma-platform-2.1-3_all_18.04.deb.tar.gz
       
- xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1_all.deb  

**D:** In Ubuntu, dopo il riavvio della macchina virtuale non è possibile trovare i FPGA: 

**A:** Verificare che il kernel non sia stato aggiornato (uname -a). In tal caso, eseguire il downgrade al kernel 4.1X. 

**D:** Se si distribuisce la propria macchina virtuale CentOS7.8, quali sono i pacchetti e i passaggi necessari?

**A:** Usare la versione kernel: 3.10.0-1160.15.2.el7.x86_64

 Installare i pacchetti seguenti.
   
 - xrt_202020.2.8.832_7.4.1708-x86_64-xrt.rpm 
      
 - xrt_202020.2.8.832_7.4.1708-x86_64-azure.rpm 
     
 - xilinx-u250-gen3x16-xdma-platform-2.1-3.noarch.rpm.tar.gz 
      
 - xilinx-u250-gen3x16-xdma-validate-2.1-3005608.1.noarch.rpm  

**D:** Quando si esegue xbutil validate in CentOS, viene visualizzato l'avviso "AVVISO: Kernel version 3.10.0-1160.15.2.el7.x86_64 is not officially supported. 4.18.0-193 è la versione supportata più recente." 

**A:** Questo può essere tranquillamente ignorato. 

**D:** Quali sono le differenze tra macchine virtuali onprem e NP per quanto riguarda XRT? 

**A:** In Azure la piattaforma XDMA 2.1 supporta solo Host_Mem(SB) e di conservazione dei dati DDR. 

Per abilitare Host_Mem(SB) (1 Gb di RAM): sudo xbutil host_mem --enable --size 1g 

Per disabilitare Host_Mem(SB): sudo xbutil host_mem --disable 

**D:** È possibile eseguire comandi xbmgmt? 

**A:** No, nelle macchine virtuali di Azure non è disponibile alcun supporto di gestione direttamente dalla macchina virtuale di Azure. 

 **D:** È necessario caricare un PLP? 

**A:** No, il PLP viene caricato automaticamente, quindi non è necessario caricarsi tramite i comandi xbmgmt. 

 
**D:** Sono supporto tecnico di Azure PLP diversi? 

**A:** Non al momento. È supportata solo la PLP fornita nei pacchetti della piattaforma di distribuzione. 

**D:** Come è possibile eseguire query sulle informazioni PLP? 

**A:** È necessario eseguire la query xbutil ed esaminare la parte inferiore. 

**D:** Se si crea una macchina virtuale e si distribuisce XRT manualmente, quali modifiche aggiuntive è necessario apportare? 

**A:** In /opt/xilinx/xrt/setup.sh aggiungere una voce per XRT_INI_PATH che punta a /opt/xilinx/xrt/xrt.ini

 
Il contenuto di /opt/xilinx/xrt/xrt.ini deve contenere: <br>
[Runtime]<br>
ert=false <br>

## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
