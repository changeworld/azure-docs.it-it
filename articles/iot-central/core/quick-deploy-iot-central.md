---
title: "Avvio rapido: Creare un'applicazione Azure IoT Central | Microsoft Docs"
description: "Avvio rapido: Creare una nuova applicazione Azure IoT Central. Creare l'applicazione usando il piano tariffario gratuito o uno dei piani tariffari standard."
author: viv-liu
ms.author: viviali
ms.date: 12/28/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: a1492593c24c2bc350205536006b648608cc5e46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101712641"
---
# <a name="quickstart---create-an-azure-iot-central-application"></a>Avvio rapido: Creare un'applicazione Azure IoT Central

Questo argomento di avvio rapido illustra come creare un'applicazione Azure IoT Central.

## <a name="prerequisite"></a>Prerequisito 

È necessaria una sottoscrizione di Azure con accesso *collaboratore* .

## <a name="create-an-application"></a>Creare un'applicazione

Passare al sito per la [creazione di app Azure IoT Central](https://aka.ms/iotcentral). Eseguire quindi l'accesso con un account Microsoft personale, aziendale o dell'istituto di istruzione.

Creare una nuova applicazione dall'elenco di modelli di IoT Central pertinenti per il settore per iniziare rapidamente oppure partire da zero usando un modello **App personalizzate**. In questa guida di avvio rapido si usa il modello **Applicazione personalizzata**.

Per creare una nuova applicazione Azure IoT Central dal modello **Applicazione personalizzata**:

1. Passare alla pagina **Crea**:

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-new-application.png" alt-text="Pagina Crea un'applicazione IoT":::

1. Scegliere **App personalizzate** e assicurarsi che sia selezionato il modello **Applicazione personalizzata**.

1. Azure IoT Central suggerisce automaticamente il **nome di un'applicazione** in base al modello di applicazione selezionato. È possibile usare questo nome oppure immettere un nome descrittivo per l'applicazione.

1. Azure IoT Central genera anche un prefisso **URL di applicazione** univoco in base al nome dell'applicazione. Questo URL viene usato per accedere all'applicazione. Sostituire questo prefisso di URL con un qualcosa di più facile da ricordare.

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-custom.png" alt-text="Pagina Creare un'applicazione di Azure IoT Central":::

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-billinginfo.png" alt-text="Informazioni sulla fatturazione di Azure IoT Central":::

    > [!Tip]
    > Se nella pagina precedente si è scelto **App personalizzata**, si vedrà l'elenco a discesa **Modello di applicazione**. Nell'elenco a discesa potrebbero essere visualizzati anche altri modelli resi disponibili dall'organizzazione.

1. Scegliere di creare questa applicazione usando il piano tariffario della versione di valutazione gratuita valida 7 giorni oppure uno dei piani tariffari standard:

    - Le applicazioni con il piano *gratuito* sono gratuite per sette giorni e supportano fino a cinque dispositivi. È possibile convertirle per l'uso di un piano tariffario standard in qualsiasi momento prima della scadenza.
        > [!NOTE]
        > Per le applicazioni create con il piano *gratuito* non sono necessarie sottoscrizioni di Azure e pertanto non sono disponibili nella sottoscrizione di azure nella portale di Azure. È possibile visualizzare e gestire solo app gratuite dal portale di IoT Central.          
    - Le applicazioni create usando il piano *Standard* vengono fatturate in base ai singoli dispositivi; è possibile scegliere il piano tariffario **Standard 0**, **Standard 1** o **Standard 2** con i primi due dispositivi gratuiti. Per altre informazioni sui piani tariffari gratuito e standard, vedere la pagina sui [prezzi di Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). Se si crea un'applicazione con un piano tariffario standard, è necessario selezionare una *directory*, una *sottoscrizione di Azure* e una *località*:
        - La *directory* è l'istanza di Azure Active Directory in cui creare l'applicazione. Un'istanza di Azure Active Directory contiene le identità degli utenti, le credenziali e altre informazioni dell'organizzazione. Se Azure Active Directory non è disponibile, ne viene creata automaticamente un'istanza quando si crea una sottoscrizione di Azure.
        - Una *sottoscrizione di Azure* consente di creare istanze dei servizi di Azure. IoT Central effettua il provisioning delle risorse nella sottoscrizione. Se non si ha una sottoscrizione di Azure, è possibile crearne una gratuitamente nella [pagina di iscrizione ad Azure](https://aka.ms/createazuresubscription). Dopo aver creato la sottoscrizione di Azure, tornare nella pagina **Nuova applicazione**. La nuova sottoscrizione è ora inclusa nell'elenco a discesa **Sottoscrizione di Azure** (Sottoscrizione di Azure).
        - La *località* è l'[area geografica](https://azure.microsoft.com/global-infrastructure/geographies/) in cui si vuole creare l'applicazione. Per ottenere prestazioni ottimali, è in genere consigliabile scegliere la località fisicamente più vicina ai dispositivi. Dopo aver scelto una località, non è possibile spostare l'applicazione in un'altra località in un secondo momento.

1. Esaminare i termini e le condizioni e selezionare **Crea** nella parte inferiore della pagina. Dopo alcuni minuti, l'applicazione IoT Central è pronta per l'uso:

    :::image type="content" source="media/quick-deploy-iot-central/iotcentral-application.png" alt-text="Applicazione Azure IoT Central":::

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata creata un'applicazione IoT Central. Ecco il passaggio successivo suggerito per proseguire con la formazione su IoT Central:

> [!div class="nextstepaction"]
> [Aggiungere un dispositivo simulato all'applicazione Azure IoT Central](./quick-create-simulated-device.md)

Per gli sviluppatori di dispositivi che desiderano approfondire il codice, il passaggio successivo suggerito consiste nel:
> [!div class="nextstepaction"]
> [Creare e connettere un'applicazione client all'applicazione Azure IoT Central](./tutorial-connect-device.md)