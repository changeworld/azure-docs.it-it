---
title: Spedizione autogestita di Microsoft Azure Data Box Disk | Microsoft Docs
description: Descrive il flusso di lavoro della spedizione autogestita per i dispositivi Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: f512b4415f4a83e779a8f9bf790ba2806e3b05c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99526331"
---
# <a name="use-self-managed-shipping-for-azure-data-box-disk-in-the-azure-portal"></a>Usare la spedizione autogestita per Azure Data Box Disk nel portale di Azure

Questo articolo descrive le attività di spedizione autogestita per l'ordine, il ritiro e la consegna di un dispositivo Azure Data Box Disk. È possibile gestire Data Box Disk usando il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

La spedizione autogestita è disponibile come opzione quando si [ordina Azure Data Box Disk](data-box-disk-deploy-ordered.md). La spedizione autogestita è disponibile solo nelle aree geografiche seguenti:

* US Gov
* Regno Unito
* Europa occidentale
* Australia
* Giappone
* Singapore
* Corea del Sud
* Sudafrica
* India (anteprima)

## <a name="use-self-managed-shipping"></a>Usare la spedizione autogestita

Quando si effettua l'ordine di Data Box Disk, è possibile scegliere l'opzione di spedizione autogestita.

1. Nell'ordine di Azure Data Box Disk selezionare **+ Aggiungi indirizzo di spedizione** in **Dettagli contatto**.

   ![Screenshot dell'ordine guidato che mostra il passaggio dettagli contatto con l'opzione Aggiungi indirizzo di spedizione denominata.](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Quando si sceglie il tipo di spedizione, selezionare l'opzione **Spedizione autogestita**. Questa opzione è disponibile solo se ci si trova in una delle aree supportate, come descritto nei prerequisiti.

3. Dopo aver fornito l'indirizzo di spedizione, sarà necessario convalidarlo e completare l'ordine.

   ![Screenshot della finestra di dialogo per l'aggiunta dell'indirizzo di spedizione, con le opzioni di spedizione e il pulsante per l'aggiunta dell'indirizzo evidenziati.](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Una volta che il dispositivo è stato preparato ed è stata ricevuta una notifica di posta elettronica, è possibile pianificare un pickup. Nell'ordine di Azure Data Box Disk passare a **Panoramica**, quindi selezionare **Pianifica ritiro**.

   ![Ordine di un dispositivo Data Box per il ritiro](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. Seguire le istruzioni riportate in **Pianifica il ritiro per Azure**. Per poter ottenere il codice di autorizzazione, è necessario inviare un messaggio di posta elettronica a [adbops@microsoft.com](mailto:adbops@microsoft.com) per pianificare il ritiro del dispositivo dal data center dell'area.

   ![Pianificare il ritiro](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

6. Dopo aver pianificato il prelievo del dispositivo, è possibile visualizzare il codice di autorizzazione nel **prelievo pianificato per Azure**.

   ![Screenshot della finestra di dialogo Seleziona pianificazione per Azure con il codice di autorizzazione per la casella di testo di prelievo denominata.](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

   Prendere nota del codice di autorizzazione.

   In base ai requisiti di sicurezza, al momento della pianificazione, è necessario presentare il nome della persona che arriverà per la selezione.

   È anche necessario specificare i dettagli relativi agli utenti che accederanno al Data Center per la selezione. L'utente o il punto di contatto deve includere un ID foto approvato dal governo che verrà convalidato nel Data Center.

   La persona che sta raccogliendo il dispositivo deve avere anche il codice di autorizzazione. Il codice di autorizzazione è univoco per un pick-up o un elenco a discesa e viene convalidato nel Data Center.

7. L'ordine passa automaticamente allo stato **prelevato** dopo che il dispositivo è stato prelevato dal Data Center.

   ![Ritirato](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-ready-disk-01b.png)

8. Dopo il ritiro del dispositivo, è possibile copiare i dati nel Data Box Disk presso il proprio sito. Dopo aver completato la copia dei dati, è possibile preparare la spedizione del Data Box Disk.

   Al termine della copia dei dati, contattare le operazioni per pianificare un appuntamento per l'elenco a discesa. Dovrai condividere i dettagli della persona che arriva al Data Center per rilasciare i dischi. Il data center dovrà anche verificare il codice di autorizzazione al momento della consegna. Il codice di autorizzazione per l'elenco a discesa è reperibile nel portale di Azure in pianificazione, a **discesa**.

   > [!NOTE]
   > Non condividere il codice di autorizzazione tramite posta elettronica. Questa operazione viene verificata solo nel Data Center durante l'eliminazione.

9. Dopo aver ricevuto un appuntamento per l'elenco a discesa, l'ordine deve essere **pronto per la ricezione al centro dati di Azure** nella portale di Azure.

   ![Screenshot della finestra di dialogo Aggiungi indirizzo di spedizione con le opzioni in uscita e l'opzione Aggiungi indirizzo di spedizione richiamata.](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

10. Dopo che l'ID e il codice di autorizzazione sono stati verificati e il dispositivo è stato eliminato dal Data Center, è necessario **ricevere** lo stato dell'ordine.

    ![Ricezione completata](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. Dopo aver ricevuto il dispositivo, la copia dei dati continuerà. Al termine della copia, l'ordine è completato.

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Distribuire Azure Data Box Disk usando il portale di Azure](data-box-disk-quickstart-portal.md)
