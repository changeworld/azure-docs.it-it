---
title: Risolvere i problemi durante l'esperienza di onboarding per Azure Percept DK
description: Suggerimenti per la risoluzione dei problemi relativi ad alcuni dei problemi più comuni riscontrati durante l'esperienza di onboarding
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: d71cfa6ba52052e4b68175be84934c8b4294ed25
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662913"
---
# <a name="azure-percept-dk-onboarding-experience-troubleshooting-guide"></a>Guida alla risoluzione dei problemi di onboarding Experience di Azure Percept DK

Di seguito sono riportati alcuni problemi che possono verificarsi durante l'esperienza di onboarding di Azure Percept DK. Se, dopo aver eseguito i passaggi descritti in questa guida, il problema persiste. Contattare il servizio supporto tecnico clienti di Azure.

|Problema|Motivo|Soluzione alternativa|
|:-----|:------|:----------|
|Quando ci si connette alle pagine di iscrizione all'account Azure o alla portale di Azure, è possibile accedere automaticamente con un account memorizzato nella cache. Se questo non è l'account che si intende usare, può verificarsi un'esperienza non coerente con la documentazione.|Questa operazione è in genere dovuta a un'impostazione nel browser per "ricordare" un account usato in precedenza.|Dalla pagina di Azure fare clic sull'angolo superiore destro della pagina in cui viene visualizzato il nome dell'account, quindi fare clic su "Esci". Sarà quindi possibile accedere con l'account corretto.|
|La rete del punto di accesso di Azure Percept DK (SCZ-xxxx) non viene visualizzata nell'elenco delle reti Wi-Fi disponibili.|Si tratta in genere di un problema temporaneo che si risolve con un breve intervallo di tempo.|Attendere che venga visualizzata la rete. Se non dopo più di 15 minuti, riavviare il dispositivo.|
|La connessione al punto di accesso di Azure Percept DK si disconnette spesso.|Ciò può essere dovuto a una connessione scadente tra il dispositivo e il computer host. Il problema può essere causato anche da interferenze provenienti da altre Wi-Fi connessioni sul computer host.|Assicurarsi che le antenne siano collegate correttamente al kit di sviluppo. Se il kit dev è distante dal computer host, provare a trasferirlo più vicino. Disattivare eventuali altre connessioni Internet, ad esempio LTE/5G se sono in esecuzione nel computer host.|
|Il computer host Visualizza un avviso di sicurezza sulla connessione al punto di accesso di Azure Percept DK.|Si tratta di un problema noto che verrà risolto in un aggiornamento successivo.|È possibile procedere in modo sicuro tramite l'esperienza di onboarding tramite il punto di accesso Wi-Fi di devkit.|
|La rete del punto di accesso di Azure Percept DK (SCZ-xxxx) viene visualizzata nell'elenco rete ma non riesce a connettersi.|Il problema potrebbe essere dovuto a un danneggiamento temporaneo del punto di accesso Wi-Fi di devkit.|Riavviare il DevKit e riprovare.|
|Non è possibile connettersi a una rete di Wi-Fi durante l'installazione.|La rete di Wi-Fi deve avere attualmente la connettività Internet per poter comunicare con Azure. Il protocollo EAP [PEAP/MSCHAP], i portali captive e la connettività EAP-TLS aziendale non sono attualmente supportati.|Verificare che il tipo Wi-Fi rete da connettere sia supportato e che disponga della connettività Internet.|