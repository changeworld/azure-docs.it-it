---
title: Aggiungere destinatari nella condivisione dati di Azure
description: Informazioni su come aggiungere destinatari a una condivisione dati esistente in una condivisione dati di Azure.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: a8e3dac620873ab11ae24395310066037f6d2df4
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680641"
---
# <a name="how-to-add-a-recipient-to-your-share"></a>Come aggiungere un destinatario alla condivisione

È possibile aggiungere un destinatario quando si crea una nuova condivisione o una condivisione esistente. Dall'interfaccia utente di condivisione dati di Azure è possibile aggiungere un destinatario usando il messaggio di posta elettronica di accesso di Azure dell'utente.  Dall'API è possibile usare una combinazione di utente/entità servizio e ID tenant. Quando viene specificato un ID tenant, l'invito può essere accettato solo in questo tenant. Inoltre, dall'API, è possibile creare un invito senza inviare un messaggio di posta elettronica al destinatario. 

## <a name="add-recipient-to-an-existing-share"></a>Aggiungi destinatario a una condivisione esistente

In condivisione dati di Azure passare alla condivisione inviata e selezionare la scheda **inviti** . Di seguito sono elencati tutti i destinatari degli inviti a questa condivisione di dati. Per aggiungerne una nuova, fare clic su **Aggiungi destinatario**.

![Screenshot Visualizza Aggiungi destinatario selezionato.](./media/how-to/how-to-add-recipients/add-recipient.png)

Un pannello viene visualizzato sul lato sinistro della pagina. Fare clic su **Aggiungi destinatario** e quindi immettere il messaggio di posta elettronica del nuovo destinatario nella riga vuota. Assicurarsi di usare l'indirizzo di posta elettronica di accesso di Azure del destinatario (usando l'alias di posta elettronica non funziona). 

![Screenshot mostra il riquadro Aggiungi destinatario in cui è possibile aggiungere e inviare un invito.](./media/how-to/how-to-add-recipients/add-recipient-side.png)

Fare clic su **Aggiungi e inviare un invito**. I nuovi destinatari riceveranno messaggi di posta elettronica di invito a questa condivisione.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su come [eliminare un invito a una condivisione](how-to-delete-invitation.md).
