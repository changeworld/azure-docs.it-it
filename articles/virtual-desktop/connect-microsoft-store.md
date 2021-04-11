---
title: Connettersi a client Microsoft Store-Azure
description: Come connettersi al desktop virtuale di Windows tramite il client Microsoft Store.
author: Heidilohr
ms.topic: how-to
ms.date: 10/05/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 5662b32d8e2c9af457ded62b0302c2c64b31edea
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448016"
---
# <a name="connect-with-the-microsoft-store-client"></a>Connettersi con il client Microsoft Store

>Si applica a: Windows 10.

È possibile accedere alle risorse di desktop virtuali Windows nei dispositivi con Windows 10.

## <a name="install-the-microsoft-store-client"></a>Installare il client di Microsoft Store

È possibile installare il client per l'utente corrente, che non richiede diritti di amministratore. In alternativa, l'amministratore può installare e configurare il client in modo che tutti gli utenti del dispositivo possano accedervi.

Dopo l'installazione, il client può essere avviato dal menu Start cercando Desktop remoto.

Per iniziare, [scaricare e installare il client dal Microsoft Store](https://www.microsoft.com/store/productId/9WZDNCRFJ3PS).

## <a name="subscribe-to-a-workspace"></a>Sottoscrivere un'area di lavoro

Sottoscrivere l'area di lavoro fornita dall'amministratore per ottenere l'elenco delle risorse gestite a cui è possibile accedere nel PC.

Per sottoscrivere un'area di lavoro:

1. Nella schermata Centro connessione Toccare **+ Aggiungi**, quindi toccare aree di **lavoro**.
2. Immettere l'URL dell'area di lavoro nel campo dell'URL dell'area di lavoro fornito dall'amministratore. L'URL dell'area di lavoro può essere un URL o un indirizzo di posta elettronica.
   
   - Se si usa un URL dell'area di lavoro, usare l'URL assegnato dall'amministratore.
   - Se ci si connette da desktop virtuale Windows, usare uno degli URL seguenti a seconda della versione del servizio in uso:
       - Desktop virtuale Windows (classico): `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx` .
       - Desktop virtuale di Windows: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery` .
  
3. Toccare **Sottoscrivi**.
4. Specificare le credenziali quando richiesto.
5. Dopo la sottoscrizione, le aree di lavoro devono essere visualizzate nel Centro connessioni.

Le aree di lavoro possono essere aggiunte, modificate o rimosse in base alle modifiche apportate dall'amministratore.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare il client di Microsoft Store, vedere [Introduzione al client Microsoft Store](/windows-server/remote/remote-desktop-services/clients/windows/).