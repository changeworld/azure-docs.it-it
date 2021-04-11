---
title: "Procedura: ripristinare o rimuovere un'applicazione eliminata di recente con la piattaforma di identità Microsoft | Azure"
titleSuffix: Microsoft identity platform
description: In questa esercitazione si apprenderà come ripristinare o eliminare definitivamente un'applicazione eliminata di recente registrata con la piattaforma di identità Microsoft.
services: active-directory
author: arcrowe
manager: dastrock
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 3/22/2021
ms.author: arcrowe
ms.custom: aaddev
ms.openlocfilehash: dbe3e16bf56c5480fbe4aff8dad78bbbb5591f67
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080339"
---
# <a name="restore-or-remove-a-recently-deleted-application-with-the-microsoft-identity-platform"></a>Ripristinare o rimuovere un'applicazione eliminata di recente con la piattaforma di identità Microsoft
Dopo aver eliminato la registrazione di un'app, l'app rimane in stato sospeso per 30 giorni. Durante tale intervallo di 30 giorni, è possibile ripristinare la registrazione dell'app insieme a tutte le relative proprietà. Dopo la scadenza di 30 giorni, le registrazioni dell'app non possono essere ripristinate e il processo di eliminazione permanente può essere avviato automaticamente.  Questa funzionalità si applica solo alle applicazioni associate a una directory.  Non è disponibile per le applicazioni da un account Microsoft personale, che non può essere ripristinato.

È possibile visualizzare le applicazioni eliminate, ripristinare un'applicazione eliminata o eliminare definitivamente un'applicazione usando l'esperienza Registrazioni app in Azure Active Directory (Azure AD) nel portale di Azure.

Si noti che né l'utente né il supporto tecnico Microsoft possono ripristinare un'applicazione eliminata definitivamente o un'applicazione eliminata più di 30 giorni prima.

> [!IMPORTANT]
> Funzionalità dell'interfaccia utente del portale applicazioni eliminate [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="required-permissions"></a>Autorizzazioni necessarie
Per eliminare definitivamente le applicazioni, è necessario disporre di uno dei ruoli seguenti.

- Amministratore globale
- Amministratore di applicazioni
- Amministratore di applicazioni cloud
- Amministratore delle identità ibride
- Proprietario dell'applicazione

Per ripristinare le applicazioni, è necessario disporre di uno dei ruoli seguenti.

- Amministratore globale
- Proprietario dell'applicazione

### <a name="view-your-deleted-applications"></a>Visualizzare le applicazioni eliminate
È possibile visualizzare tutte le applicazioni in uno stato di eliminazione temporanea.  È possibile ripristinare solo le applicazioni eliminate meno di 30 giorni fa.

#### <a name="to-view-your-restorable-applications"></a>Per visualizzare le applicazioni ripristinabili
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Cercare e selezionare **Azure Active Directory**, selezionare **registrazioni app**, quindi selezionare la scheda **applicazioni eliminate (anteprima)** .

Esaminare l'elenco delle applicazioni. Per il ripristino sono disponibili solo le applicazioni che sono state eliminate negli ultimi 30 giorni. Se si usa l'anteprima di ricerca di Registrazioni app, è possibile filtrare in base alla colonna ' deleted date ' per visualizzare solo queste applicazioni.

## <a name="restore-a-recently-deleted-application"></a>Ripristinare un'applicazione eliminata di recente

Quando si elimina una registrazione dell'app dall'organizzazione, l'app è in uno stato sospeso e le relative configurazioni vengono mantenute. Quando si ripristina la registrazione di un'app, vengono ripristinate anche le configurazioni.  Tuttavia, se sono presenti impostazioni specifiche dell'organizzazione nelle **applicazioni aziendali** per il tenant principale dell'applicazione, non verranno ripristinate.  

Questo è dovuto al fatto che le impostazioni specifiche dell'organizzazione sono archiviate in un oggetto separato, denominato entità servizio.  Le impostazioni contenute nell'entità servizio includono i consensi di autorizzazione e le assegnazioni di utenti e gruppi per una determinata organizzazione; Queste configurazioni non verranno ripristinate quando l'app viene ripristinata. Per ulteriori informazioni, vedere [oggetti applicazione e oggetti entità servizio](app-objects-and-service-principals.md). 


### <a name="to-restore-an-application"></a>Per ripristinare un'applicazione
1. Nella scheda **applicazioni eliminate (anteprima)** cercare e selezionare una delle applicazioni eliminate meno di 30 giorni fa.
2. Selezionare **Ripristina registrazione app**.

## <a name="permanently-delete-an-application"></a>Eliminare definitivamente un'applicazione
È possibile eliminare manualmente un'applicazione dall'organizzazione. Un'applicazione eliminata definitivamente non può essere ripristinata dall'utente, da un altro amministratore o dal supporto tecnico Microsoft.

### <a name="to-permanently-delete-an-application"></a>Per eliminare definitivamente un'applicazione

1. Nella scheda **applicazioni eliminate (anteprima)** cercare e selezionare una delle applicazioni disponibili.
2. Selezionare **Elimina definitivamente**.
3. Leggere il testo dell'avviso e selezionare **Sì**.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver ripristinato o eliminato definitivamente l'app, è possibile:

- [Aggiungere un'applicazione](quickstart-register-app.md).
- Altre informazioni sugli [oggetti applicazione ed entità servizio](app-objects-and-service-principals.md) in Microsoft Identity Platform.
