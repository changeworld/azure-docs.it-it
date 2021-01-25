---
title: 'Esercitazione: Controlli di conformità alle normative - Centro sicurezza di Azure'
description: 'Esercitazione: informazioni su come migliorare la conformità alle normative con il Centro sicurezza di Azure.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: b0baa532e8ca986e76cfb938a198d8a8697bd4dd
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757696"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Esercitazione: Migliorare la conformità alle normative

Il Centro sicurezza di Azure consente di semplificare il processo per soddisfare i requisiti di conformità alle normative con il **dashboard Conformità alle normative**. 

Il Centro sicurezza esegue valutazioni continue dell'ambiente cloud ibrido per analizzare i fattori di rischio in base ai controlli e alle procedure consigliate negli standard applicati alle sottoscrizioni. Il dashboard riflette lo stato della conformità con questi standard. 

Quando si Abilita il Centro sicurezza in una sottoscrizione di Azure, viene automaticamente assegnato il [benchmark di sicurezza di Azure](../security/benchmarks/introduction.md). Questo benchmark ampiamente rispettato si basa sui controlli di [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) e [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) con particolare attenzione alla sicurezza incentrata sul cloud.

Nel dashboard Conformità con le normative è possibile visualizzare lo stato di tutte le valutazioni all'interno dell'ambiente, nel contesto di una normativa o di uno standard specifico. Quando si interviene in base alle raccomandazioni riducendo i fattori di rischio nell'ambiente, il comportamento di conformità risulta migliorato.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Valutare la conformità alle normative usando il dashboard Conformità con le normative
> * Migliorare il comportamento di conformità intervenendo in base alle raccomandazioni

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per esaminare le funzionalità descritte in questa esercitazione,

- è necessario abilitare [Azure Defender](azure-defender.md). È possibile provare Azure Defender gratuitamente per 30 giorni.
- È necessario aver eseguito l'accesso con un account che abbia il ruolo con autorizzazioni di lettura per l'accesso ai dati di conformità ai criteri (il **Ruolo con autorizzazioni di lettura per la sicurezza** è insufficiente). Il **Ruolo con autorizzazioni di lettura globali** è appropriato. È necessario avere come minimo i ruoli **Collaboratore ai criteri delle risorse** e **Amministratore della sicurezza** assegnati.

##  <a name="assess-your-regulatory-compliance"></a>Valutare la conformità alle normative

Il dashboard conformità normativa Mostra gli standard di conformità selezionati con tutti i requisiti, in cui è stato eseguito il mapping dei requisiti supportati alle valutazioni della sicurezza applicabili. Lo stato di queste valutazioni riflette la conformità con lo standard.

Usare il dashboard per la conformità alle normative per concentrare l'attenzione sui Gap in conformità agli standard e alle normative che interessano. Questa visualizzazione mirata consente inoltre di monitorare costantemente la conformità nel tempo all'interno di ambienti dinamici cloud e ibridi.

1. Nel menu del Centro sicurezza selezionare **Conformità con le normative**.

    Nella parte superiore della schermata è presente un dashboard con una panoramica dello stato di conformità con il set di normative di conformità supportate. È possibile visualizzare il punteggio di conformità complessivo e il numero di valutazioni superate e non superate associate a ogni standard.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Dashboard Conformità con le normative":::

1. Selezionare la scheda di uno standard di conformità che interessa (1). Verranno visualizzate le sottoscrizioni a cui è applicato lo standard (2), oltre all'elenco di tutti i controlli dello standard (3). Per i controlli applicabili, è possibile visualizzare i dettagli delle valutazioni superate e non superate associate (4), oltre al numero delle risorse interessate (5). Alcuni controlli sono disattivati. A tali controlli non è associata alcuna valutazione del Centro sicurezza. Verificare i requisiti ed eseguirne autonomamente la valutazione nel proprio ambiente. Alcuni potrebbero essere correlati ai processi e non di tipo tecnico.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Esplorazione dei dettagli della conformità a uno standard specifico":::

1. Per generare un report PDF con un riepilogo dello stato di conformità corrente per un particolare standard, selezionare **Scarica report**.

    Il report fornisce un riepilogo dettagliato dello stato di conformità per lo standard selezionato basato sui dati di valutazione del Centro sicurezza ed è organizzato in base ai controlli di tale particolare standard. Il report può essere condiviso con gli stakeholder appropriati e può essere usato per fornire giustificativi a revisori interni ed esterni.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Scaricare il report di conformità":::

## <a name="improve-your-compliance-posture"></a>Migliorare il comportamento di conformità

In base alle informazioni riportate nel dashboard Conformità con le normative, è possibile migliorare la postura di conformità risolvendo le raccomandazioni direttamente all'interno del dashboard.

1.  Fare clic su una qualsiasi delle valutazioni non superate riportate nel dashboard per visualizzare i dettagli relativi alla raccomandazione. Ogni raccomandazione include una serie di passaggi di correzione da seguire per risolvere il problema.

1.  È possibile selezionare una determinata risorsa per visualizzare altri dettagli e risolvere la relativa raccomandazione. <br>Ad esempio, nello standard **Azure cis 1.1.0** è possibile selezionare la crittografia del disco di raccomandazione da **applicare alle macchine virtuali**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="La selezione di una raccomandazione da uno standard porta direttamente alla relativa pagina di dettagli":::

1. In questo esempio se si seleziona **Intervieni** nella pagina di dettagli della raccomandazione, si viene indirizzati nelle pagine del portale di Azure relative alle macchine virtuale di Azure, dove è possibile aprire una scheda **Sicurezza** e abilitare la crittografia:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="Il pulsante Intervieni nella pagina di dettagli della raccomandazione porta alle opzioni per la correzione del problema":::

    Per altre informazioni su come applicare le raccomandazioni, leggere l'articolo [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).

1.  Dopo l'intervento per la risoluzione delle raccomandazioni, l'impatto sarà visibile nel report del dashboard relativo alla conformità tramite il miglioramento del punteggio di conformità.

    > [!NOTE]
    > Dato che le valutazioni vengono eseguite a intervalli di circa 12 ore, l'impatto sui dati di conformità sarà visibile solo dopo l'esecuzione successiva della valutazione appropriata.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare il dashboard Conformità con le normative del Centro sicurezza per:

-   Visualizzare e monitorare il comportamento di conformità in relazione agli standard e alle normative importanti per l'utente.
-   Migliorare lo stato di conformità risolvendo le raccomandazioni rilevanti e osservando il miglioramento del punteggio di conformità.

Il dashboard Conformità con le normative può semplificare notevolmente il processo di conformità e ridurre in modo significativo il tempo necessario per raccogliere prove della conformità dell'ambiente ibrido e Azure in uso.

Per altre informazioni, vedere gli articoli correlati:

-   [Aggiornare i pacchetti di conformità dinamici nel dashboard Conformità alle normative (anteprima)](update-regulatory-compliance-packages.md): informazioni su questa funzionalità di anteprima che consente di aggiornare gli standard mostrati nel dashboard Conformità con le normative ai nuovi pacchetti *dinamici*. È anche possibile usare la stessa funzionalità di anteprima per aggiungere nuovi pacchetti di conformità e monitorare la conformità con gli standard aggiuntivi. 
-   [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
-   [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md): informazioni su come usare le raccomandazioni del Centro sicurezza di Azure per proteggere le risorse di Azure.
-   [Migliorare il punteggio di sicurezza nel Centro sicurezza di Azure](secure-score-security-controls.md): informazioni su come classificare in ordine di priorità le raccomandazioni sulla sicurezza per ottenere il massimo miglioramento del comportamento di sicurezza.
