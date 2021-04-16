---
title: Risolvere i problemi IoT Plug and Play dispositivo
description: Guida ai passaggi consigliati per la risoluzione dei problemi per i partner che certificano un IoT Plug and Play dispositivo.
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 04/15/2021
ms.custom: template-how-to
ms.openlocfilehash: 9b406e489fb83083d47f01e1483160181601d518
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559188"
---
# <a name="troubleshoot-your-iot-plug-and-play-certification-project"></a>Risolvere i problemi del IoT Plug and Play di certificazione

Durante la fase di test connect & del progetto di certificazione IoT Plug and Play, è possibile che si insedino alcuni scenari che impediscono il superamento del test del servizio di certificazione Azure per IoT.

## <a name="prerequisites"></a>Prerequisiti

- È necessario aver eseguito l'accesso e avere un progetto per il dispositivo creato [nel portale Azure Certified Device .](https://certify.azure.com) Per altre informazioni, vedere [l'esercitazione](tutorial-01-creating-your-project.md).

## <a name="when-aics-tests-arent-passing"></a>Quando i test AICS non vengono superati

Il test AICS potrebbe non essere superato a causa di diverse cause. Seguire questa procedura per verificare la presenza di problemi comuni e risolvere i problemi del dispositivo.

1. Verificare che il codice del dispositivo sta impostando il payload dell'ID modello durante il provisioning DPS. Si tratta di un requisito che AICS deve convalidare il dispositivo.
1. È possibile visualizzare i log di telemetria delle esecuzioni dei test precedenti premendo il pulsante per identificare la causa `View Logs` dell'esito negativo del test. Sia la messaggistica di test che i dati non elaborati sono disponibili per la revisione.  

    ![Esaminare i dati di test](./media/images/review-logs.png)

1. In alcuni casi in cui i log indicano `Failed to get Digital Twin Model ID of device xx due to DeviceNotConnected` , provare a riavviare il dispositivo e a riavviare il processo di provisioning dei dispositivi.
1. Se i test automatizzati continuano a non riuscire, è possibile `request a manual review` sostituire i risultati. Verrà attivata una richiesta di **convalida manuale** con il team Azure Certified Device lavoro.  

    ![Richiedere la revisione manuale](./media/images/request-manual-review.png)

## <a name="when-you-see-passed-with-warnings"></a>Quando viene visualizzato "Superato con avvisi"

Durante l'esecuzione dei test, se si riceve un risultato di , significa che alcuni dati di telemetria non `Passed with warnings` sono stati ricevuti durante il periodo di test. Ciò potrebbe essere dovuto a una dipendenza dei dati di telemetria a intervalli di tempo più lunghi o a trigger esterni non disponibili. È possibile procedere con l'invio del dispositivo per la revisione, durante il quale il team di revisione determinerà se la **convalida** manuale è necessaria in futuro.

## <a name="when-you-need-help-with-the-model-repository"></a>Quando è necessaria assistenza per il repository dei modelli

Per IoT Plug and Play relativi al repository di modelli, vedere le linee guida [di Docs sul repository del modello di dispositivo.](https://docs.microsoft.com/azure/iot-pnp/concepts-model-repository)

## <a name="next-steps"></a>Passaggi successivi

Questa guida è utile per continuare con il percorso di IoT Plug and Play certificazione. Dopo aver superato AICS, è possibile procedere con le esercitazioni per inviare e pubblicare il dispositivo.

- [Esercitazione: Test del dispositivo](tutorial-03-testing-your-device.md)
