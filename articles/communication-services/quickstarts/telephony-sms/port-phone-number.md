---
title: 'Guida introduttiva: porta un numero di telefono in servizi di comunicazione di Azure'
description: Informazioni su come trasferire un numero di telefono nella risorsa di servizi di comunicazione
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 03/20/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: b4357b8d4fe1d7184fc2dcfab2008dc6e0f334fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729931"
---
# <a name="quickstart-port-a-phone-number"></a>Guida introduttiva: porta un numero di telefono

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Per iniziare a usare i servizi di comunicazione di Azure, è necessario trasferire il numero di telefono nella risorsa servizi di comunicazione di Azure. I numeri con numero verde e geografico basati sul Stati Uniti sono idonei per il porting. Per ulteriori informazioni sui tipi di numero di telefono, vedere la [documentazione concettuale relativa al numero di telefono](../../concepts/telephony-sms/plan-solution.md).

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Una risorsa attiva di Servizi di comunicazione.](../create-communication-resource.md)

## <a name="gather-your-azure-resource-details"></a>Raccogliere i dettagli delle risorse di Azure

Prima di avviare una richiesta di porta, passare alla portale di Azure e selezionare la risorsa servizi di comunicazione. Con il `Overview` riquadro selezionato, fare clic sul `JSON View` collegamento nell'angolo superiore destro:

:::image type="content" source="./media/number-port/json-view.png" alt-text="Screenshot che mostra la selezione della visualizzazione JSON.":::

Registrare l' **ID Azure** della risorsa e l' **ID risorsa non modificabile**:

:::image type="content" source="./media/number-port/json-properties.png" alt-text="Screenshot che mostra la selezione delle proprietà JSON.":::

## <a name="initiate-the-port-request"></a>Avviare la richiesta di porta

I numeri con numero verde e geografico basati sul Stati Uniti sono idonei per il porting. Per inviare la richiesta di porta, usare uno dei moduli seguenti:

- Per i numeri con numero verde: [richiesta di porta con numero verde](https://aka.ms/acs-port-form-tollfree)
- Per i numeri geografici in base alla [richiesta della porta US: numero geografico](https://aka.ms/acs-port-form-geographic)

Al termine, inviare il modulo di richiesta di porta completato a acsporting@microsoft.com . Verificare che la riga dell'oggetto del messaggio di posta elettronica inizi con "richiesta ACS Port-In".

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come:

> [!div class="checklist"]
> * Acquisire i metadati delle risorse dei servizi di comunicazione
> * Inviare una richiesta per la porta del numero di telefono

> [!div class="nextstepaction"]
> [Inviare un SMS](../telephony-sms/send.md)
> [Introduzione alle chiamate](../voice-video-calling/getting-started-with-calling.md)
