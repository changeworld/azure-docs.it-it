---
title: Restituire il dispositivo Pro Azure Stack Edge | Microsoft Docs
description: Informazioni su come cancellare i dati e restituire il dispositivo Azure Stack Edge Pro, quindi eliminare la risorsa associata al dispositivo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.openlocfilehash: cb11d7d3b2da9ab793cb18814e4021ea7afeb806
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443591"
---
# <a name="return-your-azure-stack-edge-pro-device"></a>Restituire il dispositivo Pro Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Questo articolo descrive come eliminare i dati e restituire il dispositivo Azure Stack Edge Pro. Dopo avere restituito il dispositivo, è anche possibile eliminare la risorsa associata al dispositivo.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
>
> * Cancellare i dati dai dischi dati del dispositivo
> * Avviare la restituzione del dispositivo nel portale di Azure
> * Imballare il dispositivo e pianificare il ritiro
> * Eliminare la risorsa nel portale di Azure

## <a name="erase-data-from-the-device"></a>Cancellare i dati dal dispositivo

Per cancellare i dati dai dischi dati del dispositivo, è necessario reimpostare il dispositivo.

Prima di procedere con la reimpostazione, creare una copia dei dati locali del dispositivo, se necessario. È possibile copiare i dati del dispositivo in un contenitore di Archiviazione di Azure. 

È possibile avviare il ritorno del dispositivo anche prima della reimpostazione del dispositivo.

È possibile reimpostare il dispositivo nell'interfaccia utente Web locale o in PowerShell. Per istruzioni su PowerShell, vedere [reimpostare il dispositivo](./azure-stack-edge-connect-powershell-interface.md#reset-your-device).

[!INCLUDE [Reset data from the device](../../includes/azure-stack-edge-device-reset.md)]

