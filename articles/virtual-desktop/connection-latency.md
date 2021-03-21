---
title: Latenza connessione utente desktop virtuale Windows-Azure
description: Latenza di connessione per gli utenti di desktop virtuali Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: bb0ee52d37fe901a610723d5864240b8778d8b17
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574595"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Determinare la latenza della connessione utente nel desktop virtuale di Windows

Desktop virtuale di Windows è disponibile a livello globale. Gli amministratori possono creare macchine virtuali (VM) in tutte le aree di Azure desiderate. La latenza della connessione può variare a seconda della posizione degli utenti e delle macchine virtuali. I servizi desktop virtuali Windows vengono continuamente implementati in nuove aree geografiche per migliorare la latenza.

Lo [strumento Windows Virtual Desktop Experience Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment/) può aiutarti a determinare la posizione migliore per ottimizzare la latenza delle macchine virtuali. Si consiglia di utilizzare lo strumento ogni due o tre mesi per assicurarsi che la posizione ottimale non sia cambiata perché il desktop virtuale di Windows viene sottoposto a nuove aree.

## <a name="interpreting-results-from-the-windows-virtual-desktop-experience-estimator-tool"></a>Interpretazione dei risultati ottenuti dallo strumento Windows Virtual Desktop Experience Estimator

Nel desktop virtuale di Windows, la latenza fino a 150 MS non dovrebbe avere alcun effetto sull'esperienza utente che non implica il rendering o il video. Le latenze comprese tra 150 MS e 200 MS dovrebbero essere ottimizzate per l'elaborazione del testo. La latenza superiore a 200 ms può influisca sull'esperienza utente. 

Inoltre, la connessione desktop virtuale di Windows dipende dalla connessione Internet del computer da cui l'utente usa il servizio. È possibile che gli utenti perdano il ritardo di connessione o di input in una delle situazioni seguenti:

 - L'utente non dispone di una connessione Internet locale stabile e la latenza è superiore a 200 ms.
 - La rete è satura o limitata dalla frequenza.

Si consiglia di scegliere le posizioni delle VM più vicine possibile agli utenti. Ad esempio, se l'utente si trova in India, ma la macchina virtuale si trova nella Stati Uniti, sarà presente una latenza che influirà sull'esperienza utente complessiva. 

## <a name="azure-front-door"></a>Frontdoor di Azure

Desktop virtuale di Windows usa la [porta anteriore di Azure](https://azure.microsoft.com/services/frontdoor/) per reindirizzare la connessione utente al gateway di desktop virtuale Windows più vicino in base all'indirizzo IP di origine. Desktop virtuale Windows utilizzerà sempre il gateway desktop virtuale di Windows scelto dal client.

## <a name="next-steps"></a>Passaggi successivi

- Per controllare la posizione migliore per la latenza ottimale, vedere lo [strumento Windows Virtual Desktop Experience Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- Per i piani tariffari, vedere [prezzi di desktop virtuali Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- Per iniziare a usare la distribuzione di desktop virtuali Windows, vedere [l'esercitazione](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace).
