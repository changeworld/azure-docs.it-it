---
title: Panoramica della sicurezza di Azure Percept
description: Scopri di più sulla sicurezza di Azure Percept
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: b5a345139114842c83cb1f11792076efb1461870
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663717"
---
# <a name="azure-percept-security-overview"></a>Panoramica della sicurezza di Azure Percept

I dispositivi Azure Percept DK sono progettati con una radice hardware di attendibilità, ovvero la sicurezza incorporata in ogni dispositivo. Consente di proteggere sensori sensibili alla privacy, ad esempio fotocamere e microfoni, dati di inferenza e Abilita l'autenticazione e l'autorizzazione dei dispositivi per i servizi di Azure Percept Studio.

> [!NOTE]
> Azure Percept DK è progettato per l'uso in ambienti di sviluppo e test e per scenari di prova.

## <a name="devices"></a>Dispositivi

### <a name="azure-percept-dk"></a>Azure Percept DK

Azure Percept DK include una versione di Trusted Platform Module (TPM) 2,0 che può essere usata per connettere il dispositivo in modo sicuro ai servizi Device provisioning di Azure. TPM è uno standard ISO a livello di settore dalla Trusted Computing Group. per altre informazioni su TPM, vedere la specifica [tpm 2,0 completa](https://trustedcomputinggroup.org/resource/tpm-library-specification/) o la specifica ISO/IEC 11889. Per altre informazioni sul modo in cui DPS è in grado di eseguire il provisioning dei dispositivi in modo sicuro, vedere [servizio Device provisioning in hub Azure Internet-attestazione TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation).

### <a name="azure-percept-system-on-module-som"></a>Sistema Percept di Azure sul modulo (SOM)

Il sistema con visione abilitata per Azure Percept DK sul modulo (SOM) e l'accessorio audio di Azure Percept SOM includono entrambi un'unità di microcontroller (MCU) per la protezione dell'accesso ai sensori incorporati di intelligenza artificiale. A ogni avvio, il firmware della MCU esegue l'autenticazione e l'autorizzazione dell'acceleratore di intelligenza artificiale con i servizi di Azure Percept studio usando l'architettura del motore di composizione dell'identificatore di dispositivo (DICE). I dadi funzionano suddividendo l'avvio in livelli e creando segreti univoci per ogni livello e configurazione in base a un segreto del dispositivo univoco (UDS). Se viene avviato un codice o una configurazione diversa, in qualsiasi punto della catena, i segreti saranno diversi. Per altre informazioni sui dadi, vedere la [specifica dadi Workgroup](https://trustedcomputinggroup.org/work-groups/dice-architectures/). Per la configurazione dell'accesso ad Azure Percept studio e ai servizi richiesti, vedere la sezione relativa alla **configurazione dei firewall per Azure PERCEPT dk** di seguito.

I dispositivi Azure Percept usano il trust radice hardware per proteggere il firmware. La ROM di avvio garantisce l'integrità del firmware tra la ROM e il caricatore del sistema operativo, che a sua volta garantisce l'integrità degli altri componenti software creando una catena di trust.

## <a name="services"></a>Servizi

### <a name="iot-edge"></a>IoT Edge

Azure Percept DK si connette in modo sicuro ad Azure Percept studio e ad altri servizi di Azure che usano il protocollo TLS (Transport Layer Security). Azure Percept DK è un dispositivo abilitato per la Azure IoT Edge. IoT Edge Runtime è una raccolta di programmi che trasformano un dispositivo in un dispositivo IoT Edge. Collettivamente, i componenti di runtime IoT Edge consentono IoT Edge ai dispositivi di ricevere il codice per l'esecuzione al perimetro e di comunicare i risultati. Azure Percept DK usa i contenitori Docker per isolare i carichi di lavoro IoT Edge dal sistema operativo host e dalle applicazioni abilitate per Edge. Per ulteriori informazioni sul Framework di sicurezza di Azure IoT Edge, vedere [IOT Edge Security Manager](https://docs.microsoft.com/azure/iot-edge/iot-edge-security-manager?view=iotedge-2018-06).

### <a name="device-update-for-iot-hub"></a>Aggiornamento del dispositivo per l'hub Internet

L'aggiornamento dei dispositivi per l'hub Internet consente l'aggiornamento sicuro, scalabile e affidabile, che offre protezione rinnovabile ai dispositivi Azure Percept. Fornisce controlli di gestione avanzati e la conformità degli aggiornamenti tramite informazioni dettagliate. Azure Percept DK include una soluzione di aggiornamento dei dispositivi pre-integrata che fornisce aggiornamenti resilienti (A/B) dal firmware ai livelli del sistema operativo.

<!---I think the below topics need to be somewhere else, (i.e. not on the main page)
--->

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Configurazione dei firewall per Azure Percept DK

Se per la configurazione di rete è necessario consentire esplicitamente le connessioni effettuate dai dispositivi Azure Percept DK, esaminare l'elenco di componenti seguente.

Questo elenco di controllo è un punto di partenza per le regole del firewall:

|URL (* = carattere jolly) |Porte TCP in uscita|    Utilizzo|
|-------------------|------------------|---------|
|*. auth.azureperceptdk.azure.net|   443|    Autenticazione e autorizzazione di Azure DK SOM|
|*. auth.projectsantacruz.azure.net| 443|    Autenticazione e autorizzazione di Azure DK SOM|

Esaminare inoltre l'elenco delle [connessioni usate da Azure Internet Azure Edge](https://docs.microsoft.com/azure/iot-edge/production-checklist?view=iotedge-2018-06#allow-connections-from-iot-edge-devices).

## <a name="additional-recommendations-for-deployment-to-production"></a>Suggerimenti aggiuntivi per la distribuzione nell'ambiente di produzione

Azure Percept DK offre un'ampia gamma di funzionalità di sicurezza predefinite. Oltre alle potenti funzionalità di sicurezza incluse nella versione corrente, Microsoft suggerisce anche le linee guida seguenti quando si considerano le distribuzioni di produzione:

- Protezione fisica avanzata del dispositivo stesso
- Verifica dell'abilitazione della crittografia dei dati inattivi
- Monitoraggio continuo della postura del dispositivo e risposta rapida agli avvisi
- Limitazione del numero di amministratori che hanno accesso al dispositivo

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui modelli di [intelligenza artificiale di Azure Percept](./overview-ai-models.md)disponibili.