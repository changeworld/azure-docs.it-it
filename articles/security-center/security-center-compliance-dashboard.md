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
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 20a464011e5a8d37a6215b222323ca989e02ac04
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550935"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Esercitazione: Migliorare la conformità alle normative

Il Centro sicurezza di Azure consente di semplificare il processo per soddisfare i requisiti di conformità alle normative con il **dashboard Conformità alle normative**. 

Il Centro sicurezza valuta continuamente l'ambiente cloud ibrido per analizzare i fattori di rischio in base ai controlli e alle procedure consigliate negli standard applicati alle sottoscrizioni. Il dashboard riflette lo stato della conformità con questi standard. 

Quando si Abilita il Centro sicurezza in una sottoscrizione di Azure, viene automaticamente assegnato il [benchmark di sicurezza di Azure](../security/benchmarks/introduction.md). Questo benchmark ampiamente rispettato si basa sui controlli di [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) e [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) con particolare attenzione alla sicurezza incentrata sul cloud.

Il dashboard conformità normativa Mostra lo stato di tutte le valutazioni all'interno dell'ambiente in uso per gli standard e le normative selezionati. Quando si interviene in base alle raccomandazioni riducendo i fattori di rischio nell'ambiente, il comportamento di conformità risulta migliorato.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Valutare la conformità alle normative usando il dashboard Conformità con le normative
> * Migliorare il comportamento di conformità intervenendo in base alle raccomandazioni
> * Imposta avvisi sulle modifiche al comportamento di conformità
> * Esportare i dati di conformità come flusso continuo e come snapshot settimanali

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per esaminare le funzionalità descritte in questa esercitazione,

- è necessario abilitare [Azure Defender](azure-defender.md). È possibile provare Azure Defender gratuitamente per 30 giorni.
- È necessario aver eseguito l'accesso con un account che abbia il ruolo con autorizzazioni di lettura per l'accesso ai dati di conformità ai criteri (il **Ruolo con autorizzazioni di lettura per la sicurezza** è insufficiente). Il **Ruolo con autorizzazioni di lettura globali** è appropriato. È necessario avere come minimo i ruoli **Collaboratore ai criteri delle risorse** e **Amministratore della sicurezza** assegnati.

##  <a name="assess-your-regulatory-compliance"></a>Valutare la conformità alle normative

Il dashboard conformità normativa Mostra gli standard di conformità selezionati con tutti i requisiti, in cui è stato eseguito il mapping dei requisiti supportati alle valutazioni della sicurezza applicabili. Lo stato di queste valutazioni riflette la conformità con lo standard.

Usare il dashboard conformità normativa per concentrare l'attenzione sui Gap in conformità con gli standard e le normative scelti. Questa visualizzazione mirata consente inoltre di monitorare costantemente la conformità nel tempo all'interno di ambienti dinamici cloud e ibridi.

1. Nel menu del Centro sicurezza selezionare **Conformità con le normative**.

    Nella parte superiore della schermata è presente un dashboard con una panoramica dello stato di conformità con il set di normative di conformità supportate. Verrà visualizzato il Punteggio di conformità complessivo e il numero di valutazioni di superamento e di esito negativo associate a ogni standard.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Dashboard Conformità con le normative" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. Selezionare la scheda di uno standard di conformità che interessa (1). Verranno visualizzate le sottoscrizioni a cui è applicato lo standard (2), oltre all'elenco di tutti i controlli dello standard (3). Per i controlli applicabili, è possibile visualizzare i dettagli delle valutazioni superate e non superate associate (4), oltre al numero delle risorse interessate (5). Alcuni controlli sono disattivati. A tali controlli non è associata alcuna valutazione del Centro sicurezza. Verificare i requisiti ed eseguirne autonomamente la valutazione nel proprio ambiente. Alcuni potrebbero essere correlati ai processi e non di tipo tecnico.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Esplorazione dei dettagli della conformità a uno standard specifico":::

1. Per generare un report PDF con un riepilogo dello stato di conformità corrente per un particolare standard, selezionare **Scarica report**.

    Il report fornisce un riepilogo dettagliato dello stato di conformità per lo standard selezionato basato sui dati di valutazione del Centro sicurezza ed è organizzato in base ai controlli di tale particolare standard. Il report può essere condiviso con gli stakeholder appropriati e può essere usato per fornire giustificativi a revisori interni ed esterni.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Scaricare il report di conformità":::

## <a name="improve-your-compliance-posture"></a>Migliorare il comportamento di conformità

Usando le informazioni nel dashboard conformità normativa, migliorare la postura di conformità risolvendo le raccomandazioni direttamente nel dashboard.

