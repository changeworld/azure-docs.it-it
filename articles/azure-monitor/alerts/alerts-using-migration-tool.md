---
title: Eseguire la migrazione delle regole di avviso di monitoraggio di Azure
description: Informazioni su come usare lo strumento di migrazione volontaria per eseguire la migrazione delle regole di avviso classiche.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: fa487bec49ab9faa0f7c3dce752a30e4440fb873
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037677"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Usare lo strumento di migrazione volontaria per eseguire la migrazione delle regole di avviso classiche

Come [annunciato in precedenza](monitoring-classic-retirement.md), gli avvisi classici di monitoraggio di Azure sono ritirati per gli utenti del cloud pubblico, anche se sono ancora in uso limitato fino al **31 maggio 2021**. Gli avvisi classici per il cloud di Azure per enti pubblici e Azure Cina 21Vianet si ritireranno il **29 febbraio 2024**.

Uno strumento di migrazione è disponibile nel portale di Azure ai clienti che hanno utilizzato le regole di avviso classiche e che desiderano attivare la migrazione autonomamente. Questo articolo illustra come usare lo strumento di migrazione.

## <a name="before-you-migrate"></a>Prima della migrazione

Il processo di migrazione converte le regole di avviso classiche in nuove regole di avviso equivalenti e crea gruppi di azioni. In preparazione, tenere presenti i punti seguenti:

- Il formato del payload delle notifiche e le API per creare e gestire le nuove regole di avviso sono diverse dalle regole di avviso classiche perché supportano più funzionalità. [Informazioni su come prepararsi per la migrazione](alerts-prepare-migration.md).

- Non è possibile eseguire la migrazione di alcune regole di avviso classiche utilizzando lo strumento. [Informazioni sulle regole di cui non è possibile eseguire la migrazione e sulle operazioni da eseguire con esse](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).

    > [!NOTE]
    > Il processo di migrazione non influirà sulla valutazione delle regole di avviso classiche. Continueranno a essere eseguiti e invieranno avvisi fino a quando non verranno migrati e le nuove regole di avviso avranno effetto.

## <a name="how-to-use-the-migration-tool"></a>Come usare lo strumento di migrazione

Per attivare la migrazione delle regole di avviso classiche nella portale di Azure, attenersi alla procedura seguente:

1. In [portale di Azure](https://portal.azure.com)selezionare **monitoraggio**.

1. Selezionare **avvisi**, quindi selezionare **Gestisci regole di avviso** o **Visualizza avvisi classici**.

1. Selezionare **Esegui migrazione a nuove regole** per passare alla pagina di destinazione della migrazione. In questa pagina viene visualizzato un elenco di tutte le sottoscrizioni e del relativo stato di migrazione:

    ![Screenshot mostra la pagina della migrazione delle regole di avviso.](media/alerts-using-migration-tool/migration-landing.png "Eseguire la migrazione delle regole")

    Tutte le sottoscrizioni di cui è possibile eseguire la migrazione tramite lo strumento sono contrassegnate come **pronte per la migrazione**.

    > [!NOTE]
    > Lo strumento di migrazione viene implementato in fasi per tutte le sottoscrizioni che usano le regole di avviso classiche. Nelle fasi iniziali dell'implementazione è possibile che alcune sottoscrizioni siano contrassegnate come non pronte per la migrazione.

1. Selezionare una o più sottoscrizioni e quindi fare clic su **Anteprima migrazione**.

    La pagina risultante Mostra i dettagli delle regole di avviso classiche di cui verrà eseguita la migrazione per una sottoscrizione alla volta. È anche possibile selezionare **Scarica i dettagli della migrazione per questa sottoscrizione** per ottenere i dettagli in formato CSV.

    ![Screenshot mostra la pagina della migrazione delle regole di avviso con un collegamento per scaricare i dettagli della migrazione per questa sottoscrizione ed è possibile specificare la posta elettronica per la notifica di migrazione.](media/alerts-using-migration-tool/migration-preview.png "Anteprima migrazione")

1. Specificare uno o più indirizzi di posta elettronica per ricevere notifiche sullo stato della migrazione. Si riceverà un messaggio di posta elettronica al termine della migrazione o se è necessaria un'azione da parte dell'utente.

1. Selezionare **Avvia migrazione**. Leggere le informazioni visualizzate nella finestra di dialogo di conferma e confermare che si è pronti per avviare il processo di migrazione.

    > [!IMPORTANT]
    > Dopo aver avviato la migrazione per una sottoscrizione, non sarà possibile modificare o creare regole di avviso classiche per tale sottoscrizione. Questa restrizione garantisce che non vengano perse modifiche alle regole di avviso classiche durante la migrazione alle nuove regole. Sebbene non sia possibile modificare le regole di avviso classiche, continueranno a essere eseguite e a fornire avvisi finché non ne viene eseguita la migrazione. Al termine della migrazione per la sottoscrizione, non è più possibile usare le regole di avviso classiche.

    ![Screenshot Visualizza una richiesta di conferma per la migrazione, incluse informazioni importanti con collegamenti per ottenere ulteriori informazioni prima di procedere.](media/alerts-using-migration-tool/migration-confirm.png "Conferma avvio migrazione")

1. Al termine della migrazione o se è richiesta un'azione da parte dell'utente, si riceverà un messaggio di posta elettronica con gli indirizzi specificati in precedenza. È anche possibile controllare periodicamente lo stato nella pagina di destinazione della migrazione nel portale.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Perché la sottoscrizione è elencata come non pronta per la migrazione?

Lo strumento di migrazione viene implementato ai clienti in fasi. Nelle fasi iniziali, la maggior parte o tutte le sottoscrizioni potrebbero essere contrassegnate come **non pronte per la migrazione**. 

Quando una sottoscrizione diventa pronta per la migrazione, il proprietario della sottoscrizione riceverà un messaggio di posta elettronica che informa che lo strumento è disponibile. Tenere sotto controllo questo messaggio.

### <a name="who-can-trigger-the-migration"></a>Chi può attivare la migrazione?

Gli utenti a cui è assegnato il ruolo Collaboratore monitoraggio a livello di sottoscrizione possono attivare la migrazione. [Altre informazioni sul controllo degli accessi in base al ruolo di Azure per il processo di migrazione](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Quanto tempo richiede la migrazione?

La migrazione è stata completata per la maggior parte delle sottoscrizioni in meno di un'ora. È possibile tenere traccia dello stato di avanzamento della migrazione nella pagina di destinazione della migrazione. Durante la migrazione, assicurarsi che gli avvisi siano ancora in esecuzione nel sistema di avvisi classici o in quello nuovo.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>Quali operazioni è possibile eseguire se si riscontra un problema durante la migrazione?

Per informazioni sui problemi che potrebbero verificarsi durante la migrazione, vedere la [Guida alla risoluzione dei](alerts-understand-migration.md#common-problems-and-remedies) problemi. Se è necessaria un'azione da parte dell'utente per completare la migrazione, si riceverà una notifica agli indirizzi di posta elettronica forniti durante la configurazione dello strumento.

## <a name="next-steps"></a>Passaggi successivi

- [Preparare la migrazione](alerts-prepare-migration.md)
- [Informazioni sul funzionamento dello strumento di migrazione](alerts-understand-migration.md)