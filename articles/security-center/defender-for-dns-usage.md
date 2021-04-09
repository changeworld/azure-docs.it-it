---
title: Come rispondere ad Azure Defender per gli avvisi DNS
description: Informazioni sui passaggi necessari per rispondere agli avvisi da Azure Defender per DNS
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 375a81127a000741ca5e0397642800794610bcda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754670"
---
# <a name="respond-to-azure-defender-for-dns-alerts"></a>Rispondere agli avvisi di Azure Defender per DNS

Quando si riceve un avviso da Azure Defender per DNS, è consigliabile esaminare e rispondere all'avviso come descritto di seguito. Azure Defender per DNS protegge tutte le risorse connesse, quindi anche se si ha familiarità con l'applicazione o l'utente che ha attivato l'avviso, è importante verificare la situazione che circonda ogni avviso.  


## <a name="step-1-contact"></a>Passaggio 1. Contatto

1. Contattare il proprietario della risorsa per determinare se il comportamento era previsto o intenzionale.
1. Se è prevista l'attività, chiudere l'avviso.
1. Se l'attività è imprevista, trattare la risorsa come potenzialmente compromessa e attenuarla come descritto nel passaggio successivo.

## <a name="step-2-immediate-mitigation"></a>Passaggio 2: Mitigazione immediata 

1. Isolare la risorsa dalla rete per impedire lo spostamento laterale.
1. Eseguire un'analisi antimalware completa sulla risorsa, seguendo eventuali consigli di correzione risultanti.
1. Esaminare il software installato ed eseguito sulla risorsa, rimuovendo eventuali pacchetti sconosciuti o indesiderati.
1. Ripristinare uno stato valido noto della macchina, reinstallare il sistema operativo, se necessario, e ripristinare il software da un'origine senza malware verificata.
1. Risolvere eventuali raccomandazioni del Centro sicurezza di Azure per il computer, monitorando e aggiornando i problemi di sicurezza evidenziati per evitare violazioni future.


## <a name="next-steps"></a>Passaggi successivi

Questa pagina ha illustrato il processo di risposta a un avviso di Azure Defender per DNS. Per informazioni correlate, vedere le pagine seguenti:

- [Introduzione ad Azure Defender per DNS](defender-for-dns-introduction.md)
- [Eliminare gli avvisi di Azure Defender](alerts-suppression-rules.md)
- [Esportazione continua dei dati del Centro sicurezza](continuous-export.md)