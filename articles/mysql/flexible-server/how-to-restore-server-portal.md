---
title: Ripristinare un server flessibile di Database di Azure per MySQL con portale di Azure.
description: Questo articolo descrive come eseguire operazioni di ripristino nel server flessibile di Database di Azure per MySQL tramite portale di Azure
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 962a2cbdbcc238517616c9ade235eed9b8cae6f7
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502046"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Ripristino temporato di un database di Azure per MySQL - Server flessibile (anteprima) usando portale di Azure


> [!IMPORTANT]
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

Questo articolo illustra la procedura dettagliata per eseguire ripresi tempormente nel server flessibile usando i backup.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

-   È necessario disporre di un server flessibile di Database di Azure per MySQL.

## <a name="restore-to-the-latest-restore-point"></a>Ripristinare il punto di ripristino più recente

Seguire questa procedura per ripristinare il server flessibile usando un backup esistente meno recente.

1.  Nel [portale di Azure](https://portal.azure.com/)scegliere il server flessibile da cui si vuole ripristinare il backup.

2.  Fare **clic su Panoramica** nel pannello sinistro.

3.  Nella pagina di panoramica fare clic su **Ripristina**.

4.  Verrà visualizzata la pagina Di ripristino con un'opzione per scegliere tra **Punto di ripristino più recente** e Punto di ripristino personalizzato.

5.  Selezionare **Punto di ripristino più recente**.

6.  Specificare un nuovo nome di server nel **campo Ripristina nel nuovo server.**

    :::image type="content" source="./media/concept-backup-restore/restore-blade-latest.png" alt-text="Ora di ripristino meno recente":::

8.  Fare clic su **OK**.

9.  Verrà visualizzata una notifica che indica che l'operazione di ripristino è stata avviata.

## <a name="restoring-to-a-custom-restore-point"></a>Ripristino in un punto di ripristino personalizzato

Seguire questa procedura per ripristinare il server flessibile usando un backup esistente meno recente.

1.  Nel [portale di Azure](https://portal.azure.com/)scegliere il server flessibile da cui si vuole ripristinare il backup.

2.  Nella pagina di panoramica fare clic su **Ripristina**.

3.  Verrà visualizzata la pagina Di ripristino con un'opzione per scegliere tra Punto di ripristino meno recente e Punto di ripristino personalizzato.

4.  Scegliere **Punto di ripristino personalizzato.**

5.  Selezionare data e ora.

6.  Specificare un nuovo nome di server nel **campo Ripristina nel nuovo server.**

6.  Specificare un nuovo nome di server nel **campo Ripristina nel nuovo server.**

    :::image type="content" source="./media/concept-backup-restore/restore-blade-custom.png" alt-text="panoramica della visualizzazione":::

7.  Fare clic su **OK**.

8.  Verrà visualizzata una notifica che indica che l'operazione di ripristino è stata avviata.


## <a name="perform-post-restore-tasks"></a>Eseguire le attività post-ripristino
Al termine del ripristino, è necessario eseguire le attività seguenti per eseguire il backup e l'esecuzione di utenti e applicazioni:

- Se il nuovo server deve sostituire il server originale, reindirizzare i client e le applicazioni client al nuovo server.
- Assicurarsi che siano presenti regole di rete virtuale appropriate per la connessione degli utenti. Queste regole non vengono copiate dal server originale.
- Assicurarsi che siano disponibili gli account di accesso e le autorizzazioni a livello di database appropriati.
- Configurare gli avvisi in base alle esigenze per il nuovo server di ripristino.


## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulla [continuità aziendale](concepts-business-continuity.md)
