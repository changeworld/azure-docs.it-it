---
title: Programma Azure Certified Device-Esercitazione-test del dispositivo
description: Guida dettagliata per il test del dispositivo con il servizio AICS nel portale per i dispositivi Azure Certified
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: ef82d44ef44189c0430ba9789baf3279fbe49a9c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310955"
---
# <a name="tutorial-test-and-submit-your-device"></a>Esercitazione: testare e inviare il dispositivo

La fase principale successiva del processo di certificazione (anche se può essere completata prima di aggiungere i dettagli del dispositivo) comporta il test del dispositivo. Tramite il portale verrà usato il servizio di certificazione Azure AICS per dimostrare le prestazioni del dispositivo in base ai requisiti di certificazione. Una volta superata la fase di test, si invierà il dispositivo per la revisione e l'approvazione finali da parte del team di certificazione di Azure.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Connettere il dispositivo all'hub di Internet delle cose usando il servizio Device provisioning (DPS)
> * Testare il dispositivo in base ai programmi di certificazione selezionati
> * Inviare il dispositivo per la revisione da parte del team di certificazione di Azure

## <a name="prerequisites"></a>Prerequisiti

- È necessario aver eseguito l'accesso e avere un progetto per il dispositivo creato nel [portale per i dispositivi Azure Certified](https://certify.azure.com). Per ulteriori informazioni, vedere l' [esercitazione](tutorial-01-creating-your-project.md).
- Opzionale Si consiglia di preparare il dispositivo e verificarne manualmente le prestazioni in base ai requisiti di certificazione. Questo è dovuto al fatto che se si desidera eseguire di nuovo il test con codice del dispositivo o programma di certificazione diverso, sarà necessario creare un nuovo progetto.

## <a name="connecting-your-device-using-dps"></a>Connessione del dispositivo tramite DPS

Tutti i dispositivi certificati sono necessari per dimostrare la possibilità di connettersi all'hub delle cose usando DPS. I passaggi seguenti illustrano come connettere correttamente il dispositivo per il test nel portale.

1. Per iniziare la fase di test, selezionare il `Connect & test` collegamento nella pagina di riepilogo del progetto:  

    ![Collegamento di connessione e test](./media/images/connect-and-test-link.png)

1. A seconda delle certificazioni selezionate, i test richiesti verranno visualizzati nella pagina "Connetti & test". Esaminarli per assicurarsi che l'applicazione per il programma di certificazione sia corretta.  

    ![Pagina Connetti e prova](./media/images/connect-and-test.png)

1. Connettere il dispositivo all'hub delle cose usando il servizio Device provisioning (DPS). DPS supporta opzioni di connettività di chiavi simmetriche, certificazione X. 509 e un Trusted Platform Module (TPM). Questa operazione è necessaria per tutte le certificazioni.

    - *Per altre informazioni sulla connessione del dispositivo all'hub di Azure Internet con DPS, vedere [Panoramica dei dispositivi di provisioning](../iot-dps/about-iot-dps.md "Panoramica del servizio Device provisioning").*
    
1. Se si usano chiavi simmetriche, viene richiesto di configurare il DPS con l'ambito ID DPS, l'ID dispositivo, la chiave di autenticazione e l'endpoint DPS specificati. In caso contrario, verrà richiesto di fornire un certificato X. 509 o una chiave di verifica dell'autenticità.

1. Dopo aver configurato il dispositivo con DPS, confermare la connessione facendo clic sul `Connect` pulsante nella parte inferiore della pagina. Al completamento della connessione, è possibile passare alla fase di test facendo clic sul `Next` pulsante.  

    ![Connetti e test connessi](./media/images/connected.png)

## <a name="testing-your-device"></a>Test del dispositivo

Dopo aver connesso il dispositivo a AICS, è ora possibile eseguire i test di certificazione specifici del programma di certificazione che si sta applicando.

