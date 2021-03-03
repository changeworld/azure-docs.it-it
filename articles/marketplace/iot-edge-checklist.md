---
title: Elenco di controllo della pre-certificazione per le offerte del modulo IoT Edge in Azure Marketplace
description: Informazioni sui requisiti di certificazione specifici per la pubblicazione delle offerte del modulo IoT Edge in Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/01/2021
ms.openlocfilehash: 4782e6c0ed866c467051fc1811a60df0010a3db1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745247"
---
# <a name="pre-certification-checklist-for-iot-edge-modules"></a>Elenco di controllo pre-certificazione per moduli IoT Edge

> [!NOTE]
> Si consiglia agli editori di esaminare questo elenco di controllo e di convalidare la funzionalità del modulo prima di inviare le certificazioni. Questo consente di accelerare il processo di certificazione riducendo la necessità di modifiche e riinvii.

## <a name="validation-of-image"></a>Convalida dell'immagine

Quando l'immagine del modulo Edge è pronta per l'invio, eseguire questi passaggi per assicurarsi che l'immagine funzioni come previsto da Microsoft.

### <a name="steps-to-perform-in-the-azure-portal"></a>Passaggi da eseguire nel portale di Azure

1. Aprire il [portale di Azure](https://partner.microsoft.com/).
1. Creare un gruppo di risorse.
1. Creare un hub IoT.
1. Creare un dispositivo IoT Edge.
1. Copiare la stringa di connessione e salvarla nel blocco note.
1. Selezionare i **moduli set nel dispositivo perimetrale creato**.
1. Aggiungere i dettagli di ACR in cui risiede la versione più recente dell'immagine.
1. Selezionare **Aggiungi modulo IOT Edge** e specificare:
    - URI dell'immagine nell'impostazione del modulo
    - La variabile di ambiente (uguale a quella aggiunta nel centro per i partner)
    - Opzioni di creazione del contenitore (analogamente a quanto aggiunto nel centro per i partner)
    - Impostazione del modulo gemello (uguale a quello aggiunto nel centro per i partner)
1. Aggiungere le route (analogamente a quanto è stato aggiunto al centro per i partner).
1. Selezionare **Rivedi e crea**.

I moduli perimetrali vengono distribuiti nel dispositivo perimetrale creato in Azure.

### <a name="steps-to-perform-on-the-device"></a>Passaggi da eseguire sul dispositivo

#### <a name="device-details"></a>Dettagli dispositivo

Il team di certificazione usa il seguente hardware per convalidare le immagini in architetture diverse:

- Per le immagini x64, una macchina virtuale di Azure con dimensioni di configurazione come D2S standard V3 che esegue Ubuntu server 18.04/Ubuntu Server 16,04.
- Per Azure Resource Manager immagini 32, un modello di Raspberry Pi 3 B.
- Per Azure Resource Manager immagini 64, NVIDIA Jetson nano 4Gb.

#### <a name="steps"></a>Passaggi

1. Verificare che i dispositivi/VM creati siano accessibili tramite Putty.
1. Scaricare [IOT Edge Runtime](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge) nel dispositivo.
1. Aggiornare la stringa di connessione copiata nel passaggio 5 al file config. yaml.
1. Riavviare il modulo Edge con `sudo systemctl restart iotedge` .
1. Controllare se il modulo è stato distribuito nel dispositivo con `sudo iotedge list` . deve essere nello stato in esecuzione.
1. Verificare che nei log del modulo distribuito con non `sudo iotedge logs “Module Name“ -f` siano presenti errori. Se sono presenti errori noti, è **necessario definirlo nelle note** del centro per i partner, prima di inviare l'offerta.

## <a name="metadata-validation"></a>Convalida dei metadati

Verificare gli elementi seguenti:

- Il tag più recente è elencato sia nel centro per i partner che nel Container Registry di Azure.
- Nella descrizione dell'offerta è stato aggiunto il requisito hardware minimo.
- Il nome utente e la password del registro contenitori di Azure vengono aggiornati e aggiunti nel centro per i partner.
- Accuratezza della **proprietà dei dispositivi gemelli** desiderata, *se applicabile*.
- Accuratezza delle **variabili di ambiente** desiderate, *se applicabile*.
- Accuratezza delle **Opzioni di creazione** desiderate, *se applicabile*.
- È presente la stringa di connessione per la gestione dei lead.
- Informativa sulla privacy presente
- Condizioni per l'utilizzo presente

## <a name="next-steps"></a>Passaggi successivi

- [Distribuisci moduli dal Marketplace commerciale](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal#deploy-from-azure-marketplace)
- [Pubblicare il modulo perimetrale nel centro per i partner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)
- [Distribuisci modulo IoT Edge](https://docs.microsoft.com/azure/iot-edge/quickstart-linux)  
