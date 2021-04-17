---
title: Sistemi operativi e motori di contenitori supportati - Azure IoT Edge
description: Informazioni sui sistemi operativi in grado di eseguire il daemon e il runtime di Azure IoT Edge, oltre che sui motori di contenitore supportati per i dispositivi di produzione
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/16/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 67532fce2cac0ec9d05b4caa069e63014b813bd8
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576347"
---
# <a name="azure-iot-edge-supported-systems"></a>Sistemi supportati da Azure IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Questo articolo fornisce informazioni dettagliate sui sistemi e sui componenti supportati da IoT Edge ufficialmente o in anteprima.

## <a name="get-support"></a>Supporto

Se si verificano problemi durante l'uso del servizio Azure IoT Edge, è possibile ottenere assistenza in diversi modi. Per assistenza, provare uno dei canali seguenti:

**Segnalazione di bug**: la maggior parte delle attività di sviluppo per il prodotto Azure IoT Edge viene eseguita nel progetto open source di IoT Edge. I bug possono essere segnalati nella [pagina dei problemi](https://github.com/azure/iotedge/issues) del progetto. I bug correlati Azure IoT Edge per Linux in Windows possono essere segnalati nella pagina dei problemi [di iotedge-eflow](https://github.com/azure/iotedge-eflow/issues). Le correzioni si fanno rapidamente strada dai progetti agli aggiornamenti del prodotto.

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
In altre parole, è possibile utilizzare solo i contenitori Linux in Linux e i contenitori Windows in Windows. Quando si usano contenitori Di Windows, sono supportati solo i contenitori isolati del processo, non i contenitori con isolamento Hyper-V.  

IoT Edge per Linux in Windows usa IoT Edge in una macchina virtuale Linux in esecuzione in un host Windows. In questo modo, è possibile eseguire moduli Linux in un dispositivo Windows.
:::moniker-end
<!-- end 1.1 -->

### <a name="tier-1"></a>Livello 1

I sistemi elencati nelle tabelle seguenti sono supportati da Microsoft, disponibile a livello generale o in anteprima pubblica, e vengono testati con ogni nuova versione.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Azure IoT Edge supporta moduli compilati come contenitori Linux o Windows. I contenitori Linux possono essere distribuiti in dispositivi Linux o in dispositivi Windows usando IoT Edge per Linux in Windows. I contenitori Windows possono essere distribuiti solo nei dispositivi Windows.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Azure IoT Edge versione 1.2 supporta solo moduli compilati come contenitori Linux.

Attualmente non esiste alcun modo supportato per eseguire IoT Edge versione 1.2 nei dispositivi Windows. [IoT Edge per Linux in Windows](iot-edge-for-linux-on-windows.md) è il modo consigliato per eseguire IoT Edge nei dispositivi Windows, ma attualmente viene eseguito solo IoT Edge 1.1. Per altre informazioni, vedere la IoT Edge [1.1](?view=iotedge-2018-06&preserve-view=true) di questo articolo.

:::moniker-end
<!-- end 1.2 -->

#### <a name="linux-containers"></a>Contenitori Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
I moduli compilati come contenitori Linux possono essere distribuiti in dispositivi Linux o Windows. Per i dispositivi Linux, IoT Edge runtime viene installato direttamente nel dispositivo host. Per i dispositivi Windows, una macchina virtuale Linux predefinita con il runtime IoT Edge viene eseguita nel dispositivo host.

[IoT Edge per Linux in Windows](iot-edge-for-linux-on-windows.md) è attualmente in anteprima pubblica, ma è il modo consigliato per eseguire IoT Edge nei dispositivi Windows.

| Sistema operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry Pi OS Stretch |  | ![Raspberry Pi OS Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Anteprima pubblica |
| Windows 10 Pro | Anteprima pubblica |  |  |
| Windows 10 Enterprise | Anteprima pubblica |  |  |
| Windows 10 IoT Enterprise | Anteprima pubblica |  |  |
| Windows Server 2019 | Anteprima pubblica |  |  |

Tutti i sistemi operativi Windows devono essere versione 1809 (build 17763) o successiva.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

| Sistema operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry Pi OS Stretch |  | ![Raspberry Pi OS Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Anteprima pubblica |

:::moniker-end
<!-- end 1.2 -->

>[!NOTE]
>Il supporto di Ubuntu Server 16.04 è terminato con il rilascio IoT Edge versione 1.1.

#### <a name="windows-containers"></a>Contenitori Windows

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
>[!IMPORTANT]
>IoT Edge 1.1 LTS è l'ultimo canale di rilascio che supporterà i contenitori di Windows. A partire dalla versione 1.2, i contenitori di Windows non saranno supportati. È consigliabile usare o passare [IoT Edge per Linux in Windows](iot-edge-for-linux-on-windows.md) per eseguire IoT Edge nei dispositivi Windows.

I moduli compilati come contenitori Windows possono essere distribuiti solo nei dispositivi Windows.

| Sistema operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Windows 10 IoT Enterprise | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server 2019  | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server IoT 2019 | ![check1](./media/tutorial-c-module/green-check.png) |  |  |

Tutti i sistemi operativi Windows devono avere la versione 1809 (build 17763). La build specifica di Windows è necessaria per IoT Edge in Windows perché la versione dei contenitori di Windows deve corrispondere esattamente alla versione del dispositivo Host Windows. I contenitori windows usano attualmente solo la build 17763.

>[!NOTE]
>Windows 10 IoT Core il supporto è terminato con la versione IoT Edge 1.1.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
IoT Edge 1.1 LTS è l'ultimo canale di rilascio che supporta i contenitori di Windows. A partire dalla versione 1.2, i contenitori di Windows non sono supportati.

Per informazioni sui sistemi operativi supportati per i contenitori Windows, vedere la [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) di questo articolo.

:::moniker-end
<!-- end 1.2 -->

### <a name="tier-2"></a>Livello 2

I sistemi elencati nella tabella seguente sono considerati compatibili con Azure IoT Edge, ma non sono testati o gestiti attivamente da Microsoft.

| Sistema operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20.04 <sup>1</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspberry Pi OS Buster |  | ![Raspberry Pi OS Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspberry Pi OS Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> I passaggi di installazione di Ubuntu Server 18.04 in Installare o disinstallare [Azure IoT Edge per Linux](how-to-install-iot-edge.md) dovrebbero funzionare senza modifiche in Ubuntu 20.04.

## <a name="releases"></a>Rilasci

Gli asset e le note sulla versione di IoT Edge sono disponibili nella pagina delle [versioni di azure-iotedge](https://github.com/Azure/azure-iotedge/releases). Questa sezione offre informazioni tratte dalle note sulla versione, consentendo di visualizzare più facilmente i componenti di ciascuna versione.

Nella tabella seguente sono elencati i componenti inclusi in ogni versione a partire dalla 1.2.0. I componenti elencati in questa tabella possono essere installati o aggiornati singolarmente e sono compatibili con le versioni precedenti.

| Versione | aziot-edge | edgeHub<br>edgeAgent | aziot-identity-service |
| ------- | ---------- | -------------------- | ---------------------- |
| **1,2** | 1.2.0      | 1.2.0                | 1.2.0                  |

Nella tabella seguente sono elencati i componenti inclusi in ogni versione fino alla versione 1.1 LTS. I componenti elencati in questa tabella possono essere installati o aggiornati singolarmente e sono compatibili con le versioni precedenti.

| Versione | iotedge | edgeHub<br>edgeAgent | libiothsm | Moby |
|--|--|--|--|--|
| **1.1 LTS**<sup>1</sup> | 1.1.0<br>1.1.1<br><br> | 1.1.0<br>1.1.1<br>1.1.2 | 1.1.0<br>1.1.1<br><br> |   |
| **1.0.10** | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br>1.0.10.3<br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 |  |
| **1.0.9** | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 |  |
| **1.0.8** | 1.0.8 | 1.0.8<br>1.0.8.1<br>1.0.8.2<br>1.0.8.3<br>1.0.8.4<br>1.0.8.5 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 3.0.4 (ARMv7hl, CentOS)<br>3.0.5 |
| **1.0.6** | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

<sup>1</sup> IoT Edge 1.1 è il primo canale di rilascio con supporto a lungo termine (LTS). Questa versione non ha introdotto nuove funzionalità, ma riceverà aggiornamenti della sicurezza e correzioni per le regressioni. IoT Edge 1.1 LTS usa .NET Core 3.1 e sarà supportato fino al 3 dicembre 2022 in modo che corrisponda al ciclo di vita delle versioni di .NET Core e [.NET 5.](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)

>[!IMPORTANT]
>Con il rilascio di un canale di supporto a lungo termine, è consigliabile che tutti i clienti attuali che eseguono la versione 1.0.x abilitino i propri dispositivi alla versione 1.1.x per ricevere supporto continuo.

IoT Edge usa Microsoft.Azure.Devices.Client SDK. Per altre informazioni, vedere il [repository GitHub dell'SDK C# di Azure IoT](https://github.com/Azure/azure-iot-sdk-csharp) o il [contenuto di riferimento di Azure SDK per .NET](/dotnet/api/overview/azure/iot/client). L'elenco seguente presenta la versione dell'SDK client su cui viene eseguito il test di ogni versione:

| Versione IoT Edge | Microsoft.Azure.Devices.Client SDK versione |
|------------------|--------------------------------------------|
| 1.2.0            | 1.33.4-NestedEdge
| 1.1 (LTS)        | 1.28.0                                     |
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
