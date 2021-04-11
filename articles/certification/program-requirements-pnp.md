---
title: Requisiti di certificazione Plug and Play
description: Requisiti del programma di certificazione Plug and Play
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: IoT Plug and Play Certification Requirements
ms.service: certification
ms.openlocfilehash: bbe9a3f18463285521dde0ee64b369cffcd71d75
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969257"
---
# <a name="iot-plug-and-play-certification-requirements"></a>Requisiti di certificazione Plug and Play

Questo documento descrive le funzionalità specifiche del dispositivo che verranno rappresentate nel catalogo dei dispositivi Azure Internet. Una funzionalità è un attributo del dispositivo singolare che può essere l'implementazione del software o una combinazione di implementazioni software e hardware.

## <a name="program-purpose"></a>Scopo del programma

Plug and Play IoT (anteprima) consente agli sviluppatori di soluzioni di integrare dispositivi intelligenti con le rispettive soluzioni senza configurazione manuale. Plug and Play IoT è basato su un modello di dispositivo che viene usato dal dispositivo per annunciare le rispettive funzionalità a un'applicazione abilitata per Plug and Play IoT. Questo modello è strutturato come un set di elementi: telemetria, proprietà e comandi.

La promessa di Plug and Play la certificazione è:

1.  Le interfacce e i modelli di dispositivo definiti sono conformi al  [linguaggio Digital Twin Definition](https://github.com/Azure/opendigitaltwins-dtdl)  
2.  Provisioning sicuro e trasferimento semplice della proprietà dell'ambito ID nei servizi Device provisioning
3.  Facile integrazione con le soluzioni basate su Azure Internet con le [API gemelle digitali](../iot-pnp/concepts-digital-twin.md)  : Hub Azure e Azure IOT Central
4.  Verità del prodotto convalidata nei dispositivi certificati

## <a name="requirements"></a>Requisiti

**Necessaria Da dispositivo a cloud: lo scopo del test è verificare che i dispositivi che inviano dati di telemetria funzionino con l'hub Internet.**

| **Nome**                | IoTPnP. D2C                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilità di destinazione** | Ora disponibile                                                |
| **Si applica a**          | Dispositivo foglia/dispositivo perimetrale                                      |
| **Sistema operativo**                  | Agnostico                                                     |
| **Tipo di convalida**     | Automatizzata                                                    |
| **Convalida**          | Il dispositivo deve inviare qualsiasi schema di telemetria all'hub Internet. Microsoft fornisce il [flusso di lavoro del portale](https://certify.azure.com) per eseguire i test. Da dispositivo a cloud (obbligatorio): **1.** Verifica che il dispositivo sia in grado di inviare messaggi a AICS Managed Internet Hub **2.** L'utente deve specificare il numero e la frequenza dei messaggi. **3.** AICS convalida i dati di telemetria ricevuti dall'istanza dell'hub |
| **Risorse**           | [Procedura di certificazione](./overview.md) (con tutte le risorse aggiuntive) |

**Necessaria DPS: lo scopo del test è verificare che il dispositivo implementi e supporti il servizio Device provisioning in hub Internet con uno dei tre metodi di attestazione**

| **Nome**                | IoTPnP. DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilità di destinazione** | Ora disponibile                                                |
| **Si applica a**          | Qualsiasi dispositivo                                                   |
| **Sistema operativo**                  | Agnostico                                                     |
| **Tipo di convalida**     | Automatizzata                                                    |
| **Convalida**          | Il dispositivo deve implementare il trasferimento semplice della proprietà dell'ambito dell'ID DPS senza dover ricompilare il codice incorporato. Microsoft fornisce il [flusso di lavoro del portale](https://certify.azure.com) per eseguire i test per verificare che il dispositivo supporti DPS **1.** L'utente deve selezionare uno dei metodi di attestazione (X. 509, TPM e chiave SAS) **2.** A seconda del metodo di attestazione, l'utente deve eseguire un'azione corrispondente, ad esempio **un** , caricare il certificato X. 509 nell'ambito del DPS gestito di AICS **b)** implementare la chiave SAS o la chiave di verifica dell'autenticità nel dispositivo |
| **Risorse**           | **a)** [Panoramica del servizio Device provisioning](../iot-dps/about-iot-dps.md), **b)** [file di configurazione di esempio per il trasferimento dell'ambito ID DPS](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview-pnp/digitaltwin_client/samples/digitaltwin_sample_ll_device/sample_config) |

**Necessaria DTDL V2: lo scopo del test per garantire che i modelli e le interfacce del dispositivo definiti siano conformi al linguaggio di definizione dei gemelli digitali V2.**                                                              

| **Nome**                | IoTPnP.DTDL                                                  |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilità di destinazione** | Ora disponibile                                                |
| **Si applica a**          | Qualsiasi dispositivo                                                   |
| **Sistema operativo**                  | Agnostico                                                     |
| **Tipo di convalida**     | Automatizzata                                                    |
| **Convalida**          | Il [flusso di lavoro del portale](https://certify.azure.com) convalida: **1.** Annuncio dell'ID modello e verificare che il dispositivo sia connesso usando il protocollo MQTT o MQTT over WebSockets Protocol **2.** I modelli sono conformi a DTDL v2 **3.** I dati di telemetria, le proprietà e i comandi sono implementati correttamente e interagiscono tra i dispositivi gemelli e i dispositivi gemelli dell'hub Internet. |
| **Risorse**           | [Aggiornamenti di aggiornamento dell'anteprima pubblica](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |

**Necessaria I modelli di dispositivo sono pubblicati nel repository di modelli pubblici**

| **Nome**                | IoTPnP.ModelRepo                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilità di destinazione** | Ora disponibile                                                |
| **Si applica a**          | Qualsiasi dispositivo                                                   |
| **Sistema operativo**                  | Agnostico                                                     |
| **Tipo di convalida**     | Automatizzata                                                    |
| **Convalida**          | Tutti i modelli di dispositivo sono necessari per la pubblicazione in un repository pubblico. I modelli di dispositivo vengono risolti tramite i modelli disponibili nel repository pubblico **1.** Prima di inviare la certificazione, l'utente deve pubblicare manualmente i modelli nel repository pubblico. **2.** Si noti che, una volta pubblicati, i modelli non sono modificabili. È consigliabile eseguire la pubblicazione solo quando vengono finalizzati i modelli e il codice del dispositivo incorporato. * 1 * 1 l'utente deve contattare il supporto tecnico Microsoft per revocare i modelli dopo la pubblicazione nel repository del modello **3.** Il [flusso di lavoro del portale](https://certify.azure.com) controlla l'esistenza dei modelli nel repository pubblico quando il dispositivo è connesso al servizio di certificazione |
| **Risorse**           | [Repository modelli](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |

**Necessaria Convalida del dispositivo fisico con GSG**

| **Nome**                                  | IoTPnP. PhysicalDevice                                      |
| ----------------------------------------- | ------------------------------------------------------------ |
| **Disponibilità di destinazione**                   | Ora disponibile                                                |
| **Si applica a**                            | Qualsiasi dispositivo                                                   |
| **Sistema operativo**                                    | Agnostico                                                     |
| **Tipo di convalida**                       | Manuale                                                       |
| **Convalida**                            | I partner devono collaborare con il contatto Microsoft ( [iotcert@microsoft.com](mailto:iotcert@microsoft.com) ) per prendere provvedimenti per eseguire convalide aggiuntive sul dispositivo fisico. A causa della situazione COVID-19, verranno esaminati vari modi per eseguire la convalida dei dispositivi fisici senza inviare il dispositivo a Microsoft. |
| **Risorse**                             | I dettagli sono disponibili in seguito                                 |
| **Azure consigliato**       | N/D    |

**[Se implementato] Interfaccia informazioni sul dispositivo: lo scopo del test consiste nel convalidare l'interfaccia informazioni sul dispositivo è implementata correttamente nel codice del dispositivo**

| **Nome**                | IoTPnP.DeviceInfoInterface                                   |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilità di destinazione** | Ora disponibile                                                |
| **Si applica a**          | Qualsiasi dispositivo                                                   |
| **Sistema operativo**                  | Agnostico                                                     |
| **Tipo di convalida**     | Automatizzata                                                    |
| **Convalida**          | Il [flusso di lavoro del portale](https://certify.azure.com) convalida il codice del dispositivo che implementa l' [interfaccia informazioni sul dispositivo](https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation;1?api-version=2020-05-01-previewureiot:DeviceManagement:DeviceInformation:1) **1.** Verifica che i valori vengano emessi dal codice del dispositivo nell'hub Internet **2.** Verifica che l'interfaccia sia implementata in DCM (questa implementazione cambierà in DTDL v2) **3.** Le proprietà di controllo non sono in grado di scrivere (sola lettura) **4.** Verifica che il tipo di schema sia String e/o Long e not null |
| **Risorse**           | [Interfaccia definita da Microsoft](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |
| **Azure consigliato**  | N/D                                                          |

**[Se implementato] Da cloud a dispositivo: lo scopo del test è verificare che i messaggi possano essere inviati dal cloud ai dispositivi**

| **Nome**                | IoTPnP. C2D                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilità di destinazione** | Ora disponibile                                                |
| **Si applica a**          | Dispositivo foglia/dispositivo perimetrale                                      |
| **Sistema operativo**                  | Agnostico                                                     |
| **Tipo di convalida**     | Automatizzata                                                    |
| **Convalida**          | Il dispositivo deve essere in grado di eseguire il cloud ai messaggi del dispositivo dall'hub. Microsoft fornisce il [flusso di lavoro del portale](https://certify.azure.com) per eseguire questi test. Da cloud a dispositivo (se implementato): **1.** Verifica che il dispositivo possa ricevere il messaggio dall'hub Internet **2.** AICS Invia un messaggio casuale e viene convalidato tramite ACK del messaggio dal dispositivo |
| **Risorse**           | **1.** la [procedura di certificazione](./overview.md) (ha tutte le risorse aggiuntive), **2.** [Inviare messaggi da cloud a dispositivo da un hub Internet delle cose](../iot-hub/iot-hub-devguide-messages-c2d.md) |

**[Se implementato] Metodi diretti: lo scopo del test è verificare che i dispositivi funzionino con l'hub Internet e supportino i metodi diretti**

| **Nome**                | IoTPnP.DirectMethods                                     |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilità di destinazione** | Ora disponibile                                                |
| **Si applica a**          | Dispositivo foglia/dispositivo perimetrale                                      |
| **Sistema operativo**                  | Agnostico                                                     |
| **Tipo di convalida**     | Automatizzata                                                    |
| **Convalida**          | Il dispositivo deve essere in grado di ricevere e rispondere alle richieste di comandi dall'hub. Microsoft fornisce il [flusso di lavoro del portale](https://certify.azure.com) per eseguire i test. Metodi diretti (se implementati): **1.** L'utente deve specificare il payload del metodo diretto. **2.** AICS convalida la richiesta di payload specificata inviata dall'hub e dal messaggio ACK ricevuti dal dispositivo |
| **Risorse**           | **1.** la [procedura di certificazione](./overview.md) (ha tutte le risorse aggiuntive), **2.** [Informazioni sui metodi diretti dall'hub Internet](../iot-hub/iot-hub-devguide-direct-methods.md) |

**[Se implementato] Proprietà del dispositivo gemello: lo scopo del test è verificare che i dispositivi che inviano dati di telemetria funzionino con l'hub Internet e supportino alcune delle funzionalità dell'hub Internet, come i metodi diretti e le proprietà dei dispositivi gemelli**

| **Nome**                | IoTPnP. DeviceTwin                                        |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilità di destinazione** | Ora disponibile                                                |
| **Si applica a**          | Dispositivo foglia/dispositivo perimetrale                                      |
| **Sistema operativo**                  | Agnostico                                                     |
| **Tipo di convalida**     | Automatizzata                                                    |
| **Convalida**          | Il dispositivo deve inviare qualsiasi schema di telemetria all'hub Internet. Microsoft fornisce il [flusso di lavoro del portale](https://certify.azure.com) per eseguire i test. Proprietà del dispositivo gemello (se implementato): **1.** AICS convalida la proprietà in lettura/scrittura nel dispositivo gemello JSON **2.** L'utente deve specificare il payload JSON da modificare **3.** AICS convalida le proprietà desiderate specificate inviate dall'hub e dal messaggio ACK ricevuti dal dispositivo |
| **Risorse**           | **1.** la [procedura di certificazione](./overview.md) (ha tutte le risorse aggiuntive), **2.** [Usare dispositivi gemelli con l'hub Internet](../iot-hub/iot-hub-devguide-device-twins.md) |