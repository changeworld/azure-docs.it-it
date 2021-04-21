---
title: 'Esercitazione: Controlli di conformità alle normative - Centro sicurezza di Azure'
description: 'Esercitazione: informazioni su come migliorare la conformità alle normative con il Centro sicurezza di Azure.'
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 04/21/2021
ms.author: memildin
ms.openlocfilehash: c8ac9079321e47a1e6d9b8689be46bf55bdd4243
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834615"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Esercitazione: Migliorare la conformità alle normative

Il Centro sicurezza di Azure consente di semplificare il processo per soddisfare i requisiti di conformità alle normative con il **dashboard Conformità alle normative**. 

Il Centro sicurezza valuta continuamente l'ambiente cloud ibrido per analizzare i fattori di rischio in base ai controlli e alle procedure consigliate negli standard applicati alle sottoscrizioni. Il dashboard riflette lo stato della conformità a questi standard. 

Quando si abilita il Centro sicurezza in una sottoscrizione di Azure, [Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction) viene assegnato automaticamente a tale sottoscrizione. Questo benchmark ampiamente rispettato si basa sui controlli del [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) e del National Institute of Standards and Technology [(NIST)](https://www.nist.gov/) con particolare attenzione alla sicurezza incentrata sul cloud.

Il dashboard di conformità alle normative mostra lo stato di tutte le valutazioni all'interno dell'ambiente per gli standard e le normative scelti. Quando si interviene in base alle raccomandazioni riducendo i fattori di rischio nell'ambiente, il comportamento di conformità risulta migliorato.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Valutare la conformità alle normative usando il dashboard Conformità con le normative
> * Migliorare il comportamento di conformità intervenendo in base alle raccomandazioni
> * Configurare gli avvisi sulle modifiche apportate al posture di conformità
> * Esportare i dati di conformità come flusso continuo e come snapshot settimanali

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per esaminare le funzionalità descritte in questa esercitazione,

- è necessario abilitare [Azure Defender](azure-defender.md). È possibile provare Azure Defender gratuitamente per 30 giorni.
- È necessario aver eseguito l'accesso con un account che abbia accesso in lettura ai dati di conformità dei criteri (**Il lettore di** sicurezza non è sufficiente). Il **Ruolo con autorizzazioni di lettura globali** è appropriato. È necessario avere come minimo i ruoli **Collaboratore ai criteri delle risorse** e **Amministratore della sicurezza** assegnati.

##  <a name="assess-your-regulatory-compliance"></a>Valutare la conformità alle normative

Il dashboard di conformità alle normative mostra gli standard di conformità selezionati con tutti i relativi requisiti, in cui i requisiti supportati vengono mappati alle valutazioni di sicurezza applicabili. Lo stato di queste valutazioni riflette la conformità con lo standard.

Usare il dashboard di conformità alle normative per concentrare l'attenzione sulle lacune in conformità agli standard e alle normative scelti. Questa visualizzazione mirata consente anche di monitorare costantemente la conformità nel tempo all'interno di ambienti cloud dinamici e ibridi.

1. Nel menu del Centro sicurezza selezionare **Conformità con le normative**.

    Nella parte superiore della schermata è presente un dashboard con una panoramica dello stato di conformità con il set di normative di conformità supportate. Verranno visualizzati il punteggio di conformità complessivo e il numero di valutazioni superate e non riuscite associate a ogni standard.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Dashboard Conformità con le normative" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. Selezionare la scheda di uno standard di conformità che interessa (1). Verranno visualizzate le sottoscrizioni a cui è applicato lo standard (2), oltre all'elenco di tutti i controlli dello standard (3). Per i controlli applicabili, è possibile visualizzare i dettagli delle valutazioni di superamento e esito negativo associate a tale controllo (4) e il numero di risorse interessate (5). Alcuni controlli sono disattivati. A tali controlli non è associata alcuna valutazione del Centro sicurezza. Verificare i requisiti e valutarli nell'ambiente in uso. Alcuni potrebbero essere correlati ai processi e non di tipo tecnico.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Esplorazione dei dettagli della conformità a uno standard specifico":::

1. Per generare un report PDF con un riepilogo dello stato di conformità corrente per un determinato standard, selezionare **Scarica report**.

    Il report fornisce un riepilogo di alto livello dello stato di conformità per lo standard selezionato in base ai dati delle valutazioni del Centro sicurezza. Il report è organizzato in base ai controlli di quel particolare standard. Il report può essere condiviso con gli stakeholder appropriati e può essere usato per fornire giustificativi a revisori interni ed esterni.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Scaricare il report di conformità":::

## <a name="improve-your-compliance-posture"></a>Migliorare il comportamento di conformità

Usando le informazioni nel dashboard di conformità alle normative, migliorare il proprio stato di conformità risolvendo le raccomandazioni direttamente all'interno del dashboard.

1.  Selezionare una delle valutazioni non riuscite visualizzate nel dashboard per visualizzare i dettagli per la raccomandazione. Ogni raccomandazione include un set di passaggi di correzione per risolvere il problema.

1.  Selezionare una determinata risorsa per visualizzare altri dettagli e risolvere la raccomandazione per tale risorsa. <br>Ad esempio, nello standard **Azure CIS 1.1.0** selezionare la raccomandazione **Crittografia dischi da applicare alle macchine virtuali.**

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="La selezione di una raccomandazione da uno standard porta direttamente alla relativa pagina di dettagli":::

1. In questo esempio, quando si seleziona Take **action** (Azione) nella pagina dei dettagli della raccomandazione, si arriva alle pagine della macchina virtuale di Azure del portale di Azure, in cui è possibile abilitare la crittografia dalla **scheda** Sicurezza:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="Il pulsante Intervieni nella pagina di dettagli della raccomandazione porta alle opzioni per la correzione del problema":::

    Per altre informazioni su come applicare le raccomandazioni, leggere l'articolo [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).

1.  Dopo aver intervenire per risolvere le raccomandazioni, il risultato verrà visualizzato nel report del dashboard di conformità perché il punteggio di conformità migliora.

    > [!NOTE]
    > Dato che le valutazioni vengono eseguite a intervalli di circa 12 ore, l'impatto sui dati di conformità sarà visibile solo dopo l'esecuzione successiva della valutazione appropriata.


## <a name="export-your-compliance-status-data"></a>Esportare i dati sullo stato di conformità

Se si vuole tenere traccia dello stato di conformità con altri strumenti di monitoraggio nell'ambiente in uso, il Centro sicurezza include un meccanismo di esportazione per semplificare questa operazione. Configurare **l'esportazione continua** per inviare dati selezionati a un hub eventi di Azure o a un'area di lavoro Log Analytics.

Usare l'esportazione continua dei dati in un hub eventi di Azure o in un'area di lavoro Log Analytics:

- Esportare tutti i dati di conformità alle normative in **un flusso continuo:**

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-stream.png" alt-text="Esportare continuamente un flusso di dati di conformità alle normative" lightbox="media/security-center-compliance-dashboard/export-compliance-data-stream.png":::

- Esportare **snapshot settimanali dei** dati di conformità alle normative:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png" alt-text="Esportare continuamente uno snapshot settimanale dei dati di conformità alle normative" lightbox="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png":::

È anche possibile esportare un **report PDF/CSV** dei dati di conformità direttamente dal dashboard di conformità alle normative:

:::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-report.png" alt-text="Esportare i dati di conformità alle normative come report PDF o CSV" lightbox="media/security-center-compliance-dashboard/export-compliance-data-report.png":::

Per altre informazioni, [vedere Esportare continuamente i dati del Centro sicurezza.](continuous-export.md)


## <a name="run-workflow-automations-when-there-are-changes-to-your-compliance"></a>Eseguire automazioni del flusso di lavoro in caso di modifiche alla conformità

La funzionalità di automazione del flusso di lavoro del Centro sicurezza può attivare App per la logica ogni volta che cambia lo stato di una delle valutazioni di conformità alle normative.

Ad esempio, potrebbe essere necessario che il Centro sicurezza indirizzi un utente specifico tramite posta elettronica quando una valutazione della conformità ha esito negativo. È necessario creare prima l'app per la logica (usando [App per la logica di Azure](../logic-apps/logic-apps-overview.md)) e quindi configurare il trigger in una nuova automazione del flusso di lavoro, come illustrato in [Automatizzare](workflow-automation.md)le risposte ai trigger del Centro sicurezza .

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Uso delle modifiche alle valutazioni della conformità alle normative per attivare un'automazione del flusso di lavoro" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::




## <a name="faq---regulatory-compliance-dashboard"></a>Domande frequenti - Dashboard sulla conformità con le normative

- [Quali standard sono supportati nel dashboard di conformità?](#what-standards-are-supported-in-the-compliance-dashboard)
- [Perché alcuni controlli vengono visualizzati in grigio?](#why-do-some-controls-appear-grayed-out)
- [Come è possibile rimuovere uno standard predefinito, ad esempio PCI-DSS, ISO 27001 o SOC2 TSP dal dashboard?](#how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard)
- [È stata apportata la modifica suggerita in base alla raccomandazione, ma non viene riflessa nel dashboard](#i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard)
- [Quali autorizzazioni sono necessarie per accedere al dashboard di conformità?](#what-permissions-do-i-need-to-access-the-compliance-dashboard)
- [Il dashboard di conformità alle normative non viene caricato per l'utente](#the-regulatory-compliance-dashboard-isnt-loading-for-me)
- [Come è possibile visualizzare un report di superamento e esito negativo dei controlli per ogni standard nel dashboard?](#how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard)
- [Come è possibile scaricare un report con dati di conformità in un formato diverso da PDF?](#how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf)
- [Come è possibile creare eccezioni per alcuni criteri nel dashboard di conformità alle normative?](#how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard)
- [Quali Azure Defender o licenze necessarie per usare il dashboard di conformità alle normative?](#what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard)
- [Ricerca per categorie quale benchmark o standard usare?](#how-do-i-know-which-benchmark-or-standard-to-use)

### <a name="what-standards-are-supported-in-the-compliance-dashboard"></a>Quali standard sono supportati nel dashboard di conformità?
Per impostazione predefinita, il dashboard di conformità alle normative mostra Azure Security Benchmark. Azure Security Benchmark è una linea guida specifica di Azure per la sicurezza e le procedure consigliate per la conformità, basate su framework di conformità comuni. Per altre informazioni, vedere Introduzione [ad Azure Security Benchmark.](../security/benchmarks/introduction.md)

Per tenere traccia della conformità con qualsiasi altro standard, è necessario aggiungerli in modo esplicito al dashboard.
 
È possibile aggiungere altri standard, ad esempio Azure CIS 1.3.0, NIST SP 800-53, NIST SP 800-171, SWIFT CSP CSCF-v2020, UK Official e UK NHS, HIPAA, Canada Federal PBMM, ISO 27001, SOC2-TSP e PCI-DSS 3.2.1.  

Altri standard verranno aggiunti al dashboard e inclusi nelle informazioni in [Personalizzare il set di standard nel dashboard di conformità alle normative.](update-regulatory-compliance-packages.md)

### <a name="why-do-some-controls-appear-grayed-out"></a>Perché alcuni controlli vengono visualizzati in grigio?
Per ogni standard di conformità nel dashboard è presente un elenco dei controlli dello standard. Per i controlli applicabili, è possibile visualizzare i dettagli del superamento e dell'esito negativo delle valutazioni.

Alcuni controlli sono disattivati. A tali controlli non è associata alcuna valutazione del Centro sicurezza. Alcuni possono essere correlati a procedure o processi e pertanto non possono essere verificati dal Centro sicurezza. Alcuni non hanno ancora implementato criteri automatizzati o valutazioni, ma lo avranno in futuro. Alcuni controlli possono anche essere responsabilità della piattaforma, come illustrato in [Shared responsibility in the cloud](../security/fundamentals/shared-responsibility.md)(Responsabilità condivisa nel cloud). 

### <a name="how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard"></a>Come è possibile rimuovere uno standard predefinito, ad esempio PCI-DSS, ISO 27001 o SOC2 TSP dal dashboard? 
Per personalizzare il dashboard di conformità alle normative e concentrarsi solo sugli standard applicabili, è possibile rimuovere qualsiasi standard normativo visualizzato non pertinente per l'organizzazione. Per rimuovere uno standard, seguire le istruzioni in [Rimuovere uno standard dal dashboard.](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard)

### <a name="i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard"></a>Il suggerimento è stato modificato in base alla raccomandazione, ma non viene visualizzato nel dashboard
Dopo aver intervenire per risolvere le raccomandazioni, attendere 12 ore per visualizzare le modifiche ai dati di conformità. Le valutazioni vengono eseguite circa ogni 12 ore, quindi l'effetto sui dati di conformità verrà visualizzato solo dopo l'esecuzione delle valutazioni.
 
### <a name="what-permissions-do-i-need-to-access-the-compliance-dashboard"></a>Quali autorizzazioni sono necessarie per accedere al dashboard di conformità?
Per visualizzare i dati di conformità, è necessario avere almeno l'accesso **lettore** anche ai dati di conformità dei criteri. in modo che il solo lettore di sicurezza non sia sufficiente. Se si è un lettore globale nella sottoscrizione, anche questo sarà sufficiente.

Il set minimo di ruoli per l'accesso al dashboard e la gestione degli standard è **Collaboratore criteri risorse** e **Amministratore sicurezza**.


### <a name="the-regulatory-compliance-dashboard-isnt-loading-for-me"></a>Il dashboard di conformità alle normative non viene caricato per l'utente
Per usare il dashboard di conformità alle normative, Centro sicurezza di Azure deve Azure Defender abilitata a livello di sottoscrizione. Se il dashboard non viene caricato correttamente, seguire questa procedura:

1. Cancellare la cache del browser.
1. Provare un browser diverso.
1. Provare ad aprire il dashboard da un percorso di rete diverso.


### <a name="how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard"></a>Come è possibile visualizzare un report di superamento e esito negativo dei controlli per ogni standard nel dashboard?
Nel dashboard principale è possibile visualizzare un report di superamento e esito negativo dei controlli per (1) gli standard di conformità più bassi "primi 4" nel dashboard. Per visualizzare tutto lo stato dei controlli che superano o non riescono, selezionare (2) Mostra tutti ***x*** (dove x è il numero di standard di cui si sta tracciando). Un piano di contesto visualizza lo stato di conformità per ogni standard monitorato.

:::image type="content" source="media/security-center-compliance-dashboard/summaries-of-compliance-standards.png" alt-text="Sezione Riepilogo del dashboard di conformità alle normative":::


### <a name="how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf"></a>Come è possibile scaricare un report con dati di conformità in un formato diverso da PDF?
Quando si seleziona **Scarica report,** selezionare lo standard e il formato (PDF o CSV). Il report risultante rifletterà il set corrente di sottoscrizioni selezionate nel filtro del portale.

- Il report PDF mostra uno stato di riepilogo per lo standard selezionato
- Il report CSV fornisce risultati dettagliati per ogni risorsa, in relazione ai criteri associati a ogni controllo

Attualmente non è disponibile alcun supporto per il download di un report per un criterio personalizzato. solo per gli standard normativi forniti.


### <a name="how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard"></a>Come è possibile creare eccezioni per alcuni criteri nel dashboard di conformità alle normative?
Per i criteri incorporati nel Centro sicurezza e inclusi nel punteggio di sicurezza, è possibile creare esenzioni per una o più risorse direttamente nel portale, come illustrato in Esenzione di risorse e raccomandazioni dal punteggio di [sicurezza.](exempt-resource.md)

Per altri criteri, è possibile creare un'esenzione direttamente nel criterio stesso, seguendo le istruzioni in Criteri di Azure [esenzione.](../governance/policy/concepts/exemption-structure.md)


### <a name="what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard"></a>Quali Azure Defender o le licenze necessarie per usare il dashboard di conformità alle normative?
Se uno dei pacchetti Azure Defender è abilitato in uno qualsiasi dei tipi di risorse di Azure, è possibile accedere al dashboard di conformità alle normative, con tutti i relativi dati, nel Centro sicurezza.


### <a name="how-do-i-know-which-benchmark-or-standard-to-use"></a>Ricerca per categorie quale benchmark o standard usare?
[Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction) (ASB) è il set canonico di raccomandazioni e procedure consigliate per la sicurezza definite da Microsoft, allineate ai framework di controllo di conformità comuni, tra cui [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/) e [NIST SP 800-53.](https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final) ASB è un benchmark molto completo ed è progettato per consigliare le funzionalità di sicurezza più aggiornate di un'ampia gamma di servizi di Azure. È consigliabile usare ILB per i clienti che vogliono ottimizzare il proprio stato di sicurezza e avere la possibilità di allineare il proprio stato di conformità agli standard del settore.

[CIS Benchmark](https://www.cisecurity.org/benchmark/azure/) è stato creato da un'entità indipendente, Center for Internet Security (CIS), e contiene raccomandazioni su un subset di servizi di Azure di base. Microsoft lavora con CIS per assicurarsi che le raccomandazioni siano aggiornate con i miglioramenti più recenti in Azure, ma a volte sono in ritardo e diventano obsolete. Tuttavia, alcuni clienti desiderano usare questo obiettivo, la valutazione di terze parti di CIS come baseline di sicurezza iniziale e primaria.

Dal momento che è stato rilasciato Azure Security Benchmark, molti clienti hanno scelto di eseguirne la migrazione in sostituzione dei benchmark CIS.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare il dashboard Conformità con le normative del Centro sicurezza per:

> [!div class="checklist"]
> * Visualizzare e monitorare il proprio stato di conformità per quanto riguarda gli standard e le normative importanti per l'utente.
> * Migliorare lo stato di conformità risolvendo le raccomandazioni rilevanti e osservando il miglioramento del punteggio di conformità.

Il dashboard di conformità alle normative può semplificare notevolmente il processo di conformità e ridurre significativamente il tempo necessario per raccogliere le prove di conformità per l'ambiente Azure, ibrido e multi-cloud.

Per altre informazioni, vedere queste pagine correlate:

- [Personalizzare il set di standard nel dashboard di conformità alle normative:](update-regulatory-compliance-packages.md) informazioni su come selezionare gli standard da visualizzare nel dashboard di conformità alle normative. 
- [Gestione delle raccomandazioni sulla sicurezza in Centro sicurezza di Azure:](security-center-recommendations.md) informazioni su come usare le raccomandazioni nel Centro sicurezza per proteggere le risorse di Azure.