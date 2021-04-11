---
title: Requisiti di certificazione gestiti da Edge
description: Requisiti del programma di certificazione gestito da Edge
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Managed Certification Requirements
ms.service: certification
ms.openlocfilehash: 744f81d0544a765f60793ece1aa539c6c37e39cd
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969262"
---
# <a name="azure-certification-edge-managed"></a>Gestione Edge di certificazione di Azure 

Questo documento descrive le funzionalità specifiche del dispositivo che verranno rappresentate nel catalogo dei dispositivi Azure Certified. Una funzionalità è un attributo del dispositivo singolare che può descrivere il dispositivo. 

## <a name="program-purpose"></a>Scopo del programma

Certificazione gestita da Edge, una certificazione incrementale che esula dalla certificazione dei dispositivi Azure Certified di base. Gestione Edge si concentra sugli standard di gestione dei dispositivi per i dispositivi connessi ad Azure e convalida la compatibilità di IoT Edge Runtime per la distribuzione e la gestione dei moduli. (In precedenza, il programma veniva identificato come programma di certificazione IoT Edge). 

La certificazione gestita da Edge convalida IoT Edge compatibilità di runtime per la distribuzione e la gestione dei moduli. Questo programma consente di gestire in modo sicuro i dispositivi Azure connected.

## <a name="requirements"></a>Requisiti

La certificazione gestita da Edge richiede tutti i requisiti del [programma Azure Certified Device Baseline](.\program-requirements-azure-certified-device.md).

**DPS: lo scopo del test è verificare che il dispositivo implementi e supporti il servizio Device provisioning in hub Internet con uno dei tre metodi di attestazione**

| **Nome**                | AzureReady. DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilità di destinazione** | Ignite (in anteprima)                                                |
| **Si applica a**          | Qualsiasi dispositivo                                      |
| **Sistema operativo**                  | Agnostico                                                     |
| **Tipo di convalida**     | Automatizzata                                                    |
| **Convalida**          | AICS convalida il codice del dispositivo del supporto DPS. **1.** L'utente deve selezionare uno dei metodi di attestazione (X. 509, TPM e chiave SAS). **2.** A seconda del metodo di attestazione, l'utente deve eseguire un'azione corrispondente, ad esempio **un** , caricare il certificato X. 509 nell'ambito del DPS gestito AICS **b)** implementare la chiave SAS o la chiave di verifica dell'autenticità nel dispositivo. **3.** Quindi, l'utente preme il pulsante ' Connetti ' per connettersi all'hub AICS Managed Internet tramite DPS                                                    |
| **Risorse**           |                                                      |
| **Azure consigliato:**     | N/D                                                    |

## <a name="iot-edge"></a>IoT Edge

**Il runtime di Edge esiste: lo scopo del test è verificare che il dispositivo contenga IoT Edge Runtime ($edgehub e $edgeagent) funzioni correttamente.**

| **Nome**                | EdgeManaged.EdgeRT                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilità di destinazione** | Ora disponibile                                                          |
| **Si applica a**          | Dispositivo IoT Edge                                                   |
| **Sistema operativo**                  | [Sistema operativo Tier1 e livello 2](../iot-edge/support.md)                                                     |
| **Tipo di convalida**     | Automatizzata                                                    |
| **Convalida**          | AICS convalida la capacità di distribuzione del IoT Edge RT installato. **1.** L'utente deve specificare un sistema operativo specifico (sistema operativo non presente nell'elenco di Tier1/2 non accettate) **2.** AICS genera il file config. YAML e distribuisce il modulo 3 del [sensore temporaneo simulato della periferica](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-iot.simulated-temperature-sensor?tab=Overview) **.** AICS verifica che il sottosistema del contenitore (Moby) compatibile con Docker sia installato nel dispositivo **4.** Il risultato del test viene determinato in base alla corretta distribuzione del modulo Edge del sensore temporaneo simulato e alla funzionalità del sottosistema contenitore compatibile con Docker                                                    |
| **Risorse**           | **a)** [AICS Blog](https://azure.microsoft.com/en-in/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/), **b)** [procedure di certificazione](./overview.md) (con tutte le risorse aggiuntive), **c)** [requisiti](./program-requirements-azure-certified-device.md) |
| **Azure consigliato:**     | N/D                                                    |

### <a name="capability-template"></a>Modello di funzionalità:

**IoT Edge semplicità di installazione: lo scopo del test è verificare che IoT Edge dispositivo sia facile da configurare e convalidare IoT Edge Runtime venga preinstallato durante la convalida del dispositivo fisico**

| **Nome**                | EdgeManaged. PhysicalDevice                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilità di destinazione** | Disponibile ora (attualmente in attesa a causa di COVID-19)                                            |
| **Si applica a**          | Dispositivo IoT Edge                                                   |
| **Sistema operativo**                  | [Sistema operativo Tier1 e livello 2](../iot-edge/support.md)                                                     |
| **Tipo di convalida**     | Verifica manuale/Lab                                                    |
| **Convalida**          | L'OEM deve spedire il dispositivo fisico ad amministrazione delle cose (HCL). HCL esegue la convalida manuale del dispositivo fisico per verificare: **1.** EdgeRT usa il sottosistema Moby (versione di ridistribuzione consentita). Non Docker **2.** Selezionare il modulo Edge più recente per convalidare la possibilità di distribuire Edge.                                                     |
| **Risorse**           | **a)** [AICS Blog](https://azure.microsoft.com/en-in/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/), **b)** [requisiti](./program-requirements-azure-certified-device.md) per la [certificazione](./overview.md) , **c)** |
| **Azure consigliato:**     | N/D                                                    |