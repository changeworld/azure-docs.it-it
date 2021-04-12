---
title: Sistemi operativi e motori di contenitori supportati - Azure IoT Edge
description: Informazioni sui sistemi operativi in grado di eseguire il daemon e il runtime di Azure IoT Edge, oltre che sui motori di contenitore supportati per i dispositivi di produzione
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0bd6a8af4850f3a0519bac7644100c2dcf883635
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031171"
---
# <a name="azure-iot-edge-supported-systems"></a>Sistemi supportati da Azure IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Questo articolo fornisce informazioni dettagliate sui sistemi e sui componenti supportati da IoT Edge ufficialmente o in anteprima.

## <a name="get-support"></a>Supporto

Se si verificano problemi durante l'uso del servizio Azure IoT Edge, è possibile ottenere assistenza in diversi modi. Per assistenza, provare uno dei canali seguenti:

**Segnalazione di bug**: la maggior parte delle attività di sviluppo per il prodotto Azure IoT Edge viene eseguita nel progetto open source di IoT Edge. I bug possono essere segnalati nella [pagina dei problemi](https://github.com/azure/iotedge/issues) del progetto. È possibile segnalare i bug correlati a Azure IoT Edge per Linux in Windows nella [pagina relativa ai problemi di iotedge-eFlow](https://github.com/azure/iotedge-eflow/issues). Le correzioni vengono rapidamente riportate dai progetti negli aggiornamenti del prodotto.

**Team di supporto tecnico Microsoft**: gli utenti con un [piano di supporto](https://azure.microsoft.com/support/plans/) possono rivolgersi al team di supporto tecnico Microsoft creando un ticket di supporto dal [portale di Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Richieste di funzionalità**: il prodotto Azure IoT Edge tiene traccia delle richieste di funzionalità tramite la [pagina UserVoice](https://feedback.azure.com/forums/907045-azure-iot-edge) del prodotto.

## <a name="container-engines"></a>Motori di contenitore

I moduli di Azure IoT Edge vengono implementati come contenitori ed è quindi necessario un motore di contenitori in grado di avviarli. Per soddisfare questo requisito, Microsoft fornisce un motore di contenitore, moby-engine, basato sul progetto open source Moby. Docker CE e Docker EE sono altri motori di contenitore comuni, sempre basati sul progetto open source Moby e compatibili con Azure IoT Edge. Microsoft offre il miglior supporto possibile per i sistemi che usano i motori di contenitori, ma non può fornire correzioni per eventuali problemi. Per questo motivo, Microsoft consiglia di usare il motore moby nei sistemi di produzione.

<br>
<center>

![Motore Moby come runtime di contenitori](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Sistemi operativi

Azure IoT Edge viene eseguito nella maggior parte dei sistemi operativi che possono eseguire contenitori. Non tutti questi sistemi, tuttavia, sono ugualmente supportati. I sistemi operativi sono raggruppati in livelli che rappresentano il grado di supporto previsto per gli utenti.

* I sistemi di livello 1 sono supportati. Per i sistemi di livello 1, Microsoft:
  * Include questo sistema operativo in test automatizzati
  * Fornisce i pacchetti di installazione
* I sistemi di livello 2 sono compatibili con Azure IoT Edge e possono essere usati in modo abbastanza semplice. Per i sistemi di livello 2:
  * Microsoft ha eseguito test informali sulle piattaforme o è a conoscenza di un partner che esegue correttamente Azure IoT Edge nella piattaforma
  * I pacchetti di installazione per le altre piattaforme possono funzionare in queste piattaforme

La famiglia del sistema operativo host deve sempre corrispondere alla famiglia del sistema operativo guest usato all'interno del contenitore di un modulo.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
In altre parole, è possibile utilizzare solo i contenitori Linux in Linux e i contenitori Windows in Windows. Quando si usano i contenitori di Windows, sono supportati solo i contenitori isolati di processo, non i contenitori isolati di Hyper-V.  

IoT Edge per Linux in Windows usa IoT Edge in una macchina virtuale Linux in esecuzione in un host Windows. In questo modo, è possibile eseguire moduli Linux in un dispositivo Windows.
:::moniker-end
<!-- end 1.1 -->

### <a name="tier-1"></a>Livello 1

I sistemi elencati nelle tabelle seguenti sono supportati da Microsoft, disponibile a livello generale o in anteprima pubblica, e sono testati a ogni nuova versione.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Azure IoT Edge supporta i moduli compilati come contenitori Linux o Windows. I contenitori Linux possono essere distribuiti nei dispositivi Linux o distribuiti nei dispositivi Windows usando IoT Edge per Linux in Windows. I contenitori di Windows possono essere distribuiti solo nei dispositivi Windows.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Azure IoT Edge versione 1,2 supporta solo moduli compilati come contenitori Linux.

Attualmente non esiste alcun modo supportato per eseguire IoT Edge versione 1,2 nei dispositivi Windows. [IOT Edge per Linux in Windows](iot-edge-for-linux-on-windows.md) è il modo consigliato per eseguire IOT Edge sui dispositivi Windows, ma attualmente viene eseguito solo IOT Edge 1,1. Per altre informazioni, vedere la versione [IoT Edge 1,1](?view=iotedge-2018-06&preserve-view=true) di questo articolo.

:::moniker-end
<!-- end 1.2 -->

#### <a name="linux-containers"></a>Contenitori Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
I moduli compilati come contenitori Linux possono essere distribuiti in dispositivi Linux o Windows. Per i dispositivi Linux, il runtime di IoT Edge viene installato direttamente nel dispositivo host. Per i dispositivi Windows, una macchina virtuale Linux precompilata con il IoT Edge Runtime viene eseguita sul dispositivo host.

[IOT Edge per Linux in Windows](iot-edge-for-linux-on-windows.md) è attualmente disponibile in anteprima pubblica, ma è il modo consigliato per eseguire IOT Edge sui dispositivi Windows.

| Sistema operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Estensione del sistema operativo Raspberry Pi |  | ![Estensione del sistema operativo Raspberry Pi + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Anteprima pubblica |
| Windows 10 Pro | Anteprima pubblica |  |  |
| Windows 10 Enterprise | Anteprima pubblica |  |  |
| Windows 10 IoT Enterprise | Anteprima pubblica |  |  |
| Windows Server 2019 | Anteprima pubblica |  |  |

Tutti i sistemi operativi Windows devono essere della versione 1809 (Build 17763) o versioni successive.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

| Sistema operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Estensione del sistema operativo Raspberry Pi |  | ![Estensione del sistema operativo Raspberry Pi + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Anteprima pubblica |

:::moniker-end
<!-- end 1.2 -->

>[!NOTE]
>Il supporto per Ubuntu Server 16,04 è terminato con il rilascio di IoT Edge versione 1,1.

#### <a name="windows-containers"></a>Contenitori Windows

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
>[!IMPORTANT]
>IoT Edge 1,1 LTS è il canale dell'ultimo rilascio che supporterà i contenitori di Windows. A partire dalla versione 1,2, i contenitori di Windows non saranno supportati. Provare a usare o a passare a [IOT Edge per Linux in Windows](iot-edge-for-linux-on-windows.md) per eseguire IOT Edge nei dispositivi Windows.

I moduli compilati come contenitori di Windows possono essere distribuiti solo nei dispositivi Windows.

| Sistema operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Windows 10 IoT Enterprise | ![Check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server 2019  | ![Check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server IoT 2019 | ![Check1](./media/tutorial-c-module/green-check.png) |  |  |

Tutti i sistemi operativi Windows devono essere della versione 1809 (Build 17763). La build specifica di Windows è necessaria per IoT Edge in Windows perché la versione dei contenitori di Windows deve corrispondere esattamente alla versione del dispositivo Windows host. I contenitori di Windows attualmente usano solo la build 17763.

>[!NOTE]
>Il supporto principale di Windows 10 è terminato con il rilascio della versione di IoT Edge 1,1.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
IoT Edge 1,1 LTS è l'ultimo canale di rilascio che supporta i contenitori di Windows. A partire dalla versione 1,2, i contenitori di Windows non sono supportati.

Per informazioni sui sistemi operativi supportati per i contenitori Windows, vedere la versione [IoT Edge 1,1](?view=iotedge-2018-06&preserve-view=true) di questo articolo.

:::moniker-end
<!-- end 1.2 -->

### <a name="tier-2"></a>Livello 2

I sistemi elencati nella tabella seguente sono considerati compatibili con Azure IoT Edge, ma non sono testati o gestiti attivamente da Microsoft.

| Sistema operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20,04 <sup>1</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Sistema operativo Raspberry Pi Buster |  | ![Sistema operativo Raspberry Pi Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Sistema operativo Raspberry Pi Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> i passaggi per l'installazione di ubuntu server 18,04 in [installare o disinstallare Azure IOT Edge per Linux](how-to-install-iot-edge.md) dovrebbero funzionare senza alcuna modifica in Ubuntu 20,04.

## <a name="releases"></a>Rilasci

Gli asset e le note sulla versione di IoT Edge sono disponibili nella pagina delle [versioni di azure-iotedge](https://github.com/Azure/azure-iotedge/releases). Questa sezione offre informazioni tratte dalle note sulla versione, consentendo di visualizzare più facilmente i componenti di ciascuna versione.

I componenti di IoT Edge possono essere installati o aggiornati singolarmente e sono compatibili con i componenti di versioni precedenti. La tabella seguente elenca i componenti inclusi in ogni versione:

| Versione | Daemon di sicurezza | Hub di Edge<br>Agente IoT Edge | Libiothsm | Moby |
|--|--|--|--|--|
| **1,1 LTS**<sup>1</sup> | 1.1.0<br>1.1.1 | 1.1.0<br>1.1.1 | 1.1.0<br>1.1.1 |   |
| **1.0.10** | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br>1.0.10.3<br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 |  |
| **1.0.9** | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 |  |
| **1.0.8** | 1.0.8 | 1.0.8<br>1.0.8.1<br>1.0.8.2<br>1.0.8.3<br>1.0.8.4<br>1.0.8.5 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 3.0.4 (ARMv7hl, CentOS)<br>3.0.5 |
| **1.0.6** | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

<sup>1</sup> IoT Edge 1,1 è il primo canale di rilascio per il supporto a lungo termine (LTS). Questa versione non ha introdotto nuove funzionalità, ma riceverà gli aggiornamenti della sicurezza e le correzioni alle regressioni. IoT Edge 1,1 LTS USA .NET Core 3,1 e sarà supportato fino al 3 dicembre 2022 per la corrispondenza con il ciclo di vita della [versione di .NET Core e .NET 5](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

>[!IMPORTANT]
>Con il rilascio di un canale di supporto a lungo termine, è consigliabile che tutti i clienti correnti che eseguono 1.0. x aggiornino i dispositivi a 1.1. x per ricevere il supporto continuo.

IoT Edge USA Microsoft. Azure. Devices. Client SDK. Per altre informazioni, vedere il [repository GitHub dell'SDK C# di Azure IoT](https://github.com/Azure/azure-iot-sdk-csharp) o il [contenuto di riferimento di Azure SDK per .NET](/dotnet/api/overview/azure/iot/client). L'elenco seguente presenta la versione dell'SDK client su cui viene eseguito il test di ogni versione:

| Versione IoT Edge | Versione di Microsoft. Azure. Devices. Client SDK |
|------------------|--------------------------------------------|
| 1,1 (LTS)      | 1.28.0                                     |
| 1.0.10           | 1.28.0                                     |
| 1.0.9            | 1.21.1                                     |
| 1.0.8            | 1.20.3                                     |
| 1.0.7            | 1.20.1                                     |
| 1.0.6            | 1.17.1                                     |
| 1.0.5            | 1.17.1                                     |

## <a name="virtual-machines"></a>Macchine virtuali

Azure IoT Edge può essere eseguito nelle macchine virtuali. L'uso di una macchina virtuale come dispositivo IoT Edge è comune quando i clienti vogliono ampliare l'infrastruttura esistente con una rete perimetrale intelligente. La famiglia del sistema operativo host della VM deve corrispondere alla famiglia del sistema operativo guest usato all'interno del contenitore di un modulo. Questo requisito è lo stesso di quando Azure IoT Edge viene eseguito direttamente in un dispositivo. Azure IoT Edge è indipendente dalla tecnologia di virtualizzazione sottostante e funziona in macchine virtuali basate su piattaforme, ad esempio Hyper-V e vSphere.

<br>

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

<center>

![Azure IoT Edge in una macchina virtuale](./media/support/edge-on-vm-with-windows.png)

</center>

::: moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

<center>

![Azure IoT Edge in una macchina virtuale](./media/support/edge-on-vm.png)

</center>

:::moniker-end

## <a name="minimum-system-requirements"></a>Requisiti minimi di sistema

Azure IoT Edge funziona correttamente nei dispositivi con le dimensioni di un dispositivo Raspberry Pi3 per hardware a livello server. La scelta dell'hardware appropriato per lo scenario dipende dai carichi di lavoro che si vogliono eseguire. Prendere la decisione per il dispositivo finale può essere complicato; tuttavia, è possibile avviare facilmente una soluzione di creazione di prototipi su desktop o portatili tradizionali.

L'esperienza sarà utile per selezione il dispositivo finale durante la creazione di prototipi. Di seguito sono riportati alcuni aspetti da considerare:

* Quanti moduli sono presenti nel carico di lavoro?
* Quanti livelli condividono i contenitori dei moduli ?
* In quale lingua sono scritti i moduli?
* Quanti dati verranno elaborati dai moduli?
* I moduli necessitano di hardware specializzato per accelerare i carichi di lavoro?
* Quali sono le caratteristiche di prestazione desiderate dalla soluzione?
* Qual è il budget a disposizione per l'hardware?