1.  Fare clic su una qualsiasi delle valutazioni non superate riportate nel dashboard per visualizzare i dettagli relativi alla raccomandazione. Ogni raccomandazione include una serie di passaggi di correzione da seguire per risolvere il problema.

1.  Selezionare una determinata risorsa per visualizzare altri dettagli e risolvere il suggerimento per tale risorsa. <br>Ad esempio, nello standard **Azure cis 1.1.0** selezionare la crittografia del disco di raccomandazione da **applicare alle macchine virtuali**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="La selezione di una raccomandazione da uno standard porta direttamente alla relativa pagina di dettagli":::

1. In questo esempio, quando si seleziona **intervenire** dalla pagina dei dettagli della raccomandazione, si arriva alle pagine della macchina virtuale di Azure della portale di Azure, in cui è possibile abilitare la crittografia dalla scheda **sicurezza** :

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="Il pulsante Intervieni nella pagina di dettagli della raccomandazione porta alle opzioni per la correzione del problema":::

    Per altre informazioni su come applicare le raccomandazioni, leggere l'articolo [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).

1.  Dopo aver intrapreso le azioni per la risoluzione delle raccomandazioni, il report del dashboard di conformità verrà visualizzato in modo da migliorare il Punteggio di conformità.

    > [!NOTE]
    > Dato che le valutazioni vengono eseguite a intervalli di circa 12 ore, l'impatto sui dati di conformità sarà visibile solo dopo l'esecuzione successiva della valutazione appropriata.


## <a name="export-your-compliance-status-data"></a>Esportare i dati sullo stato di conformità

Se si vuole tenere traccia dello stato di conformità con altri strumenti di monitoraggio nell'ambiente in uso, il Centro sicurezza include un meccanismo di esportazione che semplifica questa operazione. Configurare l' **esportazione continua** per inviare i dati selezionati a un hub eventi di Azure o a un'area di lavoro log Analytics.

Usare i dati di esportazione continua in un hub eventi di Azure o in un'area di lavoro Log Analytics:

- Esportare tutti i dati di conformità normativi in un **flusso continuo**:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-stream.png" alt-text="Esporta continuamente un flusso di dati di conformità normativi" lightbox="media/security-center-compliance-dashboard/export-compliance-data-stream.png":::

- Esporta **snapshot settimanali** dei dati di conformità normativi:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png" alt-text="Esporta continuamente uno snapshot settimanale dei dati di conformità normativi" lightbox="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png":::

È anche possibile esportare un **report PDF/CSV** dei dati di conformità direttamente dal dashboard conformità normativa:

:::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-report.png" alt-text="Esportare i dati di conformità normativa come report in formato PDF o CSV" lightbox="media/security-center-compliance-dashboard/export-compliance-data-report.png":::

Per altre informazioni, vedere [esportare continuamente i dati del Centro sicurezza](continuous-export.md).


## <a name="run-workflow-automations-when-there-are-changes-to-your-compliance"></a>Eseguire le automazioni del flusso di lavoro in caso di modifiche alla conformità

La funzionalità di automazione del flusso di lavoro del Centro sicurezza può attivare app per la logica ogni volta che una delle valutazioni della conformità normativa modifica lo stato.

Ad esempio, è possibile che il Centro sicurezza invii un messaggio di posta elettronica a un utente specifico quando una valutazione della conformità non riesce. Sarà prima di tutto necessario creare l'app per la logica (usando app per la [logica di Azure](../logic-apps/logic-apps-overview.md)) e quindi configurare il trigger in una nuova automazione del flusso di lavoro, come illustrato in [automatizzare le risposte ai trigger del Centro sicurezza](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Uso delle modifiche alle valutazioni della conformità normativa per attivare un'automazione del flusso di lavoro" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare il dashboard Conformità con le normative del Centro sicurezza per:

- Consente di visualizzare e monitorare il comportamento di conformità per quanto riguarda gli standard e le normative importanti.
- Migliorare lo stato di conformità risolvendo le raccomandazioni rilevanti e osservando il miglioramento del punteggio di conformità.

Il dashboard di conformità alle normative può semplificare notevolmente il processo di conformità e ridurre significativamente il tempo necessario per la raccolta di prove di conformità per l'ambiente Azure, ibrido e multicloud.

Per ulteriori informazioni, vedere le pagine correlate seguenti:

- [Personalizzare il set di standard nel dashboard conformità normativa](update-regulatory-compliance-packages.md) : informazioni su come selezionare gli standard da visualizzare nel dashboard conformità normativa. 
- [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
- [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md): informazioni su come usare le raccomandazioni del Centro sicurezza di Azure per proteggere le risorse di Azure.