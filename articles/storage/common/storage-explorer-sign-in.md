---
title: Accedere a Azure Storage Explorer | Microsoft Docs
description: Documentazione sull'accesso Azure Storage Explorer
services: storage
author: MRayermannMSFT
ms.service: storage
ms.topic: article
ms.date: 04/01/2021
ms.author: chuye
ms.openlocfilehash: ef02842d189746a1801d97f91b92f249947c832d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568729"
---
# <a name="sign-in-to-storage-explorer"></a>Accedere a Storage Explorer

L'accesso è il modo consigliato per accedere alle risorse di archiviazione di Azure con Storage Explorer. L'accesso consente di sfruttare Azure AD autorizzazioni supportate, ad esempio il controllo degli accessi in base al ruolo e gli ACL POSIX di gen2. 

## <a name="how-to-sign-in"></a>Come effettuare l'accesso

Per accedere a Storage Explorer, aprire la **finestra di dialogo Connetti**. È possibile aprire la **finestra di** dialogo Connetti dalla barra degli strumenti verticale a sinistra oppure facendo clic su Aggiungi **account nel** pannello **Account**.

Dopo aver aperto la finestra di dialogo, scegliere **Sottoscrizione** come tipo di risorsa a cui connettersi e fare clic su **Avanti.**

È ora necessario scegliere l'ambiente di Azure a cui si vuole accedere. È possibile scegliere tra qualsiasi ambiente noto, ad esempio Azure o Azure Cina, oppure è possibile aggiungere un ambiente personalizzato. Dopo aver selezionato l'ambiente, fare clic su **Avanti.**

A questo punto, verrà avviato il **Web browser** predefinito del sistema operativo e verrà aperta una pagina di accesso. Per ottenere risultati ottimali, lasciare aperta questa finestra del browser finché si usa Storage Explorer o almeno fino a quando non sono state eseguite tutte le MFA previste. Al termine dell'accesso, è possibile tornare a Storage Explorer.

## <a name="managing-accounts"></a>Gestione di account

È possibile gestire e rimuovere gli account Azure a cui si è effettuato l'accesso dal **pannello account**. È possibile aprire il pannello **account facendo** clic sul pulsante **Gestisci account** sulla barra degli strumenti verticale a sinistra.

Nel pannello **Account** verranno visualizzati tutti gli account a cui è stato eseguito l'accesso. In ogni account sarà:
- Tenant a cui appartiene l'account
- Per ogni tenant, le sottoscrizioni a cui si ha accesso

Per impostazione predefinita, Storage Explorer solo l'utente accede al tenant principale. Per visualizzare le sottoscrizioni e le risorse da un altro tenant, è necessario attivare tale tenant. Per attivare un tenant, selezionare la casella di controllo accanto. Dopo aver finito di usare un tenant, è possibile deselezionare la relativa casella di controllo per disattivarlo. Non è possibile disattivare il tenant principale.

Dopo aver attivato un tenant, potrebbe essere necessario reimmere le credenziali prima Storage Explorer possibile caricare sottoscrizioni o accedere alle risorse dal tenant. La necessità di reimmissione delle credenziali avviene in genere a causa di un criterio di accesso condizionale (CA), ad esempio Multi-Factor Authentication (MFA). Anche se è possibile che sia già stata eseguita l'autenticazione a più fattori per un altro tenant, potrebbe comunque essere necessario eseguire di nuovo l'autenticazione. Per reim specificare di nuovo le credenziali, è sufficiente fare clic su **Reenter credentials ... (Reim).** È anche possibile fare clic su **Dettagli errore per visualizzare** esattamente il motivo per cui non è stato possibile caricare le sottoscrizioni.

Dopo aver caricato le sottoscrizioni, è possibile scegliere quelle da filtrare selezionando o deselezionando le relative caselle di controllo.

Se si vuole rimuovere l'intero account Azure, fare clic su **Rimuovi** accanto all'account.

## <a name="changing-where-sign-in-happens"></a>Modifica della posizione di accesso

Per impostazione predefinita, l'accesso avviene nel Web browser predefinito **del sistema operativo.** L'accesso con il Web browser predefinito semplifica la modalità di accesso alle risorse protette tramite criteri di accesso alla ca, ad esempio MFA. Se per qualche motivo l'accesso con il **Web browser** predefinito del sistema operativo non funziona, è possibile modificare dove o come Storage Explorer esegue l'accesso.

In **Impostazioni**  >    >  **Accesso applicazione** cercare l'impostazione **Accedi** con. Sono disponibili tre opzioni:
- **Web browser predefinito:** l'accesso verrà emesso nel Web browser predefinito **del sistema operativo.** Questa opzione è consigliata.
- **Accesso integrato: l'accesso** verrà visualizzato in Storage Explorer finestra. Questa opzione può essere utile se si sta tentando di accedere con più account Microsoft (MSA) contemporaneamente. Se si sceglie questa opzione, potrebbero verificarsi problemi con alcuni criteri della CA.
- **Flusso del codice del** dispositivo: Storage Explorer un codice da immettere in una finestra del browser. Questa opzione non è consigliata. Il flusso del codice del dispositivo non è compatibile con molti criteri della CA.

## <a name="troubleshooting-sign-in-issues"></a>Risoluzione dei problemi di accesso

Se si verificano problemi di accesso o si verificano problemi con un account Azure dopo l'accesso, vedere la sezione relativa all'accesso della guida alla risoluzione Storage Explorer [risoluzione dei problemi](./storage-explorer-troubleshooting.md#sign-in-issues).
