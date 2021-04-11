---
title: Connettersi a desktop virtuale Windows 10 o 7-Azure
description: Come connettersi al desktop virtuale di Windows tramite il client desktop di Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 09/22/2020
ms.author: helohr
manager: femila
ms.custom: template-how-to
ms.openlocfilehash: 625662a6b67e7d30e6320fe7831e4fa7793b9c30
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447880"
---
# <a name="connect-with-the-windows-desktop-client"></a>Connettersi con il client desktop di Windows

È possibile accedere alle risorse di desktop virtuali Windows nei dispositivi con Windows 10, Windows 10 Internet e Windows 7 utilizzando il client desktop di Windows. 

> [!IMPORTANT]
> Questa operazione non supporta la finestra 8 o Windows 8.1.
> 
> Questo supporta solo Azure Resource Manager oggetti, per supportare oggetti senza Azure Resource Manager, vedere [connettersi con client desktop Windows (classico)](./virtual-desktop-fall-2019/connect-windows-7-10-2019.md).
> 
> Questa operazione non supporta il client di connessione RemoteApp e desktop (RADC) o il client Connessione Desktop remoto (MSTSC).

## <a name="install-the-windows-desktop-client"></a>Installare il client desktop di Windows

Scaricare il client in base alla versione di Windows:

- [Windows a 64 bit](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows a 32 bit](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

Durante l'installazione per determinare l'accesso, selezionare una delle seguenti operazioni:

- **Installa solo per te**
- **Installare per tutti gli utenti del computer** (sono necessari i diritti di amministratore)

Per avviare il client dopo l'installazione, usare il menu **Start** e cercare **Desktop remoto**.

## <a name="subscribe-to-a-workspace"></a>Sottoscrivere un'area di lavoro

Per sottoscrivere un'area di lavoro, scegliere una delle seguenti operazioni:

- Usare un account aziendale o dell'Istituto di istruzione e chiedere al client di individuare le risorse disponibili
- Usare l'URL specifico della risorsa

Per avviare la risorsa una volta sottoscritta, passare al **Centro connessioni** e fare doppio clic sulla risorsa.

> [!TIP]
> Per avviare una risorsa dal menu **Start** , è possibile trovare la cartella con il nome dell'area di lavoro o immettere il nome della risorsa nella barra di ricerca.

### <a name="use-a-user-account"></a>Usare un account utente

1. Selezionare **Sottoscrivi** nella pagina principale.
1. Accedi con l'account utente quando richiesto.

Le risorse raggruppate per area di lavoro verranno visualizzate nel **Centro connessioni**.

   > [!NOTE]
   > Il client Windows si imposta automaticamente sul desktop virtuale Windows (classico). 
   > 
   > Tuttavia, se il client rileva risorse Azure Resource Manager aggiuntive, le aggiunge automaticamente o invia una notifica all'utente che sono disponibili.

### <a name="use-a-specific-url"></a>Usa un URL specifico

1. Selezionare **sottoscrizione con URL** nella pagina principale.
1. Immettere l' *URL dell'area di lavoro* o un indirizzo di *posta elettronica*:
   - Per **URL area di lavoro**, usare l'URL fornito dall'amministratore.

   |Risorse disponibili|URL|
   |-|-|
   |Desktop virtuale Windows (classico)|`https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`|
   |Desktop virtuale Windows|`https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`|
   |Desktop virtuale Windows (US Gov)|`https://rdweb.wvd.azure.us/api/arm/feeddiscovery`|
   
   - Per la **posta elettronica**, usare l'indirizzo di posta elettronica. 
      
   Il client troverà l'URL associato al messaggio di posta elettronica, purché l'amministratore abbia abilitato l' [individuazione della posta elettronica](/windows-server/remote/remote-desktop-services/rds-email-discovery).

1. Selezionare **Avanti**.
1. Accedi con l'account utente quando richiesto.

Le risorse raggruppate per area di lavoro verranno visualizzate nel **Centro connessioni**.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'utilizzo del client, vedere [Introduzione al client desktop di Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).

Se si è un amministratore interessato ad altre informazioni sulle funzionalità del client, vedere [Windows desktop client for Admins](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-admin).
