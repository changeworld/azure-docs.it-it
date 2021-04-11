---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: b5450e4846c3c49c89830ae65c50a95ee0c8d6eb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803327"
---
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Verificare la CI/CD di IoT Edge con la compilazione e le pipeline di rilascio

Per attivare un processo di compilazione, è possibile eseguire il push di un commit al repository del codice sorgente o attivarlo manualmente. In questa sezione si attiva manualmente la pipeline CI/CD per verificarne il funzionamento. Si verifica quindi che la distribuzione abbia esito positivo.

1. Dal menu riquadro a sinistra selezionare **pipeline** e aprire la pipeline di compilazione creata all'inizio di questo articolo.

2. È possibile attivare un processo di compilazione nella pipeline di compilazione selezionando il pulsante **Esegui pipeline** in alto a destra.

    ![Attivare manualmente la pipeline di compilazione usando il pulsante Esegui pipeline](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/manual-trigger.png)

3. Esaminare le impostazioni di **esecuzione della pipeline** . Selezionare quindi **Esegui**.

    ![Specificare le opzioni di esecuzione della pipeline e selezionare Esegui](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/run-pipeline-settings.png)

4. Selezionare il **processo dell'agente 1** per controllare lo stato dell'esecuzione. È possibile esaminare i log dell'output del processo selezionando il processo. 

    ![Esaminare l'output del log del processo](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/view-job-run.png)

5. Se la pipeline di compilazione viene completata correttamente, viene attivata una fase di **sviluppo** . La versione di **sviluppo** riuscita crea IOT Edge distribuzione per i dispositivi IOT Edge di destinazione.

    ![Rilascia a dev](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/pending-approval.png)

6. Fare clic su **dev** stage per visualizzare i log della versione.

    ![Log di versione](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/release-logs.png)

7. Se la pipeline ha esito negativo, iniziare esaminando i log. È possibile visualizzare i log passando al riepilogo dell'esecuzione della pipeline e selezionando il processo e l'attività. Se una determinata attività non riesce, controllare i log per l'attività. Per istruzioni dettagliate per la configurazione e l'uso dei log, vedere [esaminare i log per diagnosticare i problemi della pipeline](/azure/devops/pipelines/troubleshooting/review-logs).
