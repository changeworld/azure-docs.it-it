---
title: Risolvere i problemi durante l'esperienza di installazione di Azure Percept DK
description: Suggerimenti per la risoluzione dei problemi relativi ad alcuni dei problemi più comuni che si verificano durante l'installazione
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 7ce13cedff9afc25900c0bf75359ae49cc29fe19
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608494"
---
# <a name="azure-percept-dk-setup-experience-troubleshooting-guide"></a>Guida alla risoluzione dei problemi dell'esperienza di installazione di Azure Percept DK

Vedere la tabella seguente per le soluzioni alternative ai problemi comuni riscontrati durante l' [esperienza di installazione di Azure PERCEPT dk](./quickstart-percept-dk-set-up.md). Se il problema persiste, contattare il supporto tecnico di Azure.

|Problema|Motivo|Soluzione alternativa|
|:-----|:------|:----------|
|Quando ci si connette alle pagine di iscrizione all'account Azure o alla portale di Azure, è possibile accedere automaticamente con un account memorizzato nella cache. Se questo non è l'account che si intende usare, può verificarsi un'esperienza non coerente con la documentazione.|Questa operazione è in genere dovuta a un'impostazione nel browser per "ricordare" un account usato in precedenza.|Nella pagina di Azure fare clic sul nome dell'account nell'angolo in alto a destra e selezionare **disconnettersi**. Sarà quindi possibile accedere con l'account corretto.|
|Il punto di accesso Wi-Fi DK di Azure Percept (SCZ-xxxx o APD-xxxx) non viene visualizzato nell'elenco delle reti Wi-Fi disponibili.|Si tratta in genere di un problema temporaneo che risolve entro 15 minuti.|Attendere che venga visualizzata la rete. Se non viene visualizzato dopo più di 15 minuti, riavviare il dispositivo.|
|La connessione al punto di accesso Wi-Fi di Azure Percept DK si disconnette spesso.|Ciò può essere dovuto a una connessione scadente tra il dispositivo e il computer host. Il problema può essere causato anche da interferenze provenienti da altre Wi-Fi connessioni sul computer host.|Assicurarsi che le antenne siano collegate correttamente al kit di sviluppo. Se il kit dev è distante dal computer host, provare a trasferirlo più vicino. Disattivare eventuali altre connessioni Internet, ad esempio LTE/5G se sono in esecuzione nel computer host.|
|Il computer host Visualizza un avviso di sicurezza sulla connessione al punto di accesso di Azure Percept DK.|Si tratta di un problema noto che verrà risolto in un aggiornamento successivo.|È possibile procedere in modo sicuro con l'esperienza di installazione.|
|Il punto di accesso Wi-Fi DK di Azure Percept (SCZ-xxxx o APD-xxxx) viene visualizzato nell'elenco rete ma non riesce a connettersi.|Questo potrebbe essere dovuto a un danneggiamento temporaneo del punto di accesso Wi-Fi del kit di sviluppo.|Riavviare il kit di sviluppo e riprovare.|
|Non è possibile connettersi a una rete di Wi-Fi durante l'installazione.|La rete di Wi-Fi deve avere attualmente la connettività Internet per comunicare con Azure. Il protocollo EAP [PEAP/MSCHAP], i portali captive e la connettività EAP-TLS aziendale non sono attualmente supportati.|Verificare che il tipo di rete Wi-Fi sia supportato e che disponga della connettività Internet.|