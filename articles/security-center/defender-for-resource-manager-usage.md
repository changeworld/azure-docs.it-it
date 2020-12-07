---
title: Come rispondere ad Azure Defender per Gestione risorse avvisi
description: Informazioni sui passaggi necessari per rispondere agli avvisi da Azure Defender per Gestione risorse
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 54790795aab8aac247e17198159130d7139dd38c
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754651"
---
# <a name="respond-to-azure-defender-for-resource-manager-alerts"></a>Rispondere ad Azure Defender per Gestione risorse avvisi

Quando si riceve un avviso da Azure Defender per Gestione risorse, è consigliabile esaminare e rispondere all'avviso come descritto di seguito. Azure Defender per Gestione risorse protegge tutte le risorse connesse, quindi anche se si ha familiarità con l'applicazione o l'utente che ha attivato l'avviso, è importante verificare la situazione che circonda ogni avviso.  


## <a name="step-1-contact"></a>Passaggio 1. Contatto

1. Contattare il proprietario della risorsa per determinare se il comportamento era previsto o intenzionale.
1. Se è prevista l'attività, chiudere l'avviso.
1. Se l'attività è imprevista, trattare gli account utente, le sottoscrizioni e le macchine virtuali correlati come compromessi e attenuarli come descritto nel passaggio seguente.

## <a name="step-2-immediate-mitigation"></a>Passaggio 2. Mitigazione immediata 

1. Correggere gli account utente compromessi:
    - Se non si ha familiarità, eliminarli così come potrebbero essere stati creati da un attore minaccia
    - Se hanno familiarità, modificare le credenziali di autenticazione
    - Usare i log attività di Azure per esaminare tutte le attività eseguite dall'utente e identificare eventuali sospette

1. Correggere le sottoscrizioni compromesse:
    - Rimuovere qualsiasi manuali operativi non familiare dall'account di automazione compromesso
    - Verificare le autorizzazioni IAM per la sottoscrizione e rimuovere le autorizzazioni per qualsiasi account utente non noto
    - Esaminare tutte le risorse di Azure nella sottoscrizione ed eliminare quelle non note
    - Esaminare ed esaminare gli avvisi di sicurezza per la sottoscrizione nel centro sicurezza di Azure
    - Usare i log attività di Azure per esaminare tutte le attività eseguite nella sottoscrizione e identificare eventuali sospetti

1. Correggere le macchine virtuali compromesse
    - Modificare le password per tutti gli utenti
    - Eseguire un'analisi antimalware completa nel computer
    - Ricreare l'immagine delle macchine da un'origine senza malware


## <a name="next-steps"></a>Passaggi successivi

Questa pagina ha illustrato il processo di risposta a un avviso di Azure Defender per Gestione risorse. Per informazioni correlate, vedere le pagine seguenti:

- [Introduzione ad Azure Defender per Gestione risorse](defender-for-resource-manager-introduction.md)
- [Eliminare gli avvisi di Azure Defender](alerts-suppression-rules.md)
- [Esportazione continua dei dati del Centro sicurezza](continuous-export.md)