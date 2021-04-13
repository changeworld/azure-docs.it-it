---
title: Come modificare il dispositivo Azure Certified pubblicato
description: Guida per modificare le informazioni sul dispositivo dopo aver ottenuto la certificazione e pubblicato il dispositivo tramite il programma Azure Certified Device.
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 14a29d438103f07dd35b3a3380b7cc094f215824
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304444"
---
# <a name="edit-your-published-device"></a>Modificare il dispositivo pubblicato

Una volta che il dispositivo è stato certificato e pubblicato nel catalogo dei dispositivi Azure Certified, potrebbe essere necessario aggiornare i dettagli del dispositivo. Questo problema può essere dovuto a un aggiornamento dell'elenco di server di distribuzione, alle modifiche apportate agli URL della pagina di acquisto o agli aggiornamenti delle specifiche hardware, ad esempio la versione del sistema operativo o l'aggiunta di un nuovo componente. Usando il portale per dispositivi Azure Certified, è possibile aggiornare facilmente le informazioni sul dispositivo senza rimuovere il prodotto dal catalogo.

## <a name="prerequisites"></a>Prerequisiti

- È necessario aver eseguito l'accesso e avere un progetto **approvato** per il dispositivo nel [portale per i dispositivi Azure Certified](https://certify.azure.com). Se non si dispone di un dispositivo certificato, è possibile visualizzare questa [esercitazione](tutorial-01-creating-your-project.md) per iniziare.

## <a name="editing-your-published-project"></a>Modifica del progetto pubblicato

Nel riepilogo del progetto si noterà che il progetto è in modalità di sola lettura perché è già stato esaminato e accettato. Per apportare modifiche, è necessario richiedere una modifica al progetto e fare in modo che l'aggiornamento venga riapprovato dal team di certificazione di Azure.

1. Fare clic sul `Request Metadata Edit` pulsante nella parte superiore della pagina  

    ![Aggiornamento dei metadati della richiesta](./media/images/request-metadata-edit.png)

1. Confermare la notifica nella pagina in cui verrà richiesto di inviare il prodotto per la revisione dopo la modifica.
    > [!NOTE]
    > Confermando questa modifica, il dispositivo **non** verrà rimosso dal catalogo dei dispositivi Azure Certified, se è già stato pubblicato. La versione precedente del prodotto rimarrà nel catalogo fino a quando non sarà stato ripubblicato il dispositivo.

1. Una volta riconoscibile questo avviso, è possibile modificare i dettagli del dispositivo. Assicurarsi di lasciare una nota nella `Comments for Reviewer` sezione di `Device Details` che cosa è stato modificato.

    ![Nota della modifica dei metadati](./media/images/edit-notes.png)

1. Nella pagina di riepilogo del progetto fare clic su per fare in `Submit for review` modo che le modifiche vengano approvate dal team di certificazione di Azure.
1. Dopo la revisione e l'approvazione delle modifiche, è possibile ripubblicare le modifiche nel catalogo tramite il portale (vedere l' [esercitazione](./tutorial-04-publishing-your-device.md)).

## <a name="next-steps"></a>Passaggi successivi

Il dispositivo è stato modificato correttamente nel catalogo dei dispositivi Azure Certified. È possibile controllare le modifiche nel catalogo o certificare un altro dispositivo.
- [Catalogo dei dispositivi Azure Certified](https://devicecatalog.azure.com/)
- [Introduzione alla certificazione di un dispositivo](./tutorial-01-creating-your-project.md)
