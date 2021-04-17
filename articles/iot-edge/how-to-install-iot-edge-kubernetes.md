---
title: Come installare IoT Edge in Kubernetes | Microsoft Docs
description: Informazioni su come installare IoT Edge in Kubernetes usando un ambiente cluster di sviluppo locale
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: iotedge-2018-06
ms.openlocfilehash: a5407c46be6726974a920b5dddd87639dc426d0d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587256"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Come installare IoT Edge in Kubernetes (anteprima)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

IoT Edge possibile eseguire l'integrazione con Kubernetes usandolo come livello di infrastruttura resiliente e a disponibilità elevata. Ecco dove questo supporto si inserisce in una soluzione di IoT Edge di alto livello:

![Introduzione al k8s](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>Un buon modello mentale per questa integrazione è pensare a Kubernetes come a un altro ambiente IoT Edge in cui possono essere eseguite applicazioni oltre a Linux e Windows.

## <a name="architecture"></a>Architettura 
In Kubernetes IoT Edge la definizione di risorsa personalizzata (CRD, *Custom Resource Definition)* per le distribuzioni di carichi di lavoro perimetrali. IoT Edge Agent assume il ruolo di  *controller CRD che* riconcilia lo stato desiderato gestito dal cloud con lo stato del cluster locale.

La durata del modulo viene gestita dall'utilità di pianificazione di Kubernetes, che mantiene la disponibilità del modulo e sceglie il relativo posizionamento. IoT Edge gestisce la piattaforma applicativa perimetrale in esecuzione in alto, riconciliando continuamente lo stato desiderato specificato nell'hub IoT con lo stato nel cluster perimetrale. Il modello applicativo è ancora il modello familiare basato IoT Edge moduli e route. Il controller IoT Edge Agent  esegue la conversione automatica IoT Edge del modello applicativo di Kubernetes nei costrutti nativi Kubernetes, ad esempio pod, distribuzioni, servizi e così via.

Di seguito è riportato un diagramma dell'architettura di alto livello:

![arch kubernetes](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Ogni componente della distribuzione perimetrale ha come ambito uno spazio dei nomi Kubernetes specifico del dispositivo, rendendo possibile la condivisione delle stesse risorse cluster tra più dispositivi perimetrali e le relative distribuzioni.

>[!NOTE]
>IoT Edge in Kubernetes è disponibile in [anteprima pubblica.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="tutorials-and-references"></a>Esercitazioni e riferimenti 

Per altre informazioni, IoT Edge esercitazioni dettagliate e riferimenti, vedere il minissato della documentazione sulla versione di anteprima di [Kubernetes.](https://aka.ms/edgek8sdoc)