1. **Per la certificazione dei dispositivi certificati di Azure**: nella scheda ' selezionare la funzionalità del dispositivo ', è possibile esaminare e selezionare i test che si desidera eseguire nel dispositivo.
1. **Per la certificazione Internet degli plug and Play**: esaminare attentamente i parametri che verranno controllati durante il test dichiarato nel modello del dispositivo.
1. **Per la certificazione gestita da Edge**: non sono necessari passaggi aggiuntivi oltre alla dimostrazione della connettività.
1. Una volta completate le preparazioni necessarie per il programma di certificazione specificato, selezionare `Next` per procedere con la fase di test.
1. Selezionare `Run tests` nella pagina per avviare l'esecuzione di AICS con il dispositivo.
1. Dopo aver ricevuto una notifica di superamento dei test, selezionare `Finish` per tornare alla pagina di riepilogo.

![Test superato](./media/images/test-pass.png)

7. Per altre domande o per richiedere assistenza per la risoluzione dei problemi relativi a AICS, vedere la guida alla risoluzione dei problemi.

> [!NOTE]
> Anche se potrai completare il processo di certificazione online per i Plug and Play e gestiti da Edge senza dover inviare il tuo dispositivo per la revisione manuale, potresti contattare un membro del team di Azure Certified Device per ulteriore convalida dei dispositivi oltre a quanto testato tramite il servizio di automazione.

## <a name="submitting-your-device-for-review"></a>Invio del dispositivo per la revisione

Dopo aver completato tutti i campi obbligatori nella sezione "dettagli dispositivo" e aver superato il test automatizzato nel processo "Connetti & test", è ora possibile inviare una notifica al team di dispositivi certificati di Azure che si è pronti per la revisione della certificazione.

1. Selezionare `Submit for review` nella pagina di riepilogo del progetto:  

    ![Verifica e certifica il collegamento](./media/images/review-and-certify.png)

1. Confermare l'invio nella finestra popup. Dopo che un dispositivo è stato inviato, tutti i dettagli del dispositivo saranno di sola lettura fino a quando non verrà richiesta la modifica. Vedere [come modificare le informazioni sul dispositivo dopo la pubblicazione](./how-to-edit-published-device.md).  

    ![Finestra di dialogo Avvia revisione della certificazione](./media/images/start-certification-review.png)

1. Una volta inviato il progetto, la pagina di riepilogo del progetto indicherà che il progetto è del `Under Certification Review` team di certificazione di Azure:  

    ![In Revisione](./media/images/review-and-certify-under-review.png)

1. Entro 5-7 giorni lavorativi, è previsto un messaggio di posta elettronica dal team di certificazione di Azure all'indirizzo fornito nel profilo aziendale per quanto riguarda lo stato dell'invio del dispositivo.

    - Invio approvato  
        Una volta che il progetto è stato rivisto e approvato, si riceverà un messaggio di posta elettronica. Il messaggio di posta elettronica includerà un set di file, tra cui la notifica del dispositivo Azure Certified, le linee guida per l'utilizzo delle notifiche e altre informazioni su come amplificare il messaggio di certificazione del dispositivo. Congratulazioni!

    - Invio in sospeso  
        Nel caso in cui il progetto non sia approvato, sarà possibile apportare modifiche ai dettagli del progetto e quindi inviare nuovamente il dispositivo per la certificazione una volta pronto. Verrà inviato un messaggio di posta elettronica con informazioni sul motivo per cui il progetto non è stato approvato e i passaggi da inviare nuovamente per la certificazione.

## <a name="next-steps"></a>Passaggi successivi

Congratulazioni! Il dispositivo ha superato correttamente tutti i test ed è stato approvato tramite il programma Azure Certified Device. È ora possibile pubblicare il dispositivo nel catalogo dei dispositivi Azure Certified, in cui i clienti possono acquistare i prodotti in modo sicuro in base alle prestazioni con Azure.
> [!div class="nextstepaction"]
> [Esercitazione: pubblicazione del dispositivo](tutorial-04-publishing-your-device.md)

