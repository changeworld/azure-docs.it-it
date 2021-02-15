---
title: Aggiornamento automatico e notifica di scadenza del runtime di integrazione self-hosted
description: Informazioni sulle notifiche di aggiornamento automatico e scadenza del runtime di integrazione self-hosted
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 12/25/2020
ms.openlocfilehash: 972015f0f42a8a869de0edcc8f0e921ae7278cd1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376259"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>Aggiornamento automatico e notifica di scadenza del runtime di integrazione self-hosted

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In questo articolo viene descritto come consentire l'aggiornamento automatico del runtime di integrazione self-hosted alla versione più recente e il modo in cui ADF gestisce le versioni del runtime di integrazione self-hosted.

## <a name="self-hosted-integration-runtime-auto-update"></a>Aggiornamento automatico del Integration Runtime self-hosted
In genere, quando si installa un runtime di integrazione self-hosted nel computer locale o in una VM di Azure, sono disponibili due opzioni per gestire la versione del runtime di integrazione self-hosted: aggiornamento automatico o manutenzione manuale. In genere, ADF rilascia due nuove versioni del runtime di integrazione self-hosted ogni mese che include la nuova versione della funzionalità, la correzione di bug o il miglioramento. Quindi, si consiglia agli utenti di eseguire l'aggiornamento alla versione più recente per ottenere la funzionalità e i miglioramenti più recenti.

Il modo più pratico consiste nell'abilitare l'aggiornamento automatico quando si crea o si modifica il runtime di integrazione self-hosted. Quindi verrà aggiornato automaticamente alla versione più recente. È anche possibile pianificare l'aggiornamento nello slot di tempo più adatto come desiderato.

![Abilita aggiornamento automatico](media/create-self-hosted-integration-runtime/shir-auto-update.png)

È possibile controllare l'ultimo valore DateTime dell'aggiornamento nel client di Integration Runtime self-hosted.

![Screenshot della verifica dell'ora di aggiornamento](media/create-self-hosted-integration-runtime/shir-auto-update-2.png)

> [!NOTE]
> Per garantire la stabilità del runtime di integrazione self-hosted, sebbene vengano rilasciate due versioni, verrà aggiornata automaticamente solo una volta al mese. Quindi, a volte si noterà che la versione aggiornata automaticamente è la versione precedente della versione più recente effettiva. Se si vuole ottenere la versione più recente, è possibile passare all' [area download](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="self-hosted-integration-runtime-expire-notification"></a>Notifica scadenza Integration Runtime self-hosted
Se si desidera controllare manualmente la versione del runtime di integrazione self-hosted, è possibile disabilitare l'impostazione di aggiornamento automatico e installarla manualmente. Ogni versione del runtime di integrazione self-hosted scadrà tra un anno. Il messaggio in scadenza viene visualizzato nel portale di ADF e nel client del runtime di integrazione self-hosted **90 giorni** prima della scadenza.

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Concetti relativi al runtime di integrazione in Azure Data Factory](./concepts-integration-runtime.md).

- Informazioni su come [creare un runtime di integrazione self-hosted nel portale di Azure](./create-self-hosted-integration-runtime.md).
