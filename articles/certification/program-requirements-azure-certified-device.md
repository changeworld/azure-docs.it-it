---
title: Requisiti per i dispositivi certificati Azure
description: Requisiti di Azure Certified Device Program
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Azure Certified Device Certification Requirements
ms.service: certification
ms.openlocfilehash: 497ffa4b3026491d6aa95df87708b3b1f2f1619e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308286"
---
# <a name="azure-certified-device-requirements"></a>Requisiti per i dispositivi certificati Azure 
(noto in precedenza come hub Internet)

Questo documento descrive le funzionalità specifiche del dispositivo che verranno rappresentate nel catalogo dei dispositivi Azure Certified. Una funzionalità è un attributo del dispositivo singolare che può essere l'implementazione del software o una combinazione di implementazioni software e hardware. 

## <a name="program-purpose"></a>Scopo del programma

Microsoft sta semplificando l'IT e la certificazione dei dispositivi Azure Certified è un programma di certificazione di base per assicurarsi che tutti i tipi di dispositivi vengano sottoposti a provisioning nell'hub Azure.

Promessa della certificazione dei dispositivi certificati di Azure:

1. Telemetria del supporto del dispositivo che funziona con l'hub Internet
2.  Supporto dei dispositivi per il servizio Device provisioning in hub (DPS) per il provisioning sicuro nell'hub Azure.
3.  Il dispositivo supporta l'input semplice del trasferimento con ambito ID DPS di destinazione senza richiedere all'utente di ricompilare il codice incorporato.
4.  Facoltativamente convalida altri elementi, ad esempio i messaggi da cloud a dispositivo, i metodi diretti e il dispositivo gemello 

## <a name="requirements"></a>Requisiti

**Necessaria Da dispositivo a cloud: lo scopo del test è verificare che i dispositivi che inviano dati di telemetria funzionino con l'hub Internet.**

| **Nome**                | AzureCertified. D2C                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilità di destinazione** | Ora disponibile                                                |
| **Si applica a**          | Dispositivo foglia/dispositivo perimetrale                                      |
| **Sistema operativo**                  | Agnostico                                                     |
| **Tipo di convalida**     | Automatizzata                                                    |
| **Convalida**          | Il dispositivo deve inviare qualsiasi schema di telemetria all'hub Internet. Microsoft fornisce il [flusso di lavoro del portale](https://certify.azure.com/) per eseguire i test. Da dispositivo a cloud (obbligatorio): **1.** Verifica che il dispositivo sia in grado di inviare messaggi a AICS Managed Internet Hub **2.** L'utente deve specificare il numero e la frequenza dei messaggi. **3.** AICS convalida i dati di telemetria ricevuti dall'istanza dell'hub |
| **Risorse**           | [Procedura di certificazione](./overview.md) (con tutte le risorse aggiuntive) |

**Necessaria DPS: lo scopo del test è verificare che il dispositivo implementi e supporti il servizio Device provisioning in hub Internet con uno dei tre metodi di attestazione**

| **Nome**                | AzureCertified. DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilità di destinazione** | Nuova                                                          |
| **Si applica a**          | Qualsiasi dispositivo                                                   |
| **Sistema operativo**                  | Agnostico                                                     |
| **Tipo di convalida**     | Automatizzata                                                    |
| **Convalida**          | Il dispositivo supporta l'input semplice della proprietà dell'ambito dell'ID DPS di destinazione senza che sia necessario ricompilare il codice incorporato. Microsoft fornisce il [flusso di lavoro del portale](https://certify.azure.com) per eseguire i test per verificare che il dispositivo supporti DPS **1.** L'utente deve selezionare uno dei metodi di attestazione (X. 509, TPM e chiave SAS) **2.** A seconda del metodo di attestazione, l'utente deve eseguire un'azione corrispondente, ad esempio **un** , caricare il certificato X. 509 nell'ambito del DPS gestito di AICS **b)** implementare la chiave SAS o la chiave di verifica dell'autenticità nel dispositivo |
| **Risorse**           | [Panoramica del servizio Device provisioning](../iot-dps/about-iot-dps.md) |

