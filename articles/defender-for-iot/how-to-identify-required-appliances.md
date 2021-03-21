---
title: Identificare le appliance necessarie
description: Informazioni su hardware e appliance virtuali per il Defender certificato per i sensori Internet e la console di gestione locale.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/13/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 2ad5bf08542cd98f7acae36827b1a7b284a893b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149300"
---
# <a name="identify-required-appliances"></a>Identificare le appliance necessarie

Questo articolo fornisce informazioni su Certified Defender per appliance di sensori. Defender Forts può essere distribuito in appliance fisiche e virtuali.

Sono incluse le appliance certificate *preconfigurate* , in cui il software è già installato, nonché le appliance certificate non configurate su cui è possibile scaricare e installare il software richiesto.

Questo articolo fornisce anche le specifiche per un appliance della console di gestione locale. La console di gestione locale non è disponibile come appliance preconfigurata.

- Se si vuole acquistare un sensore preconfigurato, esaminare i modelli disponibili nella sezione appliance del [sensore](#sensor-appliances) e quindi procedere con l'acquisto.

- Se si vuole acquistare il proprio Appliance, esaminare i modelli disponibili nella sezione [appliance del sensore](#sensor-appliances) e nella sezione [appliance aggiuntive certificate](#additional-certified-appliances) . Dopo aver acquisito l'appliance, è possibile scaricare e installare il software.

- Se si desidera acquistare la console di gestione locale, esaminare le informazioni nella sezione [Appliance console di gestione locale](#on-premises-management-console-appliance) . Dopo aver acquisito il dispositivo, è possibile scaricare e installare il software.

Una volta completate le attività, è possibile installare il software e configurare la rete.

## <a name="sensor-appliances"></a>Appliance sensori

Defender for Internet delle cose supporta le distribuzioni sia fisiche che virtuali.

### <a name="physical-sensors"></a>Sensori fisici

In questa sezione viene fornita una panoramica dei modelli di sensori fisici disponibili. È possibile acquistare sensori con software preconfigurato o sensori di acquisto che non sono preconfigurati.

| Tipo di distribuzione | Aziendale | Enterprise | Montaggio rack SMB| SMB rinforzato|
|--|--|--|--|--|
| Immagine | :::image type="content" source="media/how-to-prepare-your-network/corporate-hpe-proliant-dl360-v2.png" alt-text="Modello a livello di azienda."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="Modello di livello aziendale."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="Modello a livello SMB."::: | :::image type="content" source="media/how-to-prepare-your-network/office-ruggedized.png" alt-text="Modello a livello di SMB."::: |
| Modello | DL360 ProLiant HPE | DL20 ProLiant HPE | DL20 ProLiant HPE | EL300 HPE |
| Monitoraggio di porte | Fino a 15 RJ45 o 8 OPT | Fino a 8 RJ45 o 6 OPT | 4 RJ45 | Fino a 5 |
| Larghezza di banda massima [1](#anchortext) | 3 GB/sec | 1 GB/sec | 200 MB/sec | 100 MB/sec |
| Numero massimo di dispositivi protetti | 30.000 | 15.000 | 1\.000 | 800 |

Vedere [specifiche di appliance](#appliance-specifications) per i dettagli del fornitore.

Informazioni sui sensori preconfigurati: Microsoft ha collaborato con Arrow per fornire sensori preconfigurati. Per acquistare un sensore preconfigurato, contattare la freccia all'indirizzo seguente: <hardware.sales@arrow.com>

Informazioni sul Bring your own Appliance: esaminare i modelli supportati descritti qui. Dopo aver acquisito il dispositivo, passare a **Defender per**  >  i **sensori di rete**  >  dell'**installazione** ISO per scaricare il software.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="Sensori di rete ISO.":::

<a id="anchortext">1</a> la capacità della larghezza di banda può variare a seconda della distribuzione dei protocolli.

### <a name="virtual-sensors"></a>Sensori virtuali

In questa sezione viene fornita una panoramica dei sensori virtuali disponibili.

| Tipo di distribuzione | Aziendale | Enterprise | SMB |
|--|--|--|--|
| Larghezza di banda massima | 2,5 GB/sec | 800 MB/sec | 160 MB/sec |
| Numero massimo di dispositivi protetti | 30.000 | 10,000 | 2\.500 |

## <a name="on-premises-management-console-appliance"></a>Appliance console di gestione locale

La console di gestione è disponibile come distribuzione virtuale.

| Tipo di distribuzione | Enterprise |
|--|--|
| Tipo di dispositivo | HPE DL20, VM |
| Numero di sensori gestiti | Fino a 300 |

Dopo aver acquisito una console di gestione locale, passare a **Defender per**  >  l'installazione della **console di gestione locale**  >   dell'utente per scaricare l'immagine ISO.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-iso-download-screen.png" alt-text="Console di gestione locale.":::

## <a name="appliance-specifications"></a>Specifiche del dispositivo

Questa sezione descrive le specifiche hardware per le appliance seguenti:

- Distribuzione aziendale: HPE ProLiant DL360

- Distribuzione aziendale: HPE ProLiant DL20

- Distribuzione SMB: HPE ProLiant DL20

- Specifiche dell'appliance virtuale

## <a name="corporate-deployment-hpe-proliant-dl360"></a>Distribuzione aziendale: HPE ProLiant DL360

| Componente | Specifiche tecniche |
|--|--|
| Chassis | server rack 1U |
| Dimensioni | 42,9 x 43,46 x 70,7 (cm)/1.69 "x 17,11" x 27,83 "(in) |
| Peso | Max 16,27 kg (35,86 lb) |
| Processore | Intel Xeon Silver 4215 R 3,2 GHz, cache 11M, 8C/16T, 130 W |
| Chipset | Intel C621 |
| Memoria | 32 GB = 2 x 16 GB 2666MT/s DDR4 ECC UDIMM |
| Archiviazione | 6 x 1,2-TB SAS 12G Enterprise 10.000 SFF (2,5 in) in Hot-Plug disco rigido-RAID 5 |
| Controller di rete | A bordo: 2 x 1-GB Broadcom BCM5720<br>LOM a bordo: iDRAC porta scheda 1-GB Broadcom BCM5720<br><br>Esterno: 1 x Intel Ethernet I350 QP 1-GB Server Adapter, low profile |
| Gestione | HPE iLO Advanced |
| Accesso al dispositivo | Due USB posteriori 3,0<br>Un front-end USB 2,0<br>Un USB interno 3,0 |
| Potenza | 2 x HPE 500 W Flex slot platino collegamento a caldo alogeno basso alimentatore |
| Supporto rack | HPE 1U Gen10 SFF Easy Install Rail Kit |

### <a name="appliance-bom"></a>BOM dispositivo

| PN | Descrizione | Quantità |
|--|--|--|
| P19766-B21 | HPE DL360 Gen10 8SFF NC CTO server | 1 |
| P19766-B21 | Europa-localizzazione multilingue | 1 |
| P24479-L21 | Intel Xeon-S 4215 R FIO kit per DL360 G10 | 1 |
| P24479-B21 | Intel Xeon-S 4215 R Kit per DL360 Gen10 | 1 |
| P00922-B21 | HPE 16-GB 2Rx8 PC4-2933Y-R Smart Kit | 2 |
| 872479-B21 | HPE 1,2-TB SAS 10.000 SFF SC DS HDD | 6 |
| 811546-B21 | HPE 1-GbE 4-p scheda I350 BASE-T | 1 |
| P02377-B21 | Cavo HPE Smart Hybrid Capacity w \_ 145 mm | 1 |
| 804331-B21 | HPE Smart Array P408i-a SR Gen10 controller | 1 |
| 665240-B21 | HPE 1-GbE 4-p FLR-T I350 adapter | 1 |
| 871244-B21 | HPE DL360 Gen10 High Performance fan kit | 1 |
| 865408-B21 | HPE 500-W FS Plat hot plug-in kit di alimentazione | 2 |
| 512485-B21 | Supporto di HPE Oil adv 1-Server License 1 year | 1 |
| 874543-B21 | HPE 1U Gen10 SFF Easy Install Rail Kit | 1 |

## <a name="enterprise-deployment-hpe-proliant-dl20"></a>Distribuzione aziendale: HPE ProLiant DL20

| Componente | Specifiche tecniche |
|--|--|
| Chassis | server rack 1U |
| Dimensioni (altezza x larghezza x profondità) | 4,32 x 43,46 x 38,22 cm/1.70 x 17,11 x 15,05 pollici |
| Peso | 7,9 kg/17.41 lb |
| Processore | Intel Xeon E-2234, 3,6 GHz, 4C/8T, 71 W |
| Chipset | Intel C242 |
| Memoria | 2 x 16 GB Dual rank x8 DDR4-2666 |
| Archiviazione | 3 x 1 TB SATA 6G midline 7,2 K SFF (2,5 in)-RAID 5 con Smart Array P408i-a SR controller |
| Controller di rete | A bordo: 2 x 1 GB <br>Scheda a bordo: iLO porta 1 GB <br>Esterno: 1 x HPE Ethernet 1-GB 4-porta 366FLR adapter |
| Gestione | HPE iLO Advanced |
| Accesso al dispositivo | Front: 1 x USB 3,0, 1 x porta servizio iLO USB <br>Rear: 2 x USB 3,0 <br>Interno: 1 x USB 3,0 |
| Potenza | Alimentatori con doppio collegamento a caldo 500 W |
| Supporto rack | HPE 1U Short attrite Rail Kit |

### <a name="appliance-bom"></a>BOM dispositivo

| PN | Descrizione: fine superiore | Quantità |
|--|--|--|
| P06963-B21 | HPE DL20 Gen10 4SFF CTO server | 1 |
| P06963-B21 | HPE DL20 Gen10 4SFF CTO server | 1 |
| P17104-L21 | HPE DL20 Gen10 E-2234 FIO Kit | 1 |
| 879507-B21 | HPE 16-GB 2Rx8 PC4-2666V-E STND Kit | 2 |
| 655710-B21 | HPE 1 TB SATA 7,2 K SFF SC DS HDD | 3 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM Riser kit | 1 |
| 665240-B21 | HPE Ethernet 1-GB 4-porta 366FLR scheda | 1 |
| 782961-B21 | Batteria Smart Storage HPE 12-W | 1 |
| 869081-B21 | HPE Smart Array P408i-a SR G10, controller LH | 1 |
| 865408-B21 | HPE 500-W FS Plat hot plug-in kit di alimentazione | 2 |
| 512485-B21 | Supporto di HPE Oil adv 1-Server License 1 year | 1 |
| P06722-B21 | HPE DL20 Gen10 RPS Enablement FIO Kit | 1 |
| 775612-B21 | HPE 1U Short attrite Rail Kit | 1 |

## <a name="smb-deployment-hpe-proliant-dl20"></a>Distribuzione SMB: HPE ProLiant DL20

| Componente | Specifiche tecniche |
|--|--|
| Chassis | server rack 1U |
| Dimensioni (altezza x larghezza x profondità) | 4,32 x 43,46 x 38,22 cm/1.70 x 17,11 x 15,05 pollici |
| Peso | 7,88 kg/17.37 lb |
| Processore | Intel Xeon E-2224, 3,4 GHz, 4C, 71 W |
| Chipset | Intel C242 |
| Memoria | 1 x 8 GB Dual rank x8 DDR4-2666 |
| Archiviazione | 2 x 1 TB SATA 6G midline 7,2 K SFF (2,5 in)-RAID 1 con Smart Array P208i-a |
| Controller di rete | A bordo: 2 x 1 GB <br>Scheda a bordo: iLO porta 1 GB <br>Esterno: 1 x HPE Ethernet 1-GB 4-porta 366FLR adapter |
| Gestione | HPE iLO Advanced |
| Accesso al dispositivo | Front: 1 x USB 3,0, 1 x porta servizio iLO USB <br>Rear: 2 x USB 3,0 <br>Interno: 1 x USB 3,0 |
| Potenza | Alimentatore hot plug 290 W |
| Supporto rack | HPE 1U Short attrite Rail Kit |

### <a name="appliance-bom"></a>BOM dispositivo

| PN | Descrizione | Quantità |
|--|--|--|
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO server | 1 |
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO server | 1 |
| P17102-L21 | HPE DL20 Gen10 E-2224 FIO Kit | 1 |
| 879505-B21 | HPE 8-GB 1Rx8 PC4-2666V-E STND Kit | 1 |
| 801882-B21 | HPE 1-TB SATA 7,2 K LFF RW HDD | 2 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM Riser kit | 1 |
| 665240-B21 | HPE Ethernet 1-GB 4-porta 366FLR scheda | 1 |
| 869079-B21 | HPE Smart Array E208i-a SR G10, controller LH | 1 |
| P21649-B21 | HPE DL20 Gen10 Plat 290 W FIO PSU Kit | 1 |
| P06683-B21 | HPE DL20 Gen10 M. 2 SATA/LFF AROC Cable Kit | 1 |
| 512485-B21 | Supporto di HPE Oil adv 1-Server License 1 year | 1 |
| 775612-B21 | HPE 1U Short attrite Rail Kit | 1 |

## <a name="smb-rugged-hpe-edgeline-el300"></a>SMB robusto: HPE EdgeLine EL300

| Componente | Specifiche tecniche |
|--|--|
| Edilizia | Alluminio, non ventola & progettazione a prova di polvere |
| Dimensioni (altezza x larghezza x profondità) | 200.5 mm (7,9 ") Tall, 232mm (9,14") wide by 100mm (3,9 ") Deep |
| Peso | 4,91 KG (10,83 lbs) |
| CPU | Intel Core i7-8650U (1.9 GHz/4-core/15W) |
| Chipset | Hub controller della piattaforma Intel® Q170 |
| Memoria | SODIMM a temperatura DDR4 2133MHz a 8 GB |
| Archiviazione | 128GB 3ME3 Wide temperature mSATA SSD |
| Controller di rete | porte Ethernet 6x Gigabit da Intel® I219 |
| Accesso al dispositivo  | 4 USBs: 2 fronti; 2 posteriori; 1 interno |
| Alimentatore | 250V/10A |
| Montaggio | Kit di montaggio, guida DIN |
| Temperatura operativa | da 0C a + 70C  |
| Umidità | 10% ~ 90%, non condensato |
| Vibrazione | 0,3 GRMS da 10Hz a 300Hz, 15 minuti per asse-DIN Rail   |
| Shock | 10G 10 ms, Half-seno, Three per ogni asse. (Sia positivo & negativo) – guida DIN |

### <a name="appliance-bom"></a>BOM dispositivo
| Prodotto | Descrizione |
|--|--|
| P25828-B21 | Sistema perimetrale convergente HPE EdgeLine EL300 V2 |
| P25828-B21 B19 | Sistema perimetrale convergente HPE EL300 V2 |
| P25833-B21 | Intel Core i7-8650U (1.9 GHz/4-core/15W) FIO Basic Processor kit per HPE EdgeLine EL300 |
| P09176-B21 | HPE EdgeLine 8GB (1x8GB) dual rank x8 DDR4-2666 SODIMM WT CAS-19-19-19 Registered memory FIO Kit |
| P09188-B21 | HPE EdgeLine A 256 GB SATA 6G read intensive M. 2 2242 3yr WTY unità SSD a temperatura larga |
| P04054-B21 | HPE EdgeLine EL300 SFF a M. 2 Enablement Kit |
| P08120-B21 | HPE EdgeLine EL300 12VDC FIO Transfer Board |
| P08641-B21 | HPE EdgeLine EL300 80W 12VDC alimentatore |
| AF564A | HPE C13-SI-32 IL 250V 10Amp 1.83 m |
| P25835-B21 | HPE EL300 v2-scheda del vettore FIO |
| R1P49AAE | HPE EL300 iSM ADV 3yr 24x7 Sup_Upd E-LTU |
| P08018-B21 facoltativo | HPE EdgeLine EL300 low profile quad Kit  |
| P08019-B21 facoltativo | HPE EdgeLine EL300 DIN Rail Mount Kit |
| P08020-B21 facoltativo | HPE EdgeLine EL300 Wall Mount Kit |
| P03456-B21 facoltativo | HPE EdgeLine 1GbE 4-porta TSN FIO daughter card |

## <a name="virtual-appliance-specifications"></a>Specifiche dell'appliance virtuale

### <a name="sensors"></a>Sensori

| Tipo | Aziendale | Enterprise | SMB |
|--|--|--|--|
| vCPU | 32 | 8 | 4 |
| Memoria | 32 GB | 32 GB | 8 GB |
| Archiviazione | 5,6 TB | 1,8 TB | 500 GB |

### <a name="on-premises-management-console-appliance"></a>Appliance console di gestione locale

| Tipo | Enterprise |
|--|--|
| Descrizione | Appliance virtuale per i tipi di distribuzione Enterprise |
| vCPU | 8 |
| Memoria | 32 GB |
| Archiviazione | 1,8 TB |

Hypervisor supportati: VMware ESXi versione 5,0 e successive, Hyper-V

## <a name="additional-certified-appliances"></a>Appliance certificate aggiuntive

Questa sezione illustra le appliance aggiuntive certificate da Microsoft, ma non sono offerte come appliance preconfigurate.

| Tipo di distribuzione | Enterprise |
|--|--|
| Immagine | :::image type="content" source="media/how-to-prepare-your-network/deployment-type-enterprise-for-azure-defender-for-iot-v2.png" alt-text="Tipo di distribuzione Enterprise."::: |
| Modello | Dell PowerEdge R340 supplementare XL |
| Monitoraggio di porte | Fino a nove RJ45 o sei OPT |
| Larghezza di banda massima [1](#anchortext2)| 1 GB/sec |
| Numero massimo di dispositivi protetti | 10,000 |

<a id="anchortext2">Uno</a> La capacità della larghezza di banda può variare a seconda della distribuzione di protocolli.

Dopo aver acquistato il dispositivo, passare a **Defender per** i  >  **sensori di rete** dell'  >  **installazione** ISO per scaricare il software.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="Sensori di rete ISO.":::

## <a name="enterprise-deployment-dell-poweredge-r340-xl"></a>Distribuzione aziendale: Dell PowerEdge R340 supplementare XL

| Componente | Specifiche tecniche |
|--|--|
| Chassis | server rack 1U
| Dimensioni | 42,8 x 434,0 x 596 (mm)/1,67 "x 17,09" x 23,5 "(in) |
| Peso | Massimo 29,98 lb/13,6 kg |
| Processore | Intel Xeon E-2144G 3,6 GHz, 8M Cache, 4C/8T, Turbo (71 W) |
| Chipset | Intel C246 |
| Memoria | 32 GB = 2 x 16 GB 2666MT/s DDR4 ECC UDIMM |
| Archiviazione | 3 x 2-TB 7,2 K RPM SATA 6-Gbps 512N 3,5-in Hot-Plug disco rigido-RAID 5 |
| Controller di rete | A bordo: 2 x 1-GB Broadcom BCM5720<br>LOM a bordo: iDRAC porta scheda 1-GB Broadcom BCM5720 <br><br>Esterno: 1 x Intel Ethernet I350 QP 1-GB Server Adapter, low profile |
| Gestione | iDRAC Nine Enterprise |
| Accesso al dispositivo | Due USB posteriori 3,0 <br> Un front-end USB 3,0 |
| Potenza | Alimentatori con doppio collegamento a caldo 350 W |
| Supporto rack | ReadyRails II guide scorrevoli per le operazioni di montaggio senza strumenti in rack di 4 post con buchi rotondi o senza thread o montaggio con strumenti in quattro post con threading a thread, con supporto per la gestione dei cavi senza strumenti facoltativi. |

## <a name="dell-r340-bom"></a>BOM R340 supplementare dell

:::image type="content" source="media/how-to-prepare-your-network/enterprise-deployment-for-azure-defender-for-iot-dell-r340-bom.png" alt-text="Dell R340 supplementare BOM.":::

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su Azure Defender per l'installazione di Internet](how-to-install-software.md)

[Informazioni sulla configurazione della rete di Azure Defender per IoT](how-to-set-up-your-network.md)

