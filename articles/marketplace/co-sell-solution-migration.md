---
title: Eseguire la migrazione di soluzioni di co-selling da OCP GTM al centro per i partner per Microsoft AppSource
description: Informazioni su come eseguire la migrazione di soluzioni di co-selling da OCP GTM al centro per i partner per Microsoft AppSource.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.reviewer: stmummer
ms.date: 3/04/2021
ms.openlocfilehash: 7ffb5a3dfd23a1515c6d21784b82ccdbf0674f0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593408"
---
# <a name="migration-of-co-sell-solutions-from-ocp-gtm-to-the-commercial-marketplace"></a>Migrazione di soluzioni di co-selling da OCP GTM al Marketplace commerciale

Microsoft sta muovendo l'esperienza di pubblicazione. Il [Marketplace commerciale](overview.md) fornisce una pubblicazione semplificata per la co-selling attraverso tre canali di Microsoft, centralizzando la creazione e la gestione delle offerte nel centro per i partner, in cui è già in corso la gestione delle relazioni con Microsoft.

I partner Microsoft iscritti al Marketplace commerciale possono:

- Pubblica le tue offerte in modo centralizzato e co-selling nei canali Microsoft Direct Customer, partner e seller.
- Assicurati che le tue offerte siano nel negozio online corretto,[Microsoft AppSource](https://appsource.microsoft.com) o [Azure Marketplace](https://azure.microsoft.com), per raggiungere i milioni di clienti cloud che si allineano alle funzionalità dell'offerta.
- Guida la tua esperienza di pubblicazione per la co-selling con le offerte allineate con gli obiettivi aziendali.
- Allinea la pubblicazione dell'offerta all'interno del centro per i partner, dove hai già gestito le tue relazioni Microsoft e le opportunità di co-selling.
- Sblocca i [premi del Marketplace](partner-center-portal/marketplace-rewards.md).

## <a name="prerequisites-to-continue-co-selling-with-microsoft"></a>Prerequisiti per continuare con la co-selling con Microsoft

Assicurarsi di disporre di un abbonamento attivo Microsoft Partner Network e di essere iscritti al Marketplace commerciale nel centro per i partner.

- Iscriviti [gratuitamente al Microsoft Partner Network.](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) In qualità di partner, potrai accedere a risorse, programmi, strumenti e connessioni esclusivi per far crescere il tuo business.
- Se non si dispone di un account nel Marketplace commerciale, [iscriversi ora](partner-center-portal/create-account.md) per continuare con la co-selling con Microsoft e accedere all'esperienza di pubblicazione completa.

## <a name="publishing-updates-for-attaining-co-sell-ready-status"></a>Pubblicazione degli aggiornamenti per ottenere lo stato di co-selling

Affinché la soluzione sia individuabile per i venditori e i partner Microsoft, deve soddisfare i requisiti di [co-selling pronti](marketplace-co-sell.md). Perché un venditore Microsoft possa essere incentivate, la soluzione deve soddisfare i [requisiti idonei](marketplace-co-sell.md)per gli incentivi. Completare questi requisiti nella scheda co-selling nel centro per i partner (vedere [questa immagine](#cosell-tab) più avanti in questo articolo).

> [!NOTE]
> Nel Marketplace commerciale, le soluzioni sono definite "Offerte" durante l'esperienza di pubblicazione.

Dopo aver eseguito la registrazione nel Marketplace commerciale, prepararsi alla migrazione delle soluzioni da OCP GTM.

Prima di importare le soluzioni da OCP GTM, seguire questa procedura:

1. Visitare l'elenco degli [editori](https://partner.microsoft.com/dashboard/account/v3/publishers/list)dell'azienda. Include il proprietario dell'account, i responsabili e gli sviluppatori che hanno accesso alla pubblicazione. Altre informazioni sui [ruoli utente del centro](./partner-center-portal/manage-account.md#define-user-roles-and-permissions)per i partner.
2. Rivolgersi a uno dei contatti elencati per [aggiungere utenti](https://partner.microsoft.com/dashboard/account/usermanagement) al Marketplace commerciale come *Manager* o *sviluppatori*, perché solo questi ruoli possono modificare e pubblicare soluzioni.
3. Collaborare con gli sviluppatori per spostare le soluzioni dall'account OCP GTM al Marketplace commerciale.
4. Decidere quale dei seguenti elementi si desidera eseguire:
    1. Unisci Questa soluzione con un'offerta simile nel Marketplace commerciale.
    1. Eseguire la migrazione di questa soluzione da OCP GTM al Marketplace commerciale.
    1. Rimuovere questa soluzione.

## <a name="migrate-your-solutions-from-ocp-gtm"></a>Eseguire la migrazione delle soluzioni da OCP GTM

1. Iniziare la migrazione [qui](https://partner.microsoft.com/solutions/migration#).
2. Selezionare la pagina **Panoramica** , quindi **fare clic qui per** iniziare.

    :::image type="content" source="media/co-sell-migrate/migration-overview.png" alt-text="Pagina Panoramica del centro per i partner, scheda Panoramica.":::

3. Per avviare la migrazione, selezionare la scheda **Solutions (soluzioni** ), in cui sono visualizzate tutte le soluzioni associate agli ID MPN.

    :::image type="content" source="media/co-sell-migrate/solutions-tab.png" alt-text="Pagina di panoramica del centro per i partner, scheda soluzioni.":::

    > [!NOTE]
    > Questa scheda indica se non sono presenti soluzioni in sospeso per la migrazione al Marketplace commerciale. Per continuare con la co-selling con Microsoft, verificare che le soluzioni migrate siano pubblicate nel Marketplace commerciale.

    Per altre informazioni sullo stato della soluzione, vedere le descrizioni comandi. Tutte le soluzioni in attesa di azione sono elencate in **azione**.<a name="beginmigration"></a>

4. Selezionare **inizia migrazione** (vedere l'immagine precedente) per la soluzione di cui si vuole eseguire la migrazione, quindi selezionare una delle opzioni seguenti:

    :::image type="content" source="media/co-sell-migrate/migration-options.png" alt-text="Tre opzioni per la migrazione.":::

### <a name="merge-solution-with-a-similar-offer"></a>Unisci soluzione con un'offerta simile

Selezionare questa opzione se la soluzione è già pubblicata nel Marketplace commerciale e le due devono essere unite in un'unica offerta. Questa operazione eviterà la creazione di offerte duplicate.

1. Identificare l'offerta esistente.
    1. Selezionare **si vuole unire questa soluzione con un'offerta simile nel Marketplace commerciale** (vedere l'immagine di **azione richiesta** [precedente](#beginmigration)).
    1. La scheda **azione 1** Mostra le offerte del Marketplace commerciale Live a cui è possibile associare la soluzione OCP GTM. Selezionare l'offerta Live nell'elenco, se disponibile. Se non è presente alcun elenco di offerte tra cui scegliere, immettere l'indirizzo per il cliente (URL) da Microsoft AppSource o Azure Marketplace.
        [![Scheda azione 1 del processo di merge.](media/co-sell-migrate/action-1-merge.png)](media/co-sell-migrate/action-1-merge.png#lightbox)
    1. Selezionare **Continua**.
1. Richiedere il merge.
    1. La scheda **azione 2** Mostra le direzioni per richiedere l'Unione della soluzione OCP GTM con quella identificata. Per richiedere il merge, selezionare **salva & contattare il supporto tecnico** per aprire una pagina di supporto per i partner in un browser.
    1. Selezionare **specificare i dettagli del problema** e immettere quanto segue: [ ![ la scheda azione 2 del processo di merge.](media/co-sell-migrate/action-2-merge.png)](media/co-sell-migrate/action-2-merge.png#lightbox)
    1. Selezionare **Submit** (Invia). Il team di supporto partner contatterà l'utente entro due giorni lavorativi.
    1. Il supporto per i partner collaborerà con l'utente per garantire una corretta Unione di questa offerta, in modo che venga pubblicata come offerta Live.

### <a name="migrate-this-solution-from-ocp-gtm"></a>Eseguire la migrazione di questa soluzione da OCP GTM

Selezionare questa opzione se si dispone di una soluzione OCP GTM che non dispone ancora di un'offerta corrispondente pubblicata nel Marketplace commerciale. Sarà necessario pubblicare questa soluzione nel Marketplace commerciale per continuare con la co-selling con Microsoft e la migrazione di questa soluzione consentirà di risparmiare tempo mantenendo le informazioni e la fattura dei materiali da OCP GTM. Per questa opzione è necessario selezionare un tipo di offerta.

1. Selezionare **voglio eseguire la migrazione di questa soluzione da OCP GTM al Marketplace commerciale** (vedere **l'azione richiesta** nell'immagine [precedente](#beginmigration)), quindi **continuare**.
1. Nella scheda **azione 1** selezionare il tipo di [offerta](publisher-guide-by-offer-type.md), quindi **continuare**.

    [![Scheda azione 1 del processo di migrazione.](media/co-sell-migrate/action-1-migrate.png)](media/co-sell-migrate/action-1-migrate.png#lightbox)

1. Nella scheda **azione 2** selezionare il profilo di [pubblicazione](partner-center-portal/create-account.md) dall'elenco fornito. Se non si dispone di un account editore, crearne uno nel centro per i [partner](https://partner.microsoft.com/solutions/migration), selezionarlo qui.

    [![Scheda azione 2 del processo di migrazione.](media/co-sell-migrate/action-2-migrate.png)](media/co-sell-migrate/action-2-migrate.png#lightbox)

1. Selezionare **Crea un'offerta bozza** per eseguire la migrazione della soluzione al Marketplace commerciale come bozza. Non sarà ancora Live.
1. Continuare il processo di pubblicazione nel centro per i partner. Per assistenza sulla pubblicazione nel centro per i partner, vedere [rendere l'offerta Live nel Marketplace commerciale di](#make-your-offer-live-in-the-marketplace) seguito.

### <a name="discard-this-solution"></a>Elimina questa soluzione

Selezionare questa opzione quando una soluzione in OCP GTM Solutions non è più pertinente. Verrà richiesto di confermare l'eliminazione ed è anche possibile annullarla in un secondo momento.

1. Selezionare **se si desidera rimuovere questa soluzione** (vedere l'immagine di **azione richiesta** [precedente](#beginmigration)), quindi **continuare**.
2. Selezionare **Ignora**.

    :::image type="content" source="media/co-sell-migrate/migration-discard.png" alt-text="Confermare l'eliminazione.":::

3. Per annullare lo scarto, selezionare **Annulla scarto**.

    :::image type="content" source="media/co-sell-migrate/migration-discard-undo.png" alt-text="Collegamento Annulla eliminazione.":::

4. Per ulteriori informazioni, selezionare la scheda **ottenere la guida** per contattare il team di supporto partner.

    :::image type="content" source="media/co-sell-migrate/get-support-link.png" alt-text="Collegamento di supporto nella scheda Get Help.":::

## <a name="make-your-offer-live-in-the-marketplace"></a>Fai vivere l'offerta nel Marketplace

Se si è scelto di eseguire la migrazione dell'offerta al Marketplace commerciale, viene visualizzato come bozza. È comunque necessario pubblicare l'offerta per renderla disponibile nel Marketplace commerciale; questo conserverà lo stato di co-selling, gli incentivi e la pipeline di riferimento.

Per istruzioni dettagliate sulle informazioni che è necessario fornire prima che l'offerta possa essere pubblicata, leggere la [Guida alla pubblicazione](publisher-guide-by-offer-type.md)appropriata. Per un riepilogo, vedere di seguito.

1. Nella pagina **Panoramica** di centro per i partner selezionare il nome dell'offerta bozza.

    :::image type="content" source="media/co-sell-migrate/offer-overview.png" alt-text="Pagina di panoramica dell'offerta nel centro per i partner.":::

<a name="cosell-tab"></a>

2. Completare le informazioni necessarie in ogni scheda. Facoltativamente, completare la pagina **rivendi tramite CSP** (nel menu di navigazione a sinistra sotto) per rivendere tramite il programma Cloud Solution Provider (CSP). I collegamenti e le descrizioni comando per **ulteriori informazioni illustrano** i requisiti e i dettagli.

    :::image type="content" source="media/co-sell-migrate/offer-setup.png" alt-text="Le schede di configurazione della panoramica dell'offerta sono disponibili nel centro per i partner.":::

3. Alcuni dei dettagli relativi ai venditori Microsoft sono stati copiati dalla soluzione OCP GTM. Completare le informazioni necessarie rimanenti nella scheda **co-selling with Microsoft** per rendere disponibile la co-selling dell'offerta. Al termine, selezionare **revisione e pubblicazione**. Per altre informazioni, vedere [configurare la co-selling per un'offerta di Marketplace commerciale](commercial-marketplace-co-sell.md).

    :::image type="content" source="media/co-sell-migrate/co-sell-page.png" alt-text="Scheda co-selling dell'offerta nel centro per i partner.":::

4. Dopo aver esaminato tutte le informazioni inviate, selezionare **pubblica** per inviare l'offerta bozza per la revisione della certificazione.

    :::image type="content" source="media/co-sell-migrate/co-sell-review-publish.png" alt-text="La scheda Revisione dell'offerta e il pulsante pubblica nel centro per i partner.":::

5. Tenere traccia dello stato dell'invio nella scheda **Panoramica** .

    :::image type="content" source="media/publish-status-publisher-signoff.png" alt-text="La barra di stato di pubblicazione dell'offerta è disponibile nella scheda Panoramica del centro per i partner.":::

6. Si riceverà una notifica quando la revisione della certificazione è stata completata. Se viene fornito un feedback di utilità pratica, risolverlo, quindi selezionare **pubblica** per avviare una ricertificazione.
7. Una volta che l'offerta ha superato la certificazione, visualizzare in anteprima l'offerta con il collegamento fornito e apportare le eventuali rettifiche finali desiderate. Quando si è pronti, selezionare **Go Live** (vedere il pulsante sopra) per pubblicare l'offerta nelle vetrine del Marketplace commerciale pertinenti. Una volta Live, l'offerta manterrà lo stato di co-selling dalla soluzione GTM OCP originale.

## <a name="next-steps"></a>Passaggi successivi

- [Rivendere tramite partner CSP](cloud-solution-providers.md)
- [Configurare la co-selling per un'offerta di Marketplace commerciale](commercial-marketplace-co-sell.md)
- Visualizza le [domande frequenti](https://partner.microsoft.com/resources/detail/co-sell-requirements-publish-commercial-marketplace-faq-pdf) (PDF)
