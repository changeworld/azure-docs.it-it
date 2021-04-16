---
title: Limiti del servizio
titleSuffix: Azure Digital Twins
description: Grafico che mostra i limiti del Gemelli digitali di Azure servizio.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 15c76bc042cb66dafbdeebac2951f5cb68310aa4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482792"
---
# <a name="azure-digital-twins-service-limits"></a>Gemelli digitali di Azure dei servizi

Questi sono i limiti del servizio Gemelli digitali di Azure.

> [!NOTE]
> Alcune aree di questo servizio hanno limiti modificabili. Questa proprietà è rappresentata nelle tabelle seguenti con la *colonna Adjustable?* . Quando è possibile regolare il limite, il *valore regolabile?* è *Sì.*
>
> Se l'azienda richiede l'innalzamento di un limite regolabile o di una quota superiore al limite predefinito, è possibile richiedere risorse aggiuntive aprendo [un ticket di supporto.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="limits-by-type"></a>Limiti per tipo

[!INCLUDE [Azure Digital Twins limits](../../includes/digital-twins-limits.md)]

## <a name="working-with-limits"></a>Uso dei limiti

Quando viene raggiunto un limite, il servizio limita le richieste aggiuntive. Verrà restituita una risposta di errore 429 da queste richieste.

Per gestire questo problema, di seguito sono riportati alcuni consigli per l'uso dei limiti.
* **Usare la logica di ripetizione dei tentativi.** Gli [GEMELLI DIGITALI DI AZURE SDK](how-to-use-apis-sdks.md) implementano la logica di ripetizione dei tentativi per le richieste non riuscite, quindi se si lavora con un SDK fornito, questo è già incorporato. In caso contrario, valutare la possibilità di implementare la logica di ripetizione dei tentativi nella propria applicazione. Il servizio restituisce un'intestazione nella risposta di errore, che è possibile usare per determinare il tempo di `Retry-After` attesa prima di riprovare.
* **Usare soglie e notifiche per avvisare sull'avvicinamento dei limiti.** Alcuni dei limiti del servizio per Gemelli digitali di Azure metriche [corrispondenti](troubleshoot-metrics.md) che possono essere usati per tenere traccia dell'utilizzo in queste aree. Per configurare le soglie e configurare un avviso per qualsiasi metrica quando viene raggiunta una soglia, vedere le istruzioni in Risoluzione dei [*problemi: Configurare gli avvisi*](troubleshoot-alerts.md). Per configurare le notifiche per altri limiti in cui non vengono fornite metriche, valutare la possibilità di implementare questa logica nel codice dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla versione corrente di Gemelli digitali di Azure panoramica del servizio:
* [*Panoramica: Che cos'è Gemelli digitali di Azure?*](overview.md)