**[Se implementato] Da cloud a dispositivo: lo scopo del test è verificare che i messaggi possano essere inviati dal cloud ai dispositivi**                                                              

| **Nome**                | AzureCertified. C2D                                                  |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilità di destinazione** | Ora disponibile                                            |
| **Si applica a**          | Dispositivo foglia/dispositivo perimetrale                                                   |
| **Sistema operativo**                  | Agnostico                                                     |
| **Tipo di convalida**     | Automatizzata                                                    |
| **Convalida**          | Il dispositivo deve essere in grado di eseguire il cloud ai messaggi del dispositivo dall'hub. Microsoft fornisce il [flusso di lavoro del portale](https://certify.azure.com) per eseguire questi test. Da cloud a dispositivo (se implementato): **1.** Verifica che il dispositivo possa ricevere il messaggio dall'hub Internet **2.** AICS Invia un messaggio casuale e viene convalidato tramite ACK del messaggio dal dispositivo  |
| **Risorse**           | **a)** i [passaggi di certificazione](./overview.md) (con tutte le risorse aggiuntive) **b)** [inviano messaggi da cloud a dispositivo da un hub Internet delle](../iot-hub/iot-hub-devguide-messages-c2d.md) cose |

**[Se implementato] Metodi diretti: lo scopo del test è verificare che i dispositivi funzionino con l'hub Internet e supportino i metodi diretti**

| **Nome**                | AzureCertified.DirectMethods                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilità di destinazione** | Ora disponibile                                            |
| **Si applica a**          | Dispositivo foglia/dispositivo perimetrale                                                   |
| **Sistema operativo**                  | Agnostico                                                     |
| **Tipo di convalida**     | Automatizzata                                                    |
| **Convalida**          | Il dispositivo deve essere in grado di ricevere e rispondere alle richieste di comandi dall'hub. Microsoft fornisce il [flusso di lavoro del portale](https://certify.azure.com) per eseguire i test. Metodi diretti (se implementati) **1.** L'utente deve specificare il payload del metodo diretto. **2.** AICS convalida la richiesta di payload specificata inviata dall'hub e dal messaggio ACK ricevuti dal dispositivo |
| **Risorse**           | **a)** [procedura di certificazione](./overview.md) (con tutte le risorse aggiuntive) **b)** [informazioni sui metodi diretti dall'hub](../iot-hub/iot-hub-devguide-direct-methods.md) Internet |

**[Se implementato] Proprietà del dispositivo gemello: lo scopo del test è verificare che i dispositivi che inviano dati di telemetria funzionino con l'hub Internet e supportino alcune delle funzionalità dell'hub Internet, come i metodi diretti e le proprietà dei dispositivi gemelli**

| **Nome**                                  | AzureCertified. DeviceTwin                                      |
| ----------------------------------------- | ------------------------------------------------------------ |
| **Disponibilità di destinazione**                   | Ora disponibile                                            |
| **Si applica a**                            | Dispositivo foglia/dispositivo perimetrale                                                   |
| **Sistema operativo**                                    | Agnostico                                                     |
| **Tipo di convalida**                       | Automatizzata                                                       |
| **Convalida**                            | Il dispositivo deve inviare qualsiasi schema di telemetria all'hub Internet. Microsoft fornisce il [flusso di lavoro del portale](https://certify.azure.com) per eseguire i test. Proprietà del dispositivo gemello (se implementata) **1.** AICS convalida la proprietà in lettura/scrittura nel dispositivo gemello JSON **2.** L'utente deve specificare il payload JSON da modificare **3.** AICS convalida le proprietà desiderate specificate inviate dall'hub e dal messaggio ACK ricevuti dal dispositivo |
| **Risorse**                             | **a)** [procedura di certificazione](./overview.md) (con tutte le risorse aggiuntive) **b)** [usare dispositivi gemelli con l'hub](../iot-hub/iot-hub-devguide-device-twins.md) Internet |