> [!NOTE]
> - Se si sta facendo un cambio di dispositivo o si sta eseguendo l'aggiornamento a un nuovo dispositivo, è consigliabile reimpostare il dispositivo solo dopo aver ricevuto quello nuovo.
> - La reimpostazione del dispositivo elimina solo tutti i dati locali dal dispositivo. I dati presenti nel cloud non vengono eliminati e sono soggetti ad [addebiti](https://azure.microsoft.com/pricing/details/storage/). Questi dati devono essere eliminati separatamente usando uno strumento di gestione dell'archiviazione nel cloud come [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="initiate-device-return"></a>Avviare il ritorno del dispositivo

Per iniziare il processo di restituzione, seguire questa procedura.

1. Passare alla risorsa Pro/Data Box Gateway di Azure Stack Edge in portale di Azure. Nella **Panoramica** passare alla barra dei comandi nel riquadro di destra e selezionare **return Device**. 

    ![Restituisci dispositivo 1](media/azure-stack-edge-return-device/return-device-1.png)  

2. Nel pannello **Restituisci dispositivo** , in **Dettagli di base**:

    1. Fornire il numero di serie del dispositivo. Per ottenere il numero di serie del dispositivo, passare all'interfaccia utente Web locale del dispositivo e quindi passare a **Panoramica**.  
    
       ![Numero di serie 1 del dispositivo](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    2. Immettere il numero di tag del servizio. Il numero di tag del servizio è un identificatore con cinque o più caratteri, che è univoco per il dispositivo. Il tag del servizio si trova nell'angolo in basso a destra del dispositivo (quando si fa fronte al dispositivo). Estrarre il tag informazioni (si tratta di un pannello di etichette di scorrimento). Questo pannello contiene informazioni di sistema, ad esempio tag di servizio, NIC, indirizzo MAC e così via. 
    
       ![Numero di tag del servizio 1](media/azure-stack-edge-return-device/service-tag-number-1.png)

    3. Nell'elenco a discesa scegliere un motivo per la restituzione.

       ![Restituisci dispositivo 2](media/azure-stack-edge-return-device/return-device-2.png) 

3. In **Dettagli spedizione**:

    1. Specificare il nome, il nome della società e l'indirizzo completo della società. Immettere un telefono dell'ufficio, inclusi il prefisso e un ID di posta elettronica per la notifica.
    2. Se serve una scatola di spedizione, è possibile richiederla. Rispondere **Sì** alla domanda **Need an empty box to return** (Serve una scatola vuota per la restituzione).

    ![Restituisci dispositivo 3](media/azure-stack-edge-return-device/return-device-3.png)

4. Esaminare le **condizioni** per la privacy, quindi selezionare la casella di controllo nella nota che è stata esaminata e si accettano le condizioni per la privacy.

5. Selezionare **Avvia la restituzione**.

    ![Restituisci dispositivo 4](media/azure-stack-edge-return-device/return-device-4.png) 

6. Dopo aver acquisito i dettagli restituiti dal dispositivo, è possibile inviare una notifica al team operativo di Azure Stack Edge Pro tramite un messaggio di posta elettronica. È possibile usare l'applicazione di posta elettronica presumendo che l'applicazione di posta elettronica sia installata e configurata. È anche possibile copiare i dati per creare e inviare un messaggio di posta elettronica.

    ![Restituisci dispositivo 5](media/azure-stack-edge-return-device/return-device-5.png) 

7. Una volta che il team operativo di Azure Stack Edge Pro riceve il messaggio di posta elettronica, invierà un'etichetta di spedizione inversa. Quando si riceve questa etichetta, è possibile pianificare il ritiro del dispositivo con il vettore. 

## <a name="schedule-a-pickup"></a>Pianificare un ritiro

Per pianificare un prelievo, seguire questa procedura.

1. Arrestare il dispositivo. Nell'interfaccia utente Web locale passare a **Manutenzione > Power settings** (Impostazioni di alimentazione).
2. Selezionare **Arresta**. Quando viene chiesta conferma, fare clic su **Sì** per continuare. Per altre informazioni, vedere [Gestire l'avvio/arresto](../databox-gateway/data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Scollegare i cavi di alimentazione e rimuovere tutti i cavi di rete dal dispositivo.
4. Preparare l'imballaggio di spedizione usando la propria scatola o la scatola vuota ricevuta da Azure. Collocare nella scatola il dispositivo e i cavi di alimentazione in dotazione con il dispositivo.
5. Applicare l'etichetta di spedizione ricevuta da Azure sull'imballaggio.
6. Pianificare un ritiro con il corriere di zona. Se il dispositivo viene restituito negli Stati Uniti, il corriere potrebbe essere UPS o FedEx. Per pianificare un ritiro con UPS:

    1. Chiamare il servizio UPS locale (numero verde specifico del paese o area).
    2. Durante la chiamata specificare il numero di tracciabilità della spedizione di reso indicato sull'etichetta stampata.
    3. Se il numero di tracciabilità non è racchiuso tra virgolette, UPS richiederà di pagare un addebito aggiuntivo durante il ritiro.

    Invece di pianificare il prelievo, è anche possibile eliminare il Azure Stack Edge Pro nel percorso di rilascio più vicino.

## <a name="delete-the-resource"></a>Eliminare la risorsa

Dopo la ricezione nel data center di Azure, il dispositivo viene controllato per escludere la presenza di danni o segnali di manomissione.

- Se il dispositivo arriva intatto ed è in forma corretta, il contatore della fatturazione si interrompe per la risorsa. Il team operativo di Azure Stack Edge Pro contatterà l'utente per verificare che il dispositivo sia stato restituito. È quindi possibile eliminare la risorsa associata al dispositivo nel portale di Azure.
- Se il dispositivo arriva notevolmente danneggiato, è possibile che vengano applicate ammende. Per altre informazioni, vedere le [domande frequenti sul dispositivo perso o danneggiato](https://azure.microsoft.com/pricing/details/databox/edge/) e le [condizioni d'uso del servizio](https://www.microsoft.com/licensing/product-licensing/products).  


È possibile eliminare il dispositivo nel portale di Azure:

- Dopo aver inserito un ordine e prima che il dispositivo venga preparato da Microsoft.
- Dopo aver restituito un dispositivo a Microsoft, il team operativo di Azure Stack Edge Pro ha chiamato per confermare che il dispositivo è stato restituito. Il team operativo non chiama finché il dispositivo restituito non supera il controllo fisico nel Data Center di Azure.

Se il dispositivo è stato attivato in un'altra sottoscrizione o località, Microsoft sposterà l'ordine nella nuova sottoscrizione o località entro un giorno lavorativo. Una volta spostato l'ordine, è possibile eliminare questa risorsa.


Per eliminare il dispositivo e la risorsa nel portale di Azure, seguire questa procedura.

1. Nel portale di Azure passare alla risorsa e quindi a **Panoramica**. Nella barra dei comandi selezionare **Elimina**.

    ![Selezionare Elimina](media/azure-stack-edge-return-device/delete-resource-1.png)

2. Nel pannello **Elimina dispositivo** digitare il nome del dispositivo da eliminare e selezionare **Elimina**.

    ![Conferma dell'eliminazione](media/azure-stack-edge-return-device/delete-resource-2.png)

Si riceverà una notifica dopo che il dispositivo e la risorsa associata saranno stati eliminati correttamente.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [ottenere una sostituzione Azure stack dispositivo Edge Pro](azure-stack-edge-replace-device.md).