---
title: Azure Marketplace
description: Descrive in che modo i clienti EA possono utilizzare Azure Marketplace
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: banders
ms.openlocfilehash: ce9dff017a796e420586ad191a59c149bed07190
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726827"
---
# <a name="azure-marketplace"></a>Azure Marketplace

Questo articolo illustra in che modo clienti e partner EA possono visualizzare gli addebiti di marketplace e abilitare gli acquisti in Azure Marketplace.

## <a name="azure-marketplace-for-ea-customers"></a>Azure Marketplace per i clienti con Contratto Enterprise

Per i clienti diretti, gli addebiti per Azure Marketplace sono visibili in Azure Enterprise Portal. Tutti gli acquisti e l'utilizzo di Azure Marketplace vengono fatturati al di fuori del pagamento anticipato con cadenza trimestrale o mensile e in modo posticipato.

Per i clienti indiretti, le sottoscrizioni di Azure Marketplace sono disponibili nella pagina **Gestisci sottoscrizioni** di Azure Enterprise Portal, ma i prezzi sono nascosti. I clienti devono contattare il proprio Licensing Solutions Provider (LSP) per informazioni sugli addebiti per Azure Marketplace.

I nuovi acquisti periodici in Azure Marketplace mensili o annuali vengono fatturati a prezzo intero durante il periodo di acquisto degli elementi di Azure Marketplace. Per questi elementi verrà eseguito il rinnovo automatico nel periodo seguente nello stesso giorno dell'acquisto originario.

Gli addebiti periodici mensili esistenti continueranno a essere rinnovati il primo giorno di ogni mese di calendario. Gli addebiti annuali verranno rinnovati alla ricorrenza annuale della data di acquisto.

Alcuni servizi di rivenditori di terze parti disponibili in Azure Marketplace ora utilizzano il saldo del pagamento anticipato di Azure del contratto Enterprise (EA). In precedenza questi servizi venivano addebitati al di fuori del pagamento anticipato di Azure del contratto Enterprise e venivano fatturati separatamente. Il pagamento anticipato di Azure del contratto Enterprise per questi servizi in Azure Marketplace semplifica la gestione di acquisti e pagamenti dei clienti. Per un elenco completo dei servizi che ora utilizzano il pagamento anticipato di Azure, vedere l'[aggiornamento del 6 marzo 2018 nel sito Web di Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/).

### <a name="partners"></a>Partner

I Licensing Solutions Provider (LSP) possono scaricare un listino prezzi di Azure Marketplace dalla pagina dell'elenco prezzi in Azure Enterprise Portal. Selezionare il collegamento per il **listino prezzi di Marketplace** in alto a destra. Il listino prezzi di Azure Marketplace elenca tutti i servizi disponibili e i relativi prezzi.

Per scaricare il listino prezzi:

1. In Azure Enterprise Portal scegliere **Report** > **Elenco prezzi**.
1. Nell'angolo superiore destro trovare il collegamento per il listino prezzi di Azure Marketplace sotto il proprio nome utente.
1. Fare clic con il pulsante destro del mouse sul collegamento e selezionare **Salva oggetto con nome**.
1. Nella finestra **Salva** modificare il titolo del documento in `AzureMarketplacePricelist.zip` in modo che il file XLSX venga cambiato in file ZIP.
1. Al termine del download, si avrà a disposizione un file ZIP con i listini prezzi specifici del paese.
1. Gli LSP devono fare riferimento al file del singolo paese per individuare i prezzi specifici del paese in questione. I Licensing Solutions Provider (LSP) possono usare la scheda **Notifiche** per visualizzare gli SKU nuovi o ritirati.
1. Le variazioni di prezzo si verificano raramente. I Licensing Solutions Provider (LSP) ricevono 30 giorni prima tramite posta elettronica le notifiche relative agli aumenti dei prezzi e alle modifiche del tasso di cambio.
1. I Licensing Solutions Provider (LSP) ricevono una fattura per registrazione, per ISV, per trimestre.

### <a name="enabling-azure-marketplace-purchases"></a>Abilitazione degli acquisti in Azure Marketplace

Gli amministratori aziendali hanno la possibilità di disabilitare o abilitare gli acquisti in Azure Marketplace per tutte le sottoscrizioni di Azure incluse nella registrazione. Se l'amministratore aziendale disabilita gli acquisti e sono presenti sottoscrizioni di Azure che hanno già sottoscrizioni di Azure Marketplace, queste sottoscrizioni di Azure Marketplace non verranno annullate o non saranno interessate.

Sebbene i clienti possano convertire le sottoscrizioni di Azure dirette al Contratto Enterprise di Azure associandole alla relativa registrazione in Azure Enterprise Portal, questa azione non converte automaticamente le sottoscrizioni figlio.

Per abilitare gli acquisti in Azure Marketplace:

1. Accedere ad Azure Enterprise Portal come amministratore aziendale.
1. Passare a **Gestisci**.
1. In **Dettagli registrazione** selezionare l'icona matita accanto alla voce **Azure Marketplace**.
1. Selezionare o deselezionare **Abilitato/Disabilitato** o **Solo SKU di tipo Gratuito/BYOL** in base alle esigenze.
1. Selezionare **Salva**.

> [!NOTE]
> L'opzione BYOL (Bring Your Own License) e Gratuito limita l'acquisto e l'acquisizione degli SKU di Azure Marketplace ai soli SKU di tipo BYOL e Gratuito.

### <a name="services-billed-hourly-for-azure-ea"></a>Servizi fatturati su base oraria per il Contratto Enterprise di Azure

I servizi seguenti vengono fatturati su base oraria secondo il Contratto Enterprise anziché in base alla tariffa mensile di MOSP:

- Application Delivery Network
- Web application firewall

### <a name="azure-remoteapp"></a>Azure RemoteApp

Se si ha un Contratto Enterprise, l'addebito per Azure RemoteApp è basato sul livello di prezzo del Contratto Enterprise. Non sono previsti addebiti aggiuntivi. Il prezzo standard include 40 ore iniziali. Il prezzo illimitato include 80 ore iniziali. RemoteApp interrompe il calcolo dell'utilizzo oltre le 80 ore.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [prezzi](ea-pricing-overview.md).
- Leggere Le domande [frequenti su Gestione dei costi](../cost-management-billing-faq.yml) e fatturazione per visualizzare un elenco di domande e risposte sui servizi Azure Marketplace e sul pagamento anticipato di Azure EA.