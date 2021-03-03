---
title: Eseguire la migrazione di un'applicazione IoT Central di Azure V2 a V3 | Microsoft Docs
description: Come amministratore, informazioni su come eseguire la migrazione dell'applicazione IoT Central di Azure V2 alla versione V3
author: troyhopwood
ms.author: troyhop
ms.date: 01/18/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 735ad7ad9ded6baded59ab3f08e239d1c8376b74
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101702726"
---
# <a name="migrate-your-v2-iot-central-application-to-v3"></a>Eseguire la migrazione dell'applicazione IoT Central V2 a V3

*Questo articolo si applica agli amministratori.*

Attualmente, quando si crea una nuova applicazione IoT Central, si tratta di un'applicazione v3. Se in precedenza è stata creata un'applicazione, a seconda del momento in cui è stata creata, è possibile che sia V2. Questo articolo descrive come eseguire la migrazione di una versione V2 a un'applicazione v3 per assicurarsi di usare le funzionalità di IoT Central più recenti.

Per informazioni su come identificare la versione di un'applicazione IoT Central, vedere [informazioni sull'applicazione](howto-get-app-info.md).

I passaggi per eseguire la migrazione di un'applicazione da V2 a V3 sono:

1. Creare una nuova applicazione v3 dall'applicazione V2.
1. Configurare l'applicazione v3.
1. Eliminare l'applicazione V2.

## <a name="create-a-new-v3-application"></a>Creare una nuova applicazione v3

Utilizzare la migrazione guidata per creare una nuova applicazione v3.

IoT Central non supporta la migrazione a un'applicazione v3 esistente. Per spostare automaticamente i dispositivi esistenti, utilizzare la migrazione guidata per creare l'applicazione v3.

Migrazione guidata:

- Crea una nuova applicazione v3.
- Verifica la compatibilità dei modelli di dispositivo con v3.
- Copia tutti i modelli di dispositivo nella nuova applicazione v3.
- Copia tutti gli utenti e le assegnazioni di ruolo nella nuova applicazione v3.

> [!NOTE]
> Per garantire la compatibilità dei dispositivi con V3, i tipi primitivi nel modello di dispositivo diventano proprietà dell'oggetto. Non è necessario apportare modifiche al codice del dispositivo.

Per eseguire la migrazione di un'applicazione a V3, è necessario essere un amministratore.

1. Nel menu **Amministrazione** selezionare **Esegui migrazione a un'applicazione v3**:

    :::image type="content" source="media/howto-migrate/migrate-menu.png" alt-text="Screenshot che mostra la migrazione guidata applicazione":::

1. Immettere un nuovo **nome dell'applicazione** e, facoltativamente, modificare l'  **URL** generato automaticamente. L'URL non può corrispondere all'URL dell'applicazione V2 corrente. Tuttavia, è possibile modificare l'URL in un secondo momento dopo aver eliminato l'applicazione V2.

1. Selezionare **Crea una nuova app V3**.

    :::image type="content" source="media/howto-migrate/create-app.png" alt-text="Screenshot che mostra le opzioni della migrazione guidata applicazione":::

    A seconda del numero e della complessità dei modelli di dispositivo, il completamento di questo processo potrebbe richiedere diversi minuti.

    > [!Warning]
    > La creazione dell'applicazione v3 avrà esito negativo se un modello di dispositivo contiene campi che iniziano con un numero o contengono uno dei caratteri seguenti ( `+` ,, `*` `?` , `^` , `$` , `(` , `)` , `[` , `]` , `{` ,,, `}` `|` `\` ). Lo schema del modello di dispositivo DTDL usato dalle applicazioni V3 non consente questi caratteri. Aggiornare il modello di dispositivo per risolvere il problema prima di eseguire la migrazione a V3.

1. Quando la nuova app V3 è pronta, selezionare **Apri la nuova app** per aprirla.

    :::image type="content" source="media/howto-migrate/open-app.png" alt-text="Screenshot che mostra la migrazione guidata applicazione dopo la migrazione dell'applicazione":::

## <a name="configure-the-v3-application"></a>Configurare l'applicazione v3

Dopo aver creato la nuova applicazione v3, apportare le modifiche alla configurazione prima di spostare i dispositivi dall'applicazione V2 all'applicazione v3.

> [!TIP]
> È possibile [acquisire familiarità con](overview-iot-central-tour.md#navigate-your-application) la versione V3 poiché presenta alcune differenze rispetto alla versione precedente.

Ecco alcuni passaggi di configurazione consigliati da considerare:

- [Configurare i dashboard](howto-add-tiles-to-your-dashboard.md)
- [Configurare l'esportazione dati](howto-export-data.md)
- [Configurare regole e azioni](quick-configure-rules.md)
- [Personalizzare l'interfaccia utente dell'applicazione](howto-customize-ui.md)

Quando l'applicazione v3 è configurata in modo da soddisfare le proprie esigenze, si è pronti per spostare i dispositivi dall'applicazione V2 all'applicazione v3.

## <a name="move-your-devices-to-the-v3-application"></a>Spostare i dispositivi nell'applicazione v3

Al termine di questo passaggio, tutti i dispositivi comunicano solo con la nuova applicazione v3.

> [!IMPORTANT]
> Prima di spostare i dispositivi nell'applicazione v3, eliminare tutti i dispositivi creati nell'applicazione v3.

Questo passaggio consente di spostare tutti i dispositivi esistenti nella nuova applicazione v3. I dati del dispositivo non vengono copiati.

Selezionare **Sposta tutti i dispositivi** per avviare lo spostamento dei dispositivi. Il completamento di questo passaggio può richiedere alcuni minuti.

:::image type="content" source="media/howto-migrate/move-devices.png" alt-text="Screenshot che mostra la migrazione guidata applicazione con l'opzione Sposta dispositivi":::

Al termine dello spostamento, riavviare tutti i dispositivi per assicurarsi che si connettano alla nuova applicazione v3.

> [!WARNING]
> Non eliminare l'applicazione v3 perché l'applicazione V2 è ora inutilizzabile.

## <a name="delete-your-old-v2-application"></a>Eliminare l'applicazione v2 precedente

Dopo aver convalidato che tutto funzioni come previsto nella nuova applicazione v3, eliminare la versione precedente dell'applicazione V2. Questo passaggio garantisce che non venga addebitato alcun costo per un'applicazione che non viene più usata.

> [!Note]
> Per eliminare un'applicazione, è necessario disporre delle autorizzazioni per eliminare le risorse nella sottoscrizione di Azure scelta al momento della creazione dell'applicazione. Per altre informazioni, vedere [Usare il controllo degli accessi in base al ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../../role-based-access-control/role-assignments-portal.md).

1. Nell'applicazione V2 selezionare la scheda **Amministrazione** nel menu
2. Selezionare **Elimina** per eliminare definitivamente l'applicazione IoT Central. Questa opzione consente di eliminare definitivamente tutti i dati associati a tale applicazione.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come eseguire la migrazione dell'applicazione, il passaggio successivo suggerito consiste nel rivedere l' [interfaccia utente di Azure IOT Central](overview-iot-central-tour.md) per vedere cosa è cambiato in V3.