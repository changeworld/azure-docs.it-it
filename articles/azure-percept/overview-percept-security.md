---
title: Panoramica della sicurezza di Azure Percept
description: Scopri di più sulla sicurezza di Azure Percept
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: template-concept
ms.openlocfilehash: 93884fb87f87651054ffff0a04c4910de634a5eb
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567645"
---
# <a name="azure-percept-security-overview"></a>Panoramica della sicurezza di Azure Percept

I dispositivi Azure Percept sono progettati con una radice hardware attendibile. Questa sicurezza incorporata aiuta a proteggere i dati di inferenza e i sensori sensibili alla privacy, come fotocamere e microfoni e Abilita l'autenticazione e l'autorizzazione dei dispositivi per i servizi di Azure Percept Studio.

> [!NOTE]
> Azure Percept DK è concesso in licenza per l'uso solo in ambienti di sviluppo e test.

## <a name="devices"></a>Dispositivi

### <a name="azure-percept-dk"></a>Azure Percept DK

Azure Percept DK include una versione di Trusted Platform Module (TPM) 2,0, che può essere usata per connettere il dispositivo ad Azure Device Provisioning Services (DPS) con maggiore sicurezza. TPM è uno standard ISO a livello di settore, dal Trusted Computing Group. Per ulteriori informazioni sulla specifica TPM 2,0 completa o sulla specifica ISO/IEC 11889, consultare il [sito web Trusted Computing Group](https://trustedcomputinggroup.org/resource/tpm-library-specification/) . Per altre informazioni sul modo in cui DPS è in grado di eseguire il provisioning dei dispositivi in modo sicuro, vedere [servizio Device provisioning in hub Azure Internet-attestazione TPM](../iot-dps/concepts-tpm-attestation.md).

### <a name="azure-percept-system-on-modules-soms"></a>Sistema Percept di Azure-on-Modules (SoMs)

Azure Percept Vision System on Module (SoM) e Azure Percept audio SoM includono entrambi un'unità di microcontroller (MCU) per la protezione dell'accesso ai sensori di intelligenza artificiale incorporati. A ogni avvio, il firmware della MCU esegue l'autenticazione e l'autorizzazione dell'acceleratore di intelligenza artificiale con i servizi di Azure Percept studio usando l'architettura del motore di composizione dell'identificatore di dispositivo (DICE). I dadi funzionano suddividendo l'avvio in livelli e creando i segreti di dispositivo (UDS) univoci per ogni livello e configurazione. Se un codice o una configurazione diversa viene avviata in un punto qualsiasi della catena, i segreti saranno diversi. Per altre informazioni sui dadi, vedere la [specifica dadi Workgroup](https://trustedcomputinggroup.org/work-groups/dice-architectures/). Per la configurazione dell'accesso ad Azure Percept studio e ai servizi richiesti, vedere l'articolo sulla [configurazione dei firewall per Azure PERCEPT dk](concept-security-configuration.md).

I dispositivi Azure Percept usano la radice hardware di trust per proteggere il firmware. La ROM di avvio garantisce l'integrità del firmware tra la ROM e il caricatore del sistema operativo (OS), che a sua volta garantisce l'integrità degli altri componenti software, creando una catena di attendibilità.

## <a name="services"></a>Servizi

### <a name="iot-edge"></a>IoT Edge

Azure Percept DK si connette ad Azure Percept studio con sicurezza aggiuntiva e altri servizi di Azure che usano il protocollo TLS (Transport Layer Security). Azure Percept DK è un dispositivo abilitato per la Azure IoT Edge. IoT Edge Runtime è una raccolta di programmi che trasformano un dispositivo in un dispositivo IoT Edge. Collettivamente, i componenti di runtime IoT Edge consentono IoT Edge ai dispositivi di ricevere il codice per l'esecuzione al perimetro e di comunicare i risultati. Azure Percept DK usa i contenitori Docker per isolare i carichi di lavoro IoT Edge dal sistema operativo host e dalle applicazioni abilitate per Edge. Per ulteriori informazioni sul Framework di sicurezza di Azure IoT Edge, vedere [IOT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

### <a name="device-update-for-iot-hub"></a>Aggiornamento del dispositivo per l'hub Internet

L'aggiornamento dei dispositivi per l'hub Internet offre un aggiornamento più sicuro, scalabile e affidabile che offre sicurezza rinnovabile ai dispositivi Azure Percept. Fornisce controlli di gestione avanzati e la conformità degli aggiornamenti tramite informazioni dettagliate. Azure Percept DK include una soluzione di aggiornamento dei dispositivi pre-integrata che fornisce aggiornamenti resilienti (A/B) dal firmware ai livelli del sistema operativo.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sulle configurazioni del firewall e le raccomandazioni sulla sicurezza](concept-security-configuration.md)

> [!div class="nextstepaction"]
> [Acquistare Azure Percept DK da Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
