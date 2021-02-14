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
ms.date: 02/10/2021
ms.author: memildin
ms.openlocfilehash: fb8dc22c923b7b53a6263baa43046862af4d2f04
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370266"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Esercitazione: Migliorare la conformità alle normative

Il Centro sicurezza di Azure consente di semplificare il processo per soddisfare i requisiti di conformità alle normative con il **dashboard Conformità alle normative**. 

Il Centro sicurezza valuta continuamente l'ambiente cloud ibrido per analizzare i fattori di rischio in base ai controlli e alle procedure consigliate negli standard applicati alle sottoscrizioni. Il dashboard riflette lo stato della conformità con questi standard. 

Quando si Abilita il Centro sicurezza in una sottoscrizione di Azure, il [benchmark di sicurezza di Azure](../security/benchmarks/introduction.md) viene assegnato automaticamente a tale sottoscrizione. Questo benchmark ampiamente rispettato si basa sui controlli di [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) e [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) con particolare attenzione alla sicurezza incentrata sul cloud.

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
- È necessario avere eseguito l'accesso con un account con accesso in lettura ai dati di conformità dei criteri (il **lettore di sicurezza** non è sufficiente). Il **Ruolo con autorizzazioni di lettura globali** è appropriato. È necessario avere come minimo i ruoli **Collaboratore ai criteri delle risorse** e **Amministratore della sicurezza** assegnati.

##  <a name="assess-your-regulatory-compliance"></a>Valutare la conformità alle normative

Il dashboard conformità normativa Mostra gli standard di conformità selezionati con tutti i requisiti, in cui è stato eseguito il mapping dei requisiti supportati alle valutazioni della sicurezza applicabili. Lo stato di queste valutazioni riflette la conformità con lo standard.

Usare il dashboard conformità normativa per concentrare l'attenzione sui Gap in conformità con gli standard e le normative scelti. Questa visualizzazione mirata consente inoltre di monitorare costantemente la conformità nel tempo all'interno di ambienti dinamici cloud e ibridi.

1. Nel menu del Centro sicurezza selezionare **Conformità con le normative**.

    Nella parte superiore della schermata è presente un dashboard con una panoramica dello stato di conformità con il set di normative di conformità supportate. Verrà visualizzato il Punteggio di conformità complessivo e il numero di valutazioni di superamento e di esito negativo associate a ogni standard.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Dashboard Conformità con le normative" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. Selezionare la scheda di uno standard di conformità che interessa (1). Verranno visualizzate le sottoscrizioni a cui è applicato lo standard (2), oltre all'elenco di tutti i controlli dello standard (3). Per i controlli applicabili, è possibile visualizzare i dettagli del passaggio e delle valutazioni non riuscite associate a tale controllo (4) e il numero di risorse interessate (5). Alcuni controlli sono disattivati. A tali controlli non è associata alcuna valutazione del Centro sicurezza. Verificare i requisiti e valutarli nell'ambiente in uso. Alcuni potrebbero essere correlati ai processi e non di tipo tecnico.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Esplorazione dei dettagli della conformità a uno standard specifico":::

1. Per generare un report PDF con un riepilogo dello stato di conformità corrente per un particolare standard, selezionare **Scarica report**.

    Il report fornisce un riepilogo generale dello stato di conformità per lo standard selezionato in base ai dati di valutazione del Centro sicurezza. Il report è organizzato in base ai controlli di quel particolare standard. Il report può essere condiviso con gli stakeholder appropriati e può essere usato per fornire giustificativi a revisori interni ed esterni.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Scaricare il report di conformità":::

## <a name="improve-your-compliance-posture"></a>Migliorare il comportamento di conformità

Usando le informazioni nel dashboard conformità normativa, migliorare la postura di conformità risolvendo le raccomandazioni direttamente nel dashboard.

1.  Selezionare una delle valutazioni non riuscite visualizzate nel dashboard per visualizzare i dettagli relativi a tale raccomandazione. Ogni raccomandazione include un set di passaggi correttivi per risolvere il problema.

1.  Selezionare una determinata risorsa per visualizzare altri dettagli e risolvere il suggerimento per tale risorsa. <br>Ad esempio, nello standard **Azure cis 1.1.0** selezionare la crittografia del disco di raccomandazione da **applicare alle macchine virtuali**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="La selezione di una raccomandazione da uno standard porta direttamente alla relativa pagina di dettagli":::

1. In questo esempio, quando si seleziona **intervenire** dalla pagina dei dettagli della raccomandazione, si arriva alle pagine della macchina virtuale di Azure della portale di Azure, in cui è possibile abilitare la crittografia dalla scheda **sicurezza** :

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="Il pulsante Intervieni nella pagina di dettagli della raccomandazione porta alle opzioni per la correzione del problema":::

    Per altre informazioni su come applicare le raccomandazioni, leggere l'articolo [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).

