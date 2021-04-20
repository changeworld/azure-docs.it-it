---
title: Limiti del servizio
titleSuffix: Azure Digital Twins
description: Grafico che mostra i limiti del Gemelli digitali di Azure servizio.
author: baanders
ms.author: baanders
ms.date: 04/08/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 651922837b2193f7a8387c4dec6a1e20b84a41a5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728025"
---
# <a name="azure-digital-twins-service-limits"></a>Gemelli digitali di Azure dei servizi

Questi sono i limiti del servizio di Gemelli digitali di Azure.

> [!NOTE]
> Alcune aree di questo servizio hanno limiti modificabili. Questo valore è rappresentato nelle tabelle seguenti con la *colonna Adjustable?* . Quando è possibile regolare il limite, il *valore regolabile?* è *Sì.*
>
> Se l'azienda richiede l'aumento di un limite modificabile o di una quota superiore al limite predefinito, è possibile richiedere risorse aggiuntive [aprendo un ticket di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="limits-by-type"></a>Limiti per tipo

[!INCLUDE [Azure Digital Twins limits](../../includes/digital-twins-limits.md)]

## <a name="working-with-limits"></a>Uso dei limiti

Quando viene raggiunto un limite, il servizio limita le richieste aggiuntive. Verrà restituita una risposta di errore 429 da queste richieste.

Per gestire questo problema, di seguito sono riportati alcuni consigli per l'uso dei limiti.
* **Usare la logica di ripetizione dei tentativi.** Gli [GEMELLI DIGITALI DI AZURE implementano](how-to-use-apis-sdks.md) la logica di ripetizione dei tentativi per le richieste non riuscite, quindi se si lavora con un SDK fornito, questo è già incorporato. In caso contrario, è consigliabile implementare la logica di ripetizione dei tentativi nella propria applicazione. Il servizio restituisce un'intestazione nella risposta di errore, che è possibile usare per determinare il tempo di `Retry-After` attesa prima di riprovare.
* **Usare soglie e notifiche per avvisare quando si avvicinano i limiti.** Alcuni dei limiti del servizio per Gemelli digitali di Azure metriche [corrispondenti](troubleshoot-metrics.md) che possono essere usate per tenere traccia dell'utilizzo in queste aree. Per configurare le soglie e configurare un avviso per qualsiasi metrica quando viene raggiunta una soglia, vedere le istruzioni in Risoluzione dei [*problemi: Configurare gli avvisi.*](troubleshoot-alerts.md) Per configurare le notifiche per altri limiti in cui non vengono fornite metriche, è consigliabile implementare questa logica nel codice dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla versione corrente di Gemelli digitali di Azure nella panoramica del servizio:
* [*Panoramica: Informazioni Gemelli digitali di Azure?*](overview.md)
