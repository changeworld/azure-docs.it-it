---
title: Controlli di base e personalizzati
description: Informazioni sul concetto di Azure Defender per la baseline.
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 1b8b9d62918e40262da6b3df48d0fece842e050f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779359"
---
# <a name="azure-defender-for-iot-baseline-and-custom-checks"></a>Azure Defender per la linea di base e i controlli personalizzati

Questo articolo illustra la linea di base di Defender for Internet e riepiloga tutte le proprietà associate dei controlli personalizzati di base.

## <a name="baseline"></a>Di base

Una linea di base stabilisce un comportamento standard per ogni dispositivo e rende più semplice stabilire un comportamento insolito o una deviazione dalle norme previste.

## <a name="baseline-custom-checks"></a>Controlli personalizzati di base

I controlli personalizzati di base stabiliscono un elenco personalizzato di controlli per ogni linea di base del dispositivo usando il **modulo identità gemello** del dispositivo.

## <a name="setting-baseline-properties"></a>Impostazione delle proprietà della linea di base

1. Nell'hub Internet delle cose individuare e selezionare il dispositivo che si vuole modificare.

1. Fare clic sul dispositivo e quindi fare clic sul modulo **azureiotsecurity** .

1. Fare clic su **Module Identity gemelle**.

1. Caricare il file di **controllo personalizzato di base** nel dispositivo.

1. Aggiungere le proprietà della linea di base a Defender-cose-micro-Agent e fare clic su **Salva**.

### <a name="baseline-custom-check-file-example"></a>Esempio di file di controllo personalizzato Baseline

Per configurare i controlli personalizzati della linea di base:

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>Proprietà controllo personalizzato Baseline

| Nome| Stato | Valori validi| Valori predefiniti| Descrizione |
|------|-----|------|-----|-----|
|baselineCustomChecksEnabled|Obbligatorio: true |Valori validi: **Boolean** |Valore predefinito: **false** |Intervallo di tempo massimo prima dell'invio dei messaggi con priorità alta.|
|baselineCustomChecksFilePath |Obbligatorio: true|Valori validi: **String**, **null** |Valore predefinito: **null** |Percorso completo della configurazione XML di base|
|baselineCustomChecksFileHash |Obbligatorio: true|Valori validi: **String**, **null** |Valore predefinito: **null** |`sha256sum` del file di configurazione XML. Per ulteriori informazioni, utilizzare il [riferimento sha256sum](https://linux.die.net/man/1/sha256sum) . |

Per esaminare esempi di base aggiuntivi, vedere [esempio di Baseline personalizzato-1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) e [esempio di Baseline personalizzato-2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml).

## <a name="next-steps"></a>Passaggi successivi

- Accedere ai [dati di sicurezza non elaborati](how-to-security-data-access.md)
- [Analizzare un dispositivo](how-to-investigate-device.md)
- Comprendere ed esplorare le [raccomandazioni sulla sicurezza](concept-recommendations.md)
- Comprendere ed esplorare gli [avvisi di sicurezza](concept-security-alerts.md)
