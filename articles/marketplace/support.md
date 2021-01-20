---
title: Ottenere supporto per il programma Commercial Marketplace nel centro per i partner
description: Informazioni sulle opzioni di supporto per il programma Commercial Marketplace nel centro per i partner, incluso come archiviare una richiesta di supporto.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 01/19/2020
ms.openlocfilehash: 6075027124352746a3adbb8f6937d3787eb22b9d
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602592"
---
# <a name="support-for-the-commercial-marketplace-program-in-partner-center"></a>Supporto per il programma Commercial Marketplace nel centro per i partner

Microsoft fornisce il supporto per un'ampia gamma di prodotti e servizi. Trovare il team di supporto giusto è importante per garantire una risposta appropriata e tempestiva. Considerare gli scenari seguenti, che possono essere utili per indirizzare la propria richiesta al team appropriato:

- Se si è un editore e si ha una domanda da parte di un cliente, chiedere al cliente di richiedere supporto tramite i collegamenti di supporto nella [portale di Azure](https://portal.azure.com/).
- Se si è un editore e si è rilevato un problema di sicurezza con un'applicazione in esecuzione in Azure, vedere [come registrare un ticket di supporto per gli eventi di sicurezza](/azure/security/fundamentals/event-support-ticket). Gli editori sono tenuti a segnalare tempestivamente a Microsoft qualsiasi evento sospetto di violazione della sicurezza, inclusi incidenti di sicurezza e vulnerabilità relativamente al software Azure Marketplace e alle offerte di servizi.
- Se si è un editore e si ha una domanda relativa all'app o al servizio, esaminare le opzioni di supporto seguenti.

## <a name="get-help-or-open-a-support-ticket"></a>Ottenere assistenza o aprire un ticket di supporto

1. Accedere con il proprio account aziendale. Se non è ancora stato fatto, sarà necessario [creare un account del centro](partner-center-portal/create-account.md)per i partner.

1. Nel menu in alto a destra nella pagina selezionare l'icona del **supporto** . Il riquadro **Guida e supporto** viene visualizzato sul lato destro della pagina.

1. Per informazioni sul Marketplace commerciale, selezionare **Marketplace commerciale**.

   ![Menu a discesa supporto](./media/support/commercial-marketplace-support-pane.png)

1. Nella casella **Riepilogo problema** immettere una breve descrizione del problema.

1. Nella casella **tipo di problema** eseguire una delle operazioni seguenti:

    - **Opzione 1**: immettere parole chiave quali Marketplace, app di Azure, offerta SaaS, gestione degli account, gestione dei lead, problema di distribuzione, pagamento o migrazione dell'offerta di co-selling. Quindi selezionare un tipo di problema dall'elenco consigliato visualizzato.

    - **Opzione 2**: selezionare **Esplora argomenti** dall'elenco **categoria** e quindi selezionare **Marketplace commerciale**. Selezionare quindi l' **argomento** e il **sottoargomento** appropriati.

1. Dopo aver trovato l'argomento desiderato, selezionare **verifica soluzioni**.

    ![Passaggio successivo](./media/support/next-step.png)

Vengono visualizzate le opzioni seguenti:

- Per selezionare un argomento diverso, fare clic su **Seleziona un problema diverso**.
- Per risolvere il problema, esaminare i passaggi e i documenti consigliati, se disponibili.

    ![Soluzioni consigliate](./media/support/recommended-solutions.png)

Se non è possibile trovare la risposta nella guida automatica, selezionare **Fornisci i dettagli del problema**. Completare tutti i campi obbligatori per velocizzare il processo di risoluzione, quindi selezionare **Invia**.

>[!Note]
>Se non è stato effettuato l'accesso a partner Center, potrebbe essere necessario eseguire l'accesso prima di poter creare un ticket.

## <a name="track-your-existing-support-requests"></a>Tenere traccia delle richieste di supporto esistenti

Per esaminare i ticket aperti e chiusi, nel menu di spostamento a sinistra selezionare supporto per **Marketplace commerciali**  >  .

## <a name="record-issue-details-with-a-har-file"></a>Registrare i dettagli del problema con un file HAR

Per consentire agli agenti di supportare la risoluzione del problema, prendere in considerazione la possibilità di allegare un file di formato di archivio HTTP (HAR) al ticket di supporto. I file HAR sono log delle richieste di rete in un Web browser.

> [!WARNING]
> I file HAR possono registrare dati sensibili sull'account del centro per i partner.

### <a name="microsoft-edge-and-google-chrome"></a>Microsoft Edge e Google Chrome

Per generare un file HAR usando **Microsoft Edge** o **Google Chrome**:

1. Passare alla pagina Web in cui si è verificato il problema.
2. Nell'angolo superiore destro della finestra fare clic sull'icona con i puntini di sospensione, quindi su strumenti di sviluppo per **altri strumenti**  >  . È possibile premere F12 come collegamento.
3. Nel riquadro strumenti di sviluppo selezionare la scheda **rete** .
4. Selezionare **Interrompi registrazione log di rete** e **Cancella** per rimuovere i log esistenti. L'icona del record diventerà grigio.

    ![Come rimuovere i log esistenti in Microsoft Edge o Google Chrome](media/support/chromium-stop-clear-session.png)

5. Selezionare **registra log di rete** per avviare la registrazione. Quando si avvia la registrazione, l'icona del record diventerà rossa.

    ![Come avviare la registrazione in Microsoft Edge o Google Chrome](media/support/chromium-start-session.png)

6. Riprodurre il problema che si desidera risolvere.
7. Dopo aver riprodotto il problema, selezionare **Interrompi registrazione log di rete**.
8. Selezionare **Esporta har**, contrassegnato con un'icona con la freccia rivolta verso il basso e salvare il file.

    ![Come esportare un file HAR in Microsoft Edge o Google Chrome](media/support/chromium-network-export-har.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

Per generare un file HAR usando **Mozilla Firefox**:

1. Passare alla pagina Web in cui si è verificato il problema.
1. Nell'angolo in alto a destra della finestra fare clic sull'icona con i puntini di sospensione e quindi su strumenti per lo **sviluppo Web**  >  . È possibile premere F12 come collegamento.
1. Selezionare la scheda **rete** , quindi **deselezionare** per rimuovere i log esistenti.

    ![Come rimuovere i log esistenti in Mozilla Firefox](media/support/firefox-clear-session.png)

1. Riprodurre il problema che si desidera risolvere.
1. Dopo aver riprodotto il problema, selezionare **har Esporta/Importa**  >  **Salva tutto come har**.

    ![Come esportare un file HAR in Mozilla Firefox](media/support/firefox-network-export-har.png)

### <a name="apple-safari"></a>Apple Safari

Per generare un file HAR usando **Safari**:

1. Abilitare gli strumenti di sviluppo in Safari: selezionare  >  **Preferenze** Safari. Passare alla scheda **Avanzate** , quindi selezionare **Mostra menu sviluppa nella barra dei menu**.
1. Passare alla pagina Web in cui si è verificato il problema.
1. Selezionare **Develop** (Sviluppo), quindi selezionare **Show Web Inspector** (Mostra Web Inspector).
1. Selezionare la scheda **rete** , quindi selezionare **Cancella elementi di rete** per rimuovere i log esistenti.

    ![Come rimuovere i log esistenti in Safari](media/support/safari-clear-session.png)

1. Riprodurre il problema che si desidera risolvere.
1. Dopo aver riprodotto il problema, selezionare **Esporta** e salvare il file.

    ![Come esportare un file HAR in Safari](media/support/safari-network-export-har.png)

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel marketplace commerciale](partner-center-portal/update-existing-offer.md)