1.  Dopo aver intrapreso le azioni per la risoluzione delle raccomandazioni, il risultato verrà visualizzato nel report Dashboard conformità perché il Punteggio di conformità è migliore.

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

Ad esempio, è possibile che il Centro sicurezza invii un messaggio di posta elettronica a un utente specifico quando una valutazione della conformità non riesce. È necessario creare prima l'app per la logica (usando [app](../logic-apps/logic-apps-overview.md)per la logica di Azure) e quindi configurare il trigger in una nuova automazione del flusso di lavoro, come illustrato in [automatizzare le risposte ai trigger del Centro sicurezza](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Uso delle modifiche alle valutazioni della conformità normativa per attivare un'automazione del flusso di lavoro" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::




## <a name="faq---regulatory-compliance-dashboard"></a>Domande frequenti-dashboard conformità normativa

- [Quali standard sono supportati nel dashboard di conformità?](#what-standards-are-supported-in-the-compliance-dashboard)
- [Perché alcuni controlli sono visualizzati in grigio?](#why-do-some-controls-appear-grayed-out)
- [Come è possibile rimuovere uno standard predefinito, ad esempio PCI-DSS, ISO 27001 o SOC2 TSP dal dashboard?](#how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard)
- [Il suggerimento è stato modificato in base alla raccomandazione, ma non viene riflesso nel dashboard](#i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard)
- [Quali autorizzazioni sono necessarie per accedere al dashboard di conformità?](#what-permissions-do-i-need-to-access-the-compliance-dashboard)
- [Il dashboard per la conformità alle normative non viene caricato](#the-regulatory-compliance-dashboard-isnt-loading-for-me)
- [Come è possibile visualizzare un report relativo ai controlli di passaggio e di errore per ogni standard nel dashboard?](#how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard)
- [Come è possibile scaricare un report con i dati di conformità in un formato diverso da PDF?](#how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf)
- [Come è possibile creare eccezioni per alcuni criteri nel dashboard conformità normativa?](#how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard)
- [Quali piani o licenze di Azure Defender sono necessari per usare il dashboard di conformità alle normative?](#what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard)

### <a name="what-standards-are-supported-in-the-compliance-dashboard"></a>Quali standard sono supportati nel dashboard di conformità?
Per impostazione predefinita, il dashboard di conformità normativa Mostra il benchmark di sicurezza di Azure. Il benchmark di sicurezza di Azure è le linee guida per la sicurezza e le procedure consigliate per la conformità di Microsoft per Azure, basate su Framework di conformità comuni. Per altre informazioni, vedere [Introduzione al benchmark di sicurezza di Azure](../security/benchmarks/introduction.md).

Per tenere traccia della conformità con altri standard, è necessario aggiungerli in modo esplicito al dashboard.
 
È possibile aggiungere standard come Azure CIS 1.1.0 (New), NIST SP 800-53 R4, NIST SP 800-171 R2, SWIFT CSP CSCF-V2020, UK Official e UK NHS, HIPAA HITRUST, Canada Federal PBMM, ISO 27001, SOC2-TSP e PCI-DSS 3.2.1.  
 
Verranno aggiunti altri standard al dashboard e verranno incluse le informazioni su come [personalizzare il set di standard nel dashboard conformità normativa](update-regulatory-compliance-packages.md).

### <a name="why-do-some-controls-appear-grayed-out"></a>Perché alcuni controlli sono visualizzati in grigio?
Per ogni standard di conformità nel dashboard, è disponibile un elenco dei controlli standard. Per i controlli applicabili, è possibile visualizzare i dettagli del passaggio e delle valutazioni non riuscite.

Alcuni controlli sono disattivati. A tali controlli non è associata alcuna valutazione del Centro sicurezza. Alcuni possono essere correlati a procedure o processi e pertanto non possono essere verificati dal centro sicurezza. Per alcuni non sono ancora stati implementati criteri o valutazioni automatizzati, ma in futuro. Alcuni controlli possono essere la responsabilità della piattaforma, come descritto in [responsabilità condivisa nel cloud](../security/fundamentals/shared-responsibility.md). 

### <a name="how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard"></a>Come è possibile rimuovere uno standard predefinito, ad esempio PCI-DSS, ISO 27001 o SOC2 TSP dal dashboard? 
Per personalizzare il dashboard di conformità alle normative e concentrarsi solo sugli standard applicabili, è possibile rimuovere gli standard normativi visualizzati che non sono rilevanti per l'organizzazione. Per rimuovere uno standard, seguire le istruzioni riportate in [rimuovere uno standard dal dashboard](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard).

### <a name="i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard"></a>Il suggerimento è stato modificato in base alla raccomandazione, ma non viene riflesso nel dashboard
Dopo aver intrapreso le azioni necessarie per risolvere le raccomandazioni, attendere 12 ore per visualizzare le modifiche apportate ai dati di conformità. Le valutazioni vengono eseguite approssimativamente ogni 12 ore, quindi l'effetto sui dati di conformità verrà visualizzato solo dopo l'esecuzione delle valutazioni.
 
### <a name="what-permissions-do-i-need-to-access-the-compliance-dashboard"></a>Quali autorizzazioni sono necessarie per accedere al dashboard di conformità?
Per visualizzare i dati di conformità, è necessario avere almeno l'accesso in **lettura** ai dati di conformità dei criteri; Pertanto, il lettore di sicurezza da solo non sarà sufficiente. Se si è un lettore globale sulla sottoscrizione, questo sarà sufficiente.

Il set minimo di ruoli per accedere al dashboard e gestire gli standard è il **collaboratore ai criteri delle risorse** e l' **amministratore della sicurezza**.


### <a name="the-regulatory-compliance-dashboard-isnt-loading-for-me"></a>Il dashboard per la conformità alle normative non viene caricato
Per usare il dashboard conformità normativa, il Centro sicurezza di Azure deve avere Azure Defender abilitato a livello di sottoscrizione. Se il dashboard non viene caricato correttamente, provare a eseguire la procedura seguente:

1. Cancellare la cache del browser.
1. Provare a usare un browser diverso.
1. Provare ad aprire il dashboard da un percorso di rete diverso.


### <a name="how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard"></a>Come è possibile visualizzare un report relativo ai controlli di passaggio e di errore per ogni standard nel dashboard?
Nel dashboard principale è possibile visualizzare un report relativo ai controlli superati e non superati per (1) gli standard di conformità "Top 4" nel dashboard. Per visualizzare lo stato di tutti i controlli superati o non riusciti, selezionare (2) **Mostra tutto *x*** (dove x è il numero di standard di cui si sta verificando). Un piano di contesto Visualizza lo stato di conformità per ogni standard rilevato.

:::image type="content" source="media/security-center-compliance-dashboard/summaries-of-compliance-standards.png" alt-text="Sezione di riepilogo del dashboard conformità normativa":::


### <a name="how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf"></a>Come è possibile scaricare un report con i dati di conformità in un formato diverso da PDF?
Quando si seleziona **Scarica report**, selezionare lo standard e il formato (PDF o CSV). Il report risultante rifletterà il set corrente di sottoscrizioni selezionato nel filtro del portale.

- Il report PDF Mostra uno stato di riepilogo per lo standard selezionato
- Il report CSV fornisce risultati dettagliati per ogni risorsa, in quanto è correlato ai criteri associati a ogni controllo

Attualmente non è disponibile alcun supporto per il download di un report per un criterio personalizzato. solo per gli standard normativi forniti.


### <a name="how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard"></a>Come è possibile creare eccezioni per alcuni criteri nel dashboard conformità normativa?
Per i criteri incorporati nel centro sicurezza e inclusi nel punteggio sicuro, è possibile creare esenzioni per una o più risorse direttamente nel portale, come descritto in [esenzione delle risorse e consigli dal punteggio sicuro](exempt-resource.md).

Per altri criteri, è possibile creare un'esenzione direttamente nel criterio stesso, seguendo le istruzioni riportate in [struttura di esenzione dei criteri di Azure](../governance/policy/concepts/exemption-structure.md).


### <a name="what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard"></a>Quali piani o licenze di Azure Defender sono necessari per usare il dashboard di conformità alle normative?
Se uno dei pacchetti di Azure Defender è abilitato in uno qualsiasi dei tipi di risorse di Azure, è possibile accedere al dashboard conformità normativa, con tutti i relativi dati, nel centro sicurezza.





## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare il dashboard Conformità con le normative del Centro sicurezza per:

> [!div class="checklist"]
> * Consente di visualizzare e monitorare il comportamento di conformità per quanto riguarda gli standard e le normative importanti.
> * Migliorare lo stato di conformità risolvendo le raccomandazioni rilevanti e osservando il miglioramento del punteggio di conformità.

Il dashboard di conformità alle normative può semplificare notevolmente il processo di conformità e ridurre significativamente il tempo necessario per la raccolta di prove di conformità per l'ambiente Azure, ibrido e multicloud.

Per ulteriori informazioni, vedere le pagine correlate seguenti:

- [Personalizzare il set di standard nel dashboard conformità normativa](update-regulatory-compliance-packages.md) : informazioni su come selezionare gli standard da visualizzare nel dashboard conformità normativa. 
- [Gestione delle raccomandazioni di sicurezza nel centro sicurezza di Azure](security-center-recommendations.md) : informazioni su come usare le raccomandazioni nel centro sicurezza per proteggere le risorse di Azure.