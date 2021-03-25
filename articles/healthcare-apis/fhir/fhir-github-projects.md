---
title: Progetti GitHub correlati per l'API di Azure per FHIR
description: Elencare tutti i repository Open Source (GitHub) per l'API di Azure per FHIR.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/01/2021
ms.author: ginle
ms.openlocfilehash: 9977765183bd567377592631d65f3e548bef4b34
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105097639"
---
# <a name="related-github-projects"></a>Progetti GitHub correlati

In GitHub sono disponibili molti progetti open source che forniscono il codice sorgente e le istruzioni per distribuire i servizi per diversi usi. Si è sempre invitati a visitare i repository GitHub per apprendere e sperimentare le funzionalità e i prodotti. 

## <a name="fhir-server"></a>Server FHIR
* [Microsoft/Fhir-server](https://github.com/microsoft/fhir-server/): server Fhir open source, che costituisce la base per l'API di Azure per Fhir
* Per visualizzare le versioni più recenti, fare riferimento alle [Note sulla versione](https://github.com/microsoft/fhir-server/releases)
* [Microsoft/Fhir-Server-Samples](https://github.com/microsoft/fhir-server-samples): ambiente di esempio

## <a name="data-conversion--anonymization"></a>Conversione dei dati & anonimato dei

#### <a name="fhir-converter"></a>Convertitore FHIR
* [Microsoft/FHIR-Converter](https://github.com/microsoft/FHIR-Converter): utilità di conversione per tradurre i formati di dati legacy in FHIR
* Integrazione con l'API di Azure per FHIR e FHIR Server for Azure sotto forma di operazione di $convert dati
* Miglioramenti continui in sistemi operativi e integrazione continua con i server FHIR
 
#### <a name="fhir-converter---vs-code-extension"></a>Convertitore FHIR-estensione VS Code
* [Microsoft/FHIR-Tools-for-anonimato dei](https://github.com/microsoft/FHIR-Tools-for-Anonymization): un set di strumenti per aiutare i dati (in formato FHIR) anonimato dei
* Integrazione con l'API di Azure per FHIR e il server FHIR per Azure sotto forma di "esportazione deselezionata"

#### <a name="fhir-tools-for-anonymization"></a>Strumenti FHIR per anonimato dei
* [Microsoft/VSCODE-azurehealthcareapis-Tools](https://github.com/microsoft/vscode-azurehealthcareapis-tools): estensione vs code che contiene una raccolta di strumenti per lavorare con le API di Azure Healthcare
* Rilasciato a Visual Studio Marketplace
* Usato per la creazione di modelli Liquid da usare in FHIR Converter

## <a name="iot-connector"></a>Connettore Internet

#### <a name="integration-with-iot-hub-and-iot-central"></a>Integrazione con l'hub Internet e IoT Central
* [Microsoft/iomt-Fhir](https://github.com/microsoft/iomt-fhir): integrazione con l'hub Internet delle cose o da IOT Central a Fhir con normalizzazione dei dati e conversione Fhir dei dati normalizzati
* Normalizzazione: le informazioni sui dati del dispositivo vengono estratte in un formato comune per un'ulteriore elaborazione
* Conversione FHIR: i dati normalizzati e raggruppati vengono mappati a FHIR. Le osservazioni vengono create o aggiornate in base ai modelli configurati e collegati al dispositivo e al paziente.
* [Strumenti per facilitare la compilazione della mappa di conversazione](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper): visualizzare la configurazione di mapping per normalizzare i dati di input del dispositivo e trasformarli nelle risorse FHIR. Gli sviluppatori possono usare questo strumento per modificare e testare i mapping, il mapping dei dispositivi e il mapping di FHIR ed esportarli per il caricamento nel connettore Internet delle cose nel portale di Azure.

#### <a name="healthkit-and-fhir-integration"></a>Integrazione di HealthKit e FHIR
* [Microsoft/Healthkit-on-Fhir](https://github.com/microsoft/healthkit-on-fhir): una libreria Swift che consente di automatizzare l'esportazione dei dati di Apple Healthkit in un server Fhir

 