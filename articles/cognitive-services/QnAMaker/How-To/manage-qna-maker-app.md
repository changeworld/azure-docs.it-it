---
title: Gestire QnA Maker app-QnA Maker
description: QnA Maker consente a più utenti di collaborare a una Knowledge Base. QnA Maker offre una funzionalità per migliorare la qualità della Knowledge base con l'apprendimento attivo. Uno può esaminare, accettare o rifiutare e aggiungere senza rimuovere o modificare le domande esistenti.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 652489e8de68d61707fd419843f0dbb2ffd83754
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99987942"
---
# <a name="manage-qna-maker-app"></a>Gestisci app QnA Maker

QnA Maker consente di collaborare con autori e editor di contenuto diversi offrendo una funzionalità per limitare l'accesso al collaboratore in base al ruolo del collaboratore.
Scopri di più sui [concetti di autenticazione QnA Maker collaboratore](../Concepts/role-based-access-control.md).

## <a name="add-azure-role-based-access-control-azure-rbac"></a>Aggiungere il controllo degli accessi in base al ruolo di Azure (RBAC di Azure)

QnA Maker consente a più utenti di collaborare su tutte le Knowledge base nella stessa risorsa QnA Maker. Questa funzionalità viene fornita con il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../../role-based-access-control/role-assignments-portal.md).

## <a name="access-at-the-qna-maker-resource-level"></a>Accesso a livello di risorsa QnA Maker

Non è possibile condividere una particolare Knowledge base in un servizio QnA Maker. Se si desidera un controllo di accesso più granulare, valutare la possibilità di distribuire le Knowledge base su risorse QnA Maker diverse, quindi aggiungere ruoli a ogni risorsa.

## <a name="add-a-role-to-a-resource"></a>Aggiungere un ruolo a una risorsa

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Aggiungere un account utente alla risorsa QnA Maker

Nei passaggi seguenti viene utilizzato il ruolo Collaboratore, ma è possibile aggiungere uno dei [ruoli](../reference-role-based-access-control.md) utilizzando questa procedura

1. Accedere al portale di [Azure](https://portal.azure.com/) e passare alla risorsa QnA Maker.

    ![Elenco di risorse QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. Passare alla scheda **Controllo di accesso (IAM)**.

    ![Controllo di accesso (IAM) di QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. Selezionare **Aggiungi**.

    ![Aggiunta di un ruolo IAM di QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. Selezionare un ruolo dall'elenco seguente:

    |Ruolo|
    |--|
    |Proprietario|
    |Collaboratore|
    |Lettore di QnA Maker di servizi cognitivi|
    |Editor QnA Maker di servizi cognitivi|
    |Utente Servizi cognitivi|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker IAM Aggiungi ruolo.":::

1. Immettere l'indirizzo di posta elettronica dell'utente e fare clic su **Salva**.

    ![Aggiunta di un indirizzo e-mail IAM di QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>Visualizza QnA Maker Knowledge base

Quando la persona che ha condiviso il servizio QnA Maker con i log nel [portale di QnA Maker](https://qnamaker.ai), può visualizzare tutte le Knowledge base nel servizio in base al proprio ruolo.

Quando si seleziona una Knowledge base, il relativo ruolo corrente su tale risorsa QnA Maker è visibile accanto al nome della Knowledge base.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="Screenshot della Knowledge base in modalità di modifica con il nome del ruolo racchiuso tra parentesi accanto al nome della Knowledge base nell'angolo superiore sinistro della pagina Web.":::

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare una knowledge base](./manage-knowledge-bases.md)
