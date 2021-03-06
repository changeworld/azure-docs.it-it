---
title: Creare e gestire i runtime di integrazione
description: Questo articolo illustra i passaggi per creare e gestire i runtime di integrazione in Azure.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: 9276f845c95b5e736180159b282ddedc33523c17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99980746"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>Creare e gestire un runtime di integrazione self-hosted

Questo articolo descrive come creare e gestire un runtime di integrazione self-hosted che consente di analizzare le origini dati in Azure.

> [!NOTE]
> Il Integration Runtime di competenza non può essere condiviso con un'analisi di sinapsi di Azure o Azure Data Factory Integration Runtime nello stesso computer. Deve essere installato in un computer separato.

## <a name="create-a-self-hosted-integration-runtime"></a>Creare un runtime di integrazione self-hosted

1. Nel home page di ambito Studio selezionare centro di **gestione** nel riquadro di spostamento a sinistra.

2. In **origini e analisi** nel riquadro sinistro selezionare Runtime di **integrazione**, quindi selezionare **+ nuovo**.

   :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="Fare clic su IR.":::

3. Nella pagina di **installazione di Integration Runtime** selezionare **self-hosted** per creare un Self-Hosted IR e quindi selezionare **continue (continua**).

   :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="Crea nuovo.":::

4. Immettere un nome per il runtime di integrazione e selezionare Crea.

5. Nella pagina **impostazioni Integration Runtime** seguire i passaggi descritti nella sezione **installazione manuale** . Sarà necessario scaricare il runtime di integrazione dal sito di download in una macchina virtuale o in un computer in cui si intende eseguirlo.

   :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="Ottieni chiave":::

   - Copiare e incollare la chiave di autenticazione.

   - Scaricare il runtime di integrazione self-hosted da [Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) in un computer Windows locale. Eseguire il programma di installazione.

   - Nella pagina **register Integration Runtime (self-hosted)** incollare una delle due chiavi salvate in precedenza e selezionare **Register (registra**).

     :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="chiave di input.":::

   - Nella pagina **Nuovo nodo di Integration Runtime (self-hosted)** fare clic su **Fine**.

6. Una volta completata la registrazione del runtime di integrazione self-hosted, viene visualizzata la finestra seguente:

   :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="registrazione completata.":::

## <a name="manage-a-self-hosted-integration-runtime"></a>Gestire un runtime di integrazione self-hosted

È possibile modificare un runtime di integrazione self-hosted passando a **Integration Runtimes (runtime di integrazione** ) nel **centro di gestione**, selezionando il runtime di integrazione e quindi facendo clic su Edit (modifica). È ora possibile aggiornare la descrizione, copiare la chiave o rigenerare le nuove chiavi.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="modificare il runtime di integrazione.":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="modificare i dettagli di IR.":::

È possibile eliminare un runtime di integrazione self-hosted passando a **Integration Runtimes (runtime di integrazione** ) nel centro di gestione, selezionando l'IR e quindi facendo clic su **Delete (Elimina**). Una volta eliminato un runtime di integrazione, le analisi in corso che si basano su di esso avranno esito negativo.

## <a name="next-steps"></a>Passaggi successivi

[Come vengono rilevati gli asset eliminati tramite le analisi](concept-detect-deleted-assets.md)
