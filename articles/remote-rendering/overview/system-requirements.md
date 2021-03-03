---
title: Requisiti di sistema
description: Elenca i requisiti di sistema per il rendering remoto di Azure
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.custom: references_regions
ms.openlocfilehash: dd91622344263dc366a76c913ce0be95718550cd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705161"
---
# <a name="system-requirements"></a>Requisiti di sistema

Questo capitolo elenca i requisiti di sistema minimi per l'uso di *Azure Remote rendering* (arr).

## <a name="development-pc"></a>COMPUTER di sviluppo

* Windows 10 versione 1903 o successiva.
* Driver grafici aggiornati.
* Facoltativo: [decodificatore video hardware H265](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7)se si vuole usare l'anteprima locale del contenuto sottoposto a rendering remoto, ad esempio in Unity.

> [!IMPORTANT]
> Windows Update non fornisce sempre i driver GPU più recenti, controllare il sito Web del produttore della GPU per i driver più recenti:
>
> * [Driver AMD](https://www.amd.com/en/support)
> * [Driver Intel](https://www.intel.com/content/www/us/en/support/detect.html)
> * [Driver NVIDIA](https://www.nvidia.com/Download/index.aspx)

La tabella seguente elenca le GPU che supportano la decodifica video hardware H265.

| Produttore GPU | Modelli supportati |
|-----------|:-----------|
| NVIDIA | Controllare la **matrice di supporto NVDEC** [nella parte inferiore della pagina](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix). Per la GPU è necessario un Sì nella colonna **H. 265 a 4:2:0 8 bit** . |
| AMD | GPU con almeno la versione 6 del [decodificatore video unificato](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6)di AMD. |
| Intel | Skylake e CPU più recenti |

Anche se è possibile che venga installato il codec H265 corretto, le proprietà di sicurezza nelle DLL dei codec possono causare errori di inizializzazione del codec. Nella [Guida alla risoluzione dei problemi](../resources/troubleshoot.md#h265-codec-not-available) vengono descritti i passaggi per risolvere il problema. Il problema della DLL può verificarsi solo quando si usa il servizio in un'applicazione desktop, ad esempio in Unity.

## <a name="devices"></a>Dispositivi

Il rendering remoto di Azure attualmente supporta solo **HoloLens 2** e Windows desktop come dispositivo di destinazione. Vedere la sezione [limitazioni della piattaforma](../reference/limits.md#platform-limitations) .

È importante usare il codec HEVC più recente, in quanto le versioni più recenti presentano miglioramenti significativi della latenza. Per verificare quale versione è installata nel dispositivo:

1. Avviare il **Microsoft Store**.
1. Fare clic sul pulsante **"..."** in alto a destra.
1. Selezionare **download e aggiornamenti**.
1. Cercare nell'elenco le **estensioni video di HEVC dal produttore del dispositivo**. Se questo elemento non è elencato in aggiornamenti, la versione più recente è già installata.
1. Verificare che il codec elencato includa almeno la versione **1.0.21821.0**.
1. Fai clic sul pulsante **Ottieni aggiornamenti** e attendi l'installazione.

## <a name="network"></a>Rete

Una connessione di rete stabile e a bassa latenza è essenziale per un'esperienza utente ottimale.

Vedere il capitolo dedicato per i [requisiti di rete](../reference/network-requirements.md).

Per la risoluzione dei problemi di rete, vedere la [Guida alla risoluzione dei](../resources/troubleshoot.md#unstable-holograms)problemi.

### <a name="network-firewall"></a>Firewall di rete

### <a name="sdk-version--0176"></a>Versione SDK >= 0.1.76

Le macchine virtuali per il rendering remoto usano indirizzi IP condivisi dagli intervalli IP seguenti:

| Nome             | Region         | Prefisso IP         |
|------------------|:---------------|:------------------|
| Australia orientale   | australiaeast  | 20.53.44.240/28   |
| Stati Uniti orientali          | eastus         | 20.62.129.224/28  |
| Stati Uniti orientali 2        | eastus2        | 20.49.103.240/28  |
| Giappone orientale       | japaneast      | 20.191.165.112/28 |
| Europa settentrionale     | northeurope    | 52.146.133.64/28  |
| Stati Uniti centro-meridionali | southcentralus | 20.65.132.80/28   |
| Asia sud-orientale   | southeastasia  | 20.195.64.224/28  |
| Regno Unito meridionale         | uksouth        | 51.143.209.144/28 |
| Europa occidentale      | westeurope     | 20.61.99.112/28   |
| West US 2        | westus2        | 20.51.9.64/28     |

Assicurarsi che i firewall (sul dispositivo, all'interno dei router e così via) non blocchino questi intervalli IP e gli intervalli di porte seguenti:

| Porta              | Protocollo  | Allow    |
|-------------------|---------- |----------|
| 49152-65534       | TCP/UDP | In uscita |

#### <a name="sdk-version--0176"></a>Versione SDK < 0.1.76

Assicurarsi che i firewall (sul dispositivo, nei router e così via) non blocchino le porte seguenti:

| Porta              | Protocollo | Allow    | Descrizione |
|-------------------|----------|----------|-------------|
| 50051             | TCP      | In uscita | Connessione iniziale (handshake HTTP) |
| 8266              | UDP      | In uscita | Trasferimento dati |
| 5000, 5433, 8443  | TCP      | In uscita | Obbligatorio per [lo strumento ArrInspector](../resources/tools/arr-inspector.md)|


## <a name="software"></a>Software

È necessario installare il software seguente:

* La versione più recente di **Visual Studio 2019** [(download)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Visual Studio Tools per Realtà mista](/windows/mixed-reality/install-the-tools). In particolare, le installazioni del *carico di lavoro* seguente sono obbligatorie:
  * **Sviluppo per desktop con C++**
  * **Sviluppo per la piattaforma UWP (Universal Windows Platform)**
* **Windows SDK 10.0.18362.0** [(download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **Git** [(download)](https://git-scm.com/downloads)
* Facoltativo: per visualizzare il flusso video dal server su un PC desktop, è necessario **HEVC video Extensions** [(collegamento Microsoft Store)](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7). Verificare che la versione più recente sia installata controllando la disponibilità di aggiornamenti nell'archivio.

## <a name="unity"></a>Unity

Per lo sviluppo con Unity, installare

* Unity 2019.3.1 [(download)](https://unity3d.com/get-unity/download)
* Installare questi moduli in Unity:
  * **UWP** - Supporto per la compilazione della piattaforma UWP (Universal Windows Platform)
  * **IL2CPP** - Supporto per la compilazione di Windows (IL2CPP)

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Eseguire il rendering di un modello con Unity](../quickstarts/render-model.md)